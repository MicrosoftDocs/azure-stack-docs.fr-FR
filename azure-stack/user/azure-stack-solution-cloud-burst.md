---
title: Créer des solutions de mise à l’échelle dans le cloud avec Azure | Microsoft Docs
description: Découvrez comment créer des solutions de mise à l’échelle dans le cloud avec Azure.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/14/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: adbe1eba6c5d852466288ddf41c803072d4cd098
ms.sourcegitcommit: 261df5403ec01c3af5637a76d44bf030f9342410
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66252079"
---
# <a name="tutorial-create-cross-cloud-scaling-solutions-with-azure"></a>Tutoriel : Créer des solutions de mise à l’échelle dans le cloud avec Azure

*S’applique à : Systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Découvrez comment créer une solution dans le cloud pour fournir un processus déclenché manuellement permettant de passer d’une application web hébergée sur Azure Stack à une application web hébergée sur Azure avec mise à l’échelle automatique via le gestionnaire de trafic, garantissant un utilitaire cloud flexible et évolutif en cas de charge.

Avec ce modèle, votre locataire n’est peut-être pas prêt à exécuter votre application dans le cloud public. Toutefois, ceci peut ne pas être économiquement faisable pour que l’entreprise puisse maintenir la capacité nécessaire dans son environnement local afin de gérer les pics de demande de l’application. Votre locataire peut utiliser l’élasticité du cloud public dans sa solution locale.

Dans ce tutoriel, vous créez un exemple d’environnement pour :

