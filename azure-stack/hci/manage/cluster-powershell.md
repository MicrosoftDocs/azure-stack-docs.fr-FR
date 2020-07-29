---
title: Gérer des clusters Azure Stack HCI avec PowerShell
description: Découvrir comment gérer des clusters sur Azure Stack HCI avec PowerShell
author: v-dasis
ms.topic: how-to
ms.date: 07/21/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 88f6ace707d14466de481f60133875968b8edce0
ms.sourcegitcommit: a15a0f955bac922cebb7bf90a72384fd84ddfe56
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86947433"
---
# <a name="manage-azure-stack-hci-clusters-using-powershell"></a>Gérer des clusters Azure Stack HCI avec PowerShell

> S’applique à Azure Stack HCI, version 20H2 ; Windows Server 2019

Windows PowerShell peut être utilisé pour gérer des ressources et configurer des fonctionnalités sur vos clusters Azure Stack HCI.

Vous gérez les clusters à partir d’un ordinateur distant plutôt que sur un serveur hôte dans un cluster. Cet ordinateur distant est appelé ordinateur de gestion.

> [!NOTE]
> Quand vous exécutez des commandes PowerShell à partir d’un ordinateur de gestion, incluez le paramètre `-Name` ou `-Cluster` dans le nom du cluster que vous gérez. De plus, vous allez avoir besoin de spécifier le nom de domaine complet (FQDN) quand vous utilisez le paramètre `-ComputerName` pour un nœud de serveur.

Pour plus d’informations sur la gestion des clusters avec PowerShell, consultez les [informations de référence sur FailoverCluster](/powershell/module/failoverclusters/?view=win10-ps).

## <a name="using-windows-powershell"></a>Utilisation de Windows PowerShell

Windows PowerShell est utilisé pour effectuer toutes les tâches mentionnées dans cet article. Il est recommandé d’épingler l’application à votre barre des tâches par souci pratique.

Si les applets de commande suivantes ne sont pas disponibles dans votre session PowerShell, vous devrez peut-être ajouter la fonctionnalité Module `Failover Cluster` pour Windows PowerShell à l’aide de la commande PowerShell suivante : `Add-WindowsFeature RSAT-Clustering-PowerShell`.

> [!NOTE]
> À compter de la mise à jour d’octobre 2018 de Windows 10, la fonctionnalité Outils d’administration de serveur distant est incluse en tant qu’ensemble de « Fonctionnalités à la demande » directement dans Windows 10. Accédez simplement à **Paramètres > Applications > Applications et fonctionnalités > Fonctionnalités facultatives > Ajouter une fonctionnalité > Outils d’administration de serveur distant : Outils de clustering avec basculement**, puis sélectionnez **Installer**. Pour voir la progression de l’installation, cliquez sur le bouton Précédent pour en afficher l’état dans la page « Gérer les fonctionnalités facultatives ». La fonctionnalité installée est conservée à l’issue des mises à niveau des versions de Windows 10.

## <a name="view-cluster-settings-and-resources"></a>Afficher les paramètres et les ressources du cluster

Obtient des informations sur un cluster nommé Cluster1 :

```powershell
Get-Cluster -Name Cluster1
```
Obtient des informations sur un ou plusieurs nœuds, ou serveurs, inclus dans Cluster1 :

```powershell
Get-ClusterNode -Cluster Cluster1
```

Pour identifier les fonctionnalités Windows installées sur un nœud de cluster, utilisez l’applet de commande `Get-WindowsFeature`. Par exemple :

```powershell
Get-WindowsFeature -ComputerName Server1
```

Pour voir les cartes réseau et leurs propriétés, comme leur nom, leurs adresses IPv4 et leur ID de réseau local virtuel (VLAN) :

```powershell
Get-NetAdapter -CimSession Server1 | Where Status -Eq "Up" | Sort InterfaceAlias | Format-Table Name, InterfaceDescription, Status, LinkSpeed, VLANID, MacAddress
```

Pour voir les commutateurs virtuels Hyper-V et le mode d’association des cartes réseau physiques :

```powershell
Get-VMSwitch -ComputerName Server1
```

Pour voir les cartes réseau virtuelles hôtes :

```powershell
Get-VMNetworkAdapter -ComputerName Server1
```

Pour voir si des espaces de stockage direct sont activés :

```powershell
Get-CimSession -ComputerName Server1 | Get-ClusterStorageSpacesDirect
```

## <a name="start-or-stop-a-cluster"></a>Démarrer ou arrêter un cluster

Utilisez les applets de commande `Start-Cluster` et `Stop-Cluster` pour ajouter ou supprimer un nœud de serveur pour votre cluster. Pour obtenir des exemples et plus d’informations, consultez la documentation de référence sur [Start-Cluster](/powershell/module/failoverclusters/start-cluster?view=win10-ps) et [Stop-Cluster](/powershell/module/failoverclusters/stop-cluster?view=win10-ps).

Démarre le service Cluster sur tous les nœuds de serveur du cluster sur lesquels il n’a pas encore démarré :

```powershell
Start-Cluster -Name Cluster1
```

Cet exemple arrête le service Cluster sur tous les nœuds du cluster nommé Cluster1, ce qui va arrêter tous les services et applications configurés dans le cluster :

```powershell
Stop-Cluster -Name Cluster1
```

## <a name="add-or-remove-a-server"></a>Ajouter ou supprimer un serveur

