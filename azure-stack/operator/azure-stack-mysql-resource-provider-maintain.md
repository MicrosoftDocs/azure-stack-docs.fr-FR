---
title: Gestion du fournisseur de ressources MySQL sur Azure Stack | Microsoft Docs
description: Découvrez comment vous pouvez gérer le service de fournisseur de ressources MySQL sur Azure Stack.
services: azure-stack
documentationCenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/06/2019
ms.author: mabrigg
ms.reviewer: jiahan
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 24e1e315a9b52693cb2b9ff26bf424ee8e9d4f3f
ms.sourcegitcommit: ccd86bd0862c45de1f6a4993f783ea2e186c187a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65172309"
---
# <a name="mysql-resource-provider-maintenance-operations"></a>Opérations de maintenance du fournisseur de ressources MySQL

Le fournisseur de ressources MySQL s’exécute sur une machine virtuelle verrouillée. Pour activer les opérations de maintenance, vous devez mettre à jour la sécurité de la machine virtuelle. Si vous souhaitez utiliser le principe de séparation des privilèges à cet effet, vous pouvez utiliser le point de terminaison PowerShell Just Enough Administration (JEA) DBAdapterMaintenance. Le package d’installation du fournisseur de ressources inclut un script pour cette opération.

## <a name="update-the-virtual-machine-operating-system"></a>Mettre à jour le système d’exploitation de la machine virtuelle

Étant donné que le fournisseur de ressources s’exécute sur une machine virtuelle de l’*utilisateur*, vous devez appliquer les correctifs et mises à jour lors de leur publication. Vous pouvez utiliser les mises à jour Windows fournies dans le cadre du cycle de correction et de mise à jour pour appliquer les mises à jour à la machine virtuelle Windows.

Mettez à jour la machine virtuelle du fournisseur à l’aide de l’une des méthodes suivantes :

- Installer le package de fournisseur de ressources le plus récent à l’aide d’une image corrigée de Windows Server 2016 Core.
- Installer un package Windows Update pendant l’installation ou la mise à jour du fournisseur de ressources.

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>Mettre à jour les définitions Windows Defender de machine virtuelle

Pour mettre à jour les définitions Defender, procédez comme suit :

1. Télécharger la mise à jour des définitions Windows Defender à partir de [Définitions Windows Defender](https://www.microsoft.com/en-us/wdsi/definitions).

    Sur la page des définitions, accédez à « Télécharger et installer les définitions manuellement ». Téléchargez le fichier « Antivirus Windows Defender pour Windows 10 et Windows 8.1 » 64 bits.

    Vous pouvez également utiliser [ce lien direct](https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64) pour télécharger/exécuter le fichier fpam-fe.exe.

2. Ouvrez une session PowerShell sur le point de terminaison de maintenance de la machine virtuelle de l’adaptateur de fournisseur de ressources MySQL.

3. Copiez le fichier de mise à jour des définitions pour la machine virtuelle de l’adaptateur de fournisseur de ressources à l’aide de la session du point de terminaison de maintenance.

4. Sur la session PowerShell de maintenance, appeler la commande _Update-DBAdapterWindowsDefenderDefinitions_.

5. Après avoir installé les définitions, nous vous recommandons de supprimer le fichier de mise à jour de définitions à l’aide de la commande _Remove-ItemOnUserDrive)_.

**Exemple de script PowerShell pour mettre à jour les définitions.**

Vous pouvez modifier et exécuter le script suivant pour mettre à jour les définitions Defender. Remplacez les valeurs dans le script avec les valeurs de votre environnement.

```powershell
# Set credentials for the local admin on the resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "<local admin user password>" -AsPlainText -Force
$vmLocalAdminUser = "<local admin user name>"
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential `
    ($vmLocalAdminUser, $vmLocalAdminPass)

# Provide the public IP address for the adapter VM.
$databaseRPMachine  = "<RP VM IP address>"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"

# Download Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions.  
Invoke-WebRequest -Uri 'https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64' `
    -Outfile $localPathToDefenderUpdate  

# Create a session to the maintenance endpoint.
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $vmLocalAdminCreds -ConfigurationName DBAdapterMaintenance

# Copy the defender update file to the adapter virtual machine.
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\"

# Install the update definitions.
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinition -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}

# Cleanup the definitions package file and session.
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession

```

## <a name="secrets-rotation"></a>Rotation des secrets

*Ces instructions s’appliquent uniquement aux systèmes intégrés Azure Stack*.

Quand vous utilisez les fournisseurs de ressources SQL et MySQL avec des systèmes intégrés Azure Stack, l’opérateur Azure Stack est chargé de faire pivoter les secrets d’infrastructure de fournisseurs de ressources suivants pour garantir qu’ils n’expirent pas :

- Certificat SSL externe [fourni au cours du déploiement](azure-stack-pki-certs.md).
- Mot de passe de compte d’administrateur local des machines virtuelles du fournisseur de ressources fourni au moment du déploiement.
- Mot de passe (dbadapterdiag) utilisateur de diagnostic du fournisseur de ressources.

