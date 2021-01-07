---
title: Activer la sauvegarde pour Azure Stack Hub avec PowerShell
description: Découvrez comment activer le service Infrastructure Backup avec PowerShell pour permettre la restauration d’Azure Stack Hub en cas de panne.
author: PatAltimore
ms.topic: article
ms.date: 04/25/2019
ms.author: patricka
ms.reviewer: hectorl
ms.lastreviewed: 03/14/2019
ms.openlocfilehash: 0e1bdbf55556d8168e517a5d9cfb30d6747c02f3
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97871750"
---
# <a name="enable-backup-for-azure-stack-hub-with-powershell"></a>Activer la sauvegarde pour Azure Stack Hub avec PowerShell

Activez le service Infrastructure Backup avec Windows PowerShell pour sauvegarder régulièrement :
 - Le service d’identité interne et le certificat racine.
 - Les plans, offres et abonnements de l’utilisateur.
 - Les quotas de calcul, de stockage et d’utilisateurs réseau.
 - Les secrets utilisateur Key Vault.
 - Les stratégies et rôles utilisateur RBAC.
 - Les comptes de stockage utilisateur.

Vous pouvez accéder aux cmdlets PowerShell pour activer la sauvegarde, démarrer la sauvegarde et obtenir des informations sur la sauvegardes via le point de terminaison de gestion d’opérateur.

## <a name="prepare-powershell-environment"></a>Préparer l’environnement PowerShell

Pour obtenir des instructions sur la configuration de l’environnement PowerShell, voir [Installer PowerShell pour Azure Stack Hub](powershell-install-az-module.md). Pour vous connecter à Azure Stack Hub, voir [Configurer l’environnement de l’opérateur et se connecter à Azure Stack Hub](azure-stack-powershell-configure-admin.md).

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>Indiquer le partage de sauvegarde, les informations d’identification et la clé de chiffrement pour activer la sauvegarde

Dans la même session PowerShell, modifiez le script PowerShell suivant en ajoutant les variables de votre environnement. Exécutez le script mis à jour pour indiquer le partage de sauvegarde, les informations d’identification et la clé de chiffrement pour le service de sauvegarde d’infrastructure.

| Variable        | Description   |
|---              |---                                        |
| `$username`       | Saisissez le **Nom d’utilisateur** à l’aide du domaine et du nom d’utilisateur de l’emplacement du lecteur partagé avec un accès suffisant pour lire et écrire des fichiers. Par exemple : `Contoso\backupshareuser`. |
| `$password`       | Saisissez le **mot de passe** de l’utilisateur. |
| `$sharepath`      | Saisissez le chemin d’accès à l’**emplacement de stockage de sauvegarde**. Vous devez utiliser une chaîne UNC (Universal Naming Convention) pour le chemin d’un partage de fichiers hébergé sur un appareil distinct. Une chaîne UNC spécifie l’emplacement de ressources telles que des appareils ou des fichiers partagés. Pour garantir la disponibilité des données de sauvegarde, l’appareil doit se trouver dans un emplacement distinct. |
| `$frequencyInHours` | La fréquence en heures détermine la fréquence à laquelle les sauvegardes sont créées. La valeur par défaut est 12. Scheduler prend en charge un maximum de 12 heures et un minimum de 4 heures.|
| `$retentionPeriodInDays` | La période de rétention en jours détermine le nombre de jours pendant lesquels les sauvegardes sont conservées sur l’emplacement externe. La valeur par défaut est 7. Scheduler prend en charge un maximum de 14 jours et un minimum de 2 jours. Les sauvegardes antérieures à la période de rétention sont automatiquement supprimées de l’emplacement externe.|
| `$encryptioncertpath` | S’applique aux versions 1901 et ultérieures. Paramètre disponible dans le module Azure Stack Hub versions 1.7 et ultérieures. Le chemin du certificat de chiffrement spécifie le chemin du fichier .CER avec la clé publique utilisée pour le chiffrement des données. |
| `$encryptionkey` | S’applique à la build 1811 et builds antérieures. Paramètre disponible dans le module Azure Stack Hub versions 1.6 et antérieures. La clé de chiffrement est utilisée pour le chiffrement des données. Pour créer une nouvelle clé, utilisez la cmdlet [New-AzsEncryptionKeyBase64](/powershell/module/azs.backup.admin/new-azsencryptionkeybase64). |
|     |     |

