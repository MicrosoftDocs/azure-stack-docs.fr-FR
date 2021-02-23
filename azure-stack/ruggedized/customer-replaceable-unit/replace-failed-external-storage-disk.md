---
title: Remplacer un lecteur de données défectueux
description: Découvrez comment remplacer un lecteur de données défectueux
author: PatAltimore
ms.topic: how-to
ms.date: 02/05/2021
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: a5b7796794a0444a434fdb9a628ff04b93adcc03
ms.sourcegitcommit: 5ea0e915f24c8bcddbcaf8268e3c963aa8877c9d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100488103"
---
# <a name="replacing-a-failed-data-drive"></a>Remplacement d’un lecteur de données défectueux

Procédez comme suit pour remplacer un lecteur de données défectueux.

## <a name="prerequisites"></a>Prérequis

1.  Passez en revue les *Remarques, précautions et avertissements* fournis au début de ce guide.

2.  Passez en revue les précautions relatives à la manipulation.

3.  Révision

    -   Connaissances requises pour l’utilisation de nœuds d’unité d’échelle dans une appliance cloud renforcée si vous utilisez un nœud d’unité d’échelle

    -   Connaissances requises pour l’utilisation de l’hôte de cycle de vie du matériel si vous utilisez l’hôte de cycle de vie du matériel

4.  Terminé

    -   Vérification de l’accès et de l’intégrité du nœud d’unité d’échelle si vous en utilisez un

    -   Vérification de l’intégrité et l’accès de l’hôte de cycle de vie du matériel si vous utilisez l’hôte de cycle de vie du matériel

5.  Terminé

    -   Mise hors tension des nœuds d’unité d’échelle si vous utilisez un nœud d’unité d’échelle

    -   Mise hors tension de l’hôte de cycle de vie du matériel si vous utilisez l’hôte de cycle de vie du matériel

## <a name="steps"></a>Étapes

1.  Localisez le nœud physique dans le rack.

2.  Vérifier le modèle de disque.

    Vérifiez que le nouveau modèle de disque est répertorié comme étant pris en charge dans la [Matrice de prise en charge](https://www.dell.com/support/home/product-support/product/cloud-for-microsoft-azure-stack14g/docs#q%3Dsupport%20matrix%26sort%3Ddate%20descending%26f%3Alang%3D%5Ben%5D).
    Si le disque ne figure pas dans la matrice de prise en charge, vous devez demander un autre remplacement.
    
    > [!CAUTION]
    > L’insertion d’un disque qui ne figure pas dans la matrice de prise en charge entraîne la mise en quarantaine du nouveau disque.
        
    Pour vérifier le modèle, localisez l’étiquette et vérifiez que le composant figure dans la matrice de prise en charge.
    
3.  Remplacez le lecteur de données défectueux.

    Suivez le [processus de remplacement de support de disque](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4) pour les nœuds d’unité d’échelle ou les hôtes de cycle de vie du matériel.
    
    Vous devez également vous reporter aux sections appropriées relatives au retrait d’un disque et à l’installation d’un disque dans un support de disque ou un adaptateur de disque.
    
4.  Après avoir remplacé le disque, vérifiez que le microprogramme est pris en charge en vous référant à la matrice de prise en charge. Pour récupérer la version du microprogramme du disque, effectuez les étapes suivantes :

    1.  Connectez-vous à l’iDRAC à l’aide de l’adresse IP routable ou directement en accédant à l’interface de l’iDRAC par le biais d’une connexion USB directe.

    1.  Dans le menu supérieur, sélectionnez **Storage** :

        ![Capture d’écran montrant la page iDRAC avec l’action « Stockage » sélectionnée.](media/image-30.png)
    
    1.  Localisez le disque remplacé et développez l’élément. Comparez la **révision** du disque à celle mentionnée dans la matrice de prise en charge. Si le disque ne figure pas dans la matrice de prise en charge, contactez le support afin de résoudre le problème.

        ![Capture d’écran montrant la page « Propriétés avancées » iDRAC avec les options « Fabricant », « ID de produit » et « Révision » mises en surbrillance.](media/image-31.png)
        
## <a name="next-steps"></a>Étapes suivantes

Si vous utilisez un nœud d’unité d’échelle :

1.  Procédez à la vérification de l’intégrité du nœud d’unité d’échelle.

2.  Procédez à la vérification de l’intégrité du disque du nœud d’unité d’échelle si vous utilisez l’hôte de cycle de vie du matériel :

    -   Procédez à la vérification de l’intégrité de l’hôte de cycle de vie du matériel
    
