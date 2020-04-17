---
title: Présentation des services, plans, offres et abonnements Azure Stack Hub
description: Présentation des services, plans, offres et abonnements Azure Stack Hub.
author: BryanLa
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 10/01/2019
ms.openlocfilehash: a5776189c1792844b6d9bf2ec229ec02936f82c1
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "80423881"
---
# <a name="azure-stack-hub-services-plans-offers-subscriptions-overview"></a>Présentation des services, plans, offres et abonnements Azure Stack Hub

[Microsoft Azure Stack Hub](azure-stack-overview.md) est une plateforme cloud hybride qui vous permet de fournir des services à partir de votre centre de données. Les services incluent notamment les machines virtuelles, les bases de données SQL Server, SharePoint, Exchange et même des [éléments de Place de marché Azure](azure-stack-marketplace-azure-items.md). En tant que fournisseur de services, vous pouvez proposer des services à vos locataires. Au sein d’une entreprise ou d’une agence gouvernementale, vous pouvez proposer des services locaux à vos employés.

## <a name="overview"></a>Vue d’ensemble

En tant qu’opérateur Azure Stack Hub, vous configurez et vous fournissez des services en utilisant des offres, des plans et des abonnements. Les offres contiennent un ou plusieurs plans, et chaque plan inclut un ou plusieurs services, chacun configuré avec des quotas. En créant des plans et en les associant en différentes offres, les utilisateurs peuvent s’abonner à vos offres et déployer des ressources. Cette structure vous permet de gérer :

- les services et ressources auxquels vos utilisateurs ont accès ;
- la quantité de ressources que les utilisateurs peuvent consommer ;
- les régions qui ont accès aux ressources.

Pour fournir un service, suivez les grandes étapes suivantes :

1. Planifiez votre offre de services en utilisant :

   - Des services fondamentaux, comme le calcul, le stockage, le réseau ou Key Vault.
   - Services à valeur ajoutée, par exemple Event Hubs, App Service, SQL Server ou MySQL Server.

2. Créez un plan qui est constitué d’un ou plusieurs services. Ce faisant, vous sélectionnez ou créez des quotas qui définissent les limites de ressources de chacun des services du plan.
3. Créez une offre qui comporte un ou plusieurs plans. L’offre peut inclure des plans de base et des plans d’extension facultatifs.

Une fois l’offre créée, vos utilisateurs peuvent s’y abonner pour accéder aux services et déployer des ressources. Ils peuvent s’abonner à autant d’offres qu’ils le souhaitent. La figure suivante montre l’exemple simple d’un utilisateur qui s’est abonné à deux offres. Chaque offre comprend un ou deux plans, et chaque plan leur donne accès à des services spécifiques.

![Abonnement client avec offres et plans](media/azure-stack-key-features/image4.png)

## <a name="services"></a>Services

