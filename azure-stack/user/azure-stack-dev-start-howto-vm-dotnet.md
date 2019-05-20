---
title: Déployer une application web ASP.NET C# sur une machine virtuelle dans Azure Stack | Microsoft Docs
description: Déployez une application web ASP.NET C# sur une machine virtuelle dans Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 3f925d7c6a7f08257dbcb054044403e1488d38cb
ms.sourcegitcommit: 41927cb812e6a705d8e414c5f605654da1fc6952
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "64482397"
---
# <a name="how-to-deploy-a-c-aspnet-web-app-to-a-vm-in-azure-stack"></a>Guide pratique pour déployer une application web ASP.NET C# sur une machine virtuelle dans Azure Stack

Vous pouvez créer une machine virtuelle pour héberger votre application web C# (ASP.NET) dans Azure Stack. Cet article explique étape par étape comment configurer le serveur, le paramétrer de façon à héberger une application web C# (ASP.NET), puis déployer directement l’application dans Visual Studio.

C# est un langage de programmation multiparadigme à usage général qui recouvre des disciplines de programmation à typage fort, à portée lexicale, impératives, déclaratives, fonctionnelles, génériques, orientées objet et orientées composant. Pour apprendre le langage de programmation C# et trouver des ressources supplémentaires sur C#, voir [Guide C#](https://docs.microsoft.com/dotnet/csharp/).

Cet article utilise une application C# 6.0 avec ASP.NET Core 2.2 sur un serveur Windows 2016.

## <a name="create-a-vm"></a>Créer une machine virtuelle

1. Créez une [machine virtuelle Windows Server](azure-stack-quick-windows-portal.md).

2. Exécutez le script suivant pour installer les composants sur votre machine virtuelle. Le script :
      - Installez IIS (avec la console de gestion).
      - Installez ASP.NET 4.6.

        ```PowerShell  
        # Install IIS (with Management Console)
        Install-WindowsFeature -name Web-Server -IncludeManagementTools
        
        # Install ASP.NET 4.6
        Install-WindowsFeature Web-Asp-Net45
        
        # Install Web Management Service
        Install-WindowsFeature -Name Web-Mgmt-Service
        ```

