---
title: Tâches d’évaluation avancées ASDK
description: En savoir plus sur les tâches d’évaluation avancées du kit de développement Azure Stack (ASDK).
author: justinha
ms.topic: article
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 10/16/2019
ms.openlocfilehash: 2c4a83ea024f83c0181cfe77c598519746deaeed
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "77695357"
---
# <a name="advanced-asdk-evaluation-tasks"></a>Tâches d’évaluation avancées ASDK
Une fois familiarisé avec les fonctionnalités de base du kit de développement Azure Stack (ASDK), vous pouvez approfondir vos connaissances d’Azure Stack en testant des scénarios plus avancés. Ces tâches d’évaluation plus avancées sont intégralement documentées dans la documentation destinée aux opérateurs Azure Stack.

> [!NOTE]
> Même si de nombreuses tâches d’opérateur sont prises en charge pour les kits ASDK ainsi que pour les déploiements de production et multinœuds d’Azure Stack, tous les scénarios d’utilisation ne sont pas pris en charge pour les déploiements du kit ASDK. Pour plus d’informations, consultez [Différences entre ASDK et les systèmes Azure Stack à plusieurs nœuds](asdk-what-is.md#asdk-and-multi-node-azure-stack-hub-differences).

## <a name="delegate-offers-in-azure-stack"></a>Déléguer des offres dans Azure Stack
En tant qu’opérateur Azure Stack, vous êtes souvent amené à confier à d’autres personnes la charge de créer des offres et d’inscrire les utilisateurs. Par exemple, si vous êtes un fournisseur de services et souhaitez que les revendeurs inscrivent les clients et les gèrent à votre place. Ou si vous faites partie d’un groupe informatique centralisé d’une entreprise, vous pouvez demander aux départements ou filiales d’inscrire les utilisateurs sans votre intervention.

La [délégation d’offres dans Azure Stack](../operator/azure-stack-delegated-provider.md) vous aide dans ces tâches en vous permettant d’atteindre et de gérer plus d’utilisateurs que vous ne pouvez le faire directement.

## <a name="make-sql-databases-available-to-your-azure-stack-users"></a>Mettre des bases de données SQL à la disposition de vos utilisateurs Azure Stack
En tant qu’opérateur Azure Stack, vous pouvez créer des offres qui permettent aux utilisateurs de créer des bases de données SQL qu’ils peuvent utiliser avec leurs applications cloud natives, leurs sites web et leurs charges de travail. En fournissant à vos utilisateurs ces bases de données cloud à la demande et personnalisées, vous leur faites gagner du temps et économiser des ressources.

Utilisez l’adaptateur du fournisseur de ressources SQL Server pour [mettre des bases de données SQL à disposition des utilisateurs Azure Stack](../operator/azure-stack-tutorial-sql-server.md) en tant que service Azure Stack. Une fois le fournisseur de ressources installé, vous le connectez à une ou plusieurs instances de SQL Server.

## <a name="make-web-and-api-apps-available-to-your-azure-stack-users"></a>Mettre des applications web et des API à la disposition des utilisateurs Azure Stack
En tant qu’opérateur Azure Stack, vous pouvez créer des offres qui permettent aux utilisateurs (locataires) de créer des fonctions Azure, ainsi que des applications web et d’API. En permettant à vos utilisateurs d’accéder à ces applications informatiques à la demande, vous pouvez leur faire gagner du temps et économiser des ressources.

Déployez le fournisseur de ressources App Service pour [mettre à la disposition de vos utilisateurs Azure Stack des applications web et d’API](../operator/azure-stack-tutorial-app-service.md).

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur l’offre de services avec les systèmes intégrés Azure Stack](../operator/service-plan-offer-subscription-overview.md)
