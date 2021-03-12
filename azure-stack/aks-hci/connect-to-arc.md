---
title: Connecter un cluster Azure Kubernetes Service sur Azure Stack HCI à Azure Arc pour Kubernetes
description: Connecter un cluster Azure Kubernetes Service sur Azure Stack HCI à Azure Arc pour Kubernetes
author: abha
ms.topic: how-to
ms.date: 12/02/2020
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: 96e1996cbf22e354b960b1a46a8848543942b0cc
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "101839859"
---
# <a name="connect-an-azure-kubernetes-service-on-azure-stack-hci-cluster-to-azure-arc-for-kubernetes"></a>Connecter un cluster Azure Kubernetes Service sur Azure Stack HCI à Azure Arc pour Kubernetes

> S’applique à : AKS sur Azure Stack HCI, AKS Runtime sur Windows Server 2019 Datacenter

Lorsqu’un cluster Azure Kubernetes Service sur Azure Stack HCI est attaché à Azure Arc, il s’affiche dans le Portail Azure. Il reçoit alors un ID Azure Resource Manager et une identité managée. Les clusters sont attachés à des abonnements Azure standard, sont situés dans un groupe de ressources et peuvent recevoir des étiquettes comme n’importe quelle autre ressource Azure.

Pour connecter un cluster Kubernetes à Azure, l’administrateur du cluster doit déployer des agents. Ces agents s’exécutent dans un espace de noms Kubernetes nommé `azure-arc` et représentent des déploiements Kubernetes standard. Les agents sont responsables de la connectivité à Azure, de la collecte des journaux et des métriques Azure Arc, et de la surveillance des demandes de configuration.

Kubernetes avec Azure Arc prend en charge le protocole SSL standard pour sécuriser les données en transit. Par ailleurs, les données sont chiffrées au repos dans une base de données Azure Cosmos DB pour garantir leur confidentialité.

Les étapes suivantes fournissent une procédure pas à pas permettant d’intégrer des clusters Azure Kubernetes Service sur Azure Stack HCI à Azure Arc. **Vous pouvez ignorer ces étapes si vous avez déjà intégré votre cluster Kubernetes à Azure Arc via Windows Admin Center.**

## <a name="before-you-begin"></a>Avant de commencer

Vérifiez que les exigences suivantes sont remplies :

* Un cluster Azure Kubernetes Service sur Azure Stack HCI avec au moins un nœud Worker Linux en cours d’exécution. 

* Vous aurez besoin d’un fichier kubeconfig pour accéder au cluster, et du rôle d’administrateur de cluster sur le cluster pour le déploiement d’agents Kubernetes compatibles avec Arc.
* Module PowerShell Azure Kubernetes Service sur Azure Stack HCI installé.
* Pour installer les extensions Kubernetes CLI compatibles avec Azure Arc, vous devez disposer de la version 2.3+ de l’interface Azure CLI. [Installez l’interface de ligne de commande Azure](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true). Vous pouvez également mettre à jour vers la dernière version pour vous assurer d’avoir Azure CLI version 2.3+.
* Un abonnement Azure sur lequel vous êtes propriétaire ou contributeur. 
* Exécutez les commandes dans ce document dans une fenêtre d’administration PowerShell.


## <a name="network-requirements"></a>Configuration requise pour le réseau

Les agents Azure Arc requièrent les protocoles/ports/URL sortantes suivants pour fonctionner.

* TCP sur le port 443 --> `https://:443`
* TCP sur le port 9418 --> `git://:9418`

| Point de terminaison (DNS)                                                                                               | Description                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `https://management.azure.com`                                                                                 | Requis pour que l’agent se connecte à Azure et inscrive le cluster.                                                        |
| `https://eastus.dp.kubernetesconfiguration.azure.com`, `https://westeurope.dp.kubernetesconfiguration.azure.com` | Point de terminaison du plan de données pour que l’agent transmette les informations de configuration d’état et de récupération.                                      |
| `https://docker.io`                                                                                            | Requis pour extraire des images de conteneur.                                                                                         |
| `https://github.com`, git://github.com                                                                         | Des exemples de dépôts GitOps sont hébergés sur GitHub. L’agent de configuration requiert une connectivité à un point de terminaison Git que vous spécifiez. |
| `https://login.microsoftonline.com`                                                                            | Requis pour extraire et mettre à jour des jetons Azure Resource. Manager                                                                                    |
| `https://azurearcfork8s.azurecr.io`                                                                            | Requis pour extraire des images de conteneur pour les agents Azure Arc.                                                                  |
| `https://eus.his.arc.azure.com`, `https://weu.his.arc.azure.com`                                                                            |  Requis pour extraire les certificats d'identité managée attribués par le système                                                                  |

