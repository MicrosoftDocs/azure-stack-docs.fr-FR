---
title: Automatiser la validation Azure Stack avec PowerShell
titleSuffix: Azure Stack Hub
description: Découvrez comment automatiser la validation Azure Stack avec PowerShell.
author: mattbriggs
ms.topic: tutorial
ms.date: 08/24/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 4d6cb5cdfe6cd294e54ae9f2fa5fc78e6d71548a
ms.sourcegitcommit: 4922a14fdbc8a3b67df065336e8a21a42f224867
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2020
ms.locfileid: "88764730"
---
# <a name="automate-azure-stack-hub-validation-with-powershell"></a>Automatiser la validation Azure Stack Hub avec PowerShell

La validation en tant que service (VaaS) offre la possibilité d’automatiser le lancement de tests en utilisant le script **RunVaaSAutomation.ps1**.

Ce script peut être utilisé pour :

> [!div class="checklist"]
> * Installer les prérequis.
> * Installer et démarrer l’agent local.
> * Lancer des tests VaaS dans les workflows de passe de test, de validation de solution et de validation de package.
> * Générer des rapports sur les résultats des tests.

Les liens suivants contiennent des informations sur la façon d’exécuter des tests via le portail VaaS. Avant d’utiliser le script, vous devez vous familiariser avec les paramètres obligatoires et leurs valeurs :

* Workflow SolutionValidation : [Valider une nouvelle solution Azure Stack Hub](azure-stack-vaas-validate-solution-new.md)
* Workflow PackageValidation : [Valider les packages OEM](azure-stack-vaas-validate-oem-package.md)
* Workflow TestPass : [Planification d’un test](azure-stack-vaas-schedule-test-pass.md)

## <a name="download-the-automation-scripts"></a>Télécharger les scripts d’automatisation

1. Ouvrez une invite PowerShell avec élévation de privilèges.

2. Exécutez le script suivant pour télécharger votre script d’automatisation :

```powershell
# Review and update the $RootFolder parameter
$RootFolder = "c:\VaaS"

if (-not(Test-Path($RootFolder))) {
    mkdir $RootFolder
}
Invoke-WebRequest -Uri https://storage.azurestackvalidation.com/packages/Microsoft.VaaS.Scripts.latest.nupkg -OutFile "$RootFolder\RunVaaSAutomation.zip"
Expand-Archive -Path "$RootFolder\RunVaaSAutomation.zip" -DestinationPath "$RootFolder\RunVaaSAutomation" -Force
Set-Location "$RootFolder\RunVaaSAutomation"
```

## <a name="launch-the-solution-validation-workflow"></a>Démarrer le workflow de validation de solution

Pour savoir comment exécuter le workflow de validation de solution via le portail VaaS, consultez [Valider des packages OEM](azure-stack-vaas-validate-oem-package.md).

Exécutez le script suivant, avec les valeurs de paramètre appropriées :

```powershell
# Review and update the following parameters
$VaaSAccountUserName = ""
$VaaSAccountPassword = ""
$VaaSAccountTenantId = ""
$ServiceAdminUserName = ""
$ServiceAdminPassword = ""
$TenantAdminUserName  = ""
$TenantAdminPassword  = ""
$CloudAdminUserName   = ""
$CloudAdminPassword   = ""
$SolutionName   = ''
$ProjectName    = ''
$DiagnosticsStorageConnection=''
$VaaSProject_SolutionValidation_Configuration='Min' # enter 'Min' or 'Max'

# No need to modify the following lines
Import-Module .\VaaSAutomation.psm1 -verbose -force
$stampInfo = Get-StampInfo -cloudAdminUserName $CloudAdminUserName -cloudAdminPassword $CloudAdminPassword -retryCount 3 -retryPeriod 30 -outputFolder "."
$AgentName = "$((Get-WmiObject win32_computersystem).DNSHostName).$((Get-WmiObject win32_computersystem).Domain)".ToLower()
$VaaSAccountCredentail = New-Object System.Management.Automation.PSCredential ($VaaSAccountUserName, (ConvertTo-SecureString $VaaSAccountPassword -AsPlainText -Force))

$testParameters = @{}
$projectParameters = @{
    "Configuration" = $VaaSProject_SolutionValidation_Configuration;
}
$scriptParameters = @{
    'VaaSAccountCredentail' = $VaaSAccountCredentail;
    'VaaSAccountTenantId' = $VaaSAccountTenantId;
    'VaaSSolutionName' = $SolutionName;
    'VaaSProjectType' = 'SolutionValidation';
    'VaaSProjectName' = $ProjectName;
    'VaaSProjectParameterHashTable'= $projectParameters;
    'VaaSTestFilter' = 'Test';
    'VaaSTestFilterValue' = '';
    'VaaSTestParameterHashTable'= $testParameters;
    'VaaSOnPremAgentName'= $AgentName;
    'MaxScriptWaitTimeInHours'=48;
    'ServiceAdminUserName' = $ServiceAdminUserName;
    'ServiceAdminPassword' = $ServiceAdminPassword;
    'TenantAdminUserName' = $TenantAdminUserName;
    'TenantAdminPassword' = $TenantAdminPassword;
    'CloudAdminUserName' = $CloudAdminUserName;
    'CloudAdminPassword' = $CloudAdminPassword;
    'DiagnosticsStorageConnection' = $DiagnosticsStorageConnection;
}
& .\RunVaaSAutomation.ps1 @scriptParameters
```

