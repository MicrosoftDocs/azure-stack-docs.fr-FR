---
title: Générer des demandes de signature de certificat pour Azure Stack Hub | Microsoft Docs
description: Découvrez comment générer des demandes de signature de certificat pour des certificats PKI Azure Stack Hub dans les systèmes intégrés Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2019
ms.author: justinha
ms.reviewer: ppacent
ms.lastreviewed: 09/10/2019
ms.openlocfilehash: 4b966ac4faec51dac06e39fab8804e295a9a40b1
ms.sourcegitcommit: 320eddb281a36d066ec80d67b103efad7d4f33c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76145833"
---
# <a name="generate-certificate-signing-requests-for-azure-stack-hub"></a>Générer des demandes de signature de certificat pour Azure Stack Hub

Vous pouvez utiliser l’outil Azure Stack Hub Readiness Checker pour créer des demandes de signature de certificat (CSR) adaptées à un déploiement Azure Stack Hub. Les certificats doivent être demandés, générés et validés avec suffisamment de temps pour les tester avant le déploiement. Vous pouvez obtenir l’outil à partir de [PowerShell Gallery](https://aka.ms/AzsReadinessChecker).

L’outil Azure Stack Hub Readiness Checker (AzsReadinessChecker) permet de demander les certificats suivants :

- **Demandes de certificat standard** conformes à [Générer une demande de signature de certificat](azure-stack-get-pki-certs.md#generate-certificate-signing-requests).
- **Plateforme en tant que service (PaaS)** : Vous pouvez demander des noms PaaS pour les certificats comme spécifié dans [Exigences de certificat pour infrastructure à clé publique Azure Stack Hub - Certificats PaaS facultatifs](azure-stack-pki-certs.md#optional-paas-certificates).

## <a name="prerequisites"></a>Conditions préalables requises

Votre système doit respecter la configuration requise suivante avant de générer des CSR pour les certificats PKI pour un déploiement Azure Stack Hub :

- Outil Microsoft Azure Stack Hub Readiness Checker
- Attributs de certificat :
  - Nom de la région
  - Nom de domaine pleinement qualifié externe (FQDN)
  - Objet
- Windows 10 ou Windows Server 2016 (ou version ultérieure)

  > [!NOTE]  
  > Après avoir récupéré vos certificats de sauvegarde auprès de votre autorité de certification, vous devrez procéder aux étapes décrites dans [Préparer des certificats PKI Azure Stack Hub](azure-stack-prepare-pki-certs.md) sur le même système.

## <a name="generate-certificate-signing-requests"></a>Générer les demandes de signature de certificat

Suivez ces étapes pour préparer et valider les certificats PKI Azure Stack Hub :

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
    New-AzsCertificateSigningRequest -certificateType Deployment -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

    Pour générer des demandes de certificat pour d’autres services Azure Stack Hub, changez la valeur de `-CertificateType`. Par exemple :

    ```powershell  
    # App Services
    New-AzsCertificateSigningRequest -certificateType AppServices -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory

    # DBAdapter
    New-AzsCertificateSigningRequest -certificateType DBAdapter -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory

    # EventHubs
    New-AzsCertificateSigningRequest -certificateType EventHubs -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory

    # IoTHub
    New-AzsCertificateSigningRequest -certificateType IoTHub -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory
    ```

7. Pour les environnements Dev/Test, afin de générer une requête de certificat unique avec plusieurs autres noms d’objets, vous pouvez ajouter le paramètre **-RequestType SingleCSR** et la valeur (**non** recommandé pour les environnements de production) :

    ```powershell  
    New-AzsCertificateSigningRequest -certificateType Deployment -RegionName $regionName -FQDN $externalFQDN -RequestType SingleCSR -subject $subject -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

8.  Passez en revue la sortie :

    ```powershell  
    New-AzsCertificateSigningRequest v1.1912.1082.37 started.
    Starting Certificate Request Process for Deployment
    CSR generating for following SAN(s): *.adminhosting.east.azurestack.contoso.com,*.adminvault.east.azurestack.contoso.com,*.blob.east.azurestack.contoso.com,*.hosting.east.azurestack.contoso.com,*.queue.east.azurestack.contoso.com,*.table.east.azurestack.contoso.com,*.vault.east.azurestack.contoso.com,adminmanagement.east.azurestack.contoso.com,adminportal.east.azurestack.contoso.com,management.east.azurestack.contoso.com,portal.east.azurestack.contoso.com
    Present this CSR to your Certificate Authority for Certificate Generation: C:\Users\checker\Documents\AzureStackCSR\wildcard_adminhosting_east_azurestack_contoso_com_CertRequest_20191219140359.req
    Certreq.exe output: CertReq: Request Created

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    New-AzsCertificateSigningRequest Completed
    ```

9.  Envoyez le fichier **.REQ** généré à votre autorité de certification (interne ou publique). Le répertoire de sortie de **New-AzsCertificateSigningRequest** contient les CSR nécessaires à envoyer à une autorité de certification. Ce répertoire contient également pour votre référence un répertoire enfant contenant le ou les fichiers INF utilisés pendant la génération de demande de certificat. Assurez-vous que votre autorité de certification génère des certificats à l’aide de votre requête générée qui est conforme aux [exigences PKI d’Azure Stack Hub](azure-stack-pki-certs.md).

## <a name="next-steps"></a>Étapes suivantes

[Préparer des certificats PKI Azure Stack Hub](azure-stack-prepare-pki-certs.md)
