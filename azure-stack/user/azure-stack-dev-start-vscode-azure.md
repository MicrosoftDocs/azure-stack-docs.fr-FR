---
title: Se connecter à Azure Stack en utilisant une extension de compte Azure dans Visual Studio Code | Microsoft Docs
description: En tant que développeur, connectez-vous à Azure Stack en utilisant une extension de compte Azure dans Visual Studio Code
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: Howto
ms.date: 05/31/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: 8e64a570ab45e57e3cf58639bc2ec23d9b9bd81b
ms.sourcegitcommit: 07cc716d97bf484c7260eb165ae205ae25e09589
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66453578"
---
# <a name="connect-to-azure-stack-using-azure-account-extension-in-visual-studio-code"></a>Se connecter à Azure Stack en utilisant une extension de compte Azure dans Visual Studio Code

Dans cet article, nous vous expliquerons comment vous connecter à Azure Stack en utilisant l’extension de compte Azure. Vous devez mettre à jour vos paramètres Visual Studio Code (VS Code).

VS Code est un éditeur léger permettant de générer et de déboguer des applications web et cloud. Il est utilisé par ASP.NET Core, Python, NodeJS, Go et d’autres développeurs. L’extension de compte Azure vous permet d’utiliser une seule connexion à Azure avec un filtrage d’abonnement pour des extensions Azure supplémentaires. L’extension rend Azure Cloud Shell disponible dans le terminal intégré à VS Code. À l’aide de l’extension, vous pouvez vous connecter à votre abonnement Azure Stack à l’aide d’Azure AD et des services AD FS (Active Directory Federation Services) pour Identity Manager. Cela vous permet de vous connecter à Azure Stack, de sélectionner votre abonnement et d’ouvrir une nouvelle ligne de commande dans Cloud Shell. 

> [!Note]  
> Vous pouvez utiliser les étapes décrites dans cet article pour un environnement AD FS (Active Directory Federation Services). Utilisez vos informations d’identification AD FS et les points de terminaison.

## <a name="pre-requisites-for-the-azure-account-extension"></a>Prérequis pour l’extension de compte Azure

1. Environnement Azure Stack, build 1904 ou ultérieure
2. [Visual Studio Code](https://code.visualstudio.com/)
3. [Extension de compte Azure](https://github.com/Microsoft/vscode-azure-account)
4. [Abonnement Azure Stack](https://azure.microsoft.com/overview/azure-stack/)

## <a name="steps-to-connect-to-azure-stack"></a>Procédure de connexion à Azure Stack

1. Ouvrez Visual Studio Code.

2. Sélectionnez **Extensions** dans le coin gauche.

3. Dans la zone de recherche, entrez `Azure Account`.

4. Sélectionnez **Compte Azure** et **Installer**.

      ![Visual Studio Code pour Azure Stack](media/azure-stack-dev-start-vscode-azure/image1.png)

5. Redémarrez VS Code pour charger l’extension.

6. Récupérez les métadonnées pour vous connecter à Azure Resource Manager dans Azure Stack. 
    
    Microsoft Azure Resource Manager est un framework de gestion qui vous permet de déployer, gérer et superviser les ressources Azure.
    - L’URL Resource Manager pour le kit de développement Azure Stack (ASDK) est : `https://management.local.azurestack.external/` 
    - L’URL Resource Manager pour un système intégré est : `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`
    - Ajoutez le texte suivant à votre URL pour accéder aux métadonnées : `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

    Par exemple, l’URL permettant de récupérer les métadonnées pour votre point de terminaison Azure Resource Manager peut ressembler à : `https://management.local.azurestack.external/metadata/endpoints?api-version=1.0`

    Notez le retour JSON. Vous aurez besoin des valeurs pour les propriétés `loginEndpoint` et `loginEndgraphEndpointpoint`.

7. Appuyez sur **Ctrl+Maj+P** et sélectionnez **Préférences : Ouvrir les paramètres utilisateur (JSON)** .

8. Dans l’éditeur de code, mettez à jour l’extrait de code JSON suivant avec les valeurs pour votre environnement, puis collez l’extrait dans le bloc de paramètres.

    - Valeurs :

        | Paramètre | Description |
        | --- | --- |
        | `tenant-ID` | La valeur de votre [ID de locataire](../operator/azure-stack-identity-overview.md) Azure Stack. |
        | `activeDirectoryEndpointUrl` | Il s’agit de l’URL de la propriété loginEndpoint. |
        | `activeDirectoryResourceId` | Il s’agit de l’URL de la propriété loginEndgraphEndpointpoint.
        | `resourceManagerEndpointUrl` | Il s’agit de l’URL racine pour Azure Resource Manager pour Azure Stack. | 

    - Extrait de code JSON :

      ```JSON  
      "azure.tenant": "tenant-ID",
      "azure.ppe": {
          "activeDirectoryEndpointUrl": "Login endpoint",
          "activeDirectoryResourceId": "graph audience",
          "resourceManagerEndpointUrl": "Management Endpoint",
      },
      "azure.cloud": "AzurePPE"
      ```

8. Enregistrez les paramètres utilisateur et utilisez **Ctrl+Maj+P** une nouvelle fois. Sélectionnez **Azure : Sign in to Azure Cloud** (Se connecter au cloud Azure). La nouvelle option, **AzurePPE**, apparaît dans la liste des cibles.

9. Sélectionnez **AzurePPE**. La page d’authentification se charge dans votre navigateur. Connectez-vous à votre point de terminaison.

11. Pour tester que vous êtes correctement connecté à votre abonnement Azure Stack, utilisez **Ctrl+Maj+P** et sélectionnez **Azure : Sélectionner un abonnement**, puis regardez si l’abonnement que vous avez est disponible.

## <a name="commands"></a>Commandes

| Azure : Sign In (Se connecter) | Connectez-vous à votre abonnement Azure : |
| --- | --- |
| Azure : Sign In with Device Code (Se connecter avec le code d’appareil) | Connectez-vous à votre abonnement Azure avec le code d’un appareil. Utilisez cette commande dans les configurations où la commande Sign In (Se connecter) ne fonctionne pas. |
| Azure : Sign in to Azure Cloud (Se connecter au cloud Azure) | Connectez-vous à votre abonnement Azure dans l’un des clouds souverains. |
| Azure : URL de déconnexion | Déconnectez-vous de votre abonnement Azure. |
| Azure : Sélectionner des abonnements | Choisissez l’ensemble des abonnements que vous souhaitez utiliser. L’extension affiche uniquement les ressources dans les abonnements filtrés. |
| Azure : Créer un compte | Si vous n’en avez pas, vous pouvez [vous inscrire](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-azure-account&mktingSource=vscode-azure-account) pour recevoir un compte Azure aujourd’hui ainsi que 200 \$ de crédits gratuits. |
| Azure : Open Bash in Cloud Shell (Ouvrir Bash dans Cloud Shell) | Ouvrez un nouveau terminal exécutant Bash dans Cloud Shell. |
| Azure : Open PowerShell in Cloud Shell (Ouvrir PowerShell dans Cloud Shell) | Ouvrez un nouveau terminal exécutant PowerShell dans Cloud Shell. |
| Azure : Upload to Cloud Shell (Charger sur Cloud Shell) | Chargez un fichier sur votre compte de stockage Cloud Shell. |

## <a name="next-steps"></a>Étapes suivantes

[Configurer un environnement de développement dans Azure Stack ](azure-stack-dev-start.md)