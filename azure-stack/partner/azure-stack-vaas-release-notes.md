---
title: Notes de publication de la validation en tant que service
titleSuffix: Azure Stack Hub
description: Notes de publication pour la validation en tant que service Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 08/24/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 780c6a2e0f3235e2681ba4cf6cc01ce2f13eb3dc
ms.sourcegitcommit: 75a2e1a52d7582e26ce8eaf37a470c62f99b4da0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94580808"
---
# <a name="release-notes-for-validation-as-a-service"></a>Notes de publication de Validation en tant que service

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Cet article présente les notes de publication de la validation en tant que service Azure Stack Hub.

## <a name="version-4432"></a>Version 4.4.3.2

2 février 2020

- Mises à jour de maintenance du service.

- Résolution des bogues
  - Correction des tests Test101LinuxEmptyAttachedDiskManagedDisk, Test101WindowsEmptyAttachedDiskManagedDisk.

## <a name="version-4442"></a>Version 4.4.4.2

2020 - Novembre 11

- Le workflow de validation CSE a été mis à jour pour permettre l’installation automatique d’un package d’extension OEM signé de test avant une mise à jour AzureStack complète.
  - Avant ce correctif, VaaS ne parvenait pas à installer un package d’extension OEM signé de test sur un tampon après une mise à jour AzureStack complète. VaaS appliquait la mise à jour AzureStack, mais quittait l’exécution.
  - Ce problème a été résolu et vous devriez voir le workflow de validation CSE installer la mise à jour AzureStack et le package d’extension EOM signé de test fournis.
- Ajout de l’extension de validation de package OEM au workflow de validation OEM
  - Cette extension sera exécutée avant le lancement des mises à jour sur le tampon.
  - L’extension valide le contenu du package d’extension OEM et les éléments du fichier oemMetadata.xml
  - En cas d’erreurs ou de problèmes liés au package d’extension OEM, nous l’intercepterons avant le lancement des tests VaaS.
  - Ces validations ont été exécutées préalablement à la signature du package, avant l’exécution du test VaaS.  
- Prérequis VaaS mis à jour pour installer une version plus récente des modules AzureStack et AzureRM PowerShell
  - Version 1.8.2 du module AzureStack PS
  - Version 2.5.0 du module AzureRM PS
- Mises à jour mineures de service

## <a name="version-443112"></a>Version 4.4.3.112

2020 - Août 23

- Mises à jour de service.
  - Mises à jour de déploiement de service.
  - Méthodes d'authentification de service mises à jour.

## <a name="version-44368"></a>Version 4.4.3.68

2020 - Juin 30

- Mises à jour de service.
  - Déplacement du service pour s’exécuter dans Service Fabric.

## <a name="version-4421"></a>Version 4.4.2.1

9 janvier 2020

- Test des mises à jour de contenu :
  - Workflow de validation OEM (version 5.1.52.0 -> 5.1.53.0) : Réduction du nombre de paramètres obligatoires dans le volet de planification des tests.
  - Résolution de bogues pour le test de Compute - TestVMOperations

- Problèmes connus :
  - Contactez vaashelp@microsoft.com si les cas de test suivants ne s’exécutent pas pendant le workflow de validation OEM :
    - Test101LinuxEmptyAttachedDiskManagedDisk
    - Test101WindowsEmptyAttachedDiskManagedDisk

3 décembre 2019

- Test des mises à jour de contenu :
  - La documentation en ligne relative au workflow de mise à jour mensuelle Azure Stack Hub et au workflow de validation de package OEM a été mise à jour. Consultez la documentation Microsoft mise à jour : [Valider les packages OEM](azure-stack-vaas-validate-oem-package.md) et [Valider les mises à jour logicielles](azure-stack-vaas-validate-microsoft-updates.md).
  - Mise à jour du workflow de validation de package VaaS : Le workflow de validation OEM est le seul test exigé pour la vérification de mise à jour mensuelle Azure Stack Hub et la validation de package OEM. Le test met à jour l’horodatage avec les packages AzureStack/OEM fournis et exécute les tests de vérification de Cloud Simulation Engine.
  - Mise à jour de l’extension VaaS PowerShell : L’automatisation du workflow de validation de package est désormais prise en charge. Pour plus d’informations sur l’emplacement et obtenir des instructions pas à pas afin d’utiliser cette extension, consultez Automatiser le service VaaS pour Azure Stack Hub avec PowerShell.

- Problèmes connus :
  - Contactez vaashelp@microsoft.com si les cas de test suivants ne s’exécutent pas pendant le workflow de validation OEM :
    - Test101LinuxEmptyAttachedDiskManagedDisk
    - Test101WindowsEmptyAttachedDiskManagedDisk

