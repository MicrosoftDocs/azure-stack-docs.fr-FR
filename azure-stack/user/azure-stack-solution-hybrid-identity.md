---
title: Configurer une identité de cloud hybride avec des applications Azure et Azure Stack | Microsoft Docs
description: Découvrez comment configurer une identité de cloud hybride avec des applications Azure et Azure Stack.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: solution
ms.date: 06/26/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 06/26/2019
ms.openlocfilehash: 3ed0c109e0253fe6d710801dbc30de04c0b5a6e5
ms.sourcegitcommit: 2a4cb9a21a6e0583aa8ade330dd849304df6ccb5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68286824"
---
# <a name="configure-hybrid-cloud-identity-for-azure-and-azure-stack-applications"></a>Configurer l’identité de cloud hybride pour des applications Azure et Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Découvrez comment configurer une identité de cloud hybride pour vos applications Azure et Azure Stack.

Il y a deux moyens d’accorder l’accès à vos applications à la fois dans le service Azure global et dans Azure Stack.

 * Si Azure Stack dispose d’une connexion permanente à Internet, il est possible d’utiliser Azure Active Directory (Azure AD).
 * Si Azure Stack est déconnecté d’Internet, vous pouvez opter pour les services de fédération Active Directory (AD FS).

Les principaux du service vous permettent d’accorder l’accès à vos applications Azure Stack à des fins de déploiement ou de configuration à l’aide d’Azure Resource Manager dans Azure Stack.

Dans cette solution, vous allez générer un exemple d’environnement pour :

> [!div class="checklist"]
> - établir une identité hybride dans Azure global et Azure Stack ;
> - récupérer un jeton pour accéder à l’API Azure Stack.

Les étapes de cette solution exigent des autorisations d’opérateur Azure Stack.

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack est une extension d’Azure. Azure Stack apporte l’agilité et l’innovation du cloud computing à votre environnement local, en activant le seul cloud hybride qui vous permet de créer et de déployer des applications hybrides en tout lieu.  
> 
> L’article [Design Considerations for Hybrid Applications](azure-stack-edge-pattern-overview.md) se penche sur les fondements de la qualité logicielle (sélection élective, scalabilité, disponibilité, résilience, facilité de gestion et sécurité) en matière de conception, de déploiement et d’exploitation des applications hybrides. Les considérations de conception vous aident à optimiser la conception d’application hybride, en réduisant les risques dans les environnements de production.


## <a name="create-a-service-principal-for-azure-ad-in-the-portal"></a>Créer un principal du service pour Azure AD sur le portail

Si vous avez déployé Azure Stack avec Azure AD comme magasin d’identités, vous pouvez créer des principaux de service de la même façon que pour Azure. La rubrique [Utiliser une identité d’application pour accéder aux ressources](../operator/azure-stack-create-service-principals.md#manage-an-azure-ad-service-principal) montre comment effectuer les étapes via le portail. Avant de commencer, vérifiez que vous disposez des [autorisations Azure AD requises](/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions).

## <a name="create-a-service-principal-for-ad-fs-using-powershell"></a>Créer un principal du service pour AD FS avec PowerShell

Si vous avez déployé Azure Stack avec AD FS, vous pouvez utiliser PowerShell pour créer un principal de service, attribuer un rôle pour l’accès et vous connecter à partir de PowerShell avec cette identité. La rubrique [Utiliser une identité d’application pour accéder aux ressources](../operator/azure-stack-create-service-principals.md#manage-an-ad-fs-service-principal) montre comment procéder à l’aide de PowerShell.

## <a name="using-the-azure-stack-api"></a>Utiliser l’API Azure Stack

La solution [API Azure Stack](azure-stack-rest-api-use.md) décrit le processus de récupération d’un jeton permettant d’accéder à l’API Azure Stack.

## <a name="connect-to-azure-stack-using-powershell"></a>Se connecter à Azure Stack avec PowerShell

Ce guide pour [devenir rapidement opérationnel avec PowerShell dans Azure Stack](../operator/azure-stack-powershell-install.md) vous explique comment installer Azure PowerShell et vous connecter à votre installation Azure Stack.

### <a name="prerequisites"></a>Prérequis

Vous devez disposer d’une installation Azure Stack connectée à Azure Active Directory avec un abonnement accessible. Si Azure Stack n’est pas installé, vous pouvez suivre ces instructions pour configurer un [Kit de développement Azure Stack](../asdk/asdk-install.md).

#### <a name="connect-to-azure-stack-using-code"></a>Se connecter à Azure Stack avec du code

Pour vous connecter à Azure Stack avec du code, utilisez l’API des points de terminaison Azure Resource Manager pour obtenir les points de terminaison d’authentification et de graphique de votre installation Azure Stack, puis authentifiez-vous à l’aide de demandes REST. Vous trouverez un exemple d’application cliente sur [GitHub](https://github.com/shriramnat/HybridARMApplication).

>[!Note]
>Si le Kit SDK Azure du langage de votre choix ne prend pas en charge les profils d’API Azure, il risque de ne pas fonctionner avec Azure Stack. Pour en savoir plus sur les profils d’API Azure, consultez l’article [Gérer les profils de version des API](azure-stack-version-profiles.md).

## <a name="next-steps"></a>Étapes suivantes

 - Pour plus d’informations sur la gestion des identités dans Azure Stack, voir [Architecture d’identité d’Azure Stack](../operator/azure-stack-identity-architecture.md).
 - Pour plus d’informations sur les modèles cloud Azure, voir [Modèles de conception cloud](https://docs.microsoft.com/azure/architecture/patterns).
