---
title: Vue d'ensemble de l'infrastructure de stockage Azure Stack Hub
titleSuffix: Azure Stack
description: Découvrez comment gérer l’infrastructure de stockage pour Azure Stack Hub.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: inhenkel
ms.lastreviewed: 5/5/2020
ms.reviewer: jiaha
ms.custom: contperf-fy20q4
ms.openlocfilehash: 8633e31ac60dfb091997657b02b633d24d39b29d
ms.sourcegitcommit: e13f27291bab236aac5d8b05401056961e9cc1e9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/19/2020
ms.locfileid: "97697599"
---
# <a name="azure-stack-hub-storage-infrastructure-overview"></a>Vue d'ensemble de l'infrastructure de stockage Azure Stack Hub

Cet article présente les concepts de l’infrastructure de stockage Azure Stack Hub. Il fournit des informations sur les lecteurs et les volumes ainsi que la façon dont ils sont utilisés dans Azure Stack Hub.

## <a name="drives"></a>Lecteurs

### <a name="drive-types"></a>Types de lecteurs

Les partenaires système intégrés Azure Stack Hub offrent de nombreuses variations de la solution, notamment un large éventail en matière de souplesse du stockage. Vous pouvez sélectionner jusqu’à **deux** types de lecteurs parmi les trois types pris en charge :

1. NVMe (Non-Volatile Memory Express)
1. Disque SSD (Solid-State Drive) SATA/SAS
1. Lecteur de disque dur (HDD).

### <a name="performance-vs-capacity"></a>Performances et capacité

Azure Stack Hub utilise des espaces de stockage direct (S2D) avec le clustering de basculement Windows Server. Cette combinaison offre un service de stockage performant, scalable et résilient.

Les déploiements Azure Stack peuvent optimiser les performances de stockage ou équilibrer les performances et la capacité.

Les espaces de stockage direct utilisent un cache pour optimiser les performances de stockage.

### <a name="how-drive-types-are-used"></a>Utilisation des types de lecteurs

Quand une appliance Azure Stack Hub dispose d’un seul type de lecteur, tous les lecteurs sont utilisés pour la capacité.

Si elle comprend deux types de lecteurs, les espaces de stockage direct utilisent automatiquement tous les lecteurs du type le « plus rapide » (NVMe &gt; SSD &gt; HDD) pour la mise en cache. Les lecteurs restants sont utilisés pour la capacité.

### <a name="all-flash-or-hybrid"></a>Tout flash ou hybride

Les lecteurs peuvent être regroupés dans un déploiement « tout flash » ou « hybride ».

L’objectif des déploiements 100 % flash est d’optimiser les performances de stockage. Ils n’incluent pas de lecteurs de disques durs rotatifs.

![Diagramme montrant un regroupement de déploiement 100 % flash.](media/azure-stack-storage-infrastructure-overview/image1.png)


Les déploiements hybrides visent à équilibrer les performances et la capacité ou à optimiser la capacité. Ils incluent des lecteurs de disques durs rotatifs.

![Diagramme montrant un regroupement de déploiement hybride.](media/azure-stack-storage-infrastructure-overview/image2.png)

### <a name="caching-behavior"></a>Comportement de mise en cache

Le comportement du cache est déterminé automatiquement selon le ou les types de lecteurs. Lors de la mise en cache pour les disques SSD (par exemple, NVMe mettant en cache pour des disques SSD), seules les écritures sont mises en cache. Cela réduit l’usure des lecteurs dédiés à la capacité, en diminuant le trafic cumulatif vers les lecteurs de capacité et prolongeant ainsi leur durée de vie.

Les lectures ne sont pas mises en cache. En effet, elles n’affectent pas de manière significative la durée de vie du déploiement flash et les disques SSD offrent une faible latence de lecture.

Lors de la mise en cache pour les lecteurs de disque dur (par exemple, disques SSD mettant en cache pour des HDD), les lectures et les écritures sont mises en cache pour fournir une latence similaire à la technologie flash (souvent /~10 fois supérieure) pour les deux.

![Diagramme montrant le comportement de mise en cache du stockage Azure Stack Hub.](media/azure-stack-storage-infrastructure-overview/image3.svg)

