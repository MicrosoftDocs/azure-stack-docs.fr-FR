---
title: Vue d’ensemble de la planification de la capacité pour Azure Stack | Microsoft Docs
description: Découvrez comment planifier la capacité pour les déploiements Azure Stack.
services: azure-stack
documentationcenter: ''
author: prchint
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: 335e92fcdb2eadd8bebfbfd3fb2e3b31edd00734
ms.sourcegitcommit: b79a6ec12641d258b9f199da0a35365898ae55ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67131340"
---
# <a name="overview-of-azure-stack-capacity-planning"></a>Vue d’ensemble de la planification de la capacité pour Azure Stack

Lorsque vous évaluez une solution Azure Stack, prenez en considération les choix de configuration matérielle qui ont une incidence directe sur la capacité globale du cloud Azure Stack. 

Par exemple, vous devez opérer des choix concernant le processeur, la densité de mémoire, la configuration du stockage et l’échelle globale ou nombre de serveurs de la solution. Contrairement à une solution de virtualisation traditionnelle, l’arithmétique simple de ces composants ne s’applique pas pour déterminer la capacité utilisable. Azure Stack est conçu pour héberger l’infrastructure ou les composants de gestion au sein-même de la solution. Par ailleurs, une partie de la capacité de la solution est réservée pour assurer la résilience, la mise à jour des logiciels de la solution d’une manière qui minimise l’interruption des charges de travail du locataire. 

> [!IMPORTANT]
> Ces informations de planification de la capacité et [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) constituent un point de départ pour la prise de décisions en matière de planification et de configuration d’Azure Stack. Elles n’ont pas pour vocation de remplacer vos propres recherches et analyses. Microsoft ne fait aucune déclaration ou n’offre aucune garantie, expresse ou implicite, concernant les informations contenues ici.
 
Une solution Azure Stack est conçue comme un cluster hyperconvergé de calcul et de stockage. La convergence permet le partage de la capacité matérielle du cluster, appelé *unité d’échelle*. Dans Azure Stack, une unité d’échelle assure la disponibilité et la scalabilité des ressources. Une unité d’échelle se compose d’un ensemble de serveurs Azure Stack, appelés *hôtes*. Les logiciels de l’infrastructure sont hébergés au sein d’un ensemble de machines virtuelles et partagent les mêmes serveurs physiques que les machines virtuelles du locataire. Toutes les machines virtuelles Azure Stack sont ensuite gérées par les technologies de clustering Windows Server de l’unité d’échelle et les instances individuelles de Hyper-V. 

L’unité d’échelle simplifie l’acquisition et la gestion d’Azure Stack. L’unité d’échelle permet également le déplacement et la scalabilité de tous les services (locataire et infrastructure) sur Azure Stack. 

Les rubriques suivantes fournissent plus de détails sur chaque composant :

- [Calcul Azure Stack](azure-stack-capacity-planning-compute.md)
- [Stockage Azure Stack](azure-stack-capacity-planning-storage.md)
- [Azure Stack Capacity Planner](azure-stack-capacity-planner.md)
