---
title: Planification de la capacité de calcul pour Azure Stack | Microsoft Docs
description: Découvrez comment planifier la capacité de calcul pour les déploiements Azure Stack.
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
ms.date: 04/03/2019
ms.author: mabrigg
ms.reviewer: prchint
ms.lastreviewed: 04/03/2019
ms.custom: ''
ms.openlocfilehash: 907a10caa2a23b58d0c63ee1abb7f4e4a074d1a1
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618915"
---
# <a name="azure-stack-compute-capacity-planning"></a>Planification de la capacité de calcul Azure Stack
Les [tailles de machine virtuelle prises en charge dans Azure Stack](../user/azure-stack-vm-sizes.md) sont un sous-ensemble de celles prises en charge dans Azure. Azure impose des limites de ressources à différents niveaux pour éviter la consommation excessive des ressources (au niveau du service ou du serveur local). Si aucune limite n’est appliquée à la consommation des locataires, la surconsommation de ressources par certains locataires aurait un impact négatif sur l’expérience des autres locataires. Pour la sortie réseau de la machine virtuelle, des limites de bande passante sont en place dans Azure Stack conformément aux limites d’Azure. Pour les ressources de stockage, Azure Stack implémente des limites d’E/S par seconde de stockage pour éviter une consommation excessive de base des ressources par les locataires pour l’accès au stockage.  

## <a name="vm-placement-and-virtual-to-physical-core-overprovisioning"></a>Placement de machine virtuelle et sur-approvisionnement de cœurs virtuels par rapport aux cœurs physiques
Dans Azure Stack, un locataire ne peut pas spécifier un serveur spécifique à utiliser pour le placement de machine virtuelle. Le seul facteur qui entre en compte pour le placement des machines virtuelles est la quantité, suffisante ou non, de mémoire disponible sur l’hôte pour ce type de machine virtuelle. Azure Stack ne surexploite pas la mémoire, mais autorise le sur-approvisionnement de cœurs. Étant donné que les algorithmes de placement ne considèrent pas le ratio de sur-provisionnement cœurs virtuels/cœurs physiques comme un facteur déterminant, chaque hôte peut présenter un ratio différent. 

Dans Azure, pour garantir la haute disponibilité d’un système de production à plusieurs machines virtuelles, ces machines virtuelles sont placées dans un groupe à haute disponibilité qui les répartit entre plusieurs domaines d’erreur. Dans Azure Stack, un domaine d'erreur au sein d'un groupe à haute disponibilité est défini comme un nœud unique dans l'unité d'échelle.

Même si l’infrastructure d’Azure Stack résiste aux défaillances, la technologie sous-jacente (clustering de basculement) implique toujours un temps d’arrêt pour les machines virtuelles qui se trouvent sur le serveur physique concerné en cas de défaillance matérielle. Actuellement, Azure Stack gère les groupes à haute disponibilité comportant trois domaines d’erreur maximum dans un souci de compatibilité avec Azure. Les machines virtuelles placées dans un groupe à haute disponibilité seront physiquement isolées les unes des autres grâce à une répartition aussi équilibrée que possible sur plusieurs domaines d’erreur (nœuds Azure Stack). En cas de défaillance matérielle, les machines virtuelles du domaine d’erreur défaillant sont redémarrées dans d’autres nœuds, si possible distincts de ceux des autres machines virtuelles du même groupe à haute disponibilité. Une fois le matériel rétabli, les machines virtuelles seront rééquilibrées de façon à maintenir une haute disponibilité.

Pour fournir une haute disponibilité, Azure utilise également des domaines de mise à jour dans les groupes à haute disponibilité. Un domaine de mise à jour est un groupe logique de matériels sous-jacents qui peuvent faire l’objet d’une opération de maintenance ou être redémarrés en même temps. Dans Azure Stack, les machines virtuelles sont migrées en direct sur les autres hôtes en ligne du cluster avant que leur hôte sous-jacent soit mis à jour. Comme il ne se produit aucun temps d’arrêt du côté du client pendant la mise à jour d’un hôte, la fonctionnalité de domaine de mise à jour d’Azure Stack n’existe qu’à des fins de compatibilité des modèles avec Azure.

## <a name="azure-stack-resiliency-resources"></a>Ressources de résilience Azure Stack
Pour permettre l’application de correctifs et de mises à jour sur un système Azure Stack intégré et garantir sa résistance aux défaillances matérielles, une partie de la mémoire totale du serveur est réservée et indisponible pour le placement de machine virtuelle de locataire.

Si un serveur échoue, les machines virtuelles hébergées sur le serveur en échec sont redémarrées sur les autres serveurs disponibles afin de garantir la disponibilité des machines virtuelles. De même, pendant le processus de mise à jour et de correctif, toutes les machines virtuelles en cours d’exécution sur un serveur sont migrées en direct vers un autre serveur disponible. Cette stratégie de gestion ou de migration des machines virtuelles est possible uniquement s’il existe une capacité réservée à ces opérations de redémarrage ou de migration.

Le calcul suivant permet d’obtenir la capacité de mémoire totale disponible qui peut être utilisée pour le placement de machine virtuelle de locataire. Cette capacité de mémoire est calculée pour l’intégralité de l’unité d’échelle Azure Stack.

  Mémoire disponible pour le placement des machines virtuelles = Mémoire totale du serveur - Réserve de résilience - Mémoire utilisée par les machines virtuelles en cours d’exécution - Surcharge de l’infrastructure Azure Stack <sup>1</sup>

  Réserve de résilience = H + R * ((N-1) * H) + V * (N-2)

> Où :
> - H = taille de la mémoire d’un seul serveur
> - N = taille de l’unité d’échelle (nombre de serveurs)
> - R = réserve de système d’exploitation pour la surcharge du système d’exploitation<sup>2</sup>
> - V = taille de la plus grande machine virtuelle dans l’unité d’échelle

  <sup>1</sup> Surcharge de l’infrastructure Azure Stack = 230 Go

  <sup>2</sup> Réserve de système d’exploitation pour la surcharge = 15 % de la mémoire de nœud. La valeur de la réserve de système d’exploitation est une estimation et varie en fonction de la capacité de mémoire physique du serveur et de la surcharge générale du système d’exploitation.

La valeur V, à savoir la taille de la plus grande machine virtuelle dans l’unité d’échelle, est basée dynamiquement sur la plus grande taille de mémoire de machine virtuelle de locataire. Par exemple, la plus grande taille de machine virtuelle peut être 7 Go ou 112 Go, ou toute autre taille de mémoire de machine virtuelle prise en charge par la solution Azure Stack.

Le calcul ci-dessus est une estimation et peut varier en fonction de la version actuelle d’Azure Stack. La capacité de déploiement de services et de machines virtuelles de locataire est basée sur les spécificités de la solution mise en œuvre. Cet exemple de calcul est fourni à titre indicatif et ne constitue pas de résultat fixe en matière de déploiement de machines virtuelles.



## <a name="next-steps"></a>Étapes suivantes
[Planification de la capacité de stockage](capacity-planning-storage.md)
