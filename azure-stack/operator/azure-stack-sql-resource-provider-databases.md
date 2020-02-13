---
title: Créer des bases de données SQL
titleSuffix: Azure Stack Hub
description: Découvrez comment créer et gérer des bases de données SQL provisionnées avec l’adaptateur du fournisseur de ressources SQL.
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: b0ea11c3245d8a3ddbf9eeaf85481f830c67dc7f
ms.sourcegitcommit: b7b86e875cf04cb0fd9d48a2b830588d3ff99b6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77125584"
---
# <a name="create-sql-databases"></a>Créer des bases de données SQL

Vous pouvez créer et gérer des bases de données libre-service dans le portail utilisateur. Un utilisateur Azure Stack Hub doit disposer d’un abonnement avec une offre incluant le service SQL Database.

1. Connectez-vous au portail utilisateur [Azure Stack Hub](azure-stack-overview.md).

2. Sélectionnez **+ Nouveau** &gt;**Données + stockage** &gt; **Base de données SQL Server** &gt; **Ajouter**.

3. Sous **Créer une base de données**, entrez les informations requises, telles que **Nom de la base de données** et **Taille maximale (en Mo)** .

   >[!NOTE]
   >La base de données doit présenter une taille minimale de 64 Mo, qui peut être étendue après son déploiement.

   Configurez les autres paramètres requis pour votre environnement.

4. Sous **Créer une base de données**, sélectionnez **Référence (SKU)** . Sous **Sélectionner une référence (SKU)** , choisissez la référence SKU de votre base de données.

   ![Créez une base de données dans le portail utilisateur Azure Stack Hub.](./media/azure-stack-sql-rp-deploy/newsqldba.png)

   >[!NOTE]
   >Les serveurs d’hébergement reçoivent une référence (SKU) quand ils sont ajoutés à Azure Stack Hub. Les bases de données sont créées dans le pool de serveurs d’hébergement d’une référence (SKU).

5. Sélectionnez **Connexion**.

6. Sous **Sélectionner une connexion**, choisissez une connexion existante, ou sélectionnez **+ Créer une connexion**.

7. Sous **Nouvelle connexion**, entrez un nom pour **Connexion à la base de données** et un **Mot de passe**.

   >[!NOTE]
   >Ces paramètres correspondent aux informations d’identification d’authentification SQL qui sont créées pour votre accès à cette seule base de données. Le nom d’utilisateur de connexion doit être globalement unique. Vous pouvez réutiliser les paramètres de connexion d’autres bases de données qui utilisent la même référence (SKU).

   ![Créer une connexion à la base de données dans le portail utilisateur Azure Stack Hub](./media/azure-stack-sql-rp-deploy/create-new-login-a.png)

8. Sélectionnez **OK** pour achever le déploiement de la base de données.

Sous **Éléments principaux**, qui s’affiche après le déploiement de la base de données, notez la valeur du champ **Chaîne de connexion**. Vous pouvez utiliser cette chaîne dans n’importe quelle application devant accéder à la base de données SQL Server.

![Récupérer la chaîne de connexion pour la base de données SQL Server](./media/azure-stack-sql-rp-deploy/sql-db-settings-a.png)

## <a name="sql-always-on-databases"></a>Bases de données SQL AlwaysOn

Du fait de leur conception, les bases de données AlwaysOn ne sont pas gérées de la même façon que dans un environnement de serveur autonome. Pour plus d’informations, consultez l’article [Présentation des groupes de disponibilité SQL Server AlwaysOn sur des machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview).

### <a name="verify-sql-always-on-databases"></a>Vérifier les bases de données SQL AlwaysOn

La capture d’écran ci-après vous indique comment vous pouvez utiliser SQL Server Management Studio pour rechercher l’état d’une base de données dans SQL AlwaysOn.

![État d’une base de données AlwaysOn dans SQL Server Management Studio](./media/azure-stack-sql-rp-deploy/verify-always-on.png)

Les bases de données AlwaysOn doivent s’afficher avec l’état **Synchronisé** et comme étant disponibles sur toutes les instances SQL, et elles doivent apparaître dans les **groupes de disponibilité**. Dans la capture d’écran précédente, l’exemple de base de données est newdb1 et présente l’état **newdb1 (synchronisé)** .

### <a name="delete-an-always-on-database"></a>Supprimer une base de données AlwaysOn

Lorsque vous supprimez une base de données SQL AlwaysOn du fournisseur de ressources, SQL supprime la base de données du réplica **principal** et du groupe de disponibilité.

SQL place ensuite la base de données à l’état **Restauration** sur les autres réplicas sans la supprimer, sauf sous l’impulsion d’un déclencheur. Si la base de données n’est pas supprimée, les réplicas secondaires passent à l’état **Sans synchronisation**.

## <a name="next-steps"></a>Étapes suivantes

Découvrir comment [offrir des bases de données SQL à haute disponibilité](azure-stack-tutorial-sql.md)
