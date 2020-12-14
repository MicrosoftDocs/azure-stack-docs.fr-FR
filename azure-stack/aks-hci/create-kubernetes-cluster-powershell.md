---
title: Démarrage rapide pour créer des clusters Kubernetes sur Azure Stack HCI à l’aide de Windows PowerShell
description: Découvrez comment créer des clusters Kubernetes sur Azure Stack HCI avec Windows PowerShell
author: jessicaguan
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: jeguan
ms.openlocfilehash: 6a215308e5eab1fa7991f912f1cd4aaff2391f5e
ms.sourcegitcommit: 0efffe1d04a54062a26d5c6ce31a417f511b9dbf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2020
ms.locfileid: "96612435"
---
# <a name="quickstart-create-kubernetes-clusters-on-azure-stack-hci-using-windows-powershell"></a>Démarrage rapide : créer des clusters Kubernetes sur Azure Stack HCI à l’aide de Windows PowerShell

> S’applique à : AKS sur Azure Stack HCI, AKS Runtime sur Windows Server 2019 Datacenter

Dans ce guide de démarrage rapide, vous allez apprendre à utiliser Windows PowerShell pour créer un cluster Kubernetes sur Azure Stack HCI. Vous allez ensuite apprendre à mettre à l’échelle votre cluster Kubernetes et à mettre à niveau la version Kubernetes de votre cluster. Si vous préférez utiliser Windows Admin Center, consultez [Configurer Azure Kubernetes Service sur Azure Stack HCI à l’aide de Windows Admin Center](setup.md).