Vous pouvez proposer des services [Infrastructure as a service](https://azure.microsoft.com/overview/what-is-iaas/) (IaaS) qui autorisent vos utilisateurs à créer une infrastructure informatique à la demande, laquelle est configurée et gérée à partir du portail d’utilisateur Azure Stack Hub.

Vous pouvez également déployer des services [Platform as a Service](https://azure.microsoft.com/overview/what-is-paas/) (PaaS) pour Azure Stack Hub provenant de Microsoft et de fournisseurs tiers. Vous pouvez déployer entre autres les services PaaS suivants :

- [Hubs d'événements](event-hubs-rp-overview.md)
- [App Service](azure-stack-app-service-overview.md)
- [SQL Server](azure-stack-sql-resource-provider-deploy.md)
- [MySQL Server](azure-stack-mysql-resource-provider-deploy.md)

Vous pouvez aussi combiner des services pour intégrer et créer des solutions complexes pour différents utilisateurs.

### <a name="quotas"></a>Quotas

Pour mieux gérer votre capacité cloud, vous pouvez utiliser des *quotas* préconfigurés ou créer un quota pour chacun des services d’un plan. Les quotas définissent les limites de ressources supérieures qu’un abonnement utilisateur peut approvisionner ou consommer. Par exemple, un quota peut autoriser un utilisateur de créer jusqu’à cinq machines virtuelles.

> [!IMPORTANT]
> Il peut s’écouler jusqu’à deux heures avant que les nouveaux quotas ne soient disponibles sur le portail de l’utilisateur ou qu’un quota modifié ne soit appliqué.

Vous pouvez définir les quotas par région. Par exemple, un plan fournissant des services de calcul pour la région A peut avoir un quota de deux machines virtuelles.

>[!NOTE]
>Dans le Kit de développement Azure Stack, une seule région (nommée *local*) est disponible.

Apprenez-en plus sur les [types de quotas dans Azure Stack Hub](azure-stack-quota-types.md).

## <a name="plans"></a>Plans

Les plans regroupent un ou plusieurs services. En tant qu’opérateur Azure Stack Hub, vous [créez des plans](azure-stack-create-plan.md) à proposer à vos utilisateurs. En retour, ceux-ci s’abonnent à vos offres pour utiliser les plans et les services qu’elles comprennent. Lors de la création des plans, veillez à définir vos quotas et vos plans de base et à envisager d’inclure des plans d’extension facultatifs.

### <a name="base-plan"></a>Plan de base

Lors de la création d’une offre, l’administrateur de services fédérés peut inclure un plan de base. Ces plans de base sont inclus par défaut lorsqu’un utilisateur s’abonne à cette offre : il a alors accès à tous les fournisseurs de ressources spécifiés dans ces plans (avec les quotas correspondants).

### <a name="add-on-plans"></a>Plans d’extension

Les plans d’extension sont des plans facultatifs que vous ajoutez à une offre. Les plans additionnels ne sont pas inclus par défaut dans l’abonnement. Les plans d’extension sont des plans supplémentaires (avec quotas), disponibles dans une offre, qu’un abonné peut ajouter à ses abonnements. Par exemple, vous pouvez proposer un plan de base avec des ressources limitées pour un essai, et un plan d’extension avec des ressources plus importantes pour les clients qui décident d’adopter le service.

## <a name="offers"></a>Offres

Les offres sont des groupes d’un ou plusieurs plans créés pour que les utilisateurs puissent s’y abonner. Par exemple : Une offre Alpha peut contenir un plan A fournissant un ensemble de services de calcul et un plan B fournissant un ensemble de services de stockage et réseau.

Lorsque vous [créez une offre](azure-stack-create-offer.md), vous devez inclure au moins un plan de base, mais vous avez également la possibilité de créer des plans d’extension que les utilisateurs pourront ajouter à leur abonnement.

Quand vous planifiez vos offres, gardez à l’esprit les points suivants :

**Offres d'évaluation** : Vous utilisez des offres d’évaluation pour attirer de nouveaux utilisateurs, qui peuvent ensuite effectuer une mise à niveau vers des services supplémentaires. Pour créer une offre d’évaluation, créez un petit [plan de base](service-plan-offer-subscription-overview.md#base-plan) avec un plan complémentaire facultatif plus important. Vous pouvez aussi créer une offre d’essai composée d’un petit plan de base et d’une offre distincte avec un plan « paiement à l’utilisation » plus étendu.

**Planification de la capacité** : peut-être craignez-vous que des utilisateurs s’accaparent de grandes quantités de ressources et encombrent le système pour tous les autres utilisateurs. Pour améliorer les performances, vous pouvez [configurer vos plans avec des quotas](service-plan-offer-subscription-overview.md#plans) afin de limiter l’utilisation.

**Fournisseurs délégués** : vous pouvez donner à d'autres personnes la possibilité de créer des offres dans votre environnement. Par exemple, si vous êtes fournisseur de services, vous pouvez [déléguer](azure-stack-delegated-provider.md) cette capacité à vos revendeurs. Si vous êtes une entreprise, vous pouvez déléguer à d’autres divisions/filiales.

## <a name="subscriptions"></a>Abonnements

Les abonnements permettent aux utilisateurs d’accéder à vos offres. Si vous êtes un opérateur Azure Stack Hub chez un fournisseur de services, vos utilisateurs (locataires) achètent vos services en s’abonnant à vos offres. Si vous êtes un opérateur Azure Stack Hub au sein d’une organisation, vos utilisateurs (employés) peuvent s’abonner gratuitement aux services que vous proposez.

Les utilisateurs créent des abonnements et obtiennent l’accès aux abonnements existants en se connectant à Azure Stack Hub. Chaque abonnement représente une association avec une seule offre. L’offre (ainsi que ses plans et quotas) affectée à un abonnement ne peut pas être partagée avec d’autres abonnements. Chaque ressource créée par un utilisateur est associée à un seul abonnement.

### <a name="default-provider-subscription"></a>Abonnement au fournisseur par défaut

L’abonnement au fournisseur par défaut est automatiquement créé lors du déploiement du Kit de développement Azure Stack. Il permet de gérer Azure Stack Hub, de déployer des fournisseurs de ressources supplémentaires et de créer des plans et des offres pour les utilisateurs. Pour des raisons de sécurité et de gestion des licences, il ne doit pas être utilisé pour exécuter des applications et des charges de travail de clients. Le quota de l’abonnement du fournisseur par défaut ne peut pas être modifié.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la création de plans, d’offres et d’abonnements, commencez par [Créer un plan ](azure-stack-create-plan.md).
