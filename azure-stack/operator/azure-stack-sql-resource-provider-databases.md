---
title: Utilisation des bases de données fournies par le fournisseur de ressources de l’adaptateur SQL sur Azure Stack | Microsoft Docs
description: Procédures de création et de gestion des bases de données SQL approvisionnées à l’aide du fournisseur de ressources de l’adaptateur SQL
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: quying
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: ad02e37faf9d4f22cb8fe8d58a203c46ae758b52
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "64309215"
---
# <a name="create-sql-databases"></a>Créer des bases de données SQL

Vous pouvez créer et gérer des bases de données libre-service dans le portail utilisateur. Un utilisateur Azure Stack doit disposer d'un abonnement avec une offre incluant le service SQL Database.

1. Connectez-vous au portail utilisateur [Azure Stack](azure-stack-overview.md).

2. Sélectionnez **+ Nouveau** &gt;**Données + stockage** &gt; **Base de données SQL Server** &gt; **Ajouter**.

3. Sous **Créer une base de données**, entrez les informations requises, telles que **Nom de la base de données** et **Taille maximale (en Mo)**.

   >[!NOTE]
   >La base de données doit présenter une taille minimale de 64 Mo et pourra être étendue après son déploiement.

   Configurez les autres paramètres requis pour votre environnement.

4. Sous **Créer une base de données**, sélectionnez **Référence (SKU)**. Sous **Sélectionner une référence (SKU)**, choisissez la référence SKU de votre base de données.

   ![Créer une base de données](./media/azure-stack-sql-rp-deploy/newsqldb.png)

   >[!NOTE]
   >Les serveurs d’hébergement reçoivent une référence (SKU) quand ils sont ajoutés à Azure Stack. Les bases de données sont créées dans le pool de serveurs d’hébergement d’une référence (SKU).

5. Sélectionnez **Connexion**.
6. Sous **Sélectionner une connexion**, choisissez une connexion existante, ou sélectionnez **+ Créer une connexion**.
7. Sous **Nouvelle connexion**, entrez un nom pour **Connexion à la base de données** et un **Mot de passe**.

   >[!NOTE]
   >Ces paramètres correspondent aux informations d’identification d’authentification SQL qui sont créées pour votre accès à cette seule base de données. Le nom d’utilisateur de connexion doit être globalement unique. Vous pouvez réutiliser les paramètres de connexion d’autres bases de données qui utilisent la même référence (SKU).

   ![Créer une connexion de base de données](./media/azure-stack-sql-rp-deploy/create-new-login.png)

8. Sélectionnez **OK** pour achever le déploiement de la base de données.

Sous **Éléments principaux**, qui s’affiche après le déploiement de la base de données, notez la valeur du champ **Chaîne de connexion**. Vous pouvez utiliser cette chaîne dans n’importe quelle application devant accéder à la base de données SQL Server.

![Récupérer la chaîne de connexion](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="sql-always-on-databases"></a>Bases de données SQL AlwaysOn

Du fait de leur conception, les bases de données AlwaysOn ne sont pas gérées de la même façon que dans un environnement de serveur autonome. Pour plus d’informations, consultez l’article [Présentation des groupes de disponibilité SQL Server AlwaysOn sur des machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview).

### <a name="verify-sql-always-on-databases"></a>Vérifier les bases de données SQL AlwaysOn

La capture d’écran ci-après vous indique comment vous pouvez utiliser SQL Server Management Studio pour rechercher l’état d’une base de données dans SQL AlwaysOn.

![État d’une base de données SQL](./media/azure-stack-sql-rp-deploy/verifyalwayson.png)

Les bases de données AlwaysOn doivent s’afficher avec l’état Synchronisé et comme étant disponibles sur toutes les instances SQL, et elles doivent apparaître dans les groupes de disponibilité. Dans la capture d’écran précédente, l’exemple de base de données considéré est newdb1 et présente l’état **newdb1 (Synchronized)**.

### <a name="delete-an-alwayson-database"></a>Supprimer une base de données AlwaysOn

Lorsque vous supprimez une base de données SQL AlwaysOn du fournisseur de ressources, SQL supprime cette base de données du réplica principal et du groupe de disponibilité.

Puis SQL place la base de données à l’état Restauration sur les autres réplicas sans la supprimer, sauf sous l’impulsion d’un déclencheur. Si la base de données n’est pas supprimée, les réplicas secondaires passent à l’état Sans synchronisation.

## <a name="next-steps"></a>Étapes suivantes

[Tenir à jour le fournisseur de ressources SQL Server](azure-stack-sql-resource-provider-maintain.md)
