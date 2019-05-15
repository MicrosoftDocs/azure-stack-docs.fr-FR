---
title: Serveurs d’hébergement MySQL sur Azure Stack | Microsoft Docs
description: Comment ajouter des instances MySQL pour l’approvisionnement via le fournisseur de ressources de l’adaptateur MySQL
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
ms.lastreviewed: 02/28/2019
ms.openlocfilehash: 00469e3dedca4609b26e1d4e967837ff0925cb9c
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618573"
---
# <a name="add-hosting-servers-for-the-mysql-resource-provider"></a>Ajouter des serveurs d’hébergement pour le fournisseur de ressources MySQL

Vous pouvez héberger une instance de serveur d'hébergement MySQL sur une machine virtuelle dans [Azure Stack](azure-stack-overview.md), ou sur une machine virtuelle à l’extérieur de votre environnement Azure Stack, à condition que le fournisseur de ressources MySQL puisse se connecter à l’instance.

> [!NOTE]
> Le fournisseur de ressources MySQL doit être créé dans l’abonnement fournisseur par défaut, alors que les serveurs d’hébergement MySQL doivent être créés dans des abonnements utilisateur facturables. Le serveur du fournisseur de ressources ne doit pas héberger de bases de données utilisateur.

Vous pouvez utiliser les versions de MySQL 5.6, 5.7 et 8.0 pour vos serveurs d’hébergement. Le fournisseur de ressources MySQL ne prend pas en charge l’authentification caching_sha2_password ; celle-ci sera ajoutée dans la prochaine version. Les serveurs MySQL 8.0 doivent être configurés pour utiliser mysql_native_password. MariaDB est également pris en charge.

## <a name="connect-to-a-mysql-hosting-server"></a>Se connecter à un serveur d’hébergement MySQL

Assurez-vous de détenir les informations d’identification d’un compte disposant de privilèges d’administrateur système. Pour ajouter un serveur d’hébergement, procédez comme suit :

1. Connectez-vous au portail d’administration Azure Stack en tant qu’administrateur de service.
2. Sélectionnez **Tous les services**.
3. Sous la catégorie **RESSOURCES ADMINISTRATIVES**, sélectionnez **Serveurs d’hébergement MySQL** > **+Ajouter**. Cette opération ouvre la boîte de dialogue **Ajouter un serveur d’hébergement MySQL**, illustrée dans la capture d’écran suivante.

   ![Configurer un serveur d’hébergement](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

4. Spécifiez les détails de connexion de votre instance de MySQL Server.

   * Pour **Nom du serveur d’hébergement MySQL**, fournissez le nom de domaine complet (FQDN) ou une adresse IPv4 valide. N’utilisez pas le nom court de la machine virtuelle.
   * Le **Nom d’utilisateur** d’administrateur par défaut des images Bitnami MySQL disponibles dans la Place de marché Azure Stack est *root*. 
   * Si vous ne connaissez pas le **Mot de passe** racine, consultez la [Documentation Bitnami](https://docs.bitnami.com/azure/faq/#how-to-find-application-credentials) pour savoir comment l’obtenir. 
   * Une instance MySQL par défaut n’est pas fournie, donc vous devez spécifier la **taille du serveur d’hébergement en Go**. Entrez une taille qui est proche de la capacité du serveur de base de données.
   * Conservez la valeur par défaut pour **Abonnement**.
   * Pour **Groupe de ressources**, créez un groupe ou utilisez un groupe existant.

   > [!NOTE]
   > Si l’instance MySQL est accessible par le locataire et l’administrateur Azure Resource Manager, vous pouvez la placer sous le contrôle du fournisseur de ressources. Mais, l’instance MySQL **doit** être allouée exclusivement au fournisseur de ressources.

5. Sélectionnez **Références (SKU)** pour ouvrir la boîte de dialogue **Créer une référence (SKU)**.

   ![Créer une référence (SKU) MySQL](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)

   Le **nom** de la référence SKU doit refléter les propriétés de la référence SKU pour que les utilisateurs puissent déployer leurs bases de données sur la référence SKU appropriée.

6. Sélectionnez **OK** pour créer la référence SKU.
   > [!NOTE]
   > Une heure entière peut être nécessaire avant que les références n’apparaissent dans le portail. Vous ne pouvez pas créer de base de données tant que la référence (SKU) n’a pas été déployée et exécutée.

7. Sous **Ajouter un serveur d’hébergement MySQL**, sélectionnez **Créer**.

À mesure que vous ajoutez des serveurs, vous devez les affecter à une référence (SKU) nouvelle ou existante pour différencier les offres de service. Par exemple, vous pouvez avoir une instance d’entreprise MySQL qui fournit des sauvegardes de base de données et automatiques améliorées. Vous pouvez réserver ce serveur hautes performances pour les différents services de votre organisation.

## <a name="security-considerations-for-mysql"></a>Considérations relatives à la sécurité pour MySQL

Les informations suivantes s’appliquent au fournisseur de ressources et aux serveurs d’hébergement MySQL :

* Vérifiez que tous les serveurs d’hébergement sont configurés pour la communication à l’aide de TLS 1.2. Consultez [Configuring MySQL to Use Encrypted Connections](https://dev.mysql.com/doc/refman/5.7/en/using-encrypted-connections.html) (Configuration de MySQL pour utiliser des connexions chiffrées).
* Utilisez [Transparent Data Encryption](https://dev.mysql.com/doc/mysql-secure-deployment-guide/5.7/en/secure-deployment-data-encryption.html).
* Le fournisseur de ressources MySQL ne prend pas en charge l’authentification caching_sha2_password.

## <a name="increase-backend-database-capacity"></a>Augmenter la capacité de base de données principale

Vous pouvez augmenter la capacité de base de données principale en déployant plus de serveurs MySQL dans le portail Azure Stack. Ajoutez ces serveurs à une référence (SKU) nouvelle ou existante. Si vous ajoutez un serveur à une référence (SKU) existante, assurez-vous que les caractéristiques du serveur sont les mêmes que pour les autres serveurs dans la référence (SKU).

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