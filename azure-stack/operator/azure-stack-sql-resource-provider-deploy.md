---
title: Déployer un fournisseur de ressources SQL Server
titleSuffix: Azure Stack
description: Découvrez comment déployer le fournisseur de ressources SQL Server sur Azure Stack.
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
ms.date: 10/02/2019
ms.lastreviewed: 03/18/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.openlocfilehash: ae2e5ec161be9dace70746c5b5460964d2348272
ms.sourcegitcommit: 08d2938006b743b76fba42778db79202d7c3e1c4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74954568"
---
# <a name="deploy-the-sql-server-resource-provider-on-azure-stack"></a>Déployer le fournisseur de ressources SQL Server sur Azure Stack

Utilisez le fournisseur de ressources SQL Server d’Azure Stack pour exposer des bases de données SQL en tant que service d’Azure Stack. Le fournisseur de ressources SQL s’exécute en tant que service sur une machine virtuelle (VM) Windows Server 2016 Server Core.

> [!IMPORTANT]
> Seul le fournisseur de ressources est pris en charge pour créer des éléments sur des serveurs qui hébergent SQL ou MySQL. Les éléments créés sur un serveur hôte qui ne sont pas créés par le fournisseur de ressources peuvent entraîner un état qui ne correspond pas.

## <a name="prerequisites"></a>Prérequis

Plusieurs prérequis doivent être remplis avant de déployer le fournisseur de ressources SQL d’Azure Stack. Afin de répondre à ces exigences, complétez les étapes suivantes sur un ordinateur ayant accès à la machine virtuelle du point de terminaison privilégié :

- Si vous ne l’avez pas encore fait, vous devez [inscrire Azure Stack](azure-stack-registration.md) auprès d’Azure pour pouvoir ensuite télécharger des éléments de la Place de marché Azure.

- Installez les modules PowerShell Azure Stack et Azure sur le système où vous allez exécuter cette installation. Le système doit être une image Windows 10 ou Windows Server 2016 dotée de la dernière version du runtime .NET. Consultez [Installer PowerShell pour Azure Stack](./azure-stack-powershell-install.md).

- Ajoutez la machine virtuelle Windows Server standard sur la Place de marché Azure Stack en téléchargeant l’image de **Windows Server 2016 Datacenter Server Core**.

