---
title: Utiliser Docker pour exécuter PowerShell dans Azure Stack Hub
description: Utiliser Docker pour exécuter PowerShell dans Azure Stack Hub
author: mattbriggs
ms.topic: how-to
ms.date: 8/17/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 8/17/2020
ms.openlocfilehash: c05f35a9ef5ad059bdf50d721acd2811fa908370
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90573732"
---
# <a name="use-docker-to-run-powershell-for-azure-stack-hub"></a>Utiliser Docker pour exécuter PowerShell pour Azure Stack Hub

Dans cet article, vous pouvez utiliser Docker pour créer un conteneur sur lequel exécuter la version de PowerShell nécessaire pour utiliser les différentes interfaces. Des instructions relatives à l’utilisation des modules AzureRM et des tout derniers modules Az sont disponibles. AzureRM nécessite un conteneur Windows. Az utilise un conteneur Linux.

## <a name="docker-prerequisites"></a>Prérequis pour Docker

### <a name="install-docker"></a>Installation de Docker

1. Installez [Docker](https://docs.docker.com/install/).

1. Dans un programme en ligne de commande, tel que PowerShell ou Bash, entrez :

    ```bash
    docker --version
    ```

### <a name="set-up-a-service-principal-for-using-powershell"></a>Configurer un principal de service pour l’utilisation de PowerShell

Pour utiliser PowerShell afin d’accéder aux ressources dans Azure Stack Hub, vous avez besoin d’un principal de service dans votre locataire Azure AD (Azure Active Directory). Vous déléguez des autorisations avec contrôle d’accès en fonction du rôle (RBAC) utilisateur. Vous devrez peut-être demander le principal de service à votre opérateur cloud.

1. Pour configurer votre principal de service, suivez les instructions dans [Permettre à des applications d’accéder à des ressources Azure Stack Hub en créant des principaux de service](../operator/azure-stack-create-service-principals.md?view=azs-2002).

2. Notez l’ID d’application, le secret, votre ID de locataire et l’ID d’objet pour les utiliser ultérieurement.

## <a name="run-powershell-in-docker"></a>Exécuter PowerShell dans Docker

### <a name="azurerm-modules"></a>[Modules AzureRM](#tab/rm)

Dans ces instructions, vous allez exécuter une image de conteneur Windows et installer PowerShell ainsi que les modules nécessaires pour Azure Stack Hub.

1. Vous devez exécuter Docker à l’aide de conteneurs Windows qui nécessitent Windows 10. Lors de l’exécution de Docker, basculez vers les conteneurs Windows. Les images prenant en charge le module Az nécessitent Docker 17.05 ou une version plus récente.

1. Exécutez Docker depuis une machine jointe au même domaine qu’Azure Stack Hub. Si vous utilisez le kit de développement Azure Stack (ASDK), vous devez installer [le VPN sur votre machine distante](azure-stack-connect-azure-stack.md#connect-to-azure-stack-hub-with-vpn).

### <a name="install-azure-stack-hub-azurerm-module-on-a-windows-container"></a>Installer le module AzureRM d’Azure Stack Hub sur un conteneur Windows

Le fichier Dockerfile ouvre l’image Microsoft *microsoft/windowsservercore* dans laquelle est installé Windows PowerShell 5.1. Le fichier charge ensuite NuGet et les modules Azure Stack Hub PowerShell, puis télécharge les outils à partir des outils Azure Stack Hub.

1. [Téléchargez le dépôt azure-stack-powershell](https://github.com/Azure-Samples/azure-stack-hub-powershell-in-docker.git) sous forme de fichier ZIP ou clonez le dépôt.

2. Ouvrez le dossier du dépôt à partir de votre terminal.

3. Ouvrez une interface de ligne de commande dans votre dépôt et entrez la commande suivante :

    ```bash  
    docker build --tag azure-stack-powershell .
    ```

4. Lorsque l’image a été créée, démarrez un conteneur interactif en entrant :

    ```bash  
    docker run -it azure-stack-powershell powershell
    ```

    Notez le nom de votre conteneur. Vous pouvez utiliser le même conteneur, plutôt que de créer un conteneur à chaque fois, en exécutant la commande Docker suivante :

    ```bash  
        docker exec -it "Container name" powershell
    ```

5. L’interpréteur de commandes est prêt pour vos applets de commande.

    ```bash
    Windows PowerShell
    Copyright (C) 2016 Microsoft Corporation. All rights reserved.

    PS C:\>
    ```

6. Connectez-vous à votre instance Azure Stack Hub à l’aide du principal de service. Vous utilisez maintenant une invite PowerShell dans Docker. 

    ```powershell
    $passwd = ConvertTo-SecureString <Secret> -AsPlainText -Force
    $pscredential = New-Object System.Management.Automation.PSCredential('<ApplicationID>', $passwd)
    Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint <Your Azure Resource Manager endoint>
    Add-AzureRmAccount -EnvironmentName "AzureStackUser" -TenantId <TenantID> -ServicePrincipal -Credential $pscredential
    ```

   PowerShell retourne votre objet de compte :

    ```powershell  
    Account    SubscriptionName    TenantId    Environment
    -------    ----------------    --------    -----------
    <AccountID>    <SubName>       <TenantID>  AzureCloud
    ```

7. Testez votre connectivité en créant un groupe de ressources dans Azure Stack Hub.

    ```powershell  
    New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
    ```

### <a name="az-modules"></a>[Modules Az](#tab/az)

Dans ces instructions, vous allez exécuter une image de conteneur Linux qui contient PowerShell et les modules nécessaires pour Azure Stack Hub.

1. Vous devez exécuter Docker au moyen d’un conteneur Linux. Lors de l’exécution de Docker, basculez vers les conteneurs Linux.

1. Exécutez Docker depuis une machine jointe au même domaine qu’Azure Stack Hub. Si vous utilisez le kit de développement Azure Stack (ASDK), vous devez installer [le VPN sur votre machine distante](azure-stack-connect-azure-stack.md#connect-to-azure-stack-hub-with-vpn).


## <a name="install-azure-stack-hub-az-module-on-a-linux-container"></a>Installer le module Az d’Azure Stack Hub sur un conteneur Linux

1. À partir de votre ligne de commande, exécutez la commande Docker suivante pour exécuter PowerShell dans un conteneur Ubuntu :

    ```bash
    docker run -it mcr.microsoft.com/azurestack/powershell
    ```

    Vous pouvez exécuter Ubuntu, Debian ou Centos. Les fichiers Docker suivants sont disponibles dans le dépôt GitHub, [azurestack-powershell](https://github.com/Azure/azurestack-powershell). Consultez le dépôt GitHub pour obtenir les dernières modifications apportées aux fichiers Docker. Chaque système d’exploitation est marqué. Remplacez l’étiquette, la partie après les deux-points, par l’étiquette du système d’exploitation souhaité.

    | Linux | Image Docker |
    | --- | --- |
    | Ubuntu | `docker run -it mcr.microsoft.com/azurestack/powershell:ubuntu-18.04` |
    | Debian | `docker run -it mcr.microsoft.com/azurestack/powershell:debian-9` |
    | Centos | `docker run -it mcr.microsoft.com/azurestack/powershell:centos-7` |

2. L’interpréteur de commandes est prêt pour vos applets de commande. Testez la connectivité de l’interpréteur de commandes en vous connectant, puis en exécutant `Test-AzureStack.ps1`.

    Tout d’abord, créez les informations d’identification du principal de service. Vous aurez besoin du **secret** et de l’**ID d’application**. Vous aurez également besoin de l’**ID d’objet** lors de l’exécution de `Test-AzureStack.ps1` pour vérifier votre conteneur. Vous devrez peut-être demander un principal de service à votre opérateur cloud.

    Tapez les applets de commande suivantes pour créer un objet principal de service :

    ```powershell  
    $passwd = ConvertTo-SecureString <Secret> -AsPlainText -Force
    $pscredential = New-Object System.Management.Automation.PSCredential('<ApplicationID>', $passwd)
    ```

5. Connectez-vous à votre environnement en exécutant le script ci-après avec les valeurs suivantes, depuis votre instance Azure Stack Hub.

    | Valeur | Description |
    | --- | --- |
    | Nom de l’environnement. | Nom de votre environnement Azure Stack Hub. |
    | Point de terminaison Resource Manager | URL pour Resource Manager. Contactez votre opérateur cloud si vous ne la connaissez pas. Elle ressemble à ceci : `https://management.region.domain.com` | 
    | ID d’annuaire du locataire | ID de votre annuaire de locataire Azure Stack Hub. | 
    | Informations d'identification | Objet contenant votre principal de service. En l’occurrence, `$pscredential`.  |

    ```powershell
    ./Login-Environment.ps1 -Name <String> -ResourceManagerEndpoint <resource manager endpoint> -DirectoryTenantId <String> -Credential $pscredential
    ```

   PowerShell retourne votre objet de compte.

7. Testez votre environnement en exécutant le script `Test-AzureStack.ps1` dans le conteneur. Spécifiez l’**ID d’objet** du principal de service. Si vous n’indiquez pas l’ID d’objet, le script s’exécutera malgré tout, mais il testera uniquement les modules (utilisateur) du locataire et échouera sur les modules qui nécessitent des privilèges d’administrateur.

    ```powershell  
    ./Test-AzureStack.ps1 <Object ID>
    ```

---

## <a name="next-steps"></a>Étapes suivantes

- Consultez une vue d’ensemble d’[Azure Stack Hub PowerShell dans Azure Stack Hub](azure-stack-powershell-overview.md).
- Découvrez les [Profils d’API pour PowerShell](azure-stack-version-profiles.md) dans Azure Stack Hub.
- Installez [Azure Stack Hub PowerShell](../operator/azure-stack-powershell-install.md).
- Découvrez comment créer des [Modèles Azure Resource Manager](azure-stack-develop-templates.md) pour la cohérence du cloud.
