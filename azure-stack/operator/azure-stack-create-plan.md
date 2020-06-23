---
title: Créer un plan dans Azure Stack Hub
description: Découvrez comment créer un plan dans Azure Stack Hub permettant aux abonnés d’approvisionner des machines virtuelles.
author: bryanla
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 06/11/2019
ms.openlocfilehash: c762f4c8c75fbfd6755c25877c52fbb68c88f7ca
ms.sourcegitcommit: 6306e0c2506106ad01ff50010f36466f3325d0a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84631118"
---
# <a name="create-a-plan-in-azure-stack-hub"></a>Créer un plan dans Azure Stack Hub

Les [plans Azure Stack Hub](azure-stack-overview.md) regroupent un ou plusieurs services et leurs quotas. En tant que fournisseur, vous pouvez créer des plans à proposer à vos utilisateurs. En retour, ceux-ci s’abonnent à vos offres pour utiliser les plans, les services et les quotas qu’elles comprennent. Cet exemple montre comment créer un plan comprenant les fournisseurs de ressources de stockage, réseau et de calcul. Ce plan donne aux abonnés la possibilité d’approvisionner des machines virtuelles.

::: moniker range=">=azs-1902"
## <a name="create-a-plan-1902-and-later"></a>Créer un plan (1902 et versions ultérieures)

1. Connectez-vous au portail administrateur Azure Stack Hub `https://adminportal.local.azurestack.external`.

