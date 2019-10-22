---
title: Définir des autorisations d’accès en utilisant le contrôle d’accès en fonction du rôle | Microsoft Docs
description: Découvrez comment définir des autorisations d’accès en utilisant le contrôle d’accès en fonction du rôle (RBAC) dans Azure Stack.
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
ms.openlocfilehash: fa4e836a2c7cd5b59a6234a05efcc1cface12620
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72277053"
---
# <a name="set-access-permissions-using-role-based-access-control"></a>Définir des autorisations d’accès en utilisant le contrôle d’accès en fonction du rôle

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