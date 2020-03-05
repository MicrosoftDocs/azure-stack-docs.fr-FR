---
title: Délégation des offres dans Azure Stack Hub
description: Découvrez comment déléguer des tâches comme la création d’offres et l’inscription des utilisateurs.
author: sethmanheim
ms.topic: article
ms.date: 01/27/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 95b7ca9d6ed5bf0c8fed0019b4b3954fd3a7d458
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77700236"
---
# <a name="delegate-offers-in-azure-stack-hub"></a>Déléguer des offres dans Azure Stack Hub

En tant qu’opérateur Azure Stack Hub, vous pouvez être amené à confier à d’autres personnes la charge d’inscrire des utilisateurs et de créer des abonnements. Par exemple, si vous êtes un fournisseur de services et souhaitez que les revendeurs inscrivent les clients et les gèrent à votre place. Si vous faites partie d’un groupe informatique centralisé d’une entreprise, vous pouvez aussi déléguer l’inscription des utilisateurs à d’autres membres de l’équipe informatique.

La délégation permet d’atteindre et de gérer plus facilement davantage d’utilisateurs que ce que vous pouvez faire par vous-même, comme le montre la figure suivante :

![Niveaux de délégation dans Azure Stack Hub](media/azure-stack-delegated-provider/image1.png)

Avec la délégation, le fournisseur délégué gère une offre (appelée *offre déléguée*) et les clients finaux obtiennent des inscriptions sous cette offre, sans intervention de l’administrateur système.

## <a name="delegation-roles"></a>Rôles de la délégation

Les rôles suivants font partie de la délégation :

* *L’opérateur cloud Azure Stack Hub* gère l’infrastructure Azure Stack Hub et crée un modèle d’offre. L’opérateur délègue à d’autres la responsabilité de fournir des offres à leur locataire.

* Les opérateurs Azure Stack Hub délégués sont des utilisateurs avec des droits de *Propriétaire* ou *Contributeur* dans les inscriptions appelées *fournisseurs délégués*. Ils peuvent appartenir à d’autres organisations (comme d’autres locataires Azure Active Directory).

* Les *utilisateurs* s’inscrivent aux offres et les utilisent pour gérer leurs charges de travail, la création de machines virtuelles, le stockage des données, etc.

## <a name="delegation-steps"></a>Étapes de la délégation

Vous configurez une délégation en deux étapes principales :

1. **Créer un abonnement de fournisseur délégué** : Abonnez un utilisateur à une offre ne contenant que le service d’abonnement. Les utilisateurs qui s’inscrivent à cette offre peuvent ensuite étendre les offres déléguées à d’autres utilisateurs en les inscrivant à ces offres.

2. **Déléguer une offre au fournisseur délégué** : Cette offre permet au fournisseur délégué de créer des abonnements ou d’étendre l’offre à leurs utilisateurs. Le fournisseur délégué peut à présent prendre l’offre et l’offrir à d’autres utilisateurs.

La figure suivante illustre les étapes de configuration d’une délégation :

![Étapes permettant de créer le fournisseur délégué et de l’autoriser à inscrire des utilisateurs dans Azure Stack Hub](media/azure-stack-delegated-provider/image2.png)

### <a name="delegated-provider-requirements"></a>Exigences pour le fournisseur délégué

Pour agir comme fournisseur délégué, un utilisateur établit une relation avec le fournisseur principal en créant un abonnement. Cet abonnement identifie le fournisseur délégué comme ayant le droit de présenter des offres déléguées pour le compte du fournisseur principal.

Une fois que cette relation est établie, l’opérateur Azure Stack Hub peut déléguer une offre au fournisseur délégué. Le fournisseur délégué peut prendre l’offre, la renommer (sans en modifier la substance) et l’offrir à ses clients.

## <a name="delegation-walkthrough"></a>Procédure pas à pas pour la délégation

Les sections suivantes fournissent une procédure pas à pas pratique pour configurer un fournisseur délégué, déléguer une offre et vérifier que les utilisateurs peuvent s’inscrire pour l’offre déléguée.

### <a name="set-up-roles"></a>Configurer les rôles

