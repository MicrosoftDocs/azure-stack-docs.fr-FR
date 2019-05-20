---
title: Configurer un environnement de développement dans Azure Stack | Microsoft Docs
description: Commencez à développer des applications pour Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/25/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 9b3d58e67d7c6ca7016b3ecb51c09171aae7c06a
ms.sourcegitcommit: 0d8ccf2a32b08ab9bcbe13d54c7c3dce2379757f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "64490034"
---
# <a name="set-up-a-development-environment-on-azure-stack"></a>Configurer un environnement de développement dans Azure Stack 

Il est possible de développer des applications pour Azure Stack avec une station de travail Windows 10, Linux ou macOS. Cet article explore plusieurs points : 

- les différents contextes d’exécution d’une application dans Azure Stack ; 
- les étapes à suivre pour configurer une station de travail Windows 10, Linux ou macOS ; 
- les étapes à suivre pour créer des ressources dans Azure Stack et déployer une application. 

## <a name="azure-stack-context-and-your-code"></a>Contexte Azure Stack et code 

Il est possible d’écrire des scripts et des applications qui peuvent tout faire dans Azure Stack. Toutefois, il peut se révéler utile de restreindre les actions en trois modes différents. 

1. Le premier mode permet de créer des applications qui provisionnent des ressources dans Azure Stack avec des modèles Azure Resource Manager. Par exemple, vous pouvez écrire un script construisant un modèle Azure Resource Manager dont l’objectif est de créer un réseau virtuel et les machines virtuelles qui hébergeront votre application. 

2. Dans le deuxième mode, il s’agit de travailler directement avec les points de terminaison à l’aide de l’API REST et d’un client REST créé dans le code. Vous pourriez écrire un script qui crée un réseau virtuel et les machines virtuelles en envoyant des demandes aux API. 

3. Dans le troisième mode, le code vise à créer une application hébergée dans Azure Stack. Une fois l’infrastructure définie dans Azure Stack pour héberger votre application, déployez cette dernière dans l’infrastructure. On prépare en général l’environnement avant d’y déployer l’application. 

###  <a name="infrastructure-as-a-service-and-platform-as-a-service"></a>Infrastructure as a service et platform as a service 

Dans sa forme cloud, Azure Stack prend en charge deux modèles : 

- infrastructure as a service (IaaS) ; 
- platform as a service (PaaS). 

Les modèles IaaS et PaaS déterminent la configuration de l’ordinateur de développement. 

Le modèle IaaS correspond à la virtualisation de différentes parties du centre de données : équipement réseau, réseau et serveurs. C’est celui qui est utilisé pour déployer une application sur une machine virtuelle qui héberge le serveur web. Dans ce paradigme, Azure Stack gère l’équipement virtuel ; l’application se trouve sur un serveur virtuel. Les fournisseurs de ressources Azure Stack prennent en charge les composants réseau et les serveurs virtuels. 

Le modèle PaaS extrait la couche d’infrastructure de telle sorte que l’application est déployée sur un point de terminaison qui l’exécutera ensuite. Dans le paradigme PaaS, il est possible d’héberger l’application dans un conteneur pour ensuite la déployer sur un service qui exécute le conteneur, ou bien d’envoyer directement l’application au service qui l’exécutera. Azure Stack peut être utilisé pour exécuter Azure App Service et Kubernetes. 

### <a name="azure-stack-resource-manager"></a>Azure Stack Resource Manager 

Ces trois modes ainsi que les modèles PaaS et IaaS sont accessibles avec la version Azure Stack d’Azure Resource Manager. L’infrastructure de gestion permet de déployer, de gérer et de surveiller les ressources Azure Stack. Elle offre la possibilité de travailler avec ces éléments en tant que groupe en une seule opération. Pour plus d’informations sur Azure Stack Resource Manager, voir [Gérer les profils de version des API dans Azure Stack](azure-stack-version-profiles.md). 

### <a name="azure-stack-development-kits"></a>Kits de développement Azure Stack 

Azure Stack utilise une version Azure Stack d’Azure Resource Manager.  Nous fournissons un certain nombre de kits de développement logiciel pour vous aider à travailler avec Azure Stack Resource Manager en utilisant le code de votre choix. Il s’agit des actions suivantes : 

