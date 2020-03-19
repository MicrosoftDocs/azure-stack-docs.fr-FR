---
title: Ajouter des serveurs d’hébergement MySQL dans Azure Stack Hub
description: Découvrez comment ajouter des serveurs d’hébergement MySQL pour l’approvisionnement via le fournisseur de ressources de l’adaptateur MySQL.
author: bryanla
ms.topic: article
ms.date: 11/06/2019
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 11/06/2019
ms.openlocfilehash: b02ea9e241faea3aeaad76b85dbde9616e1edea5
ms.sourcegitcommit: 20d10ace7844170ccf7570db52e30f0424f20164
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79295544"
---
# <a name="add-mysql-hosting-servers-in-azure-stack-hub"></a>Ajouter des serveurs d’hébergement MySQL dans Azure Stack Hub

Vous pouvez héberger une instance de serveur d’hébergement MySQL sur une machine virtuelle dans [Azure Stack Hub](azure-stack-overview.md), ou sur une machine virtuelle extérieure à votre environnement Azure Stack Hub à condition que le fournisseur de ressources MySQL puisse se connecter à l’instance.

> [!NOTE]
> Le fournisseur de ressources MySQL doit être créé dans l’abonnement fournisseur par défaut, alors que les serveurs d’hébergement MySQL doivent être créés dans des abonnements utilisateur facturables. Le serveur du fournisseur de ressources ne doit pas héberger de bases de données utilisateur.

Vous pouvez utiliser les versions de MySQL 5.6, 5.7 et 8.0 pour vos serveurs d’hébergement. Le fournisseur de ressources MySQL ne prend pas en charge l’authentification caching_sha2_password. Les serveurs MySQL 8.0 doivent être configurés pour utiliser mysql_native_password.

## <a name="configure-external-access-to-the-mysql-hosting-server"></a>Configurer l’accès externe au serveur d’hébergement MySQL

Avant de pouvoir ajouter le serveur MySQL comme hôte de serveur MySQL Azure Stack Hub, vous devez activer l’accès externe. Prenez BitNami MySQL, qui est disponible dans la Place de marché Azure Stack Hub comme exemple. Vous pouvez effectuer les étapes suivantes pour configurer l’accès externe.

1. Avec un client SSH (cet exemple utilise [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)), connectez-vous au serveur MySQL à partir d’un ordinateur ayant accès à l’adresse IP publique.

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

Ce nom d’utilisateur et ce mot de passe sont utilisés quand l’opérateur Azure Stack Hub crée un serveur d’hébergement MySQL à l’aide de ce serveur MySQL.

## <a name="connect-to-a-mysql-hosting-server"></a>Se connecter à un serveur d’hébergement MySQL

Assurez-vous de détenir les informations d’identification d’un compte disposant de privilèges d’administrateur système.

> [!NOTE]
> Pour MySQL 8.0 et les versions ultérieures, l’accès à distance n’est pas activé par défaut. Vous devez créer un compte d’utilisateur et lui octroyer le privilège d’accès à distance avant de l’ajouter en tant que serveur d’hébergement.

Pour ajouter un serveur d’hébergement, procédez comme suit :