## <a name="version-4353"></a>Version 4.3.5.3

7 novembre 2019

- Test des mises à jour de contenu :
  - Vérification de la mise à jour mensuelle Azure Stack Hub (Version 5.1.46.0 -> 5.1.49.0)
  - Vérification des packages d’extensions OEM (Version 5.1.46.0 -> 5.1.49.0).
  - Les résultats de la version 5.1.46.0 ont été conservés. Si l’exécution est réussie sur la version 5.1.46.0, adressez une notification à vaashelp@microsoft.com au moment de l’envoi des résultats.

- Résolution des bogues
  - Correction d’un problème où la vérification de la mise à jour mensuelle Azure Stack Hub ne pouvait pas s’exécuter si le fichier .zip de la mise à jour contenait des caractères spéciaux.

- Problèmes connus
  - Échec des tests VaaS si mstest.exe est introuvable. Solution de contournement :
    1. Arrêtez l’agent via Ctrl+C dans la fenêtre PowerShell.
    1. Tapez mstest.exe pour vérifier que mstest.exe est un programme reconnu.
    1. Si mstest.exe n’est pas reconnu, fermez la fenêtre PowerShell active.
    1. Cliquez sur Démarrer (et non sur PowerShell dans la barre des tâches), recherchez PowerShell, puis ouvrez-le en tant qu’administrateur.
    1. Tapez mstest.exe et vérifiez qu’il est disponible en tant que commande.
    1. Redémarrez l’agent, puis réexécutez le test.
  - Il arrive parfois que Cloud Simulation Engine signale des défaillances durant les tests de \*machines virtuelles. Contactez vaashelp@microsoft.com avant de tenter une nouvelle exécution.


29 octobre 2019

- La documentation en ligne relative au workflow de mise à jour mensuelle Azure Stack Hub et au workflow de validation de package OEM a été mise à jour.

    Consultez la documentation Microsoft mise à jour : [Valider les packages OEM](azure-stack-vaas-validate-oem-package.md) et [Valider les mises à jour logicielles](azure-stack-vaas-validate-microsoft-updates.md).
- Mise à jour du workflow VaaS : Mise à jour mensuelle Azure Stack Hub (version 5.1.30.0 -> 5.1.46.0) - Le workflow test de vérification de mise à jour mensuelle Azure Stack Hub a été mis à jour.

    Le workflow ne nécessite plus d’intervention manuelle et peut être planifié pour une exécution sans interruption.
- Mise à jour du workflow VaaS : Validation de package OEM (version 5.1.30.0 -> 5.1.46.0) - Le workflow de validation de package OEM a été mis à jour.

    Le workflow ne nécessite plus d’intervention manuelle et peut être planifié pour une exécution sans interruption.
- Cloud Simulation Engine dans le workflow de validation de package OEM (version 5.1.30.0 -> 5.1.46.0) a été mis à jour pour raccourcir le délai de validation : Durée d’exécution réduite à 1 heure.
- Dans le workflow de validation de package OEM et le workflow de mise à jour Azure Stack Hub (version 5.1.30.0 -> 5.1.46.0), Cloud Simulation Engine nécessite que les mises à jour soient validées dans deux dossiers parents distincts sans aucune autre mise à jour dans les dossiers enfants.
- Dans le workflow de validation de package OEM et le workflow de mise à jour Azure Stack Hub (version 5.1.30.0 -> 5.1.46.0), Cloud Simulation Engine nécessite une planification des tests dans l’ordre suivant : test de vérification de mise à jour mensuelle Azure Stack Hub, test de vérification de package d’extension OEM, et enfin Cloud Simulation Engine.
- Mise à jour de l’agent VaaS : L’agent VaaS mis à jour utilise désormais les informations d’identification d’administrateur du cloud Azure Stack Hub pour demander et obtenir les informations d’horodatage, afin de permettre le remplissage automatique des workflows.

    Cette mise à jour nécessite la mise à jour et le redémarrage de tous les agents. Consultez les instructions suivantes pour savoir comment mettre à jour l’agent VaaS : https://docs.microsoft.com/azure-stack/partner/azure-stack-vaas-local-agent
- Mise à jour de l’IU du portail VaaS : Le tableau de sélection d’agents a été déplacé au-dessus du volet de planification des tests pour faciliter les tests.

    Durant la planification d’un travail, il n’est plus nécessaire d’entrer des informations d’horodatage si les agents VaaS ont été correctement mis à jour.

## <a name="version-405"></a>Version 4.0.5

7 juin 2019

