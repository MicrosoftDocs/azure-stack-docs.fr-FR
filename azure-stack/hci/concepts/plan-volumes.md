---
title: Planification de volumes dans Azure Stack HCI
description: Découvrez comment planifier des volumes de stockage dans Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 03/06/2020
ms.openlocfilehash: c6410e4f0d60138ce773f7f0abfae1a5c1850bd2
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "79095226"
---
# <a name="planning-volumes-in-storage-spaces-direct"></a>Planification de volumes dans les espaces de stockage direct

> S’applique à : Windows Server 2019

Cette rubrique fournit des conseils sur la façon de planifier des volumes dans les espaces de stockage direct pour répondre aux besoins de vos charges de travail en matière de performances et de capacités, notamment en ce qui concerne le choix de leur système de fichiers, de leur type de résilience et de leur taille.

## <a name="review-what-are-volumes"></a>Révision : Présentation des volumes

Vous utilisez des volumes pour placer les fichiers nécessaires à vos charges de travail, comme les fichiers VHD ou VHDX pour les machines virtuelles Hyper-V. Les volumes combinent les lecteurs du pool de stockage en vue d’introduire la tolérance de panne, l’extensibilité et les performances supérieures des espaces de stockage direct.

   >[!NOTE]
   > Dans toute la documentation relative aux espaces de stockage direct, nous utilisons le terme « volume » pour désigner à la fois le volume et le disque virtuel sous-jacent, notamment les fonctionnalités offertes par d’autres fonctions Windows intégrées comme les volumes partagés de cluster (CSV) et ReFS. Il n’est pas nécessaire de comprendre toutes les subtilités au niveau de l’implémentation pour planifier et déployer avec succès des espaces de stockage direct.

![what-are-volumes](media/plan-volumes/what-are-volumes.png)

Tous les volumes sont accessibles à tous les serveurs du cluster en même temps. Une fois créés, ils apparaissent dans **C:\ClusterStorage\\** sur tous les serveurs.

![csv-folder-screenshot](media/plan-volumes/csv-folder-screenshot.png)

## <a name="choosing-how-many-volumes-to-create"></a>Choix du nombre de volumes à créer

Nous vous recommandons de faire en sorte que le nombre de volumes soit un multiple du nombre de serveurs dans votre cluster. Par exemple, si vous avez 4 serveurs, vous bénéficierez de performances plus cohérentes avec 4 volumes en tout qu’avec 3 ou 5. Cela permet au cluster de répartir uniformément la « propriété » des volumes (un serveur gère l’orchestration des métadonnées pour chaque volume) entre les serveurs.

Nous vous recommandons de limiter le nombre total de volumes à 64 volumes par cluster pour Windows Server 2019.

## <a name="choosing-the-filesystem"></a>Choix du système de fichiers

