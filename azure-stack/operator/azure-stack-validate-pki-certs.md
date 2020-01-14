---
title: Valider des certificats pour infrastructure à clé publique Azure Stack pour le déploiement de systèmes intégrés Azure Stack | Microsoft Docs
description: Explique comment valider les certificats pour infrastructure à clé publique Azure Stack pour des systèmes intégrés Azure Stack. Couvre l’utilisation de l’outil de vérification de certificats d’Azure Stack.
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
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: ppacent
ms.lastreviewed: 01/08/2019
ms.openlocfilehash: 61e79fb581b18825d2bde1e2838d8b653ad00da6
ms.sourcegitcommit: b9d520f3b7bc441d43d489e3e32f9b89601051e6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75727528"
---
# <a name="validate-azure-stack-pki-certificates"></a>Valider des certificats PKI Azure Stack

L’outil Azure Stack Readiness Checker décrit dans cet article est disponible [à partir de PowerShell Gallery](https://aka.ms/AzsReadinessChecker). Vous pouvez utiliser cet outil pour vérifier que les [certificats pour infrastructure à clé publique (PKI) générés](azure-stack-get-pki-certs.md) sont adaptés au prédéploiement. Validez les certificats en gardant suffisamment de temps pour tester et réémettre les certificats si nécessaire.

L’outil Readiness Checker effectue les validations de certificat suivantes :

- **Analyser PFX**  
    Vérifie si le fichier PFX est valide, si le mot de passe est correct et si les informations publiques sont protégées par le mot de passe. 
- **Date d’expiration**  
    Vérifie la validité minimale de 7 jours. 
- **Algorithme de signature**  
    Vérifie que l’algorithme de signature n’est pas SHA1 (Secure Hash Algorithm 1).
- **Clé privée**  
    Vérifie que la clé privée est présente et qu’elle est exportée avec l’attribut d’ordinateur local. 
- **Chaîne d’approbation**  
    Vérifie que la chaîne d’approbation est intacte, y compris pour les certificats auto-signés.
- **Noms DNS**  
    Vérifie que le réseau SAN contient les noms DNS appropriés pour chaque point de terminaison ou si un caractère générique de prise en charge est présent.
- **Syntaxe de la clé**  
    Vérifie que la syntaxe de la clé contient une signature numérique et un chiffrement de clé, et que la syntaxe avancée de la clé contient l’authentification du serveur et l’authentification du client.
- **Taille de la clé**  
    Vérifie que la taille de clé est au moins égale à 2 048 octets.
- **Ordre de la chaîne**  
    Vérifie l’ordre des autres certificats afin de garantir que l’ordre est correct.
- **Autres certificats**  
    Assurez-vous qu’aucun autre certificat n’a été packagé dans le PFX, à part le certificat feuille pertinent et sa chaîne.

> [!IMPORTANT]  
> Le certificat PKI est un fichier PFX et le mot de passe doit être considéré comme une information sensible.

## <a name="prerequisites"></a>Conditions préalables requises

Votre système doit respecter la configuration requise ci-après afin de permettre la validation des certificats PKI pour un déploiement Azure Stack :

- Outil Microsoft Azure Stack Readiness Checker
- Certificats SSL exportés conformément aux [instructions de préparation](azure-stack-prepare-pki-certs.md)
- DeploymentData.json
- Windows 10 ou Windows Server 2016

## <a name="perform-core-services-certificate-validation"></a>Effectuer la validation principale des certificats de service

Pour préparer et valider les certificats PKI Azure Stack en vue du déploiement et de la rotation des clés, effectuez les étapes suivantes :

1. Installez **AzsReadinessChecker** à partir d’une invite PowerShell (5.1 ou version ultérieure) en exécutant l’applet de commande suivante :

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker -force 
    ```

2. Créez la structure de répertoires de certificat. Dans l’exemple ci-après, vous pouvez remplacer `<C:\Certificates\Deployment>` par un nouveau chemin d’accès de répertoire de votre choix.
    ```powershell  
    New-Item C:\Certificates\Deployment -ItemType Directory
    
    $directories = 'ACSBlob', 'ACSQueue', 'ACSTable', 'Admin Extension Host', 'Admin Portal', 'ARM Admin', 'ARM Public', 'KeyVault', 'KeyVaultInternal', 'Public Extension Host', 'Public Portal'
    
    $destination = 'C:\Certificates\Deployment'
    
    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ```
    
    > [!Note]  
    > AD FS et Graph sont obligatoires si vous utilisez AD FS comme système d’identité. Par exemple :
    >
    > ```powershell  
    > $directories = 'ACSBlob', 'ACSQueue', 'ACSTable', 'ADFS', 'Admin Extension Host', 'Admin Portal', 'ARM Admin', 'ARM Public', 'Graph', 'KeyVault', 'KeyVaultInternal', 'Public Extension Host', 'Public Portal'
    > ```
    
     - Placez vos certificats dans les répertoires appropriés créés à l’étape précédente. Par exemple :  
        - `C:\Certificates\Deployment\ACSBlob\CustomerCertificate.pfx`
        - `C:\Certificates\Deployment\Admin Portal\CustomerCertificate.pfx`
        - `C:\Certificates\Deployment\ARM Admin\CustomerCertificate.pfx`

3. Dans la fenêtre PowerShell, changez les valeurs de **RegionName** et **FQDN** en fonction de l’environnement Azure Stack et exécutez la commande suivante :

    ```powershell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 
    Invoke-AzsCertificateValidation -CertificateType Deployment -CertificatePath C:\Certificates\Deployment -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD  
    ```

4. Vérifiez que la sortie et tous les certificats réussissent tous les tests. Par exemple :

    ```powershell
    Invoke-AzsCertificateValidation v1.1912.1082.37 started.
    Testing: KeyVaultInternal\adminvault.pfx
    Thumbprint: B1CB76****************************565B99
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
    Testing: ARM Public\management.pfx
    Thumbprint: 44A35E****************************36052A
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
    Testing: Admin Portal\adminportal.pfx
    Thumbprint: 3F5E81****************************9EBF9A
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
    Testing: Public Portal\portal.pfx

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
    Invoke-AzsCertificateValidation Completed
    ```

    Pour valider les certificats d’autres services Azure Stack, changez la valeur de ```-CertificateType```. Par exemple :

    ```powershell  
    # App Services
    Invoke-AzsCertificateValidation -CertificateType AppServices -CertificatePath C:\Certificates\AppServices -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com

    # DBAdapter
    Invoke-AzsCertificateValidation -CertificateType DBAdapter -CertificatePath C:\Certificates\DBAdapter -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com

    # EventHub
    Invoke-AzsCertificateValidation -CertificateType EventHubs -CertificatePath C:\Certificates\EventHub -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com

    # IoTHub
    Invoke-AzsCertificateValidation -CertificateType IoTHub -CertificatePath C:\Certificates\IoTHub -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com
    ```
Chaque dossier doit contenir un seul fichier PFX pour le type de certificat. Si un type de certificat a des exigences multicertificats, des dossiers imbriqués sont attendus pour chaque certificat avec un nom approprié.  Le code suivant montre un exemple de structure de dossier/certificat pour tous les types de certificat ainsi que la valeur appropriée pour ```-CertificateType``` et ```-CertificatePath```.
    
    ```powershell  
    C:\>tree c:\SecretStore /A /F
        Folder PATH listing
        Volume serial number is 85AE-DF2E
        C:\SECRETSTORE
        \---AzureStack
            +---CertificateRequests
            \---Certificates
                +---AppServices         # Invoke-AzsCertificateValidation `
                |   +---API             #     -CertificateType AppServices `
                |   |       api.pfx     #     -CertificatePath C:\Certificates\AppServices
                |   |
                |   +---DefaultDomain
                |   |       wappsvc.pfx
                |   |
                |   +---Identity
                |   |       sso.pfx
                |   |
                |   \---Publishing
                |           ftp.pfx
                |
                +---DBAdapter           # Invoke-AzsCertificateValidation `
                |       dbadapter.pfx   #   -CertificateType DBAdapter `
                |                       #   -CertificatePath C:\Certificates\DBAdapter
                |
                +---Deployment          # Invoke-AzsCertificateValidation `
                |   +---ACSBlob         #   -CertificateType Deployment `
                |   |       acsblob.pfx #   -CertificatePath C:\Certificates\Deployment
                |   |
                |   +---ACSQueue
                |   |       acsqueue.pfx
               ./. ./. ./. ./. ./. ./. ./.    <- Deployment certificate tree trimmed.
                |   \---Public Portal
                |           portal.pfx
                |
                +---EventHub            # Invoke-AzsCertificateValidation `
                |       eventhub.pfx    #   -CertificateType EventHub `
                |                       #   -CertificatePath C:\Certificates\EventHub
                |
                \---IoTHub              # Invoke-AzsCertificateValidation `
                        iothub.pfx      #   -CertificateType IoTHub `
                                        #   -CertificatePath C:\Certificates\IoTHub
    ```
### <a name="known-issues"></a>Problèmes connus

**Symptôme** : les tests sont ignorés.

**Cause** : AzsReadinessChecker ignore certains tests si aucune dépendance n’est détectée :

 - Si la chaîne d’approbation échoue, les autres certificats sont ignorés.

    ```powershell  
    Testing: ACSBlob\singlewildcard.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: Fail
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: Skipped
    Details:
    The certificate records '*.east.azurestack.contoso.com' do not contain a record that is valid for '*.blob.east.azurestack.contoso.com'. Please refer to the documentation for how to create the required certificate file.
    The Other Certificates check was skipped because Cert Chain and/or DNS Names failed. Follow the guidance to remediate those issues and recheck. 
    Detailed log can be found C:\AzsReadinessChecker\CertificateValidation\CertChecker.log

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
    Invoke-AzsCertificateValidation Completed
    ```

**Résolution** : suivez les instructions de l’outil dans la section des détails sous chaque ensemble de tests de chacun des certificats.

## <a name="certificates"></a>Certificats

| Répertoire | Certificat |
| ---    | ----        |
| acsBlob | wildcard_blob_\<region>_\<externalFQDN> |
| ACSQueue  |  wildcard_queue_\<region>_\<externalFQDN> |
| ACSTable  |  wildcard_table_\<region>_\<externalFQDN> |
| Hôte d’extension d’administration  |  wildcard_adminhosting_\<region>_\<externalFQDN> |
| Portail d’administration  |  adminportal_\<region>_\<externalFQDN> |
| Administrateur ARM  |  adminmanagement_\<region>_\<externalFQDN> |
| ARM Public  |  management_\<region>_\<externalFQDN> |
| KeyVault  |  wildcard_vault_\<region>_\<externalFQDN> |
| KeyVaultInternal  |  wildcard_adminvault_\<region>_\<externalFQDN> |
| Hôte d’extension public  |  wildcard_hosting_\<region>_\<externalFQDN> |
| Portail public  |  portal_\<region>_\<externalFQDN> |

## <a name="using-validated-certificates"></a>Utilisation des certificats validés

Une fois que vos certificats ont été validés par l’outil AzsReadinessChecker, vous êtes prêt à les utiliser dans votre déploiement Azure Stack ou pour la rotation des secrets Azure Stack. 

 - Pour le déploiement, transférez en toute sécurité vos certificats à votre ingénieur de déploiement pour lui permettre de les copier sur l’hôte de déploiement, comme spécifié dans la [documentation relative aux exigences de certificat pour infrastructure à clé publique Azure Stack](azure-stack-pki-certs.md).
 - Pour la rotation des secrets, vous pouvez utiliser les certificats afin de mettre à jour les anciens certificats des points de terminaison d’infrastructure publique de votre environnement Azure Stack en suivant les instructions de la [documentation sur la rotation des secrets Azure Stack](azure-stack-rotate-secrets.md).
 - Pour les services PaaS, vous pouvez utiliser les certificats afin d’installer des fournisseurs de ressources SQL, MySQL et App Services dans Azure Stack en suivant la documentation [Vue d’ensemble de l’offre de services dans Azure Stack](service-plan-offer-subscription-overview.md).

## <a name="next-steps"></a>Étapes suivantes

[Intégration des identités au centre de données](azure-stack-integrate-identity.md)
