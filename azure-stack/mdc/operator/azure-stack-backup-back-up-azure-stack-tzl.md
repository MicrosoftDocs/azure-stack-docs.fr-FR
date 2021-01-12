---
title: Sauvegarder Azure Stack - MDC | Microsoft Docs
description: Découvrez comment effectuer une sauvegarde à la demande sur Azure Stack pour un Modular Data Center (MDC).
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2020
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 10/26/2020
ms.openlocfilehash: f64b656b6a0a0d5310b6d6e2fbb8ff26d5206ad1
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910889"
---
# <a name="back-up-azure-stack---modular-data-center-mdc"></a>Sauvegarder Azure Stack - Modular Data Center (MDC)

*S’applique à : Modular Data Center, Azure Stack Hub renforcé*

Cet article explique comment effectuer une sauvegarde à la demande d’Azure Stack.

## <a name="start-backup"></a>Démarrer la sauvegarde

Les sauvegardes sont automatiquement planifiées. Une sauvegarde à la demande est uniquement nécessaire si vous recevez une alerte pour déclencher la sauvegarde manuellement. Pour démarrer une sauvegarde à la demande, sélectionnez **Sauvegarder maintenant**. Le fait d’effectuer une sauvegarde à la demande ne modifie pas la date et l’heure de la prochaine sauvegarde planifiée. Une fois la tâche terminée, vous pouvez confirmer les paramètres dans le panneau **Éléments principaux** :

![Paramètres de sauvegarde](media/azure-stack-backup-back-up-azure-stack-tzl/on-demand-backup.png)

## <a name="start-azure-stack-backup"></a>Démarrer la sauvegarde d’Azure Stack

Vous pouvez également exécuter l’applet de commande PowerShell **Start-AzsBackup** sur votre ordinateur d’administration Azure Stack.

### <a name="start-a-new-backup-without-job-progress-tracking"></a>Démarrer une nouvelle sauvegarde sans suivi de la progression du travail

Utilisez **Start-AzSBackup** pour démarrer immédiatement une nouvelle sauvegarde, sans suivi de la progression du travail.

```powershell
Start-AzsBackup -Force
```

## <a name="start-azure-stack-backup-with-job-progress-tracking"></a>Démarrer une sauvegarde Azure Stack avec suivi de la progression du travail

Utilisez **Start-AzSBackup** pour démarrer une nouvelle sauvegarde avec le paramètre **-AsJob** et l’enregistrer comme variable afin de suivre la progression du travail de sauvegarde.

> [!NOTE]
> Le travail de sauvegarde s’affiche comme étant terminé dans le portail environ 10 à 15 minutes avant la fin de son exécution.
>
> Il est préférable d’observer l’état réel via le code suivant.

Le délai initial d’une milliseconde est ajouté car le code est trop rapide pour inscrire correctement le travail. Le code ne signale aucune valeur **PSBeginTime** et donc, aucune valeur **État** du travail.

```powershell
$BackupJob = Start-AzsBackup -Force -AsJob
While (!$BackupJob.PSBeginTime) {
    Start-Sleep -Milliseconds 1
}
Write-Host "Start time: $($BackupJob.PSBeginTime)"
While ($BackupJob.State -eq "Running") {
    Write-Host "Job is currently: $($BackupJob.State) - Duration: $((New-TimeSpan -Start ($BackupJob.PSBeginTime) -End (Get-Date)).ToString().Split(".")[0])"
    Start-Sleep -Seconds 30
}

If ($BackupJob.State -eq "Completed") {
    Get-AzsBackup | Where-Object {$_.BackupId -eq $BackupJob.Output.BackupId}
    $Duration = $BackupJob.Output.TimeTakenToCreate
    $Pattern = '^P?T?((?<Years>\d+)Y)?((?<Mohs>\d+)M)?((?<Weeks>\d+)W)?((?<Days>\d+)D)?(T((?<Hours>\d+)H)?((?<Minutes>\d+)M)?((?<Seconds>\d*(\.)?\d*)S)?)$'
    If ($Duration -match $Pattern) {
        If (!$Matches.ContainsKey("Hours")) {
            $Hours = ""
        } 
        Else {
            $Hours = ($Matches.Hours).ToString + 'h '
        }
        $Minutes = ($Matches.Minutes)
        $Seconds = [math]::round(($Matches.Seconds))
        $Runtime = '{0}{1:00}m {2:00}s' -f $Hours, $Minutes, $Seconds
    }
    Write-Host "BackupJob: $($BackupJob.Output.BackupId) - Completed with Status: $($BackupJob.Output.Status) - It took: $($Runtime) to run" -ForegroundColor Green
}
ElseIf ($BackupJob.State -ne "Completed") {
    $BackupJob
    $BackupJob.Output
}
```

## <a name="confirm-backup-has-completed"></a>Confirmer la fin de la sauvegarde

### <a name="confirm-backup-has-completed-using-powershell"></a>Confirmer la fin de la sauvegarde à l’aide de PowerShell

Utilisez les commandes PowerShell suivantes pour vérifier que la sauvegarde s’est bien terminée :

```powershell
Get-AzsBackup
```

Le résultat doit ressembler à la sortie suivante :

```shell
BackupDataVersion : 1.0.1
BackupId          : <backup ID>
RoleStatus        : {NRP, SRP, CRP, KeyVaultInternalControlPlane...}
Status            : Succeeded
CreatedDateTime   : 7/6/2018 6:46:24 AM
TimeTakenToCreate : PT20M32.364138S
DeploymentID      : <deployment ID>
StampVersion      : 1.1807.0.41
OemVersion        : 
Id                : /subscriptions/<subscription ID>/resourceGroups/System.local/providers/Microsoft.Backup.Admin/backupLocations/local/backups/<backup ID>
Name              : local/<local name>
Type              : Microsoft.Backup.Admin/backupLocations/backups
Location          : local
Tags              : {}
```

### <a name="confirm-backup-has-completed-in-the-administrator-portal"></a>Vérifier que la sauvegarde est terminée dans le portail d’administration

Dans le portail d’administration Azure Stack, vérifiez que la sauvegarde s’est terminée en effectuant les étapes suivantes :

1. Ouvrez le [portail d’administration Azure Stack](../../operator/azure-stack-manage-portals.md).

2. Sélectionnez **Tous les services** puis, sous la catégorie **Administration**, sélectionnez **Sauvegarde d’infrastructure**. Choisissez **Configuration** dans le panneau **Sauvegarde d’infrastructure**.

3. Recherchez le **nom** et la **date d’exécution** de la sauvegarde dans la liste des **sauvegardes disponibles**.

4. Vérifiez que l’**état** indique une **réussite**.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les [meilleures pratiques en matière de sauvegarde d’infrastructure](azure-stack-backup-best-practices-tzl.md).
