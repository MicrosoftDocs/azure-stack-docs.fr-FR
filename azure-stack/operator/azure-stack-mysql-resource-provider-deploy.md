---
title: Déployer le fournisseur de ressources MySQL sur Azure Stack Hub
description: Découvrez comment déployer l’adaptateur du fournisseur de ressources MySQL et des bases de données MySQL en tant que service sur Azure Stack Hub.
author: bryanla
ms.topic: article
ms.date: 1/22/2020
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 03/18/2019
ms.openlocfilehash: 699c9a32c316e183c5432308ca26239533f71c98
ms.sourcegitcommit: 09fbc4e8fc53828647d515bfb556dfe42df28c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2020
ms.locfileid: "86419151"
---
# <a name="deploy-the-mysql-resource-provider-on-azure-stack-hub"></a>Déployer le fournisseur de ressources MySQL sur Azure Stack Hub

Utilisez le fournisseur de ressources MySQL Server pour exposer des bases de données MySQL en tant que service Azure Stack Hub. Le fournisseur de ressources MySQL s’exécute en tant que service sur une machine virtuelle Windows Server 2016 Server Core.

> [!IMPORTANT]
> Seul le fournisseur de ressources est pris en charge pour créer des éléments sur des serveurs qui hébergent SQL ou MySQL. Les éléments créés sur un serveur hôte qui ne sont pas créés par le fournisseur de ressources peuvent entraîner un état qui ne correspond pas.

## <a name="prerequisites"></a>Conditions préalables requises

Plusieurs conditions préalables doivent être remplies avant de pouvoir déployer le fournisseur de ressources MySQL Azure Stack Hub. Afin de répondre à ces exigences, complétez les étapes de cet article sur un ordinateur ayant accès à la machine virtuelle du point de terminaison privilégié.

* Si ce n'est déjà fait, [inscrivez Azure Stack Hub](./azure-stack-registration.md) auprès d'Azure pour pouvoir télécharger des éléments de la Place de marché Azure.

* Ajoutez la machine virtuelle Windows Server standard sur la Place de marché Azure Stack Hub en téléchargeant l'image de **Windows Server 2016 Datacenter Server Core**.

* Téléchargez le binaire du fournisseur de ressources MySQL, puis exécutez le fichier auto-extracteur pour extraire le contenu dans un répertoire temporaire.

  >[!NOTE]
  >Pour déployer le fournisseur MySQL sur un système qui n’a pas accès à Internet, copiez le fichier [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi) sur un chemin local. Fournissez le nom du chemin à l’aide du paramètre **DependencyFilesLocalPath**.

