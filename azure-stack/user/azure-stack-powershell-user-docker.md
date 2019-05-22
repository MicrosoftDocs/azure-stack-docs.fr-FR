---
title: Utiliser Docker pour exécuter PowerShell avec Azure Stack | Microsoft Docs
description: Utiliser Docker pour exécuter PowerShell sur Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: article
ms.date: 04/25/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 0eacd2c5a058bca68e86f2d34df8d3cc91987c1c
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985544"
---
# <a name="use-docker-to-run-powershell"></a>Utiliser Docker pour exécuter PowerShell

Vous pouvez utiliser Docker pour créer des conteneurs Windows dans lesquels exécuter la version particulière de PowerShell qui est nécessaire pour utiliser les différentes interfaces. Vous devez utiliser des conteneurs Windows dans Docker.

## <a name="docker-prerequisites"></a>Prérequis pour Docker

1. Installez [Docker](https://docs.docker.com/install/).
2. Ouvrez une ligne de commande, telle que Powershell ou Bash, et tapez :

    ```bash
        Docker -version
    ```

3. Vous devez exécuter Docker à l’aide de conteneurs Windows qui nécessitent Windows 10. Lors de l’exécution de Docker, basculez vers les conteneurs Windows.

4. Vous devez exécuter Docker depuis une machine jointe au même domaine qu’Azure Stack. Si vous utilisez le kit ASDK, vous devez installer [le VPN sur votre machine distante](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn).

## <a name="service-principals-for-using-powershell"></a>Principaux de service pour l’utilisation de PowerShell

Vous avez besoin d’un principal de service dans votre locataire Azure AD pour utiliser PowerShell afin d’accéder aux ressources dans Azure Stack. Vous déléguez des autorisations avec contrôle d’accès en fonction du rôle utilisateur.

1. Configurez votre principal de service en suivant les instructions dans l’article [Permettre à des applications d’accéder à des ressources Azure Stack en créant des principaux de service](azure-stack-create-service-principals.md).
2. Prenez note de l’ID d’application, du secret et de votre ID de locataire.

## <a name="docker---azure-stack-api-profiles-module"></a>Docker - Module Profils d’API Azure Stack

Le fichier Dockerfile ouvre l’image Microsoft microsoft/windowsservercore, dans laquelle est installé Windows PowerShell 5.1. Le fichier charge ensuite NuGet, les modules Azure Stack PowerShell, puis télécharge les outils à partir d’Azure Stack Tools.

1. Téléchargez ce dépôt sous forme de fichier ZIP ou clonez le dépôt :  
[https://github.com/mattbriggs/azure-stack-powershell](https://github.com/mattbriggs/azure-stack-powershell)

2. Ouvrez le dossier du dépôt à partir de votre terminal.

3. Ouvrez une interface de ligne de commande dans votre dépôt et tapez :

    ```bash  
    docker build --tag azure-stack-powershell .
    ```

4. Lorsque l’image est créée, vous pouvez démarrer un conteneur interactif. Tapez :

    ```bash  
        docker run -it azure-stack-powershell powershell
    ```

5. L’interpréteur de commandes est prêt pour vos applets de commande.

    ```bash
    Windows PowerShell
    Copyright (C) 2016 Microsoft Corporation. All rights reserved.

    PS C:\>
    ```

6. Connectez-vous à votre système Azure Stack à l’aide du principal du service. Vous utilisez maintenant une invite PowerShell dans Docker. 

    ```Powershell
    $passwd = ConvertTo-SecureString <Secret> -AsPlainText -Force
    $pscredential = New-Object System.Management.Automation.PSCredential('<ApplicationID>', $passwd)
    Connect-AzureRmAccount -ServicePrincipal -Credential $pscredential -TenantId <TenantID>
    ```

   PowerShell retourne votre objet de compte :

    ```PowerShell  
    Account    SubscriptionName    TenantId    Environment
    -------    ----------------    --------    -----------
    <AccountID>    <SubName>       <TenantID>  AzureCloud
    ```

7. Testez votre connectivité en créant un groupe de ressources dans Azure Stack.

    ```PowerShell  
    New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
    ```

## <a name="next-steps"></a>Étapes suivantes

-  Consultez une vue d’ensemble d’[Azure Stack PowerShell sur Azure Stack](azure-stack-powershell-overview.md).
- Approfondissez votre connaissance des [Profils d’API pour PowerShell](azure-stack-version-profiles.md) sur Azure Stack.
- [Installez Azure Stack PowerShell](../operator/azure-stack-powershell-install.md).
- Découvrez la création des [Modèles Azure Resource Manager](azure-stack-develop-templates.md) pour la cohérence du cloud.