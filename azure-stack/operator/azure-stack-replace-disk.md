---
title: Remplacer un disque physique dans Azure Stack | Microsoft Docs
description: Explique comment remplacer un disque physique dans Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 449ae53e-b951-401a-b2c9-17fee2f491f1
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/04/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 06/04/2019
ms.openlocfilehash: 5d0c3bdff2684c90f118e26ac62b8219802fa25b
ms.sourcegitcommit: b8260ef3e43f3703dd0df16fb752610ec8a86942
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2019
ms.locfileid: "70008651"
---
# <a name="replace-a-physical-disk-in-azure-stack"></a>Remplacer un disque physique dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Cet article décrit le processus général de remplacement d’un disque physique dans Azure Stack. En cas de défaillance d’un disque physique, vous devez le remplacer dès que possible.

Vous pouvez appliquer cette procédure pour les systèmes intégrés et pour les déploiements de Kit de développement qui ont des disques remplaçables à chaud.

Les étapes de remplacement de disque varient en fonction de votre fabricant de matériel OEM. Pour obtenir des instructions détaillées propres à votre système, consultez la documentation FRU (Field Replaceable Unit) de votre fournisseur.

## <a name="review-disk-alert-information"></a>Examiner les informations sur les alertes de disque
En cas de défaillance d’un disque, vous recevez une alerte indiquant que la connectivité à un disque physique a été perdue.

![Alerte montrant la perte de connectivité au disque physique](media/azure-stack-replace-disk/DiskAlert.png)

Si vous ouvrez l’alerte, la description contient le nœud d’unité d’échelle et l’emplacement exact du logement physique pour le disque que vous devez remplacer. Azure Stack vous aide à identifier le disque en panne à l’aide de fonctionnalités d’indicateurs LED.

## <a name="replace-the-disk"></a>Remplacer le disque

Suivez les instructions FRU de votre fabricant de matériel OEM pour remplacer le disque.

> [!note]
> Remplacez des disques pour un nœud d’unité d’échelle à la fois. Attendez que les tâches de réparation du disque virtuel soient terminées avant de passer au nœud d’unité d’échelle suivant

Pour empêcher toute utilisation d’un disque non pris en charge dans un système intégré, le système bloque les disques qui ne sont pas pris en charge par votre fournisseur. Si vous essayez d’utiliser un disque non pris en charge, une alerte signale qu’un disque a été mis en quarantaine à cause d’un modèle ou d’un microprogramme non pris en charge.

Une fois le disque remplacé, Azure Stack détecte automatiquement le nouveau disque et commence le processus de réparation de disque virtuel.

## <a name="check-the-status-of-virtual-disk-repair-using-azure-stack-powershell"></a>Vérifier l’état de la réparation de disque virtuel à l’aide d’Azure Stack PowerShell

Après avoir remplacé le disque, vous pouvez superviser l’état d’intégrité du disque virtuel et la progression du travail de réparation à l’aide d’Azure Stack PowerShell.

1. Vérifiez qu’Azure Stack PowerShell est installé. Pour plus d’informations, consultez [Installer PowerShell pour Azure Stack](azure-stack-powershell-install.md).
2. Connectez-vous en tant qu’opérateur à Azure Stack à l’aide de PowerShell. Pour plus d’informations, consultez [Se connecter à Azure Stack en tant qu’opérateur à l’aide de PowerShell](azure-stack-powershell-configure-admin.md).
3. Exécutez les applets de commande suivantes pour vérifier l’intégrité du disque virtuel et l’état de la réparation :
    ```powershell  
    $scaleunit=Get-AzsScaleUnit
    $StorageSubSystem=Get-AzsStorageSubSystem -ScaleUnit $scaleunit.Name
    Get-AzsVolume -StorageSubSystem $StorageSubSystem.Name -ScaleUnit $scaleunit.name | Select-Object VolumeLabel, OperationalStatus, RepairStatus
    ```

    ![Intégrité des volumes Azure Stack](media/azure-stack-replace-disk/get-azure-stack-volumes-health.png)

4. Validez l’état du système Azure Stack. Pour obtenir des instructions, consultez [Valider l’état du système Azure Stack](azure-stack-diagnostic-test.md).
5. Si vous le souhaitez, vous pouvez exécuter la commande suivante pour vérifier l’état du disque physique remplacé.

```powershell  
$scaleunit=Get-AzsScaleUnit
$StorageSubSystem=Get-AzsStorageSubSystem -ScaleUnit $scaleunit.Name

Get-AzsDrive -StorageSubSystem $StorageSubSystem.Name -ScaleUnit $scaleunit.name | Sort-Object StorageNode,MediaType,PhysicalLocation | Format-Table Storagenode, Healthstatus, PhysicalLocation, Model, MediaType,  CapacityGB, CanPool, CannotPoolReason
```

![Disques physiques remplacés dans Azure Stack](media/azure-stack-replace-disk/check-replaced-physical-disks-azure-stack.png)

## <a name="check-the-status-of-virtual-disk-repair-using-the-privileged-endpoint"></a>Vérifier l’état de la réparation de disque virtuel à l’aide du point de terminaison privilégié
 
Après avoir remplacé le disque, vous pouvez surveiller l’état d’intégrité du disque virtuel et la progression du travail de réparation à l’aide du point de terminaison privilégié. Suivez ces étapes à partir de n’importe quel ordinateur qui dispose d’une connectivité réseau au point de terminaison privilégié.

1. Ouvrez une session Windows PowerShell et connectez-vous au point de terminaison privilégié.
    ```powershell
        $cred = Get-Credential
        Enter-PSSession -ComputerName <IP_address_of_ERCS>`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
    ``` 
  
2. Exécutez la commande suivante pour afficher l’intégrité du disque virtuel :
    ```powershell
        Get-VirtualDisk -CimSession s-cluster
    ```
   ![Sortie PowerShell de la commande Get-VirtualDisk](media/azure-stack-replace-disk/GetVirtualDiskOutput.png)

3. Exécutez la commande suivante pour afficher l’état actuel du travail de stockage :
    ```powershell
        Get-VirtualDisk -CimSession s-cluster | Get-StorageJob
    ```
      ![Sortie PowerShell de la commande Get-StorageJob](media/azure-stack-replace-disk/GetStorageJobOutput.png)

4. Validez l’état du système Azure Stack. Pour obtenir des instructions, consultez [Valider l’état du système Azure Stack](azure-stack-diagnostic-test.md).


## <a name="troubleshoot-virtual-disk-repair-using-the-privileged-endpoint"></a>Résoudre les problèmes de réparation de disque virtuel à l’aide du point de terminaison privilégié

Si le travail de réparation de disque virtuel semble bloqué, exécutez la commande suivante pour le redémarrer :
  ```powershell
        Get-VirtualDisk -CimSession s-cluster | Repair-VirtualDisk
  ``` 
