---
title: Activer la sauvegarde d’Azure Stack avec PowerShell | Microsoft Docs
description: Activez le service de sauvegarde d’infrastructure avec Windows PowerShell pour permettre la restauration d’Azure Stack en cas de panne.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 03/14/2019
ms.openlocfilehash: 08950940510d874b407448bab37c6a43f6965c7f
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66268946"
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>Activer la sauvegarde d’Azure Stack avec PowerShell

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Activez le service Infrastructure Backup avec Windows PowerShell pour effectuer des sauvegardes périodiques de ce qui suit :
 - Service d’identité interne et certificat racine
 - Plans, offres et abonnements de l’utilisateur
 - Calcul, stockage et quotas d'utilisateurs réseau
 - Secrets utilisateur Key Vault
 - Stratégies et rôles utilisateur RBAC
 - Comptes de stockage utilisateur

Vous pouvez accéder aux cmdlets PowerShell pour activer la sauvegarde, démarrer la sauvegarde et obtenir des informations sur la sauvegardes via le point de terminaison de gestion d’opérateur.

## <a name="prepare-powershell-environment"></a>Préparer l’environnement PowerShell

Pour obtenir des instructions sur la configuration de l’environnement PowerShell, consultez [Installer PowerShell pour Azure Stack](azure-stack-powershell-install.md). Pour vous connecter à Azure Stack, consultez [Configurer l’environnement de l’opérateur et se connecter à Azure Stack](azure-stack-powershell-configure-admin.md).

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>Indiquer le partage de sauvegarde, les informations d’identification et la clé de chiffrement pour activer la sauvegarde

Dans la même session PowerShell, modifiez le script PowerShell suivant en ajoutant les variables de votre environnement. Exécutez le script mis à jour pour indiquer le partage de sauvegarde, les informations d’identification et la clé de chiffrement pour le service de sauvegarde d’infrastructure.

| Variable        | Description   |
|---              |---                                        |
| $username       | Saisissez le **Nom d’utilisateur** à l’aide du domaine et du nom d’utilisateur de l’emplacement du lecteur partagé avec un accès suffisant pour lire et écrire des fichiers. Par exemple : `Contoso\backupshareuser`. |
| $password       | Saisissez le **mot de passe** de l’utilisateur. |
| $sharepath      | Saisissez le chemin d’accès à l’**emplacement de stockage de sauvegarde**. Vous devez utiliser une chaîne UNC (Universal Naming Convention) pour le chemin d’un partage de fichiers hébergé sur un appareil distinct. Une chaîne UNC spécifie l’emplacement de ressources telles que des appareils ou des fichiers partagés. Pour garantir la disponibilité des données de sauvegarde, l’appareil doit se trouver dans un emplacement distinct. |
| $frequencyInHours | La fréquence en heures détermine la fréquence à laquelle les sauvegardes sont créées. La valeur par défaut est 12. Scheduler prend en charge un maximum de 12 heures et un minimum de 4 heures.|
| $retentionPeriodInDays | La période de rétention en jours détermine le nombre de jours pendant lesquels les sauvegardes sont conservées sur l’emplacement externe. La valeur par défaut est 7. Scheduler prend en charge un maximum de 14 jours et un minimum de 2 jours. Les sauvegardes antérieures à la période de rétention sont automatiquement supprimées de l’emplacement externe.|
| $encryptioncertpath | S’applique à 1901 et aux versions ultérieures.  Paramètre disponible dans le module Azure Stack 1.7 et les versions ultérieures. Le chemin du certificat de chiffrement spécifie le chemin du fichier .CER avec la clé publique utilisée pour le chiffrement des données. |
| $encryptionkey | Appliqué à la build 1811 ou versions antérieures. Paramètre disponible dans le module Azure Stack 1.6 et les versions antérieures. La clé de chiffrement utilisée pour le chiffrement des données. Pour créer une nouvelle clé, utilisez la cmdlet [New-AzsEncryptionKeyBase64](https://docs.microsoft.com/powershell/module/azs.backup.admin/new-azsencryptionkeybase64). |
|     |     |

### <a name="enable-backup-on-1901-and-beyond-using-certificate"></a>Activer la sauvegarde sur 1901 et les versions ultérieures, à l’aide d’un certificat
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

### <a name="azure-stack-powershell"></a>Azure Stack PowerShell 
La cmdlet PowerShell permettant de configurer la sauvegarde de l'infrastructure est Set-AzsBackupConfiguration. Dans les versions précédentes, il s'agissait de la cmdlet Set-AzsBackupShare. Cette cmdlet nécessite la présentation d'un certificat. Si la sauvegarde de l'infrastructure est configurée avec une clé de chiffrement, vous ne pouvez pas mettre à jour la clé de chiffrement ni afficher la propriété. Vous devrez utiliser la version 1.6 d'Admin PowerShell. 

Si la sauvegarde de l'infrastructure a été configurée avant la mise à jour vers la version 1901, vous pouvez utiliser la version 1.6 d'Admin PowerShell pour définir et afficher la clé de chiffrement. La version 1.6 ne vous permettra pas de procéder à la mise à jour en passant d'une clé de chiffrement à un fichier de certificat.
Pour plus d'informations sur l'installation de la version appropriée du module, reportez-vous à [Installer Azure Stack PowerShell](azure-stack-powershell-install.md). 


## <a name="next-steps"></a>Étapes suivantes

Pour exécuter une sauvegarde, consultez [Sauvegarde d'Azure Stack](azure-stack-backup-back-up-azure-stack.md).

Pour vérifier que votre sauvegarde a bien été exécutée, consultez [Vérifier que la sauvegarde est terminée sur le portail d'administration](azure-stack-backup-back-up-azure-stack.md).
