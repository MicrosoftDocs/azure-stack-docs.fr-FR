---
title: Remplacer un module DIMM
description: Découvrez comment remplacer un module DIMM.
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: dbbeaa330e47fda4208ba0e44211fe41d01a982f
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97392690"
---
# <a name="replacing-a-dimm"></a>Remplacement d’un module DIMM

Appliquez la procédure suivante pour ajouter ou remplacer un module DIMM.

## <a name="prerequisites"></a>Prérequis

1.  Passez en revue les *Remarques, précautions et avertissements* fournis au début de ce guide.

2.  Passez en revue les précautions relatives à la manipulation.

3.  Révision

    -   Connaissances requises pour l’utilisation des nœuds d’unité d’échelle dans une appliance cloud tactique si vous utilisez un nœud d’unité d’échelle

    -   Connaissances requises pour l’utilisation de l’hôte de cycle de vie du matériel si vous utilisez l’hôte de cycle de vie du matériel

4.  Terminé

    -   Vérification de l’accès et de l’intégrité des nœuds d’unité d’échelle si vous utilisez un nœud d’unité d’échelle

    -   Vérification de l’intégrité et l’accès de l’hôte de cycle de vie du matériel si vous utilisez l’hôte de cycle de vie du matériel

5.  Terminé

    -   Mise hors tension des nœuds d’unité d’échelle si vous utilisez un nœud d’unité d’échelle

    -   Mise hors tension de l’hôte de cycle de vie du matériel si vous utilisez l’hôte de cycle de vie du matériel

## <a name="steps"></a>Étapes

1.  Localisez le nœud physique dans le rack.

2.  Vérifiez que le nœud est hors tension. Le voyant d’alimentation doit être orange.

    > [!CAUTION]
    > Avant de déconnecter les câbles sur le serveur sur lequel vous travaillez, vérifiez que chaque câble est correctement étiqueté. Les câbles DOIVENT être reconnectés aux mêmes ports.
    
3.  Remplacez le ou les modules DIMM.

    Suivez le processus de remplacement de module de mémoire indiqué dans [Dell EMC PowerEdge R640 Manuel d’installation et de maintenance](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4) pour les nœuds d’unité d’échelle ou les hôtes de cycle de vie du matériel.
    
4.  Mettez le nœud sous tension.

    Une  fois l’alimentation rétablie, si le serveur ne redémarre pas automatiquement, appuyez sur le bouton d’alimentation pour rallumer le nœud.
    
## <a name="next-steps"></a>Étapes suivantes

Si vous utilisez un nœud d’unité d’échelle :

1.  Procédez à la mise sous tension du nœud d’unité d’échelle.

2.  Procédez à la vérification de l’intégrité du nœud d’unité d’échelle. Si vous travaillez avec l’hôte de cycle de vie du matériel, procédez à la vérification de l’intégrité de l’hôte de cycle de vie du matériel.
    
