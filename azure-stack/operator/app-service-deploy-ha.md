---
title: Déployer App Service sur Azure Stack dans une configuration hautement disponible | Microsoft Docs
description: Découvrez comment déployer App Service dans Azure Stack à l’aide d’une configuration hautement disponible.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2019
ms.author: mabrigg
ms.reviewer: anwestg
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: 78476afd09d6c85fbe5790568dd46366beaf1991
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618360"
---
# <a name="deploy-app-service-in-a-highly-available-configuration"></a>Déployer App Service dans une configuration hautement disponible

Cet article explique comment utiliser des éléments de la Place de marché Azure Stack afin de déployer App Service pour Azure Stack dans une configuration hautement disponible. En plus des éléments de la Place de marché disponibles, cette solution utilise également le modèle de démarrage rapide Azure Stack [appservice-fileshare-sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha). Ce modèle automatise la création d’une infrastructure hautement disponible pour héberger le fournisseur de ressources App Service. App Service est ensuite installé sur cette infrastructure de machines virtuelles hautement disponible. 

## <a name="deploy-the-highly-available-app-service-infrastructure-vms"></a>Déployer les machines virtuelles de l’infrastructure App Service hautement disponible
Le modèle de démarrage rapide Azure Stack [appservice-fileshare-sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) simplifie le déploiement d’App Service dans une configuration hautement disponible. Il doit être déployé dans l’abonnement du fournisseur par défaut. 

Quand il est utilisé pour créer une ressource personnalisée dans Azure Stack, le modèle crée :
- Un réseau virtuel et des sous-réseaux requis.
- Des groupes de sécurité réseau pour le serveur de fichiers, SQL Server et les sous-réseaux AD DS (Active Directory Domain Services).
- Des comptes de stockage pour les disques de machine virtuelle et le témoin de cloud de cluster.
- Un équilibreur de charge interne pour les machines virtuelles SQL avec une adresse IP privée liée à l’écouteur SQL AlwaysOn.
- Trois groupes à haute disponibilité pour les services AD DS, le cluster de serveur de fichiers et le cluster SQL.
- Un cluster SQL à deux nœuds.
- Un cluster de serveur de fichiers à deux nœuds.
- Deux contrôleurs de domaine.

### <a name="required-azure-stack-marketplace-items"></a>Éléments de la Place de marché Azure Stack requis
Avant d’utiliser ce modèle, vérifiez que les [éléments de la Place de marché Azure Stack](azure-stack-marketplace-azure-items.md) suivants sont disponibles dans votre instance Azure Stack :

- Image Windows Server 2016 Datacenter Core (pour les machines virtuelles de serveur de fichiers et AD DS)
- SQL Server 2016 SP2 sur Windows Server 2016 (Enterprise)
- Extension IaaS SQL la plus récente 
- Extension de configuration d’état souhaité PowerShell la plus récente 

> [!TIP]
> Consultez [le fichier lisezmoi sur le modèle](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) sur GitHub pour plus d’informations sur les exigences relatives au modèle et les valeurs par défaut. 

### <a name="deploy-the-app-service-infrastructure"></a>Déployer l’infrastructure App Service
Utilisez les étapes de cette section pour créer un déploiement personnalisé à l’aide du modèle de démarrage rapide Azure Stack **appservice-fileshare-sqlserver-ha**.

1. [!INCLUDE [azs-admin-portal](../includes/azs-admin-portal.md)]

2. Sélectionnez **\+** **Créer une ressource** > **Personnalisé**, puis **Déploiement de modèle**.

   ![Déploiement de modèle personnalisé](media/app-service-deploy-ha/1.png)


3. Dans le panneau **Déploiement personnalisé**, sélectionnez **Modifier le modèle** > **Modèle de démarrage rapide**, puis utilisez la liste déroulante des modèles personnalisés disponibles pour sélectionner le modèle **appservice-fileshare-sqlserver-ha**, cliquez sur **OK**, puis sur **Enregistrer**.

   ![Sélectionner le modèle de démarrage rapide appservice-fileshare-sqlserver-ha](media/app-service-deploy-ha/2.png)

4. Dans le panneau **Déploiement personnalisé**, sélectionnez **Modifier les paramètres** et faites défiler l’écran vers le bas pour examiner les valeurs du modèle par défaut. Modifiez ces valeurs de façon appropriée pour fournir toutes les informations des paramètres nécessaires, puis cliquez sur **OK**.<br><br> Au minimum, indiquez des mots de passe complexes pour les paramètres ADMINPASSWORD, FILESHAREOWNERPASSWORD, FILESHAREUSERPASSWORD, SQLSERVERSERVICEACCOUNTPASSWORD et SQLLOGINPASSWORD.
    
   ![Modifier les paramètres du déploiement personnalisé](media/app-service-deploy-ha/3.png)

