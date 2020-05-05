---
title: Diriger le trafic avec une application géodistribuée en utilisant Azure et Azure Stack Hub
description: Découvrez comment diriger le trafic vers des points de terminaison spécifiques avec une solution d’application géodistribuée à l’aide d’Azure et d’Azure Stack Hub.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: d219da5d219b5d341e5c62cfbf823be1334cc614
ms.sourcegitcommit: e5b587216a137819444680ec619281c90f37bad9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/27/2020
ms.locfileid: "82167107"
---
# <a name="direct-traffic-with-a-geo-distributed-app-using-azure-and-azure-stack-hub"></a>Diriger le trafic avec une application géodistribuée en utilisant Azure et Azure Stack Hub

Découvrez comment diriger le trafic vers des points de terminaison spécifiques en fonction de différentes mesures à l’aide du modèle d’applications géolocalisées. En créant un profil Traffic Manager avec configuration du routage et du point de terminaison basée sur la géolocalisation, vous êtes certain que les informations sont dirigées vers les points de terminaison en fonction des exigences régionales, des réglementations organisationnelles et internationales et de vos besoins en matière de données.

Dans cette solution, vous allez générer un exemple d’environnement pour :

> [!div class="checklist"]
> - Créer une application géodistribuée.
> - Utiliser Traffic Manager pour cibler votre application.

## <a name="use-the-geo-distributed-apps-pattern"></a>Utiliser le modèle d’application géolocalisée.

Avec le modèle géodistribué, l’application couvre plusieurs régions. Vous pouvez utiliser le cloud public par défaut, mais certains utilisateurs ont besoin que leurs données restent dans leur région. Dans ce cas, orientez les utilisateurs vers le cloud le plus approprié en fonction de leurs besoins.

### <a name="issues-and-considerations"></a>Problèmes et considérations

#### <a name="scalability-considerations"></a>Considérations relatives à l’extensibilité

La solution que vous allez créer avec cet article n’a pas pour objectif de s’adapter à la scalabilité. Toutefois, utilisée en combinaison avec d’autres solutions Azure et locales, elle permet de s’adapter à ces exigences d’extensibilité. Pour plus d’informations sur la création d’une solution hybride avec mise à l’échelle automatique via Traffic Manager, consultez [Créer des solutions de mise à l’échelle multicloud avec Azure](solution-deployment-guide-cross-cloud-scaling.md).

#### <a name="availability-considerations"></a>Considérations relatives à la disponibilité

Cette solution ne prend pas directement en charge les besoins de disponibilité. Toutefois, vous pouvez implémenter des solutions Azure et locales dans cette solution pour garantir la haute disponibilité de tous les composants impliqués.

### <a name="when-to-use-this-pattern"></a>Quand utiliser ce modèle

- Les succursales internationales de votre organisation exigent des politiques de sécurité et de distribution régionales personnalisées.

- Chacun des bureaux de votre organisation extrait des données sur les employés, l’activité et les installations. Cela nécessite de rendre compte de l’activité en lien avec les réglementations locales et les fuseaux horaires.

- Les exigences de grande échelle sont satisfaites par la mise à l’échelle horizontale (scale-out) des applications, avec plusieurs déploiements dans une ou plusieurs régions, pour gérer des besoins extrêmes en termes de charge.

### <a name="planning-the-topology"></a>Planification de la topologie

Avant de créer une empreinte d’application distribuée, il est utile de disposer des informations suivantes :

- **Domaine personnalisé pour l’application :** quel est le nom de domaine personnalisé que les clients utiliseront pour accéder à l’application ? Pour l’exemple d’application, le nom de domaine personnalisé est *www\.scalableasedemo.com.*

- **Domaine Traffic Manager :** vous devez choisir un nom de domaine au moment de la création d’un [profil Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-manage-profiles). Ce nom est associé au suffixe *trafficmanager.net* pour enregistrer une entrée de domaine gérée par Traffic Manager. Dans l’exemple d’application, le nom choisi est *scalable-ase-demo*. Par conséquent, le nom de domaine complet géré par Traffic Manager est *scalable-ase-demo.trafficmanager.net*.

- **Stratégie de mise à l’échelle de l’empreinte de l’application :** décidez si l’empreinte de l’application doit être distribuée dans plusieurs environnements App Service au sein d’une ou de plusieurs régions, ou dans un mélange des deux approches. La décision doit être prise en fonction des attentes depuis l’emplacement d’origine du trafic du client, ainsi que de la manière dont peut évoluer le reste de l’application prenant en charge l’infrastructure principale. Par exemple, avec une application à 100 % sans état, une application peut être adaptée à très grande échelle à l’aide d’une combinaison de plusieurs environnements App Service par région Azure, puis multipliée par les environnements App Service déployés dans plusieurs régions Azure. Avec plus de 15 régions Azure globales disponibles, les clients peuvent véritablement créer une empreinte d’application à échelle mondiale. Pour l’exemple d’application utilisé ici, trois environnements App Service ont été créés dans une seule région Azure (USA Centre Sud).

