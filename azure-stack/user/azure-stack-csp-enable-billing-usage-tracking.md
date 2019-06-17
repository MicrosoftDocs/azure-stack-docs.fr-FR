---
title: Laisser votre fournisseur de services cloud gérer votre abonnement Azure Stack à votre place | Microsoft Docs
description: Découvrez comment laisser votre fournisseur de services cloud (CSP) gérer votre abonnement Azure Stack à votre place.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/20/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/20/2019
ms.openlocfilehash: 0dc162dd1d4021323f1bf80ad4f89fc721e575a9
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2019
ms.locfileid: "66691979"
---
# <a name="let-your-cloud-service-provider-manage-your-azure-stack-subscription"></a>Laisser votre fournisseur de services cloud gérer votre abonnement Azure Stack à votre place

*S’applique à : systèmes intégrés Azure Stack*

Si vous utilisez Azure Stack avec un CSP (fournisseur de services cloud), vous pouvez choisir de gérer votre propre abonnement pour accéder aux ressources dans Azure et Azure Stack. Vous pouvez également laisser le fournisseur gérer votre abonnement à votre place. Cet article vous montre comment procéder.

* Autoriser votre fournisseur de services à accéder à votre abonnement.
* Vérifier que le fournisseur de services peut gérer votre service.

> [!NOTE]
> Si vous ignorez les étapes suivantes alors que le fournisseur de services cloud ne gère pas déjà votre compte, celui-ci ne peut pas gérer votre abonnement Azure Stack à votre place.

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>Gérer votre abonnement avec un fournisseur de services cloud

Ajoutez le fournisseur de services cloud comme **utilisateur** dans votre abonnement.

1. Ajoutez votre fournisseur de services cloud en tant qu’utilisateur invité avec le rôle d’**utilisateur** dans votre répertoire de locataire. Pour savoir comment ajouter un utilisateur, consultez [Ajouter de nouveaux utilisateurs dans Azure Active Directory](/azure/active-directory/add-users-azure-active-directory).

2. Le fournisseur de services cloud crée l’abonnement Azure Stack local pour vous. Vous êtes prêt à utiliser Azure Stack.
3. Votre fournisseur de services cloud doit créer une ressource dans votre abonnement pour vérifier s’il peut également gérer vos ressources. Par exemple, il peut [Créer une machine virtuelle Windows avec le portail Azure Stack](azure-stack-quick-windows-portal.md).

## <a name="let-the-cloud-service-provider-manage-your-subscription-using-rbac-rights"></a>Laisser le fournisseur de services cloud gérer votre abonnement à votre place avec les droits de contrôle d’accès en fonction du rôle (RBAC)

Ajoutez le fournisseur de services cloud comme **propriétaire** dans votre abonnement.

1. Ajoutez votre fournisseur de services cloud comme utilisateur invité dans votre répertoire de locataire. Pour savoir comment ajouter un utilisateur, consultez [Ajouter de nouveaux utilisateurs dans Azure Active Directory](/azure/active-directory/add-users-azure-active-directory).

2. Ajoutez le rôle **Propriétaire** à l’utilisateur invité CSP. Pour savoir comment ajouter un utilisateur du fournisseur de services cloud à votre abonnement, consultez [Utiliser le contrôle d’accès en fonction du rôle pour gérer l’accès aux ressources de votre abonnement Azure](/azure/role-based-access-control/role-assignments-portal). Le fournisseur de services cloud crée l’abonnement Azure Stack local pour vous. Vous êtes prêt à utiliser Azure Stack.
3. Votre fournisseur de services cloud doit créer une ressource dans votre abonnement pour vérifier s’il peut gérer vos ressources.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la récupération d’informations d’utilisation de ressources à partir d’Azure Stack, consultez [Usage and billing in Azure Stack](../operator/azure-stack-billing-and-chargeback.md) (Utilisation et facturation dans Azure Stack).
