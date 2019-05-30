---
title: Offrir des bases de données SQL à haute disponibilité dans Azure Stack | Microsoft Docs
description: Découvrez comment créer un ordinateur hôte fournisseur de ressources SQL Server et des bases de données SQL AlwaysOn à haute disponibilité avec Azure Stack.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: justinha
ms.reviewer: quying
ms.lastreviewed: 10/23/2018
ms.openlocfilehash: e21819fc6475161e63fcdb99a723847c5d7fbbad
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66268408"
---
# <a name="tutorial-offer-highly-available-sql-databases"></a>Didacticiel : Offrir des bases de données SQL hautement disponibles

En tant qu’opérateur Azure Stack, vous pouvez configurer des machines virtuelles serveurs pour héberger des bases de données SQL Server. Une fois qu’un serveur d’hébergement SQL est correctement créé et géré par Azure Stack, les utilisateurs qui se sont abonnés aux services SQL peuvent facilement créer des bases de données SQL.

Ce tutoriel montre comment utiliser un modèle de démarrage rapide Azure Stack pour créer un [groupe de disponibilité AlwaysOn SQL Server](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017), comment l’ajouter en tant que serveur d’hébergement SQL Azure Stack, puis comment créer une base de données SQL à haute disponibilité.

Contenu :

> [!div class="checklist"]
> * Créer un groupe de disponibilité AlwaysOn SQL Server à partir d’un modèle
> * Créer un serveur d’hébergement SQL Azure Stack
> * Créer une base de données SQL à haute disponibilité

Dans ce tutoriel, un groupe de disponibilité AlwaysOn SQL Server de deux machines virtuelles est créé et configuré avec des éléments disponibles sur la Place de marché Azure Stack. 

Avant de commencer les étapes décrites dans ce didacticiel, vérifiez que le [fournisseur de ressources SQL Server](azure-stack-sql-resource-provider-deploy.md) a été installé avec succès et que les éléments suivants soient disponibles sur le marketplace Azure Stack :

> [!IMPORTANT]
> Tous les éléments suivants sont nécessaires pour pouvoir utiliser le modèle de démarrage rapide Azure Stack.

- Image de la Place de marché de [Windows Server 2016 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WindowsServer).
- SQL Server 2016 SP1 ou SP2 (Standard, Enterprise ou Developer) sur l’image du serveur Windows Server 2016. Ce tutoriel utilise l’image de la Place de marché de [SQL Server 2016 SP2 Enterprise sur Windows Server 2016](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoftsqlserver.sql2016sp2-ws2016).
- [SQL Server IaaS Extension](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) version 1.2.30 ou ultérieure. L’extension IaaS SQL installe les composants nécessaires pour les éléments de la Place de marché SQL Server pour toutes les versions de Windows. Il permet la configuration de paramètres spécifiques à SQL sur les machines virtuelles SQL. Si l’extension n’est pas installée dans la Place de marché locale, le provisionnement de SQL échoue.
- [Extension de script personnalisé pour Windows](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.CustomScriptExtension) version 1.9.1 ou ultérieure. L’extension de script personnalisé est un outil qui peut être utilisé pour lancer automatiquement des tâches de personnalisation post-déploiement des machines virtuelles.
- [Configuration d’état souhaité PowerShell](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.DSC-arm) version 2.76.0.0 ou ultérieure. Configuration d’état souhaité est une plateforme de gestion dans Windows PowerShell qui permet le déploiement et la gestion des données de configuration des services logiciels, et la gestion de l’environnement dans lequel ces services s’exécutent.

Pour plus d’informations sur l’ajout d’éléments à la Place de marché Azure Stack, consultez [Vue d’ensemble de la Place de marché Azure Stack](azure-stack-marketplace.md).

