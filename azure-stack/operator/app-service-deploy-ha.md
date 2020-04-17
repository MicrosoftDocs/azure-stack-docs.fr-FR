---
title: Déployer App Service sur Azure Stack Hub dans une configuration à haute disponibilité
description: Apprenez à déployer App Service sur Azure Stack Hub à l'aide d'une configuration hautement disponible.
author: BryanLa
ms.topic: article
ms.date: 01/02/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 01/02/2019
ms.openlocfilehash: ec4f3dc2a17e362038d11ec988d19ffa9edd6a6e
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "77701851"
---
# <a name="deploy-app-service-in-a-highly-available-configuration"></a>Déployer App Service dans une configuration hautement disponible

Cet article explique comment utiliser des éléments de la Place de marché Azure Stack Hub afin de déployer App Service pour Azure Stack Hub dans une configuration hautement disponible. En plus des éléments de la Place de marché disponibles, cette solution utilise également le modèle de démarrage rapide Azure Stack Hub [appservice-fileshare-sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha). Ce modèle automatise la création d’une infrastructure hautement disponible pour héberger le fournisseur de ressources App Service. App Service est ensuite installé sur cette infrastructure de machines virtuelles hautement disponible. 

## <a name="deploy-the-highly-available-app-service-infrastructure-vms"></a>Déployer les machines virtuelles de l’infrastructure App Service hautement disponible
Le modèle de démarrage rapide Azure Stack Hub [appservice-fileshare-sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) simplifie le déploiement d'App Service dans une configuration hautement disponible. Il doit être déployé dans l’abonnement du fournisseur par défaut. 

Lorsqu'il est utilisé pour créer une ressource personnalisée dans Azure Stack Hub, le modèle crée ce qui suit :
- Un réseau virtuel et des sous-réseaux requis.
- Des groupes de sécurité réseau pour le serveur de fichiers, SQL Server et les sous-réseaux AD DS (Active Directory Domain Services).
- Des comptes de stockage pour les disques de machine virtuelle et le témoin de cloud de cluster.
- Un équilibreur de charge interne pour les machines virtuelles SQL avec une adresse IP privée liée à l’écouteur SQL AlwaysOn.
- Trois groupes à haute disponibilité pour les services AD DS, le cluster de serveur de fichiers et le cluster SQL.
- Un cluster SQL à deux nœuds.
- Un cluster de serveur de fichiers à deux nœuds.
- Deux contrôleurs de domaine.

### <a name="required-azure-stack-hub-marketplace-items"></a>Éléments de la Place de marché Azure Stack Hub requis
Avant d'utiliser ce modèle, vérifiez que les [éléments de la Place de marché Azure Stack Hub](azure-stack-marketplace-azure-items.md) suivants sont disponibles dans votre instance d'Azure Stack Hub :

- Image Windows Server 2016 Datacenter Core (pour les machines virtuelles de serveur de fichiers et AD DS)
- SQL Server 2016 SP2 sur Windows Server 2016 (Enterprise)
- Extension IaaS SQL la plus récente 
- Extension de configuration d’état souhaité PowerShell la plus récente 

> [!TIP]
> Consultez [le fichier lisezmoi sur le modèle](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) sur GitHub pour plus d’informations sur les exigences relatives au modèle et les valeurs par défaut. 

### <a name="deploy-the-app-service-infrastructure"></a>Déployer l’infrastructure App Service
Utilisez les étapes de cette section pour créer un déploiement personnalisé à l'aide du modèle de démarrage rapide Azure Stack Hub **appservice-fileshare-sqlserver-ha**.

1. [!INCLUDE [azs-admin-portal](../includes/azs-admin-portal.md)]

2. Sélectionnez **\+** **Créer une ressource** > **Personnalisé**, puis **Déploiement de modèle**.

   ![Déploiement de modèle personnalisé](media/app-service-deploy-ha/1.png)


