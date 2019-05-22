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
ms.date: 05/16/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/22/2019
ms.openlocfilehash: 797e49f82938888776b2685ab44add281b730943
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782410"
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
 
## <a name="check-the-status-of-virtual-disk-repair"></a>Vérifier l’état de la réparation de disque virtuel
 
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

## <a name="troubleshoot-virtual-disk-repair"></a>Résoudre les problèmes de réparation de disque virtuel

Si le travail de réparation de disque virtuel semble bloqué, exécutez la commande suivante pour le redémarrer :
  ```powershell
        Get-VirtualDisk -CimSession s-cluster | Repair-VirtualDisk
  ``` 
