---
title: Délégation des offres dans Azure Stack | Microsoft Docs
description: Découvrez comment placer d’autres personnes en charge de la création d’offres et de l’inscription des utilisateurs.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/12/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 97b253e4df3762d4579923124b7768c14f40d3de
ms.sourcegitcommit: 3af71025e85fc53ce529de2f6a5c396b806121ed
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71159668"
---
# <a name="delegate-offers-in-azure-stack"></a>Déléguer des offres dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

En tant qu’opérateur Azure Stack, vous pouvez être amené à confier à d’autres personnes la charge d’inscrire des utilisateurs et de créer des abonnements. Par exemple, si vous êtes un fournisseur de services et souhaitez que les revendeurs inscrivent les clients et les gèrent à votre place. Si vous faites partie d’un groupe informatique centralisé d’une entreprise, vous pouvez aussi déléguer l’inscription des utilisateurs à d’autres membres de l’équipe informatique.

La délégation permet d’atteindre et de gérer plus facilement davantage d’utilisateurs que ce que vous pouvez faire par vous-même, comme le montre la figure suivante :

![Niveaux de délégation](media/azure-stack-delegated-provider/image1.png)

Avec la délégation, le fournisseur délégué gère une offre (appelée *offre déléguée*) et les clients finaux obtiennent des inscriptions sous cette offre, sans intervention de l’administrateur système.

## <a name="delegation-roles"></a>Rôles de la délégation

Les rôles suivants font partie de la délégation :

* *L’opérateur cloud Azure Stack* gère l’infrastructure Azure Stack, crée un modèle d’offre. L’opérateur délègue à d’autres la responsabilité de fournir des offres à leur locataire.

* Les opérateurs Azure Stack délégués sont des utilisateurs avec des droits *Propriétaire* ou *Contributeur* dans les inscriptions appelées *fournisseurs délégués*. Ils peuvent appartenir à d’autres organisations (comme d’autres locataires Azure Active Directory).

* Les *utilisateurs* s’inscrivent aux offres et les utilisent pour gérer leurs charges de travail, la création de machines virtuelles, le stockage des données, etc.

## <a name="delegation-steps"></a>Étapes de la délégation

Vous configurez une délégation en deux étapes principales :

1. **Créer un abonnement de fournisseur délégué** : Abonnez un utilisateur à une offre ne contenant que le service d’abonnement. Les utilisateurs qui s’inscrivent à cette offre peuvent ensuite étendre les offres déléguées à d’autres utilisateurs en les inscrivant à ces offres.

2. **Déléguer une offre au fournisseur délégué** : Cette offre permet au fournisseur délégué de créer des abonnements ou d’étendre l’offre à leurs utilisateurs. Le fournisseur délégué peut à présent prendre l’offre et l’offrir à d’autres utilisateurs.

La figure suivante illustre les étapes de configuration d’une délégation :

![Créer le fournisseur délégué et lui permettre d’inscrire des utilisateurs](media/azure-stack-delegated-provider/image2.png)

### <a name="delegated-provider-requirements"></a>Exigences pour le fournisseur délégué

Pour agir comme fournisseur délégué, un utilisateur établit une relation avec le fournisseur principal en créant un abonnement. Cet abonnement identifie le fournisseur délégué comme ayant le droit de présenter des offres déléguées pour le compte du fournisseur principal.

Une fois que cette relation est établie, l’opérateur Azure Stack peut déléguer une offre au fournisseur délégué. Le fournisseur délégué peut prendre l’offre, la renommer (sans en modifier la substance) et l’offrir à ses clients.

## <a name="delegation-walkthrough"></a>Procédure pas à pas pour la délégation

Les sections suivantes fournissent une procédure pas à pas pratique pour configurer un fournisseur délégué, déléguer une offre et vérifier que les utilisateurs peuvent s’inscrire pour l’offre déléguée.

### <a name="set-up-roles"></a>Configurer les rôles

