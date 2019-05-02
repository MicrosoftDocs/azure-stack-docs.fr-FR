---
title: Valider une sauvegarde Azure Stack à l’aide du Kit de développement Azure Stack (ASDK) | Microsoft Docs
description: Comment valider une sauvegarde de systèmes intégrés Azure Stack à l’aide du Kit de développement Azure Stack (ASDK).
services: azure-stack
author: WenJason
manager: digimobile
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
origin.date: 02/15/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: hectorl
ms.lastreviewed: 02/15/2019
ms.openlocfilehash: b2f85366ed9d5f3b526549b220fe6b55f5e6c13f
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "64308975"
---
# <a name="use-the-asdk-to-validate-an-azure-stack-backup"></a>Utiliser l’ASDK pour valider une sauvegarde Azure Stack
Après déploiement d’Azure Stack et approvisionnement de ressources utilisateur telles que des offres, des plans, des quotas et des abonnements, vous devez [activer la sauvegarde d’infrastructure Azure Stack](../operator/azure-stack-backup-enable-backup-console.md). La planification et l’exécution de sauvegardes d’infrastructure régulières garantit que les données de gestion d’infrastructure ne seront pas perdues en cas de défaillance catastrophique du matériel ou du service.

> [!TIP]
> Avant d’entamer cette procédure, nous vous recommandons d’[exécuter une sauvegarde à la demande](../operator/azure-stack-backup-back-up-azure-stack.md) afin de disposer d’une copie des données d’infrastructure les plus récentes. Une fois la sauvegarde terminée avec succès, veillez à capturer l’ID de sauvegarde. Cet ID sera nécessaire lors de la récupération à partir du cloud. 

Les sauvegardes d’infrastructure Azure Stack contiennent des données importantes sur votre cloud, qui peuvent être restaurées lors du redéploiement d’Azure Stack. L’ASDK permet de valider ces sauvegardes sans que cela ait d’incidence sur votre cloud de production. 

La validation de sauvegardes sur l’ASDK est prise en charge pour les scénarios suivants :

|Scénario|Objectif|
|-----|-----|
|Valider des sauvegardes d’infrastructure à partir d’une solution intégrée.|Effectuer une validation de courte durée du fait que les données dans la sauvegarde sont valides.|
|Découvrir le flux de travail de récupération de bout en bout.|Utiliser l’ASDK pour valider l’ensemble de l’expérience de sauvegarde et de restauration.|
|     |     |

Le scénario suivant **n’est pas** pris en charge lors de la validation de sauvegardes sur l’ASDK :

|Scénario|Objectif|
|-----|-----|
|Build de l’ASDK pour la sauvegarde et la restauration de build.|Restaurer des données de sauvegarde d’une version antérieure vers une version plus récente de l’ASDK.|
|     |     |


## <a name="cloud-recovery-deployment"></a>Déploiement de récupération cloud
Vous pouvez valide les sauvegardes d’infrastructure de votre déploiement de systèmes intégrés en effectuant un déploiement de récupération cloud de l’ASDK. Dans ce type de déploiement, des données de service spécifiques sont restaurées à partir de la sauvegarde, une fois l’ASDK installé sur l’ordinateur hôte.

### <a name="prereqs"></a>Prérequis de la récupération cloud
Avant de commencer un déploiement de récupération cloud de l’ASDK, vérifiez que vous disposez des informations suivantes :

**Exigences du programme d’installation de l’interface utilisateur**

*Le programme d’installation actuel de l’interface utilisateur prend uniquement en charge la clé de chiffrement*

