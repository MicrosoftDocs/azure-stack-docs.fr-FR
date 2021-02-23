---
title: Notes de publication d’Event Hubs sur Azure Stack Hub 1.2012.1.0
description: Découvrez les notes de publication 1.2012.1.0 pour Event Hubs sur Azure Stack Hub, qui englobent les correctifs de bogues, les fonctionnalités et le mode d’installation de la mise à jour.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: article
ms.date: 02/15/2021
ms.reviewer: kalkeea
ms.lastreviewed: 02/15/2021
ms.openlocfilehash: ef28447b87c0647b65c996112c5739bf7c6caccf
ms.sourcegitcommit: 34babe5abf1bceee718011b5c5c25f75e1b03b0c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100563526"
---
# <a name="event-hubs-on-azure-stack-hub-1201210-release-notes"></a>Notes de publication d’Event Hubs sur Azure Stack Hub 1.2012.1.0

Ces notes de publication décrivent les améliorations et les correctifs introduits dans Event Hubs sur Azure Stack Hub version 1.2012.1.0, ainsi que les problèmes connus. 

[!INCLUDE [Azure Stack Hub update reminder](../includes/event-hubs-hub-update-banner.md)]

## <a name="updates-in-this-release"></a>Mises à jour dans cette mise en production

Cette version inclut les mises à jour suivantes :

- Pour les développeurs du kit de développement logiciel (SDK) du portail Azure, le portail version 5.0.303.7361 bénéficie désormais d’un support.
- Améliorations de la journalisation interne pour les clusters Event Hubs.

## <a name="issues-fixed-in-this-release"></a>Problèmes résolus dans cette version

Cette version inclut les corrections suivantes :

- Un correctif de l’ordre de mise à niveau pour les clusters Event Hubs, afin de résoudre un problème de mise à niveau.
- L’intégrité du cluster et le contrôle d’intégrité de la sauvegarde pour les clusters Event Hubs n’étaient pas actifs lorsque l’état des clusters était « Mise à niveau » ou « Échec de la mise à niveau ». Ce problème a été résolu dans cette mise en production.
- Correction d’un bogue qui avait pour effet que des enregistrements d’utilisation indiquaient une quantité erronée. Au lieu de cœurs, nous émettions des unités de capacité (CU). Auparavant, un cluster 1 CU affichait 1 cœur dans l’utilisation horaire. Les utilisateurs verront à présent la quantité correcte de 10 cœurs pour un cluster 1 CU dans leur utilisation horaire.

## <a name="known-issues"></a>Problèmes connus 

Il n’existe aucun problème connu pour cette mise en production.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations, commencez par consulter [Vue d’ensemble d’Event Hubs sur Azure Stack Hub pour l’opérateur](event-hubs-rp-overview.md).

