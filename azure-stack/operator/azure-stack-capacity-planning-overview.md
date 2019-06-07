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
ms.openlocfilehash: 0d2610d2082973f4ab255027cc299b5858660c58
ms.sourcegitcommit: 80775f5c5235147ae730dfc7e896675a9a79cdbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/03/2019
ms.locfileid: "66461076"
---
# <a name="overview-of-azure-stack-capacity-planning"></a>Vue d’ensemble de la planification de la capacité pour Azure Stack

Lorsque vous envisagez d’adopter une solution Azure Stack, vous devez savoir que certaines options de configuration matérielle ont un impact direct sur la capacité globale du cloud Azure Stack. Parmi ces choix se trouvent celui de l’UC, de la densité de mémoire, de la configuration du stockage et de la mise à l’échelle globale de la solution (ou nombre de serveurs). Contrairement à une solution de virtualisation traditionnelle, l’arithmétique simple de ces composants pour déterminer la capacité utilisable ne s’applique pas. La première raison est que l’architecture d’Azure Stack permet d’héberger les composants de gestion ou d’infrastructure au sein-même de la solution. La deuxième raison est qu’une partie de la capacité de la solution est réservée pour prendre en charge la résilience ; la mise à jour des logiciels de la solution est une façon de minimiser l’interruption de charges de travail des locataires. 

> [!IMPORTANT]
> Ces informations de planification de la capacité et [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) constituent un point de départ pour la prise de décisions en matière de planification et de configuration d’Azure Stack. Ils n’ont pas vocation à remplacer vos propres recherches et analyses. Microsoft ne fait aucune déclaration ou n’offre aucune garantie, expresse ou implicite, concernant les informations contenues ici.
 
Une solution Azure Stack est conçue sous la forme d’un cluster hyperconvergé de ressources de calcul et de stockage. La convergence permet le partage de la capacité matérielle du cluster, appelé *unité d’échelle*. Dans Azure Stack, une unité d’échelle assure la disponibilité et la scalabilité des ressources. Une unité d’échelle se compose d’un ensemble de serveurs Azure Stack, appelés *hôtes*. Les logiciels d’infrastructure sont hébergés au sein d’un ensemble de machines virtuelles et partagent les mêmes serveurs physiques que les machines virtuelles du locataire. Toutes les machines virtuelles Azure Stack sont ensuite gérées par les technologies de clustering Windows Server de l’unité d’échelle et les instances individuelles de Hyper-V. L’unité d’échelle simplifie l’acquisition et la gestion d’Azure Stack. L’unité d’échelle permet également le déplacement et la scalabilité de tous les services (locataire et infrastructure) sur Azure Stack. 

Les rubriques suivantes fournissent plus de détails sur chaque composant :

- [Calcul Azure Stack](azure-stack-capacity-planning-compute.md)
- [Stockage Azure Stack](azure-stack-capacity-planning-storage.md)
- [Azure Stack Capacity Planner](azure-stack-capacity-planner.md)
