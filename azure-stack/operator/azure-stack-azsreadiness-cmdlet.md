---
title: Informations de référence sur l’applet de commande Start-AzsReadinessChecker
description: Aide sur l’applet de commande PowerShell pour le module Azure Stack Hub Readiness Checker.
author: ihenkel
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: inhenkel
ms.reviewer: unknown
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: e4794df9c66f1d060b6701d782c0486ddb914b02
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76877763"
---
# <a name="start-azsreadinesschecker-cmdlet-reference"></a>Informations de référence sur l’applet de commande Start-AzsReadinessChecker

Module : **Microsoft.AzureStack.ReadinessChecker**

Ce module contient une seule applet de commande. L’applet de commande effectue une ou plusieurs fonctions préalables au déploiement ou à la maintenance d’Azure Stack Hub.

## <a name="syntax"></a>Syntaxe

```powershell
Start-AzsReadinessChecker
       [-CertificatePath <String>]
       -PfxPassword <SecureString>
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       [-CertificatePath <String>]
       -PfxPassword <SecureString>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -DeploymentDataJSONPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       -Subject <OrderedDictionary>
       -RequestType <String>
       [-IncludePaaS]
       -OutputRequestPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -PfxPassword <SecureString>
       -PfxPath <String>
       -ExportPFXPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -AADDirectoryTenantName <String>
       -IdentitySystem <String>
       -AzureEnvironment <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -RegistrationAccount <PSCredential>
       -RegistrationSubscriptionID <Guid>
       -AzureEnvironment <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -RegistrationAccount <PSCredential>
       -RegistrationSubscriptionID <Guid>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -ReportPath <String>
       [-ReportSections <String>]
       [-Summary]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

## <a name="description"></a>Description

L’applet de commande **Start-AzsReadinessChecker** valide les certificats, les comptes Azure, les abonnements Azure et les annuaires Azure Active Directory (Azure AD). Exécutez la validation avant de déployer Azure Stack Hub, ou avant d’effectuer les actions de maintenance Azure Stack Hub telles que la rotation des secrets. L’applet de commande peut également servir à générer des demandes de signature de certificat pour des certificats d’infrastructure et, éventuellement, des certificats PaaS. Enfin, l’applet de commande peut repackager des certificats PFX pour corriger les problèmes de packaging courants.

## <a name="examples"></a>Exemples

### <a name="example-generate-certificate-signing-request"></a>Exemple : Générer une demande de signature de certificat

```powershell
$regionName = 'east'
$externalFQDN = 'azurestack.contoso.com'
$subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
Start-AzsReadinessChecker -regionName $regionName -externalFQDN $externalFQDN -subject $subjectHash -IdentitySystem ADFS -requestType MultipleCSR
```

Dans cet exemple, `Start-AzsReadinessChecker` génère plusieurs demandes de signature de certificat pour des certificats adaptés à un déploiement AD FS d’Azure Stack Hub, avec **east** (est) comme nom de région et **azurestack.contoso.com** comme FQDN externe.

### <a name="example-validate-certificates"></a>Exemple : valider des certificats

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
```

Dans cet exemple, le mot de passe PFX est demandé à des fins de sécurité, et `Start-AzsReadinessChecker` recherche dans le dossier relatif **Certificates** des certificats valides pour un déploiement Azure AD, avec **east** comme nom de région et **azurestack.contoso.com** comme FQDN externe.

