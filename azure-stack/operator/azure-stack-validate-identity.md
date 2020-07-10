---
title: Valider l’identité Azure
titleSuffix: Azure Stack Hub
description: Utilisez l’outil Azure Stack Hub Readiness Checker pour valider l’identité Azure.
author: BryanLa
ms.topic: how-to
ms.date: 06/25/2020
ms.author: bryanla
ms.reviewer: jerskine
ms.lastreviewed: 06/25/2020
ms.openlocfilehash: 63f071b50b2e0dac09b5a8ae628001734457d7b8
ms.sourcegitcommit: 28850ae18844213ee410cfe96fc936655b5f6412
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86148127"
---
# <a name="validate-azure-identity"></a>Valider l’identité Azure

Utilisez l’outil Azure Stack Hub Readiness Checker (**AzsReadinessChecker**) pour vérifier que votre annuaire Azure Active Directory (Azure AD) peut être utilisé avec Azure Stack Hub. Validez votre solution d’identité Azure avant de commencer un déploiement Azure Stack Hub.  

L’outil Readiness Checker valide ce qui suit :

- Azure AD en tant que fournisseur d’identité pour Azure Stack Hub.
- Le compte Azure AD que vous prévoyez d’utiliser peut se connecter en tant qu’administrateur général de votre annuaire Azure AD.

La validation garantit que votre environnement permet à Azure Stack Hub de stocker des informations sur les utilisateurs, applications, groupes et principaux de service d’Azure Stack Hub dans votre annuaire Azure AD.

## <a name="get-the-readiness-checker-tool"></a>Obtenir l’outil Readiness Checker

