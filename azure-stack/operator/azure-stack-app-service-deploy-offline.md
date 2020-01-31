---
title: Déployer Azure App Service dans un environnement hors connexion au sein d’Azure Stack Hub
description: Découvrez comment déployer Azure App Service dans un environnement Azure Stack Hub sécurisé par AD FS hors connexion.
author: BryanLa
ms.topic: article
ms.date: 01/13/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 01/13/2020
ms.openlocfilehash: fe20a682041e3e23e2a67957ecdce85f922fbf20
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76876369"
---
# <a name="deploy-azure-app-service-in-an-offline-environment-in-azure-stack-hub"></a>Déployer Azure App Service dans un environnement hors connexion au sein d’Azure Stack Hub

> [!IMPORTANT]
> Appliquez la mise à jour 1910 à votre système intégré Azure Stack Hub ou déployez le dernier kit de développement Azure Stack Hub (ASDK) avant de déployer Azure App Service 1.8.

Les instructions de cet article vous permettent de déployer le [fournisseur de ressources Azure App Service](azure-stack-app-service-overview.md) dans un environnement Azure Stack Hub qui :
- n’est pas connecté à internet.
- est sécurisé par les services AD FS (Active Directory Federation Services).

> [!IMPORTANT]
> Avant d’exécuter le programme d’installation du fournisseur de ressources, vérifiez que vous avez bien effectué les étapes décrites dans [Prérequis pour le déploiement d’Azure App Service sur Azure Stack Hub](azure-stack-app-service-before-you-get-started.md). Vous devez également lire les [notes de publication](azure-stack-app-service-release-notes-update-eight.md) qui accompagnent la version 1.8 afin de découvrir les nouvelles fonctionnalités, les correctifs et les problèmes connus susceptibles d’affecter votre déploiement.

Pour ajouter le fournisseur de ressources Azure App Service à votre déploiement Azure Stack Hub hors connexion, vous devez effectuer ces tâches de niveau supérieur :

1. Effectuez les [étapes préalables](azure-stack-app-service-before-you-get-started.md) (telles que l’achat de certificats, ce qui peut prendre quelques jours avant leur réception).
2. [Téléchargez et extrayez les fichiers d’installation et helper](azure-stack-app-service-before-you-get-started.md) sur une machine connectée à Internet.
3. Créez un package d’installation hors connexion.
4. Exécutez le fichier du programme d’installation appservice.exe.

## <a name="create-an-offline-installation-package"></a>Créer un package d'installation hors connexion

Pour déployer Azure App Service dans un environnement hors connexion, créez d’abord un package d’installation hors connexion sur une machine connectée à Internet.

1. Exécutez le programme d’installation AppService.exe sur une machine connectée à Internet.

2. Sélectionnez **Avancé** > **Créer un package d’installation hors connexion**.

    ![Créer un package hors connexion dans le programme d’installation Azure App Service][1]

3. Le programme d’installation Azure App Service crée un package d’installation hors connexion et affiche le chemin pour y accéder. Vous pouvez sélectionner **Ouvrir le dossier** pour ouvrir le dossier dans l’Explorateur de fichiers.

    ![Package d’installation hors connexion généré dans le programme d’installation Azure App Service](media/azure-stack-app-service-deploy-offline/image02.png)

4. Copiez le programme d’installation AppService.exe et le package d’installation hors connexion sur l’ordinateur hôte Azure Stack Hub.

## <a name="complete-the-offline-installation-of-azure-app-service-on-azure-stack-hub"></a>Terminer l’installation hors connexion d’Azure App Service sur Azure Stack Hub

1. Exécutez appservice.exe en tant qu’administrateur sur un ordinateur ayant accès au point de terminaison de gestion des ressources Azure de l’administrateur Azure Stack Hub.

1. Sélectionnez **Avancé** > **Effectuer l’installation hors connexion**.

    ![Effectuer une installation hors connexion dans le programme d’installation Azure App Service][2]

