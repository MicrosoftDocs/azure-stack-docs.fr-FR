---
title: Gérer les autorisations d’accès aux ressources par utilisateur dans Azure Stack (administrateur de service et locataire) | Microsoft Docs
description: En tant qu’administrateur de service ou locataire, découvrez comment gérer les autorisations de contrôle d’accès en fonction du rôle (RBAC).
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 9fc4e6bb48c8cdd7508a1a85d193a22761c62e99
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66268598"
---
# <a name="manage-role-based-access-control"></a>Gérer le contrôle d’accès en fonction du rôle

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Un utilisateur Azure Stack peut être un lecteur, un propriétaire ou un collaborateur pour chaque instance d’un abonnement, d’un groupe de ressources ou d’un service. Par exemple, l’utilisateur A peut disposer des autorisations de lecteur sur l’abonnement un, mais disposer des autorisations de propriétaire sur la machine virtuelle sept.

 - Lecteur : L’utilisateur peut tout voir, mais ne peut faire aucun changement.
 - Collaborateur : L'utilisateur peut tout gérer, à l’exception de l’accès aux ressources.
 - Propriétaire : L'utilisateur peut tout gérer, y compris l’accès aux ressources.

## <a name="set-access-permissions-for-a-user"></a>Définir des autorisations d’accès pour un utilisateur

1. Connectez-vous avec un compte disposant des autorisations de propriétaire sur la ressource que vous souhaitez gérer.
2. Dans le panneau de la ressource, cliquez sur l’icône **Accès** ![](media/azure-stack-manage-permissions/image1.png).
3. Dans le panneau **Utilisateurs**, cliquez sur **Rôles**.
4. Dans le panneau **Rôles**, cliquez sur **Ajouter** pour ajouter des autorisations pour l’utilisateur.

## <a name="set-access-permissions-for-a-universal-group"></a>Définir des autorisations d’accès pour un groupe universel 

> [!Note]
> Applicable uniquement aux services de fédération Active Directory (AD FS).

1. Connectez-vous avec un compte disposant des autorisations de propriétaire sur la ressource que vous souhaitez gérer.
2. Dans le panneau de la ressource, cliquez sur l’icône **Accès** ![](media/azure-stack-manage-permissions/image1.png).
3. Dans le panneau **Utilisateurs**, cliquez sur **Rôles**.
4. Dans le panneau **Rôles**, cliquez sur **Ajouter** pour ajouter des autorisations pour le groupe Active Directory universel.

## <a name="next-steps"></a>Étapes suivantes

[Ajouter un locataire Azure Stack](azure-stack-add-new-user-aad.md)