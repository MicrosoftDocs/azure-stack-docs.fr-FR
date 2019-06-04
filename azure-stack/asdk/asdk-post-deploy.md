---
title: Configurations post-déploiement pour le kit de développement Azure Stack (ASDK) | Microsoft Docs
description: Décrit les changements de configuration qu’il est recommandé d’effectuer après avoir installé le kit de développement Azure Stack (ASDK).
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 10/10/2018
ms.openlocfilehash: 3d6b7ff0d52874fcbb058e87a737d7510aecc5db
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66267566"
---
# <a name="post-asdk-installation-configuration-tasks"></a>Tâches de configuration après l’installation du kit ASDK

Après avoir installé le [Kit de développement Azure Stack (ASDK)](asdk-install.md), vous devez effectuer certains changements de configuration recommandés pendant que vous êtes connecté en tant que AzureStack\AzureStackAdmin sur l’ordinateur hôte ASDK.

## <a name="install-azure-stack-powershell"></a>Installer Azure Stack PowerShell

Des modules Azure PowerShell compatibles avec Azure Stack sont nécessaires pour utiliser Azure Stack.

Les commandes PowerShell pour Azure Stack sont installées par le biais de PowerShell Gallery. Pour inscrire le référentiel PSGallery, ouvrez une session PowerShell avec élévation de privilèges, puis exécutez la commande suivante :

``` Powershell
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

Vous pouvez utiliser des profils de version d’API pour spécifier les modules AzureRM compatibles avec Azure Stack.  Les profils de version des API permettent de gérer les différences de version entre Azure et Azure Stack. Un profil de version d’API est un ensemble de modules PowerShell AzureRM avec des versions d’API spécifiques. Le module **AzureRM.BootStrapper** qui est disponible par le biais de PowerShell Gallery fournit des applets de commande PowerShell nécessaires pour utiliser des profils de version d’API.

Vous pouvez installer la dernière version du module Azure Stack PowerShell sur l’ordinateur hôte ASDK avec ou sans connexion Internet :

> [!IMPORTANT]
> Avant d’installer la version requise, vérifiez que vous avez [désinstallé tous les modules Azure PowerShell existants](../operator/azure-stack-powershell-install.md#3-uninstall-existing-versions-of-the-azure-stack-powershell-modules).

- **Avec une connexion Internet** sur l’ordinateur hôte du kit ASDK. Exécutez le script PowerShell suivant pour installer ces modules dans le kit de développement :

  - Pour les builds 1904 ou ultérieures :

    ```powershell  
      # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
      Install-Module -Name AzureRM.BootStrapper

      # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
      Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
      Install-Module -Name AzureStack -RequiredVersion 1.7.2
    ```

  - Azure Stack version 1903 ou antérieure, installez uniquement les deux modules ci-dessous :

    ```powershell
    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Install-Module -Name AzureRM -RequiredVersion 2.4.0
    Install-Module -Name AzureStack -RequiredVersion 1.7.1
    ```

    > [!Note]  
    > La version du module Azure Stack 1.7.1 est un changement cassant. Pour migrer à partir d’Azure Stack 1.6.0, consultez le [guide de migration](https://aka.ms/azspshmigration171).

  - Azure Stack 1811 :

    ``` PowerShell
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet.
    Install-Module -Name AzureRM.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    # Install Azure Stack Module Version 1.6.0.
    Install-Module -Name AzureStack -RequiredVersion 1.6.0
    ```

  Si l’installation réussit, les modules AzureRM et AzureStack sont affichés dans la sortie.

- **Sans connexion Internet** sur l’ordinateur hôte du kit ASDK. Dans un scénario hors connexion, vous devez d’abord télécharger les modules PowerShell sur un ordinateur qui dispose d’une connexion Internet à l’aide des commandes PowerShell suivantes :

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

Pour garantir le succès du déploiement du kit ASDK, vous pouvez utiliser l’applet de commande Test-AzureStack en procédant aux étapes suivantes :

1. Connectez-vous en tant qu’AzureStack\AzureStackAdmin sur l’ordinateur hôte du kit ASDK.
2. Ouvrez PowerShell en tant qu’administrateur (et non PowerShell ISE).
3. Exécuter : `Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint`
4. Exécuter : `Test-AzureStack`

L’exécution de ces tests nécessite quelques minutes. Si l’installation a réussi, la sortie ressemble à ceci :

![test-azurestack](media/asdk-post-deploy/test-azurestack.png)

En cas d’échec, suivez les étapes de dépannage.

## <a name="reset-the-password-expiration-policy"></a>Réinitialiser la stratégie d’expiration du mot de passe

Pour faire en sorte que le mot de passe de l’hôte du kit de développement n’expire pas avant la fin de la période d’expiration, suivez ces étapes après avoir déployé l’ASDK.

### <a name="to-change-the-password-expiration-policy-from-powershell"></a>Pour modifier la stratégie d’expiration de mot de passe à partir de Powershell

À partir d’une console Powershell avec élévation de privilèges, exécutez la commande :

```powershell
Set-ADDefaultDomainPasswordPolicy -MaxPasswordAge 180.00:00:00 -Identity azurestack.local
```

### <a name="to-change-the-password-expiration-policy-manually"></a>Pour modifier la stratégie d’expiration de mot de passe manuellement

1. Sur l’hôte du kit de développement, ouvrez **Gestion des stratégies de groupe** (GPMC.MMC), puis accédez à **Gestion des stratégies de groupe** - **Forêt : azurestack.local** - **Domaines** - **azurestack.local**.
2. Cliquez avec le bouton droit sur **Stratégie de domaine par défaut**, puis cliquez sur **Modifier**.
3. Dans l’Éditeur de gestion de stratégie de groupe, accédez à **Configuration de l’ordinateur** -  **Stratégies** - **Paramètres Windows** - **Paramètres de sécurité** - **Stratégies de comptes** - **Stratégie de mot de passe**.
4. Dans le volet droit, double-cliquez sur **Antériorité maximale du mot de passe**.
5. Dans la boîte de dialogue **Antériorité maximale du mot de passe - Propriétés**, remplacez la valeur de **Le mot de passe expirera dans** par **180**, puis cliquez sur **OK**.

![Console de gestion des stratégies de groupe](media/asdk-post-deploy/gpmc.png)

## <a name="enable-multi-tenancy"></a>Activer l’architecture mutualisée

Pour les déploiements à l’aide d’Azure AD, vous devez [activer la mutualisation](../operator/azure-stack-enable-multitenancy.md#enable-multi-tenancy) pour votre installation d’ASDK.

> [!NOTE]
> Lorsque des comptes d’administrateur ou d’utilisateur de domaines autres que celui utilisé pour inscrire Azure Stack sont utilisés pour se connecter à un portail Azure Stack, le nom de domaine utilisé pour inscrire Azure Stack doit être ajouté à l’URL du portail. Par exemple, si Azure Stack a été inscrit avec fabrikam.onmicrosoft.com et que le compte utilisateur est admin@contoso.com, voici l’url à utiliser pour se connecter au portail utilisateur : https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.

## <a name="next-steps"></a>Étapes suivantes

[Inscrire le kit ASDK auprès d’Azure](asdk-register.md)