1. Accédez à l’emplacement du package d’installation hors connexion que vous avez créé, puis sélectionnez **Suivant**.

    ![Spécifier le chemin du package d’installation hors connexion dans le programme d’installation Azure App Service](media/azure-stack-app-service-deploy-offline/image04.png)

1. Passez en revue et acceptez les termes du contrat de licence logiciel Microsoft, puis sélectionnez **Suivant**.

1. Passez en revue et acceptez les termes du contrat de licence tiers, puis sélectionnez **Suivant**.


1. Vérifiez que les informations de configuration du cloud Azure App Service sont correctes. Si vous avez utilisé les paramètres par défaut durant le déploiement de l’ASDK, vous pouvez accepter les valeurs par défaut. Cependant, si vous avez personnalisé les options lors du déploiement d’Azure Stack Hub, ou que vous effectuez le déploiement sur un système intégré, vous devez modifier les valeurs dans cette fenêtre de façon à refléter ces modifications. Par exemple, si vous utilisez le suffixe de domaine mycloud.com, vous devez remplacer votre point de terminaison Azure Resource Manager de locataire Azure Stack Hub par `management.<region>.mycloud.com`. Après avoir vérifié vos informations, sélectionnez **Suivant**.

    ![Configurer le cloud Azure App Service dans le programme d’installation Azure App Service][3]

1. Sur la page du programme d’installation App Service suivante, vous devez vous connecter à votre Azure Stack Hub :

    1. Sélectionnez la méthode de connexion que vous souhaitez utiliser : **Informations d’identification** ou **Principal du service**
        - **Informations d'identification**
            - Si vous utilisez Azure Active Directory (Azure AD), entrez le compte et le mot de passe d'administrateur Azure AD que vous avez indiqués lors du déploiement d'Azure Stack Hub. Sélectionnez **Connecter**.
            - Si vous utilisez Active Directory Federation Services (AD FS), fournissez votre compte d’administrateur. Par exemple : cloudadmin@azurestack.local. Entrez votre mot de passe, puis sélectionnez **Se connecter**.
        - **Principal du service**
            - Le principal de service que vous utilisez **doit** avoir des droits de **propriétaire** sur l'**abonnement fournisseur par défaut**
            - Indiquez l'**ID du principal de service**, le **fichier de certificat** et le **mot de passe**, puis sélectionnez **Se connecter**.

    1. Dans **Abonnements Azure Stack Hub**, sélectionnez **Abonnement au fournisseur par défaut**.  Azure App Service sur Azure Stack Hub **doit** être déployé dans l'**abonnement au fournisseur par défaut**.

    1. Dans **Emplacements Azure Stack Hub**, sélectionnez l'emplacement qui correspond à la région où vous effectuez le déploiement. Par exemple, sélectionnez **local** si vous effectuez le déploiement sur ASDK.

