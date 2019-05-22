---
title: S’abonner à une offre dans Azure Stack | Microsoft Docs
description: Créer des abonnements pour des offres dans Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 7f3f8683-ef09-4838-92ed-41f2fddbbbed
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: efemmano
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: b0dba609acd7b12cdb95c1ec3e40ad906674244d
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2019
ms.locfileid: "64984774"
---
# <a name="create-subscriptions-to-offers-in-azure-stack"></a>Créer des abonnements pour des offres dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Après avoir [créé une offre](azure-stack-create-offer.md), les utilisateurs ont besoin d’un abonnement à cette offre pour pouvoir l’utiliser. Les utilisateurs peuvent s’abonner à une offre de deux façons :

- En tant qu’opérateur cloud, vous pouvez créer un abonnement pour un utilisateur à partir du portail d’administration. Les abonnements créés peuvent être destinés à des offres publiques et privées.
- En tant qu’un utilisateur locataire, vous pouvez vous abonner à une offre publique lorsque vous utilisez le portail utilisateur.  

## <a name="create-a-subscription-as-a-cloud-operator"></a>Créer un abonnement en tant qu’opérateur cloud

Les opérateurs cloud peuvent utiliser le portail d’administration pour créer un abonnement à une offre pour un utilisateur. Vous pouvez créer des abonnements pour les membres de votre propre locataire d’annuaire. Lorsque la [mutualisation](azure-stack-enable-multitenancy.md) est activée, vous pouvez également créer des abonnements pour les utilisateurs dans des locataires d’annuaires supplémentaires.

Si vous ne voulez pas que vos locataires puissent créer leurs propres abonnements, rendez vos offres privées et créez ensuite des abonnements pour vos locataires. Cette approche est courante lors de l’intégration d’Azure Stack avec les systèmes externes de catalogue de service ou de facturation.

Après avoir créé un abonnement pour un utilisateur, ce dernier peut se connecter au portail utilisateur et constater qu’il est abonné à l’offre.  

### <a name="to-create-a-subscription-for-a-user"></a>Créer un abonnement pour un utilisateur

1. Dans le portail d’administration, accédez à **Abonnements utilisateur.**
2. Sélectionnez **Ajouter**. Sous **Nouvel abonnement utilisateur**, entrez les informations suivantes :  

   - **Nom d’affichage** : nom convivial pour identifier l’abonnement qui s’affiche en tant que *Nom d’abonnement utilisateur*.
   - **Utilisateur** : spécifiez un utilisateur depuis un locataire d’annuaire disponible pour cet abonnement. Le nom d’utilisateur apparaît en tant que *Propriétaire*.  Le format du nom d’utilisateur dépend de votre solution d’identité. Par exemple : 

     - **Azure AD :** `<user1>@<contoso.onmicrosoft.com>`

     - **AD FS :** `<user1>@<azurestack.local>`

   - **Locataire d’annuaire** : sélectionnez le locataire d’annuaire auquel appartient le compte d’utilisateur. Si vous n’avez pas activé la mutualisation, seul votre locataire d’annuaire local est disponible.

3. Sélectionnez **Offre**. Sous **Offres**, choisissez une **offre** pour cet abonnement. Étant donné que vous créez l’abonnement pour un utilisateur, sélectionnez **Privé** pour l’état de l’accessibilité.

4. Sélectionnez **Créer** pour créer l’abonnement. Le nouvel abonnement apparaît sous **Abonnement utilisateur**. Lorsque l’utilisateur se connecte au portail, il voit les détails de l’abonnement.

### <a name="to-make-an-add-on-plan-available"></a>Pour rendre un plan additionnel disponible

Un opérateur cloud peut ajouter à tout moment un plan à un abonnement créé précédemment :

1. Dans le portail d’administration, sélectionnez **Tous les services**, puis dans la catégorie **RESSOURCES ADMINISTRATIVES**, sélectionnez  **Abonnements utilisateur**. Sélectionnez l’abonnement que vous souhaitez modifier.

2. Sélectionnez **Modules complémentaires**, puis **+Ajouter**.  

3. Sous **Ajouter un plan**, sélectionnez le plan à ajouter en tant que module complémentaire.

## <a name="create-a-subscription-as-a-user"></a>Créer un abonnement en tant qu’utilisateur

En tant qu’utilisateur, vous pouvez vous connecter au portail utilisateur pour trouver et vous abonner aux offres publiques et aux plans additionnels pour votre locataire d’annuaire (organisation).

>[!NOTE]
>Si votre environnement Azure Stack prend en charge la [mutualisation](azure-stack-enable-multitenancy.md), vous pouvez également vous abonner à des offres à partir d’un locataire d’annuaire distant.

### <a name="to-subscribe-to-an-offer"></a>S’abonner à une offre

1. [Connectez-vous](../asdk/asdk-connect.md) au [portail utilisateur Azure Stack](https://portal.local.azurestack.external) et sélectionnez **Prendre un abonnement**.

   ![Prendre un abonnement](media/azure-stack-subscribe-plan-provision-vm/image01.png)
  
2. Sous **Prendre un abonnement**, entrez le nom convivial de l’abonnement dans **Nom d’affichage**. Sélectionnez **Offre** et, sous **Choisir une offre**, sélectionnez une offre. Sélectionnez **Créer** pour créer l’abonnement.

   ![Créer une offre](media/azure-stack-subscribe-plan-provision-vm/image02.png)
  
3. Une fois que vous êtes abonné à une offre, actualisez le portail pour voir les services qui font partie du nouvel abonnement.

4. Pour voir l’abonnement que vous avez créé, sélectionnez **Tous les services**, puis **Abonnements** sous la catégorie **GÉNÉRAL**. Sélectionnez l’abonnement pour en afficher les détails.  

### <a name="to-subscribe-to-an-add-on-plan"></a>S’abonner à un plan additionnel

Si une offre possède un plan additionnel, vous pouvez ajouter ce plan à votre abonnement à tout moment.  

1. Dans le portail utilisateur, sélectionnez **Tous les services**. Ensuite, dans la catégorie **GÉNÉRAL**, sélectionnez **Abonnements**, puis sélectionnez l’abonnement à modifier. Si des plans additionnels sont disponibles, l’option **+Ajouter un plan** est active et il existe une vignette pour **Plans additionnels**. 

   Si l’option **+Ajouter un plan** n’est pas active, cela signifie qu’il n’existe aucun plan additionnel pour l’offre associée à cet abonnement.

1. Sélectionnez **+Ajouter un plan** ou la vignette **Plans additionnels**. Sous **Plans additionnels**, sélectionnez le plan que vous souhaitez ajouter.

## <a name="next-steps"></a>Étapes suivantes

- [Approvisionner une machine virtuelle](../user/azure-stack-create-vm-template.md)
