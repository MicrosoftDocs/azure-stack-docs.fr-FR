---
title: Tâches d’évaluation avancées d’Azure Stack | Microsoft Docs
description: Cet article aborde les tâches d’évaluation avancées d’Azure Stack.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: fe78fa4b99ef7acad924165b98e20ca6156777bb
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66267115"
---
# <a name="advanced-azure-stack-development-kit-evaluation-tasks"></a>Tâches d’évaluation avancées du kit de développement Azure Stack
Une fois familiarisé avec les fonctionnalités de base du kit de développement Azure Stack (ASDK), vous pouvez approfondir vos connaissances d’Azure Stack en testant des scénarios plus avancés. Ces tâches d’évaluation plus avancées sont intégralement documentées dans la documentation destinée aux opérateurs Azure Stack.

> [!NOTE]
> Même si de nombreuses tâches d’opérateur sont prises en charge pour les déploiements Azure Stack multinœuds ASDK et production, tous les scénarios d’utilisation ne sont pas pris en charge pour les déploiements du kit ASDK. Pour plus d’informations, consultez [Différences entre ASDK et les systèmes Azure Stack à plusieurs nœuds](asdk-what-is.md#asdk-and-multi-node-azure-stack-differences).

## <a name="delegate-offers-in-azure-stack"></a>Déléguer des offres dans Azure Stack
En tant qu’opérateur Azure Stack, vous êtes souvent amené à confier à d’autres personnes la charge de créer des offres et d’inscrire les utilisateurs. Par exemple, si vous êtes un fournisseur de services et souhaitez que les revendeurs inscrivent les clients et les gèrent à votre place. Ou si vous faites partie d’un groupe informatique centralisé d’une entreprise, vous pouvez demander aux départements ou filiales d’inscrire les utilisateurs sans votre intervention.

La [délégation d’offres dans Azure Stack](../operator/azure-stack-delegated-provider.md) vous aide dans ces tâches en vous permettant d’atteindre et de gérer plus d’utilisateurs que vous ne pouvez le faire directement.

## <a name="make-sql-databases-available-to-your-azure-stack-users"></a>Mettre des bases de données SQL à la disposition de vos utilisateurs Azure Stack
En tant qu’opérateur Azure Stack, vous pouvez créer des offres qui permettent aux utilisateurs de créer des bases de données SQL qu’ils peuvent utiliser avec leurs applications cloud natives, leurs sites web et leurs charges de travail. En fournissant à vos utilisateurs ces bases de données cloud à la demande et personnalisées, vous pouvez leur faire gagner du temps et économiser des ressources.

Utilisez l’adaptateur du fournisseur de ressources SQL Server pour [mettre des bases de données SQL à disposition des utilisateurs Azure Stack](../operator/azure-stack-tutorial-sql-server.md) en tant que service Azure Stack. Une fois le fournisseur de ressources installé, vous le connectez à une ou plusieurs instances de SQL Server.

## <a name="make-web-and-api-apps-available-to-your-azure-stack-users"></a>Mettre des applications web et des API à la disposition des utilisateurs Azure Stack
En tant qu’opérateur Azure Stack, vous pouvez créer des offres qui permettent aux utilisateurs (locataires) de créer des fonctions Azure, ainsi que des applications web et des API. En permettant à vos utilisateurs d’accéder à ces applications cloud à la demande, vous pouvez leur faire gagner du temps et économiser des ressources.

Déployez le fournisseur de ressources App Service pour [mettre à la disposition de vos utilisateurs Azure Stack des applications web et des API](../operator/azure-stack-tutorial-app-service.md).

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur l’offre de services avec les systèmes intégrés Azure Stack](../operator/azure-stack-offer-services-overview.md)
