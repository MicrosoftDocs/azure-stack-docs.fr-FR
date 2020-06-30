---
title: Installer le module PowerShell AzureRM pour Azure Stack Hub
description: Découvrez comment installer PowerShell pour Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 04/14/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/14/2020
ms.openlocfilehash: 114be8564f264f38715ebacbb74c85559540c72f
ms.sourcegitcommit: 8ad3ff64e6a47e512c7ae4442d5418fa79345fb5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264156"
---
# <a name="install-powershell-azurerm-module-for-azure-stack-hub"></a>Installer le module PowerShell AzureRM pour Azure Stack Hub

Azure PowerShell AzureRM fournit un ensemble d’applets de commande qui utilisent le modèle Azure Resource Manager pour gérer vos ressources Azure Stack Hub.

Vous devez aussi utiliser des *profils d’API* pour spécifier les points de terminaison compatibles pour les fournisseurs de ressources Azure Stack Hub.

Les profils d’API permettent de gérer les différences de version entre Azure et Azure Stack Hub. Un profil de version d’API est un ensemble de modules PowerShell Azure Resource Manager avec des versions d’API spécifiques. Chaque plateforme cloud a un ensemble de profils de version d’API pris en charge. Par exemple, Azure Stack Hub prend en charge une version de profil spécifique telle que **2019-03-01-hybrid**. Quand vous installez un profil, les modules PowerShell Azure Resource Manager qui correspondent au profil spécifié sont installés.

Vous pouvez installer des modules PowerShell compatibles avec Azure Stack Hub dans des scénarios connectés à Internet, partiellement connectés, ou déconnectés. Cet article vous guide dans les instructions détaillées de ces scénarios.

## <a name="1-verify-your-prerequisites"></a>1. Vérifier la configuration requise

Avant de commencer à utiliser Azure Stack Hub et le module PowerShell AzureRM, les conditions préalables suivantes doivent être réunies :

- **Version de PowerShell 5.1** <br>
Pour vérifier votre version, exécutez **$PSVersionTable.PSVersion** et comparez la version **Major**. Si vous n’avez pas PowerShell 5.1, suivez les instructions d’[installation de PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell).

  > [!Note]
  > PowerShell 5.1 nécessite un ordinateur Windows.

- **Exécutez PowerShell dans une invite de commandes avec élévation de privilèges**.

