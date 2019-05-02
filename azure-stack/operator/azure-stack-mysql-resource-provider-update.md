---
title: Mise à jour du fournisseur de ressources mySQL Azure Stack | Microsoft Docs
description: Découvrez comment vous pouvez mettre à jour le fournisseur de ressources mySQL Azure Stack.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2019
ms.author: jeffgilb
ms.reviewer: jiahan
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 976c05449704b0ecbc48ee5bd4799f300fcac0aa
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "64308255"
---
# <a name="update-the-mysql-resource-provider"></a>Mettre à jour le fournisseur de ressources MySQL 

*S’applique à : systèmes intégrés Azure Stack.*

Un nouvel adaptateur de fournisseur de ressources MySQL peut être publié quand des builds Azure Stack sont mises à jour. Même si l’adaptateur existant continue de fonctionner, nous vous recommandons d’effectuer une mise à jour dès que possible vers la build la plus récente. 

À compter de la publication de la version 1.1.33.0 du fournisseur de ressources MySQL, les mises à jour sont cumulatives et ne doivent pas être installées dans l’ordre où elles ont été publiées dès lors que vous démarrez par la version 1.1.24.0 ou ultérieure. Par exemple, si vous exécutez la version 1.1.24.0 du fournisseur de ressources MySQL, vous pouvez mettre à niveau vers la version 1.1.33.0 ou ultérieure sans devoir d’abord installer la version 1.1.30.0. Pour passer en revue les versions disponibles du fournisseur de ressources et la version d’Azure Stack sur laquelle elles sont prises en charge, reportez-vous à la liste des versions dans [Déployer les prérequis du fournisseur de ressources](./azure-stack-mysql-resource-provider-deploy.md#prerequisites).

Pour mettre à jour le fournisseur de ressources, vous utilisez le script **UpdateMySQLProvider.ps1**. Le processus est semblable au processus utilisé pour installer un fournisseur de ressources, comme décrit dans la section Déployer le fournisseur de ressources de cet article. Le script est inclus avec le téléchargement du fournisseur de ressources. 

 > [!IMPORTANT]
 > Avant de mettre à niveau le fournisseur de ressources, passez en revue les notes de publication pour en savoir plus sur les nouvelles fonctionnalités, les correctifs et les problèmes connus qui pourraient affecter votre déploiement.

## <a name="update-script-processes"></a>Processus du script de mise à jour

Le script **UpdateMySQLProvider.ps1** crée une machine virtuelle avec le dernier code de fournisseur de ressources et migre les paramètres de l’ancienne machine virtuelle vers la nouvelle. Les paramètres qui migrent incluent des informations sur la base de données et le serveur d’hébergement, ainsi que l’enregistrement DNS nécessaire. 

>[!NOTE]
>Nous vous conseillons de télécharger la dernière image Windows Server 2016 Core à partir de la Gestion de la Place de marché. Si vous devez installer une mise à jour, vous pouvez placer un **seul** package MSU dans le chemin de dépendance local. Le script échoue s’il existe plusieurs fichiers MSU à cet emplacement.

Le script exige d’utiliser les mêmes arguments que ceux décrits pour le script DeployMySqlProvider.ps1. Fournissez également le certificat ici.  


## <a name="update-script-parameters"></a>Paramètres du script de mise à jour 
Vous pouvez spécifier les paramètres suivants à partir de la ligne de commande quand vous exécutez le script PowerShell **UpdateMySQLProvider.ps1**. Si vous ne le faites pas, ou si la validation d’un paramètre échoue, vous êtes invité à fournir les paramètres requis. 

| Nom du paramètre | Description | Commentaire ou valeur par défaut | 
| --- | --- | --- | 
| **CloudAdminCredential** | Informations d’identification de l’administrateur du cloud, nécessaires pour accéder au point de terminaison privilégié. | _Obligatoire_ | 
| **AzCredential** | Informations d’identification du compte d’administration de service Azure Stack. Utilisez les mêmes informations d’identification que celles utilisées pour le déploiement d’Azure Stack. | _Obligatoire_ | 
| **VMLocalCredential** |Informations d’identification du compte d’administrateur local de la machine virtuelle du fournisseur de ressources SQL. | _Obligatoire_ | 
| **PrivilegedEndpoint** | Adresse IP ou nom DNS du point de terminaison privilégié. |  _Obligatoire_ | 
| **AzureEnvironment** | L’environnement Azure du compte administrateur de service que vous avez utilisé pour déployer Azure Stack. Nécessaire uniquement pour les déploiements Azure AD. Les noms d’environnement pris en charge sont **AzureCloud**, **AzureUSGovernment**, ou, si vous utilisez un compte Azure AD en Chine, **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | Votre fichier de certificat .pfx doit également être placé dans ce répertoire. | _Facultatif_ (_obligatoire_ pour les nœuds multiples) | 
| **DefaultSSLCertificatePassword** | Mot de passe pour le certificat .pfx. | _Obligatoire_ | 
| **MaxRetryCount** | Nombre de fois où vous souhaitez réessayer chaque opération en cas d’échec.| 2 | 
| **RetryDuration** | Délai d’attente entre les tentatives, en secondes. | 120 | 
| **Désinstaller** | Supprimez le fournisseur de ressources et toutes les ressources associées (voir les remarques ci-dessous). | Non  | 
| **DebugMode** | Empêche le nettoyage automatique en cas d’échec. | Non  | 
| **AcceptLicense** | Ignore l’invite à accepter la licence GPL.  (https://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | | 

## <a name="update-script-example"></a>Exemple de script de mise à jour
Voici un exemple d’utilisation du script *UpdateMySQLProvider.ps1* que vous pouvez exécuter à partir d’une console PowerShell avec privilèges élevés. Veillez à changer les informations des variables et les mots de passe selon vos besoins :  

> [!NOTE] 
> Le processus de mise à jour s’applique uniquement aux systèmes intégrés. 

```powershell 
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
# Note that this might not be the most currently available version of Azure Stack PowerShell.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time. 
$domain = "AzureStack" 

# For integrated systems, use the IP address of one of the ERCS virtual machines 
$privilegedEndpoint = "AzS-ERCS01" 

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported environment names are AzureCloud, AzureUSGovernment, or AzureChinaCloud. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted. 
$tempDir = 'C:\TEMP\MYSQLRP' 

# The service admin account (can be Azure Active Directory or Active Directory Federation Services). 
$serviceAdmin = "admin@mydomain.onmicrosoft.com" 
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass) 
 
# Set credentials for the new resource provider VM. 
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass) 
 
# And the cloudadmin credential required for privileged endpoint access. 
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass) 

# Change the following as appropriate. 
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
 
# Change directory to the folder where you extracted the installation files. 
# Then adjust the endpoints. 
$tempDir\UpdateMySQLProvider.ps1 -AzCredential $AdminCreds ` 
-VMLocalCredential $vmLocalAdminCreds ` 
-CloudAdminCredential $cloudAdminCreds ` 
-PrivilegedEndpoint $privilegedEndpoint ` 
-AzureEnvironment $AzureEnvironment `
-DefaultSSLCertificatePassword $PfxPass ` 
-DependencyFilesLocalPath $tempDir\cert ` 
-AcceptLicense 
```  

## <a name="next-steps"></a>Étapes suivantes
[Tenir à jour le fournisseur de ressources MySQL](azure-stack-mysql-resource-provider-maintain.md)
