---
title: Choix des lecteurs pour Azure Stack HCI
description: Découvrez comment choisir des lecteurs pour les espaces de stockage direct dans Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: ca2903e6d7680b38f21482076bf46e49097d444f
ms.sourcegitcommit: cfc453689a5a2f2491ce00ab77a7c5bfddfe01d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87997751"
---
# <a name="choosing-drives-for-azure-stack-hci"></a>Choix des lecteurs pour Azure Stack HCI

> S’applique à : Azure Stack HCI, version 20H2 ; Windows Server 2019

Cette rubrique fournit des conseils sur le choix de lecteurs pour les [espaces de stockage direct](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) dans le but de répondre à vos besoins de performances et de capacité pour Azure Stack HCI.

## <a name="drive-types"></a>Types de lecteurs

Pour l’heure, les espaces de stockage direct fonctionnent avec quatre types de lecteurs :

| Type de lecteur | Description |
|----------------------|--------------------------|
|![PMem](media/choose-drives/pmem-100px.png)|**PMem** fait référence à la mémoire persistante, un nouveau type de stockage à faible latence et haute performance.|
|![NVMe](media/choose-drives/NVMe-100-px.png)|**NVMe** (Non-Volatile Memory Express) fait référence aux disques SSD qui se trouvent directement sur le bus PCIe. Les facteurs de forme courants sont les suivants : U.2 2,5 pouces, PCIe Add-In-Card (AIC) et M.2. NVMe offre un plus haut débit d’E/S par seconde (IOPS) et d’E/S avec une latence plus faible que tout autre type de lecteur actuellement pris en charge, sauf PMem.|
|![SSD](media/choose-drives/SSD-100-px.png)|**SSD** fait référence aux disques SSD qui se connectent par le biais de connecteurs SATA ou SAS conventionnels.|
|![HDD](media/choose-drives/HDD-100-px.png)|**HDD** fait référence aux lecteurs de disque dur magnétiques rotationnels qui offrent une capacité de stockage importante.|

## <a name="built-in-cache"></a>Cache intégré

Les espaces de stockage direct intègrent un cache coté serveur. Il s’agit d’un cache en lecture et écriture volumineux, persistant et en temps réel. Dans les déploiements avec plusieurs types de lecteurs, les espaces de stockage direct utilisent automatiquement tous les lecteurs les plus rapides. Les lecteurs restants sont utilisés pour la capacité.

Pour plus d’informations, consultez [Présentation du cache dans les espaces de stockage direct](/windows-server/storage/storage-spaces/understand-the-cache).

## <a name="option-1--maximizing-performance"></a>Option 1 : Maximisation des performances

