---
title: Déployer des applications sur Azure et Azure Stack | Microsoft Docs
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
ms.topic: solution
ms.date: 03/11/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/07/2018
ms.openlocfilehash: 9fbadb923452fc2420d1f8626a69d377c4d72e12
ms.sourcegitcommit: 2a4cb9a21a6e0583aa8ade330dd849304df6ccb5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68286969"
---
# <a name="deploy-apps-to-azure-and-azure-stack"></a>Déployer des applications sur Azure et Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Découvrez comment déployer des applications sur Azure et Azure Stack à l’aide d’un pipeline d’intégration continue/de livraison continue (CI/CD) hybride.

Dans cette solution, vous allez créer un exemple d’environnement pour :

> [!div class="checklist"]
> * Lancer une nouvelle build basée sur des validations de code dans votre référentiel Azure DevOps Services.
> * Déployer automatiquement votre application sur Azure mondial pour le test d’acceptation des utilisateurs.
> * Lorsque votre code réussit le test, déployez automatiquement l’application sur Azure Stack.

## <a name="benefits-of-the-hybrid-delivery-build-pipeline"></a>Avantages du pipeline de build de livraison hybride

La continuité, la sécurité et la fiabilité sont des éléments clés du déploiement d’application. Ces éléments sont essentiels pour votre organisation et critiques pour votre équipe de développement. Un pipeline CI/CD hybride vous permet de consolider vos pipelines de build dans votre environnement local et dans le cloud public. Un modèle de livraison hybride vous permet également de changer les emplacements de déploiement sans modifier votre application.

L’approche hybride offre les autres avantages suivants :

* Vous pouvez gérer un ensemble cohérent d’outils de développement dans votre environnement Azure Stack local et dans le cloud public Azure.  Un ensemble d’outils courants simplifie l’implémentation des modèles et pratiques de CI/CD.
* Les applications et services déployés dans Azure ou Azure Stack sont interchangeables et le même code peut s’exécuter dans les deux environnements. Vous pouvez tirer parti des fonctions et fonctionnalités en local et de cloud public.

Pour en savoir plus sur CI et CD, consultez :