3. Dans le panneau **Déploiement personnalisé**, sélectionnez **Modifier le modèle** > **Modèle de démarrage rapide**, puis utilisez la liste déroulante des modèles personnalisés disponibles pour sélectionner le modèle **appservice-fileshare-sqlserver-ha**. Cliquez sur **OK**, puis sur **Enregistrer**.

   ![Sélectionner le modèle de démarrage rapide appservice-fileshare-sqlserver-ha](media/app-service-deploy-ha/2.png)

4. Dans le panneau **Déploiement personnalisé**, sélectionnez **Modifier les paramètres** et faites défiler l’écran vers le bas pour examiner les valeurs du modèle par défaut. Modifiez ces valeurs de façon appropriée pour fournir toutes les informations des paramètres nécessaires, puis cliquez sur **OK**.<br><br> Au minimum, fournissez des mots de passe complexes aux paramètres `ADMINPASSWORD`, `FILESHAREOWNERPASSWORD`, `FILESHAREUSERPASSWORD`, `SQLSERVERSERVICEACCOUNTPASSWORD` et `SQLLOGINPASSWORD`.
    
   ![Modifier les paramètres du déploiement personnalisé](media/app-service-deploy-ha/3.png)

5. Dans le panneau **Déploiement personnalisé**, vérifiez que l’option **Abonnement Fournisseur par défaut** est sélectionnée en tant qu’abonnement à utiliser, puis créez un groupe de ressources ou sélectionnez un groupe de ressources existant pour le déploiement personnalisé.<br><br> Ensuite, sélectionnez l’emplacement du groupe de ressources (**local** pour les installations ASDK), puis cliquez sur **Créer**. Les paramètres du déploiement personnalisé sont validés avant le début du déploiement du modèle.

    ![Créer un déploiement personnalisé](media/app-service-deploy-ha/4.png)

