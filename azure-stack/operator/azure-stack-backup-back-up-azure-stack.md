---
title: Sauvegarder Azure Stack Hub
description: Découvrez comment effectuer une sauvegarde à la demande sur Azure Stack Hub.
author: PatAltimore
ms.topic: article
ms.date: 02/12/2019
ms.author: patricka
ms.reviewer: hectorl
ms.lastreviewed: 09/05/2019
ms.openlocfilehash: 5df894d32d335b488ad51b09bb38c3011f754e15
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97871835"
---
# <a name="back-up-azure-stack-hub"></a>Sauvegarder Azure Stack Hub

Cet article explique comment effectuer une sauvegarde à la demande sur Azure Stack Hub. Pour obtenir des instructions sur la configuration de l’environnement PowerShell, voir [Installer PowerShell pour Azure Stack Hub](powershell-install-az-module.md). Pour vous connecter à Azure Stack Hub, voir [Utilisation du portail administrateur dans Azure Stack Hub](azure-stack-manage-portals.md).

## <a name="start-azure-stack-hub-backup"></a>Démarrer une sauvegarde d’Azure Stack Hub

### <a name="start-a-new-backup-without-job-progress-tracking"></a>Démarrer une nouvelle sauvegarde sans suivi de la progression du travail
Utilisez Start-AzSBackup pour démarrer une nouvelle sauvegarde immédiatement sans aucun suivi de la progression du travail.

```powershell
   Start-AzsBackup -Force
```

### <a name="start-azure-stack-hub-backup-with-job-progress-tracking"></a>Démarrer une sauvegarde d’Azure Stack Hub avec suivi de la progression du travail
Utilisez Start-AzSBackup pour démarrer une nouvelle sauvegarde avec le paramètre **-AsJob** et l’enregistrer comme variable afin de suivre la progression du travail de sauvegarde.

> [!NOTE]
> Le travail de sauvegarde s’affiche comme étant terminé dans le portail environ 10 à 15 minutes avant la fin de son exécution.
>
> Il est préférable d’observer l’état réel dans le code ci-dessous.

> [!IMPORTANT]
> Le délai initial d’une (1) milliseconde est ajouté, car le code s’exécute trop rapidement pour pouvoir inscrire le travail correctement, et il ne retourne pas de valeur **PSBeginTime** et donc, pas de valeur **État** du travail.

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
        $Pattern = '^P?T?((?<Years>\d+)Y)?((?<Months>\d+)M)?((?<Weeks>\d+)W)?((?<Days>\d+)D)?(T((?<Hours>\d+)H)?((?<Minutes>\d+)M)?((?<Seconds>\d*(\.)?\d*)S)?)$'
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

```powershell
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
Dans le portail administrateur Azure Stack Hub, vérifiez que la sauvegarde a abouti en procédant comme suit :

1. Ouvrez le [portail administrateur Azure Stack Hub](azure-stack-manage-portals.md).
2. Sélectionnez **Tous les services** et, sous la catégorie **ADMINISTRATION**, sélectionnez > **Sauvegarde d’infrastructure**. Choisissez **Configuration** dans le panneau **Sauvegarde d’infrastructure**.
3. Recherchez le **nom** et la **date d’exécution** de la sauvegarde dans la liste des **sauvegardes disponibles**.
4. Vérifiez que l’**état** indique une **réussite**.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le flux de travail de [récupération à partir d'un événement de perte de données](azure-stack-backup-recover-data.md).