* [Qu’est-ce que l’intégration continue ?](https://www.visualstudio.com/learn/what-is-continuous-integration/)
* [Qu’est-ce que la livraison continue ?](https://www.visualstudio.com/learn/what-is-continuous-delivery/)

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack est une extension d’Azure. Azure Stack apporte l’agilité et l’innovation du cloud computing à votre environnement local, en activant le seul cloud hybride qui vous permet de créer et de déployer des applications hybrides en tout lieu.  
> 
> L’article [Design Considerations for Hybrid Applications](azure-stack-edge-pattern-overview.md) se penche sur les fondements de la qualité logicielle (sélection élective, scalabilité, disponibilité, résilience, facilité de gestion et sécurité) en matière de conception, de déploiement et d’exploitation des applications hybrides. Les considérations de conception vous aident à optimiser la conception d’application hybride, en réduisant les risques dans les environnements de production.

## <a name="prerequisites"></a>Prérequis

Des composants doivent être en place pour pouvoir créer un pipeline CI/CD hybride. La préparation des composants suivants prendra de temps :

* Un partenaire OEM/matériel Azure peut déployer un Azure Stack de production. Tous les utilisateurs peuvent déployer le Kit de développement Azure Stack (ASDK).
* Un opérateur Azure Stack doit également effectuer les étapes suivantes : déployer App Service, créer des plans et des offres, créer un abonnement de locataire et ajouter l’image Windows Server 2016.

>[!NOTE]
>Si certains de ces composants sont déjà déployés, vérifiez qu’ils remplissent toutes les exigences avant de commencer cette solution.

Cette solution suppose que vous disposez de connaissances de base sur Azure et Azure Stack. Pour en savoir plus avant de commencer la solution, lisez les articles suivants :

* [Présentation de Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/)
* [Concepts clés d’Azure Stack](../operator/azure-stack-overview.md)

### <a name="azure-requirements"></a>Conditions requises pour Azure

* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
* Créez une [application web](https://docs.microsoft.com/azure/app-service/overview) dans Azure. Notez l’URL de l’application web, car vous devrez l’utiliser dans la solution.

### <a name="azure-stack-requirements"></a>Configuration requise d’Azure Stack

* Utilisez un système intégré Azure Stack ou déployez le Kit de développement Azure Stack (ASDK). Pour déployer l’ASDK :
  * La [solution : Déployer le Kit de développement Azure Stack à l’aide du programme d’installation](../asdk/asdk-install.md) fournit des instructions de déploiement détaillées.
  * Utilisez le script PowerShell [ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 ) pour automatiser les étapes de post-déploiement ASDK.

    > [!Note]
    > Comme l’installation du Kit de développement Azure Stack prend environ 7 heures, adaptez votre planification en conséquence.

  * Déployez les services PaaS [App Service](../operator/azure-stack-app-service-deploy.md) sur Azure Stack.
  * Créez des [plans/offres](../operator/azure-stack-plan-offer-quota-overview.md) dans Azure Stack.
  * Créez un [abonnement de locataire](../operator/azure-stack-subscribe-plan-provision-vm.md) dans Azure Stack.
  * Créez une application web dans l’abonnement de locataire. Notez l’URL de la nouvelle application web, car elle sera utilisée plus tard.
  * Déployez une machine virtuelle Windows Serveur 2012 dans l’abonnement de locataire. Ce serveur vous servira de serveur de builds, sur lequel vous exécuterez Azure DevOps Services.
* Fournissez une image Windows Server 2016 avec .NET 3.5 pour une machine virtuelle. Cette machine virtuelle est créée sur votre service Azure Stack en tant qu’agent de build privé.

### <a name="developer-tool-requirements"></a>Configuration requise de l’outil de développeur

* Créez un [espace de travail Azure DevOps Services](https://docs.microsoft.com/azure/devops/repos/tfvc/create-work-workspaces). Le processus d’inscription crée un projet nommé **MyFirstProject**.
* [Installez Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio), puis [connectez-vous à Azure DevOps Services](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services).
* Connectez-vous à votre projet et [clonez-le localement](https://www.visualstudio.com/docs/git/gitquickstart).

  > [!Note]
  > Votre environnement Azure Stack a besoin des images correctes syndiquées pour exécuter Windows Server et SQL Server. Le service App Service doit également y être déployé.

## <a name="prepare-the-private-azure-pipelines-agent-for-azure-devops-services-integration"></a>Préparer l’agent Azure Pipelines privé pour l’intégration d’Azure DevOps Services

### <a name="prerequisites"></a>Prérequis

Azure DevOps Services s’authentifie auprès d’Azure Resource Manager à l’aide d’un principal de service. Azure DevOps Services doit disposer du rôle **Contributeur** pour approvisionner des ressources dans un abonnement Azure Stack.

Les étapes suivantes décrivent ce qui est nécessaire pour configurer l’authentification :

1. Créez un principal du service, ou utilisez un principal du service existant.
2. Créez des clés d’authentification pour le principal du service.
3. Validez l’abonnement Azure Stack avec le contrôle d’accès en fonction du rôle (RBAC) pour autoriser le nom de principal du service (SPN) à faire partie du rôle Contributeur.
4. Créez une nouvelle définition de service dans Azure DevOps Services avec les points de terminaison Azure Stack et les informations du SPN.

### <a name="create-a-service-principal"></a>Créer un principal du service

Pour créer un principal du service, reportez-vous aux instructions [Création du principal du service](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Pour Type d’application, choisissez **Application web/API** ou [utilisez le script PowerShell](https://github.com/Microsoft/vsts-rm-extensions/blob/master/TaskModules/powershell/Azure/SPNCreation.ps1#L5) comme expliqué dans l’article [Créer une connexion de service Azure Resource Manager avec un principal du service existant](https://docs.microsoft.com/vsts/pipelines/library/connect-to-azure?view=vsts#create-an-azure-resource-manager-service-connection-with-an-existing-service-principal).

 > [!Note]  
 > Si vous utilisez le script pour créer un point de terminaison Azure Resource Manager Azure Stack, vous devez transmettre le paramètre **-azureStackManagementURL** et le paramètre **-environmentName**. Par exemple :  
> `-azureStackManagementURL https://management.local.azurestack.external -environmentName AzureStack`

### <a name="create-an-access-key"></a>Créer une clé d’accès

Un principal du service nécessite une clé pour l’authentification. Effectuez les étapes suivantes pour générer une clé :

1. Dans **Inscriptions d’applications** dans Azure Active Directory, sélectionnez votre application.

    ![Sélectionner l’application - Azure Active Directory](media/azure-stack-solution-hybrid-pipeline/000_01.png)

2. Notez la valeur de **l’ID d’application**. Vous en aurez besoin au moment de la configuration du point de terminaison du service dans Azure DevOps Services.

    ![ID d’application - Azure Active Directory](media/azure-stack-solution-hybrid-pipeline/000_02.png)

3. Pour générer une clé d’authentification, sélectionnez **Paramètres**.

    ![Modifier les paramètres de l’application - Azure Active Directory](media/azure-stack-solution-hybrid-pipeline/000_03.png)

4. Pour générer une clé d’authentification, sélectionnez **Clés**.

    ![Configurer des paramètres de clé - Azure Active Directory](media/azure-stack-solution-hybrid-pipeline/000_04.png)

5. Fournissez une description de la clé et définissez la durée de la clé. Lorsque vous avez terminé, sélectionnez **Enregistrer**.

    ![Description et durée de la clé - Azure Active Directory](media/azure-stack-solution-hybrid-pipeline/000_05.png)

    Une fois la clé enregistrée, la clé **VALUE** s’affiche. Copiez cette valeur, car vous ne pourrez pas l’obtenir ultérieurement. Vous fournissez la **valeur de la clé** avec l’**ID d’application** pour vous connecter en tant qu’application. Stockez la valeur de la clé à un emplacement accessible par votre application.

    ![Valeur de la clé - Azure Active Directory](media/azure-stack-solution-hybrid-pipeline/000_06.png)

### <a name="get-the-tenant-id"></a>Obtenir l’ID de locataire

Dans le cadre de la configuration du point de terminaison de service, Azure DevOps Services a besoin de l’**ID de locataire** qui correspond à l’annuaire AAD sur lequel votre pile Azure Stack est déployée. Effectuez les étapes suivantes pour obtenir l’ID de locataire.

1. Sélectionnez **Azure Active Directory**.

    ![Azure Active Directory pour locataire](media/azure-stack-solution-hybrid-pipeline/000_07.png)

2. Pour obtenir l’ID de locataire, sélectionnez **Propriétés** pour votre client Azure AD.

    ![Afficher les propriétés du locataire - Azure Active Directory](media/azure-stack-solution-hybrid-pipeline/000_08.png)

3. Copiez l’**ID Directory**. Cette valeur est votre ID de locataire.

    ![ID d’annuaire - Azure Active Directory](media/azure-stack-solution-hybrid-pipeline/000_09.png)

### <a name="grant-the-service-principal-rights-to-deploy-resources-in-the-azure-stack-subscription"></a>Accorder au principal du service les droits nécessaires pour déployer des ressources dans l’abonnement Azure Stack

Pour accéder aux ressources de votre abonnement, vous devez attribuer un rôle à l’application. Choisissez le rôle qui représente les autorisations les plus appropriées pour l’application. Pour en savoir plus sur les rôles disponibles, consultez [RBAC : rôles intégrés](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Vous pouvez définir l’étendue au niveau de l’abonnement, du groupe de ressources ou de la ressource. Les autorisations sont héritées des niveaux inférieurs de l’étendue Par exemple, l’ajout d’une application au rôle Lecteur pour un groupe de ressources signifie que l’application peut lire le groupe de ressources et toutes ses ressources.

1. Accédez au niveau d’étendue que vous souhaitez affecter à l’application. Par exemple, pour affecter un rôle sur l’étendue de l’abonnement, sélectionnez **Abonnements**.

    ![Sélectionner des abonnements - Azure Stack](media/azure-stack-solution-hybrid-pipeline/000_10.png)

2. Dans **Abonnement**, sélectionnez Visual Studio Enterprise.

    ![Visual Studio Enterprise - Azure Stack](media/azure-stack-solution-hybrid-pipeline/000_11.png)

3. Dans Visual Studio Enterprise, sélectionnez **Contrôle d’accès (IAM)** .

4. Sélectionnez **Ajouter une attribution de rôle**.

    ![Ajouter une attribution de rôle - Azure Stack](media/azure-stack-solution-hybrid-pipeline/000_13.png)

5. Dans **Ajouter des autorisations**, sélectionnez le rôle que vous souhaitez attribuer à l’application. Dans cet exemple, il s’agit du rôle **Propriétaire**.

    ![Autorisations du rôle Propriétaire - Azure Stack](media/azure-stack-solution-hybrid-pipeline/000_14.png)

6. Par défaut, les applications Azure Active Directory ne figurent pas dans les options disponibles. Pour trouver votre application, vous devez la rechercher en entrant son nom dans le champ **Sélectionner**. Sélectionnez l’application.

    ![Résultat de la recherche d’application - Azure Stack](media/azure-stack-solution-hybrid-pipeline/000_16.png)

7. Sélectionnez **Enregistrer** pour finaliser l’attribution du rôle. Votre application apparaît dans la liste des utilisateurs affectés à un rôle pour cette étendue.

### <a name="role-based-access-control"></a>Contrôle d’accès en fonction du rôle

Le contrôle d’accès en fonction du rôle (RBAC) Azure offre une gestion précise de l’accès pour Azure. Grâce à RBAC, vous pouvez contrôler le niveau d’accès dont les utilisateurs ont besoin pour faire leur travail. Pour plus d’informations sur le contrôle d’accès en fonction du rôle, consultez [Gérer l’accès aux ressources d’un abonnement Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json).

### <a name="azure-devops-services-agent-pools"></a>Pools d'agents Azure DevOps Services

Au lieu de gérer chaque agent séparément, vous pouvez organiser les agents en pools d’agents. Un pool d’agents définit la limite de partage pour tous les agents de ce pool. Dans Azure DevOps Services, les pools d’agents s’appliquent à l’organisation Azure DevOps Services. En d’autres termes, vous pouvez partager un pool d’agents sur plusieurs projets. Pour en savoir plus sur les pools d’agents, consultez [Create Agent Pools and Queue](https://docs.microsoft.com/azure/devops/pipelines/agents/pools-queues?view=vsts) (Créer des pools d’agents et des files d’attente).

### <a name="add-a-personal-access-token-pat-for-azure-stack"></a>Ajouter un jeton d’accès personnel (PAT) pour Azure Stack

Créez un jeton d’accès personnel pour accéder à Azure DevOps Services.

1. Connectez-vous à votre organisation Azure DevOps Services et sélectionnez le nom de profil de votre organisation.

2. Sélectionnez **Gérer la sécurité** pour accéder à la page de création d’un jeton d’accès. 

    ![Gérer la sécurité - Azure Stack](media/azure-stack-solution-hybrid-pipeline/000_18.png)

3. Cliquez sur **Ajouter** pour créer un jeton d’accès personnel.

    ![Ajouter un jeton d’accès personnel - Azure Stack](media/azure-stack-solution-hybrid-pipeline/000_18a.png)

    ![Créer un jeton - Azure Stack](media/azure-stack-solution-hybrid-pipeline/000_18b.png)

4. Copiez le jeton.

    > [!Note]
    > Enregistrez les informations du jeton. Ces informations ne sont pas stockées et ne s’affichent plus lorsque vous quittez la page web.

    ![Jeton d’accès personnel - Azure Stack](media/azure-stack-solution-hybrid-pipeline/000_19.png)

### <a name="install-the-azure-devops-services-build-agent-on-the-azure-stack-hosted-build-server"></a>Installer l’agent de build Azure DevOps Services sur le serveur de builds hébergé sur Azure Stack

1. Connectez-vous au serveur de builds que vous avez déployé sur l’hôte Azure Stack.
2. Téléchargez et déployez l’agent de build comme service en utilisant votre jeton d’accès personnel, et exécutez-le en tant qu’administrateur de la machine virtuelle.

    ![Télécharger l’agent de build](media/azure-stack-solution-hybrid-pipeline/010_downloadagent.png)

3. Accédez au dossier de l’agent de build extrait. Exécutez le fichier **config.cmd** à partir d’une invite de commandes avec élévation de privilèges.

    ![Agent de build extrait](media/azure-stack-solution-hybrid-pipeline/000_20.png)

    ![Inscrire l’agent de build](media/azure-stack-solution-hybrid-pipeline/000_21.png)

4. Une fois l’exécutée de config.cmd terminée, le dossier de l’agent de build est mis à jour avec des fichiers supplémentaires. Le dossier avec le contenu extrait doit se présenter comme dans cet exemple :

    ![Mise à jour du dossier de l’agent de build](media/azure-stack-solution-hybrid-pipeline/009_token_file.png)

    Vous pouvez voir l’agent dans le dossier Azure DevOps Services.

## <a name="endpoint-creation-permissions"></a>Autorisations de création des points de terminaison

Grâce à la création de points de terminaison, une build de Visual Studio Online (VSTO) peut déployer des applications Azure Service dans Azure Stack. Azure DevOps Services se connecte à l’agent de build, qui se connecte à Azure Stack.

![Exemple d’application NorthwindCloud dans VSTO](media/azure-stack-solution-hybrid-pipeline/012_securityendpoints.png)

1. Connectez-vous à VSTO et accédez à la page de paramètres de l’application.
2. Dans **Paramètres**, sélectionnez **Sécurité**.
3. Dans **Groupes Azure DevOps Services**, sélectionnez **Créateurs de points de terminaison**.

    ![Créateurs de points de terminaison NorthwindCloud](media/azure-stack-solution-hybrid-pipeline/013_endpoint_creators.png)

4. Sous l’onglet **Membres**, sélectionnez **Ajouter**.

    ![Ajouter un membre](media/azure-stack-solution-hybrid-pipeline/014_members_tab.png)

5. Dans la page **Ajouter des utilisateurs et groupes**, entrez un nom d’utilisateur et sélectionnez cet utilisateur dans la liste des utilisateurs.
6. Sélectionnez **Enregistrer les modifications**.
7. Dans la liste **Groupes Azure DevOps Services**, sélectionnez **Administrateurs de points de terminaison**.

    ![Administrateurs du point de terminaison NorthwindCloud](media/azure-stack-solution-hybrid-pipeline/015_save_endpoint.png)

8. Sous l’onglet **Membres**, sélectionnez **Ajouter**.
9. Dans la page **Ajouter des utilisateurs et groupes**, entrez un nom d’utilisateur et sélectionnez cet utilisateur dans la liste des utilisateurs.
10. Sélectionnez **Enregistrer les modifications**.

Maintenant que les informations du point de terminaison existent, la connexion de Azure DevOps Services à Azure Stack est prête à être utilisée. Dans Azure Stack, l’agent de build reçoit des instructions d’Azure DevOps Services, puis il transmet les informations du point de terminaison pour la communication avec Azure Stack.

## <a name="create-an-azure-stack-endpoint"></a>Créer un point de terminaison Azure Stack

### <a name="create-an-endpoint-for-azure-ad-deployments"></a>Créer un point de terminaison pour les déploiements Azure AD

Pour créer une connexion au service avec un principal du service existant, suivez les instructions dans l’article [Créer une connexion au service Azure Resource Manager avec un principal du service existant](https://docs.microsoft.com/vsts/pipelines/library/connect-to-azure?view=vsts#create-an-azure-resource-manager-service-connection-with-an-existing-service-principal) et utilisez le mappage suivant :

| Nom | Exemples | Description |
| --- | --- | --- |
| Nom de connexion | Azure Stack Azure AD | Le nom de la connexion. |
| Environnement | AzureStack | Le nom de votre environnement. |
| URL d’environnement | `https://management.local.azurestack.external` | Votre point de terminaison de gestion. |
| Niveau de portée | Subscription | La portée de la connexion. |
| Identifiant d’abonnement | 65710926-XXXX-4F2A-8FB2-64C63CD2FAE9 | ID d’abonnement de l’utilisateur d’Azure Stack |
| Nom d’abonnement | name@contoso.com | Nom d’abonnement de l’utilisateur d’Azure Stack. |
| ID client du principal du service | FF74AACF-XXXX-4776-93FC-C63E6E021D59 | ID du principal de [cette](azure-stack-solution-pipeline.md#create-a-service-principal) section dans cet article. |
| Clé du principal du service | THESCRETGOESHERE = | La clé du même article (ou le mot de passe si vous avez utilisé le script). |
| ID client | D073C21E-XXXX-4AD0-B77E-8364FCA78A94 | ID du locataire récupéré en suivant les instructions dans [Obtenir l’ID du locataire](azure-stack-solution-pipeline.md#get-the-tenant-id).  |
| Connexion : | Non vérifié | Validez vos paramètres de connexion au principal du service. |

Maintenant que le point de terminaison est créé, la connexion de DevOps à Azure Stack est prête pour l’utilisation. L’agent de build dans Azure Stack obtient des instructions de DevOps, puis l’agent transmet les informations du point de terminaison pour la communication avec Azure Stack.

![Agent de build Azure AD](media/azure-stack-solution-hybrid-pipeline/016_save_changes.png)

### <a name="create-an-endpoint-for-ad-fs"></a>Créer un point de terminaison pour AD FS

Avec la dernière mise à jour d’Azure DevOps, vous pouvez créer une connexion de service en utilisant un principal du service avec un certificat d’authentification. Cette connexion est nécessaire quand Azure Stack est déployé avec AD FS en tant que fournisseur d’identité. 

![Agent de build AD FS](media/azure-stack-solution-hybrid-pipeline/image06.png)

Vous pouvez créer une connexion de service en utilisant le mappage suivant :

| Nom | Exemples | Description |
| --- | --- | --- |
| Nom de connexion | Azure Stack ADFS | Le nom de la connexion. |
| Environnement | AzureStack | Le nom de votre environnement. |
| URL d’environnement | `https://management.local.azurestack.external` | Votre point de terminaison de gestion. |
| Niveau de portée | Subscription | La portée de la connexion. |
| Identifiant d’abonnement | 65710926-XXXX-4F2A-8FB2-64C63CD2FAE9 | ID d’abonnement de l’utilisateur d’Azure Stack |
| Nom d’abonnement | name@contoso.com | Nom d’abonnement de l’utilisateur d’Azure Stack. |
| ID client du principal du service | FF74AACF-XXXX-4776-93FC-C63E6E021D59 | L’ID client du principal du service que vous avez créé pour AD FS. |
| Certificat | `<certificate>` |  Convertissez le fichier de certificat PFX en PEM. Collez le contenu du fichier de certificat PEM dans ce champ. <br> Conversion de PFX en PEM :<br>`openssl pkcs12 -in file.pfx -out file.pem -nodes -password pass:<password_here>` |
| ID client | D073C21E-XXXX-4AD0-B77E-8364FCA78A94 | ID du locataire récupéré en suivant les instructions dans [Obtenir l’ID du locataire](azure-stack-solution-pipeline.md#get-the-tenant-id). |
| Connexion : | Non vérifié | Validez vos paramètres de connexion au principal du service. |

Maintenant que le point de terminaison est créé, la connexion d’Azure DevOps à Azure Stack est prête pour l’utilisation. Dans Azure Stack, l’agent de build reçoit des instructions d’Azure DevOps, puis il transmet les informations du point de terminaison pour la communication avec Azure Stack.

> [!Note]
> Si votre point de terminaison Azure Resource Manager n’est pas exposé à Internet, la validation de la connexion échoue. Ce comportement est attendu. Pour valider votre connexion, vous pouvez créer un pipeline de mise en production avec une tâche simple. 

## <a name="develop-your-application-build"></a>Développer votre build d’application

Dans cette partie de la solution, vous allez :

* Ajouter du code à un projet Azure DevOps Services.
* Créer un déploiement d’application web autonome.
* Configurer le processus de déploiement continu

> [!Note]
 > Votre environnement Azure Stack a besoin des images correctes syndiquées pour exécuter Windows Server et SQL Server. Le service App Service doit également y être déployé. Lisez la section « Prérequis » de la documentation d’App Service pour connaître la configuration requise de l’opérateur Azure Stack.

La CI/CD hybride peut s’appliquer au code d’application et au code d’infrastructure. Utilisez des [modèles Azure Resource Manager](https://azure.microsoft.com/resources/templates/) comme code d’application web d’Azure DevOps Services pour déployer sur les deux clouds.

### <a name="add-code-to-an-azure-devops-services-project"></a>Ajouter du code à un projet Azure DevOps Services

1. Connectez-vous à Azure DevOps Services avec une organisation disposant des droits de création de projet dans Azure Stack. La capture d’écran suivante montre comment se connecter au projet HybridCICD.

    ![Se connecter à un projet - Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/017_connect_to_project.png)

2. **Clonez le référentiel** en créant et en ouvrant l’application web par défaut.

    ![Cloner le dépôt - Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/018_link_arm.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Créer un déploiement d’applications web autonomes pour App Services dans les deux clouds

1. Modifiez le fichier **WebApplication.csproj** : sélectionnez `Runtimeidentifier`, puis ajoutez `win10-x64.`. Pour plus d’informations, consultez la documentation [Déploiements autonomes](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd).

    ![Configurer Runtimeidentifier](media/azure-stack-solution-hybrid-pipeline/019_runtimeidentifer.png)

2. Utilisez Team Explorer pour archiver le code dans Azure DevOps Services.

3. Vérifiez que le code d’application a été archivé dans Azure DevOps Services.

### <a name="create-the-build-pipeline"></a>Créer le pipeline de build

1. Connectez-vous à Azure DevOps Services à l’aide d’une organisation disposant des droits de création de pipeline de build.

2. Accédez à la page **Build Web Application** (Créer une application web) du projet.

3. Dans **Arguments**, ajoutez le code **-r win10-x64**. Cette étape est obligatoire pour déclencher un déploiement autonome avec .NET Core.

    ![Ajouter un pipeline de build d’argument](media/azure-stack-solution-hybrid-pipeline/020_publish_additions.png)

4. Exécutez la build. Le processus de [build de déploiement autonome](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) publie des artefacts qui peuvent s’exécuter sur Azure et Azure Stack.

### <a name="use-an-azure-hosted-build-agent"></a>Utiliser un agent de build hébergé dans Azure

L’utilisation d’un agent de build hébergé dans Azure DevOps Services est une option pratique pour créer et déployer des applications web. La maintenance et les mises à niveau de l’agent sont effectuées automatiquement par Microsoft Azure, ce qui permet un cycle de développement continu et sans interruption.

### <a name="configure-the-continuous-deployment-cd-process"></a>Configurer le processus de déploiement continu (CD)

Azure DevOps Services et Team Foundation Server (TFS) fournissent un pipeline hautement configurable et gérable pour des mises en production sur plusieurs environnements (développement, préproduction, assurance qualité (AQ) et production, par exemple). Ce processus peut inclure la nécessite d’approbations à des étapes spécifique du cycle de vie de l’application.

### <a name="create-release-pipeline"></a>Créer un pipeline de mise en production

La création d’un pipeline de mise en production est la dernière étape du processus de création d’application. Ce pipeline de mise en production est utilisé pour créer une mise en production et déployer une build.

1. Connectez-vous à Azure DevOps Services et accédez aux **Pipelines Azure** pour votre projet.
2. Sous l’onglet **Versions**, sélectionnez  **\[ +]** , puis choisissez **Créer une définition de mise en production**.

   ![Créer un pipeline de mise en production - Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/021a_releasedef.png)

3. Dans la page **Sélectionner un modèle**, choisissez **Déploiement d’Azure App Service**, puis sélectionnez **Appliquer**.

    ![Appliquer un modèle - Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/102.png)

4. Dans la page **Ajouter un artefact**, dans le menu déroulant **Source (définition de build)** , sélectionnez l’application de build Azure Cloud.

    ![Ajouter un artefact - Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/103.png)

5. Sous l’onglet **Pipeline**, sélectionnez le lien **1 Phase**, **1 Tâche** vers **Afficher les tâches d’environnement**.

    ![Afficher les tâches du pipeline - Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/104.png)

6. Sous l’onglet **Tâches**, entrez Azure comme **nom d’environnement** et sélectionnez AzureCloud Traders-Web EP dans la liste déroulante **Abonnement Azure**.

    ![Définir les variables d’environnement - Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/105.png)

7. Entrez le **nom de l’Azure App Service**, qui est « northwindtraders » dans la capture d’écran suivante.

    ![Nom de l’App Service - Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/106.png)

8. Pour la phase d’agent, sélectionnez **VS2017 hébergé** dans la liste déroulante **File d’attente d’agents**.

    ![Agent hébergé - Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/107.png)

9. Dans **Déployer Azure App Service**, sélectionnez le **package ou dossier** valide pour l’environnement.

    ![Sélectionner un package ou un dossier - Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/108.png)

10. Dans la page **Sélectionner un fichier ou un dossier**, sélectionnez **OK** pour l’emplacement du dossier.

    ![Sélectionner un fichier ou un dossier - Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/109.png)

11. Enregistrez toutes les modifications et revenez à **Pipeline**.

    ![Enregistrer les modifications - Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/110.png)

12. Sous l’onglet **Pipeline**, sélectionnez **Ajouter un artefact** et choisissez **NorthwindCloud Traders-Vessel** dans la liste déroulante **Source (définition de build)** .

    ![Ajouter un nouvel artefact - Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/111.png)

13. Dans la page **Sélectionner un modèle**, ajoutez un autre environnement. Choisissez **Déploiement d'Azure App Service**, puis sélectionnez **Appliquer**.

    ![Sélectionner un modèle - Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/112.png)

14. Entrez « Azure Stack » comme **nom d’environnement**.

    ![Nom de l’environnement - Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/113.png)

15. Sous l’onglet **Tâches**, recherchez et sélectionnez Azure Stack.

    ![Environnement Azure Stack - Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/114.png)

16. Dans la liste déroulante **Abonnement Azure**, sélectionnez « AzureStack Traders-Vessel EP » pour le point de terminaison Azure Stack.

    ![Liste déroulante Abonnement Azure - Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/115.png)

17. Entrez le nom de l’application web Azure Stack comme **nom de l’App Service**.

    ![Nom de l’App Service - Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/116.png)

18. Sous **Sélection de l’Agent**, choisissez « AzureStack -bDouglas Fir » dans la liste déroulante **file d’attente d’agents**.

    ![Sélectionner un agent - Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/117.png)

19. Pour **Déployer Azure App Service**, sélectionnez le **package ou dossier** valide pour l’environnement. Sous **Sélectionner un fichier ou un dossier**, sélectionnez **OK** pour le dossier **Emplacement**.

    ![Sélectionner un package ou un dossier - Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/118.png)

    ![Approuver l’emplacement - Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/119.png)

20. Sous l’onglet **Variables**, recherchez la variable nommée **VSTS_ARM_REST_IGNORE_SSL_ERRORS**. Définissez la valeur de la variable sur **true** et définissez sa portée sur **Azure Stack**.

    ![Configurer une variable - Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/120.png)

21. Sous l’onglet **Pipeline**, sélectionnez l’icône **Déclencheur de déploiement continu** pour l’artefact NorthwindCloud Traders-Web et définissez le **Déclencheur de déploiement continu** sur **Activé**.  Procédez de la même manière pour l’artefact « NorthwindCloud Traders-Vessel ».

    ![Définir le déclencheur de déploiement continu - Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/121.png)

22. Pour l’environnement Azure Stack, sélectionnez l’icône **Conditions préalables au déploiement** et définissez le déclencheur sur **Après la mise en production**.

    ![Définir le déclencheur de conditions préalables au déploiement - Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/122.png)

23. Enregistrez toutes vos modifications.

> [!Note]
> Il se peut que certains paramètres des tâches de mise en production aient été définis automatiquement en tant que [variables d’environnement](https://docs.microsoft.com/azure/devops/pipelines/release/variables?view=vsts#custom-variables) lors de la création du pipeline de mise en production à partir d’un modèle. Ces paramètres ne peuvent pas être modifiés dans les paramètres de tâche. Toutefois, vous pouvez modifier ces paramètres dans les éléments d’environnement parent.

## <a name="create-a-release"></a>Créer une mise en production

Maintenant que vous avez terminé les modifications du pipeline de mise en production, il est temps de commencer le déploiement. Pour commencer le déploiement, créez une mise en production à partir du pipeline de mise en production. Une mise en production peut être créée automatiquement, par exemple, quand le déclencheur de déploiement continu est défini dans le pipeline de mise en production. Quand ce déclencheur est défini, la modification du code source démarre une nouvelle build et ensuite une nouvelle mise en production. Dans cette section, vous créez manuellement une nouvelle mise en production.

1. Sous l’onglet **Pipeline**, ouvrez la liste déroulante **Version finale** et sélectionnez **Créer une mise en production**.

    ![Créer une mise en production - Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/200.png)

2. Entrez une description de la mise en production, vérifiez que les artefacts corrects sont sélectionnés, puis sélectionnez **Créer**. Après quelques instants, une bannière s’affiche, indiquant que la nouvelle mise en production a été créée ; le nom de la mise en production est affichée sous forme de lien. Sélectionnez le lien pour consulter la page récapitulative de la mise en production.

    ![Bannière de création de mise en production - Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/201.png)

3. La page récapitulative de mise en production fournit des détails sur la mise en production. Dans la capture d’écran suivante pour « Release-2 », la section **Environnements** indique que l’**état du déploiement** d’Azure est « IN PROGRESS » (En cours) et que l’état d’Azure Stack est « SUCCEEDED » (Réussi). Lorsque l’état du déploiement de l’environnement Azure passe à « SUCCEEDED » (Réussi), une bannière indiquant que la mise en production est prête pour l’approbation s’affiche. Lorsqu’un déploiement est en attente ou a échoué, une icône d’informations **(i)** bleue est affichée. Pointez sur l’icône pour afficher une fenêtre contextuelle qui indique le motif du retard ou de l’échec.

    ![Page récapitulative de la mise en production - Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/202.png)

D’autres vues, comme la liste des mises en production, affichent aussi une icône indiquant qu’une approbation est en attente. La fenêtre contextuelle de cette icône indique le nom de l’environnement et plus de détails sur le déploiement. Un administrateur peut facilement voir la progression globale des mises en production et savoir quelles mises en production sont en attente d’approbation.

### <a name="monitor-and-track-deployments"></a>Surveiller et suivre les déploiements

Cette section montre comment vous pouvez surveiller et suivre tous vos déploiements. La mise en production pour le déploiement de deux sites web Azure App Services en est un bon exemple.

1. Sur la page récapitulative « Release-2 », sélectionnez **Journaux d’activité**. Pendant un déploiement, cette page affiche le journal en direct de l’agent. Le volet gauche indique l’état de chaque opération du déploiement pour chaque environnement.

    Sélectionnez l’icône représentant une personne dans la colonne **Action** pour une approbation de prédéploiement ou de postdéploiement afin de voir qui a approuvé (ou rejeté) le déploiement ainsi que le message entré par les utilisateurs.

2. Lorsque le déploiement est terminé, l’intégralité du fichier journal s’affiche dans le volet droit. Sélectionnez une **étape** dans le volet gauche pour voir le fichier journal d’une étape spécifique comme « Initialiser le travail ». La possibilité de voir les journaux d’activité individuels facilite le suivi et le débogage de parties du déploiement global. Vous pouvez également **enregistrer** le fichier journal d’une étape ou **Télécharger tous les journaux d’activité au format zip**.

    ![Journaux de mise en production - Azure DevOps Services](media/azure-stack-solution-hybrid-pipeline/203.png)

3. Ouvrez l’onglet **Résumé** pour afficher des informations générales sur la mise en production. Cette vue montre les détails de la build, les environnements où elle a été déployée, l’état du déploiement et d’autres informations sur la mise en production.

4. Sélectionnez un lien d’environnement (**Azure** ou **Azure Stack**) pour afficher des informations sur les déploiements existants et en attente dans un environnement spécifique. Vous pouvez utiliser ces vues pour vérifier rapidement que la même build a été déployée sur les deux environnements.

5. Ouvrez l’**application de production déployée** dans votre navigateur. Par exemple, pour le site web Azure App Services, ouvrez l’URL `https://[your-app-name].azurewebsites.net`.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les modèles Azure Cloud, consultez [Modèles de conception cloud](https://docs.microsoft.com/azure/architecture/patterns).