## <a name="create-a-sql-server-alwayson-availability-group"></a>Créer un groupe de disponibilité SQL Server AlwaysOn
Utilisez les étapes de cette section pour déployer le groupe de disponibilité SQL Server AlwaysOn avec le [modèle de démarrage rapide Azure Stack sql-2016-alwayson](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2016-alwayson). Ce modèle déploie deux instances de SQL Server Enterprise, Standard ou Developer dans un groupe de disponibilité AlwaysOn. Il crée les ressources suivantes :

- Un groupe de sécurité réseau
- Un réseau virtuel
- Quatre comptes de stockage (un pour Active Directory (AD), un pour SQL, pour le témoin de partage de fichiers et un pour les diagnostics de machine virtuelle)
- Quatre adresses IP publiques (une pour AD, deux pour chaque machine virtuelle SQL et une pour l’équilibreur de charge public lié à l’écouteur SQL AlwaysOn)
- Un équilibreur de charge externe pour les machines virtuelles SQL avec une adresse IP publique liée à l’écouteur SQL AlwaysOn
- Une machine virtuelle (Windows Server 2016) configurée comme contrôleur de domaine pour une nouvelle forêt avec un seul domaine
- Deux machines virtuelles (Windows Server 2016) configurées en cluster avec SQL Server 2016 SP1 ou SP2 Enterprise, Standard ou Developer Edition. Il doit s’agir d’images de la Place de marché.
- Une machine virtuelle (Windows Server 2016) configurée comme témoin de partage de fichiers pour le cluster
- Un groupe à haute disponibilité contenant les machines virtuelles SQL et témoin de partage de fichiers  

1. 
   [!INCLUDE [azs-admin-portal](../includes/azs-admin-portal.md)]

2. Sélectionnez **\+** **Créer une ressource** > **Personnalisé**, puis **Déploiement de modèle**.

   ![Déploiement de modèle personnalisé](media/azure-stack-tutorial-sqlrp/1.png)


