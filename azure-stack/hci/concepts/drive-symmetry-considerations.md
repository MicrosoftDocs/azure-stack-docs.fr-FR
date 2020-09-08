---
title: Considérations relatives à la symétrie des lecteurs pour Azure Stack HCI
description: Cette rubrique décrit les contraintes de symétrie des lecteurs et propose des exemples de configurations prises en charge et non prises en charge.
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/01/2020
ms.openlocfilehash: d4d10d7a2470f474130b3a14758b608b7ac2ad34
ms.sourcegitcommit: 08a421ab5792ab19cc06b849763be22f051e6d78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89365068"
---
# <a name="drive-symmetry-considerations-for-azure-stack-hci"></a>Considérations relatives à la symétrie des lecteurs pour Azure Stack HCI

> S’applique à : Azure Stack HCI, version 20H2 ; Windows Server 2019

Azure Stack HCI fonctionne mieux lorsque chaque serveur possède les mêmes lecteurs.

Dans la réalité, nous savons que cela n’est pas toujours évident, car Azure Stack HCI est conçu pour s’exécuter pendant des années et pour évoluer à mesure que les besoins de votre organisation évoluent. Aujourd’hui, vous pouvez acheter des disques durs spacieux de 3 To, mais à échéance d’un an, il peut s’avérer impossible de trouver des disques aussi petits. Dès lors, une certaine combinaison est possible et prise en charge. Gardez toutefois à l’esprit que la symétrie est toujours la meilleure solution.

Cette rubrique décrit les contraintes et propose des exemples de configurations prises en charge et non prises en charge.

## <a name="constraints"></a>Contraintes

### <a name="type"></a>Type