|Configuration requise|Description|
|-----|-----|
|Chemin du partage de la sauvegarde|Chemin d'accès du partage de fichiers UNC de la dernière sauvegarde Azure Stack qui sera utilisée pour récupérer les informations d’infrastructure d’Azure Stack. Ce partage local sera créé pendant le processus de déploiement de la récupération cloud.|
|ID de la sauvegarde à restaurer|ID de sauvegarde, sous la forme alphanumérique « xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx », identifiant la sauvegarde à restaurer lors de la récupération cloud.|
|Adresse IP du serveur de temps|Une adresse IP de serveur de temps valide, telle que 132.163.97.2, est requise pour le déploiement d’Azure Stack.|
|Mot de passe du certificat externe|Mot de passe du certificat externe utilisé par Azure Stack. La sauvegarde de l’autorité de certification contient des certificats externes qui doivent être restaurés avec ce mot de passe.|
|Clé de chiffrement de la sauvegarde|Obligatoire si vous avez effectué une mise à niveau vers Azure Stack version 1901 ou ultérieure et si les paramètres de sauvegarde sont toujours configurés avec une clé de chiffrement. La clé de chiffrement est dépréciée depuis la version 1901. Le programme d’installation prendra en charge la clé de chiffrement en mode de compatibilité descendante pour au moins 3 publications de version. Dès lors que vous mettez à jour les paramètres de sauvegarde dans l’optique d’utiliser un certificat, consultez le tableau suivant pour en savoir plus sur les informations obligatoires.|

|     |     | 

**Exigences du programme d’installation de PowerShell**

*Le programme d’installation actuel de PowerShell prend en charge la clé de chiffrement ou le certificat de déchiffrement*

|Configuration requise|Description|
|-----|-----|
|Chemin du partage de la sauvegarde|Chemin d'accès du partage de fichiers UNC de la dernière sauvegarde Azure Stack qui sera utilisée pour récupérer les informations d’infrastructure d’Azure Stack. Ce partage local sera créé pendant le processus de déploiement de la récupération cloud.|
|ID de la sauvegarde à restaurer|ID de sauvegarde, sous la forme alphanumérique « xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx », identifiant la sauvegarde à restaurer lors de la récupération cloud.|
|Adresse IP du serveur de temps|Une adresse IP de serveur de temps valide, telle que 132.163.97.2, est requise pour le déploiement d’Azure Stack.|
|Mot de passe du certificat externe|Mot de passe du certificat externe utilisé par Azure Stack. La sauvegarde de l’autorité de certification contient des certificats externes qui doivent être restaurés avec ce mot de passe.|
|Mot de passe de certification de déchiffrement|facultatif. Obligatoire uniquement si la sauvegarde est chiffrée à l’aide d’un certificat. Le mot de passe est destiné au certificat auto-signé (.pfx) qui contient la clé privée nécessaire au déchiffrement des données de sauvegarde.|
|Clé de chiffrement de la sauvegarde|facultatif. Obligatoire si vous avez effectué une mise à niveau vers Azure Stack version 1901 ou ultérieure et si les paramètres de sauvegarde sont toujours configurés avec une clé de chiffrement. Le programme d’installation prendra en charge la clé de chiffrement en mode de compatibilité descendante pour au moins 3 publications de version. Dès lors que vous mettez à jour les paramètres de sauvegarde dans l’optique d’utiliser un certificat, vous devez fournir le mot de passe pour le certificat de déchiffrement.|
|     |     | 

## <a name="prepare-the-host-computer"></a>Préparer l’ordinateur hôte 
Comme pour un déploiement normal de l’ASDK, l’environnement du système hôte de l’ASDK doit être préparé pour l’installation. Une fois préparé, l’ordinateur hôte du kit de développement Azure Stack démarre à partir du disque dur de la machine virtuelle CloudBuilder.vhdx pour commencer le déploiement du kit.

Sur l’ordinateur hôte de l’ASDK, téléchargez un nouveau fichier cloudbuilder.vhdx de la même version que l’Azure Stack sauvegardé, puis suivez les instructions de [préparation de l’ordinateur hôte de l’ASDK](asdk-prepare-host.md).

Lorsque le serveur hôte redémarre à partir du fichier cloudbuilder.vhdx, vous devez créer un partage de fichiers et y copier vos données de sauvegarde. Le partage de fichiers doit être accessible au compte exécutant le programme d’installation, à savoir le compte Administrator dans ces exemples de commandes PowerShell : 

```powershell
$shares = New-Item -Path "c:\" -Name "Shares" -ItemType "directory"
$azsbackupshare = New-Item -Path $shares.FullName -Name "AzSBackups" -ItemType "directory"
New-SmbShare -Path $azsbackupshare.FullName -FullAccess ($env:computername + "\Administrator")  -Name "AzSBackups"
```

