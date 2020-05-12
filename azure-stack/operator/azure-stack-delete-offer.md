---
title: Supprimer des quotas, plans, offres et abonnements
description: Découvrez comment supprimer des quotas, plans, offres et abonnements Azure Stack Hub.
author: bryanla
ms.topic: article
ms.date: 08/13/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 04/25/2019
ms.openlocfilehash: 3c8c8b424d6ce555b19f4a77dbb8275b731f488e
ms.sourcegitcommit: 70c344b3c9c63f8c12867b2cdfdd1794fcc518dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82836128"
---
# <a name="delete-quotas-plans-offers-and-subscriptions"></a>Supprimer des quotas, plans, offres et abonnements

Cet article explique comment supprimer des quotas, des plans, des offres et des abonnements dont vous n’avez plus besoin. En règle générale, vous ne pouvez supprimer que ce qui n'est pas utilisé. Par exemple, la suppression d’une offre n’est possible que s’il n’existe aucun abonnement appartenant à cette offre.

Les abonnements sont l'exception à ce principe général : vous pouvez supprimer des abonnements qui contiennent des ressources ; les ressources seront supprimées avec l'abonnement.

Si vous souhaitez supprimer un quota, vous devez examiner les plans et offres qui utilisent ce quota. Commencez par les offres : assurez-vous qu'elles ne disposent d'aucun abonnement, supprimez chaque offre, puis supprimez les plans qui utilisent le quota, etc.

## <a name="delete-a-subscription"></a>Supprimer un abonnement

Pour supprimer un abonnement, sélectionnez **Tous les services**, puis **Abonnements utilisateur**, pour afficher la liste de tous les abonnements sur le système. Si vous travaillez sur une offre, vous pouvez également sélectionner **Abonnements** à partir de là.

Vous pouvez supprimer des abonnements à partir de cette liste, ou utiliser PowerShell pour écrire un script qui supprimera tous les abonnements pour vous. Ces commandes sont décrites dans la rubrique [Abonnements - Supprimer la référence](/rest/api/azurestack/subscriptions/delete).

> [!CAUTION]
> La suppression d’un abonnement supprime également les données et les ressources qu’il contient.

## <a name="delete-an-offer"></a>Supprimer une offre

Pour supprimer une offre, dans le portail d’administration, accédez à **Tous les services**, puis à **Offres**. Sélectionnez l’offre que vous souhaitez supprimer, puis sélectionnez **Supprimer**.

![Supprimer des abonnements sur le portail d'administration Azure Stack Hub](media/azure-stack-delete-offer/delsub1.png)

Vous pouvez supprimer une offre uniquement s’il n’existe aucun abonnement l’utilisant. S'il existe des abonnements basés sur l'offre, l'option **Supprimer** n'est pas disponible. Dans ce cas, consultez la section [Supprimer un abonnement](#delete-a-subscription).

## <a name="delete-a-plan"></a>Supprimer un plan

Pour supprimer un plan, dans le portail d’administration, accédez à **Tous les services**, puis à **Plans**. Sélectionnez le plan que vous souhaitez supprimer, puis sélectionnez **Supprimer**.

![Supprimer des plans sur le portail d'administration Azure Stack Hub](media/azure-stack-delete-offer/delsub2.png)

Vous pouvez supprimer un plan uniquement s’il n’existe aucune offre ni abonnement l’utilisant. Si des offres sont tributaires du plan, supprimez le plan, autorisez son échec et vous recevrez un message d'erreur. Vous pouvez sélectionner **Offres parentes** pour afficher la liste des offres qui utilisent le plan. Pour plus d’informations sur la suppression des offres, consultez [Supprimer une offre](#delete-an-offer).

Des plans peuvent avoir été ajoutés directement à un abonnement en tant que plans complémentaires, même s'ils ne font pas partie de l'offre. Dans ce cas, ils doivent être supprimés des abonnements qui les utilisent avant de pouvoir supprimer le plan.

Par ailleurs, un plan ne peut pas être supprimé d'un abonnement s'il est l'unique source d'une ressource donnée pour cet abonnement. Par exemple, si le Plan A a été ajouté à l'Abonnement 1, et qu'il est le seul plan à fournir un quota réseau à l'abonnement, il ne peut pas être retiré de l'abonnement. Il n'est donc pas possible de le supprimer.

## <a name="edit-and-delete-a-quota"></a>Modifier et supprimer un quota

Vous pouvez afficher et modifier les quotas existants par le biais du portail d'administration : sélectionnez **Gestion des régions**, puis le fournisseur de ressources approprié et sélectionnez **Quotas**. Vous pouvez également supprimer des quotas pour certains fournisseurs de ressources.

![Supprimer des quotas sur le portail d'administration Azure Stack Hub](media/azure-stack-delete-offer/delsub3.png)

Vous pouvez également supprimer certains quotas à l'aide de ces API REST :

- [Calcul](/rest/api/azurestack/quotas%20(compute)/delete)
- [Réseau](/rest/api/azurestack/quotas%20(network)/delete)

> [!NOTE]
> Vous ne pouvez pas supprimer un quota s'il est utilisé par des plans actuels. Vous devez d’abord supprimer le plan qui fait référence au quota.

## <a name="next-steps"></a>Étapes suivantes

- [Création d’abonnements](azure-stack-subscribe-plan-provision-vm.md)
- [Approvisionner une machine virtuelle](../user/azure-stack-create-vm-template.md)
