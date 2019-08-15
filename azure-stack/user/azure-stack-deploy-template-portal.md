---
title: Déployer un modèle à l’aide du portail dans Azure Stack | Microsoft Docs
description: Apprenez à utiliser le portail Azure Stack pour déployer un modèle.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/13/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: e6a9c9569f7a2aeff044232d15817313b43ed69f
ms.sourcegitcommit: aefcf9c61bd8089a0aaa569af7643e5e15f4947c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68991859"
---
# <a name="deploy-a-template-using-the-portal-in-azure-stack"></a>Déployer un modèle à l’aide du portail dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Vous pouvez utiliser le portail pour déployer des modèles Azure Resource Manager dans Azure Stack.

## <a name="to-deploy-a-template"></a>Pour déployer un modèle

1. Connectez-vous au portail, sélectionnez **+ Créer une ressource**, puis sélectionnez **Personnalisé**.

   ![Créer une ressource dans le portail Azure Stack](media/azure-stack-deploy-template-portal/template-deploy1.png)

1. Sélectionnez **Déploiement de modèle**.

   ![Déployer un modèle dans le portail Azure Stack](media/azure-stack-deploy-template-portal/template-deploy2.png)

1. Sélectionnez **Modifier un modèle**, puis collez le code de votre modèle JSON dans la fenêtre de code. Sélectionnez **Enregistrer**.

   ![Modifier un modèle dans le portail Azure Stack](media/azure-stack-deploy-template-portal/template-deploy3.png)

1. Sélectionnez **Modifier les paramètres**, indiquez des valeurs pour les paramètres affichés, puis sélectionnez **OK**.

   ![Modifier les paramètres dans le portail Azure Stack](media/azure-stack-deploy-template-portal/template-deploy4.png)

1. Sélectionnez **Abonnement**. Choisissez l’abonnement que vous voulez utiliser, puis sélectionnez **OK**.

   ![Sélectionner un abonnement dans le portail Azure Stack](media/azure-stack-deploy-template-portal/template-deploy5.png)

1. Sélectionnez **Groupe de ressources**. Choisissez un groupe de ressources existant ou créez-en un, puis sélectionnez **OK**.

   ![Sélectionner un groupe de ressources dans le portail Azure Stack](media/azure-stack-deploy-template-portal/template-deploy6.png)

1. Sélectionnez **Créer**. Le tableau de bord contient maintenant une nouvelle vignette qui suit la progression du déploiement de votre modèle.

   ![Créer un modèle dans le portail Azure Stack](media/azure-stack-deploy-template-portal/template-deploy7.png)

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le déploiement de modèles, consultez l’article suivant :

- [Déployer des modèles avec PowerShell](azure-stack-deploy-template-powershell.md)