- Téléchargez le binaire du fournisseur de ressources SQL, puis exécutez le fichier auto-extracteur pour extraire le contenu dans un répertoire temporaire. Le fournisseur de ressources possède une build Azure Stack minimale correspondante.

  |Version minimale d’Azure Stack|Version SQL RP|
  |-----|-----|
  |Version 1910 (1.1910.0.58)|[SQL RP version 1.1.47.0](https://aka.ms/azurestacksqlrp11470)|
  |Version 1808 (1.1808.0.97)|[SQL RP version 1.1.33.0](https://aka.ms/azurestacksqlrp11330)|  
  |Version 1808 (1.1808.0.97)|[SQL RP version 1.1.30.0](https://aka.ms/azurestacksqlrp11300)|  
  |Version 1804 (1.0.180513.1)|[SQL RP version 1.1.24.0](https://aka.ms/azurestacksqlrp11240)  
  |     |     |

> [!IMPORTANT]
> Avant de déployer la version 1.1.47.0 du fournisseur de ressources SQL, votre système Azure Stack doit être mis à niveau vers la mise à jour 1910 ou une version ultérieure. La version 1.1.47.0 du fournisseur de ressources SQL sur les versions antérieures Azure Stack non prises en charge ne fonctionne pas.

- Vérifiez que les conditions préalables d’intégration du centre de données sont remplies :

    |Configuration requise|Informations de référence|
    |-----|-----|
    |La redirection DNS conditionnelle est correctement définie.|[Intégration au centre de données Azure Stack - DNS](azure-stack-integrate-dns.md)|
    |Les ports d’entrée pour les fournisseurs de ressources sont ouverts.|[Intégration au centre de données Azure Stack - Ports et protocoles entrants](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound)|
    |Les objets du certificat PKI et SAN sont correctement définis.|[Prérequis PKI obligatoires pour le déploiement Azure Stack](azure-stack-pki-certs.md#mandatory-certificates)<br>[Prérequis de certificat PaaS pour le déploiement Azure Stack](azure-stack-pki-certs.md#optional-paas-certificates)|
    |     |     |

### <a name="certificates"></a>Certificats

_Pour les installations de systèmes intégrés uniquement_. Vous devez fournir le certificat PKI SQL Paas décrit dans la section sur les certificats PaaS facultatif de [Exigences relatives à l’infrastructure de clé publique pour le déploiement Azure Stack](./azure-stack-pki-certs.md#optional-paas-certificates). Copiez le fichier .pfx à l’emplacement spécifié par le paramètre **DependencyFilesLocalPath**. Ne fournissez pas de certificat pour les systèmes ASDK.

## <a name="deploy-the-sql-resource-provider"></a>Déployer le fournisseur de ressources SQL

Une fois tous les prérequis installés, exécutez le script **DeploySqlProvider.ps1** pour déployer le fournisseur de ressources SQL. Le script DeploySqlProvider.ps1 est extrait du binaire du fournisseur de ressources SQL que vous avez téléchargé pour votre version d’Azure Stack.

 > [!IMPORTANT]
 > Avant de déployer le fournisseur de ressources, passez en revue les notes de publication pour en savoir plus sur les nouvelles fonctionnalités, les correctifs et les problèmes connus qui pourraient affecter votre déploiement.

Pour déployer le fournisseur de ressources SQL, ouvrez une **nouvelle** fenêtre PowerShell (pas PowerShell ISE) avec élévation de privilèges et basculez vers le répertoire où vous avez extrait les fichiers binaires du fournisseur de ressources SQL. Nous vous recommandons d’utiliser une nouvelle fenêtre PowerShell pour éviter les problèmes potentiels engendrés par les modules PowerShell qui sont déjà chargés.

Exécutez le script DeploySqlProvider.ps1, qui complète les tâches suivantes :

- Chargement des certificats et des autres artefacts dans un compte de stockage sur Azure Stack.
- Publication des packages de la galerie afin que vous puissiez déployer des bases de données SQL à l’aide de la galerie.
- Publication d’un package de galerie pour déployer des serveurs d’hébergement.
- Déployez une machine virtuelle à l’aide de l’image Windows Server 2016 Core que vous avez téléchargée, puis installez le fournisseur de ressources SQL.
- Inscription d’un enregistrement DNS local mappé à la machine virtuelle de votre fournisseur de ressources.
- Inscription de votre fournisseur de ressources auprès de l’Azure Resource Manager local pour le compte d’opérateur.

> [!NOTE]
> Lorsque le déploiement du fournisseur de ressources SQL démarre, le groupe de ressources **system.local.sqladapter** est créé. Il peut falloir jusqu’à 75 minutes pour terminer les déploiements requis dans ce groupe de ressources.

### <a name="deploysqlproviderps1-parameters"></a>Paramètres de DeploySqlProvider.ps1

Vous pouvez spécifier les paramètres suivants à partir de la ligne de commande. Si vous ne le faites pas, ou si la validation d’un paramètre échoue, vous êtes invité à fournir les paramètres requis.

| Nom du paramètre | Description | Commentaire ou valeur par défaut |
| --- | --- | --- |
| **CloudAdminCredential** | Informations d’identification de l’administrateur du cloud, nécessaires pour accéder au point de terminaison privilégié. | _Obligatoire_ |
| **AzCredential** | Informations d’identification du compte d’administration de service Azure Stack. Utilisez les mêmes informations d’identification que celles utilisées pour le déploiement d’Azure Stack. | _Obligatoire_ |
| **VMLocalCredential** | Informations d’identification du compte d’administrateur local de la machine virtuelle du fournisseur de ressources SQL. | _Obligatoire_ |
| **PrivilegedEndpoint** | Adresse IP ou nom DNS du point de terminaison privilégié. |  _Obligatoire_ |
| **AzureEnvironment** | L’environnement Azure du compte administrateur de service utilisé pour déployer Azure Stack. Nécessaire uniquement pour les déploiements Azure AD. Les noms d’environnement pris en charge sont **AzureCloud**, **AzureUSGovernment** ou, si vous utilisez Azure Active Directory en Chine, **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | Pour les systèmes intégrés uniquement, votre fichier de certificat .pfx doit être placé dans ce répertoire. Vous pouvez éventuellement copier un package MSU Windows Update ici. | _Facultatif_ (_obligatoire_ pour les systèmes intégrés) |
| **DefaultSSLCertificatePassword** | Mot de passe pour le certificat .pfx. | _Obligatoire_ |
| **MaxRetryCount** | Nombre de fois où vous souhaitez réessayer chaque opération en cas d’échec.| 2 |
| **RetryDuration** | Délai d’attente entre les tentatives, en secondes. | 120 |
| **Désinstaller** | Supprime le fournisseur de ressources et toutes les ressources associées (voir les remarques ci-dessous). | Non |
| **DebugMode** | Empêche le nettoyage automatique en cas d’échec. | Non |

## <a name="deploy-the-sql-resource-provider-using-a-custom-script"></a>Déployer le fournisseur de ressources SQL à l’aide d’un script personnalisé

Si vous déployez la version 1.1.33.0 ou une version antérieure du fournisseur de ressources SQL, vous devez installer des versions spécifiques des modules Azure Stack et AzureRm.Bootstrapper dans PowerShell. Si vous déployez la version 1.1.47.0 du fournisseur de ressources SQL, cette étape peut être ignorée.

```powershell
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureStack module
# Note that this might not be the most currently available version of Azure Stack PowerShell
Install-Module -Name AzureRm.BootStrapper -RequiredVersion 0.5.0 -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0
```

Pour éliminer toute configuration manuelle lors du déploiement du fournisseur de ressources, vous pouvez personnaliser le script suivant.  

Modifiez les informations de compte et les mots de passe par défaut en fonction des besoins de votre déploiement Azure Stack.

```powershell
# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS VMs
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud, or AzureUSGovernment depending which Azure subscription you're using.
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account can be Azure Active Directory or Active Directory Federation Services.
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM local admin account.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# Add the cloudadmin credential that's required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Clear the existing login information from the Azure PowerShell context.
Clear-AzureRMContext -Scope CurrentUser -Force
Clear-AzureRMContext -Scope Process -Force

# Change to the directory folder where you extracted the installation files. Don't provide a certificate on ASDK!
. $tempDir\DeploySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -AzureEnvironment $AzureEnvironment `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert

 ```

Une fois le script d’installation du fournisseur de ressources terminé, actualisez votre navigateur pour vous assurer que vous pouvez voir les dernières mises à jour.

## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Vérifier le déploiement à l’aide du portail Azure Stack

Vous pouvez utiliser les étapes suivantes pour vérifier que le fournisseur de ressources SQL est correctement déployé.

1. Connectez-vous au portail d’administration en tant qu’administrateur de service.
2. Sélectionnez **Groupes de ressources**.
3. Sélectionnez le groupe de ressources **system.\<location\>.sqladapter**.
4. La page de la vue d’ensemble du groupe de ressources ne doit pas indiquer que des déploiements ont échoué.

    ![Vérifier le déploiement du fournisseur de ressources SQL dans le portail administrateur Azure Stack](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)

5. Enfin, sélectionnez **Machines virtuelles** dans le portail administrateur pour vérifier que la machine virtuelle du fournisseur de ressources SQL a été correctement créée et est en cours d’exécution.

## <a name="next-steps"></a>Étapes suivantes

[Ajouter des serveurs d’hébergement](azure-stack-sql-resource-provider-hosting-servers.md)
