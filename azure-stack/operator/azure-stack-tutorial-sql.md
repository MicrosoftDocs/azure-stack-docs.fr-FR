---
title: Créer des bases de données SQL hautement disponibles
titleSuffix: Azure Stack Hub
description: Découvrez comment créer un ordinateur hôte fournisseur de ressources SQL Server et des bases de données SQL AlwaysOn à haut niveau de disponibilité avec Azure Stack Hub.
author: BryanLa
ms.topic: article
ms.date: 10/07/2019
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 10/23/2019
ms.openlocfilehash: bd62be6a7a2990a7a405dd5c5e1ff44e64007b6f
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77696797"
---
# <a name="create-highly-available-sql-databases-with-azure-stack-hub"></a>Créer des bases de données SQL à haute disponibilité avec Azure Stack Hub

En tant qu’opérateur Azure Stack Hub, vous pouvez configurer des machines virtuelles serveurs pour héberger des bases de données SQL Server. Une fois qu’un serveur d’hébergement SQL est créé et managé par Azure Stack Hub, les utilisateurs qui se sont abonnés aux services SQL peuvent facilement créer des bases de données SQL.

Cet article montre comment utiliser un modèle de démarrage rapide Azure Stack Hub pour créer un [groupe de disponibilité SQL Server AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017), l’ajouter en tant que serveur d’hébergement SQL Azure Stack Hub, puis créer une base de données SQL à haut niveau de disponibilité.

Ce que vous allez apprendre :

> [!div class="checklist"]
> * Créer un groupe de disponibilité SQL Server AlwaysOn à partir d’un modèle.
> * Créer un serveur d’hébergement SQL Azure Stack Hub.
> * Créer une base de données SQL à haute disponibilité.

Un groupe de disponibilité SQL Server AlwaysOn composé de deux machines virtuelles est créé et configuré avec des éléments disponibles sur la Place de marché Azure Stack.

Avant de commencer, vérifiez que le [fournisseur de ressources SQL Server](azure-stack-sql-resource-provider-deploy.md) a été installé avec succès et que les éléments suivants sont disponibles dans la Place de marché Azure Stack :

> [!IMPORTANT]
> Tous les éléments suivants sont nécessaires pour pouvoir utiliser le modèle de démarrage rapide Azure Stack Hub.

- Image de la Place de marché de [Windows Server 2016 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/MicrosoftWindowsServer.WindowsServer).
- SQL Server 2016 SP1 ou SP2 (Standard, Enterprise ou Developer) sur l’image du serveur Windows Server 2016. Cet article utilise l’image de la Place de marché [SQL Server 2016 SP2 Enterprise on Windows Server 2016](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoftsqlserver.sql2016sp2-ws2016).
- [SQL Server IaaS Extension](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) version 1.2.30 ou ultérieure. L’extension IaaS SQL installe les composants nécessaires pour les éléments de la Place de marché SQL Server pour toutes les versions de Windows. Elle permet la configuration de paramètres SQL sur les machines virtuelles SQL. Si l’extension n’est pas installée dans la place de marché locale, le provisionnement de SQL échoue.
- [Extension de script personnalisé pour Windows](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.CustomScriptExtension) version 1.9.1 ou ultérieure. L’extension de script personnalisé est un outil qui peut être utilisé pour lancer automatiquement des tâches de personnalisation post-déploiement des machines virtuelles.
- [Configuration d’état souhaité PowerShell](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.DSC-arm) version 2.76.0.0 ou ultérieure. DSC (Desired State Configuration) est une plateforme de gestion dans Windows PowerShell qui permet le déploiement et la gestion des données de configuration des services logiciels. Cette plateforme gère également l’environnement d’exécution de ces services.

Pour plus d’informations sur l’ajout d’éléments à la Place de marché Azure Stack, consultez [Vue d’ensemble de la Place de marché Azure Stack Hub](azure-stack-marketplace.md).

## <a name="create-a-sql-server-alwayson-availability-group"></a>Créer un groupe de disponibilité SQL Server AlwaysOn

