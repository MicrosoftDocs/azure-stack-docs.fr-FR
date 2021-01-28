---
title: Migrer vers Azure Stack HCI sur le même matériel
description: Découvrir comment migrer un cluster vers Azure Stack HCI sur le même matériel
author: v-dasis
ms.topic: how-to
ms.date: 01/22/2021
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 35c1de7da10fbecbf6b861a23cdebb752502ca44
ms.sourcegitcommit: e772df8ac78c86d834a68d1a8be83b7f738019b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98772269"
---
# <a name="migrate-to-azure-stack-hci-on-same-hardware"></a>Migrer vers Azure Stack HCI sur le même matériel

> S’applique à Azure Stack HCI version 20H2, Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2008 R2

Cette rubrique explique comment migrer un cluster Windows Server 2016 ou Windows Server 2019 vers Azure Stack HCI à l’aide de votre matériel serveur existant. Ce processus installe le nouveau système d’exploitation Azure Stack HCI, conserve vos paramètres de cluster et votre stockage existants, et importe vos machines virtuelles.

Le diagramme suivant illustre la migration de votre cluster Windows Server sur place à l’aide du même matériel de serveur. Après l’arrêt de votre cluster, Azure Stack HCI est installé, le stockage est rattaché et vos machines virtuelles sont importées et rendues hautement disponibles (HA).

:::image type="content" source="media/migrate/migrate-cluster-same-hardware.png" alt-text="Migrer un cluster vers Azure Stack HCI sur le même matériel" lightbox="media/migrate/migrate-cluster-same-hardware.png":::

Pour migrer vos machines virtuelles vers un nouveau matériel Azure Stack HCI, consultez [Migrer vers Azure Stack HCI sur du nouveau matériel](migrate-cluster-new-hardware.md).

> [!NOTE]
> La migration de clusters étendus n’est pas abordée dans cet article.

## <a name="before-you-begin"></a>Avant de commencer

Avant d’entreprendre la migration, vous devez prendre en considération plusieurs éléments et exigences :

- Toutes les commandes Windows PowerShell doivent être exécutées en tant qu’administrateur.

- Vous devez disposer d’informations d’identification de domaine avec des autorisations d’administrateur pour Azure Stack HCI.

- Sauvegardez toutes les machines virtuelles sur votre cluster source. Effectuez une sauvegarde cohérente en cas d’incident de l’ensemble des applications et des données et une sauvegarde cohérente du point de vue des applications de toutes les bases de données.  Pour effectuer une sauvegarde sur Azure, consultez [Utiliser la Sauvegarde Azure](../manage/use-azure-backup.md).

- Collectez l’inventaire et la configuration de tous les nœuds de cluster, le nommage des clusters, la configuration réseau, la résilience et de la capacité du volume partagé de cluster (CSV) et le témoin de quorum.

- Arrêtez vos machines virtuelles de cluster, vos volumes partagés de cluster hors connexion, vos pools de stockage hors connexion et le service de cluster.
- Désactivez l’objet du nom du cluster (CNO) (il est réutilisé ultérieurement) et :
    - Vérifiez que le CNO dispose de droits de création d’objet sur sa propre unité d’organisation (UO)
    - Vérifiez que la stratégie héritée de blocage a été définie sur l’unité d’organisation
    - Définissez la stratégie nécessaire pour Azure Stack HCI sur cette unité d’organisation

## <a name="vm-version-support-and-update"></a>Prise en charge et mise à jour des versions de machines virtuelles

Le tableau suivant liste les versions de système d’exploitation Windows Server prises en charge et les versions de leurs machines virtuelles pour la migration sur place sur le même matériel.

Quelle que soit la version du système d’exploitation sur lequel s’exécute une machine virtuelle, la version minimale de machine virtuelle prise en charge pour la migration vers Azure Stack HCI est la version 5.0. Par conséquent, toute machine virtuelle exécutant la version 2.0, 3.0 ou 4.0 sur votre cluster Windows Server 2016 ou Windows Server 2019 doit être mise à jour vers la version 5.0 avant la migration.

|Version du SE|Version de la machine virtuelle|
|---|---|
|Windows Server 2008 SP1|2.0|
|Windows Server 2008 R2|3.0|
|Windows Server 2012|4.0|
|Windows Server 2012 R2|5.0|
|Windows Server 2016|8.0|
|Windows Server 2019|9.0|
|Azure Stack HCI|9.0|

