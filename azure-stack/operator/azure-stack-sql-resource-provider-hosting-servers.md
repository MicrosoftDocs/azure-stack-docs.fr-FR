---
title: Serveurs d’hébergement SQL sur Azure Stack | Microsoft Docs
description: Comment ajouter des instances SQL pour l’approvisionnement via le fournisseur de ressources de l’adaptateur SQL
services: azure-stack
documentationCenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/26/2019
ms.author: mabrigg
ms.reviewer: quying
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: 54394d654bafae669addb2ccb0ceeb9cc4e3a6ed
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2019
ms.locfileid: "64984926"
---
# <a name="add-hosting-servers-for-the-sql-resource-provider"></a>Ajouter des serveurs d’hébergement pour le fournisseur de ressources SQL

Vous pouvez créer une base de données SQL Server hébergeant des serveurs sur une machine virtuelle dans [Azure Stack](azure-stack-overview.md), ou sur une machine virtuelle à l’extérieur de votre environnement Azure Stack, à condition que le fournisseur de ressources SQL puisse se connecter à l’instance.

> [!NOTE]
> Le fournisseur de ressources SQL doit être créé dans l’abonnement fournisseur par défaut, alors que les serveurs d’hébergement SQL doivent être créés dans un abonnement utilisateur facturable. Le serveur du fournisseur de ressources ne doit pas héberger de bases de données utilisateur.

## <a name="overview"></a>Vue d’ensemble

Avant d’ajouter un serveur d’hébergement SQL, consultez les exigences générales et obligations suivantes.

### <a name="mandatory-requirements"></a>Obligations

* Activez l’authentification SQL sur l’instance SQL Server. Étant donné que la machine virtuelle du fournisseur de ressources SQL n’est pas jointe à un domaine, elle ne peut se connecter à un serveur d’hébergement qu’à l’aide de l’authentification SQL.
* Configurez les adresses IP pour les instances SQL avec la valeur Public quand elles sont installées sur Azure Stack. Le fournisseur de ressources et les utilisateurs, notamment Web Apps, communiquent sur le réseau de l’utilisateur ; c’est pourquoi une connectivité à l’instance SQL sur ce réseau est nécessaire.

### <a name="general-requirements"></a>Conditions générales

* Dédiez l’instance SQL à une utilisation par le fournisseur de ressources et les charges de travail utilisateur. Vous ne pouvez pas utiliser d’instance SQL utilisée par un autre consommateur. Cette restriction s’applique également à App Services.
* Configurez un compte avec les niveaux de privilège appropriés pour le fournisseur de ressources (voir plus bas).
* Vous êtes chargé de gérer les instances SQL et leurs hôtes.  Par exemple, le fournisseur de ressources n’applique pas les mises à jour, ne gère pas les sauvegardes et ne gère pas non plus la rotation des informations d’identification.

### <a name="sql-server-virtual-machine-images"></a>Images de machines virtuelles SQL Server

Des images de machines virtuelles IaaS SQL sont disponibles via la fonctionnalité Gestion de la Place de Marché. Ces images sont les mêmes que les machines virtuelles SQL disponibles dans Azure.

Veillez à toujours télécharger la dernière version de **l’extension IaaS SQL** avant de déployer une machine virtuelle SQL à l’aide d’un élément de la Place de marché. L’extension IaaS et les améliorations apportées au portail correspondantes fournissent des fonctionnalités supplémentaires de mise à jour corrective et de sauvegarde automatique. Pour plus d’information sur cette extension, consultez [Automatiser les tâches de gestion sur des machines virtuelles Azure avec l’extension SQL Server Agent](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension).

> [!NOTE]
> L’extension SQL IaaS est _requise_ pour toutes les images SQL sur Windows dans la Place de marché. Si vous n’avez pas téléchargé cette extension, la machine virtuelle ne pourra pas être déployée. L’extension n’est pas utilisée avec les images de machine virtuelle SQL sur Linux.

Il existe d’autres options pour le déploiement de machines virtuelles SQL, y compris des modèles dans la [Galerie de démarrage rapide Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates).

> [!NOTE]
> Les serveurs d’hébergement installés sur un Azure Stack à plusieurs nœuds doivent être créés à partir d’un abonnement utilisateur et non de l’abonnement du fournisseur par défaut. Ils doivent être créés à partir du portail de l’utilisateur ou à partir d’une session PowerShell avec un nom de connexion approprié. Tous les serveurs d’hébergement sont des machines virtuelles facturables et doivent disposer des licences SQL appropriées. L’administrateur de service _peut_ être le propriétaire de cet abonnement.

### <a name="required-privileges"></a>Privilèges requis

Vous pouvez créer un utilisateur administratif avec des privilèges plus faibles qu’un administrateur système SQL. L’utilisateur a uniquement besoin des autorisations pour les opérations suivantes :

