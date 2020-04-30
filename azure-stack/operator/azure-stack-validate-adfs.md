---
title: Valider l’intégration d’AD FS
titleSuffix: Azure Stack Hub
description: Découvrez comment utiliser Azure Stack Hub Readiness Checker pour valider l’intégration d’AD FS pour Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: BryanLa
ms.topic: how-to
ms.date: 03/04/2020
ms.author: bryanla
ms.reviewer: jerskine
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: a8809c9f3a041d6bb4812c58d614693ce2d5431a
ms.sourcegitcommit: d930d52e27073829b8bf8ac2d581ec2accfa37e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/27/2020
ms.locfileid: "82173996"
---
# <a name="validate-ad-fs-integration-for-azure-stack-hub"></a>Valider l’intégration d’AD FS pour Azure Stack Hub

Utilisez l’outil Azure Stack Hub Readiness Checker (AzsReadinessChecker) pour vérifier que votre environnement est prêt pour l’intégration des services de fédération Active Directory (AD FS) avec Azure Stack Hub. Validez l’intégration d’AD FS avant de commencer l’intégration de centre de données ou avant un déploiement Azure Stack Hub.

L’outil Readiness Checker valide ce qui suit :

* Les *métadonnées de fédération* contiennent les éléments XML valides pour la fédération.
* Le *certificat SSL AD FS* peut être récupéré et une chaîne d’approbation peut être générée. L’AD FS avec tampon doit approuver la chaîne de certificats SSL. Le certificat doit être signé par la même *autorité de certification* que celle utilisée pour les certificats de déploiement Azure Stack Hub ou par un partenaire d’autorité racine approuvé. Pour obtenir la liste complète des partenaires d’autorité racine approuvés, consultez : [TechNet](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).
* Le *Certificat de signature AD FS* est approuvé et n’approche pas l’expiration.

Pour plus d’informations sur l’intégration de centre de données Azure Stack Hub, voir [Intégration de centre de données Azure Stack Hub – Identité](azure-stack-integrate-identity.md).

## <a name="get-the-readiness-checker-tool"></a>Obtenir l’outil Readiness Checker

Téléchargez la dernière version de l’outil Azure Stack Hub Readiness Checker (AzsReadinessChecker) à partir de [PowerShell Gallery](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Prérequis

Vérifiez les prérequis suivants.

**Ordinateur sur lequel l’outil est exécuté :**

* Windows 10 ou Windows Server 2016, avec connectivité au domaine.
* PowerShell 5.1 ou ultérieur. Pour vérifier votre version, exécutez la commande PowerShell suivante et examinez la version *principale* et les versions *mineures* :  
    ```powershell
    $PSVersionTable.PSVersion
    ```
* Dernière version de l’outil [Microsoft Azure Stack Hub Readiness Checker](https://aka.ms/AzsReadinessChecker).

**Environnement services de fédération Active Directory :**

Vous avez besoin au minimum d’une des formes de métadonnées suivantes :

- L’URL des métadonnées de fédération AD FS. Par exemple : `https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`.
* Le fichier XML des métadonnées de fédération. Par exemple : FederationMetadata.xml.

## <a name="validate-ad-fs-integration"></a>Valider l’intégration d’AD FS

1. Sur un ordinateur qui répond aux prérequis, ouvrez une invite PowerShell d’administration, puis exécutez la commande suivante pour installer AzsReadinessChecker :

    ```powershell
    Install-Module Microsoft.AzureStack.ReadinessChecker -Force
    ```

1. À l’invite PowerShell, exécutez la commande suivante pour démarrer la validation. Spécifiez la valeur de **-CustomADFSFederationMetadataEndpointUri** comme étant l’URI pour les métadonnées de fédération.

     ```powershell
     Invoke-AzsADFSValidation -CustomADFSFederationMetadataEndpointUri https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml
     ```

1. Au terme de l’exécution de l’outil, passez en revue la sortie. Vérifiez que l’état pour les conditions d’intégration d’AD FS est OK. Une validation réussie ressemble à l’exemple suivant :

    ```powershell
    Invoke-AzsADFSValidation v1.1809.1001.1 started.

    Testing ADFS Endpoint https://sts.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

            Read Metadata:                         OK
            Test Metadata Elements:                OK
            Test SSL ADFS Certificate:             OK
            Test Certificate Chain:                OK
            Test Certificate Expiry:               OK

    Details:
    [-] In standalone mode, some tests should not be considered fully indicative of connectivity or readiness the Azure Stack Hub Stamp requires prior to Datacenter Integration.
    Additional help URL: https://aka.ms/AzsADFSIntegration

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json

    Invoke-AzsADFSValidation Completed
    ```

Dans des environnements de production, le test des chaînes d’approbation des certificats à partir d’une station de travail d’opérateur est insuffisant pour attester de la fiabilité de l’infrastructure de clés publiques dans l’infrastructure Azure Stack Hub. Le réseau d’adresses IP virtuelles public du tampon d’Azure Stack Hub doit être connecté à la liste de révocation de certificats pour l’indicateur d’infrastructure de clés publiques.

## <a name="report-and-log-file"></a>Rapport et fichier journal

Chaque fois qu’une validation s’exécute, les résultats sont journalisés dans **AzsReadinessChecker.log** et **AzsReadinessCheckerReport.json**. L’emplacement de ces fichiers apparaît avec les résultats de la validation dans PowerShell.

Ces fichiers de validation peuvent vous aider à partager l’état avant de déployer Azure Stack Hub ou d’enquêter sur des problèmes de validation. Les deux fichiers conservent les résultats des vérifications de validation postérieures. Le rapport fournit à votre équipe de déploiement la confirmation de la configuration de l’identité. Le fichier journal peut aider l’équipe de déploiement ou de support à enquêter sur les problèmes de validation.

Par défaut, les deux fichiers sont écrits dans `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\`.

Utilisez :

* `-OutputPath`: Le paramètre *path* situé à la fin de la commande d’exécution pour spécifier un emplacement de rapport différent.
* `-CleanReport`: Le paramètre à la fin de la commande d’exécution pour effacer les informations AzsReadinessCheckerReport.json du rapport précédent. Pour plus d’informations, voir [Rapport de validation Azure Stack Hub](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Échec de validation

En cas d’échec de vérification de la validation, des détails s’affichent dans la fenêtre PowerShell. L’outil journalise également des informations dans le fichier *AzsReadinessChecker.log*.

Les exemples suivants donnent des conseils sur la façon de résoudre les échecs de validation courants.

### <a name="command-not-found"></a>Commande introuvable

```powershell
Invoke-AzsADFSValidation : The term 'Invoke-AzsADFSValidation' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.
```

**Cause** : La fonctionnalité de chargement automatique PowerShell n’a pas pu charger le module Readiness Checker correctement.

**Résolution** : Importez le module Readiness Checker de façon explicite. Copiez et collez le code suivant dans PowerShell et mettez à jour `<version>` avec le numéro de la version actuellement installée.

```powershell
Import-Module "c:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.ReadinessChecker\<version>\Microsoft.AzureStack.ReadinessChecker.psd1" -Force
```

## <a name="next-steps"></a>Étapes suivantes

[Afficher le rapport de préparation](azure-stack-validation-report.md)  
[Considérations générales relatives à l’intégration d’Azure Stack Hub](azure-stack-datacenter-integration.md)  