Ensuite, copiez vos derniers fichiers de sauvegarde Azure Stack vers le partage nouvellement créé. La structure de dossiers au sein du partage doit être la suivante : `\\<ComputerName>\AzSBackups\MASBackup\<BackupID>\`.

Pour finir, copiez le certificat de déchiffrement (.pfx) dans le répertoire du certificat `C:\CloudDeployment\Setup\Certificates\` et renommez le fichier `BackupDecryptionCert.pfx`.

## <a name="deploy-the-asdk-in-cloud-recovery-mode"></a>Déployer l’ASDK en mode de récupération cloud

> [!IMPORTANT]
> 1. L’interface utilisateur du programme d’installation actuel prend uniquement en charge la clé de chiffrement. Vous pouvez valider uniquement les sauvegardes des systèmes qui continuent d’utiliser la clé de chiffrement. Si la sauvegarde a été chiffrée sur un système intégré ou le kit ASDK à l’aide du certificat, vous devez utiliser le programme d’installation de PowerShell (**InstallAzureStackPOC.ps1**). 
> 2. Le programme d’installation de PowerShell (**InstallAzureStackPOC.ps1**) prend en charge le certificat ou la clé de chiffrement.
> 3. L’installation d’ASDK prend en charge une seule carte réseau de mise en réseau. Si vous avez plusieurs cartes réseau, vérifiez qu’une seule d’entre elles est activée (et que toutes les autres sont désactivées) avant d’exécuter le script de déploiement.

### <a name="use-the-installer-ui-to-deploy-the-asdk-in-recovery-mode"></a>Utiliser l’interface utilisateur du programme d’installation pour déployer le kit ASDK en mode de récupération
Cette section explique étape par étape comment déployer le kit ASDK à l’aide d’une interface graphique utilisateur (GUI) installable en téléchargeant et en exécutant le script PowerShell **asdk-installer.ps1**.

> [!NOTE]
> L’interface utilisateur du programme d’installation du Kit de développement Azure Stack est un script open source basé sur WCF et PowerShell.

> [!IMPORTANT]
> L’interface utilisateur du programme d’installation actuel prend uniquement en charge la clé de chiffrement.

1. Une fois que l’ordinateur hôte a correctement démarré dans l’image CloudBuilder.vhdx, connectez-vous avec les informations d’identification d’administration spécifiées lorsque vous avez [préparé l’ordinateur hôte du kit de développement](asdk-prepare-host.md) en vue de l’installation du kit ASDK. Il doit s’agir des mêmes informations d’identification d’administrateur local que celles de l’hôte du Kit de développement.
2. Ouvrez une console PowerShell avec élévation de privilèges et exécutez le script PowerShell **&lt;lettre de lecteur>\AzureStack_Installer\asdk-installer.ps1**. Le script peut maintenant se trouver sur un lecteur autre que C:\ dans l’image CloudBuilder.vhdx. Cliquez sur **Restaurer**.

    ![Script du programme d’installation ASDK](media/asdk-validate-backup/1.PNG) 

3. Entrez les informations relatives à votre annuaire Azure AD (facultatives) et le mot de passe d’administrateur local de l’ordinateur hôte ASDK sur la page du fournisseur d’identité et des informations d’identification. Cliquez sur **Suivant**.

    ![Page Identité et informations d'identification](media/asdk-validate-backup/2.PNG) 

4. Sélectionnez la carte réseau que l’ordinateur hôte ASDK utilisera, puis cliquez sur **Suivant**. Toutes les autres interfaces réseau seront désactivées durant l’installation du kit ASDK. 

    ![Interface de la carte réseau](media/asdk-validate-backup/3.PNG) 

5. Sur la page Configuration réseau, indiquez un serveur de temps valide et l’adresse IP des redirecteurs DNS. Cliquez sur **Suivant**.

    ![Page Configuration réseau](media/asdk-validate-backup/4.PNG) 

6. Une fois les propriétés de la carte d’interface réseau vérifiées, cliquez sur **Suivant**. 

    ![Vérification des paramètres de la carte réseau](media/asdk-validate-backup/5.PNG) 

7. Indiquez les informations requises, décrites précédemment dans la section [Prérequis](#prereqs), sur la page Paramètres de sauvegarde, ainsi que le nom d’utilisateur et le mot de passe à utiliser pour accéder au partage. Cliquez sur **Suivant** : 

   ![Page Paramètres de sauvegarde](media/asdk-validate-backup/6.PNG) 

8. Vérifiez le script de déploiement à utiliser pour déployer le kit ASDK sur la page Résumé. Cliquez sur **Déployer** pour commencer le déploiement. 

    ![Page de résumé](media/asdk-validate-backup/7.PNG) 


### <a name="use-powershell-to-deploy-the-asdk-in-recovery-mode"></a>Utiliser PowerShell pour déployer le kit ASDK en mode de récupération

Modifiez les commandes PowerShell suivantes pour votre environnement, puis exécutez-les pour déployer l’ASDK en mode de récupération cloud :

**Utilisez le script InstallAzureStackPOC.ps1 pour lancer la récupération cloud avec une clé de chiffrement.**

```powershell
cd C:\CloudDeployment\Setup     
$adminpass = Read-Host -AsSecureString -Prompt "Local Administrator password"
$certPass = Read-Host -AsSecureString -Prompt "Password for the external certificate"
$backupstorecredential = Read-Host -AsSecureString -Prompt "Credential for backup share"
$key = Read-Host -AsSecureString -Prompt "Your backup encryption key"

