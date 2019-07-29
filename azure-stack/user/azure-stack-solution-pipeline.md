---
title: Déployer des applications sur Azure et Azure Stack
description: Découvrez comment déployer des applications sur Azure et Azure Stack avec un pipeline CI/CD hybride.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 07/23/2019
ms.topic: solution
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/07/2018
ms.openlocfilehash: 86b7fca6b9d2b9aaa322849f2490f83851a80940
ms.sourcegitcommit: d8981947a4bf7752d608e21e6fe0bf2ccd4825d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68462888"
---
# <a name="deploy-apps-to-azure-and-azure-stack"></a>Déployer des applications sur Azure et Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Cette solution vous indique comment déployer des applications sur Azure et Azure Stack à l’aide de l’intégration continue et de la livraison continue (CI/CD) hybrides d’Azure Pipelines.

Après avoir configuré Azure Stack, Azure DevOps et des applications web conformément aux [Composants requis](#prerequisites), vous pouvez :

> [!div class="checklist"]
> - inscrire votre application web et configurer l’accès Azure Pipelines dans Azure Active Directory (Azure AD) ; 
> - créer des points de terminaison Azure Pipelines pour Azure AD et Active Directory Federation Services (AD FS) ; 
> - installer l’agent de build Azure Pipelines sur le serveur de build Azure Stack ; 
> - configurer le déploiement d’applications autonomes sur Azure et Azure Stack ;
> - créer un pipeline de build qui exécute des builds automatiques basés sur des validations de code dans votre projet ;
> - créer un pipeline de mise en production qui déploie automatiquement vos builds dans Azure AD et Azure Stack ; 
> - créer et déployer manuellement des mises en production et consulter des résumés et des journaux de mise en production. 

Pour en savoir plus sur CI/CD, consultez les articles suivants :

* [Qu’est-ce que l’intégration continue ?](https://www.visualstudio.com/learn/what-is-continuous-integration/)
* [Qu’est-ce que la livraison continue ?](https://www.visualstudio.com/learn/what-is-continuous-delivery/)

## <a name="azure-stack-and-hybrid-cicd"></a>Azure Stack et CI/CD hybrides

Microsoft Azure Stack est une extension d’Azure qui intègre l’agilité et l’innovation du cloud computing à vos environnements locaux. C’est le seul cloud hybride qui vous permet de générer et de déployer des applications hybrides dans des environnements locaux et de cloud public. Vous pouvez créer une application dans Azure Stack et la déployer ensuite dans Azure Stack, Azure ou votre cloud hybride Azure. 

La continuité, la sécurité et la fiabilité du déploiement d’applications sont essentiels pour votre organisation et pour votre équipe de développement. Le modèle de livraison CI/CD Azure Pipelines vous permet de consolider vos pipelines de build dans votre environnement local et dans le cloud public et de modifier les emplacements de déploiement sans modifier votre application. L’approche hybride offre les autres avantages suivants :

- Vous pouvez gérer un ensemble cohérent d’outils de développement dans votre environnement Azure Stack local et dans le cloud public Azure.  Un ensemble d’outils courants simplifie l’implémentation des modèles et pratiques de CI/CD.
- Les applications et services déployés dans Azure ou Azure Stack sont interchangeables et le même code peut s’exécuter dans les deux emplacements. Vous pouvez tirer parti des fonctions et fonctionnalités en local et de cloud public.

> [!TIP]
> ![hybrid-pillars.png](./media/azure-stack-solution-pipeline/hybrid-pillars.png)  
> L’article [Modèles de conception du cloud hybride pour Azure Stack](azure-stack-edge-pattern-overview.md) passe en revue les piliers de qualité logicielle pour la conception, le déploiement et l’exploitation d’applications hybrides. Les critères de qualité incluent le placement, l’extensibilité, la résilience, la facilité de gestion et la sécurité. Ces considérations de conception vous aident à optimiser la conception d’applications hybrides, en réduisant les risques dans les environnements de production.

## <a name="prerequisites"></a>Prérequis

- Connaissances de base d’Azure et d’Azure Stack. Pour en savoir plus sur cette solution avant de la déployer, lisez les articles suivants :
  
  - [Présentation de Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/)
  - [Vue d’ensemble d’Azure Stack](../operator/azure-stack-overview.md)
  
- Un abonnement Azure. Si vous n’en avez pas, [créez un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
  
- Une application web créée dans Azure. Utilisez un [modèle de Azure Resource Manager](https://azure.microsoft.com/resources/templates/) pour créer une application web que vous pouvez déployer à la fois en local et dans le cloud public. Prenez note de l’URI de l’application à utiliser ultérieurement. 
  
- Visual Studio 2019 [installé](/visualstudio/install/install-visual-studio).
  
- Accès administrateur à une organisation [Azure DevOps](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services) qui peut créer des pipelines et un [projet](/azure/devops/organizations/projects/create-project) ou [un espace de travail](/azure/devops/repos/tfvc/create-work-workspaces) DevOps. 
  
- Une image Windows Server 2016 avec .NET 3,5 pour les builds Azure Pipelines des machines virtuelles de l’agent de build privé sur votre Azure Stack.
  
- Un système intégré Azure Stack ou le Kit de développement Azure Stack (ASDK) déployé et configuré conformément aux instructions suivantes. 
  
  
  Le Kit de développement Azure Stack (ASDK) est un déploiement à un seul nœud d’Azure Stack que vous pouvez télécharger et utiliser gratuitement. ASDK vous permet d’évaluer Azure Stack et d’utiliser des API et des outils Azure dans un environnement hors production.
  
  Tout utilisateur disposant d’informations d’identification d’administrateur Azure AD ou Services de fédération Active Directory (AD FS) peut déployer le kit ASDK. Un partenaire OEM/matériel Azure peut déployer un Azure Stack de production. Vous devez être un opérateur d’Azure Stack pour effectuer les tâches de configuration Azure Stack suivantes : 
  
  - Déployer Azure App Service
  - Créer des plans et des offres
  - Créer un abonnement du locataire
  - Appliquer une image Windows Server 2016
  
  > [!Note]
  > L’installation du Kit de développement Azure Stack prend environ 7 heures, adaptez donc votre planification en conséquence.
    
  Pour déployer et configurer le kit ASDK :
  
  1. Suivez les instructions détaillées pour le déploiement dans [Déployer le kit ASDK à l’aide du programme d'installation](../asdk/asdk-install.md).
     
  1. Utilisez le script PowerShell [ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1) pour automatiser les étapes de post-déploiement ASDK.
     
  1. Déployez les services PaaS [Azure App Service](../operator/azure-stack-app-service-deploy.md) sur Azure Stack.
     
  1. Créez un [plan et une offre](../operator/azure-stack-plan-offer-quota-overview.md) dans Azure Stack.
     
  1. Créez un [abonnement du locataire](../operator/azure-stack-subscribe-plan-provision-vm.md) pour l’offre dans Azure Stack. 
     
  1. Créez une application web dans l’abonnement du locataire. Notez l’URL de la nouvelle application web. Vous en aurez besoin plus tard.
     
  1. Déployez une machine virtuelle Windows Server 2016 avec .NET 3,5 dans l’abonnement du locataire, pour qu’elle soit le serveur de build qui exécute Azure Pipelines.
  
  > [!Note]
  > Votre environnement Azure Stack a besoin des images correctes pour exécuter Windows Server et SQL Server. Le service App Service doit également y être déployé.

## <a name="register-your-web-app-and-give-it-access-to-resources"></a>Inscrire votre application web et lui accorder l’accès aux ressources 

Dans Azure Active Directory (Azure AD), Azure Pipelines s’authentifie auprès d’Azure Resource Manager à l’aide d’un *principal de service*. Pour approvisionner des ressources pour Azure Pipelines, le principal de service doit avoir le rôle **Contributeur** dans l’abonnement Azure. 

Vous pouvez utiliser le Portail Azure pour configurer l'authentification pour votre application. 

1. Inscrivez votre application pour créer un principal de service.
1. Utilisez le *contrôle d’accès en fonction du rôle (RBAC)* pour attribuer le rôle de **Contributeur** au nom de principal du service (SPN).
1. Copiez et enregistrez les valeurs ID de l’application et ID de l’abonné dont vous avez besoin pour créer des points de terminaison pour Azure Pipelines. 
1. Créez et enregistrez une valeur de clé secrète de l’application.

Vous pouvez également [utiliser un script PowerShell](https://github.com/Microsoft/vsts-rm-extensions/blob/master/TaskModules/powershell/Azure/SPNCreation.ps1#L5) pour créer un principal de service et des points de terminaison. L’article [Créer une connexion de service Azure Resource Manager avec un principal de service existant](/vsts/pipelines/library/connect-to-azure?view=vsts#create-an-azure-resource-manager-service-connection-with-an-existing-service-principal) explique ce processus.

 > [!Note]  
 > Si vous utilisez le script PowerShell pour créer un point de terminaison Azure Resource Manager Azure Stack, vous devez transmettre le paramètre **-azureStackManagementURL** et le paramètre **-environmentName**. Par exemple :  
 > `-azureStackManagementURL https://management.local.azurestack.external -environmentName AzureStack`

### <a name="register-your-app-in-azure-ad"></a>Inscrire votre application dans Azure AD 

1. Dans le [Portail Azure](https://portal.azure.com), sélectionnez **Azure Active Directory**, puis **Inscriptions d’applications** dans le menu de navigation de gauche.
   
1. Sélectionnez **Nouvelle inscription**.
   
1. Dans la page **Inscrire une application**
   1. Entrez le nom de votre application web.
   1. Sélectionnez un type de compte pris en charge. 
   1. Sous **URI de redirection**, sélectionnez **Web** pour le type d’application que vous souhaitez créer et entrez l’URI de votre application web. 
   1. Sélectionnez **Inscription**.
      
      ![Inscrivez votre application](./media/azure-stack-solution-pipeline/create-app.png) 

### <a name="assign-the-app-to-a-role"></a>Attribuer un rôle à l’application

Vous devez attribuer un rôle à votre application pour lui donner accès aux ressources de votre abonnement. RBAC d’Azure vous permet de contrôler le niveau d’accès dont les utilisateurs ont besoin pour faire leur travail. Pour en savoir plus sur RBAC, consultez [Gérer l’accès aux ressources d’abonnement Azure](/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json). Pour en savoir plus sur les rôles disponibles, consultez [RBAC : pour les ressources Azure](/azure/role-based-access-control/built-in-roles).

Azure Pipelines doit disposer du rôle **Contributeur** pour être en mesure d’approvisionner des ressources dans un abonnement Azure Stack. 

Vous pouvez définir l’étendue du rôle au niveau de l’abonnement, du groupe de ressources ou de la ressource. Les autorisations sont héritées des niveaux inférieurs de l’étendue Par exemple, l’ajout d’une application au rôle **Lecteur** pour un groupe de ressources signifie que l’application peut lire le groupe de ressources et toutes ses ressources.

Pour attribuer votre application à un rôle **Contributeur** :

1. dans le Portail Azure, naviguez vers le niveau d’étendue désiré. Par exemple, pour assigner un rôle au niveau de l’abonnement, sélectionnez **Tous les services** et **Abonnements**.
   
   ![Attribuer un rôle au niveau d’un abonnement](./media/azure-stack-solution-pipeline/select-subscription.png)
   
1. Sélectionnez l’abonnement auquel assigner l’application.
   
1. Dans le menu de navigation de gauche, sélectionnez **Contrôle d’accès (IAM)** .
   
1. Sélectionnez **Ajouter une attribution de rôle**.
   
1. Dans la boîte de dialogue **Ajouter une attribution de rôle**, sélectionnez le rôle **Contributeur**. Par défaut, les applications Azure AD ne figurent pas dans les options disponibles. Pour trouver votre application, recherchez-la par son nom et sélectionnez-la.
   
   ![Sélectionnez le rôle et l’application](./media/azure-stack-solution-pipeline/select-role.png)
   
1. Sélectionnez **Enregistrer** pour finaliser l’attribution du rôle. Votre application apparaît dans la liste des utilisateurs assignés à un rôle pour cette étendue.

Votre principal de service est créé. La section suivante montre comment obtenir les valeurs dont Azure Pipelines à besoin pour se connecter par programmation.

### <a name="get-values-for-signing-in"></a>Obtenir les valeurs pour la connexion

Lors de la création de points de terminaison pour Azure Pipelines, vous devez saisir l’ID de l’abonné et l’ID de l’application. Pour obtenir ces valeurs :

1. Dans le portail Azure, sélectionnez **Azure Active Directory**.
   
1. Dans le menu de navigation de gauche, sélectionnez **Inscriptions d’applications**, puis sélectionnez votre application.
   
1. Copiez et enregistrez l’**ID du répertoire (abonné)** et l’**ID de l’application (client)** à utiliser pour la création de points de terminaison.
   
   ![Copier le répertoire (ID abonné) et l’ID de l’application (client)](./media/azure-stack-solution-pipeline/copy-app-id.png)

### <a name="create-a-new-application-secret"></a>Créer un secret d’application

Lors de la création de points de terminaison pour Azure Pipelines, l’application doit s’authentifier. Elle peut [utiliser un certificat](/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets) ou un secret d’application. Si vous avez déployé Azure Stack avec AD FS en tant que fournisseur d’identité, vous devez utiliser un certificat.

Suivez les étapes indiquées dans [Certificats et secrets](/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets) pour créer et télécharger un nouveau certificat. 

Ou, créez un nouveau secret d’application :

1. Dans le portail Azure, sélectionnez **Azure Active Directory**.
   
1. Dans le menu de navigation de gauche, sélectionnez **Inscriptions d’applications**, puis sélectionnez votre application.
   
1. Dans le menu de navigation de gauche, sélectionnez **Certificats et secrets**.
   
1. Sous **Secrets client**, sélectionnez **Nouveau secret client**.
   
1. Dans **Ajouter un secret client**, tapez une description, sélectionnez une date d’expiration puis sélectionnez **Ajouter**.
   
1. Copiez la **VALUE** du nouveau secret. Vous devez fournir la valeur pour vous connecter en tant qu’application. Il est important d’enregistrer cette valeur maintenant, car elle ne s’affichera plus une fois que vous aurez quitté cette page.
   
   ![Copiez la valeur du secret, car vous ne pourrez plus la récupérer ultérieurement](./media/azure-stack-solution-pipeline/copy-secret.png)

## <a name="create-endpoints"></a>Créer des points de terminaison

En créant des points de terminaison, une build Azure Pipelines peut déployer des applications Azure AD sur Azure Stack. Azure Pipelines se connecte à l’agent de build, qui se connecte à Azure Stack.

Après avoir défini les autorisations de création de points de terminaison, vous pouvez créer des points de terminaison pour Azure AD ou AD FS. 

- Si vous avez déployé Azure Stack avec Azure AD en tant que fournisseur d’identité, vous pouvez utiliser un certificat ou un secret d’application pour créer une connexion de service Azure Resource Manager pour les déploiements Azure. 
  
- Si vous avez utilisé les services de fédération Active Directory (AD FS) comme fournisseur d’identité pour Azure Stack, vous devez créer la connexion de service à l’aide d’un certificat plutôt que d’un secret client pour l’authentification. 

### <a name="set-endpoint-creation-permissions"></a>Définir les autorisations de création des points de terminaison

1. Dans votre organisation et votre projet Azure DevOps, sélectionnez **Paramètres du projet**. 
   
1. Sélectionnez **Sécurité**, puis sous **Groupes Azure DevOps**, sélectionnez **Administrateurs de points de terminaison**.
   
1. Sous l’onglet **Membres**, sélectionnez **Ajouter**.
   
1. Dans **Ajouter des utilisateurs et des groupes**, sélectionnez les noms d’utilisateur dans la liste, y compris vous-même, puis sélectionnez **Enregistrer les modifications**.
   
   ![Ajouter un membre](./media/azure-stack-solution-pipeline/endpoint-permissions.png)
   
1. Dans la liste **Groupes Azure DevOps**, sélectionnez **Créateurs de points de terminaison**, puis répétez les étapes précédentes pour ajouter des utilisateurs au groupe **Créateurs de points de terminaison**. 

### <a name="create-an-endpoint-for-azure-ad-or-ad-fs-deployments"></a>Créer un point de terminaison pour des déploiements Azure AD ou AD FS

Suivez les instructions dans [Créer une connexion au service Azure Resource Manager avec un principal du service existant](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-with-an-existing-service-principal) pour créer le point de terminaison de connexion au service.  

Utilisez les valeurs suivantes pour remplir le formulaire : 

- **Nom de la connexion** : Entrez un nom convivial à utiliser pour faire référence à cette connexion de service.
  
- **Environment** : Sélectionnez le nom de l’environnement, tel que **AzureCloud** ou **AzureStack**. Si vous ne voyez pas AzureStack dans la liste déroulante, consultez [Se connecter à Azure Stack](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops#connect-to-azure-stack).
  
- **URL d’environnement** : Si vous n’avez pas sélectionné **AzureCloud**, entrez l’URL de votre environnement, par exemple *https:\//management.local.azurestack.external*.
  
- **Niveau d’étendue** : Sélectionnez le niveau d’étendue dont vous avez besoin, par exemple **Abonnement**. 
  
- **ID de l'abonnement** : Entrez votre ID d’abonnement.
  
- **Nom de l’abonnement** : Entrez votre nom d’utilisateur à partir d’Azure Stack.
  
- **ID de client du principal du service** : Entrez l'**ID de l’application (client)** que vous avez enregistré précédemment. 
  
- **Clé Principal du service** ou **Certificat** : Sélectionnez l’une ou l’autre option. 
  
  > [!NOTE]
  > Pour créer un point de terminaison AD FS, vous devez utiliser un certificat pour l’authentification. 
  
  - Pour la **Clé du principal du service**, entrez la clé secrète client que vous avez enregistrée précédemment.
  - Si vous choisissez **Certificat**, entrez le contenu des sections certificat et clé privée du fichier du certificat *. pem*. 
    
    > [!NOTE]
    > Pour convertir un fichier de certificat *.pfx* en *. pem* exécutez `openssl pkcs12 -in file.pfx -out file.pem -nodes -password pass:<password_here>`.
  
- **ID du locataire** : Entrez l'**ID du répertoire (abonné)** que vous avez enregistré précédemment.
  
- **Connexion : Non vérifié** : Sélectionnez **Vérifier la connexion** pour valider vos paramètres de connexion au principal du service.
  
  > [!NOTE]
  > Si votre point de terminaison Azure Resource Manager n’est pas exposé à Internet, la validation de la connexion échoue. Ce comportement est attendu et vous pouvez valider votre connexion en créant un pipeline de mise en production avec une tâche simple.

## <a name="install-and-configure-the-build-agent"></a>Installer et configurer l’agent de build 

L’utilisation d’un agent de build hébergé dans Azure Pipelines est une option pratique pour créer et déployer des applications web. Azure effectue automatiquement la maintenance et les mises à niveau de l’agent, ce qui permet un cycle de développement continu et sans interruption.

Dans Azure DevOps, créez un jeton d’accès personnel (PAT) à utiliser pour Azure Stack. Utilisez ensuite le PAT pour déployer et configurer l’agent de build sur la machine virtuelle du serveur de build Azure Stack. 
   
### <a name="create-a-personal-access-token"></a>Créer un jeton d’accès personnel

1. Connectez-vous à Azure DevOps et sélectionnez **Mon Profil** dans l’angle supérieur droit. 
   
1. Sur la page de votre profil, développez la liste déroulante en regard au nom de votre organisation Azure Stack, puis sélectionnez **Gérer la sécurité**. 
   
   ![Gérer la sécurité](media/azure-stack-solution-pipeline/managesecurity.png)
   
1. Dans la page **Jetons d’accès personnels**, sélectionnez **Nouveau jeton**.
   
   ![Jetons d'accès personnels](media/azure-stack-solution-pipeline/pats.png)
   
1. Dans la page **Créer un nouveau jeton d’accès personnel**, renseignez les informations sur le jeton et sélectionnez **Créer**. 
   
   ![Créer un PAT](media/azure-stack-solution-pipeline/createpat.png)
   
1. Copiez et enregistrez le jeton. Il ne s’affichera plus une fois que vous aurez quitté la page Web.
   
   ![Avertissement du PAT](media/azure-stack-solution-pipeline/patwarning.png)
   
### <a name="install-and-configure-the-agent-on-the-build-server"></a>Installer et configurer l’agent sur le serveur de build

1. Connectez-vous à la machine virtuelle du serveur de build déployé sur l’hôte Azure Stack.
   
1. Téléchargez l’image de l’agent de build. 
   
1. Dans une invite de commandes d’administrateur, déployez l’agent en tant que service à l’aide de votre PAT, puis exécutez-le.
   
1. Accédez au dossier de l’agent de build extrait et exécutez le fichier *config.cmd*. Le fichier *config.cmd* met à jour le dossier de l’agent de build avec des fichiers supplémentaires.
   
   ![Installer et configurer l’agent de build](media/azure-stack-solution-pipeline/buildagent.png)

Maintenant que vous avez créé le point de terminaison et installé l’agent de build Azure Pipelines sur le serveur de build, la connexion Azure Pipelines sur Azure Stack est prête à être utilisée. L’agent de build dans Azure Stack reçoit des instructions d’Azure Pipelines, puis il transmet les informations du point de terminaison pour la communication avec Azure Stack.

Au lieu de gérer chaque agent séparément, vous pouvez organiser les agents en *pools d’agents*. Un pool d’agents définit la limite de partage pour tous les agents de ce pool. Les pools d’agents sont étendus à l’organisation Azure DevOps Services. En d’autres termes, vous pouvez partager un pool d’agents sur plusieurs projets. Pour en savoir plus sur les pools d’agents, consultez [Créer des pools d’agents et des files d’attente](/azure/devops/pipelines/agents/pools-queues).

## <a name="create-build-and-release-pipelines"></a>Créer des pipelines de build et de mise en production 

Azure Pipelines fournit un pipeline hautement configurable et gérable pour des mises en production sur plusieurs environnements : développement, mise en lots, assurance qualité (AQ) et production, par exemple. Ce processus de mise en production peut inclure des approbations requises à des étapes spécifiques du cycle de vie de l’application.

Dans cette partie de la solution, vous allez :

- cloner, connecter et ajouter du code à votre projet Azure DevOps dans Visual Studio ;
- créer un déploiement d’application web autonome ;
- configurer les pipelines de build et de mise en production de CI/CD.

La CI/CD hybride peut s’appliquer tant au code d’application qu’au code d’infrastructure. Vous pouvez utiliser les [modèles hybrides Azure Resource Manager](https://azure.microsoft.com/resources/templates/) pour déployer votre code d’application web Azure dans des clouds locaux et publics.

### <a name="clone-your-project"></a>Cloner votre projet

1. Dans Visual Studio **Team Explorer**, sélectionnez l'icône **Se connecter** et connectez-vous à votre organisation Azure DevOps. 
   
1. Sélectionnez **Gérer les connexions** > **Se connecter à un projet**. 
   
   ![Se connecter à un projet à partir de Team Explorer](media/azure-stack-solution-pipeline/connecttoprojectteamexp.png)

1. Dans la boîte de dialogue **Se connecter à un projet**, sélectionnez votre projet d’application web, définissez un chemin d' accès local, puis sélectionnez **Cloner** pour cloner le référentiel localement.
   
   ![Cloner le référentiel dans Se connecter à un projet](media/azure-stack-solution-pipeline/cloneproject.png)

### <a name="create-a-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Créer un déploiement d’applications web autonomes pour App Services dans les deux clouds

1. Dans Visual Studio **Explorateur de solutions**, ouvrez votre fichier *WebApplication. csproj* et ajoutez `<RuntimeIdentifier>win10-x64</RuntimeIdentifier>`. Pour plus d’informations sur cette étape, consultez la rubrique [Déploiement autonome](/dotnet/core/deploying/#self-contained-deployments-scd).
   
   ![Configurer RuntimeIdentifier](media/azure-stack-solution-pipeline/runtimeidentifier.png)
   
1. Enregistrez votre travail et utilisez **Team Explorer** pour vérifier le code dans votre projet.

### <a name="create-a-build-pipeline-and-run-a-build"></a>Créer un pipeline de build et exécuter un build

1. Dans votre navigateur Web, ouvrez votre organisation et votre projet Azure DevOps.
   
1. Sélectionnez **Pipelines** > **Builds** dans le menu de navigation de gauche, puis sélectionnez **Nouveau pipeline**. 
   
1. Sous **Sélectionner un modèle**, sélectionnez le modèle **ASP.NET Core**, puis sélectionnez **Appliquer**. 
   
1. Dans la page de configuration, sélectionnez **Publier** dans le volet de gauche.
   
1. Dans le volet de droite, sous **Arguments**, ajoutez `-r win10-x64` à la configuration. 
   
   ![Ajouter un argument de pipeline de build](media/azure-stack-solution-pipeline/buildargument.png)
   
1. En haut de la page, sélectionnez **Enregistrer et mettre en file d’attente**.
   
1. Dans la boîte de dialogue **Exécuter le pipeline**, sélectionnez **Enregistrer et exécuter**. 
   
Le [build de déploiement autonome](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) publie des artefacts qui peuvent s’exécuter sur Azure et Azure Stack.

### <a name="create-a-release-pipeline"></a>Créer un pipeline de mise en production

La création d’un pipeline de mise en production est la dernière étape du processus de configuration du CI/CD hybride. Le pipeline de mise en production est utilisé pour créer une mise en production et déployer votre build.

1. Dans votre projet Azure DevOps, sélectionnez **Pipelines** > **Mises en production** dans le menu de navigation de gauche, puis sélectionnez **Nouveau pipeline**. 
   
1. Dans la page **Sélectionner un modèle**, sélectionnez **Déploiement d’Azure App Service**, puis sélectionnez **Appliquer**.
   
   ![Sélectionner un modèle de mise en production](media/azure-stack-solution-pipeline/releasetemplate.png)
   
1. Sous l'onglet **Pipeline**, sélectionnez **Ajouter un artefact** dans le volet de gauche. Dans le volet de droite, sélectionnez le build de l’application web que vous venez de créer dans le menu déroulant **Source (pipeline de build )** , puis sélectionnez **Ajouter**.
   
   ![Ajouter un artefact de build](media/azure-stack-solution-pipeline/addartifact.png)
   
1. Sous l'onglet **Pipeline**, sous **Index**, sélectionnez le lien hypertexte à l’**Index 1** pour **Afficher les tâches d’index**.
   
   ![Afficher les tâches d’index](media/azure-stack-solution-pipeline/viewstagetasks.png)
   
1. Sous l'onglet **Tâches**, entrez *Azure* comme **Nom d’index**. 
   
1. Sous **Paramètres**, sélectionnez votre abonnement dans la liste déroulante **Abonnement Azure**, puis entrez votre **Nom d’App Service**.
   
   ![Sélectionner un abonnement et entrer le nom d’App Service](media/azure-stack-solution-pipeline/stage1.png)
   
1. Dans le volet de gauche, sélectionnez **Exécuter sur l’agent**. Dans le volet de droite, sélectionnez **VS2017 hébergé** dans la liste déroulante **Pool d’agents** s’il n’est pas déjà sélectionné.
   
   ![Sélectionner un agent hébergé](media/azure-stack-solution-pipeline/agentjob.png)
   
1. Dans le volet de gauche, sélectionnez **Déployer Azure App Service**, puis dans le volet de droite, accédez au **Package ou dossier** de votre build d’application web Azure.
   
   ![Sélectionner le package ou dossier](media/azure-stack-solution-pipeline/packageorfolder.png)
   
1. Dans la boîte de dialogue **Sélectionner un fichier ou un dossier**, sélectionnez **OK**.
   
1. Sélectionnez **Enregistrer** dans le coin supérieur droit de la page **Nouveau pipeline de mise en production**.
   
   ![Enregistrer les modifications](media/azure-stack-solution-pipeline/save-devops-icon.png)
   
1. Sous l'onglet **Pipeline**, sélectionnez **Ajouter un artefact**. Sélectionnez votre projet, puis sélectionnez votre build Azure Stack dans le menu déroulant **Source (pipeline de build)** . Sélectionnez **Ajouter**. 
   
1. Sous l'onglet **Pipeline**, sous **Index**, sélectionnez **Ajouter**.
   
1. Dans le nouvel index, sélectionnez le lien hypertexte pour **Afficher les tâches d’index**. Entrez *Azure Stack* comme nom d’index. 
   
   ![Afficher le nouvel index](media/azure-stack-solution-pipeline/newstage.png)
   
1. Sous **Paramètres**, sélectionnez votre point de terminaison Azure Stack, puis entrez le nom de votre application web Azure Stack en tant que **Nom App Service**.
   
1. Sous **Exécuter sur l'agent**, sélectionnez votre agent du serveur de build Azure Stack dans la liste déroulante **Pool d’agents**.
   
1. Pour **Déployer Azure App Service**, sélectionnez le **Package ou dossier** pour le build Azure Stack, puis sélectionnez **OK** dans la boîte de dialogue **Sélectionner un fichier ou un dossier**.
   
1. Sous l’onglet **Variables**, recherchez la variable nommée **VSTS_ARM_REST_IGNORE_SSL_ERRORS**. Définissez la valeur de la variable sur **true** et définissez sa portée sur **Azure Stack**.
   
   ![Configurer la variable](media/azure-stack-solution-pipeline/setvariable.png)
   
1. Sous l'onglet **Pipeline**, sélectionnez l’icône **Déclencheur de déploiement continu** pour chaque artefact, puis affectez-lui la valeur **Activé**.  
   
   ![Définir la déclencheur de déploiement continu](media/azure-stack-solution-pipeline/contindeploymentenabled.png)
   
1. Sélectionnez l’icône **Conditions de prédéploiement** dans l’index Azure Stack et définissez le déclencheur sur **Après la mise en production**.
   
   ![Définir le déclencheur de conditions préalables au déploiement](media/azure-stack-solution-pipeline/predeployafterrelease.png)
   
1. Sélectionnez **Enregistrer** dans le coin supérieur droit de la page **Nouveau pipeline de mise en production** pour enregistrer le pipeline de mise en production.

> [!Note]
> Il se peut que certains paramètres des tâches de mise en production aient été définis automatiquement en tant que [variables d’environnement](/azure/devops/pipelines/release/variables?view=vsts#custom-variables) lors de la création du pipeline de mise en production à partir du modèle. Ces paramètres ne peuvent pas être modifiés dans les paramètres de tâche, mais peuvent être modifiés dans les éléments d’index parent.

## <a name="release-the-app"></a>Mettre l’application en production

Maintenant que vous disposez d’un pipeline de mise en production, vous pouvez l’utiliser pour créer une mise en production et déployer votre application. 

Étant donné que le déclencheur de déploiement continu est défini dans votre pipeline de mise en production, la modification du code source démarre un nouveau build et crée automatiquement une nouvelle mise en production. Cependant, vous créez et exécutez manuellement cette nouvelle mise en production.

Pour créer et déployer une mise en production :

1. Sur la page du nouveau pipeline de mise en production, sélectionnez **Créer une mise en production** en haut à droite. 
   
   ![Créer une mise en production ](media/azure-stack-solution-pipeline/createreleaseicon.png)
   
1. Dans la page **Créez une nouvelle mise en production** :
   1. Sous **Pipeline**, sélectionnez l’index **Azure** pour modifier son déclencheur automatisé en déclencheur manuel. 
   1. Sous **Artefacts**, assurez-vous que les artefacts appropriés sont sélectionnés.
   1. Entrez une **Description de mise en production**, puis sélectionnez **Créer**. 
   
   Une bannière indique que la nouvelle mise en production est créée. Vous pouvez sélectionner le lien du nom de la mise en production pour afficher une page de résumé de la mise en sortie indiquant les détails de la mise en production, tels que l’état du déploiement.
   
1. Pour déployer la mise en production manuelle, sélectionnez l'index **Azure**, sélectionnez **Déployer**, puis sélectionnez **Déployer** dans la boîte de dialogue de l’index. 
   
1. Une fois le déploiement terminé, ouvrez l’application déployée dans votre navigateur. Par exemple, pour le site web Azure App Services, ouvrez l’URL `https://<your-app-name>.azurewebsites.net`.

### <a name="monitor-and-track-releases"></a>Surveiller et suivre les mises en production

Vous pouvez sélectionner l’état du lien hypertexte dans un index de mise en production pour afficher plus d’informations sur le déploiement. 

![Page récapitulative de mise en production](media/azure-stack-solution-pipeline/releasesummary.png)

Un administrateur peut facilement suivre la progression globale des mises en production et savoir quelles mises en production sont en attente d’approbation.

![Page de résumé de la mise en attente avec approbation en attente](media/azure-stack-solution-pipeline/releasepending.png)

Vous pouvez consulter les journaux de mise en production de tous vos déploiements : 

1. Dans votre projet Azure DevOps, sélectionnez **Pipelines** > **Mises en production** à gauche, puis sélectionnez une mise en production. 
   
1. Sur la page du résumé de la mise en production, pointez ou sélectionnez un index, puis sélectionnez **Journaux**. 
   
   Dans le journal des mises en production, le volet de gauche indique l’état de chaque opération pour chaque index. Pendant un déploiement, le volet de droite affiche le journal en direct de l’agent. Lorsque le déploiement est terminé, l’intégralité du fichier journal s’affiche dans le volet de droite. 
   
1. Sélectionnez un index dans le volet de gauche pour afficher le fichier journal pour cet index, tel que **Approbations de prédéploiement**. 
   
1. Pour afficher les approbations, sélectionnez **Afficher l’approbation** dans le volet de droite pour afficher qui a approuvé ou rejeté la mise en production, ainsi que d’autres détails. 
   
Afficher des journaux des index individuels facilite le suivi et le débogage de parties du déploiement global. Vous pouvez également **Télécharger tous les journaux** en tant que fichier *. zip*.
   
![Journal de mise en production](media/azure-stack-solution-pipeline/releaselog.png)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les modèles Azure Cloud, consultez [Modèles de conception cloud](/azure/architecture/patterns).
