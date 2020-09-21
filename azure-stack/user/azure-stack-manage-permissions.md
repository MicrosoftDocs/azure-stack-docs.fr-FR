---
title: Gérer l’accès aux ressources dans Azure Stack Hub avec un contrôle d’accès en fonction du rôle
description: Découvrez comment gérer les autorisations de contrôle d’accès en fonction du rôle (RBAC) en tant qu’administrateur ou abonné dans Azure Stack Hub.
author: bryanla
ms.topic: article
ms.date: 09/13/2019
ms.author: bryanla
ms.reviewer: fiseraci
ms.lastreviewed: 03/11/2019
ms.openlocfilehash: 1002f45b51f4a70ddbadb8230df6e98f591fcbb6
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90573749"
---
# <a name="manage-access-to-resources-in-azure-stack-hub-with-role-based-access-control"></a>Gérer l’accès aux ressources dans Azure Stack Hub avec le contrôle d’accès en fonction du rôle

Azure Stack Hub prend en charge le contrôle d’accès en fonction du rôle (RBAC), qui est le même [modèle de sécurité de la gestion des accès](/azure/role-based-access-control/overview) que celui utilisé par Microsoft Azure. Le contrôle d’accès en fonction du rôle vous permet de gérer les accès des utilisateurs, groupes et applications aux abonnements, ressources et services.

## <a name="basics-of-access-management"></a>Concepts de base de la gestion des accès

Avec le contrôle d’accès en fonction du rôle (RBAC), vous pouvez effectuer un contrôle affiné des accès pour sécuriser votre environnement. Vous accordez aux utilisateurs les autorisations spécifiques dont ils ont besoin en leur attribuant un rôle RBAC dans une étendue donnée. L’étendue du rôle attribué peut être un abonnement, un groupe de ressources ou une ressource unique. Pour plus d’informations sur la gestion de l’accès, consultez l’article [Contrôle d’accès en fonction du rôle (RBAC) dans le portail Azure](/azure/role-based-access-control/overview).

> [!NOTE]
> Lorsque vous déployez Azure Stack Hub à l’aide des services de fédération Active Directory (AD FS) comme fournisseur d’identité, seuls les groupes universels sont pris en charge pour les scénarios RBAC.

### <a name="built-in-roles"></a>Rôles intégrés

Azure Stack Hub fournit trois rôles de base applicables à tous les types de ressources :

* Le **propriétaire** : peut tout gérer, y compris l’accès aux ressources.
* Le **contributeur** : peut tout gérer, sauf l’accès aux ressources.
* Le **Lecteur** : peut tout afficher, mais ne peut apporter aucune modification.

### <a name="resource-hierarchy-and-inheritance"></a>Hiérarchie des ressources et héritage

Dans Azure Stack Hub, la hiérarchie des ressources est la suivante :

* Chaque abonnement est membre d’un seul répertoire.
* Chaque groupe de ressources est membre d’un seul abonnement.
* Chaque ressource est membre d’un seul groupe de ressources.

L’accès que vous accordez dans une étendue parente est hérité dans les étendues enfants. Exemple :

* Vous attribuez le rôle de **lecteur** à un groupe Azure AD dans l’étendue de l’abonnement. Les membres de ce groupe peuvent consulter tous les groupes de ressources et les ressources de l’abonnement.
* Vous attribuez le rôle de **Contributeur** à une application dans l’étendue du groupe de ressources. L’application peut gérer tous les types de ressources dans ce groupe de ressources, mais pas d’autres groupes de ressources de l’abonnement.

### <a name="assigning-roles"></a>Attribution de rôles

Vous pouvez attribuer plusieurs rôles à un utilisateur et associer chaque rôle à une étendue différente. Exemple :

* Vous attribuez le rôle **Lecteur** au TestUser-A pour l’Abonnement-1.
* Vous attribuez le rôle **Propriétaire** au TestUser-A pour TestVM-1.

L’article sur les [attributions de rôles](/azure/role-based-access-control/role-assignments-portal) Azure fournit des informations détaillées sur l’affichage, l’attribution et la suppression des rôles.

## <a name="set-access-permissions-for-a-user"></a>Définir des autorisations d’accès pour un utilisateur

Les étapes suivantes permettent de configurer des autorisations pour un utilisateur.

1. Connectez-vous avec un compte disposant des autorisations de propriétaire sur la ressource que vous souhaitez gérer.
2. Dans le volet de navigation de gauche, sélectionnez **Groupes de ressources**.
3. Choisissez le nom du groupe de ressources sur lequel vous souhaitez définir des autorisations.
4. Dans le volet de navigation du groupe de ressources, choisissez **Contrôle d’accès (IAM)**.<BR> La vue **Attributions de rôles** liste les éléments qui ont accès au groupe de ressources. Vous pouvez regrouper et filtrer les résultats.
5. Dans la barre de menus **Contrôle d’accès**, choisissez **Ajouter**.
6. Sur le volet **Ajouter des autorisations** :

   * Choisissez le rôle que vous souhaitez attribuer dans la liste déroulante **Rôle**.
   * Choisissez la ressource dont vous voulez autoriser l’accès dans la liste déroulante **Attribuer l'accès à**.
   * Sélectionnez l’utilisateur, le groupe ou l’application de votre répertoire auquel vous voulez accorder l’accès. Dans le répertoire, vous pouvez rechercher des noms d’affichage, des adresses de messagerie et des identificateurs d’objet.

7. Sélectionnez **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

[Créer des principaux de service](../operator/azure-stack-create-service-principals.md)