## <a name="step-1-log-in-to-azure"></a>Étape 1 : Connexion à Azure

Connectez-vous à Azure puis, une fois connecté, définissez un abonnement Azure sur lequel vous êtes propriétaire ou contributeur comme votre abonnement par défaut.

```console
az login
az account set --subscription "00000000-aaaa-bbbb-cccc-000000000000"
```

## <a name="step-2-register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Étape 2 : Inscrire les deux fournisseurs pour Kubernetes à extension Azure Arc :

Vous pouvez ignorer cette étape si vous avez déjà inscrit les deux fournisseurs pour le service Kubernetes compatible avec Azure Arc sur votre abonnement. L’inscription est un processus asynchrone et doit être effectuée une fois par abonnement. L’inscription peut prendre environ 10 minutes. 

```console
az provider register --namespace Microsoft.Kubernetes
az provider register --namespace Microsoft.KubernetesConfiguration
```

Vous pouvez vérifier si vous êtes inscrit avec les commandes suivantes :

```console
az provider show -n Microsoft.Kubernetes -o table
az provider show -n Microsoft.KubernetesConfiguration -o table
```

## <a name="step-3-create-a-resource-group"></a>Étape 3 : Créer un groupe de ressources

Vous avez besoin d’un groupe de ressources pour contenir la ressource de cluster connectée. Vous pouvez utiliser un groupe de ressources existant dans l’emplacement USA Est ou Europe Ouest. Si vous n’avez pas de groupe de ressources existant dans l’emplacement USA Est ou Europe Ouest, utilisez la commande suivante pour créer un nouveau groupe de ressources :

```console
az group create --name AzureArcTest -l EastUS -o table
```

## <a name="step-4-create-a-new-service-principal"></a>Étape 4 : Création d’un principal de service


Vous pouvez ignorer cette étape si vous avez déjà créé un principal du service avec le rôle `contributor` et que vous connaissez les valeurs d’appID, de mot de passe et de locataire du principal du service.

Créez un nouveau principal du service avec un nom informatif. Ce nom doit être unique pour votre locataire Azure Active Directory. Un principal du service a le rôle `Contributor` (Collaborateur) par défaut. Ce rôle dispose des autorisations complètes de lecture et d’écriture dans un compte Azure. Vous pouvez également réutiliser ce principal du service pour intégrer plusieurs clusters à Azure Arc. Définissez l’étendue de votre principal du service sur *subscriptions/resource-group*. *Veillez à enregistrer les valeurs d’appID, de mot de passe et de locataire du principal du service car vous en aurez besoin dans les étapes suivantes.*

```console
az ad sp create-for-RBAC --name "azure-arc-for-k8s" --scope /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}
```

**Output:**

```
{
  "appId": "00000000-0000-0000-0000-000000000000",
  "displayName": "azure-arc-for-k8s",
  "name": "https://azure-arc-for-k8s",
  "password": "aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee",
  "tenant": "ffffffff-gggg-hhhh-iiii-jjjjjjjjjjjj"
}
```
## <a name="step-5-save-service-principal-details"></a>Étape 5 : Enregistrer les détails du principal du service
Enregistrez les valeurs d’appID, de mot de passe et de locataire du principal du service créé ainsi que le nom du cluster, l’ID d’abonnement Azure, le nom du groupe de ressources et l’emplacement dans les variables PowerShell. Vous pourrez ainsi réutiliser ces détails dans d’autres tutoriels. Veillez à enregistrer également ces valeurs dans un fichier Bloc-notes au cas où vous souhaiteriez fermer votre session PowerShell.

```PowerShell
$clusterName = #<name of your Kubernetes cluster>
$resourceGroup = #<Azure resource group to store your connected Kubernetes cluster in Azure Arc>
$location = #<Azure resource group location. This can only be eastus or westeurope for Azure Arc for Kubernetes>
$subscriptionId = #<Azure subscription Id>
$appId = #<appID from the service principal created above>
$password = #<password from the service principal created above>
$tenant = #<tenant from the service principal created above>
```
Vérifiez que vous avez attribué les valeurs appropriées aux variables en exécutant :

