---
title: Utiliser l’outil de validation Azure Stack | Microsoft Docs
description: Comment collecter des fichiers journaux de diagnostics dans Azure Stack
services: azure-stack
author: justinha
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 04/20/2019
ms.author: justinha
ms.reviewer: adshar
ms.lastreviewed: 12/03/2018
ms.openlocfilehash: 3ec925406ad3553c0beb073d39d84ae20f5bc472
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66268643"
---
# <a name="validate-azure-stack-system-state"></a>Valider l’état du système Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

En tant qu’opérateur Azure Stack, il est essentiel que vous puissiez déterminer l’intégrité et l’état de votre système à la demande. L’outil de validation Azure Stack (**Test-AzureStack**) est une applet de commande PowerShell qui vous permet d’exécuter une série de tests sur votre système pour identifier les éventuelles défaillances. Il vous sera généralement demandé d’exécuter cet outil par le biais du [point de terminaison privilégié (PEP)](azure-stack-privileged-endpoint.md) quand vous contacterez les services de support technique (CSS) Microsoft pour signaler un problème. Avec toutes les informations sur l’état et l’intégrité du système à portée de main, les services CSS peuvent collecter et analyser des journaux d’activité détaillés, se concentrer sur la zone où l’erreur s’est produite et collaborer avec vous afin de résoudre le problème.

## <a name="running-the-validation-tool-and-accessing-results"></a>Exécution de l’outil de validation et accès aux résultats

Comme indiqué précédemment, l’outil de validation est exécuté par le biais du point de terminaison privilégié. Chaque test retourne un état **PASS/FAIL** dans la fenêtre PowerShell. De plus, un rapport HTML détaillé est créé, auquel vous pouvez accéder ultérieurement pendant la [collecte des journaux](azure-stack-diagnostics.md). Voici une brève présentation du processus de test de validation de bout en bout : 

1. Accédez au point de terminaison privilégié. Exécutez les commandes suivantes afin d’établir une session de point de terminaison privilégié :

   ```powershell
   Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
   ```

   > [!TIP]
   > Pour accéder au point de terminaison privilégié sur un ordinateur hôte du Kit ASDK, utilisez AzS-ERCS01 pour -ComputerName.

