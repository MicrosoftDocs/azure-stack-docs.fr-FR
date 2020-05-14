---
title: Vue d'ensemble de l'infrastructure de stockage Azure Stack Hub
titleSuffix: Azure Stack
description: Découvrez comment gérer l’infrastructure de stockage pour Azure Stack Hub.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 5/11/2020
ms.author: inhenkel
ms.lastreviewed: 5/5/2020
ms.reviewer: jiaha
ms.custom: contperfq4
ms.openlocfilehash: 0712caec89d3a6e2203ca780b4877b330953c61c
ms.sourcegitcommit: 4a8d7203fd06aeb2c3026d31ffec9d4fbd403613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83202487"
---
# <a name="azure-stack-hub-storage-infrastructure-overview"></a>Vue d'ensemble de l'infrastructure de stockage Azure Stack Hub

Cet article propose une vue d’ensemble de l’infrastructure de stockage Azure Stack Hub.

## <a name="understand-drives-and-volumes"></a>Comprendre les lecteurs et les volumes

### <a name="drives"></a>Lecteurs

Alimenté par le logiciel Windows Server, Azure Stack Hub définit des capacités de stockage à l’aide d’une combinaison d’espaces de stockage direct (S2D), et Clustering de basculement Windows Server. Cette combinaison offre un service de stockage performant, scalable et résilient.

Les partenaires système intégrés Azure Stack Hub offrent de nombreuses variations de la solution, notamment un large éventail en matière de souplesse du stockage. Vous pouvez sélectionner jusqu’à deux types de lecteurs parmi les trois types pris en charge : NVMe (mémoire expresse non volatile), SATA/SAS SSD (disque sans pièce mobile), HDD (disque dur). 

Les espaces de stockage direct disposent d’un cache pour optimiser les performances de stockage. Dans une appliance Azure Stack Hub disposant d’un seul type de lecteur (à savoir, NVMe ou SSD), tous les lecteurs sont utilisés pour la capacité. Si elle comprend deux types de lecteurs, les espaces de stockage direct utilisent automatiquement tous les lecteurs du type le « plus rapide » (NVMe &gt; SSD &gt; HDD) pour la mise en cache. Les lecteurs restants sont utilisés pour la capacité. Les lecteurs peuvent être regroupés dans un déploiement « 100 % flash » ou « hybride » :

![Infrastructure de stockage Azure Stack Hub](media/azure-stack-storage-infrastructure-overview/image1.png)

L’objectif des déploiements 100 % flash est d’optimiser les performances de stockage. Ils n’incluent pas de lecteurs de disques durs rotatifs.

![Infrastructure de stockage Azure Stack Hub](media/azure-stack-storage-infrastructure-overview/image2.png)

Les déploiements hybrides visent à équilibrer les performances et la capacité ou à optimiser la capacité. Ils incluent des lecteurs de disques durs rotatifs.

Le comportement du cache est déterminé automatiquement selon le ou les types de lecteurs pour lesquels s’effectue la mise en cache. Lors de la mise en cache pour les disques SSD (par exemple, NVMe mettant en cache pour des disques SSD), seules les écritures sont mises en cache. Cela réduit l’usure des lecteurs dédiés à la capacité, en diminuant le trafic cumulatif vers les lecteurs de capacité et prolongeant ainsi leur durée de vie. En parallèle, les lectures ne sont pas mises en cache. En effet, elles n’affectent pas de manière significative la durée de vie du déploiement flash et les disques SSD offrent une faible latence de lecture. Lors de la mise en cache pour les lecteurs de disque dur (par exemple, disques SSD mettant en cache pour des HDD), les lectures et les écritures sont mises en cache pour fournir une latence similaire à la technologie flash (souvent /~10 fois supérieure) pour les deux.

![Infrastructure de stockage Azure Stack Hub](media/azure-stack-storage-infrastructure-overview/image3.png)

