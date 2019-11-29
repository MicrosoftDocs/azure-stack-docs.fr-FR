---
title: Surveiller les mises à jour dans Azure Stack à l’aide de PowerShell | Microsoft Docs
description: Apprendre à surveiller les mises à jour dans Azure Stack à l’aide de PowerShell
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2019
ms.author: mabrigg
ms.lastreviewed: 08/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: a724320bd066e6d46e33e6bdc9c80687cfb97736
ms.sourcegitcommit: 55ec59f831a98c42a4e9ff0dd954bf10adb98ff1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74540280"
---
# <a name="monitor-updates-in-azure-stack-using-powershell"></a>Surveiller les mises à jour dans Azure Stack à l’aide de PowerShell

*S’applique à : systèmes intégrés Azure Stack*

Vous pouvez utiliser les points de terminaison d’administration Azure Stack pour surveiller et gérer vos mises à jour. Ils sont accessibles avec PowerShell. Pour obtenir des instructions sur la configuration de PowerShell sur Azure Stack, consultez [Installer PowerShell pour Azure Stack](azure-stack-powershell-install.md).

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

-   [Gestion des mises à jour dans Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates)