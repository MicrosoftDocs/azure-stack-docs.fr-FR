---
title: Configurer l’identité de cloud hybride avec des applications Azure et Azure Stack | Microsoft Docs
description: Découvrez comment configurer l’identité de cloud hybride avec des applications Azure et Azure Stack.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/14/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: ed85055c04e068ad614a406caf113555601bd620
ms.sourcegitcommit: 261df5403ec01c3af5637a76d44bf030f9342410
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66252046"
---
# <a name="tutorial-configure-hybrid-cloud-identity-for-azure-and-azure-stack-applications"></a>Didacticiel : Configurer l’identité de cloud hybride pour des applications Azure et Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Découvrez comment configurer l’identité de cloud hybride pour vos applications Azure et Azure Stack.

Il y a deux moyens d’accorder l’accès à vos applications à la fois dans le service Azure global et dans Azure Stack.

 * Si Azure Stack dispose d’une connexion permanente à Internet, il est possible d’utiliser Azure Active Directory (Azure AD).
 * Dans le cas contraire, vous pouvez opter pour les services de fédération Active Directory (AD FS).

Les principaux du service vous permettent d’accorder l’accès à vos applications Azure Stack à des fins de déploiement ou de configuration à l’aide d’Azure Resource Manager dans Azure Stack.

Dans ce tutoriel, vous créez un exemple d’environnement pour :

> [!div class="checklist"]
> - établir une identité hybride dans Azure global et Azure Stack ;
> - récupérer un jeton pour accéder à l’API Azure Stack.

Les étapes de ce didacticiel exigent des autorisations d’opérateur Azure Stack.

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack est une extension d’Azure. Azure Stack apporte l’agilité et l’innovation du cloud computing à votre environnement local et active le seul cloud hybride qui vous permet de créer et de déployer des applications hybrides en tous lieux.  
> 
> Le livre blanc [Design Considerations for Hybrid Applications](https://aka.ms/hybrid-cloud-applications-pillars) (Étude des conceptions pour les applications hybrides) se penche sur les fondements de la qualité logicielle (sélection élective, extensibilité, disponibilité, résilience, facilité de gestion et sécurité) en matière de conception, de déploiement et d’exploitation des applications hybrides. Les considérations de conception vous aident à optimiser la conception des applications hybrides, en réduisant les risques dans les environnements de production.


## <a name="create-a-service-principal-for-azure-ad-in-the-portal"></a>Créer un principal du service pour Azure AD sur le portail

Si vous avez déployé Azure Stack avec Azure AD comme magasin d’identités, vous pouvez créer des principaux de service de la même façon que pour Azure. L’article [Créer des principaux de service](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad) explique comment effectuer ces étapes via le portail. Vérifiez que vous disposez des [autorisations Azure AD requises](/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) avant de commencer.

## <a name="create-a-service-principal-for-ad-fs-using-powershell"></a>Créer un principal du service pour AD FS avec PowerShell

Si vous avez déployé Azure Stack avec AD FS, vous pouvez utiliser PowerShell pour créer un principal de service, attribuer un rôle pour l’accès et vous connecter à partir de PowerShell avec cette identité. L’article [Créer un principal de service pour AD FS](azure-stack-create-service-principals.md#create-service-principal-for-ad-fs) explique comment effectuer les étapes nécessaires à l’aide de PowerShell.

## <a name="using-the-azure-stack-api"></a>Utiliser l’API Azure Stack

Le didacticiel [API Azure Stack](azure-stack-rest-api-use.md) décrit le processus de récupération d’un jeton permettant d’accéder à l’API Azure Stack.

## <a name="connect-to-azure-stack-using-powershell"></a>Se connecter à Azure Stack avec PowerShell

Ce guide pour [devenir rapidement opérationnel avec PowerShell dans Azure Stack](../operator/azure-stack-powershell-install.md) vous explique comment installer Azure PowerShell et vous connecter à votre installation Azure Stack.

### <a name="prerequisites"></a>Prérequis

Une installation Azure Stack connectée à Azure Active Directory avec un abonnement accessible. Si Azure Stack n’est pas installé, vous pouvez suivre ces instructions pour configurer un [Kit de développement Azure Stack](../asdk/asdk-install.md).

#### <a name="connect-to-azure-stack-using-code"></a>Se connecter à Azure Stack avec du code

Pour vous connecter à Azure Stack avec du code, utilisez l’API des points de terminaison Azure Resource Manager pour obtenir les points de terminaison d’authentification et de graphique de votre installation Azure Stack, puis authentifiez-vous à l’aide de demandes REST. Vous trouverez un exemple d’application cliente sur [GitHub](https://github.com/shriramnat/HybridARMApplication).

>[!Note]
>Si le Kit SDK Azure du langage de votre choix ne prend pas en charge les profils d’API Azure, il risque de ne pas fonctionner avec Azure Stack. Pour en savoir plus sur les profils d’API Azure, consultez l’article [Gérer les profils de version des API](azure-stack-version-profiles.md).

## <a name="next-steps"></a>Étapes suivantes

 - Pour plus d’informations sur la gestion des identités dans Azure Stack, voir [Architecture d’identité d’Azure Stack](../operator/azure-stack-identity-architecture.md).
 - Pour plus d’informations sur les modèles cloud Azure, voir [Modèles de conception cloud](https://docs.microsoft.com/azure/architecture/patterns).
