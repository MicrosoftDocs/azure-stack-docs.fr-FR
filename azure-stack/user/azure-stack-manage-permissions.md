---
title: Gérer les autorisations d’accès aux ressources par utilisateur dans Azure Stack (administrateur de service et locataire) | Microsoft Docs
description: En tant qu’administrateur de service ou locataire, découvrez comment gérer les autorisations de contrôle d’accès en fonction du rôle (RBAC).
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 03/11/2019
ms.openlocfilehash: 0f6cae1604c6635f7eb401ed4db16a9a967e1ab9
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985676"
---
# <a name="manage-access-to-resources-with-azure-stack-role-based-access-control"></a>Gérer l’accès aux ressources à l’aide du contrôle d’accès en fonction du rôle dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Azure Stack prend en charge le contrôle d’accès en fonction du rôle (RBAC), qui est le même [modèle de sécurité de la gestion des accès](https://docs.microsoft.com/azure/role-based-access-control/overview) que celui utilisé par Microsoft Azure. Le contrôle d’accès en fonction du rôle vous permet de gérer les accès des utilisateurs, groupes et applications aux abonnements, ressources et services.

## <a name="basics-of-access-management"></a>Concepts de base de la gestion des accès

Avec le contrôle d’accès en fonction du rôle, vous pouvez effectuer un contrôle affiné des accès pour sécuriser votre environnement. Pour cela, vous accordez aux utilisateurs les autorisations spécifiques dont ils ont besoin en leur attribuant un rôle RBAC dans une étendue définie. L’étendue du rôle attribué peut être un abonnement, un groupe de ressources ou une ressource unique. Consultez l’article [Bien démarrer avec le contrôle d’accès en fonction du rôle dans le portail Azure](https://docs.microsoft.com/azure/role-based-access-control/overview) pour obtenir des informations plus détaillées sur la gestion des accès.

### <a name="built-in-roles"></a>Rôles intégrés

Azure Stack fournit trois rôles de base applicables à tous les types de ressources :

* Le **propriétaire** peut tout gérer, y compris l’accès aux ressources.
* Le **collaborateur** peut tout gérer, sauf l’accès aux ressources.
* Le **lecteur** peut tout voir, mais ne peut faire aucun changement.

### <a name="resource-hierarchy-and-inheritance"></a>Hiérarchie des ressources et héritage

Dans Azure Stack, la hiérarchie des ressources est la suivante :

* Chaque abonnement est membre d’un seul répertoire.
* Chaque groupe de ressources est membre d’un seul abonnement.
* Chaque ressource est membre d’un seul groupe de ressources.

L’accès que vous accordez dans une étendue parente est hérité dans les étendues enfants. Par exemple : 

* Vous attribuez le rôle de **lecteur** à un groupe Azure AD dans l’étendue de l’abonnement. Les membres de ce groupe peuvent consulter tous les groupes de ressources et les ressources de l’abonnement.
* Vous attribuez le rôle de **contributeur** à une application dans l’étendue du groupe de ressources. L’application peut gérer tous les types de ressources dans ce groupe de ressources, mais pas dans les autres groupes de ressources de l’abonnement.

### <a name="assigning-roles"></a>Attribution de rôles

Vous pouvez attribuer plusieurs rôles à un utilisateur et associer chaque rôle à une étendue différente. Par exemple : 

* Vous attribuez le rôle de lecteur à UtilisateurTest-A sur Abonnement-1.
* Vous attribuez le rôle de propriétaire à UtilisateurTest-A sur MVTest-1.

L’article sur les [attributions de rôles](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) Azure fournit des informations détaillées sur l’affichage, l’attribution et la suppression des rôles.

## <a name="set-access-permissions-for-a-user"></a>Définir des autorisations d’accès pour un utilisateur

Les étapes suivantes permettent de configurer des autorisations pour un utilisateur.

1. Connectez-vous avec un compte disposant des autorisations de propriétaire sur la ressource que vous souhaitez gérer.
2. Dans le volet de navigation de gauche, sélectionnez **Groupes de ressources**.
3. Choisissez le nom du groupe de ressources sur lequel vous souhaitez définir des autorisations.
4. Dans le volet de navigation du groupe de ressources, choisissez **Contrôle d’accès (IAM)**. La vue **Attributions de rôles** liste les éléments qui ont accès au groupe de ressources. Vous pouvez regrouper et filtrer les résultats.
5. Dans la barre de menus **Contrôle d’accès**, choisissez **Ajouter**.
6. Sur le volet **Ajouter des autorisations** :

   * Choisissez le rôle que vous souhaitez attribuer dans la liste déroulante **Rôle**.
   * Choisissez la ressource dont vous voulez autoriser l’accès dans la liste déroulante **Attribuer l'accès à**.
   * Sélectionnez l’utilisateur, le groupe ou l’application de votre répertoire auquel vous voulez accorder l’accès. Dans le répertoire, vous pouvez rechercher des noms d’affichage, des adresses de messagerie et des identificateurs d’objet.

7. Sélectionnez **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

[Créer les principaux du service](azure-stack-create-service-principals.md)
