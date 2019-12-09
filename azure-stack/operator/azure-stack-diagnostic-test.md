---
title: Utiliser l’outil de validation Azure Stack pour valider l’état du système | Microsoft Docs
description: Découvrez comment utiliser l’outil de validation Azure Stack pour valider l’état du système.
services: azure-stack
author: justinha
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 06/26/2019
ms.author: justinha
ms.reviewer: adshar
ms.lastreviewed: 12/03/2018
ms.openlocfilehash: 98732c3eb5933e1fd6d7ce42d726d3f5019c97eb
ms.sourcegitcommit: 53f7daf295783a30feb284d4c48c30c6936557c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74830955"
---
# <a name="validate-azure-stack-system-state"></a>Valider l’état du système Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

En tant qu’opérateur Azure Stack, il est essentiel de pouvoir déterminer à la demande l’intégrité et l’état de votre système. L’outil de validation Azure Stack (**Test-AzureStack**) est une applet de commande PowerShell qui vous permet d’exécuter une série de tests sur votre système pour identifier les éventuelles défaillances. Il vous est généralement demandé d’exécuter cet outil via le [point de terminaison privilégié (PEP)](azure-stack-privileged-endpoint.md) quand vous contactez les services de support technique Microsoft pour un problème. Avec toutes les informations sur l’état et l’intégrité du système à portée de main, les services de support technique peuvent collecter et analyser des journaux détaillés, se concentrer sur la zone où l’erreur s’est produite et collaborer avec vous afin de résoudre le problème.

## <a name="running-the-validation-tool-and-accessing-results"></a>Exécution de l’outil de validation et accès aux résultats

Comme indiqué précédemment, l’outil de validation est exécuté via le point de terminaison privilégié. Chaque test retourne un état **PASS/FAIL** dans la fenêtre PowerShell. Voici une brève présentation du processus de test de validation de bout en bout :

1. Accédez au point de terminaison privilégié. Exécutez les commandes suivantes afin d’établir une session de point de terminaison privilégié :

   ```powershell
   Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
   ```

   > [!TIP]
   > Pour accéder au point de terminaison privilégié sur un ordinateur hôte ASDK, utilisez AzS-ERCS01 pour -ComputerName.

