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
ms.openlocfilehash: 8d929a3b1fd67f3ec73137b2e1a87f594ea5b544
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68417825"
---
# <a name="validate-azure-stack-pki-certificates"></a>Valider des certificats PKI Azure Stack

L’outil Azure Stack Readiness Checker décrit dans cet article est disponible [à partir de PowerShell Gallery](https://aka.ms/AzsReadinessChecker). Vous pouvez utiliser cet outil pour vérifier que les [certificats pour infrastructure à clé publique (PKI) générés](azure-stack-get-pki-certs.md) sont adaptés au prédéploiement. Validez les certificats en gardant suffisamment de temps pour tester et réémettre les certificats si nécessaire.

L’outil Readiness Checker effectue les validations de certificat suivantes :

- **Lecture PFX**  
    Vérifie la validité du fichier PFX, l’exactitude du mot de passe et si les informations publiques ne sont pas protégées par le mot de passe. 
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
- **Aucun profil**  
    Vérifie qu’un nouvel utilisateur peut charger les données PFX sans profil utilisateur chargé, en simulant le comportement des comptes de service administré de groupe pendant la maintenance du certificat.

> [!IMPORTANT]  
> Le certificat PKI est un fichier PFX et le mot de passe doit être considéré comme une information sensible.

## <a name="prerequisites"></a>Prérequis

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

2. Créez la structure de répertoires de certificat. Dans l’exemple ci-après, vous pouvez remplacer `<c:\certificates>` par un nouveau chemin d’accès de répertoire de votre choix.
    ```powershell  
    New-Item C:\Certificates -ItemType Directory
    
    $directories = 'ACSBlob', 'ACSQueue', 'ACSTable', 'Admin Extension Host', 'Admin Portal', 'ARM Admin', 'ARM Public', 'KeyVault', 'KeyVaultInternal', 'Public Extension Host', 'Public Portal'
    
    $destination = 'c:\certificates'
    
    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ```
    
    > [!Note]  
    > AD FS et Graph sont obligatoires si vous utilisez AD FS comme système d’identité. Par exemple :
    >
    > ```powershell  
    > $directories = 'ACSBlob', 'ACSQueue', 'ACSTable', 'ADFS', 'Admin Extension Host', 'Admin Portal', 'ARM Admin', 'ARM Public', 'Graph', 'KeyVault', 'KeyVaultInternal', 'Public Extension Host', 'Public Portal'
    > ```
    
     - Placez vos certificats dans les répertoires appropriés créés à l’étape précédente. Par exemple :  
        - `c:\certificates\ACSBlob\CustomerCertificate.pfx`
        - `c:\certificates\Admin Portal\CustomerCertificate.pfx`
        - `c:\certificates\ARM Admin\CustomerCertificate.pfx`

3. Dans la fenêtre PowerShell, changez les valeurs de **RegionName** et **FQDN** en fonction de l’environnement Azure Stack et exécutez la commande suivante :

    ```powershell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 

    Invoke-AzsCertificateValidation -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD  
    ```

4. Vérifiez que la sortie et tous les certificats réussissent tous les tests. Par exemple :

```powershell
Invoke-AzsCertificateValidation v1.1809.1005.1 started.
Testing: ARM Public\ssl.pfx
Thumbprint: 7F6B27****************************E9C35A
    PFX Encryption: OK
    Signature Algorithm: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Parse PFX: OK
    Private Key: OK
    Cert Chain: OK
    Chain Order: OK
    Other Certificates: OK
Testing: Admin Extension Host\ssl.pfx
Thumbprint: A631A5****************************35390A
    PFX Encryption: OK
    Signature Algorithm: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Parse PFX: OK
    Private Key: OK
    Cert Chain: OK
    Chain Order: OK
    Other Certificates: OK
Testing: Public Extension Host\ssl.pfx
Thumbprint: 4DBEB2****************************C5E7E6
    PFX Encryption: OK
    Signature Algorithm: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Parse PFX: OK
    Private Key: OK
    Cert Chain: OK
    Chain Order: OK
    Other Certificates: OK

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsCertificateValidation Completed
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

## <a name="perform-platform-as-a-service-certificate-validation"></a>Effectuer une validation de certificats Platform as a service

Utilisez ces étapes pour préparer et valider les certificats PKI Azure Stack pour les certificats PaaS (Platform as a service), si des déploiements SQL/MySQL ou App Services sont planifiés.

1.  Installez **AzsReadinessChecker** à partir d’une invite PowerShell (5.1 ou version ultérieure) en exécutant l’applet de commande suivante :

    ```powershell  
      Install-Module Microsoft.AzureStack.ReadinessChecker -force
    ```

2.  Créez une table de hachage imbriquée qui contient les chemins et mot de passe de chaque certificat PaaS à valider. Dans la fenêtre PowerShell, exécutez les commandes suivantes :

    ```powershell  
        $PaaSCertificates = @{
        'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        }
    ```

3.  Changez les valeurs de **RegionName** et **FQDN** en fonction de votre environnement Azure Stack pour démarrer la validation. Exécutez ensuite la commande suivante :

    ```powershell  
    Invoke-AzsCertificateValidation -PaaSCertificates $PaaSCertificates -RegionName east -FQDN azurestack.contoso.com 
    ```
4.  Vérifiez que la sortie et tous les certificats réussissent tous les tests.

    ```powershell
    Invoke-AzsCertificateValidation v1.0 started.
    Thumbprint: 95A50B****************************FA6DDA
        Signature Algorithm: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Thumbprint: EBB011****************************59BE9A
        Signature Algorithm: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Thumbprint: 76AEBA****************************C1265E
        Signature Algorithm: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Thumbprint: 8D6CCD****************************DB6AE9
        Signature Algorithm: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
    ```

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
 - Pour les services PaaS, vous pouvez utiliser les certificats afin d’installer des fournisseurs de ressources SQL, MySQL et App Services dans Azure Stack en suivant la documentation [Vue d’ensemble de l’offre de services dans Azure Stack](azure-stack-offer-services-overview.md).

## <a name="next-steps"></a>Étapes suivantes

[Intégration des identités au centre de données](azure-stack-integrate-identity.md)
