---
title: Déployer l’agent local
titleSuffix: Azure Stack Hub
description: Découvrez comment déployer l’agent local pour la validation en tant que service Azure Stack Hub.
author: mattbriggs
ms.topic: quickstart
ms.date: 04/20/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ff20f82244adbbf4b1aebebd31bbf99e716bd6ce
ms.sourcegitcommit: 32834e69ef7a804c873fd1de4377d4fa3cc60fb6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661273"
---
# <a name="deploy-the-local-agent"></a>Déployer l’agent local

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Découvrez comment utiliser l’agent local de la validation en tant que service (VaaS) pour exécuter des tests de validation. Vous devez déployer l’agent local avant d’exécuter des tests de validation.

> [!Note]  
> Assurez-vous que la machine sur laquelle s’exécute l’agent local ne perd pas l’accès sortant à Internet. Cette machine doit être accessible uniquement aux utilisateurs qui sont autorisés à utiliser le service VaaS pour le compte de votre abonné.

Pour déployer l’agent local :

1. Téléchargez et installez l’agent local.
2. Effectuez des vérifications de validité avant de commencer les tests.
3. Exécutez l’agent local.

## <a name="download-and-start-the-local-agent"></a>Télécharger et démarrer l’agent local

Dans votre centre de données, téléchargez l’agent sur une machine répondant aux prérequis et ayant accès à tous les points de terminaison Azure Stack Hub. Cette machine ne doit pas faire partie intégrante du système Azure Stack Hub ni être hébergée dans le cloud Azure Stack Hub.

### <a name="machine-prerequisites"></a>Configuration requise pour les machines

Vérifiez que votre machine répond aux critères suivants :

- Accès à tous les points de terminaison Azure Stack Hub.
- .NET 4.6 et PowerShell 5.0 installés.
- 8 Go de RAM au minimum.
- 8 processeurs principaux au minimum.
- 200 Go d’espace disque au minimum.
- Connectivité réseau à Internet stable.

### <a name="download-and-install-the-local-agent"></a>Télécharger et installer l’agent local

1. Ouvrez Windows PowerShell dans une invite de commandes avec élévation de privilèges sur la machine que vous allez utiliser pour exécuter les tests.
2. Exécutez la commande suivante pour télécharger et installer les dépendances de l’agent local, puis copiez les images PIR (disque dur virtuel du système d’exploitation) dans votre environnement Azure Stack Hub.

    ```powershell
    # Review and update the following five parameters
    $RootFolder = "c:\VaaS"
    $CloudAdmindUserName = "<Cloud admin user name>"
    $CloudAdminPassword = "<Cloud admin password>"
    $AadServiceAdminUserName = "<AAD service admin user name>"
    $AadServiceAdminPassword = "<AAD service admin password>"

    if (-not(Test-Path($RootFolder))) {
        mkdir $RootFolder
    }
    Set-Location $RootFolder
    Invoke-WebRequest -Uri "https://storage.azurestackvalidation.com/packages/Microsoft.VaaSOnPrem.TaskEngineHost.latest.nupkg" -outfile "$rootFolder\OnPremAgent.zip"
    Expand-Archive -Path "$rootFolder\OnPremAgent.zip" -DestinationPath "$rootFolder\VaaSOnPremAgent" -Force
    Set-Location "$rootFolder\VaaSOnPremAgent\lib\net46"

    $cloudAdminCredential = New-Object System.Management.Automation.PSCredential($cloudAdmindUserName, (ConvertTo-SecureString $cloudAdminPassword -AsPlainText -Force))
    $getStampInfoUri = "https://ASAppGateway:4443/ServiceTypeId/4dde37cc-6ee0-4d75-9444-7061e156507f/CloudDefinition/GetStampInformation" 
    $stampInfo = Invoke-RestMethod -Method Get -Uri $getStampInfoUri -Credential $cloudAdminCredential -ErrorAction Stop
    $serviceAdminCreds = New-Object System.Management.Automation.PSCredential $aadServiceAdminUserName, (ConvertTo-SecureString $aadServiceAdminPassword -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $stampInfo.AADTenantID `
                            -ServiceAdminCreds $serviceAdminCreds `
                            -ArmEndpoint $stampInfo.AdminExternalEndpoints.AdminResourceManager `
                            -Region $stampInfo.RegionName
    ```

> [!Note]  
> L’applet de commande `Install-VaaSPrerequisites` télécharge des fichiers image de machine virtuelle volumineux. Si vous êtes confronté à une vitesse de réseau lente, vous pouvez télécharger des fichiers sur votre serveur de fichiers local et ajouter manuellement des images de machine virtuelle à votre environnement de test. Pour plus d’informations, consultez la section [Handle slow network connectivity](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity) (Gérer les problèmes de lenteur de connectivité réseau).

**Paramètres**

| Paramètre | Description |
| --- | --- |
| `AadServiceAdminUser` | Utilisateur administrateur général pour votre locataire Azure AD. Par exemple : vaasadmin@contoso.onmicrosoft.com. |
| `AadServiceAdminPassword` | Mot de passe de l’utilisateur administrateur général. |
| `CloudAdminUserName` | Utilisateur administrateur cloud qui peut accéder et exécuter des commandes autorisées dans le point de terminaison privilégié. Par exemple : AzusreStack\CloudAdmin. Pour plus d’informations, consultez [Paramètres courants de workflow pour VaaS](azure-stack-vaas-parameters.md). |
| `CloudAdminPassword` | Mot de passe pour le compte d’administrateur cloud.|

![Télécharger les prérequis de l’agent local](media/installing-prereqs.png)

## <a name="perform-sanity-checks-before-starting-the-tests"></a>Effectuez des vérifications de validité avant de commencer les tests

Les tests exécutent des opérations à distance. Pour le bon déroulement des tests, la machine qui exécute les tests doit avoir accès aux points de terminaison Azure Stack Hub. Si vous utilisez l’agent local VaaS, utilisez la machine sur laquelle l’agent s’exécutera. Vous pouvez vérifier que votre machine a accès aux points de terminaison Azure Stack Hub en effectuant les vérifications suivantes :

1. Vérifiez que l’URI de base est accessible. Ouvrez une invite de commande ou interpréteur de commandes bash, puis exécutez la commande suivante en remplaçant `<EXTERNALFQDN>` par le nom de domaine complet (FQDN) externe de votre environnement :

    ```bash
    nslookup adminmanagement.<EXTERNALFQDN>
    ```

2. Ouvrez un navigateur et accédez à `https://adminportal.<EXTERNALFQDN>` afin de vérifier que le portail MAS est accessible.