3. Téléchargez le fichier [MSI de déploiement web 3.6](https://www.microsoft.com/download/details.aspx?id=43717). Installez Web Deploy à partir du fichier MSI, puis activez sur toutes les fonctionnalités.

4. Installez le bundle d’hébergement .NET Core pour 2.2 sur votre serveur. Pour connaître les étapes à suivre, voir [Programme d’installation .NET Core](https://dotnet.microsoft.com/download/dotnet-core/2.2). Utilisez la même version de .NET Core sur votre ordinateur de développement et votre serveur cible.

5. Revenez au portail Azure Stack et ouvrez les ports dans les paramètres réseau de votre machine virtuelle.

    1. Ouvrez le portail Azure Stack de votre locataire.
    2. Localisez votre machine virtuelle. Vous l’avez peut-être épinglée sur votre tableau de bord ; sinon, vous pouvez la rechercher dans la zone **Rechercher des ressources**.
    3. Sélectionnez **Mise en réseau**.
    4. Sélectionnez **Ajouter une règle de port d’entrée** sous la machine virtuelle.
    1. Ajoutez une règle de sécurité de trafic entrant pour les ports suivants :

    | Port | Protocole | Description |
    | --- | --- | --- |
    | 80 | HTTP | Le protocole HTTP (Hypertext Transfer Protocol) est un protocole d’application pour les systèmes d’information hypermédias, collaboratifs et distribués. Les clients se connectent à l’application web avec l’adresse IP publique ou le nom DNS de la machine virtuelle. |
    | 443 | HTTPS | Le protocole HTTPS (Hypertext Transfer Protocol Secure) est une extension du protocole HTTP (Hypertext Transfer Protocol). Il sert à sécuriser la communication sur un réseau d’ordinateurs. Les clients se connectent à l’application web avec l’adresse IP publique ou le nom DNS de la machine virtuelle. |
    | 22 | SSH | Le protocole SSH (Secure Shell) est un protocole réseau de chiffrement qui permet d’exploiter en toute sécurité des services réseau sur un réseau non sécurisé. Nous utiliserons cette connexion avec un client SSH pour configurer la machine virtuelle et déployer l’application. |
    | 3389 | RDP | facultatif. Le protocole RDP (Remote Desktop Protocol) permet d’utiliser une connexion Bureau à distance avec une interface graphique utilisateur.   |

    Pour chaque port :

    1. Sélectionnez **Indifférent** comme Source.
    1. Tapez `*` comme plage de ports sources.
    1. Sélectionnez **Indifférent** comme **Destination**.
    1. Ajoutez le port que vous souhaitez ouvrir comme **Plage de ports de destination**.
    1. Sélectionnez **Indifférent** comme **Protocole**.
    1. Sélectionnez **Autoriser** comme **Action**.
    1. Laissez la valeur par défaut de **Priorité**.
    1. Donnez un **Nom** et une **Description** pour vous souvenir de la raison pour laquelle vous avez ouvert le port.
    1. Sélectionnez Ajouter.

5.  Dans les paramètres **Mise en réseau** de votre machine virtuelle dans Azure Stack, créez un nom DNS pour votre serveur. Les utilisateurs pourront se connecter à votre site web avec l’URL.

    1. Ouvrez le portail Azure Stack de votre locataire.
    1. Localisez votre machine virtuelle. Vous l’avez peut-être épinglée sur votre tableau de bord ; sinon, vous pouvez la rechercher dans la zone **Rechercher des ressources**.
    1. Sélectionnez **Vue d’ensemble**.
    1. Sélectionnez **Configurer** sous la machine virtuelle.
    1. Sélectionnez **Dynamique** comme **Attribution**.
    1. Tapez le nom DNS, par exemple `mywebapp`. Votre URL complète devrait se présenter ainsi : `mywebapp.local.cloudapp.azurestack.external`.

## <a name="create-an-app"></a>Créer une application 

Vous pouvez utiliser votre propre application web ou bien l’exemple issu de [Publier une application ASP.NET Core sur Azure avec Visual Studio](https://docs.microsoft.com/aspnet/core/tutorials/razor-pages/razor-pages-start?view=aspnetcore-2.2&tabs=visual-studio
).

L’article explique comment créer et publier une application web ASP.NET sur une machine virtuelle Azure avec la fonctionnalité de publication Machines virtuelles Microsoft Azure de Visual Studio 2017. Après avoir effectué l’installation et vérifié que votre application s’exécute localement, vous devrez remplacer votre cible de publication par la machine virtuelle Windows dans Azure Stack.

## <a name="deploy-and-run-the-app"></a>Déployer et exécuter l’application

Créez une cible de publication sur votre machine virtuelle dans Azure Stack.

1. Cliquez avec le bouton droit sur le projet dans l’Explorateur de solutions et sélectionnez **Publier**.

    ![Déployer une application web ASP.NET dans Azure Stack – Publier](media/azure-stack-dev-start-howto-vm-dotnet/deploy-app-to-azure-stack.png)

2.  Dans la fenêtre Publier, sélectionnez Nouveau profil.
3. Sélectionnez IIS, FTP, etc.
4. Sélectionnez Publier.

5.  Sélectionnez **Web Deploy** comme **Mode de publication**.
6.  Pour **Serveur**, entrez le nom DNS que vous avez défini tout à l’heure, par exemple `w21902.local.cloudapp.azurestack.external`.
7.  Pour **Nom de site**, tapez `Default Web Site`.
8.  Pour **Nom d’utilisateur**, tapez le nom d’utilisateur de la machine.
9.  Pour **Mot de passe**, tapez le mot de passe de la machine.
10. Pour **URL de destination**, tapez l’URL du site, par exemple `mywebapp.local.cloudapp.azurestack.external`.

    ![Déployer une application web ASP.NET – Configurer Web Deploy](media/azure-stack-dev-start-howto-vm-dotnet/configure-web-deploy.png)

9. Sélectionnez **Valider la connexion** pour valider la configuration Web Deploy. Cliquez ensuite sur **Suivant**.
10. Définissez votre **Configuration** sur **Production**.
11. Définissez **Version cible de .NET Framework** sur **netcoreapp2.2**.
12. Définissez **Runtime cible** sur **Portable**.
13. Sélectionnez **Enregistrer**.
14. Sélectionnez **Publier**.
15. Accédez à votre nouveau server. Vous devriez voir votre application web en cours d’exécution.

```HTTP  
    mywebapp.local.cloudapp.azurestack.external
```

## <a name="next-steps"></a>Étapes suivantes

- Apprenez à [Développer dans Azure Stack](azure-stack-dev-start.md).
- Découvrez les [Déploiements courants pour Azure Stack en IaaS](azure-stack-dev-start-deploy-app.md).