### <a name="enable-backup-on-1901-and-later-using-certificate"></a>Activer la sauvegarde sur la version 1901 et ultérieur à l’aide d’un certificat
```powershell
    # Example username:
    $username = "domain\backupadmin"
 
    # Example share path:
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

    $password = Read-Host -Prompt ("Password for: " + $username) -AsSecureString

    # Create a self-signed certificate using New-SelfSignedCertificate, export the public key portion and save it locally.

    $cert = New-SelfSignedCertificate `
        -DnsName "www.contoso.com" `
        -CertStoreLocation "cert:\LocalMachine\My" 

    New-Item -Path "C:\" -Name "Certs" -ItemType "Directory" 

    #make sure to export the PFX format of the certificate with the public and private keys and then delete the certificate from the local certificate store of the machine where you created the certificate
    
    Export-Certificate `
        -Cert $cert `
        -FilePath c:\certs\AzSIBCCert.cer 

    # Set the backup settings with the name, password, share, and CER certificate file.
    Set-AzsBackupConfiguration -BackupShare $sharepath -Username $username -Password $password -EncryptionCertPath "c:\temp\cert.cer"
```
### <a name="enable-backup-on-1811-or-earlier-using-certificate"></a>Activer la sauvegarde sur 1811 et les versions antérieures, à l’aide d’un certificat
```powershell
    # Example username:
    $username = "domain\backupadmin"
 
    # Example share path:
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

    $password = Read-Host -Prompt ("Password for: " + $username) -AsSecureString

    # Create a self-signed certificate using New-SelfSignedCertificate, export the public key portion and save it locally.

    $key = New-AzsEncryptionKeyBase64
    $Securekey = ConvertTo-SecureString -String ($key) -AsPlainText -Force

    # Set the backup settings with the name, password, share, and CER certificate file.
    Set-AzsBackupConfiguration -BackupShare $sharepath -Username $username -Password $password -EncryptionKey $Securekey
```

   
##  <a name="confirm-backup-settings"></a>Confirmer les paramètres de sauvegarde

Dans la même session PowerShell, exécutez les commandes suivantes :

   ```powershell
    Get-AzsBackupConfiguration | Select-Object -Property Path, UserName
   ```

Le résultat doit ressembler à l’exemple de sortie suivant :

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
   ```

## <a name="update-backup-settings"></a>Mettre à jour les paramètres de sauvegarde
Dans la même session PowerShell, vous pouvez mettre à jour les valeurs par défaut pour la période de rétention et la fréquence des sauvegardes. 

   ```powershell
    #Set the backup frequency and retention period values.
    $frequencyInHours = 10
    $retentionPeriodInDays = 5

    Set-AzsBackupConfiguration -BackupFrequencyInHours $frequencyInHours -BackupRetentionPeriodInDays $retentionPeriodInDays

    Get-AzsBackupConfiguration | Select-Object -Property Path, UserName, AvailableCapacity, BackupFrequencyInHours, BackupRetentionPeriodInDays
   ```

Le résultat doit ressembler à l’exemple de sortie suivant :

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
    AvailableCapacity           : 60 GB
    BackupFrequencyInHours      : 10
    BackupRetentionPeriodInDays : 5
   ```

### <a name="azure-stack-hub-powershell"></a>Azure Stack Hub PowerShell 
La cmdlet PowerShell permettant de configurer la sauvegarde de l'infrastructure est Set-AzsBackupConfiguration. Dans les versions précédentes, il s'agissait de la cmdlet Set-AzsBackupShare. Cette cmdlet nécessite la présentation d'un certificat. Si la sauvegarde de l’infrastructure est configurée avec une clé de chiffrement, vous ne pouvez pas mettre à jour la clé de chiffrement ni voir la propriété. Vous devrez utiliser PowerShell version 1.6 en mode Administrateur.

Si la sauvegarde de l’infrastructure a été configurée avant la mise à jour vers la version 1901, vous pouvez utiliser la version 1.6 du mode Administrateur de PowerShell pour configurer et voir la clé de chiffrement. La version 1.6 ne vous permettra pas de procéder à la mise à jour en passant d’une clé de chiffrement à un fichier de certificat.
Pour plus d’informations sur l’installation de la version appropriée du module, voir [Installer Azure Stack Hub PowerShell](powershell-install-az-module.md).


## <a name="next-steps"></a>Étapes suivantes

Pour exécuter une sauvegarde, voir [Sauvegarder Azure Stack Hub](azure-stack-backup-back-up-azure-stack.md).

Pour savoir comment vérifier que votre sauvegarde a été exécutée, voir [Confirmer que la sauvegarde est terminée dans le portail d’administration](azure-stack-backup-back-up-azure-stack.md).
