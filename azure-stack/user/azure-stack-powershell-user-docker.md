---
title: Utiliser Docker pour exécuter PowerShell dans Azure Stack | Microsoft Docs
description: Utiliser Docker pour exécuter PowerShell dans Azure Stack
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
ms.openlocfilehash: 42ed9766b43ce3c0c1c455d8ea286a5b453df325
ms.sourcegitcommit: 2ee75ded704e8cfb900d9ac302d269c54a5dd9a3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66394373"
---
# <a name="use-docker-to-run-powershell-in-azure-stack"></a>Utiliser Docker pour exécuter PowerShell dans Azure Stack

Dans cet article, vous utilisez Docker pour créer des conteneurs Windows sur lesquels exécuter la version de PowerShell nécessaire pour utiliser les différentes interfaces. Vous devez utiliser des conteneurs Windows dans Docker.

## <a name="docker-prerequisites"></a>Prérequis pour Docker

1. Installez [Docker](https://docs.docker.com/install/).

1. Dans un programme en ligne de commande, tel que Powershell ou Bash, entrez :

    ```bash
        Docker -version
    ```

1. Vous devez exécuter Docker à l’aide de conteneurs Windows qui nécessitent Windows 10. Lors de l’exécution de Docker, basculez vers les conteneurs Windows.

1. Exécutez Docker depuis une machine jointe au même domaine qu’Azure Stack. Si vous utilisez le kit de développement Azure Stack (ASDK), vous devez installer [le VPN sur votre machine distante](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn).

## <a name="set-up-a-service-principal-for-using-powershell"></a>Configurer un principal de service pour l’utilisation de PowerShell

Pour utiliser PowerShell afin d’accéder aux ressources dans Azure Stack, vous avez besoin d’un principal de service dans votre locataire Azure AD (Azure Active Directory). Vous déléguez des autorisations avec contrôle d’accès en fonction du rôle (RBAC) utilisateur.

1. Pour configurer votre principal de service, suivez les instructions dans [Permettre à des applications d’accéder à des ressources Azure Stack en créant des principaux de service](azure-stack-create-service-principals.md).

2. Notez l’ID d’application, le secret et votre ID de locataire pour une utilisation ultérieure.

## <a name="docker---azure-stack-api-profiles-module"></a>Docker - Module Profils d’API Azure Stack

Le fichier Dockerfile ouvre l’image Microsoft *microsoft/windowsservercore* dans laquelle est installé Windows PowerShell 5.1. Le fichier charge ensuite NuGet et les modules Azure Stack PowerShell, puis télécharge les outils à partir des outils Azure Stack.

1. [Téléchargez le dépôt azure-stack-powershell](https://github.com/mattbriggs/azure-stack-powershell) sous forme de fichier ZIP ou clonez le dépôt.

2. Ouvrez le dossier du dépôt à partir de votre terminal.

3. Ouvrez une interface de ligne de commande dans votre dépôt et entrez la commande suivante :

    ```bash  
    docker build --tag azure-stack-powershell .
    ```

4. Lorsque l’image a été créée, démarrez un conteneur interactif en entrant :

    ```bash  
        docker run -it azure-stack-powershell powershell
    ```

5. L’interpréteur de commandes est prêt pour vos applets de commande.

    ```bash
    Windows PowerShell
    Copyright (C) 2016 Microsoft Corporation. All rights reserved.

    PS C:\>
    ```

6. Connectez-vous à votre instance Azure Stack à l’aide du principal de service. Vous utilisez maintenant une invite PowerShell dans Docker. 

    ```powershell
    $passwd = ConvertTo-SecureString <Secret> -AsPlainText -Force
    $pscredential = New-Object System.Management.Automation.PSCredential('<ApplicationID>', $passwd)
    Connect-AzureRmAccount -ServicePrincipal -Credential $pscredential -TenantId <TenantID>
    ```

   PowerShell retourne votre objet de compte :

    ```powershell  
    Account    SubscriptionName    TenantId    Environment
    -------    ----------------    --------    -----------
    <AccountID>    <SubName>       <TenantID>  AzureCloud
    ```

7. Testez votre connectivité en créant un groupe de ressources dans Azure Stack.

    ```powershell  
    New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
    ```

## <a name="next-steps"></a>Étapes suivantes

-  Consultez une vue d’ensemble d’[Azure Stack PowerShell dans Azure Stack](azure-stack-powershell-overview.md).
- Découvrez les [Profils d’API pour PowerShell](azure-stack-version-profiles.md) dans Azure Stack.
- Installez [Azure Stack PowerShell](../operator/azure-stack-powershell-install.md).
- Découvrez comment créer des [Modèles Azure Resource Manager](azure-stack-develop-templates.md) pour la cohérence du cloud.