> [!div class="checklist"]
> - Créer une application web à plusieurs nœuds.
> - Configurer et gérer le processus de déploiement continu (CD).
> - Publier l’application web dans Azure Stack.
> - Créer une mise en production.
> - Apprenez à surveiller et à suivre vos déploiements.

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack est une extension d’Azure. Azure Stack apporte l’agilité et l’innovation du cloud computing à votre environnement local et active le seul cloud hybride qui vous permet de créer et de déployer des applications hybrides en tout lieu.  
> 
> Le livre blanc [Design Considerations for Hybrid Applications](https://aka.ms/hybrid-cloud-applications-pillars) (Étude des conceptions pour les applications hybrides) se penche sur les fondements de la qualité logicielle (sélection élective, extensibilité, disponibilité, résilience, facilité de gestion et sécurité) en matière de conception, de déploiement et d’exploitation des applications hybrides. Les considérations de conception vous aident à optimiser la conception des applications hybrides, en réduisant les risques dans les environnements de production.

## <a name="prerequisites"></a>Prérequis

-   Abonnement Azure. Si nécessaire, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

- Système intégré Azure Stack ou déploiement du Kit de développement Azure Stack.
    - La page [Installer le Kit de développement Azure Stack](../asdk/asdk-install.md) comporte des instructions permettant d’installer Azure Stack.
    - [https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1) Cette installation peut nécessiter plusieurs heures.

-   Déployez les services PaaS [App Service](../operator/azure-stack-app-service-deploy.md) sur Azure Stack.

-   [Créez un plan/des offres](../operator/azure-stack-plan-offer-quota-overview.md) dans l’environnement Azure Stack.

-   [Créez un abonnement client](../operator/azure-stack-subscribe-plan-provision-vm.md) dans l’environnement Azure Stack.

-   Créez une application web au sein de l’abonnement du locataire. Notez l’URL de la nouvelle application web, car elle sera utilisée plus tard.

-   Déployez la machine virtuelle Azure Pipelines au sein de l’abonnement du locataire.

-   Une machine virtuelle Windows Server 2016 avec .NET 3.5 est nécessaire. Cette machine virtuelle est créée dans l’abonnement du locataire sur Azure Stack en tant qu’agent de build privé.

-   [Windows Server 2016 avec l’image de machine virtuelle SQL 2017](../operator/azure-stack-add-vm-image.md#add-a-vm-image-through-the-portal) est disponible dans la Place de marché Azure Stack. Si cette image n’est pas disponible, utilisez un opérateur Azure Stack pour vous assurer qu’elle est ajoutée à l’environnement.

## <a name="issues-and-considerations"></a>Problèmes et considérations

### <a name="scalability-considerations"></a>Considérations relatives à l’extensibilité

Le principal composant de la mise à l’échelle dans le cloud est la capacité à fournir une mise à l’échelle à la demande immédiate entre l’infrastructure de cloud public et locale, garantissant un service fiable et cohérent comme stipulé par la demande.

### <a name="availability-considerations"></a>Considérations relatives à la disponibilité

Vérifiez que les applications déployées en local sont configurées pour la haute disponibilité via la configuration matérielle locale et le déploiement de logiciels.

### <a name="manageability-considerations"></a>Considérations relatives à la facilité de gestion

La solution dans le cloud garantit une gestion transparente et une interface familière entre les environnements. PowerShell est recommandé pour une gestion multiplateforme.

## <a name="cross-cloud-scaling"></a>Mise à l’échelle dans le cloud

### <a name="obtain-a-custom-domain-and-configure-dns"></a>Obtenir un domaine personnalisé et configurer DNS

Mettez à jour le fichier de zone DNS pour le domaine. Azure AD vérifie la propriété du nom de domaine personnalisé. Utilisez [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) pour les enregistrements DNS Azure/Office 365/externes dans Azure, ou ajoutez l’entrée DNS à [un autre bureau d’enregistrement DNS](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

1.  Inscrivez un domaine personnalisé auprès d’un bureau d’enregistrement public.

2.  Connectez-vous au Bureau d’enregistrement des noms de domaine pour le domaine. Il se peut qu’un administrateur approuvé doive effectuer les mises à jour DNS. 

3.  Mettez à jour le fichier de zone DNS du domaine en ajoutant l’entrée DNS fournie par Azure AD. (L’entrée DNS n’affectera pas le routage du courrier ni les comportements d’hébergement web.) 

### <a name="create-a-default-multi-node-web-app-in-azure-stack"></a>Créer une application web à plusieurs nœuds par défaut dans Azure Stack

Configurez l’intégration continue et le déploiement continu (CI/CD) hybride pour déployer Web App sur Azure et Azure Stack, et pour envoyer (push) les modifications sur les deux clouds.

> [!Note]  
> Vous avez besoin d’Azure Stack avec les images appropriées syndiquées pour s’exécuter (Windows Server et SQL), et App Service doit être déployé. Pour l’opérateur Azure Stack, consultez la section «[ Avant de commencer avec App Service sur Azure Stack ](../operator/azure-stack-app-service-before-you-get-started.md)» dans la documentation d’App Service.

### <a name="add-code-to-azure-repos"></a>Ajouter du code à Azure Repos

Azure Repos

1. Connectez-vous à Azure Repos avec un compte ayant les droits de création de projet sur Azure Repos.

    La CI/CD hybride peut s’appliquer au code d’application et au code d’infrastructure. Utilisez les [modèles Azure Resource Manager](https://azure.microsoft.com/resources/templates/) pour les développements cloud privé et hébergé.

    ![Alt text](media/azure-stack-solution-cloud-burst/image1.JPG)

2. **Clonez le référentiel** en créant et en ouvrant l’application web par défaut.

    ![Alt text](media/azure-stack-solution-cloud-burst/image2.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Créer un déploiement d’applications web autonomes pour App Services dans les deux clouds

1.  Modifiez le fichier **WebApplication.csproj**. Sélectionnez **Runtimeidentifier** et ajoutez **win10-x64**. (Consultez la documentation sur le [déploiement autonome](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd).) 

    ![Alt text](media/azure-stack-solution-cloud-burst/image3.png)

2.  Archivez le code dans Azure Repos avec Team Explorer.

3.  Vérifiez que le code d’application a été archivé dans Azure Repos.

## <a name="create-the-build-definition"></a>Créer la définition de build

1. Connectez-vous à Azure Pipelines pour vérifier la possibilité de créer des définitions de build.

2. Ajoutez le code **-r win10-x64**. Cette action est nécessaire pour déclencher un déploiement autonome avec .NET Core.

    ![Alt text](media/azure-stack-solution-cloud-burst/image4.png)

3. Exécutez la build. Le processus de [build de déploiement autonome](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) publie des artefacts qui peuvent s’exécuter sur Azure et Azure Stack.

## <a name="use-an-azure-hosted-agent"></a>Utiliser un agent hébergé sur Azure

L’utilisation d’un agent hébergé dans Azure Pipelines est une option pratique pour créer et déployer des applications web. La maintenance et les mises à niveau sont effectuées automatiquement par Microsoft Azure, ce qui permet le développement, le test et le déploiement continus et sans interruption.

### <a name="manage-and-configure-the-cd-process"></a>Gérer et configurer le processus CD

Azure Pipelines et Azure DevOps Server fournissent un pipeline hautement configurable et gérable pour des mises en production sur plusieurs environnements, comme des environnements de développement, de préproduction, d’assurance qualité et de production, avec des demandes d’approbation à des étapes spécifiques.

## <a name="create-release-definition"></a>Créer une définition de mise en production

![Alt text](media/azure-stack-solution-cloud-burst/image5.png)

1.  Sélectionnez le bouton **plus** pour ajouter une nouvelle mise en production sous l’onglet **Mises en production** dans la page Build et mise en production de VSO.

    ![Alt text](media/azure-stack-solution-cloud-burst/image6.png)

2. Appliquez le modèle de déploiement Azure App Service.

    ![Alt text](media/azure-stack-solution-cloud-burst/image7.png)

3. Sous Ajouter un artefact, ajoutez l’artefact pour l’application de build Azure Cloud.

    ![Alt text](media/azure-stack-solution-cloud-burst/image8.png)

4. Sous l’onglet Pipeline, sélectionnez le lien **Phase, Tâche** de l’environnement et définissez les valeurs de l’environnement cloud Azure.

    ![Alt text](media/azure-stack-solution-cloud-burst/image9.png)

5. Définissez le **nom de l’environnement** et sélectionnez **Abonnement** Azure pour le point de terminaison Azure Cloud.

    ![Alt text](media/azure-stack-solution-cloud-burst/image10.png)

6. Sous le nom de l’environnement, définissez le **nom de l’App Service Azure**.

    ![Alt text](media/azure-stack-solution-cloud-burst/image11.png)

7. Entrez **VS2017 hébergé** sous la file d’attente de l’agent pour l’environnement hébergé dans le cloud Azure.

    ![Alt text](media/azure-stack-solution-cloud-burst/image12.png)

8. Dans le menu Déployer Azure App Service, sélectionnez **le package ou le dossier** valide pour l’environnement. Sélectionnez **OK** pour **l’emplacement du dossier**.

    ![Alt text](media/azure-stack-solution-cloud-burst/image13.png)

    ![Alt text](media/azure-stack-solution-cloud-burst/image14.png)

9. Enregistrez toutes les modifications et revenez au **pipeline de mises en production**.

    ![Alt text](media/azure-stack-solution-cloud-burst/image15.png)

10. Ajoutez un nouvel artefact en sélectionnant la build pour l’application Azure Stack.

    ![Alt text](media/azure-stack-solution-cloud-burst/image16.png)

11. Ajoutez un autre environnement en appliquant le déploiement Azure App Service.

    ![Alt text](media/azure-stack-solution-cloud-burst/image17.png)

12. Nommez le nouvel environnement Azure Stack.

    ![Alt text](media/azure-stack-solution-cloud-burst/image18.png)

13. Recherchez l’environnement Azure Stack sous l’onglet **Tâche**.

    ![Alt text](media/azure-stack-solution-cloud-burst/image19.png)

14. Sélectionnez l’abonnement pour le point de terminaison Azure Stack.

    ![Alt text](media/azure-stack-solution-cloud-burst/image20.png)

15. Définissez le nom de l’application web Azure Stack comme nom de l’App Service.

    ![Alt text](media/azure-stack-solution-cloud-burst/image21.png)

16. Sélectionnez l’agent Azure Stack.

    ![Alt text](media/azure-stack-solution-cloud-burst/image22.png)

17. Sous la section Déployer Azure App Service, sélectionnez **le package ou le dossier** valide pour l’environnement. Sélectionnez **OK** pour l’emplacement du dossier.

    ![Alt text](media/azure-stack-solution-cloud-burst/image23.png)

    ![Alt text](media/azure-stack-solution-cloud-burst/image24.png)

18. Sous l’onglet Variable, ajoutez une variable nommée `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS`, définissez sa valeur sur **true** et sa portée sur Azure Stack.

    ![Alt text](media/azure-stack-solution-cloud-burst/image25.png)

19. Sélectionnez l’icône du déclencheur de déploiement **Continu** dans les deux artefacts et activez le déclencheur de déploiement **Continu**.

    ![Alt text](media/azure-stack-solution-cloud-burst/image26.png)

20. Sélectionnez l’icône des conditions **Prédéploiement** dans l’environnement Azure Stack et définissez le déclencheur sur **Après la mise en production**.

21. Enregistrez toutes les modifications.

> [!Note]  
> Certains paramètres des tâches peuvent avoir été automatiquement définis en tant que [variables d’environnement](https://docs.microsoft.com/azure/devops/pipelines/release/variables?view=vsts&tabs=batch#custom-variables) lors de la création d’une définition de mise en production à partir d’un modèle. Ces paramètres ne peuvent pas être modifiés dans les paramètres de la tâche ; l’élément de l’environnement parent doit être sélectionner pour modifier ces paramètres

## <a name="publish-to-azure-stack-via-visual-studio"></a>Publier sur Azure Stack via Visual Studio

Grâce à la création de points de terminaison, une build de Visual Studio Online (VSTO) peut déployer des applications Azure Service dans Azure Stack. Azure Pipelines se connecte à l’agent de build, qui se connecte à Azure Stack.

1.  Connectez-vous à VSTO et accédez à la page de paramètres de l’application.

2.  Dans **Paramètres**, sélectionnez **Sécurité**.

3.  Dans **Groupes VSTS**, sélectionnez **Créateurs de points de terminaison**.

4.  Sous l’onglet **Membres**, sélectionnez **Ajouter**.

5.  Dans **Ajouter des utilisateurs et groupes**, entrez un nom d’utilisateur et sélectionnez cet utilisateur dans la liste des utilisateurs.

6.  Sélectionnez **Enregistrer les modifications**.

7.  Dans la liste **Groupes VSTS**, sélectionnez **Administrateurs du point de terminaison**.

8.  Sous l’onglet **Membres**, sélectionnez **Ajouter**.

9.  Dans **Ajouter des utilisateurs et groupes**, entrez un nom d’utilisateur et sélectionnez cet utilisateur dans la liste des utilisateurs.

10. Sélectionnez **Enregistrer les modifications**.

Maintenant que les informations du point de terminaison ont été ajoutées, la connexion entre Azure Pipelines et Azure Stack est prête à être utilisée. L’agent de build dans Azure Stack reçoit des instructions d’Azure Pipelines, puis il transmet les informations du point de terminaison pour la communication avec Azure Stack.

## <a name="develop-the-application-build"></a>Développer la build de l’application

> [!Note]  
> Vous avez besoin d’Azure Stack avec les images appropriées syndiquées pour s’exécuter (Windows Server et SQL), et App Service doit être déployé. Pour l’opérateur Azure Stack, consultez la section «[ Avant de commencer avec App Service sur Azure Stack ](../operator/azure-stack-app-service-before-you-get-started.md)» dans la documentation d’App Service.

Utilisez des [modèles Azure Resource Manager](https://azure.microsoft.com/resources/templates/) comme du code d’application web d’Azure Repos pour le déploiement dans les deux clouds.

### <a name="add-code-to-an-azure-repos-project"></a>Ajouter du code à un projet Azure Repos

1.  Connectez-vous à Azure Repos avec un compte ayant les droits de création de projet sur Azure Stack. La capture d’écran suivante montre comment se connecter au projet HybridCICD.

2.  **Clonez le référentiel** en créant et en ouvrant l’application web par défaut.

#### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Créer un déploiement d’applications web autonomes pour App Services dans les deux clouds

1.  Modifiez le fichier **WebApplication.csproj** : Sélectionnez **Runtimeidentifier**, puis ajoutez win10-x64. Pour plus d’informations, consultez la documentation sur le [déploiement autonome](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd).

2.  Utilisez Team Explorer pour archiver le code dans Azure Repos.

3.  Vérifiez que le code d’application a été archivé dans Azure Repos.

### <a name="create-the-build-definition"></a>Créer la définition de build

1.  Connectez-vous à Azure Pipelines avec un compte permettant de créer une définition de build.

2.  Accédez à la page **Build Web Application** (Créer une application web) du projet.

3.  Dans **Arguments**, ajoutez le code **-r win10-x64**. Cette action est requise pour déclencher un déploiement autonome avec .NET Core.

4.  Exécutez la build. Le processus de [build de déploiement autonome](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) publie des artefacts qui peuvent s’exécuter sur Azure et Azure Stack.

#### <a name="use-an-azure-hosted-build-agent"></a>Utiliser un agent de build hébergé Azure

L’utilisation d’un agent de build hébergé dans Azure Pipelines est une option pratique pour créer et déployer des applications web. La maintenance et les mises à niveau de l’agent sont effectuées automatiquement par Microsoft Azure, ce qui permet un cycle de développement continu et sans interruption.

### <a name="configure-the-continuous-deployment-cd-process"></a>Configurer le processus de déploiement continu (CD)

Azure Pipelines et Azure DevOps Server fournissent un pipeline hautement configurable et gérable pour des mises en production sur plusieurs environnements (développement, préproduction, assurance qualité et production, par exemple). Ce processus peut inclure la nécessite d’approbations à des étapes spécifique du cycle de vie de l’application.

#### <a name="create-release-definition"></a>Créer une définition de mise en production

La création d’une définition de mise en production est la dernière étape du processus de création d’application. Cette définition de mise en production est utilisée pour créer une mise en production et déployer une build.

1.  Connectez-vous à Azure Pipelines et accédez à **Build et mise en production** pour le projet.

2.  Sous l’onglet **Versions**, sélectionnez **[ + ]** , puis choisissez **Créer une définition de mise en production**.

3.  Sous **Sélectionner un modèle**, choisissez **Déploiement d'Azure App Service**, puis sélectionnez **Appliquer**.

4.  Sous **Ajouter un artefact**, dans **Source (définition de build)** , sélectionnez l’application de build Azure Cloud.

5.  Sous l’onglet **Pipeline**, sélectionnez le lien **1 Phase**, **1 Tâche** vers **Afficher les tâches d’environnement**.

6.  Sous l’onglet **Tâches**, entrez Azure comme **nom d’environnement** et sélectionnez AzureCloud Traders-Web EP dans la liste **Abonnement Azure**.

7.  Entrez le **nom de l’Azure App Service**, qui est `northwindtraders` dans la capture d’écran suivante.

8.  Pour la phase d’agent, sélectionnez **VS2017 hébergé** dans la liste **File d’attente d’agents**.

9.  Dans **Déployer Azure App Service**, sélectionnez le **package ou dossier** valide pour l’environnement.

10. Dans **Sélectionner un fichier ou un dossier**, sélectionnez **OK** pour **Emplacement**.

11. Enregistrez toutes les modifications et revenez à **Pipeline**.

12. Sous l’onglet **Pipeline**, sélectionnez **Ajouter un artefact** et choisissez **NorthwindCloud Traders-Vessel** dans la liste **Source (définition de build)** .

13. Sous **Sélectionner un modèle**, ajoutez un autre environnement. Choisissez **Déploiement d'Azure App Service**, puis sélectionnez **Appliquer**.

14. Entrez `Azure Stack` comme **nom d’environnement**.

15. Sous l’onglet **Tâches**, recherchez et sélectionnez Azure Stack.

16. Dans la liste **Abonnement Azure**, sélectionnez **AzureStack Traders-Vessel EP** pour le point de terminaison Azure Stack.

17. Entrez le nom de l’application web Azure Stack comme **nom de l’App Service**.

18. Sous **Sélection de l’Agent**, choisissez **AzureStack -b Douglas Fir** dans la liste **File d’attente d’agents**.

19. Pour **Déployer Azure App Service**, sélectionnez le **package ou dossier** valide pour l’environnement. Sous **Sélectionner un fichier ou un dossier**, sélectionnez **OK** pour le dossier **Emplacement**.

20. Sous l’onglet **Variable**, recherchez la variable nommée `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS`. Définissez la valeur de la variable sur **true** et définissez sa portée sur **Azure Stack**.

21. Sous l’onglet **Pipeline**, sélectionnez l’icône **Déclencheur de déploiement continu** pour l’artefact NorthwindCloud Traders-Web et définissez le **Déclencheur de déploiement continu** sur **Activé**. Procédez de la même manière pour l’artefact **NorthwindCloud Traders-Vessel**.

22. Pour l’environnement Azure Stack, sélectionnez l’icône **Conditions préalables au déploiement** et définissez le déclencheur sur **Après la mise en production**.

23. Enregistrez toutes les modifications.

> [!Note]  
> Certains paramètres des tâches de mise en production sont automatiquement définis en tant que [variables d’environnement](https://docs.microsoft.com/azure/devops/pipelines/release/variables?view=vsts&tabs=batch#custom-variables) lors de la création d’une définition de mise en production à partir d’un modèle. Ces paramètres ne peuvent pas être modifiés dans les paramètres de tâche, mais peuvent être modifiés dans les éléments d’environnement parent.

## <a name="create-a-release"></a>Créer une mise en production

1.  Sous l’onglet **Pipeline**, ouvrez la liste **Version finale** et choisissez **Créer une mise en production**.

2.  Entrez une description pour la mise en production, vérifiez que les artefacts corrects sont sélectionnés, puis choisissez **Créer**. Après quelques instants, une bannière s’affiche, indiquant que la nouvelle mise en production a été créée ; le nom de la mise en production est affichée sous forme de lien. Choisissez le lien pour afficher la page récapitulative de la mise en production.

3.  La page récapitulative de mise en production fournit des détails sur la mise en production. Dans la capture d’écran suivante pour « Release-2 », la section **Environnements** indique que l’**état du déploiement** d’Azure est « IN PROGRESS » (En cours) et que l’état d’Azure Stack est « SUCCEEDED » (Réussi). Lorsque l’état du déploiement de l’environnement Azure passe à « SUCCEEDED » (Réussi), une bannière indiquant que la mise en production est prête pour l’approbation s’affiche. Lorsqu’un déploiement est en attente ou a échoué, une icône d’informations **(i)** bleue est affichée. Pointez sur l’icône pour afficher une fenêtre contextuelle qui indique le motif du retard ou de l’échec.

4.  D’autres vues, comme la liste des mises en production, affichent aussi une icône indiquant qu’une approbation est en attente. La fenêtre contextuelle de cette icône indique le nom de l’environnement et plus de détails sur le déploiement. Un administrateur peut facilement voir la progression globale des mises en production et savoir quelles mises en production sont en attente d’approbation.

## <a name="monitor-and-track-deployments"></a>Surveiller et suivre les déploiements

1.  Sur la page récapitulative **Release-2**, sélectionnez **Journaux d’activité**. Pendant un déploiement, cette page affiche le journal en direct de l’agent. Le volet gauche indique l’état de chaque opération du déploiement pour chaque environnement.

2.  Choisissez une personne dans la colonne **Action** pour une approbation de prédéploiement ou de postdéploiement afin de voir qui a approuvé (ou rejeté) le déploiement ainsi que le message qu’elle a entré.

3.  Lorsque le déploiement est terminé, l’intégralité du fichier journal s’affiche dans le volet droit. Choisissez une **étape** dans le volet gauche pour afficher le fichier journal d’une seule étape telle que **Initialiser le travail**. La possibilité de voir les journaux d’activité individuels facilite le suivi et le débogage de parties du déploiement global. **Enregistrez** le fichier journal d’une étape ou **téléchargez tous les journaux d’activité au format zip**.

4.  Ouvrez l’onglet **Résumé** pour afficher des informations générales sur la mise en production. Cette vue montre les détails de la build, les environnements où elle a été déployée, l’état du déploiement et d’autres informations sur la mise en production.

5.  Sélectionnez un lien d’environnement (**Azure** ou **Azure Stack**) pour afficher des informations sur les déploiements existants et en attente dans un environnement spécifique. Utilisez ces vues pour vérifier rapidement que la même build a été déployée sur les deux environnements.

6.  Ouvrez l’**application de production déployée** dans le navigateur. Par exemple, pour le site web Azure App Services, ouvrez l’URL [https://[nom-application\].azurewebsites.net](https:// [your-app-name].azurewebsites.net).

**L’intégration d’Azure et Azure Stack fournit une solution dans le cloud évolutive**

Un service à plusieurs clouds flexible et fiable offre sécurité des données, sauvegarde et redondance, disponibilité rapide et cohérente, stockage et distribution évolutifs, et routage conforme géographiquement. Ce processus déclenché manuellement garantit une commutation de charge fiable et efficace entre les applications web hébergées, assurant ainsi la disponibilité immédiate des données critiques. 

## <a name="next-steps"></a>Étapes suivantes
- Pour plus d’informations sur les modèles Azure Cloud, consultez [Modèles de conception cloud](https://docs.microsoft.com/azure/architecture/patterns).
