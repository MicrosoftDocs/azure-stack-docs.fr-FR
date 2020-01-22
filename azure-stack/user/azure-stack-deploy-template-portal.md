---
title: Déployer un modèle à l’aide du portail dans Azure Stack Hub | Microsoft Docs
description: Apprenez à utiliser le portail Azure Stack Hub pour déployer un modèle.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: a35029865c7b0559a2cc7c54d2d8ff34445b438c
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75883743"
---
# <a name="deploy-a-template-using-the-portal-in-azure-stack-hub"></a>Déployer un modèle à l’aide du portail dans Azure Stack Hub

Vous pouvez utiliser le portail pour déployer des modèles Azure Resource Manager vers Azure Stack Hub.

## <a name="to-deploy-a-template"></a>Pour déployer un modèle

1. Connectez-vous au portail, sélectionnez **+ Créer une ressource**, puis sélectionnez **Personnalisé**.

   ![Créer une ressource dans le portail Azure Stack Hub](media/azure-stack-deploy-template-portal/template-deploy1.png)

1. Sélectionnez **Déploiement de modèle**.

   ![Déployer un modèle dans le portail Azure Stack Hub](media/azure-stack-deploy-template-portal/template-deploy2.png)

1. Sélectionnez **Modifier un modèle**, puis collez le code de votre modèle JSON dans la fenêtre de code. Sélectionnez **Enregistrer**.

   ![Modifier un modèle dans le portail Azure Stack Hub](media/azure-stack-deploy-template-portal/template-deploy3.png)

1. Sélectionnez **Modifier les paramètres**, indiquez des valeurs pour les paramètres affichés, puis sélectionnez **OK**.

   ![Modifier des paramètres dans le portail Azure Stack Hub](media/azure-stack-deploy-template-portal/template-deploy4.png)

1. Sélectionnez **Abonnement**. Choisissez l’abonnement que vous voulez utiliser, puis sélectionnez **OK**.

   ![Sélectionner un abonnement dans le portail Azure Stack Hub](media/azure-stack-deploy-template-portal/template-deploy5.png)

1. Sélectionnez **Groupe de ressources**. Choisissez un groupe de ressources existant ou créez-en un, puis sélectionnez **OK**.

   ![Sélectionner un groupe de ressources dans le portail Azure Stack Hub](media/azure-stack-deploy-template-portal/template-deploy6.png)

1. Sélectionnez **Create** (Créer). Le tableau de bord contient maintenant une nouvelle vignette qui suit la progression du déploiement de votre modèle.

   ![Créer un modèle dans le portail Azure Stack Hub](media/azure-stack-deploy-template-portal/template-deploy7.png)

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le déploiement de modèles, consultez l’article suivant :

- [Déployer des modèles avec PowerShell](azure-stack-deploy-template-powershell.md)
