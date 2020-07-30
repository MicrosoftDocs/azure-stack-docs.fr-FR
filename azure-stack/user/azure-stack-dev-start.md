---
title: Configurez un environnement de développement dans Azure Stack Hub
description: Commencez à développer des applications pour Azure Stack Hub.
author: mattbriggs
ms.topic: overview
ms.date: 7/23/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: ac9e373881ef5d7c88a0397130fdfd560e41e352
ms.sourcegitcommit: f2a5ce52fcf69e05fe89be8211b7360de46f4a94
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133739"
---
# <a name="set-up-a-development-environment-in-azure-stack-hub"></a>Configurez un environnement de développement dans Azure Stack Hub 

Vous pouvez développer des applications pour Azure Stack Hub avec une station de travail Windows 10, Linux ou macOS. Dans cet article, nous allons répondre aux questions suivantes : 

- les différents contextes d’exécution d’une application dans Azure Stack Hub ; 
- les étapes à suivre pour configurer une station de travail Windows 10, Linux ou macOS ; 
- les étapes à suivre pour créer des ressources dans Azure Stack Hub et les déployer sur une application. 

## <a name="azure-stack-hub-context-and-your-code"></a>Contexte Azure Stack Hub et code 

Vous pouvez écrire des scripts et des applications qui peuvent effectuer de nombreuses tâches dans Azure Stack Hub. Toutefois, il est utile de limiter votre étendue aux trois modes suivants : 

1. Le premier mode permet de créer des applications qui provisionnent des ressources dans Azure Stack Hub avec des modèles Azure Resource Manager. Par exemple, vous pouvez écrire un script construisant un modèle Azure Resource Manager dont l’objectif est de créer un réseau virtuel et les machines virtuelles qui hébergent votre application. 

2. Dans le deuxième mode, il s’agit de travailler directement avec les points de terminaison à l’aide de l’API REST et d’un client REST créés dans le code. Vous pouvez par exemple écrire un script qui crée un réseau virtuel et les machines virtuelles en envoyant des demandes aux API. 

3. Dans le troisième mode, vous pouvez utiliser votre code pour créer une application hébergée dans Azure Stack Hub. Une fois que vous avez créé l’infrastructure dans Azure Stack Hub pour héberger votre application, déployez cette dernière dans l’infrastructure. Normalement, vous préparez l’environnement avant d’y déployer l’application. 

###  <a name="infrastructure-as-a-service-and-platform-as-a-service"></a>PaaS (Platform as a Service) et IaaS (Infrastructure as a Service) 

En tant produit de la plateforme cloud, Azure Stack Hub prend en charge deux modèles : 

- Infrastructure as a service (IaaS) 
- Platform as a service (PaaS) 

Les modèles IaaS et PaaS déterminent la configuration de l’ordinateur de développement. 

Le modèle IaaS correspond à la virtualisation de différentes parties du centre de données : équipement réseau, réseau et serveurs. C’est celui qui est utilisé pour déployer une application sur une machine virtuelle qui héberge le serveur web. Dans ce modèle, Azure Stack Hub gère l’équipement virtuel ; l’application se trouve sur un serveur virtuel. Les fournisseurs de ressources Azure Stack Hub prennent en charge les composants réseau et les serveurs virtuels. 

Le modèle PaaS abstrait la couche d’infrastructure de telle sorte que l’application est déployée sur un point de terminaison qui l’exécute ensuite. Dans le modèle PaaS, il est possible d’héberger l’application dans un conteneur pour ensuite la déployer sur un service qui exécute le conteneur, ou bien d’envoyer directement l’application au service qui l’exécute. Vous pouvez utiliser Azure Stack Hub pour exécuter Azure App Service et Kubernetes. 

### <a name="azure-stack-hub-resource-manager"></a>Azure Stack Hub Resource Manager 

Les trois modes mentionnés plus haut ainsi que les modèles PaaS et IaaS sont accessibles avec la version Azure Stack Hub d’Azure Resource Manager. Ce framework de gestion permet de déployer, de gérer et de superviser les ressources Azure Stack Hub. Elle offre la possibilité de travailler avec les ressources en tant que groupe en une seule opération. Pour plus d’informations sur l’utilisation d’Azure Stack Hub Resource Manager, consultez [Gérer les profils de version des API dans Azure Stack Hub](azure-stack-version-profiles.md). 

### <a name="azure-stack-hub-sdks"></a>Kits SDK Azure Stack Hub 

Azure Stack Hub utilise une version Azure Stack Hub d’Azure Resource Manager. Nous fournissons un certain nombre de kits SDK pour vous aider à travailler avec Azure Stack Hub Resource Manager en utilisant le code de votre choix, notamment : 