### <a name="example-validate-certificates-with-deployment-data-deployment-and-support"></a>Exemple : valider des certificats avec des données de déploiement (déploiement et support)

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -DeploymentDataJSONPath .\deploymentdata.json
```

Dans cet exemple de déploiement et de support, le mot de passe PFX est demandé à des fins de sécurité. `Start-AzsReadinessChecker` recherche dans le dossier **Certificats** des certificats valides pour un déploiement dans lequel l’identité, la région et le nom de domaine complet externe sont lus à partir du fichier JSON de données de déploiement généré pour le déploiement.

### <a name="example-validate-paas-certificates"></a>Exemple : valider des certificats PaaS

```powershell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates -RegionName east -FQDN azurestack.contoso.com
```

Dans cet exemple, une table de hachage est construite avec des chemins et des mots de passe pour chaque certificat PaaS. Les certificats peuvent être omis. `Start-AzsReadinessChecker` vérifie l’existence de chaque chemin PFX et les valide en utilisant la région **east** et le FQDN externe **azurestack.contoso.com**.

### <a name="example-validate-paas-certificates-with-deployment-data"></a>Exemple : valider des certificats PaaS avec des données de déploiement

```powershell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates -DeploymentDataJSONPath .\deploymentdata.json
```

Dans cet exemple, une table de hachage est construite avec des chemins et des mots de passe pour chaque certificat PaaS. Les certificats peuvent être omis. `Start-AzsReadinessChecker` vérifie l’existence de chaque chemin PFX et les valide en utilisant la région et le FQDN externe lus à partir du fichier JSON de données de déploiement généré pour le déploiement.

### <a name="example-validate-azure-identity"></a>Exemple : valider l’identité Azure

```powershell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment "<environment name>" -AzureDirectoryTenantName azurestack.contoso.com
```

Dans cet exemple, les informations d’identification du compte Administrateur de service sont demandées à des fins de sécurité. `Start-AzsReadinessChecker` vérifie que le compte Azure et que l’annuaire Azure AD sont valides pour un déploiement Azure AD avec  **azurestack.contoso.com** comme nom d’annuaire de locataire.

### <a name="example-validate-azure-identity-with-deployment-data-deployment-support"></a>Exemple : valider l’identité Azure avec des données de déploiement (déploiement et support)

```PowerShell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

Dans cet exemple, les informations d’identification du compte Administrateur de service sont demandées à des fins de sécurité. `Start-AzsReadinessChecker` vérifie ensuite que le compte Azure et que l’annuaire Azure AD sont valides pour un déploiement Azure AD, où **AzureCloud** et **TenantName** sont lus à partir du fichier JSON de données de déploiement généré pour le déploiement.

### <a name="example-validate-azure-registration"></a>Exemple : valider l’inscription auprès d’Azure

```powershell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -AzureEnvironment "<environment name>"
```

Dans cet exemple, les informations d’identification du propriétaire de l’abonnement sont demandées à des fins de sécurité. `Start-AzsReadinessChecker` valide ensuite le compte et l’abonnement donnés afin de vérifier s’ils peuvent être utilisés pour l’inscription d’Azure Stack Hub.

### <a name="example-validate-azure-registration-with-deployment-data-deployment-team"></a>Exemple : valider l’inscription auprès d’Azure avec des données de déploiement (équipe de déploiement)

```powershell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID>"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

Dans cet exemple, les informations d’identification du propriétaire de l’abonnement sont demandées à des fins de sécurité. `Start-AzsReadinessChecker` valide ensuite le compte et l’abonnement donnés pour vérifier qu’ils peuvent être utilisés pour l’inscription d’Azure Stack Hub, où des détails supplémentaires sont lus à partir du fichier JSON de données de déploiement généré pour le déploiement.

### <a name="example-importexport-pfx-package"></a>Exemple : importer/exporter un package PFX

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
```

Dans cet exemple, le mot de passe PFX est requis pour des raisons de sécurité. Le fichier Ssl.pfx est importé dans le magasin de certificats de l’ordinateur local, réexporté avec le même mot de passe, puis enregistré en tant que Ssl_new.pfx. Cette procédure est utilisée lorsque la validation de certificat signale que l’attribut **Ordinateur local** n’est pas défini pour une clé privée, quand la chaîne d’approbation est rompue, quand des certificats inadaptés sont présents dans le PFX, ou quand l’ordre de la chaîne d’approbation est incorrect.

### <a name="example-view-validation-report-deployment-and-support"></a>Exemple : afficher un rapport de validation (déploiement et support)

```powershell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json
```

Dans cet exemple, l’équipe de déploiement ou de support reçoit le rapport de préparation du client (Contoso), et utilise `Start-AzsReadinessChecker` pour afficher l’état des exécutions de validation effectuées par Contoso.