Pour les machines virtuelles sur les clusters Windows Server 2008 SP1, Windows Server 2008 R2-SP1 et Windows 2012, la migration directe vers Azure Stack HCI n’est pas prise en charge. Dans ce cas-là, deux options s’offrent à vous :

- Migrez d’abord ces machines virtuelles vers Windows Server 2012 R2, Windows Server 2016 ou Windows Server 2019, mettez à jour la version de la machine virtuelle, puis commencez le processus de migration.

- Utilisez Robocopy pour copier tous les disques durs virtuels (VHD) des machines virtuelles sur Azure Stack HCI. Créez ensuite des machines virtuelles, puis attachez les VHD copiés à leurs machines virtuelles respectives situées dans Azure Stack HCI. Cela a pour effet de contourner la limitation liée à la version des machines virtuelles pour ce qui est des machines virtuelles plus anciennes.

## <a name="updating-the-vm-version"></a>Mise à jour de la version des machines virtuelles

Utilisez la commande suivante pour afficher toutes les versions de machines virtuelles sur un même serveur :

```powershell
Get-VM * | Format-Table Name,Version
```

Pour afficher toutes les versions de machines virtuelles sur tous les nœuds de votre cluster Windows Server :

```powershell
Get-VM –ComputerName (Get-ClusterNode)
```

Pour mettre à jour toutes les machines virtuelles vers la dernière version sur tous les nœuds Windows Server :

```powershell
Get-VM –ComputerName (Get-ClusterNode) | Update-VMVersion -Force
```

## <a name="updating-the-servers-and-cluster"></a>Mise à jour des serveurs et du cluster

La migration consiste à exécuter le programme d’installation d’Azure Stack HCI sur votre déploiement de Windows Server pour une nouvelle installation du système d’exploitation avec vos machines virtuelles et votre stockage intacts. Cela remplace le système d’exploitation actuel par Azure Stack HCI. Pour obtenir des informations détaillées, consultez [Déployer le système d’exploitation Azure Stack HCI](operating-system.md). Ensuite, vous créez un cluster Azure Stack HCI, vous rattachez votre stockage et vous importez les machines virtuelles dessus.

1. Arrêtez vos machines virtuelles de cluster existantes, vos volumes partagés de cluster hors connexion existants, vos pools de stockage hors connexion existants et le service de cluster.

1. Accédez à l’emplacement où vous avez téléchargé Azure Stack HCI, puis exécutez le programme d’installation d’Azure Stack HCI sur chaque nœud Windows Server.

1. Pendant l’installation, sélectionnez **Personnalisée : installer la version la plus récente d’Azure Stack HCI uniquement (avancé)** . Répétez cette opération pour chaque serveur.

1. Créez le nouveau cluster Azure Stack HCI. Pour ce faire, vous pouvez utiliser Windows Admin Center ou Windows PowerShell, comme décrit ci-dessous.  

> [!IMPORTANT]
> Le nom du commutateur virtuel Hyper-V (`VMSwitch`) doit être le même que celui capturé dans l’inventaire de la configuration de cluster. Vérifiez que le nom du commutateur virtuel utilisé sur le cluster Azure Stack HCI correspond au nom du commutateur virtuel source d’origine avant l’importation des machines virtuelles.

> [!NOTE]
> Vous devez inscrire le cluster Azure Stack HCI auprès d’Azure avant de pouvoir créer des machines virtuelles sur celui-ci. Pour plus d’informations, consultez [S’inscrire auprès d’Azure](register-with-azure.md).

### <a name="using-windows-admin-center"></a>Utilisation de Windows Admin Center

Si vous utilisez Windows Admin Center pour créer le cluster Azure Stack HCI, l’Assistant Création d’un cluster installe automatiquement tous les rôles et fonctionnalités nécessaires sur chaque nœud de serveur.

Pour obtenir des informations détaillées sur la façon de créer le cluster, consultez [Créer un cluster Azure Stack HCI en utilisant Windows Admin Center](create-cluster.md).

> [!IMPORTANT]
> Ignorez l’étape **4.1 Nettoyage des lecteurs** dans l’Assistant Création d’un cluster. Sinon, vous allez supprimer vos machines virtuelles et votre stockage existants.

1. Démarrez l’Assistant Création d’un cluster. Quand vous accédez à l’**étape 4 : Stockage** :

