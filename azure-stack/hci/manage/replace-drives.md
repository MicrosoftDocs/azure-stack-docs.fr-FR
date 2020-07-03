---
title: Remplacer les lecteurs défectueux sur Azure Stack HCI
description: Guide pratique pour remplacer les lecteurs défectueux sur Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 06/24/2020
ms.openlocfilehash: 93aa7035719fcfd6d96be0c21fc4616608e8af85
ms.sourcegitcommit: bd775dfb298ba1dc67ac9ac7d591794179151026
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85764556"
---
# <a name="replace-failed-drives-on-azure-stack-hci"></a>Remplacer les lecteurs défectueux sur Azure Stack HCI

Azure Stack HCI fonctionne avec les lecteurs SATA, SAS, NVMe ou à mémoire persistante en attachement direct qui sont physiquement attachés à un seul serveur chacun. En cas de défaillance d’un lecteur, vous devez accéder au matériel du serveur physique pour le remplacer.

## <a name="find-the-alert"></a>Trouver l’alerte
En cas de défaillance d’un lecteur, une alerte apparaît dans la zone **Alertes** en haut à gauche du tableau de bord de **Windows Admin Center**. Vous pouvez également sélectionner **Lecteurs** dans le volet de navigation à gauche, ou cliquer sur le lien **VIEW DRIVES >** (VOIR LES LECTEURS) sur la vignette en bas à droite pour parcourir les lecteurs et voir leur état par vous-même. Sous l’onglet **Affichage**, la grille prend en charge le tri, le regroupement et la recherche par mot clé.

1. Dans le tableau de bord, cliquez sur l’alerte pour en voir les détails, par exemple l’emplacement physique du lecteur.
1. Pour en savoir plus, cliquez sur le raccourci **Go to drive** (Accéder au lecteur), qui permet d’accéder à la page de détails **Drive** (Lecteur).
1. Si votre matériel le prend en charge, vous pouvez cliquer sur **Turn light on/off** (Allumer/éteindre le voyant) pour contrôler le témoin lumineux du lecteur.
   La fonctionnalité Espaces de stockage direct effectue automatiquement la mise hors service et le retrait des disques défectueux. Quand cela se produit, le lecteur est à l’état Retired (Hors service), et sa barre de capacité de stockage est vide.
1. Enlevez le lecteur défectueux, puis insérez l’équipement de remplacement.

## <a name="wait-for-the-alert-to-clear"></a>Attendez que l’alerte disparaisse
Dans **Lecteurs > Inventaire**, le nouveau lecteur apparaît. Au bout d’un certain temps, l’alerte disparaît, les volumes retournent à l’état OK, et le stockage se rééquilibre par rapport au nouveau lecteur. Aucune action de l’utilisateur n’est nécessaire.

## <a name="next-steps"></a>Étapes suivantes
- Pour en savoir plus sur le suivi de l’intégrité du stockage à différents niveaux, notamment au niveau du lecteur, consultez [États d’intégrité et opérationnels](/windows-server/storage/storage-spaces/storage-spaces-states).
- Si vous utilisez PMem, consultez [Comprendre et déployer la mémoire persistante](/windows-server/storage/storage-spaces/deploy-pmem).