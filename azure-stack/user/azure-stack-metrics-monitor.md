---
title: Consommer des données de surveillance à partir d’Azure Stack| Microsoft Docs
description: Découvrez les différentes façons de consommer des données de supervision à partir d’Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/15/2019
ms.author: mabrigg
ms.lastreviewed: 12/01/2018
ms.openlocfilehash: 0b04a7834218d07706e6314a9c41e0370ff6f6fd
ms.sourcegitcommit: b36d078e699c7924624b79641dbe9021af9606ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67816185"
---
# <a name="how-to-consume-monitoring-data-from-azure-stack"></a>Comment consommer des données de surveillance à partir d’Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Tout comme avec Azure Monitor dans Azure global, les données de supervision se trouvent dans un emplacement unique avec le pipeline Azure Monitor. Cependant, toutes les données de surveillance trouvées dans Azure global ne sont pas disponibles dans Azure Stack. Dans cet article, nous récapitulons les différentes façons d’utiliser les données de supervision dans Azure Stack.
 
## <a name="options-for-data-consumption"></a>Options pour la consommation de données

| Type de données | Catégorie | Services pris en charge | Méthodes d’accès |
|-------------------------------------------------------------|----------|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Mesures au niveau de la plateforme Azure Monitor | Mesures | [Mesures prises en charge avec Azure Monitor sur Azure Stack](azure-stack-metrics-supported.md) | API REST |
| Mesures de SE invité Compute (calcul des performances, par exemple) | Mesures | Machines virtuelles Windows et Linux | Blob ou table de stockage :<br>Diagnostics Azure Windows ou Linux <br>Event Hub :<br>Diagnostics Azure pour Windows |
| Métriques de stockage | Mesures | Stockage Azure | Table de stockage :<br>Storage Analytics |
| Journal d’activité | Événements | Tous les services Azure | API REST :<br>API d’événements Azure Monitor |
| Journaux d’activité de SE invité Compute (par exemple, IIS, ETW et journaux d’activité syslog) | Événements | Machines virtuelles Windows et Linux | Blob ou table de stockage :<br>Diagnostics Azure Windows ou Linux <br>Event Hub :<br>Diagnostics Azure pour Windows |
| Journaux d’activité de stockage | Événements | Stockage Azure | Table de stockage :<br>Storage Analytics |

## <a name="next-steps"></a>Étapes suivantes

En savoir plus grâce à l’article [Azure Monitor sur Azure Stack](azure-stack-metrics-azure-data.md).
