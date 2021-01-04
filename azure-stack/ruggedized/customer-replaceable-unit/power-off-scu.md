---
title: Éteindre les nœuds d’unité d’échelle
description: Découvrez comment mettre hors tension les nœuds d’unité d’échelle
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: fde7496a54f0c2d3b28d98584295a761c683e17c
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97392597"
---
# <a name="powering-off-scale-unit-nodes"></a>Mise hors tension des nœuds d’unité d’échelle

Avant de pouvoir mettre hors tension un nœud d’unité d’échelle, vous devez vérifier l’intégrité de l’unité d’échelle et identifier le nœud qui doit être réparé.

Si l’**État de l’alimentation** du nœud n’est pas **Arrêté**, appliquez la procédure suivante pour arrêter le nœud de manière sécurisée.

**Étapes**

1.  Drainez le nœud d’unité d’échelle.

    1.  À partir du portail d’administration, sélectionnez le nœud nécessitant une réparation, puis sélectionnez **Drainer**.

        ![](media/image-23.png)
        
    1.  À l’invite, entrez le nom du nœud à drainer, puis sélectionnez **Oui**.

        ![](media/image-24.png)
    
    1.  Vous verrez une notification indiquant que l’opération de drainage est en cours.
    
        ![](media/image-25.png)
        
    1.  Connectez-vous à l’interface de l’iDRAC et vérifiez l’étiquette de service du nœud dans le volet **System Information**.
    

2.  Arrêtez le nœud d’unité d’échelle.

    1.  Une fois l’opération de drainage terminée, resélectionnez le nœud, vérifiez que l’**État opérationnel** est **Maintenance** et sélectionnez **Arrêter**.

        ![](media/image-26.png)
        
    1.  À l’invite, sélectionnez **Oui** pour confirmer l’arrêt.
    
        ![](media/image-27.png)
        
    1.  Vous verrez une notification indiquant que l’arrêt est en cours.

        ![](media/image-28.png)
    
    1.  Une fois l’arrêt terminé, l’**État de l’alimentation** est **Arrêté**.
    
        ![](media/image-29.png)
        