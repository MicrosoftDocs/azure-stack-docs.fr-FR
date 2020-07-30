---
title: Gérer des machines virtuelles sur Azure Stack HCI avec Windows PowerShell
description: Guide pratique pour gérer des machines virtuelles sur Azure Stack HCI à l’aide de Windows PowerShell
author: v-dasis
ms.topic: how-to
ms.date: 07/21/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: bbb77e9d96daa3f7f6eefd88812a11656e2acc5d
ms.sourcegitcommit: ad6bbb611ac671b295568d3f00a193b783470c68
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87397428"
---
# <a name="manage-vms-on-azure-stack-hci-using-windows-powershell"></a>Gérer des machines virtuelles sur Azure Stack HCI avec Windows PowerShell

> S’applique à Azure Stack HCI, version 20H2 ; Windows Server 2019

Windows PowerShell peut être utilisé pour créer et gérer vos machines virtuelles sur Azure Stack HCI.

En règle générale, vous gérez les machines virtuelles à partir d’un ordinateur distant plutôt que sur un serveur hôte dans un cluster. Cet ordinateur distant est appelé ordinateur de gestion.

> [!NOTE]
> Quand vous exécutez des commandes PowerShell à partir d’un ordinateur de gestion, incluez le paramètre -ComputerName dans le nom du serveur hôte que vous gérez. Les noms NetBIOS, les adresses IP et les noms de domaine complets sont autorisés.

Pour obtenir la documentation de référence complète sur la gestion de machines virtuelles avec PowerShell, consultez les [informations de référence sur Hyper-V](/powershell/module/hyper-v/?view=win10-ps).

## <a name="create-a-vm"></a>Créer une machine virtuelle  

L’applet de commande `New-VM` est utilisée pour créer une machine virtuelle. Pour plus d’informations sur son utilisation, consultez la documentation de référence sur [New-VM](/powershell/module/hyper-v/new-vm?view=win10-ps).

Voici les paramètres que vous pouvez spécifier lors de la création d’une machine virtuelle avec un disque dur virtuel existant, où :

- **-Name** correspond au nom que vous fournissez pour la machine virtuelle que vous créez.

- **-MemoryStartupBytes** correspond à la quantité de mémoire disponible pour la machine virtuelle au démarrage.

- **-BootDevice** correspond à l’appareil sur lequel la machine virtuelle s’amorce quand elle démarre.
 Il s’agit généralement d’un disque dur virtuel (VHD), d’un fichier .iso pour un démarrage sur DVD ou d’une carte réseau (NetworkAdapter) pour un démarrage réseau.

- **-VHDPath** correspond au chemin du disque de la machine virtuelle à utiliser.

- **-Path** correspond au chemin de stockage des fichiers de configuration de la machine virtuelle.

- **-Generation** correspond à la génération de la machine virtuelle. Utilisez la génération 1 pour un VHD et la génération 2 pour un VHDX.

- **-Switch** correspond au nom du commutateur virtuel que vous voulez que la machine virtuelle utilise pour se connecter à d’autres machines virtuelles ou au réseau. Obtenez le nom du commutateur virtuel à l’aide de [Get-VMSwitch](/powershell/module/hyper-v/get-vmswitch?view=win10-ps). Par exemple :  

La commande complète pour créer une machine virtuelle appelée VM1 est la suivante :

 ```powershell
 New-VM -ComputerName Server1 -Name VM1 -MemoryStartupBytes <Memory> -BootDevice <BootDevice> -VHDPath <VHDPath> -Path <Path> -Generation <Generation> -Switch <SwitchName>
 ```

L’exemple suivant crée une machine virtuelle de génération 2 dotée de 4 Go de mémoire. Elle démarre à partir du dossier VMs\Win10.vhdx du répertoire actuel et utilise le commutateur virtuel nommé ExternalSwitch. Les fichiers de configuration de la machine virtuelle sont stockés dans le dossier VMData.  

``` powershell
New-VM -ComputerName Server1 -Name VM1 -MemoryStartupBytes 4GB -BootDevice VHD -VHDPath .\VMs\Win10.vhdx -Path .\VMData -Generation 2 -Switch ExternalSwitch
```

Les paramètres suivants sont utilisés pour spécifier des disques durs virtuels.

Pour créer une machine virtuelle avec un nouveau disque dur virtuel, remplacez le paramètre **-VHDPath** de l’exemple ci-dessus par **-NewVHDPath** et ajoutez le paramètre **-NewVHDSizeBytes** comme illustré ici :  

``` powershell  
New-VM -ComputerName Server1 -Name VM1 -MemoryStartupBytes 4GB -BootDevice VHD -NewVHDPath .\VMs\Win10.vhdx -Path .\VMData -NewVHDSizeBytes 20GB -Generation 2 -Switch ExternalSwitch  
 ```  