2. Pour créer un plan et une offre auxquels les utilisateurs peuvent s’abonner, sélectionnez **+ Créer une ressource**, puis **Offres + plans** et **Plan**.
  
   ![Sélectionner un plan dans le portail d'administration Azure Stack Hub](media/azure-stack-create-plan/select-plan.png)

3. Une interface utilisateur à onglets s’affiche qui vous permet de spécifier le nom du plan, d’ajouter des services et de définir les quotas pour chaque service sélectionné. Plus important encore, vous pouvez consulter les détails de l’offre que vous créez, avant de décider de la créer.

   Sous l’onglet **Concepts de base** de la fenêtre **Nouveau plan**, entrez un **Nom d’affichage** et un **Nom de ressource**. Le nom d’affichage correspond au nom convivial du plan tel qu’il s’affiche pour les opérateurs. Dans le portail d’administration, seuls les opérateurs peuvent voir les détails du plan.

   ![Spécifier les détails d’un nouveau plan dans Azure Stack Hub](media/azure-stack-create-plan/plan-name.png)

4. Créez un **Groupe de ressources** ou sélectionnez-en un qui servira de conteneur pour le plan.

   ![Spécifier le groupe de ressources d’un nouveau plan dans Azure Stack Hub](media/azure-stack-create-plan/resource-group.png)

5. Sélectionnez l’onglet **Services** ou cliquez sur le bouton **Suivant : Services >** , puis cochez la case pour **Microsoft.Compute**, **Microsoft.Network** et **Microsoft.Storage**.
  
   ![Sélectionner les services d’un nouveau plan dans Azure Stack Hub](media/azure-stack-create-plan/services.png)

6. Sélectionnez l’onglet **Quotas** ou cliquez sur le bouton **Suivant : Quotas >** . En regard de **Microsoft.Storage**, choisissez le quota par défaut dans la liste déroulante ou sélectionnez **Créer un quota** pour créer un quota personnalisé.
  
   ![Spécifier les quotas d’un nouveau plan dans Azure Stack Hub](media/azure-stack-create-plan/quotas.png)

7. Si vous créez un quota, entrez un **Nom** pour le quota, puis spécifiez ses valeurs. Sélectionnez **OK** pour créer le quota.

   ![Créer un quota pour un nouveau plan dans Azure Stack Hub](media/azure-stack-create-plan/new-quota.png)

8. Répétez les étapes 6 et 7 pour créer et assigner des quotas pour **Microsoft.Network** et **Microsoft.Compute**. Une fois que des quotas ont été assignés aux trois services, ces derniers ressemblent à l’exemple suivant.

   ![Terminer l'affectation des quotas d’un nouveau plan dans Azure Stack Hub](media/azure-stack-create-plan/all-quotas-assigned.png)

9. Sélectionnez **Examiner + créer** pour passer en revue le plan. Passez en revue toutes les valeurs et les quotas pour vous assurer qu’ils sont corrects. L’interface vous permet d’étendre les quotas dans les plans choisis, un à la fois, pour afficher les détails de chaque quota dans un plan. Vous pouvez également revenir en arrière pour effectuer toutes les modifications nécessaires.

   ![Créer le plan dans Azure Stack Hub](media/azure-stack-create-plan/create.png)

10. Quand vous êtes prêt, sélectionnez **Créer** pour créer le plan.

11. Pour afficher le nouveau plan, sur le côté gauche, cliquez sur **Tous les services**, sélectionnez **Plans**, puis recherchez le plan et sélectionnez son nom. Si votre liste de ressources est longue, utilisez **Rechercher** pour retrouver votre plan par nom.
::: moniker-end

::: moniker range="<=azs-1901"
## <a name="create-a-plan-1901-and-earlier"></a>Créer un plan (1901 et versions antérieures)

1. Connectez-vous au portail administrateur Azure Stack Hub `https://adminportal.local.azurestack.external`.

2. Pour créer un plan et une offre auxquels les utilisateurs peuvent s’abonner, sélectionnez **+ Nouveau**, puis **Offres + plans** et **Plan**.
  
   ![Sélectionner un plan dans le portail d'administration Azure Stack Hub](media/azure-stack-create-plan/select-plan1901.png)

3. Sous **Nouveau plan**, entrez un **nom d’affichage** et un **nom de la ressource**. Le nom d’affichage correspond au nom convivial du plan tel qu’il s’affiche pour les utilisateurs. Seul l’administrateur peut voir le nom de la ressource, dont les administrateurs se servent pour utiliser le plan comme une ressource Azure Resource Manager.

   ![Spécifier les détails d’un nouveau plan dans Azure Stack Hub](media/azure-stack-create-plan/plan-name1901.png)

4. Créez un **Groupe de ressources** ou sélectionnez-en un qui servira de conteneur pour le plan.

   ![Spécifier le groupe de ressources d’un nouveau plan dans Azure Stack Hub](media/azure-stack-create-plan/resource-group1901.png)

5. Sélectionnez **Services**, puis cochez la case pour **Microsoft.Compute**, **Microsoft.Network** et **Microsoft.Storage**. Choisissez ensuite **Sélectionner** pour enregistrer la configuration. Les cases à cocher s’affichent lorsque la souris pointe sur chaque option.
  
   ![Sélectionner les services d’un nouveau plan dans Azure Stack Hub](media/azure-stack-create-plan/services1901.png)

6. Sélectionnez **Quotas**, **Microsoft.Storage (local)** , puis choisissez le quota par défaut ou sélectionnez **Créer un quota** pour créer un quota personnalisé.
  
   ![Spécifier les quotas d’un nouveau plan dans Azure Stack Hub](media/azure-stack-create-plan/quotas1901.png)

7. Si vous créez un quota, entrez un **Nom** pour le quota > spécifiez ses valeurs > sélectionnez **OK**. La boîte de dialogue **Créer un quota** se ferme.

   ![Créer un quota pour un nouveau plan dans Azure Stack Hub](media/azure-stack-create-plan/new-quota1901.png)

   Vous sélectionnez ensuite le nouveau quota que vous avez créé. La sélection du quota permet de l’assigner et ferme la boîte de dialogue de sélection.
  
   ![Attribuer le quota pour un nouveau plan dans Azure Stack Hub](media/azure-stack-create-plan/assign-quota1901.png)

8. Répétez les étapes 6 et 7 pour créer et assigner des quotas pour **Microsoft.Network (local)** et **Microsoft.Compute (local)** . Une fois que des quotas ont été assignés aux trois services, ces derniers ressemblent à l’exemple suivant.

   ![Terminer l'affectation des quotas d’un nouveau plan dans Azure Stack Hub](media/azure-stack-create-plan/all-quotas-assigned1901.png)

9. Sous **Quotas**, choisissez **OK** puis, sous **Nouveau plan**, choisissez **Créer** pour créer le plan.

    ![Créer le plan dans Azure Stack Hub](media/azure-stack-create-plan/create1901.png)

10. Pour voir votre nouveau plan, sélectionnez **Toutes les ressources**, puis recherchez le plan et sélectionnez son nom. Si votre liste de ressources est longue, utilisez **Rechercher** pour retrouver votre plan par nom.

    ![Vérifier le nouveau plan dans Azure Stack Hub](media/azure-stack-create-plan/plan-overview1901.png)
::: moniker-end

## <a name="next-steps"></a>Étapes suivantes

* [Créer une offre](azure-stack-create-offer.md)
