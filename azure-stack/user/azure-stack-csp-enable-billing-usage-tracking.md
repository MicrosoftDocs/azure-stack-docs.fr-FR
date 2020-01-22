---
title: Laisser votre fournisseur de solutions cloud gérer votre abonnement Azure Stack Hub | Microsoft Docs
description: Découvrez comment laisser votre fournisseur de solutions cloud (CSP) gérer votre abonnement Azure Stack Hub à votre place.
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
ms.date: 10/03/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/20/2019
ms.openlocfilehash: 44d00381ea11ef6ac7e947c31adce41b3228d93d
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75883777"
---
# <a name="let-your-cloud-solution-provider-manage-your-azure-stack-hub-subscription"></a>Laisser votre fournisseur de solutions cloud gérer votre abonnement Azure Stack Hub

Si vous utilisez Azure Stack Hub avec un CSP, vous pouvez choisir de gérer votre propre abonnement pour accéder aux ressources dans Azure et Azure Stack Hub. Vous pouvez également laisser le fournisseur gérer votre abonnement à votre place. Cet article vous montre comment :

* Autoriser votre fournisseur de services à accéder à votre abonnement.
* Vérifier que le fournisseur de services peut gérer votre service.

> [!NOTE]
> Si le CSP ne gère pas votre compte et si vous ignorez les étapes suivantes, le CSP ne peut pas gérer votre abonnement Azure Stack Hub à votre place.

## <a name="manage-your-subscription-with-a-csp"></a>Gérer votre abonnement avec un CSP

Ajoutez le fournisseur de services cloud comme **utilisateur** dans votre abonnement.

1. Ajoutez votre fournisseur de services cloud en tant qu’utilisateur invité avec le rôle d’**utilisateur** dans votre répertoire de locataire. Pour savoir comment ajouter un utilisateur, consultez [Ajouter de nouveaux utilisateurs dans Azure Active Directory](/azure/active-directory/add-users-azure-active-directory).

2. Le CSP crée l’abonnement Azure Stack Hub local pour vous. Vous êtes prêt à utiliser Azure Stack Hub.

3. Votre fournisseur de services cloud doit créer une ressource dans votre abonnement pour vérifier s’il peut également gérer vos ressources. Par exemple, il peut [Créer une machine virtuelle Windows avec le portail Azure Stack Hub](azure-stack-quick-windows-portal.md).

## <a name="let-the-csp-manage-your-subscription-using-rbac-rights"></a>Laisser le CSP gérer votre abonnement à votre place avec les droits de contrôle d’accès en fonction du rôle (RBAC)

Ajoutez le fournisseur de services cloud comme **propriétaire** dans votre abonnement.

1. Ajoutez votre fournisseur de services cloud comme utilisateur invité dans votre répertoire de locataire. Pour plus d’informations sur l’ajout d’un utilisateur, consultez [Ajouter de nouveaux utilisateurs à Azure Active Directory](/azure/active-directory/add-users-azure-active-directory).

2. Ajoutez le rôle **Propriétaire** à l’utilisateur invité CSP. Pour plus d’informations sur l’ajout d’un utilisateur du CSP à votre abonnement, consultez [Utiliser le contrôle d’accès en fonction du rôle pour gérer l’accès aux ressources de votre abonnement Azure](/azure/role-based-access-control/role-assignments-portal). Le CSP crée l’abonnement Azure Stack Hub local pour vous. Vous êtes prêt à utiliser Azure Stack Hub.
3. Votre fournisseur de services cloud doit créer une ressource dans votre abonnement pour vérifier s’il peut gérer vos ressources.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la récupération d’informations d’utilisation de ressources à partir d’Azure Stack Hub, voir [Utilisation et facturation dans Azure Stack Hub](../operator/azure-stack-billing-and-chargeback.md).