.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass `
 -BackupStorePath ("\\" + $env:COMPUTERNAME + "\AzSBackups") `
 -BackupEncryptionKeyBase64 $key `
 -BackupStoreCredential $backupstorecredential `
 -BackupId "<Backup ID to restore>" `
 -TimeServer "<Valid time server IP>" -ExternalCertPassword $certPass
```

**Utilisez le script InstallAzureStackPOC.ps1 pour lancer la récupération cloud avec un certificat de déchiffrement.**

```powershell
cd C:\CloudDeployment\Setup     
$adminpass = Read-Host -AsSecureString -Prompt "Local Administrator password"
$certPass = Read-Host -AsSecureString -Prompt "Password for the external certificate"
$backupstorecredential = Read-Host -AsSecureString -Prompt "Credential for backup share"
$decryptioncertpassword  = Read-Host -AsSecureString -Prompt "Password for the decryption certificate"

.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass `
 -BackupStorePath ("\\" + $env:COMPUTERNAME + "\AzSBackups") `
 -BackupDecryptionCertPassword $decryptioncertpassword `
 -BackupStoreCredential $backupstorecredential `
 -BackupId "<Backup ID to restore>" `
 -TimeServer "<Valid time server IP>" -ExternalCertPassword $certPass
```

## <a name="complete-cloud-recovery"></a>Effectuer la récupération cloud 
Après un déploiement de récupération cloud réussi, vous devez effectuer la restauration à l’aide de cmdlet **restauration-AzureStack**. 

Après vous être connecté en tant qu’opérateur Azure Stack, [installez Azure Stack PowerShell](asdk-post-deploy.md#install-azure-stack-powershell) et exécutez les commandes suivantes pour spécifier le certificat et le mot de passe à utiliser pendant la restauration de la sauvegarde :

**Mode de récupération avec fichier de certificat**

> [!NOTE] 
> Le déploiement d’Azure Stack ne conserve pas le certificat de déchiffrement pour des raisons de sécurité. Vous devrez fournir à nouveau le certificat de déchiffrement et le mot de passe associé.

```powershell
$decryptioncertpassword = Read-Host -AsSecureString -Prompt "Password for the decryption certificate"
Restore-AzsBackup -ResourceId "<BackupID>" `
 -DecryptionCertPath "<path to decryption certificate with file name (.pfx)>" `
 -DecryptionCertPassword $decryptioncertpassword
```

**Mode de récupération avec clé de chiffrement**
```powershell
$decryptioncertpassword = Read-Host -AsSecureString -Prompt "Password for the decryption certificate"
Restore-AzsBackup -ResourceId "<BackupID>"
```

Attendez 60 minutes après l’appel de cette cmdlet pour démarrer la vérification des données de sauvegarde sur l’ASDK récupéré du cloud.

## <a name="next-steps"></a>Étapes suivantes
[Inscrire Azure Stack](asdk-register.md)