* Le fournisseur de ressources dispose d'une build Azure Stack Hub minimale correspondante.

  |Version minimale d'Azure Stack Hub|Version du fournisseur de ressources MySQL|
  |-----|-----|
  |Version 1910 (1.1910.0.58)|[MySQL RP version 1.1.47.0](https://aka.ms/azurestackmysqlrp11470)|
  |Version 1808 (1.1808.0.97)|[MySQL RP version 1.1.33.0](https://aka.ms/azurestackmysqlrp11330)|  
  |Version 1808 (1.1808.0.97)|[MySQL RP version 1.1.30.0](https://aka.ms/azurestackmysqlrp11300)|
  |Version 1804 (1.0.180513.1)|[Version 1.1.24.0 du fournisseur de ressources MySQL](https://aka.ms/azurestackmysqlrp11240)
  |     |     |
  
> [!IMPORTANT]
> Avant de déployer la version 1.1.47.0 du fournisseur de ressources MySQL, votre système Azure Stack Hub doit être mis à niveau vers la mise à jour 1910 ou ultérieure. Sur les versions antérieures d'Azure Stack Hub non prises en charge, la version 1.1.47.0 du fournisseur de ressources MySQL ne fonctionne pas.

* Vérifiez que les conditions préalables d’intégration du centre de données sont remplies :

    |Configuration requise|Informations de référence|
    |-----|-----|
    |La redirection DNS conditionnelle est correctement définie.|[Intégration au centre de données Azure Stack Hub - DNS](azure-stack-integrate-dns.md)|
    |Les ports d’entrée pour les fournisseurs de ressources sont ouverts.|[Intégration au centre de données Azure Stack Hub - Publier des points de terminaison](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound)|
    |Les objets du certificat PKI et SAN sont correctement définis.|[Prérequis PKI obligatoires pour le déploiement Azure Stack Hub](azure-stack-pki-certs.md)[Prérequis de certificat PaaS pour le déploiement Azure Stack Hub](azure-stack-pki-certs.md)|
    |     |     |

Dans un scénario déconnecté, procédez comme suit pour télécharger les modules PowerShell requis et enregistrer le référentiel manuellement.

1. Connectez-vous à un ordinateur disposant d'une connexion Internet et utilisez les scripts suivants pour télécharger les modules PowerShell.

```powershell
Import-Module -Name PowerShellGet -ErrorAction Stop
Import-Module -Name PackageManagement -ErrorAction Stop

# path to save the packages, c:\temp\azs1.6.0 as an example here
$Path = "c:\temp\azs1.6.0"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.6.0
```

2. Copiez ensuite les packages téléchargés sur un périphérique USB.

3. Connectez-vous à la station de travail déconnectée, puis copiez les packages du périphérique USB vers un emplacement sur la station de travail.

4. Enregistrez cet emplacement en tant que référentiel local.

```powershell
# requires -Version 5
# requires -RunAsAdministrator
# requires -Module PowerShellGet
# requires -Module PackageManagement

$SourceLocation = "C:\temp\azs1.6.0"
$RepoName = "azs1.6.0"

Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

New-Item -Path $env:ProgramFiles -name "SqlMySqlPsh" -ItemType "Directory" 
```

### <a name="certificates"></a>Certificats

_Pour les installations de systèmes intégrés uniquement_. Vous devez fournir le certificat PKI SQL Paas décrit dans la section facultative consacrée aux certificats PaaS de [Exigences relatives à l'infrastructure de clés publiques pour le déploiement d'Azure Stack Hub](./azure-stack-pki-certs.md). Copiez le fichier .pfx à l’emplacement spécifié par le paramètre **DependencyFilesLocalPath**. Ne fournissez pas de certificat pour les systèmes ASDK.

## <a name="deploy-the-resource-provider"></a>Déployer le fournisseur de ressources

Une fois que tous les prérequis sont remplis, exécutez le script **DeployMySqlProvider.ps1** à partir d’un ordinateur qui a accès au point de terminaison de gestion des ressources Azure de l’administrateur Azure Stack Hub et au point de terminaison privilégié pour déployer le fournisseur de ressources MySQL. Le script DeployMySqlProvider.ps1 est extrait des fichiers d’installation du fournisseur de ressources MySQL que vous avez téléchargé pour votre version d’Azure Stack Hub.

 > [!IMPORTANT]
 > Avant de déployer le fournisseur de ressources, passez en revue les notes de publication pour en savoir plus sur les nouvelles fonctionnalités, les correctifs et les problèmes connus qui pourraient affecter votre déploiement.

Pour déployer le fournisseur de ressources mySQL, ouvrez une nouvelle fenêtre PowerShell (pas PowerShell ISE) avec élévation de privilèges et basculez vers le répertoire où vous avez extrait les fichiers binaires du fournisseur de ressources mySQL. Nous vous recommandons d’utiliser une nouvelle fenêtre PowerShell pour éviter les problèmes potentiels engendrés par les modules PowerShell qui sont déjà chargés.

Exécutez le script **DeployMySqlProvider.ps1**, qui complète les tâches suivantes :

* Chargement des certificats et autres artefacts sur un compte de stockage d'Azure Stack Hub.
* Publication des packages de la galerie afin que vous puissiez déployer des bases de données MySQL par le biais de la galerie.
* Publication d’un package de galerie pour déployer des serveurs d’hébergement.
* Déploiement d’une machine virtuelle à l’aide de l’image Windows Server 2016 Core que vous avez téléchargée, puis installation du fournisseur de ressources MySQL.
* Inscription d’un enregistrement DNS local mappé à la machine virtuelle de votre fournisseur de ressources.
* Inscription de votre fournisseur de ressources auprès de l’Azure Resource Manager local pour le compte d’opérateur.

> [!NOTE]
> Lorsque le déploiement du fournisseur de ressources MySQL démarre, le groupe de ressources **system.local.sqladapter** est créé. Il peut falloir jusqu’à 75 minutes pour terminer les déploiements dans ce groupe de ressources. Vous ne devez pas placer d’autres ressources dans le groupe de ressources **system.local.mysqladapter**.

### <a name="deploymysqlproviderps1-parameters"></a>Paramètres de DeployMySqlProvider.ps1

Vous pouvez spécifier ces paramètres à partir de la ligne de commande. Si vous ne le faites pas, ou si la validation d’un paramètre échoue, vous êtes invité à fournir les paramètres requis.

| Nom du paramètre | Description | Commentaire ou valeur par défaut |
| --- | --- | --- |
| **CloudAdminCredential** | Informations d’identification de l’administrateur du cloud, nécessaires pour accéder au point de terminaison privilégié. | _Obligatoire_ |
| **AzCredential** | Informations d'identification du compte administrateur de service Azure Stack Hub. Utilisez les mêmes informations d'identification que celles utilisées pour le déploiement d'Azure Stack Hub. Le script échoue si le compte que vous utilisez avec AzCredential nécessite l’authentification multifacteur (MFA). | _Obligatoire_ |
| **VMLocalCredential** | Les informations d’identification du compte d’administrateur local de la machine virtuelle du fournisseur de ressources MySQL. | _Obligatoire_ |
| **PrivilegedEndpoint** | Adresse IP ou nom DNS du point de terminaison privilégié. |  _Obligatoire_ |
| **AzureEnvironment** | Environnement Azure du compte administrateur de service utilisé pour déployer Azure Stack Hub. Nécessaire uniquement pour les déploiements Azure AD. Les noms d’environnement pris en charge sont **AzureCloud**, **AzureUSGovernment**, ou, si vous utilisez un compte Azure AD en Chine, **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | Pour les systèmes intégrés uniquement, votre fichier de certificat .pfx doit être placé dans ce répertoire. Pour les environnements déconnectés, téléchargez [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi) dans ce répertoire. Vous pouvez éventuellement copier un package MSU Windows Update ici. | _Facultatif_ (_obligatoire_ pour les systèmes intégrés ou les environnements déconnectés) |
| **DefaultSSLCertificatePassword** | Mot de passe pour le certificat .pfx. | _Obligatoire_ |
| **MaxRetryCount** | Nombre de fois où vous souhaitez réessayer chaque opération en cas d’échec.| 2 |
| **RetryDuration** | Délai d’attente entre les tentatives, en secondes. | 120 |
| **Désinstaller** | Supprime le fournisseur de ressources et toutes les ressources associées (voir les remarques ci-dessous). | Non |
| **DebugMode** | Empêche le nettoyage automatique en cas d’échec. | Non |
| **AcceptLicense** | Ignore l’invite à accepter la licence GPL.  <https://www.gnu.org/licenses/old-licenses/gpl-2.0.html> | |

## <a name="deploy-the-mysql-resource-provider-using-a-custom-script"></a>Déployer le fournisseur de ressources MySQL à l’aide d’un script personnalisé

Si vous déployez le fournisseur de ressources MySQL version 1.1.33.0 ou antérieure, vous devez installer des versions spécifiques des modules Azure Stack Hub et AzureRm.Bootstrapper dans PowerShell. Si vous déployez le fournisseur de ressources MySQL version 1.1.47.0, le script de déploiement télécharge et installe automatiquement les modules PowerShell nécessaires sous C:\Program Files\SqlMySqlPsh.

```powershell
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
# Note that this might not be the most currently available version of Azure Stack Hub PowerShell
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0
```

> [!NOTE]
> Dans un scénario déconnecté, vous devez télécharger les modules PowerShell requis et inscrire manuellement le référentiel en tant que condition préalable.

Pour éliminer toute configuration manuelle lors du déploiement du fournisseur de ressources, vous pouvez personnaliser le script suivant. Modifiez les informations de compte et les mots de passe par défaut en fonction des besoins de votre déploiement Azure Stack Hub.

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
$AdminPass = ConvertTo-SecureString 'P@ssw0rd1' -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM local admin account
$vmLocalAdminPass = ConvertTo-SecureString 'P@ssw0rd1' -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString 'P@ssw0rd1' -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString 'P@ssw0rd1' -AsPlainText -Force

# For version 1.1.47.0, the PowerShell modules used by the RP deployment are placed in C:\Program Files\SqlMySqlPsh,
# The deployment script adds this path to the system $env:PSModulePath to ensure correct modules are used.
$rpModulePath = Join-Path -Path $env:ProgramFiles -ChildPath 'SqlMySqlPsh'
$env:PSModulePath = $env:PSModulePath + ";" + $rpModulePath

# Change to the directory folder where you extracted the installation files. Don't provide a certificate on ASDK!
. $tempDir\DeployMySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -AzureEnvironment $AzureEnvironment `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert `
    -AcceptLicense

```

Au terme du script d'installation du fournisseur de ressources, actualisez votre navigateur pour vous assurer que vous pouvez voir les dernières mises à jour, puis fermez la session PowerShell en cours.

## <a name="verify-the-deployment-by-using-the-azure-stack-hub-portal"></a>Vérifier le déploiement à l'aide du portail Azure Stack Hub

1. Connectez-vous au portail d’administration en tant qu’administrateur de service.
2. Sélectionnez **Groupes de ressources**.
3. Sélectionnez le groupe de ressources **system.\<location\>.mysqladapter**.
4. La page de la vue d’ensemble du groupe de ressources ne doit pas indiquer que des déploiements ont échoué.
5. Enfin, sélectionnez **Machines virtuelles** dans le portail d’administration pour vérifier que la machine virtuelle du fournisseur de ressources MySQL a été correctement créée et est en cours d’exécution.

## <a name="next-steps"></a>Étapes suivantes

[Ajouter des serveurs d’hébergement](azure-stack-mysql-resource-provider-hosting-servers.md)
