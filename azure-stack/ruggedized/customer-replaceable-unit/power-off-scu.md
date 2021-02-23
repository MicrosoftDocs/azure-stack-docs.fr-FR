---
title: Éteindre les nœuds d’unité d’échelle
description: Découvrez comment mettre hors tension des nœuds d’unité d’échelle
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: f35c21320a90a68c7933e9fd0bf898496fda9398
ms.sourcegitcommit: 5ea0e915f24c8bcddbcaf8268e3c963aa8877c9d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100488052"
---
# <a name="powering-off-scale-unit-nodes"></a>Mise hors tension de nœuds d’unité d’échelle

Avant de pouvoir mettre hors tension un nœud d’unité d’échelle, vous devez vérifier l’intégrité de l’unité d’échelle et identifier le nœud à réparer.

Si l’**État de l’alimentation** du nœud n’est pas **Arrêté**, appliquez la procédure suivante pour arrêter le nœud de manière sécurisée.

**Étapes**

1.  Videz le nœud d’unité d’échelle.

    1.  À partir du portail d’administration, sélectionnez le nœud nécessitant une réparation, puis sélectionnez **Drainer**.

        ![Capture d’écran montrant la page « Administration » avec l’action « Drainer » sélectionnée et un nœud mis en surbrillance.](media/image-23.png)
        
    1.  À l’invite, entrez le nom du nœud à drainer, puis sélectionnez **Oui**.

        ![Capture d’écran montrant la page « Administration - Nœuds ».](media/image-24.png)
    
    1.  Vous verrez une notification indiquant que l’opération de drainage est en cours.
    
        ![Capture d’écran montrant la page « Administration - Nœuds » avec la notification de drainage affichée.](media/image-25.png)
        
    1.  Connectez-vous à l’interface de l’iDRAC et vérifiez l’étiquette de service du nœud dans le volet **System Information**.
    

2.  Arrêtez le nœud d’unité d’échelle.

    1.  Une fois l’opération de drainage terminée, resélectionnez le nœud, vérifiez que l’**État opérationnel** est **Maintenance** et sélectionnez **Arrêter**.

        ![Capture d’écran montrant la page « Administration - Nœuds » avec un nœud et l’action « Arrêter » sélectionnée.](media/image-26.png)
        
    1.  À l’invite, sélectionnez **Oui** pour confirmer l’arrêt.
    
        ![Capture d’écran montrant la page « Administration - Nœuds » avec la boîte de dialogue « Nœud Arrêt » affichée.](media/image-27.png)
        
    1.  Vous verrez une notification indiquant que l’arrêt est en cours.

        ![Capture d’écran montrant la page « Administration - Nœuds » avec la boîte de dialogue de progression « Arrêt » affichée.](media/image-28.png)
    
    1.  Une fois l’arrêt terminé, l’**État de l’alimentation** est **Arrêté**.
    
        ![Capture d’écran montrant la page « Administration - Nœuds » avec un nœud sélectionné et « Arrêté » mis en surbrillance pour « État de l’alimentation ».](media/image-29.png)
        