3. Dans le panneau **Déploiement personnalisé**, sélectionnez **Modifier le modèle** > **Modèle de démarrage rapide**, puis utilisez la liste déroulante des modèles personnalisés disponibles pour sélectionner le modèle **sql-2016-alwayson**, cliquez sur **OK**, puis sur **Enregistrer**.

   [![](media/azure-stack-tutorial-sqlrp/2-sm.PNG "Sélectionner un modèle de démarrage rapide")](media/azure-stack-tutorial-sqlrp/2-lg.PNG#lightbox)

4. Dans le panneau **Déploiement personnalisé**, sélectionnez **Modifier les paramètres** et examinez les valeurs par défaut. Modifiez les valeurs de façon appropriée pour fournir toutes les informations des paramètres nécessaires, puis cliquez sur **OK**.<br><br> Au minimum :

    - Spécifiez des mots de passe complexes pour les paramètres ADMINPASSWORD, SQLSERVERSERVICEACCOUNTPASSWORD et SQLAUTHPASSWORD.
    - Entrez le suffixe DNS pour la recherche inversée tout en minuscules pour le paramètre DNSSUFFIX (**azurestack.external** pour les installations ASDK).
    
   [![](media/azure-stack-tutorial-sqlrp/3-sm.PNG "Modifier les paramètres du déploiement personnalisé")](media/azure-stack-tutorial-sqlrp/3-lg.PNG#lightbox)

5. Dans le panneau **Déploiement personnalisé**, sélectionnez l’abonnement à utiliser, et créez un groupe de ressources ou sélectionnez un groupe de ressources existant pour le déploiement personnalisé.<br><br> Ensuite, sélectionnez l’emplacement du groupe de ressources (**local** pour les installations ASDK), puis cliquez sur **Créer**. Les paramètres du déploiement personnalisé sont validés, puis le déploiement commence.

    [![](media/azure-stack-tutorial-sqlrp/4-sm.PNG "Créer un déploiement personnalisé")](media/azure-stack-tutorial-sqlrp/4-lg.PNG#lightbox)


6. Dans le portail d’administration, sélectionnez **Groupes de ressources**, puis le nom du groupe de ressources que vous avez créé pour le déploiement personnalisé (**resource-group** pour cet exemple). Regardez l’état du déploiement pour vérifier que tous les déploiements ont réussi.<br><br>Ensuite, passez en revue les éléments du groupe de ressources et sélectionnez l’élément Adresse IP publique de **SQLPIPsql\<nom_groupe_ressources\>** . Notez l’adresse IP publique et le nom de domaine complet de l’équilibreur de charge. Vous devrez fournir ces informations à un opérateur Azure Stack pour qu’il puisse créer un serveur d’hébergement SQL tirant parti de ce groupe de disponibilité SQL AlwaysOn.

   > [!NOTE]
   > Le déploiement du modèle peut durer plusieurs heures.

   ![Déploiement personnalisé terminé](./media/azure-stack-tutorial-sqlrp/5.png)

### <a name="enable-automatic-seeding"></a>Activer l’amorçage automatique
Une fois que le modèle a déployé et configuré le groupe de disponibilité AlwaysOn SQL, vous devez activer [l’amorçage automatique](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) sur chaque instance de SQL Server du groupe de disponibilité. 

Quand vous créez un groupe de disponibilité avec amorçage automatique, SQL Server crée automatiquement les réplicas secondaires pour chaque base de données du groupe, sans qu’aucune autre intervention manuelle ne soit nécessaire pour garantir la haute disponibilité des bases de données AlwaysOn.

Utilisez ces commandes SQL pour configurer l’amorçage automatique pour le groupe de disponibilité AlwaysOn. Remplacez \<InstanceName\> par le nom de l’instance SQL Server principale et <availability_group_name> par le nom du groupe de disponibilité AlwaysOn si nécessaire. 

Sur l’instance SQL principale :

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON '<InstanceName>'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

>  ![Script de l’instance SQL principale](./media/azure-stack-tutorial-sqlrp/sql1.png)

Sur les instances SQL secondaires :

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```
>  ![Script des instances SQL secondaires](./media/azure-stack-tutorial-sqlrp/sql2.png)

### <a name="configure-contained-database-authentication"></a>Configurer l’option contained database authentication
Avant d’ajouter une base de données autonome à un groupe de disponibilité, vérifiez que l’option de serveur contained database authentication est définie sur 1 sur chaque instance de serveur qui héberge un réplica de disponibilité pour le groupe de disponibilité. Pour plus d’informations, consultez [contained database authentication](https://docs.microsoft.com/sql/database-engine/configure-windows/contained-database-authentication-server-configuration-option?view=sql-server-2017).

Utilisez ces commandes pour définir l’option du serveur « contained database authentication » pour chaque instance SQL Server du groupe de disponibilité :

  ```sql
  EXEC sp_configure 'contained database authentication', 1
  GO
  RECONFIGURE
  GO
  ```
>  ![Définir l’option « contained database authentication »](./media/azure-stack-tutorial-sqlrp/sql3.png)

## <a name="create-an-azure-stack-sql-hosting-server"></a>Créer un serveur d’hébergement SQL Azure Stack
Une fois que le groupe de disponibilité SQL Server AlwaysOn a été créé et configuré correctement, un opérateur Azure Stack doit créer un serveur d’hébergement SQL Azure Stack pour rendre la fonctionnalité supplémentaire permettant de créer des bases de données disponible pour les utilisateurs. 

Veillez à utiliser l’adresse IP publique ou le nom de domaine complet pour l’adresse IP publique de l’équilibreur de charge SQL que vous avez enregistré précédemment lors de la création du groupe de ressources du groupe de disponibilité AlwaysOn SQL (**SQLPIPsql\<nom du groupe de ressources\>** ). En outre, vous devez connaître les informations d’identification SQL Server permettant d’accéder aux instances SQL du groupe de disponibilité AlwaysOn.

> [!NOTE]
> Cette étape doit être effectuée à partir du portail d’administration Azure Stack par un opérateur Azure Stack.

Avec l’adresse IP publique et les informations de connexion d’authentification SQL de l’écouteur de l’équilibreur de charge du groupe de disponibilité AlwaysOn SQL, un opérateur Azure Stack peut maintenant [créer un serveur d’hébergement SQL avec le groupe de disponibilité AlwaysOn SQL](azure-stack-sql-resource-provider-hosting-servers.md#provide-high-availability-using-sql-always-on-availability-groups). 

Vérifiez également que vous avez créé des plans et des offres pour rendre la création de bases de données AlwaysOn SQL disponible pour les utilisateurs. L’opérateur doit ajouter le service **Microsoft.SqlAdapter** à un plan et créer un quota spécifiquement pour les bases de données à haute disponibilité. Pour plus d’informations sur la création de plans, consultez [Vue d’ensemble des plans, des offres, des quotas et des abonnements](azure-stack-plan-offer-quota-overview.md).

> [!TIP]
> Le service **Microsoft.SqlAdapter** ne peut pas être ajouté à des plans tant que [le fournisseur de ressources SQL Server n’a pas été déployé](azure-stack-sql-resource-provider-deploy.md).

## <a name="create-a-highly-available-sql-database"></a>Créer une base de données SQL à haute disponibilité
Une fois que le groupe de disponibilité AlwaysOn SQL a été créé, configuré et ajouté en tant que serveur d’hébergement SQL Azure Stack par un opérateur Azure Stack, un utilisateur du locataire avec un abonnement incluant les fonctionnalités de base de données SQL Server peut créer des bases de données SQL prenant en charge les fonctionnalités AlwaysOn en suivant les étapes décrites dans cette section. 

> [!NOTE]
> Effectuez ces étapes à partir du portail utilisateur Azure Stack en tant qu’utilisateur du locataire, avec un abonnement fournissant des fonctionnalités SQL Server (service Microsoft.SQLAdapter).

1. 
   [!INCLUDE [azs-user-portal](../includes/azs-user-portal.md)]

2. Cliquez sur **\+** **Créer une ressource** > **Données\+Stockage**, puis sur **Base de données SQL**.<br><br>Spécifiez les informations des propriétés de base de données nécessaires, notamment le nom, le classement, la taille maximale, et l’abonnement, le groupe de ressources et l’emplacement à utiliser pour le déploiement. 

   ![Créer une base de données SQL](./media/azure-stack-tutorial-sqlrp/createdb1.png)

3. Sélectionnez **Référence (SKU)** , puis choisissez la référence de serveur d’hébergement SQL appropriée à utiliser. Dans cet exemple, l’opérateur Azure Stack a créé la référence **Enterprise-HA** pour prendre en charge la haute disponibilité pour les groupes de disponibilité AlwaysOn SQL.

   ![Sélectionner une référence (SKU)](./media/azure-stack-tutorial-sqlrp/createdb2.png)

4. Sélectionnez **Se connecter** > **Créer une connexion**, puis spécifiez les informations d’identification d’authentification SQL à utiliser pour la nouvelle base de données. Quand vous avez terminé, cliquez sur **OK**, puis sur **Créer** pour commencer le processus de déploiement de la base de données.

   ![Créer une connexion](./media/azure-stack-tutorial-sqlrp/createdb3.png)

5. Une fois que le déploiement de la base de données SQL est terminé, examinez les propriétés de la base de données pour découvrir la chaîne de connexion à utiliser pour la connexion à la base de données à haute disponibilité. 

   ![Voir la chaîne de connexion](./media/azure-stack-tutorial-sqlrp/createdb4.png)




## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un groupe de disponibilité AlwaysOn SQL Server à partir d’un modèle
> * Créer un serveur d’hébergement SQL Azure Stack
> * Créer une base de données SQL à haute disponibilité

Passez au didacticiel suivant pour savoir comment :
> [!div class="nextstepaction"]
> [Créer des bases de données MySQL à haute disponibilité](azure-stack-tutorial-mysql.md)