```PowerShell
echo $clusterName 
echo $resourceGroup
echo $location 
echo $subscriptionId 
echo $appId 
echo $password 
echo $tenant 
```

## <a name="step-6-connect-to-azure-arc-using-service-principal-and-the-aks-hci-powershell-module"></a>Étape 6 : Se connecter à Azure Arc à l’aide du principal du service et du module PowerShell Aks-Hci

Nous allons ensuite connecter notre cluster Kubernetes à Azure à l’aide du principal du service et du module PowerShell Aks-Hci. Cette étape déploie des agents Azure Arc pour Kubernetes dans l’espace de noms `azure-arc`.

Référencez le principal du service nouvellement créé et exécutez la commande `Install-AksHciArcOnboarding` disponible dans le module PowerShell Aks-Hci.

```PowerShell
Install-AksHciArcOnboarding -name $clusterName -resourcegroup $resourceGroup -location $location -subscriptionid $subscriptionId -clientid $appId -clientsecret $password -tenantid $tenant
```
## <a name="verify-connected-cluster"></a>Vérifier un cluster connecté

Vous pouvez voir votre ressource de cluster Kubernetes sur le [Portail Azure](https://portal.azure.com/). Après avoir ouvert le portail dans votre navigateur, accédez au groupe de ressources et à la ressource Kubernetes avec Azure Arc basée sur le nom de ressource et le nom de du groupe de ressources utilisés plus tôt dans la commande PowerShell `Install-AksHciArcOnboarding`.

> [!NOTE]
> Après l’intégration du cluster, il faut environ 5 à 10 minutes pour que les métadonnées du cluster (version du cluster, version de l’agent, nombre de nœuds) soient visibles sur la page Vue d’ensemble de la ressource Kubernetes avec Azure Arc dans le portail Azure.

Pour supprimer votre cluster, ou pour vous connecter à votre cluster s’il se trouve derrière un serveur proxy sortant, consultez [Connecter un cluster Kubernetes avec Azure Arc](/azure/azure-arc/kubernetes/connect-cluster).

## <a name="azure-arc-agents-for-kubernetes"></a>Agents Azure Arc pour Kubernetes

Kubernetes avec Azure Arc déploie quelques opérateurs dans l’espace de noms `azure-arc`. Vous affichez ces déploiements et ces pods par `kubectl` ci-dessous. 

```console
kubectl -n azure-arc get deployments,pods
```

Kubernetes avec Azure Arc se compose de quelques agents (opérateurs) qui s’exécutent dans votre cluster déployé dans l’espace de noms `azure-arc`.

* `deployment.apps/config-agent` : surveille le cluster connecté pour les ressources de configuration de contrôle de code source appliquées au cluster, et met à jour l’état de conformité.
* `deployment.apps/controller-manager` : opérateur d’opérateurs orchestrant les interactions entre les composants Azure Arc.
* `deployment.apps/metrics-agent` : collecte les métriques d’autres agents Arc pour s’assurer que ces agents offrent des performances optimales.
* `deployment.apps/cluster-metadata-operator` : collecte des métadonnées de cluster : version du cluster, nombre de nœuds et version de l’agent Azure Arc
* `deployment.apps/resource-sync-agent` : synchronise les métadonnées de cluster précitées dans Azure.
* `deployment.apps/clusteridentityoperator` : Kubernetes avec Azure Arc prend actuellement en charge l’identité affectée par le système. clusteridentityoperator gère le certificat d’identité de service managé (MSI) utilisé par d’autres agents pour la communication avec Azure.
* `deployment.apps/flux-logs-agent` : collecte des journaux des opérateurs de flux déployés dans le cadre de la configuration du contrôle de code source.

## <a name="next-steps"></a>Étapes suivantes

* [Utiliser GitOps dans un cluster connecté](/azure/azure-arc/kubernetes/use-gitops-connected-cluster)
* [Utiliser Azure Policy pour gérer la configuration du cluster](/azure/azure-arc/kubernetes/use-azure-policy)
* [Activer Azure Monitor sur un cluster Kubernetes avec Azure Arc](/azure/azure-monitor/insights/container-insights-enable-arc-enabled-clusters)