Vous devez opter pour une solution « 100 % flash » si vous souhaitez garantir une latence uniforme et prévisible inférieure à une milliseconde pour toutes les opérations aléatoires de lecture et d’écriture de données, ou si vous voulez obtenir un très grand nombre d’E/S par seconde (nous en avons réalisé [plus de 13 millions](https://techcommunity.microsoft.com/t5/storage-at-microsoft/the-new-hci-industry-record-13-7-million-iops-with-windows/ba-p/428314)) ou un débit d’E/S très élevé (nous avons effectué des lectures de plus de 500 Go/s).

Il existe actuellement trois façons de le faire :

![All-Flash-Deployment-Possibilities](media/choose-drives/All-Flash-Deployment-Possibilities.png)

1. **NVMe uniquement**. L’utilisation de disques exclusivement NVMe offre des performances sans égal, en plus d’une faible latence hautement prévisible. Si tous vos lecteurs sont du même modèle, il n’y a pas de cache. Vous pouvez également combiner des modèles NVMe plus ou moins endurants, en configurant les modèles plus endurants pour mettre en cache les écritures sur les modèles moins endurants (ce qui [nécessite une configuration](/windows-server/storage/storage-spaces/understand-the-cache#manual-configuration)).

2. **NVMe + SSD**. Si vous utilisez NVMe avec des SSD, NVMe met automatiquement en cache les écritures sur les SSD. De cette manière, les écritures sont fusionnées dans le cache et déstockées uniquement si nécessaire pour réduire la charge sur les SSD. Les caractéristiques d’écriture sont similaires à NVMe, mais les lectures sont effectuées directement à partir de SSD également rapides.

3. **SSD uniquement**. Comme pour l’option NVMe uniquement, il n’y a pas de cache si tous vos lecteurs sont du même modèle. Si vous combinez des modèles plus ou moins endurants, vous pouvez configurer les modèles plus endurants pour mettre en cache les écritures sur les modèles moins endurants (ce qui [nécessite une configuration](/windows-server/storage/storage-spaces/understand-the-cache#manual-configuration)).

   >[!NOTE]
   > L’un des avantages d’utiliser uniquement des NVMe ou SSD sans cache est que vous exploitez toute la capacité de stockage de chaque lecteur. Aucune partie de la capacité n’est utilisée pour la mise en cache, ce qui peut être intéressant à plus petite échelle.

## <a name="option-2--balancing-performance-and-capacity"></a>Option 2 : Équilibre entre performances et capacité

Pour les environnements avec des applications et des charges de travail variées, certaines étant assorties d’exigences strictes en termes de performances et d’autres nécessitant une importante capacité de stockage, vous devez opter pour une solution « hybride » avec des NVMe ou des SSD assurant la mise en cache pour les HDD plus importants.

![Hybrid-Deployment-Possibilities](media/choose-drives/Hybrid-Deployment-Possibilities.png)

1. **NVMe + HDD**. Les lecteurs NVMe accélèrent les opérations de lecture et d’écriture en les mettant toutes en cache. La mise en cache des lectures permet aux HDD de se concentrer sur les écritures. La mise en cache des écritures absorbe les pics d’opérations et permet de fusionner les écritures et de les déstocker uniquement si nécessaire, d’une manière sérialisée artificielle qui optimise le débit d’E/S par seconde et d’E/S des lecteurs HDD. Les caractéristiques d’écriture sont similaires à celles de NVMe. Pour les données lues récemment ou fréquemment, les caractéristiques de lecture sont également similaires à celles de NVMe.

2. **SSD + HDD**. À l’instar de ce qui précède, les SSD accélèrent les opérations de lecture et d’écriture en les mettant toutes les deux en cache. Les caractéristiques d’écriture sont similaires à celles des SSD. Pour les données lues récemment ou fréquemment, les caractéristiques de lecture sont également similaires à celles des SSD.

    Il existe une autre option, plus marginale, qui consiste à utiliser les *trois types* de lecteurs.

3. **NVMe + SSD + HDD**. Si vous utilisez les trois types de lecteurs, les lecteurs NVMe assurent la mise en cache pour les SSD et les HDD. L’avantage est que cela vous permet de créer des volumes sur les SSD et les HDD, côte-à-côte dans le même cluster et accélérés par NVMe. Les premiers fonctionnent exactement comme dans un déploiement « 100 % flash » et les seconds exactement comme dans le déploiement « hybride » décrit plus haut. D’un point de vue conceptuel, cela revient à avoir deux pools, avec une gestion indépendante de la capacité, des cycles d’échec et de réparation, etc.

   >[!IMPORTANT]
   > Nous vous recommandons d’utiliser le niveau SSD pour placer vos charges de travail les plus sensibles aux performances dans une configuration 100 % flash.

## <a name="option-3--maximizing-capacity"></a>Option 3 : Optimisation de la capacité

Pour les charges de travail qui nécessitent une grande capacité et des écritures peu fréquentes, comme l’archivage, les cibles de sauvegarde, les entrepôts de données ou le stockage « à froid », nous vous conseillons de combiner quelques SSD pour la mise en cache avec de nombreux disques durs plus importants pour la capacité.

![Options de déploiement pour optimiser la capacité](media/choose-drives/maximizing-capacity.png)

1. **SSD + HDD**. Les SSD assurent la mise en cache des lectures et des écritures pour absorber les pics d’opérations et offrir les performances d’écriture des SSD, et les déstockent seulement quand nécessaire sur les HDD pour optimiser la capacité.

>[!IMPORTANT]
>La configuration avec des HDD uniquement n’est pas prise en charge. La mise en cache de SSD à forte endurance vers des SSD à faible endurance n’est pas conseillée.

## <a name="sizing-considerations"></a>Considérations relatives à la taille

### <a name="cache"></a>Cache

Chaque serveur doit avoir au minimum deux lecteurs de cache pour assurer la redondance. Nous vous recommandons de faire en sorte que le nombre de lecteurs de capacité soit un multiple du nombre de lecteurs de cache. Par exemple, si vous avez 4 lecteurs de cache, vous bénéficierez de performances plus cohérentes avec 8 lecteurs de capacité (ratio de 1:2) qu’avec 7 ou 9.

Le cache doit être dimensionné de façon à accueillir la plage de travail de vos applications et charges de travail, c’est-à-dire toutes les données activement lues ou écrites à n’importe quel moment. Il n’y a pas d’autre exigence en matière de taille du cache. Pour les déploiements avec des HDD, un bon point de départ est 10 % de capacité. Par exemple, si chaque serveur a 4 x 4 To (HDD) = 16 To de capacité, vous avez alors 2 x 800 Go (SSD) = 1,6 To de cache par serveur. Pour les déploiements 100 % flash, en particulier avec des SSD à [forte endurance](https://techcommunity.microsoft.com/t5/storage-at-microsoft/understanding-ssd-endurance-drive-writes-per-day-dwpd-terabytes/ba-p/426024), il peut être judicieux de commencer plus près de 5 % de capacité. Par exemple, si chaque serveur a 24 x 1,2 To (SSD) = 28,8 To de capacité, vous avez alors 2 x 750 Go (NVMe) = 1,5 To de cache par serveur. Vous pouvez toujours ajouter ou supprimer des lecteurs de cache par la suite en fonction de vos besoins.

### <a name="general"></a>Général

Nous vous recommandons de limiter la capacité de stockage totale par serveur à environ 400 téraoctets (To). Plus la capacité de stockage par serveur est importante, plus il faut de temps pour resynchroniser les données après un arrêt ou un redémarrage (par exemple, lors de l’application de mises à jour logicielles). La taille maximale actuelle par pool de stockage est de 4 pétaoctets (Po), soit 4 000 To pour Windows Server 2019.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez également :

- [Fonctionnement du cache dans Azure Stack HCI](cache.md)
- [Configuration matérielle requise pour les espaces de stockage direct](/windows-server/storage/storage-spaces/storage-spaces-direct-hardware-requirements)
- [Planification de volumes dans Azure Stack HCI](plan-volumes.md)
- [Tolérance de panne et efficacité du stockage](fault-tolerance.md)
- [Comprendre et déployer la mémoire persistante](/windows-server/storage/storage-spaces/deploy-pmem)
