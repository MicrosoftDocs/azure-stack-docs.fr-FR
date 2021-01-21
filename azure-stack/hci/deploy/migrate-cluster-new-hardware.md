---
title: Migrer vers Azure Stack HCI sur du nouveau matériel
description: Découvrez comment migrer vers Azure Stack HCI sur du nouveau matériel
author: v-dasis
ms.topic: how-to
ms.date: 12/10/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 7a0c0ca7a99b3554b74cc80911acbee92793aa52
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98254821"
---
# <a name="migrate-to-azure-stack-hci-on-new-hardware"></a>Migrer vers Azure Stack HCI sur du nouveau matériel

> S’applique à Azure Stack HCI, version 20H2 ; Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2008 R2

Cette rubrique explique comment migrer des fichiers de machines virtuelles sur Windows Server 2012 R2, Windows Server 2016 ou Windows Server 2019 vers un nouveau serveur matériel Azure Stack HCI à l’aide de Windows PowerShell et Robocopy. Robocopy est une méthode fiable pour copier des fichiers d’un serveur vers un autre. En cas de déconnexion, il reprend le travail à son dernier état connu. Robocopy prend aussi en charge la copie de fichiers multithread via le protocole SMB (Server Message Block). Pour plus d’informations, consultez [Robocopy](/windows-server/administration/windows-commands/robocopy).

> [!NOTE]
> La migration dynamique et la réplication Hyper-V de Windows Server vers Azure Stack HCI ne sont pas prises en charge.

