---
title: Vue d’ensemble des clusters étendus
description: En savoir plus sur les clusters étendus
author: v-dasis
ms.topic: how-to
ms.date: 12/04/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 13ec96862c6aaf494c96f9e3cb3ff6312bc9ab97
ms.sourcegitcommit: 0efffe1d04a54062a26d5c6ce31a417f511b9dbf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2020
ms.locfileid: "96611621"
---
# <a name="stretched-clusters-overview"></a>Vue d’ensemble des clusters étendus

> S’applique à Azure Stack HCI, version v20H2

Une solution de clusters étendus Azure Stack HCI pour la reprise d’activité après sinistre prend en charge le basculement automatique pour restaurer rapidement la production, sans aucune intervention manuelle. Le réplica de stockage assure la réplication des volumes entre les sites pour la reprise d’activité, tous les serveurs restant synchronisés.

Le réplica de stockage prend en charge les réplications synchrones et asynchrones :

- La réplication synchrone met en miroir les données entre des sites situés dans un réseau à faible latence avec des volumes cohérents en cas de plantage, ce qui permet d’éviter toute perte de données au niveau du système de fichiers durant une défaillance.
- La réplication asynchrone assure la mise en miroir des données entre des sites situés hors des zones métropolitaines sur des liaisons de réseau à latence élevée, mais sans garantie que les deux sites aient des copies identiques des données au moment de la panne.

>[!NOTE]
> La réplication asynchrone est un basculement manuel, ce qui vous oblige à la mettre en ligne. Pour plus d’informations, consultez [Réplication asynchrone](https://docs.microsoft.com/windows-server/storage/storage-replica/storage-replica-overview#asynchronous-replication).

Il existe deux types de clusters étendus : actif/passif et actif/actif. Dans une réplication de site en mode actif/passif, vous indiquez un site et une direction pour la réplication. Dans une réplication en mode actif/actif, la réplication peut être bidirectionnelle entre les deux sites. Cet article traite uniquement du mode actif/passif.

Pour faire simple, un site *actif* est doté de ressources et fournit des rôles et des charges de travail auxquelles les clients se connectent. Un site *passif* ne fournit aucun rôle ni aucune charge de travail aux clients et attend un basculement du site actif pour assurer la reprise d’activité.

Les sites peuvent se trouver dans des régions différentes, des villes différentes, des étages différents ou des pièces différentes. Les clusters étendus utilisant deux sites assurent la reprise d’activité et la continuité de l’activité en cas de panne ou de défaillance d’un site.

Prenez quelques minutes pour visionner la vidéo sur le clustering étendu avec Azure Stack HCI :
> [!VIDEO https://www.youtube.com/embed/rYnZL1wMiqU]

## <a name="active-passive-stretched-cluster"></a>Cluster étendu actif-passif

Dans le diagramme suivant, le site 1 est le site actif et la réplication est unidirectionnelle vers le site 2.

:::image type="content" source="media/stretched-cluster/active-passive-stretched-cluster.png" alt-text="Scénario de cluster étendu actif/passif"  lightbox="media/stretched-cluster/active-passive-stretched-cluster.png":::

## <a name="active-active-stretched-cluster"></a>Cluster étendu actif/actif

Dans le diagramme suivant, le site 1 et le site 2 sont actifs et la réplication est bidirectionnelle entre les deux sites.

:::image type="content" source="media/stretched-cluster/active-active-stretched-cluster.png" alt-text="Scénario de cluster étendu actif/actif" lightbox="media/stretched-cluster/active-active-stretched-cluster.png":::

## <a name="next-steps"></a>Étapes suivantes

- Découvrez plus en détail les réplicas de stockage. Consultez [Vue d’ensemble des réplicas de stockage](https://docs.microsoft.com/windows-server/storage/storage-replica/storage-replica-overview).
- Découvrez le matériel et les autres exigences des clusters étendus. Voir [Configuration requise](system-requirements.md).
- Découvrez comment déployer un cluster étendu à l’aide de Windows Admin Center. Consultez [Créer un cluster à l’aide de Windows Admin Center](../deploy/create-cluster.md).
- Découvrez comment déployer un cluster étendu à l’aide de PowerShell. Consultez [Créer un cluster à l’aide de PowerShell](../deploy/create-cluster-powershell.md).
- Découvrez comment créer des volumes et configurer la réplication pour des clusters étendus. Consultez [Créer des volumes et configurer la réplication pour des clusters étendus](../manage/create-stretched-volumes.md).
