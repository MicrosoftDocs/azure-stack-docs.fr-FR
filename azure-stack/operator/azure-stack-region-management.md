---
title: Gestion des régions dans Azure Stack | Microsoft Docs
description: Vue d’ensemble de la gestion des régions dans Azure Stack.
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: e94775d5-d473-4c03-9f4e-ae2eada67c6c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 04/16/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: efemmano
ms.lastreviewed: 11/27/2018
ms.openlocfilehash: d0c61d5a0056b416bcbfaa26003f55955a89d0ad
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "64308933"
---
# <a name="region-management-in-azure-stack"></a>Gestion des régions dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Azure Stack utilise le concept des *régions*, qui sont des entités logiques composées des ressources matérielles qui constituent l’infrastructure Azure Stack. Dans la gestion des régions se trouvent toutes les ressources nécessaires pour utiliser correctement l’infrastructure Azure Stack.

Un déploiement de système intégré (appelé *cloud Azure Stack*) constitue une région unique. Chaque Kit de développement Azure Stack (ASDK) est associé à une région, nommée **local**. Si vous déployez un deuxième système intégré Azure Stack, ou si vous configurez une autre instance du Kit de développement sur un ordinateur distinct, ce cloud Azure Stack est une région différente.

## <a name="information-available-through-the-region-management-tile"></a>Informations disponibles par le biais de la vignette Gestion des régions

Azure Stack dispose d’un ensemble de fonctionnalités de gestion des régions disponibles dans la vignette **Gestion des régions**. Cette vignette est accessible à un opérateur Azure Stack sur le tableau de bord par défaut du portail administrateur. Elle vous permet de surveiller et de mettre à jour votre région Azure Stack et ses composants, qui sont propres à la région.

![Vignette de gestion des régions](media/azure-stack-region-management/image1.png)

Si vous cliquez sur une région dans la vignette **Gestion des régions**, vous pouvez accéder aux informations suivantes :

[![Description des volets du panneau Gestion des régions](media/azure-stack-region-management/regionssm.png "Panneau Gestion des régions")](media/azure-stack-region-management/regions.png#lightbox)

1. **Le menu de ressources**. Accédez à des zones de gestion de l’infrastructure spécifiques, puis affichez et gérez des ressources utilisateur telles que des comptes de stockage et des réseaux virtuels.

2. **Alertes**. Affichez la liste des alertes à l’échelle du système ainsi que les détails de chacune de ces alertes.

3. **Mises à jour**. Consultez la version actuelle de votre infrastructure Azure Stack, les mises à jour disponibles et l’historique des mises à jour. Vous pouvez également mettre à jour votre système intégré.

4. **Fournisseurs de ressources**. Gérez les fonctionnalités utilisateur proposées par les composants nécessaires pour exécuter Azure Stack. Chaque fournisseur de ressources est fourni avec une expérience d’administration. Cette expérience peut inclure des alertes relatives au fournisseur spécifique, aux mesures et à d’autres fonctionnalités de gestion propres au fournisseur de ressources.

5. **Rôles d’infrastructure**. Composants nécessaires à l’exécution d’Azure Stack. Seuls les rôles d’infrastructure qui signalent des alertes sont répertoriés. En sélectionnant un rôle, vous pouvez voir les alertes qui sont associées à ce rôle et aux instances de rôle où il est en cours d’exécution.

6. **Properties**. État de l’inscription et détails de votre environnement dans le panneau Gestion des régions. L’état peut être **Inscrit**, **Non inscrit** ou **Expiré**. S’il est inscrit, il montre également l’ID d’abonnement Azure que vous avez utilisé pour inscrire votre instance Azure Stack ainsi que le groupe de ressources et le nom de l’inscription.

## <a name="next-steps"></a>Étapes suivantes

- [Surveiller l’intégrité et les alertes dans Azure Stack](azure-stack-monitor-health.md)
- [Gérer les mises à jour dans Azure Stack](azure-stack-updates.md)

<!-- Update_Description: wording update -->