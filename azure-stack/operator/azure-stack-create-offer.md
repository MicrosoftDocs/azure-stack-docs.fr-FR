---
title: Créer une offre dans Azure Stack Hub
description: Découvrez comment créer une offre pour vos utilisateurs dans Azure Stack Hub.
author: bryanla
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 06/11/2019
ms.openlocfilehash: fe2cb87c666e8c3447eca46bdcb49c71514bccdc
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "77704486"
---
# <a name="create-an-offer-in-azure-stack-hub"></a>Créer une offre dans Azure Stack Hub

Les [offres](azure-stack-overview.md) sont des groupes d’un ou plusieurs plans que les fournisseurs proposent à l’achat ou à l’abonnement aux utilisateurs. Cet article décrit comment créer une offre comprenant le [plan que vous avez créé](azure-stack-create-plan.md). Cette offre donne aux abonnés la possibilité de configurer des machines virtuelles.

::: moniker range=">=azs-1902"
## <a name="create-an-offer-1902-and-later"></a>Créer une offre (1902 et versions ultérieures)

1. Connectez-vous au [portail d’administration Azure Stack Hub](https://adminportal.local.azurestack.external) et sélectionnez **+ Créer une ressource**, puis **Offres + plans** et **Offre**.

   ![Créer une offre dans Azure Stack Hub](media/azure-stack-create-offer/offers.png)

2. Une interface utilisateur à onglets apparaît et vous permet de définir le nom de l'offre. Vous pouvez également ajouter des plans de base et des plans complémentaires existants ou en créer de nouveaux. Plus important encore, vous pouvez consulter les détails de l’offre que vous créez, avant de décider de la créer.

   Sous l’onglet **De base**, entrez un **Nom d’affichage** et le **Nom de la ressource**, puis sous **Groupe de ressources**, sélectionnez **Créer nouveau** ou **Utiliser existant**. Le nom d’affichage correspond au nom convivial de l’offre. Ce nom convivial est la seule information sur l’offre que les utilisateurs verront au moment de l’abonnement à l’offre dans le portail utilisateur. Utilisez un nom intuitif qui aide les utilisateurs à comprendre en quoi elle consiste. Seul l’administrateur peut voir le nom de la ressource. Il s’agit du nom que les administrateurs utilisent pour gérer l’offre en tant que ressource Azure Resource Manager. Dans cet onglet, vous pouvez également choisir de rendre cette offre publique ou de la garder privée. Le paramètre par défaut est « privée ». Vous pouvez à tout moment [modifier l’état public ou privé de l’offre](#change-the-state-of-an-offer).

   ![Nouvelle offre dans Azure Stack Hub](media/azure-stack-create-offer/new-offer.png)
  
3. Sélectionnez l’onglet **Plans de base** ou cliquez sur le bouton **Suivant : Plans de base >** . Sélectionnez le(s) plan(s) que vous souhaitez inclure dans l’offre.

   ![Sélectionner le plan à inclure dans votre offre Azure Stack Hub](media/azure-stack-create-offer/select-plan.png)

4. À ce stade, vous pouvez créer un plan complémentaire pour modifier le plan de base, mais cette étape est facultative. Vous aurez l’occasion de créer un plan additionnel dans l’article suivant intitulé [Plans additionnels Azure Stack Hub](create-add-on-plan.md).

5. Sélectionnez l’onglet **Vérifier + créer**. Passez en revue le résumé de l’offre pour vous assurer que toutes les valeurs sont correctes. L’interface vous permet d’étendre les quotas dans les plans choisis, un à la fois, pour afficher les détails de chaque quota dans un plan. Vous pouvez également revenir en arrière pour effectuer toutes les modifications nécessaires.

6. Sélectionnez **Créer** pour créer l’offre.

   ![Vérifier et créer une offre dans Azure Stack Hub](media/azure-stack-create-offer/review-offer.png)

### <a name="change-the-state-of-an-offer"></a>Changer l’état d’une offre

Après avoir créé l’offre, vous pouvez modifier son état. Les offres doivent être rendues **publiques** pour permettre aux utilisateurs d’avoir une vue d’ensemble lorsqu’ils s’abonnent. Les offres peuvent être :

- **Public** : visibles par les utilisateurs.
- **Privé** : visibles uniquement par les administrateurs cloud. Ce paramètre est utile lors de l’élaboration du plan ou de l’offre, ou si l’administrateur cloud souhaite [créer chaque abonnement pour les utilisateurs](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator).
- **Désactivées** : fermées aux nouveaux abonnés. L’administrateur cloud peut désactiver des offres pour empêcher tout nouveau abonnement, sans que cela n’affecte les abonnés actuels.

  > [!TIP]  
  > Les modifications apportées à l’offre ne sont pas immédiatement visibles par l’utilisateur. Pour voir les modifications et afficher la nouvelle offre, les utilisateurs pourraient devoir se déconnecter et se reconnecter au portail de l’utilisateur.

Il existe deux façons de changer l’état d’une offre :

1. Dans **Toutes les ressources**, sélectionnez le nom de l’offre. Dans l’écran **Vue d’ensemble** de l’offre, sélectionnez **Changer l’état**. Choisissez l’état à utiliser (par exemple, **Public**).

   ![Changer l’état de votre offre Azure Stack Hub](media/azure-stack-create-offer/change-state.png)

2. Sélectionnez **Paramètres de l’offre**. Choisissez l’état à utiliser (par exemple, **Public**), puis sélectionnez **Enregistrer**.

   ![Paramètres de l’offre Azure Stack Hub](media/azure-stack-create-offer/offer-settings.png)
::: moniker-end

::: moniker range="<=azs-1901"
## <a name="create-an-offer-1901-and-earlier"></a>Créer une offre (1901 et versions antérieures)

1. Connectez-vous au [portail d’administration Azure Stack Hub](https://adminportal.local.azurestack.external) et sélectionnez **+ Créer une ressource**, puis **Offres + plans de locataire** et **Offre**.

   ![Créer une offre dans Azure Stack Hub](media/azure-stack-create-offer/image01.png)
  
2. Sous **Nouvelle offre**, renseignez le **Nom d’affichage** et le **Nom de la ressource**, puis sélectionnez un **Groupe de ressources**, sélectionnez **Créer nouveau** ou **Utiliser existant**. Le nom d’affichage correspond au nom convivial de l’offre. Ce nom convivial est la seule information sur l’offre que les utilisateurs verront au moment de l’abonnement à l’offre. Utilisez un nom intuitif qui aide les utilisateurs à comprendre en quoi elle consiste. Seul l’administrateur peut voir le nom de la ressource. Il s’agit du nom que les administrateurs utilisent pour gérer l’offre en tant que ressource Azure Resource Manager.

   ![Nouvelle offre dans Azure Stack Hub](media/azure-stack-create-offer/image01a.png)
  
3. Sélectionnez **Plans de base** pour ouvrir le **Plan**. Sélectionnez les plans que vous souhaitez inclure dans l’offre, puis choisissez **Sélectionner**. Pour créer l’offre, sélectionnez **Créer**.

   ![Sélectionner le plan à inclure dans votre offre Azure Stack Hub](media/azure-stack-create-offer/image02.png)
  
4. Après avoir créé l’offre, vous pouvez modifier son état. Les offres doivent être rendues **publiques** pour permettre aux utilisateurs d’avoir une vue d’ensemble lorsqu’ils s’abonnent. Les offres peuvent être :

   - **Public** : visibles par les utilisateurs.
   - **Privé** : visibles uniquement par les administrateurs cloud. Ce paramètre est utile lors de l’élaboration du plan ou de l’offre, ou si l’administrateur cloud souhaite [créer chaque abonnement pour les utilisateurs](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator).
   - **Désactivées** : fermées aux nouveaux abonnés. L’administrateur cloud peut désactiver des offres pour empêcher tout nouveau abonnement, sans que cela n’affecte les abonnés actuels.

   > [!TIP]  
   > Les modifications apportées à l’offre ne sont pas immédiatement visibles par l’utilisateur. Pour voir les modifications et afficher la nouvelle offre, les utilisateurs pourraient devoir se déconnecter et se reconnecter au portail de l’utilisateur.

   Dans l’écran de vue d’ensemble de l’offre, sélectionnez **État de l’accessibilité**. Choisissez l’état à utiliser (par exemple, **Public**), puis sélectionnez **Enregistrer**.

     ![Changer l’état de votre offre Azure Stack Hub](media/azure-stack-create-offer/change-stage-1807.png)

     Une autre possibilité consiste à sélectionner l’option**Changer l’état**, puis à choisir un état.

    ![Sélectionner l’état d’accessibilité de votre offre Azure Stack Hub](media/azure-stack-create-offer/change-stage-select-1807.png)

> [!NOTE]
> Vous pouvez également utiliser PowerShell pour créer des offres, des plans et des quotas par défaut. Pour plus d’informations, consultez l’article [Module Azure Stack Hub PowerShell 1.4.0](/powershell/azure/azure-stack/overview?view=azurestackps-1.4.0).
::: moniker-end

## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment modifier une offre et fournir à vos utilisateurs un plan additionnel, continuez avec [Créer un plan additionnel](create-add-on-plan.md) (facultatif)
- Sinon, passez à [S’abonner à une offre](azure-stack-subscribe-plan-provision-vm.md)