Suivez les étapes de cette section pour déployer le groupe de disponibilité SQL Server AlwaysOn avec le [modèle de démarrage rapide Azure Stack Hub sql-2016-alwayson](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2016-alwayson). Ce modèle déploie deux instances de SQL Server Enterprise, Standard ou Developer dans un groupe de disponibilité AlwaysOn. Il crée les ressources suivantes :

- Un groupe de sécurité réseau.
- Un réseau virtuel.
- Quatre comptes de stockage (un pour Active Directory, un pour SQL, un pour le témoin de partage de fichiers et un pour les diagnostics de machine virtuelle).
- Quatre adresses IP publiques (une pour AD, deux pour chaque machine virtuelle SQL et une pour l’équilibreur de charge public lié à l’écouteur SQL AlwaysOn).
- Un équilibreur de charge externe pour les machines virtuelles SQL avec une adresse IP publique liée à l’écouteur SQL AlwaysOn.
- Une machine virtuelle (Windows Server 2016) configurée comme contrôleur de domaine pour une nouvelle forêt avec un seul domaine.
- Deux machines virtuelles (Windows Server 2016) configurées en cluster avec SQL Server 2016 SP1 ou SP2 Enterprise, Standard ou Developer Edition. Il doit s’agir d’images de la Place de marché.
- Une machine virtuelle (Windows Server 2016) configurée comme témoin de partage de fichiers pour le cluster.
- Un groupe à haute disponibilité contenant les machines virtuelles SQL et témoins de partage de fichiers.

1. 
   [!INCLUDE [azs-admin-portal](../includes/azs-admin-portal.md)]

2. Sélectionnez **\+** **Créer une ressource** > **Personnalisé**, puis **Déploiement de modèle**.

   ![Déploiement de modèle personnalisé dans le portail d’administration Azure Stack Hub](media/azure-stack-tutorial-sqlrp/1.png)

