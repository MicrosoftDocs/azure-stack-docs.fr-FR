---
title: Gérer des clusters Azure Stack HCI avec PowerShell
description: Découvrir comment gérer des clusters sur Azure Stack HCI avec PowerShell
author: v-dasis
ms.topic: article
ms.date: 05/21/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 922a6188c1529d3461288ac28ff542db6fa9a527
ms.sourcegitcommit: f2203f070c17e19ba1a41d681662bac3ab50ed12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83855703"
---
# <a name="manage-azure-stack-hci-clusters-using-powershell"></a>Gérer des clusters Azure Stack HCI avec PowerShell

> S’applique à Windows Server 2019

Windows PowerShell peut être utilisé pour gérer des ressources et configurer des fonctionnalités sur vos clusters Azure Stack HCI.

En règle générale, vous gérez les clusters à partir d’un ordinateur distant exécutant Windows 10, plutôt que sur un serveur hôte dans un cluster. Cet ordinateur distant est appelé ordinateur de gestion.

> [!NOTE]
> Quand vous exécutez des commandes PowerShell à partir d’un ordinateur de gestion, incluez le paramètre `-Name` ou `-Cluster` dans le nom du cluster que vous gérez. De plus, vous allez avoir besoin de spécifier le nom de domaine complet (FQDN) quand vous utilisez le paramètre `-ComputerName` pour un nœud de serveur.

