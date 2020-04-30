---
title: Installer le module PowerShell Az pour Azure Stack Hub
description: Découvrez comment installer PowerShell pour Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 04/14/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/14/2020
ms.openlocfilehash: d39bac1a99c2dc7d7a43f211a5fd3e5a7275de33
ms.sourcegitcommit: d930d52e27073829b8bf8ac2d581ec2accfa37e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/27/2020
ms.locfileid: "82174013"
---
# <a name="install-powershell-az-preview-module-for-azure-stack-hub"></a>Installer le module en préversion PowerShell Az pour Azure Stack Hub

Cet article explique comment installer les modules d’administrateur Azure PowerShell Az et Azure Stack Hub compatibles avec PowerShellGet. Les modules Az peuvent être installés sur des plateformes Windows, macOS et Linux.

Si vous souhaitez installer le module PowerShell AzureRM pour Azure Stack Hub, consultez [Installer le module PowerShell AzureRM pour Azure Stack Hub](azure-stack-powershell-install.md).

> [!IMPORTANT]
> Le module PowerShell Az est en préversion publique.
> Cette préversion peut présenter des changements cassants dans les versions à venir. Pour plus d’informations, consultez [Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> Il n’y aura probablement pas de nouvelles versions du module AzureRM. Les modules AzureRM ne sont pris en charge que pour les correctifs critiques. À l’avenir, il y aura uniquement des versions Az pour Azurestack

Vous pouvez utiliser des *profils d’API* pour spécifier les points de terminaison compatibles pour les fournisseurs de ressources Azure Stack Hub.

Les profils d’API permettent de gérer les différences de version entre Azure et Azure Stack Hub. Un profil de version d’API est un ensemble de modules PowerShell Azure Resource Manager avec des versions d’API spécifiques. Chaque plateforme cloud a un ensemble de profils de version d’API pris en charge. Par exemple, Azure Stack Hub prend en charge une version de profil spécifique telle que **2019-03-01-hybrid**. Quand vous installez un profil, les modules PowerShell Azure Resource Manager qui correspondent au profil spécifié sont installés.

Vous pouvez installer des modules PowerShell Az compatibles avec Azure Stack Hub dans des scénarios connectés à Internet, partiellement connectés ou déconnectés. Cet article vous guide dans les instructions détaillées de ces scénarios.

## <a name="1-verify-your-prerequisites"></a>1. Vérifier la configuration requise

Les modules Az sont pris en charge uniquement sur [Azure Stack Hub avec la mise à jour 2002](https://docs.microsoft.com/azure-stack/operator/release-notes?view=azs-2002#2002-build-reference) et le dernier [correctif](https://docs.microsoft.com/azure-stack/operator/release-notes?view=azs-2002#hotfixes).

Azure PowerShell fonctionne avec PowerShell 5.1 ou ultérieur sur Windows, ou avec PowerShell 6.x et ultérieur sur toutes les plateformes. Vous devez installer la [dernière version de PowerShell Core](https://docs.microsoft.com/powershell/scripting/install/installing-powershell#powershell-core) disponible pour votre système d’exploitation. Azure PowerShell ne présente aucune exigence supplémentaire quand il est exécuté sur PowerShell Core.

Pour vérifier votre version de PowerShell, exécutez la commande :

```powershell  
$PSVersionTable.PSVersion
```

### <a name="prerequisites-for-windows"></a>Prérequis pour Windows
Pour utiliser Azure PowerShell dans PowerShell 5.1 sur Windows :

1. Mettrez à jour vers [Windows PowerShell 5.1](https://docs.microsoft.com//powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell) si nécessaire. Si vous êtes sur Windows 10, PowerShell 5.1 est déjà installé.
2. Installez [.NET Framework 4.7.2 ou ultérieur](https://docs.microsoft.com//dotnet/framework/install).
3. Veillez à disposer de la dernière version de PowerShellGet. Exécutez `Install-Module PowerShellGet -MinimumVersion 2.2.3 -Force`. 

## <a name="2-prerequisites-for-linux-and-mac"></a>2. Prérequis pour Linux et Mac
PowerShell Core 6.x ou version ultérieure est nécessaire. Pour obtenir des instructions, suivez le [lien](https://docs.microsoft.com//powershell/scripting/install/installing-powershell-core-on-windows)

## <a name="3-uninstall-existing-versions-of-the-azure-stack-hub-powershell-modules"></a>3. Désinstaller les versions existantes des modules Azure Stack Hub PowerShell

Avant d’installer la version requise, vérifiez que vous avez désinstallé les modules PowerShell Azure Stack Hub AzureRM ou Az installés précédemment. Désinstallez les modules en appliquant l’une des deux méthodes suivantes :

1. Pour désinstaller les modules PowerShell AzureRM et Az existants, fermez toutes les sessions PowerShell actives et exécutez les applets de commande suivantes :

    ```powershell
    Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose -ErrorAction Continue
    Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose -ErrorAction Continue
    Get-Module -Name Az.* -ListAvailable | Uninstall-Module -Force -Verbose -ErrorAction Continue
    ```
    Si vous rencontrez une erreur du type « Le module est déjà en cours d’utilisation », fermez les sessions PowerShell qui utilisent les modules, puis réexécutez le script ci-dessus.

2. Supprimez tous les dossiers qui commencent par `Azure`, `Az` ou `Azs.` des dossiers `C:\Program Files\WindowsPowerShell\Modules` et `C:\Users\{yourusername}\Documents\WindowsPowerShell\Modules`. La suppression de ces dossiers supprime tous les modules PowerShell existants.

## <a name="4-connected-install-with-internet-connectivity"></a>4. Connecté : Installer avec une connexion Internet

Le module Azure Stack Az fonctionne avec Azure Stack Hub 2002 ou ultérieur. De plus, le module Azure Stack Az fonctionne avec PowerShell 5.1 ou ultérieur sur un ordinateur Windows, ou PowerShell 6.x ou ultérieur sur une plateforme Linux ou macOS. L’utilisation des applets de commande PowerShellGet est la méthode d’installation par défaut. Cette méthode fonctionne de la même façon sur les plateformes prises en charge.

Exécutez la commande suivante à partir d’une session PowerShell :

```powershell  
Install-Module -Name Az.BootStrapper -Force -AllowPrerelease
Install-AzProfile -Profile 2019-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 2.0.0-preview -AllowPrerelease
```

> [!Note]  
> La version 2.0.0 du module Azure Stack Hub marque un changement cassant. Pour plus d’informations, reportez-vous à [Effectuer une migration depuis AzureRM vers Azure PowerShell Az dans Azure Stack Hub](migrate-azurerm-az.md).

> [!WARNING]
> Vous ne pouvez pas avoir en même temps les modules AzureRM et Az installés pour PowerShell 5.1 pour Windows. Si vous avez besoin de conserver AzureRM disponible sur votre système, installez le module Az pour PowerShell Core 6.x ou ultérieur. Pour cela, [installez PowerShell Core 6.x ou ultérieur](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-windows) et suivez ces instructions dans un terminal PowerShell Core.

## <a name="5-disconnected-install-without-internet-connection"></a>5. Déconnecté : Installer sans connexion à Internet

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

Vous pouvez utiliser les modules AzureRM ou les modules en préversion Az. Pour les modules RM, consultez les instructions dans [Installer le module PowerShell AzureRM](azure-stack-powershell-install.md).

```powershell

Install-module -Name PowerShellGet -MinimumVersion 2.2.3 -Force
Import-Module -Name PackageManagement -ErrorAction Stop

$Path = "<Path that is used to save the packages>"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name Az -Path $Path -Force -RequiredVersion 0.10.0-preview
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 2.0.0-preview
```
::: moniker-end

> [!NOTE]  
> Sur les ordinateurs sans connexion Internet, nous vous recommandons d’exécuter la cmdlet suivante pour désactiver la collecte des données de télémétrie. Les performances des cmdlets pourraient se dégrader si la collecte des données de télémétrie n’est pas désactivée. Cela s’applique uniquement aux ordinateurs sans connexion Internet
> ```powershell
> Disable-AzDataCollection
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

   Install-Module -Name Az.BootStrapper -Repository $RepoName -Scope AllUsers -AllowPrerelease

   Set-BootstrapRepo -Repo $RepoName

   Install-AzProfile -Profile '2019-03-01-hybrid' -Force -Scope AllUsers

   Install-Module -Name AzureStack -Repository $RepoName -RequiredVersion 2.0.0-preview -AllowPrerelease -Scope AllUsers
   ```

### <a name="confirm-the-installation-of-powershell"></a>Confirmer l’installation de PowerShell

Vérifiez l’installation en exécutant la commande suivante :

```powershell
Get-Module -Name "Az*" -ListAvailable
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

## <a name="7-use-the-az-module"></a>7. Utiliser le module Az

Vous pouvez utiliser les applets de commande et les exemples de code basés sur AzureRM. Toutefois, vous pouvez changer le nom des modules et des applets de commande. Les noms de module ont été changés pour que `AzureRM` et Azure deviennent `Az`, et il en va de même pour les applets de commande. Par exemple, le module `AzureRM.Compute` a été renommé `Az.Compute`.` New-AzureRMVM` est devenu ` New-AzVM` et `Get-AzureStorageBlob` est désormais `Get-AzStorageBlob`.

Pour une discussion plus approfondie et des conseils sur le déplacement du script AzureRM vers Az et sur les changements cassants dans le module Az d’Azure Stack Hub, consultez [Migrer d’AzureRM vers Azure PowerShell Az](migrate-azurerm-az.md).

Les extraits de code PowerShell utilisés dans le contenu Azure Stack Hub se servent du module AzureRM. Le module Az est une préversion. Vous pouvez refactoriser les extraits de code à utiliser avec le module Az en suivant les instructions du guide de migration, [Effectuer une migration depuis AzureRM vers Azure PowerShell Az dans Azure Stack Hub](powershell-install-az-module.md).

## <a name="next-steps"></a>Étapes suivantes

- [Télécharger les outils Azure Stack Hub à partir de GitHub](azure-stack-powershell-download.md)
- [Configurer l’environnement PowerShell de l’utilisateur Azure Stack Hub](../user/azure-stack-powershell-configure-user.md)
- [Configurer l’environnement PowerShell de l’opérateur Azure Stack Hub](azure-stack-powershell-configure-admin.md)
- [Gérer les profils de version d’API dans Azure Stack Hub](../user/azure-stack-version-profiles.md)
