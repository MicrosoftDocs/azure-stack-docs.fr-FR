---
title: Vue d’ensemble d’Azure App Service sur Azure Stack
description: Vue d'ensemble d'Azure App Service et d'Azure Functions sur Azure Stack Hub.
author: BryanLa
ms.topic: article
ms.date: 01/13/2020
ms.author: BryanLa
ms.reviewer: anwestg
ms.lastreviewed: 01/13/2019
ms.openlocfilehash: ac9c6707e0b6e179fdae3dac0b4ec94bad11e6d7
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77688880"
---
# <a name="azure-app-service-and-azure-functions-on-azure-stack-hub-overview"></a>Vue d'ensemble d'Azure App Service et d'Azure Functions sur Azure Stack Hub

Azure App Service sur Azure Stack Hub est une offre PaaS (platform-as-a-service) de Microsoft Azure disponible sur Azure Stack Hub. Le service permet à vos clients internes ou externes de créer des applications web, API et Azure Functions pour la plateforme ou l’appareil de leur choix. Ils peuvent intégrer des applications locales à vos applications et automatiser leurs processus d’entreprise. Les opérateurs de cloud Azure Stack Hub peuvent exécuter les applications clientes sur des machines virtuelles complètement managées, avec les ressources de machines virtuelles partagées ou les machines virtuelles dédiées de leur choix.

Azure App Service vous permet d’automatiser des processus métier et des API cloud d’hôte. En tant que service intégré unique, Azure App Service vous permet de combiner différents composants (comme des sites web, des API REST et des processus métier) en une seule solution.

## <a name="why-offer-azure-app-service-on-azure-stack-hub"></a>Pourquoi proposer Azure App Service sur Azure Stack Hub ?

Voici quelques fonctionnalités et capacités clés d'Azure App Service :

- **Plusieurs langages et frameworks** : Azure App Service offre une excellente prise en charge d'ASP.NET, Node.js, Java, PHP et Python. Vous pouvez également exécuter Windows PowerShell et d’autres scripts ou exécutables sur les machines virtuelles App Service.
- **Optimisation DevOps** : configurez l’intégration et le déploiement continus avec GitHub, local Git, ou BitBucket. Vous pouvez promouvoir des mises à jour dans des environnements de test et de préproduction, et gérer vos applications dans App Service à l’aide d’Azure PowerShell ou de l’interface de ligne de commande (CLI) multiplateforme.
- **Intégration à Visual Studio** : Les outils dédiés de Visual Studio simplifient la création et le déploiement des applications.

## <a name="app-types-in-app-service"></a>Types d’applications dans App Service

App Service offre plusieurs types d’application, chacun d’eux étant destiné à héberger une charge de travail spécifique :

- [Web Apps](/azure/app-service/overview) pour l’hébergement de sites et d’applications web.
- [API Apps](/azure/app-service/overview) pour l’hébergement d’API REST.
- Azure Functions pour l’hébergement de charges de travail sans serveur, pilotées par les événements.

Le mot *application* fait référence aux ressources d’hébergement dédiées à l’exécution d’une charge de travail. Par exemple, vous avez certainement coutume de penser qu’une *application web* se compose de ressources de calcul et de code d’application qui, ensemble, offrent des fonctionnalités à un navigateur. Dans Azure App Service, une application web est la ressource de calcul qu'Azure Stack Hub fournit pour héberger le code de votre application.

Votre application peut être composée d’applications App Service de différents types. Par exemple, si votre application se compose d’un front-end web et d’un back-end d’API REST, vous pouvez :

- Déployer les deux (front-end et API) sur une même application web.
- Déployer votre code frontal dans une application web et votre code principal dans une application API.

   [![Présentation d'App Service avec données de surveillance](media/azure-stack-app-service-overview/image01.png "Présentation d’App Service avec données de surveillance")](media/azure-stack-app-service-overview/image01.png#lightbox)

## <a name="what-is-an-app-service-plan"></a>Qu’est-ce qu’un plan App Service ?

Le fournisseur de ressources App Service utilise le même code que celui utilisé par Azure App Service, et donc partage certains concepts communs. Dans App Service, le conteneur des tarifs des applications est appelé *plan App Service*. Il représente l’ensemble des machines virtuelles dédiées qui sont utilisées pour contenir vos applications. Vous pouvez avoir plusieurs plans App Service dans un abonnement donné.

Dans Azure, il existe des Workers partagés et dédiés. Un Worker partagé prend en charge l’hébergement d’applications multilocataires à densité élevée, et il n’existe qu’un seul ensemble de Workers partagés. Les serveurs dédiés sont utilisés par un seul locataire et se présentent dans trois tailles : petit, moyen et grand. Les besoins des clients locaux ne peuvent pas toujours être décrits à l’aide de ces termes. Dans App Service sur Azure Stack Hub, les administrateurs du fournisseur de ressources définissent les niveaux Worker qu'ils souhaitent rendre disponibles. Vous pouvez définir plusieurs jeux de Workers partagés ou des jeux différents de Workers dédiés selon leurs besoins d’hébergements uniques. À l’aide de ces définitions de niveau Worker, ils peuvent ensuite définir leurs propres références de tarification.

## <a name="portal-features"></a>Fonctionnalités du portail


Azure App Service sur Azure Stack Hub utilise la même interface utilisateur qu'Azure App Service. Il en est de même avec le back-end. Cependant, certaines fonctionnalités sont désactivées dans Azure Stack Hub. Les exigences ou les services spécifiques à Azure dont ces fonctionnalités ont besoin ne sont pas encore disponibles dans Azure Stack Hub.

## <a name="next-steps"></a>Étapes suivantes

- [Conditions préalables au déploiement d'App Service sur Azure Stack Hub](azure-stack-app-service-before-you-get-started.md)
- [Installer le fournisseur de ressources Azure App Service](azure-stack-app-service-deploy.md)

Vous pouvez également tester d’autres [services PaaS](service-plan-offer-subscription-overview.md), comme le [fournisseur de ressources SQL Server](azure-stack-sql-resource-provider-deploy.md) et le [fournisseur de ressources MySQL](azure-stack-mysql-resource-provider-deploy.md).