1. Connectez-vous au portail administrateur Azure Stack Hub en tant qu’administrateur de service.
2. Sélectionnez **Tous les services**.
3. Sous la catégorie **RESSOURCES ADMINISTRATIVES**, sélectionnez **Serveurs d’hébergement MySQL** >  **+Ajouter**. La boîte de dialogue **Ajouter un serveur d’hébergement MySQL** apparaît comme dans la capture d’écran suivante.

   ![Configurer un serveur d’hébergement MySQL](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

4. Spécifiez les détails de connexion de votre instance de MySQL Server.

   * Pour **Nom du serveur d’hébergement MySQL**, fournissez le nom de domaine complet (FQDN) ou une adresse IPv4 valide. N’utilisez pas le nom court de la machine virtuelle.
   * Le **Nom d’utilisateur** d’administrateur par défaut des images Bitnami MySQL disponibles sur la Place de marché Azure Stack Hub est *root*.
   * Si vous ne connaissez pas le **Mot de passe** racine, consultez la [Documentation Bitnami](https://docs.bitnami.com/azure/faq/#how-to-find-application-credentials) pour savoir comment l’obtenir.
   * Une instance MySQL par défaut n’est pas fournie, donc vous devez spécifier la **taille du serveur d’hébergement en Go**. Entrez une taille qui est proche de la capacité du serveur de base de données.
   * Conservez la valeur par défaut pour **Abonnement**.
   * Pour **Groupe de ressources**, créez un groupe ou utilisez un groupe existant.

   > [!NOTE]
   > Si l’instance MySQL est accessible par le locataire et l’administrateur Azure Resource Manager, vous pouvez la placer sous le contrôle du fournisseur de ressources. Mais, l’instance MySQL **doit** être allouée exclusivement au fournisseur de ressources.

5. Sélectionnez **Références (SKU)** pour ouvrir la boîte de dialogue **Créer une référence (SKU)** .

   ![Créer une référence (SKU) MySQL](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)

   Le **nom** de la référence SKU doit refléter les propriétés de la référence SKU pour que les utilisateurs puissent déployer leurs bases de données sur la référence SKU appropriée.

6. Sélectionnez **OK** pour créer la référence SKU.
   > [!NOTE]
   > Une heure entière peut être nécessaire avant que les références n’apparaissent dans le portail. Vous ne pouvez pas créer de base de données tant que la référence (SKU) n’a pas été déployée et exécutée.

7. Sous **Ajouter un serveur d’hébergement MySQL**, sélectionnez **Créer**.

À mesure que vous ajoutez des serveurs, vous devez les affecter à une référence (SKU) nouvelle ou existante pour différencier les offres de service. Par exemple, vous pouvez avoir une instance d’entreprise MySQL qui fournit des sauvegardes de base de données et automatiques améliorées. Vous pouvez réserver ce serveur hautes performances pour les différents services de votre organisation.

## <a name="security-considerations-for-mysql"></a>Considérations relatives à la sécurité pour MySQL

Les informations suivantes s’appliquent au fournisseur de ressources et aux serveurs d’hébergement MySQL :

* Vérifiez que tous les serveurs d'hébergement sont configurés pour la communication à l'aide de TLS 1.1. Consultez [Configuring MySQL to Use Encrypted Connections](https://dev.mysql.com/doc/refman/5.7/en/using-encrypted-connections.html) (Configuration de MySQL pour utiliser des connexions chiffrées).
* Utilisez [Transparent Data Encryption](https://dev.mysql.com/doc/mysql-secure-deployment-guide/5.7/en/secure-deployment-data-encryption.html).
* Le fournisseur de ressources MySQL ne prend pas en charge l’authentification caching_sha2_password.

## <a name="increase-backend-database-capacity"></a>Augmenter la capacité de base de données principale

Vous pouvez augmenter la capacité de base de données principale en déployant plus de serveurs MySQL dans le portail Azure Stack Hub. Ajoutez ces serveurs à une référence (SKU) nouvelle ou existante. Si vous ajoutez un serveur à une référence (SKU) existante, assurez-vous que les caractéristiques du serveur sont les mêmes que pour les autres serveurs dans la référence (SKU).

## <a name="sku-notes"></a>Remarques relatives aux références (SKU)
Utilisez un nom de référence SKU qui décrit les fonctionnalités des serveurs de la référence, comme la capacité et les performances. Le nom aide les utilisateurs à déployer leurs bases de données dans la référence (SKU) appropriée. Par exemple, vous pouvez utiliser des noms de référence SKU pour différencier les offres de service avec les caractéristiques suivantes :
  
* haute capacité
* hautes performances
* haute disponibilité

Il est considéré comme une bonne pratique que tous les serveurs d’hébergement d’une référence (SKU) aient les mêmes caractéristiques de performances et de ressources.

Vous ne pouvez pas attribuer de références SKU à des utilisateurs ou groupes.

Pour modifier une référence SKU, accédez à **Tous les services** > **Adaptateur MySQL** > **Références**. Sélectionnez la référence SKU à modifier, apportez les changements nécessaires, puis cliquez sur **Enregistrer** pour enregistrer les changements. 

Pour supprimer une référence SKU dont vous ne vous servez plus, accédez à **Tous les services** > **Adaptateur MySQL** > **Références**. Cliquez sur le nom de la référence SKU et sélectionnez **Supprimer** pour la supprimer.

> [!IMPORTANT]
> Il peut s’écouler jusqu’à une heure avant que les nouvelles référence SKU soient disponibles sur le portail de l’utilisateur.

## <a name="make-mysql-database-servers-available-to-your-users"></a>Mise à disposition des serveurs de base de données MySQL pour vos utilisateurs

Créez des plans et des offres pour mettre les serveurs de base de données MySQL à disposition des utilisateurs. Ajoutez le service Microsoft.MySqlAdapter au plan, puis créez un quota. MySQL n’autorise pas la limitation de la taille des bases de données.

> [!IMPORTANT]
> Il peut s’écouler jusqu’à deux heures avant que les nouveaux quotas ne soient disponibles sur le portail de l’utilisateur ou qu’un quota modifié ne soit appliqué.

## <a name="next-steps"></a>Étapes suivantes

[Création d’une base de données MySQL](azure-stack-mysql-resource-provider-databases.md)
