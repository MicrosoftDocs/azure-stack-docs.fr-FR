---
title: Mettre sous tension et réparer un nœud d’unité d’échelle
description: Apprenez-en davantage sur la mise sous tension et la réparation d’un nœud d’unité d’échelle.
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 255a40bc2439ae1a6995d4ddf89df192d5e57551
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97392709"
---
# <a name="powering-on-and-repairing-a-scale-unit-node"></a>Mise sous tension et réparation d’un nœud d’unité d’échelle

**Étapes**

Pour réparer et remettre un nœud d’unité d’échelle en production, vous devez appliquer la procédure de réparation Azure Stack Hub.

> [!NOTE]
> La procédure de réparation prend environ trois heures.

1.  Dans le **portail d’administration**, sélectionnez le nœud, puis **Réparer**.

    ![](media/image-52.png)

1.  Indiquez l’**Adresse IP BMC** qui correspond au nœud que vous réparez, puis sélectionnez **Réparer**.

    ![](media/image-53.png)

1.  Suivez la progression dans le volet de notification :

    ![](media/image-54.png)
    
    
    > [!NOTE]
    > Si la procédure de réparation ne se termine pas dans les trois heures, ou si un problème se produit, ouvrez un cas auprès du Support Dell Technologies, qui contactera à son tour le Support Microsoft à des fins de dépannage.
    
    Une fois le processus de réparation terminé, le nœud retourne à un **état opérationnel En cours d’exécution**.
    