- [.NET/C#](azure-stack-version-profiles-net.md)
- [Java](azure-stack-version-profiles-java.md)
- [Go](azure-stack-version-profiles-go.md)
- [Ruby](azure-stack-version-profiles-ruby.md)
- [Python](azure-stack-version-profiles-python.md)
- [Node.JS](azure-stack-version-profile-nodejs.md)

## <a name="before-you-start"></a>Avant de commencer 

Avant de commencer la configuration de votre environnement, voici ce dont vous avez besoin : 

- Accédez au portail utilisateur Azure Stack Hub. 
- le nom de votre locataire ; 
- un gestionnaire d’identité au choix : soit Azure Active Directory (Azure AD), soit les Services de fédération Active Directory (AD FS). 

Pour toute question sur Azure Stack Hub, contactez votre opérateur cloud. 

## <a name="windows-10"></a>Windows 10 

Si vous utilisez un ordinateur Windows 10, vous pouvez utiliser PowerShell 5.0 et Visual Studio. Par ailleurs, si vous utilisez un Kit de développement Azure Stack (ASDK), vous pouvez vous connecter à votre environnement avec une connexion VPN. 

### <a name="set-up-your-tools"></a>Configurer les outils 

1. Configurez PowerShell. Pour obtenir des instructions, consultez [Installer Azure Stack Hub PowerShell](../operator/azure-stack-powershell-install.md). 

2. Téléchargez les outils Azure Stack Hub. Pour obtenir des instructions, consultez [Télécharger les outils Azure Stack Hub à partir de GitHub](../operator/azure-stack-powershell-download.md). 

3. Si vous utilisez un Kit ASDK, installez et configurez une [connexion VPN à Azure Stack Hub](azure-stack-connect-azure-stack.md#connect-to-azure-stack-hub-with-vpn). 

4. Installez et configurez Azure CLI. Pour obtenir des instructions, consultez [Utiliser des profils de version d’API avec Azure CLI dans Azure Stack Hub](azure-stack-version-profiles-azurecli2.md). 

5. Installez et configurez l’Explorateur Stockage Azure. Il s’agit d’une application autonome qui permet de travailler sur des données de stockage Azure Stack Hub. Pour obtenir des instructions, consultez [Connecter l’Explorateur Stockage à un compte de stockage ou abonnement Azure Stack Hub](azure-stack-storage-connect-se.md). 

### <a name="install-your-integrated-development-environment"></a>Installer un environnement de développement intégré 

1. Installez votre environnement de développement intégré (IDE) en fonction de votre codebase et de vos préférences. 

     - Visual Studio Code (Python, Go, Node.js). Téléchargez Visual Studio Code pour votre machine à l’adresse [code.visualstudio.com](https://code.visualstudio.com/Download). 
     - Visual Studio (.NET/C#). Téléchargez Visual Studio Community Edition à l’adresse [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/). 
     - Eclipse (Java). Téléchargez Eclipse à l’adresse [eclipse.org](https://www.eclipse.org/downloads/). 

2. Installez le SDK pour votre code : 

     - [.NET/C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="linux"></a>Linux 

Si vous utilisez un ordinateur Linux, vous pouvez utiliser Azure CLI, Visual Studio Code ou l’environnement de développement intégré de votre choix. 

> [!Note]   
> Si vous utilisez un ordinateur Linux avec le Kit ASDK, votre ordinateur distant doit se trouver dans le même réseau que le kit. Vous ne pouvez pas vous connecter à l’aide d’un réseau privé virtuel. 

### <a name="set-up-your-tools"></a>Configurer les outils 

1. Installez et configurez Azure CLI. Pour obtenir des instructions, consultez [Utiliser des profils de version d’API avec Azure CLI dans Azure Stack Hub](azure-stack-version-profiles-azurecli2.md). 

2. Installez et configurez l’Explorateur Stockage Azure. Il s’agit d’une application autonome qui permet de travailler sur des données de stockage Azure Stack Hub. Pour obtenir des instructions, consultez [Connecter l’Explorateur Stockage à un compte de stockage ou abonnement Azure Stack Hub](azure-stack-storage-connect-se.md). 

### <a name="install-your-integrated-development-environment"></a>Installer un environnement de développement intégré 

1. Installez votre environnement de développement intégré (IDE) en fonction de votre codebase et de vos préférences. 

     - Visual Studio Code (Python, Go, Node.js). Téléchargez Visual Studio Code pour votre machine à l’adresse [code.visualstudio.com](https://code.visualstudio.com/Download). 
     - Visual Studio (.NET/C#). Téléchargez Visual Studio Community Edition à l’adresse [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/). 
     - Eclipse (Java). Téléchargez Eclipse à l’adresse [eclipse.org](https://www.eclipse.org/downloads/). 

2. Installez le SDK pour votre code : 

     - [.NET/C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="macos"></a>macOS 

Avec un ordinateur macOS, vous pouvez utiliser Azure CLI, Visual Studio Code ou l’environnement de développement intégré de votre choix. 

> [!Note]   
> Si vous utilisez un ordinateur macOS avec le Kit ASDK, votre ordinateur distant doit se trouver dans le même réseau que le kit. Vous ne pouvez pas vous connecter à l’aide d’un réseau privé virtuel. 

### <a name="set-up-your-tools"></a>Configurer les outils 

1. Installez et configurez Azure CLI. Pour obtenir des instructions, consultez [Utiliser des profils de version d’API avec Azure CLI dans Azure Stack Hub](azure-stack-version-profiles-azurecli2.md). 

2. Installez et configurez l’Explorateur Stockage Azure. Il s’agit d’une application autonome qui permet de travailler sur des données de stockage Azure Stack Hub. Pour obtenir des instructions, consultez [Connecter l’Explorateur Stockage à un compte de stockage ou abonnement Azure Stack Hub](azure-stack-storage-connect-se.md). 

### <a name="install-your-integrated-development-environment"></a>Installer un environnement de développement intégré 

1. Installez votre environnement de développement intégré (IDE) en fonction de votre codebase et de vos préférences. 

     - Visual Studio Code (Python, Go, Node.js). Téléchargez Visual Studio Code pour votre machine à l’adresse [code.visualstudio.com](https://code.visualstudio.com/Download). 
     - Visual Studio (.NET/C#). Téléchargez Visual Studio Community Edition à l’adresse [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/). 
     - Eclipse (Java). Téléchargez Eclipse à l’adresse [eclipse.org](https://www.eclipse.org/downloads/). 

2. Installez le SDK pour votre code : 

     - [.NET/C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md)
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="next-steps"></a>Étapes suivantes 

Pour déployer une application sur des ressources dans Azure Stack Hub, consultez [Déploiements courants pour Azure Stack Hub](azure-stack-dev-start-deploy-app.md).