* Base de données : Créer, Modifier, Avec imbrication (pour Always On uniquement), Supprimer, Sauvegarder
* Groupe de disponibilité : Modifier, Joindre, Ajouter/Supprimer une base de données
* Connexion : Créer, Sélectionner, Modifier, Supprimer, Révoquer
* Sélection d’opérations : \[master\].\[sys\].\[availability_group_listeners\] (AlwaysOn), sys.availability_replicas (AlwaysOn), sys.databases, \[master\].\[sys\].\[dm_os_sys_memory\], SERVERPROPERTY, \[master\].\[sys\].\[availability_groups\] (AlwaysOn), sys.master_files

### <a name="additional-security-information"></a>Informations supplémentaires relatives à la sécurité

Les informations suivantes fournissent des conseils de sécurité supplémentaires :

* Tout le stockage Azure Stack étant chiffré à l’aide de BitLocker, toute instance SQL sur Azure Stack utilise le stockage d’objets blob chiffré.
* Le fournisseur de ressources SQL prend entièrement en charge TLS 1.2. Vérifiez que tout serveur SQL géré par le biais du fournisseur de ressources SQL est configuré pour TLS 1.2 _uniquement_ et que le fournisseur de ressources utilise ce dernier par défaut. Toutes les versions prises en charge de SQL Server prennent en charge TLS 1.2 ; consultez [Prise en charge de TLS 1.2 pour Microsoft SQL Server](https://support.microsoft.com/en-us/help/3135244/tls-1-2-support-for-microsoft-sql-server).
* Utilisez le Gestionnaire de configuration SQL Server pour définir l’option **ForceEncryption** afin que toutes les communications vers le serveur SQL soient toujours chiffrées. Consultez [Pour configurer le serveur afin qu’il force les connexions chiffrées](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine?view=sql-server-2017#ConfigureServerConnections).
* Vérifiez que toute application cliente communique également via une connexion chiffrée.
* Le fournisseur de ressources est configuré pour approuver les certificats utilisés par les instances de SQL Server.

## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>Fournir une capacité par le biais d’une connexion à un serveur SQL d’hébergement

Vous pouvez utiliser des serveurs SQL (non-HA) autonomes à l’aide de n’importe quelle édition de SQL Server 2014 ou SQL Server 2016. Assurez-vous de détenir les informations d’identification d’un compte disposant de privilèges d’administrateur système.

Pour ajouter un serveur d’hébergement autonome déjà configuré, effectuez les étapes suivantes :

1. Connectez-vous au portail d’administration Azure Stack en tant qu’administrateur de service.

2. Sélectionnez **Tous les services** &gt; **RESSOURCES ADMINISTRATIVES** &gt; **Serveurs d’hébergement SQL**.

   ![Serveurs d’hébergement SQL](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

   Sous **Serveurs d’hébergement SQL**, vous pouvez connecter le fournisseur de ressources SQL à des instances de SQL Server qui font office de back-end du fournisseur de ressources.

   ![Tableau de bord de l’adaptateur SQL](./media/azure-stack-sql-rp-deploy/sqlrp-hostingserver.png)

3. Cliquez sur **Ajouter**, puis fournissez les détails de la connexion pour votre instance SQL Server dans le panneau **Ajouter un serveur d’hébergement SQL**.

   ![Ajouter un serveur d’hébergement SQL](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.png)

    Vous pouvez également fournir un nom d’instance et spécifier un numéro de port si l’instance n’est pas affectée au port par défaut 1433.

   > [!NOTE]
   > Tant que l’instance SQL est accessible par l’utilisateur et l’administrateur Azure Resource Manager, elle peut être placée sous contrôle du fournisseur de ressources. L’instance de SQL __doit__ être allouée exclusivement au fournisseur de ressources.

4. Quand vous ajoutez des serveurs, vous devez les affecter à une référence (SKU) existante ou en créer une nouvelle. Sous **Ajouter un serveur d’hébergement**, sélectionnez **Références**.

   * Pour utiliser une référence (SKU) existante, choisissez-en une disponible, puis sélectionnez **Créer**.
   * Pour créer une référence (SKU), sélectionnez **+ Créer une référence**. Dans **Créer une référence SKU**, entez les informations obligatoires, puis sélectionnez **OK**.

     ![Créer une référence (SKU)](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

## <a name="provide-high-availability-using-sql-always-on-availability-groups"></a>Fournir une haute disponibilité à l’aide de groupes de disponibilité Always On SQL

La configuration d’instances Always On SQL nécessite des étapes supplémentaires et exige trois machines virtuelles (ou machines physiques). Cet article suppose que vous disposiez déjà d’une connaissance approfondie des groupes de disponibilité Always On. Pour plus d’informations, consultez les articles suivants :

* [Présentation des groupes de disponibilité SQL Server Always On sur des machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)
* [Groupes de disponibilité SQL Server Always On (SQL Server)](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2017)

> [!NOTE]
> Le fournisseur de ressources de l’adaptateur SQL prend en charge _uniquement_ SQL 2016 SP1 Enterprise ou les instances ultérieures pour les groupes de disponibilité Always On. Cette configuration de l’adaptateur requiert des nouvelles fonctionnalités SQL telles que l’amorçage automatique.

### <a name="automatic-seeding"></a>Amorçage automatique

Vous devez activer [l’amorçage automatique](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) sur chaque groupe de disponibilité pour chaque instance de SQL Server.

Pour activer l’amorçage automatique sur toutes les instances, modifiez, puis exécutez la commande SQL suivante sur le réplica principal pour chaque instance secondaire :

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON '<secondary_node>'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

Le groupe de disponibilité doit être placé entre crochets.

Sur les nœuds secondaires, exécutez la commande SQL suivante :

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```

### <a name="configure-contained-database-authentication"></a>Configurer l’option contained database authentication

Avant d’ajouter une base de données autonome à un groupe de disponibilité, vérifiez que l’option de serveur contained database authentication est définie sur 1 sur chaque instance de serveur qui héberge un réplica de disponibilité pour le groupe de disponibilité. Pour plus d’informations, consultez [contained database authentication (option de configuration de serveur)](https://docs.microsoft.com/sql/database-engine/configure-windows/contained-database-authentication-server-configuration-option?view=sql-server-2017).

Utilisez ces commandes pour définir l’option de serveur contained database authentication pour chaque instance :

  ```sql
  EXEC sp_configure 'contained database authentication', 1
  GO
  RECONFIGURE
  GO
  ```

### <a name="to-add-sql-always-on-hosting-servers"></a>Pour ajouter des serveurs d’hébergement Always On SQL

1. Connectez-vous au portail d’administration Azure Stack en tant qu’administrateur de service.

2. Sélectionnez **Parcourir** &gt; **RESSOURCES ADMINISTRATIVES** &gt; **Serveurs d’hébergement SQL** &gt; **+Ajouter**.

   Sous **Serveurs d’hébergement SQL**, vous pouvez connecter le fournisseur de ressources SQL Server à des instances réelles de SQL Server qui font office de back-end du fournisseur de ressources.

3. Remplissez le formulaire avec les détails de connexion de votre instance de SQL Server. Assurez-vous que vous utilisez l’adresse de nom de domaine complet de l’écouteur Always On (et les numéro de port et nom d’instance facultatifs). Fournissez les informations du compte que vous avez configuré avec des privilèges d’administrateur système.

4. Cochez la case Groupe de disponibilité Always On pour activer la prise en charge des instances de groupe de disponibilité Always On SQL.

   ![Activer Always On](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. Ajouter l’instance Always On SQL à une référence SKU.

   > [!IMPORTANT]
   > Vous ne pouvez pas mélanger des serveurs autonomes avec des instances Always On dans la même référence SKU. Toute tentative de mélange de types après l’ajout du premier serveur d’hébergement entraîne une erreur.

## <a name="sku-notes"></a>Remarques relatives aux références (SKU)
Utilisez un nom de référence SKU qui décrit les fonctionnalités des serveurs de la référence, comme la capacité et les performances. Le nom aide les utilisateurs à déployer leurs bases de données dans la référence (SKU) appropriée. Par exemple, vous pouvez utiliser des noms de référence SKU pour différencier les offres de service avec les caractéristiques suivantes :
  
* haute capacité
* hautes performances
* haute disponibilité

Il est considéré comme une bonne pratique que tous les serveurs d’hébergement d’une référence (SKU) aient les mêmes caractéristiques de performances et de ressources.

Vous ne pouvez pas attribuer de références SKU à des utilisateurs ou groupes.

Une heure entière peut être nécessaire avant que les références n’apparaissent dans le portail. Les utilisateurs ne peuvent pas créer de base de données tant que la référence (SKU) n’a pas été complètement créée.

Pour modifier une référence SKU, accédez à **Tous les services** > **Adaptateur SQL** > **Références**. Sélectionnez la référence SKU à modifier, apportez les changements nécessaires, puis cliquez sur **Enregistrer** pour enregistrer les changements. 

Pour supprimer une référence SKU dont vous ne vous servez plus, accédez à **Tous les services** > **Adaptateur SQL** > **Références**. Cliquez sur le nom de la référence SKU et sélectionnez **Supprimer** pour la supprimer.

> [!IMPORTANT]
> Il peut s’écouler jusqu’à une heure avant que les nouvelles référence SKU soient disponibles sur le portail de l’utilisateur.

## <a name="make-sql-databases-available-to-users"></a>Mettre les bases de données SQL à la disposition des utilisateurs

Créez des plans et des offres pour mettre des bases de données SQL à la disposition d’utilisateurs. Ajoutez le service **Microsoft.SqlAdapter** au plan, puis créez un quota.

> [!IMPORTANT]
> Il peut s’écouler jusqu’à deux heures avant que les nouveaux quotas ne soient disponibles sur le portail de l’utilisateur ou qu’un quota modifié ne soit appliqué.

## <a name="next-steps"></a>Étapes suivantes

[Ajouter des bases de données](azure-stack-sql-resource-provider-databases.md)
