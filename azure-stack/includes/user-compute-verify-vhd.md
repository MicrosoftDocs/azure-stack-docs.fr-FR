---
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: include
ms.date: 08/04/2020
ms.reviewer: thoroet
ms.lastreviewed: 08/04/2020
ms.openlocfilehash: 4c0e9fd823b7b932ec11c61e24ce650df1cf9cc9
ms.sourcegitcommit: 362081a8c19e7674c3029c8a44d7ddbe2deb247b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91936125"
---
Avant de charger votre disque dur virtuel, vous devez vérifier qu’il répond à la configuration requise. Les disques durs virtuels qui ne satisfont pas à la configuration requise ne sont pas chargés dans Azure Stack Hub.

1. Vous allez utiliser les modules PowerShell disponibles avec Hyper-V. L’activation des installations Hyper-V prend en charge les modules PowerShell. Vous pouvez vérifier que vous disposez du module en ouvrant PowerShell avec une invite avec élévation de privilèges et en exécutant l’applet de commande suivante :

    ```powershell  
    Get-Command -Module hyper-v
    ```

    Si vous ne disposez pas des commandes Hyper-V, consultez [Utilisation d’Hyper-V et de Windows PowerShell](/virtualization/hyper-v-on-windows/quick-start/try-hyper-v-powershell). 

2. Récupérez le chemin d’accès à votre disque dur virtuel sur votre ordinateur. Exécutez l’applet de commande suivante :

    ```powershell  
    get-vhd <path-to-your-VHD>
    ```

    L’applet de commande retourne l’objet VHD et affiche les attributs, tels que :
    
    ```powershell  
    ComputerName            : YOURMACHINENAME
    Path                    : <path-to-your-VHD>
    VhdFormat               : VHD
    VhdType                 : Fixed
    FileSize                : 68719477248
    Size                    : 68719476736
    MinimumSize             : 32212254720
    LogicalSectorSize       : 512
    PhysicalSectorSize      : 512
    BlockSize               : 0
    ParentPath              :
    DiskIdentifier          : 3C084D21-652A-4C0E-B2D1-63A8E8E64C0C
    FragmentationPercentage : 0
    Alignment               : 1
    Attached                : False
    DiskNumber              :
    IsPMEMCompatible        : False
    AddressAbstractionType  : None
    Number                  :
    ```

3. Avec l’objet VHD, vérifiez qu’il répond à la configuration requise pour Azure Stack Hub.
    - [Le disque dur virtuel est de type fixe.](#vhd-is-of-fixed-type)
    - [Le disque dur virtuel a une taille virtuelle minimale d’au moins 20 Mo.](#vhd-has-minimum-virtual-size-of-at-least-20-mb)
    - [Le disque dur virtuel est aligné.](#vhd-is-aligned)
    - [Longueur du blob VHD = taille virtuelle + longueur de pied de page VHD (512).](#vhd-blob-length) 
    
    En outre, Azure Stack Hub prend en charge uniquement les images provenant de machines virtuelles [de génération 1](#generation-one-vms).

4. Si votre disque dur virtuel n’est pas compatible avec Azure Stack Hub, vous devez revenir à l’image source et à Hyper-V, créer un disque dur virtuel qui répond à la configuration requise et le charger. Pour réduire le risque de dommages possibles dans le processus de chargement, utilisez AzCopy.

### <a name="how-to-fix-your-vhd"></a>Corriger votre disque dur virtuel

Les exigences suivantes doivent être satisfaites pour garantir la compatibilité de votre disque dur virtuel avec Azure Stack Hub.

#### <a name="vhd-is-of-fixed-type"></a>Le disque dur virtuel est de type fixe
**Identification** : Utilisez l’applet de commande `get-vhd` pour récupérer l’objet VHD.  
**Correctif** : Vous pouvez convertir un fichier VHDX en VHD, convertir un disque à expansion dynamique en disque de taille fixe, mais vous ne pouvez pas modifier la génération d’une machine virtuelle.
Utilisez [Gestionnaire Hyper-V ou PowerShell](/azure/virtual-machines/windows/prepare-for-upload-vhd-image#use-hyper-v-manager-to-convert-the-disk) pour convertir le disque.

### <a name="vhd-has-minimum-virtual-size-of-at-least-20-mb"></a>Le disque dur virtuel a une taille virtuelle minimale d’au moins 20 Mo
**Identification** : Utilisez l’applet de commande `get-vhd` pour récupérer l’objet VHD.  
**Correctif** : Utilisez [Gestionnaire Hyper-V ou PowerShell](/azure/virtual-machines/windows/prepare-for-upload-vhd-image#use-hyper-v-manager-to-resize-the-disk) pour redimensionner le disque. 

### <a name="vhd-is-aligned"></a>Le disque dur virtuel est aligné
**Identifiez** : Utilisez l’applet de commande `get-vhd` pour récupérer l’objet VHD.  
**Correctif** : La taille virtuelle doit être un multiple de 1 Mo. 

Les disques doivent avoir une taille virtuelle alignée sur 1 Mio. Si votre disque dur virtuel est une fraction de 1 Mio, vous devez le redimensionner en un multiple de 1 Mio. Les disques qui sont des fractions d’un Mio entraînent des erreurs lors de la création d’images à partir du disque dur virtuel chargé. Pour vérifier la taille, vous pouvez utiliser la cmdlet PowerShell Get-VHD pour afficher « Size », qui doit être un multiple de 1 Mio dans Azure, et « FileSize », qui est égal à « Size » plus 512 octets pour le pied de page VHD.

Utilisez [Gestionnaire Hyper-V ou PowerShell](/azure/virtual-machines/windows/prepare-for-upload-vhd-image#use-hyper-v-manager-to-resize-the-disk) pour redimensionner le disque. 


### <a name="vhd-blob-length"></a>Longueur blob VHD
**Identification** : Utilisez l’applet de commande `get-vhd` pour afficher `Size`   
**Correctif** : Longueur du blob VHD = taille virtuelle + longueur du pied de page VHD (512). Un petit pied de page à la fin de l’objet blob décrit les propriétés du disque dur virtuel. `Size` doit être un multiple de 1 Mio dans Azure et `FileSize`, qui sera égal à `Size` + 512 octets pour le pied de page du disque dur virtuel.

Utilisez [Gestionnaire Hyper-V ou PowerShell](/azure/virtual-machines/windows/prepare-for-upload-vhd-image#use-hyper-v-manager-to-resize-the-disk) pour redimensionner le disque. 

### <a name="generation-one-vms"></a>Machine virtuelle de génération 1
**Identification** : Pour vérifier si votre machine virtuelle est de génération 1, utilisez l’applet de commande `Get-VM | Format-Table Name, Generation`.  
**Correctif** : Vous devez recréer votre machine virtuelle dans votre hyperviseur (Hyper-V).