2. Une fois que vous êtes dans le point de terminaison privilégié, exécutez : 

   ```powershell
   Test-AzureStack
   ```

   Pour plus d’informations, consultez les sections [Considérations relatives aux paramètres](azure-stack-diagnostic-test.md#parameter-considerations) et [Exemples d’utilisation](azure-stack-diagnostic-test.md#use-case-examples).

3. Si un test retourne **FAIL**, exécutez :

   ```powershell
   Get-AzureStackLog -FilterByRole SeedRing -OutputSharePath "<path>" -OutputShareCredential $cred
   ```

   L’applet de commande recueille des journaux d’activité générés par Test-AzureStack. Pour plus d’informations sur les journaux de diagnostic, voir [Outils de diagnostics Azure Stack](azure-stack-diagnostics.md). Vous ne devez pas recueillir de journaux d’activité ni contacter les services de support technique si les tests retournent **WARN**.

4. Si les services de support technique vous ont demandé d’exécuter l’outil de validation, le représentant du support technique vous demandera de lui fournir les journaux d’activité que vous avez recueillis afin de poursuivre la résolution de votre problème.

## <a name="tests-available"></a>Tests disponibles

L’outil de validation vous permet d’exécuter une série de tests au niveau du système et des scénarios cloud de base. Avec ces insights sur l’état actuel, vous pouvez identifier les problèmes affectant votre système.

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

En plus des tests d’infrastructure ci-dessus, vous pouvez également exécuter des tests de scénarios cloud pour vérifier les fonctionnalités entre les composants d’infrastructure. Des informations d’identification d’administrateur du cloud sont nécessaires pour exécuter ces tests, car ceux-ci impliquent un déploiement de ressources.

> [!NOTE]
> Actuellement, vous ne pouvez pas exécuter de tests de scénarios cloud avec les informations d’identification des services de fédération Active Directory (AD FS). 

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

- Vous pouvez utiliser les paramètres **Include** et **Ignore** pour inclure ou exclure des catégories de tests. Pour en savoir plus sur les informations à utiliser avec ces arguments, consultez la section suivante.

  ```powershell
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
  ```

  ```powershell
  Test-AzureStack -Ignore AzsInfraPerformance
  ```

- Une machine virtuelle de locataire est déployée dans le cadre de l’un des tests de scénario cloud. Vous pouvez utiliser **DoNotDeployTenantVm** pour désactiver cette option.

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

Vous devez taper le nom d’utilisateur de l’administrateur du cloud au format UPN : serviceadmin@contoso.onmicrosoft.com (Azure AD). Lorsque vous y êtes invité, saisissez le mot de passe du compte d’administrateur de cloud.

### <a name="groups"></a>Groupes

Afin d’améliorer l’expérience de l’opérateur, un paramètre **Group** a été activé pour exécuter plusieurs catégories de test en même temps. Actuellement, il existe 3 groupes définis : **Default**, **UpdateReadiness** et **SecretRotationReadiness**.

- **Par défaut** : considéré comme une exécution standard de **Test-AzureStack**. Ce groupe est exécuté par défaut si aucun autre groupe n’est sélectionné.
- **UpdateReadiness** : une vérification pour savoir si le tampon peut être mis à jour. Lorsque le groupe **UpdateReadiness** est exécuté, les avertissements sont affichés sous forme d’erreurs dans la sortie de console ; ils doivent être considérés comme des obstacles à la mise à jour. Les catégories suivantes font partie du groupe **UpdateReadiness** :

  - **AzsAcsSummary**
  - **AzsDefenderSummary**
  - **AzsHostingInfraSummary**
  - **AzsInfraCapacity**
  - **AzsInfraRoleSummary**
  - **AzsPortalAPISummary**
  - **AzsSFRoleSummary**
  - **AzsStoreSummary**

- **SecretRotationReadiness** : une vérification pour savoir si le tampon pour telle rotation des secrets peut être exécuté. Lorsque le groupe **SecretRotationReadiness** est exécuté, les avertissements sont affichés sous forme d’erreurs dans la sortie de console ; ils doivent être considérés comme des obstacles à la rotation des secrets. Les catégories suivantes font partie du groupe SecretRotationReadiness :

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

L’exemple suivant exécute **Test-AzureStack** pour tester la disponibilité du système avant d’installer une mise à jour ou un correctif logiciel avec **Group**. Avant de commencer l’installation d’une mise à jour ou d’un correctif, vous devez exécuter **Test-AzureStack** pour vérifier l’état de votre système Azure Stack :

```powershell
Test-AzureStack -Group UpdateReadiness
```

Toutefois, si votre système Azure Stack exécute une version inférieure à 1811, utilisez les commandes PowerShell suivantes pour exécuter **Test-AzureStack** :

```powershell
New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
```

### <a name="run-validation-tool-to-test-infrastructure-backup-settings"></a>Exécuter l’outil de validation pour tester les paramètres de sauvegarde d’infrastructure

*Avant* de configurer la sauvegarde de l’infrastructure, vous pouvez tester le chemin et les informations d’authentification du partage de sauvegarde à l’aide du test **AzsBackupShareAccessibility** : 

  ```powershell
  New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility -BackupSharePath "\\<fileserver>\<fileshare>" -BackupShareCredential <PSCredentials-for-backup-share>
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



## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les outils de diagnostic Azure Stack et l’enregistrement des problèmes, consultez [Outils de diagnostic Azure Stack](azure-stack-diagnostics.md).

Pour plus d’informations sur la résolution des problèmes, consultez [Résolution des problèmes de Microsoft Azure Stack](azure-stack-troubleshooting.md).
