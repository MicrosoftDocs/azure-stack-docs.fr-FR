---
title: Valider l’inscription auprès d’Azure
titleSuffix: Azure Stack Hub
description: Découvrez comment valider l’inscription Azure avec l’outil Azure Stack Hub Readiness Checker.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 10/19/2020
ms.author: inhenkel
ms.reviewer: jerskine
ms.lastreviewed: 10/19/2020
ms.openlocfilehash: 0034bd17e42aba8ddbbf55c86aaffea9c243a600
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94545718"
---
# <a name="validate-azure-registration"></a>Valider l’inscription auprès d’Azure

Utilisez l’outil Azure Stack Hub Readiness Checker ( **AzsReadinessChecker** ) pour vérifier que votre abonnement Azure est prêt à être utilisé avec Azure Stack Hub avant de démarrer un déploiement Azure Stack Hub. L’outil Readiness Checker valide ce qui suit :

- Le type d’abonnement Azure que vous utilisez est pris en charge. Les abonnements doivent être de type Fournisseur de solutions cloud (CSP) ou Contrat Entreprise (EA).
- Le compte que vous utilisez pour inscrire votre abonnement auprès d’Azure peut se connecter à Azure et correspond à un propriétaire de l’abonnement.

Pour plus d’informations sur l’inscription d’Azure Stack Hub, consultez [Inscrire Azure Stack Hub auprès d’Azure](azure-stack-registration.md).

## <a name="get-the-readiness-checker-tool"></a>Obtenir l’outil Readiness Checker