- **Convention de nommage pour les environnements App Service :** chaque environnement App Service requiert un nom unique. Au-delà d’un ou de deux environnements App Service, une convention de nommage identifiant chaque environnement App Service s’avère utile. Pour l’exemple d’application utilisé ici, une convention d’affectation de noms simple a été utilisée. Les noms des trois environnements App Service sont respectivement *fe1ase*, *fe2ase* et *fe3ase*.

- **Convention d’affectation de noms pour les applications :** comme plusieurs instances de l’application vont être déployées, un nom est requis pour chacune d’entre elles. Avec App Service Environment pour PowerApps, le même nom d’application peut être utilisé dans plusieurs environnements. Étant donné que chaque environnement App Service comporte un suffixe de domaine unique, les développeurs peuvent choisir de réutiliser le même nom d’application dans chaque environnement. Par exemple, un développeur peut avoir des applications nommées comme suit : *myapp.foo1.p.azurewebsites.net*, *myapp.foo2.p.azurewebsites.net*, *myapp.foo3.p.azurewebsites.net*, et ainsi de suite. Pour l’application utilisée ici, chaque instance de l’application a un nom unique. Les noms d’instance application utilisés sont *webfrontend1*, *webfrontend2* et *webfrontend3*.

> [!Tip]  
> ![hybrid-pillars.png](./media/solution-deployment-guide-cross-cloud-scaling/hybrid-pillars.png)  
> Microsoft Azure Stack Hub est une extension d’Azure. Azure Stack Hub offre à votre environnement local l’agilité et l’innovation du cloud computing grâce au seul cloud hybride qui vous permette de créer et de déployer des applications hybrides en tout lieu.  
> 
> L’article [Considérations sur la conception d’applications hybrides](overview-app-design-considerations.md) se penche sur les fondements de la qualité logicielle (sélection élective, scalabilité, disponibilité, résilience, facilité de gestion et sécurité) pour la conception, le déploiement et le fonctionnement des applications hybrides. Les considérations de conception vous aident à optimiser la conception d’application hybride, en réduisant les risques dans les environnements de production.

## <a name="part-1-create-a-geo-distributed-app"></a>Première partie : Créer une application géolocalisée

Dans cette partie, vous allez créer une application web.

> [!div class="checklist"]
> - Créer des applications web et publier.
> - Ajouter du code à Azure Repos.
> - Pointez la génération de l’application sur plusieurs cibles du cloud.
> - Gérer et configurer le processus CD.

### <a name="prerequisites"></a>Prérequis

Un abonnement Azure et l’installation d’Azure Stack Hub sont requis.

### <a name="geo-distributed-app-steps"></a>Procédure de création d’une application géolocalisée

### <a name="obtain-a-custom-domain-and-configure-dns"></a>Obtenir un domaine personnalisé et configurer DNS

