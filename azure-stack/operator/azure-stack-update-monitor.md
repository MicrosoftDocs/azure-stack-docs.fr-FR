---
title: Superviser les mises à jour dans Azure Stack Hub à l’aide de PowerShell
description: Apprendre à surveiller les mises à jour dans Azure Stack Hub à l’aide de PowerShell
author: IngridAtMicrosoft
ms.topic: article
ms.date: 1/22/2020
ms.author: inhenkel
ms.lastreviewed: 08/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: 8be3e34605d2759494a7315959285829c3ab41c6
ms.sourcegitcommit: 97806b43314d306e0ddb15847c86be2c92ae001e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77508954"
---
# <a name="monitor-updates-in-azure-stack-hub-using-powershell"></a>Superviser les mises à jour dans Azure Stack Hub à l’aide de PowerShell

Vous pouvez utiliser les points de terminaison d’administration Azure Stack Hub pour superviser et gérer vos mises à jour. Ils sont accessibles avec PowerShell. Pour obtenir des instructions sur la configuration de PowerShell sur Azure Stack Hub, consultez [Installer PowerShell pour Azure Stack Hub](azure-stack-powershell-install.md).

Vous pouvez utiliser l’applet de commande PowerShell suivante pour gérer vos mises à jour :

| Applet de commande | Description |
|------------------------------------------------------|-------------|
| [Get-AzsUpdate](https://docs.microsoft.com/powershell/module/azs.update.admin/Get-AzsUpdate?view=azurestackps-1.8.0) | Obtient la liste des mises à jour disponibles. |
| [Get-AzsUpdateLocation](https://docs.microsoft.com/powershell/module/azs.update.admin/Get-AzsUpdateLocation?view=azurestackps-1.8.0)| Obtient la liste des emplacements des mises à jour. |
| [Get-AzsUpdateRun](https://docs.microsoft.com/powershell/module/azs.update.admin/Get-AzsUpdateRun?view=azurestackps-1.8.0) | Obtient la liste des exécutions de mises à jour.  |
| [Install-AzsUpdate](https://docs.microsoft.com/powershell/module/azs.update.admin/Install-AzsUpdate?view=azurestackps-1.8.0) | Appliquez une mise à jour spécifique à un emplacement de mise à jour. |
| [Resume-AzsUpdateRun](https://docs.microsoft.com/powershell/module/azs.update.admin/Resume-AzsUpdateRun?view=azurestackps-1.8.0) | Reprend une exécution de mise à jour précédemment démarrée qui a échoué. |

## <a name="get-a-list-of-update-runs"></a>Obtenir une liste d’exécutions de mises à jour

Pour afficher la liste des commandes d’exécution des mises à jour :

```powershell
Get-AzsUpdateRun -UpdateName Microsoft1.0.180302.1
```

## <a name="resume-a-failed-update-operation"></a>Reprendre une opération de mise à jour ayant échoué

Si la mise à jour échoue, vous pouvez la reprendre là où elle s’est arrêtée en exécutant la commande suivante :

```powershell
Get-AzsUpdateRun -Name 5173e9f4-3040-494f-b7a7-738a6331d55c -UpdateName Microsoft1.0.180305.1 | Resume-AzsUpdateRun
```

## <a name="next-steps"></a>Étapes suivantes

-   [Gestion des mises à jour dans Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates)