### <a name="example-view-validation-report-summary-for-certificate-validation-only-deployment-and-support"></a>Exemple : afficher le récapitulatif du rapport de validation pour la validation de certificat uniquement (déploiement et support)

```powershell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json -ReportSections Certificate -Summary
```

Dans cet exemple, l’équipe de déploiement ou de support reçoit le rapport de préparation du client Contoso, et utilise `Start-AzsReadinessChecker` pour afficher un récapitulatif de l’état des exécutions de validation de certificat effectuées par Contoso.

## <a name="required-parameters"></a>Paramètres obligatoires

### <a name="-regionname"></a>-RegionName

Spécifie le nom de la région du déploiement Azure Stack Hub.

|  |  |
|----------------------------|--------------|
|Tapez :                       |String        |
|Position :                   |named         |
|Valeur par défaut :              |None          |
|Accepter l’entrée de pipeline :      |False         |
|Accepter les caractères génériques : |False         |

### <a name="-fqdn"></a>-FQDN

Spécifie le FQDN externe du déploiement Azure Stack Hub (alias : **ExternalFQDN** et **ExternalDomainName**).

|  |  |
|----------------------------|--------------|
|Tapez :                       |String        |
|Position :                   |named         |
|Valeur par défaut :              |ExternalFQDN, ExternalDomainName |
|Accepter l’entrée de pipeline :      |False         |
|Accepter les caractères génériques : |False         |

### <a name="-identitysystem"></a>-IdentitySystem

Spécifie les valeurs valides du système d’identité du déploiement Azure Stack Hub, AAD (Azure Active Directory) ou ADFS (Active Directory Federated Services).

|  |  |
|----------------------------|--------------|
|Tapez :                       |String        |
|Position :                   |named         |
|Valeur par défaut :              |None          |
|Valeurs valides :               |« AAD », « ADFS »  |
|Accepter l’entrée de pipeline :      |False         |
|Accepter les caractères génériques : |False         |

### <a name="-pfxpassword"></a>-PfxPassword

Spécifie le mot de passe associé aux fichiers de certificat PFX.

|  |  |
|----------------------------|---------|
|Tapez :                       |SecureString |
|Position :                   |named    |
|Valeur par défaut :              |None     |
|Accepter l’entrée de pipeline :      |False    |
|Accepter les caractères génériques : |False    |

### <a name="-paascertificates"></a>-PaaSCertificates

Spécifie la table de hachage contenant les chemins et mots de passe des certificats PaaS.

|  |  |
|----------------------------|---------|
|Tapez :                       |Hashtable |
|Position :                   |named    |
|Valeur par défaut :              |None     |
|Accepter l’entrée de pipeline :      |False    |
|Accepter les caractères génériques : |False    |

### <a name="-deploymentdatajsonpath"></a>-DeploymentDataJSONPath

Spécifie le fichier config JSON des données de déploiement Azure Stack Hub. Ce fichier est généré pour le déploiement.

|  |  |
|----------------------------|---------|
|Tapez :                       |String   |
|Position :                   |named    |
|Valeur par défaut :              |None     |
|Accepter l’entrée de pipeline :      |False    |
|Accepter les caractères génériques : |False    |

### <a name="-pfxpath"></a>-PfxPath

Spécifie le chemin à un certificat problématique dont la correction nécessite une routine d’importation/exportation, comme indiqué par la validation du certificat dans cet outil.

|  |  |
|----------------------------|---------|
|Tapez :                       |String   |
|Position :                   |named    |
|Valeur par défaut :              |None     |
|Accepter l’entrée de pipeline :      |False    |
|Accepter les caractères génériques : |False    |

### <a name="-exportpfxpath"></a>-ExportPFXPath  

Spécifie le chemin de destination du fichier PFX résultant de la routine d’importation/exportation.  

|  |  |
|----------------------------|---------|
|Tapez :                       |String   |
|Position :                   |named    |
|Valeur par défaut :              |None     |
|Accepter l’entrée de pipeline :      |False    |
|Accepter les caractères génériques : |False    |

### <a name="-subject"></a>-Subject

Spécifie un dictionnaire ordonné de l’objet pour la génération de demande de certificat.

