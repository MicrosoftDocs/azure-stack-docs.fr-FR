---
title: Planification de la capacité pour Event Hubs sur Azure Stack Hub
description: Découvrez comment planifier la capacité pour le fournisseur de ressources Event Hubs sur Azure Stack Hub.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/09/2019
ms.reviewer: jfggdl
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: 06b0dabf4c7fc54d5c886e7ecc777f8a754e3ef0
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86490114"
---
# <a name="how-to-do-capacity-planning-for-event-hubs-on-azure-stack-hub"></a>Planification de la capacité pour Event Hubs sur Azure Stack Hub

[!INCLUDE [preview-banner](../includes/event-hubs-preview.md)]

En tant qu’opérateur, vous gérez votre capacité Azure Stack Hub en utilisant des [quotas](azure-stack-quota-types.md) sur les ressources. Vous contrôlez la consommation des ressources Event Hubs en définissant des quotas sur le nombre maximal de cœurs utilisés par les clusters Event Hubs. Les clusters Event Hubs sont créés par les utilisateurs lorsqu’ils déploient une ressource Event Hubs. Il existe également différentes exigences en matière de consommation des ressources pour le fournisseur de ressources, qui sont traitées dans cet article.

## <a name="cluster-resource-consumption"></a>Consommation des ressources de cluster

Pour comprendre la consommation de capacité des déploiements Event Hubs, il est important de noter que les utilisateurs créent des clusters Event Hubs basés sur des unités de capacité. Ils ne spécifient pas de nombre de cœurs de processeur lors de la création d’un cluster Event Hubs. Toutefois, chaque unité de capacité correspond directement à un nombre spécifique de cœurs consommés. 

Vos utilisateurs devront créer des clusters Event Hubs avec des unités de capacité qui répondent aux besoins de leur entreprise. Pour vous aider à choisir la configuration de quota, le tableau suivant présente les éléments suivants :
- Nombre total de cœurs utilisés par un cluster Event Hubs de 1 unité de capacité.
- Capacité approximative requise pour la consommation d’autres ressources, notamment le stockage de machine virtuelle, la mémoire et les comptes de stockage.

| | Type de machine virtuelle | Nœuds de cluster | Cœurs par machine virtuelle/nœud | Nombre total de cœurs | Stockage de machine virtuelle | Mémoire | Comptes de stockage | Adresses IP publiques |
|-|---------|-------|-------------------|-------------|------------|--------|------------------|---|
| **Cluster Event Hubs de 1 unité de capacité** | [D11_V2](../user/azure-stack-vm-sizes.md#mo-dv2) | 5 | 2 | 10 | 500 Go | 70 Gio | 4 | 1 |

Tous les clusters Event Hubs utilisent un type de machine virtuelle [D11_V2](../user/azure-stack-vm-sizes.md#mo-dv2) pour leurs nœuds. Un type de machine virtuelle D11_V2 se compose de 2 cœurs. Ainsi, un cluster Event Hubs de 1 unité de capacité utilise 5 machines virtuelles D11_V2, ce qui se traduit par 10 cœurs utilisés. Pour déterminer le nombre de cœurs à configurer pour un quota, utilisez un multiple du nombre total de cœurs utilisés par 1 unité de capacité. Ce calcul reflète le nombre maximal d’unités de capacité que vous autorisez les utilisateurs à utiliser lors de la création de clusters Event Hubs. Par exemple, pour configurer un quota qui permet aux utilisateurs de créer un cluster avec 2 unités de capacité, définissez votre quota sur 20 cœurs.

> [!NOTE]
> **Préversion publique uniquement** La version disponible d’Event Hubs sur Azure Stack Hub prend uniquement en charge la création de clusters de 1 unité de capacité. La version en disponibilité générale d’Event Hubs inclut la prise en charge de différentes options de configuration d’unités de capacité.

## <a name="resource-provider-resource-consumption"></a>Consommation des ressources du fournisseur de ressources  

La consommation des ressources du fournisseur de ressources Event Hubs est constante et indépendante du nombre ou de la taille des clusters créés par les utilisateurs. Le tableau suivant montre l’utilisation des cœurs par le fournisseur de ressources Event Hubs sur Azure Stack Hub et la consommation approximative des ressources par d’autres ressources. Le fournisseur de ressources Event Hubs utilise un type de machine virtuelle [D2_V2](../user/azure-stack-vm-sizes.md#dv2-series) pour son déploiement.

|                                  | Type de machine virtuelle | Nœuds de cluster | Cœurs | Stockage de machine virtuelle | Mémoire | Comptes de stockage | Adresses IP publiques |
|----------------------------------|---------|---------------|-------|------------|--------|------------------|------------|
| **Fournisseur de ressources Event Hubs** | D2_V2   | 3     | 6     | 300 Gio | 21 Gio | 2 | 1 |

> [!IMPORTANT]
> La consommation du fournisseur de ressources n’est pas contrôlée par les quotas. Vous n’avez pas besoin de prendre en compte les cœurs utilisés par le fournisseur de ressources dans vos configurations de quota. Les fournisseurs de ressources sont installés à l’aide d’un abonnement d’administrateur. L’abonnement n’impose pas de limites de consommation de ressources sur les opérateurs lors de l’installation des fournisseurs de ressources requis.

## <a name="total-resource-consumption"></a>Consommation totale des ressources

La capacité totale consommée par le service Event Hubs comprend la consommation des ressources par le fournisseur de ressources et la consommation par les clusters créés par l’utilisateur.

Le tableau suivant indique la consommation totale d’Event Hubs sous différentes configurations, gérées ou non par quota. Ces nombres sont basés sur les consommations du fournisseur de ressources et des clusters Event Hubs présentées ci-dessus. Vous pouvez facilement calculer votre utilisation Azure Stack Hub totale pour d’autres tailles de déploiement à l’aide de ces exemples.

|                                      | Cœurs | Stockage de machine virtuelle | Mémoire  | Comptes de stockage | Stockage total\* | Adresses IP publiques\*\* |
|--------------------------------------|-------|------------|---------|------------------|---------------|------------|
| **Cluster de 1 unité de capacité + fournisseur de ressources** | 16    | 800 Gio    | 91 Gio  | 6                | variable    | 2 |
| **Cluster de 2 unités de capacité + fournisseur de ressources** | 26    | 1,3 To     | 161 Gio | 10               | variable    | 2 |
| **Cluster de 4 unités de capacité + fournisseur de ressources** | 46    | 2,3 To     | 301 Gio | 18               | variable    | 2 |

\* Le débit du bloc de données d’entrée (message/événement) et la conservation des messages sont deux facteurs importants qui contribuent au stockage utilisé par les clusters Event Hubs. Par exemple, si la conservation des messages est définie sur 7 jours lors de la création d’un hub d’événements et que les messages sont ingérés à un débit de 1 Mo/s, le stockage approximatif utilisé est de 604 Go (1 Mo x 60 secondes x 60 minutes x 24 heures x 7 jours). Si les messages sont envoyés à un débit de 20 Mo/s avec une conservation de 7 jours, la consommation de stockage approximative est de 12 To. Veillez à prendre en compte le débit des données d’entrée et la durée de conservation pour comprendre pleinement la consommation de capacité de stockage.

\*\*Les adresses IP publiques sont consommées à partir du [quota réseau fourni dans le cadre de votre abonnement](azure-stack-quota-types.md#network-quota-types).

## <a name="next-steps"></a>Étapes suivantes

Avant de commencer le processus d’installation, passez en revue [Prérequis pour l’installation d’Event Hubs sur Azure Stack Hub](event-hubs-rp-prerequisites.md).
