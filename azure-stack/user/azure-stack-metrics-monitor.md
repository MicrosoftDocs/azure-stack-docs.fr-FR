---
title: Consommer des données de supervision à partir d’Azure Stack Hub | Microsoft Docs
description: Découvrez les différentes façons de consommer des données de supervision à partir d’Azure Stack Hub.
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
ms.date: 11/11/2019
ms.author: mabrigg
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 066a179df94d9a61b89e5f6679968db7e4dea314
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75883539"
---
# <a name="how-to-consume-monitoring-data-from-azure-stack-hub"></a>Comment consommer des données de supervision à partir d’Azure Stack Hub

Tout comme avec Azure Monitor dans Azure global, les données de supervision se trouvent dans un emplacement unique avec le pipeline Azure Monitor. Cependant, toutes les données de supervision trouvées dans Azure global ne sont pas disponibles dans Azure Stack Hub. Dans cet article, nous récapitulons les différentes façons d’utiliser les données de supervision dans Azure Stack Hub.
 
## <a name="options-for-data-consumption"></a>Options pour la consommation de données

| Type de données | Category | Services pris en charge | Méthodes d’accès |
|-------------------------------------------------------------|----------|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Mesures au niveau de la plateforme Azure Monitor | Mesures | [Métriques prises en charge avec Azure Monitor sur Azure Stack Hub](azure-stack-metrics-supported.md) | API REST |
| Mesures de SE invité Compute (calcul des performances, par exemple) | Mesures | Machines virtuelles Windows et Linux | Blob ou table de stockage :<br>Diagnostics Azure Windows ou Linux <br>Event Hub :<br>Diagnostics Azure pour Windows |
| Métriques de stockage | Mesures | Stockage Azure | Table de stockage :<br>Storage Analytics |
| Journal d’activité | Événements | Tous les services Azure | API REST :<br>API d’événements Azure Monitor |
| Journaux d’activité de SE invité Compute (par exemple, IIS, ETW et journaux d’activité syslog) | Événements | Machines virtuelles Windows et Linux | Blob ou table de stockage :<br>Diagnostics Azure Windows ou Linux <br>Event Hub :<br>Diagnostics Azure pour Windows |
| Journaux d’activité de stockage | Événements | Stockage Azure | Table de stockage :<br>Storage Analytics |

## <a name="next-steps"></a>Étapes suivantes

En savoir plus grâce à l’article [Azure Monitor sur Azure Stack Hub](azure-stack-metrics-azure-data.md).
