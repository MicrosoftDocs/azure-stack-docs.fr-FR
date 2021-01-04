---
title: Vérifier l’intégrité du disque de nœud d’unité d’échelle
description: Découvrez comment vérifier l’intégrité du disque de nœud d’unité d’échelle
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: bebff3ff8970a43d0893f057a94ec3dfbf0a7fc9
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97392588"
---
# <a name="verifying-scale-unit-node-disk-health"></a>Vérification de l’intégrité du disque de nœud d’unité d’échelle

1.  Connectez-vous au point de terminaison privilégié (PEP).

    1.  Pour obtenir des instructions sur la connexion au point de terminaison privilégié, consultez Accès à la station de travail à accès privilégié et aux points de terminaison privilégiés.

    1.  Une fois connecté, entrez la session PEP, `Enter-PSSession -Session $pep`.

2.  Récupérez l’intégrité du disque virtuel.

    1.  Exécutez `Get-VirtualDisk -cimsession "S-Cluster"` pour vérifier l’intégrité du disque virtuel.

        Si le système ne retourne pas **OK** comme **OperationalStatus** et **Healthy** comme **HealthStatus**, patientez quelques minutes, puis réexécutez la commande.
        
        ![](media/image-57.png)
        
    1.  Exécutez `Get-VirtualDisk -cimsession "S-Cluster" | Get-StorageJob` pour vérifier que toutes les tâches de stockage en cours d’exécution sont terminées.
    
    1.  Vérifiez qu’aucun résultat n’est retourné. Si des travaux sont toujours en cours d’exécution, comme indiqué par **JobState**, ou si tous les travaux sont marqués comme terminés, patientez 10 minutes, puis réexécutez la même commande. À l’état final, aucun travail ne doit être listé.
    
    1.  Si nécessaire, vous trouverez des étapes de vérification de l’intégrité du stockage supplémentaires dans [Vérifier l’état de la réparation de disque virtuel à l’aide d’Azure Stack Hub PowerShell](https://docs.microsoft.com/azure-stack/operator/azure-stack-replace-disk?view=azs-2002&check-the-status-of-virtual-disk-repair-using-azure-stack-hub-powershell).
        
