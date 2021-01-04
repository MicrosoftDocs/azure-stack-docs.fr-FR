---
title: Vue d’ensemble de la planification de la capacité pour Azure Stack Hub | Microsoft Docs
description: Découvrez comment planifier la capacité pour des déploiements Azure Stack Hub. Consultez les spécifications des modèles de base et supérieurs d’Azure Stack Hub renforcé.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2020
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: b300a787105b39f2d4fa87f61f67e4aee4530440
ms.sourcegitcommit: 5fbc60b65d27c916ded7a95ba4102328d550c7e5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97598196"
---
# <a name="overview-of-azure-stack-hub-capacity-planning"></a>Vue d’ensemble de la planification de la capacité pour Azure Stack Hub

Lorsque vous évaluez une solution Azure Stack Hub, prenez en considération les choix de configuration matérielle qui ont une incidence directe sur la capacité globale du cloud Azure Stack Hub. 

Par exemple, vous devez opérer des choix concernant le processeur, la densité de mémoire, la configuration du stockage et l’échelle globale ou nombre de serveurs de la solution. Contrairement à une solution de virtualisation traditionnelle, l’arithmétique simple de ces composants ne s’applique pas pour déterminer la capacité utilisable. Azure Stack Hub est conçu pour héberger l’infrastructure ou les composants de gestion au sein-même de la solution. Par ailleurs, une partie de la capacité de la solution est réservée pour assurer la résilience, la mise à jour des logiciels de la solution d’une manière qui minimise l’interruption des charges de travail du locataire. 

> [!IMPORTANT]
> Ces informations de planification de la capacité et [Azure Stack Hub Capacity Planner](https://aka.ms/azstackcapacityplanner) constituent un point de départ pour la prise de décisions en matière de planification et de configuration d’Azure Stack Hub. Elles n’ont pas pour vocation de remplacer vos propres recherches et analyses. Microsoft ne fait aucune déclaration ou n’offre aucune garantie, expresse ou implicite, concernant les informations contenues ici.
 
Une solution Azure Stack Hub est conçue comme un cluster hyperconvergé de calcul et de stockage. La convergence permet le partage de la capacité matérielle du cluster, appelé *unité d’échelle*. Dans Azure Stack Hub, une unité d’échelle assure la disponibilité et l’extensibilité des ressources. Une unité d’échelle se compose d’un ensemble de serveurs Azure Stack Hub, appelés *hôtes*. Les logiciels de l’infrastructure sont hébergés au sein d’un ensemble de machines virtuelles et partagent les mêmes serveurs physiques que les machines virtuelles du locataire. Toutes les machines virtuelles Azure Stack Hub sont ensuite gérées par les technologies de clustering Windows Server de l’unité d’échelle et des instances individuelles de Hyper-V. 

L’unité d’échelle simplifie l’acquisition et la gestion d’Azure Stack Hub. L’unité d’échelle permet également le déplacement et l’extensibilité de tous les services (locataire et infrastructure) sur Azure Stack Hub. 

Les rubriques suivantes fournissent plus de détails sur chaque composant :

- [Calcul d’Azure Stack Hub](../operator/azure-stack-capacity-planning-compute.md?toc=/azure-stack/tdc/toc.json&bc=/azure-stack/breadcrumb/toc.json)
- [Stockage Azure Stack Hub](../operator/azure-stack-capacity-planning-storage.md?toc=/azure-stack/tdc/toc.json&bc=/azure-stack/breadcrumb/toc.json)
- [Azure Stack Hub Capacity Planner](../operator/azure-stack-app-service-capacity-planning.md?toc=/azure-stack/tdc/toc.json&bc=/azure-stack/breadcrumb/toc.json)

## <a name="azure-stack-hub-ruggedized-high-and-low-models"></a>Modèles de base et supérieur d’Azure Stack Hub renforcé

Le tableau suivant liste les spécifications des modèles de base et supérieur d’Azure Stack Hub renforcé.

| Composant               | Caractéristique |
|-------------------------|---------------|
| UC                     |Élevée : 284 cœurs de processeur virtuel<br>Faible : 184 cœurs de processeur virtuel  |
| Mémoire                  |Élevée : 1 037 Go<br>Faible : 547 Go                |
| Stockage                 |Élevée : 34,2 To<br>Faible : 15,4 To                |
| Acceleration            |N/A                                          |
| Taille/Poids             |Calcul : 31,5" (L) x 23,8" (l) x 15,33" (h) / 80,01 cm (L) x 60,45 cm (l) x 38,93 cm (h)<br>Poids : 120 lb (environ 54,4 kg). (qté : 2)<br>Réseaux : 31,5" (L) x 23,8" (l) x 17,12" (h) / 80,01 cm (L) x 60,45 cm (l) x 43,48 cm (h)<br>Poids : 145 lb (environ 65,8 kg). (qté : 1)              |
| Configurations facultatives |Préchauffeur<br>Configurations supérieure ou de base     |

## <a name="next-steps"></a>Étapes suivantes

[Calcul Azure Stack Hub](../operator/azure-stack-capacity-planning-compute.md?toc=/azure-stack/tdc/toc.json&bc=/azure-stack/breadcrumb/toc.json)
[Stockage Azure Stack Hub](../operator/azure-stack-capacity-planning-storage.md?toc=/azure-stack/tdc/toc.json&bc=/azure-stack/breadcrumb/toc.json)
[Capacity Planner pour Azure Stack Hub](../operator/azure-stack-app-service-capacity-planning.md?toc=/azure-stack/tdc/toc.json&bc=/azure-stack/breadcrumb/toc.json)