Pour utiliser cette procédure pas à pas, vous avez besoin de deux comptes Azure AD en plus de votre compte d’opérateur Azure Stack Hub. Si vous n’avez pas ces deux comptes, vous devez les créer. Les comptes peuvent appartenir à n’importe quel utilisateur de Azure AD et désignent le fournisseur délégué l’utilisateur.

| **Rôle** | **Droits d’organisation** |
| --- | --- |
| Fournisseur délégué |Utilisateur |
| Utilisateur |Utilisateur |

 > [!NOTE]
 > Dans le cas d’un revendeur CSP, la création de ce fournisseur délégué nécessite que ces utilisateurs se trouvent dans l’annuaire du locataire (l’annuaire Azure AD des utilisateurs). L’opérateur Azure Stack Hub doit [d’abord intégrer](azure-stack-enable-multitenancy.md) l’annuaire Azure AD de ce locataire, puis configurer l’utilisation et la facturation en suivant [ces étapes](azure-stack-csp-howto-register-tenants.md).

### <a name="identify-the-delegated-provider"></a>Identifier les fournisseurs délégués.

1. Connectez-vous au portail d’administration comme opérateur Azure Stack Hub.

1. Pour créer une offre qui permet à un utilisateur de devenir un fournisseur délégué :

   a.  [Créer un plan](azure-stack-create-plan.md).
       Ce plan doit inclure uniquement le service d’abonnement. Cet article utilise un plan nommé **PlanForDelegation** comme exemple.

   b.  [Créer une offre](azure-stack-create-offer.md) basée sur ce plan. Cet article utilise une offre nommée **OfferToDP** comme exemple.

   c.  Ajoutez le fournisseur délégué comme abonné à cette offre en sélectionnant **Abonnements**, puis **Ajouter** et **Nouvel abonnement de locataire**.

   ![Ajouter le fournisseur délégué en tant qu’abonné dans le portail d’administration Azure Stack Hub](media/azure-stack-delegated-provider/image3.png)

   > [!NOTE]
   > Comme avec toutes les offres Azure Stack Hub, vous avez la possibilité de rendre l’offre publique et de laisser les utilisateurs y souscrire, ou de conserver l’offre comme privée et demander à l’opérateur Azure Stack Hub de gérer l’inscription. Les fournisseurs délégués appartiennent généralement à un petit groupe. Vous souhaitez contrôler qui y est admis, donc le fait de conserver cette offre privée est logique dans la plupart des cas.

### <a name="azure-stack-hub-operator-creates-the-delegated-offer"></a>L’opérateur Azure Stack Hub crée l’offre déléguée

L’étape suivante consiste à créer le plan et l’offre que vous allez déléguer, et que vos clients utiliseront. Il est recommandé de définir cette offre comme les utilisateurs doivent la voir, car le fournisseur délégué ne peut pas changer les plans et les quotas qu’elle inclut.

1. Comme opérateur Azure Stack Hub, [créez un plan](azure-stack-create-plan.md) et [une offre](azure-stack-create-offer.md) basée sur le plan. Cet article utilise une offre nommée **DelegatedOffer** comme exemple.

   > [!NOTE]
   > Cette offre ne doit pas nécessairement être publique, mais vous pouvez la rendre publique. Cependant, dans la plupart des cas, vous voulez que seuls les fournisseurs délégués aient accès à l’offre. Une fois que vous déléguez une offre privée comme décrit dans les étapes suivantes, le fournisseur délégué y a accès.

2. Déléguez l’offre. Accédez à **DelegatedOffer**. Sous **Paramètres**, sélectionnez **Fournisseurs délégués**, puis **Ajouter**.

3. Sélectionnez l’abonnement pour le fournisseur délégué dans la liste déroulante, puis cliquez sur **Déléguer**.

   ![Ajouter un fournisseur délégué dans le portail d’administration Azure Stack Hub](media/azure-stack-delegated-provider/image4.png)

### <a name="delegated-provider-customizes-the-offer"></a>Le fournisseur délégué personnalise l’offre

Connectez-vous au portail utilisateur en tant que fournisseur délégué et créez une offre en utilisant l’offre déléguée comme modèle.

