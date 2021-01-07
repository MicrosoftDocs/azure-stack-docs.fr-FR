---
title: Configurations post-déploiement pour l’ASDK
description: Découvrez les changements de configuration qu’il est recommandé d’effectuer après avoir installé le kit de développement Azure Stack (ASDK).
author: PatAltimore
ms.topic: article
ms.date: 12/03/2020
ms.author: patricka
ms.reviewer: misainat
ms.lastreviewed: 12/03/2020
ms.openlocfilehash: f2e039e2b0934e17a4335753e787c6cdf3808644
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97873314"
---
# <a name="post-deployment-configurations-for-asdk"></a>Configurations post-déploiement pour ASDK

Après avoir installé le [Kit de développement Azure Stack (ASDK)](asdk-install.md), vous devez effectuer certains changements de configuration recommandés pendant que vous êtes connecté en tant que AzureStack\AzureStackAdmin sur l’ordinateur hôte ASDK.

## <a name="install-azure-stack-powershell"></a>Installer Azure Stack PowerShell

Des modules Azure PowerShell compatibles avec Azure Stack sont nécessaires pour utiliser Azure Stack.

Les commandes PowerShell pour Azure Stack sont installées par le biais de PowerShell Gallery. Pour inscrire le référentiel PSGallery, ouvrez une session PowerShell avec élévation de privilèges, puis exécutez la commande suivante :

``` Powershell
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

Utilisez des profils de version d’API pour spécifier les modules Az compatibles avec Azure Stack.  Les profils de version des API permettent de gérer les différences de version entre Azure et Azure Stack. Un profil de version d’API est un ensemble de modules PowerShell Az avec des versions d’API spécifiques. Le module **Az.BootStrapper**, qui est disponible via PowerShell Gallery, fournit les cmdlets PowerShell nécessaires pour utiliser des profils de version d’API.

Vous pouvez installer la dernière version du module Azure Stack PowerShell sur l’ordinateur hôte du kit ASDK avec ou sans connexion Internet.

1.  Vérifiez le respect des prérequis sur votre machine Windows. Pour obtenir des instructions, consultez [Prérequis pour Windows](../operator/powershell-install-az-module.md#prerequisites-for-windows).
2. Avant d’installer la version PowerShell nécessaire, veillez à [désinstaller les modules Azure PowerShell existants](../operator/powershell-install-az-module.md#3-uninstall-existing-versions-of-the-azure-stack-hub-powershell-modules). 

- **Avec une connexion Internet** sur l’ordinateur hôte du kit ASDK : Exécutez le script PowerShell suivant pour installer ces modules dans le kit ASDK :

### <a name="az-modules"></a>[Modules Az](#tab/az1)

  ```powershell  
    [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12

    Install-Module -Name Az.BootStrapper -Force -AllowPrerelease
    Install-AzProfile -Profile 2019-03-01-hybrid -Force
    Install-Module -Name AzureStack -RequiredVersion 2.0.2-preview -AllowPrerelease

    Get-Module -Name "Az*" -ListAvailable
    Get-Module -Name "Azs*" -ListAvailable
  ```

Si l’installation réussit, les modules Az et AzureStack sont affichés dans la sortie.

### <a name="azurerm-modules"></a>[Modules AzureRM](#tab/azurerm1)

  ```powershell  
    [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
    
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRM.BootStrapper
    
    # Install and import the API Version Profile required by Azure Stack Hub into the current PowerShell session.
    Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
    Install-Module -Name AzureStack -RequiredVersion 1.8.2
    
    Get-Module -Name "Az*" -ListAvailable
    Get-Module -Name "Azs*" -ListAvailable
  ```

Si l’installation réussit, les modules AzureRM et AzureStack sont affichés dans la sortie.

---

- **Sans connexion Internet** sur l’ordinateur hôte du kit ASDK : Dans un scénario hors connexion, vous devez d’abord télécharger les modules PowerShell sur un ordinateur qui dispose d’une connexion Internet à l’aide des commandes PowerShell suivantes :

### <a name="az-modules"></a>[Modules Az](#tab/az2)

  ```powershell
  $Path = "<Path that is used to save the packages>"

  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name Az -Path $Path -Force -RequiredVersion 2.3.0
  
  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
  ```

  Ensuite, copiez les packages téléchargés sur l’ordinateur du kit ASDK, inscrivez cet emplacement comme référentiel par défaut, puis installez les modules Az et AzureStack à partir de ce référentiel :

  ```powershell  
  $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
  $RepoName = "MyNuGetSource"

  Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

  Install-Module Az -Repository $RepoName

  Install-Module AzureStack -Repository $RepoName
  ```

### <a name="azurerm-modules"></a>[Modules AzureRM](#tab/azurerm2)

  ```powershell
  $Path = "<Path that is used to save the packages>"

  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.5.0
  
  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.8.0
  ```

  Ensuite, copiez les packages téléchargés sur l’ordinateur du kit ASDK, inscrivez cet emplacement comme référentiel par défaut, puis installez les modules AzureRM et AzureStack à partir de ce référentiel :

  ```powershell  
  $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
  $RepoName = "MyNuGetSource"

  Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

  Install-Module AzureRM -Repository $RepoName

  Install-Module AzureStack -Repository $RepoName
  ```

---

## <a name="download-the-azure-stack-tools"></a>Télécharger les outils Azure Stack

[AzureStack-Tools](https://github.com/Azure/AzureStack-Tools) est un dépôt GitHub qui héberge des modules PowerShell permettant de gérer et déployer des ressources sur Azure Stack. Vous utilisez les outils à l’aide des modules PowerShell Az ou des modules AzureRM.

### <a name="az-modules"></a>[Modules Az](#tab/az3)

Pour obtenir ces outils, clonez le dépôt GitHub à partir de la branche `az` ou téléchargez le dossier **AzureStack-Tools** en exécutant le script suivant :

```powershell
# Change directory to the root directory.
cd \

# Download the tools archive.
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/az.zip `
  -OutFile az.zip

# Expand the downloaded files.
expand-archive az.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
cd AzureStack-Tools-az

```
### <a name="azurerm-modules"></a>[Modules AzureRM](#tab/azurerm3)

Pour obtenir ces outils, clonez le dépôt GitHub à partir de la branche `master` ou téléchargez le dossier **AzureStack-Tools** en exécutant le script suivant dans une invite PowerShell avec élévation de privilèges :

```powershell
# Change directory to the root directory.
cd \

# Download the tools archive.
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files.
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
cd AzureStack-Tools-master

```
Pour plus d’informations sur l’utilisation du module AzureRM pour Azure Stack Hub, consultez [Installer le module PowerShell AzureRM pour Azure Stack Hub](../operator/azure-stack-powershell-install.md).

---

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
