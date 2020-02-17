---
title: Créer des bases de données MySQL hautement disponibles
titleSuffix: Azure Stack Hub
description: Découvrez comment créer un ordinateur hôte fournisseur de ressources de serveur MySQL et des bases de données MySQL à haute disponibilité dans Azure Stack Hub.
author: BryanLa
ms.topic: article
ms.date: 10/07/2019
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 10/23/2018
ms.openlocfilehash: 0a15f4256349b9080f73d976f4e4a9782fd5b665
ms.sourcegitcommit: 0a3c8b0bf9c116a5caaeca453a2bbc6e7f7cbfb9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77147889"
---
# <a name="create-highly-available-mysql-databases"></a>Créer des bases de données MySQL hautement disponibles

En tant qu’opérateur Azure Stack Hub, vous pouvez configurer des machines virtuelles serveurs pour héberger des bases de données de serveur MySQL. Une fois qu’un cluster MySQL est créé et managé par Azure Stack Hub, les utilisateurs qui se sont abonnés aux services MySQL peuvent facilement créer des bases de données MySQL à haute disponibilité.

Cet article montre comment utiliser des éléments de Place de marché Azure Stack pour créer un [cluster MySQL with Replication](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster). Cette solution utilise plusieurs machines virtuelles pour répliquer les bases de données à partir du nœud principal sur un nombre configurable de réplicas. Une fois créé, le cluster peut être ajouté en tant que serveur d’hébergement MySQL Azure Stack Hub afin de permettre aux utilisateurs de créer des bases de données MySQL à haute disponibilité.

> [!IMPORTANT]
> L’élément **MySQL with Replication** de la Place de marché Azure Stack peut ne pas être disponible pour certains environnements d’abonnement cloud Azure. Vérifiez que cet élément de la Place de marché est disponible dans votre abonnement avant d’essayer de poursuivre ce tutoriel.

Ce que vous allez apprendre :

> [!div class="checklist"]
> * Créer un cluster MySQL Server à partir d’éléments de la Place de marché.
> * Créer un serveur d’hébergement MySQL Azure Stack Hub.
> * Créer une base de données MySQL à haute disponibilité.

Un cluster MySQL Server de trois machines virtuelles est créé et configuré avec des éléments disponibles dans la Place de marché Azure Stack.

Avant de commencer, vérifiez que le [fournisseur de ressources MySQL Server](azure-stack-mysql-resource-provider-deploy.md) a bien été installé et que les éléments suivants sont disponibles dans la Place de marché Azure Stack :

> [!IMPORTANT]
> Tous les éléments suivants sont nécessaires pour créer le cluster MySQL.

- [MySQL with Replication](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster) : Il s’agit du modèle de solution Bitnami qui sera utilisé pour le déploiement du cluster MySQL.
- [Debian 8 « Jessie »](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.Debian) : Debian 8 « Jessie » avec noyau backports pour Microsoft Azure fourni par credativ. Debian GNU/Linux est l’une des distributions Linux les plus populaires.
- [Script personnalisé pour Linux 2.0](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft.custom-script-linux?tab=Overview) : L’extension de script personnalisé est un outil pour exécuter les tâches de personnalisation de votre machine virtuelle après son provisionnement. Quand cette extension est ajoutée à une machine virtuelle, elle peut télécharger des scripts depuis le Stockage Azure et les exécuter sur la machine virtuelle. Les tâches de l’extension de script personnalisé peuvent également être automatisées avec des applets de commande Azure PowerShell et avec l’interface de ligne de commande multiplateforme Azure (xPlat CLI).
- VM Access For Linux Extension 1.4.7 : L’extension d’accès aux machines virtuelles vous permet de réinitialiser le mot de passe, la clé SSH ou les configurations SSH afin de récupérer l’accès à votre machine virtuelle. Avec cette extension, vous pouvez également ajouter un nouvel utilisateur avec un mot de passe ou une clé SSH, ou supprimer un utilisateur. Cette extension cible les machines virtuelles Linux.

Pour plus d’informations sur l’ajout d’éléments à la Place de marché Azure Stack, consultez [Vue d’ensemble de la Place de marché Azure Stack](azure-stack-marketplace.md).