- Cloud Simulation Engine dans le workflow de validation de package a été mis à jour afin d’accélérer le processus de validation :  
    Temps d’exécution : réduit à 6 heures  
    Version : 5.1.13.0 -> 5.1.22.0  


17 janvier 2019

- Disk Identification Test mis à jour pour résoudre les incohérences du pool de stockage. Version : 5.1.14.0 -> 5.1.15.0
- Mise à jour de la vérification de la mise à jour mensuelle Azure Stack Hub pour résoudre les incohérences de validation du contenu et du logiciel approuvés. Version : 5.1.14.0 -> 5.1.17.0
- Mise à jour de la vérification des packages d’extension OEM pour effectuer les vérifications nécessaires avant l’étape de mise à jour d’Azure Stack Hub. Version : 5.1.14.0 -> 5.1.16.0
- Correctifs de bogues internes.

## <a name="version-403"></a>Version 4.0.3

7 janvier 2019

Si vous exécutez le workflow de vérification de mise à jour mensuelle Azure Stack Hub et que la version correspondant à votre package de mise à jour OEM n’est pas 1810 ou une version ultérieure, vous recevrez un message d’erreur lorsque vous accéderez à l’étape de mise à jour OEM. Cette erreur est un bogue. Un correctif est en cours de développement. Les étapes d’atténuation sont les suivantes :

1. Exécutez normalement la mise à jour OEM.
2. Exécutez Test-AzureStack après avoir appliqué le package et enregistré la sortie.
3. Annuler le test.
4. Envoyez la sorte enregistrée à VaaSHelp@microsoft.com pour recevoir les résultats de validation de l'exécution.

## <a name="version-402"></a>Version 4.0.2

30 novembre 2018

- Correctifs de bogues internes.

## <a name="version-401"></a>Version 4.0.1

8 octobre 2018

- Prérequis VaaS :

    `Install-VaaSPrerequisites` ne nécessite pas d’informations d’identification d’administrateur du cloud. Si vous exécutez la dernière version de cette cmdlet, consultez la section [Télécharger et installer l’agent local](azure-stack-vaas-local-agent.md#download-and-install-the-local-agent) pour connaître les commandes révisées pour l’installation des prérequis. Voici les commandes :

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

- Prérequis à la validation VaaS et mises à jour de disque dur virtuel :

    `Install-VaaSPrerequisites` requiert désormais des informations d’identification d’administrateur cloud pour résoudre un problème lors de la validation d’un package. La documentation indiquée dans la section [Télécharger et installer l’agent local](azure-stack-vaas-local-agent.md#download-and-install-the-local-agent) a été mise à jour avec le code suivant :

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
    > Les informations d’identification `$CloudAdminCreds` demandées par le script concernent l’instance Azure Stack Hub en cours de validation. Ce ne sont pas les informations d’identification Azure Active Directory utilisées par le locataire VaaS.

- Mise à jour de l’agent local :

    La version précédente de l’agent local n’est pas compatible avec la version 4.0.0 actuelle du service. Tous les utilisateurs doivent mettre à jour leurs agents locaux. Consultez la section [Download and install the local agent](azure-stack-vaas-local-agent.md#download-and-install-the-local-agent) (Télécharger et installer l’agent local) pour connaître les instructions sur l’installation de l’agent le plus récent.

- Mise à jour automatique de PowerShell :

    Des modifications ont été apportées aux scripts PowerShell `LaunchVaaSTests` qui nécessitent la dernière version des packages de script. Consultez la section [Lancer le flux de travail de passe de test](azure-stack-vaas-automate-with-powershell.md) pour obtenir des instructions sur l’installation de la dernière version du package de script.

- Portail de la validation en tant que service :

  - Notifications de signature de package

    Quand un package de personnalisation OEM est envoyé dans le cadre du workflow de validation du package, le format du package est validé pour garantir qu’il suit la spécification publiée. Si le package n’est pas conforme, l’exécution échoue. Des notifications par e-mail sont envoyées à l’adresse e-mail du contact Azure Active Directory inscrit pour le locataire.

  - Catégorie de test interactif :

    La catégorie de test **interactif** a été ajoutée. Ces tests exécutent des scénarios Azure Stack Hub interactifs, non automatisés.

  - Vérification interactive des fonctionnalités :

    La capacité de fournir des commentaires ciblés pour certaines fonctionnalités est maintenant disponible dans le flux de travail de passe de test. Le test `OEM Update on Azure Stack Hub 1806 RC Validation 5.1.4.0` vérifie si des mises à jour spécifiques ont été correctement appliquées, puis il collecte des commentaires.

## <a name="next-steps"></a>Étapes suivantes

- [Résoudre les problèmes de validation en tant que service](azure-stack-vaas-troubleshoot.md)
