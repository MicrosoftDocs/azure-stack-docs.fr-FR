---
title: Configurations post-déploiement pour l’ASDK
description: Découvrez les changements de configuration qu’il est recommandé d’effectuer après avoir installé le kit de développement Azure Stack (ASDK).
author: justinha
ms.topic: article
ms.date: 07/31/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 07/31/2019
ms.openlocfilehash: 8c0cd14dbeda4e2b7844aefaf10f4a8cf685d60d
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77700593"
---
# <a name="post-deployment-configurations-for-asdk"></a>Configurations post-déploiement pour ASDK

Après avoir installé le [Kit de développement Azure Stack (ASDK)](asdk-install.md), vous devez effectuer certains changements de configuration recommandés pendant que vous êtes connecté en tant que AzureStack\AzureStackAdmin sur l’ordinateur hôte ASDK.

## <a name="install-azure-stack-powershell"></a>Installer Azure Stack PowerShell

Des modules Azure PowerShell compatibles avec Azure Stack sont nécessaires pour utiliser Azure Stack.

Les commandes PowerShell pour Azure Stack sont installées par le biais de PowerShell Gallery. Pour inscrire le référentiel PSGallery, ouvrez une session PowerShell avec élévation de privilèges, puis exécutez la commande suivante :

``` Powershell
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

Utilisez des profils de version d’API pour spécifier les modules AzureRM compatibles avec Azure Stack.  Les profils de version des API permettent de gérer les différences de version entre Azure et Azure Stack. Un profil de version d’API est un ensemble de modules PowerShell AzureRM avec des versions d’API spécifiques. Le module **AzureRM.BootStrapper**, qui est disponible via PowerShell Gallery, fournit les applets de commande PowerShell nécessaires pour utiliser des profils de version d’API.

Vous pouvez installer la dernière version du module Azure Stack PowerShell sur l’ordinateur hôte ASDK avec ou sans connexion Internet :

> [!IMPORTANT]
> Avant d’installer la version requise, vérifiez que vous avez [désinstallé tous les modules Azure PowerShell existants](../operator/azure-stack-powershell-install.md#3-uninstall-existing-versions-of-the-azure-stack-hub-powershell-modules).

- **Avec une connexion Internet** sur l’ordinateur hôte du kit ASDK : Exécutez le script PowerShell suivant pour installer ces modules dans le kit ASDK :


  ```powershell  
  Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose
  Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose

  # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
  Install-Module -Name AzureRM.BootStrapper

  # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
  Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
  Install-Module -Name AzureStack -RequiredVersion 1.8.0
  ```

  Si l’installation réussit, les modules AzureRM et AzureStack sont affichés dans la sortie.

- **Sans connexion Internet** sur l’ordinateur hôte du kit ASDK : Dans un scénario hors connexion, vous devez d’abord télécharger les modules PowerShell sur un ordinateur qui dispose d’une connexion Internet à l’aide des commandes PowerShell suivantes :

  ```powershell
  $Path = "<Path that is used to save the packages>"

  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
  
  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
  ```

  Ensuite, copiez les packages téléchargés sur l’ordinateur du kit ASDK, inscrivez cet emplacement comme référentiel par défaut, puis installez les modules AzureRM et AzureStack à partir de ce référentiel :

    ```powershell  
    $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
    $RepoName = "MyNuGetSource"

    Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

    Install-Module AzureRM -Repository $RepoName

    Install-Module AzureStack -Repository $RepoName
    ```

## <a name="download-the-azure-stack-tools"></a>Télécharger les outils Azure Stack

[AzureStack-Tools](https://github.com/Azure/AzureStack-Tools) est un dépôt GitHub qui héberge des modules PowerShell permettant de gérer et déployer des ressources sur Azure Stack. Pour obtenir ces outils, clonez le référentiel GitHub ou téléchargez le dossier AzureStack-Tools en exécutant le script suivant :

  ```powershell
  # Change directory to the root directory.
  cd \

  # Enforce usage of TLSv1.2 to download the Azure Stack tools archive from GitHub
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
  Invoke-WebRequest `
    -Uri https://github.com/Azure/AzureStack-Tools/archive/master.zip `
    -OutFile master.zip

  # Expand the downloaded files.
  Expand-Archive -Path master.zip -DestinationPath . -Force

  # Change to the tools directory.
  cd AzureStack-Tools-master
  ```

## <a name="validate-the-asdk-installation"></a>Valider l’installation du kit ASDK

Pour garantir le succès du déploiement du kit ASDK, utilisez l’applet de commande Test-AzureStack en procédant aux étapes suivantes :

1. Connectez-vous en tant qu’AzureStack\AzureStackAdmin sur l’ordinateur hôte du kit ASDK.
2. Ouvrez PowerShell en tant qu’administrateur (et non PowerShell ISE).
3. Exécutez l’instruction suivante : `Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint`
4. Exécutez l’instruction suivante : `Test-AzureStack`

L’exécution de ces tests nécessite quelques minutes. Si l’installation a réussi, la sortie ressemble à ceci :

![Tester Azure Stack - Installation réussie](media/asdk-post-deploy/test-azurestack.png)

En cas d’échec, suivez les étapes de dépannage.

## <a name="enable-multi-tenancy"></a>Activer l’architecture mutualisée

Pour les déploiements à l’aide d’Azure AD, vous devez [activer la mutualisation](../operator/azure-stack-enable-multitenancy.md#enable-multi-tenancy) pour votre installation d’ASDK.

> [!NOTE]
> Lorsque des comptes d’administrateur ou d’utilisateur de domaines autres que celui utilisé pour inscrire Azure Stack sont utilisés pour se connecter à un portail Azure Stack, le nom de domaine utilisé pour inscrire Azure Stack doit être ajouté à l’URL du portail. Par exemple, si Azure Stack a été inscrit auprès de fabrikam.onmicrosoft.com et que la connexion du compte d’utilisateur est admin@contoso.com, l’URL à utiliser pour se connecter au portail de l’utilisateur est la suivante : https\://portal.local.azurestack.external/fabrikam.onmicrosoft.com.

## <a name="next-steps"></a>Étapes suivantes

[Inscrire le kit ASDK auprès d’Azure](asdk-register.md)
