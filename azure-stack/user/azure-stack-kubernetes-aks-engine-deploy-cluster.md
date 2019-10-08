---
title: Déployer un cluster Kubernetes avec le moteur AKS sur Azure Stack | Microsoft Docs
description: Comment déployer un cluster Kubernetes sur Azure Stack à partir d’une machine virtuelle cliente exécutant le moteur AKS.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na (Kubernetes)
ms.devlang: nav
ms.topic: article
ms.date: 09/27/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/27/2019
ms.openlocfilehash: 0cccd93ca24f2e93717bfbbd6ec05137d91f5bd0
ms.sourcegitcommit: 036d4b22a8076ca9ba5c667a451c544d88f8bb94
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71681817"
---
# <a name="deploy-a-kubernetes-cluster-with-the-aks-engine-on-azure-stack"></a>Déployer un cluster Kubernetes avec le moteur AKS sur Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Vous pouvez déployer un cluster Kubernetes sur Azure Stack à partir d’une machine virtuelle cliente exécutant le moteur AKS. Dans cet article, nous nous intéressons à l’écriture d’une spécification de cluster, au déploiement d’un cluster avec le fichier `apimodel.json` et à la vérification du cluster en déployant MySQL avec Helm.

## <a name="define-a-cluster-specification"></a>Définir une spécification de cluster

