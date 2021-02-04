---
title: Effectuer la rotation des secrets du registre de conteneurs dans Azure Stack Hub | Microsoft Docs
titleSuffix: Azure Stack Hub
description: Découvrez comment effectuer la rotation des secrets dans Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: chasat
ms.lastreviewed: 12/17/2019
ms.openlocfilehash: 3bfde6e55883e124379353302e77a439c4de0505
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99245634"
---
# <a name="rotate-container-registry-secrets-in-azure-stack-hub"></a>Effectuer la rotation des secrets dans Azure Stack Hub

Vos utilisateurs Azure Stack Hub peuvent faire effectuer la rotation des secrets (certificats, nom d’utilisateur et mot de passe) pour un déploiement de modèle de registre de conteneurs. Vous pouvez exécuter un script pour renseigner de nouvelles valeurs secrètes dans Microsoft Azure Key Vault et **redéployer** l’instance de modèle de registre de conteneurs existante. La rotation des secrets à proprement parler ne requiert pas de nouveau déploiement.

## <a name="prerequisites-for-the-user"></a>Conditions préalables pour l’utilisateur

 - L’utilisateur doit avoir installé les modules Azure Stack Hub PowerShell. Pour plus d’informations, consultez [Installer PowerShell pour Azure Stack](../../operator/azure-stack-powershell-install.md?toc=https%3A%2F%2Fdocs.microsoft.com%2Fazure-stack%2Fuser%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fazure-stack%2Fbreadcrumb%2Ftoc.json).

 - Procurez-vous les secrets mis à jour pour le modèle de registre de conteneurs. Vous pouvez utiliser un nouveau certificat SSL ou une nouvelle combinaison nom d’utilisateur/mot de passe pour accéder au registre Docker.

 - Procurez-vous les scripts trouvés dans `\registry\scripts` après avoir téléchargé le fichier zip à partir du référentiel GitHub [msazurestackworkloads/azurestack-gallery](https://github.com/msazurestackworkloads/azurestack-gallery/archive/master.zip).

## <a name="import-new-secrets-into-key-vault"></a>Importer de nouveaux secrets dans Key Vault

Suivez les instructions ci-dessous pour définir de nouveaux secrets dans Key Vault.

### <a name="set-updated-registry-user-password-for-existing-username"></a>Définir le mot de passe utilisateur du registre mis à jour pour le nom d’utilisateur existant

1.  Ouvrez une invite PowerShell avec élévation de privilèges, puis exécutez `Import-Module .\\pre-reqs.ps1` à partir du dossier de scripts.

2.  Pour mettre à jour la valeur de l’utilisateur de registre existant, exécutez la cmdlet :

    ```powershell  
    Set-RegistryAccessSecret -KeyVaultName newregkv `
        -RegistryUserName <username> `
        -RegistryUserPassword <newpassword> `
        -SkipExistCheck $true
    ```
    Par exemple, la cmdlet renvoie la sortie suivante :

    ```powershell  
    PS C:\azurestack-gallery-master\registry\Scripts> Set-RegistryAccessSecret -KeyVaultName newregkv `
        -RegistryUserName admin `
        -RegistryUserPassword password1 `
        -SkipExistCheck $true 
    
    Check if key vault secret name (admin) exists.
    Creating key vault secret name (admin) as it does not exist.
    ```

1.  Pour vérifier l’entrée d’une nouvelle valeur pour cet enregistrement, ouvrez une invite PowerShell avec élévation de privilèges et exécutez la cmdlet suivante :

    ```powershell  
    Get-AzureKeyVaultSecret -VaultName newregkv -Name admin -IncludeVersions
    ```
    Par exemple, la cmdlet renvoie la sortie suivante :
    ```powershell  
    PS C:\azurestack-gallery-master\registry\Scripts> Get-AzureKeyVaultSecret -VaultName newregkv -Name admin -IncludeVersions
    
    
    Vault Name   : newregkv
    Name         : admin
    Version      : 2a1495372c474cc890c888518f02b19f
    Id           : https://newregkv.vault.shanghai.azurestack.corp.microsoft.com:443/secrets/
                   admin/2a1495372c474cc890c888518f02b19f
    Enabled      : True
    Expires      : 
    Not Before   : 
    Created      : 12/18/2019 7:05:56 PM
    Updated      : 12/18/2019 7:05:56 PM
    Content Type : 
    Tags         : 
    
    Vault Name   : newregkv
    Name         : admin
    Version      : 3fd65c1719c74997984648de18a1fa0e
    Id           : https://newregkv.vault.shanghai.azurestack.corp.microsoft.com:443/secrets/
                   admin/3fd65c1719c74997984648de18a1fa0e
    Enabled      : True
    Expires      : 
    Not Before   : 
    Created      : 12/17/2019 5:05:56 AM
    Updated      : 12/17/2019 5:05:56 AM
    Content Type : user credentials
    Tags         : 
    ```

### <a name="set-new-registry-username-and-password"></a>Définir un nom d'utilisateur et un mot de passe de registre

1.  Ouvrez une invite PowerShell avec élévation de privilèges et `Import-Module .\pre-reqs.ps1` à partir du dossier des scripts.

2.  Pour créer un secret pour le nouveau nom d’utilisateur et le nouveau mot de passe, ouvrez une invite PowerShell avec élévation de privilèges et exécutez la cmdlet suivante :

    ```powershell  
    Set-RegistryAccessSecret -KeyVaultName newregkv `
        -RegistryUserName <newusername> `
        -RegistryUserPassword <newpassword> 
    ```
    Par exemple, la cmdlet renvoie la sortie suivante :
    ```powershell  
    PS C:\azurestack-gallery-master\registry\Scripts> Set-RegistryAccessSecret -KeyVaultName newregkv `
        -RegistryUserName admin1 `
        -RegistryUserPassword password1
    
    Check if key vault secret name (admin1) exists.
    Creating key vault secret name (admin1) as it does not exist. 
    ```

1.  Pour vérifier qu’un nouveau secret a été créé, ouvrez une invite PowerShell avec élévation de privilèges et exécutez la cmdlet suivante :

    ```powershell  
    Get-AzureKeyVaultSecret -VaultName \<KeyVaultName> -Name \<username>
    ```

    Par exemple, la cmdlet renvoie la sortie suivante :

    ```powershell  
    PS C:\azurestack-gallery-master\registry\Scripts> Get-AzureKeyVaultSecret -VaultName newregkv -Name admin1
    
    
    Vault Name   : newregkv
    Name         : admin1
    Version      : 2ae9a7239f4044be82ca9d1e9b80e85a
    Id           : https://newregkv.vault.shanghai.azurestack.corp.microsoft.com:443/secrets/admin1/2ae9a7239f4044be82ca9d1e9b80e85a
    Enabled      : True
    Expires      : 
    Not Before   : 
    Created      : 12/18/2019 11:28:18 PM
    Updated      : 12/18/2019 11:28:18 PM
    Content Type : user credentials
    Tags         : 
    ```

> [!Important]  
> Si vous créez un secret (combinaison nom d’utilisateur/mot de passe), vous devrez supprimer l’ancien secret Key Vault. Si vous redéployez le modèle de registre de conteneurs existant sans supprimer l’ancien secret, l’ancienne combinaison et la nouvelle combinaison nom d’utilisateur/mot de passe seront valides pour la connexion au registre.

### <a name="update-the-ssl-certificate-for-existing-key-vault-secret"></a>Mettre à jour le certificat SSL pour le secret Key Vault existant

1. Ouvrez une invite PowerShell avec élévation de privilèges et exécutez la cmdlet suivante :

    ```powershell
    Set-CertificateSecret -KeyVaultName \<keyvaultname> `
     -CertificateSecretName \<originalsecretnameforcertificate> `
    Set-CertificateSecret -KeyVaultName <keyvaultname> `
        -CertificateSecretName <originalsecretnameforcertificate> `
        -CertificateFilePath <pathtonewcertificate> `
        -CertificatePassword <certificatepassword> `
        -SkipExistCheck $true
    ```

    Par exemple, la cmdlet renvoie la sortie suivante :

    ```powershell  
    PS C:\azurestack-gallery-master\registry\Scripts> Set-CertificateSecret -KeyVaultName newregkv `
        -CertificateSecretName containersecret `
        -CertificateFilePath C:\crinstall\shanghairegcertnew.pfx `
        -CertificatePassword <certificatepassword> `
        -SkipExistCheck $true
    Check if key vault secret name (containersecret) exists.
    Creating key vault secret name (containersecret) as it does not exist.
    ----------------------------------------------------------------
    PFX KeyVaultResourceId       : /subscriptions/997da68a-xxxx-xxxx-ad3d-ffeac81b02dc/resourceGroups/newregreg/providers/Microsoft.KeyVault/vaults/newregkv
    PFX KeyVaultSecretUrl        : https://newregkv.vault.shanghai.azurestack.corp.microsoft.com:443/secrets/containersecret/a07ece6b9914408e8f20c516e15b66c9
    PFX Certificate Thumbprint   : 31810AA7FEF1173188691FB3F47208E5389FBA61
    ---------------------------------------------------------------- 
    ```

1.  Vous utiliserez les valeurs générées par cette fonction lors du redéploiement du modèle de registre de conteneurs existant.

2.  Pour vérifier qu’une nouvelle version du secret existant a été créée, ouvrez une invite PowerShell avec élévation de privilèges et exécutez la cmdlet :

    ```powershell  
    Get-AzureKeyVaultSecret -VaultName <KeyVaultName> -Name <secretname>
    ```

    Par exemple, la cmdlet renvoie la sortie suivante :

    ```powershell  
    PS C:\azurestack-gallery-master\registry\Scripts> Get-AzureKeyVaultSecret -VaultName newregkv -Name containersecret -IncludeVersions
    
    
    Vault Name   : newregkv
    Name         : containersecret
    Version      : a07ece6b9914408e8f20c516e15b66c9
    Id           : https://newregkv.vault.shanghai.azurestack.corp.microsoft.com:443/secrets/containersecret/a07ece6b9914408e8f20c516e15b66c9
    Enabled      : True
    Expires      : 
    Not Before   : 
    Created      : 12/18/2019 11:46:28 PM
    Updated      : 12/18/2019 11:46:28 PM
    Content Type : 
    Tags         : 
    
    Vault Name   : newregkv
    Name         : containersecret
    Version      : 0199c7ec1d8d41bb9ddff0f39dca9931
    Id           : https://newregkv.vault.shanghai.azurestack.corp.microsoft.com:443/secrets/containersecret/0199c7ec1d8d41bb9ddff0f39dca9931
    Enabled      : True
    Expires      : 
    Not Before   : 
    Created      : 12/17/2019 5:06:03 AM
    Updated      : 12/17/2019 5:06:03 AM
    Content Type : pfx
    Tags         : 
    ```

### <a name="set-a-new-ssl-certificate-for-the-container-registry-template"></a>Définir un nouveau certificat SSL pour le modèle de registre de conteneurs

1.  Ouvrez une invite de commandes PowerShell avec élévation de privilèges et exécutez la cmdlet suivante :

    ```powershell  
    Set-CertificateSecret -KeyVaultName <keyvaultname> `
        -CertificateSecretName <newsecretnameforcertificate> `
        -CertificateFilePath <pathtonewcertificate> `
        -CertificatePassword <certificatepassword>
    ```

    Par exemple, la cmdlet renvoie la sortie suivante :

    ```powershell  
    PS C:\azurestack-gallery-master\registry\Scripts>    Set-CertificateSecret -KeyVaultName newregkv `
        -CertificateSecretName containersecret121719 `
        -CertificateFilePath C:\crinstall\shanghairegcertnew.pfx `
        -CertificatePassword <certificatepassword> 
    Check if key vault secret name (containersecret121719) exists.
    Creating key vault secret name (containersecret121719) as it does not exist.
    ----------------------------------------------------------------
    PFX KeyVaultResourceId       : /subscriptions/997da68a-xxxx-xxxx-ad3d-ffeac81b02dc/resourc
    eGroups/newregreg/providers/Microsoft.KeyVault/vaults/newregkv
    PFX KeyVaultSecretUrl        : https://newregkv.vault.shanghai.azurestack.corp.microsoft.c
    om:443/secrets/containersecret121719/bb2cfe4df7bc4fbe854a00799afa8566
    PFX Certificate Thumbprint   : 31810AA7FEF1173188691FB3F47208E5389FBA61 
    ```

## <a name="redeploy-existing-container-registry-template"></a>Redéployer un modèle de registre de conteneurs existant

1. Connectez-vous au portail utilisateur Azure Stack Hub.

2.  Accédez au groupe de ressources dans lequel la machine virtuelle du modèle de registre de conteneurs est déployée.

    ![Capture d’écran montrant le groupe de ressources dans lequel la machine virtuelle du modèle de registre de conteneurs est déployée.](./media/container-registry-template-rotating-secrets-tzl/image1.png)

3. Sélectionnez les déploiements sous **Déploiements**.

    ![Capture d’écran montrant un déploiement sélectionné sur la page « Déploiements ».](./media/container-registry-template-rotating-secrets-tzl/image2.png)

4.  Si vous effectuez la rotation des secrets pour la première fois, sélectionnez le déploiement d’origine. Si ce n’est pas le cas, sélectionnez le déploiement le plus récent, puis **Redéployer**.

    ![Capture d’écran montrant la page « Présentation » du modèle avec l’action « Redéployer » mise en surbrillance.](./media/container-registry-template-rotating-secrets-tzl/image3.png)

5.  Dans **Déployer un modèle de solution**, sélectionnez **Utiliser un groupe de ressources existant**, puis le groupe de ressources utilisé pour déployer initialement le modèle de registre de conteneurs. Pour qu’un redéploiement aboutisse, il doit utiliser le même groupe de ressources.

    ![Capture d’écran montrant les pages « Déployer le modèle de solution » et « Paramètres ».](./media/container-registry-template-rotating-secrets-tzl/image4.png)

6.  Dans **Paramètres**, vérifiez que les paramètres correspondent au déploiement d’origine. L’ID client et le secret du principal du service devront être ajoutés.

    - Si vous effectuez uniquement la rotation du nom d’utilisateur et du mot de passe pour le registre du service, vous devez juste ajouter les paramètres du principal du service.

    - Si vous effectuez la rotation du certificat, vous devez entrer les nouvelles valeurs pour PFXKeyVaultSecretURL et PFXThumbprint générées lors de la définition des nouveaux secrets.

    ![Modèle de registre de conteneurs](./media/container-registry-template-rotating-secrets-tzl/image5.png)

7.  Sélectionnez **OK**, puis **Créer**. Le redéploiement se poursuit. Le registre reste fonctionnel lors du redéploiement.

    - Si vous effectuez la rotation du nom d’utilisateur et du mot de passe, vous devrez vous authentifier à nouveau dans le registre une fois le redéploiement terminé.

    - Si vous effectuez la rotation du certificat, vous devriez continuer d’avoir accès au registre. Cela suppose l’utilisation d’un certificat provenant d’un fournisseur de certificats approuvés. Si vous utilisez un certificat privé, ce certificat doit être installé sur les clients pour éviter toute perte d’accès.

## <a name="next-steps"></a>Étapes suivantes

[Vue d’ensemble de la Place de Marché Azure Stack](../../operator/azure-stack-marketplace.md)
