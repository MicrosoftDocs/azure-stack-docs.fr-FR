---
title: Planification de la capacité pour Azure Stack | Microsoft Docs
description: Découvrez comment planifier la capacité pour les déploiements Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2019
ms.author: mabrigg
ms.reviewer: prchint
ms.lastreviewed: 03/29/2019
ms.openlocfilehash: 1a5bde916779961f9cc4c099b57324b8ddbd3bda
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2019
ms.locfileid: "64984502"
---
# <a name="azure-stack-capacity-planning"></a>Planification de capacité Azure Stack
Lorsque vous envisagez d’adopter une solution Azure Stack, vous devez savoir que certaines options de configuration matérielle ont un impact direct sur la capacité globale du cloud Azure Stack. Parmi ces choix se trouvent celui de l’UC, de la densité de mémoire, de la configuration du stockage et de la mise à l’échelle globale de la solution (ou nombre de serveurs). Contrairement à une solution de virtualisation traditionnelle, l’arithmétique simple de ces composants pour déterminer la capacité utilisable ne s’applique pas. La première raison est que l’architecture d’Azure Stack permet d’héberger les composants de gestion ou d’infrastructure au sein-même de la solution. La deuxième raison est qu’une partie de la capacité de la solution est réservée pour prendre en charge la résilience ; la mise à jour des logiciels de la solution est une façon de minimiser l’interruption de charges de travail des locataires.

> [!IMPORTANT]
> Les informations de planification de la capacité fournies et la feuille de calcul qui accompagne cet article sont destinées uniquement à accompagner votre prise de décisions en matière de planification et de configuration d’Azure Stack. Elles n’ont pas vocation à remplacer vos propres recherches et analyses. 

## <a name="compute-and-storage-capacity-planning"></a>Planification de la capacité de stockage et de calcul
Une solution Azure Stack est conçue sous la forme d’un cluster hyperconvergé de ressources de calcul, de mise en réseau et de stockage. Cela permet une utilisation ou un partage efficace de toute la capacité matérielle du cluster, appelé « unité d’échelle » pour Azure Stack, de façon à garantir disponibilité et évolutivité. Tous les logiciels d’infrastructure sont hébergés au sein d’un ensemble de machines virtuelles et partagent les mêmes serveurs physiques que les machines virtuelles du locataire. Toutes les machines virtuelles sont ensuite gérées par les technologies de clustering Windows Server de l’unité d’échelle et les instances individuelles de Hyper-V. Cette approche simplifie l’acquisition et la gestion d’une solution Azure Stack et permet le déplacement et l’évolutivité de tous les services (locataire et infrastructure) au sein de l’unité d’échelle.

La seule ressource physique qui n’est pas surapprovisionnée dans une solution Azure Stack est la mémoire du serveur. Les autres ressources, les cœurs de processeur, la bande passante réseau et la capacité de stockage, seront surapprovisionnées afin de tirer le meilleur parti des ressources disponibles. La mémoire du serveur physique est le principal facteur pris en compte dans le calcul de la capacité disponible d’une solution. L’utilisation des autres ressources repose donc sur l’identification du rapport de surapprovisionnement possible et de la capacité qui sera acceptable pour la charge de travail prévue.

Environ 30 machines virtuelles sont utilisées pour héberger l’infrastructure d’Azure Stack. Celles-ci consomment au total environ 230 Go de mémoire et 140 cœurs virtuels. Le nombre de machines virtuelles est lié à la nécessité de séparer les différents services pour répondre aux exigences de sécurité, d’évolutivité, de maintenance et d’action corrective. Grâce à cette structure de service interne, de nouveaux services d’infrastructure pourront être intégrés à mesure qu’ils sont développés.

Pour prendre en charge la mise à jour automatique de l’ensemble des composants logiciels de l’infrastructure et des serveurs physiques, ou encore la mise en place de correctifs et de mises à jour, les placements de machine virtuelle utilisateur et d’infrastructure ne consommeront pas toutes les ressources de mémoire de l’unité d’échelle. Une partie de la mémoire totale sur tous les serveurs d’une unité d’échelle sera non allouée pour assurer la résilience de la solution. Par exemple, lorsque l’image Windows Server du serveur physique est mise à jour, les machines virtuelles hébergées sur le serveur sont déplacées à un autre endroit dans l’unité d’échelle le temps de la mise à jour. Lorsque la mise à jour est terminée, le serveur est redémarré et recommence à prendre en charge des charges de travail. Pour le correctif et la mise à jour d’une solution Azure Stack, l’objectif consiste à éviter d’avoir à arrêter les machines virtuelles hébergées. Pour atteindre cet objectif, la capacité de mémoire d’au moins un serveur est non allouée pour permettre le déplacement des machines virtuelles au sein de l’unité d’échelle. Ce processus de placement et de mouvement s’applique à la fois aux machines virtuelles d’infrastructure et aux machines virtuelles créées pour le compte de l’utilisateur ou du locataire de la solution Azure Stack. Lors de l’implémentation finale, la quantité de mémoire réservée pour prendre en charge le déplacement des machines virtuelles peut être beaucoup élevée que la capacité d’un seul serveur en raison des difficultés de placement de machines virtuelles ayant différentes exigences en termes de mémoire. L’utilisation de la mémoire de l’instance Windows Server ajoute également une certaine complexité. L’instance du système d’exploitation de base pour chaque serveur consommera de la mémoire pour le système d’exploitation et ses tables de page virtuelle, à laquelle s’ajoute la mémoire utilisée par Hyper-V pour gérer chacune des machines virtuelles hébergées.

La capacité de la machine virtuelle est déterminée par la mémoire disponible. Les ratios cœurs virtuels/cœurs physiques illustrent comment la densité de la machine virtuelle va modifier la capacité de processeur disponible, sauf si la solution est dotée d’un plus grand nombre de cœurs physiques (un autre processeur est choisi). Il en est de même pour la capacité de stockage et la capacité de stockage du cache.

Les exemples de densité de machine virtuelle ci-dessus sont donnés uniquement à titre indicatif. D’autres difficultés interviennent dans le calcul de la capacité. Contactez Microsoft ou un partenaire de solution pour mieux appréhender les différents choix en matière de planification de la capacité et la capacité disponible qui en résulte.

Le reste de cette section décrit les exigences de déploiement Azure Stack pour le calcul et le stockage. Utilisez ces informations pour identifier les composants requis et leurs valeurs de configuration minimales. Cette section décrit également la façon dont la solution est configurée par rapport à la capacité disponible et aux limites potentielles du système liées à la capacité et au niveau de performance du locataire.

> [!NOTE]
> Les exigences de planification de la capacité pour la mise en réseau sont minimes car seule la taille de l’adresse IP virtuelle publique est configurable. Pour découvrir comment ajouter d’autres adresses IP publiques à Azure Stack, consultez [Ajouter des adresses IP publiques](azure-stack-add-ips.md).


## <a name="next-steps"></a>Étapes suivantes
[Planification de la capacité de calcul](capacity-planning-compute.md)