Vous pouvez préciser une spécification de cluster dans un fichier de document au format JSON appelé [modèle d’API](https://github.com/Azure/aks-engine/blob/master/docs/topics/architecture.md#architecture-diagram). Le moteur AKS utilise une spécification de cluster dans le modèle d’API pour créer votre cluster. 

### <a name="update-the-api-model"></a>Mettre à jour le modèle d’API

Cette section présente la création d’un modèle d’API pour votre cluster.

1.  Commencez avec un [exemple](https://github.com/Azure/aks-engine/tree/master/examples/azure-stack) de fichier de modèle d’API Azure Stack et faites-en une copie locale pour votre déploiement. À partir de la machine sur laquelle vous avez installé le moteur AKS, exécutez :

    ```bash
    curl -o kubernetes-azurestack.json https://raw.githubusercontent.com/Azure/aks-engine/master/examples/azure-stack/kubernetes-azurestack.json
    ```

    > [!Note]  
    > Si vous êtes déconnecté, vous pouvez télécharger le fichier et le copier manuellement sur la machine déconnectée sur laquelle vous envisagez de le modifier. Vous pouvez copier le fichier sur votre machine Linux à l’aide d’outils comme [PuTTY ou WinSCP.](https://www.suse.com/documentation/opensuse103/opensuse103_startup/data/sec_filetrans_winssh.html)

2.  Pour ouvrir le fichier dans un éditeur, vous pouvez utiliser nano :

    ```bash
    nano ./kubernetes-azurestack.json
    ```

    > [!Note]  
    > Si vous n’avez pas installé nano, vous pouvez le faire sur Ubuntu : `sudo apt-get install nano`.

3.  Dans le fichier kubernetes-azurestack.JSON, recherchez `orchestratorRelease`. Sélectionnez l’une des versions de Kubernetes prises en charge. Exemples : 1.11, 1.12, 1.13, 1.14. Les versions sont souvent des mises à jour. Spécifiez la version sous la forme x.xx plutôt que x.xx.x. Pour obtenir la liste des versions actuelles, consultez [Versions de Kubernetes prises en charge](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions). Vous pouvez déterminer la version prise en charge en exécutant la commande de moteur AKS suivante :

    ```bash
    aks-engine get-versions
    ```

4.  Recherchez `customCloudProfile` et fournissez l’URL du portail du locataire. Par exemple : `https://portal.local.azurestack.external`. 

5. Ajoutez `"identitySystem":"adfs"` si vous utilisez AD FS. Par exemple,

    ```JSON  
        "customCloudProfile": {
            "portalURL": "https://portal.local.azurestack.external",
            "identitySystem": "adfs"
        },
    ```

    > [!Note]  
    > Si vous utilisez Azure AD pour votre système d’identité, vous n’avez pas besoin d’ajouter le champ **identitySystem**.

6. Recherchez `portalURL` et fournissez l’URL du portail du locataire. Par exemple : `https://portal.local.azurestack.external`.

7.  Dans le tableau `masterProfile`, définissez les champs suivants :

    | Champ | Description |
    | --- | --- |
    | dnsPrefix | Entrez une chaîne unique qui servira à identifier le nom d’hôte des machines virtuelles. Par exemple, un nom basé sur le nom du groupe de ressources. |
    | count |  Entrez le nombre de maîtres que vous souhaitez pour votre déploiement. Le minimum pour un déploiement HA s’élève à 3, mais 1 seul maître est autorisé pour les déploiements non-HA. |
    | vmSize |  Entrez [une taille prise en charge par Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes), par exemple `Standard_D2_v2`. |
    | distro | Entrez `aks-ubuntu-16.04`. |

8.  Dans le tableau `agentPoolProfiles`, mettez à jour :

    | Champ | Description |
    | --- | --- |
    | count | Entrez le nombre d’agents que vous souhaitez pour votre déploiement. |
    | vmSize | Entrez [une taille prise en charge par Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes), par exemple `Standard_D2_v2`. |
    | distro | Entrez `aks-ubuntu-16.04`. |

9.  Dans le tableau `linuxProfile`, mettez à jour :

    | Champ | Description |
    | --- | --- |
    | adminUsername | Entrez le nom d’utilisateur administrateur de la machine virtuelle. |
    | ssh | Entrez la clé publique qui sera utilisée pour l’authentification SSH auprès des machines virtuelles. |

### <a name="more-information-about-the-api-model"></a>Plus d’informations sur le modèle d’API

- Pour obtenir des informations de référence complètes sur toutes les options disponibles dans le modèle d’API, reportez-vous aux [définitions de cluster](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md).  
- Pour obtenir des précisions sur des options spécifiques pour Azure Stack , reportez-vous aux [spécificités des définitions de cluster Azure Stack](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#cluster-definition-aka-api-model).  

## <a name="deploy-a-kubernetes-cluster"></a>Déployer un cluster Kubernetes

Une fois que vous avez collecté toutes les valeurs requises dans votre modèle d’API, vous pouvez créer votre cluster. À ce stade, vous devez :

Demander à votre opérateur Azure Stack de :

- Vérifier l’intégrité du système, en lui suggérant d’exécuter `Test-AzureStack` et l’outil de supervision de matériel de votre fournisseur OEM.
- Vérifiez la capacité système, notamment les ressources comme la mémoire, le stockage et les adresses IP publiques.
- Fournir les détails du quota associé à votre abonnement afin de vous permettre de vérifier que l’espace est toujours suffisant pour le nombre de machines virtuelles que vous envisagez d’utiliser.

Procéder au déploiement d’un cluster :

1.  Passez en revue les paramètres disponibles pour les [indicateurs CLI](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#cli-flags) du moteur AKS sur Azure Stack.

    | Paramètre | Exemples | Description |
    | --- | --- | --- |
    | azure-env | AzureStackCloud | Pour indiquer au moteur AKS que votre plateforme cible est Azure Stack, utilisez `AzureStackCloud`. |
    | identity-system | adfs | facultatif. Spécifiez votre solution de gestion des identités si vous utilisez Active Directory Federated Services (AD FS). |
    | location | local | Nom de la région de votre Azure Stack. Pour le Kit de développement Azure Stack (ASDK), la région a la valeur `local`. |
    | resource-group | kube-rg | Entrez le nom d’un nouveau groupe de ressources ou sélectionnez un groupe de ressources existant. Le nom de la ressource doit être alphanumérique et en minuscules. |
    | api-model | ./kubernetes-azurestack.json | Chemin du fichier de configuration du cluster ou modèle d’API. |
    | output-directory | kube-rg | Entrez le nom du répertoire contenant le fichier de sortie `apimodel.json`, ainsi que d’autres fichiers générés. |
    | client-id | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Entrez le GUID du principal du service. ID de client identifié comme ID d’application quand votre administrateur Azure Stack a créé le principal du service. |
    | client-secret | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Entrez le secret du principal du service. Il s’agit de la clé secrète client que vous définissez lors de la création de votre service. |
    | subscription-id | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Entrez votre ID d’abonnement. Pour plus d’informations, consultez [S’abonner à une offre](https://docs.microsoft.com/azure-stack/user/azure-stack-subscribe-services#subscribe-to-an-offer). |

    Voici un exemple :

    ```bash  
    aks-engine deploy \
    --azure-env AzureStackCloud \
    --location <for asdk is local> \
    --resource-group kube-rg \
    --api-model ./kubernetes-azurestack.json \
    --output-directory kube-rg \
    --client-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --client-secret xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --subscription-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --identity-system adfs # required if using AD FS
    ```

2.  Si, pour une raison quelconque, l’exécution échoue après la création du répertoire de sortie, vous pouvez corriger le problème et réexécuter la commande. Si vous réexécutez le déploiement et si vous avez déjà utilisé le même répertoire de sortie, le moteur AKS renvoie une erreur indiquant que le répertoire existe déjà. Vous pouvez remplacer le répertoire existant à l’aide de l’indicateur : `--force-overwrite`.

3.  Enregistrez la configuration de cluster du moteur AKS à un emplacement sécurisé et chiffré.

    Localisez le fichier `apimodel.json`. Enregistrez-le à un emplacement sécurisé. Ce fichier sera utilisé comme entrée dans toutes vos autres opérations du moteur AKS.

    Le fichier `apimodel.json` généré contient le principal du service, le secret et la clé publique SSH que vous utilisez dans le modèle d’API d’entrée. Il contient aussi toutes les autres métadonnées dont le moteur AKS a besoin pour effectuer toutes les autres opérations. Si vous le perdez, le moteur AKS ne peut pas configurer le cluster.

    Les secrets sont **non chiffrés**. Conservez le fichier à un emplacement sécurisé et chiffré. 

## <a name="verify-your-cluster"></a>Vérifier votre cluster

Vérifiez votre cluster en déployant mysql avec Helm pour contrôler votre cluster.

1. Obtenez l’adresse IP publique de l’un de vos nœuds principaux à l’aide du portail Azure Stack.

2. À partir d’une machine disposant d’un accès à votre instance Azure Stack, connectez-vous par le biais de SSH au nouveau nœud principal à l’aide d’un client comme PuTTy ou MobaXterm. 

3. Pour le nom d’utilisateur SSH, vous utilisez « azureuser » et le fichier de clé privée de la paire de clés que vous avez fournie pour le déploiement du cluster.

4.  Exécutez les commandes suivantes :

    ```bash
    sudo snap install helm –classic
    helm repo update
    helm install stable/mysql
    ```

5. Si, après avoir essayé d’exécuter `install stable/mysql`, vous recevez une erreur comme `Error: incompatible versions client[v2.XX.X] server[v2.YY.Y]`. Exécutez les commandes suivantes :

    ```bash 
    helm init --force-upgrade
    and retry:
    helm install stable/mysql
    ```

6.  Pour nettoyer le test, recherchez le nom utilisé pour le déploiement mysql. Dans l’exemple suivant, le nom est `wintering-rodent`. Ensuite, supprimez-le. 

    Exécutez les commandes suivantes :

    ```bash
    helm ls
    NAME REVISION UPDATED STATUS CHART APP VERSION NAMESPACE
    wintering-rodent 1 Thu Oct 18 15:06:58 2018 DEPLOYED mysql-0.10.1 5.7.14 default
    helm delete wintering-rodent
    ```

    L’interface CLI affiche :
    ```bash
    release "wintering-rodent" deleted
    ```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Résoudre les problème du moteur AKS sur Azure Stack](azure-stack-kubernetes-aks-engine-troubleshoot.md)