---
title: Installer PowerShell pour Azure Stack | Microsoft Docs
description: Découvrez comment installer PowerShell pour Azure Stack.
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
origin.date: 04/13/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: thoroet
ms.lastreviewed: 04/13/2019
ms.openlocfilehash: d523e981663218f39815e3bfe3911f8c8f4c363f
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "64308135"
---
# <a name="install-powershell-for-azure-stack"></a>Installer PowerShell pour Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Pour utiliser votre cloud, vous devrez installer des modules PowerShell compatibles avec Azure Stack. La compatibilité est activée via une fonctionnalité appelée *profils d’API*.

Les profils API permettent de gérer les différences de version entre Azure et Azure Stack. Un profil de version d’API est un ensemble de modules PowerShell Azure Resource Manager avec des versions d’API spécifiques. Chaque plateforme cloud a un ensemble de profils de version d’API pris en charge. Par exemple, Azure Stack prend en charge une version de profil spécifique telle que **2018-03-01-hybrid**. Quand vous installez un profil, les modules PowerShell Azure Resource Manager qui correspondent au profil spécifié sont installés. Les profils sont utilisés pour Azure Stack version 1811 ou antérieure. En ce qui concerne, Azure Stack version 1901 ou ultérieure, les développeurs peuvent utiliser les modules AzureRM **2.4.0** pour installer les modules PowerShell Azure Resource Manager corrects.

Vous pouvez installer les modules PowerShell compatibles avec Azure Stack dans des scénarios connectés à Internet, partiellement connectés, ou déconnectés. Cet article vous guide dans les instructions détaillées de ces scénarios.

## <a name="1-verify-your-prerequisites"></a>1. Vérifier la configuration requise

Avant de démarrer avec Azure Stack et PowerShell, les conditions préalables suivantes doivent être réunies :

- **PowerShell Version 5.0** Pour vérifier votre version, exécutez **$PSVersionTable.PSVersion** et comparez la version **Major**. Si vous n’avez pas PowerShell 5.0, suivez les instructions d’[Installation de PowerShell](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell).

  > [!Note]
  > PowerShell 5.0 nécessite un ordinateur Windows.

- **Exécutez Powershell dans une invite de commandes avec élévation de privilèges**.

