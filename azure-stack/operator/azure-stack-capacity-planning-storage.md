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
ms.openlocfilehash: 2845a90f97c1b859269f73333448bf42ff699da9
ms.sourcegitcommit: b79a6ec12641d258b9f199da0a35365898ae55ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67131085"
---
# <a name="azure-stack-storage"></a>Stockage Azure Stack

Les sections suivantes fournissent des informations relatives à la planification de la capacité de stockage d’Azure Stack pour vous aider à planifier selon les besoins de stockage de la solution.

## <a name="uses-and-organization-of-storage-capacity"></a>Utilisations et organisation de la capacité de stockage
La configuration hyperconvergée d’Azure Stack permet le partage d’appareils de stockage physique. Trois divisions principales du stockage disponible peuvent être partagées : l’infrastructure, le stockage temporaire des machines virtuelles du locataire et le stockage auquel s’adossent les objets blob, les tables et les files d’attente des services de stockage compatible Azure (ACS).

## <a name="storage-spaces-direct-cache-and-capacity-tiers"></a>Cache direct et niveaux de capacité des espaces de stockage direct
La capacité de stockage est utilisée pour le système d’exploitation, la journalisation locale, les vidages et pour les autres besoins de stockage temporaires de l’infrastructure. Cette capacité de stockage local est séparée (appareils et capacité) des appareils de stockage placés sous la gestion de la configuration d’espaces de stockage direct. Le reste des appareils de stockage est placé dans un pool unique de capacité de stockage, quel que soit le nombre de serveurs dans l’unité d’échelle.

Ces appareils sont de deux types : cache et capacité. Les espaces de stockage direct utilisent des appareils de cache pour le cache en écriture différée et la mise en cache de lecture. Les capacités de ces appareils de cache, quand elles sont utilisées, ne sont pas destinées à une capacité « visible », formatée, des disques virtuels formatés. En revanche, les espaces de stockage direct utilisent des appareils de capacité à cet effet, fournissant l’emplacement de base des données managées.

L’infrastructure Azure Stack alloue et gère directement toutes les capacités de stockage. L’opérateur n’a besoin de faire de choix concernant la configuration, l’allocation ou l’expansion de capacité. Azure Stack automatise ces décisions de conception en fonction des besoins de la solution pendant l’installation et le déploiement initiaux, ou lors de l’expansion de capacité. Dans le cadre de la conception, Azure Stack prend en considération la résilience, la capacité de réserve pour les régénérations et d’autres aspects. 

Les opérateurs peuvent choisir entre une configuration de stockage *entièrement flash* ou *hybride* :

![Diagramme de planification de la capacité de stockage Azure](media/azure-stack-capacity-planning/storage.png)

La configuration entièrement flash peut être à un ou à deux niveaux. Dans le cas d’une configuration à un niveau, tous les appareils de capacité sont du même type (par exemple, NVMe, SSD SATA ou SSD SAS), et les appareils de cache ne sont pas utilisés. Dans une configuration entièrement flash à deux niveaux classique, les appareils de cache sont de type NVMe, et les appareils de capacité de type SSD SATA ou SAS.

Dans une configuration hybride à deux niveaux, les appareils de cache sont de type NVMe, SATA ou SSD SATA, et les appareils de capacité de type HDD. 

Voici un bref résumé de la configuration des espaces de stockage direct et du stockage Azure Stack :
- Un pool d’espaces de stockage direct par unité d’échelle (tous les appareils de stockage sont configurés dans un pool unique).
- Les disques virtuels sont créés sous la forme d’un miroir triple pour optimiser les performances et la résilience.
- Chaque disque virtuel est formaté comme un système de fichiers ReFS.
- La capacité du disque virtuel est calculée et allouée de manière à laisser la capacité de données d’un appareil de capacité non allouée dans le pool. Cela équivaut à un disque de capacité par serveur.
- BitLocker est activé sur chaque système de fichiers ReFS pour le chiffrement des données au repos. 

Les disques virtuels créés automatiquement et leurs capacités sont les suivants :

|Nom|Calcul de la capacité|Description|
|-----|-----|-----|
|Appareil local/de démarrage|Minimum de 340 Go<sup>1</sup>|Stockage serveur individuel pour les images du système d’exploitation et les machines virtuelles de l’infrastructure « locale ».|
|Infrastructure|3,5 To|Utilisation de toute l’infrastructure Azure Stack.|
|VmTemp|Voir ci-dessous<sup>2</sup>|Les machines virtuelles du locataire disposent d’un disque temporaire attaché, et les données sont stockées sur ces disques virtuels.|
|ACS|Voir ci-dessous <sup>3</sup>|Capacité de stockage compatible Azure pour la maintenance des objets blob, des tables et des files d’attente.|

<sup>1</sup> Capacité de stockage minimale requise pour le partenaire de solutions Azure Stack.

<sup>2</sup> La taille du disque virtuel utilisé pour les disques temporaires de la machine virtuelle du locataire est calculée comme un ratio de la mémoire physique du serveur. Le disque temporaire est un ratio de la mémoire physique allouée à la machine virtuelle. L’allocation pour le stockage du « disque temporaire » dans Azure Stack capture la plupart des cas d’utilisation, mais n’est peut-être pas en mesure de satisfaire tous les besoins de stockage sur disque temporaire. Le ratio est un compromis entre la mise à disposition d’un stockage temporaire et l’économie d’une majorité de la capacité de stockage de la solution uniquement pour la capacité de disque temporaire. Un disque de stockage temporaire est créé par serveur dans l’unité d’échelle. La capacité du stockage temporaire ne dépasse pas 10 % de la capacité totale de stockage disponible dans le pool de stockage de l’unité d’échelle. Le calcul est similaire à l’exemple suivant :

```
  DesiredTempStoragePerServer = PhysicalMemory * 0.65 * 8
  TempStoragePerSolution = DesiredTempStoragePerServer * NumberOfServers
  PercentOfTotalCapacity = TempStoragePerSolution / TotalAvailableCapacity
  If (PercentOfTotalCapacity <= 0.1)
      TempVirtualDiskSize = DesiredTempStoragePerServer
  Else
      TempVirtualDiskSize = (TotalAvailableCapacity * 0.1) / NumberOfServers
```

<sup>3</sup> Les disques virtuels créés pour être utilisés par des stockages compatibles Azure sont une simple division de la capacité restante. Comme indiqué, tous les disques virtuels sont des miroirs triples, et un disque de capacité pour chaque serveur est non alloué. Les différents disques virtuels énumérés ci-dessus sont d’abord alloués, et la capacité restante est ensuite utilisée pour les disques virtuels de stockage compatible Azure.


## <a name="next-steps"></a>Étapes suivantes
Apprenez-en davantage sur [Azure Stack Capacity Planner](azure-stack-capacity-planner.md).
