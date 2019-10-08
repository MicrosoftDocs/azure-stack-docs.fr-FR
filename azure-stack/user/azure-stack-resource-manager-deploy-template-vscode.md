---
title: Déployer avec Visual Studio Code dans Azure Stack | Microsoft Docs
description: En tant qu’utilisateur, je souhaite créer un modèle Azure Resource Manager dans Visual Studio Code et utiliser le schéma de déploiement pour préparer un modèle compatible avec ma version d’Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/30/2019
ms.openlocfilehash: 914e3e8db57009d58a14aa87d24ff86a8291e52b
ms.sourcegitcommit: e8aa26b078a9bab09c8fafd888a96785cc7abb4d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71711102"
---
# <a name="deploy-with-visual-studio-code-to-azure-stack"></a>Déployer avec Visual Studio Code dans Azure Stack

Vous pouvez utiliser Visual Studio Code et l’extension Outils Azure Resource Manager pour créer et modifier des modèles Azure Resource Manager qui fonctionneront avec votre version d’Azure Stack. Vous pouvez créer des modèles Resource Manager dans Visual Studio Code sans l’extension, mais cette dernière fournit des options de saisie semi-automatique qui simplifient le développement de modèles. De plus, vous pouvez spécifier un schéma de déploiement qui vous aidera à comprendre les ressources disponibles sur Azure Stack.

Dans cet article, vous allez déployer une machine virtuelle Windows.

## <a name="concepts-for-azure-stack-resource-manager"></a>Concepts relatifs à Azure Stack Resource Manager

### <a name="azure-stack-resource-manager"></a>Azure Stack Resource Manager

Pour comprendre les concepts associés au déploiement et à la gestion de vos solutions Azure dans Azure Stack, consultez [Utiliser les modèles Azure Resource Manager dans Azure Stack](azure-stack-arm-templates.md).

### <a name="api-profiles"></a>Profils d’API
Pour comprendre les concepts associés à la coordination de fournisseurs de ressources sur Azure Stack, consultez [Gérer les profils de version des API dans Azure Stack](azure-stack-version-profiles.md).

### <a name="the-deployment-schema"></a>Schéma de déploiement

Le schéma de déploiement Azure Stack prend en charge les profils hybrides par le biais des modèles Azure Resource Manager dans Visual Studio Code. Vous pouvez changer une ligne dans le modèle JSON pour référencer le schéma, puis utiliser IntelliSense pour passer en revue la ressource compatible avec Azure. Avec le schéma, passez en revue les fournisseurs de ressources, les types et les versions d’API pris en charge dans votre version d’Azure Stack. Le schéma dépend du profil d’API utilisé pour récupérer les versions spécifiques des points de terminaison d’API dans les fournisseurs de ressources pris en charge dans votre version d’Azure Stack. Vous pouvez utiliser la saisie semi-automatique pour les attributs type et apiVersion. Vous êtes alors êtes limité aux versions d’API et aux types de ressources disponibles pour le profil d’API.

## <a name="prerequisites"></a>Prérequis

- [Visual Studio Code](https://code.visualstudio.com/)
- Accéder à Azure Stack
- [Azure Stack PowerShell installé](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fuser%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fbreadcrumb%2Ftoc.json) sur une machine qui atteint les points de terminaison de gestion

## <a name="install-resource-manager-tools-extension"></a>Installer l’extension Resource Manager Tools

Pour installer l’extension Outils Azure Resource Manager, effectuez les étapes suivantes :

1. Ouvrez Visual Studio Code.
2. Appuyez sur CTRL+MAJ+X pour ouvrir le volet Extensions.
3. Recherchez `Azure Resource Manager Tools`, puis sélectionnez **Installer**.
4. Pour terminer l’installation de l’extension, sélectionnez **Recharger**.

## <a name="get-a-template"></a>Obtenir un modèle

Au lieu de créer un modèle à partir de zéro, ouvrez un modèle à partir du dépôt AzureStack-QuickStart-Templates (https://github.com/Azure/AzureStack-QuickStart-Templates). AzureStack-QuickStart-Templates est un dépôt pour les modèles Resource Manager qui déploient des ressources sur Azure Stack. 

Le modèle de cet article est appelé `101-vm-windows-create`. Le modèle définit un déploiement de base d’une machine virtuelle Windows dans Azure Stack.  Ce modèle déploie également un réseau virtuel (avec DNS), un groupe de sécurité réseau et une interface réseau.

1. Ouvrez Visual Studio Code, puis accédez à un dossier de travail sur votre machine.
2. Ouvrez un terminal Git Bash dans Visual Studio Code.
3. Exécutez la commande suivante pour récupérer le dépôt de démarrage rapide Azure Stack.
    ```bash  
    Git clone https://github.com/Azure/AzureStack-QuickStart-Templates.git
    ```
4. Ouvrez le répertoire contenant le dépôt.
    ```bash  
    CD AzureStack-QuickStart-Templates
    ```
5. Sélectionnez **Ouvrir** pour ouvrir le fichier à l’emplacement `/101-vm-windows-create/azuredeploy.json` dans le dépôt.
6. Enregistrez le fichier dans votre propre espace de travail ou, si vous avez créé une branche du dépôt, vous pouvez travailler sur place.
7. Le fichier étant toujours ouvert, remplacez le champ `$Schema` par `https://schema.management.azure.com/schemas/2019-03-01-hybrid/deploymentTemplate.json#`.
8. Vous pouvez vérifier que le schéma de déploiement fonctionne en effaçant la valeur du champ apiProfile.
    ```JSON  
    "apiProfile": ""
    ```
9. Placez votre curseur entre les guillemets vides, puis appuyez sur CTRL+Espace. Vous pouvez effectuer votre choix parmi les profils d’API valides dans le schéma de déploiement pour Azure Stack. Vous pouvez effectuer cette opération avec chacun des fournisseurs de ressources du modèle.

    ![Schéma de déploiement Azure Stack Resource Manager](./media/azure-stack-resource-manager-deploy-template-vscode/azure-stack-resource-manager-vscode-schema.png)

10. Quand vous êtes prêt, vous pouvez déployer votre modèle à l’aide de PowerShell. Suivez les instructions mentionnées dans [Déployer avec PowerShell](azure-stack-deploy-template-powershell.md). Spécifiez l’emplacement du modèle dans le script.
11. Après avoir déployé votre machine virtuelle Windows, accédez au portail Azure Stack, puis recherchez le groupe de ressources. Si vous voulez effacer le résultat de cet exercice de votre infrastructure Azure Stack, supprimez le groupe de ressources.

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur les [modèles Azure Stack Resource Manager](azure-stack-arm-templates.md).  
- Découvrez-en plus sur les [profils d’API dans Azure Stack](azure-stack-version-profiles.md).