---
title: Remplacer un disque de système d’exploitation
description: Découvrez comment remplacer un disque de système d’exploitation.
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 36050d42a012ffc40db98ab506b1ef81086e609a
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97867636"
---
# <a name="replacing-an-operating-system-disk"></a>Remplacement d’un disque de système d’exploitation

Appliquez la procédure suivante pour remplacer un disque de système d’exploitation défectueux dans un nœud d’unité d’échelle.

## <a name="prerequisites"></a>Prérequis

1.  Passez en revue les *Remarques, précautions et avertissements* fournis au début de ce guide.

2.  Passez en revue les précautions relatives à la manipulation.

3.  Connaissances requises pour l’utilisation des nœuds d’unité d’échelle dans une appliance cloud tactique si vous utilisez un nœud d’unité d’échelle.

4.  Effectuez la vérification de l’accès et de l’intégrité des nœuds d’unité d’échelle.

5.  Effectuez la mise hors tension des nœuds d’unité d’échelle.

    Pour les nœuds d’unité d’échelle Azure Stack Hub, le système d’exploitation s’exécute à partir d’une paire mise en miroir de modules de disques SSD M.2 résidant sur la carte Dell PowerEdge Boot Optimized Storage Solution (BOSS). Vous devez mettre le système hors tension avant de remplacer un disque de système d’exploitation.
    
## <a name="steps"></a>Étapes

1.  Localisez le nœud physique dans le rack.

2.  Vérifiez que le nœud est hors tension. Le voyant d’alimentation doit être orange.

    > [!CAUTION]
    > Avant de déconnecter les câbles sur le serveur sur lequel vous travaillez, vérifiez que chaque câble est correctement étiqueté. Les câbles DOIVENT être reconnectés aux mêmes ports.
    
3.  Remplacez le module SSD M.2 défectueux.

    Suivez le processus de remplacement de module SSD M.2 indiqué dans [Dell EMC PowerEdge R640 Manuel d’installation et de maintenance](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4) pour les nœuds d’unité d’échelle.
    
4.  Mettez le nœud sous tension.

    Une  fois l’alimentation rétablie, si le serveur ne redémarre pas automatiquement, appuyez sur le bouton d’alimentation pour rallumer le nœud.
    
## <a name="next-steps"></a>Étapes suivantes

1.  Procédez à la mise sous tension et à la réparation du nœud d’unité d’échelle.

2.  Procédez à la vérification de l’intégrité du nœud d’unité d’échelle.