Si vous avez des machines virtuelles sur Windows 2012 R2 ou version antérieure et que vous voulez les migrer, consultez [Migration de machines virtuelles anciennes](#migrating-older-vms).

Pour migrer vers Azure Stack HCI en utilisant le même matériel, consultez [Migrer vers Azure Stack HCI sur le même matériel](migrate-cluster-same-hardware.md).

Le diagramme suivant montre un cluster source Windows Server et un cluster de destination Azure Stack HCI à titre d’exemple. Vous pouvez aussi migrer des machines virtuelles sur des serveurs autonomes.

:::image type="content" source="media/migrate/migrate-cluster.png" alt-text="Migrer un cluster vers Azure Stack HCI" lightbox="media/migrate/migrate-cluster.png":::

Pour ce qui est des temps d’arrêt attendus, l’utilisation d’une seule carte réseau avec un réseau Est-Ouest RDMA de 40 Go entre les clusters et de Robocopy configuré pour 32 multithreads, vous pouvez atteindre des vitesses de transfert de 1,9 To par heure.

> [!NOTE]
> La migration de machines virtuelles pour des clusters étendus n’est pas abordée dans cet article.

## <a name="before-you-begin"></a>Avant de commencer

Avant d’entreprendre la migration, vous devez prendre en considération plusieurs éléments et exigences :

- Toutes les commandes Windows PowerShell doivent être exécutées en tant qu’administrateur.

- Vous devez disposer d’informations d’identification de domaine avec des autorisations d’administrateur pour les clusters source et de destination et détenir des droits complets sur les unités d’organisation source et de destination qui contiennent les deux clusters.

- Les deux clusters doivent se trouver dans le même domaine et la même forêt Active Directory pour faciliter l’authentification Kerberos entre les clusters pour la migration des machines virtuelles.

- Les deux clusters doivent résider dans une unité d’organisation Active Directory avec le paramètre Bloquer l’héritage de l’objet de stratégie de groupe (GPO) défini sur cette unité d’organisation. Cela garantit qu’aucun objet GPO et aucune stratégie de sécurité au niveau du domaine ne peut impacter la migration.

- Les deux clusters doivent être connectés à la même source de temps pour prendre en charge une authentification Kerberos cohérente entre les clusters.

- Notez le nom du commutateur virtuel Hyper-V utilisé par les machines virtuelles du cluster source. Vous devez utiliser le même nom de commutateur virtuel pour le « réseau de machines virtuelles » du cluster de destination Azure Stack HCI avant d’importer les machines virtuelles.

- Supprimez les fichiers image ISO pour vos machines virtuelles sources. Pour ce faire, utilisez le Gestionnaire Hyper-V dans les **Propriétés de machine virtuelle** de la **section Matériel**. Sélectionnez **Supprimer** pour tous les lecteurs de CD/DVD virtuels.

- Arrêtez toutes les machines virtuelles sur le cluster source. Cela est nécessaire pour maintenir la gestion de version et l’état tout au long du processus de migration.

- Vérifiez si Azure Stack HCI prend en charge la version de vos machines virtuelles en vue de leur importation, et mettez-les à jour si nécessaire. Pour savoir comment faire, consultez la section [Prise en charge et mise à jour des versions de machines virtuelles](#vm-version-support-and-update).

- Sauvegardez toutes les machines virtuelles sur votre cluster source. Effectuez une sauvegarde cohérente en cas d’incident de l’ensemble des applications et des données et une sauvegarde cohérente du point de vue des applications de toutes les bases de données. Pour sauvegarder sur Azure, consultez [Utiliser Sauvegarde Azure](../manage/use-azure-backup.md).

- Créez un point de contrôle pour les machines virtuelles et le contrôleur de domaine de votre cluster source au cas où vous devriez revenir à un état antérieur. Cela ne s’applique pas aux serveurs physiques.

- Veillez à ce que la taille maximale des trames Jumbo soit identique entre les réseaux de stockage des clusters source et de destination, en particulier les cartes réseau RDMA et les ports réseau de leur commutateur respectif pour fournir la taille de paquet de transfert de bout en bout la plus efficace possible.

- Notez le nom du commutateur virtuel Hyper-V du cluster source. Vous vous en reservirez sur le cluster de destination.

- Le matériel Azure Stack HCI doit avoir une capacité et une configuration au moins égales à celles du matériel source.

- Réduisez le nombre de tronçons réseau ou la distance physique entre les clusters source et de destination pour assurer le transfert de fichiers le plus rapide possible.

## <a name="vm-version-support-and-update"></a>Prise en charge et mise à jour des versions de machines virtuelles

Ce tableau liste les versions du système d’exploitation Windows Server et les versions de leurs machines virtuelles.

Quelle que soit la version du système d’exploitation sur lequel s’exécute une machine virtuelle, la version minimale de machine virtuelle prise en charge pour la migration directe vers Azure Stack HCI est la version 5.0. Il s’agit de la version par défaut pour les machines virtuelles sur Windows Server 2012 R2. Ainsi, toute machine virtuelle de version 2.0, 3.0 ou 4.0 en cours d’exécution doit par exemple être mise à jour vers la version 5.0 avant la migration.

|Version du SE|Version de la machine virtuelle|
|---|---|
|Windows Server 2008 SP1|2.0|
|Windows Server 2008 R2|3.0|
|Windows Server 2012|4.0|
|Windows Server 2012 R2|5.0|
|Windows Server 2016|8.0|
|Windows Server 2019|9.0|
|Azure Stack HCI|9.0|

Pour les machines virtuelles sur Windows Server 2012 R2, Windows Server 2016 et Windows Server 2019, mettez d’abord à jour toutes les machines virtuelles vers la dernière version de machine virtuelle prise en charge sur le matériel source avant d’exécuter le script de migration Robocopy. Vous serez ainsi assuré que toutes les machines virtuelles sont au moins en version 5.0, condition de la réussite de leur importation.

Pour les machines virtuelles sur Windows Server 2008 SP1, Windows Server 2008 R2-SP1 et Windows 2012, leur version sera inférieure à la version 5.0. Par ailleurs, ces machines virtuelles utilisent un fichier .xml pour la configuration et non un fichier .vcmx. Il n’est donc pas possible d’importer les machines virtuelles directement dans Azure Stack HCI. Dans ce cas, vous avez deux options, qui sont détaillées dans [Migration de machines virtuelles anciennes](#migrating-older-vms).

### <a name="updating-the-vm-version"></a>Mise à jour de la version des machines virtuelles

Les commandes suivantes s’appliquent à Windows Server 2012 R2 et versions ultérieures. Utilisez la commande suivante pour afficher toutes les versions de machines virtuelles d’un même serveur :

```powershell
Get-VM * | Format-Table Name,Version
```

Pour afficher toutes les versions de machines virtuelles de tous les serveurs d’un cluster :

```powershell
Get-VM –ComputerName (Get-ClusterNode)
```

Pour mettre à jour toutes les machines virtuelles vers la dernière version prise en charge sur tous les serveurs :

```powershell
Get-VM –ComputerName (Get-ClusterNode) | Update-VMVersion -Force
```

## <a name="rdma-recommendations"></a>Recommandations concernant RDMA

Si vous utilisez l’accès direct à la mémoire à distance (RDMA), Robocopy peut s’en servir pour copier vos machines virtuelles entre les clusters. Voici quelques recommandations pour utiliser RDMA :

- Connectez les deux clusters au même commutateur ToR (Top of Rack, en haut du rack) pour utiliser le chemin réseau le plus rapide entre les clusters source et de destination. Concernant le chemin réseau de stockage, celui-ci prend généralement en charge des vitesses de l’ordre de 10 GbE/25GbE voire plus et exploite RDMA.

- Si la carte ou le standard RDMA est différent entre les clusters source et de destination (ROCE ou iWARP), Robocopy utilise à la place le protocole SMB sur TCP/IP via le réseau disponible le plus rapide. Il s’agira généralement d’une vitesse double 10Gbe/25Gbe voire plus pour le réseau Est-Ouest, offrant ainsi la méthode optimale pour copier des fichiers VHDX de machines virtuelles entre les clusters.

- Pour faire en sorte que Robocopy puisse tirer parti de RDMA entre les clusters (réseau Est-Ouest), configurez des réseaux de stockage RDMA afin qu’ils puissent être routés entre les clusters source et de destination.

## <a name="create-the-new-cluster"></a>Créer le cluster

Avant de pouvoir créer le cluster Azure Stack HCI, vous devez installer le système d’exploitation Azure Stack HCI sur chaque nouveau serveur qui fera partie du cluster. Pour savoir comment procéder, consultez [Déployer le système d’exploitation Azure Stack HCI](operating-system.md).

Utilisez Windows Admin Center ou Windows PowerShell pour créer le cluster. Pour obtenir des informations détaillées sur la procédure à suivre, consultez [Créer un cluster Azure Stack HCI à l’aide de Windows Admin Center](create-cluster.md) et [Créer un cluster Azure Stack HCI à l’aide de Windows PowerShell](create-cluster-powershell.md).

> [!IMPORTANT]
> Les noms des commutateurs virtuels Hyper-V (`VMSwitch`) doivent être identiques entre les clusters. Vérifiez que les noms des commutateurs virtuels créés sur le cluster de destination correspondent à ceux utilisés sur le cluster source sur tous les serveurs. Vérifiez les noms des commutateurs pour ceux-ci avant d’importer les machines virtuelles.

> [!NOTE]
> Vous devez inscrire le cluster Azure Stack HCI auprès d’Azure avant de créer de nouvelles machines virtuelles sur celui-ci. Pour plus d’informations, consultez [Inscrire auprès d’Azure](register-with-azure.md).

## <a name="run-the-migration-script"></a>Exécuter le script de migration

Le script PowerShell suivant `Robocopy_Remote_Server_.ps1` utilise Robocopy pour copier les fichiers de machines virtuelles et leurs répertoires et métadonnées dépendants du cluster source vers le cluster de destination. Ce script a été modifié par rapport au script d’origine de TechNet : [Robocopy Files to Remote Server Using PowerShell and RoboCopy](https://gallery.technet.microsoft.com/scriptcenter/Robocoy-Files-to-Remote-bdfc5154).

Le script copie tous les fichiers VHD, VHDX et VMCX de machines virtuelles sur votre cluster de destination pour un volume partagé de cluster (CSV) donné. Un seul CSV est migré à la fois.

Le script de migration est exécuté localement sur chaque serveur source pour tirer parti de l’avantage de RDMA et du transfert réseau rapide. Pour ce faire :

1. Vérifiez que chaque nœud du cluster de destination est défini sur le propriétaire du CSV de destination.

1. Pour déterminer l’emplacement de tous les fichiers VHD et VHDX de machines virtuelles à copier, utilisez l’applet de commande suivante. Examinez le fichier `C:\vmpaths.txt` pour identifier le chemin de fichier source le plus haut à partir duquel Robocopy doit commencer pour l’étape 4 :

    ```powershell  
    Get-ChildItem -Path "C:\Clusterstorage\Volume01\*.vhd*" -Recurse > c:\vmpaths.txt
    ```

    > [!NOTE]
    > Si vos fichiers VHD et VHDX se situent dans des chemins différents sur le même volume, vous devez exécuter le script de migration pour chaque chemin différent pour les copier tous.

1. Modifiez les trois variables suivantes pour faire correspondre le chemin des machines virtuelles du cluster source à celui du cluster de destination :

    - `$Dest_Server = "Node01"`
    - `$source  = "C:\Clusterstorage\Volume01"`
    - `$dest = "\\$Dest_Server\C$\Clusterstorage\Volume01"`

1. Exécutez le script suivant sur chaque serveur source Windows Server :

```powershell
<#
#===========================================================================  
# Script: Robocopy_Remote_Server_.ps1
#===========================================================================  
.DESCRIPTION:
Change the following variables to match your source cluster VM path with the destination cluster VM path. Then run this script on each source Cluster Node CSV owner and make sure the destination cluster node is set to the CSV owner for the destination CSV.

        Change $Dest_Server = "Node01"
        Change $source  = "C:\Clusterstorage\Volume01"
        Change $dest = "\\$Dest_Server\C$\Clusterstorage\Volume01"
#>

$Space       = Write-host ""
$Dest_Server = "Node01"
$source      = "C:\Clusterstorage\Volume01"
$dest        = "\\$Dest_Server\C$\Clusterstorage\Volume01"
$Logfile     = "c:\temp\Robocopy1-$date.txt"
$date        = Get-Date -UFormat "%Y%m%d"
$cmdArgs     = @("$source","$dest",$what,$options)  
$what        = @("/COPYALL")
$options     = @("/E","/MT:32","/R:0","/W:1","/NFL","/NDL","/LOG:$logfile","/xf")
 
## Get Start Time
$startDTM = (Get-Date)
 
$Dest_Server     = "Node01"
$TARGETDIR   = \\$Dest_Server\C$\Clusterstorage\Volume01
$Space
Clear
## Provide Information
Write-host ".....Copying Virtual Machines FROM $Source to $TARGETDIR ....................." -fore Green -back black
Write-Host "........................................." -Fore Green

## Kick off the copy with options defined  
robocopy @cmdArgs
 
## Get End Time
$endDTM = (Get-Date)
 
## Echo Time elapsed
$Time = "Elapsed Time: = $(($endDTM-$startDTM).totalminutes) minutes"  
## Provide time it took
Write-host ""
Write-host " Copy Virtual Machines to $Dest_Server has been completed......" -fore Green -back black
Write-host " Copy Virtual Machines to $Dest_Server took $Time        ......" -fore Cyan
```

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

    > [!NOTE]
    > Windows Server 2012 R2 et les machines virtuelles anciennes utilisent un fichier XML et non un fichier VCMX. Pour plus d’informations, consultez la section **Migration de machines virtuelles anciennes**.

1. Exécutez l’applet de commande suivante pour chaque nœud de serveur afin d’importer, inscrire et rendre les machines virtuelles hautement disponibles sur chaque nœud de propriétaire de CSV. Cela garantit une répartition équitable des machines virtuelles pour une allocation de mémoire et de processeurs optimale :

    ```powershell
    Get-ChildItem -Path "C:\Clusterstorage\Volume01\*.vmcx" -Recurse | Import-VM -Register | Get-VM | Add-ClusterVirtualMachineRole
    ```

1. Démarrez chaque machine virtuelle de destination sur chaque nœud :

    ```powershell
    Start-VM -Name
    ```

1. Connectez-vous et vérifiez que toutes les machines virtuelles s’exécutent et que toutes vos applications et données sont présentes :

    ```powershell
    Get-VM -ComputerName Server01 | Where-Object {$_.State -eq 'Running'}
    ```

1. Mettez à jour vos machines virtuelles vers la dernière version pour Azure Stack HCI de façon à tirer parti de toutes les avancées :

    ```powershell
    Get-VM | Update-VMVersion -Force
    ```

1. Une fois le script terminé, vérifiez si des erreurs sont listées dans le fichier journal Robocopy et assurez-vous que toutes les machines virtuelles sont bien copiées.

## <a name="migrating-older-vms"></a>Migration de machines virtuelles anciennes

Si vous disposez de machines virtuelles Windows Server 2008 SP1, Windows Server 2008 R2-SP1, Windows Server 2012 ou Windows Server 2012 R2, cette section vous concerne. Pour gérer ces machines virtuelles, deux options s’offrent à vous :

- Commencez par migrer ces machines virtuelles vers Windows Server 2012 R2, Windows Server 2016 ou Windows Server 2019, mettez à jour la version des machines virtuelles, puis lancez le processus de migration.

- Utilisez Robocopy pour copier tous les disques durs virtuels (VHD) des machines virtuelles sur Azure Stack HCI. Créez ensuite de nouvelles machines virtuelles et attachez les VHD copiés aux machines virtuelles dans Azure Stack HCI. Cela a pour effet de contourner la limitation liée à la version des machines virtuelles anciennes.

Les anciens hôtes Windows Server 2012 R2 et Hyper-V utilisent un format de fichier XML pour la configuration de leurs machines virtuelles, qui se distingue du format de fichier VCMX utilisé pour les hôtes Windows Server 2016 et Hyper-V ultérieurs. De ce fait, une commande Robocopy différente doit être utilisée pour copier ces machines virtuelles vers Azure Stack HCI.

### <a name="option-1-staged-migration"></a>Option 1 : Migration par étapes

Il s’agit d’une migration en deux étapes destinée aux machines virtuelles hébergées sur Windows Server 2008 SP1, Windows Server 2008 R2-SP et Windows Server 2012. Voici le processus à utiliser :

1. Découvrez l’emplacement de tous les fichiers VHD et VHDX de machines virtuelles à copier, puis examinez le fichier `vmpaths.txt` pour identifier le chemin du fichier source le plus haut à partir duquel Robocopy doit commencer. Utilisez l’applet de commande suivante :

    ```powershell
    Get-ChildItem -Path "C:\Clusterstorage\Volume01\*.vhd*" -Recurse > c:\vmpaths.txt
    ```

1. Utilisez l’exemple de commande Robocopy suivant pour copier les machines virtuelles vers Windows Server 2012 R2 en utilisant d’abord le chemin le plus haut identifié à l’étape 1 :

    `Robocopy \\2012R2-Clus01\c$\clusterstorage\volume01\Hyper-V\ \\20H2-Clus01\c$\clusterstorage\volume01\Hyper-V\ /E /MT:32 /R:0 /w:1 /NFL /NDL /copyall /log:c:\log.txt /xf`

1. Vérifiez que le nom du commutateur virtuel (`VMSwitch`) utilisé sur le cluster Windows Server 2012 R2 est le même que celui utilisé sur la source Windows 2008 R2 ou Windows Server 2008 R2-SP1. Pour afficher les noms des commutateurs utilisés sur tous les serveurs d’un cluster, utilisez ceci :

     ```powershell
    Get-VMSwitch -CimSession $Servers | Select-Object Name
    ```

    Si nécessaire, renommez le commutateur sur Windows Server 2012 R2. Pour renommer le commutateur sur tous les serveurs du cluster, utilisez ceci :

    ```powershell
    Invoke-Command -ComputerName $Servers -ScriptBlock {rename-VMSwitch -Name $using:vSwitcholdName -NewName $using:vSwitchnewname}
    ```

1. Copiez et importez les machines virtuelles sur Windows Server 2012 R2 :

     ```powershell
    Get-ChildItem -Path "c:\clusterstorage\volume01\Hyper-V\*.xml"-Recurse
    ```

    ```powershell
    Get-ChildItem -Path "c:\clusterstorage\volume01\image\*.xml" -Recurse    | Import-VM -Register | Get-VM | Add-ClusterVirtualMachineRole  
    ```

1. Sur Windows Server 2012 R2, mettez à jour la version de toutes les machines virtuelles vers 5.0 :

    ```powershell
    Get-VM | Update-VMVersion -Force
    ```

1. [Exécutez le script de migration](#run-the-migration-script) pour copier les machines virtuelles sur Azure Stack HCI.

1. Suivez le processus décrit dans [Importer les machines virtuelles](#import-the-vms) en remplaçant les étapes 3 et 4 par ce qui suit pour gérer les fichiers XML et importer les machines virtuelles dans Azure Stack HCI :

    ```powershell
    Get-ChildItem -Path "c:\clusterstorage\volume01\Hyper-V\*.xml"-Recurse
    ```

    ```powershell
    Get-ChildItem -Path "c:\clusterstorage\volume01\image\*.xml" -Recurse    | Import-VM -Register | Get-VM | Add-ClusterVirtualMachineRole  
    ```

1. Effectuez les étapes restantes décrites dans [Importer les machines virtuelles](#import-the-vms).

### <a name="option-2-direct-vhd-copy"></a>Option n°2 : Copie directe des VHD

Cette méthode s’appuie sur Robocopy pour copier les VHD de machines virtuelles hébergés sur Windows 2008 SP1, Windows 2008 R2-SP1 et Windows 2012 sur Azure Stack HCI. Cela a pour effet de contourner la limitation liée à la version minimale prise en charge des machines virtuelles anciennes. Nous vous recommandons d’utiliser cette option pour les machines virtuelles hébergées sur Windows Server 2008 SP1 et Windows Server 2008 R2-SP1.

Les machines virtuelles hébergées sur Windows 2008 SP1 et Windows 2008 R2-SP1 prennent uniquement en charge les machines virtuelles de génération 1 avec des VHD de génération 1. Par conséquent, des machines virtuelles de génération 1 correspondantes doivent être créées sur Azure Stack HCI afin que les VHD copiés puissent être attachés aux nouvelles machines virtuelles. Notez que ces VHD ne peuvent pas être mis à niveau vers des VHD de génération 2.

> [!NOTE]
> Windows Server 2012 prend en charge les machines virtuelles de génération 1 et 2.

Voici le processus à utiliser :

1. Utilisez l’exemple Robocopy pour copier les VHD de machines virtuelles directement sur Azure Stack HCI :

    `Robocopy \\2012R2-Clus01\c$\clusterstorage\volume01\Hyper-V\ \\20H2-Clus01\c$\clusterstorage\volume01\Hyper-V\ /E /MT:32 /R:0 /w:1 /NFL /NDL /copyall /log:c:\log.txt /xf`

1. Créez de nouvelles machines virtuelles de génération 1. Pour obtenir des informations détaillées sur la procédure à suivre, consultez [Gérer les machines virtuelles](../manage/vm.md).

1. Attachez les fichiers VHD copiés aux nouvelles machines virtuelles. Pour plus d’informations, consultez [Gérer les disques durs virtuels (VHD)](/windows-server/storage/disk-management/manage-virtual-hard-disks).

À titre d’information, les systèmes d’exploitation invités Windows Server suivants prennent en charge les machines virtuelles de génération 2 :

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows 10
- Versions 64 bits de Windows 8.1 (64 bits)
- Versions 64 bits de Windows 8 (64 bits)
- Linux (consultez [Machines virtuelles Linux et FreeBSD prises en charge](/windows-server/virtualization/hyper-v/Supported-Linux-and-FreeBSD-virtual-machines-for-Hyper-V-on-Windows))

## <a name="next-steps"></a>Étapes suivantes

- Validez le cluster après la migration. Consultez [Valider un cluster Azure Stack HCI](validate.md).

- Pour migrer vers Azure Stack HCI sur place en utilisant le même matériel, consultez [Migrer vers Azure Stack HCI sur le même matériel](migrate-cluster-same-hardware.md).