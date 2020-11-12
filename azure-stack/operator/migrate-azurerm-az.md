---
title: Migrer des scripts Azure PowerShell depuis AzureRM vers Az dans Azure Stack Hub
description: Découvrez les étapes et les outils pour effectuer une migration des scripts à partir du module AzureRM vers le nouveau module Az dans Azure Stack Hub.
author: mattbriggs
ms.author: mabrigg
ms.topic: conceptual
ms.date: 10/05/2020
ms.reviewer: sijuman
ms.lastreviewed: 04/14/2020
ms.openlocfilehash: bfb385e29a89ad8d47ace3b15776bd368e0cf521
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94546169"
---
# <a name="migrate-from-azurerm-to-azure-powershell-az-in-azure-stack-hub"></a>Effectuer une migration depuis AzureRM vers Azure PowerShell Az dans Azure Stack Hub

Le module Az a la même parité des fonctionnalités que AzureRM, mais il utilise des noms de cmdlets plus courts et plus cohérents.
Les scripts écrits pour les cmdlets AzureRM ne fonctionnent pas automatiquement avec le nouveau module. Pour faciliter la transition, Az propose des outils qui vous permettent d’exécuter vos scripts existants à l’aide d’AzureRM. Il n’existe pas une migration vers un nouveau jeu de commandes qui soit pratique, mais cet article vous aide à bien démarrer la transition vers le nouveau module.

Pour obtenir la liste complète des changements cassants entre AzureRM et Az, consultez le [guide de migration pour Az 1.0.0](/powershell/azure/migrate-az-1.0.0).

## <a name="check-for-installed-versions-of-azurerm"></a>Rechercher les versions installées d’AzureRM

Avant d’effectuer toutes les étapes de migration, vérifiez les versions d’AzureRM installées sur votre système. Vous pouvez ainsi vous assurer que les scripts s’exécutent déjà sur la dernière version et savoir si vous pouvez activer des alias de commande sans désinstaller AzureRM.

Pour vérifier quelles sont les versions d’AzureRM que vous avez installées, exécutez la commande :

```powershell-interactive
Get-InstalledModule -Name AzureRM -AllVersions
```

## <a name="check-current-scripts-work-with-azurerm"></a>Vérifier que les scripts actuels fonctionnent avec AzureRM

Il s’agit de l’étape la plus importante. Exécutez vos scripts existants et assurez-vous qu’ils fonctionnent avec la _dernière_ version d’AzureRM ( __2.5.0__ ). Si vos scripts ne fonctionnent pas, veuillez lire le [guide de migration AzureRM](/powershell/azure/azurerm/migration-guide.6.0.0).

## <a name="install-the-azure-powershell-az-module"></a>Installer le module Azure PowerShell Az

La première étape consiste à installer le module Az sur votre plateforme. Lorsque vous installez Az, il est recommandé de désinstaller AzureRM. Dans les étapes suivantes, vous découvrez comment poursuivre l’exécution de vos scripts existants et activer la compatibilité pour les anciens noms de cmdlets.

Pour installer le module Azure PowerShell Az, suivez les étapes suivantes :

