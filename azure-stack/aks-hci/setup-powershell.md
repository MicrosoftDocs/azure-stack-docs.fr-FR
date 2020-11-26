---
title: Démarrage rapide en vue de configurer un hôte Azure Kubernetes Service sur Azure Stack HCI à l’aide de Windows PowerShell
description: Découvrez comment configurer un hôte Azure Kubernetes Service sur Azure Stack HCI à l’aide de Windows PowerShell
author: jessicaguan
ms.topic: quickstart
ms.date: 09/23/2020
ms.author: jeguan
ms.openlocfilehash: 089488e246bdb7c12bbd0808ef2e92a4c83b0fce
ms.sourcegitcommit: b50dd116d6d1f89d42bd35ad0f85bb25c5192921
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/26/2020
ms.locfileid: "92253958"
---
# <a name="quickstart-set-up-an-azure-kubernetes-service-host-on-azure-stack-hci-using-powershell"></a>Démarrage rapide : Configurer un hôte Azure Kubernetes Service sur Azure Stack HCI à l’aide de PowerShell

> S’applique à : Azure Stack HCI

Dans ce guide de démarrage rapide, vous allez apprendre à configurer un hôte Azure Kubernetes Service sur Azure Stack HCI à l’aide de PowerShell. Si vous préférez utiliser Windows Admin Center, consultez [Configurer avec Windows Admin Center](setup.md).

## <a name="before-you-begin"></a>Avant de commencer

Tout d’abord, vérifiez que vous disposez d’un cluster Azure Stack HCI doté de 2 à 4 nœuds ou d’un nœud simple Azure Stack HCI. **Nous vous recommandons de disposer d’un cluster Azure Stack HCI doté de 2 à 4 nœuds.** Si vous n’en avez pas, suivez les instructions de la [page d’inscription Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/).

## <a name="step-1-download-and-install-the-akshci-powershell-module"></a>Étape 1 : Téléchargez et installez le module PowerShell AksHci.

