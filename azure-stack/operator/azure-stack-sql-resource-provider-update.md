---
title: Mettre à jour le fournisseur de ressources SQL Azure Stack Hub
titleSuffix: Azure Stack Hub
description: Découvrez comment mettre à jour le fournisseur de ressources SQL Azure Stack Hub.
author: bryanla
ms.topic: article
ms.date: 11/11/2019
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 43099dfb4bfb2ffe50e6c8ccdfc05633ff4f3a6e
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77697159"
---
# <a name="update-the-sql-resource-provider"></a>Mettre à jour le fournisseur de ressources SQL

Un nouveau fournisseur de ressources SQL peut être publié lorsque le build Azure Stack Hub est mis à jour. Même si le fournisseur de ressources existant continue de fonctionner, nous vous recommandons d’effectuer une mise à jour dès que possible vers le build le plus récent.

À compter de la publication de la version 1.1.33.0 du fournisseur de ressources SQL, les mises à jour sont cumulatives et ne doivent pas être installées dans l’ordre où elles ont été publiées dès lors que vous démarrez par la version 1.1.24.0 ou ultérieure. Par exemple, si vous exécutez la version 1.1.24.0 du fournisseur de ressources SQL, vous pouvez mettre à niveau vers la version 1.1.33.0 ou ultérieure sans devoir d’abord installer la version 1.1.30.0. Pour passer en revue les versions disponibles du fournisseur de ressources et la version d’Azure Stack Hub sur laquelle elles sont prises en charge, consultez la liste des versions dans [Déployer les prérequis du fournisseur de ressources](./azure-stack-sql-resource-provider-deploy.md#prerequisites).

Pour mettre à jour le fournisseur de ressources, utilisez le script *UpdateSQLProvider.ps1*. Vous devez utiliser votre compte de service avec des droits d’administrateur local et être **propriétaire** de l’abonnement. Le script est inclus avec le téléchargement du nouveau fournisseur de ressources SQL. Le processus de mise à jour est similaire au processus utilisé pour [déployer le fournisseur de ressources](./azure-stack-sql-resource-provider-deploy.md). Le script de mise à jour utilise les mêmes arguments que le script DeploySqlProvider.ps1. Vous devrez fournir les informations de certificat.

 > [!IMPORTANT]
 > Avant de mettre à niveau le fournisseur de ressources, passez en revue les notes de publication pour en savoir plus sur les nouvelles fonctionnalités, les correctifs et les problèmes connus qui pourraient affecter votre déploiement.

## <a name="update-script-processes"></a>Processus du script de mise à jour

Le script *UpdateSQLProvider.ps1* crée une machine virtuelle (VM) avec le tout dernier code de fournisseur de ressources.

> [!NOTE]
> Nous vous conseillons de télécharger la dernière image Windows Server 2016 Core à partir de la Gestion de la Place de marché. Si vous devez installer une mise à jour, vous pouvez placer un **seul** package MSU dans le chemin de dépendance local. Le script échoue s’il existe plusieurs fichiers MSU à cet emplacement.

Une fois qu’il a créé une machine virtuelle, le script *UpdateSQLProvider.ps1* migre les paramètres suivants depuis la machine virtuelle de l’ancien fournisseur :

* les informations de base de données
* les informations sur le serveur d’hébergement
* l’enregistrement DNS requis

## <a name="update-script-parameters"></a>Paramètres du script de mise à jour

Vous pouvez spécifier les paramètres suivants à partir de la ligne de commande quand vous exécutez le script PowerShell **UpdateSQLProvider.ps1**. Si vous ne le faites pas, ou si la validation d’un paramètre échoue, vous êtes invité à fournir les paramètres requis.

| Nom du paramètre | Description | Commentaire ou valeur par défaut |
| --- | --- | --- |
| **CloudAdminCredential** | Informations d’identification de l’administrateur du cloud, nécessaires pour accéder au point de terminaison privilégié. | _Obligatoire_ |
| **AzCredential** | Informations d'identification du compte administrateur de service Azure Stack Hub. Utilisez les mêmes informations d'identification que celles utilisées pour le déploiement d'Azure Stack Hub. | _Obligatoire_ |
| **VMLocalCredential** | Informations d’identification du compte d’administrateur local de la machine virtuelle du fournisseur de ressources SQL. | _Obligatoire_ |
| **PrivilegedEndpoint** | Adresse IP ou nom DNS du point de terminaison privilégié. |  _Obligatoire_ |
| **AzureEnvironment** | L’environnement Azure du compte administrateur de service que vous avez utilisé pour déployer Azure Stack Hub. Nécessaire uniquement pour les déploiements Azure AD. Les noms d’environnement pris en charge sont **AzureCloud**, **AzureUSGovernment**, ou, si vous utilisez un compte Azure AD en Chine, **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | Vous devez également placer le fichier de certificat .pfx dans ce répertoire. | _Facultatif pour un seul nœud, mais obligatoire pour plusieurs nœuds._ |
| **DefaultSSLCertificatePassword** | Mot de passe pour le certificat .pfx. | _Obligatoire_ |
| **MaxRetryCount** | Nombre de fois où vous souhaitez réessayer chaque opération en cas d’échec.| 2 |
| **RetryDuration** |Délai d’attente entre les tentatives, en secondes. | 120 |
| **Désinstaller** | Supprime le fournisseur de ressources et toutes les ressources associées. | Non |
| **DebugMode** | Empêche le nettoyage automatique en cas d’échec. | Non |

## <a name="update-script-powershell-example"></a>Exemple de script de mise à jour PowerShell
> [!NOTE]
> Ce processus de mise à jour s’applique uniquement aux systèmes Azure Stack Hub intégrés.

Si vous mettez à jour le fournisseur de ressources SQL vers la version 1.1.33.0 ou une version antérieure, vous devez installer des versions spécifiques des modules Azure Stack Hub et AzureRm.Bootstrapper dans PowerShell. Si vous mettez à jour vers le fournisseur de ressources SQL version 1.1.47.0, le script de déploiement télécharge et installe automatiquement les modules PowerShell nécessaires dans le chemin C:\Program Files\SqlMySqlPsh.

```powershell
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureStack module.
# Note that this might not be the most currently available version of Azure Stack Hub PowerShell.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0
```

> [!NOTE]
> Dans un scénario déconnecté, vous devez télécharger les modules PowerShell requis et inscrire manuellement le référentiel en tant que condition préalable. Pour plus d’informations, voir [Déployer un fournisseur de ressources SQL](azure-stack-sql-resource-provider-deploy.md)

Voici un exemple d’utilisation du script *UpdateSQLProvider.ps1* que vous pouvez exécuter à partir d’une console PowerShell avec privilèges élevés. Veillez à changer les informations des variables et les mots de passe selon vos besoins :  

```powershell
# Use the NetBIOS name for the Azure Stack Hub domain. On the Azure Stack Hub SDK, the default is AzureStack but this might have been changed at installation.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS VMs.
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack Hub. Required only for Azure AD deployments. Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud, or AzureUSGovernment depending which Azure subscription you're using.
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (this can be Azure AD or AD FS).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# Add the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# For version 1.1.47.0, the PowerShell modules used by the RP deployment are placed in C:\Program Files\SqlMySqlPsh
# The deployment script adds this path to the system $env:PSModulePath to ensure correct modules are used.
$rpModulePath = Join-Path -Path $env:ProgramFiles -ChildPath 'SqlMySqlPsh'
$env:PSModulePath = $env:PSModulePath + ";" + $rpModulePath

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\UpdateSQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -AzureEnvironment $AzureEnvironment `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert

 ```

Une fois l’exécution du script de mise à jour du fournisseur de ressources terminée, fermez la session PowerShell active.

## <a name="next-steps"></a>Étapes suivantes

[Tenir à jour le fournisseur de ressources SQL](azure-stack-sql-resource-provider-maintain.md)