1. Vous pouvez autoriser le programme d’installation Azure App Service à créer un réseau virtuel et des sous-réseaux associés. Vous pouvez aussi déployer sur un réseau virtuel existant, comme ce qui est configuré via [ces étapes](azure-stack-app-service-before-you-get-started.md#virtual-network).
   - Pour utiliser la méthode du programme d’installation Azure App Service, sélectionnez **Créer un réseau virtuel avec les paramètres par défaut**, acceptez les valeurs par défaut, puis sélectionnez **Suivant**.
   - Pour déployer sur un réseau existant, sélectionnez **Utiliser le réseau virtuel et les sous-réseaux existants**, puis :
       1. Sélectionnez l’option **Groupe de ressources** qui contient votre réseau virtuel.
       2. Choisissez le nom du **réseau virtuel** sur lequel vous voulez déployer.
       3. Sélectionnez les valeurs **Sous-réseau** correspondant à chacun des sous-réseaux de rôle nécessaires.
       4. Sélectionnez **Suivant**.

      ![Informations sur le réseau et le sous-réseau virtuels dans le programme d’installation Azure App Service][5]

1. Entrez les informations du partage de fichiers, puis sélectionnez **Suivant**. L’adresse du partage de fichiers doit utiliser le nom de domaine complet (FQDN) ou bien l’adresse IP de votre serveur de fichiers. Par exemple : \\\appservicefileserver.local.cloudapp.azurestack.external\websites ou \\\10.0.0.1\websites.  Si vous utilisez un serveur de fichiers qui est joint à un domaine, vous devez entrer le nom d’utilisateur complet incluant le domaine. Par exemple : `<myfileserverdomain>\<FileShareOwner>`.

    > [!NOTE]
    > Le programme d’installation tente de tester la connectivité au partage de fichiers avant de continuer. Cependant, si vous avez choisi de déployer sur un réseau virtuel existant, il est possible que le programme d’installation ne puisse pas se connecter au partage de fichiers et affiche un avertissement vous demandant si vous voulez continuer. Vérifiez les informations du partage de fichiers et continuez si elles sont correctes.

   ![Informations sur le partage de fichiers dans le programme d’installation Azure App Service][8]

1. Sur la page suivante :
    1. Dans la zone **ID d’application d’identité**, entrez le GUID de l’application que vous utilisez pour l’identité (à partir d’Azure AD).
    1. Dans la zone **fichier de certificat d’application d’identité**, entrez (ou accédez à) l’emplacement du fichier de certificat.
    1. Dans la zone **Mot de passe du certificat d’application d’identité**, entrez le mot de passe du certificat. Ce mot de passe est celui que vous avez noté lorsque vous avez utilisé le script pour créer les certificats.
    1. Dans la zone **fichier de certificat racine Azure Resource Manager**, entrez (ou accédez à) l’emplacement du fichier de certificat.
    1. Sélectionnez **Suivant**.

    ![Entrer l’ID d’application et les informations de certificat dans le programme d’installation Azure App Service][10]

1. Pour chacune des trois zones de fichier de certificat, cliquez sur **Parcourir** et accédez au fichier de certificat approprié. Vous devez fournir le mot de passe pour chaque certificat. Ces certificats sont ceux que vous avez créés lors de l’étape [Créer les certificats requis](azure-stack-app-service-before-you-get-started.md#get-certificates). Cliquez sur **Suivant** après avoir entré toutes les informations.

    | Box | Exemple de nom de fichier de certificat |
    | --- | --- |
    | **Fichier de certificat SSL par défaut d’App Service** | \_.appservice.local.AzureStack.external.pfx |
    | **Fichier de certificat SSL d’API App Service** | api.appservice.local.AzureStack.external.pfx |
    | **Fichier de certificat SSL Publisher App Service** | ftp.appservice.local.AzureStack.external.pfx |

    Si vous avez utilisé un suffixe de domaine différent lorsque vous avez créé les certificats, vos noms de fichier n’utilisent pas *local.AzureStack.external*. À la place, utilisez vos informations de domaine personnalisées.

    ![Entrer informations de certificat SSL dans le programme d’installation Azure App Service][11]

1. Entrez les détails SQL Server de l’instance de serveur utilisée pour héberger les bases de données du fournisseur de ressources Azure App Service, puis sélectionnez **Suivant**. Le programme d’installation valide les propriétés de connexion SQL. Pour le nom du serveur SQL Server, vous **devez** entrer l’adresse IP interne ou le nom de domaine complet (FQDN).

    > [!NOTE]
    > Le programme d’installation tente de tester la connectivité à l’ordinateur exécutant SQL Server avant de continuer. Cependant, si vous avez choisi d’effectuer le déploiement sur un réseau virtuel existant, il est possible que le programme d’installation ne puisse pas se connecter à l’ordinateur exécutant SQL Server et affiche un avertissement vous demandant si vous voulez continuer. Vérifiez les informations du serveur SQL Server et continuez si elles sont correctes.
    >
    > À compter d’Azure App Service sur Azure Stack Hub 1.3 et version ultérieure, le programme d’installation vérifie si l’ordinateur exécutant SQL Server a l’autonomie de la base de données activée au niveau de SQL Server. Si ce n’est pas le cas, l’exception suivante s’affiche :
    > ```sql
    >    Enable contained database authentication for SQL server by running below command on SQL server (Ctrl+C to copy)
    >    ***********************************************************
    >    sp_configure 'contained database authentication', 1;
    >    GO
    >    RECONFIGURE;
    >    GO
    >    ***********************************************************
    > ```
    > Pour plus d’informations, consultez les [notes de publication pour Azure App Service sur Azure Stack Hub 1.3](azure-stack-app-service-release-notes-update-three.md).

    ![Entrer les informations sur le serveur SQL Server dans le programme d’installation Azure App Service][12]

1. Passez en revue de l’instance de rôle et les options de la référence SKU. Les valeurs par défaut sont remplies avec le nombre minimal d’instances et la référence SKU minimale pour chaque rôle dans un déploiement ASDK. Un résumé des exigences en termes de processeur virtuel et de mémoire est fourni pour vous aider à planifier votre déploiement. Une fois vos sélections effectuées, sélectionnez **Suivant**.

     > [!NOTE]
     > Pour les déploiements de production, suivez l’aide fournie par la page [Planification de la capacité pour les rôles serveur Azure App Service dans Azure Stack Hub](azure-stack-app-service-capacity-planning.md).
     >
     >

    | Role | Nombre minimal d’instances | Nombre minimal de références (SKU) | Notes |
    | --- | --- | --- | --- |
    | Contrôleur | 1 | Standard_A2 - (2 processeurs virtuels, 3584 Mo) | Gère et maintient l’intégrité du cloud Azure App Service. |
    | Gestion | 1 | Standard_A2 - (2 processeurs virtuels, 3 584 Mo) | Gère les points de terminaison Azure App Service Azure Resource Manager et d’API, les extensions du portail (administrateur, locataire, portail Functions) et du service des données. Pour prendre en charge le basculement, définissez le nombre d’instances recommandées sur 2. |
    | Serveur de publication | 1 | Standard_A1 - (1 processeur virtuel, 1 792 Mo) | Publie du contenu via FTP et un déploiement web. |
    | FrontEnd | 1 | Standard_A1 - (1 processeur virtuel, 1 792 Mo) | Route les requêtes vers les applications Azure App Service. |
    | Worker partagé | 1 | Standard_A1 - (1 processeur virtuel, 1 792 Mo) | Héberge les applications web ou d’API et les applications Azure Functions. Il peut être nécessaire d’ajouter plus d’instances. En tant qu’opérateur, vous pouvez définir votre offre et choisir n’importe quel niveau de référence. Les niveaux doivent avoir au minimum un processeur virtuel. |

    ![Définir des niveaux de rôle et des options de référence SKU dans le programme d’installation Azure App Service][14]

    > [!NOTE]
    > Windows Server 2016 Core *n’est pas* une image de plateforme prise en charge pour une utilisation avec Azure App Service sur Azure Stack Hub.  N’utilisez pas d’images d’évaluation pour les déploiements en production. Azure App Service sur Azure Stack Hub nécessite l’activation de Microsoft .NET 3.5.1 SP1 sur l’image utilisée pour le déploiement. Cette fonctionnalité n’est pas activée sur les images Windows Server 2016 syndiquées sur la Place de marché. Par conséquent, vous devez créer et utiliser une image Windows Server 2016 avec cette fonctionnalité pré-activée.

1. Dans la zone **Sélectionner l’image de plateforme**, choisissez votre image de machine virtuelle Windows Server 2016 de déploiement parmi les images disponibles dans le fournisseur de ressources de calcul pour le cloud Azure App Service. Sélectionnez **Suivant**.

1. Sur la page suivante :
     1. Entrez le nom d’utilisateur et le mot de passe de l’administrateur de la machine virtuelle ayant le rôle de worker.
     2. Entrez le nom d’utilisateur et le mot de passe de l’administrateur de machines virtuelles Autres rôles.
     3. Sélectionnez **Suivant**.

    ![Entrer le rôle Administrateurs de machines virtuelles dans le programme d’installation Azure App Service][16]

1. Sur la page de résumé :
    1. Vérifiez les choix effectués. Pour apporter des modifications, utilisez les boutons **Précédent** pour visiter les pages précédentes.
    2. Si les configurations sont correctes, cochez la case.
    3. Pour démarrer le déploiement, sélectionnez **Suivant**.

    ![Résumé des sélections effectuées dans le programme d’installation Azure App Service][17]

1. Sur la page suivante :
    1. Suivez la progression de l’installation. Le déploiement d’App Service sur Azure Stack Hub peut prendre jusqu’à 240 minutes en fonction des sélections par défaut et de l’ancienneté de l’image de Windows 2016 Datacenter de base.

    2. Une fois l’exécution du programme d’installation terminée, sélectionnez **Quitter**.

    ![Suivre le processus d’installation dans le programme d’installation Azure App Service][18]

## <a name="post-deployment-steps"></a>Étapes de post-déploiement

> [!IMPORTANT]
> Si vous avez spécifié le fournisseur de ressources Azure App Service avec une instance SQL Always On, vous *devez* [ajouter les bases de données appservice_hosting et appservice_metering à un groupe de disponibilité](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database). Vous devez aussi synchroniser les bases de données pour éviter toute perte de service en cas de basculement d’une base de données.

Si vous avez choisi de procéder au déploiement dans un réseau virtuel existant et une adresse IP interne pour vous connecter à votre serveur de fichiers, vous devez ajouter une règle de sécurité sortante, qui autorise le trafic SMB entre le sous-réseau Worker et le serveur de fichiers. Dans le portail administrateur, accédez au groupe de sécurité réseau WorkersNsg, puis ajoutez une règle de sécurité sortante ayant les propriétés suivantes :

- Source : Quelconque
- Plage de ports source : : *
- Destination : Adresses IP
- Plage d’adresses IP de destination : plage d’adresses IP de votre serveur de fichiers
- Plage de ports de destination : 445
- Protocole : TCP
- Action : Allow
- Priorité : 700
- Nom : Outbound_Allow_SMB445

## <a name="validate-the-azure-app-service-on-azure-stack-hub-installation"></a>Valider l’installation Azure App Service sur Azure Stack Hub

1. Dans le portail administrateur Azure Stack Hub, allez dans **Administration - App Service**.

1. Dans la vue d’ensemble, sous les statuts, vérifiez que le **Statut** affiche **Tous les rôles sont prêts**.

    ![Vue d’ensemble de l’administration d’Azure App Service](media/azure-stack-app-service-deploy/image12.png)

## <a name="test-drive-azure-app-service-on-azure-stack-hub"></a>Tester Azure App Service sur Azure Stack Hub

Après avoir déployé et inscrit le fournisseur de ressources Azure App Service, testez-le pour vous assurer que les utilisateurs peuvent déployer des applications web et d’API.

> [!NOTE]
> Vous devez créer une offre avec l’espace de noms Microsoft.Web dans le plan. Ensuite, vous devez avoir un abonnement de locataire qui s’abonne à cette offre. Pour plus d’informations, consultez [Créer une offre](azure-stack-create-offer.md) et [Créer un plan](azure-stack-create-plan.md).
>
> Vous *devez* avoir un abonnement locataire pour créer des applications qui utilisent Azure App Service sur Azure Stack Hub. Les seules fonctionnalités qu’un administrateur de service peut effectuer dans le portail administrateur sont liées à l’administration du fournisseur de ressources Azure App Service. Ces fonctionnalités incluent l’ajout de capacité, la configuration de sources de déploiement, l’ajout de niveaux Worker et de références.
>
> À compter de la troisième préversion technique, vous devez utiliser le portail du locataire et disposer d’un abonnement utilisateur pour créer des applications web, d’API et Azure Functions.

1. Sur le portail utilisateur Azure Stack Hub, sélectionnez **Créer une ressource** > **Web + Mobile** > **Application web**.

1. Dans le panneau **Application web**, tapez un nom dans la zone **Application web**.

1. Sous **Groupe de ressources**, sélectionnez **Nouveau**. Tapez un nom dans la zone **Groupe de ressources**.

1. Sélectionnez **Plan App Service/Emplacement** > **Créer**.

1. Dans le panneau **Plan App Service**, tapez un nom dans la zone **Plan App Service**.

1. Sélectionnez **Niveau tarifaire** > **Libre-Partagé** ou **Partagé-Partagé** > **Sélectionnez** > **OK** > **Créer**.

1. En moins d’une minute, une vignette pour la nouvelle application web apparaît dans le tableau de bord. Sélectionnez la vignette.

1. Dans le panneau **Application web**, cliquez sur **Parcourir** pour voir le site web par défaut pour cette application.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Déployer un site web WordPress, DNN ou Django (facultatif)

1. Dans le portail utilisateur Azure Stack Hub, sélectionnez **+** , accédez à la Place de marché Azure, déployez un site web Django et attendez que l’opération se termine. La plateforme web Django utilise une base de données basée sur système de fichiers. Elle ne nécessite aucun fournisseur de ressources supplémentaire, comme SQL ou MySQL.

1. Si vous avez également déployé un fournisseur de ressources MySQL, vous pouvez déployer un site web WordPress à partir de la Place de marché Azure. Lorsque vous êtes invité à entrer les paramètres de la base de données, entrez le nom d’utilisateur *User1\@Server1*, avec le nom d’utilisateur et le nom de serveur de votre choix.

1. Si vous avez également déployé un fournisseur de ressources SQL Server, vous pouvez déployer un site web DNN à partir de la Place de marché Azure. Quand vous êtes invité à entrer les paramètres de la base de données, sélectionnez une base de données sur l’ordinateur exécutant SQL Server qui est connectée à votre fournisseur de ressources.

## <a name="next-steps"></a>Étapes suivantes

Anticipez les opérations d'administration supplémentaires pour Azure App Service sur Azure Stack Hub :

- [Planification de la capacité](azure-stack-app-service-capacity-planning.md)
- [Configurer des sources de déploiement](azure-stack-app-service-configure-deployment-sources.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-advanced-create-package.png
[2]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-advanced-complete-offline.png
[3]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-arm-endpoints.png
[4]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-subscription-information.png
[5]: ./media/azure-stack-app-service-deploy-offline/app-service-default-VNET-config.png
[6]: ./media/azure-stack-app-service-deploy-offline/app-service-custom-VNET-config.png
[7]: ./media/azure-stack-app-service-deploy-offline/app-service-custom-VNET-config-with-values.png
[8]: ./media/azure-stack-app-service-deploy-offline/app-service-fileshare-configuration.png
[9]: ./media/azure-stack-app-service-deploy-offline/app-service-fileshare-configuration-error.png
[10]: ./media/azure-stack-app-service-deploy-offline/app-service-identity-app.png
[11]: ./media/azure-stack-app-service-deploy-offline/app-service-certificates.png
[12]: ./media/azure-stack-app-service-deploy-offline/app-service-sql-configuration.png
[13]: ./media/azure-stack-app-service-deploy-offline/app-service-sql-configuration-error.png
[14]: ./media/azure-stack-app-service-deploy-offline/app-service-cloud-quantities.png
[15]: ./media/azure-stack-app-service-deploy-offline/app-service-windows-image-selection.png
[16]: ./media/azure-stack-app-service-deploy-offline/app-service-role-credentials.png
[17]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-deployment-summary.png
[18]: ./media/azure-stack-app-service-deploy-offline/app-service-deployment-progress.png
