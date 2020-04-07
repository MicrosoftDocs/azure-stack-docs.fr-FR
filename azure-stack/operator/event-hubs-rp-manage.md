---
title: Guide pratique pour gérer Event Hubs sur Azure Stack Hub
description: Découvrez comment gérer le fournisseur de ressources Event Hubs sur Azure Stack Hub.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/09/2019
ms.reviewer: jfggdl
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: e9f500da4cab68688a6dd33374cfbc1cc166828d
ms.sourcegitcommit: dd53af1b0fc2390de162d41e3d59545d1baad1a7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80424637"
---
# <a name="how-to-manage-event-hubs-on-azure-stack-hub"></a>Guide pratique pour gérer Event Hubs sur Azure Stack Hub

[!INCLUDE [preview-banner](../includes/event-hubs-preview.md)]

L’expérience de gestion Event Hubs vous permet de contrôler le service et de visualiser son état et ses alertes. 

## <a name="overview"></a>Vue d’ensemble

Pour accéder à la page de gestion Event Hubs, procédez comme suit :

1. Connectez-vous au portail d’administration Azure Stack Hub.
2. Sélectionnez **Tous les services** dans le volet de gauche.
3. Recherchez « Event Hubs » et sélectionnez le service. Si vous ne trouvez pas le service Event Hubs, le fournisseur de ressources doit d’abord être [installé](event-hubs-rp-install.md).
4. La page de vue d’ensemble de la gestion Event Hubs s’affiche. Vous trouverez quatre sections dans le volet gauche :
   - **Vue d’ensemble** : fournit une vue générale et un accès à des zones de gestion spécifiques.
   - **Alertes** : affiche toutes les alertes critiques et d’avertissement pour Event Hubs. Pour plus de détails, consultez la section [Alertes](#alerts).
   - **Quotas** : vous permet de créer, mettre à jour et supprimer des quotas. Pour plus de détails, consultez la section [Quotas](#quotas).
   - **Clusters Event Hubs** : affiche la liste de tous les clusters configurés. Pour plus de détails, consultez la section [Clusters Event Hubs](#event-hubs-clusters).

   [![Gérer Event Hubs](media/event-hubs-rp-manage/1-manage-event-hubs.png)](media/event-hubs-rp-manage/1-manage-event-hubs.png#lightbox)

## <a name="quotas"></a>Quotas

La sélection de **Quotas** dans la page principale affiche la liste des quotas en cours d’utilisation, dont les plans associés qui spécifient les quotas. 
 
[![Gérer Event Hubs - quotas](media/event-hubs-rp-manage/3-quotas.png)](media/event-hubs-rp-manage/3-quotas.png#lightbox)

Pour plus d’informations sur les types de quotas définis pour Event Hubs, consultez [Types de quotas](azure-stack-quota-types.md#event-hubs-quota-types)

## <a name="alerts"></a>Alertes

Le fournisseur de ressources Event Hubs prend en charge les alertes suivantes :
   
| Category | Alerte | Type | Condition |
|----------|-------|------|-----------|
| Performances | | | |
| | EventHub-CpuUsage | Avertissement | La moyenne du pourcentage d’utilisation du processeur du cluster Event Hubs au cours des 6 dernières heures est supérieure à 50 %. |
| | EventHub-MemoryUsage | Avertissement | La moyenne du pourcentage d’utilisation du disque de données (E:) du cluster Event Hubs au cours des 6 dernières heures est supérieure à 50 %. |
| | EventHub-DiskUsage | Avertissement | La moyenne du pourcentage d’espace mémoire libre du cluster Event Hubs au cours des 6 dernières heures est inférieure à 50 %. |
| Utilisation/quota | | | |
| | EventHub-QuotaExceeded | Avertissement | Une erreur de dépassement de quota s’est produite au cours des six dernières heures. |
| | EventHub-NamespaceCreditUsage | Avertissement | La somme des utilisations de crédit des espaces de noms au cours des six dernières heures est supérieure à 10000,0. |
| Service dégradé | | | |
| | EventHub-InternalServerError | Avertissement | Une erreur de serveur interne s’est produite au cours des six dernières heures. |
| | EventHub-ServerBusy | Avertissement | Une erreur de serveur occupé s’est produite au cours des six dernières heures. |
| Client | | | |
| | EventHub-ClientError | Avertissement | Une erreur client s’est produite au cours des six dernières heures. |
| Ressource | | | |
| | EventHub-PendingDeletingResources | Avertissement | La somme de la suppression des ressources en attente au cours des six dernières heures est supérieure à 100. |
| | EventHub-ProvisioningQueueLength | Avertissement | La moyenne de la longueur de la file d’attente de provisionnement au cours des six dernières heures est supérieure à 30. |

La sélection d’**Alertes** dans la page principale affiche la liste des alertes émises :

[![Gérer Event Hubs - récapitulatif des alertes](media/event-hubs-rp-manage/2-alerts-summary.png)](media/event-hubs-rp-manage/2-alerts-summary.png#lightbox)

La sélection d’une alerte dans la liste affiche le panneau **Détails de l’alerte** à droite :

[![Gérer Event Hubs - détails des alertes](media/event-hubs-rp-manage/2-alerts-detail.png)](media/event-hubs-rp-manage/2-alerts-detail.png#lightbox)

Pour plus d’informations sur la fonctionnalité de supervision Azure Stack Hub, notamment la génération d’alertes, consultez [Surveiller l’intégrité et les alertes](azure-stack-monitor-health.md). Pour plus d’informations sur la collecte des journaux, consultez [Vue d’ensemble de la collecte automatique des journaux de diagnostic Azure Stack](azure-stack-diagnostic-log-collection-overview.md).

## <a name="event-hubs-clusters"></a>Clusters Event Hubs

La sélection de **Clusters Event Hubs** dans la page principale affiche la liste des clusters utilisateur disponibles. La liste comprend les éléments suivants pour chaque cluster :

- Informations de configuration générales.
- Intégrité du service.
- État de la sauvegarde.

[![Gérer Event Hubs - ressources utilisateur](media/event-hubs-rp-manage/4-user-resources.png)](media/event-hubs-rp-manage/4-user-resources.png#lightbox)

La sélection d’un lien sous **Intégrité** ou **Sauvegarde** affiche des informations détaillées sur l’état d’intégrité et l’état de sauvegarde Event Hubs, respectivement. Le lien sous **Nom** affiche plus de détails pour le cluster, notamment :
- Informations d’état et de configuration.
- Liste des limites du service pour le cluster.

[![Gérer Event Hubs - clusters utilisateur](media/event-hubs-rp-manage/4-user-clusters.png)](media/event-hubs-rp-manage/4-user-clusters.png#lightbox)

Les limites du service sont des paramètres de configuration qui définissent les limites opérationnelles d’Event Hubs. Les limites du service disponibles sont similaires à celles proposées pour Azure Event Hubs Dedicated. En sélectionnant le ou les liens sous **Valeur de configuration**, vous pouvez modifier la valeur assignée.

> [!IMPORTANT]
> Vous devez consacrer du temps à analyser toutes les implications avant de modifier les limites du service. Les modifications des limites du service peuvent avoir un impact sur le comportement de votre solution qui consomme et génère des événements. Les modifications peuvent également avoir un impact sur la consommation des ressources de votre capacité Azure Stack.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur :

- les types de quotas définis pour Event Hubs, consultez [Types de quotas](azure-stack-quota-types.md#event-hubs-quota-types).
- la fonctionnalité de supervision Azure Stack Hub, notamment la génération d’alertes, consultez [Surveiller l’intégrité et les alertes](azure-stack-monitor-health.md). 
- la collecte des journaux Azure Stack Hub, consultez [Vue d’ensemble de la collecte automatique des journaux de diagnostic Azure Stack](azure-stack-diagnostic-log-collection-overview.md).