|  |  |
|----------------------------|---------|
|Tapez :                       |OrderedDictionary   |
|Position :                   |named    |
|Valeur par défaut :              |None     |
|Accepter l’entrée de pipeline :      |False    |
|Accepter les caractères génériques : |False    |

### <a name="-requesttype"></a>-RequestType

Spécifie le type SAN de la demande de certificat. Valeurs valides : **MultipleCSR** et **SingleCSR**.

- **MultipleCSR** génère plusieurs demandes de certificat, une pour chaque service.
- **SingleCSR** génère une demande de certificat pour tous les services.

|  |  |
|----------------------------|---------|
|Tapez :                       |String   |
|Position :                   |named    |
|Valeur par défaut :              |None     |
|Valeurs valides :               |« MultipleCSR », « SingleCSR » |
|Accepter l’entrée de pipeline :      |False    |
|Accepter les caractères génériques : |False    |

### <a name="-outputrequestpath"></a>-OutputRequestPath

Spécifie le chemin de destination pour les fichiers de demande de certificat. Le répertoire doit déjà exister.

|  |  |
|----------------------------|---------|
|Tapez :                       |String   |
|Position :                   |named    |
|Valeur par défaut :              |None     |
|Accepter l’entrée de pipeline :      |False    |
|Accepter les caractères génériques : |False    |

### <a name="-aadserviceadministrator"></a>-AADServiceAdministrator

Spécifie l’administrateur de service Azure AD à utiliser pour le déploiement d’Azure Stack Hub.

|  |  |
|----------------------------|---------|
|Tapez :                       |PSCredential   |
|Position :                   |named    |
|Valeur par défaut :              |None     |
|Accepter l’entrée de pipeline :      |False    |
|Accepter les caractères génériques : |False    |

### <a name="-aaddirectorytenantname"></a>-AADDirectoryTenantName

Spécifie le nom Azure AD à utiliser pour le déploiement d’Azure Stack Hub.

|  |  |
|----------------------------|---------|
|Tapez :                       |String   |
|Position :                   |named    |
|Valeur par défaut :              |None     |
|Accepter l’entrée de pipeline :      |False    |
|Accepter les caractères génériques : |False    |

### <a name="-azureenvironment"></a>-AzureEnvironment

Spécifie l’instance des services Azure contenant les comptes, annuaires et les abonnements à utiliser pour le déploiement et l’inscription d’Azure Stack Hub.

|  |  |
|----------------------------|---------|
|Tapez :                       |String   |
|Position :                   |named    |
|Valeur par défaut :              |None     |
|Valeurs valides :               |« AzureCloud », « AzureChinaCloud », « AzureUSGovernment » |
|Accepter l’entrée de pipeline :      |False    |
|Accepter les caractères génériques : |False    |

### <a name="-registrationaccount"></a>-RegistrationAccount

Spécifie le compte d’inscription à utiliser pour l’inscription d’Azure Stack Hub.

|  |  |
|----------------------------|---------|
|Tapez :                       |String   |
|Position :                   |named    |
|Valeur par défaut :              |None     |
|Accepter l’entrée de pipeline :      |False    |
|Accepter les caractères génériques : |False    |

### <a name="-registrationsubscriptionid"></a>-RegistrationSubscriptionID

Spécifie l’ID d’abonnement d’inscription à utiliser pour l’inscription d’Azure Stack Hub.

|  |  |
|----------------------------|---------|
|Tapez :                       |Guid     |
|Position :                   |named    |
|Valeur par défaut :              |None     |
|Accepter l’entrée de pipeline :      |False    |
|Accepter les caractères génériques : |False    |

### <a name="-reportpath"></a>-ReportPath

Spécifie le chemin du rapport de préparation. Le répertoire actif et le nom de rapport par défaut sont utilisés par défaut.

|  |  |
|----------------------------|---------|
|Tapez :                       |String   |
|Position :                   |named    |
|Valeur par défaut :              |Tous      |
|Accepter l’entrée de pipeline :      |False    |
|Accepter les caractères génériques : |False    |

## <a name="optional-parameters"></a>Paramètres facultatifs

### <a name="-certificatepath"></a>-CertificatePath