- [.NET/C#](azure-stack-version-profiles-net.md)
- [Java](azure-stack-version-profiles-java.md)
- [Go](azure-stack-version-profiles-go.md)
- [Ruby](azure-stack-version-profiles-ruby.md)
- [Python](azure-stack-version-profiles-python.md)

## <a name="before-you-start"></a>Avant de commencer 

Éléments requis : 

- l’accès au portail utilisateur Azure Stack ; 
- le nom de votre locataire ; 
- un gestionnaire d’identité au choix : soit Azure Active Directory (Azure AD), soit les services de fédération Active Directory (AD FS). 

Pour toute question sur votre système Azure Stack, contactez votre opérateur cloud. 

## <a name="windows-10"></a>Windows 10 

Un ordinateur Windows 10 permet de travailler avec PowerShell 5.0, Visual Studio et, dans le cas d’un kit ASDK, de se connecter à l’environnement par VPN. 

### <a name="set-up-your-tools"></a>Configurer les outils 

1. Configurez PowerShell. Pour obtenir des instructions, suivez les étapes de [Installer PowerShell d’Azure Stack](../operator/azure-stack-powershell-install.md). 

2. Téléchargez les outils Azure Stack. Pour obtenir des instructions, suivez les étapes de [Télécharger les outils Azure Stack sur GitHub](../operator/azure-stack-powershell-download.md). 

3. Si vous utilisez un ASDK, installez et configurez une [connexion VPN à Azure Stack](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn). 

4. Installez et configurez Azure CLI. Pour obtenir des instructions, suivez les étapes de [Utiliser des profils de version des API avec Azure CLI dans Azure Stack](azure-stack-version-profiles-azurecli2.md). 

5. Installez et configurez l’Explorateur Stockage Azure. Il s’agit d’une application autonome qui permet de travailler sur des données de stockage Azure Stack.  Pour obtenir des instructions, suivez les étapes de [Connecter l’Explorateur Stockage à un compte de stockage ou à un abonnement Azure Stack](azure-stack-storage-connect-se.md). 

### <a name="install-your-integrated-development-environment"></a>Installer un environnement de développement intégré 

1. Installez votre environnement de développement intégré (IDE) en fonction de votre codebase et de vos préférences. 

     - Visual Studio Code (Python, Go, Node.js). Téléchargez Visual Studio Code pour votre machine à l’adresse [code.visualstudio.com](https://code.visualstudio.com/Download). 
     - Visual Studio (.NET/C#). Téléchargez Visual Studio Community Edition à l’adresse [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/). 
     - Eclipse (Java). Téléchargez Eclipse à l’adresse [eclipse.org](https://www.eclipse.org/downloads/). 

2. Installez le kit de développement logiciel (SDK) pour votre code. 

     - [.NET/C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="linux"></a>Linux 

Un ordinateur Linux permet de travailler avec Azure CLI et Visual Studio Code, ou avec n’importe quel environnement de développement intégré. 

> [!Note]   
> Si vous utilisez un ordinateur Linux avec le kit ASDK, votre ordinateur distant doit se trouver dans le même réseau que le kit. Vous ne pourrez pas vous connecter par réseau privé virtuel. 

### <a name="set-up-your-tools"></a>Configurer les outils 

1. Installez et configurez Azure CLI. Pour obtenir des instructions, suivez les étapes de [Utiliser des profils de version des API avec Azure CLI dans Azure Stack](azure-stack-version-profiles-azurecli2.md). 

2. Installez et configurez l’Explorateur Stockage Azure. Il s’agit d’une application autonome qui permet de travailler sur des données de stockage Azure Stack.  Pour obtenir des instructions, suivez les étapes de [Connecter l’Explorateur Stockage à un compte de stockage ou à un abonnement Azure Stack](azure-stack-storage-connect-se.md). 

### <a name="install-your-integrated-development-environment"></a>Installer un environnement de développement intégré 

1. Installez votre environnement de développement intégré (IDE) en fonction de votre codebase et de vos préférences. 

     - Visual Studio Code (Python, Go, Node.js). Téléchargez Visual Studio Code pour votre machine à l’adresse [code.visualstudio.com](https://code.visualstudio.com/Download). 
     - Visual Studio (.NET/C#). Téléchargez Visual Studio Community Edition à l’adresse [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/). 
     - Eclipse (Java). Téléchargez Eclipse à l’adresse [eclipse.org](https://www.eclipse.org/downloads/). 

2. Installez le kit de développement logiciel (SDK) pour votre code. 

     - [.NET/C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="macos"></a>macOS 

Un ordinateur macOS permet de travailler avec Azure CLI et Visual Studio Code, ou avec n’importe quel environnement de développement intégré. 

> [!Note]   
> Si vous utilisez un ordinateur macOS avec le kit ASDK, votre ordinateur distant doit se trouver dans le même réseau que le kit. Vous ne pourrez pas vous connecter par réseau privé virtuel. 

### <a name="set-up-your-tools"></a>Configurer les outils 

1. Installez et configurez Azure CLI. Pour obtenir des instructions, suivez les étapes de [Utiliser des profils de version des API avec Azure CLI dans Azure Stack](azure-stack-version-profiles-azurecli2.md). 

2. Installez et configurez l’Explorateur Stockage Azure. Il s’agit d’une application autonome qui permet de travailler sur des données de stockage Azure Stack. Pour obtenir des instructions, suivez les étapes de [Connecter l’Explorateur Stockage à un compte de stockage ou à un abonnement Azure Stack](azure-stack-storage-connect-se.md). 

### <a name="install-your-integrated-development-environment"></a>Installer un environnement de développement intégré 

1. Installez votre environnement de développement intégré (IDE) en fonction de votre codebase et de vos préférences. 

     - Visual Studio Code (Python, Go, Node.js). Téléchargez Visual Studio Code pour votre machine à l’adresse [code.visualstudio.com](https://code.visualstudio.com/Download). 
     - Visual Studio (.NET/C#). Téléchargez Visual Studio Community Edition à l’adresse [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/). 
     - Eclipse (Java). Téléchargez Eclipse à l’adresse [eclipse.org](https://www.eclipse.org/downloads/). 

2. Installez le kit de développement logiciel (SDK) pour votre code. 

     - [.NET/C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="next-steps"></a>Étapes suivantes 

Déployer une application dans des ressources dans Azure Stack. Vous trouverez les étapes à suivre dans [Déploiements courants pour Azure Stack](azure-stack-dev-start-deploy-app.md).