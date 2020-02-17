---
title: Rapport de validation Azure Stack Hub
titleSuffix: Azure Stack Hub
description: Utilisez l’outil Azure Stack Hub Readiness Checker pour générer un rapport de validation.
author: ihenkel
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: inhenkel
ms.reviewer: unknown
ms.lastreviewed: 10/23/2018
ms.openlocfilehash: a2264608c295a29fecc5335ce4970499dd10c895
ms.sourcegitcommit: 0a3c8b0bf9c116a5caaeca453a2bbc6e7f7cbfb9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77147724"
---
# <a name="azure-stack-hub-validation-report"></a>Rapport de validation Azure Stack Hub

Utilisez l’outil [Azure Stack Hub Readiness Checker](https://www.powershellgallery.com/packages/Microsoft.AzureStack.ReadinessChecker/1.2002.1111.69) pour exécuter des validations qui prennent en charge le déploiement et la maintenance d’un environnement Azure Stack Hub. L’outil écrit les résultats dans un fichier de rapport .json. Le rapport affiche des données détaillées et synthétisées sur l’état des prérequis pour le déploiement d’Azure Stack Hub. Le rapport affiche également des informations sur la rotation des secrets pour les déploiements Azure Stack Hub existants.  

## <a name="where-to-find-the-report"></a>Emplacement du rapport

Quand l’outil s’exécute, il journalise les résultats dans **AzsReadinessCheckerReport.json**. L’outil crée également un journal nommé **AzsReadinessChecker.log**. L’emplacement de ces fichiers est indiqué avec les résultats de la validation dans PowerShell :

![résultats de l’exécution de la validation pour Azure Stack Hub Readiness Checker](./media/azure-stack-validation-report/validation.png)

Les deux fichiers conservent les résultats des vérifications de validation exécutées postérieurement sur le même ordinateur. Par exemple, vous pouvez exécuter l’outil pour valider des certificats, le réexécuter pour valider l’identité Azure, puis l’exécuter une troisième fois pour valider l’inscription. Les résultats des trois validations sont disponibles dans le rapport .json généré.  

Par défaut, les deux fichiers sont écrits dans `C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json`.  

- Utilisez le paramètre `-OutputPath <path>` situé à la fin de la ligne de commande pour spécifier un emplacement de rapport différent.
- Utilisez le paramètre `-CleanReport` à la fin de la ligne de commande pour effacer les informations sur les exécutions précédentes de l’outil du fichier **AzsReadinessCheckerReport.json**.

## <a name="view-the-report"></a>Afficher le rapport

Pour voir le rapport dans PowerShell, affectez à la valeur de `-ReportPath` le chemin au rapport. Cette commande affiche le contenu du rapport et identifie les validations qui n’ont pas encore de résultats.

Par exemple, pour voir le rapport à partir d’une invite PowerShell ouverte à l’emplacement du rapport, exécutez la commande suivante :

```powershell
Read-AzsReadinessReport -ReportPath .\AzsReadinessReport.json
```

Le résultat ressemble à l’exemple suivant :

```shell
Reading All Validation(s) from Report C:\Contoso-AzsReadinessCheckerReport.json

############### Certificate Validation Summary ###############

Certificate Validation results not available.

############### Registration Validation Summary ###############

Azure Registration Validation results not available.

############### Azure Identity Results ###############

Test                          : ServiceAdministrator
Result                        : OK
AAD Service Admin             : admin@contoso.onmicrosoft.com
Azure Environment             : AzureCloud
Azure Active Directory Tenant : contoso.onmicrosoft.com
Error Details                 : 

############### Azure Identity Validation Summary ###############

Azure Identity Validation found no errors or warnings.

############### Azure Stack Hub Graph Validation Summary ###############

Azure Stack Hub Graph Validation results not available.

############### Azure Stack Hub ADFS Validation Summary ###############

Azure Stack Hub ADFS Validation results not available.

############### AzsReadiness Job Summary ###############

Index             : 0
Operations        : 
StartTime         : 2018/10/22 14:24:16
EndTime           : 2018/10/22 14:24:19
Duration          : 3
PSBoundParameters :
```

## <a name="view-the-report-summary"></a>Afficher le récapitulatif du rapport

Pour voir un récapitulatif du rapport, vous pouvez ajouter le paramètre `-summary` à la fin de la commande PowerShell. Par exemple :

```powershell
Read-AzsReadinessReport -ReportPath .\Contoso-AzsReadinessReport.json -summary
```

Le récapitulatif affiche les validations qui n’ont pas de résultats, et indique l’état de réussite ou d’échec des validations terminées. Le résultat ressemble à l’exemple suivant :

```shell
Reading All Validation(s) from Report C:\Contoso-AzsReadinessCheckerReport.json

############### Certificate Validation Summary ###############

Certificate Validation found no errors or warnings.

############### Registration Validation Summary ###############

Registration Validation found no errors or warnings.

############### Azure Identity Validation Summary ###############

Azure Identity Validation found no errors or warnings.

############### Azure Stack Hub Graph Validation Summary ###############

Azure Stack Hub Graph Validation results not available.

############### Azure Stack Hub ADFS Validation Summary ###############

Azure Stack Hub ADFS Validation results not available.
```

## <a name="view-a-filtered-report"></a>Afficher un rapport filtré

Pour afficher un rapport filtré sur un seul type de validation, utilisez le paramètre `-ReportSections` avec l’une des valeurs suivantes :

- Certificat
- AzureRegistration
- AzureIdentity
- Graph
- ADFS
- travaux
- Tous  

Par exemple, pour afficher le récapitulatif du rapport pour les certificats uniquement, utilisez la ligne de commande PowerShell suivante :

```powershell
Read-AzsReadinessReport -ReportPath .\Contoso-AzsReadinessReport.json -ReportSections Certificate - Summary
```