## <a name="launch-package-validation-workflow"></a>Démarrer le workflow de validation de package

Pour savoir comment exécuter le workflow de validation de package via le port VaaS, consultez [Valider des packages OEM](azure-stack-vaas-validate-oem-package.md).

Exécutez le script suivant, avec les valeurs de paramètre appropriées :

```powershell
# Review and update the following parameters
$VaaSAccountUserName = ""
$VaaSAccountPassword = ""
$VaaSAccountTenantId = ""
$ServiceAdminUserName = ""
$ServiceAdminPassword = ""
$TenantAdminUserName  = ""
$TenantAdminPassword  = ""
$CloudAdminUserName   = ""
$CloudAdminPassword   = ""
$SolutionName   = ''
$ProjectName    = ''
$DiagnosticsStorageConnection=''
$VaaSProject_PackageValidation_PackageBlobUri=''
$VaaSProject_PackageValidation_RequireDigitalSignature = "false" # enter 'true' or 'false' string
$VaaSProject_PackageValidation_LocalPathtoAzureStackUpdatePkgs = ""
$VaaSProject_PackageValidation_LocalPathtoOEMUpdatePkgs = ""

# No need to modify the following lines
Import-Module .\VaaSAutomation.psm1 -verbose -force
$stampInfo = Get-StampInfo -cloudAdminUserName $CloudAdminUserName -cloudAdminPassword $CloudAdminPassword -retryCount 3 -retryPeriod 30 -outputFolder "."
$AgentName = "$((Get-WmiObject win32_computersystem).DNSHostName).$((Get-WmiObject win32_computersystem).Domain)".ToLower()
$VaaSAccountCredentail = New-Object System.Management.Automation.PSCredential ($VaaSAccountUserName, (ConvertTo-SecureString $VaaSAccountPassword -AsPlainText -Force))

$testParameters = @{
    "RequireDigitalSignature" = $VaaSProject_PackageValidation_RequireDigitalSignature;
    "LocalPathtoAzureStackUpdatePkgs" = $VaaSProject_PackageValidation_LocalPathtoAzureStackUpdatePkgs;
    "LocalPathtoOEMUpdatePkgs" = $VaaSProject_PackageValidation_LocalPathtoOEMUpdatePkgs;
}

$projectParameters = @{
    "PackageBlobUri" = $VaaSProject_PackageValidation_PackageBlobUri;
}
$scriptParameters = @{
    'VaaSAccountCredentail' = $VaaSAccountCredentail;
    'VaaSAccountTenantId' = $VaaSAccountTenantId;
    'VaaSSolutionName' = $SolutionName;
    'VaaSProjectType' = 'PackageValidation';
    'VaaSProjectName' = $ProjectName;
    'VaaSProjectParameterHashTable' = $projectParameters;
    'VaaSTestFilter' = 'Test';
    'VaaSTestFilterValue' = '';
    'VaaSTestParameterHashTable'= $testParameters;
    'VaaSOnPremAgentName'= $AgentName;
    'MaxScriptWaitTimeInHours'=96;
    'ServiceAdminUserName' = $ServiceAdminUserName;
    'ServiceAdminPassword' = $ServiceAdminPassword;
    'TenantAdminUserName' = $TenantAdminUserName;
    'TenantAdminPassword' = $TenantAdminPassword;
    'CloudAdminUserName' = $CloudAdminUserName;
    'CloudAdminPassword' = $CloudAdminPassword;
    'DiagnosticsStorageConnection' = $DiagnosticsStorageConnection;
}
& .\RunVaaSAutomation.ps1 @scriptParameters
```

## <a name="launch-the-test-pass-workflow"></a>Lancer le flux de travail de passe de test

Pour savoir comment exécuter le workflow de passe de test via le port VaaS, consultez [Planification d’un test](azure-stack-vaas-schedule-test-pass.md).

Exécutez le script suivant, avec les valeurs de paramètre appropriées :