- **Accès à PowerShell Gallery** <br>
  Vous devez accéder à [PowerShell Gallery](https://www.powershellgallery.com). La galerie constitue le référentiel central pour le contenu PowerShell. Le module **PowerShellGet** contient des cmdlets pour la découverte, l’installation, la mise à jour et la publication des artefacts PowerShell. Les modules, les ressources DSC, les fonctionnalités de rôle, et les scripts de PowerShell Gallery et d’autres référentiels privés sont des exemples de ces artefacts. Si vous utilisez PowerShell dans un scénario déconnecté, vous devez récupérer des ressources d’un ordinateur disposant d’une connexion à Internet, et les stocker dans un emplacement accessible à votre ordinateur déconnecté.

## <a name="2-validate-the-powershell-gallery-accessibility"></a>2. Vérifier l’accessibilité de PowerShell Gallery

Vérifiez que PSGallery est inscrit en tant que référentiel.

> [!Note]  
> Cette étape requiert un accès Internet.

Ouvrez une invite de commandes PowerShell avec élévation de privilèges et exécutez les applets de commande suivantes :

```powershell
Install-module -Name PowerShellGet -Force
Import-Module -Name PackageManagement -ErrorAction Stop
Get-PSRepository -Name "PSGallery"
```

Si le référentiel n’est pas enregistré, ouvrez une session PowerShell avec élévation de privilèges, puis exécutez la commande suivante :

```powershell
Register-PSRepository -Default
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

## <a name="3-uninstall-existing-versions-of-the-azure-stack-hub-powershell-modules"></a>3. Désinstaller les versions existantes des modules Azure Stack Hub PowerShell

Avant d’installer la version requise, vérifiez que vous avez désinstallé les modules Azure Stack Hub AzureRM PowerShell existants. Désinstallez les modules en appliquant l’une des deux méthodes suivantes :

1. Pour désinstaller les modules PowerShell AzureRM et Az existants, fermez toutes les sessions PowerShell actives et exécutez les applets de commande suivantes :

    ```powershell
    Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose -ErrorAction Continue
    Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose -ErrorAction Continue
    Get-Module -Name Az.* -ListAvailable | Uninstall-Module -Force -Verbose -ErrorAction Continue
    ```

    Si vous rencontrez une erreur du type « Le module est déjà en cours d’utilisation », fermez les sessions PowerShell qui utilisent les modules, puis réexécutez le script ci-dessus.

2. Supprimez tous les dossiers qui commencent par `Azure`, `Az` ou `Azs.` des dossiers `C:\Program Files\WindowsPowerShell\Modules` et `C:\Users\{yourusername}\Documents\WindowsPowerShell\Modules`. La suppression de ces dossiers supprime tous les modules PowerShell existants.

## <a name="4-connected-install-powershell-for-azure-stack-hub-with-internet-connectivity"></a>4. Connecté : installer PowerShell pour Azure Stack Hub avec une connectivité Internet

Le profil de version d’API et les modules PowerShell pour Azure Stack Hub dont vous avez besoin varient selon la version d’Azure Stack Hub que vous exécutez.

### <a name="install-azure-stack-hub-powershell"></a>Installer Azure Stack Hub PowerShell

Exécutez le script PowerShell suivant pour installer ces modules sur votre station de travail de développement :

::: moniker range=">=azs-2002"
Pour Azure Stack Hub 2002 ou version ultérieure :

Vous pouvez utiliser les modules AzureRM ou les modules en préversion Az. Pour utiliser les modules Az, Azure Stack Hub 2002 et le dernier correctif logiciel sont requis.

Pour utiliser les modules en préversion Az, suivez les instructions dans [Installer le module PowerShell Az](powershell-install-az-module.md).

```powershell  
# Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
Install-Module -Name AzureRM.BootStrapper

# Install and import the API Version Profile required by Azure Stack Hub into the current PowerShell session.
Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.8.1
```

::: moniker-end
::: moniker range="azs-1910"
Pour Azure Stack Hub 1910 :

```powershell  
# Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
Install-Module -Name AzureRM.BootStrapper

# Install and import the API Version Profile required by Azure Stack Hub into the current PowerShell session.
Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.8.0
```

> [!Note]  
> - La version 1.8.0 du module Azure Stack Hub marque un changement cassant. Consultez les [notes de publication](release-notes.md) pour plus d’informations.

::: moniker-end
::: moniker range="<=azs-1908"
Pour Azure Stack Hub 1908 ou version antérieure :

```powershell  
# Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
Install-Module -Name AzureRM.BootStrapper

# Install and import the API Version Profile required by Azure Stack Hub into the current PowerShell session.
Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.7.2
```

> [!Note]  
> La version 1.7.2 du module Azure Stack Hub marque un changement cassant. Pour migrer à partir d’Azure Stack Hub 1.6.0, voir le [Guide de migration](https://aka.ms/azspshmigration171).

::: moniker-end

### <a name="confirm-the-installation-of-powershell"></a>Confirmer l’installation de PowerShell

Vérifiez l’installation en exécutant la commande suivante :

```powershell
Get-Module -Name "Azure*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

Si l’installation réussit, les modules `AzureRm` et `AzureStack` sont affichés dans la sortie.

## <a name="5-disconnected-install-powershell-without-an-internet-connection"></a>5. Déconnecté : installer PowerShell sans connexion Internet

Dans un scénario hors connexion, téléchargez d’abord les modules PowerShell sur un ordinateur qui dispose d’une connexion Internet. Transférez-les ensuite vers le Kit de développement Azure Stack (ASDK) pour l’installation.

Connectez-vous à un ordinateur disposant d’une connexion Internet et utilisez le script suivant pour télécharger les packages Azure Resource Manager et Azure Stack Hub, selon votre version d’Azure Stack Hub.

L’installation comporte cinq étapes :

1. Installez Azure Stack Hub PowerShell sur une machine connectée.
2. Activez les fonctionnalités de stockage supplémentaire.
3. Transportez les packages PowerShell vers votre station de travail déconnectée.
4. Démarrez manuellement le fournisseur NuGet sur votre station de travail déconnecté.
5. Confirmez l’installation de PowerShell.

### <a name="install-azure-stack-hub-powershell"></a>Installer Azure Stack Hub PowerShell

::: moniker range=">=azs-2002"
Azure Stack Hub 2002 ou version ultérieure.

Vous pouvez utiliser les modules AzureRM ou les modules en préversion Az. Pour les modules Az, consultez les instructions dans [Installer le module PowerShell Az](powershell-install-az-module.md).

```powershell

Install-module -Name PowerShellGet -Force 
Import-Module -Name PackageManagement -ErrorAction Stop

$Path = "<Path that is used to save the packages>"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.5.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.8.1
```
::: moniker-end

::: moniker range="azs-1910"
Azure Stack Hub 1910.

```powershell
Install-module -Name PowerShellGet -Force 
Import-Module -Name PackageManagement -ErrorAction Stop

$Path = "<Path that is used to save the packages>"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.5.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.8.0
```

> [!NOTE]  
> La version 1.8.0 du module Azure Stack Hub marque un changement cassant. Consultez les [notes de publication](release-notes.md) pour plus d’informations.

::: moniker-end
::: moniker range="<=azs-1908"
Pour Azure Stack Hub 1908 ou version antérieure :

```powershell
Install-module -Name PowerShellGet -Force 
Import-Module -Name PackageManagement -ErrorAction Stop

$Path = "<Path that is used to save the packages>"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.5.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.7.2
```

> [!NOTE]  
> La version 1.7.1 du module Azure Stack Hub marque un changement cassant. Pour migrer à partir d’Azure Stack Hub 1.6.0, voir le [Guide de migration](https://github.com/Azure/azure-powershell/tree/AzureRM/documentation/migration-guides/Stack).

::: moniker-end

> [!NOTE]  
> Sur les ordinateurs sans connexion Internet, nous vous recommandons d’exécuter la cmdlet suivante pour désactiver la collecte des données de télémétrie. Les performances des cmdlets pourraient se dégrader si la collecte des données de télémétrie n’est pas désactivée. Cela s’applique uniquement aux ordinateurs sans connexion Internet
> ```powershell
> Disable-AzureRmDataCollection
> ```

### <a name="add-your-packages-to-your-workstation"></a>Ajouter vos packages à votre station de travail

1. Copiez les packages téléchargés sur un appareil USB.

2. Connectez-vous à la station de travail déconnectée, puis copiez les packages du périphérique USB vers un emplacement sur la station de travail.

3. Démarrez manuellement le fournisseur NuGet sur votre station de travail déconnecté. Pour plus d’instructions, consultez la section [Démarrage manuel du fournisseur NuGet sur un ordinateur qui n’est pas connecté à Internet](https://docs.microsoft.com/powershell/scripting/gallery/how-to/getting-support/bootstrapping-nuget#manually-bootstrapping-the-nuget-provider-on-a-machine-that-is-not-connected-to-the-internet).

4. Inscrivez cet emplacement en tant que dépôt par défaut, puis installez les modules AzureRM et `AzureStack` à partir de ce dépôt :

   ```powershell
   # requires -Version 5
   # requires -RunAsAdministrator
   # requires -Module PowerShellGet
   # requires -Module PackageManagement

   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

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

Dans les scénarios qui requièrent un serveur proxy pour accéder à Internet, commencez par configurer PowerShell pour l’utilisation d’un serveur proxy existant :

1. Ouvrez une invite PowerShell avec élévation de privilèges.
2. Exécutez les commandes suivantes :

   ```powershell
   #To use Windows credentials for proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

   #Alternatively, to prompt for separate credentials that can be used for #proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
   ```

## <a name="known-issue"></a>Problème connu

###  <a name="method-get_serializationsettings-error"></a>Erreur de la méthode get_SerializationSettings 

- Cause : Le module PowerShell Az et les modules PowerShell AzureRM ne sont pas compatibles.

    L’erreur suivante indique que les modules AzureRM et les modules Az sont chargés dans la même session : 

    ```powershell  
    >  Method 'get_SerializationSettings' in type 'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly 'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' does 
    not have an implementation.
    ```

- Correction : Désinstallez les modules en conflit. 

  Si vous souhaitez utiliser les modules AzureRM, désinstallez les modules Az. Et si vous souhaitez utiliser les modules Az, désinstallez le module AzureRM. Fermez votre session PowerShell et désinstallez soit les modules Az, soit les modules AzureRM. 
  
  Vous trouverez des instructions à la section [Désinstaller les versions existantes des modules Azure Stack Hub PowerShell](#3-uninstall-existing-versions-of-the-azure-stack-hub-powershell-modules).

## <a name="next-steps"></a>Étapes suivantes

- [Télécharger les outils Azure Stack Hub à partir de GitHub](azure-stack-powershell-download.md)
- [Configurer l’environnement PowerShell de l’utilisateur Azure Stack Hub](../user/azure-stack-powershell-configure-user.md)
- [Configurer l’environnement PowerShell de l’opérateur Azure Stack Hub](azure-stack-powershell-configure-admin.md)
- [Gérer les profils de version d’API dans Azure Stack Hub](../user/azure-stack-version-profiles.md)