Pour la configuration de stockage disponible, vous pouvez consulter les spécifications détaillées des partenaires OEM Azure Stack Hub OEM (https://azure.microsoft.com/overview/azure-stack/partners/)).

> [!Note]  
> L’appareil Azure Stack Hub peut être remis dans un déploiement hybride, avec des lecteurs HDD et SSD (ou NVMe). Mais les lecteurs de type plus rapide seraient utilisés en tant que lecteurs de cache, et tous les lecteurs restants seraient utilisés en tant que lecteurs de capacité sous forme de pool. Les données de locataire (blobs, tables, files d’attente et disques) seraient placées sur les lecteurs de capacité. Le provisionnement de disques Premium ou la sélection du type de compte de stockage Premium ne garantit pas que les objets seront attribués à des lecteurs SSD ou NVMe.

### <a name="volumes"></a>Volumes

Le *service de stockage* partitionne le stockage disponible en volumes distincts qui sont alloués pour stocker des données système et de locataire. Les volumes combinent les lecteurs du pool de stockage en vue de fournir la tolérance de panne, l’extensibilité et les performances supérieures des espaces de stockage direct.

![Infrastructure de stockage Azure Stack Hub](media/azure-stack-storage-infrastructure-overview/image4.png)

Il existe trois types de volumes créés sur le pool de stockage Azure Stack Hub :

- Infrastructure : héberge les fichiers utilisés par les services de base et les machines virtuelles d’infrastructure Azure Stack Hub.

- Machine virtuelle temporaire : héberge les disques temporaires attachés aux machines virtuelles de locataire et ces données sont stockées sur ces disques.

- Magasin d’objets : héberge les données de locataire pour les blobs, tables, files d’attente et disques de machine virtuelle.

Dans un déploiement à plusieurs nœuds, vous devriez voir trois volumes d’infrastructure, tandis que le nombre de volumes Machine virtuelle temporaire et de volumes Magasin d’objets est égal au nombre de nœuds dans le déploiement d’Azure Stack Hub :

- Un déploiement à quatre nœuds comprend quatre volumes Machine virtuelle temporaire égaux et quatre volumes Magasin d’objets égaux.

- Si vous ajoutez un nouveau nœud au cluster, un nouveau volume des deux types est créé.

- Le nombre de volumes reste identique, même si un nœud est défectueux ou supprimé.

- Si vous utilisez le Kit de développement Azure Stack, il n’y a qu’un seul volume avec plusieurs partages.

Les volumes dans les espaces de stockage direct offrent une résilience pour se prémunir des problèmes liés au matériel, comme les défaillances de lecteur ou de serveur. Ils permettent également d’avoir une disponibilité continue pendant toues les opérations de maintenance du serveur, comme les mises à jour de logiciel. Le déploiement Azure Stack Hub utilise la mise en miroir triple pour garantir la résilience des données. Trois copies des données de locataire sont écrites sur différents serveurs, où elles arrivent dans le cache :

![Infrastructure de stockage Azure Stack Hub](media/azure-stack-storage-infrastructure-overview/image5.png)

La mise en miroir fournit la tolérance de panne en conservant plusieurs copies de toutes les données. La façon dont les données sont agrégées par bande et placées n’est pas négligeable, mais il est vrai de dire que toutes les données stockées à l’aide de la mise en miroir sont écrites, dans leur intégralité, plusieurs fois. Chaque copie est écrite sur différents supports physiques (lecteurs différents sur différents serveurs) qui sont supposés échouer de manière indépendante. La mise en miroir triple peut tolérer en toute sécurité au moins deux problèmes de matériel (lecteur ou serveur) à la fois. Par exemple, si vous redémarrez un serveur et qu’un autre lecteur ou serveur échoue, toutes les données restent protégées et accessibles en continu.

## <a name="next-step"></a>Étape suivante

[Gérer la capacité de stockage](azure-stack-manage-storage-shares.md) 