Pour utiliser cette procédure pas à pas, vous avez besoin de deux comptes Azure AD en plus de votre compte d’opérateur Azure Stack. Si vous n’avez pas ces deux comptes, vous devez les créer. Les comptes peuvent appartenir à n’importe quel utilisateur de Azure AD et désignent le fournisseur délégué l’utilisateur.

| **Rôle** | **Droits d’organisation** |
| --- | --- |
| Fournisseur délégué |Utilisateur |
| Utilisateur |Utilisateur |

 > [!NOTE]
 > Dans le cas d’un revendeur CSP, la création de ce fournisseur délégué nécessite que ces utilisateurs se trouvent dans le répertoire du locataire (l’utilisateur d’Azure AD). L’opérateur Azure Stack doit [d’abord intégrer](azure-stack-enable-multitenancy.md) l’instance Azure AD de ce locataire, puis configurer l’utilisation et la facturation en suivant [ces étapes](azure-stack-csp-howto-register-tenants.md).

### <a name="identify-the-delegated-provider"></a>Identifier les fournisseurs délégués.

1. Connectez-vous au portail d’administration comme opérateur Azure Stack.

1. Pour créer une offre qui permet à un utilisateur de devenir un fournisseur délégué :

   a.  [Créer un plan](azure-stack-create-plan.md).
       Ce plan doit inclure uniquement le service d’abonnement. Cet article utilise un plan nommé **PlanForDelegation** comme exemple.

   b.  [Créer une offre](azure-stack-create-offer.md) basée sur ce plan. Cet article utilise une offre nommée **OfferToDP** comme exemple.

   c.  Ajoutez le fournisseur délégué comme abonné à cette offre en sélectionnant **Abonnements**, puis **Ajouter** et **Nouvel abonnement de locataire**.

   ![Ajouter le fournisseur délégué en tant qu’abonné](media/azure-stack-delegated-provider/image3.png)

   > [!NOTE]
   > Comme avec toutes les offres Azure Stack, vous avez la possibilité de rendre l’offre publique et de laisser les utilisateurs y souscrire, ou de conserver l’offre comme privée et demander à l’opérateur Azure Stack de gérer l’inscription. Les fournisseurs délégués appartiennent généralement à un petit groupe. Vous souhaitez contrôler qui y est admis, donc le fait de conserver cette offre privée est logique dans la plupart des cas.

### <a name="azure-stack-operator-creates-the-delegated-offer"></a>L’opérateur Azure Stack crée l’offre déléguée

L’étape suivante consiste à créer le plan et l’offre que vous allez déléguer, et que vos clients utiliseront. Il est recommandé de définir cette offre exactement comme les utilisateurs doivent la voir, car le fournisseur délégué ne peut changer les plans et les quotas qu’elle inclut.

1. Comme opérateur Azure Stack, [créez un plan](azure-stack-create-plan.md) et [une offre](azure-stack-create-offer.md) basée sur le plan. Cet article utilise une offre nommée **DelegatedOffer** comme exemple.

   > [!NOTE]
   > Cette offre ne doit pas nécessairement être publique, mais vous pouvez la rendre publique. Cependant, dans la plupart des cas, vous voulez que seuls les fournisseurs délégués aient accès à l’offre. Une fois que vous déléguez une offre privée comme décrit dans les étapes suivantes, le fournisseur délégué y a accès.

2. Déléguez l’offre. Accédez à **DelegatedOffer**. Sous **Paramètres**, sélectionnez **Fournisseurs délégués**, puis **Ajouter**.

3. Sélectionnez l’abonnement pour le fournisseur délégué dans la liste déroulante, puis cliquez sur **Déléguer**.

   ![Ajouter un fournisseur délégué](media/azure-stack-delegated-provider/image4.png)

### <a name="delegated-provider-customizes-the-offer"></a>Le fournisseur délégué personnalise l’offre

Connectez-vous au portail utilisateur en tant que fournisseur délégué et créez une offre en utilisant l’offre déléguée comme modèle.