6. Dans le portail d’administration, sélectionnez **Groupes de ressources**, puis le nom du groupe de ressources que vous avez créé pour le déploiement personnalisé (**app-service-ha** dans cet exemple). Regardez l’état du déploiement pour vérifier que tous les déploiements ont réussi.

   > [!NOTE]
   > Le déploiement du modèle dure environ une heure.

   [![](media/app-service-deploy-ha/5-sm.png "Review template deployment status")](media/app-service-deploy-ha/5-lg.png#lightbox)


### <a name="record-template-outputs"></a>Enregistrer les sorties de modèle
Une fois le déploiement du modèle terminé, enregistrez les sorties de déploiement du modèle. Vous avez besoin de ces informations lors de l’exécution du programme d’installation d’App Service.

Vérifiez que vous enregistrez chacune de ces valeurs de sortie :
- FileSharePath
- FileShareOwner
- FileShareUser
- SQLserver
- SQLuser

Suivez ces étapes pour découvrir les valeurs de sortie de modèle :

1. [!INCLUDE [azs-admin-portal](../includes/azs-admin-portal.md)]

2. Dans le portail d’administration, sélectionnez **Groupes de ressources**, puis le nom du groupe de ressources que vous avez créé pour le déploiement personnalisé (**app-service-ha** dans cet exemple). 

3. Cliquez sur **Déploiements**, puis sélectionnez **Microsoft.Template**.

    ![Déploiement de Microsoft.Template](media/app-service-deploy-ha/6.png)

4. Après avoir sélectionné le déploiement **Microsoft.Template**, sélectionnez **Sorties** et enregistrez la sortie de paramètre de modèle. Ces informations sont requises lors du déploiement d’App Service.

    ![Sortie de paramètre](media/app-service-deploy-ha/7.png)


## <a name="deploy-app-service-in-a-highly-available-configuration"></a>Déployer App Service dans une configuration hautement disponible
Suivez les étapes décrites dans cette section pour déployer App Service pour Azure Stack Hub dans une configuration hautement disponible basée sur le modèle de démarrage rapide Azure Stack Hub [appservice-fileshare-sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha). 

Après avoir installé le fournisseur de ressources App Service, vous pouvez l’inclure dans vos offres et vos plans. Les utilisateurs peuvent ensuite s’abonner pour obtenir le service et commencer à créer des applications.

> [!IMPORTANT]
> Avant d’exécuter le programme d’installation du fournisseur de ressources, vérifiez que vous avez lu les notes de publication qui accompagnent chaque version d’App Service afin d’en savoir plus sur les nouvelles fonctionnalités, les correctifs et les problèmes connus qui pourraient affecter votre déploiement.

### <a name="prerequisites"></a>Conditions préalables requises
Avant de pouvoir exécuter le programme d'installation d'App Service, plusieurs étapes sont nécessaires, comme décrit dans l'article [Avant de commencer à utiliser App Service sur Azure Stack Hub](azure-stack-app-service-before-you-get-started.md) :

> [!TIP]
> Les étapes décrites dans l’article [Avant de commencer à utiliser App Service](azure-stack-app-service-before-you-get-started.md) ne sont pas toutes nécessaires, car le déploiement du modèle configure les machines virtuelles de l’infrastructure pour vous.

- [Téléchargez le programme d’installation App Service et les scripts d’assistance](azure-stack-app-service-before-you-get-started.md#download-the-installer-and-helper-scripts).
- [Téléchargez des éléments à partir de la Place de marché Azure Stack Hub](azure-stack-app-service-before-you-get-started.md#download-items-from-the-azure-marketplace).
- [Générez les certificats requis](azure-stack-app-service-before-you-get-started.md#get-certificates).
- Créez l'ID Application selon le fournisseur d'identité que vous avez choisi pour Azure Stack Hub. Un ID Application peut être généré pour [Azure AD](azure-stack-app-service-before-you-get-started.md#create-an-azure-active-directory-app) ou les [services de fédération Active Directory (AD FS)](azure-stack-app-service-before-you-get-started.md#create-an-active-directory-federation-services-app), et enregistrer l’ID d’application.
- Vérifiez que vous avez ajouté l'image Windows Server 2016 Datacenter à la Place de marché Azure Stack Hub. Cette image est nécessaire pour l’installation d’App Service.

### <a name="steps-for-app-service-deployment"></a>Étapes de déploiement d’App Service
L’installation du fournisseur de ressources App Service prend au moins une heure. Le temps nécessaire dépend du nombre d’instances de rôle que vous déployez. Lors du déploiement, le programme d’installation exécute les tâches suivantes :

- Crée un conteneur d'objets blob dans le compte de stockage Azure Stack Hub spécifié.
- Crée une zone DNS et les entrées pour App Service.
- Inscrit le fournisseur de ressources App Service.
- Inscrit les éléments de la galerie App Service.

Pour déployer le fournisseur de ressources App Service, procédez comme suit :

1. Exécutez le programme d'installation d'App Service précédemment téléchargé (**appservice.exe**) en tant qu'administrateur sur un ordinateur qui a accès au point de terminaison de gestion des ressources Azure de l'administrateur Azure Stack Hub.

2. Sélectionnez **Déployer App Service ou effectuer une mise à niveau vers la dernière version**.

    ![Déployer ou mettre à niveau App Service](media/app-service-deploy-ha/01.png)

3. Acceptez les termes du contrat de licence Microsoft et cliquez sur **Suivant**.

    ![Termes du contrat de licence Microsoft sur App Service](media/app-service-deploy-ha/02.png)

4. Acceptez les termes du contrat de licence non-Microsoft et cliquez sur **Suivant**.

    ![Termes du contrat de licence non Microsoft sur App Service](media/app-service-deploy-ha/03.png)

5. Indiquez la configuration du point de terminaison cloud App Service pour votre environnement Azure Stack Hub.

    ![Configuration du point de terminaison cloud App Service sur App Service](media/app-service-deploy-ha/04.png)

6. **Connectez-vous** à l'abonnement Azure Stack Hub à utiliser pour l'installation et choisissez l'emplacement. 

    ![Se connecter à l'abonnement Azure Stack Hub sur App Service](media/app-service-deploy-ha/05.png)

7. Sélectionnez **Utiliser le réseau virtuel et les sous-réseaux existants** et **Nom du groupe de ressources** pour le groupe de ressources utilisé pour déployer le modèle hautement disponible.<br><br>Ensuite, sélectionnez le réseau virtuel créé dans le cadre du déploiement du modèle, puis les sous-réseaux de rôle appropriés parmi les options de liste déroulante. 

    ![Sélection du réseau virtuel sur App Service](media/app-service-deploy-ha/06.png)

8. Indiquez les informations sur les sorties de modèle précédemment enregistrées pour le chemin du partage de fichiers et les paramètres du propriétaire du partage de fichiers. Une fois que vous avez terminé, cliquez sur **Suivant**.

    ![Informations sur les sorties pour le partage de fichiers sur App Service](media/app-service-deploy-ha/07.png)

9. Étant donné que la machine utilisée pour installer App Service ne se trouve pas sur le même réseau virtuel que le serveur de fichiers utilisé pour héberger le partage de fichiers App Service, vous ne pouvez pas résoudre le nom. **Cette erreur est normale**.<br><br>Vérifiez que les informations saisies pour le chemin d'accès UNC du partage de fichiers et les informations du compte sont correctes. Appuyez sur **Oui** sur la boîte de dialogue d’alerte pour poursuivre l’installation d’App Service.

    ![Boîte de dialogue d’erreur attendue sur App Service](media/app-service-deploy-ha/08.png)

    Si vous avez choisi d’effectuer un déploiement sur un réseau virtuel existant en utilisant une adresse IP interne pour vous connecter à votre serveur de fichiers, vous devez ajouter une règle de sécurité de trafic sortant. Cette règle active le trafic SMB entre le sous-réseau worker et le serveur de fichiers. Accédez au WorkersNsg dans le portail administrateur, puis ajoutez une règle de sécurité sortante comportant les propriétés suivantes :
    - Source : Toutes
    - Plage de ports source : : *
    - Destination : adresses IP
    - Plage d’adresses IP de destination : plage d’adresses IP de votre serveur de fichiers
    - Plage de ports de destination : 445
    - Protocole : TCP
    - Action : Autoriser
    - Priorité : 700
    - Nom : Outbound_Allow_SMB445

10. Fournissez l’ID d’application d’identité, le chemin et les mots de passe pour les certificats d’identité, puis cliquez sur **Suivant** :
    - Certificat d’application d’identité (au format **sso.appservice.local.azurestack.external.pfx**)
    - Certificat racine Azure Resource Manager (**AzureStackCertificationAuthority.cer**)

    ![Certificat d’application d’identité et certificat racine sur App Service](media/app-service-deploy-ha/008.png)

11. Ensuite, fournissez les informations requises restantes pour les certificats suivants et cliquez sur **Suivant** :
    - Certificat SSL Azure Stack Hub par défaut (au format **_.appservice.local.azurestack.external.pfx**)
    - Certificat SSL de l’API (au format **api.appservice.local.azurestack.external.pfx**)
    - Certificat de l’éditeur (au format **ftp.appservice.local.azurestack.external.pfx**) 

    ![Certificats de configuration supplémentaires sur App Service](media/app-service-deploy-ha/09.png)

12. Fournissez les informations de connexion SQL Server en utilisant les informations de connexion SQL Server des sorties de déploiement du modèle de haute disponibilité :

    ![Informations de connexion SQL Server sur App Service](media/app-service-deploy-ha/10.png)

13. Étant donné que la machine utilisée pour installer App Service ne se trouve pas sur le même réseau virtuel que le serveur SQL utilisé pour héberger les bases de données App Service, vous ne pouvez pas résoudre le nom.  **Ce comportement est normal**.<br><br>Vérifiez que les informations entrées pour le nom SQL Server et les informations relatives aux comptes sont correctes, puis appuyez sur **Oui** pour continuer l’installation d’App Service. Cliquez sur **Suivant**.

    ![Informations de connexion SQL Server sur App Service](media/app-service-deploy-ha/11.png)

14. Acceptez les valeurs de configuration de rôle par défaut ou remplacez-les par les valeurs recommandées et cliquez sur **Suivant**.<br><br>Nous recommandons de changer les valeurs par défaut pour les instances de rôle d’infrastructure App Service comme suit pour obtenir des configurations hautement disponibles :

    |Role|Default|Configuration hautement disponible|
    |-----|-----|-----|
    |Rôle de contrôleur|2|2|
    |Rôle de gestion|1|3|
    |Rôle d’éditeur|1|3|
    |Rôle FrontEnd|1|3|
    |Rôle de worker partagé|1|2|
    |     |     |     |

    ![Valeurs d’instances de rôle d’infrastructure sur App Service](media/app-service-deploy-ha/12.png)

    > [!NOTE]
    > Le remplacement des valeurs par défaut par celles recommandées dans ce tutoriel augmente la configuration matérielle requise pour l’installation d’App Service. Un total de 18 cœurs et 32 256 Mo de RAM sont nécessaires pour prendre en charge les 13 machines virtuelles recommandées au lieu des 9 cœurs par défaut et 16 128 Mo de RAM pour 6 machines virtuelles.

15. Sélectionnez l’image de plateforme à utiliser pour l’installation des machines virtuelles de l’infrastructure App Service et cliquez sur **Suivant** :

    ![Sélection de l’image de plateforme sur App Service](media/app-service-deploy-ha/13.png)

16. Indiquez les informations d’identification de rôle d’infrastructure App Service à utiliser, puis cliquez sur **Suivant** :

    ![Informations d'identification de rôle d’infrastructure sur App Service](media/app-service-deploy-ha/14.png)

17. Passez en revue les informations à utiliser pour déployer App Service et cliquez sur **Suivant** pour commencer le déploiement.

    ![Passer en revue le résumé de l’installation sur App Service](media/app-service-deploy-ha/15.png)

18. Examinez la progression du déploiement App Service. Ce déploiement peut prendre plus d’une heure en fonction de votre configuration de déploiement spécifique et du matériel. Une fois le programme d’installation terminé avec succès, sélectionnez **Quitter**.

    ![Installation terminée d’App Service](media/app-service-deploy-ha/16.png)

## <a name="next-steps"></a>Étapes suivantes

[Ajouter les bases de données appservice_hosting et appservice_metering à un groupe de disponibilité](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) si vous avez spécifié le fournisseur de ressources App Service avec une instance SQL Always On. Synchroniser les bases de données pour éviter toute perte de service en cas de basculement d’une base de données. Vous pouvez également exécuter un [script](https://blog.sqlauthority.com/2017/11/30/sql-server-alwayson-availability-groups-script-sync-logins-replicas/) pour importer les connexions AppServices à partir du serveur principal d’origine vers un serveur de basculement.

[Effectuer un scale-out d’App Service](azure-stack-app-service-add-worker-roles.md). Vous devrez peut-être ajouter d’autres workers de rôle d’infrastructure App Service pour répondre à la demande d’applications attendue dans votre environnement. Par défaut, App Service sur Azure Stack Hub prend en charge les niveaux Worker gratuits et partagés. Pour ajouter d’autres niveaux Worker, vous devez ajouter davantage de rôles de travail.

[Configurer des sources de déploiement](azure-stack-app-service-configure-deployment-sources.md). Une configuration supplémentaire est requise pour prendre en charge le déploiement à la demande à partir de plusieurs fournisseurs de contrôle de code source comme GitHub, BitBucket, OneDrive et DropBox.

[Sauvegarder App Service](app-service-back-up.md). Après avoir déployé et configuré App Service, vous devez vous assurer que tous les composants nécessaires pour la récupération d’urgence sont sauvegardés. La sauvegarde de vos composants nécessaires aide à éviter la perte de données et des temps d’arrêt de service inutiles lors des opérations de récupération.