Tous les serveurs doivent posséder les mêmes [types de lecteurs](choose-drives.md#drive-types).

Par exemple, si un serveur possède un lecteur NVMe, ils doivent *tous* posséder un lecteur NVMe.

### <a name="number"></a>Number

Tous les serveurs doivent posséder le même nombre de lecteurs de chaque type.

Par exemple, si un serveur possède six disques SSD, ils doivent *tous* posséder six disques SSD.

   > [!NOTE]
   > Il est possible que le nombre de lecteurs diffère temporairement en cas de défaillances ou lors de l’ajout ou de la suppression de lecteurs.

### <a name="model"></a>Modèle

Chaque fois que possible, nous vous recommandons d’utiliser des lecteurs de même modèle et présentant la même version de microprogramme. Si vous n’êtes pas en mesure de le faire, sélectionnez avec soin des lecteurs le plus proche possible. Nous déconseillons la combinaison de lecteurs du même type avec des caractéristiques de performances ou d’endurance très différentes (sauf si l’un d’eux est le cache et l’autre la capacité), car Azure Stack HCI distribue les E/S uniformément et ne fait pas de distinction en fonction du modèle.

   > [!NOTE]
   > Il est en revanche possible de combiner des disques SATA et SAS.

### <a name="size"></a>Taille

Nous vous recommandons d’utiliser des lecteurs de même taille chaque fois possible. L’utilisation de lecteurs de capacité différente peut entraîner une capacité inutilisable, et l’utilisation de lecteurs de cache de taille différente peut ne pas améliorer les performances du cache. Voir la section suivante pour plus de détails.

   > [!WARNING]
   > Des tailles de lecteurs de capacité différente sur plusieurs serveurs peuvent entraîner une capacité bloquée.

## <a name="understand-capacity-imbalance"></a>Comprendre : déséquilibre de la capacité

Azure Stack HCI résiste bien au déséquilibre de la capacité entre les lecteurs et entre les serveurs. Même en cas de déséquilibre sévère, tout continue de fonctionner. Toutefois, selon plusieurs facteurs, la capacité non disponible dans chaque serveur peut ne pas être utilisable.

Pour le comprendre, penchez-vous sur l’illustration simplifiée ci-dessous. Chaque zone de couleur représente une copie de données mises en miroir. Par exemple, les zones marquées A, A', and A'' correspondent à trois copies des mêmes données. À des fins de tolérance de panne du serveur, ces copies *doivent* être stockées sur des serveurs différents.

### <a name="stranded-capacity"></a>Capacité bloquée

Comme illustré, le Serveur 1 (10 To) et le Serveur 2 (10 To) sont saturés. Le Serveur 3 possède des lecteurs plus volumineux et dès lors, sa capacité totale est supérieure (15 To). Toutefois, le stockage de données miroir triple sur le Serveur 3 requiert des copies sur le Serveur 1 et le Serveur 2, qui sont déjà saturés. La capacité restante de 5 To du Serveur 3 ne peut pas être utilisée car elle est *« bloquée »* .

![Miroir triple, trois serveurs, capacité bloquée](media/drive-symmetry-considerations/Size-Asymmetry-3N-Stranded.png)

### <a name="optimal-placement"></a>Sélection élective optimale

À l’inverse, avec quatre serveurs de 10 To, 10 To, 10 To, 15 To et une résilience de miroir triple, il *est* possible d’opérer une sélection élective des copies de manière à utiliser toute la capacité disponible, comme illustré. Chaque fois que possible, l’allocateur d’espaces de stockage direct recherche et utilise la sélection élective optimale afin d’éviter toute capacité bloquée.

![Miroir triple, quatre serveurs, aucune capacité bloquée](media/drive-symmetry-considerations/Size-Asymmetry-4N-No-Stranded.png)

Entre autres facteurs, le nombre de serveurs, la résilience, la sévérité du déséquilibre de la capacité déterminent s’il existe une capacité bloquée. **La règle générale la plus prudente consiste à supposer que seule la capacité disponible dans chaque serveur est toujours utilisable.**

## <a name="understand-cache-imbalance"></a>Comprendre : déséquilibre du cache

Azure Stack HCI résiste bien au déséquilibre du cache entre les lecteurs et entre les serveurs. Même en cas de déséquilibre sévère, tout continue de fonctionner. En outre, Azure Stack HCI utilise toujours de manière la plus complète possible l’intégralité du cache disponible.

Toutefois, l’utilisation de lecteurs de cache de taille différente peut ne pas améliorer les performances de cache de façon uniforme ou prévisible : seules les E/S vers les [liaisons de lecteurs](cache.md#server-side-architecture) présentant des lecteurs de cache plus volumineux permettent d’améliorer les performances. Azure Stack HCI distribue uniformément les E/S entre les liaisons et ne fait aucune distinction en termes de rapport cache-capacité.

![Déséquilibre du cache](media/drive-symmetry-considerations/Cache-Asymmetry.png)

   > [!TIP]
   > Pour plus d’informations sur les liaisons de cache, consultez [Compréhension du cache](cache.md).

## <a name="example-configurations"></a>Exemples de configurations

Vous trouverez ci-dessous plusieurs configurations prises en charge et non prises en charge :

### <a name="supported-supported-different-models-between-servers"></a>![pris en charge](media/drive-symmetry-considerations/supported.png) Pris en charge : différents modèles entre les serveurs

Les deux premiers serveurs utilisent le modèle NVMe « X », tandis que le troisième utilise le modèle NVMe « Z », qui est très similaire.

| Serveur 1                    | Serveur 2                    | Serveur 3                    |
|-----------------------------|-----------------------------|-----------------------------|
| 2 x modèle NVMe X (cache)    | 2 x modèle NVMe X (cache)    | 2 x modèle NVMe Z (cache)    |
| 10 x modèle SSD Y (capacité) | 10 x modèle SSD Y (capacité) | 10 x modèle SSD Y (capacité) |

Cette topologie est prise en charge.

### <a name="supported-supported-different-models-within-server"></a>![pris en charge](media/drive-symmetry-considerations/supported.png) Pris en charge : différents modèles dans le serveur

Chaque serveur utilise une combinaison différente de modèles HDD « Y » et « Z », qui sont très similaires. Chaque serveur dispose d’un total de 10 disques HDD.

| Serveur 1                   | Serveur 2                   | Serveur 3                   |
|----------------------------|----------------------------|----------------------------|
| 2 x modèle SSD X (cache)    | 2 x modèle SSD X (cache)    | 2 x modèle SSD X (cache)    |
| 7 x modèle HDD Y (capacité) | 5 x modèle HDD Y (capacité) | 1 x modèle HDD Y (capacité) |
| 3 x modèle HDD Z (capacité) | 5 x modèle HDD Z (capacité) | 9 x modèle HDD Z (capacité) |

Cette topologie est prise en charge.

### <a name="supported-supported-different-sizes-across-servers"></a>![pris en charge](media/drive-symmetry-considerations/supported.png) Pris en charge : différentes tailles sur plusieurs serveurs

Les deux premiers serveurs utilisent un disque HDD 4 To, tandis que le troisième utilise un disque HDD6 To très similaire.

| Serveur 1                | Serveur 2                | Serveur 3                |
|-------------------------|-------------------------|-------------------------|
| 2 x NVMe 800 Go (cache) | 2 x NVMe 800 Go (cache) | 2 x NVMe 800 Go (cache) |
| 4 x HDD 4 To (capacité) | 4 x HDD 4 To (capacité) | 4 x HDD 6 To (capacité) |

Bien qu’elle entraîne une capacité bloquée, cette configuration est prise en charge.

### <a name="supported-supported-different-sizes-within-server"></a>![pris en charge](media/drive-symmetry-considerations/supported.png) Pris en charge : différentes tailles dans le serveur

Chaque serveur utilise une combinaison différente de disques SSD 1.2 To et 1.6 To très similaires. Chaque serveur possède un total de 4 disques SSD.

| Serveur 1                 | Serveur 2                 | Serveur 3                 |
|--------------------------|--------------------------|--------------------------|
| 3 x SSD 1.2 To (cache)   | 2 x SSD 1.2 To (cache)   | 4 x SSD 1.2 To (cache)   |
| 1 x SSD 1.6 To (cache)   | 2 x SSD 1.6 To (cache)   | -                        |
| 20 x HDD 4 To (capacité) | 20 x HDD 4 To (capacité) | 20 x HDD 4 To (capacité) |

Cette topologie est prise en charge.

### <a name="unsupported-not-supported-different-types-of-drives-across-servers"></a>![absence de prise en charge](media/drive-symmetry-considerations/unsupported.png) Non pris en charge : différents types de lecteurs sur les serveurs

Le Serveur 1 possède un NVMe, mais pas les autres.

| Serveur 1            | Serveur 2            | Serveur 3            |
|---------------------|---------------------|---------------------|
| 6 x NVMe (cache)    | -                   | -                   |
| -                   | 6 x SSD (cache)     | 6 x SSD (cache)     |
| 18 x HDD (capacité) | 18 x HDD (capacité) | 18 x HDD (capacité) |

Ceci n’est pas pris en charge. Les types de lecteurs doivent être identiques sur chaque serveur.

### <a name="unsupported-not-supported-different-number-of-each-type-across-servers"></a>![absence de prise en charge](media/drive-symmetry-considerations/unsupported.png) Non pris en charge : nombre différent de chaque type sur plusieurs serveurs

Le Serveur 3 possède plus de lecteurs que les autres.

| Serveur 1            | Serveur 2            | Serveur 3            |
|---------------------|---------------------|---------------------|
| 2 x NVMe (cache)    | 2 x NVMe (cache)    | 4 x NVMe (cache)    |
| 10 x HDD (capacité) | 10 x HDD (capacité) | 20 x HDD (capacité) |

Ceci n’est pas pris en charge. Le nombre de lecteurs de chaque type doit être identique sur chaque serveur.

### <a name="unsupported-not-supported-only-hdd-drives"></a>![absence de prise en charge](media/drive-symmetry-considerations/unsupported.png) Non pris en charge : disques HDD uniquement

Seuls des disques HDD sont connectés aux serveurs.

|Serveur 1|Serveur 2|Serveur 3|
|-|-|-|
|18 x HDD (capacité) |18 x HDD (capacité)|18 x HDD (capacité)|

Ceci n’est pas pris en charge. Vous devez ajouter au moins deux lecteurs de cache (NvME ou SSD) attachés à chacun des serveurs.

## <a name="summary"></a>Récapitulatif

Pour résumer, chaque serveur du cluster doit présenter les mêmes types de lecteurs et le même nombre de chaque type. Il est possible de combiner les modèles et les tailles des lecteurs si besoin, en prenant en compte les points ci-dessus.

| Contrainte | State |
|--|--|
| Mêmes types de lecteurs sur chaque serveur | **Obligatoire** |
| Même nombre de chaque type sur chaque serveur | **Obligatoire** |
| Mêmes modèles de lecteurs sur chaque serveur | Recommandé |
| Mêmes tailles de lecteurs sur chaque serveur | Recommandé |

## <a name="next-steps"></a>Étapes suivantes

Pour consulter des informations connexes, reportez-vous également à :

- [Avant le déploiement d’Azure Stack HCI](../deploy/before-you-start.md)
- [Choisir des lecteurs](choose-drives.md)
