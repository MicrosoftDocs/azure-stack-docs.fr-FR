---
title: Activer la sauvegarde d’Azure Stack avec PowerShell - MDC
description: Découvrez comment activer le service Infrastructure Backup avec PowerShell pour permettre la restauration d’Azure Stack en cas de panne. Pour un Modular Data Center (MDC).
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2020
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 10/27/2020
ms.openlocfilehash: f3d28fa9f5610f91ab8b35ad017c836745f5117d
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910872"
---
# <a name="configure-backup-for-azure-stack-with-powershell---modular-data-center-mdc"></a>Configurer la sauvegarde d’Azure Stack avec PowerShell - Modular Data Center (MDC)

*S’applique à : Modular Data Center, Azure Stack Hub renforcé*

Vous pouvez configurer le service Infrastructure Backup pour exporter les sauvegardes d’infrastructure vers un emplacement de stockage externe, à l’aide de PowerShell. Les sauvegardes d’infrastructure peuvent être utilisées par le support pour corriger les services dégradés.

## <a name="prepare-powershell-environment"></a>Préparer l’environnement PowerShell

Pour obtenir des instructions sur la configuration de l’environnement PowerShell, consultez [Installer PowerShell pour Azure Stack](../../operator/powershell-install-az-module.md). Pour vous connecter à Azure Stack, consultez [Configurer l’environnement de l’opérateur et se connecter à Azure Stack](../../operator/azure-stack-powershell-configure-admin.md).

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>Indiquer le partage de sauvegarde, les informations d’identification et la clé de chiffrement pour activer la sauvegarde

Dans la même session PowerShell, modifiez le script PowerShell suivant en ajoutant les variables de votre environnement. Exécutez le script mis à jour pour indiquer le partage de sauvegarde, les informations d’identification et la clé de chiffrement pour le service de sauvegarde d’infrastructure.

|Variable  |Description  |
|---------|---------|
|$username     | Saisissez le **Nom d’utilisateur** à l’aide du domaine et du nom d’utilisateur de l’emplacement du lecteur partagé avec un accès suffisant pour lire et écrire des fichiers. Par exemple, Contoso\\backupshareuser.        |
|$password     | Saisissez le **mot de passe** de l’utilisateur.        |
|$path     | Saisissez le chemin d’accès à l’**emplacement de stockage de sauvegarde**. Vous devez utiliser une chaîne UNC (Universal Naming Convention) pour le chemin d’un partage de fichiers hébergé sur un appareil distinct. Une chaîne UNC spécifie l’emplacement de ressources telles que des appareils ou des fichiers partagés. Pour garantir la disponibilité des données de sauvegarde, l’appareil doit se trouver dans un emplacement distinct.        |
|$backupfrequencyinhours     | La fréquence en heures détermine la fréquence à laquelle les sauvegardes sont créées. La valeur par défaut est 12. Scheduler prend en charge un minimum de 4 jours et un maximum de 12 jours.        |
|$backupretentionperiodindays     | La période de rétention en jours détermine le nombre de jours pendant lesquels les sauvegardes sont conservées sur l’emplacement externe. La valeur par défaut est 7. Scheduler prend en charge un minimum de 2 jours et un maximum de 14 jours. Les sauvegardes antérieures à la période de rétention sont automatiquement supprimées de l’emplacement externe.        |
|$encryptioncertpath     | Certificat fourni lors du déploiement. Il n’est pas nécessaire de fournir un nouveau certificat lors de la configuration de l’emplacement de stockage externe. Le chemin du certificat de chiffrement spécifie le chemin du fichier .CER avec la clé publique utilisée pour le chiffrement des données.        |
|$isbackupschedulerenabled     | Active/désactive les sauvegardes automatiques. Les sauvegardes automatiques sont activées par défaut une fois le partage et les informations d’identification fournis.        |

## <a name="configure-backup"></a>Configurer une sauvegarde

```powershell
# Example username
$username = "domain\backupadmin"

# Example share path
$sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

$password = Read-Host -Prompt ("Password for: " + $username) -AsSecureString

# Set the backup settings with the name, password, share, and CER certificate file.
Set-AzsBackupConfiguration -BackupShare path -Username $username -Password $password
```

## <a name="confirm-backup-settings"></a>Confirmer les paramètres de sauvegarde

Dans la même session PowerShell, exécutez les commandes suivantes :

```powershell
Get-AzsBackupConfiguration | Select-Object -Property Path, UserName
```

Le résultat doit ressembler à l’exemple de sortie suivant :

```shell
Path : \\serverIP\AzsBackupStore\contoso.com\seattle
UserName : domain\backupadmin
```

## <a name="update-backup-settings"></a>Mettre à jour les paramètres de sauvegarde

Dans la même session PowerShell, vous pouvez mettre à jour les valeurs par défaut pour la période de rétention et la fréquence des sauvegardes :

```powershell
# Set the backup frequency and retention period values.
$frequencyInHours = 10
$retentionPeriodInDays = 5

Set-AzsBackupConfiguration -BackupFrequencyInHours $backupfrequencyInHours -BackupRetentionPeriodInDays $backupretentionPeriodInDays

Get-AzsBackupConfiguration | Select-Object -Property Path, UserName, AvailableCapacity, BackupFrequencyInHours, BackupRetentionPeriodInDays
```

## <a name="update-encryption-certificate"></a>Mettre à jour le certificat de chiffrement

Dans la même session PowerShell, vous pouvez mettre à jour le certificat de chiffrement utilisé pour chiffrer les données de sauvegarde. Seules les nouvelles sauvegardes utiliseront le nouveau certificat de chiffrement. Toutes les sauvegardes existantes resteront chiffrées avec l’ancienne clé de chiffrement. Veillez à conserver une copie du certificat précédent pendant au moins un mois pour vous assurer que les sauvegardes précédentes chiffrées avec l’ancien certificat ont été purgées :

```powershell
#Set the new encryption certificate by providing local path to CER file.
Set-AzsBackupConfiguration -EncryptionCertPath "c:\temp\cert.cer"
```

## <a name="enable-or-disable-automatic-backups"></a>Activer ou désactiver les sauvegardes automatiques

Dans la même session PowerShell, vous pouvez activer ou désactiver les sauvegardes automatiques. Les sauvegardes automatiques sont activées par défaut.

```powershell
#Disable automatic backups.
Set-AzsBackupConfiguration - Isbackupschedulerenabled $false

#Enable automatic backups.
Set-AzsBackupConfiguration - Isbackupschedulerenabled $true
```

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment vérifier l’exécution de votre sauvegarde, consultez [Vérifier que la sauvegarde est terminée sur le portail d'administration](../../operator/azure-stack-backup-back-up-azure-stack.md).
