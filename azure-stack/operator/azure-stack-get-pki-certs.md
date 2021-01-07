---
title: Générer des demandes de signature de certificat pour Azure Stack Hub
description: Découvrez comment générer des demandes de signature de certificat pour des certificats PKI Azure Stack Hub dans les systèmes intégrés Azure Stack Hub.
author: PatAltimore
ms.topic: article
ms.date: 10/19/2020
ms.author: patricka
ms.reviewer: ppacent
ms.lastreviewed: 10/19/2020
ms.openlocfilehash: b03766efe531683310b81dbf2d03de8e990deec0
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97870441"
---
# <a name="generate-certificate-signing-requests-for-azure-stack-hub"></a>Générer des demandes de signature de certificat pour Azure Stack Hub

Vous pouvez utiliser l’outil Azure Stack Hub Readiness Checker pour créer des demandes de signature de certificat (CSR) adaptées à un déploiement Azure Stack Hub. Les certificats doivent être demandés, générés et validés avec suffisamment de temps pour les tester avant le déploiement. Vous pouvez obtenir l’outil à partir de [PowerShell Gallery](https://aka.ms/AzsReadinessChecker).

L’outil Azure Stack Hub Readiness Checker (AzsReadinessChecker) permet de demander les certificats suivants :

- **Demandes de certificat standard** conformes à [Générer une demande de signature de certificat pour les nouveaux déploiements](azure-stack-get-pki-certs.md#generate-certificate-signing-requests-for-new-deployments).
- **Demandes de renouvellement de certificat** conformes à [Générer une demande de signature de certificat pour le renouvellement de certificat](azure-stack-get-pki-certs.md#generate-certificate-signing-requests-for-certificate-renewal).
- **Plateforme en tant que service (PaaS)** : Vous pouvez demander des noms PaaS pour les certificats comme spécifié dans [Exigences de certificat pour infrastructure à clé publique Azure Stack Hub - Certificats PaaS facultatifs](azure-stack-pki-certs.md#optional-paas-certificates).

## <a name="prerequisites"></a>Prérequis

Votre système doit respecter la configuration requise suivante avant de générer des CSR pour les certificats PKI pour un déploiement Azure Stack Hub :

- Outil Microsoft Azure Stack Hub Readiness Checker
- Attributs de certificat :
  - Nom de la région
  - Nom de domaine pleinement qualifié externe (FQDN)
  - Objet
- Windows 10 ou Windows Server 2016 (ou version ultérieure)

  > [!NOTE]  
  > Après avoir récupéré vos certificats de sauvegarde auprès de votre autorité de certification, vous devrez procéder aux étapes décrites dans [Préparer des certificats PKI Azure Stack Hub](azure-stack-prepare-pki-certs.md) sur le même système.

## <a name="generate-certificate-signing-requests-for-new-deployments"></a>Générer des demandes de signature de certificat pour les nouveaux déploiements

Suivez les étapes ci-après afin de préparer des demandes de signature de certificat pour les nouveaux certificats PKI Azure Stack Hub :

1. Installez AzsReadinessChecker à partir d’une invite PowerShell (5.1 ou version ultérieure) en exécutant l’applet de commande suivante :

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker
    ```

2. Déclarez le **sujet**. Par exemple :

    ```powershell  
    $subject = "C=US,ST=Washington,L=Redmond,O=Microsoft,OU=Azure Stack Hub"
    ```

    > [!NOTE]  
    > Si un CN (nom commun) est fourni, il est configuré pour chaque demande de certificat. Si un CN est omis, le premier nom DNS du service Azure Stack Hub est configuré pour la demande de certificat.

3. Déclarez un répertoire de sortie qui existe déjà. Par exemple :

    ```powershell  
    $outputDirectory = "$ENV:USERPROFILE\Documents\AzureStackCSR"
    ```

4. Déclarer un système d’identité.

    Azure Active Directory (Azure AD) :

    ```powershell
    $IdentitySystem = "AAD"
    ```

    Services de fédération Active Directory (AD FS) :

    ```powershell
    $IdentitySystem = "ADFS"
    ```
    > [!NOTE]  
    > Le paramètre est obligatoire uniquement pour le déploiement de CertificateType.

5. Déclarez le **nom de région** et un **FQDN externe** pour le déploiement Azure Stack Hub.

    ```powershell
    $regionName = 'east'
    $externalFQDN = 'azurestack.contoso.com'
    ```

    > [!NOTE]  
    > `<regionName>.<externalFQDN>` constitue la base sur laquelle tous les noms DNS externes dans Azure Stack Hub sont créés. Dans cet exemple, le portail est représenté par `portal.east.azurestack.contoso.com`.  

6. Pour générer des demandes de signature de certificat pour le déploiement :

    ```powershell  
    New-AzsHubDeploymentCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

    Pour générer des demandes de certificat pour d’autres services Azure Stack Hub, changez la valeur de `-CertificateType`. Par exemple :

    ```powershell  
    # App Services
    New-AzsHubAppServicesCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory

    # DBAdapter
    New-AzsHubDbAdapterCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory

    # EventHubs
    New-AzsHubEventHubsCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory

    # IoTHub
    New-AzsHubIoTHubCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory
    ```

7. Pour les environnements Dev/Test, afin de générer une requête de certificat unique avec plusieurs autres noms d’objets, vous pouvez ajouter le paramètre **-RequestType SingleCSR** et la valeur (**non** recommandé pour les environnements de production) :

    ```powershell  
    New-AzsHubDeploymentCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -RequestType SingleCSR -subject $subject -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

8.  Passez en revue la sortie :

    ```powershell  
    Starting Certificate Request Process for Deployment
    CSR generating for following SAN(s): *.adminhosting.east.azurestack.contoso.com,*.adminvault.east.azurestack.contoso.com,*.blob.east.azurestack.contoso.com,*.hosting.east.azurestack.contoso.com,*.queue.east.azurestack.contoso.com,*.table.east.azurestack.contoso.com,*.vault.east.azurestack.contoso.com,adminmanagement.east.azurestack.contoso.com,adminportal.east.azurestack.contoso.com,management.east.azurestack.contoso.com,portal.east.azurestack.contoso.com
    Present this CSR to your Certificate Authority for Certificate Generation: C:\Users\[*redacted*]\Documents\AzureStackCSR\Deployment_east_azurestack_contoso_com_SingleCSR_CertRequest_20200710165538.req
    Certreq.exe output: CertReq: Request Created
    ```

9.  Envoyez le fichier **.REQ** généré à votre autorité de certification (interne ou publique). Le répertoire de sortie de **New-AzsCertificateSigningRequest** contient les CSR nécessaires à envoyer à une autorité de certification. Ce répertoire contient également pour votre référence un répertoire enfant contenant le ou les fichiers INF utilisés pendant la génération de demande de certificat. Assurez-vous que votre autorité de certification génère des certificats à l’aide de votre requête générée qui est conforme aux [exigences PKI d’Azure Stack Hub](azure-stack-pki-certs.md).

## <a name="generate-certificate-signing-requests-for-certificate-renewal"></a>Générer des demandes de signature de certificat pour le renouvellement de certificat

Suivez les étapes ci-après afin de préparer des demandes de signature de certificat pour le renouvellement des certificats PKI Azure Stack Hub existants :

1. Installez AzsReadinessChecker à partir d’une invite PowerShell (5.1 ou version ultérieure) en exécutant l’applet de commande suivante :

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker -Force -AllowPrerelease
    ```

2. Déclarez le point de terminaison d’horodatage **stampEndpoint** sous la forme de regionname.domain.com du système Azure Stack Hub. Par exemple (si l’adresse du portail du locataire Azure Stack Hub est <code> https://</code><code>portal.east.azurestack.contoso.com</code>) :

    ```powershell  
    $stampEndpoint = 'east.azurestack.contoso.com'
    ```

    > [!NOTE]  
    > Une connexion HTTPS est requise pour le système Azure Stack Hub ci-dessus.
    > L’outil de vérification de la disponibilité utilise le point de terminaison d’horodatage stampendpoint (région et domaine) pour créer un pointeur vers un certificat existant requis par le type de certificat. Par exemple, pour les certificats de déploiement, l’outil ajoute « portal » devant. Ainsi, portal.east.azurestack.contoso.com est utilisé dans le clonage de certificat pour les AppServices sso.appservices.east.azurestack.contoso.com, etc. Le certificat lié au point de terminaison calculé va être utilisé pour cloner des attributs tels que le sujet, la longueur de clé et l’algorithme de signature.  Si vous souhaitez modifier l’un de ces attributs, vous devez plutôt suivre les étapes décrites dans [Générer une demande de signature de certificat pour les nouveaux déploiements](azure-stack-get-pki-certs.md#generate-certificate-signing-requests-for-new-deployments).

3. Déclarez un répertoire de sortie qui existe déjà. Par exemple :

    ```powershell  
    $outputDirectory = "$ENV:USERPROFILE\Documents\AzureStackCSR"
    ```

4. Pour générer des demandes de signature de certificat pour le déploiement :

    ```powershell  
    New-AzsHubDeploymentCertificateSigningRequest -StampEndpoint $stampEndpoint -OutputRequestPath $OutputDirectory
    ```

    Si vous souhaitez générer des demandes de certificat pour d’autres services Azure Stack Hub, utilisez :

    ```powershell  
    # App Services
    New-AzsHubAppServicesCertificateSigningRequest -StampEndpoint $stampEndpoint -OutputRequestPath $OutputDirectory

    # DBAdapter
    New-AzsHubDBAdapterCertificateSigningRequest -StampEndpoint $stampEndpoint -OutputRequestPath $OutputDirectory

    # EventHubs
    New-AzsHubEventHubsCertificateSigningRequest -StampEndpoint $stampEndpoint -OutputRequestPath $OutputDirectory

    # IoTHub
    New-AzsHubIotHubCertificateSigningRequest -StampEndpoint $stampEndpoint -OutputRequestPath $OutputDirectory
    ```

5. Pour les environnements Dev/Test, afin de générer une requête de certificat unique avec plusieurs autres noms d’objets, vous pouvez ajouter le paramètre **-RequestType SingleCSR** et la valeur (**non** recommandé pour les environnements de production) :

    ```powershell  
    New-AzsHubDeploymentCertificateSigningRequest -StampEndpoint $stampendpoint -OutputRequestPath $OutputDirectory -RequestType SingleCSR
    ```

6.  Passez en revue la sortie :

    ```powershell  
    Querying StampEndpoint portal.east.azurestack.contoso.com for existing certificate
    Starting Certificate Request Process for Deployment
    CSR generating for following SAN(s): *.adminhosting.east.azurestack.contoso.com,*.adminvault.east.azurestack.contoso.com,*.blob.east.azurestack.contoso.com,*.hosting.east.azurestack.contoso.com,*.queue.east.azurestack.contoso.com,*.table.east.azurestack.contoso.com,*.vault.east.azurestack.contoso.com,adminmanagement.east.azurestack.contoso.com,adminportal.east.azurestack.contoso.com,management.east.azurestack.contoso.com,portal.east.azurestack.contoso.com
    Present this CSR to your Certificate Authority for Certificate Generation: C:\Users\[*redacted*]\Documents\AzureStackCSR\Deployment_east_azurestack_contoso_com_SingleCSR_CertRequest_20200710122723.req
    Certreq.exe output: CertReq: Request Created
    ```

7.  Envoyez le fichier **.REQ** généré à votre autorité de certification (interne ou publique). Le répertoire de sortie de **New-AzsCertificateSigningRequest** contient les CSR nécessaires à envoyer à une autorité de certification. Ce répertoire contient également pour votre référence un répertoire enfant contenant le ou les fichiers INF utilisés pendant la génération de demande de certificat. Assurez-vous que votre autorité de certification génère des certificats à l’aide de votre requête générée qui est conforme aux [exigences PKI d’Azure Stack Hub](azure-stack-pki-certs.md).

## <a name="next-steps"></a>Étapes suivantes

[Préparer des certificats PKI Azure Stack Hub](azure-stack-prepare-pki-certs.md)