### <a name="powershell-examples-for-rotating-secrets"></a>Exemples PowerShell pour la rotation des secrets

**Changer tous les secrets en même temps.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DiagnosticsUserPassword $passwd `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd `  
    -VMLocalCredential $localCreds

```

**Changer le mot de passe utilisateur de diagnostic.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DiagnosticsUserPassword  $passwd

```

**Changer le mot de passe de compte administrateur local des machines virtuelles.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -VMLocalCredential $localCreds

```

**Changer le mot de passe du certificat SSL.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd

```

### <a name="secretrotationmysqlproviderps1-parameters"></a>SecretRotationMySQLProvider.ps1 parameters

|Paramètre|Description|
|-----|-----|
|AzCredential|Informations d’identification du compte d’administration de service Azure Stack.|
|CloudAdminCredential|Informations d’identification du compte de domaine d’administrateur cloud d’Azure Stack.|
|PrivilegedEndpoint|Point de terminaison privilégié pour accéder à Get-AzureStackStampInformation.|
|DiagnosticsUserPassword|Diagnostique le mot de passe de compte d’utilisateur.|
|VMLocalCredential|Le compte administrateur local de la machine virtuelle MySQLAdapter.|
|DefaultSSLCertificatePassword|Mot de passe du certificat SSL par défaut (*.pfx)|
|DependencyFilesLocalPath|Chemin local des fichiers de dépendances.|
|     |     |

### <a name="known-issues"></a>Problèmes connus

**Problème :**<br>
Les journaux d’activité de la rotation des secrets ne sont pas automatiquement collectés en cas d’échec de l’exécution du script de la rotation des secrets.

**Solution de contournement :**<br>
Utilisez le cmdlet Get-AzsDBAdapterLogs pour collecter tous les journaux d’activité du fournisseur de ressources, notamment AzureStack.DatabaseAdapter.SecretRotation.ps1_*.log, enregistré sous C:\Logs.

## <a name="collect-diagnostic-logs"></a>Collecter des journaux de diagnostic

S’il s’avère nécessaire de collecter des journaux d’activité à partir de la machine virtuelle verrouillée, un point de terminaison PowerShell JEA (Just Enough Administration) DBAdapterDiagnostics est fourni à cette fin. Ce point de terminaison propose les commandes suivantes :

- **Get-AzsDBAdapterLog**. Cette commande crée un package zip des journaux de diagnostic du fournisseur de ressources et enregistre le fichier sur le lecteur de l’utilisateur de la session. Vous pouvez exécuter cette commande sans aucun paramètre : les quatre dernières heures de journaux d’activité seront collectées.

- **Remove-AzsDBAdapterLog**. Cette commande nettoie les packages de journaux existants sur la machine virtuelle du fournisseur de ressources.

### <a name="endpoint-requirements-and-process"></a>Processus et exigences du point de terminaison

Lorsqu’un fournisseur de ressources est installé ou mis à jour, le compte d’utilisateur dbadapterdiag est créé. Ce compte vous permet de collecter les journaux de diagnostic.

>[!NOTE]
>Le mot de passe du compte dbadapterdiag est identique au mot de passe utilisé pour l’administrateur local sur la machine virtuelle créée au cours d’un déploiement ou d’une mise à jour de fournisseur.

Pour utiliser les commandes _DBAdapterDiagnostics_, créez une session PowerShell à distance sur la machine virtuelle du fournisseur de ressources, puis appelez la commande **Get-AzsDBAdapterLog**.

Vous définirez l’intervalle de temps pour la collecte de journaux à l’aide des paramètres **FromDate** et **ToDate**. Si vous ne spécifiez pas l’un de ces paramètres ou les deux, les valeurs par défaut suivantes sont utilisées :

* FromDate correspond à quatre heures avant l’heure actuelle.
* ToDate renvoie l’heure en cours.

**Exemple de script PowerShell pour collecter les journaux d’activité.**

Le script suivant montre comment collecter les journaux de diagnostic à partir de la machine virtuelle du fournisseur de ressources.

```powershell
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP address>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<Enter Diagnostic password>'
$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, (ConvertTo-SecureString -String $diagnosticsUserPassword -AsPlainText -Force))
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds
        -ConfigurationName DBAdapterDiagnostics

# Sample that captures logs from the previous hour.
$fromDate = (Get-Date).AddHours(-1)
$dateNow = Get-Date
$sb = {param($d1,$d2) Get-AzSDBAdapterLog -FromDate $d1 -ToDate $d2}
$logs = Invoke-Command -Session $session -ScriptBlock $sb -ArgumentList $fromDate,$dateNow

# Copy the logs to the user drive.
$sourcePath = "User:\{0}" -f $logs
$destinationPackage = Join-Path -Path (Convert-Path '.') -ChildPath $logs
Copy-Item -FromSession $session -Path $sourcePath -Destination $destinationPackage

# Cleanup the logs.
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove-AzsDBAdapterLog}
# Close the session.
$session | Remove-PSSession

```

## <a name="next-steps"></a>Étapes suivantes

[Supprimer le fournisseur de ressources MySQL](azure-stack-mysql-resource-provider-remove.md)