Mettez à jour le fichier de zone DNS pour le domaine. Azure AD peut ensuite vérifier la propriété du nom de domaine personnalisé. Utilisez [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) pour les enregistrements DNS Azure/Office 365/externes dans Azure, ou ajoutez l’entrée DNS à [un autre bureau d’enregistrement DNS](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

1. Inscrivez un domaine personnalisé auprès d’un bureau d’enregistrement public.

2. Connectez-vous au Bureau d’enregistrement des noms de domaine pour le domaine. Un administrateur approuvé devra peut-être effectuer les mises à jour DNS.

3. Mettez à jour le fichier de zone DNS du domaine en ajoutant l’entrée DNS fournie par Azure AD. Elle ne modifie pas les comportements comme le routage du courrier ou l’hébergement web.

### <a name="create-web-apps-and-publish"></a>Créer des applications web et publier

Configurez une intégration/livraison continue (CI/CD) hybride pour déployer Web App sur Azure et Azure Stack Hub, et pour envoyer les modifications aux deux clouds.

> [!Note]  
> Vous avez besoin d’Azure Stack Hub avec les images appropriées syndiquées pour s’exécuter (Windows Server et SQL), et App Service doit être déployé. Pour plus d’informations, consultez [Conditions préalables au déploiement d’App Service sur Azure Stack Hub](../operator/azure-stack-app-service-before-you-get-started.md).

#### <a name="add-code-to-azure-repos"></a>Ajouter du code à Azure Repos

1. Connectez-vous à Visual Studio avec un **compte ayant les droits de création de projet** sur Azure Repos.

    La CI/CD hybride peut s’appliquer tant au code d’application qu’au code d’infrastructure. Utilisez les [modèles Azure Resource Manager](https://azure.microsoft.com/resources/templates/) pour les développements cloud privé et hébergé.

    ![Se connecter à un projet dans Visual Studio](media/solution-deployment-guide-geo-distributed/image1.JPG)

2. **Clonez le référentiel** en créant et en ouvrant l’application web par défaut.

    ![Cloner un référentiel dans Visual Studio](media/solution-deployment-guide-geo-distributed/image2.png)

### <a name="create-web-app-deployment-in-both-clouds"></a>Création d’un déploiement d’application web dans les deux clouds

1. Modifiez le fichier **WebApplication.csproj** : Sélectionnez `Runtimeidentifier`, puis ajoutez `win10-x64`. (Consultez la documentation sur le [déploiement autonome](https://docs.microsoft.com/dotnet/core/deploying/deploy-with-vs#simpleSelf).)

    ![Modifier un fichier de projet d’application web dans Visual Studio](media/solution-deployment-guide-geo-distributed/image3.png)

2. **Archivez le code dans Azure Repos** avec Team Explorer.

3. Vérifiez que le **code d’application** a été archivé dans Azure Repos.

### <a name="create-the-build-definition"></a>Créer la définition de build

1. **Connectez-vous à Azure Pipelines** pour vérifier la possibilité de créer des définitions de build.

2. Ajoutez le code `-r win10-x64`. Cet ajout est nécessaire pour déclencher un déploiement autonome avec .NET Core.

    ![Ajouter du code à la définition de build dans Azure Pipelines](media/solution-deployment-guide-geo-distributed/image4.png)

3. **Exécutez la build**. Le processus de [build de déploiement autonome](https://docs.microsoft.com/dotnet/core/deploying/deploy-with-vs#simpleSelf) publie des artefacts qui peuvent s’exécuter sur Azure et Azure Stack Hub.

#### <a name="using-an-azure-hosted-agent"></a>Utilisation d’un agent hébergé sur Azure

L’utilisation d’un agent hébergé dans Azure Pipelines est une option pratique pour créer et déployer des applications web. La maintenance et les mises à niveau sont effectuées automatiquement par Microsoft Azure, ce qui permet de développer, de tester et de déployer sans interruption.

### <a name="manage-and-configure-the-cd-process"></a>Gérer et configurer le processus CD

Azure DevOps Services fournit un pipeline hautement configurable et gérable pour des mises en production sur plusieurs environnements, comme des environnements de développement, de préproduction, d’assurance qualité et de production, avec des demandes d’approbation à des étapes spécifiques.

## <a name="create-release-definition"></a>Créer une définition de mise en production

1. Sélectionnez le bouton **plus** pour ajouter une nouvelle mise en production sous l’onglet **Mises en production** dans la section **Build et mise en production** d’Azure DevOps Services.

    ![Créer une définition de mise en production dans Azure DevOps Services](media/solution-deployment-guide-geo-distributed/image5.png)

2. Appliquez le modèle de déploiement Azure App Service.

   ![Appliquer un modèle de déploiement Azure App Service dans Azure DevOps Services](media/solution-deployment-guide-geo-distributed/image6.png)

3. Sous **Ajouter un artefact**, ajoutez l’artefact pour l’application de build Azure Cloud.

   ![Ajouter un artefact à la build de cloud Azure dans Azure DevOps Services](media/solution-deployment-guide-geo-distributed/image7.png)

4. Sous l’onglet Pipeline, sélectionnez le lien **Phase, Tâche** de l’environnement, et définissez les valeurs de l’environnement cloud Azure.

   ![Définir des valeurs d’environnement cloud Azure dans Azure DevOps Services](media/solution-deployment-guide-geo-distributed/image8.png)

5. Définissez le **nom d’environnement** et sélectionnez l’**abonnement Azure** pour le point de terminaison du Cloud Azure.

      ![Sélectionner un abonnement Azure pour le point de terminaison du cloud Azure dans Azure DevOps Services](media/solution-deployment-guide-geo-distributed/image9.png)

6. Sous **Nom de l’App Service**, définissez le nom de service de l’application Azure.

      ![Définir un nom Azure App Service dans Azure DevOps Services](media/solution-deployment-guide-geo-distributed/image10.png)

7. Entrez VS2017 hébergé sous **File d’attente de l’agent** pour l’environnement hébergé dans le cloud Azure.

      ![Définir une file d’attente d’agents pour l’environnement hébergé dans le cloud Azure dans Azure DevOps Services](media/solution-deployment-guide-geo-distributed/image11.png)

8. Dans le menu Déployer Azure App Service, sélectionnez **le package ou le dossier** valide pour l’environnement. Sélectionnez **OK** pour **l’emplacement du dossier**.
  
      ![Sélectionner un package ou un dossier pour l’environnement Azure App Service dans Azure DevOps Services](media/solution-deployment-guide-geo-distributed/image12.png)

      ![Sélectionner un package ou un dossier pour l’environnement Azure App Service dans Azure DevOps Services](media/solution-deployment-guide-geo-distributed/image13.png)

9. Enregistrez toutes les modifications et revenez au **pipeline de mises en production**.

    ![Enregistrer les modifications dans le pipeline de mise en production dans Azure DevOps Services](media/solution-deployment-guide-geo-distributed/image14.png)

10. Ajoutez un nouvel artefact en sélectionnant la build pour l’application Azure Stack Hub.

    ![Ajouter un nouvel artefact pour une application Azure Stack Hub dans Azure DevOps Services](media/solution-deployment-guide-geo-distributed/image15.png)


11. Ajoutez un environnement supplémentaire en appliquant le déploiement Azure App Service.

    ![Ajouter un environnement à un déploiement Azure App Service dans Azure DevOps Services](media/solution-deployment-guide-geo-distributed/image16.png)

12. Nommez le nouvel environnement Azure Stack Hub.

    ![Nommer un environnement dans un déploiement Azure App Service dans Azure DevOps Services](media/solution-deployment-guide-geo-distributed/image17.png)

13. Recherchez l’environnement Azure Stack Hub sous l’onglet **Tâche**.

    ![Environnement Azure Stack Hub dans Azure DevOps Services](media/solution-deployment-guide-geo-distributed/image18.png)

14. Sélectionnez l’abonnement pour le point de terminaison Azure Stack Hub.

    ![Sélectionner l’abonnement pour le point de terminaison Azure Stack Hub dans Azure DevOps Services](media/solution-deployment-guide-geo-distributed/image19.png)

15. Définissez le nom de l’application web Azure Stack Hub en tant que nom App Service.

    ![Définir un nom d’application web Azure Stack Hub dans Azure DevOps Services](media/solution-deployment-guide-geo-distributed/image20.png)

16. Sélectionnez l’agent Azure Stack Hub.

    ![Sélectionner l’agent Azure Stack Hub dans Azure DevOps Services](media/solution-deployment-guide-geo-distributed/image21.png)

17. Sous la section Déployer Azure App Service, sélectionnez **le package ou le dossier** valides pour l’environnement. Sélectionnez **OK** pour l’emplacement du dossier.

    ![Sélectionner un dossier pour un déploiement Azure App Service dans Azure DevOps Services](media/solution-deployment-guide-geo-distributed/image22.png)

    ![Sélectionner un dossier pour un déploiement Azure App Service dans Azure DevOps Services](media/solution-deployment-guide-geo-distributed/image23.png)

18. Sous l’onglet Variable, ajoutez une variable nommée `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS`, définissez sa valeur sur **true** et sa portée sur Azure Stack Hub.

    ![Ajouter une variable à un déploiement Azure App Service dans Azure DevOps Services](media/solution-deployment-guide-geo-distributed/image24.png)

19. Sélectionnez l’icône du déclencheur de déploiement **Continu** dans les deux artefacts et activez le déclencheur de déploiement **Continu**.

    ![Sélectionner un déclencheur de déploiement continu dans Azure DevOps Services](media/solution-deployment-guide-geo-distributed/image25.png)

20. Sélectionnez l’icône des conditions **Prédéploiement** dans l’environnement Azure Stack Hub et définissez le déclencheur sur **Après la mise en production**.

    ![Sélectionner des conditions préalables au déploiement dans Azure DevOps Services](media/solution-deployment-guide-geo-distributed/image26.png)

21. Enregistrez toutes les modifications.

> [!Note]  
> Certains paramètres des tâches peuvent avoir été automatiquement définis en tant que [variables d’environnement](https://docs.microsoft.com/azure/devops/pipelines/release/variables?view=vsts&tabs=batch#custom-variables) lors de la création d’une définition de mise en production à partir d’un modèle. Ces paramètres ne peuvent pas être modifiés dans les paramètres de la tâche. Au lieu de cela, l’élément d’environnement parent doit être sélectionné pour modifier ces paramètres.

## <a name="part-2-update-web-app-options"></a>Deuxième partie : Mettre à jour les options de l’application web

[Azure App Service](https://docs.microsoft.com/azure/app-service/overview) offre un service d’hébergement web hautement évolutif appliquant des mises à jour correctives automatiques.

![Azure App Service](media/solution-deployment-guide-geo-distributed/image27.png)

> [!div class="checklist"]
> - Mappez un nom DNS personnalisé existant à Azure Web Apps.
> - Utilisez un **enregistrement CNAME** ou un **enregistrement A** pour mapper un nom DNS personnalisé à App Service.

### <a name="map-an-existing-custom-dns-name-to-azure-web-apps"></a>Mapper un nom DNS personnalisé existant à des applications web Azure

> [!Note]  
> Utilisez un enregistrement CNAME pour tous les noms DNS personnalisés, à l’exception d’un domaine racine (par exemple, northwind.com).

Pour migrer un site actif et son nom de domaine DNS vers App Service, voir [Migrer un nom DNS actif vers Azure App Service](https://docs.microsoft.com/azure/app-service/manage-custom-dns-migrate-domain).

### <a name="prerequisites"></a>Prérequis

Pour suivre cette solution :

- [Créez une application App Service](https://docs.microsoft.com/azure/app-service/), ou utilisez une application créée pour une autre solution.

- Achetez un nom de domaine et fournissez un accès au registre DNS au fournisseur de domaine.

Mettez à jour le fichier de zone DNS pour le domaine. Azure AD vérifie la propriété du nom de domaine personnalisé. Utilisez [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) pour les enregistrements DNS Azure/Office 365/externes dans Azure, ou ajoutez l’entrée DNS à [un autre bureau d’enregistrement DNS](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

- Inscrivez un domaine personnalisé auprès d’un bureau d’enregistrement public.

- Connectez-vous au Bureau d’enregistrement des noms de domaine pour le domaine. (Il se peut qu’un administrateur approuvé doive effectuer les mises à jour DNS.)

- Mettez à jour le fichier de zone DNS du domaine en ajoutant l’entrée DNS fournie par Azure AD.

Par exemple, pour ajouter des entrées DNS pour northwindcloud.com et www\.northwindcloud.com, configurez les paramètres DNS pour le domaine racine northwindcloud.com.

> [!Note]  
> Vous pouvez acheter un nom de domaine à l’aide du [portail Azure](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain). Pour mapper un nom DNS personnalisé à une application web, le [plan App Service](https://azure.microsoft.com/pricing/details/app-service/) de l’application web doit être un niveau payant (**Partagé**, **De base**, **Standard** ou **Premium**).

### <a name="create-and-map-cname-and-a-records"></a>Créer et mapper des enregistrements CNAME et A

#### <a name="access-dns-records-with-domain-provider"></a>Accès aux enregistrements DNS avec le fournisseur de domaine

> [!Note]  
>  Utilisez Azure DNS pour configurer un nom DNS personnalisé pour les applications web Azure. Pour plus d’informations, consultez [Use Azure DNS to provide custom domain settings for an Azure service](https://docs.microsoft.com/azure/dns/dns-custom-domain) (Utiliser DNS Azure pour fournir des paramètres de domaine personnalisé pour un service Azure).

1. Connectez-vous au site web du fournisseur principal.

2. Trouvez la page de gestion des enregistrements DNS. Chaque fournisseur de domaine a sa propre interface d’enregistrements DNS. Recherchez les zones du site qui portent les mentions **Nom de domaine**, **DNS** ou **Gestion du nom de serveur**.

Vous pouvez consulter la page d’enregistrements DNS dans **Mes domaines**. Trouvez le lien nommé **Fichier de zone**, **Enregistrements DNS** ou **Configuration avancée**.

La capture d’écran suivante est un exemple d’une page d’enregistrements DNS :

![Exemple de page d’enregistrements DNS](media/solution-deployment-guide-geo-distributed/image28.png)

1. Dans le bureau d’enregistrement de noms de domaine, sélectionnez **Ajouter ou Créer** pour créer un enregistrement. Certains fournisseurs ont différents liens pour ajouter divers types d’enregistrements. Consultez la documentation du fournisseur.

2. Ajoutez un enregistrement CNAME pour mapper un sous-domaine au nom d’hôte par défaut de l’application.

   Pour l’exemple de domaine www\.northwindcloud.com, ajoutez un enregistrement CNAME qui mappe le nom à `<app_name>.azurewebsites.net`.

Après avoir ajouté l’enregistrement CNAME, la page d’enregistrements DNS ressemble à l’exemple suivant :

![Navigation au sein du portail pour accéder à l’application Azure](media/solution-deployment-guide-geo-distributed/image29.png)

### <a name="enable-the-cname-record-mapping-in-azure"></a>Activer le mappage d’enregistrement CNAME dans Azure

1. Dans un nouvel onglet, connectez-vous au portail Azure.

2. Accédez à App Services.

3. Sélectionnez l’application web.

4. Dans le volet de navigation gauche de la page d’application du portail Azure, sélectionnez **Domaines personnalisés**.

5. Cliquez sur l’icône **+** en regard de **Ajouter un nom d’hôte**.

6. Tapez le nom de domaine complet, par exemple `www.northwindcloud.com`.

7. Sélectionnez **Valider**.

8. Si cela est indiqué, ajoutez des enregistrements supplémentaires d’autres types (`A` ou `TXT`) aux enregistrements DNS du bureau d’enregistrement de noms de domaine. Azure fournit les valeurs et les types de ces enregistrements :

   a.  Un enregistrement **A** pour effectuer un mappage vers l’adresse IP de l’application.

   b.  Un enregistrement **TXT** pour effectuer un mappage vers le nom d’hôte par défaut de l’application `<app_name>.azurewebsites.net`. App Service utilise cet enregistrement uniquement au moment de la configuration, pour vérifier la propriété du domaine personnalisé. Après vérification, supprimez l’enregistrement TXT.

9. Effectuez cette tâche dans l’onglet du bureau d’enregistrement de domaine et validez à nouveau pour que le bouton **Ajouter un nom d’hôte** soit activé.

10. Assurez-vous que **Type d’enregistrement du nom d’hôte** est défini sur **CNAME** (www.exemple.com ou tout sous-domaine).

11. Sélectionnez **Ajouter un nom d’hôte**.

12. Tapez le nom de domaine complet, par exemple `northwindcloud.com`.

13. Sélectionnez **Valider**. **Ajouter** est activé.

14. Assurez-vous que **Type d’enregistrement du nom d’hôte** est défini sur **Enregistrement A** (exemple.com).

15. **Ajoutez un nom d’hôte**.

    Un certain temps peut être nécessaire pour que les nouveaux noms d’hôte soient reflétés sur la page **Domaines personnalisés** de votre application. Essayez d’actualiser le navigateur pour mettre à jour les données.
  
    ![Domaines personnalisés](media/solution-deployment-guide-geo-distributed/image31.png) 
  
    En cas d’erreur, une notification d’erreur de vérification s’affiche en bas de la page. ![Erreur de vérification du domaine](media/solution-deployment-guide-geo-distributed/image32.png)

> [!Note]  
>  Les étapes ci-dessus peuvent être répétées pour mapper un domaine contenant un caractère générique (\*.northwindcloud.com). Cela vous permet d’ajouter n’importe quel sous-domaine à cet App Service sans avoir à créer un enregistrement CNAME distinct pour chacun. Suivez les instructions du bureau d’enregistrement pour configurer ce paramètre.

#### <a name="test-in-a-browser"></a>Test dans un navigateur

Parcourez les noms DNS configurés précédemment (par exemple, `northwindcloud.com` ou www.northwindcloud.com).

## <a name="part-3-bind-a-custom-ssl-cert"></a>Troisième partie : Lier un certificat SSL personnalisé

Dans cette partie, nous allons :

> [!div class="checklist"]
> - lier le certificat SSL personnalisé à App Service ;
> - appliquer le protocole HTTPS pour l’application ;
> - automatiser une liaison de certificat SSL avec des scripts.

> [!Note]  
> Si nécessaire, obtenez un certificat SSL dans le portail Azure et liez-le à l’application web. Pour plus d’informations, voir le [didacticiel sur App Service Certificates](https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site).

### <a name="prerequisites"></a>Prérequis

Pour effectuer cette solution :

- [Créez une application App Service](https://docs.microsoft.com/azure/app-service/).
- [Mappez un nom DNS personnalisé à votre application web.](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain)
- Acquérez un certificat SSL auprès d’une autorité de certification approuvée et utilisez la clé pour signer la demande.

### <a name="requirements-for-your-ssl-certificate"></a>Conditions requises pour le certificat SSL

Pour l’utiliser dans App Service, un certificat doit remplir toutes les conditions suivantes :

- être signé par une autorité de certification approuvée ;

- être exporté sous la forme d’un fichier PFX protégé par mot de passe ;

- contenir une clé privée d’une longueur minimale de 2048 bits ;

- contenir tous les certificats intermédiaires dans la chaîne de certificats.

> [!Note]  
> Les **certificats de chiffrement à courbe elliptique (ECC)** sont compatibles avec App Service, mais ne sont pas abordés dans ce guide. Consultez une autorité de certification pour bénéficier d’une assistance lors de la création de certificats ECC.

#### <a name="prepare-the-web-app"></a>Préparation de l’application web

Pour lier un certificat SSL personnalisé à votre application web, le [plan App Service](https://azure.microsoft.com/pricing/details/app-service/) doit se trouver dans le niveau **De base**, **Standard** ou **Premium**.

#### <a name="sign-in-to-azure"></a>Connexion à Azure

1. Ouvrez le [portail Azure](https://portal.azure.com/) et accédez à l’application web.

2. Dans le menu de gauche, sélectionnez **App Services**, puis le nom de l’application web.

![Sélectionner l’application web dans le portail Azure](media/solution-deployment-guide-geo-distributed/image33.png)

#### <a name="check-the-pricing-tier"></a>Vérification du niveau tarifaire

1. Dans le volet de navigation de gauche de la page d’application web, accédez à la section **Paramètres** et sélectionnez **Monter en puissance (plan App Service)** .

    ![Menu Scale-up dans l’application web](media/solution-deployment-guide-geo-distributed/image34.png)

1. Vérifiez que l’application web n’est pas de niveau **Gratuit** ou **Partagé**. Le niveau actuel de l’application web est encadré d’un rectangle bleu foncé.

    ![Vérifier le niveau tarifaire dans l’application web](media/solution-deployment-guide-geo-distributed/image35.png)

Le certificat SSL personnalisé n’est pas pris en charge aux niveaux **Gratuit** et **Partagé**. Pour contourner ce problème, suivez la procédure décrite dans la section suivante ou dans la page **Choisir votre niveau de tarification**, puis passez à [Charger et lier votre certificat SSL](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl).

#### <a name="scale-up-your-app-service-plan"></a>Évolution de votre plan App Service

1. Sélectionnez l’un des niveaux **De base**, **Standard** ou **Premium**.

2. Sélectionnez **Sélectionner**.

![Choisir un niveau tarifaire pour votre application web](media/solution-deployment-guide-geo-distributed/image36.png)

L’opération de mise à l’échelle est terminée lorsque la notification s’affiche.

![Notification de montée en puissance](media/solution-deployment-guide-geo-distributed/image37.png)

#### <a name="bind-your-ssl-certificate-and-merge-intermediate-certificates"></a>Liaison de votre certificat SSL et fusion des certificats intermédiaires

Fusionnez plusieurs certificats dans la chaîne.

1. **Ouvrez chaque certificat** reçu dans un éditeur de texte.

2. Créez un fichier pour le certificat fusionné appelé *mergedcertificate.crt*. Dans un éditeur de texte, copiez le contenu de chaque certificat dans ce fichier. L’ordre de vos certificats devrait suivre l’ordre dans la chaîne d’approbation, commençant par votre certificat et finissant par le certificat racine. Cela ressemble à l’exemple suivant :

    ```Text

    -----BEGIN CERTIFICATE-----

    <your entire Base64 encoded SSL certificate>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded intermediate certificate 1>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded intermediate certificate 2>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded root certificate>

    -----END CERTIFICATE-----
    ```

#### <a name="export-certificate-to-pfx"></a>Exportation du certificat vers PFX

Exportez le certificat SSL fusionné avec la clé privée générée par le certificat.

Un fichier de clé privée est créé via OpenSSL. Pour exporter le certificat vers PFX, exécutez la commande suivante, en remplaçant les espaces réservés `<private-key-file>` et `<merged-certificate-file>` par le chemin de clé privée et le fichier de certificat fusionné :

```powershell
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>
```

Lorsque vous y êtes invité, définissez un mot de passe d’exportation pour charger votre certificat SSL dans App Service ultérieurement.

Lorsque vous utilisez IIS ou **Certreq.exe** pour générer la demande de certificat, installez le certificat sur un ordinateur local, puis [exportez le certificat au format PFX](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).

#### <a name="upload-the-ssl-certificate"></a>Charger le certificat SSL

1. Dans le volet de navigation gauche, sélectionnez **Paramètres SSL**.

2. Sélectionnez **Charger un certificat**.

3. Dans **Fichier de certificat PFX**, sélectionnez le fichier PFX.

4. Dans **Mot de passe du certificat**, tapez le mot de passe créé lors de l’exportation du fichier PFX.

5. Sélectionnez **Télécharger**.

    ![Charger un certificat SSL](media/solution-deployment-guide-geo-distributed/image38.png)

Lorsque App Service finit de charger le certificat, celui-ci apparaît dans la page **Paramètres SSL**.

![Paramètres SSL](media/solution-deployment-guide-geo-distributed/image39.png)

#### <a name="bind-your-ssl-certificate"></a>Liaison de votre certificat SSL

1. Dans la section **Liaisons SSL**, sélectionnez **Ajouter une liaison**.

    > [!Note]  
    >  Si le certificat a été chargé, mais n’apparaît pas dans les noms de domaine dans la liste déroulante **Nom d’hôte**, essayez d’actualiser la page du navigateur.

2. Dans la page **Ajouter une liaison SSL**, utilisez les listes déroulantes pour sélectionner le nom de domaine à sécuriser et le certificat à utiliser.

3. Dans **Type SSL**, choisissez d’utiliser [**l’indication du nom du serveur (SNI)** ](https://en.wikipedia.org/wiki/Server_Name_Indication) ou le protocole SSL basé sur IP.

    - **SSL basé sur SNI** : plusieurs liaisons SSL basées sur SNI peuvent être ajoutées. Cette option permet de sécuriser plusieurs domaines sur la même adresse IP avec plusieurs certificats SSL. La plupart des navigateurs actuels (y compris Internet Explorer, Chrome, Firefox et Opera) prennent en charge SNI (plus d’informations sur la prise en charge des navigateurs dans [Indication du nom du serveur](https://wikipedia.org/wiki/Server_Name_Indication)).

    - **SSL basé sur IP** : une seule liaison SSL basée sur IP peut être ajoutée. Cette option permet de sécuriser une adresse IP publique dédiée avec un seul certificat SSL. Pour sécuriser plusieurs domaines, vous devez tous les sécuriser en utilisant le même certificat SSL. SSL basé sur IP est l’option habituelle pour la liaison SSL.

4. Sélectionnez **Ajouter une liaison**.

    ![Ajouter une liaison SSL](media/solution-deployment-guide-geo-distributed/image40.png)

Lorsque App Service finit de charger votre certificat, celui-ci apparaît dans les sections **Liaisons SSL**.

![Le chargement des liaisons SSL est terminé](media/solution-deployment-guide-geo-distributed/image41.png)

#### <a name="remap-the-a-record-for-ip-ssl"></a>Remappage d’un enregistrement pour IP SSL

Si vous n’utilisez pas SSL basé sur IP dans l’application web, passez à [Tester HTTPS pour votre domaine personnalisé](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl).

Par défaut, l’application web utilise une adresse IP publique partagée. Lorsque le certificat est lié avec SSL basé sur IP, App Service crée une adresse IP dédiée pour l’application web.

Lorsqu’un enregistrement A est mappé sur l’application web, le registre de domaine doit être mis à jour avec l’adresse IP dédiée.

La page **Domaine personnalisé** est mise à jour avec la nouvelle adresse IP dédiée. Copiez cette [adresse IP](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain), puis remappez [l’enregistrement A](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain) sur cette nouvelle adresse IP.

#### <a name="test-https"></a>Test du protocole HTTPS

Dans différents navigateurs, accédez à `https://<your.custom.domain>` pour vérifier que l’application web est fournie.

![accéder à l’application web](media/solution-deployment-guide-geo-distributed/image42.png)

> [!Note]  
> Les erreurs de validation de certificat éventuelles peuvent être dues à un certificat auto-signé ou à des certificats intermédiaires mis de côté lors de l’exportation vers le fichier PFX.

#### <a name="enforce-https"></a>Appliquer le protocole HTTPS

Par défaut, chacun peut accéder à l’application web avec HTTP. Vous pouvez rediriger toutes les requêtes HTTP vers le port HTTPS.

Dans la page d’application web, sélectionnez **Paramètres SSL**. Ensuite, dans **HTTPS uniquement**, sélectionnez **Activer**.

![Appliquer le protocole HTTPS](media/solution-deployment-guide-geo-distributed/image43.png)

Lorsque l’opération est terminée, accédez à l’une des URL HTTP pointant vers l’application. Par exemple :

- https://<nom_application>.azurewebsites.net
- `https://northwindcloud.com`
- <https://www.northwindcloud.com>

#### <a name="enforce-tls-1112"></a>Appliquer le protocole TLS 1.1/1.2

L’application autorise [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.0 par défaut, qui n’est plus considéré comme sécurisé par les normes du secteur telles que [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Pour appliquer des versions ultérieures de TLS, procédez comme suit :

1. Dans le volet de navigation gauche de la page d’application web, sélectionnez **Paramètres SSL**.

2. Dans **Version TLS**, sélectionnez la version minimale de TLS.

    ![Appliquer le protocole TLS 1.1 ou 1.2](media/solution-deployment-guide-geo-distributed/image44.png)

### <a name="create-a-traffic-manager-profile"></a>Créer un profil Traffic Manager

1. Sélectionnez **Créer une ressource** > **Mise en réseau** > **Profil Traffic Manager** > **Créer**.

2. Dans **Créer un profil Traffic Manager**, procédez comme suit :

    1. Sous **Nom**, entrez un nom pour le profil. Ce nom doit être unique au sein de la zone trafficmanager.net et produit le nom DNS trafficmanager.net qui est utilisé pour accéder au profil Traffic Manager.

    2. Sous **Méthode de routage**, sélectionnez la **méthode de routage géographique**.

    3. Sous **Abonnement**, sélectionnez l’abonnement sous lequel créer ce profil.

    4. Sous **Groupe de ressources**, créez un groupe de ressources où placer ce profil.

    5. Sous **Emplacement du groupe de ressources**, sélectionnez l’emplacement du groupe de ressources. Ce paramètre fait référence à l’emplacement du groupe de ressources et n’a pas d’impact sur le profil Traffic Manager déployé globalement.

    6. Sélectionnez **Create** (Créer).

    7. Lorsque le déploiement global du profil Traffic Manager est terminé, il est répertorié comme l’une des ressources dans le groupe de ressources concerné.

        ![Groupes de ressources dans le panneau Créer un profil Traffic Manager](media/solution-deployment-guide-geo-distributed/image45.png)

### <a name="add-traffic-manager-endpoints"></a>Ajouter des points de terminaison Traffic Manager

1. Dans la barre de recherche du portail, recherchez le nom du **profil Traffic Manager** créé dans la section précédente, puis sélectionnez le profil Traffic Manager dans les résultats affichés.

2. Dans **Profil Traffic Manager**, à la section **Paramètres**, sélectionnez **Points de terminaison**.

3. Sélectionnez **Ajouter**.

4. Ajout du point de terminaison Azure Stack Hub.

5. Pour **Type**, sélectionnez **Point de terminaison externe**.

6. Fournissez un **nom** pour ce point de terminaison, dans l’idéal, le nom du déploiement Azure Stack Hub.

7. Pour le nom de domaine complet (**FQDN**), utilisez l’URL externe de l’application web Azure Stack Hub.

8. Sous Géocartographie, sélectionnez la région ou le continent où se trouve la ressource. Par exemple, **Europe**.

9. Dans la liste déroulante Pays/Région qui s’affiche, sélectionnez le pays qui s’applique à ce point de terminaison. Par exemple, **Allemagne**.

10. Vérifiez que la case **Ajouter comme désactivé** est désélectionnée.

11. Sélectionnez **OK**.

12. Ajout du point de terminaison Azure :

    1. Sous **Type**, sélectionnez **Point de terminaison Azure**.

    2. Entrez un **Nom** pour le point de terminaison.

    3. Sous **Type de ressource cible**, sélectionnez **App Service**.

    4. Sous **Ressource cible**, sélectionnez **Choisir un service d’application** pour afficher la liste des applications Web sous le même abonnement. Dans **Ressources**, sélectionnez le service d’application utilisé en tant que premier point de terminaison.

13. Sous Géocartographie, sélectionnez la région ou le continent où se trouve la ressource. Par exemple, **Amérique du Nord/Amérique Centrale/Antilles**.

14. Dans la liste déroulante Pays/Région qui s’affiche, laissez ce champ vide pour sélectionner le regroupement régional complet au-dessus.

15. Vérifiez que la case **Ajouter comme désactivé** est désélectionnée.

16. Sélectionnez **OK**.

    > [!Note]  
    >  Créez au moins un point de terminaison en choisissant la portée géographique Tous (Monde) comme point de terminaison par défaut de la ressource.

17. Une fois l’ajout des deux points de terminaison terminé, ceux-ci s’affichent dans **Profil Traffic Manager** avec leur état de surveillance défini sur **En ligne**.

    ![État du point de terminaison de profil Traffic Manager](media/solution-deployment-guide-geo-distributed/image46.png)

#### <a name="global-enterprise-relies-on-azure-geo-distribution-capabilities"></a>Les multinationales s’appuient sur les fonctionnalités de géodistribution Azure

En dirigeant le trafic de données à l’aide d’Azure Traffic Manager et de points de terminaison spécifiquement répartis à travers le monde, les multinationales peuvent se conformer aux réglementations régionales et assurer la conformité et la protection des données, indispensables au succès des sites d’activité locaux et distants.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les modèles Azure Cloud, consultez [Modèles de conception cloud](https://docs.microsoft.com/azure/architecture/patterns).
