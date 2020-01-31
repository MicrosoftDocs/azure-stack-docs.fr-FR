---
title: Créer un abonnement avec une offre dans Azure Stack Hub
description: Apprenez à créer un nouvel abonnement avec une offre dans Azure Stack Hub, puis testez l’offre avec une machine virtuelle de test.
author: bryanla
ms.topic: tutorial
ms.date: 06/04/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 11/13/2018
ms.openlocfilehash: ea0bf7287fd0463aa7b57a24386a30f7f3a1b754
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76884326"
---
# <a name="tutorial-create-and-test-a-subscription-in-azure-stack-hub"></a>Tutoriel : Créer et tester un abonnement dans Azure Stack Hub

Ce tutoriel montre comment créer un abonnement qui contient une offre et comment la tester. Pour le test, connectez-vous au portail utilisateur Azure Stack Hub en tant qu’administrateur cloud, abonnez-vous à l’offre, puis créez une machine virtuelle.

> [!TIP]
> Pour une expérience d’évaluation plus avancée, vous pouvez [créer un abonnement pour un utilisateur particulier](../operator/azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator), puis vous connecter en tant que cet utilisateur dans le portail utilisateur.

Dans ce tutoriel, vous allez apprendre à vous abonner à une offre Azure Stack Hub.

Ce que vous allez apprendre :

> [!div class="checklist"]
> * S’abonner à une offre 
> * Tester l’offre

## <a name="subscribe-to-an-offer"></a>S’abonner à une offre

Pour vous abonner à une offre en tant qu’utilisateur, connectez-vous au portail utilisateur Azure Stack Hub afin de découvrir les services offerts par l’opérateur Azure Stack Hub.

1. Connectez-vous au portail utilisateur et sélectionnez **Obtenir un abonnement**.

   ![Prendre un abonnement](media/azure-stack-subscribe-services/get-subscription.png)

2. Dans le champ **Nom d’affichage** , tapez un nom pour votre abonnement. Ensuite, sélectionnez **Offre** pour choisir l’une des offres disponibles dans la section **Choisir une offre**. Sélectionnez ensuite **Créer**.

   ![Créer une offre](media/azure-stack-subscribe-services/create-subscription.png)

   > [!TIP]
   > Actualisez le portail utilisateur pour commencer à utiliser votre abonnement.

3. Pour afficher l’abonnement que vous avez créé, sélectionnez **Tous les services**. Ensuite, dans la catégorie **GÉNÉRAL**, sélectionnez **Abonnements**, puis sélectionnez votre nouvel abonnement. Une fois que vous êtes abonné à une offre, actualisez le portail pour voir si les nouveaux services ont été ajoutés à votre nouvel abonnement. Dan cet exemple, **Machines virtuelles** a été ajouté.

   ![Afficher l’abonnement](media/azure-stack-subscribe-services/view-subscription.png)

## <a name="test-the-offer"></a>Tester l’offre

En étant connecté au portail utilisateur, testez l’offre en provisionnant une machine virtuelle à l’aide des fonctionnalités du nouvel abonnement.

> [!NOTE]
> Ce test nécessite d’avoir ajouté préalablement une machine virtuelle Windows Server 2016 Datacenter à la Place de marché Azure Stack Hub.

1. Connectez-vous au portail utilisateur.

2. Dans le portail utilisateur, sélectionnez **Machines virtuelles**, **Ajouter**, **Windows Server 2016 Datacenter**, puis sélectionnez **Créer**.

3. Dans la section **Bases**, tapez un **Nom**, un **Nom d’utilisateur** et un **Mot de passe**, choisissez un **Abonnement**, créez un **Groupe de ressources** (ou sélectionnez un groupe existant), puis sélectionnez **OK**.

4. Dans la section **Choisir une taille**, sélectionnez **A1 Standard**, puis choisissez **Sélectionner**.  

5. Dans le panneau **Paramètres**, acceptez les valeurs par défaut et sélectionnez **OK**.

6. Dans la section **Résumé**, sélectionnez **OK** pour créer la machine virtuelle.  

7. Pour afficher votre nouvelle machine virtuelle, sélectionnez **Machines virtuelles**, puis recherchez la nouvelle machine virtuelle et sélectionnez son nom.

    ![Toutes les ressources](media/azure-stack-subscribe-services/view-vm.png)

> [!NOTE]
> Le déploiement de la machine virtuelle prend quelques minutes.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une machine virtuelle à partir d’un modèle de communauté](azure-stack-create-vm-template.md)
