---
title: Superviser les mises à jour avec PowerShell dans Azure Stack Hub
description: Découvrez comment superviser les mises à jour avec PowerShell dans Azure Stack Hub.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.lastreviewed: 08/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: 3e6f5ebac251ca18dd15d269ad23ac1632a4794c
ms.sourcegitcommit: ddcd083430ca905653d412dc2f7b813218d79509
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83374987"
---
# <a name="monitor-updates-with-powershell-in-azure-stack-hub"></a>Superviser les mises à jour avec PowerShell dans Azure Stack Hub

Vous pouvez utiliser les points de terminaison d’administration Azure Stack Hub pour superviser et gérer vos mises à jour. Ils sont accessibles avec PowerShell. Pour obtenir des instructions sur la configuration de PowerShell sur Azure Stack Hub, consultez [Installer PowerShell pour Azure Stack Hub](azure-stack-powershell-install.md).

Vous pouvez utiliser les applets de commande PowerShell suivantes pour gérer vos mises à jour :

| Applet de commande | Description |
|------------------------------------------------------|-------------|
| [Get-AzsUpdate](https://docs.microsoft.com/powershell/module/azs.update.admin/Get-AzsUpdate?view=azurestackps-1.8.0) | Obtient la liste des mises à jour disponibles. |
| [Get-AzsUpdateLocation](https://docs.microsoft.com/powershell/module/azs.update.admin/Get-AzsUpdateLocation?view=azurestackps-1.8.0)| Obtient la liste des emplacements des mises à jour. |
| [Get-AzsUpdateRun](https://docs.microsoft.com/powershell/module/azs.update.admin/Get-AzsUpdateRun?view=azurestackps-1.8.0) | Obtient la liste des exécutions de mises à jour.  |
| [Install-AzsUpdate](https://docs.microsoft.com/powershell/module/azs.update.admin/Install-AzsUpdate?view=azurestackps-1.8.0) | Appliquez une mise à jour spécifique à un emplacement de mise à jour. |
| [Resume-AzsUpdateRun](https://docs.microsoft.com/powershell/module/azs.update.admin/Resume-AzsUpdateRun?view=azurestackps-1.8.0) | Reprend une exécution de mise à jour précédemment démarrée qui a échoué. |

## <a name="get-a-list-of-update-runs"></a>Obtenir une liste d’exécutions de mises à jour

Pour afficher la liste des commandes d’exécution des mises à jour, exécutez la commande suivante :

```powershell
Get-AzsUpdateRun -UpdateName Microsoft1.0.180302.1
```

## <a name="resume-a-failed-update-operation"></a>Reprendre une opération de mise à jour ayant échoué

Si la mise à jour échoue, vous pouvez la reprendre là où elle s’est arrêtée en exécutant la commande suivante :

```powershell
Get-AzsUpdateRun -Name 5173e9f4-3040-494f-b7a7-738a6331d55c -UpdateName Microsoft1.0.180305.1 | Resume-AzsUpdateRun
```

## <a name="troubleshoot"></a>Dépanner

Pour plus d’informations sur les solutions apportées aux problèmes de mise à jour, consultez [Résolution des problèmes liés à Azure Stack](azure-stack-troubleshooting.md).

## <a name="next-steps"></a>Étapes suivantes

- [Gestion des mises à jour dans Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates)
