---
title: Déployer un cloud hybride avec Azure et Azure Stack | Microsoft Docs
description: Découvrez comment déployer un cloud hybride avec Azure et Azure Stack.
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
ms.date: 01/25/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: 97869ef7659cb5619ff962fc4b3bc8facbc599ed
ms.sourcegitcommit: eccbd0098ef652919f357ef6dba62b68abde1090
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67492448"
---
# <a name="tutorial-deploy-a-hybrid-cloud-solution-with-azure-and-azure-stack"></a>Didacticiel : Déployer une solution de cloud hybride avec Azure et Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Ce didacticiel vous montre comment déployer une solution de cloud hybride qui utilise le cloud public Azure et le cloud privé Azure Stack.

En utilisant une solution de cloud hybride, vous pouvez combiner les avantages de la conformité d’un cloud privé avec l’extensibilité du cloud public. En outre, vos développeurs peuvent tirer parti de l’écosystème de développement Microsoft et appliquer leurs compétences aux environnements cloud et locaux.

## <a name="overview-and-assumptions"></a>Vue d’ensemble et hypothèses

Suivez ce didacticiel pour configurer un flux de travail qui permet aux développeurs de déployer une application web identique vers un cloud public et un cloud privé. Cette application peut accéder à un réseau routable non-Internet hébergé sur le cloud privé. Ces applications web sont surveillées : en cas de pic de trafic, un programme modifie les enregistrements DNS pour rediriger le trafic vers le cloud public. Lorsque le trafic redescend au niveau avant le pic, le trafic est routé vers le cloud privé.

Ce tutoriel décrit les tâches suivantes :

> [!div class="checklist"]
> - Déployer un serveur de base de données SQL Server à connexion hybride.
> - Connecter une application web présente dans Azure global à un réseau hybride.
> - Configurer DNS pour une mise à l’échelle dans le cloud.
> - Configurer des certificats SSL pour une mise à l’échelle dans le cloud.
> - Configurez et déployez l’application web.
> - Créer un profil Traffic Manager et configurez-le pour une mise à l’échelle dans le cloud.
> - Configurer la supervision et les alertes Application Insights en cas d’augmentation du trafic.
> - Configurer la commutation automatique du trafic entre Azure global et Azure Stack.

### <a name="assumptions"></a>Hypothèses

Ce didacticiel suppose que vous disposez de connaissances de base sur Azure global et Azure Stack. Si vous voulez en savoir plus avant de commencer le didacticiel, consultez les articles suivants :

 - [Présentation de Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/)
 - [Concepts clés d’Azure Stack](../operator/azure-stack-overview.md)

