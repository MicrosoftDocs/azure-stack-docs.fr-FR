---
title: Vue d'ensemble du serveur
description: Découvrir le serveur pour Azure Stack Hub Ruggedized
author: justinha
ms.topic: how-to
ms.date: 01/28/2021
ms.author: justinha
ms.reviewer: thoroet
ms.lastreviewed: ''
ms.openlocfilehash: a922c99121157c03705784dd99b8abf97fbec507
ms.sourcegitcommit: 283b1308142e668749345bf24b63d40172559509
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99577357"
---
# <a name="server-overview"></a>Vue d'ensemble du serveur

Cette section donne une vue d’ensemble des composants serveur.

## <a name="chassis-front-view"></a>Vue frontale du châssis

La figure suivante montre le serveur 2U.


![Diagramme montrant le châssis frontal du serveur.](media/image-60.png)

Le tableau suivant décrit les caractéristiques du panneau frontal.

| Ports, panneaux et emplacements  | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Panneau de configuration gauche        | Contient l’ID système et d’intégrité du système, le voyant d’état et le voyant iDRAC Quick Sync 2 (sans fil). <br>**Remarque :** Le voyant iDRAC Quick Sync 2 n’est disponible que sur certaines configurations. <br>Voyant d’état : vous permet d’identifier les composants matériels défectueux. Il y a jusqu’à cinq voyants d’état et une barre de voyants d’intégrité système globale (ID système et d’intégrité du châssis). <br>Quick Sync 2 (sans fil) : indique que le système est compatible avec Quick Sync. La fonctionnalité Quick Sync est facultative. Elle permet de gérer le système à l’aide d’appareils mobiles. Cette fonctionnalité agrège l’inventaire du matériel ou du microprogramme et diverses informations de diagnostic et d’erreur au niveau du système qui peuvent être utilisées pour la résolution des problèmes du système.  |
| Port VGA                  | Vous permet de connecter un périphérique d’affichage au système.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| Panneau de commande de droite       | Contient le bouton d’alimentation, le port USB, le micro port iDRAC Direct et le voyant d’état iDRAC Direct.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |

## <a name="chassis-back-view"></a>Vue arrière du châssis

La figure ci-dessous montre les composants du châssis arrière.


![Diagramme montrant le châssis arrière du serveur.](media/image-61.png)

Le tableau suivant décrit les caractéristiques du panneau arrière.


| Ports, panneaux et emplacements       | Icône                      | Description                                                                                                                                                                                                                                                                 |
|-------------------------------|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Disques durs                   | N/A                       | Stockage de système d’exploitation et de données pour les nœuds de serveur HLH et SU.                                                                                                                                                                                                                        |
| Module BOSS                   | N/A                       | Module BOSS (Boot Optimized Storage Solution), utilisé pour installer le système d’exploitation ou l’hyperviseur.                                                                                                                                                                                 |
| Blocs d’alimentation (2)        | N/A                       | Ils alimentent le serveur et sont fournies en tandem à des fins de redondance.                                                                                                                                                                                                 |
| Ports USB 3.0                 |  :::image type="icon" source="media/image-62.png"::: | Les ports USB sont conformes à la norme 3.0 et comportent neuf broches. Ils vous permettent de connecter des périphériques USB au système.                                                                                                                                                                     |
| Port VGA                      |   :::image type="icon" source="media/image-63.png":::  | Vous permet de connecter un périphérique d’affichage au système.                                                                                                                                                                                                                      |
| Port série                   |   :::image type="icon" source="media/image-64.png":::  | Vous permet de connecter un périphérique série au système.                                                                                                                                                                                                                       |
| Port iDRAC9 Enterprise        |   :::image type="icon" source="media/image-65.png":::  | Vous permet d’accéder à l’iDRAC à distance.                                                                                                                                                                                                                                       |
| Port d’alimentation CMA                | N/A                       | Le port d’alimentation CMA (Cable Management Arm) vous permet de connecter le câble de voyant d’état au CMA.                                                                                                                                                                     |
| Bouton d’identification du système  |   :::image type="icon" source="media/image-66.png"::: | Le bouton d’identification du système (ID) est disponible à l’avant et à l’arrière du serveur. Un appui sur ce bouton provoque le clignotement du voyant d’ID système\intégrité système. Vous pouvez également utiliser le bouton d’ID système pour réinitialiser l’iDRAC et accéder au BIOS à l’aide du mode pas à pas.  |

## <a name="right-control-panel"></a>Panneau de commande de droite

La figure suivante montre le panneau de contrôle de droite.

![Schéma qui montre le bouton d’alimentation et les ports USB sur le panneau de contrôle.](media/image-67.png)

Le tableau suivant décrit les contrôles et les voyants du panneau de droite.


| Élément  | Voyant ou bouton  | Icône                      | Description                                                                                                                                                                                               |
|-------|----------------------|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1     | Bouton d'alimentation         |   :::image type="icon" source="media/image-68.png":::  | Indique si le système est allumé ou éteint. Appuyez sur le bouton d’alimentation pour allumer ou éteindre manuellement le système.  **Remarque :** Appuyez sur le bouton d’alimentation pour arrêter correctement un système d’exploitation compatible ACPI.  |
| 2     | Port USB             | :::image type="icon" source="media/image-70.png":::    | Le port USB est conforme à la norme 2.0 et comporte quatre broches. Il vous permet de connecter des périphériques USB au système.                                                                                                         |
| 3     | Voyant iDRAC Direct     | N/A                       | Le voyant iDRAC Direct s’allume pour indiquer que le port iDRAC Direct est connecté activement à un appareil.                                                                                        |
| 4     | Port iDRAC Direct    |   :::image type="icon" source="media/image-65.png"::: | Le port iDRAC Direct est compatible avec la norme micro USB 2.0. Il vous permet d’accéder aux fonctionnalités iDRAC Direct.                                                                                              |

## <a name="inside-the-server"></a>Intérieur du serveur

Les figures suivantes montrent les composants serveur internes.

![Schéma qui montre une vue du dessus des composants de serveurs internes R640 HLH.](media/image-71.png)


![Schéma qui montre une vue du dessus des composants de serveurs internes R640 SU.](media/image-72.png)

## <a name="motherboard"></a>Carte mère

L’illustration suivante montre la disposition de la carte mère.


![Schéma qui montre les informations de service R640 et la disposition de la carte mère.](media/image-73.png)

## <a name="memory"></a>Mémoire

La figure suivante montre les informations relatives à la mémoire.


![Schéma qui montre les informations de mémoire R640.](media/image-74.png)
