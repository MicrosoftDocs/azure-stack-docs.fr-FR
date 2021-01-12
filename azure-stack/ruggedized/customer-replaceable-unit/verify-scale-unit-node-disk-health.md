---
title: Vérifier l’intégrité du disque de nœud d’unité d’échelle
description: Découvrez comment vérifier l’intégrité du disque de nœud d’unité d’échelle
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 35deff6c48c02c1deda13bb3cd8dec87e54f52ef
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97909869"
---
# <a name="verifying-scale-unit-node-disk-health"></a>Vérification de l’intégrité du disque de nœud d’unité d’échelle

1.  Connectez-vous au point de terminaison privilégié (PEP).

    1.  Pour obtenir des instructions sur la connexion au point de terminaison privilégié, consultez Accès à la station de travail à accès privilégié et aux points de terminaison privilégiés.

    1.  Une fois connecté, entrez la session PEP, `Enter-PSSession -Session $pep`.

2.  Récupérez l’intégrité du disque virtuel.

    1.  Exécutez `Get-VirtualDisk -cimsession "S-Cluster"` pour vérifier l’intégrité du disque virtuel.

        Si le système ne retourne pas **OK** comme **OperationalStatus** et **Healthy** comme **HealthStatus**, patientez quelques minutes, puis réexécutez la commande.
        
        ![Capture d’écran montrant Windows PowerShell avec les colonnes « OperationsStatus » et « HealthStatus » mises en surbrillance.](media/image-57.png)
        
    1.  Exécutez `Get-VirtualDisk -cimsession "S-Cluster" | Get-StorageJob` pour vérifier que toutes les tâches de stockage en cours d’exécution sont terminées.
    
    1.  Vérifiez qu’aucun résultat n’est retourné. Si des travaux sont toujours en cours d’exécution, comme indiqué par **JobState**, ou si tous les travaux sont marqués comme terminés, patientez 10 minutes, puis réexécutez la même commande. À l’état final, aucun travail ne doit être listé.
    
    1.  Si nécessaire, vous trouverez des étapes de vérification de l’intégrité du stockage supplémentaires dans [Vérifier l’état de la réparation de disque virtuel à l’aide d’Azure Stack Hub PowerShell](https://docs.microsoft.com/azure-stack/operator/azure-stack-replace-disk?view=azs-2002&check-the-status-of-virtual-disk-repair-using-azure-stack-hub-powershell).
        