Téléchargez la dernière version d’ **AzsReadinessChecker** sur le site [PowerShell Gallery](https://aka.ms/AzsReadinessChecker).  

## <a name="install-and-configure"></a>Installation et configuration

### <a name="az-powershell"></a>[Az PowerShell](#tab/az)

### <a name="prerequisites"></a>Prérequis

Les prérequis suivants sont obligatoires :

#### <a name="az-powershell-modules"></a>Modules Az PowerShell

Les modules Az PowerShell doivent être installés. Pour obtenir des instructions, consultez [Installer le module en préversion Az PowerShell](powershell-install-az-module.md).

#### <a name="azure-active-directory-aad-environment"></a>Environnement Azure Active Directory (AAD)

- Identifiez le nom d’utilisateur et le mot de passe d’un compte associé à un propriétaire de l’abonnement Azure que vous allez utiliser avec Azure Stack Hub.  
- Identifiez l’ID d’abonnement pour l’abonnement Azure à utiliser.

### <a name="steps-to-validate-the-azure-registration"></a>Étapes pour valider l’inscription auprès d’Azure

1. Ouvrez une invite PowerShell avec privilège élevé, puis exécutez la commande suivante pour installer **AzsReadinessChecker**  :

   ```powershell
   Install-Module -Name Az.BootStrapper -Force -AllowPrerelease
   Install-AzProfile -Profile 2019-03-01-hybrid -Force
   Install-Module -Name Microsoft.AzureStack.ReadinessChecker -AllowPrerelease
   ```

2. À l’invite PowerShell, exécutez la commande suivante pour définir `$subscriptionID` comme l’abonnement Azure à utiliser. Remplacez `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` par votre propre ID d’abonnement :

   ```powershell
   $subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
   ```

3. À partir de l’invite PowerShell, exécutez la commande suivante : 

   ```powershell
   Connect-AzAccount -subscription $subscriptionID
   ```

4. À partir de l’invite PowerShell, exécutez la commande suivante pour démarrer la validation de votre abonnement. Spécifiez le nom de votre administrateur Azure AD et celui de votre locataire Azure AD :

   ```powershell
   Invoke-AzsRegistrationValidation  -RegistrationSubscriptionID $subscriptionID
   ```

5. Au terme de l’exécution de l’outil, passez en revue la sortie. Vérifiez que l’état est correct pour la connexion et les conditions d’inscription. Un résultat de validation réussie s’affiche, semblable à l’exemple suivant :

   ```powershell
   Invoke-AzsRegistrationValidation v1.2005.1269 started.
   Checking Registration Requirements: OK

   Log location (contains PII): C:\Users\[*redacted*]\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Report location (contains PII): C:\Users\[*redacted*]\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
   Invoke-AzsRegistrationValidation Completed
   ```

### <a name="azurerm-powershell"></a>[AzureRM PowerShell](#tab/rm)

### <a name="prerequisites"></a>Prérequis

Les prérequis suivants sont obligatoires :

#### <a name="azurerm-powershell-modules"></a>Modules AzureRM PowerShell

Les modules Az PowerShell doivent être installés. Pour obtenir des instructions, consultez [Installer le module AzureRM PowerShell](powershell-install-az-module.md).

#### <a name="the-computer-on-which-the-tool-runs"></a>Ordinateur sur lequel l’outil est exécuté

- Windows 10 ou Windows Server 2016, avec connectivité à Internet.
- PowerShell 5.1 ou ultérieur. Pour vérifier votre version, exécutez l’applet de commande PowerShell suivante, puis examinez les versions **Major** et **Minor**  :  
  ```powershell
  $PSVersionTable.PSVersion
  ```
- [PowerShell configuré pour Azure Stack Hub](powershell-install-az-module.md).
- Dernière version de l’outil [Microsoft Azure Stack Hub Readiness Checker](https://aka.ms/AzsReadinessChecker).  

#### <a name="azure-active-directory-azure-ad-environment"></a>Environnement Azure Active Directory (Azure AD)

- Identifiez le nom d’utilisateur et le mot de passe d’un compte associé à un propriétaire de l’abonnement Azure que vous allez utiliser avec Azure Stack Hub.  
- Identifiez l’ID d’abonnement pour l’abonnement Azure à utiliser.
- Identifiez l’environnement **AzureEnvironment** que vous allez utiliser. Les valeurs prises en charge pour le paramètre du nom d’environnement sont **AzureCloud** , **AzureChinaCloud** ou **AzureUSGovernment** selon l’abonnement Azure que vous utilisez.

### <a name="steps-to-validate-the-azure-registration"></a>Étapes pour valider l’inscription auprès d’Azure

1. Sur un ordinateur qui répond aux prérequis, ouvrez une invite PowerShell avec privilège élevé, puis exécutez la commande suivante pour installer **AzsReadinessChecker**  :

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force -AllowPrerelease
   ```

2. À l’invite PowerShell, exécutez la commande suivante pour définir `$registrationCredential` comme le compte associé au propriétaire de l’abonnement. Remplacez `subscriptionowner@contoso.onmicrosoft.com` par votre compte et le nom de votre locataire :

   ```powershell
   $registrationCredential = Get-Credential subscriptionowner@contoso.onmicrosoft.com -Message "Enter Credentials for Subscription Owner"
   ```

   > [!NOTE]
   > En tant que fournisseur de services cloud, quand vous utilisez un abonnement à des services partagés ou avec des droits d’utilisation interne, vous devez fournir les informations d’identification d’un utilisateur de cette instance Azure AD. En règle générale, celles-ci sont similaires à `subscriptionowner@iurcontoso.onmicrosoft.com`. Cet utilisateur doit avoir les informations d’identification appropriées, comme décrit dans l’étape précédente.

3. À l’invite PowerShell, exécutez la commande suivante pour définir `$subscriptionID` comme l’abonnement Azure à utiliser. Remplacez `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` par votre propre ID d’abonnement :

   ```powershell
   $subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
   ```

4. À l’invite PowerShell, exécutez la commande suivante pour démarrer la validation de votre abonnement :

   - Spécifiez la valeur pour `AzureEnvironment` avec **AzureCloud** , **AzureGermanCloud** ou **AzureChinaCloud**.  
   - Indiquez le nom de votre administrateur Azure AD et celui de votre locataire Azure AD.
      ```powershell
      Invoke-AzsRegistrationValidation -RegistrationAccount $registrationCredential -AzureEnvironment AzureCloud -RegistrationSubscriptionID $subscriptionID
      ```

5. Au terme de l’exécution de l’outil, passez en revue la sortie. Vérifiez que l’état est correct pour la connexion et les conditions d’inscription. Un résultat de validation réussie s’affiche, semblable à l’exemple suivant :

   ```powershell
   Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
   Checking Registration Requirements: OK
   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
   Invoke-AzsRegistrationValidation Completed
   ```
---

## <a name="report-and-log-file"></a>Rapport et fichier journal

Chaque fois qu’une validation s’exécute, les résultats sont journalisés dans **AzsReadinessChecker.log** et **AzsReadinessCheckerReport.json**. L’emplacement de ces fichiers est indiqué avec les résultats de la validation dans PowerShell.

Ces fichiers peuvent vous aider à partager l’état de validation avant de déployer Azure Stack Hub ou à enquêter sur les problèmes de validation. Les deux fichiers conservent les résultats des vérifications de validation postérieures. Le rapport fournit à votre équipe de déploiement la confirmation de la configuration de l’identité. Le fichier journal peut aider l’équipe de déploiement ou de support à enquêter sur les problèmes de validation.

Par défaut, les deux fichiers sont écrits dans `C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json`.  

- Utilisez le paramètre `-OutputPath <path>` situé à la fin de la ligne de commande d’exécution pour spécifier un emplacement de rapport différent.
- Utilisez le paramètre `-CleanReport` à la fin de la ligne de commande d’exécution pour effacer les informations sur les exécutions précédentes de l’outil du fichier **AzsReadinessCheckerReport.json**.

Pour plus d’informations, voir [Rapport de validation Azure Stack Hub](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Échec de validation

En cas d’échec de vérification de la validation, des détails sont fournis dans la fenêtre PowerShell. L’outil journalise également des informations dans le fichier **AzsReadinessChecker.log**.

Les exemples suivants fournissent des informations supplémentaires sur les échecs de validation courants.

### <a name="user-must-be-an-owner-of-the-subscription"></a>L’utilisateur doit être un propriétaire de l’abonnement.

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail
Error Details for registration account admin@contoso.onmicrosoft.com:
The user admin@contoso.onmicrosoft.com is role(s) Reader for subscription 3f961d1c-d1fb-40c3-99ba-44524b56df2d. User must be an owner of the subscription to be used for registration.
Additional help URL https://aka.ms/AzsRemediateRegistration

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**Cause** : Le compte n’est pas celui d’un administrateur de l’abonnement Azure.

**Résolution** : Utilisez le compte d’un administrateur de l’abonnement Azure auquel l’utilisation du déploiement d’Azure Stack Hub sera facturée.

### <a name="expired-or-temporary-password"></a>Mot de passe temporaire ou ayant expiré

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail
Error Details for registration account admin@contoso.onmicrosoft.com:
Checking Registration failed with: Retrieving TenantId for subscription [subscription ID] using account admin@contoso.onmicrosoft.com failed with AADSTS50055: Force Change Password.
Trace ID: [Trace ID]
Correlation ID: [Correlation ID]
Timestamp: 2018-10-22 11:16:56Z: The remote server returned an error: (401) Unauthorized.

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**Cause**  : Le compte ne peut pas se connecter parce que le mot de passe est temporaire ou a expiré.

**Résolution**  : Dans PowerShell, exécutez la commande suivante, puis suivez les invites pour réinitialiser le mot de passe.

```powershell
Login-AzureRMAccount
```

Vous pouvez également vous connecter au [portail Azure](https://portal.azure.com) en tant que propriétaire du compte. Ainsi, l’utilisateur sera obligé de changer de mot de passe.

### <a name="unknown-user-type"></a>Type d’utilisateur inconnu  

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail
Error Details for registration account admin@contoso.onmicrosoft.com:
Checking Registration failed with: Retrieving TenantId for subscription <subscription ID> using <account> failed with unknown_user_type: Unknown User Type

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**Cause**  : Le compte ne peut pas se connecter à l’environnement Azure AD spécifié. Dans cet exemple, **AzureChinaCloud** est spécifié comme **AzureEnvironment**.  

**Résolution**  : Vérifiez que le compte est valide pour l’environnement Azure spécifié. Dans PowerShell, exécutez la commande suivante pour vérifier que le compte est valide pour un environnement spécifique :

```powershell
Login-AzureRmAccount -EnvironmentName AzureChinaCloud
```

## <a name="next-steps"></a>Étapes suivantes

- [Valider l’identité Azure](azure-stack-validate-identity.md)
- [Afficher le rapport de préparation](azure-stack-validation-report.md)
- [Considérations générales relatives à l’intégration d’Azure Stack Hub](azure-stack-datacenter-integration.md)
