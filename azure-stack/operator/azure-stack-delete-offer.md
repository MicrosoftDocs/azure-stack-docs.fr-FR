---
title: Supprimer des quotas, plans, offres et abonnements
description: Découvrez comment supprimer des quotas, plans, offres et abonnements Azure Stack Hub.
author: bryanla
ms.topic: article
ms.date: 08/13/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 04/25/2019
ms.openlocfilehash: 61d55c3b11dd89894a062e71c51dc5cc3e89e01c
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76882426"
---
# <a name="delete-quotas-plans-offers-and-subscriptions"></a>Supprimer des quotas, plans, offres et abonnements

Cet article explique comment supprimer des quotas, des plans, des offres et des abonnements dont vous n’avez plus besoin. En règle générale, vous pouvez supprimer uniquement ce qui n’est pas en cours d’utilisation. Par exemple, la suppression d’une offre n’est possible que s’il n’existe aucun abonnement appartenant à cette offre.

Les abonnements sont l’exception à ce principe général : vous pouvez supprimer des abonnements qui contiennent des ressources ; les ressources seront supprimées avec l’abonnement.

Ainsi, lorsque vous souhaitez supprimer un quota, vous devez revenir sur les plans et offres qui utilisent ce quota : en commençant par les offres, assurez-vous qu’elles ne contiennent aucun abonnement avant de les supprimer, puis supprimez les plans qui utilisent le quota, et ainsi de suite.

## <a name="delete-a-subscription"></a>Supprimer un abonnement

Pour supprimer un abonnement, sélectionnez **Tous les services**, puis **Abonnements utilisateur**, pour afficher la liste de tous les abonnements sur le système. Si vous travaillez sur une offre, vous pouvez également sélectionner **Abonnements** à partir de là.

Vous pouvez supprimer des abonnements à partir de cette liste, mais vous pouvez aussi utiliser PowerShell et écrire un script qui supprime tous les abonnements pour vous, en vous servant des commandes décrites dans la rubrique [Abonnements - Supprimer la référence](/rest/api/azurestack/subscriptions/delete).

> [!CAUTION]
> La suppression d’un abonnement supprime également les données et les ressources qu’il contient.

## <a name="delete-an-offer"></a>Supprimer une offre

Pour supprimer une offre, dans le portail d’administration, accédez à **Tous les services**, puis à **Offres**. Sélectionnez l’offre que vous souhaitez supprimer, puis sélectionnez **Supprimer**.

![delsub1](media/azure-stack-delete-offer/delsub1.png)

Vous pouvez supprimer une offre uniquement s’il n’existe aucun abonnement l’utilisant. L’existence d’abonnements dépendant de l’offre conditionne l’option **Supprimer** qui est indisponible. Dans ce cas, consultez la section [Supprimer un abonnement](#delete-a-subscription).

## <a name="delete-a-plan"></a>Supprimer un plan

Pour supprimer un plan, dans le portail d’administration, accédez à **Tous les services**, puis à **Plans**. Sélectionnez le plan que vous souhaitez supprimer, puis sélectionnez **Supprimer**.

![delsub2](media/azure-stack-delete-offer/delsub2.png)

Vous pouvez supprimer un plan uniquement s’il n’existe aucune offre ni abonnement l’utilisant. Si des offres sont tributaires du plan, supprimez le plan, autorisez son échec et vous recevrez un message d’erreur. Vous pouvez sélectionner **Offres parentes** pour afficher la liste des offres qui utilisent le plan. Pour plus d’informations sur la suppression des offres, consultez [Supprimer une offre](#delete-an-offer).

Des plans peuvent avoir été ajoutés directement à un abonnement comme complément de plan, même s’ils ne font pas partie de l’offre. Dans ce cas, ils doivent être supprimés des abonnements qui les utilisent avant de pouvoir supprimer le plan.

Par ailleurs, il est impossible de supprimer un plan depuis un abonnement s’il est l’unique source d’une ressource donnée pour cet abonnement. Par exemple, si le Plan A a été ajouté à l’abonnement 1, et qu’il est le seul plan fournissant un quota réseau à l’abonnement, il ne peut pas être retiré de l’abonnement. Il n’est donc pas possible de le supprimer.

## <a name="edit-and-delete-a-quota"></a>Modifier et supprimer un quota

Vous pouvez afficher et modifier les quotas existants par le biais du portail d’administration : sélectionnez **Gestion des régions**, puis le fournisseur de ressources approprié et cliquez sur **Quotas**. Vous pouvez également supprimer des quotas pour certains fournisseurs de ressources.

![delsub3](media/azure-stack-delete-offer/delsub3.png)

Sinon, vous pouvez supprimer certains quotas à l’aide de ces API REST :

- [Calcul](/rest/api/azurestack/quotas%20(compute)/delete)
- [Réseau](/rest/api/azurestack/quotas%20(network)/delete)
- [Stockage](/rest/api/azurestack/storagequotas/delete)

> [!NOTE]
> Vous ne pouvez pas supprimer un quota s’il existe des plans actuels qui l’utilisent. Vous devez d’abord supprimer le plan qui fait référence au quota.

## <a name="next-steps"></a>Étapes suivantes

- [Création d’abonnements](azure-stack-subscribe-plan-provision-vm.md)
- [Approvisionner une machine virtuelle](../user/azure-stack-create-vm-template.md)