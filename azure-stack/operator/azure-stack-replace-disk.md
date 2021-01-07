---
title: Remplacer un disque physique
titleSuffix: Azure Stack Hub
description: Découvrez comment remplacer un disque physique dans Azure Stack Hub.
author: PatAltimore
ms.topic: how-to
ms.date: 03/04/2020
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 12/02/2019
ms.openlocfilehash: 17c8a963ec49ebefff08f56a3411e71ae9d68011
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97869489"
---
# <a name="replace-a-physical-disk-in-azure-stack-hub"></a>Remplacer un disque physique dans Azure Stack Hub

Cet article décrit le processus général de remplacement d’un disque physique dans Azure Stack Hub. En cas de défaillance d’un disque physique, vous devez le remplacer dès que possible.

> [!Note]  
> Le remplacement d’un lecteur de données physique **n’exige pas** que le nœud d’unité d’échelle soit d’abord mis en mode de maintenance (drainage). De même, une fois le disque physique remplacé, le nœud d’unité d’échelle n’a pas besoin d’être réparé avec le portail d’administration Azure Stack Hub. L’article suivant contient plus d’informations sur la nécessité des réparations : [Remplacer un composant matériel sur un nœud d’unité d’échelle Azure Stack Hub](azure-stack-replace-component.md).

Vous pouvez suivre cette procédure pour les déploiements présentant des disques remplaçables à chaud.

Les étapes de remplacement de disque varient en fonction de votre fabricant de matériel OEM. Pour obtenir des instructions détaillées propres à votre système, consultez la documentation FRU (Field Replaceable Unit) de votre fournisseur.

## <a name="review-disk-alert-information"></a>Examiner les informations sur les alertes de disque
En cas de défaillance d’un disque, vous recevez une alerte indiquant que la connectivité à un disque physique a été perdue.

![Alerte montrant la perte de connectivité au disque physique dans l’administration Azure Stack Hub](media/azure-stack-replace-disk/DiskAlert.png)

Si vous ouvrez l’alerte, la description contient le nœud d’unité d’échelle et l’emplacement exact du logement physique pour le disque que vous devez remplacer. Azure Stack Hub vous aide à identifier le disque défaillant à l’aide de fonctionnalités d’indicateurs LED.

## <a name="replace-the-physical-disk"></a>Remplacer le disque physique

Suivez les instructions FRU de votre fabricant de matériel OEM pour remplacer le disque.

> [!note]
> Remplacez des disques pour un nœud d’unité d’échelle à la fois. Attendez la fin des tâches de réparation du disque virtuel pour passer au nœud d’unité d’échelle suivant.

Pour empêcher l’utilisation d’un disque non pris en charge dans un système intégré, le système bloque les disques qui ne sont pas pris en charge par votre fournisseur. Si vous essayez d’utiliser un disque non pris en charge, une nouvelle alerte vous indique qu’un disque a été mis en quarantaine en raison d’un modèle ou d’un microprogramme non pris en charge.

Une fois le disque remplacé, Azure Stack Hub détecte automatiquement le nouveau disque et commence le processus de réparation de disque virtuel.

## <a name="check-the-status-of-virtual-disk-repair-using-azure-stack-hub-powershell"></a>Vérifier l’état de la réparation de disque virtuel à l’aide d’Azure Stack Hub PowerShell

Après avoir remplacé le disque, vous pouvez superviser l’état d’intégrité du disque virtuel et la progression de l'opération de réparation à l’aide d’Azure Stack Hub PowerShell.

1. Vérifiez qu’Azure Stack Hub PowerShell est installé. Pour plus d’informations, consultez [Installer PowerShell pour Azure Stack Hub](powershell-install-az-module.md).
2. Connectez-vous à Azure Stack Hub en tant qu’opérateur à l’aide de PowerShell. Pour plus d’informations, consultez [Se connecter à Azure Stack Hub en tant qu’opérateur à l’aide de PowerShell](azure-stack-powershell-configure-admin.md).
3. Exécutez les applets de commande suivantes pour vérifier l’intégrité du disque virtuel et l’état de la réparation :

    ```powershell  
    $scaleunit=Get-AzsScaleUnit
    $StorageSubSystem=Get-AzsStorageSubSystem -ScaleUnit $scaleunit.Name
    Get-AzsVolume -StorageSubSystem $StorageSubSystem.Name -ScaleUnit $scaleunit.name | Select-Object VolumeLabel, OperationalStatus, RepairStatus
    ```

    ![Intégrité des volumes Azure Stack Hub dans PowerShell](media/azure-stack-replace-disk/get-azure-stack-volumes-health.png)

4. Validez l’état du système Azure Stack Hub. Pour obtenir des instructions, consultez [Valider l’état du système Azure Stack Hub](azure-stack-diagnostic-test.md).
5. Si vous le souhaitez, vous pouvez exécuter la commande suivante pour vérifier l’état du disque physique remplacé.

    ```powershell  
    $scaleunit=Get-AzsScaleUnit
    $StorageSubSystem=Get-AzsStorageSubSystem -ScaleUnit $scaleunit.Name

    Get-AzsDrive -StorageSubSystem $StorageSubSystem.Name -ScaleUnit $scaleunit.name | Sort-Object StorageNode,MediaType,PhysicalLocation | Format-Table Storagenode, Healthstatus, PhysicalLocation, Model, MediaType,  CapacityGB, CanPool, CannotPoolReason
    ```

    ![Disques physiques remplacés dans Azure Stack Hub avec PowerShell](media/azure-stack-replace-disk/check-replaced-physical-disks-azure-stack.png)

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

4. Validez l’état du système Azure Stack Hub. Pour obtenir des instructions, consultez [Valider l’état du système Azure Stack Hub](azure-stack-diagnostic-test.md).

## <a name="troubleshoot-virtual-disk-repair-using-the-privileged-endpoint"></a>Résoudre les problèmes de réparation de disque virtuel à l’aide du point de terminaison privilégié

Si le travail de réparation de disque virtuel semble bloqué, exécutez la commande suivante pour le redémarrer :

```powershell
Get-VirtualDisk -CimSession s-cluster | Repair-VirtualDisk
```
