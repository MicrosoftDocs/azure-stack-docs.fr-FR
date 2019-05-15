---
title: Mettre des applications web et API à la disposition des utilisateurs Azure Stack | Microsoft Docs
description: Didacticiel pour installer le fournisseur de ressources App Service et créer des offres qui donnent la possibilité aux utilisateurs Azure Stack de créer des applications web et API.
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
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: anwestg
ms.custom: mvc
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: 9954e351ecd584925d264d0c8c5e8fb8e2507192
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618654"
---
# <a name="tutorial-make-web-and-api-apps-available-to-your-azure-stack-users"></a>Tutoriel : Mettre des applications web et API à la disposition des utilisateurs Azure Stack

En tant qu’administrateur cloud Azure Stack, vous pouvez créer des offres qui permettent aux utilisateurs (locataires) de créer des fonctions Azure Functions et des applications web et API. En permettant à vos utilisateurs d’accéder à ces applications cloud à la demande, vous pouvez leur faire gagner du temps et économiser des ressources.

Pour effectuer cette configuration, vous allez effectuer les étapes suivantes :

> [!div class="checklist"]
> * Déployer le fournisseur de ressources App Service
> * Créer une offre
> * Tester l’offre

## <a name="deploy-the-app-service-resource-provider"></a>Déployer le fournisseur de ressources App Service

1. [Préparez l’hôte du Kit de développement Azure Stack](azure-stack-app-service-before-you-get-started.md). Cette opération inclut le déploiement du fournisseur de ressources SQL Server, qui est nécessaire pour la création de certaines applications.
2. [Téléchargez le programme d’installation et les scripts d’assistance](azure-stack-app-service-deploy.md).
3. [Exécutez le script d’assistance pour créer des certificats nécessaires](azure-stack-app-service-deploy.md).
4. [Installez le fournisseur de ressources App Service](azure-stack-app-service-deploy.md) (Plusieurs heures sont nécessaires pour effectuer l’installation et afficher tous les rôles de travail.)
5. [Validez l’installation](azure-stack-app-service-deploy.md#validate-the-app-service-on-azure-stack-installation).

## <a name="create-an-offer"></a>Créer une offre

Vous pouvez créer une offre qui, par exemple, permet aux utilisateurs de créer des systèmes de gestion de contenu web DNN. Cette opération nécessite le service SQL Server que vous est déjà activé en installant le fournisseur de ressources SQL Server.

1.  [Définissez un quota](azure-stack-plan-offer-quota-overview.md ) et nommez-le *AppServiceQuota*. Sélectionnez **Microsoft.Web** pour le champ **Namespace**.
2.  [Créer un plan](azure-stack-create-plan.md). Nommez-le *TestAppServicePlan*, puis sélectionnez le service **Microsoft.SQL** et le quota **AppService Quota**.

    > [!NOTE]
    > Pour permettre aux utilisateurs de créer d’autres applications, il est possible que d’autres services soient exigés dans le plan. Par exemple, Azure Functions requiert le service **Microsoft.Storage** dans le plan, tandis que Wordpress requiert **Microsoft.MySQL**.

3.  [Créez une offre](azure-stack-create-offer.md), nommez-la **TestAppServiceOffer**, puis sélectionnez le plan **TestAppServicePlan**.

## <a name="test-the-offer"></a>Tester l’offre

Maintenant que vous avez déployé le fournisseur de ressources App Service et créé une offre, vous pouvez vous connecter en tant qu’utilisateur, vous abonner à l’offre, puis créer une application.

Pour cet exemple, nous allons créer un système de gestion de contenu de plateforme DNN. Vous devez d’abord créer une base de données SQL, puis l’application web DNN.

### <a name="subscribe-to-the-offer"></a>S’abonner à l’offre

1. Connectez-vous au portail Azure Stack (https://portal.local.azurestack.external) en tant que locataire.
2. Sélectionnez **Prendre un abonnement** >, entrez **TestAppServiceSubscription** sous **Nom d’affichage** > **Sélectionner une offre** > **TestAppServiceOffer** > **Créer**.

### <a name="create-a-sql-database"></a>Créer une base de données SQL

1. Sélectionnez **+** > **Données et stockage** > **Base de données SQL**.
2. Conservez les valeurs par défaut, sauf pour les champs suivants :

    - **Nom de la base de données** : DNNdb
    - **Taille maximale (en Mo)**  : 100
    - **Abonnement**: TestAppServiceOffer
    - **Groupe de ressources** : DNN-RG

3. Sélectionnez **Paramètres de connexion**, entrez les informations d’identification pour la base de données, puis sélectionnez **OK**. Vous allez utiliser ces informations d’identification plus tard dans ce tutoriel.
4. Sous **Référence SKU** > sélectionnez le SKU SQL que vous avez créé pour le serveur d’hébergement SQL > puis sélectionnez **OK**.
5. Sélectionnez **Créer**.

### <a name="create-a-dnn-app"></a>Créer une application DNN

1. Sélectionnez **+** > **Afficher tout** > **Aperçu de la plateforme DNN** > **Créer**.
2. Entrez *DNNapp* sous **Nom de l’application**, puis sélectionnez **TestAppServiceOffer** sous **Abonnement**.
3. Sélectionnez **Configurer les paramètres obligatoires** > **Créer** > entrez un nom de**Plan App Service**.
4. Sélectionnez **Niveau tarifaire** > **F1 Gratuit** > **Sélectionner** > **OK**.
5. Sélectionnez **Base de données**, puis entrez les informations d’identification de la base de données SQL créée précédemment.
6. Sélectionnez **Créer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Déployer le fournisseur de ressources App Service
> * Créer une offre
> * Tester l’offre

Passez au didacticiel suivant pour savoir comment :

> [!div class="nextstepaction"]
> [Déployer des applications sur Azure et Azure Stack](../user/azure-stack-solution-pipeline.md)