1. Sélectionnez **+ Créer une ressource**, puis **Offres + plans locataire**, puis sélectionnez **Offre**.

    ![Créer une offre dans le portail utilisateur Azure Stack Hub](media/azure-stack-delegated-provider/image5.png)

2. Attribuez un nom à l’offre. Cet exemple utilise **ResellerOffer**. Sélectionnez l’offre déléguée sur laquelle baser cette offre, puis cliquez sur **Créer**.

   ![Affecter un nom dans le portail utilisateur Azure Stack Hub](media/azure-stack-delegated-provider/image6.png)

   >[!IMPORTANT]
   >Il est important de comprendre que les fournisseurs délégués peuvent seulement choisir des offres qui leur ont été déléguées. Les utilisateurs ne peuvent pas modifier ces offres. Seul un opérateur Azure Stack Hub peut changer ces offres. Par exemple, seul un opérateur peut changer les plans et les quotas. Un fournisseur délégué ne construit pas une offre à partir de plans de base et de plans additionnels.

3. Le fournisseur délégué peut publier ces offres via l’URL de son propre portail. Pour publier l’offre, sélectionnez **Parcourir**, puis **Offres**. Sélectionnez l’offre, puis **Changer l’état**.

4. Les offres déléguées publiques sont désormais visibles uniquement via le portail délégué. Pour rechercher et modifier cette URL :

    a.  Sélectionnez **Parcourir**, puis **Tous les services**, puis, sous la catégorie **GÉNÉRAL**, sélectionnez **Abonnements**. Sélectionnez l’abonnement du fournisseur délégué (par exemple, **DPSubscription**), puis **Propriétés**.

    b.  Copiez l’URL du portail vers un autre emplacement, comme le Bloc-notes.

    ![Sélectionnez l’abonnement du fournisseur délégué dans le portail utilisateur Azure Stack Hub.](media/azure-stack-delegated-provider/dpportaluri.png)  

   Vous avez terminé la création d’une offre déléguée en tant que fournisseur délégué. Déconnectez-vous en tant que fournisseur délégué et fermez la fenêtre du navigateur.

### <a name="sign-up-for-the-offer"></a>S’inscrire à l’offre

1. Dans une nouvelle fenêtre de navigateur, accédez à l’URL du portail délégué que vous avez enregistrée à l’étape précédente. Connectez-vous au portail en tant qu’utilisateur.

   >[!NOTE]
   >Les offres déléguées sont visibles seulement si vous utilisez le portail délégué.

1. Dans le tableau de bord, sélectionnez **Prendre un abonnement**. Vous voyez que seules les offres déléguées créées par le fournisseur délégué sont présentées à l’utilisateur.

   ![Consulter et sélectionner des offres dans le portail utilisateur Azure Stack Hub](media/azure-stack-delegated-provider/image8.png)

Le processus de délégation d’une offre est terminé. Un utilisateur peut désormais s’inscrire à cette offre en prenant un abonnement à celle-ci.

## <a name="move-subscriptions-between-delegated-providers"></a>Déplacer des abonnements entre des fournisseurs délégués

Si nécessaire, un abonnement peut être déplacé entre des abonnements de fournisseurs délégués nouveaux ou existants appartenant au même locataire d’annuaire. Vous pouvez les déplacer en utilisant l’applet de commande PowerShell [Move-AzsSubscription](/powershell/module/azs.subscriptions.admin).

Le déplacement des abonnements est utile dans les cas suivants :

* Vous intégrez un nouveau membre de l’équipe qui tiendra le rôle de fournisseur délégué et souhaitez affecter à ce membre des abonnements créés précédemment dans l’abonnement du fournisseur par défaut.
* Vous avez plusieurs abonnements de fournisseurs délégués dans le même locataire d’annuaire (Azure AD) et vous devez déplacer des abonnements d’utilisateurs entre eux. Cela peut être le cas lorsqu’un membre de l’équipe se déplace entre les équipes et que son abonnement doit être affecté à la nouvelle équipe.

## <a name="next-steps"></a>Étapes suivantes

* [Approvisionner une machine virtuelle](../user/azure-stack-create-vm-template.md)
