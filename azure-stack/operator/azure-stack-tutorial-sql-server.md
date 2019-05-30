---
title: Mettre des bases de données SQL à la disposition de vos utilisateurs Azure Stack | Microsoft Docs
description: Didacticiel pour installer le fournisseur de ressources SQL Server et de créer des offres permettant aux utilisateurs d’Azure Stack de créer des bases de données SQL.
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
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: quying
ms.lastreviewed: 11/05/2018
ms.custom: mvc
ms.openlocfilehash: cc3af256adbcea8e6055b237945dc2286762437e
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66267997"
---
# <a name="tutorial-make-sql-databases-available-to-your-azure-stack-users"></a>Didacticiel : mettre des bases de données SQL à la disposition de vos utilisateurs Azure Stack

En tant qu’administrateur de cloud Azure Stack, vous pouvez créer des offres qui permettent aux utilisateurs de créer des bases de données SQL qu’ils peuvent utiliser avec leurs applications cloud natives, sites web et charges de travail. En fournissant à vos utilisateurs ces bases de données cloud à la demande et personnalisées, vous pouvez leur faire gagner du temps et économiser des ressources. Pour effectuer cette configuration, vous allez effectuer les étapes suivantes :

> [!div class="checklist"]
> * Déployer le fournisseur de ressources SQL Server
> * Créer une offre
> * Tester l’offre

## <a name="deploy-the-sql-server-resource-provider"></a>Déployer le fournisseur de ressources SQL Server

Le processus de déploiement est décrit en détail dans l’[article Utiliser des bases de données SQL sur Azure Stack](azure-stack-sql-resource-provider-deploy.md) et comprend les étapes principales suivantes :

1. [Déployez un fournisseur de ressources SQL](azure-stack-sql-resource-provider-deploy.md).
2. [Vérifiez le déploiement](azure-stack-sql-resource-provider-deploy.md#verify-the-deployment-using-the-azure-stack-portal).
3. Fournissez de la capacité par le biais d’une connexion à un serveur SQL d’hébergement. Pour plus d’informations, consultez [Ajouter des serveurs d’hébergement](azure-stack-sql-resource-provider-hosting-servers.md)

## <a name="create-an-offer"></a>Créer une offre

1.  [Définissez un quota](azure-stack-plan-offer-quota-overview.md ) et nommez-le *SQLServerQuota*. Sélectionnez **Microsoft.SQLAdapter** pour le champ **Namespace**.
2.  [Créer un plan](azure-stack-create-plan.md). Nommez-le *TestSQLServerPlan*, puis sélectionnez le service **Microsoft.SQLAdapter** et le quota **SQLServerQuota**.

    > [!NOTE]
    > Pour permettre aux utilisateurs de créer d’autres applications, il est possible que d’autres services soient exigés dans le plan. Par exemple, Azure Functions exige le service **Microsoft.Storage** dans le plan, tandis que Wordpress exige **Microsoft.MySQLAdapter**.

3.  [Créez une offre](azure-stack-create-offer.md), nommez-la **TestSQLServerOffer**, puis sélectionnez le plan **TestSQLServerPlan**.

## <a name="test-the-offer"></a>Tester l’offre

Maintenant que vous avez déployé le fournisseur de ressources SQL Server et créé une offre, vous pouvez vous connecter en tant qu’utilisateur, vous abonner à l’offre, puis créer une base de données.

### <a name="subscribe-to-the-offer"></a>S’abonner à l’offre

1. Connectez-vous au portail Azure Stack (https://portal.local.azurestack.external) en tant que locataire.
2. Sélectionnez **Prendre un abonnement**, puis entrez **TestSQLServerSubscription** sous **Nom d’affichage**.
3. Sélectionnez **Sélectionner une offre** > **TestSQLServerOffer** > **Créer**.
4. Sélectionnez **Tous les services** > **Abonnements** > **TestSQLServerSubscription** > **Fournisseurs de ressources**.
5. Sélectionnez **Inscrire** en regard du fournisseur **Microsoft.SQLAdapter**.

### <a name="create-a-sql-database"></a>Créer une base de données SQL

1. Sélectionnez **+**  > **Données et stockage** > **Base de données SQL**.
2. Conservez les valeurs par défaut ou utilisez ces exemples pour les champs suivants :
    - **Nom de la base de données** : SQLdb
    - **Taille maximale (en Mo)**  : 100
    - **Abonnement**: TestSQLOffer
    - **Groupe de ressources** : SQL-RG
3. Sélectionnez **Paramètres de connexion**, entrez les informations d’identification pour la base de données, puis sélectionnez **OK**.
4. Sélectionnez **Référence** > sélectionnez le SKU SQL que vous avez créé pour le serveur d’hébergement SQL > puis sélectionnez **OK**.
5. Sélectionnez **Créer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Déployer le fournisseur de ressources SQL Server
> * Créer une offre
> * Tester l’offre

Passez au didacticiel suivant pour savoir comment :

> [!div class="nextstepaction"]
> [Rendre les applications web, mobiles et API disponibles pour vos utilisateurs]( azure-stack-tutorial-app-service.md)