* __RECOMMANDÉ__  : [Désinstaller le module AzureRM](/powershell/azure/uninstall-az-ps#uninstall-the-azurerm-module).
  Assurez-vous de supprimer _toutes les_ versions installées d’AzureRM, et non pas simplement la plus récente.
* [Installer le module Az](/powershell/azure/install-az-ps)

## <a name="enable-azurerm-compatibility-aliases"></a>Activer les alias de compatibilité AzureRM 

> [!IMPORTANT]
>
> Activez uniquement le mode de compatibilité si vous avez désinstallé _toutes_ les versions d’AzureRM. L’activation du mode de compatibilité alors que les cmdlets AzureRM sont encore disponibles peut entraîner un comportement imprévisible. Ignorez cette étape si vous avez décidé de conserver l’installation d’AzureRM, mais n’oubliez pas que les cmdlets AzureRM utiliseront les anciens modules et n’appelleront pas appeler les cmdlets Az.

Une fois AzureRM désinstallé et vos scripts utilisant la dernière version d’AzureRM, vous pouvez procéder à l’activation du mode de compatibilité pour le module Az. La compatibilité est activée à l’aide de la commande :

```powershell-interactive
Enable-AzureRmAlias -Scope CurrentUser
```

Les alias rendent possible l’utilisation des anciens noms de cmdlets avec le module Az installé. Ces alias sont écrits dans le profil utilisateur pour l’étendue sélectionnée. S’il n’en existe aucun, un profil utilisateur est créé.

> [!WARNING]
>
> Vous pouvez utiliser une autre `-Scope` pour cette commande, mais ce n’est pas conseillé ! Les alias sont écrits dans le profil utilisateur pour l’étendue sélectionnée. Ainsi, poursuivez leur activation pour une étendue aussi limitée que possible. L’activation des alias à l’échelle du système peut également entraîner des problèmes pour d’autres utilisateurs qui ont installé AzureRM dans leur étendue locale.

Une fois le mode alias activé, exécutez une nouvelle fois les scripts pour confirmer qu’ils fonctionnent toujours comme prévu. 

## <a name="change-module-and-cmdlet-names"></a>Changer les noms des applets de commande et des modules

En règle générale, les noms de module ont été modifiés pour que `AzureRM` et `Azure` deviennent `Az`et il en va de même pour les cmdlets.
Par exemple, le module `AzureRM.Compute` a été renommé `Az.Compute`. `New-AzureRMVM` est devenu `New-AzVM` et `Get-AzureStorageBlob` est désormais `Get-AzStorageBlob`.

Il existe des exceptions à cette modification de dénomination que vous devez connaître. Certains modules ont été renommés ou fusionnés dans les modules existants sans incidence sur le suffixe de leurs cmdlets, à l’exception du remplacement de `AzureRM` ou `Azure` par `Az`. Sinon, le suffixe complet de la cmdlet a été modifié pour refléter le nouveau nom de module.

| Module AzureRM | Module Az | Suffixe de cmdlet modifié ? |
|----------------|-----------|------------------------|
| AzureRM.Profile | Az.Accounts | Oui |
| AzureRM.Insights | Az.Monitor | Oui |
| AzureRM.Tags | Az.Resources | Non |
| AzureRM.UsageAggregates | Az.Billing | Non |
| AzureRM.Consumption | Az.Billing | Non |

## <a name="summary"></a>Récapitulatif

Suivez les étapes suivantes pour mettre à jour tous vos scripts existants et utiliser le nouveau module. Si vous avez des questions concernant ces étapes ou rencontrez des problèmes rendant la migration difficile, veuillez laisser un commentaire sur cet article afin que nous puissions améliorer les instructions.

## <a name="breaking-changes-for-az-100"></a>Changements cassants pour Az 1.0.0

Ce document fournit des informations détaillées sur les changements intervenus entre AzureRM 6.x et le nouveau module Az version 1.x et ultérieure. La table des matières constitue un guide du parcours de migration complet, notamment en ce qui concerne les modifications spécifiques aux modules susceptibles d’affecter vos scripts.

## <a name="general-breaking-changes"></a>Dernières modifications générales

Cette section détaille les changements cassants généraux qui font partie de cette reconception du module Az.

### <a name="cmdlet-noun-prefix-changes"></a>Modifications des préfixes des noms des applets de commande

Dans le module AzureRM, les applets de commande utilisaient `AzureRM` ou `Azure` comme préfixes pour les noms.  Az simplifie et normalise les noms des applets de commande : elles utilisent dorénavant toutes « Az » comme préfixe pour leurs noms. Par exemple :

```powershell  
Get-AzureRMVM
Get-AzureKeyVaultSecret
```

A été remplacé par :

```powershell  
Get-AzVM
Get-AzKeyVaultSecret
```

Pour simplifier la transition vers ces nouveaux noms des applets de commande, Az introduit deux nouvelles applets de commande : [Enable-AzureRmAlias](/powershell/module/az.accounts/enable-azurermalias) et [Disable-AzureRmAlias](/powershell/module/az.accounts/disable-azurermalias).  `Enable-AzureRmAlias` crée des alias pour les anciens noms des applets de commande dans AzureRM qui établissent la correspondance avec leurs nouveaux noms dans Az. L’utilisation de l’argument `-Scope` avec `Enable-AzureRmAlias` vous permet de choisir où les alias sont activés.

Par exemple, le script suivant dans AzureRM :

```powershell  
#Requires -Modules AzureRM.Storage
Get-AzureRmStorageAccount | Get-AzureStorageContainer | Get-AzureStorageBlob
```

Peut être exécuté avec des modifications minimes à l’aide de `Enable-AzureRmAlias` :

```powershell  
#Requires -Modules Az.Storage
Enable-AzureRmAlias -Scope Process
Get-AzureRmStorageAccount | Get-AzureStorageContainer | Get-AzureStorageBlob
```

L’exécution de `Enable-AzureRmAlias -Scope CurrentUser` active les alias pour toutes les sessions PowerShell que vous ouvrez. Ainsi, après l’exécution de cette applet de commande, un script comme celui-ci ne nécessite aucune modification :

```powershell  
Get-AzureRmStorageAccount | Get-AzureStorageContainer | Get-AzureStorageBlob
```

Pour plus d’informations sur l’utilisation des alias des applets de commande, consultez les [Informations de référence sur Enable-AzureRmAlias](/powershell/module/az.accounts/enable-azurermalias).

Quand vous êtes prêt à désactiver les alias, `Disable-AzureRmAlias` supprime les alias créés. Pour plus d’informations, consultez les [Informations de référence sur Disable-AzureRmAlias](/powershell/module/az.accounts/disable-azurermalias).

> [!IMPORTANT]
> Quand vous désactivez les alias, vérifiez qu’ils sont désactivés pour _toutes_ les étendues où les alias étaient activés.

### <a name="module-name-changes"></a>Changements de nom de module

Les noms de module ont été modifiés de `AzureRM.*` vers `Az.*`, sauf pour les modules suivants :

| Module AzureRM | Module Az |
|----------------|-----------|
| Azure.Storage | Az.Storage |
| Azure.AnalysisServices | Az.AnalysisServices |
| AzureRM.Profile | Az.Accounts |
| AzureRM.Insights | Az.Monitor |
| AzureRM.RecoveryServices.Backup | Az.RecoveryServices |
| AzureRM.RecoveryServices.SiteRecovery | Az.RecoveryServices |
| AzureRM.Tags | Az.Resources |
| AzureRM.MachineLearningCompute | Az.MachineLearning |
| AzureRM.UsageAggregates | Az.Billing |
| AzureRM.Consumption | Az.Billing |

Les modifications dans les noms de module signifient que n’importe quel script qui utilise `#Requires` ou `Import-Module` pour charger des modules spécifiques devra être modifié pour utiliser le nouveau module à la place. Pour les modules où le suffixe des applets de commande n’a pas changé, cela signifie que, bien que le nom du module ait changé, le suffixe indiquant l’espace des opérations n’a _pas_ changé.

#### <a name="migrating-requires-and-import-module-statements"></a>Migration des instructions requires et d’importation de module

Les scripts qui utilisent `#Requires`ou `Import-Module` pour déclarer une dépendance vis-à-vis de modules AzureRM doivent être mis à jour de façon à utiliser les nouveaux noms des modules. Par exemple :

```powershell  
#Requires -Module AzureRM.Compute
```

Doit être remplacé par :

```powershell  
#Requires -Module Az.Compute
```

Pour `Import-Module` :

```powershell  
Import-Module -Name AzureRM.Compute
```

Doit être remplacé par :

```powershell  
Import-Module -Name Az.Compute
```

### <a name="migrating-fully-qualified-cmdlet-invocations"></a>Migration des appels d’applets de commande complets

Les scripts qui utilisent des appels d’applets de commande qualifiés par module, comme :

```powershell  
AzureRM.Compute\Get-AzureRmVM
```

doivent être modifiés de façon à utiliser les nouveaux noms des modules et des applets de commande :

```powershell  
Az.Compute\Get-AzVM
```

### <a name="migrating-module-manifest-dependencies"></a>Migration des dépendances de manifeste de module

Pour les modules qui expriment des dépendances vis-à-vis de modules AzureRM via un fichier manifeste de module (.psd1), vous devez mettre à jour les noms des modules dans leur section `RequiredModules` :

```powershell
RequiredModules = @(@{ModuleName="AzureRM.Profile"; ModuleVersion="5.8.2"})
```

Doit être remplacé par :

```powershell
RequiredModules = @(@{ModuleName="Az.Accounts"; ModuleVersion="1.0.0"})
```

### <a name="removed-modules"></a>Modules supprimés

Les modules suivants ont été supprimés :

- `AzureRM.Backup`
- `AzureRM.Compute.ManagedService`
- `AzureRM.Scheduler`

Les outils pour ces services ne sont plus activement pris en charge.  Les clients sont encouragés à choisir d’autres services dès que possible.

### <a name="windows-powershell-51-and-net-472"></a>Windows PowerShell 5.1 et .NET 4.7.2

L’utilisation d’Az avec PowerShell 5.1 pour Windows nécessite l’installation de .NET Framework 4.7.2. L’utilisation de PowerShell Core 6.x ou ultérieur ne nécessite pas le .NET Framework.

### <a name="temporary-removal-of-user-login-using-pscredential"></a>Suppression temporaire de la connexion de l’utilisateur avec PSCredential

En raison de modifications dans le flux d’authentification pour .NET Standard, nous supprimons temporairement la connexion utilisateur via PSCredential. Cette fonctionnalité sera réintroduite dans la version du 15/01/2019 pour PowerShell 5.1 pour Windows. Les détails sont abordés dans [ce problème GitHub.](https://github.com/Azure/azure-powershell/issues/7430)

### <a name="default-device-code-login-instead-of-web-browser-prompt"></a>Connexion via le code d’appareil par défaut au lieu de l’invite du navigateur web

En raison de modifications dans le flux d’authentification pour .NET Standard, nous utilisons la connexion à l’appareil en tant que flux de connexion par défaut lors de la connexion interactive. La connexion basée sur un navigateur web sera réintroduite pour PowerShell 5.1 pour Windows comme option par défaut dans la version du 15/01/2019. À ce moment, les utilisateurs seront en mesure de se connecter à l’appareil à l’aide d’un paramètre de commutateur.

## <a name="module-breaking-changes"></a>Changements cassants de module

Cette section détaille les changements cassants spécifiques à des applets de commande et des modules individuels.

### <a name="azapimanagement-previously-azurermapimanagement"></a>Az.ApiManagement (précédemment AzureRM.ApiManagement)

- Les applets de commande suivantes ont été supprimées :
  - New-AzureRmApiManagementHostnameConfiguration
  - Set-AzureRmApiManagementHostnames
  - Update-AzureRmApiManagementDeployment
  - Import-AzureRmApiManagementHostnameCertificate
  - Utilisez à la place l’applet de commande **Set-AzApiManagement** pour définir ces propriétés.
- Les propriétés suivantes ont été supprimées :
  - Suppression des propriétés `PortalHostnameConfiguration`, `ProxyHostnameConfiguration`, `ManagementHostnameConfiguration` et `ScmHostnameConfiguration` de type `PsApiManagementHostnameConfiguration` dans `PsApiManagementContext`. À la place, utilisez `PortalCustomHostnameConfiguration`, `ProxyCustomHostnameConfiguration`, `ManagementCustomHostnameConfiguration` et `ScmCustomHostnameConfiguration` de type `PsApiManagementCustomHostNameConfiguration`.
  - Suppression de la propriété `StaticIPs` dans PsApiManagementContext. La propriété a été divisée en `PublicIPAddresses` et `PrivateIPAddresses`.
  - Suppression de la propriété obligatoire `Location` dans la cmdlet New-AzureApiManagementVirtualNetwork.

### <a name="azbilling-previously-azurermbilling-azurermconsumption-and-azurermusageaggregates"></a>Az.Billing (précédemment AzureRM.Billing, AzureRM.Consumption et AzureRM.UsageAggregates)

- Le paramètre `InvoiceName` a été supprimé de la cmdlet `Get-AzConsumptionUsageDetail`.  Les scripts doivent utiliser d’autres paramètres d’identité pour la facture.

### <a name="azcompute-previously-azurermcompute"></a>Az.Compute (précédemment AzureRM.Compute)

- `IdentityIds` sont supprimés de la propriété `Identity` dans les objets `PSVirtualMachine` et `PSVirtualMachineScaleSet`. Les scripts ne doivent plus utiliser la valeur de ce champ pour prendre des décisions de traitement.
- Le type de la propriété `InstanceView` de l’objet `PSVirtualMachineScaleSetVM` passe de `VirtualMachineInstanceView` à `VirtualMachineScaleSetVMInstanceView`.
- Les propriétés `AutoOSUpgradePolicy` et `AutomaticOSUpgrade` sont supprimées de la propriété `UpgradePolicy`.
- Le type de la propriété `Sku` de l’objet `PSSnapshotUpdate` passe de `DiskSku` à `SnapshotSku`.
- `VmScaleSetVMParameterSet` est supprimé de la cmdlet `Add-AzVMDataDisk`. Vous ne pouvez plus ajouter un disque de données individuellement à une machine virtuelle ScaleSet.

### <a name="azkeyvault-previously-azurermkeyvault"></a>Az.KeyVault (précédemment AzureRM.KeyVault)

- La propriété `PurgeDisabled` a été supprimée des objets `PSKeyVaultKeyAttributes`, `PSKeyVaultKeyIdentityItem` et `PSKeyVaultSecretAttributes`. Les scripts ne doivent plus faire référence à la propriété ```PurgeDisabled``` pour prendre des décisions de traitement.

### <a name="azmonitor-previously-azurerminsights"></a>Az.Monitor (précédemment AzureRM.Insights)

- Suppression des paramètres `Categories` et `Timegrains` avec des noms pluriels en faveur des noms de paramètre au singulier dans la cmdlet `Set-AzDiagnosticSetting`. Tout script utilisant
  ```powershell  
  Set-AzureRmDiagnosticSetting -Timegrains PT1M -Categories Category1, Category2
  ```

  Doit être remplacé par
  ```powershell  
  Set-AzDiagnosticSetting -Timegrain PT1M -Category Category1, Category2
  ```

### <a name="aznetwork-previously-azurermnetwork"></a>Az.Network (précédemment AzureRM.Network)

- Suppression du paramètre déconseillé `ResourceId` de la cmdlet `Get-AzServiceEndpointPolicyDefinition`
- Suppression de la propriété déconseillée `EnableVmProtection` de l’objet `PSVirtualNetwork`
- Suppression de la cmdlet déconseillée `Set-AzVirtualNetworkGatewayVpnClientConfig`

Les scripts ne doivent plus décider des traitements sur la base des valeurs de ces champs.

### <a name="azresources-previously-azurermresources"></a>Az.Resources (précédemment AzureRM.Resources)

- Suppression du paramètre `Sku` dans la cmdlet `New/Set-AzPolicyAssignment`
- Suppression du paramètre `Password` dans les cmdlets `New-AzADServicePrincipal` et `New-AzADSpCredential`. Les mots de passe sont automatiquement générés. Tout script qui a fourni le mot de passe :

  ```powershell  
  New-AzAdSpCredential -ObjectId 1f99cf81-0146-4f4e-beae-2007d0668476 -Password $secPassword
  ```

  Doit être changé de façon à récupérer le mot de passe à partir de la sortie :

  ```powershell  
  $credential = New-AzAdSpCredential -ObjectId 1f99cf81-0146-4f4e-beae-2007d0668476
  $secPassword = $credential.Secret
  ```


### <a name="azstorage-previously-azurestorage-and-azurermstorage"></a>Az.Storage (précédemment Azure.Storage et AzureRM.Storage)

- Pour prendre en charge la création d’un contexte de stockage Oauth avec uniquement le nom du compte de stockage, le jeu de paramètres par défaut a été remplacé par `OAuthParameterSet`.
  - Exemple : `$ctx = New-AzureStorageContext -StorageAccountName $accountName`
- Le paramètre `Location` est devenu obligatoire dans la cmdlet `Get-AzStorageUsage`.
- Les méthodes d’API de stockage utilisent maintenant le modèle asynchrone basé sur des tâches (TAP), au lieu d’appels d’API synchrones. Les exemples suivants montrent les nouvelles commandes asynchrones :

#### <a name="blob-snapshot"></a>Instantané d’objet blob

AzureRM :

```powershell  
$b = Get-AzureStorageBlob -Container $containerName -Blob $blobName -Context $ctx
$b.ICloudBlob.Snapshot()
```

Az :

```powershell  
$b = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx
$task = $b.ICloudBlob.SnapshotAsync()
$task.Wait()
$snapshot = $task.Result
```

#### <a name="share-snapshot"></a>Partager l’instantané

AzureRM :

```powershell  
$Share = Get-AzureStorageShare -Name $containerName -Context $ctx
$snapshot = $Share.Snapshot()
```

Az :

```powershell  
$Share = Get-AzStorageShare -Name $containerName -Context $ctx
$task = $Share.SnapshotAsync()
$task.Wait()
$snapshot = $task.Result
```

#### <a name="undelete-soft-deleted-blob"></a>Annuler la suppression d’un objet blob supprimé de manière réversible

AzureRM :

```powershell  
$b = Get-AzureStorageBlob -Container $containerName -Blob $blobName -IncludeDeleted -Context $ctx
$b.ICloudBlob.Undelete()
```

Az :

```powershell  
$b = Get-AzStorageBlob -Container $containerName -Blob $blobName -IncludeDeleted -Context $ctx
$task = $b.ICloudBlob.UndeleteAsync()
$task.Wait()
```

#### <a name="set-blob-tier"></a>Définir un niveau d’objet blob

AzureRM :

```powershell  
$blockBlob = Get-AzureStorageBlob -Container $containerName -Blob $blockBlobName -Context $ctx
$blockBlob.ICloudBlob.SetStandardBlobTier("hot")

$pageBlob = Get-AzureStorageBlob -Container $containerName -Blob $pageBlobName -Context $ctx
$pageBlob.ICloudBlob.SetPremiumBlobTier("P4")
```

Az :

```powershell  
$blockBlob = Get-AzStorageBlob -Container $containerName -Blob $blockBlobName -Context $ctx
$task = $blockBlob.ICloudBlob.SetStandardBlobTierAsync("hot")
$task.Wait()

$pageBlob = Get-AzStorageBlob -Container $containerName -Blob $pageBlobName -Context $ctx
$task = $pageBlob.ICloudBlob.SetPremiumBlobTierAsync("P4")
$task.Wait()
```

### <a name="azwebsites-previously-azurermwebsites"></a>Az.Websites (précédemment AzureRM.Websites)

- Suppression des propriétés déconseillées des objets `PSAppServicePlan`, `PSCertificate`, `PSCloningInfo` et `PSSite`.

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur PowerShell sur Azure Stack Hub, consultez [Bien démarrer avec PowerShell dans Azure Stack Hub](../user/azure-stack-powershell-overview.md).
- Pour installer le module PowerShell Az, consultez [Installer le module PowerShell Az pour Azure Stack Hub](powershell-install-az-module.md).
