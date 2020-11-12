---
title: Préparer des certificats PKI Azure Stack Hub pour un déploiement ou une rotation
titleSuffix: Azure Stack Hub
description: Découvrez comment préparer des certificats PKI pour le déploiement d’Azure Stack Hub ou la rotation des secrets.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 10/19/2020
ms.author: inhenkel
ms.reviewer: ppacent
ms.lastreviewed: 10/19/2020
ms.openlocfilehash: 86e3a87bf869d6bd9980746742a7ba03d142d5fe
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94545021"
---
# <a name="prepare-azure-stack-hub-pki-certificates-for-deployment-or-rotation"></a>Préparer des certificats PKI Azure Stack Hub pour un déploiement ou une rotation

Les fichiers de certificat [obtenus auprès de l’autorité de certification](azure-stack-get-pki-certs.md) doivent être importés et exportés avec des propriétés correspondant aux exigences de certificat d’Azure Stack Hub.

Cet article explique comment importer, empaqueter et à valider des certificats afin de préparer le déploiement d’Azure Stack Hub ou la rotation des secrets. 

## <a name="prerequisites"></a>Prérequis

Votre système doit respecter les prérequis suivants afin de permettre le packaging des certificats PKI pour un déploiement Azure Stack Hub :

- stockage des certificats retournés par l’autorité de certification dans un seul répertoire au format .cer (autres formats configurables : .cert, .sst ou .pfx) ;
- Windows 10 ou Windows Server 2016 ou version ultérieure ;
- utilisation du système qui a généré la demande de signature de certificat (sauf si vous ciblez un certificat prépackagé en PFX).

