---
title: Gestion des régions dans Azure Stack Hub
titleSuffix: Azure Stack Hub
description: Vue d’ensemble de la gestion des régions dans Azure Stack Hub.
author: sethmanheim
ms.topic: article
ms.date: 08/27/2020
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 11/27/2019
ms.openlocfilehash: e40d79dcac58035172d4e292461fd0950ee77979
ms.sourcegitcommit: 03aad17afe8519536066c735c59ad1bdfe8de083
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89041636"
---
# <a name="region-management-in-azure-stack-hub"></a>Gestion des régions dans Azure Stack Hub

Azure Stack Hub utilise le concept des *régions*, qui sont des entités logiques composées des ressources matérielles qui constituent l’infrastructure Azure Stack Hub. Dans la gestion des régions se trouvent toutes les ressources nécessaires pour utiliser correctement l’infrastructure Azure Stack Hub.

Un déploiement de système intégré (appelé *cloud Azure Stack Hub*) constitue une région unique. Chaque Kit de développement Azure Stack (ASDK) est associé à une région, nommée **local**. Si vous déployez un deuxième système intégré Azure Stack Hub, ou si vous configurez une autre instance du ASDK sur un ordinateur distinct, ce cloud Azure Stack Hub est une région différente.

## <a name="information-available-through-the-region-management-tile"></a>Informations disponibles par le biais de la vignette Gestion des régions

Azure Stack Hub dispose d’un ensemble de fonctionnalités de gestion des régions disponibles dans la vignette **Gestion des régions**. Cette vignette est accessible à un opérateur Azure Stack Hub sur le tableau de bord par défaut du portail administrateur. Cet écran vous permet de surveiller et de mettre à jour votre région Azure Stack Hub et ses composants, qui sont propres à la région.

![Vignette Gestion des régions dans le portail administrateur Azure Stack Hub](media/azure-stack-region-management/image1.png)

Si vous sélectionnez une région dans la vignette **Gestion des régions**, vous accédez aux informations suivantes :

[![Description des volets du panneau de gestion des régions dans le portail administrateur Azure Stack Hub](media/azure-stack-region-management/regionssm.png "Panneau de gestion des région dans le portail administrateur Azure Stack Hub")](media/azure-stack-region-management/regions.png#lightbox)

1. **Le menu de ressources** : Accédez à différentes zones de gestion d’infrastructure, visualisez et gérez les ressources utilisateur telles que les comptes de stockage et les réseaux virtuels.

2. **Alertes** : Affichez la liste des alertes à l’échelle du système ainsi que les détails de chacune de ces alertes.

3. **Mises à jour** : Consultez la version actuelle de votre infrastructure Azure Stack Hub, les mises à jour disponibles et l’historique des mises à jour. Vous pouvez également mettre à jour votre système intégré.

4. **Fournisseurs de ressources** : Gérez les fonctionnalités utilisateur proposées par les composants nécessaires pour exécuter Azure Stack Hub. Chaque fournisseur de ressources est fourni avec une expérience d’administration. Cette expérience peut inclure des alertes relatives au fournisseur spécifique, aux mesures et à d’autres fonctionnalités de gestion propres au fournisseur de ressources.

5. **Rôles d’infrastructure** : Composants nécessaires à l’exécution d’Azure Stack Hub. Seuls les rôles d’infrastructure qui signalent des alertes sont répertoriés. En sélectionnant un rôle, vous pouvez voir les alertes qui sont associées à ce rôle et aux instances de rôle où il est en cours d’exécution.

6. **Propriétés** : État de l’inscription et détails de votre environnement dans le panneau Gestion des régions. L’état peut être **Inscrit**, **Non inscrit** ou **Expiré**. S’il est inscrit, il montre également l’ID d’abonnement Azure que vous avez utilisé pour inscrire votre instance Azure Stack Hub ainsi que le groupe de ressources et le nom de l’inscription.

## <a name="next-steps"></a>Étapes suivantes

- [Surveiller l’intégrité et les alertes dans Azure Stack Hub](azure-stack-monitor-health.md)
- [Gérer les mises à jour dans Azure Stack Hub](azure-stack-updates.md)
