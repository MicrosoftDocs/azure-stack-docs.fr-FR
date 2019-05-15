---
title: Sauvegarde d’Azure Stack | Microsoft Docs
description: Effectuez une sauvegarde à la demande sur Azure Stack avec la sauvegarde en place.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 9565DDFB-2CDB-40CD-8964-697DA2FFF70A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: mabrigg
ms.reviewer: hectorl
ms.lastreviewed: 09/05/2018
ms.openlocfilehash: dc2805b868475a181a0eb1902d0e238e14193cd8
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618783"
---
# <a name="back-up-azure-stack"></a>Sauvegarde d’Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Effectuez une sauvegarde à la demande sur Azure Stack. Pour obtenir des instructions sur la configuration de l’environnement PowerShell, consultez [Installer PowerShell pour Azure Stack](azure-stack-powershell-install.md). Pour vous connecter à Azure Stack, consultez [Utilisation du portail administrateur dans Azure Stack](azure-stack-manage-portals.md).

## <a name="start-azure-stack-backup"></a>Démarrer la sauvegarde d’Azure Stack

### <a name="start-a-new-backup-without-job-progress-tracking"></a>Démarrer une nouvelle sauvegarde sans suivi de la progression du travail
Utilisez Start-AzSBackup pour démarrer une nouvelle sauvegarde immédiatement sans aucun suivi de la progression du travail.

```powershell
   Start-AzsBackup -Force
```

### <a name="start-azure-stack-backup-with-job-progress-tracking"></a>Démarrer une sauvegarde Azure Stack avec suivi de la progression du travail
Utilisez Start-AzSBackup pour démarrer une nouvelle sauvegarde avec le paramètre **-AsJob** et l’enregistrer comme variable afin de suivre la progression du travail de sauvegarde.

> [!NOTE]
> Le travail de sauvegarde s’affiche comme correctement effectué dans le portail environ 10 à 15 minutes avant la fin du travail.
>
> Il est donc préférable d’observer l’état réel dans le code ci-dessous.

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
Utilisez les commandes PowerShell suivantes pour vérifier que la sauvegarde s’est terminée avec succès :

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

### <a name="confirm-backup-has-completed-in-the-administration-portal"></a>Confirmer la sauvegarde dans le portail d’administration
Utilisez le portail d’administration Azure Stack pour vérifier que la sauvegarde s’est terminée avec succès en suivant ces étapes :

1. Ouvrez le [portail d’administration Azure Stack](azure-stack-manage-portals.md).
2. Sélectionnez **Tous les services** et, sous la catégorie **ADMINISTRATION**, sélectionnez > **Sauvegarde d’infrastructure**. Choisissez **Configuration** dans le panneau **Sauvegarde d’infrastructure**.
3. Recherchez le **nom** et la **date d’exécution** de la sauvegarde dans la liste des **sauvegardes disponibles**.
4. Vérifiez que l’**état** indique une **réussite**.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le flux de travail de [récupération à partir d'un événement de perte de données](azure-stack-backup-recover-data.md).
