---
title: Mettre sous tension et réparer un nœud d’unité d’échelle
description: Découvrez comment mettre sous tension et réparer un nœud d’unité d’échelle
author: PatAltimore
ms.topic: how-to
ms.date: 02/05/2021
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 0a2aa54234b5dccc8f1ce3425906425064463910
ms.sourcegitcommit: 5ea0e915f24c8bcddbcaf8268e3c963aa8877c9d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100488120"
---
# <a name="powering-on-and-repairing-a-scale-unit-node"></a>Mise sous tension et réparation d’un nœud d’unité d’échelle

**Étapes**

Pour réparer et remettre un nœud d’unité d’échelle en production, vous devez suivre la procédure de réparation Azure Stack Hub.

> [!NOTE]
> La procédure de réparation prend environ trois heures.

1.  Dans le **portail d’administration**, sélectionnez le nœud, puis **Réparer**.

    ![Capture d’écran montrant la page « Administration - Nœuds » avec un nœud et l’action « Réparer » sélectionnée.](media/image-52.png)

1.  Indiquez l’**Adresse IP BMC** qui correspond au nœud que vous réparez, puis sélectionnez **Réparer**.

    ![Capture d’écran montrant la page « Administration - Nœuds » avec un nœud sélectionné, l’adresse IP mise en surbrillance et la boîte de dialogue « Nœud Réparer » affichée.](media/image-53.png)

1.  Suivez la progression dans le volet de notification :

    ![Capture d’écran montrant le volet « Notifications » et l’option « Nœud Réparation - En cours d’exécution ».](media/image-54.png)
    
    
    > [!NOTE]
    > Si la procédure de réparation ne se termine pas dans les trois heures, ou si un problème se produit, ouvrez un cas auprès du Support Microsoft à des fins de dépannage.
    
    Une fois le processus de réparation terminé, le nœud retourne à un **état opérationnel En cours d’exécution**.
    