## <a name="before-you-begin"></a>Avant de commencer

 - Assurez-vous de disposer d’un hôte Azure Stack Kubernetes configuré. Si ce n’est pas le cas, consultez [Démarrage rapide : configurer un hôte Azure Kubernetes Service sur Azure Stack HCI à l’aide de PowerShell](./setup-powershell.md).
 - Assurez-vous que vous disposez bien du module PowerShell Aks-Hci le plus récent. Si ce n’est pas le cas, consultez [Télécharger et installer le module PowerShell AksHci](./setup-powershell.md#step-1-download-and-install-the-akshci-powershell-module).

## <a name="step-1-create-a-kubernetes-cluster"></a>Étape 1 : Créer un cluster Kubernetes

Après avoir installé votre hôte Azure Kubernetes Service, vous êtes prêt à déployer un cluster Kubernetes.

Ouvrez PowerShell en tant qu’administrateur et exécutez la commande suivante.

   ```powershell
   New-AksHciCluster -clusterName <String>
                    [-kubernetesVersion <String>]
                    [-controlPlaneNodeCount <int>]
                    [-linuxNodeCount <int>]
                    [-windowsNodeCount <int>]
                    [-controlPlaneVmSize <VmSize>]
                    [-loadBalancerVmSize <VmSize>]
                    [-linuxNodeVmSize <VmSize>]
                    [-windowsNodeVmSize <VmSize>]
   ```

### <a name="example"></a>Exemple

   ```powershell
   New-AksHciCluster -clusterName mynewcluster -kubernetesVersion v1.18.8 -controlPlaneNodeCount 1 -linuxNodeCount 1 -windowsNodeCount 0 
   ```

### <a name="required-parameters"></a>Paramètres obligatoires

`-clusterName`

Nom alphanumérique de votre cluster Kubernetes.

### <a name="optional-parameters"></a>Paramètres facultatifs

`-kubernetesVersion`

Version de Kubernetes que vous souhaitez déployer. La version par défaut est la version 1.18.8. Pour obtenir la liste des tailles de versions disponibles, exécutez `Get-AksHciKubernetesVersion`.

`-controlPlaneNodeCount`

Nombre de nœuds dans votre plan de contrôle. 1 constitue la valeur par défaut.

`-linuxNodeCount`

Nombre de nœuds Linux dans votre cluster Kubernetes. 1 constitue la valeur par défaut.

`-windowsNodeCount`

Nombre de nœuds Windows dans votre cluster Kubernetes. La valeur par défaut est 0. Vous pouvez déployer des nœuds Windows uniquement si vous exécutez Kubernetes 1.18.8.

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
Set-AksHciClusterNodeCount –clusterName <String>
                           -controlPlaneNodeCount <int>
```

Pour mettre à l’échelle des nœuds Worker, exécutez la commande suivante.

```powershell
Set-AksHciClusterNodeCount –clusterName <String>
                           -linuxNodeCount <int>
                           -windowsNodeCount <int>
```

Les nœuds de plan de contrôle et les nœuds Worker doivent être mis à l’échelle indépendamment.

### <a name="example"></a>Exemple

```powershell
Set-AksHciClusterNodeCount –clusterName mynewcluster -controlPlaneNodeCount 3
```

```powershell
Set-AksHciClusterNodeCount –clusterName mynewcluster -linuxNodeCount 2 -windowsNodeCount 2 
```

## <a name="step-3-upgrade-kubernetes-version"></a>Étape 3 : Mettre à niveau la version de Kubernetes

Pour afficher la liste des versions de Kubernetes disponibles, exécutez la commande suivante.

```powershell
Get-AksHciKubernetesVersion
```

Pour effectuer une mise à jour vers la prochaine version de Kubernetes, exécutez la commande suivante.

```powershell
Update-AksHciCluster -clusterName <String>
                     [-patch]
```
Chaque version de Kubernetes a une version majeure, une version mineure et une version de correctif. Par exemple, dans la version 1.18.6, 1 est la version majeure, 18 est la version mineure, et 6 est la version du correctif. Au fil du temps, AKS-HCI prendra en charge une version majeure, trois versions mineures et deux correctifs par version mineure, pour un total de six versions prises en charge. Toutefois, pour cette version préliminaire, nous prenons en charge un total de quatre versions : 1.16.10, 1.16.15, 1.17.11, 1.18.8. 

Lorsque le paramètre `patch` est ajouté lors de l’exécution de `Update-AksHciCluster`, la commande est mise à niveau vers la version corrective suivante (le cas échéant) pour la version mineure. Lorsque la commande est exécutée sans le paramètre `patch`, la mise à niveau par défaut concerne la version mineure suivante. Pour faciliter cette opération, le tableau suivant contient toutes les expériences possibles de mise à jour :

| Version actuelle           | Version mise à jour de Kubernetes sans correctif         | Version mise à jour de Kubernetes avec correctif
| ---------------------------- | ------------ | -------------------------------- |
| v1.16.10           |     v1.17.11      | v1.16.15
| v1.16.15            | v1.17.11 | mise à niveau du module complémentaire sur place
| v1.17.11           |  v1.18.8          | mise à niveau du module complémentaire sur place
| v1.18.8             | mise à niveau du module complémentaire sur place   | mise à niveau du module complémentaire sur place

La mise à niveau du module complémentaire sur place met à jour tous les modules complémentaires Kubernetes comme CSI que AKS-HCI gère pour vous. Cette mise à niveau ne modifie pas la version du système d’exploitation du nœud. Elle ne modifie pas non plus la version de Kubernetes.

### <a name="example---upgrade-kubernetes-version-to-the-next-minor-version"></a>Exemple : mise à niveau de la version Kubernetes vers la version mineure suivante

```powershell
Update-AksHciCluster -clusterName mynewcluster
```

### <a name="example---upgrade-kubernetes-version-to-the-next-patch-version"></a>Exemple : mise à niveau de la version Kubernetes vers la version corrective suivante

```powershell
Update-AksHciCluster -clusterName mynewcluster -patch
```


## <a name="step-4-access-your-clusters-using-kubectl"></a>Étape 4 : Accéder à vos clusters à l’aide de kubectl

Pour accéder à vos clusters Kubernetes à l’aide de kubectl, exécutez la commande suivante. Cette opération utilise le fichier kubeconfig du cluster spécifié comme fichier kubeconfig par défaut pour kubectl.

```powershell
Get-AksHciCredential -clusterName <String>
                     [-outputLocation <String>]
```

### <a name="example"></a>Exemple

```powershell
Get-AksHciCredential -clusterName mynewcluster
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

### <a name="example"></a>Exemple

```powershell
Remove-AksHciCluster -clusterName mynewcluster
```

## <a name="get-logs"></a>Obtenir des journaux d’activité

Pour récupérer les journaux de vos pods, exécutez la commande suivante. Cette commande crée un dossier compressé de sortie appelé `akshcilogs` dans le chemin d’accès `C:\wssd\akshcilogs`.

```powershell
Get-AksHciLogs
```

Dans ce guide de démarrage rapide, vous avez appris à créer, mettre à l’échelle et mettre à niveau la version Kubernetes d’un cluster utilisant PowerShell.

## <a name="next-steps"></a>Étapes suivantes

- [Connecter vos clusters à Azure Arc pour Kubernetes](./connect-to-arc.md).
- [Déployer une application Linux sur votre cluster Kubernetes](./deploy-linux-application.md).
- [Déployer une application Windows sur votre cluster Kubernetes](./deploy-windows-application.md).