1. Sélectionnez **+ Créer une ressource**, puis **Offres + plans locataire**, puis sélectionnez **Offre**.

    ![Créer une offre](media/azure-stack-delegated-provider/image5.png)

2. Attribuez un nom à l’offre. Cet exemple utilise **ResellerOffer**. Sélectionnez l’offre déléguée sur laquelle baser cette offre, puis cliquez sur **Créer**.

   ![Attribuer un nom](media/azure-stack-delegated-provider/image6.png)

   >[!IMPORTANT]
   >Il est important de comprendre que les fournisseurs délégués peuvent seulement choisir des offres qui leur ont été déléguées. Les utilisateurs ne peuvent pas modifier ces offres. Seuls les opérateurs Azure Stack peuvent modifier ces offres, par exemple, leurs plans et quotas. Un fournisseur délégué ne construit pas une offre à partir de plans de base et de plans additionnels.

3. Le fournisseur délégué peut publier ces offres via l’URL de son propre portail. Pour publier l’offre, sélectionnez **Parcourir**, puis **Offres**. Sélectionnez l’offre, puis **Changer l’état**.

4. Les offres déléguées publiques sont désormais visibles uniquement via le portail délégué. Pour rechercher et modifier cette URL :

    a.  Sélectionnez **Parcourir**, puis **Tous les services**, puis, sous la catégorie **GÉNÉRAL**, sélectionnez **Abonnements**. Sélectionner l’abonnement du fournisseur délégué ; par exemple, **DPSubscription**, puis **Propriétés**.

    b.  Copiez l’URL du portail vers un autre emplacement, comme le Bloc-notes.

    ![Sélectionner l’abonnement du fournisseur délégué](media/azure-stack-delegated-provider/dpportaluri.png)  

   Vous avez terminé la création d’une offre déléguée en tant que fournisseur délégué. Déconnectez-vous en tant que fournisseur délégué et fermez la fenêtre du navigateur.

### <a name="sign-up-for-the-offer"></a>S’inscrire à l’offre

1. Dans une nouvelle fenêtre de navigateur, accédez à l’URL du portail délégué que vous avez enregistrée à l’étape précédente. Connectez-vous au portail en tant qu’utilisateur.

   >[!NOTE]
   >Les offres déléguées ne sont pas visibles, à moins d’utiliser le portail délégué.

1. Dans le tableau de bord, sélectionnez **Prendre un abonnement**. Vous voyez que seules les offres déléguées créées par le fournisseur délégué sont présentées à l’utilisateur.

   ![Afficher et sélectionner des offres](media/azure-stack-delegated-provider/image8.png)

Le processus de délégation d’une offre est terminé. Un utilisateur peut désormais s’inscrire à cette offre en prenant un abonnement à celle-ci.

## <a name="move-subscriptions-between-delegated-providers"></a>Déplacer des abonnements entre des fournisseurs délégués

Si nécessaire, un abonnement peut être déplacé entre des abonnements de fournisseurs délégués nouveaux ou existants appartenant au même locataire d’annuaire. Pour ce faire, vous utilisez la cmdlet PowerShell [Move-AzsSubscription](/powershell/module/azs.subscriptions.admin).

Cela est utile quand :

* Vous intégrez un nouveau membre de l’équipe qui tiendra le rôle de fournisseur délégué et souhaitez affecter à ce membre des abonnements créés précédemment dans l’abonnement du fournisseur par défaut.
* Vous avez plusieurs abonnements de fournisseurs délégués dans le même locataire d’annuaire (Azure Active Directory) et devez déplacer des abonnements d’utilisateurs entre eux. Cela peut être le cas lorsqu’un membre de l’équipe se déplace entre les équipes et que son abonnement doit être affecté à la nouvelle équipe.

## <a name="next-steps"></a>Étapes suivantes

* [Approvisionner une machine virtuelle](../user/azure-stack-create-vm-template.md)
