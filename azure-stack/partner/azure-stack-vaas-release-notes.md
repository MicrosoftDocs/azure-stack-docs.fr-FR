---
title: Notes de publication d’Azure Stack Validation en tant que service | Microsoft Docs
description: Notes de publication d’Azure Stack Validation en tant que service
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ms.openlocfilehash: 0ac21cc388b55be6548f9fdba6c8985dd2316c4e
ms.sourcegitcommit: cc3534e09ad916bb693215d21ac13aed1d8a0dde
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73167139"
---
# <a name="release-notes-for-validation-as-a-service"></a>Notes de publication de Validation en tant que service

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Cet article présente les notes de publication d’Azure Stack Validation en tant que service.

## <a name="version-405"></a>Version 4.0.5

7 juin 2019

- Cloud Simulation Engine dans le workflow de validation de package a été mis à jour afin d’accélérer le processus de validation :  
    Temps d’exécution : réduit à 6 heures  
    Version : 5.1.13.0 -> 5.1.22.0  


17 janvier 2019

- Disk Identification Test mis à jour pour résoudre les incohérences du pool de stockage. Version : 5.1.14.0 -> 5.1.15.0
- Azure Stack Monthly Update Verification mis à jour pour résoudre les incohérences de validation du contenu et du logiciel approuvés. Version : 5.1.14.0 -> 5.1.17.0
- OEM Extension Package Verification mis à jour pour effectuer les vérifications nécessaires avant l’étape de mise à jour d’Azure Stack. Version : 5.1.14.0 -> 5.1.16.0
- Correctifs de bogues internes

## <a name="version-402"></a>Version 4.0.2

7 janvier 2019

Si vous exécutez le flux de travail Vérification de mise à jour mensuelle Azure Stack et que la version correspondant à votre package de mise à jour OEM n'est pas 1810 ou version ultérieure, vous recevez un message d'erreur lorsque vous accéder à l'étape de mise à jour OEM. Il s'agit d'un bogue. Un correctif est en cours de développement. Les étapes d’atténuation sont les suivantes :

1. Exécutez normalement la mise à jour OEM.
2. Exécutez Test-AzureStack après avoir appliqué le package et enregistré la sortie.
3. Annuler le test.
4. Envoyez la sorte enregistrée à VaaSHelp@microsoft.com pour recevoir les résultats de validation de l'exécution.

## <a name="version-402"></a>Version 4.0.2

30 novembre 2018

- Correctifs de bogues internes

## <a name="version-401"></a>Version 4.0.1

8 octobre 2018

- Prérequis à la validation VaaS

    `Install-VaaSPrerequisites` ne nécessite pas d’informations d’identification d’administrateur du cloud. Si vous exécutez la dernière version de cette cmdlet, consultez la section [Download and install the local agent](azure-stack-vaas-local-agent.md#download-and-install-the-local-agent) (Télécharger et installer l’agent local) pour connaître les commandes révisées pour l’installation des composants requis. Voici les commandes :

    ```powershell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region
    ```

## <a name="version-400"></a>Version 4.0.0

29 août 2018

- Prérequis à la validation VaaS et mises à jour de disque dur virtuel

    `Install-VaaSPrerequisites` requiert désormais des informations d’identification d’administrateur cloud pour résoudre un problème lors de la validation d’un package. La documentation indiquée dans la section [Download and install the local agent](azure-stack-vaas-local-agent.md#download-and-install-the-local-agent) (Télécharger et installer l’agent local) a été mise à jour comme suit :

    ```powershell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    $CloudAdminCreds = New-Object System.Management.Automation.PSCredential "<cloudAdminDomain\username>", (ConvertTo-SecureString "<cloudAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region `
                              -CloudAdminCredentials $CloudAdminCreds
    ```
    > [!NOTE]
    > Les informations d’identification `$CloudAdminCreds` requises par le script concernent l’instance Azure Stack en cours de validation. Ce ne sont pas les informations d’identification Azure Active Directory utilisées par le locataire VaaS.

- Mise à jour de l’agent local

    La version précédente de l’agent local n’est pas compatible avec la version 4.0.0 actuelle du service. Tous les utilisateurs doivent mettre à jour leurs agents locaux. Consultez la section [Download and install the local agent](azure-stack-vaas-local-agent.md#download-and-install-the-local-agent) (Télécharger et installer l’agent local) pour connaître les instructions sur l’installation de l’agent le plus récent.

- Mise à jour automatique de PowerShell

    Des modifications ont été apportées aux scripts PowerShell `LaunchVaaSTests` qui nécessitent la dernière version des packages de script. Consultez la section [Lancer le flux de travail de passe de test](azure-stack-vaas-automate-with-powershell.md#launch-the-test-pass-workflow) pour obtenir des instructions sur l’installation de la dernière version du package de script.

- Validation en tant que portail de service

  - Notifications de signature de package

    Quand un package de personnalisation OEM est envoyé dans le cadre du flux de travail de validation du package, le format du package est validé pour garantir qu’il suit la spécification publiée. Si le package n’est pas conforme, l’exécution échoue. Des notifications par courrier sont envoyées à l’adresse e-mail du contact Azure Active Directory enregistré pour le locataire.

  - Catégorie de test interactif

    La catégorie de test **interactif** a été ajoutée. Ces tests exécutent des scénarios Azure Stack interactifs, non automatisés.

  - Vérification interactive des fonctionnalités

    La capacité de fournir des commentaires ciblés pour certaines fonctionnalités est maintenant disponible dans le flux de travail de passe de test. Le test `OEM Update on Azure Stack 1806 RC Validation 5.1.4.0` vérifie si des mises à jour spécifiques ont été correctement appliquées, puis il collecte des commentaires.

## <a name="next-steps"></a>Étapes suivantes

- [Résoudre les problèmes de validation en tant que service](azure-stack-vaas-troubleshoot.md)
