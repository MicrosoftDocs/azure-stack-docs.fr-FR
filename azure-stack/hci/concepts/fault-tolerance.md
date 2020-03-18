---
title: Tolérance de panne et efficacité du stockage dans Azure Stack HCI
description: Discussion des options de résilience dans les espaces de stockage direct, notamment la mise en miroir et la parité.
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 02/28/2020
ms.openlocfilehash: 9ace3960b4c54461a4153c4997694e6d17ee4fd1
ms.sourcegitcommit: a77dea675af6500bdad529106f5782d86bec6a34
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "79026115"
---
# <a name="fault-tolerance-and-storage-efficiency-in-azure-stack-hci"></a>Tolérance de panne et efficacité du stockage dans Azure Stack HCI

>S’applique à : Windows Server 2019

Cette rubrique présente les options de résilience disponibles dans les espaces de stockage direct et décrit les exigences en matière d’échelle, l’efficacité du stockage ainsi que les compromis et avantages d’ordre général de chaque option. Elle fournit également des instructions d’utilisation pour vous aider à bien démarrer ainsi que des références à des livres blancs, blogs et contenus supplémentaires pour en savoir plus.

Si vous avez déjà utilisé les espaces de stockage, vous pouvez passer à la section [Récapitulatif](#summary).

## <a name="overview"></a>Vue d’ensemble

À la base, les espaces de stockage ont pour but d’assurer la tolérance de panne, ou « résilience », de vos données. Leur implémentation est similaire à celle de la technologie RAID, à la différence près qu’ils sont distribués sur les serveurs et implémentés au niveau logiciel.

Comme pour RAID, il existe plusieurs façons d’utiliser les espaces de stockage pour parvenir à l’objectif fixé, d’où différents compromis entre tolérance de panne, efficacité du stockage et complexité des calculs. L’approche que vous suivez appartient généralement à l’une des deux catégories suivantes : « mise en miroir » et « parité » (parfois appelée « codage d’effacement »).

## <a name="mirroring"></a>Mise en miroir

La mise en miroir fournit la tolérance de panne en conservant plusieurs copies de toutes les données. Cette approche s’apparente à RAID-1. La façon dont les données sont agrégées par bande et placées n’est pas négligeable (consultez [ce blog](https://blogs.technet.microsoft.com/filecab/2016/11/21/deep-dive-pool-in-spaces-direct/) pour en savoir plus), mais il est absolument vrai de dire que toutes les données stockées à l’aide de la mise en miroir sont écrites, dans leur intégralité, plusieurs fois. Chaque copie est écrite sur différents supports physiques (lecteurs différents sur différents serveurs) qui sont supposés échouer de manière indépendante.

Les espaces de stockage proposent deux types de mise en miroir : « double » et « triple ».

### <a name="two-way-mirror"></a>Miroir double

La mise en miroir double écrit deux copies de tout. Son efficacité de stockage est de 50 % : pour écrire 1 To de données, vous devez disposer d’au moins 2 To de capacité de stockage physique. De même, vous devez disposer d’au moins deux [« domaines d’erreur » matériels](/windows-server/failover-clustering/fault-domains). Avec les espaces de stockage direct, cela signifie deux serveurs.

![two-way-mirror](media/fault-tolerance/two-way-mirror-180px.png)

   >[!WARNING]
   > Si vous avez plus de deux serveurs, nous vous recommandons d’utiliser à la place la mise en miroir triple.

### <a name="three-way-mirror"></a>Miroir triple

La mise en miroir triple écrit trois copies de tout. Son efficacité de stockage est de 33,3% : pour écrire 1 To de données, vous devez disposer d’au moins 3 To de capacité de stockage physique. De même, vous devez disposer d’au moins trois domaines d’erreur matériels. Avec les espaces de stockage direct, cela signifie trois serveurs.

La mise en miroir triple peut tolérer en toute sécurité au moins [deux problèmes matériels (lecteur ou serveur) à la fois](#examples). Par exemple, si vous redémarrez un serveur et qu’un autre lecteur ou serveur échoue, toutes les données restent protégées et accessibles en continu.

![three-way-mirror](media/fault-tolerance/three-way-mirror-180px.png)

## <a name="parity"></a>Parité

L’encodage de parité (souvent appelé « codage d’effacement ») assure la tolérance de panne à l’aide d’opérations arithmétiques au niveau du bit, ce qui peut devenir [extrêmement compliqué](https://www.microsoft.com/research/wp-content/uploads/2016/02/LRC12-cheng20webpage.pdf). Cette solution est plus complexe que la mise en miroir, et de nombreuses ressources en ligne (comme le document [Dummies Guide to Erasure Coding](http://smahesh.com/blog/2012/07/01/dummies-guide-to-erasure-coding/)) peuvent vous aider à vous en faire une idée plus précise. Il va sans dire qu’elle offre un stockage plus efficace sans compromettre la tolérance de panne.

Les espaces de stockage proposent deux types de parité : la parité « simple » et la parité « double », la seconde employant une technique avancée appelée « codes de reconstruction locale » à plus grande échelle.

> [!IMPORTANT]
> Nous vous recommandons d’utiliser la mise en miroir pour la plupart des charges de travail sensibles aux performances. Pour en savoir plus sur la façon d’équilibrer les performances et la capacité en fonction de votre charge de travail, consultez [Planifier des volumes](/windows-server/storage/storage-spaces/plan-volumes#choosing-the-resiliency-type).

### <a name="single-parity"></a>Parité simple
La parité simple conserve un seul symbole de parité au niveau du bit, ce qui assure la tolérance de panne pour une seule défaillance à la fois. Cette approche s’apparente à RAID-5. Pour utiliser la parité simple, vous devez disposer d’au moins trois domaines d’erreur matériels. Avec les espaces de stockage direct, cela signifie trois serveurs. Étant donné que la mise en miroir triple fournit une plus grande tolérance de panne à la même échelle, nous déconseillons l’utilisation de la parité simple. Toutefois, cette solution est là si vous en avez vraiment besoin et elle est totalement prise en charge.

   >[!WARNING]
   > Nous déconseillons l’utilisation de la parité simple car elle ne tolère qu’une défaillance matérielle à la fois : si vous redémarrez un serveur et qu’un autre lecteur ou serveur tombe soudainement en panne, vous subissez un temps d’arrêt. Si vous n’avez que trois serveurs, nous vous recommandons d’utiliser la mise en miroir triple. Si vous en avez quatre ou plus, consultez la section suivante.

### <a name="dual-parity"></a>Parité double

La parité double implémente les codes de correction d’erreur Reed-Solomon pour conserver deux symboles de parité au niveau du bit. Elle offre ainsi la même tolérance de panne que la mise en miroir triple (c’est-à-dire jusqu’à deux défaillances en même temps), mais avec une meilleure efficacité de stockage. Cette approche s’apparente à RAID-6. Pour utiliser la parité double, vous devez disposer d’au moins quatre domaines d’erreur matériels. Avec les espaces de stockage direct, cela signifie quatre serveurs. À cette échelle, l’efficacité du stockage est de 50 % : pour stocker 2 To de données, vous devez disposer de 4 To de capacité de stockage physique.

![dual-parity](media/fault-tolerance/dual-parity-180px.png)

L’efficacité du stockage de la parité double passe de 50 % à 80 % quand vous augmentez votre nombre de domaines d’erreur matériels. Par exemple, si vous avez sept domaines d’erreur matériels (soit sept serveurs avec les espaces de stockage direct), l’efficacité passe à 66,7 %. Pour stocker 4 To de données, vous n’avez donc besoin que de 6 To de capacité de stockage physique.

![dual-parity-wide](media/fault-tolerance/dual-parity-wide-180px.png)

Pour connaître l’efficacité de la parité double et des codes de reconstruction locale à chaque échelle, consultez la section [Récapitulatif](#summary).

### <a name="local-reconstruction-codes"></a>Codes de reconstruction locale

Les espaces de stockage introduisent une technique de pointe développée par Microsoft Research appelée « codes de reconstruction locale » (LRC, Local Reconstruction Codes). À grande échelle, la parité double utilise LRC pour fractionner son encodage/décodage en groupes plus petits afin de réduire la surcharge associée aux écritures ou à la récupération après des défaillances.

Avec des lecteurs de disque dur (HDD), la taille des groupes est de quatre symboles. Avec les disques SSD, la taille des groupes est de six symboles. Par exemple, voici à quoi ressemble la disposition avec des lecteurs de disque dur et 12 domaines d’erreur matériels (c’est-à-dire 12 serveurs). Vous avez deux groupes de quatre symboles de données. L’efficacité du stockage est de 72,7 %.

![local-reconstruction-codes](media/fault-tolerance/local-reconstruction-codes-180px.png)

Nous vous recommandons de suivre cette procédure de [Claus Joergensen](https://twitter.com/clausjor), qui explique en détail mais de manière très compréhensible [comment les codes de reconstruction locale gèrent différents scénarios de panne et en quoi ils sont intéressants](https://blogs.technet.microsoft.com/filecab/2016/09/06/volume-resiliency-and-efficiency-in-storage-spaces-direct/).

## <a name="mirror-accelerated-parity"></a>Parité avec accélération par miroir

Un volume d’espaces de stockage direct peut utiliser à la fois la mise en miroir et la parité. Les écritures sont dans un premier temps hébergées dans la partie miroir, puis progressivement déplacées dans la partie parité. Il s’agit en fait d’[utiliser la mise en miroir pour accélérer le codage d’effacement](https://blogs.technet.microsoft.com/filecab/2016/09/06/volume-resiliency-and-efficiency-in-storage-spaces-direct/).

Pour combiner le miroir triple et la parité double, vous devez disposer d’au moins quatre domaines d’erreur (soit quatre serveurs).

L’efficacité du stockage de la parité avec accélération par miroir varie entre ce que vous pouvez tirer d’une utilisation 100 % miroir et d’une utilisation 100 % parité, et dépend des proportions que vous choisissez. Par exemple, la démonstration débutant à la 37e minute de cette présentation montre [plusieurs combinaisons permettant d’obtenir une efficacité de 46 %, 54 % et 65 %](https://www.youtube.com/watch?v=-LK2ViRGbWs&t=36m55s) avec 12 serveurs.

> [!IMPORTANT]
> Nous vous recommandons d’utiliser la mise en miroir pour la plupart des charges de travail sensibles aux performances. Pour en savoir plus sur la façon d’équilibrer les performances et la capacité en fonction de votre charge de travail, consultez [Planifier des volumes](/windows-server/storage/storage-spaces/plan-volumes#choosing-the-resiliency-type).

## <a name="summary"></a>Résumé

Cette section récapitule les types de résilience disponibles dans les espaces de stockage direct, l’échelle minimale requise pour chaque type, le nombre de défaillances que chaque type peut tolérer et l’efficacité du stockage correspondante.

### <a name="resiliency-types"></a>Types de résilience

|    Résilience          |    Tolérance de panne       |    Efficacité du stockage      |
|------------------------|----------------------------|----------------------------|
|    Miroir double      |    1                       |    50,0 %                   |
|    Miroir triple    |    2                       |    33,3 %                   |
|    Parité double         |    2                       |    50,0 % - 80,0 %           |
|    Mixte               |    2                       |    33,3 % - 80,0 %           |

### <a name="minimum-scale-requirements"></a>Échelle minimale requise

|    Résilience          |    Nombre minimal de domaines d’erreur requis   |
|------------------------|-------------------------------------|
|    Miroir double      |    2                                |
|    Miroir triple    |    3                                |
|    Parité double         |    4                                |
|    Mixte               |    4                                |

   >[!TIP]
   > À moins que vous n’utilisiez la [tolérance de panne de châssis ou de racks](/windows-server/failover-clustering/fault-domains), le nombre de domaines d’erreur fait référence au nombre de serveurs. Le nombre de lecteurs dans chaque serveur n’affecte pas les types de résilience que vous pouvez utiliser tant que vous respectez les exigences minimales applicables aux espaces de stockage direct.

### <a name="dual-parity-efficiency-for-hybrid-deployments"></a>Efficacité de la parité double pour les déploiements hybrides

Ce tableau montre l’efficacité de stockage de la parité double et des codes de reconstruction locale à chaque échelle pour des déploiements hybrides contenant à la fois des lecteurs de disque dur (HDD) et des disques SSD.

|    Domaines d’erreur      |    Disposition           |    Efficacité   |
|-----------------------|---------------------|-----------------|
|    2                  |    –                |    –            |
|    3                  |    –                |    –            |
|    4                  |    RS 2+2           |    50,0 %        |
|    5                  |    RS 2+2           |    50,0 %        |
|    6                  |    RS 2+2           |    50,0 %        |
|    7                  |    RS 4+2           |    66,7 %        |
|    8                  |    RS 4+2           |    66,7 %        |
|    9                  |    RS 4+2           |    66,7 %        |
|    10                 |    RS 4+2           |    66,7 %        |
|    11                 |    RS 4+2           |    66,7 %        |
|    12                 |    LRC (8, 2, 1)    |    72,7 %        |
|    13                 |    LRC (8, 2, 1)    |    72,7 %        |
|    14                 |    LRC (8, 2, 1)    |    72,7 %        |
|    15                 |    LRC (8, 2, 1)    |    72,7 %        |
|    16                 |    LRC (8, 2, 1)    |    72,7 %        |

### <a name="dual-parity-efficiency-for-all-flash-deployments"></a>Efficacité de la parité double pour les déploiements 100 % flash

Ce tableau montre l’efficacité du stockage de la parité double et des codes de reconstruction locale à chaque échelle pour des déploiements 100 % flash contenant uniquement des disques SSD. La disposition de parité peut utiliser des groupes plus volumineux et offrir un stockage plus efficace dans une configuration 100 % flash.

|    Domaines d’erreur      |    Disposition           |    Efficacité   |
|-----------------------|---------------------|-----------------|
|    2                  |    –                |    –            |
|    3                  |    –                |    –            |
|    4                  |    RS 2+2           |    50,0 %        |
|    5                  |    RS 2+2           |    50,0 %        |
|    6                  |    RS 2+2           |    50,0 %        |
|    7                  |    RS 4+2           |    66,7 %        |
|    8                  |    RS 4+2           |    66,7 %        |
|    9                  |    RS 6+2           |    75,0 %        |
|    10                 |    RS 6+2           |    75,0 %        |
|    11                 |    RS 6+2           |    75,0 %        |
|    12                 |    RS 6+2           |    75,0 %        |
|    13                 |    RS 6+2           |    75,0 %        |
|    14                 |    RS 6+2           |    75,0 %        |
|    15                 |    RS 6+2           |    75,0 %        |
|    16                 |    LRC (12, 2, 1)   |    80,0 %        |

## <a name="examples"></a>Exemples

À moins que vous n’ayez que deux serveurs, nous vous recommandons d’utiliser la mise en miroir triple et/ou la parité double car elles offrent une meilleure tolérance de panne. Plus précisément, ces approches garantissent la sécurité et l’accessibilité de toutes les données en permanence, même en cas de défaillance simultanée des deux domaines d’erreur (soit deux serveurs avec les espaces de stockage direct).

### <a name="examples-where-everything-stays-online"></a>Exemples où tout reste en ligne

Ces six exemples montrent ce que la mise en miroir triple et/ou la parité double **peuvent** tolérer.

- **1.**    Perte d’un lecteur (lecteurs de cache inclus)
- **2.**    Perte d’un serveur

![fault-tolerance-examples-1-and-2](media/fault-tolerance/Fault-Tolerance-Example-12.png)

- **3.**    Perte d’un serveur et d’un lecteur
- **4.**    Perte de deux lecteurs dans des serveurs différents

![fault-tolerance-examples-3-and-4](media/fault-tolerance/Fault-Tolerance-Example-34.png)

- **5.**    Perte de plus de deux lecteurs, tant que deux serveurs au maximum sont affectés
- **6.**    Perte de deux serveurs

![fault-tolerance-examples-5-and-6](media/fault-tolerance/Fault-Tolerance-Example-56.png)

Dans tous les cas, tous les volumes restent en ligne. (Vérifiez que votre cluster conserve le quorum.)

### <a name="examples-where-everything-goes-offline"></a>Exemples où tout passe hors connexion

Pendant leur durée de vie, les espaces de stockage peuvent tolérer un nombre illimité de défaillances car ils restaurent une résilience totale après chaque incident, à condition qu’ils en aient le temps. Toutefois, au maximum deux domaines peuvent être affectés par des défaillances à un moment donné. Les exemples suivants montrent ce que la mise en miroir triple et/ou la parité double **ne peuvent pas** tolérer.

- **7.** Perte de lecteurs dans trois serveurs ou plus à la fois
- **8.** Perte de trois serveurs ou plus à la fois

![fault-tolerance-examples-7-and-8](media/fault-tolerance/Fault-Tolerance-Example-78.png)

## <a name="usage"></a>Usage

Consultez [Création de volumes dans les espaces de stockage direct](/windows-server/storage/storage-spaces/create-volumes).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les sujets mentionnés dans cet article, consultez les pages suivantes :

- [Codage d’effacement dans Azure par Microsoft Research](https://www.microsoft.com/research/publication/erasure-coding-in-windows-azure-storage/)
- [Local Reconstruction Codes and Accelerating Parity Volumes](https://blogs.technet.microsoft.com/filecab/2016/09/06/volume-resiliency-and-efficiency-in-storage-spaces-direct/)
- [Volumes in the Storage Management API](https://blogs.technet.microsoft.com/filecab/2016/08/29/deep-dive-volumes-in-spaces-direct/)
- [Démonstration de l’efficacité du stockage (Microsoft Ignite 2016)](https://www.youtube.com/watch?v=-LK2ViRGbWs&t=36m55s)
- [Calculatrice de capacité pour les espaces de stockage direct (préversion)](https://aka.ms/s2dcalc)
