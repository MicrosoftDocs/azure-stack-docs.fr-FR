---
title: Mettre sous tension et réparer un nœud d’unité d’échelle
description: Apprenez-en davantage sur la mise sous tension et la réparation d’un nœud d’unité d’échelle.
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: d05c696f62aad4d9305137edab777d69b6442814
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97909920"
---
# <a name="powering-on-and-repairing-a-scale-unit-node"></a>Mise sous tension et réparation d’un nœud d’unité d’échelle

**Étapes**

Pour réparer et remettre un nœud d’unité d’échelle en production, vous devez appliquer la procédure de réparation Azure Stack Hub.

> [!NOTE]
> La procédure de réparation prend environ trois heures.

1.  Dans le **portail d’administration**, sélectionnez le nœud, puis **Réparer**.

    ![Capture d’écran montrant la page « Administration - Nœuds » avec un nœud et l’action « Réparer » sélectionnée.](media/image-52.png)

1.  Indiquez l’**Adresse IP BMC** qui correspond au nœud que vous réparez, puis sélectionnez **Réparer**.

    ![Capture d’écran montrant la page « Administration - Nœuds » avec un nœud sélectionné, l’adresse IP mise en surbrillance et la boîte de dialogue « Nœud Réparer » affichée.](media/image-53.png)

1.  Suivez la progression dans le volet de notification :

    ![Capture d’écran montrant le volet « Notifications » et l’option « Nœud Réparation - En cours d’exécution ».](media/image-54.png)
    
    
    > [!NOTE]
    > Si la procédure de réparation ne se termine pas dans les trois heures, ou si un problème se produit, ouvrez un cas auprès du Support Dell Technologies, qui contactera à son tour le Support Microsoft à des fins de dépannage.
    
    Une fois le processus de réparation terminé, le nœud retourne à un **état opérationnel En cours d’exécution**.
    