Ce didacticiel part du principe que vous disposez d’un abonnement Azure. Si vous n’avez pas d’abonnement, vous pouvez [créer un compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Avant de commencer ce didacticiel, veillez à ce que les conditions suivantes soient réunies :

- Un Kit de développement Azure Stack (ASDK) ou un abonnement à un système intégré Azure Stack. Pour déployer un Kit de développement Azure Stack, suivez les instructions de [Déployer l’ASDK à l’aide du programme d’installation](../asdk/asdk-install.md).
- Votre installation Azure Stack doit avoir installé les éléments suivants :
  - Azure App Service. Travaillez avec votre opérateur Azure Stack pour déployer et configurer Azure App Service sur votre environnement. Ce didacticiel nécessite qu’App Service dispose d’au moins (1) rôle de travail dédié disponible.
  - Une image Windows Server 2016.
  - Un serveur Windows Server 2016 avec une image Microsoft SQL Server.
  - Les plans et offres appropriés.
  - Un nom de domaine pour votre application web. Si vous n’avez pas de nom de domaine, vous pouvez en acheter un auprès d’un fournisseur de domaine comme GoDaddy, Bluehost ou InMotion.
- Un certificat SSL pour votre domaine reçu d’une autorité de certification de confiance comme LetsEncrypt.
- Une application web qui communique avec une base de données SQL Server et prend en charge Application Insights. Vous pouvez télécharger l’exemple d’application [dotnetcore-sqldb-tutorial](https://github.com/Azure-Samples/dotnetcore-sqldb-tutorial) à partir de GitHub.
- Un réseau hybride entre un réseau virtuel Azure et un réseau virtuel Azure Stack. Pour obtenir des instructions détaillées, consultez [Configurer la connectivité de cloud hybride avec Azure et Azure Stack](azure-stack-solution-hybrid-connectivity.md).

- Un pipeline hybride d’intégration continue/déploiement continu (CI/CD) avec un agent de build privé sur Azure Stack. Pour obtenir des instructions détaillées, voir [Configurer l’identité de cloud hybride avec Azure et Azure Stack](azure-stack-solution-hybrid-identity.md).

## <a name="deploy-a-hybrid-connected-sql-server-database-server"></a>Déployer un serveur de base de données SQL Server à connexion hybride

1. Connectez-vous au portail utilisateur Azure Stack.

2. Sur le **Tableau de bord**, sélectionnez **Place de marché**.

    ![Place de marché Azure Stack](media/azure-stack-solution-hybrid-cloud/image1.png)

3. Dans **Place de marché**, sélectionnez **Calcul**, puis choisissez **Plus**. Sous **Plus**, sélectionnez l’image **Licence SQL Server gratuite : SQL Server 2017 Developer sur Windows Server**.

    ![Sélectionner une image de machine virtuelle](media/azure-stack-solution-hybrid-cloud/image2.png)

4. Dans **Licence SQL Server gratuite : SQL Server 2017 Developer sur Windows Server**, sélectionnez **Créer**.

5. Sur **Paramètres de base > Configurer les paramètres de base**, saisissez un **Nom** pour la machine virtuelle (VM), un **Nom d’utilisateur** pour l’association de sécurité de SQL Server et un **Mot de passe** pour l’association de sécurité.  Dans la liste déroulante **Abonnement**, sélectionnez l’abonnement sur lequel vous effectuez le déploiement. Pour le **Groupe de ressources**, utilisez **Choisir un élément déjà existant** et placez la machine virtuelle dans le même groupe de ressources que votre application web Azure Stack.

    ![Configurer les paramètres de base des machines virtuelles](media/azure-stack-solution-hybrid-cloud/image3.png)

6. Sous **Taille**, choisissez une taille pour votre machine virtuelle. Pour ce didacticiel, nous vous recommandons A2_Standard ou un DS2_V2_Standard.

7. Sous **Paramètres > Configurer les fonctionnalités facultatives**, configurez les paramètres suivants :

   - **Compte de stockage** : Créez un nouveau compte si nécessaire.
   - **Réseau virtuel** :

     > [!Important]  
     > Assurez-vous que votre machine virtuelle SQL Server est déployée sur le même réseau virtuel que les passerelles VPN.

   - **Adresse IP publique** : Conservez les paramètres par défaut.
   - **Groupe de sécurité réseau** : (NSG). Créer un NSG.
   - **Extensions et supervision** : Conservez les paramètres par défaut.
   - **Compte de stockage de diagnostics** : Créez un nouveau compte si nécessaire.
   - Cliquez sur **OK** pour enregistrer votre configuration.

     ![Configurer des fonctionnalités facultatives](media/azure-stack-solution-hybrid-cloud/image4.png)

8. Sous **Paramètres SQL Server**, configurez les paramètres suivants :
   - Pour **Connectivité SQL**, sélectionnez **Public (Internet)** .
   - Pour **Port**, conservez la valeur par défaut, **1433**.
   - Pour **Authentification SQL**, sélectionnez **Activer**.

     > [!Note]  
     > Lorsque vous activez l’authentification SQL, les informations de « SQLAdmin » que vous avez configurées dans **Paramètres de base** devraient se renseigner automatiquement.

   - Conservez les valeurs par défaut pour les autres paramètres. Sélectionnez **OK**.

     ![Configurer les paramètres de SQL Server](media/azure-stack-solution-hybrid-cloud/image5.png)

9. Sur **Résumé**, vérifiez la configuration de la machine virtuelle, puis sélectionnez **OK** pour démarrer le déploiement.

    ![Résumé de la configuration](media/azure-stack-solution-hybrid-cloud/image6.png)

10. La création de la machine virtuelle prend un certain temps. Vous pouvez afficher l’ÉTAT de vos machines virtuelles dans **Machines virtuelles**.

    ![Machines virtuelles](media/azure-stack-solution-hybrid-cloud/image7.png)

## <a name="create-web-apps-in-azure-and-azure-stack"></a>Créer des applications web dans Azure et Azure Stack

Le service Azure App Service simplifie l’exécution et la gestion d’une application web. Étant donné qu’Azure Stack est cohérent avec Azure, le service App Service peut s’exécuter dans les deux environnements. Vous allez utiliser App Service pour héberger votre application.

### <a name="create-web-apps"></a>Créer des applications web

1. Créez une application web dans Azure en suivant les instructions dans [Gérer un plan App Service dans Azure](https://docs.microsoft.com/azure/app-service/app-service-plan-manage#create-an-app-service-plan). Veillez à placer l’application web dans le même abonnement et groupe de ressources que votre réseau hybride.

2. Répétez l’étape précédente (1) dans Azure Stack.

### <a name="add-route-for-azure-stack"></a>Ajouter un itinéraire pour Azure Stack

Le service App Service sur Azure Stack doit être routable à partir de l’Internet public pour permettre aux utilisateurs d’accéder à votre application. Si votre Azure Stack est accessible à partir d’Internet, notez l’adresse IP ou l’URL publiques de l’application web Azure Stack.

Si vous utilisez un ASDK, vous pouvez [configurer un mappage NAT statique](../operator/azure-stack-create-vpn-connection-one-node.md#configure-the-nat-virtual-machine-on-each-azure-stack-development-kit-for-gateway-traversal) pour exposer App Service en dehors de l’environnement virtuel.

### <a name="connect-a-web-app-in-azure-to-a-hybrid-network"></a>Connecter une application web présente dans Azure à un réseau hybride

Pour fournir la connectivité entre le web frontal dans Azure et la base de données SQL Server dans Azure Stack, l’application web doit être connectée au réseau hybride entre Azure et Azure Stack. Pour activer la connectivité, vous devrez :

- Configurer la connectivité point à site.
- Configurer l’application web.
- Modifier la passerelle de réseau local dans Azure Stack.

### <a name="configure-the-azure-virtual-network-for-point-to-site-connectivity"></a>Configurer le réseau virtuel Azure pour une connectivité point à site

La passerelle de réseau virtuel du côté Azure du réseau hybride doit autoriser l’intégration des connexions point à site à Azure App Service.

1. Dans Azure, accédez à la page de la passerelle de réseau virtuel. Sous **Paramètres**, sélectionnez **Configuration de point à site**.

    ![Option point à site](media/azure-stack-solution-hybrid-cloud/image8.png)

2. Sélectionnez **Configurer maintenant** pour configurer la connectivité point à site.

    ![Démarrer la configuration de point à site](media/azure-stack-solution-hybrid-cloud/image9.png)

3. Sur la page de la configuration de **Point à site**, ajoutez la plage d’adresses IP privées que vous souhaitez utiliser dans la zone **Pool d’adresses**.

   > [!Note]  
   > Assurez-vous que la plage que vous spécifiez ne chevauche aucune des plages d’adresses déjà utilisées par les sous-réseaux dans les composants Azure global ou Azure Stack du réseau hybride.

   Sous **Type de Tunnel**, décochez **VPN IKEv2**. Sélectionnez **Enregistrer** pour terminer la configuration de point-to-site.

   ![Paramètres de point à site](media/azure-stack-solution-hybrid-cloud/image10.png)

### <a name="integrate-the-azure-app-service-app-with-the-hybrid-network"></a>Intégrer l’application Azure App Service avec le réseau hybride

1. Pour connecter l’application au réseau virtuel Azure, suivez les instructions fournies dans la section [Intégration de réseau virtuel requise par la passerelle](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet#gateway-required-vnet-integration).

2. Accédez aux **Paramètres** du plan App Service hébergeant l’application web. Sous **Paramètres**, sélectionnez **Mise en réseau**.

    ![Configurer la mise en réseau](media/azure-stack-solution-hybrid-cloud/image11.png)

3. Dans **Intégration au réseau virtuel**, sélectionnez **Cliquer ici pour gérer**.

    ![Gérer l’intégration au réseau virtuel](media/azure-stack-solution-hybrid-cloud/image12.png)

4. Sélectionnez le réseau virtuel à configurer. Sous **ADRESSES IP ROUTÉES VERS LE RÉSEAU VIRTUEL**, entrez la plage d’adresses IP pour les espaces d’adressage du réseau virtuel Azure, du réseau virtuel Azure Stack et du réseau de point à site. Cliquez sur **Enregistrer** pour valider et enregistrer ces paramètres.

    ![Plages d’adresses IP à router](media/azure-stack-solution-hybrid-cloud/image13.png)

Pour en savoir plus sur comment App Service s’intègre aux réseaux virtuels Azure, consultez [Intégrer votre application à un réseau virtuel Azure](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet).

### <a name="configure-the-azure-stack-virtual-network"></a>Configurer le réseau virtuel Azure Stack

La passerelle de réseau local dans le réseau virtuel Azure Stack doit être configurée pour router le trafic à partir de la plage d’adresses de point-to-site App Service.

1. Dans Azure Stack, accédez à la **Passerelle de réseau locale**. Sous **Paramètres**, sélectionnez **Configuration**.

    ![Option de configuration de la passerelle](media/azure-stack-solution-hybrid-cloud/image14.png)

2. Dans **Espace d’adressage**, entrez la plage d’adresses point à site pour la passerelle de réseau virtuel dans Azure.

    ![Espace d’adressage de point à site](media/azure-stack-solution-hybrid-cloud/image15.png)

3. Sélectionnez **Enregistrer** pour valider et enregistrer la configuration.

## <a name="configure-dns-for-cross-cloud-scaling"></a>Configurer DNS pour une mise à l’échelle dans le cloud

En configurant correctement DNS pour les applications inter-cloud, les utilisateurs peuvent accéder aux instances globales Azure et Azure Stack de votre application web. La configuration DNS pour ce didacticiel permet également à Azure Traffic Manager de router le trafic lorsque la charge augmente ou diminue.

Ce didacticiel utilise Azure DNS pour gérer le serveur DNS, car des domaines App Service ne fonctionneront pas.

### <a name="create-subdomains"></a>Créer des sous-domaines

Étant donné que Traffic Manager s’appuie sur les CNAME DNS, un sous-domaine est nécessaire pour router correctement le trafic vers les points de terminaison. Pour plus d’informations sur les enregistrements DNS et le mappage de domaine, voir [Mapper des domaines avec Traffic Manager](https://docs.microsoft.com/azure/app-service/web-sites-traffic-manager-custom-domain-name).

Pour le point de terminaison Azure, vous allez créer un sous-domaine que les utilisateurs peuvent utiliser pour accéder à votre application web. Pour ce didacticiel, vous pouvez utiliser **app.northwind.com**, mais nous vous conseillons de personnaliser cette valeur en fonction de votre propre domaine.

Vous devrez également créer un sous-domaine avec un enregistrement A pour le point de terminaison Azure Stack. Vous pouvez utiliser **azurestack.northwind.com**.

### <a name="configure-a-custom-domain-in-azure"></a>Configurer un domaine personnalisé dans Azure

1. Ajoutez le nom d’hôte **app.northwind.com** à l’application web Azure en [mappant un CNAME vers Azure App Service](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record).

### <a name="configure-custom-domains-in-azure-stack"></a>Configurer des domaines personnalisés dans Azure Stack

1. Ajoutez le nom d’hôte **azurestack.northwind.com** à l’application web Azure Stack en [mappant un enregistrement A vers Azure App Service](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-an-a-record). Utilisez l’adresse IP routable sur Internet pour l’application App Service.

2. Ajoutez le nom d’hôte **app.northwind.com** à l’application web Azure Stack en [mappant un CNAME vers Azure App Service](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record). Utilisez le nom d’hôte que vous avez configuré à l’étape précédente (1) comme cible pour le CNAME.

## <a name="configure-ssl-certificates-for-cross-cloud-scaling"></a>Configurer des certificats SSL pour une mise à l’échelle dans le cloud

Il est important de vérifier que les données sensibles recueillies par votre application web sont sécurisées tant en transit vers la base de données SQL qu’au repos dans celle-ci.

Vous allez configurer vos applications web Azure et Azure Stack pour utiliser des certificats SSL pour tout le trafic entrant.

### <a name="add-ssl-to-azure-and-azure-stack"></a>Ajouter SSL à Azure et Azure Stack

Pour ajouter SSL à Azure :

1. Assurez-vous que le certificat SSL que vous obtenez est valide pour le sous-domaine que vous avez créé. (Vous pouvez utiliser des certificats avec caractères génériques.)

2. Dans Azure, suivez les instructions fournies dans les sections **Préparer votre application web** et **Lier votre certificat SSL** de l’article [Lier un certificat SSL personnalisé existant à Azure Web Apps](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-ssl). Sélectionnez **SSL basé sur SNI** en tant que **Type de SSL**.

3. Rediriger tout le trafic vers le port HTTPS. Suivez les instructions dans la section **Appliquer le protocole HTTPS** de l’article [Lier un certificat SSL personnalisé existant à Azure Web Apps](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-ssl).

Pour ajouter SSL à Azure Stack :

- Répétez les étapes 1 à 3 effectuées pour Azure.

## <a name="configure-and-deploy-the-web-app"></a>Configurer et déployer l’application web

Vous allez configurer le code d’application pour envoyer les données de télémétrie à l’instance Application Insights appropriée, puis configurer les applications web avec les chaînes de connexion adéquates. Pour en savoir plus sur Application Insights, consultez [Présentation d’Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview)

### <a name="add-application-insights"></a>Ajouter Application Insights

1. Ouvrez votre application web dans Microsoft Visual Studio.

2. [Ajoutez Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#enable-client-side-telemetry-for-web-applications) à votre projet pour transmettre les données de télémétrie qu’Application Insights utilise pour créer des alertes lorsque le trafic web augmente ou diminue.

### <a name="configure-dynamic-connection-strings"></a>Configurer des chaînes de connexion dynamiques

Chaque instance de l’application web utilise une méthode différente pour se connecter à la base de données SQL. L’application dans Azure utilise l’adresse IP privée de la machine virtuelle (VM) SQL Server, et l’application dans Azure Stack l’adresse IP publique de la machine virtuelle SQL Server.

> [!Note]  
> Sur un système intégré Azure Stack, l’adresse IP publique ne doit pas être routable sur Internet. Sur un Kit de développement Azure Stack (ASDK), l’adresse IP publique n’est pas routable en dehors de l’ASDK.

Vous pouvez utiliser des variables d’environnement App Service pour transmettre une chaîne de connexion différente à chaque instance de l’application.

1. Ouvrez l’application dans Visual Studio.

2. Ouvrez Startup.cs et recherchez le bloc de code suivant :

    ```C#
    services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
    ```

3. Remplacez le bloc de code précédent par le code suivant qui utilise une chaîne de connexion définie dans le fichier *appsettings.json* :

    ```C#
    services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
     // Automatically perform database migration
     services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
    ```

### <a name="configure-app-service-app-settings"></a>Configurer les paramètres d’application App Service

1. Créez des chaînes de connexion pour Azure et Azure Stack. Les chaînes doivent être identiques à l’exception des adresses IP utilisées.

2. Dans Azure et Azure Stack, ajoutez la chaîne de connexion appropriée [comme paramètre d’application](https://docs.microsoft.com/azure/app-service/web-sites-configure) dans l’application web en utilisant `SQLCONNSTR\_` comme préfixe dans le nom.

3. **Enregistrez** les paramètres de l’application web et redémarrez l’application.

## <a name="enable-automatic-scaling-in-global-azure"></a>Activer la mise à l’échelle automatique dans Azure global

Lorsque vous créez votre application web dans un environnement App Service, l’application démarre avec une seule instance. Vous pouvez ensuite augmenter la taille des instances automatiquement pour fournir des ressources de calcul supplémentaires à votre application. De même, vous pouvez automatiquement diminuer la taille et le nombre d’instances dont votre application a besoin.

> [!Note]  
> Vous devez disposer d’un plan App Service pour configurer l’augmentation ou la diminution de la taille des instances. Si vous n’avez pas de plan, créez-le avant de commencer les étapes suivantes.

### <a name="enable-automatic-scale-out"></a>Activer l’augmentation automatique de la taille des instances

1. Dans Azure, recherchez le plan App Service des sites pour lesquels vous souhaitez augmenter la taille des instances, puis sélectionnez **Scale-out (plan App Service)** .

    ![Montée en charge](media/azure-stack-solution-hybrid-cloud/image16.png)

2. Sélectionnez **Activer la mise à l’échelle automatique**.

    ![Activer la mise à l’échelle automatique](media/azure-stack-solution-hybrid-cloud/image17.png)

3. Fournissez un nom pour le **Nom du paramètre de mise à l’échelle automatique**. Pour la règle de mise à l’échelle automatique **Par défaut**, sélectionnez **Mettre à l’échelle selon une mesure**. Définissez les **Limites d’instance** sur **Minimum : 1**, **Maximum : 10** et **Par défaut : 1**.

    ![Configurer la mise à l’échelle automatique](media/azure-stack-solution-hybrid-cloud/image18.png)

4. Sélectionnez **+Ajouter une règle**.

5. Dans **Source de la mesure**, sélectionnez **Ressource actuelle**. Utilisez les critères et les actions suivantes pour la règle.

**Critères**

1. Sous **Agrégation du temps**, sélectionnez **Moyenne**.

2. Sous **Nom de mesure**, sélectionnez **Pourcentage UC**.

3. Sous **Opérateur**, sélectionnez **Supérieur à**.

   - Définissez le **Seuil** sur **50**.
   - Définir la **Durée** sur **10**.

**Action**

1. Sous **Opération**, sélectionnez **Augmenter le nombre de**.

2. Définissez le **Nombre d’instances** sur **2**.

3. Définissez le **Refroidissement** sur **5**.

4. Sélectionnez **Ajouter**.

5. Sélectionnez **+Ajouter une règle**.

6. Dans **Source de la mesure**, sélectionnez **Ressource actuelle**.

   > [!Note]  
   > La ressource actuelle contient le nom/GUID de votre plan App Service, et les listes déroulantes **Type de ressource** et **Ressource** sont indisponibles.

### <a name="enable-automatic-scale-in"></a>Activer la diminution automatique de la taille des instances

Lorsque le trafic diminue, l’application web Azure peut diminuer automatiquement le nombre d’instances actives afin de réduire les coûts. Cette action est moins agressive que l’augmentation de la taille des instances et minimise l’impact sur les utilisateurs de l’application.

1. Accédez à la condition d’augmentation de la taille des instances **Par défaut**, sélectionnez **+ Ajouter une règle**. Utilisez les critères et les actions suivantes pour la règle.

**Critères**

1. Sous **Agrégation du temps**, sélectionnez **Moyenne**.

2. Sous **Nom de mesure**, sélectionnez **Pourcentage UC**.

3. Sous **Opérateur**, sélectionnez **Inférieur à**.

   - Définissez le **Seuil** sur **30**.
   - Définir la **Durée** sur **10**.

**Action**

1. Sous **Opération**, sélectionnez **Diminuer le nombre de**.

   - Définissez le **Nombre d’instances** sur **1**.
   - Définissez le **Refroidissement** sur **5**.

2. Sélectionnez **Ajouter**.

## <a name="create-a-traffic-manager-profile-and-configure-cross-cloud-scaling"></a>Créer un profil Traffic Manager et le configurer pour une mise à l’échelle dans le cloud

Créez un profil Traffic Manager dans Azure, puis configurez les points de terminaison pour activer la mise à l’échelle inter-cloud.

### <a name="create-traffic-manager-profile"></a>Créer un profil Traffic Manager

1. Sélectionnez **Créer une ressource**.
2. Sélectionnez **Mise en réseau**.
3. Sélectionnez **Profil Traffic Manager**, puis configurez les paramètres suivants :

   - Sous **Nom**, entrez un nom pour votre profil. Ce nom **doit** être unique dans la zone trafficmanager.net et il est utilisé pour créer un nom DNS (par exemple, northwindstore.trafficmanager.net).
   - Pour la **Méthode de routage**, sélectionnez la méthode **Pondérée**.
   - Sous **Abonnement**, sélectionnez l’abonnement dans lequel vous souhaitez créer ce profil.
   - Sous **Groupe de ressources**, créez un groupe de ressources pour ce profil.
   - Sous **Emplacement du groupe de ressources**, sélectionnez l’emplacement du groupe de ressources. Ce paramètre fait référence à l’emplacement du groupe de ressources et n’a pas d’incidence sur le profil Traffic Manager déployé globalement.

4. Sélectionnez **Créer**.

    ![Créer un profil Traffic Manager](media/azure-stack-solution-hybrid-cloud/image19.png)

   Lorsque le déploiement global de votre profil Traffic Manager est terminé, il apparait dans la liste des ressources pour le groupe de ressources sous lequel vous l’avez créé.

### <a name="add-traffic-manager-endpoints"></a>Ajouter des points de terminaison Traffic Manager

1. Recherchez le profil Traffic Manager que vous avez créé. Si vous avez accédé au groupe de ressources pour le profil, sélectionnez celui-ci.

2. Dans **Profil Traffic Manager**, sous **PARAMÈTRES**, sélectionnez **Points de terminaison**.

3. Sélectionnez **Ajouter**.

4. Dans **Ajouter un point de terminaison**, utilisez les paramètres suivants pour Azure Stack :

   - Pour **Type**, sélectionnez **Point de terminaison externe**.
   - Entrez un **Nom** pour le point de terminaison.
   - Pour **Nom de domaine complet (FQDN) ou adresse IP**, entrez l’URL externe de votre application web Azure Stack.
   - Pour **Poids**, conservez la valeur par défaut **1**. Ce poids a pour effet que tout le trafic est dirigé vers ce point de terminaison s’il est intègre.
   - Laissez la case **Ajouter comme désactivé** décochée.

5. Sélectionnez **OK** pour enregistrer le point de terminaison Azure Stack.

Vous configurerez ensuite le point de terminaison Azure.

1. Dans **Profil Traffic Manager**, sélectionnez **Points de terminaison**.
2. Sélectionnez **+Ajouter**.
3. Dans **Ajouter un point de terminaison**, utilisez les paramètres suivants pour Azure :

   - Sous **Type**, sélectionnez **Point de terminaison Azure**.
   - Entrez un **Nom** pour le point de terminaison.
   - Sous **Type de ressource cible**, sélectionnez **App Service**.
   - Sous **Ressource cible**, sélectionnez **Choisir un service d’application** pour afficher la liste des applications Web dans le même abonnement.
   - Dans **Ressources**, choisissez le service d’application que vous souhaitez ajouter en tant que premier point de terminaison.
   - Pour **Poids**, sélectionnez **2**. Ainsi, tout le trafic est dirigé vers ce point de terminaison si le point de terminaison principal n’est pas intègre ou si vous disposez d’une règle/alerte qui redirige le trafic lorsqu’elle est déclenchée.
   - Laissez la case **Ajouter comme désactivé** décochée.

4. Sélectionnez **OK** pour enregistrer le point de terminaison Azure.

Une fois que les deux points de terminaison sont configurés, ils sont répertoriés dans **Profil Traffic Manager** lorsque vous sélectionnez **Points de terminaison**. L’exemple sur la capture d’écran suivante montre deux points de terminaison, avec les informations d’état et de configuration pour chacun d’entre eux.

![Points de terminaison](media/azure-stack-solution-hybrid-cloud/image20.png)

## <a name="set-up-application-insights-monitoring-and-alerting"></a>Configurer la supervision et les alertes Application Insights

Azure Application Insights vous permet de surveiller votre application et d’envoyer des alertes en fonction des conditions que vous configurez. Voici quelques exemples : l’application n’est pas disponible, rencontre des erreurs ou présente des problèmes de performances.

Les mesures d’Application Insights vous permettront de créer des alertes. Lorsque ces alertes se déclenchent, l’instance de votre application web bascule automatiquement d’Azure Stack vers Azure pour augmenter la taille des instances avant de revenir à Azure Stack pour la diminuer.

### <a name="create-an-alert-from-metrics"></a>Créer une alerte à partir de mesures

Pour ce didacticiel, accédez au groupe de ressources, puis sélectionnez l’instance Application Insights pour ouvrir **Application Insights**.

![Application Insights](media/azure-stack-solution-hybrid-cloud/image21.png)

Cet affichage vous permettra de créer une alerte pour augmenter la taille des instances et une autre pour la diminuer.

### <a name="create-the-scale-out-alert"></a>Créer l’alerte pour augmenter la taille des instances

1. Sous **CONFIGURER**, sélectionnez **Alertes (classique)** .
2. Sélectionnez **Ajouter une alerte métrique (classique)** .
3. Dans **Ajouter une règle**, configurez les paramètres suivants :

   - Pour **Nom**, entrez **Augmenter la taille dans Azure Cloud**.
   - La **Description** est facultative.
   - Sous **Source** > **Alerte pour**, sélectionnez **Métriques**.
   - Sous **Critères**, sélectionnez votre abonnement, le groupe de ressources pour votre profil Traffic Manager, et le nom du profil Traffic Manager pour la ressource.

4. Pour **Mesure**, sélectionnez **Taux de requêtes**.
5. Pour **Condition**, sélectionnez **Supérieur à**.
6. Pour **Seuil**, entrez **2**.
7. Pour **Période**, sélectionnez **Au cours des 5 dernières minutes**.
8. Sous **Notifier via** :
   - Cochez la case pour **Envoyer des e-mails aux propriétaires, contributeurs et lecteurs**.
   - Saisissez votre adresse e-mail pour **Adresse(s) e-mail administrateur supplémentaire(s)** .

9. Dans la barre de menus, sélectionnez **Enregistrer**.

### <a name="create-the-scale-in-alert"></a>Créer l’alerte pour diminuer la taille des instances

1. Sous **CONFIGURER**, sélectionnez **Alertes (classique)** .
2. Sélectionnez **Ajouter une alerte métrique (classique)** .
3. Dans **Ajouter une règle**, configurez les paramètres suivants :

   - Pour **Nom**, entrez **Diminuer la taille dans Azure Stack**.
   - La **Description** est facultative.
   - Sous **Source** > **Alerte pour**, sélectionnez **Métriques**.
   - Sous **Critères**, sélectionnez votre abonnement, le groupe de ressources pour votre profil Traffic Manager, et le nom du profil Traffic Manager pour la ressource.

4. Pour **Mesure**, sélectionnez **Taux de requêtes**.
5. Pour **Condition**, sélectionnez **Inférieur à**.
6. Pour **Seuil**, entrez **2**.
7. Pour **Période**, sélectionnez **Au cours des 5 dernières minutes**.
8. Sous **Notifier via** :
   - Cochez la case pour **Envoyer des e-mails aux propriétaires, contributeurs et lecteurs**.
   - Saisissez votre adresse e-mail pour **Adresse(s) e-mail administrateur supplémentaire(s)** .

9. Dans la barre de menus, sélectionnez **Enregistrer**.

La capture d’écran suivante montre les alertes pour l’augmentation et la diminution de la taille des instances.

   ![Alertes (classique)](media/azure-stack-solution-hybrid-cloud/image22.png)

## <a name="redirect-traffic-between-azure-and-azure-stack"></a>Rediriger le trafic entre Azure et Azure Stack

Vous pouvez configurer le basculement manuel ou automatique du trafic de votre application web pour basculer entre Azure et Azure Stack.

### <a name="configure-manual-switching-between-azure-and-azure-stack"></a>Configurer un basculement manuel entre Azure et Azure Stack

Lorsque votre site web atteint les seuils que vous avez configurés, vous recevez une alerte. Utilisez les étapes suivantes pour rediriger manuellement le trafic vers Azure.

1. Dans le portail Azure, sélectionnez votre profil Traffic Manager.

    ![Points de terminaison Traffic Manager](media/azure-stack-solution-hybrid-cloud/image20.png)

2. Sélectionnez **Points de terminaison**.
3. Sélectionnez le **point de terminaison Azure**.
4. Sous **État**, sélectionnez **Activé**, puis **Enregistrer**.

    ![Activer un point de terminaison Azure](media/azure-stack-solution-hybrid-cloud/image23.png)

5. Sous **Points de terminaison**du profil Traffic Manager, sélectionnez **Point de terminaison externe**.
6. Sous **État**, sélectionnez **Désactivé**, puis **Enregistrer**.

    ![Désactiver un point de terminaison Azure](media/azure-stack-solution-hybrid-cloud/image24.png)

Une fois les points de terminaison configurés, le trafic de l’application accède à votre application web augmentant de la taille des instances Azure plutôt qu’à l’application web Azure Stack.

 ![Points de terminaison modifiés](media/azure-stack-solution-hybrid-cloud/image25.png)

Pour inverser le flux vers Azure Stack, utilisez les étapes précédentes pour :

- activer le point de terminaison Azure Stack ;
- désactiver le point de terminaison Azure.

### <a name="configure-automatic-switching-between-azure-and-azure-stack"></a>Configurer un basculement automatique entre Azure et Azure Stack

Vous pouvez également utiliser la supervision d’Application Insights si votre application s’exécute dans un environnement [serverless](https://azure.microsoft.com/overview/serverless-computing/) fourni par Azure Functions.

Dans ce scénario, vous pouvez configurer Application Insights pour utiliser un webhook qui appelle une application de fonction. Cette application active ou désactive automatiquement un point de terminaison en réponse à une alerte.

Utilisez les étapes suivantes comme guide pour configurer le basculement automatique du trafic.

1. Créez une application Azure Function.
2. Créez une fonction déclenchée par HTTP.
3. Importez les kits de développement logiciel (SDK) Azure pour Resource Manager, Web Apps et Traffic Manager.
4. Développer du code pour :

   - Vous authentifier auprès de votre abonnement Azure.
   - Utiliser un paramètre qui active ou désactive les points de terminaison Traffic Manager afin de diriger le trafic vers Azure ou Azure Stack.

5. Enregistrer votre code et ajouter l’URL de l’application de fonction ainsi que les paramètres appropriés à la section **Webhook** des paramètres de règle d’alerte Application Insights.
6. Le trafic est redirigé automatiquement lorsqu’une alerte Application Insights se déclenche.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les modèles Azure Cloud, consultez [Modèles de conception cloud](https://docs.microsoft.com/azure/architecture/patterns).