Passez à la section [Préparer les certificats (Azure Stack Readiness Checker)](#prepare-certificates-azure-stack-readiness-checker) ou [Préparer les certificats (étapes manuelles)](#prepare-certificates-manual-steps).

## <a name="prepare-certificates-azure-stack-readiness-checker"></a>Préparer les certificats (Azure Stack Readiness Checker)

Suivez les étapes ci-après pour empaqueter des certificats à l’aide des cmdlets PowerShell d’Azure Stack Readiness Checker :

1. Installez le module Azure Stack Readiness Checker à partir d’une invite PowerShell (5.1 ou version ultérieure) en exécutant la cmdlet suivante :

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker -Force -AllowPrerelease
    ```
2. Spécifiez le **chemin d’accès** aux fichiers de certificat. Par exemple :

    ```powershell  
        $Path = "$env:USERPROFILE\Documents\AzureStack"
    ```

3. Déclarez le **pfxPassword**. Par exemple :

    ```powershell  
        $pfxPassword = Read-Host -AsSecureString -Prompt "PFX Password"
    ```
4. Déclarez le **ExportPath** où les PFX résultants sont exportés. Par exemple :

    ```powershell  
        $ExportPath = "$env:USERPROFILE\Documents\AzureStack"
    ```

5. Convertissez les certificats en certificats Azure Stack Hub. Par exemple :

    ```powershell  
        ConvertTo-AzsPFX -Path $Path -pfxPassword $pfxPassword -ExportPath $ExportPath
    ```
8.  Passez en revue la sortie :

    ```powershell  
    ConvertTo-AzsPFX v1.2005.1286.272 started.

    Stage 1: Scanning Certificates
        Path: C:\Users\[*redacted*]\Documents\AzureStack Filter: CER Certificate count: 11
        adminmanagement_east_azurestack_contoso_com_CertRequest_20200710235648.cer
        adminportal_east_azurestack_contoso_com_CertRequest_20200710235645.cer
        management_east_azurestack_contoso_com_CertRequest_20200710235644.cer
        portal_east_azurestack_contoso_com_CertRequest_20200710235646.cer
        wildcard_adminhosting_east_azurestack_contoso_com_CertRequest_20200710235649.cer
        wildcard_adminvault_east_azurestack_contoso_com_CertRequest_20200710235642.cer
        wildcard_blob_east_azurestack_contoso_com_CertRequest_20200710235653.cer
        wildcard_hosting_east_azurestack_contoso_com_CertRequest_20200710235652.cer
        wildcard_queue_east_azurestack_contoso_com_CertRequest_20200710235654.cer
        wildcard_table_east_azurestack_contoso_com_CertRequest_20200710235650.cer
        wildcard_vault_east_azurestack_contoso_com_CertRequest_20200710235647.cer

    Detected ExternalFQDN: east.azurestack.contoso.com

    Stage 2: Exporting Certificates
        east.azurestack.contoso.com\Deployment\ARM Admin\ARMAdmin.pfx
        east.azurestack.contoso.com\Deployment\Admin Portal\AdminPortal.pfx
        east.azurestack.contoso.com\Deployment\ARM Public\ARMPublic.pfx
        east.azurestack.contoso.com\Deployment\Public Portal\PublicPortal.pfx
        east.azurestack.contoso.com\Deployment\Admin Extension Host\AdminExtensionHost.pfx
        east.azurestack.contoso.com\Deployment\KeyVaultInternal\KeyVaultInternal.pfx
        east.azurestack.contoso.com\Deployment\ACSBlob\ACSBlob.pfx
        east.azurestack.contoso.com\Deployment\Public Extension Host\PublicExtensionHost.pfx
        east.azurestack.contoso.com\Deployment\ACSQueue\ACSQueue.pfx
        east.azurestack.contoso.com\Deployment\ACSTable\ACSTable.pfx
        east.azurestack.contoso.com\Deployment\KeyVault\KeyVault.pfx

    Stage 3: Validating Certificates.

    Validating east.azurestack.contoso.com-Deployment-AAD certificates in C:\Users\[*redacted*]\Documents\AzureStack\east.azurestack.contoso.com\Deployment 

    Testing: KeyVaultInternal\KeyVaultInternal.pfx
    Thumbprint: E86699****************************4617D6
        PFX Encryption: OK
        Expiry Date: OK
        Signature Algorithm: OK
        DNS Names: OK
        Key Usage: OK
        Key Length: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: ARM Public\ARMPublic.pfx
        ...
    Log location (contains PII): C:\Users\[*redacted*]\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    ConvertTo-AzsPFX Completed
    ```
    > [!NOTE]
    > Le cas échéant, utilisez Get-help ConvertTo-AzsPFX -Full pour une utilisation future, par exemple la désactivation de la validation ou le filtrage d’autres formats de certificat.

    Après une validation réussie, les certificats peuvent être présentés pour un déploiement ou une rotation sans étapes supplémentaires.

## <a name="prepare-certificates-manual-steps"></a>Préparer les certificats (étapes manuelles)

Suivez les étapes ci-après pour packager les nouveaux certificats PKI Azure Stack Hub manuellement :

### <a name="import-the-certificate"></a>Importer le certificat

1. Copiez les versions du certificat d’origine [obtenues auprès de l’autorité de certification de votre choix](azure-stack-get-pki-certs.md) dans un répertoire de l’ordinateur hôte de déploiement. 
   > [!WARNING]
   > Ne copiez pas les fichiers importés, exportés ou modifiés d’une façon ou d’une autre, à partir des fichiers fournis directement par l’autorité de certification.

1. Cliquez avec le bouton droit sur le certificat, puis sélectionnez **Installer le certificat** ou **Installer PFX** , selon la façon dont le certificat a été remis par votre autorité de certification.

1. Dans **l’Assistant Importation de certificat** , sélectionnez **Ordinateur local** en tant qu’emplacement d’importation. Sélectionnez **Suivant**. Sur l’écran suivant, sélectionnez à nouveau Suivant.

    ![Emplacement d’importation du certificat sur la machine locale](./media/prepare-pki-certs/1.png)

1. Sélectionnez **Placer tous les certificats dans le magasin suivant** , puis sélectionnez **Approbation de l’entreprise** comme emplacement. Sélectionnez **OK** pour fermer la boîte de dialogue de sélection du magasin de certificats, puis sélectionnez **Suivant**.

   ![Configurer le magasin de certificats pour l’importation de certificats](./media/prepare-pki-certs/3.png)

   a. Si vous importez un fichier PFX, une boîte de dialogue supplémentaire s’affiche. Dans la page **Protection de la clé privée** , entrez le mot de passe de vos fichiers de certificat, puis activez l’option **Marquer cette clé comme exportable** qui vous permettra de sauvegarder ou de transporter vos clés ultérieurement. Sélectionnez **Suivant**.

   ![Marquer la clé comme exportable](./media/prepare-pki-certs/2.png)

1. Sélectionnez **Terminer** pour effectuer l’importation.

> [!NOTE]
> Après importation d’un certificat pour Azure Stack Hub, la clé privée du certificat est stockée sous la forme d’un fichier PKCS 12 (PFX) sur le stockage en cluster.

### <a name="export-the-certificate"></a>Exportation du certificat

Ouvrez la console MMC du Gestionnaire de certificats et connectez-vous au magasin de certificats de l’ordinateur Local.

1. Ouvrez la console MMC (Microsoft Management Console). Pour ouvrir la console dans Windows 10, cliquez avec le bouton droit sur le **menu Démarrer** , sélectionnez **Exécuter** , tapez **mmc** , puis appuyez sur Entrée.

2. Sélectionnez **Fichier** > **Ajouter/supprimer un composant logiciel enfichable** , sélectionnez **Certificats** , puis **Ajouter**.

    ![Ajouter le composant logiciel enfichable Certificats dans la console MMC](./media/prepare-pki-certs/mmc-2.png)

3. Sélectionnez **Compte d’ordinateur** , puis **Suivant**. Sélectionnez **Ordinateur local** , puis **Terminer**. Sélectionnez **OK** pour fermer la page Ajouter/Supprimer un composant logiciel enfichable.

    ![Sélectionner un compte pour l’ajout du composant logiciel enfichable Certificats dans la console MMC](./media/prepare-pki-certs/mmc-3.png)

4. Accédez à **Certificats** > **Confiance de l’entreprise** > **Emplacement des certificats**. Vérifiez que votre certificat apparaît sur la droite.

5. Dans la barre des tâches de la console Gestionnaire de certificats, sélectionnez **Actions** > **Toutes les tâches** > **Exporter**. Sélectionnez **Suivant**.

   > [!NOTE]
   > Selon le nombre de certificats Azure Stack Hub que vous avez, il se peut que vous deviez effectuer ce processus plusieurs fois.

6. Sélectionnez **Oui, exporter la clé privée** , puis cliquez sur **Suivant**.

7. Dans la section Format du fichier d’exportation :
    
   - Sélectionnez **Inclure tous les certificats dans le certificat si possible**.  
   - Sélectionnez **Exporter toutes les propriétés étendues**.  
   - Sélectionnez **Activer la confidentialité de certificat**.  
   - Sélectionnez **Suivant**.  
    
     ![Assistant Exportation du certificat avec options sélectionnées](./media/prepare-pki-certs/azure-stack-save-cert.png)

8. Sélectionnez **Mot de passe** et indiquez un mot de passe pour les certificats. Créez un mot de passe qui répond aux exigences suivantes de complexité des mots de passe :

    * Longueur minimale de huit caractères.
    * Au moins trois des caractères suivants : majuscules, minuscules, chiffres de 0 à 9, caractères spéciaux, caractères alphabétiques autres que des majuscules ou des minuscules.

    Notez ce mot de passe. Vous allez l’utiliser en tant que paramètre de déploiement.

9. Sélectionnez **Suivant**.

10. Choisissez un nom de fichier et l’emplacement du fichier PFX à exporter. Sélectionnez **Suivant**.

11. Sélectionnez **Terminer**.

## <a name="next-steps"></a>Étapes suivantes

[Validate PKI certificates](azure-stack-validate-pki-certs.md) (Valider des certificats d’infrastructure à clé publique)
