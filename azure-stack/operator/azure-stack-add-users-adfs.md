---
title: Ajouter des utilisateurs Azure Stack Hub dans AD FS
description: Découvrez comment ajouter des utilisateurs Azure Stack Hub pour les déploiements des services de fédération Active Directory (AD FS).
author: IngridAtMicrosoft
ms.topic: article
ms.date: 06/03/2019
ms.author: inhenkel
ms.reviewer: unknown
ms.lastreviewed: 06/03/2019
ms.openlocfilehash: 79380d40282757e704f9dc0ef3bdaf977253ba14
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "77703347"
---
# <a name="add-a-new-azure-stack-hub-user-account-in-active-directory-federation-services-ad-fs"></a>Ajouter un compte d’utilisateur Azure Stack Hub dans les services de fédération Active Directory (AD FS).

Vous pouvez utiliser le composant logiciel enfichable **Utilisateurs et ordinateurs Active Directory** pour ajouter des utilisateurs supplémentaires à un environnement Azure Stack Hub en utilisant AD FS comme son fournisseur d’identité.

## <a name="add-windows-server-active-directory-users"></a>Ajouter des utilisateurs Windows Server Active Directory

1. Connectez-vous à un ordinateur avec un compte fournissant un accès aux outils d’administration Windows, puis ouvrez une nouvelle console MMC (Microsoft Management Console).
2. Sélectionnez **Fichier > Ajouter ou supprimer un composant logiciel enfichable**.

   > [!TIP]
   > Remplacez *directory-domain* par le domaine qui correspond à votre répertoire. 

3. Sélectionnez **Utilisateurs et ordinateurs Active Directory** > *directory-domain* > **Utilisateurs**.
4. Sélectionnez **Action** > **Nouveau** > **Utilisateur**.
5. Dans Nouvel objet - Utilisateur, fournissez les informations utilisateur. Sélectionnez **Suivant**.
6. Fournissez un mot de passe et confirmez-le.
7. Sélectionnez **Suivant** pour compléter les valeurs. Sélectionnez **Terminer** pour créer l’utilisateur.


## <a name="next-steps"></a>Étapes suivantes

[Créer une identité d’application pour accéder aux ressources Azure Stack Hub](azure-stack-create-service-principals.md)