Pour créer une machine virtuelle avec un nouveau disque virtuel qui démarre sur une image de système d’exploitation, consultez l’exemple PowerShell mentionné dans la [procédure pas à pas pour créer une machine virtuelle pour Hyper-V sur Windows 10](/virtualization/hyper-v-on-windows/quick-start/create-virtual-machine).  

## <a name="get-a-list-of-vms"></a>Obtenir la liste des machines virtuelles

L’exemple suivant retourne la liste de toutes les machines virtuelles sur Server1.

```powershell
Get-VM -ComputerName Server1
```

L’exemple suivant retourne la liste de toutes les machines virtuelles en cours d’exécution sur un serveur en ajoutant un filtre à l’aide de la commande `Where-Object`. Pour plus d’informations, consultez la documentation sur l’[utilisation de Where-Object](/previous-versions/windows/it-pro/windows-powershell-1.0/ee177028(v=technet.10)).

```powershell
Get-VM -ComputerName Server1 | Where-Object -Property State -eq "Running"
```

L’exemple suivant retourne la liste de toutes les machines virtuelles arrêtées sur le serveur.

```powershell
Get-VM -ComputerName Server1 | Where-Object -Property State -eq "Off"
```

## <a name="start-and-stop-a-vm"></a>Démarrer et arrêter une machine virtuelle

Utilisez les commandes `Start-VM` et `Stop-VM` pour démarrer ou arrêter une machine virtuelle. Pour plus d’informations, consultez la documentation de référence sur [Start-VM](/powershell/module/hyper-v/start-vm?view=win10-ps) et [Stop-VM](/powershell/module/hyper-v/stop-vm?view=win10-ps).

L’exemple suivant montre comment démarrer une machine virtuelle nommée VM1 :

```powershell
Start-VM -Name VM1 -ComputerName Server1
```

L’exemple suivant montre comment arrêter une machine virtuelle nommée TestVM :

```powershell
Stop-VM -Name VM1 -ComputerName Server1
```

## <a name="move-a-vm"></a>Déplacer une machine virtuelle

L’applet de commande `Move-VM` déplace une machine virtuelle vers un autre serveur. Pour plus d’informations, consultez la documentation de référence sur [Move-VM](/powershell/module/hyper-v/move-vm?view=win10-ps).

 L’exemple suivant montre comment déplacer une machine virtuelle vers Server2 quand elle est stockée sur un partage SMB sur Server1 :

```powershell
Move-VM -ComputerName Server1 -Name VM1 -DestinationHost Server2
 ```

L’exemple suivant montre comment déplacer une machine virtuelle vers Server2 à partir de Server1 et comment déplacer tous les fichiers associés à la machine virtuelle vers D:\VM_name sur l’ordinateur distant :

```powershell
Move-VM -ComputerName Server1 -Name VM1 -DestinationHost Server2 -IncludeStorage -DestinationStoragePath D:\VM_name
```

## <a name="import-or-export-a-vm"></a>Importer ou exporter une machine virtuelle

Les applets de commande `Import-VM` et `Export-VM` permettent d’importer et d’exporter une machine virtuelle. Voici quelques exemples. Pour plus d’informations, consultez la documentation de référence sur [Import-VM](/powershell/module/hyper-v/import-vm?view=win10-ps) et [Export-VM](/powershell/module/hyper-v/export-vm?view=win10-ps).

L’exemple suivant montre comment importer une machine virtuelle à partir de son fichier de configuration. La machine virtuelle étant inscrite sur place, ses fichiers ne sont pas copiés :

```powershell
Import-VM -ComputerName Server1 -Name VM1 -Path 'C:\<vm export path>\2B91FEB3-F1E0-4FFF-B8BE-29CED892A95A.vmcx'
```

L’exemple suivant exporte une machine virtuelle à la racine du lecteur D :

```powershell
Export-VM -ComputerName Server1 -Name VM1 -Path D:\
```

## <a name="rename-a-vm"></a>Renommer une machine virtuelle

L’applet de commande `Rename-VM` est utilisée pour renommer une machine virtuelle. Pour plus d’informations, consultez la documentation de référence sur [Rename-VM](/powershell/module/hyper-v/rename-vm?view=win10-ps).

L’exemple suivant remplace le nom VM1 par VM2 et affiche la machine virtuelle renommée :

```powershell
Rename-VM -ComputerName Server1 -Name VM1 -NewName VM2
```

## <a name="create-a-vm-checkpoint"></a>Créer un point de contrôle de machine virtuelle

