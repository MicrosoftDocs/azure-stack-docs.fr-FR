---
title: Station de travail d’accès à l’opérateur Azure Stack Hub
description: Découvrez comment télécharger et configurer une station de travail d’accès à l’opérateur Azure Stack Hub.
author: ashika789
ms.topic: article
ms.date: 11/04/2020
ms.author: justinha
ms.reviewer: asganesh
ms.lastreviewed: 11/04/2020
ms.openlocfilehash: 3c01f6f4e51adb9d91ee1b3098c11814a618ac1c
ms.sourcegitcommit: ec00358a8780086dda217f93f2ef813a8b24dd3b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95006818"
---
# <a name="azure-stack-hub-operator-access-workstation"></a>Station de travail d’accès à l’opérateur Azure Stack Hub 

La station de travail d’accès à l’opérateur (OAW) est utilisée pour déployer une machine virtuelle Jump Box sur un hôte de cycle de vie du matériel (HLH) version 2005 ou versions ultérieures afin qu’un opérateur Azure Stack Hub puisse accéder au point de terminaison privilégié (PEP) et au portail d’administration pour les scénarios de support. 

La machine virtuelle de la station de travail d’accès à l’opérateur doit être créée lorsqu’un opérateur effectue une nouvelle tâche. Une fois qu’une tâche requise à l’intérieur de la machine virtuelle est terminée, la machine virtuelle doit être arrêtée et supprimée, car Azure Stack Hub n’a pas besoin de l’exécuter à chaque fois.  

## <a name="oaw-scenarios"></a>Scénarios OAW

Les tableaux suivants répertorient les scénarios courants pour l’OAW, mais cela n’est pas exclusif. Il est conseillé d’utiliser le Bureau à distance pour vous connecter à l’OAW. 

