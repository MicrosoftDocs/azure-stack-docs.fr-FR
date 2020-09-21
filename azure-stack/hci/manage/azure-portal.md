---
title: Utiliser le portail Azure avec Azure Stack HCI
description: Comment afficher et gérer vos clusters Azure Stack HCI à l’aide du portail Azure.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/15/2020
ms.openlocfilehash: e261c54140ed62a7b2fd19588e2b3ffc3b3911b8
ms.sourcegitcommit: 0579942530284d63472fb346cf5ff55de328cea4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90560873"
---
# <a name="use-the-azure-portal-with-azure-stack-hci"></a>Utiliser le portail Azure avec Azure Stack HCI

> S’applique à : Azure Stack HCI, version 20H2 ; Windows Server 2019

Cette rubrique explique comment se connecter aux solutions Azure Stack HCI du portail Azure pour avoir une vue globale de vos clusters Azure Stack HCI. Le portail Azure vous permet de gérer et surveiller vos clusters, même si votre infrastructure physique est hébergée localement. La surveillance basée sur le cloud élimine la nécessité de conserver un système de surveillance et une base de données locaux, ce qui a pour effet de réduire la complexité de l’infrastructure. Elle améliore également la scalabilité en chargeant des alertes et d’autres informations directement sur Azure qui gère déjà des millions par jour.

## <a name="view-your-clusters-in-the-azure-portal"></a>Affichez vos clusters dans le portail Azure

Une fois qu’un cluster Azure Stack HCI est inscrit, sa ressource Azure est visible dans le portail Azure. Pour l’afficher, commencez par vous connecter au [portail Azure](https://portal.azure.com). Si vous avez déjà [inscrit votre cluster auprès d’Azure](../deploy/register-with-azure.md), vous devriez voir un nouveau groupe de ressources portant le nom de votre cluster avec le suffixe « -rg ». Si votre groupe de ressources Azure Stack HCI n’est pas affiché, recherchez « HCI », puis sélectionnez votre cluster dans le menu déroulant :

:::image type="content" source="media/azure-portal/azure-portal-search.png" alt-text="Recherchez hci dans le portail Azure pour trouver votre ressource Azure Stack HCI":::

La page d’hébergement du service Azure Stack HCI répertorie tous vos clusters, ainsi que leur groupe de ressources, leur emplacement et l’abonnement associé.

:::image type="content" source="media/azure-portal/azure-portal-home.png" alt-text="Page d’accueil du service Azure Stack HCI sur le portail Azure":::

Cliquez sur une ressource Azure Stack HCI pour afficher la page de vue d’ensemble de cette ressource, qui présente une synthèse des nœuds de cluster et de serveur.

:::image type="content" source="media/azure-portal/azure-portal-overview.png" alt-text="Page de vue d’ensemble de ressource Azure Stack HCI sur le portail Azure":::

## <a name="view-the-activity-log"></a>Afficher le journal d’activité

Le journal d’activité fournit une liste des opérations et événements récents sur le cluster, ainsi que leur état, leur heure de survenance, l’abonnement associé et l’utilisateur qui les a déclenchés. Vous pouvez filtrer les événements par abonnement, gravité, intervalle de temps, groupe de ressources et ressource.

:::image type="content" source="media/azure-portal/azure-portal-activity-log.png" alt-text="Écran du journal d’activité pour une ressource Azure Stack HCI sur le portail Azure":::

## <a name="configure-access-control"></a>Configurer le contrôle d’accès

Utilisez le contrôle d’accès pour vérifier l’accès des utilisateurs, gérer les rôles, ainsi qu’ajouter et afficher des attributions de rôles ou refuser des attributions.

:::image type="content" source="media/azure-portal/azure-portal-iam.png" alt-text="Écran de contrôle d’accès d’une ressource Azure Stack HCI sur le portail Azure":::

## <a name="add-and-edit-tags"></a>Ajouter et modifier des étiquettes

Les étiquettes sont des paires nom/valeur qui vous permettent de catégoriser les ressources et d’afficher une facturation centralisée en appliquant la même étiquette à plusieurs ressources et groupes de ressources. Les noms d’étiquette ne sont pas sensibles à la casse, alors que les valeurs d’étiquette le sont. [En savoir plus sur les étiquettes](/azure/azure-resource-manager/management/tag-resources).

:::image type="content" source="media/azure-portal/azure-portal-tags.png" alt-text="Ajouter ou modifier des balises pour une ressource Azure Stack HCI sur le portail Azure":::

## <a name="compare-azure-portal-and-windows-admin-center"></a>Comparer le portail Azure et Windows Admin Center

Contrairement au Windows Admin Center, le portail Azure pour Azure Stack HCI est conçu pour une surveillance de plusieurs clusters à l’échelle mondiale. Le tableau suivant vous aidera à déterminer l’outil de gestion adapté à vos besoins. Ensemble, ces ressources permettent une conception cohérente et sont utiles dans des scénarios complémentaires.

| Windows Admin Center | Portail Azure |
| --------------- | --------------- |
| Gestion de matériel périphérique local et de machine virtuelle, toujours disponible | Gestion à grande échelle, fonctionnalités supplémentaires |
| Gérer une infrastructure Azure Stack HCI | Gérer d’autres services Azure |
| Surveiller et mettre à jour des clusters individuels | Surveiller et mettre à jour à grande échelle |
| Gestion et approvisionnement manuels de machines virtuelles | Machines virtuelles en libre-service d’Azure Arc |

## <a name="next-steps"></a>Étapes suivantes

Pour consulter des informations connexes, reportez-vous également à :

- [Connecter Azure Stack HCI à Azure](../deploy/register-with-azure.md)
- [Superviser Azure Stack HCI avec Azure Monitor](azure-monitor.md)