1. Ignorez l’étape **4.1 Nettoyage des lecteurs**. Ne le faites pas.

1. Quittez l’Assistant.

1. Ouvrez PowerShell, puis exécutez l’applet de commande suivante pour créer l’ID de `Storagesubsystem Object`, redécouvrez tous les boîtiers de stockage, puis affectez des numéros de lecteur SES :

    ```powershell
    Enable-ClusterS2D -Verbose
    ```

    Si vous effectuez une migration à partir de Windows Server 2016, cela crée également un volume ReFS `ClusterperformanceHistory` et l’affecte au groupe de ressources de cluster SDDC.

    Si vous effectuez une migration à partir de Windows Server 2019, cela ajoute également le volume ReFS `ClusterperformanceHistory` existant et l’affecte au groupe de ressources de cluster SDDC.

1. Revenez à l’Assistant. À l’étape **4.2 Vérifier les lecteurs**, vérifiez que tous les lecteurs sont listés sans avertissements ou erreurs.

1. Effectuez toutes les étapes de l'Assistant.

### <a name="using-windows-powershell"></a>Utilisation de Windows PowerShell

Si vous utilisez PowerShell pour créer le cluster Azure Stack HCI, les fonctionnalités et rôles suivants doivent être installés sur chaque nœud de clusters Azure Stack HCI à l’aide de cet applet de commande :

```powershell
Install-WindowsFeature -Name Hyper-V, Failover-Clustering, FS-Data-Deduplication, Bitlocker, Data-Center-Bridging, RSAT-AD-PowerShell -IncludeAllSubFeature -IncludeManagementTools -Verbose
```

Pour plus d’informations sur la façon de créer le cluster à l’aide de PowerShell, consultez [Créer un cluster Azure Stack HCI en utilisant Windows PowerShell](create-cluster-powershell.md).

> [!NOTE]
> Réutilisez le même nom que celui de l’objet du nom du cluster désactivé précédemment.

1. Exécutez l’applet de commande suivante pour créer le cluster :

    ```powershell
    New-cluster –name "clustername" –node Server01,Server02 –staticaddress xx.xx.xx.xx –nostorage
    ```

1. Exécutez l’applet de commande suivante pour créer l’ID de `Storagesubsystem Object`, redécouvrez tous les boîtiers de stockage, puis affectez des numéros de lecteur SES :

    ```powershell
    Enable-ClusterS2D -Verbose
    ```

1. Si vous effectuez une migration à partir de Windows Server 2016, cela crée également un volume ReFS `ClusterperformanceHistory` et l’affecte au groupe de ressources de cluster SDDC.

    > [!NOTE]
    > Si un pool de stockage affiche des erreurs de disques minoritaires (visibles dans le Gestionnaire de cluster), réexécutez l’applet de commande `Enable-ClusterS2D -verbose`.

1. À l’aide du Gestionnaire de cluster, activez chaque volume CSV, à l’exception du volume `ClusterperformanceHistory`, qui est un volume ReFS (vérifiez qu’il ne s’agit pas d’un volume ReFS CSV).

1. Si vous effectuez une migration à partir de Windows Server 2019, réexécutez l’applet de commande `Enable-ClusterS2D -verbose`. Cette opération associe le volume ReFS `ClusterperformanceHistory` au groupe de ressources de cluster SDDC.

1. Déterminez le nom et la version de votre pool de stockage actuel en exécutant la commande suivante :

    ```powershell
    Get-StoragePool | ? IsPrimordial -eq $false | ft FriendlyName,Version
    ```

1. Déterminez maintenant le nom et la version de votre nouveau pool de stockage :

    ```powershell
    Get-StoragePool | ? IsPrimordial -eq $false | ft FriendlyName,Version
    ```

1. Créez le témoin de quorum. Pour plus d’informations sur la façon de procéder, consultez [Configurer un témoin de cluster](../manage/witness.md).

1. Vérifiez que les travaux de réparation de stockage sont terminés à l’aide de la commande suivante :

    ```powershell
    Get-StorageJob
    ```

    > [!NOTE]
    >Cela peut prendre beaucoup de temps en fonction du nombre de machines virtuelles en cours d’exécution pendant la mise à niveau.

1. Vérifiez que tous les disques sont intègres :

    ```powershell
    Get-VirtualDisk
    ```