Vous avez également besoin d’un client SSH comme [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) pour vous connecter aux machines virtuelles Linux après leur déploiement.

## <a name="create-a-mysql-server-cluster"></a>Créer un cluster MySQL Server

Utilisez les étapes décrites dans cette section pour déployer le cluster MySQL Server avec l’élément de la Place de marché [MySQL with Replication](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster). Ce modèle déploie trois instances MySQL Server configurées dans un cluster MySQL à haute disponibilité. Par défaut, il crée les ressources suivantes :

- Un réseau virtuel
- Un groupe de sécurité réseau
- Un compte de stockage
- Un groupe à haute disponibilité
- Trois interfaces réseau (une pour chacune des machines virtuelles par défaut)
- Une adresse IP publique (pour la machine virtuelle principale du cluster MySQL)
- Trois machines virtuelles Linux pour héberger le cluster MySQL

1. 
   [!INCLUDE [azs-admin-portal](../includes/azs-admin-portal.md)]

2. Sélectionnez **\+** **Créer une ressource** > **Calcul**, puis **MySQL with Replication**.

   ![Déploiement de modèle personnalisé dans Azure Stack Hub](media/azure-stack-tutorial-mysqlrp/1.png)

3. Spécifiez les informations de base du déploiement dans la page **De base**. Examinez les valeurs par défaut et modifiez-les au besoin, puis sélectionnez **OK**.

    Au minimum, spécifiez les informations suivantes :

   - Nom du déploiement (« mymysql » par défaut).
   - Mot de passe racine de l’application. Entrez un mot de passe de 12 caractères alphanumériques **sans aucun caractère spécial**.
   - Nom de la base de données d’application (« bitnami » par défaut).
   - Nombre de machines virtuelles de réplica de base de données MySQL à créer (par défaut, 2).
   - Sélectionnez l’abonnement à utiliser.
   - Sélectionnez le groupe de ressources existant à utiliser ou créez-en un.
   - Sélectionnez l’emplacement (par défaut, « local » pour ASDK).

   [![Concepts de base du déploiement : Créer MySQL with Replication](media/azure-stack-tutorial-mysqlrp/2-sm.PNG)](media/azure-stack-tutorial-mysqlrp/2-lg.PNG#lightbox)

4. Dans la page **Configuration de l’environnement**, entrez les informations suivantes, puis sélectionnez **OK** :

   - Mot de passe ou clé publique SSH à utiliser pour l’authentification SSH (secure shell). Si vous utilisez un mot de passe, celui-ci doit contenir des lettres et des chiffres, et il **peut** contenir des caractères spéciaux.
   - Taille de machine virtuelle (« Standard D1 v2 » par défaut).
   - Taille du disque de données en Go

   [![Configuration de l’environnement : Créer MySQL with Replication](media/azure-stack-tutorial-mysqlrp/3-sm.PNG)](media/azure-stack-tutorial-mysqlrp/3-lg.PNG#lightbox)

5. Examinez le **Résumé** du déploiement. Si vous le souhaitez, téléchargez le modèle personnalisé et les paramètres associés, puis sélectionnez **OK**.

   [![Récapitulatif : Créer MySQL with Replication](media/azure-stack-tutorial-mysqlrp/4-sm.PNG)](media/azure-stack-tutorial-mysqlrp/4-lg.PNG#lightbox)

6. Sélectionnez **Créer** dans la page **Acheter** pour démarrer le déploiement.

   ![Page Acheter : Créer MySQL with Replication](media/azure-stack-tutorial-mysqlrp/5.png)

    > [!NOTE]
    > Le déploiement prendra environ une heure. Vérifiez que le déploiement est terminé et que le cluster MySQL a été entièrement configuré avant de continuer.

7. Une fois tous les déploiements terminés, examinez les éléments du groupe de ressources, puis sélectionnez l’élément Adresse IP publique **mysqlip**. Notez l’adresse IP publique et le nom de domaine complet de l’adresse IP pour le cluster.

    Vous devrez fournir ces informations à un opérateur Azure Stack Hub pour lui permettre de créer un serveur d’hébergement MySQL qui utilise ce cluster MySQL.

### <a name="create-a-network-security-group-rule"></a>Créer une règle de groupe de sécurité réseau

Par défaut, aucun accès public n’est configuré pour MySQL dans la machine virtuelle hôte. Pour que le fournisseur de ressources Azure Stack Hub MySQL puisse se connecter au cluster MySQL et le gérer, vous devez créer une règle de groupe de sécurité réseau de trafic entrant.

1. Dans le portail d’administration, accédez au groupe de ressources créé lors du déploiement du cluster MySQL et sélectionnez le groupe de sécurité réseau (**default-subnet-sg**) :

   ![Sélectionner un groupe de sécurité réseau dans le portail d’administration Azure Stack Hub](media/azure-stack-tutorial-mysqlrp/6.png)

2. Sélectionnez les **règles de sécurité de trafic entrant**, puis sélectionnez **Ajouter**.

    Entrez **3306** dans **Plage de ports de destination** et, si vous le souhaitez, spécifiez une description dans les champs **Nom** et **Description**.

   ![ouvert](media/azure-stack-tutorial-mysqlrp/7.png)

3. Sélectionnez **Ajouter** pour fermer la boîte de dialogue Règle de sécurité de trafic entrant.

### <a name="configure-external-access-to-the-mysql-cluster"></a>Configurer l’accès externe au cluster MySQL

Avant de pouvoir ajouter le cluster MySQL comme hôte de serveur MySQL Azure Stack Hub, vous devez activer un accès externe.

1. Avec un client SSH (cet exemple utilise [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)), connectez-vous à la machine MySQL principale à partir d’un ordinateur ayant accès à l’adresse IP publique. Le nom de la machine virtuelle MySQL principale se termine généralement par **0** et une adresse IP publique lui est affectée.

    Utilisez l’adresse IP publique et connectez-vous à la machine virtuelle avec le nom d’utilisateur **bitnami** et avec le mot de passe d’application sans caractères spéciaux que vous avez créé précédemment.

   ![LinuxLogin](media/azure-stack-tutorial-mysqlrp/bitnami1.png)

2. Dans la fenêtre du client SSH, utilisez la commande suivante pour vérifier que le service bitnami est actif et en cours d’exécution. Spécifiez à nouveau le mot de passe de bitnami quand vous y êtes invité :

   `sudo service bitnami status`

   ![Vérifier le service bitnami](media/azure-stack-tutorial-mysqlrp/bitnami2.png)

3. Créez un compte d’utilisateur d’accès à distance à utiliser par le serveur d’hébergement MySQL Azure Stack Hub pour vous connecter à MySQL, puis quittez le client SSH.

    Exécutez les commandes suivantes pour vous connecter à MySQL en tant qu’utilisateur racine, en utilisant le mot de passe racine créé précédemment. Créez un utilisateur administrateur et remplacez *\<nom d’utilisateur\>* et *\<mot de passe\>* par les valeurs appropriées pour votre environnement. Dans cet exemple, l’utilisateur créé est nommé **sqlsa** et un mot de passe fort est utilisé :

   ```mysql
   mysql -u root -p
   create user <username>@'%' identified by '<password>';
   grant all privileges on *.* to <username>@'%' with grant option;
   flush privileges;
   ```

   ![Créer un utilisateur administrateur](media/azure-stack-tutorial-mysqlrp/bitnami3.png)

4. Notez les informations du nouvel utilisateur MySQL.

    Vous devrez fournir ce nom d’utilisateur et ce mot de passe, ainsi que l’adresse IP publique ou le nom de domaine complet de l’adresse IP publique du cluster, à un opérateur Azure Stack Hub pour lui permettre de créer un serveur d’hébergement MySQL avec ce cluster MySQL.

## <a name="create-an-azure-stack-hub-mysql-hosting-server"></a>Créer un serveur d’hébergement MySQL Azure Stack Hub

Une fois le cluster MySQL Server créé et correctement configuré, un opérateur Azure Stack Hub doit créer un serveur d’hébergement MySQL Azure Stack Hub pour rendre la capacité supplémentaire permettant de créer des bases de données disponible pour les utilisateurs.

Veillez à utiliser l’adresse IP publique ou le nom de domaine complet pour l’adresse IP publique de la machine virtuelle principale du cluster MySQL que vous avez enregistré précédemment lors de la création du groupe de ressources du cluster MySQL (**mysqlip**). En outre, l’opérateur doit connaître les informations d’identification d’authentification de MySQL Server que vous avez créées pour accéder à distance à la base de données du cluster MySQL.

> [!NOTE]
> Cette étape doit être effectuée par un opérateur Azure Stack Hub à partir du portail d’administration Azure Stack Hub.

À l’aide de l’adresse IP publique du cluster MySQL et des informations de connexion d’authentification MySQL, un opérateur Azure Stack Hub peut désormais [créer un serveur d’hébergement MySQL avec le nouveau cluster MySQL](azure-stack-mysql-resource-provider-hosting-servers.md#connect-to-a-mysql-hosting-server).

Vérifiez également que vous avez créé des plans et des offres qui permettent aux utilisateurs de créer des bases de données MySQL. Un opérateur doit ajouter le service **Microsoft.MySqlAdapter** à un plan et créer un quota spécifiquement pour les bases de données à haute disponibilité. Pour plus d’informations sur la création de plans, consultez [Vue d’ensemble des services, des plans, des offres et des abonnements](service-plan-offer-subscription-overview.md).

> [!TIP]
> Le service **Microsoft.MySqlAdapter** ne peut pas être ajouté à des plans tant que [le fournisseur de ressources MySQL Server n’a pas été déployé](azure-stack-mysql-resource-provider-deploy.md).

## <a name="create-a-highly-available-mysql-database"></a>Créer une base de données MySQL à haute disponibilité

Une fois le cluster MySQL créé, configuré et ajouté en tant que serveur d’hébergement MySQL Azure Stack Hub par un opérateur Azure Stack Hub, un utilisateur du locataire doté d'un abonnement incluant les fonctionnalités de base de données de serveur MySQL peut créer des bases de données MySQL à haute disponibilité en suivant la procédure décrite dans cette section.

> [!NOTE]
> Effectuez ces étapes à partir du portail utilisateur Azure Stack Hub en tant qu’utilisateur du locataire doté d'un abonnement fournissant des fonctionnalités de serveur MySQL (service Microsoft.MySQLAdapter).

1. 
   [!INCLUDE [azs-user-portal](../includes/azs-user-portal.md)]

2. Sélectionnez **\+** **Créer une ressource** > **Données\+Stockage**, puis **Base de données MySQL**.

    Spécifiez les informations des propriétés de base de données nécessaires, notamment le nom, le classement, l’abonnement et l’emplacement à utiliser pour le déploiement.

    ![Créer une base de données MySQL dans le portail utilisateur Azure Stack Hub](./media/azure-stack-tutorial-mysqlrp/createdb1.png)

3. Sélectionnez **Référence (SKU)** , puis choisissez la référence de serveur d’hébergement MySQL appropriée à utiliser. Dans cet exemple, l’opérateur Azure Stack Hub a créé la référence SKU **MySQL-HA** afin de prendre en charge la haute disponibilité pour les bases de données MySQL.

   ![Sélectionner une référence SKU dans le portail utilisateur Azure Stack Hub](./media/azure-stack-tutorial-mysqlrp/createdb2.png)

4. Sélectionnez **Se connecter** > **Créer une connexion**, puis spécifiez les informations d’identification d’authentification MySQL à utiliser pour la nouvelle base de données. Quand vous avez terminé, sélectionnez **OK**, puis **Créer** pour commencer le processus de déploiement de la base de données.

   ![Ajouter une connexion dans le portail utilisateur Azure Stack Hub](./media/azure-stack-tutorial-mysqlrp/createdb3.png)

5. Une fois que le déploiement de la base de données MySQL est terminé, examinez les propriétés de la base de données pour découvrir la chaîne de connexion à utiliser pour la connexion à la base de données à haute disponibilité.

   ![Afficher la chaîne de connexion dans le portail utilisateur Azure Stack Hub](./media/azure-stack-tutorial-mysqlrp/createdb4.png)

## <a name="next-steps"></a>Étapes suivantes

[Mettre à jour le fournisseur de ressources MySQL](azure-stack-mysql-resource-provider-update.md)