Téléchargez la dernière version de l’outil Azure Stack Hub Readiness Checker (AzsReadinessChecker) à partir de [PowerShell Gallery](https://aka.ms/AzsReadinessChecker).

## <a name="install-and-configure"></a>Installation et configuration

### <a name="azurerm-powershell"></a>[AzureRM PowerShell](#tab/rm)

## <a name="prerequisites"></a>Prérequis

Les prérequis suivants sont obligatoires :

#### <a name="azurerm-powershell-modules"></a>Modules AzureRM PowerShell

Les modules Az PowerShell doivent être installés. Pour obtenir des instructions, consultez [Installer le module AzureRM PowerShell](azure-stack-powershell-install.md).

#### <a name="the-computer-on-which-the-tool-runs"></a>Ordinateur sur lequel l’outil est exécuté

- Windows 10 ou Windows Server 2016, avec connectivité à Internet.
- PowerShell 5.1 ou ultérieur. Pour vérifier votre version, exécutez la commande PowerShell suivante et examinez la version **principale** et les versions **mineures** :  
  ```powershell
  $PSVersionTable.PSVersion
  ```
- [PowerShell configuré pour Azure Stack Hub](azure-stack-powershell-install.md).
- Dernière version de l’outil [Microsoft Azure Stack Hub Readiness Checker](https://aka.ms/AzsReadinessChecker).

#### <a name="azure-ad-environment"></a>Environnement Azure AD

- Identifiez le compte Azure AD à utiliser pour Azure Stack Hub et vérifiez qu’il correspond à un administrateur général Azure AD.
- Identifiez le nom du locataire Azure AD. Le nom du locataire doit être celui du domaine principal de votre annuaire Azure AD. Par exemple, **contoso.onmicrosoft.com**.
- Identifiez l’environnement Azure que vous allez utiliser. Les valeurs prises en charge pour le paramètre du nom d’environnement sont **AzureCloud**, **AzureChinaCloud** ou **AzureUSGovernment** selon l’abonnement Azure que vous utilisez.

## <a name="steps-to-validate-azure-identity"></a>Étapes de validation de l’identité Azure

1. Sur un ordinateur qui répond aux prérequis, ouvrez une invite de commande PowerShell avec privilège élevé, puis exécutez la commande suivante pour installer **AzsReadinessChecker** :  

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force
   ```

2. À l’invite PowerShell, exécutez la commande suivante pour définir `$serviceAdminCredential` comme l’administrateur de service de votre locataire Azure AD.  Remplacez `serviceadmin\@contoso.onmicrosoft.com` par votre compte et le nom de votre locataire :

   ```powershell
   $serviceAdminCredential = Get-Credential serviceadmin@contoso.onmicrosoft.com -Message "Enter credentials for service administrator of Azure Active Directory tenant"
   ```

3. À l’invite PowerShell, exécutez la commande suivante pour démarrer la validation de votre annuaire Azure AD :

   - Spécifiez la valeur du nom d’environnement pour **AzureEnvironment**. Les valeurs prises en charge pour le paramètre du nom d’environnement sont **AzureCloud**, **AzureChinaCloud** ou **AzureUSGovernment** selon l’abonnement Azure que vous utilisez.
   - Remplacez `contoso.onmicrosoft.com` par le nom de votre locataire Azure AD.

   ```powershell
   Invoke-AzsAzureIdentityValidation -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment <environment name> -AADDirectoryTenantName contoso.onmicrosoft.com
   ```

4. Au terme de l’exécution de l’outil, passez en revue la sortie. Vérifiez que l’état est **OK** pour les conditions d’installation. Une validation réussie génère une image semblable à la suivante :

   ```powershell
   Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
   Starting Azure Identity Validation

   Checking Installation Requirements: OK

   Finished Azure Identity Validation

   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
   Invoke-AzsAzureIdentityValidation Completed
   ```


### <a name="az-powershell"></a>[Az PowerShell](#tab/az)

### <a name="prerequisites"></a>Prérequis

Les prérequis suivants sont obligatoires :

#### <a name="az-powershell-modules"></a>Modules Az PowerShell

Les modules Az PowerShell doivent être installés. Pour obtenir des instructions, consultez [Installer le module en préversion Az PowerShell](powershell-install-az-module.md).

#### <a name="azure-active-directory-azure-ad-environment"></a>Environnement Azure Active Directory (Azure AD)

- Identifiez le compte Azure AD à utiliser pour Azure Stack Hub et vérifiez qu’il correspond à un administrateur général Azure AD.
- Identifiez le nom du locataire Azure AD. Le nom du locataire doit être celui du domaine principal de votre annuaire Azure AD. Par exemple, **contoso.onmicrosoft.com**.

### <a name="steps-to-validate-azure-identity"></a>Étapes de validation de l’identité Azure

1. Sur un ordinateur qui répond aux prérequis, ouvrez une invite de commande PowerShell avec privilège élevé, puis exécutez la commande suivante pour installer **AzsReadinessChecker** :  

   ```powershell
   Install-Module -Name Az.BootStrapper -Force -AllowPrerelease
   Install-AzProfile -Profile 2019-03-01-hybrid -Force
   Install-Module -Name Microsoft.AzureStack.ReadinessChecker -AllowPrerelease
   ```

2. À partir de l’invite PowerShell, exécutez la commande suivante. Remplacez `contoso.onmicrosoft.com` par le nom de votre locataire Azure AD :

   ```powershell
   Connect-AzAccount -tenant contoso.onmicrosoft.com
   ```

3. À partir de l’invite PowerShell, exécutez la commande suivante pour démarrer la validation de votre annuaire Azure AD. Remplacez `contoso.onmicrosoft.com` par le nom de votre locataire Azure AD :

   ```powershell
   Invoke-AzsAzureIdentityValidation -AADDirectoryTenantName contoso.onmicrosoft.com 
   ```

4. Au terme de l’exécution de l’outil, passez en revue la sortie. Vérifiez que l’état est **OK** pour les conditions d’installation. Une validation réussie génère une image semblable à la suivante :

   ```powershell
   Invoke-AzsAzureIdentityValidation v1.2005.1269 started.
   Starting Azure Identity Validation

   Checking Installation Requirements: OK

   Finished Azure Identity Validation

   Log location (contains PII): C:\Users\[*redacted*]\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Report location (contains PII): C:\Users\[*redacted*]\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
   Invoke-AzsAzureIdentityValidation Completed
   ```

--- 


## <a name="report-and-log-file"></a>Rapport et fichier journal

Chaque fois qu’une validation s’exécute, les résultats sont journalisés dans **AzsReadinessChecker.log** et **AzsReadinessCheckerReport.json**. L’emplacement de ces fichiers est indiqué avec les résultats de la validation dans PowerShell.

Ces fichiers peuvent vous aider à partager l’état de validation avant de déployer Azure Stack Hub ou à examiner les problèmes de validation. Les deux fichiers conservent les résultats des vérifications de validation postérieures. Le rapport fournit à votre équipe de déploiement la confirmation de la configuration de l’identité. Le fichier journal peut aider l’équipe de déploiement ou de support à enquêter sur les problèmes de validation.

Par défaut, les deux fichiers sont écrits dans `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json`.  

- Utilisez le paramètre `-OutputPath <path>` situé à la fin de la ligne de commande d’exécution pour spécifier un emplacement de rapport différent.
- Utilisez le paramètre `-CleanReport` à la fin de la ligne de commande d’exécution pour effacer les informations sur les exécutions précédentes de l’outil du fichier **AzsReadinessCheckerReport.json**.

Pour plus d’informations, voir [Rapport de validation Azure Stack Hub](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Échec de validation

En cas d’échec de vérification de la validation, des détails sont fournis dans la fenêtre PowerShell. L’outil journalise également des informations dans le fichier AzsReadinessChecker.log.

Les exemples suivants donnent des conseils sur la façon de résoudre les échecs de validation courants.

### <a name="expired-or-temporary-password"></a>Mot de passe temporaire ou ayant expiré

```powershell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
The password for account  has expired or is a temporary password that needs to be reset before continuing. Run Login-AzureRMAccount, login with  credentials and follow the prompts to reset.
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```

**Cause** : Le compte ne peut pas se connecter parce que le mot de passe est temporaire ou a expiré.

**Résolution** : Dans PowerShell, exécutez la commande suivante, puis suivez les invites pour réinitialiser le mot de passe :

```powershell
Login-AzureRMAccount
```

Vous pouvez également vous connecter au [portail Azure](https://portal.azure.com) en tant que propriétaire du compte. Ainsi, l’utilisateur sera obligé de changer de mot de passe.

### <a name="unknown-user-type"></a>Type d’utilisateur inconnu 
 
```powershell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
Unknown user type detected. Check the account  is valid for AzureChinaCloud
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```

**Cause** : Le compte ne peut pas se connecter à l’instance Azure AD spécifiée (**AADDirectoryTenantName**). Dans cet exemple, **AzureChinaCloud** est spécifié comme **AzureEnvironment**.

**Résolution** : Vérifiez que le compte est valide pour l’environnement Azure spécifié. Dans PowerShell, exécutez la commande suivante pour vérifier que le compte est valide pour un environnement spécifique :

```powershell
Login-AzureRmAccount -EnvironmentName AzureChinaCloud
```

### <a name="account-is-not-an-administrator"></a>Le compte n’est pas un administrateur

```powershell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
The Service Admin account you entered 'admin@contoso.onmicrosoft.com' is not an administrator of the Azure Active Directory tenant 'contoso.onmicrosoft.com'.
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```

**Cause** : Même si le compte peut ouvrir une session, il ne s’agit pas d’un compte d’administrateur Azure AD (**AADDirectoryTenantName**).  

**Résolution** : Connectez-vous au [portail Azure](https://portal.azure.com) en tant que propriétaire du compte, accédez à **Azure Active Directory**, puis à **Utilisateurs** et **Sélectionner l’utilisateur**. Ensuite, sélectionnez **Rôle d’annuaire**,puis vérifiez que l’utilisateur est un **Administrateur général**. Si le compte correspond à un **utilisateur**, accédez à **Azure Active Directory** > **Noms de domaine personnalisés**, puis vérifiez que le nom que vous avez fourni pour **AADDirectoryTenantName** est marqué comme un nom de domaine principal pour cet annuaire. Dans cet exemple, il s’agit de **contoso.onmicrosoft.com**.

Azure Stack Hub exige que le nom de domaine corresponde au nom de domaine principal.

## <a name="next-steps"></a>Étapes suivantes

[Valider l’inscription auprès d’Azure](azure-stack-validate-registration.md)  
[Afficher le rapport de préparation](azure-stack-validation-report.md)  
[Considérations générales relatives à l’intégration d’Azure Stack Hub](azure-stack-datacenter-integration.md)  