2. Une fois que vous êtes dans le point de terminaison privilégié, exécutez :

   ```powershell
   Test-AzureStack
   ```

   Pour plus d’informations, consultez les sections [Considérations relatives aux paramètres](azure-stack-diagnostic-test.md#parameter-considerations) et [Exemples de cas d’usage](azure-stack-diagnostic-test.md#use-case-examples).

3. Si un test retourne **FAIL**, exécutez `Get-AzureStackLog`. Pour obtenir des instructions sur un système intégré, consultez [Pour exécuter Get-AzureStackLog sur des systèmes intégrés Azure Stack](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs) ou, sur ASDK, consultez [Exécuter Get-AzureStackLog sur un système ASDK](azure-stack-configure-on-demand-diagnostic-log-collection.md#run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system).

   L’applet de commande recueille des journaux d’activité générés par Test-AzureStack. Nous vous recommandons de ne pas collecter les journaux et de contacter le service de support technique si des tests indiquent **WARN**.

4. Si les services de support technique vous ont demandé d’exécuter l’outil de validation, le représentant du support technique vous demandera de fournir les journaux que vous avez collectés afin de poursuivre la résolution de votre problème.

## <a name="tests-available"></a>Tests disponibles

L’outil de validation vous permet d’exécuter une série de tests au niveau du système et des scénarios cloud de base, qui vous fournissent des insights sur l’état actuel vous permettant de corriger les problèmes affectant votre système.

### <a name="cloud-infrastructure-tests"></a>Tests d’infrastructure cloud

Ces tests à faible impact sont exécutés au niveau de l’infrastructure et fournissent des informations sur les différents composants et fonctions du système. Actuellement, les tests sont regroupés dans les catégories suivantes :

| Catégorie de test                                        | Argument pour -Include et -Ignore |
| :--------------------------------------------------- | :-------------------------------- |
| Résumé ACS Azure Stack                              | AzsAcsSummary                     |
| Résumé Azure Stack Active Directory                 | AzsAdSummary                      |
| Résumé des alertes Azure Stack                            | AzsAlertSummary                   |
| Résumé de l’arrêt brutal de l’application Azure Stack                | AzsApplicationCrashSummary        |
| Résumé de l’accessibilité du partage de sauvegarde Azure Stack       | AzsBackupShareAccessibility       |
| Résumé BMC Azure Stack                              | AzsStampBMCSummary                |
| Résumé de l’infrastructure d’hébergement cloud Azure Stack     | AzsHostingInfraSummary            |
| Utilisation de l’infrastructure d’hébergement cloud Azure Stack | AzsHostingInfraUtilization        |
| Résumé du plan de contrôle Azure Stack                    | AzsControlPlane                   |
| Résumé Defender Azure Stack                         | AzsDefenderSummary                |
| Résumé du microprogramme de l’infrastructure d’hébergement Azure Stack  | AzsHostingInfraFWSummary          |
| Capacité de l’infrastructure Azure Stack                  | AzsInfraCapacity                  |
| Performances de l’infrastructure Azure Stack               | AzsInfraPerformance               |
| Résumé des rôles d’infrastructure Azure Stack              | AzsInfraRoleSummary               |
| Infrastructure réseau Azure Stack                            | AzsNetworkInfra                   |
| Résumé de l’API et du portail Azure Stack                   | AzsPortalAPISummary               |
| Événements de machine virtuelle d’unité d’échelle Azure Stack                     | AzsScaleUnitEvents                |
| Ressources de machine virtuelle d’unité d’échelle Azure Stack                  | AzsScaleUnitResources             |
| Scénarios d’Azure Stack                                | AzsScenarios                      |
| Résumé des validations SDN Azure                   | AzsSDNValidation                  |
| Résumé des rôles Azure Stack Service Fabric              | AzsSFRoleSummary                  |
| Plan de données du stockage Azure Stack                       | AzsStorageDataPlane               |
| Résumé des services de stockage Azure Stack                 | AzsStorageSvcsSummary             |
| Résumé de magasin SQL Azure Stack                        | AzsStoreSummary                   |
| Résumé de mise à jour Azure Stack                           | AzsInfraUpdateSummary             |
| Résumé de la sélection élective de machine virtuelle Azure Stack                     | AzsVmPlacement                    |

### <a name="cloud-scenario-tests"></a>Tests de scénarios cloud

En plus des tests d’infrastructure ci-dessus, vous pouvez également exécuter des tests de scénarios cloud pour vérifier les fonctionnalités entre les composants de l’infrastructure. Des informations d’identification d’administrateur cloud sont nécessaires pour exécuter ces tests, car ceux-ci impliquent le déploiement de ressources.

> [!NOTE]
> Actuellement, vous ne pouvez pas exécuter de tests de scénarios cloud avec les informations d’identification des services AD FS.

Les scénarios cloud suivants sont testés par l’outil de validation :
- Création de groupe de ressources
- Création de plan
- Création d’offre
- Création de compte de stockage
- Création de machine virtuelle
- Opération de stockage d’objet blob
- Opération de stockage de file d’attente
- Opération de stockage de table

## <a name="parameter-considerations"></a>Considérations relatives aux paramètres

- Vous pouvez utiliser le paramètre **List** pour afficher toutes les catégories de tests disponibles.

- Vous pouvez utiliser les paramètres **Include** et **Ignore** pour inclure ou exclure des catégories de tests. Pour plus d’informations sur ces arguments, consultez la section suivante.

  ```powershell
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
  ```

  ```powershell
  Test-AzureStack -Ignore AzsInfraPerformance
  ```

- Une machine virtuelle de locataire est déployée dans le cadre des tests de scénario cloud. Vous pouvez utiliser **DoNotDeployTenantVm** pour désactiver ce déploiement de machine virtuelle.

- Vous devez fournir le paramètre **ServiceAdminCredential** pour exécuter des tests de scénarios cloud, comme décrit dans la section [Exemples d’utilisation](azure-stack-diagnostic-test.md#use-case-examples).

- **BackupSharePath** et **BackupShareCredential** sont utilisés quand vous testez des paramètres de sauvegarde d’infrastructure comme indiqué dans la section [Exemples d’utilisation](azure-stack-diagnostic-test.md#use-case-examples).

- **DetailedResults** peut être utilisé pour obtenir des informations de réussite/échec/avertissement sur chaque test, ainsi que sur l’exécution globale. Lorsqu’il n’est pas spécifié, **Test-AzureStack** retourne **$true** en l’absence d’échecs, et **$false** en présence d’échecs.
- **TimeoutSeconds** peut s’utiliser pour définir une heure précise d’exécution de chaque groupe.

- L’outil de validation prend également en charge des paramètres PowerShell courants : Verbose, Debug, ErrorAction, ErrorVariable, WarningAction, WarningVariable, OutBuffer, PipelineVariable et OutVariable. Pour plus d’informations, voir [About Common Parameters](https://go.microsoft.com/fwlink/?LinkID=113216) (À propos des paramètres courants).  

## <a name="use-case-examples"></a>Exemples d’utilisation

### <a name="run-validation-without-cloud-scenarios"></a>Exécuter la validation sans scénarios cloud

Exécutez l’outil de validation sans le paramètre **ServiceAdminCredential** pour ignorer l’exécution des tests de scénarios cloud : 

```powershell
New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred
Test-AzureStack
```

### <a name="run-validation-with-cloud-scenarios"></a>Exécuter la validation avec scénarios cloud

Quand vous spécifiez le paramètre **ServiceAdminCredentials**, l’outil de validation exécute les tests de scénarios cloud par défaut : 

```powershell
Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -ServiceAdminCredential "<Cloud administrator user name>" 
```

Si vous souhaitez exécuter UNIQUEMENT des scénarios cloud sans exécuter le reste des tests, vous pouvez utiliser le paramètre **Include** : 

```powershell
Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -ServiceAdminCredential "<Cloud administrator user name>" -Include AzsScenarios   
```

Vous devez taper le nom d’utilisateur de l’administrateur cloud au format UPN : serviceadmin@contoso.onmicrosoft.com (Azure AD). Quand vous y êtes invité, tapez le mot de passe du compte de l’administrateur cloud.

### <a name="groups"></a>Groupes

Afin d’améliorer l’expérience de l’opérateur, un paramètre **Group** a été activé pour exécuter plusieurs catégories de test en même temps. Actuellement, 3 groupes sont définis : **Default**, **UpdateReadiness** et **SecretRotationReadiness**.

- **Par défaut** : considéré comme une exécution standard de **Test-AzureStack**. Ce groupe est exécuté par défaut si aucun autre groupe n’est sélectionné.
- **UpdateReadiness** : Une vérification pour voir si l’instance Azure Stack peut être mise à jour. Quand le groupe **UpdateReadiness** est exécuté, les avertissements sont affichés sous forme d’erreurs dans la sortie de la console ; ils doivent être considérés comme bloquants pour la mise à jour. À compter de la version 1910 d’Azure Stack, les catégories suivantes font partie du groupe **UpdateReadiness** :

  - **AzsInfraFileValidation**
  - **AzsActionPlanStatus**
  - **AzsStampBMCSummary**

- **SecretRotationReadiness** : Une vérification pour voir si l’instance Azure Stack est dans un état où la rotation des secrets peut être effectuée. Quand le groupe **SecretRotationReadiness** est exécuté, les avertissements sont affichés sous forme d’erreurs dans la sortie de la console ; ils doivent être considérés comme bloquants pour la rotation des secrets. Les catégories suivantes font partie du groupe SecretRotationReadiness :

  - **AzsAcsSummary**
  - **AzsDefenderSummary**
  - **AzsHostingInfraSummary**
  - **AzsInfraCapacity**
  - **AzsInfraRoleSummary**
  - **AzsPortalAPISummary**
  - **AzsSFRoleSummary**
  - **AzsStorageSvcsSummary**
  - **AzsStoreSummary**

#### <a name="group-parameter-example"></a>Exemple de paramètre de groupe

L’exemple suivant exécute **Test-AzureStack** pour tester la disponibilité du système avant d’installer une mise à jour ou un correctif logiciel avec **Group**. Avant de commencer l’installation d’une mise à jour ou d’un correctif, exécutez **Test-AzureStack** pour vérifier l’état de votre système Azure Stack :

```powershell
Test-AzureStack -Group UpdateReadiness
```

Si votre système Azure Stack exécute une version antérieure à 1811, utilisez les commandes PowerShell suivantes pour exécuter **Test-AzureStack** :

```powershell
New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
```

### <a name="run-validation-tool-to-test-infrastructure-backup-settings"></a>Exécuter l’outil de validation pour tester les paramètres de sauvegarde d’infrastructure

*Avant* de configurer la sauvegarde de l’infrastructure, vous pouvez tester le chemin et les informations d’authentification du partage de sauvegarde à l’aide du test **AzsBackupShareAccessibility** :

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility -BackupSharePath "\\<fileserver>\<fileshare>" -BackupShareCredential $using:backupcred
  ```

*Après* avoir configuré la sauvegarde, vous pouvez exécuter **AzsBackupShareAccessibility** pour vérifier que le partage est accessible à partir du système ERCS :

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility
  ```

Pour tester les nouvelles informations d’identification avec le partage de sauvegarde configuré, exécutez : 

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility -BackupShareCredential "<PSCredential for backup share>"
  ```

### <a name="run-validation-tool-to-test-network-infrastructure"></a>Exécuter l’outil de validation pour tester l’infrastructure réseau

Ce test vérifie la connectivité de l’infrastructure réseau en contournant le SDN (Software Defined Network) Azure Stack. Il démontre la connectivité d’une adresse IP virtuelle publique aux redirecteurs DNS, serveurs NTP et points de terminaison d’authentification configurés. Ceci inclut la connectivité à Azure quand Azure AD est utilisé comme fournisseur d’identité ou au serveur fédéré quand AD FS est utilisé comme fournisseur d’identité.

Incluez le paramètre de débogage pour obtenir une sortie détaillée de la commande :

```powershell
Test-AzureStack -Include AzsNetworkInfra -Debug
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les outils de diagnostic Azure Stack et l’enregistrement des problèmes, consultez [Outils de diagnostic Azure Stack](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs).

Pour plus d’informations sur la résolution des problèmes, consultez [Résolution des problèmes de Microsoft Azure Stack](azure-stack-troubleshooting.md).
