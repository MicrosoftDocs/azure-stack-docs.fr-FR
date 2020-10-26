---
title: Démarrage rapide pour créer des clusters Kubernetes sur Azure Stack HCI à l’aide de Windows PowerShell
description: Découvrez comment créer des clusters Kubernetes sur Azure Stack HCI avec Windows PowerShell
author: jessicaguan
ms.topic: quickstart
ms.date: 09/22/2020
ms.author: jeguan
ms.openlocfilehash: b9287add391d2a3132b3ef0baadf5668b1ea057e
ms.sourcegitcommit: be445f183d003106192f039990d1fb8ee151c8d7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253976"
---
# <a name="quickstart-create-kubernetes-clusters-on-azure-stack-hci-using-windows-powershell"></a>Démarrage rapide : créer des clusters Kubernetes sur Azure Stack HCI à l’aide de Windows PowerShell

> S’applique à : Azure Stack HCI

Dans ce guide de démarrage rapide, vous allez apprendre à utiliser Windows PowerShell pour créer un cluster Kubernetes sur Azure Stack HCI. Si vous préférez utiliser Windows Admin Center, consultez [Configurer Azure Kubernetes Service sur Azure Stack HCI à l’aide de Windows Admin Center](setup.md).

## <a name="before-you-begin"></a>Avant de commencer

Avant de commencer, vérifiez que :

- Vous disposez d’un cluster Azure Stack HCI doté de 2 à 4 nœuds ou d’un nœud simple Azure Stack HCI. **Nous vous recommandons de disposer d’un cluster Azure Stack HCI doté de 2 à 4 nœuds.** Si ce n’est pas le cas, suivez les instructions relatives à la création d’un cluster [ici](./system-requirements.md).
- Vous disposez d’un hôte Azure Stack Kubernetes configuré. Si ce n’est pas le cas, suivez les instructions relatives à la configuration d’un hôte [ici](./setup-powershell.md).

## <a name="step-1-create-a-kubernetes-cluster"></a>Étape 1 : Créer un cluster Kubernetes

Après avoir installé votre hôte Azure Kubernetes Service, vous êtes prêt à déployer un cluster Kubernetes.

Ouvrez PowerShell en tant qu’administrateur et exécutez la commande suivante.

   ```powershell
   New-AksHciCluster -clusterName
                    [-kubernetesVersion]
                    [-controlPlaneNodeCount]
                    [-linuxNodeCount]
                    [-windowsNodeCount]
                    [-controlPlaneVmSize]
                    [-loadBalancerVmSize]
                    [-linuxNodeVmSize]
                    [-windowsNodeVmSize]
   ```

### <a name="required-parameters"></a>Paramètres obligatoires

`-clusterName`

Nom alphanumérique de votre cluster Kubernetes.

### <a name="optional-parameters"></a>Paramètres facultatifs

`-kubernetesVersion`

Version de Kubernetes que vous souhaitez déployer. La valeur par défaut est v1.18.6. Pour obtenir la liste des tailles de versions disponibles, exécutez `Get-AksHciKubernetesVersion`.

`-controlPlaneNodeCount`

Nombre de nœuds dans votre plan de contrôle. 1 constitue la valeur par défaut.

`-linuxNodeCount`

Nombre de nœuds Linux dans votre cluster Kubernetes. 1 constitue la valeur par défaut.

`-windowsNodeCount`

Nombre de nœuds Windows dans votre cluster Kubernetes. La valeur par défaut est 0.

`-controlPlaneVmSize`

Taille de la machine virtuelle du plan de contrôle. La valeur par défaut est Standard_A2_v2. Pour obtenir la liste des tailles de machines virtuelles disponibles, exécutez `Get-AksHciVmSize`.

`-loadBalancerVmSize`

Taille de votre machine virtuelle équilibreur de charge. La valeur par défaut est Standard_A2_v2. Pour obtenir la liste des tailles de machines virtuelles disponibles, exécutez `Get-AksHciVmSize`.

`-linuxNodeVmSize`

Taille de votre machine virtuelle de nœud Linux. La valeur par défaut est Standard_K8S3_v1. Pour obtenir la liste des tailles de machines virtuelles disponibles, exécutez `Get-AksHciVmSize`.

`-windowsNodeVmSize`

Taille de votre machine virtuelle de nœud Windows. La valeur par défaut est Standard_K8S3_v1. Pour obtenir la liste des tailles de machines virtuelles disponibles, exécutez `Get-AksHciVmSize`.

### <a name="check-your-deployed-clusters"></a>Vérifier vos clusters déployés

Pour obtenir la liste de vos clusters hôtes Azure Kubernetes Service et Kubernetes déployés, exécutez la commande suivante.

```powershell
Get-AksHciCluster
```

## <a name="step-2-scale-a-kubernetes-cluster"></a>Étape 2 : Mettre à l’échelle un cluster Kubernetes

Si vous devez mettre à l’échelle votre cluster, vous pouvez modifier le nombre de nœuds de plan de contrôle, de nœuds Worker Linux ou de nœuds Worker Windows.

Pour mettre à l’échelle des nœuds de plan de contrôle, exécutez la commande suivante.

```powershell
Set-AksHciClusterNodeCount –clusterName
                           -controlPlaneNodeCount
```

Pour mettre à l’échelle des nœuds Worker, exécutez la commande suivante.

```powershell
Set-AksHciClusterNodeCount –clusterName
                           -linuxNodeCount
                           -windowsNodeCount
```

Les nœuds de plan de contrôle et les nœuds Worker doivent être mis à l’échelle indépendamment.

## <a name="step-3-upgrade-kubernetes-version"></a>Étape 3 : Mettre à niveau la version de Kubernetes

Pour afficher la version actuelle de Kubernetes que vous exécutez, exécutez la commande suivante.

```powershell
Get-AksHciKubernetesVersion
```

Pour effectuer une mise à niveau vers la prochaine version de Kubernetes, exécutez la commande suivante.

```powershell
Update-AksHciCluster -clusterName
```

Si vous souhaitez utiliser des nœuds Windows, la version minimale requise est la v1.1.8.6.

## <a name="step-4-access-your-clusters-using-kubectl"></a>Étape 4 : Accéder à vos clusters à l’aide de kubectl

Pour accéder à vos clusters Kubernetes à l’aide de kubectl, exécutez la commande suivante. Cette opération utilise le fichier kubeconfig du cluster spécifié comme fichier kubeconfig par défaut pour kubectl.

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

## <a name="delete-a-kubernetes-cluster"></a>Supprimer un cluster Kubernetes

Si vous devez supprimer un cluster Kubernetes, exécutez la commande suivante.

```powershell
Remove-AksHciCluster -clusterName
```

## <a name="get-logs"></a>Obtenir des journaux d’activité

Pour récupérer les journaux de vos pods, exécutez la commande suivante. Cette commande crée un dossier compressé de sortie appelé `akshcilogs` dans le chemin d’accès `C:\wssd\akshcilogs`.

```powershell
Get-AksHciLogs
```

Dans ce guide de démarrage rapide, vous avez appris à créer, à mettre à l’échelle et à mettre à niveau un cluster Kubernetes avec PowerShell.

## <a name="next-steps"></a>Étapes suivantes

- [Connecter vos clusters à Azure Arc pour Kubernetes](./connect-to-arc.md).
- [Déployer une application Linux sur votre cluster Kubernetes](./deploy-linux-application.md).
- [Déployer une application Windows sur votre cluster Kubernetes](./deploy-windows-application.md).