Téléchargez `AKS-HCI-Public=Preview-Oct-2020` à partir de la [page d’inscription Azure Kubernetes Service sur Azure Stack HCI](https://aka.ms/AKS-HCI-Evaluate). Le fichier zip `AksHci.Powershell.zip` contient le module PowerShell.

Si vous avez déjà installé Azure Kubernetes Service sur Azure Stack HCI avec PowerShell ou Windows Admin Center, exécutez la commande suivante avant de continuer.

   ```powershell
   Uninstall-AksHci
   ```

**Fermez toutes les fenêtres PowerShell.** Supprimez les répertoires existants pour AksHci, AksHci.Day2 et MSK8sDownloadAgent qui se trouvent dans le chemin `%systemdrive%\program files\windowspowershell\modules`. Une fois cette opération effectuée, vous pouvez extraire le contenu du nouveau fichier zip. Veillez à extraire le fichier zip à l’emplacement approprié (`%systemdrive%\program files\windowspowershell\modules`).

   ```powershell
   Import-Module AksHci
   ```

Après avoir exécuté la commande ci-dessus, fermez toutes les fenêtres PowerShell et rouvrez une session d’administration pour exécuter les commandes dans les étapes suivantes.

## <a name="step-2-prepare-your-machines-for-deployment"></a>Étape 2 : Préparer votre ou vos machines en vue du déploiement

Exécutez des vérifications sur chaque nœud physique pour vérifier si toutes les conditions requises sont satisfaites pour installer Azure Kubernetes Service sur Azure Stack HCI.

Ouvrez PowerShell en tant qu’administrateur et exécutez la commande suivante.

   ```powershell
   Initialize-AksHciNode
   ```

Une fois les vérifications terminées, le texte « Terminé » s’affiche en vert.

## <a name="step-3-configure-your-deployment"></a>Étape 3 : Configurer votre déploiement

Définissez les paramètres de configuration de l’hôte Azure Kubernetes Service. **Pour cluster Azure Stack HCI comptant de 2 à 4 nœuds, vous devez spécifier `MultiNode` dans les paramètres `-deploymentType`, `wssdImageDir` et `cloudConfigLocation`.** Pour un cluster Azure Stack HCI à nœud simple, tous les paramètres sont facultatifs et réglés sur leurs valeurs par défaut. Toutefois, pour des performances optimales, **nous vous recommandons de préférer un déploiement de cluster Azure Stack HCI comptant de 2 à 4 nœuds.**

Configurez votre déploiement avec la commande suivante.

   ```powershell
   Set-AksHciConfig [-deploymentType {SingleNode, MultiNode}]
                    [-wssdImageDir]
                    [-cloudConfigLocation]
                    [-nodeConfigLocation]
                    [-vnetName]
                    [-controlPlaneVmSize]
                    [-loadBalancerVmSize]
                    [-sshPublicKey]
                    [-vipPoolStartIp]
                    [-vipPoolEndIp]
                    [-macPoolStart]
                    [-macPoolEnd]
                    [-vlanID]
                    [-cloudServiceCidr]
                    [-wssdDir]
                    [-akshciVersion]
                    [-vnetType]
                    [-nodeAgentPort]
                    [-nodeAgentAuthorizerPort]
                    [-clusterRoleName]
                    [-skipHostLimitChecks]
                    [-insecure]
                    [-skipUpdates]
                    [-forceDnsReplication]

   ```

### <a name="optional-parameters"></a>Paramètres facultatifs

`-deploymentType`

Type de déploiement. Valeurs acceptées : SingleNode, MultiNode. La valeur par défaut est SingleNode.

`-wssdImageDir`

Chemin d’accès au répertoire où Azure Kubernetes Service sur Azure Stack HCI stocke ses images VHD. Valeur par défaut `%systemdrive%\wssdimagestore` pour les déploiements à nœud unique. *Pour les déploiements multinœud, ce paramètre est obligatoire*. Le chemin d’accès doit pointer vers un chemin d’accès de stockage partagé, par exemple `C:\ClusterStorage\Volume2\ImageStore` ou un partage SMB tel que `\\FileShare\ImageStore`.

`-cloudConfigLocation`

Emplacement de stockage de la configuration de l’agent cloud. Valeur par défaut `%systemdrive%\wssdimagestore` pour les déploiements à nœud unique. L’emplacement peut être le même que le chemin d’accès de `-wssdImageDir` ci-dessus. Pour les *déploiements multinœud, ce paramètre est obligatoire*.

`-nodeConfigLocation`

Emplacement où les agents de nœud stockeront leur configuration. Il doit s’agir d’un chemin d’accès local.

`-vnetName`

Nom du commutateur virtuel auquel connecter les ordinateurs virtuels. La valeur par défaut est le nom « External ». Le commutateur sera créé s’il n’existe pas.  

`-controlPlaneVmSize`

Taille de la machine virtuelle à créer pour le plan de contrôle. Pour obtenir la liste des tailles de machines virtuelles disponibles, exécutez `Get-AksHciVmSize`.

`-loadBalancerVmSize`

Taille de la machine virtuelle à créer pour les machines virtuelles Load Balancer. Pour obtenir la liste des tailles de machines virtuelles disponibles, exécutez `Get-AksHciVmSize`.

`-sshPublicKey`

Chemin d’accès à un fichier de clé publique SSH. À l’aide de cette clé publique, vous serez en mesure de vous connecter aux machines virtuelles créées par le déploiement Azure Kubernetes Service sur Azure Stack HCI. Si aucune clé n’est fournie, nous allons en rechercher une dans  `%systemdrive%\Users\<username>\.ssh\id_rsa.pub`. Si le fichier n’existe pas, une paire de clés SSH est générée et utilisée dans l’emplacement ci-dessus.  

`-vipPoolStartIp`

Lorsque vous utilisez des pools d’adresses IP virtuelles pour votre déploiement, ce paramètre spécifie le démarrage réseau du pool. La valeur par défaut est none.

`-vipPoolEndIp`

Lorsque vous utilisez des pools d’adresses IP virtuelles pour votre déploiement, ce paramètre spécifie la fin réseau du pool. La valeur par défaut est none.

`-macPoolStart`

Cela permet de spécifier le début de l’adresse MAC du pool MAC que vous souhaitez utiliser pour la machine virtuelle hôte Azure Kubernetes Service. La syntaxe de l’adresse MAC exige que le bit le moins significatif du premier octet soit toujours égal à 0 et que le premier octet soit toujours un nombre pair (par exemple, 00, 02, 04, 06...). Une adresse MAC type peut se présenter comme suit : 02:1E:2B:78:00:00. La valeur par défaut est none.

`-macPoolEnd`

Cela permet de spécifier la fin de l’adresse MAC du pool MAC que vous souhaitez utiliser pour la machine virtuelle hôte Azure Kubernetes Service. La syntaxe de l’adresse MAC exige que le bit le moins significatif du premier octet soit toujours égal à 0 et que le premier octet soit toujours un nombre pair (par exemple, 00, 02, 04, 06...). Le premier octet de l’adresse passé en tant que `-macPoolEnd` doit être le même que le premier octet de l’adresse passé en tant que `-macPoolStart`. La valeur par défaut est none.

`-vlandID`

Elle peut être utilisée pour spécifier un ID de réseau local virtuel. L’hôte Azure Kubernetes Service et les cartes réseau des machines virtuelles du cluster Kubernetes seront marqués avec l’ID de réseau local virtuel fourni. La valeur par défaut est none.

`cloudServiceCidr`

Elle peut être utilisé pour fournir un préfixe IP/réseau statique à attribuer au service MOC CloudAgent. Cette valeur doit être fournie au format CIDR. (Exemple : 192.168.1.2/16). La valeur par défaut est none.

`-wssdDir`

Il s’agit d’un répertoire de travail que le module doit utiliser pour stocker des fichiers de petite taille. Par défaut, pointe vers `%PROGRAMFILES%\AksHci` et ne doit pas être modifiée pour la plupart des déploiements.  

`-akshciVersion`

Version d’Azure Kubernetes Service sur Azure Stack HCI que vous souhaitez déployer. La version la plus récente est utilisée par défaut.

`-vnetType`

Type de commutateur virtuel à connecter ou à créer. Par défaut, il s’agit du type de commutateur « External ».

`-nodeAgentPort`

Numéro de port TCP/IP sur lequel nodeagents doit écouter le trafic. La valeur par défaut est 45000.  

`-nodeAgentAuthorizerPort`

Numéro de port TCP/IP que nodeagents doit utiliser pour son port d’autorisation. La valeur par défaut est 45001.  

`-clusterRoleName`

Spécifie le nom à utiliser lors de la création de cloudagent en tant que service générique au sein du cluster. Par défaut, il s’agit d’un nom unique avec un préfixe ca et un suffixe GUID (par exemple : « ca-9e6eb299-bc0b-4f00-9fd7-942843820c26 »)

`-skipHostLimitChecks`

Demande au script d’ignorer les vérifications qu’il effectue pour confirmer l’espace mémoire et disque est disponible avant de permettre au déploiement de continuer.

`-insecure`

Déploie les composants Azure Kubernetes Service sur Azure Stack HCI, tels que cloudagent et nodeagent, en mode non sécurisé (aucune connexion TLS sécurisée).  Il est déconseillé d’utiliser le mode non sécurisé dans les environnements de production.

`-skipUpdates`

Utilisez cet indicateur si vous souhaitez ignorer les mises à jour disponibles.

`-forceDnsReplication`

La réplication DNS peut prendre jusqu’à une heure sur certains systèmes. Le déploiement s’effectue alors lentement. Si vous avez rencontré ce problème, c’est que Install-AksHci est bloqué dans une boucle. Pour ignorer ce problème, essayez d’utiliser cet indicateur. L’indicateur `-forceDnsReplication` n’est pas un correctif garanti. En cas d’échec de la logique sous-jacente de l’indicateur, l’erreur est masquée et la commande s’exécute comme si l’indicateur n’avait pas été fourni.

### <a name="reset-the-azure-kubernetes-service-on-azure-stack-hci-configuration"></a>Réinitialiser la configuration d’Azure Kubernetes Service sur Azure Stack HCI

Pour réinitialiser la configuration d’Azure Kubernetes Service sur Azure Stack HCI, exécutez la commande suivante. L’exécution de cette commande seule permet de rétablir les valeurs par défaut de la configuration.

```powershell
Set-AksHciConfig
```

## <a name="step-4-start-a-new-deployment"></a>Étape 4 : Démarrer un nouveau déploiement

Après avoir configuré votre déploiement, vous devez le démarrer. Cette opération installe les agents/services Azure Kubernetes Service sur Azure Stack HCI et l’hôte Azure Kubernetes Service.

Pour commencer le déploiement, exécutez la commande suivante.

```powershell
Install-AksHci
```

### <a name="verify-your-deployed-azure-kubernetes-service-host"></a>Vérifier votre hôte Azure Kubernetes Service déployé

Pour vous assurer que votre hôte Azure Kubernetes Service a été déployé, exécutez la commande suivante. Vous pouvez également obtenir la liste des clusters Kubernetes à l’aide de la même commande après les avoir déployés.

```powershell
Get-AksHciCluster
```

## <a name="step-5-access-your-clusters-using-kubectl"></a>Étape 5 : Accéder à vos clusters à l’aide de kubectl

Pour accéder à votre hôte Azure Kubernetes Service ou à votre cluster Kubernetes à l’aide de kubectl, exécutez la commande suivante. Cette opération utilise le fichier kubeconfig du cluster spécifié comme fichier kubeconfig par défaut pour kubectl.

```powershell
Get-AksHciCredential -clusterName
                     [-outputLocation]
```

### <a name="required-parameters"></a>Paramètres obligatoires

`clusterName`

Nom du cluster.

### <a name="optional-parameters"></a>Paramètres facultatifs

`outputLocation`

Emplacement auquel vous voulez que le fichier kubeconfig soit téléchargé. La valeur par défaut est `%USERPROFILE%\.kube`.

## <a name="get-logs"></a>Obtenir des journaux d’activité

Pour récupérer les journaux de vos pods, exécutez la commande suivante. Cette commande crée un dossier compressé de sortie appelé `akshcilogs` dans le chemin d’accès `C:\wssd\akshcilogs`.

```powershell
Get-AksHciLogs
```

## <a name="reinstall-azure-kubernetes-service-on-azure-stack-hci"></a>Réinstaller Azure Kubernetes Service sur Azure Stack HCI

La réinstallation du service Azure Kubernetes sur Azure Stack HCI supprime tous vos clusters Kubernetes, le cas échéant, et l’hôte Azure Kubernetes Service. Elle désinstalle également les agents et services Azure Kubernetes Service sur Azure Stack HCI des nœuds. Elle repasse ensuite par les étapes du processus d’installation d’origine jusqu’à ce que l’hôte soit recréé. Azure Kubernetes Service sur Azure Stack HCI que vous avez configuré via `Set-AksHciConfig` et les images VHDX téléchargées sont conservés.

Pour réinstaller Azure Kubernetes Service sur Azure Stack HCI, exécutez la commande suivante.

```powershell
Restart-AksHci
```

## <a name="remove-azure-kubernetes-service-on-azure-stack-hci"></a>Supprimer Azure Kubernetes Service sur Azure Stack HCI

Pour supprimer Azure Kubernetes Service sur Azure Stack HCI, exécutez la commande suivante.

```powershell
Uninstall-AksHci
```

## <a name="next-steps"></a>Étapes suivantes

- [Créer un cluster Kubernetes pour vos applications](create-kubernetes-cluster-powershell.md)