1. Déterminez la version du nœud de cluster, qui affiche `ClusterFunctionalLevel` et `ClusterUpgradeVersion`. Pour l’obtenir, exécutez la commande suivante :

    ```powershell
    Get-ClusterNodeSupportedVersion
    ```

    > [!NOTE]
    > `ClusterFunctionalLevel` est automatiquement défini sur `10` et ne nécessite pas de mise à jour en raison du nouveau système d’exploitation et de la création du cluster.

1. Mettez à jour votre pool de stockage comme suit :

    ```powershell
    Get-StoragePool | Update-StoragePool
    ```

## <a name="refs-volumes"></a>Volumes ReFS

Si vous effectuez une migration à partir de Windows Server 2016, les volumes ReFS (Resilient File System) sont pris en charge, mais ces volumes ne bénéficient pas des améliorations de performances suivantes dans Azure Stack HCI :

- Parité avec accélération par miroir
- Contournement du journal de mappage

Ces améliorations nécessitent la création d’un volume ReFS à l’aide de l’applet de commande `New-Volume`.

> [!NOTE]
> Pour les volumes de parité accélérés par mise en miroir de Windows Server 2016, le compactage ReFS n’était pas disponible. Par conséquent, le rattachement de ces volumes est possible mais il sera moins que la création d’un volume de mappage sur un cluster Azure Stack HCI.

## <a name="import-the-vms"></a>Importer les machines virtuelles

Il est recommandé de créer au moins un volume partagé de cluster (CSV) par nœud de cluster pour répartir équitablement le nombre de machines virtuelles entre les différents propriétaire de CSV pour optimiser la résilience, le niveau de performances et l’échelle des charges de machines virtuelles. Par défaut, cet équilibre se fait automatiquement toutes les cinq minutes et doit être pris en compte quand Robocopy est utilisé entre un nœud du cluster source et le nœud du cluster de destination pour faire en sorte que les propriétaires de CSV source et de destination soient en adéquation pour fournir le chemin et la vitesse de transfert optimaux.

Effectuez les étapes suivantes sur votre cluster Azure Stack HCI pour importer les machines virtuelles, les rendre hautement disponibles et les démarrer :

1. Exécutez l’applet de commande suivante pour afficher tous les nœuds de propriétaire de CSV :

    ```powershell
    Get-ClusterSharedVolume
    ```

1. Pour chaque nœud de serveur, accédez à `C:\Clusterstorage\Volume` et définissez le chemin pour toutes les machines virtuelles, par exemple `C:\Clusterstorage\volume01`.

1. Exécutez l’applet de commande suivante sur chaque nœud de propriétaire de CSV pour afficher le chemin de tous les fichiers VMCX de machines virtuelles par volume avant l’importation des machines virtuelles. Modifiez le chemin en fonction de votre environnement :

    ```powershell
    Get-ChildItem -Path "C:\Clusterstorage\Volume01\*.vmcx" -Recurse
    ```

1. Exécutez l’applet de commande suivante pour chaque nœud de serveur afin d’importer et inscrire toutes les machines virtuelles, et de les rendre hautement disponibles sur chaque nœud propriétaire de volume CSV. Cela garantit une répartition équitable des machines virtuelles pour une allocation optimale de la mémoire et des processeurs :

    ```powershell
    Get-ChildItem -Path "C:\Clusterstorage\Volume01\*.vmcx" -Recurse | Import-VM -Register | Get-VM | Add-ClusterVirtualMachineRole
    ```

1. Démarrez chaque machine virtuelle de destination sur chaque nœud :

    ```powershell
    Start-VM -Name
    ```

1. Connectez-vous et vérifiez que toutes les machines virtuelles sont en cours d’exécution et que l’ensemble de vos applications et données sont là :

    ```powershell
    Get-VM -ComputerName Server01 | Where-Object {$_.State -eq 'Running'}
    ```

1. Enfin, mettez à jour vos machines virtuelles vers la dernière version d’Azure Stack HCI afin de tirer parti de toutes les améliorations :

    ```powershell
    Get-VM | Update-VMVersion -Force
    ```

## <a name="next-steps"></a>Étapes suivantes

- Validez le cluster après la migration. Consultez [Valider un cluster Azure Stack HCI](validate.md).
- Pour migrer des machines virtuelles Windows Server vers un nouveau matériel Azure Stack HCI, consultez [Migrer vers Azure Stack HCI sur du nouveau matériel](migrate-cluster-new-hardware.md).