|Scénario                                                                                                                          |Description                 |
|----------------------------------------------------------------------------------------------------------------------------------|-----------------------------|
|[Accès au portail d’administration](./azure-stack-manage-portals.md)                     |Effectuer des opérations administratives                                                                           |
|[Accès au PEP](./azure-stack-privileged-endpoint.md)                                     |Collecte des journaux et téléchargement :<br>-[Créer un partage SMB](#transfer-files-between-the-hlh-and-oaw) sur le HLH pour le transfert de fichiers à partir d’Azure Stack Hub<br>\- Utiliser l’Explorateur Stockage Azure pour charger les journaux enregistrés dans le partage SMB |
|[Inscrire Azure Stack Hub](./azure-stack-registration.md#renew-or-change-registration) |Pour la réinscription, obtenez le nom d’inscription précédent et le groupe de ressources à partir du portail d’administration                               |
|[Syndication de Place de marché](./azure-stack-download-azure-marketplace-item.md)            |[Créer un partage SMB](#transfer-files-between-the-hlh-and-oaw) sur le HLH pour stocker l’image ou l’extension téléchargée                                                        |

## <a name="download-files"></a>Télécharger les fichiers

Pour obtenir les fichiers et créer la machine virtuelle de l’OAW, [**téléchargez ici**](https://aka.ms/OAWDownload). Veillez à consulter la [Déclaration de confidentialité Microsoft](https://privacy.microsoft.com/privacystatement) et les [Conditions juridiques](/legal/azure-stack-hub/azure-stack-operator-access-workstation-legal-terms) avant le téléchargement.

En raison de la nature sans état de la solution, aucune mise à jour n’est apportée à la machine virtuelle de l’OAW. Pour chaque jalon, une nouvelle version du fichier image de la machine virtuelle sera publiée. Utilisez la dernière version pour créer une machine virtuelle de l’OAW. Le fichier image est basé sur la dernière version de Windows Server 2019. Après l’installation, vous pouvez appliquer des mises à jour, y compris des mises à jour critiques, à l’aide de Windows Update. 

Validez le code de hachage du fichier OAW.zip téléchargé pour vous assurer qu’il n’a pas été modifié avant de l’utiliser pour créer la machine virtuelle de l’ordinateur virtuel. Exécutez le script PowerShell suivant. Si la valeur renvoyée est True, vous pouvez utiliser le fichier OAW.zip téléchargé :

```powershell
param(
    [Parameter(Mandatory=$True)]
    [ValidateNotNullOrEmpty()]
    [ValidateScript({Test-Path $_ -PathType Leaf})]
    [string]
    $DownloadedOAWZipFilePath
)

$expectedHash = '459D8BA232E4315372FCE7CDD705057D051C1BD60772FC36E0136C741C27A273'
$actualHash = (Get-FileHash -Path $DownloadedOAWZipFilePath).Hash

Write-Host "Expected hash: $expectedHash"

if ($expectedHash -eq $actualHash)
{
    Write-Host 'SUCCESS: OAW.zip file hash matches.'
}
else
{
    Write-Error 'ERROR: OAW.zip file hash does not match! It is not safe to use it, please download it again.'
    Write-Error "Actual hash: $actualHash"
}
```

## <a name="user-account-policy"></a>Politique de compte d'utilisateur 
La stratégie de compte d’utilisateur suivante est appliquée à la machine virtuelle de l’OAW :

- Nom d’utilisateur Administrateur intégré : AdminUser
- MinimumPasswordLength = 14
- PasswordComplexity est activé
- MinimumPasswordAge = 1 (jour)
- MaximumPasswordAge = 42 (jours)
- NewGuestName = GUser (désactivé par défaut)

## <a name="pre-installed-software"></a>Logiciels préinstallés
Le tableau suivant répertorie les logiciels préinstallés sur la machine virtuelle de l’OAW.

| Nom du logiciel           | Emplacement                                                                                       |
|--------------------------|------------------------------------------------------------------------------------------------|
| [Microsoft Skype Entreprise](https://www.microsoft.com/edge/business/)                                            | \[SystemDrive\]\Program Files (x86)\Microsoft\Edge\Application                                                                                        |
| [Modules Az](./powershell-install-az-module.md)                         | \[SystemDrive\]\ProgramFiles\WindowsPowerShell\Modules                                         |  
| [PowerShell 7](https://devblogs.microsoft.com/powershell/announcing-PowerShell-7-0/)| \[SystemDrive\]\Program Files\PowerShell\7                                                                       |
| [Azure CLI](/cli/azure/?view=azure-cli-latest) | \[SystemDrive\]\Program Files (x86)\Microsoft SDKs\Azure\CLI2 |
| [Explorateur Stockage Microsoft Azure](https://azure.microsoft.com/features/storage-explorer/)   | \[SystemDrive\]\Program Files (x86)\Microsoft Azure Storage Explorer                                                                       |
| [AZCopy](/azure/storage/common/storage-use-azcopy-v10)                             | \[SystemDrive\]\VMSoftware\azcopy_windows_amd64_10.3.4                                         |
| [Outils AzureStack](https://github.com/Azure/AzureStack-Tools/tree/az)                  | \[SystemDrive\]\VMSoftware\AzureStack-Tools                                                    |

## <a name="check-hlh-version"></a>Vérifier la version de HLH

1. Connectez-vous à HLH avec vos informations d’identification.
1. Ouvrez PowerShell ISE et exécutez le script suivant :

   ```powershell
   'C:\Version\Get-Version.ps1'
   ```

   Par exemple :

   ![Capture d’écran de la cmdlet PowerShell pour vérifier la version de la machine virtuelle de l’OAW](./media/operator-access-workstation/check-hardware-lifecycle-host-version.png)

## <a name="create-the-oaw-vm-using-a-script"></a>Créer la machine virtuelle de l’OAW à l’aide d’un script

Le script suivant prépare la machine virtuelle en tant que station de travail d’accès à l’opérateur, utilisée pour accéder à Microsoft Azure Stack Hub pour l’administration et les diagnostics.

1. Connectez-vous à HLH avec vos informations d’identification.
1. Téléchargez le OAW.zip et extrayez les fichiers.
1. Ouvrez une session PowerShell avec élévation de privilèges.
1. Accédez au contenu extrait du fichier OAW.zip.
1. Exécutez le script New-OAW.ps1. 

Par exemple, pour créer la machine virtuelle de l’OAW sur le HLH sans aucune personnalisation à l’aide d’Azure Stack Hub version 2005 ou ultérieure, exécutez le script New-OAW.ps1 uniquement avec le paramètre **-LocalAdministratorPassword** :

```powershell
$securePassword = Read-Host -Prompt "Enter password for Azure Stack OAW's local administrator" -AsSecureString
New-OAW.ps1 -LocalAdministratorPassword $securePassword  
```

Pour créer la machine virtuelle de l’OAW sur un ordinateur hôte disposant d’une connexion réseau à Azure Stack Hub :

```powershell
$securePassword = Read-Host -Prompt "Enter password for Azure Stack OAW's local administrator" -AsSecureString
New-OAW.ps1 -LocalAdministratorPassword $securePassword `
   -IPAddress '192.168.0.20' `
   -SubnetMask '255.255.255.0' `
   -DefaultGateway '192.168.0.1' `
   -DNS '192.168.0.10'
```

Pour récupérer l’adresse IP de la machine virtuelle ERCS à partir du fichier AzureStackStampInformation.json :

```powershell
$securePassword = Read-Host -Prompt "Enter password for Azure Stack OAW's local administrator" -AsSecureString
New-OAW.ps1 -LocalAdministratorPassword $securePassword `
   -AzureStackCertificatePath 'F:\certroot.cer' `
   -DeploymentDataFilePath 'F:\DeploymentData.json' `
   -AzSStampInfoFilePath 'F:\AzureStackStampInformation.json'
```

Pour créer la machine virtuelle de l’OAW sur le HLH avec DeploymentData.json :

```powershell
$securePassword = Read-Host -Prompt "Enter password for Azure Stack OAW's local administrator" -AsSecureString
New-OAW.ps1 -LocalAdministratorPassword $securePassword `
   -DeploymentDataFilePath 'D:\AzureStack\DeploymentData.json'
```

Si le fichier DeploymentData.json contient le préfixe d’attribution de nom pour la machine virtuelle de l’OAW, cette valeur sera utilisée pour le paramètre **VirtualMachineName**. Dans le cas contraire, le nom par défaut est **AzSOAW** ou n’importe quel nom spécifié par l’utilisateur.

Deux jeux de paramètres sont disponibles pour New-OAW. Les paramètres facultatifs sont indiqués entre crochets.

```powershell
New-OAW 
-LocalAdministratorPassword <Security.SecureString> `
[-AzureStackCertificatePath <String>] `
[-AzSStampInfoFilePath <String>] `
[-CertificatePassword <Security.SecureString>] `
[-ERCSVMIP <String[]>] `
[-DNS <String[]>] `
[-DeploymentDataFilePath <String>] `
[-SkipNetworkConfiguration] `
[-ImageFilePath <String>] `
[-VirtualMachineName <String>] `
[-VirtualMachineMemory <int64>] `
[-VirtualProcessorCount <int>] `
[-VirtualMachineDiffDiskPath <String>] `
[-PhysicalAdapterMACAddress <String>] `
[-VirtualSwitchName <String>] `
[-ReCreate] `
[-AsJob] `
[-Passthru] `
[-WhatIf] `
[-Confirm] `
[<CommonParameters>]
```

```powershell
New-OAW
-LocalAdministratorPassword <Security.SecureString> `
-IPAddress <String> `
-SubnetMask <String> `
-DefaultGateway <String> `
-DNS <String[]> `
[-AzureStackCertificatePath <String>] `
[-AzSStampInfoFilePath <String>] `
[-CertificatePassword <Security.SecureString>] `
[-ERCSVMIP <String[]>] `
[-ImageFilePath <String>] `
[-VirtualMachineName <String>] `
[-VirtualMachineMemory <int64>] `
[-VirtualProcessorCount <int>] `
[-VirtualMachineDiffDiskPath <String>] `
[-PhysicalAdapterMACAddress <String>] `
[-VirtualSwitchName <String>] `
[-ReCreate] `
[-AsJob] `
[-Passthru] `
[-WhatIf] `
[-Confirm] `
[<CommonParameters>]
```

Le tableau suivant répertorie la définition de chaque paramètre.

| Paramètre   | Obligatoire ou facultatif  | Description       |
|-------------|--------------------|-------------------|
| LocalAdministratorPassword | Obligatoire | Mot de passe du compte administrateur local AdminUser des machines virtuelles. |
| IPAddress                  | Obligatoire | Adresse IPv4 statique pour configurer TCP/IP sur la machine virtuelle.                                                |
| SubnetMask                 | Obligatoire | Masque de sous-réseau IPv4 pour configurer TCP/IP sur la machine virtuelle.                                                   |
| DefaultGateway             | Obligatoire | Adresse IPv4 de la passerelle par défaut pour configurer TCP/IP sur la machine virtuelle.                                    |
| DNS                        | Obligatoire | Le ou les serveurs DNS pour configurer TCP/IP sur la machine virtuelle.                                                          |
| ImageFilePath              | Facultatif | Chemin d’accès au fichier OAW.vhdx fourni par Microsoft. La valeur par défaut est **OAW.vhdx** sous le même dossier parent de ce script. |
| VirtualMachineName         | Facultatif | Le nom à attribuer à la machine virtuelle. Si le préfixe de nom se trouve dans le fichier DeploymentData.json, il sera utilisé comme nom par défaut. Dans le cas contraire, **AzSOAW** sera utilisé comme nom par défaut. Vous pouvez spécifier un autre nom pour remplacer la valeur par défaut. |
| VirtualMachineMemory       | Facultatif | La mémoire à attribuer à la machine virtuelle. La valeur par défaut est **4Go**.                            |
| VirtualProcessorCount      | Facultatif | Nombre de processeurs virtuels à attribuer à la machine virtuelle. La valeur par défaut est **8**.        |
| VirtualMachineDiffDiskPath | Facultatif | Chemin d’accès pour stocker les fichiers de disque diff temporaires pendant que la machine virtuelle de gestion était active. La valeur par défaut est le sous-répertoire **DiffDisks** sous le même dossier parent de ce script. |
| AzureStackCertificatePath  | Facultatif | Chemin d’accès aux certificats à importer sur la machine virtuelle pour l’accès à Azure Stack Hub. |
| AzSStampInfoFilePath       | Facultatif | Chemin d’accès du fichier AzureStackStampInformation.json dans lequel le script peut récupérer les adresses IP de la machine virtuelle ERCS. |
| CertificatePassword        | Facultatif | Mot de passe du certificat à importer sur la machine virtuelle pour l’accès à Azure Stack Hub. |
| ERCSVMIP                   | Facultatif | Adresse IP des machines virtuelles ERCS Azure Stack Hub à ajouter à la liste des hôtes approuvés de la machine virtuelle. Ne prend pas effet si **-SkipNetworkConfiguration** est défini. |
SkipNetworkConfiguration     | Facultatif | Ignore la configuration réseau de la machine virtuelle afin que l’utilisateur puisse la configurer ultérieurement. |
| DeploymentDataFilePath     | Facultatif | Chemin d’accès de DeploymentData.json. Ne prend pas effet si **-SkipNetworkConfiguration** est défini.            |
| PhysicalAdapterMACAddress  | Facultatif | L’adresse MAC de la carte réseau de l’hôte qui sera utilisée pour y connecter la machine virtuelle.<br>- S’il n’y a qu’une seule carte réseau physique, ce paramètre n’est pas nécessaire et le seul adaptateur réseau est utilisé.<br>- S’il y a plus d’une carte réseau physique, ce paramètre est requis pour spécifier lequel utiliser.<br> |
| VirtualSwitchName          | Facultatif | Nom du commutateur virtuel qui doit être configuré dans Hyper-V pour la machine virtuelle.<br>- S’il existe un VMSwitch avec le nom fourni, un tel VMSwitch est sélectionné.<br>- S’il n’existe aucun VMSwitch avec le nom fourni, un VMSwitch sera créé avec le nom fourni.<br> |
| ReCreate                   | Facultatif | Supprime et recrée la machine virtuelle s’il existe déjà une machine virtuelle portant le même nom. |

## <a name="check-the-oaw-vm-version"></a>Vérifier la version de la machine virtuelle de l’OAW

1. Connectez-vous à la machine virtuelle de l’OAW avec vos informations d’identification.
1. Ouvrez PowerShell ISE et exécutez le script suivant :

   ```powershell
   'C:\Version\Get-Version.ps1'
   ```

   Par exemple :

   ![Capture d’écran de la cmdlet PowerShell pour vérifier la version de l’hôte du cycle de vie du matériel](./media/operator-access-workstation/check-operator-access-workstation-vm-version.png)

## <a name="transfer-files-between-the-hlh-and-oaw"></a>Transfert de fichiers entre HLH et l'OAW

Si vous avez besoin de transférer des fichiers entre le HLH et l'OAW, créez un partage SMB à l’aide de l’applet de commande [New-SmbShare](/powershell/module/smbshare/new-smbshare?view=win10-ps). New-SmbShare expose un dossier de système de fichiers aux clients distants en tant que partage de protocole SMB (Server Message Block). Par exemple :

Pour supprimer un partage qui a été créé par cette applet de commande, utilisez l’applet de commande [Remove-SmbShare](/powershell/module/smbshare/remove-smbshare?view=win10-ps). Par exemple :

## <a name="remove-the-oaw-vm"></a>Supprimer la machine virtuelle de l’OAW

Le script suivant supprime la machine virtuelle de l’OAW, utilisée pour accéder à Azure Stack Hub pour l’administration et les diagnostics. Ce script supprime également les fichiers de disque et le gardien associés à la machine virtuelle.

1. Connectez-vous à HLH avec vos informations d’identification.
1. Ouvrez une session PowerShell avec élévation de privilèges. 
1. Accédez au contenu extrait du fichier OAW.zip installé.
1. Supprimez la machine virtuelle en exécutant le script Remove-OAW.ps1 : 

   ```powershell
   Remove-OAW.ps1 -VirtualMachineName <name>
   ```

   Où \<name\> est le nom de la machine virtuelle à supprimer. Par défaut, le nom est **AzSOAW**.

   Par exemple :

   ```powershell
   Remove-OAW.ps1 -VirtualMachineName AzSOAW
   ```

## <a name="next-steps"></a>Étapes suivantes

[Tâches de gestion Azure Stack](azure-stack-manage-basics.md)
