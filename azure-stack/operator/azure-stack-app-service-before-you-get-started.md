---
title: Prérequis pour le déploiement d’Azure App Service sur Azure Stack Hub
description: Découvrez les étapes de vérification des prérequis à effectuer avant de déployer Azure App Service sur Azure Stack Hub.
author: BryanLa
ms.topic: article
ms.date: 05/05/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 04/13/2019
zone_pivot_groups: state-connected-disconnected
ms.openlocfilehash: 95d038b53432e94d0259352a2d71c88c24f68c1f
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86489723"
---
# <a name="prerequisites-for-deploying-app-service-on-azure-stack-hub"></a>Prérequis pour le déploiement d’App Service sur Azure Stack Hub

[!INCLUDE [Azure Stack Hub update reminder](../includes/app-service-hub-update-banner.md)]

Avant de déployer Azure App Service sur Azure Stack Hub, vous devez effectuer les étapes de prérequis décrites dans cet article.

## <a name="before-you-get-started"></a>Avant de commencer 

Cette section répertorie les prérequis relatifs aux déploiements du système intégré et du Kit de développement Azure Stack (ASDK).

### <a name="resource-provider-prerequisites"></a>Prérequis pour le fournisseur de ressources

[!INCLUDE [Common RP prerequisites](../includes/marketplace-resource-provider-prerequisites.md)]

### <a name="installer-and-helper-scripts"></a>Programme d’installation et scripts d’assistance