```powershell
# Review and update the following parameters
$VaaSAccountUserName = ""
$VaaSAccountPassword = ""
$VaaSAccountTenantId = ""
$ServiceAdminUserName = ""
$ServiceAdminPassword = ""
$TenantAdminUserName  = ""
$TenantAdminPassword  = ""
$CloudAdminUserName   = ""
$CloudAdminPassword   = ""
$SolutionName   = ''
$ProjectName    = ''
$DiagnosticsStorageConnection=''

# No need to modify the following lines
# The following code is an example of running the "Cloud Simulation Engine" test
Import-Module .\VaaSAutomation.psm1 -verbose -force
$stampInfo = Get-StampInfo -cloudAdminUserName $CloudAdminUserName -cloudAdminPassword $CloudAdminPassword -retryCount 3 -retryPeriod 30 -outputFolder "."
$AgentName = "$((Get-WmiObject win32_computersystem).DNSHostName).$((Get-WmiObject win32_computersystem).Domain)".ToLower()
$VaaSAccountCredentail = New-Object System.Management.Automation.PSCredential ($VaaSAccountUserName, (ConvertTo-SecureString $VaaSAccountPassword -AsPlainText -Force))

$VaaSTestFilter='Test'
$VaaSTestFilterValue='cloudsimulation'
$testParameters = @{
    'ServiceAdminUser' = $ServiceAdminUserName;
    'ServiceAdminPassword' = $ServiceAdminPassword;
    'TenantAdminUser' = $TenantAdminUserName;
    'TenantAdminPassword' = $TenantAdminPassword;
    'CloudAdminUser' = $CloudAdminUserName;
    'CloudAdminPassword' = $CloudAdminPassword;
    'DomainFQDN' = "";
    'DomainAdminUser' = "";
    'DomainAdminPassword' = "";
    'TenantId' = $stampInfo.AadTenantId;
    'ExternalFqdn' = $stampInfo.ExternalDomainFQDN;
    'NumberOfNodes' = "$($stampInfo.NumberOfNodes)";
    'Region' = $stampInfo.RegionName;
    'RunDurationInHours' = 24;
    'EnableSuBR' = "false";
    'Faults' = "";
    'Resources' = "";
    'FaultControllerSettings' = "default";
    'DiagnosticsStorageConnection' = $diagnosticsStorageConnection;
    'DiagnosticsContainerName' = "$(New-Guid).ToString().ToLower()";
    'MaxFiddlerCaptureSizeInMB' = "0";
    'PackageHashCode' = "";
}

$projectParameters = @{}

$scriptParameters = @{
    'VaaSAccountCredentail' = $VaaSAccountCredentail;
    'VaaSAccountTenantId' = $VaaSAccountTenantId
    'VaaSSolutionName' = $SolutionName;
    'VaaSProjectType' = 'TestPass';
    'VaaSProjectName' = $ProjectName;
    'VaaSProjectParameterHashTable'= $projectParameters;
    'VaaSTestFilter'= $VaaSTestFilter;
    'VaaSTestFilterValue' = $VaaSTestFilterValue;
    'VaaSTestParameterHashTable'= $testParameters;
    'VaaSOnPremAgentName' = $AgentName;
    'MaxScriptWaitTimeInHours' = 24;
    'ServiceAdminUserName' = $ServiceAdminUserName;
    'ServiceAdminPassword' = $ServiceAdminPassword;
    'TenantAdminUserName' = $TenantAdminUserName;
    'TenantAdminPassword' = $TenantAdminPassword;
    'CloudAdminUserName' = $CloudAdminUserName;
    'CloudAdminPassword' = $CloudAdminPassword;
    'DiagnosticsStorageConnection' = $DiagnosticsStorageConnection;
}
& .\RunVaaSAutomation.ps1 @scriptParameters
```

## <a name="parameter-table"></a>Tableau des paramètres

Pour plus d’informations, consultez [Paramètres courants de workflow](azure-stack-vaas-parameters.md).

| Paramètre | Description |
| --- | --- |
| VaaSAccountUserName | Votre nom d’utilisateur VaaS pour le portail VaaS. |
| VaaSAccountPassword | Votre mot de passe VaaS pour le portail VaaS. |
| VaaSAccountTenantId | Votre identificateur global unique de locataire VaaS GUID. |
| ServiceAdminUserName | Votre compte d’administration du service Azure Stack Hub.  |
| ServiceAdminPassword | Votre mot de passe du service Azure Stack Hub.  |
| TenantAdminUserName | L’administrateur du locataire principal.  |
| TenantAdminPassword | Le mot de passe du locataire principal.  |
| CloudAdminUserName | Le nom d’utilisateur de l’administrateur de cloud.  |
| CloudAdminPassword | Le mot de passe de l’utilisateur administrateur de cloud.  |
| SolutionName | Nom de la solution VaaS. |
| ProjectName | Nom du workflow VaaS. |
| DiagnosticsStorageConnection | URL SAS d’un compte Stockage Azure dans lequel les journaux de diagnostic sont copiés pendant l’exécution des tests. Pour obtenir des instructions sur la génération de l’URL de signature d’accès partagé, consultez [Générer la chaîne de connexion des diagnostics](azure-stack-vaas-parameters.md). |

## <a name="review-the-results"></a>Passer en revue les résultats.

Les journaux des tests et les rapports sont enregistrés dans le dossier de travail actif. 

Pour plus d’options, consultez [Surveiller et gérer les tests dans le portail VaaS](azure-stack-vaas-monitor-test.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations concernant PowerShell sur Azure Stack Hub, consultez les derniers modules.

- [Module Azure Stack Hub](/powershell/azure/azure-stack/overview?view=azurestackps-1.6.0)