3. Connectez-vous à l’aide des valeurs du mot de passe et du nom d’administrateur de service Azure AD fournies lors de la création de la passe de test.

4. Vérifiez l’état d’intégrité du système en exécutant la cmdlet PowerShell **Test-AzureStack** comme décrit dans l’article [Exécuter un test de validation pour Azure Stack Hub](../operator/azure-stack-diagnostic-test.md). Corrigez les problèmes signalés par les avertissements et les erreurs avant de lancer le moindre test.

## <a name="run-the-local-agent"></a>Exécutez l’agent local.

1. Ouvrez Windows PowerShell dans une invite de commandes avec élévation des privilèges.

2. Exécutez la commande suivante :

    ```powershell
   # Review and update the following five parameters
    $RootFolder = "c:\VAAS"
    $CloudAdmindUserName = "<Cloud admin user name>"
    $CloudAdminPassword = "<Cloud admin password>"
    $VaaSUserId = "<VaaS user ID>"
    $VaaSTenantId = "<VaaS tenant ID>"

    Set-Location "$rootFolder\VaaSOnPremAgent\lib\net46"
    .\Microsoft.VaaSOnPrem.TaskEngineHost.exe -u $VaaSUserId -t $VaaSTenantId -x $CloudAdmindUserName -y $CloudAdminPassword
    ```

      **Paramètres**  

    | Paramètre | Description |
    | --- | --- |
    | `CloudAdminUserName` | Utilisateur administrateur cloud qui peut accéder et exécuter des commandes autorisées dans le point de terminaison privilégié. Par exemple : AzusreStack\CloudAdmin. Pour plus d’informations, consultez [Paramètres courants de workflow pour VaaS](azure-stack-vaas-parameters.md). |
    | `CloudAdminPassword` | Mot de passe pour le compte d’administrateur cloud.|
    | `VaaSUserId` | ID utilisateur qui a servi à se connecter au portail Validation Azure Stack Hub. Par exemple : NomUtilisateur\@Contoso.com). |
    | `VaaSTenantId` | ID de locataire Azure AD pour le compte Azure inscrit auprès de la validation en tant que service. |

    > [!Note]  
    > Lorsque vous exécutez l’agent, le répertoire de travail actuel doit être l’emplacement de l’exécutable de l’hôte du moteur de tâches, **Microsoft.VaaSOnPrem.TaskEngineHost.exe.**

Si aucune erreur ne vous est signalée, l’agent local a réussi. L’exemple de texte ci-après s’affiche dans la fenêtre de console.

`Heartbeat was sent successfully.`

![Agent démarré](media/started-agent.png)

Un agent est identifié de manière unique par son nom. Par défaut, il utilise le FQDN de la machine sur laquelle il a été démarré. Vous devez réduire la fenêtre pour éviter les sélections accidentelles dans cette dernière, car le basculement vers cette fenêtre interrompt toutes les autres actions.

## <a name="next-steps"></a>Étapes suivantes

- [Résoudre les problèmes de validation en tant que service](azure-stack-vaas-troubleshoot.md)
- [Validation as a Service key concepts](azure-stack-vaas-key-concepts.md) (Concepts clés à propos de la validation en tant que service)
- [Démarrage rapide : Utiliser le portail Validation Azure Stack Hub pour planifier votre premier test](azure-stack-vaas-schedule-test-pass.md)