Pour plus d’informations sur la gestion des clusters avec PowerShell, consultez les [informations de référence sur FailoverCluster](https://docs.microsoft.com/powershell/module/failoverclusters/?view=win10-ps).

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

Utilisez les applets de commande `Start-Cluster` et `Stop-Cluster` pour ajouter ou supprimer un nœud de serveur pour votre cluster. Pour obtenir des exemples et plus d’informations, consultez la documentation de référence sur [Start-Cluster](https://docs.microsoft.com/powershell/module/failoverclusters/start-cluster?view=win10-ps) et [Stop-Cluster](https://docs.microsoft.com/powershell/module/failoverclusters/stop-cluster?view=win10-ps).

Démarre le service Cluster sur tous les nœuds de serveur du cluster sur lesquels il n’a pas encore démarré :

```powershell
Start-Cluster -Name Cluster1
```

Cet exemple arrête le service Cluster sur tous les nœuds du cluster nommé Cluster1, ce qui va arrêter tous les services et applications configurés dans le cluster :

```powershell
Stop-Cluster -Name Cluster1
```

## <a name="add-or-remove-a-server"></a>Ajouter ou supprimer un serveur

Utilisez les applets de commande `Add-ClusterNode` et `Remove-ClusterNode` pour ajouter ou supprimer un nœud de serveur pour votre cluster. Pour obtenir des exemples et plus d’informations, consultez la documentation de référence sur [Add-ClusterNode](https://docs.microsoft.com/powershell/module/failoverclusters/add-clusternode?view=win10-ps) et [Remove-ClusterNode](https://docs.microsoft.com/powershell/module/failoverclusters/remove-clusternode?view=win10-ps).

Cet exemple ajoute un serveur nommé Node4 à un cluster nommé Cluster1. Vérifiez d’abord que le serveur est sous tension et connecté au réseau du cluster.

```powershell
Add-ClusterNode -Cluster Cluster1 -Name Node4
```

Cet exemple supprime le nœud nommé Node4 du cluster Cluster1 :

```powershell
Remove-ClusterNode -Cluster Cluster1 -Name Node4
```

## <a name="set-quorum-options"></a>Définir les options de quorum

Utilisez l’applet de commande `Set-ClusterQuorum` pour définir les options de quorum du cluster. Pour obtenir des exemples et plus d’informations, consultez la documentation de référence sur [Set-ClusterQuorum](https://docs.microsoft.com/powershell/module/failoverclusters/set-clusterquorum?view=win10-ps).

Cet exemple modifie la configuration du quorum pour utiliser un témoin cloud sur le cluster nommé Cluster1 :

```powershell
Set-ClusterQuorum -Cluster Cluster1 -CloudWitness
```

Cet exemple remplace la configuration du quorum par Nœud et partage de fichiers majoritaires sur le cluster nommé Cluster1, en utilisant la ressource du disque située sur \\fileserver\fsw pour le témoin de partage de fichiers.

```powershell
Set-ClusterQuorum -Cluster Cluster1 -NodeAndFileShareMajority \\fileserver\fsw
```

## <a name="enable-storage-spaces-direct"></a>Activer les espaces de stockage direct

Utilisez l’applet de commande `Enable-ClusterStorageSpacesDirect` pour activer des espaces de stockage direct sur le cluster. Pour obtenir des exemples et plus d’informations, consultez la documentation de référence sur [Enable-ClusterStorageSpacesDirect](https://docs.microsoft.com/powershell/module/failoverclusters/enable-clusterstoragespacesdirect?view=win10-ps).

Cet exemple active les espaces de stockage direct sur Server1 :

```powershell
Enable-ClusterStorageSpacesDirect -CimSession Cluster1
```

## <a name="configure-a-hyper-v-host"></a>Configurer un hôte Hyper-V

Utilisez l’applet de commande `Set-VMHost` pour configurer divers paramètres d’hôte Hyper-V, comme les chemins des disques durs virtuels et des machines virtuelles, les migrations dynamiques, les migrations de stockage, l’authentification, la répartition NUMA, etc. Pour obtenir des exemples et plus d’informations, consultez la documentation de référence sur [Enable-ClusterStorageSpacesDirect](https://docs.microsoft.com/powershell/module/hyper-v/Set-VMHost?view=win10-ps).

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

## <a name="validate-a-cluster"></a>Valider un cluster

Utilisez l’applet de commande `Test-Cluster` pour exécuter des tests de validation sur un cluster. Pour obtenir des exemples et plus d’informations, consultez la documentation de référence sur [Test-Cluster](https://docs.microsoft.com/powershell/module/failoverclusters/test-cluster?view=win10-ps).

Cet exemple exécute tous les tests de validation de cluster applicables sur un cluster nommé Cluster1 :

```powershell
Test-Cluster -Cluster Cluster1
```

Cet exemple liste les noms de tous les tests et de toutes les catégories de la validation du cluster. Spécifiez ces noms de test avec les paramètres *Ignore* ou *Include* pour exécuter des tests spécifiques :


```powershell
Test-Cluster -Cluster Cluster1 -List
Category                                DisplayName                             Description
--------                                -----------                             -----------
Cluster Configuration                   List Cluster Core Groups                List information about the available...
Cluster Configuration                   List Cluster Network Information        List cluster-specific network settin...
Cluster Configuration                   List Cluster Resources                  List the resources that are configur...
Cluster Configuration                   List Cluster Volumes                    List information for the volumes in ...
Cluster Configuration                   List Clustered Roles                    List information about clustered roles.
Cluster Configuration                   Validate Quorum Configuration           Validate that the current quorum con...
Cluster Configuration                   Validate Resource Status                Validate that cluster resources are ...
Cluster Configuration                   Validate Service Principal Name         Validate that a Service Principal Na...
Cluster Configuration                   Validate Volume Consistency             If any volumes are flagged as incons...
Hyper-V Configuration                   List Information About Servers Runni... List Hyper-V related information on ...
Hyper-V Configuration                   Validate Compatibility of Virtual Fi... Validate that all specified nodes sh...
Hyper-V Configuration                   Validate Hyper-V Memory Resource Poo... Validate that all specified nodes sh...
Hyper-V Configuration                   Validate Hyper-V Network Resource Po... Validate that all specified nodes sh...
Hyper-V Configuration                   Validate Hyper-V Processor Resource ... Validate that all specified nodes sh...
Hyper-V Configuration                   Validate Hyper-V Role Installed         Validate that all the nodes have the...
Hyper-V Configuration                   Validate Hyper-V Storage Resource Po... Validate that all specified nodes sh...
Hyper-V Configuration                   Validate Matching Processor Manufact... Validate that all specified nodes sh...
Hyper-V Configuration                   List Hyper-V Virtual Machine Informa... List Hyper-V virtual machine informa...
Hyper-V Configuration                   Validate Hyper-V Integration Service... Validate that the Hyper-V integratio...
Hyper-V Configuration                   Validate Hyper-V Virtual Machine Net... Validate that all virtual machines o...
Hyper-V Configuration                   Validate Hyper-V Virtual Machine Sto... Validate that all virtual machines o...
Inventory                               List Fibre Channel Host Bus Adapters    List Fibre Channel host bus adapters...
Inventory                               List iSCSI Host Bus Adapters            List iSCSI host bus adapters on each...
Inventory                               List SAS Host Bus Adapters              List Serial Attached SCSI (SAS) host...
Inventory                               List BIOS Information                   List BIOS information from each node...
Inventory                               List Environment Variables              List environment variables set on ea...
Inventory                               List Memory Information                 List memory information for each node.
Inventory                               List Operating System Information       List information about the operating...
Inventory                               List Plug and Play Devices              List Plug and Play devices on each n...
Inventory                               List Running Processes                  List the running processes on each n...
Inventory                               List Services Information               List information about the services ...
Inventory                               List Software Updates                   List software updates that have been...
Inventory                               List System Drivers                     List the system drivers on each node.
Inventory                               List System Information                 List system information such as comp...
Inventory                               List Unsigned Drivers                   List the unsigned drivers on each node.
Network                                 List Network Binding Order              List the order in which networks are...
Network                                 Validate Cluster Network Configuration  Validate the cluster networks that w...
Network                                 Validate IP Configuration               Validate that IP addresses are uniqu...
Network                                 Validate Multiple Subnet Properties     For clusters using multiple subnets,...
Network                                 Validate Network Communication          Validate that servers can communicat...
Network                                 Validate Windows Firewall Configuration Validate that the Windows Firewall i...
Storage                                 List Disks                              List all disks visible to one or mor...
Storage                                 List Potential Cluster Disks            List disks that will be validated fo...
Storage                                 Validate CSV Network Bindings           Validate that network bindings requi...
Storage                                 Validate CSV Settings                   Validate that settings and configura...
Storage                                 Validate Disk Access Latency            Validate acceptable latency for disk...
Storage                                 Validate Disk Arbitration               Validate that a node that owns a dis...
Storage                                 Validate Disk Failover                  Validate that a disk can fail over s...
Storage                                 Validate File System                    Validate that the file system on dis...
Storage                                 Validate Microsoft MPIO-based disks     Validate that disks that use Microso...
Storage                                 Validate Multiple Arbitration           Validate that in a multiple-node arb...
Storage                                 Validate SCSI device Vital Product D... Validate uniqueness of inquiry data ...
Storage                                 Validate SCSI-3 Persistent Reservation  Validate that storage supports the S...
Storage                                 Validate Simultaneous Failover          Validate that disks can fail over si...
System Configuration                    Validate Active Directory Configuration Validate that all the nodes have the...
System Configuration                    Validate All Drivers Signed             Validate that tested servers contain...
System Configuration                    Validate Cluster Service and Driver ... Validate startup settings used by se...
System Configuration                    Validate Memory Dump Settings           Validate that none of the nodes curr...
System Configuration                    Validate Operating System Edition       Validate that all tested servers are...
System Configuration                    Validate Operating System Installati... Validate that the operating systems ...
System Configuration                    Validate Operating System Version       Validate that the operating systems ...
System Configuration                    Validate Required Services              Validate that services required for ...
System Configuration                    Validate Same Processor Architecture    Validate that all servers run as 64-...
System Configuration                    Validate Service Pack Levels            Validate that all servers with same ...
System Configuration                    Validate Software Update Levels         Validate that all tested servers hav...
System Configuration                    Validate System Drive Variable          Validate that all nodes have the sam...
```

## <a name="remove-cluster-and-resources"></a>Supprimer le cluster et les ressources

Utilisez l’applet de commande `Remove-ClusterResource` pour supprimer une ressource ou toutes les ressources d’un cluster. Pour obtenir des exemples et plus d’informations, consultez la documentation de référence sur [Remove-ClusterResource](https://docs.microsoft.com/powershell/module/failoverclusters/remove-clusterresource?view=win10-ps).

> [!NOTE]
> Vous devrez activer temporairement l’authentification CredSSP (Credential Security Service Provider) pour supprimer un cluster. Pour plus d’informations, consultez [Enable-WSManCredSSP](https://docs.microsoft.com/powershell/module/microsoft.wsman.management/enable-wsmancredssp?view=powershell-7).

L’exemple suivant supprime les ressources de cluster selon leur nom sur le cluster Cluster1 :

```powershell
Remove-ClusterResource -Cluster Cluster1 -Name "Cluster Disk 4"
```

Cet exemple supprime entièrement le cluster Cluster1 à l’aide de l’applet de commande `Remove-Cluster` :

```powershell
Remove-Cluster -Cluster Cluster1
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment gérer vos clusters à l’aide de Windows Admin Center. Consultez [Gérer des clusters sur Azure Stack HCI avec Windows Admin Center](cluster.md).