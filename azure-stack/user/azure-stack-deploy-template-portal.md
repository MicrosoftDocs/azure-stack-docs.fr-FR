---
title: Déployer un modèle à l’aide du portail dans Azure Stack Hub
description: Apprenez à utiliser le portail Azure Stack Hub pour déployer un modèle.
author: mattbriggs
ms.topic: how-to
ms.date: 10/05/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/05/2020
ms.openlocfilehash: b428c99f7c8b847e575e40af2915a4da60f2312f
ms.sourcegitcommit: 868887e4b13b1572f15004a9db2c334e60d8add2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91778045"
---
# <a name="deploy-a-template-using-the-portal-in-azure-stack-hub"></a>Déployer un modèle à l’aide du portail dans Azure Stack Hub

Vous pouvez utiliser le portail utilisateur Azure Stack Hub pour déployer des modèles Azure Resource Manager sur Azure Stack Hub.

## <a name="to-deploy-a-template"></a>Pour déployer un modèle

1. Connectez-vous au portail utilisateur Azure Stack Hub, sélectionnez **+ Créer une ressource** > **Personnaliser** > **Déploiement de modèle**.

   ![Créer une ressource dans le portail Azure Stack Hub](media/azure-stack-deploy-template-portal/template-deploy1.png)

2. Vous pouvez soit sélectionner **Tapez pour commencer le filtrage** afin de choisir un modèle de démarrage rapide GitHub, soit choisir **Créer votre propre modèle dans l’éditeur**.

   ![Déployer un modèle dans le portail Azure Stack Hub](media/azure-stack-deploy-template-portal/template-deploy2.png)

    Les modèles [**AzureStack-QuickStart-Templates**](https://github.com/Azure/AzureStack-QuickStart-Templates) sont créés par un membre de la communauté Azure Stack Hub et non par Microsoft. Chaque modèle vous est concédé sous licence sous un contrat de licence par son propriétaire, et non par Microsoft. Microsoft ne peut pas être tenu responsable de ces modèles ni ne vérifie leur sécurité, leur compatibilité ou leurs performances. Les modèles fournis par la communauté ne sont pris en charge par aucun programme ou service d’assistance technique Microsoft. Ils sont proposés *en l’état* sans garantie d’aucune sorte.

3. Si vous avez sélectionné **Créer votre propre modèle dans l’éditeur**, collez le code de votre modèle JSON dans la fenêtre de code.

   ![Modifier un modèle dans le portail Azure Stack Hub](media/azure-stack-deploy-template-portal/template-deploy3.png)

    - Sélectionnez **Modèle de démarrage rapide** pour charger un modèle fourni par la communauté dans l’éditeur.

    - Sélectionnez **Charger le fichier** pour charger un modèle Azure Resource Manager dans l’éditeur à partir de votre ordinateur local.

    - Sélectionnez **Télécharger** pour enregistrer le modèle Azure Resource Manager sur votre ordinateur local.

    Quand vous avez terminé d’apporter des changements à votre modèle, sélectionnez **Enregistrer**.

4. Sélectionnez **Abonnement**. Choisissez l’abonnement que vous voulez utiliser. Sélectionnez **Groupe de ressources**. Vous pouvez choisir un groupe de ressources existant ou en créer un, puis sélectionner **OK**. Sélectionnez ensuite **Vérifier + créer**.

   ![Modifier des paramètres dans le portail Azure Stack Hub](media/azure-stack-deploy-template-portal/template-deploy4.png)

5. Sélectionnez **Créer**.

   ![Sélectionner un abonnement dans le portail Azure Stack Hub](media/azure-stack-deploy-template-portal/template-deploy5.png)

6. Le tableau de bord contient maintenant une nouvelle vignette qui suit la progression du déploiement de votre modèle.

   ![Sélectionner un groupe de ressources dans le portail Azure Stack Hub](media/azure-stack-deploy-template-portal/template-deploy6.png)

   Vous pouvez utiliser les modèles Azure Resource Manager pour déployer et provisionner toutes les ressources de votre application en une seule opération coordonnée. Vous pouvez également redéployer des modèles pour apporter des changements aux ressources d’un groupe de ressources. Pour plus d’informations sur l’utilisation de modèles avec Azure Stack Hub, consultez [Utiliser les modèles Azure Resource Manager dans Azure Stack Hub](azure-stack-arm-templates.md).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le déploiement de modèles, consultez l’article suivant :

- [Déployer des modèles avec PowerShell](azure-stack-deploy-template-powershell.md)