1. Téléchargez les [scripts d’assistance au déploiement App Service sur Azure Stack Hub](https://aka.ms/appsvconmashelpers).
2. Téléchargez le [programme d’installation d’App Service sur Azure Stack Hub](https://aka.ms/appsvconmasinstaller).
3. Extrayez les fichiers à partir du fichier zip des scripts d’assistance. Les fichiers et dossiers suivants sont extraits :

   - Common.ps1
   - Create-AADIdentityApp.ps1
   - Create-ADFSIdentityApp.ps1
   - Create-AppServiceCerts.ps1
   - Get-AzureStackRootCert.ps1
   - Dossier Modules
     - GraphAPI.psm1

<!-- MultiNode Only --->
## <a name="certificates-and-server-configuration-integrated-systems"></a>Certificats et configuration du serveur (systèmes intégrés)

Cette section répertorie les prérequis relatifs aux déploiements de systèmes intégrés. 

### <a name="certificate-requirements"></a>Configuration requise des certificats

Pour exécuter le fournisseur de ressources en production, vous devez fournir les certificats suivants :

- Certificat de domaine par défaut
- Certificat d’API
- Certificat de publication
- Certificat d’identité

#### <a name="default-domain-certificate"></a>Certificat de domaine par défaut

Le certificat de domaine par défaut est placé sur le rôle front-end. Les applications utilisateur pour une demande de domaine par défaut ou de caractères génériques à Azure App Service utilisent ce certificat. Le certificat est également utilisé pour les opérations de contrôle de code source (Kudu).

Le certificat doit être un certificat générique à trois sujets au format .pfx. Cette exigence permet à un seul certificat de couvrir à la fois au domaine par défaut et au point de terminaison SCM pour les opérations de contrôle de code source.

| Format | Exemple |
| --- | --- |
| `*.appservice.<region>.<DomainName>.<extension>` | `*.appservice.redmond.azurestack.external` |
| `*.scm.appservice.<region>.<DomainName>.<extension>` | `*.scm.appservice.redmond.azurestack.external` |
| `*.sso.appservice.<region>.<DomainName>.<extension>` | `*.sso.appservice.redmond.azurestack.external` |

#### <a name="api-certificate"></a>Certificat d’API

Le certificat de l’API est placé sur le rôle de gestion. Le fournisseur de ressources l’utilise pour aider à sécuriser les appels d’API. Le certificat de publication doit contenir un objet qui correspond à l’entrée DNS de l’API.

| Format | Exemple |
| --- | --- |
| api.appservice.\<region\>.\<DomainName\>.\<extension\> | api.appservice.redmond.azurestack.external |

#### <a name="publishing-certificate"></a>Certificat de publication

Le certificat du rôle de serveur de publication sécurise le trafic FTPS quand les propriétaires d’applications chargent du contenu. Le certificat de publication doit contenir un objet qui correspond à l’entrée DNS FTPS.

| Format | Exemple |
| --- | --- |
| ftp.appservice.\<region\>.\<DomainName\>.\<extension\> | ftp.appservice.redmond.azurestack.external |

#### <a name="identity-certificate"></a>Certificat d’identité

Le certificat de l’application d’identité permet :

- l’intégration entre le répertoire Azure Active Directory (Azure AD) ou les services de fédération Active Directory (AD FS), Azure Stack Hub et App Service pour prendre en charge l’intégration avec le fournisseur de ressources de calcul.
- Des scénarios d’authentification unique pour les outils de développement avancés dans Azure App Service sur Azure Stack Hub.

Le certificat d’identité doit contenir un objet qui correspond au format suivant.

| Format | Exemple |
| --- | --- |
| sso.appservice.\<region\>.\<DomainName\>.\<extension\> | sso.appservice.redmond.azurestack.external |

### <a name="validate-certificates"></a>Valider les certificats

Avant de déployer le fournisseur de ressources App Service, vous devez [valider les certificats à utiliser](azure-stack-validate-pki-certs.md) à l’aide de l’outil Azure Stack Hub Readiness Checker disponible dans la [PowerShell Gallery](https://aka.ms/AzsReadinessChecker). L’outil Azure Stack Hub Readiness Checker vérifie que les certificats PKI générés conviennent pour le déploiement d’App Service.

En guise de bonne pratique, quand vous utilisez un ou plusieurs des [certificats PKI Azure Stack Hub](azure-stack-pki-certs.md) nécessaires, prévoyez suffisamment de temps pour tester et réémettre les certificats au besoin.

### <a name="prepare-the-file-server"></a>Préparer le serveur de fichiers

Azure App Service requiert l’utilisation d’un serveur de fichiers. Pour les déploiements de production, le serveur de fichiers doit être configuré en haute disponibilité et capable de gérer les défaillances.

#### <a name="quickstart-template-for-highly-available-file-server-and-sql-server"></a>Modèle de démarrage rapide pour un serveur de fichiers et SQL Server à haute disponibilité

Un [modèle de démarrage rapide d'architecture de référence](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/appservice-fileserver-sqlserver-ha) est maintenant disponible pour déployer un serveur de fichiers et SQL Server. Ce modèle prend en charge l’infrastructure Active Directory dans un réseau virtuel qui est configuré pour prendre en charge un déploiement à haut niveau de disponibilité d’Azure App Service sur Azure Stack Hub.

> [!NOTE]
> L’instance de système intégré doit pouvoir télécharger des ressources à partir de GitHub pour terminer le déploiement.

#### <a name="steps-to-deploy-a-custom-file-server"></a>Étapes pour déployer un serveur de fichiers personnalisé

>[!IMPORTANT]
> Si vous choisissez de déployer App Service dans un réseau virtuel existant, vous devez déployer le serveur de fichiers dans un sous-réseau distinct d’App Service.

>[!NOTE]
> Si vous avez choisi de déployer un serveur de fichiers à l’aide de l’un des modèles de démarrage rapide mentionnés ci-dessus, vous pouvez ignorer cette section car les serveurs de fichiers sont configurés dans le cadre du déploiement de modèle.

##### <a name="provision-groups-and-accounts-in-active-directory"></a>Approvisionner des groupes et des comptes dans Active Directory

1. Créez les groupes de sécurité globaux Active Directory suivants :

   - FileShareOwners
   - FileShareUsers

2. Créez les comptes Active Directory suivants en tant que comptes du service :

   - FileShareOwner
   - FileShareUser

   En ce qui concerne les meilleures pratiques en termes de sécurité, les utilisateurs de ces comptes (et pour tous les rôles web) doivent être uniques et détenir des noms d’utilisateur et des mots de passe sécurisés. Définissez les mots de passe avec les conditions suivantes :

   - Activez **Le mot de passe n’expire jamais**.
   - Activez **L’utilisateur ne peut pas changer de mot de passe**.
   - Désactivez **L’utilisateur doit changer de mot de passe à la prochaine ouverture de session**.

3. Ajoutez les comptes aux appartenances aux groupes comme suit :

   - Ajoutez **FileShareOwner** au groupe **FileShareOwners**.
   - Ajoutez **FileShareUser** au groupe **FileShareUsers**.

##### <a name="provision-groups-and-accounts-in-a-workgroup"></a>Approvisionner des groupes et des comptes dans un groupe de travail

>[!NOTE]
> Quand vous configurez un serveur de fichiers, exécutez toutes les commandes suivantes à partir d’une **invite de commandes en mode Administrateur**. <br>***N’utilisez pas PowerShell.***

Lorsque vous utilisez le modèle Azure Resource Manager, les utilisateurs sont déjà créés.

1. Exécutez les commandes suivantes pour créer les comptes FileShareOwner et FileShareUser. Remplacez `<password>` par vos propres valeurs.

   ``` DOS
   net user FileShareOwner <password> /add /expires:never /passwordchg:no
   net user FileShareUser <password> /add /expires:never /passwordchg:no
   ```

2. Définissez les mots de passe pour les comptes de sorte qu’ils n’expirent jamais en exécutant les commandes WMIC suivantes :

   ``` DOS
   WMIC USERACCOUNT WHERE "Name='FileShareOwner'" SET PasswordExpires=FALSE
   WMIC USERACCOUNT WHERE "Name='FileShareUser'" SET PasswordExpires=FALSE
   ```

3. Créez les groupes locaux FileShareUsers et FileShareOwners et ajoutez-y les comptes lors de la première étape :

   ``` DOS
   net localgroup FileShareUsers /add
   net localgroup FileShareUsers FileShareUser /add
   net localgroup FileShareOwners /add
   net localgroup FileShareOwners FileShareOwner /add
   ```

#### <a name="provision-the-content-share"></a>Approvisionner le partage de contenu

Le partage de contenu contient le contenu du site web du locataire. La procédure de provisionnement du partage de contenu sur un seul serveur de fichiers est identique pour les environnements Active Directory et de groupe de travail. Mais elle est différente pour un cluster de basculement dans Active Directory.

#### <a name="provision-the-content-share-on-a-single-file-server-active-directory-or-workgroup"></a>Approvisionnez le partage de contenu sur un seul serveur de fichiers (Active Directory ou Groupe de travail)

Exécutez les commandes suivantes dans une invite de commandes avec élévation de privilèges sur un serveur de fichiers unique. Remplacez la valeur pour `C:\WebSites` par les chemins d’accès correspondants dans votre environnement.

```DOS
set WEBSITES_SHARE=WebSites
set WEBSITES_FOLDER=C:\WebSites
md %WEBSITES_FOLDER%
net share %WEBSITES_SHARE% /delete
net share %WEBSITES_SHARE%=%WEBSITES_FOLDER% /grant:Everyone,full
```

### <a name="configure-access-control-to-the-shares"></a>Configurer le contrôle d’accès aux partages

Exécutez les commandes suivantes dans une invite de commandes avec élévation de privilèges sur le serveur de fichiers ou sur le nœud de cluster de basculement, qui est le propriétaire actuel de la ressource de cluster. Remplacez les valeurs en italiques par les valeurs spécifiques de votre environnement.

#### <a name="active-directory"></a>Active Directory

```DOS
set DOMAIN=<DOMAIN>
set WEBSITES_FOLDER=C:\WebSites
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

#### <a name="workgroup"></a>Groupe de travail

```DOS
set WEBSITES_FOLDER=C:\WebSites
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

### <a name="prepare-the-sql-server-instance"></a>Préparer l’instance SQL Server

> [!NOTE]
> Si vous avez choisi de déployer le modèle de démarrage rapide pour un serveur de fichiers et SQL Server à haute disponibilité, vous pouvez ignorer cette section, car le modèle déploie et configure SQL Server dans une configuration haute disponibilité (HA).

Pour qu’Azure App Service sur Azure Stack Hub héberge et contrôle les bases de données, vous devez préparer une instance SQL Server pour stocker les bases de données d’App Service.

Pour des raisons de production et de haute disponibilité, vous devez utiliser une version complète de SQL Server 2014 SP2 ou une version ultérieure, activer l’authentification en mode mixte et déployer une [configuration hautement disponible](/sql/sql-server/failover-clusters/high-availability-solutions-sql-server).

L’instance SQL Server pour Azure App Service sur Azure Stack Hub doit être accessible depuis tous les rôles App Service. SQL Server peut être déployé au sein d’un abonnement de fournisseur par défaut dans Azure Stack Hub. Vous pouvez aussi vous servir d’une infrastructure existante au sein de votre organisation (sous réserve d’une connectivité avec Azure Stack Hub). Si vous utilisez une image de Place de marché Azure, pensez à configurer le pare-feu en conséquence.

> [!NOTE]
> Un certain nombre d'images de machines virtuelles IaaS SQL sont disponibles via la fonctionnalité Gestion de la Place de marché. Assurez-vous de toujours télécharger la dernière version de l’extension Iaas SQL avant de déployer une machine virtuelle à l’aide d’un élément de la Place de marché. Les images SQL sont les mêmes que les machines virtuelles SQL sont disponibles dans Azure. Pour les machines virtuelles SQL créées à partir de ces images, l’extension IaaS et les améliorations apportées au portail correspondantes fournissent des fonctionnalités de mise à jour corrective et de sauvegarde automatique.
>
> Pour tous les rôles SQL Server, vous pouvez utiliser une instance par défaut ou une instance nommée. Si vous utilisez une instance nommée, assurez-vous de démarrer manuellement le service SQL Server Browser et d’ouvrir le port 1434.

Le programme d’installation App Service vérifie que l’autonomie de la base de données est activée sur SQL Server. Pour activer l’autonomie de la base de données sur le serveur SQL Server qui hébergera les bases de données App Service, exécutez ces commandes SQL :

```sql
sp_configure 'contained database authentication', 1;
GO
RECONFIGURE;
GO
```

<!-- ASDK Only --->
## <a name="certificates-and-server-configuration-asdk"></a>Certificats et configuration du serveur (ASDK)

Cette section répertorie les prérequis relatifs aux déploiements d'ASDK. 

### <a name="certificates-required-for-asdk-deployment-of-azure-app-service"></a>Certificats requis pour le déploiement ASDK d’Azure App Service

Le script *Create-AppServiceCerts.ps1* fonctionne avec l’autorité de certification Azure Stack Hub pour créer les quatre certificats dont App Service a besoin.

| Nom de fichier | Utilisation |
| --- | --- |
| _.appservice.local.azurestack.external.pfx | Certificat SSL par défaut d’App Service |
| api.appservice.local.azurestack.external.pfx | Certificat SSL de l’API App Service |
| ftp.appservice.local.azurestack.external.pfx | Certificat SSL d’App Service Publisher |
| sso.appservice.local.azurestack.external.pfx | Certificat d’application d’identité App Service |

Pour créer les certificats, suivez les étapes ci-dessous :

1. Connectez-vous à l’hôte ASDK en utilisant le compte AzureStack\AzureStackAdmin.
2. Ouvrez une session PowerShell avec élévation de privilèges.
3. Exécutez le script *Create-AppServiceCerts.ps1* à partir du dossier où vous avez extrait les scripts d’assistance. Ce script crée quatre certificats dans le même dossier que le script dont App Service a besoin pour créer des certificats.
4. Entrez un mot de passe pour sécuriser les fichiers .pfx et prenez-en note. Vous devez l’entrer ultérieurement dans le programme d’installation d’App Service sur Azure Stack Hub.

#### <a name="create-appservicecertsps1-script-parameters"></a>Paramètres du script Create-AppServiceCerts.ps1

| Paramètre | Obligatoire ou facultatif | Valeur par défaut | Description |
| --- | --- | --- | --- |
| pfxPassword | Obligatoire | Null | Mot de passe pour aider à protéger la clé privée du certificat |
| DomainName | Obligatoire | local.azurestack.external | Suffixe de la région et du domaine Azure Stack Hub |

### <a name="quickstart-template-for-file-server-for-deployments-of-azure-app-service-on-asdk"></a>Modèle de démarrage rapide pour un serveur de fichiers utilisé pour les déploiements d’Azure App Service sur ASDK.

Pour les déploiements d’ASDK uniquement, vous pouvez utiliser cet [exemple de modèle de déploiement Azure Resource Manager](https://aka.ms/appsvconmasdkfstemplate) pour déployer un serveur de fichiers configuré avec un seul nœud. Le serveur de fichiers à nœud unique sera dans un groupe de travail.  

> [!NOTE]
> L’instance ASDK doit être en mesure de télécharger des ressources à partir de GitHub pour terminer le déploiement.

### <a name="sql-server-instance"></a>Instance SQL Server

Pour qu’Azure App Service sur Azure Stack Hub héberge et contrôle les bases de données, vous devez préparer une instance SQL Server pour stocker les bases de données d’App Service.

Pour les déploiements d’ASDK, vous pouvez utiliser SQL Server Express 2014 SP2 ou une version ultérieure. SQL Server doit être configuré pour prendre en charge l’authentification en **mode mixte**, car App service sur Azure Stack Hub ne prend **PAS** en charge l’authentification Windows.

L’instance SQL Server pour Azure App Service sur Azure Stack Hub doit être accessible depuis tous les rôles App Service. SQL Server peut être déployé au sein d’un abonnement de fournisseur par défaut dans Azure Stack Hub. Vous pouvez aussi vous servir d’une infrastructure existante au sein de votre organisation (sous réserve d’une connectivité avec Azure Stack Hub). Si vous utilisez une image de Place de marché Azure, pensez à configurer le pare-feu en conséquence.

> [!NOTE]
> Un certain nombre d'images de machines virtuelles IaaS SQL sont disponibles via la fonctionnalité Gestion de la Place de marché. Assurez-vous de toujours télécharger la dernière version de l’extension Iaas SQL avant de déployer une machine virtuelle à l’aide d’un élément de la Place de marché. Les images SQL sont les mêmes que les machines virtuelles SQL sont disponibles dans Azure. Pour les machines virtuelles SQL créées à partir de ces images, l’extension IaaS et les améliorations apportées au portail correspondantes fournissent des fonctionnalités de mise à jour corrective et de sauvegarde automatique.
>
> Pour tous les rôles SQL Server, vous pouvez utiliser une instance par défaut ou une instance nommée. Si vous utilisez une instance nommée, assurez-vous de démarrer manuellement le service SQL Server Browser et d’ouvrir le port 1434.

Le programme d’installation App Service vérifie que l’autonomie de la base de données est activée sur SQL Server. Pour activer l’autonomie de la base de données sur le serveur SQL Server qui hébergera les bases de données App Service, exécutez ces commandes SQL :

```sql
sp_configure 'contained database authentication', 1;
GO
RECONFIGURE;
GO

```

## <a name="licensing-concerns-for-required-file-server-and-sql-server"></a>Problèmes de licences pour le serveur de fichiers requis et SQL Server

Azure App Service sur Azure Stack Hub nécessite un serveur de fichiers et SQL Server pour fonctionner. Vous êtes libre d’utiliser des ressources préexistantes situées en dehors de votre déploiement Azure Stack Hub ou de déployer des ressources au sein de leur abonnement fournisseur par défaut Azure Stack Hub.

Si vous choisissez de déployer les ressources au sein de votre Abonnement fournisseur par défaut Azure Stack Hub, les licences pour ces ressources (licences Windows Server et licences SQL Server) sont incluses dans le coût d’Azure App Service sur Azure Stack Hub et soumises aux contraintes suivantes :

- l’infrastructure est déployée dans l’abonnement fournisseur par défaut ;
- l’infrastructure est utilisée exclusivement par Azure App Service sur le fournisseur de ressources Azure Stack Hub. Les autres charges de travail, d’administration (autres fournisseurs de ressources, par exemple : SQL-RP) ou de locataire (par exemple, applications de locataire, qui nécessitent une base de données) ne sont pas autorisées à utiliser cette infrastructure.

## <a name="operational-responsibility-of-file-and-sql-servers"></a>Responsabilité opérationnelle des serveurs de fichiers et SQL

Les opérateurs cloud sont responsables de la maintenance et du fonctionnement du serveur de fichiers et de SQL Server.  Le fournisseur de ressources ne gère pas ces ressources.  L'opérateur cloud est responsable de la sauvegarde des bases de données App Service et du partage des fichiers de contenu des locataires.

## <a name="retrieve-the-azure-resource-manager-root-certificate-for-azure-stack-hub"></a>Récupérer le certificat racine Azure Resource Manager pour Azure Stack Hub

Ouvrez une session PowerShell avec élévation de privilèges sur un ordinateur qui peut atteindre le point de terminaison privilégié sur le système intégré Azure Stack Hub ou l’hôte ASDK.

Exécutez le script *Get-AzureStackRootCert.ps1* à partir du dossier où vous avez extrait les scripts d’assistance. Le script crée un certificat racine dans le même dossier que le script dont App Service a besoin pour créer des certificats.

Lorsque vous exécutez la commande PowerShell suivante, vous devez fournir le point de terminaison privilégié et les informations d’identification du compte AzureStack\CloudAdmin.

```powershell
    Get-AzureStackRootCert.ps1
```

#### <a name="get-azurestackrootcertps1-script-parameters"></a>Paramètres du script Get-AzureStackRootCert.ps1

| Paramètre | Obligatoire ou facultatif | Valeur par défaut | Description |
| --- | --- | --- | --- |
| PrivilegedEndpoint | Obligatoire | AzS-ERCS01 | Point de terminaison privilégié |
| CloudAdminCredential | Obligatoire | AzureStack\CloudAdmin | Informations d’identification du compte de domaine pour les administrateurs cloud d’Azure Stack Hub |

## <a name="network-and-identity-configuration"></a>Configuration du réseau et de l'identité

### <a name="virtual-network"></a>Réseau virtuel

> [!NOTE]
> La création préalable d’un réseau virtuel personnalisé est optionnelle, car le service Azure App Service sur Azure Stack Hub peut créer le réseau virtuel requis, mais il devra ensuite communiquer avec SQL et File Server via des adresses IP publiques.  Si vous utilisez le serveur de fichiers haute disponibilité App Service et le modèle de démarrage rapide SQL Server pour déployer les ressources de serveur de fichiers et SQL préalablement requises, le modèle déploie également un réseau virtuel.

Azure App Service sur Azure Stack Hub permet de déployer le fournisseur de ressources sur un réseau virtuel existant ou de créer un réseau virtuel dans le cadre du déploiement. Si vous avez un réseau virtuel existant, vous pouvez utiliser des adresses IP internes pour vous connecter au serveur de fichiers et à SQL Server requis par Azure App Service sur Azure Stack Hub. Vous devez configurer le réseau virtuel avec la plage d’adresses et les sous-réseaux suivants avant d’installer Azure App Service sur Azure Stack Hub :

Réseau virtuel - /16

Sous-réseaux

- ControllersSubnet /24
- ManagementServersSubnet /24
- FrontEndsSubnet /24
- PublishersSubnet /24
- WorkersSubnet /21

>[!IMPORTANT]
> Si vous choisissez de déployer App Service dans un réseau virtuel existant, SQL Server doit être déployé dans un sous-réseau distinct d’App Service et du serveur de fichiers.
>

### <a name="create-an-identity-application-to-enable-sso-scenarios"></a>Créer une application d’identité pour activer les scénarios d’authentification unique

Azure App Service utilise une application d’identité (principal de service) pour prendre en charge les opérations suivantes :

- Intégration d’un groupe de machines virtuelles identiques sur les niveaux de travail.
- Authentification unique pour le portail Azure Functions et outils de développement avancés (Kudu).

Selon le fournisseur d’identité utilisé par Azure Stack Hub, Azure Active Directory (Azure AD) ou AD FS, vous devez suivre les étapes ci-dessous qui conviennent pour créer le principal de service à utiliser par le fournisseur de ressources Azure App Service sur Azure Stack Hub.

::: zone pivot="state-connected"
#### <a name="create-an-azure-ad-app"></a>Créer une application Azure AD

Suivez ces étapes pour créer le principal de service dans votre locataire Azure AD :

1. Ouvrez une instance PowerShell en tant que azurestack\Azurestackadmin.
1. Accédez à l’emplacement où les scripts ont été téléchargés et extraits dans [l’étape des prérequis](azure-stack-app-service-before-you-get-started.md).
1. [Installez PowerShell pour Azure Stack Hub](azure-stack-powershell-install.md).
1. Exécutez le script **Create-AADIdentityApp.ps1**. Lorsque vous y êtes invité, entrez l’ID de locataire Azure AD que vous utilisez pour votre déploiement Azure Stack Hub. Par exemple, entrez **myazurestack.onmicrosoft.com**.
1. Dans la fenêtre **Informations d’identification**, entrez votre compte administrateur et votre mot de passe pour le service Azure AD. Sélectionnez **OK**.
1. Entrez le chemin d’accès au fichier du certificat et le mot de passe du certificat pour le [certificat créé précédemment](azure-stack-app-service-before-you-get-started.md). Le certificat par défaut créé pour cette étape est **sso.appservice.local.azurestack.external.pfx**.
1. Notez l’ID d’application qui est retourné dans la sortie PowerShell. Vous utilisez l’ID au cours des étapes suivantes pour donner votre consentement aux demandes d’autorisation de l’application, et pendant l’installation. 
1. Ouvrez une nouvelle fenêtre de navigateur et connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur du service Azure Active Directory.
1. Ouvrez le service Azure Active Directory.
1. Dans le volet gauche, sélectionnez **Inscriptions d’applications**.
1. Recherchez l’ID d’application que vous avez noté à l’étape 7. 
1. Sélectionnez l’inscription d’application App Service dans la liste.
1. Dans le volet gauche, sélectionnez **Autorisations d’API**.
1. Sélectionnez **Accorder un consentement d’administrateur pour \<tenant\>** , où \<tenant\> représente le nom de votre locataire Azure AD. Confirmez l’octroi du consentement en sélectionnant **Oui**.

```powershell
    Create-AADIdentityApp.ps1
```

| Paramètre | Obligatoire ou facultatif | Valeur par défaut | Description |
| --- | --- | --- | --- |
| DirectoryTenantName | Obligatoire | Null | ID de locataire Azure AD. Fournir le GUID ou une chaîne. Par exemple : myazureaaddirectory.onmicrosoft.com. |
| AdminArmEndpoint | Obligatoire | Null | Point de terminaison Azure Resource Manager d’administrateur. Par exemple : adminmanagement.local.azurestack.external. |
| TenantARMEndpoint | Obligatoire | Null | Point de terminaison Azure Resource Manager de locataire. Par exemple : management.local.azurestack.external. |
| AzureStackAdminCredential | Obligatoire | Null | Informations d’identification de l’administrateur du service Azure AD. |
| CertificateFilePath | Obligatoire | Null | **Chemin complet** du fichier de certificat d’application d’identité généré précédemment. |
| CertificatePassword | Obligatoire | Null | Mot de passe pour aider à protéger la clé privée du certificat. |
| Environnement | Facultatif | AzureCloud | Le nom de l’environnement de Cloud pris en charge dans lequel le service Graph Azure Active Directory cible est disponible.  Valeurs autorisées : 'AzureCloud', 'AzureChinaCloud', 'AzureUSGovernment', 'AzureGermanCloud'.|
::: zone-end

#### <a name="create-an-adfs-app"></a>Créer une application ADFS

1. Ouvrez une instance PowerShell en tant que azurestack\Azurestackadmin.
1. Accédez à l’emplacement où les scripts ont été téléchargés et extraits dans [l’étape des prérequis](azure-stack-app-service-before-you-get-started.md).
1. [Installez PowerShell pour Azure Stack Hub](azure-stack-powershell-install.md).
1. Exécutez le script **Create-ADFSIdentityApp.ps1**.
1. Dans la fenêtre **Informations d’identification**, entrez votre compte administrateur et votre mot de passe pour le cloud AD FS. Sélectionnez **OK**.
1. Entrez le chemin d’accès au fichier du certificat et le mot de passe du certificat pour le [certificat créé précédemment](azure-stack-app-service-before-you-get-started.md). Le certificat par défaut créé pour cette étape est **sso.appservice.local.azurestack.external.pfx**.

```powershell
    Create-ADFSIdentityApp.ps1
```

| Paramètre | Obligatoire ou facultatif | Valeur par défaut | Description |
| --- | --- | --- | --- |
| AdminArmEndpoint | Obligatoire | Null | Point de terminaison Azure Resource Manager d’administrateur. Par exemple : adminmanagement.local.azurestack.external. |
| PrivilegedEndpoint | Obligatoire | Null | Point de terminaison privilégié. Par exemple : AzS-ERCS01. |
| CloudAdminCredential | Obligatoire | Null | Informations d’identification du compte de domaine pour les administrateurs cloud d’Azure Stack Hub. Par exemple : Azurestack\CloudAdmin. |
| CertificateFilePath | Obligatoire | Null | **Chemin complet** du fichier PFX du certificat d’application d’identité. |
| CertificatePassword | Obligatoire | Null | Mot de passe pour aider à protéger la clé privée du certificat. |

<!--Connected/Disconnected-->

### <a name="download-items-from-the-azure-marketplace"></a>Télécharger des éléments à partir de la Place de marché Azure

Azure App Service sur Azure Stack Hub nécessite que les éléments soient [téléchargés à partir de la Place de marché Azure](azure-stack-download-azure-marketplace-item.md) afin de les rendre disponibles dans la Place de marché Azure Stack Hub. Vous devez télécharger les éléments suivants avant de commencer le déploiement ou la mise à niveau d’Azure App Service sur Azure Stack Hub :

<!-- Connected --->
::: zone pivot="state-connected"
> [!IMPORTANT]
> Windows Server Core n’est pas une image de plateforme prise en charge pour une utilisation avec Azure App Service sur Azure Stack Hub.
>
> N’utilisez pas d’images d’évaluation pour les déploiements de production.
>
1. La **dernière version de l’image de machine virtuelle Windows Server 2016 Datacenter**.
::: zone-end

::: zone pivot="state-disconnected"
<!-- Disconnected --->
1. **Image de machine virtuelle complète de Windows Server 2016 Datacenter avec Microsoft.Net 3.5.1 SP1 activé** .  Azure App Service sur Azure Stack Hub exige l'activation de Microsoft .NET 3.5.1 SP1 sur l'image utilisée pour le déploiement. Cette fonctionnalité n'est pas activée pour les images Windows Server 2016 syndiquées de la Place de marché. Dans les environnements déconnectés, elles n'ont pas accès à Microsoft Update pour télécharger les packages à installer via DISM. Par conséquent, vous devez créer et utiliser une image Windows Server 2016 sur laquelle cette fonctionnalité est pré-activée et avec des déploiements déconnectés.

   Pour plus d’informations sur la création d’une image personnalisée et sur son ajout à la Place de marché, consultez [Ajouter une image de machine virtuelle personnalisée à Azure Stack Hub](azure-stack-add-vm-image.md). Lorsque vous ajoutez l'image à la Place de marché, veillez à spécifier les propriétés suivantes :

   - Serveur de publication = MicrosoftWindowsServer
   - Offre = WindowsServer
   - Référence SKU = 2016-Datacenter
   - Version = Spécifier la dernière version

::: zone-end

<!-- For All --> 
2. **L’extension de script personnalisé v1.9.1 (ou version ultérieure)** . Cet élément est une extension de machine virtuelle.

## <a name="next-steps"></a>Étapes suivantes

[Installer le fournisseur de ressources App Service](azure-stack-app-service-deploy.md)