5. Dans le panneau **Déploiement personnalisé**, vérifiez que l’option **Abonnement Fournisseur par défaut** est sélectionnée en tant qu’abonnement à utiliser, puis créez un groupe de ressources ou sélectionnez un groupe de ressources existant pour le déploiement personnalisé.<br><br> Ensuite, sélectionnez l’emplacement du groupe de ressources (**local** pour les installations ASDK), puis cliquez sur **Créer**. Les paramètres du déploiement personnalisé sont validés avant le début du déploiement du modèle.

    ![Créer un déploiement personnalisé](media/app-service-deploy-ha/4.png)

6. Dans le portail d’administration, sélectionnez **Groupes de ressources**, puis le nom du groupe de ressources que vous avez créé pour le déploiement personnalisé (**app-service-ha** dans cet exemple). Regardez l’état du déploiement pour vérifier que tous les déploiements ont réussi.

   > [!NOTE]
   > Le déploiement du modèle dure environ une heure.

   [![](media/app-service-deploy-ha/5-sm.png "Examiner l’état du déploiement du modèle")](media/app-service-deploy-ha/5-lg.png#lightbox)


### <a name="record-template-outputs"></a>Enregistrer les sorties de modèle
Une fois le déploiement du modèle terminé, enregistrez les sorties de déploiement du modèle. Vous devrez fournir ces informations lors de l’exécution du programme d’installation App Service. 

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

4. Après avoir sélectionné le déploiement **Microsoft.Template**, sélectionnez **Sorties** et enregistrez la sortie de paramètre de modèle. Ces informations seront requises lors du déploiement d’App Service.

    ![Sortie de paramètre](media/app-service-deploy-ha/7.png)


## <a name="deploy-app-service-in-a-highly-available-configuration"></a>Déployer App Service dans une configuration hautement disponible
Suivez les étapes décrites dans cette section pour déployer App Service pour Azure Stack dans une configuration hautement disponible basée sur le modèle de démarrage rapide Azure Stack [appservice-fileshare-sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha). 

Après avoir installé le fournisseur de ressources App Service, vous pouvez l’inclure dans vos offres et vos plans. Les utilisateurs peuvent ensuite s’abonner pour obtenir le service et commencer à créer des applications.

> [!IMPORTANT]
> Avant d’exécuter le programme d’installation du fournisseur de ressources, vérifiez que vous avez lu les notes de publication qui accompagnent chaque version d’App Service afin d’en savoir plus sur les nouvelles fonctionnalités, les correctifs et les problèmes connus qui pourraient affecter votre déploiement.

### <a name="prerequisites"></a>Prérequis
Avant de pouvoir exécuter le programme d’installation App Service, plusieurs étapes sont nécessaires comme décrit dans l’article [Avant de commencer avec App Service sur Azure Stack](azure-stack-app-service-before-you-get-started.md) :

> [!TIP]
> Les étapes décrites dans l’article Avant de commencer ne sont pas toutes nécessaires, car le déploiement du modèle configure les machines virtuelles de l’infrastructure pour vous. 

- [Téléchargez le programme d’installation App Service et les scripts d’assistance](azure-stack-app-service-before-you-get-started.md#download-the-installer-and-helper-scripts).
- [Téléchargez la dernière extension de script personnalisé sur la Place de marché Azure Stack](azure-stack-app-service-before-you-get-started.md#syndicate-the-custom-script-extension-from-the-marketplace).
- [Générez les certificats requis](azure-stack-app-service-before-you-get-started.md#get-certificates).
- Créez l’ID Application selon le fournisseur d’identité que vous avez choisi pour Azure Stack. Un ID Application peut être généré pour [Azure AD](azure-stack-app-service-before-you-get-started.md#create-an-azure-active-directory-application) ou les [services de fédération Active Directory (AD FS)](azure-stack-app-service-before-you-get-started.md#create-an-active-directory-federation-services-application), et enregistrer l’ID d’application.
- Vérifiez que vous avez ajouté l’image Windows Server 2016 Datacenter à la Place de marché Azure Stack. Elle est nécessaire pour l’installation d’App Service.

### <a name="deploy-app-service-in-highly-available-configuration"></a>Déployer App Service dans une configuration hautement disponible
L’installation du fournisseur de ressources App Service prend au moins une heure. Le temps nécessaire dépend du nombre d’instances de rôle que vous déployez. Lors du déploiement, le programme d’installation exécute les tâches suivantes :

- Crée un conteneur d’objets blob dans le compte de stockage Azure Stack spécifié.
- Crée une zone DNS et les entrées pour App Service.
- Inscrit le fournisseur de ressources App Service.
- Inscrit les éléments de la galerie App Service.

Pour déployer le fournisseur de ressources App Service, procédez comme suit :

1. Exécutez le programme d’installation App Service précédemment téléchargé (**appservice.exe**) en tant qu’administrateur sur un ordinateur pouvant atteindre le point de terminaison de gestion des ressources Azure de l’administrateur Azure Stack.

2. Sélectionnez **Déployer App Service ou effectuer une mise à niveau vers la dernière version**.

    ![Déployer ou mettre à niveau](media/app-service-deploy-ha/01.png)

3. Acceptez les termes du contrat de licence Microsoft et cliquez sur **Suivant**.

    ![Termes du contrat de licence Microsoft](media/app-service-deploy-ha/02.png)

4. Acceptez les termes du contrat de licence non-Microsoft et cliquez sur **Suivant**.

    ![Termes du contrat de licence non-Microsoft](media/app-service-deploy-ha/03.png)

5. Indiquez la configuration du point de terminaison cloud App Service pour votre environnement Azure Stack.

    ![Configuration du point de terminaison cloud App Service](media/app-service-deploy-ha/04.png)

6. **Connectez-vous** à l’abonnement Azure Stack à utiliser pour l’installation et choisissez l’emplacement. 

    ![Se connecter à l’abonnement Azure Stack](media/app-service-deploy-ha/05.png)

7. Sélectionnez **Utiliser le réseau virtuel et les sous-réseaux existants** et **Nom du groupe de ressources** pour le groupe de ressources utilisé pour déployer le modèle hautement disponible.<br><br>Ensuite, sélectionnez le réseau virtuel créé dans le cadre du déploiement du modèle, puis les sous-réseaux de rôle appropriés parmi les options de liste déroulante. 

    ![Sélection du réseau virtuel](media/app-service-deploy-ha/06.png)

8. Indiquez les informations sur les sorties de modèle précédemment enregistrées pour le chemin du partage de fichiers et les paramètres du propriétaire du partage de fichiers. Une fois que vous avez terminé, cliquez sur **Suivant**.

    ![Informations sur les sorties pour le partage de fichiers](media/app-service-deploy-ha/07.png)

9. Étant donné que la machine utilisée pour installer App Service ne se trouve pas sur le même réseau virtuel que le serveur de fichiers utilisé pour héberger le partage de fichiers App Service, vous ne serez pas en mesure de résoudre le nom. **Ce comportement est normal**.<br><br>Vérifiez que les informations entrées pour le chemin UNC du partage de fichiers et les informations relatives aux comptes sont correctes, puis appuyez sur **Oui** dans la boîte de dialogue d’alerte pour continuer l’installation d’App Service.

    ![Boîte de dialogue d’erreur attendue](media/app-service-deploy-ha/08.png)

    Si vous avez choisi de procéder au déploiement dans un réseau virtuel existant et une adresse IP interne pour vous connecter à votre serveur de fichiers, vous devez ajouter une règle de sécurité sortante, qui autorise le trafic SMB entre le sous-réseau Worker et le serveur de fichiers. Accédez au WorkersNsg dans le portail d’administration, puis ajoutez une règle de sécurité sortante comportant les propriétés suivantes :
    - Source : Quelconque
    - Plage de ports source : : *
    - Destination : Adresses IP
    - Plage d’adresses IP de destination : plage d’adresses IP de votre serveur de fichiers
    - Plage de ports de destination : 445
    - Protocole : TCP
    - Action : AUTORISER
    - Priorité : 700
    - Nom : Outbound_Allow_SMB445

10. Fournissez l’ID d’application d’identité, le chemin et les mots de passe pour les certificats d’identité, puis cliquez sur **Suivant** :
    - Certificat d’application d’identité (au format **sso.appservice.local.azurestack.external.pfx**)
    - Certificat racine Azure Resource Manager (**AzureStackCertificationAuthority.cer**)

    ![Certificat d’application d’identité et certificat racine](media/app-service-deploy-ha/008.png)

10. Ensuite, fournissez les informations requises restantes pour les certificats suivants et cliquez sur **Suivant** :
    - Certificat SSL Azure Stack par défaut (au format **_.appservice.local.azurestack.external.pfx**)
    - Certificat SSL de l’API (au format **api.appservice.local.azurestack.external.pfx**)
    - Certificat de l’éditeur (au format **ftp.appservice.local.azurestack.external.pfx**) 

    ![Certificats de configuration supplémentaires](media/app-service-deploy-ha/09.png)

11. Fournissez les informations de connexion SQL Server en utilisant les informations de connexion SQL Server des sorties de déploiement du modèle de haute disponibilité :

    ![Informations de connexion SQL Server](media/app-service-deploy-ha/10.png)

12. Étant donné que la machine utilisée pour installer App Service ne se trouve pas sur le même réseau virtuel que le serveur SQL Server utilisé pour héberger les bases de données App Service, vous ne serez pas en mesure de résoudre le nom.  **Ce comportement est normal**.<br><br>Vérifiez que les informations entrées pour le nom SQL Server et les informations relatives aux comptes sont correctes, puis appuyez sur **Oui** pour continuer l’installation d’App Service. Cliquez sur **Suivant**.

    ![Informations de connexion SQL Server](media/app-service-deploy-ha/11.png)

13. Acceptez les valeurs de configuration de rôle par défaut ou remplacez-les par les valeurs recommandées et cliquez sur **Suivant**.<br><br>Nous recommandons de changer les valeurs par défaut pour les instances de rôle d’infrastructure App Service comme suit pour obtenir des configurations hautement disponibles :

    |Rôle|Default|Configuration hautement disponible|
    |-----|-----|-----|
    |Rôle de contrôleur|2|2|
    |Rôle de gestion|1|3|
    |Rôle d’éditeur|1|3|
    |Rôle FrontEnd|1|3|
    |Rôle de worker partagé|1|10|
    |     |     |     |

    ![Valeurs d’instances de rôle d’infrastructure](media/app-service-deploy-ha/12.png)

    > [!NOTE]
    > Le remplacement des valeurs par défaut par celles recommandées dans ce tutoriel augmente la configuration matérielle requise pour l’installation d’App Service. Un total de 26 cœurs et 46 592 Mo de RAM sont nécessaires pour prendre en charge les 21 machines virtuelles recommandées au lieu des 18 cœurs par défaut et 32 256 Mo de RAM pour 15 machines virtuelles.

14. Sélectionnez l’image de plateforme à utiliser pour l’installation des machines virtuelles de l’infrastructure App Service et cliquez sur **Suivant** :

    ![Sélection de l’image de plateforme](media/app-service-deploy-ha/13.png)

15. Indiquez les informations d’identification de rôle d’infrastructure App Service à utiliser, puis cliquez sur **Suivant** :

    ![Informations d’identification de rôle d’infrastructure](media/app-service-deploy-ha/14.png)

16. Passez en revue les informations à utiliser pour déployer App Service et cliquez sur **Suivant** pour commencer le déploiement. 

    ![Passer en revue le résumé de l’installation](media/app-service-deploy-ha/15.png)

17. Examinez la progression du déploiement App Service. Cette opération peut prendre plus d’une heure en fonction de votre configuration de déploiement spécifique et du matériel. Une fois le programme d’installation terminé avec succès, sélectionnez **Quitter**.

    ![Installation terminée](media/app-service-deploy-ha/16.png)


## <a name="next-steps"></a>Étapes suivantes

[Effectuer un scale-out d’App Service](azure-stack-app-service-add-worker-roles.md). Vous devrez peut-être ajouter d’autres workers de rôle d’infrastructure App Service pour répondre à la demande d’applications attendue dans votre environnement. Par défaut, App Service sur Azure Stack prend en charge les niveaux Worker gratuits et partagés. Pour ajouter d’autres niveaux Worker, vous devez ajouter davantage de rôles de travail.

[Configurer des sources de déploiement](azure-stack-app-service-configure-deployment-sources.md). Une configuration supplémentaire est requise pour prendre en charge le déploiement à la demande à partir de plusieurs fournisseurs de contrôle de code source comme GitHub, BitBucket, OneDrive et DropBox.

[Sauvegarder App Service](app-service-back-up.md). Après avoir déployé et configuré App Service, vous devez vous assurer que tous les composants nécessaires pour la récupération d’urgence sont sauvegardés, afin d’éviter la perte de données et des temps d’arrêt de service inutiles lors des opérations de récupération.