Pour la configuration de stockage disponible, vous pouvez consulter les spécifications détaillées des partenaires OEM Azure Stack Hub OEM (https://azure.microsoft.com/overview/azure-stack/partners/) ).

> [!NOTE]
> L’appliance Azure Stack Hub peut être remise dans un déploiement hybride, avec des lecteurs HDD et SSD (ou NVMe). Mais les lecteurs de type plus rapide seraient utilisés en tant que lecteurs de cache, et tous les lecteurs restants seraient utilisés en tant que lecteurs de capacité sous forme de pool. Les données de locataire (blobs, tables, files d’attente et disques) seraient placées sur les lecteurs de capacité. Le provisionnement de disques Premium ou la sélection du type de compte de stockage Premium ne garantit pas que les objets seront attribués à des lecteurs SSD ou NVMe.

## <a name="volumes"></a>Volumes

Le *service de stockage* partitionne le stockage disponible en volumes distincts qui sont alloués pour stocker des données système et de locataire. Les volumes combinent les lecteurs du pool de stockage en vue de fournir la tolérance de panne, l’extensibilité et les performances supérieures des espaces de stockage direct.

![Diagramme montrant des partitions de service de stockage Azure Stack Hub.](media/azure-stack-storage-infrastructure-overview/image4.svg)

### <a name="volume-types"></a>Types de volume

Il existe trois types de volumes créés sur le pool de stockage Azure Stack Hub :

1. Les volumes d’**infrastructure** hébergent les fichiers utilisés par les services de base et les machines virtuelles d’infrastructure Azure Stack Hub.
1. Les volumes de **machine virtuelle temporaire** hébergent les disques temporaires attachés aux machines virtuelles de locataire et ces données sont stockées sur ces disques.
1. Les volumes de **magasin d’objets** hébergent les données de locataire pour les objets blob, tables, files d’attente et disques de machine virtuelle.

### <a name="volumes-in-a-multi-node-deployment"></a>Volumes dans un déploiement sur plusieurs nœuds

Dans un déploiement sur plusieurs nœuds, il existe trois volumes d’infrastructure.

Le nombre de volumes de machine virtuelle temporaire et de magasin d’objets est égal au nombre de nœuds du déploiement Azure Stack Hub :

- Un déploiement à quatre nœuds comprend quatre volumes Machine virtuelle temporaire égaux et quatre volumes Magasin d’objets égaux.

- Si vous ajoutez un nouveau nœud au cluster, un nouveau volume des deux types est créé.

- Le nombre de volumes reste identique, même si un nœud est défectueux ou supprimé.

> [!NOTE]
> Si vous utilisez le [kit de développement Azure Stack (ASDK)](../asdk/index.yml), il n’y a qu’un seul volume avec plusieurs [partages](azure-stack-manage-storage-shares.md).

### <a name="fault-tolerance-and-mirroring"></a>Tolérance de panne et mise en miroir

Les volumes dans les espaces de stockage direct offrent une résilience pour se prémunir des problèmes liés au matériel, comme les défaillances de lecteur ou de serveur. Ils permettent d’avoir une disponibilité continue pendant toutes les opérations de maintenance du serveur, comme les mises à jour logicielles.

La mise en miroir fournit la tolérance de panne en conservant plusieurs copies de toutes les données. La façon dont les données sont agrégées par bande et placées n’est pas négligeable, mais toutes les données stockées à l’aide de la mise en miroir sont écrites, dans leur intégralité, plusieurs fois. Chaque copie est écrite sur différents supports physiques (lecteurs différents sur différents serveurs) qui sont supposés échouer de manière indépendante. 

Le déploiement Azure Stack Hub utilise la mise en miroir triple pour garantir la résilience des données. La mise en miroir triple peut tolérer en toute sécurité au moins deux problèmes de matériel (lecteur ou serveur) à la fois. Par exemple, si vous redémarrez un serveur et qu’un autre lecteur ou serveur échoue, toutes les données restent protégées et accessibles en continu.

Trois copies des données de locataire sont écrites sur différents serveurs, où elles arrivent dans le cache :

![Diagramme montrant comment trois copies des données de locataire sont écrites sur différents serveurs.](media/azure-stack-storage-infrastructure-overview/image5.png)

## <a name="next-step"></a>Étape suivante

[Gérer la capacité de stockage](azure-stack-manage-storage-shares.md) 