L’applet de commande `Checkpoint-VM` est utilisée pour créer un point de contrôle pour une machine virtuelle. Pour plus d’informations, consultez la documentation de référence sur [Checkpoint-VM](/powershell/module/hyper-v/checkpoint-vm?view=win10-ps).

L’exemple suivant crée un point de contrôle nommé BeforeInstallingUpdates pour la machine virtuelle nommée Test.

```powershell
Checkpoint-VM -ComputerName Server1 -Name VM1 -SnapshotName BeforeInstallingUpdates
```

## <a name="create-a-vhd-for-a-vm"></a>Créer un disque dur virtuel pour une machine virtuelle

L’applet de commande `New-VHD` est utilisée pour créer un disque dur virtuel (VHD) pour une machine virtuelle. Pour plus d’informations sur son utilisation, consultez la documentation de référence sur [New-VHD](/powershell/module/hyper-v/new-vhd?view=win10-ps).

L’exemple suivant crée un disque dur virtuel dynamique au format VHDX d’une taille de 10 Go. L’extension de nom de fichier détermine le format. Aucun type n’étant spécifié, le type par défaut (dynamique) est utilisé.

```powershell
New-VHD -ComputerName Server1 -Name VM1 -Path c:\Base.vhdx -SizeBytes 10GB
```

## <a name="add-a-network-adapter-to-a-vm"></a>Ajouter une carte réseau à une machine virtuelle

L’applet de commande `Add-VMNetworkAdapter` est utilisée pour ajouter une carte réseau virtuelle à une machine virtuelle. Voici quelques exemples. Pour plus d’informations sur son utilisation, consultez la documentation de référence sur [Add-VMNetworkAdapter](/powershell/module/hyper-v/add-vmnetworkadapter?view=win10-ps).

L’exemple suivant ajoute une carte réseau virtuelle nommée Redmond NIC1 à une machine virtuelle nommée VM1 :

```powershell
Add-VMNetworkAdapter -ComputerName Server1 -VMName VM1 -Name "Redmond NIC1"
```

Cet exemple ajoute une carte réseau virtuelle à une machine virtuelle nommée VM1 et la connecte à un commutateur virtuel nommé Network :

```powershell
Add-VMNetworkAdapter -ComputerName Server1 -VMName VM1 -SwitchName Network
```

## <a name="create-a-virtual-switch-for-a-vm"></a>Créer un commutateur virtuel pour une machine virtuelle

L’applet de commande `New-VMSwitch` est utilisée pour le nouveau commutateur virtuel sur un hôte de machine virtuelle. Pour plus d’informations sur son utilisation, consultez la documentation de référence sur [New-VMswitch](/powershell/module/hyper-v/new-vmswitch?view=win10-ps).

L’exemple suivant crée un commutateur appelé « QoS Switch », qui est lié à une carte réseau appelée « Wired Ethernet Connection 3 » et prend en charge une bande passante minimale selon le poids.

```powershell
New-VMSwitch "QoS Switch" -NetAdapterName "Wired Ethernet Connection 3" -MinimumBandwidthMode Weight
```

## <a name="set-memory-for-a-vm"></a>Définir la mémoire d’une machine virtuelle

L’applet de commande `Set-VMMemory` est utilisée pour configurer la mémoire d’une machine virtuelle. Pour plus d’informations sur son utilisation, consultez la documentation de référence sur [Set-VMMemory](/powershell/module/hyper-v/set-vmmemory?view=win10-ps).

L’exemple suivant active la mémoire dynamique sur une machine virtuelle nommée VM1, définit sa mémoire minimale, de démarrage et maximale, la priorité de sa mémoire et son tampon.

```powershell
Set-VMMemory -ComputerName Server1 -Name VM1 -DynamicMemoryEnabled $true -MinimumBytes 64MB -StartupBytes 256MB -MaximumBytes 2GB -Priority 80 -Buffer 25
```

## <a name="set-virtual-processors-for-a-vm"></a>Définir des processeurs virtuels pour une machine virtuelle

L’applet de commande `Set-VMProcessor` est utilisée pour configurer les processeurs virtuels d’une machine virtuelle. Pour plus d’informations sur son utilisation, consultez la documentation de référence sur [Set-VMProcessor](/powershell/module/hyper-v/set-vmprocessor?view=win10-ps).

L’exemple suivant configure une machine virtuelle nommée VM1 avec deux processeurs virtuels, une réserve de 10 %, une limite de 75 % et un poids relatif de 200.

```powershell
Set-VMProcessor -ComputerName Server1 -Name VM1 -Count 2 -Reserve 10 -Maximum 75 -RelativeWeight 200
```

## <a name="next-steps"></a>Étapes suivantes  

Vous pouvez également créer et gérer des machines virtuelles à l’aide de Windows Admin Center. Pour plus d’informations, consultez [Windows Admin Center](vm.md).
