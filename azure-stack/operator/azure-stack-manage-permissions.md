---
title: Définir des autorisations d’accès en utilisant le contrôle d’accès en fonction du rôle
description: Découvrez comment définir des autorisations d’accès en utilisant le contrôle d’accès en fonction du rôle (RBAC) dans Azure Stack Hub.
author: justinha
ms.topic: article
ms.date: 12/23/2019
ms.author: justinha
ms.reviewer: thoroet
ms.lastreviewed: 12/23/2019
ms.openlocfilehash: f74f35a8b1124ed9678e6b4bc254d4d2cd05a84e
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77699267"
---
# <a name="set-access-permissions-using-role-based-access-control"></a>Définir des autorisations d’accès en utilisant le contrôle d’accès en fonction du rôle

Un utilisateur Azure Stack Hub peut être un lecteur, un propriétaire ou un collaborateur pour chaque instance d’un abonnement, d’un groupe de ressources ou d’un service. Par exemple, l’utilisateur A peut disposer des autorisations de lecteur sur l’abonnement un, mais disposer des autorisations de propriétaire sur la machine virtuelle sept.

 - Lecteur : L’utilisateur peut tout voir, mais ne peut faire aucun changement.
 - Collaborateur : L'utilisateur peut tout gérer, à l’exception de l’accès aux ressources.
 - Propriétaire : L'utilisateur peut tout gérer, y compris l’accès aux ressources.
 - Personnalisé : L’utilisateur dispose d’un accès limité et spécifique aux ressources.

 Pour plus d’informations sur la création d’un rôle personnalisé, consultez [Rôles personnalisés pour les ressources Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles).

## <a name="set-access-permissions-for-a-user"></a>Définir des autorisations d’accès pour un utilisateur

1. Connectez-vous avec un compte disposant des autorisations de propriétaire sur la ressource que vous souhaitez gérer.
2. Dans le panneau de la ressource, cliquez sur l’icône **Accès**![](media/azure-stack-manage-permissions/image1.png).
3. Dans le panneau **Utilisateurs**, cliquez sur **Rôles**.
4. Dans le panneau **Rôles**, cliquez sur **Ajouter** pour ajouter des autorisations pour l’utilisateur.

## <a name="set-access-permissions-for-a-universal-group"></a>Définir des autorisations d’accès pour un groupe universel 

> [!Note]
> Applicable uniquement aux services de fédération Active Directory (AD FS).

1. Connectez-vous avec un compte disposant des autorisations de propriétaire sur la ressource que vous souhaitez gérer.
2. Dans le panneau de la ressource, cliquez sur l’icône **Accès**![](media/azure-stack-manage-permissions/image1.png).
3. Dans le panneau **Utilisateurs**, cliquez sur **Rôles**.
4. Dans le panneau **Rôles**, cliquez sur **Ajouter** pour ajouter des autorisations pour le groupe Active Directory universel.

## <a name="next-steps"></a>Étapes suivantes

[Ajouter un locataire Azure Stack Hub](azure-stack-add-new-user-aad.md)