---
title: Stockage de la planification de la capacité Azure Stack | Microsoft Docs
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
ms.openlocfilehash: 30ce69f96747ab8dbdafd9e20e8cea07026074d5
ms.sourcegitcommit: 80775f5c5235147ae730dfc7e896675a9a79cdbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/03/2019
ms.locfileid: "66461088"
---
# <a name="azure-stack-storage"></a>Stockage Azure Stack

Les sections suivantes fournissent des informations relatives à la planification de la capacité de stockage Azure Stack pour vous aider à planifier selon les besoins de stockage de la solution.

## <a name="uses-and-organization-of-storage-capacity"></a>Utilisations et organisation de la capacité de stockage
La configuration hyperconvergée d’Azure Stack permet le partage des appareils de stockage physiques. Les trois principales divisions de stockage disponible sont réparties entre l’infrastructure, le stockage temporaire des machines virtuelles du locataire et le stockage prenant en charge les objets Blob, les tables et les files d’attente des services de stockage cohérent Azure (ACS).

## <a name="spaces-direct-cache-and-capacity-tiers"></a>Cache direct et niveaux de capacité des espaces
La capacité de stockage est utilisée pour le système d’exploitation, la journalisation locale, les vidages et pour les autres besoins de stockage temporaires de l’infrastructure. Cette capacité de stockage local est séparée (appareils et capacité) des appareils de stockage placés sous la gestion de la configuration d’espaces de stockage direct. Le reste des appareils de stockage est placé dans un unique pool de capacité de stockage, quel que soit le nombre de serveurs dans l’unité d’échelle. Ces appareils sont de deux types : Cache et Capacité.  Les appareils Cache sont exactement cela : des caches. Les espaces de stockage direct consomment ces appareils pour l’écriture différée et la mise en cache en lecture. Les capacités de ces appareils Cache, bien qu’utilisées, ne conviennent pas pour la capacité « visible », formatée des disques virtuels formatés. Les appareils Capacité sont utilisés à cet effet et fournissent « l’emplacement d’origine » des données gérées par les espaces de stockage.

Toute la capacité de stockage est allouée et gérée directement par l’infrastructure Azure Stack. L’opérateur n’a besoin de prendre de décisions concernant la configuration, l’allocation, ou même l’expansion de la capacité. Ces décisions de conception ont été faites pour s’aligner avec les exigences de la solution et sont automatisées pendant l’installation ou le déploiement initiaux ou durant l’expansion de la capacité. Les informations sur la résilience, la capacité réservée pour les reconstructions et d’autres détails ont été considérées comme faisant partie de la conception. 

Les opérateurs peuvent choisir entre une configuration de stockage hybride ou entièrement flash :

![Planification de la capacité de stockage Azure](media/azure-stack-capacity-planning/storage.png)

Dans la configuration entièrement flash, la configuration peut être à un ou deux niveaux.  Dans le cas d'une configuration à un niveau, tous les appareils Capacité sont de même type (par exemple, NVMe, SSD SATA ou SSD SAS) et les appareils Cache ne sont pas utilisés. Dans le cas d'une configuration flash à deux niveaux, la configuration type est NVMe pour les appareils Cache et SATA ou SSD SAS pour les appareils Capacité.

Dans le cas d'une configuration hybride à deux niveaux, le cache est un disque NVMe, SATA ou SSD SATA, tandis que la capacité est HDD. 

Voici un bref résumé de la configuration des espaces de stockage direct et du stockage Azure Stack :
- Un pool d’espaces de stockage par unité d’échelle (tous les appareils de stockage sont configurés dans un pool unique)
- Les disques virtuels sont créés sous la forme d’un miroir triple pour optimiser les performances et la résilience
- Chaque disque virtuel est formaté comme un système de fichiers ReFS
- La capacité du disque virtuel est calculée et allouée de manière à laisser une quantité de capacité de données de l’appareil Capacité non allouée dans le pool. Cela équivaut à un disque Capacité par serveur.
- BitLocker sera activé sur chaque système de fichiers ReFS pour le chiffrement des données au repos. 

Voici les disques virtuels créés automatiquement et leurs capacités :

|Nom|Calcul de la capacité|Description|
|-----|-----|-----|
|Appareil local/de démarrage|Minimum de 340 Go<sup>1</sup>|Stockage serveur individuel pour les images du système d’exploitation et les machines virtuelles de l’infrastructure « locale »|
|Infrastructure|3,5 To|Toutes les utilisations de l’infrastructure Azure Stack|
|VmTemp|Voir ci-dessous<sup>2</sup>|Les machines virtuelles du locataire ont un disque temporaire attaché et ces données sont stockées dans ces disques virtuels|
|ACS|Voir ci-dessous <sup>3</sup>|Capacité de stockage cohérent Azure pour la maintenance des objets Blob, des tables et des files d’attente|

<sup>1</sup> Capacité de stockage minimale requise pour le partenaire de solutions Azure Stack.

<sup>2</sup> La taille du disque virtuel utilisé pour les disques temporaires de la machine virtuelle du locataire est calculée comme un ratio de la mémoire physique du serveur. Comme indiqué dans les tableaux ci-dessous concernant les tailles de machine virtuelle Azure IaaS, le disque temporaire est un ratio de la mémoire physique allouée à la machine virtuelle. L’allocation du stockage du « disque temporaire » dans Azure Stack se fait de manière à capturer la plupart des cas d’utilisation, mais n’est peut-être pas en mesure de satisfaire tous les besoins de stockage du disque temporaire. Le ratio choisi est un compromis entre la mise à disposition d’un stockage temporaire tout en ne consommant pas une majorité de la capacité de stockage de la solution pour la capacité du disque temporaire uniquement. Un disque de stockage temporaire est créé par serveur dans l’unité d’échelle. La capacité du stockage temporaire n’ira pas au-delà de 10 % de la capacité de stockage total disponible dans le pool de stockage de l’unité d’échelle. Le calcul est similaire à l’exemple suivant :

```
  DesiredTempStoragePerServer = PhysicalMemory * 0.65 * 8
  TempStoragePerSolution = DesiredTempStoragePerServer * NumberOfServers
  PercentOfTotalCapacity = TempStoragePerSolution / TotalAvailableCapacity
  If (PercentOfTotalCapacity <= 0.1)
      TempVirtualDiskSize = DesiredTempStoragePerServer
  Else
      TempVirtualDiskSize = (TotalAvailableCapacity * 0.1) / NumberOfServers
```

<sup>3</sup> Les disques virtuels créés pour être utilisés par ACS sont une division simple de la capacité restante. Comme indiqué, tous les disques virtuels sont des miroirs triples et un disque Capacité d’une capacité de chaque serveur est non alloué. Les différents disques virtuels énumérés ci-dessus sont d’abord alloués, et la capacité restante est ensuite utilisée pour les disques virtuels ACS.


## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur [Azure Stack Capacity Planner](azure-stack-capacity-planner.md)
