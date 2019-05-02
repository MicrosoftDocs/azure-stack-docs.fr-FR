---
title: Créer un plan dans Azure Stack | Microsoft Docs
description: En tant qu’administrateur cloud, créez un plan permettant aux abonnés d’approvisionner des machines virtuelles.
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
origin.date: 03/07/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: efemmano
ms.lastreviewed: 03/07/2019
ms.openlocfilehash: 85c6fd58108653ba4876d8aa7802d5674ae1336c
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "64311237"
---
# <a name="create-a-plan-in-azure-stack"></a>Créer un plan dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Les [plans](azure-stack-overview.md) regroupent un ou plusieurs services et leurs quotas. En tant que fournisseur, vous pouvez créer des plans à proposer à vos utilisateurs. En retour, ceux-ci s’abonnent à vos offres pour utiliser les plans, les services et les quotas qu’elles comprennent. Cet exemple montre comment créer un plan comprenant les fournisseurs de ressources de stockage, réseau et de calcul. Ce plan donne aux abonnés la possibilité d’approvisionner des machines virtuelles.

## <a name="create-a-plan-1902-and-later"></a>Créer un plan (1902 et versions ultérieures)

1. Connectez-vous au [portail d’administration Azure Stack](https://adminportal.local.azurestack.external).

2. Pour créer un plan et une offre auxquels les utilisateurs peuvent s’abonner, sélectionnez **+ Créer une ressource**, puis **Offres + plans** et **Plan**.
  
   ![Sélectionner un plan](media/azure-stack-create-plan/select-plan.png)

3. Une interface utilisateur à onglets s’affiche qui vous permet de spécifier le nom du plan, d’ajouter des services et de définir les quotas pour chaque service sélectionné. Plus important encore, vous pouvez consulter les détails de l’offre que vous créez, avant de décider de la créer.

   Sous l’onglet **Concepts de base** de la fenêtre **Nouveau plan**, entrez un **Nom d’affichage** et un **Nom de ressource**. Le nom d’affichage correspond au nom convivial du plan tel qu’il s’affiche pour les opérateurs. Notez que dans le portail d’administration, seuls les opérateurs peuvent voir les détails du plan.

   ![Spécifier les détails](media/azure-stack-create-plan/plan-name.png)

4. Créez un **Groupe de ressources** ou sélectionnez-en un qui servira de conteneur pour le plan.

   ![Spécifier le groupe de ressources](media/azure-stack-create-plan/resource-group.png)

5. Sélectionnez l’onglet **Services**, puis cochez la case pour **Microsoft.Compute**, **Microsoft.Network** et **Microsoft.Storage**.
  
   ![Sélectionner des services](media/azure-stack-create-plan/services.png)

6. Sélectionnez l’onglet **Quotas**. En regard de **Microsoft.Storage**, choisissez le quota par défaut dans la liste déroulante ou sélectionnez **Créer un quota** pour créer un quota personnalisé.
  
   ![Quotas](media/azure-stack-create-plan/quotas.png)

7. Si vous créez un quota, entrez un **Nom** pour le quota, puis spécifiez ses valeurs. Sélectionnez **OK** pour créer le quota.

   ![Nouveau quota](media/azure-stack-create-plan/new-quota.png)

8. Répétez les étapes 6 et 7 pour créer et assigner des quotas pour **Microsoft.Network** et **Microsoft.Compute**. Une fois que des quotas ont été assignés aux trois services, ces derniers ressemblent à l’exemple suivant.

   ![Affectations de quota terminées](media/azure-stack-create-plan/all-quotas-assigned.png)

9. Sélectionnez **Examiner + créer** pour passer en revue le plan. Passez en revue toutes les valeurs et les quotas pour vous assurer qu’ils sont corrects. Notez les flèches d’expansion à gauche de chaque paire service/quota. Une nouvelle fonctionnalité vous permet d’étendre les quotas dans les plans sélectionnés, un à la fois, pour afficher les détails de chaque quota dans un plan et revenir en arrière pour effectuer toutes les modifications nécessaires.

   ![Créer le plan](media/azure-stack-create-plan/create.png)

10. Quand vous êtes prêt, sélectionnez **Créer** pour créer le plan.

11. Pour voir le nouveau plan, sélectionnez **Plans**, puis recherchez le plan et sélectionnez son nom. Si votre liste de ressources est longue, utilisez **Rechercher** pour retrouver votre plan par nom.

## <a name="create-a-plan-1901-and-earlier"></a>Créer un plan (1901 et versions antérieures)

1. Connectez-vous au [portail d’administration Azure Stack](https://adminportal.local.azurestack.external).

2. Pour créer un plan et une offre auxquels les utilisateurs peuvent s’abonner, sélectionnez **+ Créer une ressource**, puis **Offres + plans** et **Plan**.
  
   ![Sélectionner un plan](media/azure-stack-create-plan/select-plan1901.png)

3. Sous **Nouveau plan**, entrez un **nom d’affichage** et un **nom de la ressource**. Le nom d’affichage correspond au nom convivial du plan tel qu’il s’affiche pour les utilisateurs. Seul l’administrateur peut voir le nom de la ressource, dont les administrateurs se servent pour utiliser le plan comme une ressource Azure Resource Manager.

   ![Spécifier les détails](media/azure-stack-create-plan/plan-name1901.png)

4. Créez un **Groupe de ressources** ou sélectionnez-en un qui servira de conteneur pour le plan.

   ![Spécifier le groupe de ressources](media/azure-stack-create-plan/resource-group1901.png)

5. Sélectionnez **Services**, puis cochez la case pour **Microsoft.Compute**, **Microsoft.Network** et **Microsoft.Storage**. Choisissez ensuite **Sélectionner** pour enregistrer la configuration. Les cases à cocher s’affichent lorsque la souris pointe sur chaque option.
  
   ![Sélectionner des services](media/azure-stack-create-plan/services1901.png)

6. Sélectionnez **Quotas**, **Microsoft.Storage (local)**, puis choisissez le quota par défaut ou sélectionnez **Créer un quota** pour créer un quota personnalisé.
  
   ![Quotas](media/azure-stack-create-plan/quotas1901.png)

7. Si vous créez un quota, entrez un **Nom** pour le quota > spécifiez ses valeurs > sélectionnez **OK**. La boîte de dialogue **Créer un quota** se ferme.

   ![Nouveau quota](media/azure-stack-create-plan/new-quota1901.png)

   Vous sélectionnez ensuite le nouveau quota que vous avez créé. La sélection du quota permet de l’assigner et ferme la boîte de dialogue de sélection.
  
   ![Assigner le quota](media/azure-stack-create-plan/assign-quota1901.png)

8. Répétez les étapes 6 et 7 pour créer et assigner des quotas pour **Microsoft.Network (local)** et **Microsoft.Compute (local)**. Une fois que des quotas ont été assignés aux trois services, ces derniers ressemblent à l’exemple suivant.

   ![Affectations de quota terminées](media/azure-stack-create-plan/all-quotas-assigned1901.png)

9. Sous **Quotas**, choisissez **OK** puis, sous **Nouveau plan**, choisissez **Créer** pour créer le plan.

    ![Créer le plan](media/azure-stack-create-plan/create1901.png)

10. Pour voir votre nouveau plan, sélectionnez **Toutes les ressources**, puis recherchez le plan et sélectionnez son nom. Si votre liste de ressources est longue, utilisez **Rechercher** pour retrouver votre plan par nom.

    ![Vérifier le plan](media/azure-stack-create-plan/plan-overview1901.png)

## <a name="next-steps"></a>Étapes suivantes

* [Créer une offre](azure-stack-create-offer.md)

<!-- Update_Description: wording update -->