Spécifie le chemin sous lequel seuls les dossiers de certificat exigés sont présents.

Les dossiers nécessaires pour le déploiement d’Azure Stack Hub avec le système d’identité Azure AD sont les suivants :

- ACSBlob, ACSQueue, ACSTable, Admin Portal, ARM Admin, ARM Public, KeyVault, KeyVaultInternal, Public Portal

Les dossiers nécessaires pour le déploiement d’Azure Stack Hub avec le système d’identité des services de fédération Active Directory (AD FS) sont les suivants :

- ACSBlob, ACSQueue, ACSTable, ADFS, Admin Portal, ARM Admin, ARM Public, Graph, KeyVault, KeyVaultInternal, Public Portal

|  |  |
|----------------------------|---------|
|Tapez :                       |String   |
|Position :                   |named    |
|Valeur par défaut :              |.\Certificates |
|Accepter l’entrée de pipeline :      |False    |
|Accepter les caractères génériques : |False    |

### <a name="-includepaas"></a>-IncludePaaS  

Spécifie si les noms d’hôte/services PaaS doivent être ajoutés à la ou aux demandes de certificat.

|  |  |
|----------------------------|------------------|
|Tapez :                       |SwitchParameter   |
|Position :                   |named             |
|Valeur par défaut :              |False             |
|Accepter l’entrée de pipeline :      |False             |
|Accepter les caractères génériques : |False             |

### <a name="-reportsections"></a>-ReportSections

Spécifie d’afficher uniquement le récapitulatif du rapport. Omet les détails.

|  |  |
|----------------------------|---------|
|Tapez :                       |String   |
|Position :                   |named    |
|Valeur par défaut :              |Tous      |
|Valeurs valides :               |« Certificat », « AzureRegistration », « AzureIdentity », « Jobs », « All » |
|Accepter l’entrée de pipeline :      |False    |
|Accepter les caractères génériques : |False    |

### <a name="-summary"></a>-Summary

Spécifie d’afficher uniquement le récapitulatif du rapport. Omet les détails.

|  |  |
|----------------------------|------------------|
|Tapez :                       |SwitchParameter   |
|Position :                   |named             |
|Valeur par défaut :              |False             |
|Accepter l’entrée de pipeline :      |False             |
|Accepter les caractères génériques : |False             |

### <a name="-cleanreport"></a>-CleanReport

Supprime l’historique des exécutions et validations précédentes, et écrit les validations dans un nouveau rapport.

|  |  |
|----------------------------|------------------|
|Tapez :                       |SwitchParameter   |
|Alias :                    |cf                |
|Position :                   |named             |
|Valeur par défaut :              |False             |
|Accepter l’entrée de pipeline :      |False             |
|Accepter les caractères génériques : |False             |

### <a name="-outputpath"></a>-OutputPath

Spécifie un chemin personnalisé pour enregistrer le rapport JSON de préparation et le fichier journal détaillé. Si le chemin n’existe pas, la commande tente de créer le répertoire.

|  |  |
|----------------------------|------------------|
|Tapez :                       |String            |
|Position :                   |named             |
|Valeur par défaut :              |$ENV:TEMP\AzsReadinessChecker  |
|Accepter l’entrée de pipeline :      |False             |
|Accepter les caractères génériques : |False             |

### <a name="-confirm"></a>-Confirm

Demande confirmation avant d’exécuter l’applet de commande.

|  |  |
|----------------------------|------------------|
|Tapez :                       |SwitchParameter   |
|Alias :                    |cf                |
|Position :                   |named             |
|Valeur par défaut :              |False             |
|Accepter l’entrée de pipeline :      |False             |
|Accepter les caractères génériques : |False             |

### <a name="-whatif"></a>-WhatIf

Montre ce qui se passe en cas d’exécution de l’applet de commande. L’applet de commande n’est pas exécutée.

|  |  |
|----------------------------|------------------|
|Tapez :                       |SwitchParameter   |
|Alias :                    |wi                |
|Position :                   |named             |
|Valeur par défaut :              |False             |
|Accepter l’entrée de pipeline :      |False             |
|Accepter les caractères génériques : |False             |
