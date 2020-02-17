---
title: Vue d’ensemble de la planification de la capacité
titleSuffix: Azure Stack Hub
description: Découvrez comment planifier la capacité pour des déploiements Azure Stack Hub.
author: ihenkel
ms.topic: article
ms.date: 05/31/2019
ms.author: inhenkel
ms.reviewer: prchint
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: 79a73d9bd60837190aa0dc2c060593a39e6ecbf0
ms.sourcegitcommit: 0a3c8b0bf9c116a5caaeca453a2bbc6e7f7cbfb9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77147626"
---
# <a name="capacity-planning-for-azure-stack-hub-overview"></a>Vue d’ensemble de la planification de la capacité pour Azure Stack Hub

Lorsque vous évaluez une solution Azure Stack Hub, prenez en considération les choix de configuration matérielle qui ont une incidence directe sur la capacité globale du cloud Azure Stack Hub.

Par exemple, vous devez opérer des choix concernant le processeur, la densité de mémoire, la configuration du stockage et l’échelle globale ou nombre de serveurs de la solution. Contrairement à une solution de virtualisation traditionnelle, l’arithmétique simple de ces composants ne s’applique pas pour déterminer la capacité utilisable. Azure Stack Hub est conçu pour héberger l’infrastructure ou les composants de gestion au sein-même de la solution. Par ailleurs, une partie de la capacité de la solution est réservée pour assurer la résilience. La résilience se définit comme la mise à jour des logiciels de la solution, d’une manière qui minimise l’interruption des charges de travail du locataire.

> [!IMPORTANT]
> Ces informations de planification de la capacité et [Azure Stack Hub Capacity Planner](https://aka.ms/azstackcapacityplanner) constituent un point de départ pour la prise de décisions en matière de planification et de configuration d’Azure Stack Hub. Elles n’ont pas pour vocation de remplacer vos propres recherches et analyses. Microsoft ne fait aucune déclaration ou n’offre aucune garantie, expresse ou implicite, concernant les informations contenues ici.

Une solution Azure Stack Hub est conçue comme un cluster hyperconvergé de calcul et de stockage. La convergence permet le partage de la capacité matérielle du cluster, appelé *unité d’échelle*. Dans Azure Stack Hub, une unité d’échelle assure la disponibilité et l’extensibilité des ressources. Une unité d’échelle se compose d’un ensemble de serveurs Azure Stack Hub, appelés *hôtes*. Les logiciels de l’infrastructure sont hébergés au sein d’un ensemble de machines virtuelles et partagent les mêmes serveurs physiques que les machines virtuelles du locataire. Toutes les machines virtuelles Azure Stack Hub sont ensuite gérées par les technologies de clustering Windows Server de l’unité d’échelle et des instances individuelles de Hyper-V.

L’unité d’échelle simplifie l’acquisition et la gestion d’Azure Stack Hub. L’unité d’échelle permet également le déplacement et l’extensibilité de tous les services (locataire et infrastructure) sur Azure Stack Hub.

Les rubriques suivantes fournissent plus de détails sur chaque composant :

- [Calcul d’Azure Stack Hub](azure-stack-capacity-planning-compute.md)
- [Stockage d’Azure Stack Hub](azure-stack-capacity-planning-storage.md)
- [Capacity Planner d’Azure Stack Hub](azure-stack-capacity-planner.md)
