---
title: Créer des plans d’extension
titleSuffix: Azure Stack Hub
description: Découvrez comment créer des plans d’extension dans Azure Stack Hub.
author: bryanla
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 06/11/2019
ms.openlocfilehash: 2fd22acfe6f47d3a12502d5528e1f3fd96294460
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77696479"
---
# <a name="create-add-on-plans-in-azure-stack-hub"></a>Créer des plans d’extension dans Azure Stack Hub

En tant qu'opérateur Azure Stack Hub, vous créez des plans additionnels pour modifier un [plan de base](azure-stack-create-plan.md) lorsque vous souhaitez proposer des services supplémentaires ou étendre vos quotas en matière d'*ordinateurs*, de *stockage* ou de *réseau* au-delà de l'offre initiale du plan de base. Les plans additionnels modifient le plan de base et sont des extensions facultatives que les utilisateurs peuvent choisir d’activer dans leur abonnement.

La meilleure solution est parfois de tout regrouper dans un seul plan. Mais vous pouvez aussi parfois préférer disposer d’un plan de base, puis proposer des services supplémentaires à l’aide de plans d’extension. Par exemple, vous pouvez choisir d’offrir des services IaaS dans le cadre d’un plan de base, avec tous les services PaaS traités comme des plans d’extension.

Le fait que les plans d’extension contribuent à surveiller l’utilisation des ressources constitue une autre raison de les utiliser. Pour ce faire, vous pouvez commencer avec un plan de base qui inclut des quotas relativement petits (selon les services requis). Ensuite, alors les utilisateurs atteignent une pleine capacité, ils sont avertis de leur consommation des ressources allouées en fonction de leur plan attribué. Dès lors, les utilisateurs peuvent sélectionner un plan d’extension qui fournit les ressources supplémentaires.

> [!NOTE]
> Lorsque vous ne souhaitez pas utiliser un plan d’extension pour étendre un quota, vous pouvez également choisir de [modifier la configuration d’origine du quota](azure-stack-quota-types.md#edit-a-quota).

Les plans additionnels sont [créés de la même façon ](azure-stack-create-plan.md) qu’un plan de base. La différence entre les deux est déterminée quand le plan est ajouté à une offre. Il est conçu comme plan de base ou comme plan additionnel. Quand vous ajoutez un plan additionnel à une offre existante, les ressources supplémentaires peuvent mettre jusqu’à une heure pour apparaître dans l’abonnement.

::: moniker range=">=azs-1902"
## <a name="create-an-add-on-plan-1902-and-later"></a>Créer un plan d’extension (versions 1902 et ultérieures)

1. Connectez-vous au portail d'administration Azure Stack Hub en tant qu'administrateur de cloud.
2. Suivez les étapes décrites pour [créer un nouveau plan de base](azure-stack-create-plan.md) afin de créer un plan offrant des services qui n’étaient pas proposés auparavant.
3. Dans le portail de l’administrateur, sélectionnez **Offres** , puis sélectionnez l’offre à mettre à jour avec un plan d’extension.

   ![Sélectionner l’offre à mettre à jour avec le plan d’extension dans le portail d’administration Azure Stack](media/create-add-on-plan/add-on1.png)

4. En bas des propriétés de l’offre, sélectionnez **Plans d’extension**. Sélectionnez **Ajouter**.

    ![Sélectionner les plans d’extension dans le portail d’administration Azure Stack](media/create-add-on-plan/add-on2.png)

5. Sélectionnez le plan à ajouter. Dans cet exemple, le plan s’intitule **20-storageaccounts**. Après avoir sélectionné le plan, cliquez sur **Sélectionnez** pour ajouter le plan à l’offre. Vous devriez recevoir une notification vous indiquant que le plan a été ajouté à l’offre.

    ![Sélectionner le plan d’extension à ajouter dans le portail d’administration Azure Stack](media/create-add-on-plan/add-on3.png)

6. Passez en revue la liste des plans d’extension inclus dans l’offre pour vérifier que le nouveau y figure.

    ![[Passer en revue la liste des plans additionnels dans le portail administrateur Azure Stack](media/create-add-on-plan/add-on4.png "Créer un plan additionnel")](media/create-add-on-plan/add-on4lg.png#lightbox)
::: moniker-end

::: moniker range="<=azs-1901"

## <a name="create-an-add-on-plan-1901-and-earlier"></a>Créer un plan d’extension (versions 1901 et antérieures)

1. Connectez-vous au portail d'administration Azure Stack Hub en tant qu'administrateur de cloud.
2. Suivez les étapes décrites pour [créer un nouveau plan de base](azure-stack-create-plan.md) afin de créer un plan offrant des services qui n’étaient pas proposés auparavant. Dans cet exemple, les services Key Vault (**Microsoft.KeyVault**) seront inclus dans le nouveau plan.
3. Dans le portail de l’administrateur, sélectionnez **Offres** , puis sélectionnez l’offre à mettre à jour avec un plan d’extension.

   ![Sélectionner l’offre à mettre à jour avec le plan d’extension dans le portail d’administration Azure Stack](media/create-add-on-plan/1.PNG)

4. Faites défiler les propriétés de l’offre jusqu’en bas et sélectionnez **Plans d’extension**. Sélectionnez **Ajouter**.

    ![Sélectionner les plans d’extension dans le portail d’administration Azure Stack](media/create-add-on-plan/2.PNG)

5. Sélectionnez le plan à ajouter. Dans cet exemple, le plan s’intitule **Key vault plan**. Après avoir sélectionné le plan, cliquez sur **Sélectionnez** pour ajouter le plan à l’offre. Vous devriez recevoir une notification vous indiquant que le plan a été ajouté à l’offre.

    ![Sélectionner le plan d’extension à ajouter dans le portail d’administration Azure Stack](media/create-add-on-plan/3.PNG)

6. Passez en revue la liste des plans d’extension inclus dans l’offre pour vérifier que le nouveau y figure.

    ![Passer en revue la liste des plans d’extension dans le portail d’administration Azure Stack](media/create-add-on-plan/4.PNG)

::: moniker-end

## <a name="next-steps"></a>Étapes suivantes

* [Créer une offre](azure-stack-create-offer.md)
