---
title: Mettre à jour le fournisseur de ressources MySQL dans Azure Stack Hub
description: Découvrez comment mettre à jour le fournisseur de ressources MySQL dans Azure Stack Hub.
author: bryanla
ms.topic: article
ms.date: 9/22/2020
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 01/11/2020
ms.openlocfilehash: 61149b7eb89908b641fd995b10b22e3d05a5fb8d
ms.sourcegitcommit: af4374755cb4875a7cbed405b821f5703fa1c8cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95812698"
---
# <a name="update-the-mysql-resource-provider-in-azure-stack-hub"></a>Mettre à jour le fournisseur de ressources MySQL dans Azure Stack Hub

> [!IMPORTANT]
> Avant de mettre à jour le fournisseur de ressources, passez en revue les notes de publication pour en savoir plus sur les nouvelles fonctionnalités, les correctifs et les problèmes connus qui pourraient affecter votre déploiement. Les notes de publication spécifient également la version minimale d'Azure Stack Hub requise par le fournisseur de ressources.

Un nouvel adaptateur de fournisseur de ressources MySQL peut être publié lors de la mise à jour de builds Azure Stack Hub. Même si l’adaptateur existant continue de fonctionner, nous vous recommandons d’effectuer une mise à jour dès que possible vers la build la plus récente.

  |Versions d’Azure Stack Hub prises en charge|Version du fournisseur de ressources MySQL|Windows Server sur lequel le service RP s’exécute
  |-----|-----|-----|
  |2008, 2005|[MySQL RP version 1.1.93.0](https://aka.ms/azshmysqlrp11930)|Microsoft AzureStack Add-On RP Windows Server INTERNE UNIQUEMENT
  |2005, 2002, 1910|[MySQL RP version 1.1.47.0](https://aka.ms/azurestackmysqlrp11470)|Windows Server 2016 Datacenter - Server Core|
  |1908|[MySQL RP version 1.1.33.0](https://aka.ms/azurestackmysqlrp11330)|Windows Server 2016 Datacenter - Server Core|
  |     |     |     |

La mise à jour du fournisseur de ressources MySQL est cumulative. Lors de la mise à jour à partir d’une ancienne version, vous pouvez effectuer directement la mise à jour vers la version la plus récente. 

Pour mettre à jour le fournisseur de ressources, vous utilisez le script **UpdateMySQLProvider.ps1**. Vous devez utiliser votre compte de service avec des droits d’administrateur local et être **propriétaire** de l’abonnement. Le script de mise à jour est inclus avec le téléchargement du fournisseur de ressources. 

Le processus de mise à jour est similaire au processus utilisé pour [déployer le fournisseur de ressources](./azure-stack-mysql-resource-provider-deploy.md). Le script de mise à jour utilise les mêmes arguments que le script DeployMySqlProvider.ps1. Vous devrez fournir les informations de certificat.

## <a name="update-script-processes"></a>Processus du script de mise à jour

Le script **UpdateMySQLProvider.ps1** crée une machine virtuelle avec la dernière image du système d’exploitation, déploie le dernier code de fournisseur de ressources et migre les paramètres de l’ancien fournisseur de ressources vers le nouveau.

>[!NOTE]
>Nous vous conseillons de télécharger la dernière image de Windows Server 2016 Core ou de Microsoft AzureStack Add-on RP Windows Server à partir de la Gestion de la Place de marché. Si vous devez installer une mise à jour, vous pouvez placer un **seul** package MSU dans le chemin de dépendance local. Le script échoue s’il existe plusieurs fichiers MSU à cet emplacement.

Une fois que le script *UpdateMySQLProvider.ps1* a créé une machine virtuelle, il migre les paramètres suivants depuis la machine virtuelle de l’ancien fournisseur de ressources :

* les informations de base de données
* les informations sur le serveur d’hébergement
* l’enregistrement DNS requis

## <a name="update-script-parameters"></a>Paramètres du script de mise à jour 
Spécifiez les paramètres suivants à partir de la ligne de commande quand vous exécutez le script PowerShell **UpdateMySQLProvider.ps1**. Si vous ne le faites pas, ou si la validation d’un paramètre échoue, vous êtes invité à fournir les paramètres requis.

| Nom du paramètre | Description | Commentaire ou valeur par défaut | 
| --- | --- | --- | 
| **CloudAdminCredential** | Informations d’identification de l’administrateur du cloud, nécessaires pour accéder au point de terminaison privilégié. | _Obligatoire_ | 
| **AzCredential** | Informations d'identification du compte administrateur de service Azure Stack Hub. Utilisez les mêmes informations d'identification que celles utilisées pour le déploiement d'Azure Stack Hub. Le script échoue si le compte que vous utilisez avec AzCredential nécessite l’authentification multifacteur (MFA). | _Obligatoire_ | 
| **VMLocalCredential** |Informations d’identification du compte d’administrateur local de la machine virtuelle du fournisseur de ressources SQL. | _Obligatoire_ | 
| **PrivilegedEndpoint** | Adresse IP ou nom DNS du point de terminaison privilégié. |  _Obligatoire_ | 
| **AzureEnvironment** | Environnement Azure du compte administrateur de service utilisé pour déployer Azure Stack Hub. Nécessaire uniquement pour les déploiements Azure AD. Les noms d’environnement pris en charge sont **AzureCloud**, **AzureUSGovernment**, ou, si vous utilisez un compte Azure AD en Chine, **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | Votre fichier de certificat .pfx doit également être placé dans ce répertoire. | _Facultatif_ (_obligatoire_ pour les nœuds multiples) | 
| **DefaultSSLCertificatePassword** | Mot de passe pour le certificat .pfx. | _Obligatoire_ | 
| **MaxRetryCount** | Nombre de fois où vous souhaitez réessayer chaque opération en cas d’échec.| 2 | 
| **RetryDuration** | Délai d’attente entre les tentatives, en secondes. | 120 | 
| **Désinstaller** | Supprimez le fournisseur de ressources et toutes les ressources associées (voir les remarques ci-dessous). | Non | 
| **DebugMode** | Empêche le nettoyage automatique en cas d’échec. | Non | 
| **AcceptLicense** | Ignore l’invite à accepter la licence GPL.  (https://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | | 

## <a name="update-script-example"></a>Exemple de script de mise à jour

Si vous mettez à jour le fournisseur de ressources MySQL vers la version 1.1.33.0 ou une version antérieure, vous devez installer des versions spécifiques des modules Azure Stack Hub et AzureRm.Bootstrapper dans PowerShell. 

Si vous mettez à jour le fournisseur de ressources MySQL vers la version 1.1.47.0 ou ultérieure, vous pouvez ignorer cette étape. Le script de déploiement télécharge et installe automatiquement les modules PowerShell nécessaires sous C:\Program Files\SqlMySqlPsh. 

>[!NOTE]
>Si le dossier C:\Program Files\SqlMySqlPsh existe déjà avec le module PowerShell téléchargé, il est recommandé de nettoyer ce dossier avant d’exécuter le script de mise à jour. Cela permet de s’assurer que la version appropriée du module PowerShell est téléchargée et utilisée.

```powershell 
# Run the following scripts when updating to version 1.1.33.0 only.
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module.
# Note that this might not be the most currently available version of Azure Stack Hub PowerShell.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0
```

> [!NOTE]
> Dans un scénario déconnecté, vous devez télécharger les modules PowerShell requis et inscrire manuellement le référentiel en tant que condition préalable. Pour plus d’informations, voir [Déployer un fournisseur de ressources MySQL](azure-stack-mysql-resource-provider-deploy.md)

Voici un exemple d’utilisation du script *UpdateMySQLProvider.ps1* que vous pouvez exécuter à partir d’une console PowerShell avec privilèges élevés. Veillez à changer les informations des variables et les mots de passe selon vos besoins :

```powershell 
# Use the NetBIOS name for the Azure Stack Hub domain. On the Azure Stack Hub SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack" 

# For integrated systems, use the IP address of one of the ERCS VMs.
$privilegedEndpoint = "AzS-ERCS01" 

# Provide the Azure environment used for deploying Azure Stack Hub. Required only for Azure AD deployments. Supported environment names are AzureCloud, AzureUSGovernment, or AzureChinaCloud. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted. 
$tempDir = 'C:\TEMP\MYSQLRP' 

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com" 
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass) 
 
# Set credentials for the new resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass) 
 
# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass) 

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 

# For version 1.1.47.0 or later, the PowerShell modules used by the RP deployment are placed in C:\Program Files\SqlMySqlPsh
# The deployment script adds this path to the system $env:PSModulePath to ensure correct modules are used.
$rpModulePath = Join-Path -Path $env:ProgramFiles -ChildPath 'SqlMySqlPsh'
$env:PSModulePath = $env:PSModulePath + ";" + $rpModulePath 

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
.$tempDir\UpdateMySQLProvider.ps1 -AzCredential $AdminCreds `
-VMLocalCredential $vmLocalAdminCreds `
-CloudAdminCredential $cloudAdminCreds `
-PrivilegedEndpoint $privilegedEndpoint `
-AzureEnvironment $AzureEnvironment `
-DefaultSSLCertificatePassword $PfxPass `
-DependencyFilesLocalPath $tempDir\cert `
-AcceptLicense
```  

Une fois l’exécution du script de mise à jour du fournisseur de ressources terminée, fermez la session PowerShell active.

## <a name="next-steps"></a>Étapes suivantes
[Tenir à jour le fournisseur de ressources MySQL](azure-stack-mysql-resource-provider-maintain.md)