- **Accès à PowerShell Gallery** Vous devez avoir accès à [PowerShell Gallery](https://www.powershellgallery.com). La galerie constitue le référentiel central pour le contenu PowerShell. Le module **PowerShellGet** contient des applets de commande pour la découverte, l’installation, la mise à jour et la publication d’artefacts PowerShell tels que des modules, des ressources DSC, des capacités de rôle et des scripts provenant de PowerShell Gallery et d’autres référentiels privés. Si vous utilisez PowerShell dans un scénario déconnecté, vous devez récupérer des ressources d’un ordinateur disposant d’une connexion à Internet, et les stocker dans un emplacement accessible à votre ordinateur déconnecté.

## <a name="2-validate-the-powershell-gallery-accessibility"></a>2. Vérifier l’accessibilité de PowerShell Gallery

Vérifiez que PSGallery est inscrit en tant que référentiel.

> [!Note]  
> Cette étape requiert un accès Internet.

Ouvrez une invite de commandes PowerShell avec élévation de privilèges et exécutez les applets de commande suivantes :

```powershell
Import-Module -Name PowerShellGet -ErrorAction Stop
Import-Module -Name PackageManagement -ErrorAction Stop
Get-PSRepository -Name "PSGallery"
```

Si le référentiel n’est pas enregistré, ouvrez une session PowerShell avec élévation de privilèges, puis exécutez la commande suivante :

```powershell
Register-PsRepository -Default
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

## <a name="3-uninstall-existing-versions-of-the-azure-stack-powershell-modules"></a>3. Désinstaller les versions existantes des modules Azure Stack PowerShell

Avant d’installer la version requise, vérifiez que vous avez désinstallé les modules AzureRM PowerShell existants. Vous pouvez les désinstaller en appliquant l’une des deux méthodes suivantes :

1. Pour désinstaller les modules AzureRM PowerShell existants, fermez toutes les sessions PowerShell actives et exécutez les applets de commande suivantes :

    ```powershell
    Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose
    Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose
    ```

    Si vous rencontrez une erreur du type « Le module est déjà en cours d’utilisation », fermez les sessions PowerShell qui utilisent les modules, puis réexécutez le script ci-dessus.

2. Supprimez tous les dossiers qui commencent par `Azure` ou `Azs.` des dossiers `C:\Program Files\WindowsPowerShell\Modules` et `C:\Users\{yourusername}\Documents\WindowsPowerShell\Modules`. La suppression de ces dossiers supprime tous les modules PowerShell existants.

## <a name="4-connected-install-powershell-for-azure-stack-with-internet-connectivity"></a>4. Connecté : installer PowerShell pour Azure Stack avec connectivité Internet

Utilisez AzureRM version 2.4.0 pour Azure Stack version 1901 ou ultérieure. En plus des modules AzureRM, installez les modules PowerShell spécifiques à Azure Stack. Le profil de version d’API et les modules Azure Stack PowerShell dont vous avez besoin varient selon la version d’Azure Stack que vous exécutez.

L’installation comporte trois étapes :

1. Installer Azure Stack PowerShell selon votre version d’Azure Stack
2. Activer les fonctionnalités de stockage supplémentaire
3. Confirmer l’installation de PowerShell

### <a name="install-azure-stack-powershell"></a>Installer Azure Stack PowerShell

Exécutez le script PowerShell suivant pour installer ces modules sur votre station de travail de développement :

- Azure Stack version 1901 ou ultérieure, installez uniquement les deux modules ci-dessous :

    ```powershell  
    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.

    Install-Module AzureRM -RequiredVersion 2.4.0
    Install-Module -Name AzureStack -RequiredVersion 1.7.1
    ```

    > [!Note]  
    > - La version du module Azure Stack 1.7.1 est un changement cassant. Pour migrer à partir d’Azure Stack 1.6.0, consultez le [guide de migration](https://aka.ms/azspshmigration171).
    > - Le module AzureRm version 2.4.0 est fourni avec un changement cassant pour la cmdlet Remove-AzureRmStorageAccount. Cette cmdlet s’attend à ce que le paramètre -Force soit spécifié pour la suppression du compte de stockage sans confirmation.
    > - Vous n’avez pas besoin d’installer **AzureRM.Bootstrapper** pour installer les modules destinés à Azure stack version 1901 ou ultérieure.
    > - N’installez pas le profil hybride 2018-03-01 si vous utilisez les modules AzureRM ci-dessus avec Azure Stack version 1901 ou ultérieure.

- Azure Stack version 1811, installez le profil à l’aide de **AzureRM.Bootstrapper**, en plus des versions indiquées dans les applets de commande :

    ```powershell  
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRM.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    Install-Module -Name AzureStack -RequiredVersion 1.6.0
    ```

- Azure Stack version 1810 ou antérieure, installez le profil avec **AzureRM.Bootstrapper** :

    ```powershell  
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRM.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    Install-Module -Name AzureStack -RequiredVersion 1.5.0
    ```

> [!Note]  
> Pour mettre à niveau Azure PowerShell à partir la version **2017-03-09-profile** vers la version **2018-03-01-hybrid**, voir le [Guide de migration](https://github.com/azure/azure-powershell/blob/AzureRM/documentation/migration-guides/Stack/migration-guide.2.3.0.md).

### <a name="enable-additional-storage-features"></a>Activer les fonctionnalités de stockage supplémentaire

Pour profiter des fonctionnalités de stockage supplémentaires (mentionnées dans la section Connecté), téléchargez et installez également les packages suivants.

```powershell
# Install the Azure.Storage module version 4.5.0
Install-Module -Name Azure.Storage -RequiredVersion 4.5.0 -Force -AllowClobber

# Install the AzureRm.Storage module version 5.0.4
Install-Module -Name AzureRM.Storage -RequiredVersion 5.0.4 -Force -AllowClobber

# Remove incompatible storage module installed by AzureRM.Storage
Uninstall-Module Azure.Storage -RequiredVersion 4.6.1 -Force

# Load the modules explicitly specifying the versions
Import-Module -Name Azure.Storage -RequiredVersion 4.5.0
Import-Module -Name AzureRM.Storage -RequiredVersion 5.0.4
```

### <a name="confirm-the-installation-of-powershell"></a>Confirmer l’installation de PowerShell

Vérifiez l’installation en exécutant la commande suivante :

```powershell
Get-Module -Name "Azure*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

Si l’installation réussit, les modules AzureRM et AzureStack sont affichés dans la sortie.

## <a name="5-disconnected-install-powershell-without-an-internet-connection"></a>5. Déconnecté : installer PowerShell sans connexion Internet

Dans un scénario déconnecté, vous devez tout d’abord télécharger les modules PowerShell sur un ordinateur qui dispose d’une connexion Internet, puis les transférer vers le Kit de développement Azure Stack pour l’installation.

Connectez-vous à un ordinateur disposant d’une connexion Internet et utilisez le script suivant pour télécharger les packages Azure Resource Manager et Azure Stack, selon votre version d’Azure Stack.

L’installation comporte quatre étapes :

1. Installer Azure Stack PowerShell sur un ordinateur connecté
2. Activer les fonctionnalités de stockage supplémentaire
3. Transporter les packages PowerShell vers votre station de travail déconnectée
4. Confirmer l’installation de PowerShell

### <a name="install-azure-stack-powershell"></a>Installer Azure Stack PowerShell

- Azure Stack 1901 ou version ultérieure.

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.4.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.7.1
    ```

    > [!Note]  
    > La version du module Azure Stack 1.7.1 est un changement cassant. Pour migrer à partir d’Azure Stack 1.6.0, consultez le [guide de migration](https://github.com/Azure/azure-powershell/tree/AzureRM/documentation/migration-guides/Stack).

  - Azure Stack 1811 ou version antérieure.

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.6.0
    ```

  - Azure Stack 1809 ou version antérieure.

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
    ```

    > [!NOTE]
    > Sur les ordinateurs sans connexion Internet, nous vous recommandons d’exécuter la cmdlet suivante pour désactiver la collecte des données de télémétrie. Les performances des cmdlets pourraient se dégrader si la collecte des données de télémétrie n’est pas désactivée. Cela s’applique uniquement aux ordinateurs sans connexion Internet
    > ```powershell
    > Disable-AzureRmDataCollection
    > ```

### <a name="enable-additional-storage-features"></a>Activer les fonctionnalités de stockage supplémentaire

Pour profiter des fonctionnalités de stockage supplémentaires (mentionnées dans la section Connecté), téléchargez et installez également les packages suivants.

```powershell
$Path = "<Path that is used to save the packages>"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name Azure.Storage -Path $Path -Force -RequiredVersion 4.5.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRm.Storage -Path $Path -Force -RequiredVersion 5.0.4
```

### <a name="add-your-packages-to-your-workstation"></a>Ajouter vos packages à votre station de travail

1. Copiez les packages téléchargés sur un appareil USB.

2. Connectez-vous à la station de travail déconnectée, puis copiez les packages du périphérique USB vers un emplacement sur la station de travail.

3. Inscrivez à présent cet emplacement comme référentiel par défaut, puis installez les modules AzureRM et AzureStack à partir de ce référentiel :

   ```powershell
   #requires -Version 5
   #requires -RunAsAdministrator
   #requires -Module PowerShellGet
   #requires -Module PackageManagement

   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation  -InstallationPolicy Trusted

   Install-Module -Name AzureRM -Repository $RepoName

   Install-Module -Name AzureStack -Repository $RepoName
   ```

### <a name="confirm-the-installation-of-powershell"></a>Confirmer l’installation de PowerShell

Vérifiez l’installation en exécutant la commande suivante :

```powershell
Get-Module -Name "Azure*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

## <a name="6-configure-powershell-to-use-a-proxy-server"></a>6. Configurer PowerShell pour l’utilisation d’un serveur proxy

Dans les scénarios qui requièrent un serveur proxy pour accéder à Internet, vous devez commencer par configurer PowerShell pour l’utilisation d’un serveur proxy existant :

1. Ouvrez une invite PowerShell avec élévation de privilèges.
2. Exécutez les commandes suivantes :

   ```powershell
   #To use Windows credentials for proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

   #Alternatively, to prompt for separate credentials that can be used for #proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
   ```

## <a name="next-steps"></a>Étapes suivantes

- [Télécharger les outils Azure Stack à partir de GitHub](azure-stack-powershell-download.md)
- [Configurez l’environnement PowerShell de l’utilisateur Azure Stack.](../user/azure-stack-powershell-configure-user.md)
- [Configurer l’environnement PowerShell de l’opérateur Azure Stack](azure-stack-powershell-configure-admin.md)
- [Gérer les profils de version des API dans Azure Stack](../user/azure-stack-version-profiles.md)