Nous vous recommandons d’utiliser le nouveau [système ReFS](/en-us/windows-server/storage/refs/refs-overview) (Resilient File System) pour les espaces de stockage direct. ReFS est le premier système de fichiers conçu spécialement pour la virtualisation. Il offre de nombreux avantages, notamment une accélération considérable des performances et une protection intégrée contre la corruption des données. Il prend également en charge presque toutes les principales fonctionnalités de NTFS, notamment la déduplication des données dans Windows Server version 1709 ou ultérieure. Pour plus d’informations, consultez le [tableau de comparaison des fonctionnalités](/windows-server/storage/refs/refs-overview#feature-comparison) de ReFS.

Si votre charge de travail nécessite une fonctionnalité que ReFS ne prend pas encore en charge, vous pouvez utiliser NTFS à la place.

   > [!TIP]
   > Les volumes utilisant des systèmes de fichiers différents peuvent coexister au sein d’un même cluster.

## <a name="choosing-the-resiliency-type"></a>Choix du type de résilience

Les volumes dans les espaces de stockage direct fournissent la résilience pour se protéger contre les problèmes de matériel, comme les défaillances de disque ou de serveur, et pour activer la disponibilité continue au cours de la maintenance du serveur, comme les mises à jour logicielles.

   > [!NOTE]
   > Les types de résilience que vous pouvez choisir ne dépendent pas des types de lecteurs dont vous disposez.

### <a name="with-two-servers"></a>Avec deux serveurs

Avec deux serveurs dans le cluster, vous pouvez utiliser la mise en miroir double. Si vous exécutez Windows Server 2019, vous pouvez également utiliser la résilience imbriquée.

La mise en miroir double conserve deux copies de toutes les données, une copie sur les lecteurs de chaque serveur. L’efficacité du stockage est de 50% : pour écrire 1 To de données, vous devez disposer d’au moins 2 To de capacité de stockage physique dans le pool de stockage. La mise en miroir double peut tolérer en toute sécurité une défaillance matérielle à la fois (un serveur ou un lecteur).

![two-way-mirror](media/plan-volumes/two-way-mirror.png)

La résilience imbriquée (disponible uniquement sur Windows Server 2019) assure la résilience des données entre les serveurs avec mise en miroir double, puis ajoute la résilience au sein d’un serveur avec une mise en miroir double ou une parité avec accélération par miroir. L’imbrication assure la résilience des données même quand un serveur est en cours de redémarrage ou indisponible. L’efficacité du stockage est de 25 % pour la mise en miroir double imbriquée et d’environ 35-40 % pour la parité imbriquée avec accélération par miroir. La résilience imbriquée peut tolérer en toute sécurité deux défaillances matérielles à la fois (deux lecteurs, ou un serveur et un lecteur sur le serveur restant). En raison de cette résilience des données accrue, nous vous recommandons d’utiliser la résilience imbriquée sur les déploiements de production de clusters à deux serveurs si vous utilisez Windows Server 2019. Pour plus d’informations, consultez [Résilience imbriquée](/windows-server/storage/storage-spaces/nested-resiliency).

![Parité imbriquée avec accélération par miroir](media/plan-volumes/nested-mirror-accelerated-parity.png)

### <a name="with-three-servers"></a>Avec trois serveurs

Avec trois serveurs, il est préférable d’utiliser la mise en miroir triple pour améliorer la tolérance de panne et les performances. La mise en miroir triple conserve trois copies de toutes les données, une copie sur les lecteurs de chaque serveur. L’efficacité du stockage est de 33,3% : pour écrire 1 To de données, vous devez disposer d’au moins 3 To de capacité de stockage physique dans le pool de stockage. La mise en miroir triple peut tolérer en toute sécurité [au moins deux problèmes de matériel (lecteur ou serveur) à la fois](/windows-server/storage/storage-spaces/storage-spaces-fault-tolerance#examples). En cas d’indisponibilité de 2 nœuds, le pool de stockage perd le quorum puisque 2/3 des disques ne sont pas disponibles et que les disques virtuels ne sont pas accessibles. Toutefois, un nœud peut être en panne et un ou plusieurs disques sur un autre nœud peuvent échouer, mais les disques virtuels restent en ligne. Par exemple, si vous redémarrez un serveur et qu’un autre lecteur ou serveur échoue, toutes les données restent protégées et accessibles en continu.

![three-way-mirror](media/plan-volumes/three-way-mirror.png)

### <a name="with-four-or-more-servers"></a>Avec quatre serveurs ou plus

Avec quatre serveurs ou plus, vous pouvez choisir pour chaque volume d’utiliser la mise en miroir triple, la parité double (souvent appelée « codage d’effacement ») ou combiner les deux avec la parité avec accélération par miroir.

La parité double offre la même tolérance de panne que la mise en miroir triple, mais avec une meilleure efficacité de stockage. Avec quatre serveurs, l’efficacité du stockage est de 50,0% : pour stocker 2 To de données, vous devez disposer de 4 To de capacité de stockage physique dans le pool de stockage. L’efficacité du stockage passe à 66,7 % avec sept serveurs et peut atteindre 80,0 %. La contrepartie est que l’encodage de parité est plus gourmand en ressources système, ce qui peut limiter les performances.

![dual-parity](media/plan-volumes/dual-parity.png)

Le choix du type de résilience que vous utilisez dépend des besoins de votre charge de travail. Voici un tableau qui récapitule les charges de travail adaptées à chaque type de résilience, ainsi que les performances et l’efficacité du stockage pour chaque type de résilience.

| Type de résilience | Efficacité de la capacité | Vitesse | Charges de travail |
| ------------------- | ----------------------  | --------- | ------------- |
| **Miroir**         | ![Efficacité du stockage de 33 %](media/plan-volumes/3-way-mirror-storage-efficiency.png)<br>Miroir triple : 33 % <br>Miroir double : 50     |![Niveau de performance de 100 %](media/plan-volumes/three-way-mirror-perf.png)<br> Niveau de performance maximal  | Charges de travail virtualisées<br> Bases de données<br>Autres charges de travail haute performance |
| **Parité avec accélération par miroir** |![Efficacité du stockage d’environ 50 %](media/plan-volumes/mirror-accelerated-parity-storage-efficiency.png)<br> Dépend de la proportion de miroir et de parité | ![Niveau de performance d’environ 20 %](media/plan-volumes/mirror-accelerated-parity-perf.png)<br>Beaucoup plus lent que le miroir, mais jusqu’à deux fois plus rapide que la parité double<br> Idéal pour les lectures et écritures séquentielles volumineuses | Archivage et sauvegarde<br> Infrastructure de bureau virtualisée     |
| **Parité double**               | ![Efficacité du stockage d’environ 80 %](media/plan-volumes/dual-parity-storage-efficiency.png)<br>4 serveurs : 50 <br>16 serveurs : jusqu’à 80 % | ![Niveau de performance d’environ 10 %](media/plan-volumes/dual-parity-perf.png)<br>Latence d’E/S et utilisation du processeur les plus élevées sur les écritures<br> Idéal pour les lectures et écritures séquentielles volumineuses | Archivage et sauvegarde<br> Infrastructure de bureau virtualisée  |

#### <a name="when-performance-matters-most"></a>S’il faut privilégier le niveau de performance

Exécutez les charges de travail qui ont des exigences strictes en matière de latence ou qui nécessitent un grand nombre d’E/S par seconde (IOPS) aléatoires mixtes, comme les bases de données SQL Server ou les machines virtuelles Hyper-V sensibles aux performances, sur des volumes utilisant la mise en miroir pour optimiser les performances.

   >[!TIP]
   > La mise en miroir est plus rapide que tout autre type de résilience. Nous utilisons la mise en miroir pour la quasi totalité de nos exemples axés sur les performances.

#### <a name="when-capacity-matters-most"></a>S’il faut privilégier la capacité

Exécutez les charges de travail qui réalisent peu d’écritures, comme les entrepôts de données ou le stockage « à froid », sur des volumes utilisant la parité double pour optimiser l’efficacité du stockage. Certaines charges de travail, comme les serveurs de fichiers traditionnels, les infrastructures VDI (Virtual Desktop Infrastructure) ou d’autres qui ne génèrent pas beaucoup de trafic d’E/S aléatoire à dérive rapide et/ou qui ne nécessitent pas les meilleures performances peuvent également utiliser la parité double, à votre convenance. La parité augmente inévitablement l’utilisation du processeur et la latence d’E/S, en particulier sur les écritures, par rapport à la mise en miroir.

#### <a name="when-data-is-written-in-bulk"></a>Si les données sont écrites en bloc

Les charges de travail qui écrivent en grandes passes séquentielles, comme les cibles d’archivage ou de sauvegarde, ont une autre option : un même volume peut combiner la mise en miroir et la parité double. Les écritures sont dans un premier temps hébergées dans la partie miroir, puis progressivement déplacées dans la partie parité. Cela permet d’accélérer l’ingestion et de réduire l’utilisation des ressources lors de l’arrivée d’écritures volumineuses puisque l’encodage de parité, qui nécessite beaucoup de ressources, peut se produire sur une durée plus longue. Lors du dimensionnement, n’oubliez pas que la partie miroir doit être de taille suffisante pour accueillir les écritures simultanées (par exemple, lors des sauvegardes quotidiennes). Ainsi, si vous ingérez 100 Go une fois par jour, prévoyez une mise en miroir de 150 Go à 200 Go et une parité double pour le reste.

L’efficacité du stockage résultante varie en fonction des proportions que vous choisissez. Pour obtenir des exemples, consultez [cette démonstration](https://www.youtube.com/watch?v=-LK2ViRGbWs&t=36m55s).

   > [!TIP]
   > Si vous observez une baisse soudaine des performances d’écriture au cours de l’ingestion des données, cela peut indiquer que la partie miroir n’est pas assez grande ou que la parité avec accélération par miroir n’est pas adaptée à votre cas d’usage. Par exemple, si les performances d’écriture baissent de 400 Mo/s à 40 Mo/s, envisagez d’étendre la partie miroir ou de passer à un miroir triple.

### <a name="about-deployments-with-nvme-ssd-and-hdd"></a>À propos des déploiements avec NVMe, SSD et HDD

Dans les déploiements avec deux types de lecteurs, les lecteurs plus rapides assurent la mise en cache tandis que les plus lents fournissent la capacité. Cela se fait automatiquement. Pour plus d’informations, consultez [Présentation du cache dans les espaces de stockage direct](/windows-server/storage/storage-spaces/understand-the-cache). Dans ce type de déploiement, tous les volumes résident finalement sur le même type de lecteur : les lecteurs de capacité.

Dans les déploiements utilisant les trois types de lecteurs, seuls les lecteurs les plus rapides (NVMe) assurent la mise en cache, les deux autres types (SSD et HDD) devant fournir la capacité. Pour chaque volume, vous pouvez choisir s’il réside entièrement sur le niveau SSD, entièrement sur le niveau HDD ou sur les deux.

   > [!IMPORTANT]
   > Nous vous recommandons d’utiliser le niveau SSD pour placer vos charges de travail les plus sensibles aux performances dans une configuration 100 % flash.

## <a name="choosing-the-size-of-volumes"></a>Choix de la taille des volumes

Nous vous recommandons de limiter la taille de chaque volume à 64 To dans Windows Server 2019.

   > [!TIP]
   > Si vous utilisez une solution de sauvegarde qui repose sur le service VSS et le fournisseur de logiciel Volsnap, comme c’est souvent le cas avec les charges de travail de serveur de fichiers, la limitation de la taille du volume à 10 To permet d’améliorer les performances et la fiabilité. Les solutions de sauvegarde qui utilisent l’API RCT Hyper-V plus récente et/ou le clonage de bloc ReFS et/ou les API de sauvegarde SQL natives fonctionnent bien jusqu’à 32 To et au-delà.

### <a name="footprint"></a>Empreinte

La taille d’un volume fait référence à sa capacité utilisable, c’est-à-dire la quantité de données qu’il peut stocker. Elle est spécifiée par le paramètre **-Size** de l’applet de commande **New-Volume** et apparaît dans la propriété **Size** quand vous exécutez l’applet de commande **Get-Volume**.

La taille est différente de l’*empreinte* du volume, à savoir la capacité de stockage physique totale qu’il occupe sur le pool de stockage. L’empreinte d’un volume dépend de son type de résilience. Par exemple, les volumes qui utilisent la mise en miroir triple ont une empreinte trois fois plus grande.

Les empreintes de vos volumes doivent tenir dans le pool de stockage.

![size-versus-footprint](media/plan-volumes/size-versus-footprint.png)

### <a name="reserve-capacity"></a>Capacité de réserve

Le fait de conserver une certaine capacité non allouée dans le pool de stockage donne aux volumes un espace de réparation « sur place » en cas de panne des lecteurs, ce qui améliore la sécurité des données et les performances. Si la capacité est suffisante, une réparation parallèle, immédiate et sur place permet de restaurer les volumes dans un état de résilience totale avant même le remplacement des lecteurs défaillants. Cela se fait automatiquement.

Nous vous recommandons de réserver l’équivalent d’un lecteur de capacité par serveur, jusqu’à 4 lecteurs. Vous pouvez réserver davantage de capacité, à votre convenance, mais cette recommandation minimale vous donne l’assurance qu’une réparation parallèle, immédiate et sur place pourra être effectuée si un lecteur quelconque tombe en panne.

![reserve](media/plan-volumes/reserve.png)

Par exemple, si vous disposez de 2 serveurs et que vous utilisez des lecteurs de capacité de 1 To, mettez 2 x 1 = 2 To du pool en réserve. Si vous disposez de 3 serveurs et que vous utilisez des lecteurs de capacité de 1 To, mettez 3 x 1 = 3 To en réserve. Si vous disposez de 4 serveurs ou plus et que vous utilisez des lecteurs de capacité de 1 To, mettez 4 x 1 = 4 To en réserve.

   >[!NOTE]
   > Dans les clusters utilisant les trois types de lecteurs (NVMe + SSD + HDD), nous vous recommandons de réserver l’équivalent d’un SSD plus un HDD par serveur, jusqu’à 4 lecteurs de chaque type.

## <a name="example-capacity-planning"></a>Exemple : planification de la capacité

Prenons l’exemple d’un cluster à quatre serveurs. Chaque serveur a des lecteurs de cache, plus seize lecteurs de capacité de 2 To.

```
4 servers x 16 drives each x 2 TB each = 128 TB
```

Avec 128 To dans le pool de stockage, nous réservons quatre lecteurs, soit 8 To, pour permettre les réparations sur place sans devoir remplacer immédiatement les lecteurs défaillants. Nous disposons donc de 120 To de capacité de stockage physique dans le pool pour créer nos volumes.

```
128 TB – (4 x 2 TB) = 120 TB
```

Prenons un exemple. Notre déploiement doit héberger un certain nombre de machines virtuelles Hyper-V très actives, mais nous avons également beaucoup de stockage à froid (anciens fichiers et anciennes sauvegardes que nous devons conserver). Étant donné que nous avons quatre serveurs, nous allons créer quatre volumes.

Nous plaçons les machines virtuelles sur les deux premiers volumes, *Volume1* et *Volume2*. Nous choisissons ReFS comme système de fichiers (pour accélérer la création et disposer de points de contrôle) et une mise en miroir triple pour la résilience afin de maximiser les performances. Nous plaçons le stockage à froid sur les deux autres volumes, *Volume 3* et *Volume 4*. Nous choisissons NTFS comme système de fichiers (pour la déduplication des données) et la parité double pour la résilience afin de maximiser la capacité.

Tous les volumes ne doivent pas nécessairement être de la même taille, mais pour simplifier, nous leur attribuons une taille identique de 12 To.

*Volume1* et *Volume2* occupent chacun 12 To x 33,3 % d’efficacité = 36 To de capacité de stockage physique.

*Volume3* et *Volume4* occupent chacun 12 To x 50,0 % d’efficacité = 24 To de capacité de stockage physique.

```
36 TB + 36 TB + 24 TB + 24 TB = 120 TB
```

Nos quatre volumes correspondent exactement à la capacité de stockage physique disponible dans notre pool. Parfait !

![exemple](media/plan-volumes/example.png)

   >[!TIP]
   > Vous n’êtes pas obligé de créer tous vos volumes en une seule fois. Vous avez toujours la possibilité d’étendre les volumes ou d’en créer d’autres par la suite.

Par souci de simplicité, cet exemple utilise des unités décimales (base 10), ce qui signifie que 1 To = 1 000 000 000 000 octets. Toutefois, les quantités de stockage dans Windows sont exprimées en unités binaires (base 2). Par exemple, tout disque de 2 To indique une capacité de 1,82 Tio dans Windows. De même, notre pool de stockage de 128 To indique une capacité de 116,41 Tio. Ceci est normal.

## <a name="usage"></a>Usage

Consultez [Création de volumes dans Azure Stack HCI](../manage/create-volumes.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez également :

- [Vue d’ensemble d’Azure Stack HCI](../overview.md)
- [Choix des lecteurs pour les espaces de stockage direct](choose-drives.md)
- [Tolérance de panne et efficacité du stockage](fault-tolerance.md)