Utilisez les applets de commande `Add-ClusterNode` et `Remove-ClusterNode` pour ajouter ou supprimer un nœud de serveur pour votre cluster. Pour obtenir des exemples et plus d’informations, consultez la documentation de référence sur [Add-ClusterNode](/powershell/module/failoverclusters/add-clusternode?view=win10-ps) et [Remove-ClusterNode](/powershell/module/failoverclusters/remove-clusternode?view=win10-ps).

Cet exemple ajoute un serveur nommé Node4 à un cluster nommé Cluster1. Vérifiez d’abord que le serveur est en cours d’exécution et qu’il est connecté au réseau du cluster.

```powershell
Add-ClusterNode -Cluster Cluster1 -Name Node4
```

Cet exemple supprime le nœud nommé Node4 du cluster Cluster1 :

```powershell
Remove-ClusterNode -Cluster Cluster1 -Name Node4
```

## <a name="setup-the-cluster-witness"></a>Configurer le témoin de cluster

Utilisez l’applet de commande `Set-ClusterQuorum` afin de définir des options de témoin de quorum pour le cluster. Pour obtenir des exemples et plus d’informations, consultez la documentation de référence sur [Set-ClusterQuorum](/powershell/module/failoverclusters/set-clusterquorum?view=win10-ps).

Cet exemple modifie la configuration du quorum pour utiliser un témoin cloud sur le cluster nommé Cluster1 :

```powershell
Set-ClusterQuorum -Cluster Cluster1 -CloudWitness
```

Cet exemple remplace la configuration du quorum par Nœud et partage de fichiers majoritaires sur le cluster nommé Cluster1, en utilisant la ressource du disque située sur \\fileserver\fsw pour le témoin de partage de fichiers.

```powershell
Set-ClusterQuorum -Cluster Cluster1 -NodeAndFileShareMajority \\fileserver\fsw
```

## <a name="enable-storage-spaces-direct"></a>Activer les espaces de stockage direct

Utilisez l’applet de commande `Enable-ClusterStorageSpacesDirect` pour activer des espaces de stockage direct sur le cluster. Pour obtenir des exemples et plus d’informations, consultez la documentation de référence sur [Enable-ClusterStorageSpacesDirect](/powershell/module/failoverclusters/enable-clusterstoragespacesdirect?view=win10-ps).

Cet exemple active les espaces de stockage direct sur Server1 :

```powershell
Enable-ClusterStorageSpacesDirect -CimSession Cluster1
```

## <a name="configure-a-hyper-v-host"></a>Configurer un hôte Hyper-V

Utilisez l’applet de commande `Set-VMHost` pour configurer divers paramètres d’hôte Hyper-V, comme les chemins des disques durs virtuels et des machines virtuelles, les migrations dynamiques, les migrations de stockage, l’authentification, la répartition NUMA, etc. Pour obtenir des exemples et plus d’informations, consultez la documentation de référence sur [Enable-ClusterStorageSpacesDirect](/powershell/module/hyper-v/set-vmhost?view=win10-ps).

Cet exemple spécifie de nouveaux emplacements par défaut pour les disques durs virtuels et les machines virtuelles sur le serveur hôte nommé Server1 :

```powershell
Set-VMHost -ComputerName Server1 -VirtualHardDiskPath "C:\Hyper-V\Virtual Hard Disks" -VirtualMachinePath "C:\Hyper-V\Configuration Files"
```

Cet exemple configure le serveur hôte Server1 de manière à autoriser 10 migrations dynamiques et migrations de stockage simultanées :

```powershell
Set-VMHost -ComputerName Server1 -MaximumVirtualMachineMigrations 10 -MaximumStorageMigrations 10
```

Cet exemple configure le serveur hôte Server1 de manière à utiliser Kerberos pour authentifier les migrations dynamiques entrantes :

```powershell
Set-VMHost -ComputerName Server1 -VirtualMachineMigrationAuthenticationType Kerberos
```

## <a name="remove-cluster-and-resources"></a>Supprimer le cluster et les ressources

Utilisez l’applet de commande `Remove-ClusterResource` pour supprimer une ressource ou toutes les ressources d’un cluster. Pour obtenir des exemples et plus d’informations, consultez la documentation de référence sur [Remove-ClusterResource](/powershell/module/failoverclusters/remove-clusterresource?view=win10-ps).

> [!NOTE]
> Vous devrez activer temporairement l’authentification CredSSP (Credential Security Service Provider) pour supprimer un cluster. Pour plus d’informations, consultez [Enable-WSManCredSSP](/powershell/module/microsoft.wsman.management/enable-wsmancredssp?view=powershell-7).

L’exemple suivant supprime les ressources de cluster selon leur nom sur le cluster Cluster1 :

```powershell
Remove-ClusterResource -Cluster Cluster1 -Name "Cluster Disk 4"
```

Cet exemple supprime entièrement le cluster Cluster1 à l’aide de l’applet de commande `Remove-Cluster` :

```powershell
Remove-Cluster -Cluster Cluster1
```

## <a name="next-steps"></a>Étapes suivantes

- Vous devez ensuite valider le cluster après avoir apporté des modifications. Pour plus d’informations, consultez [Valider un cluster Azure Stack HCI](../deploy/validate.md).
- Découvrez comment gérer vos clusters à l’aide de Windows Admin Center. Consultez [Gérer des clusters sur Azure Stack HCI avec Windows Admin Center](cluster.md).