3. Dans le panneau **Déploiement personnalisé**, sélectionnez **Modifier le modèle** > **Modèle de démarrage rapide**, puis utilisez la liste déroulante des modèles personnalisés disponibles pour sélectionner le modèle **sql-2016-alwayson**. Sélectionnez **OK**, puis **Enregistrer**.

   [![Modifier un modèle dans le portail d’administration Azure Stack Hub](media/azure-stack-tutorial-sqlrp/2-sm.PNG "Sélectionner un modèle de démarrage rapide")](media/azure-stack-tutorial-sqlrp/2-lg.PNG#lightbox)

4. Dans le panneau **Déploiement personnalisé**, sélectionnez **Modifier les paramètres** et examinez les valeurs par défaut. Modifiez les valeurs de façon appropriée pour fournir toutes les informations des paramètres nécessaires, puis sélectionnez **OK**.

    Au minimum :
    - Spécifiez des mots de passe complexes pour les paramètres ADMINPASSWORD, SQLSERVERSERVICEACCOUNTPASSWORD et SQLAUTHPASSWORD.
    - Entrez le suffixe DNS pour la recherche inversée tout en minuscules pour le paramètre DNSSUFFIX (**azurestack.external** pour les installations ASDK).
    
   [![Modifier les paramètres dans le portail d’administration Azure Stack Hub](media/azure-stack-tutorial-sqlrp/3-sm.PNG "Modifier les paramètres du déploiement personnalisé")](media/azure-stack-tutorial-sqlrp/3-lg.PNG#lightbox)

5. Dans le panneau **Déploiement personnalisé**, sélectionnez l’abonnement à utiliser, et créez un groupe de ressources ou sélectionnez un groupe de ressources existant pour le déploiement personnalisé.

    Ensuite, sélectionnez l’emplacement du groupe de ressources (**local** pour les installations ASDK), puis cliquez sur **Créer**. Les paramètres du déploiement personnalisé sont validés, puis le déploiement commence.

    [![Choisir un abonnement dans le portail d’administration Azure Stack Hub](media/azure-stack-tutorial-sqlrp/4-sm.PNG "Créer un déploiement personnalisé")](media/azure-stack-tutorial-sqlrp/4-lg.PNG#lightbox)

6. Dans le portail d’administration, sélectionnez **Groupes de ressources**, puis le nom du groupe de ressources que vous avez créé pour le déploiement personnalisé (**resource-group** pour cet exemple). Regardez l’état du déploiement pour vérifier que tous les déploiements ont réussi.
    
    Ensuite, passez en revue les éléments du groupe de ressources et sélectionnez l’élément Adresse IP publique de **SQLPIPsql\<nom_groupe_ressources\>** . Notez l’adresse IP publique et le nom de domaine complet de l’équilibreur de charge. Vous devrez fournir ces informations à un opérateur Azure Stack Hub pour que celui-ci puisse créer un serveur d’hébergement SQL qui utilise ce groupe de disponibilité SQL AlwaysOn.

   > [!NOTE]
   > Le déploiement du modèle peut durer plusieurs heures.

   ![Déploiement personnalisé terminé dans le portail d’administration Azure Stack Hub](./media/azure-stack-tutorial-sqlrp/5.png)

### <a name="enable-automatic-seeding"></a>Activer l’amorçage automatique

Une fois que le modèle a déployé et configuré le groupe de disponibilité AlwaysOn SQL, vous devez activer [l’amorçage automatique](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) sur chaque instance de SQL Server du groupe de disponibilité.

Quand vous créez un groupe de disponibilité avec amorçage automatique, SQL Server crée automatiquement les réplicas secondaires pour chaque base de données du groupe, sans qu’aucune autre intervention manuelle ne soit nécessaire. Cette mesure garantit la haute disponibilité des bases de données AlwaysOn.

Utilisez ces commandes SQL pour configurer l’amorçage automatique pour le groupe de disponibilité AlwaysOn. Remplacez `<InstanceName>` par le nom de l’instance SQL Server principale et `<availability_group_name>` par le nom du groupe de disponibilité AlwaysOn approprié.

Sur l’instance SQL principale :

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON '<InstanceName>'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

![Script de l’instance SQL principale](./media/azure-stack-tutorial-sqlrp/sql1.png)

Sur les instances SQL secondaires :

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```

![Script des instances SQL secondaires](./media/azure-stack-tutorial-sqlrp/sql2.png)

### <a name="configure-contained-database-authentication"></a>Configurer l’option contained database authentication

Avant d’ajouter une base de données autonome à un groupe de disponibilité, vérifiez que l’option de serveur contained database authentication est définie sur 1 sur chaque instance de serveur qui héberge un réplica de disponibilité pour le groupe de disponibilité. Pour plus d’informations, consultez [contained database authentication](https://docs.microsoft.com/sql/database-engine/configure-windows/contained-database-authentication-server-configuration-option?view=sql-server-2017).

Utilisez ces commandes pour définir l’option du serveur « contained database authentication » pour chaque instance SQL Server du groupe de disponibilité :

  ```sql
  EXEC sp_configure 'contained database authentication', 1
  GO
  RECONFIGURE
  GO
  ```

![Définir l’option « contained database authentication »](./media/azure-stack-tutorial-sqlrp/sql3.png)

## <a name="create-an-azure-stack-hub-sql-hosting-server"></a>Créer un serveur d’hébergement SQL Azure Stack Hub

Une fois le groupe de disponibilité SQL Server AlwaysOn créé et configuré, un opérateur Azure Stack Hub doit créer un serveur d’hébergement SQL Azure Stack Hub. Ce serveur fournit aux utilisateurs des capacités supplémentaires pour la création de bases de données.

Veillez à utiliser l’adresse IP publique ou le nom de domaine complet pour l’adresse IP publique de l’équilibreur de charge SQL que vous avez enregistré précédemment lors de la création du groupe de ressources du groupe de disponibilité AlwaysOn SQL (**SQLPIPsql\<nom du groupe de ressources\>** ). En outre, vous devez connaître les informations d’identification SQL Server permettant d’accéder aux instances SQL du groupe de disponibilité AlwaysOn.

> [!NOTE]
> Cette étape doit être effectuée par un opérateur Azure Stack Hub à partir du portail d’administration Azure Stack Hub.

Avec l’adresse IP publique et les informations de connexion d’authentification SQL de l’écouteur de l’équilibreur de charge du groupe de disponibilité SQL AlwaysOn, un opérateur Azure Stack Hub peut [créer un serveur d’hébergement SQL à l’aide du groupe de disponibilité SQL AlwaysOn](azure-stack-sql-resource-provider-hosting-servers.md#provide-high-availability-using-sql-always-on-availability-groups). 

Vérifiez également que vous avez créé des plans et des offres pour rendre la création de bases de données AlwaysOn SQL disponible pour les utilisateurs. L’opérateur doit ajouter le service **Microsoft.SqlAdapter** à un plan et créer un quota spécifiquement pour les bases de données à haute disponibilité. Pour plus d’informations sur la création de plans, consultez [Vue d’ensemble des services, des plans, des offres et des abonnements](service-plan-offer-subscription-overview.md).

> [!TIP]
> Le service **Microsoft.SqlAdapter** ne peut pas être ajouté à des plans tant que [le fournisseur de ressources SQL Server n’a pas été déployé](azure-stack-sql-resource-provider-deploy.md).

## <a name="create-a-highly-available-sql-database"></a>Créer une base de données SQL à haute disponibilité

Une fois le groupe de disponibilité SQL AlwaysOn créé, configuré et ajouté en tant que serveur d’hébergement SQL Azure Stack Hub par un opérateur Azure Stack Hub, les utilisateurs du locataire titulaires d’un abonnement incluant les fonctionnalités de base de données SQL Server peuvent créer des bases de données SQL qui prennent en charge les fonctionnalités AlwaysOn. Ils peuvent créer ces bases de données en suivant les étapes décrites dans cette section.

> [!NOTE]
> Effectuez ces étapes à partir du portail utilisateur Azure Stack Hub en tant qu’utilisateur du locataire, avec un abonnement fournissant des fonctionnalités SQL Server (service Microsoft.SQLAdapter).

1. 
   [!INCLUDE [azs-user-portal](../includes/azs-user-portal.md)]

2. Cliquez sur **\+** **Créer une ressource** > **Données\+Stockage**, puis sur **SQL Database**.

    Spécifiez les informations des propriétés de base de données nécessaires. Cela inclut le nom, le classement, la taille maximale, l’abonnement, le groupe de ressources et l’emplacement à utiliser pour le déploiement.

   ![Créer une base de données SQL dans le portail utilisateur Azure Stack Hub](./media/azure-stack-tutorial-sqlrp/createdb1.png)

3. Sélectionnez **Référence (SKU)** , puis choisissez la référence de serveur d’hébergement SQL appropriée à utiliser. Dans cet exemple, l’opérateur Azure Stack Hub a créé la référence SKU **Enterprise-HA** afin de prendre en charge la haute disponibilité pour les groupes de disponibilité SQL AlwaysOn.

   ![Sélectionner une référence SKU dans le portail utilisateur Azure Stack Hub](./media/azure-stack-tutorial-sqlrp/createdb2.png)

4. Sélectionnez **Se connecter** > **Créer une connexion**, puis spécifiez les informations d’identification d’authentification SQL à utiliser pour la nouvelle base de données. Quand vous avez terminé, sélectionnez **OK**, puis **Créer** pour commencer le processus de déploiement de la base de données.

   ![Créer une connexion dans le portail utilisateur Azure Stack Hub](./media/azure-stack-tutorial-sqlrp/createdb3.png)

5. Une fois que le déploiement de la base de données SQL est terminé, examinez les propriétés de la base de données pour découvrir la chaîne de connexion à utiliser pour la connexion à la base de données à haute disponibilité.

   ![Afficher la chaîne de connexion dans le portail utilisateur Azure Stack Hub](./media/azure-stack-tutorial-sqlrp/createdb4.png)

## <a name="next-steps"></a>Étapes suivantes

[Mettre à jour le fournisseur de ressources SQL](azure-stack-sql-resource-provider-update.md)
