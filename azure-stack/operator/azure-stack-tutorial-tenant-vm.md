---
title: Mettre des machines virtuelles à la disposition de vos utilisateurs Azure Stack | Microsoft Docs
description: Découvrez comment rendre des machines virtuelles disponibles sur Azure Stack
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: unknown
ms.lastreviewed: 09/11/2018
ms.custom: mvc
ms.openlocfilehash: 3719019ebdbc6edad17c523a937d77d4f11bb02a
ms.sourcegitcommit: cf9440cd2c76cc6a45b89aeead7b02a681c4628a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/03/2019
ms.locfileid: "66469220"
---
# <a name="tutorial-make-virtual-machines-available-to-your-azure-stack-users"></a>Didacticiel : mettre des machines virtuelles à la disposition de vos utilisateurs Azure Stack

En tant qu’un administrateur de cloud Azure Stack, vous pouvez créer des offres auxquelles vos utilisateurs (parfois appelée « locataires ») peuvent s’abonner. En s’abonnant à une offre, les utilisateurs peuvent utiliser les services Azure Stack inclus dans cette offre.

Ce didacticiel montre comment créer une offre pour une machine virtuelle, puis se connecter en tant qu’utilisateur pour tester l’offre.

Contenu :

> [!div class="checklist"]
> * Créer une offre
> * Ajouter une image
> * Tester l’offre

Dans Azure Stack, les services sont fournis aux utilisateurs par le biais d’abonnements, d’offres et de plans. Les utilisateurs peuvent s’abonner à plusieurs offres. Les offres peuvent contenir un ou plusieurs plans, et un plan peut contenir un ou plusieurs services.

![Abonnements, offres et plans](media/azure-stack-key-features/image4.png)

Pour plus d’informations, consultez [Vue d’ensemble des plans, des offres, des quotas et des abonnements](azure-stack-plan-offer-quota-overview.md).

## <a name="create-an-offer"></a>Créer une offre

Les offres sont des groupes d’un ou plusieurs plans que les fournisseurs proposent à l’achat ou à l’abonnement aux utilisateurs. La création d’une offre se fait en plusieurs étapes. Vous êtes d’abord invité à créer l’offre, puis un plan, et enfin des quotas.

1. [Connectez-vous](../asdk/asdk-connect.md) au portail en tant qu’administrateur cloud, puis sélectionnez **+ Créer une ressource** > **Offres + Plans** > **Offre**.

   ![Nouvelle offre](media/azure-stack-tutorial-tenant-vm/image01.png)

1. Dans **Nouvelle offre**, renseignez le **Nom d’affichage** et le **Nom de la ressource**, puis sélectionnez un **Groupe de ressources** nouveau ou existant. Le nom d’affichage correspond au nom convivial de l’offre. Seul l’opérateur cloud peut voir le nom de la ressource qui est utilisé par les administrateurs pour travailler avec une offre comme ressource Azure Resource Manager.

   ![Nom complet](media/azure-stack-tutorial-tenant-vm/image02.png)

1. Sélectionnez **Plans de base**, puis dans la section **Plan**, sélectionnez **Ajouter** pour ajouter un nouveau plan à l’offre.

   ![Ajouter un plan](media/azure-stack-tutorial-tenant-vm/image03.png)

1. Dans la section **Nouveau plan**, renseignez le **Nom d’affichage** et le **Nom de la ressource**. Le nom d’affichage correspond au nom convivial du plan, que les utilisateurs voient. Seul l’opérateur cloud peut voir le nom de la ressource qui est utilisé par les opérateurs cloud pour travailler avec le plan comme ressource Azure Resource Manager.

   ![Nom d’affichage du plan](media/azure-stack-tutorial-tenant-vm/image04.png)

1. Sélectionnez **Services**. Dans la liste des services, sélectionnez **Microsoft.Compute**, **Microsoft.Network** et **Microsoft.Storage**. Cliquez sur **Sélectionner** pour ajouter ces services au plan.

   ![Services du plan](media/azure-stack-tutorial-tenant-vm/image05.png)

1. Cliquez sur **Quotas**, puis sélectionnez le premier service pour lequel vous voulez créer un quota. Pour un quota IaaS, utilisez l’exemple suivant comme guide pour configurer des quotas pour les services Calcul, Réseau et Stockage.

   - Commencez par créer un quota pour le service Calcul. Dans la liste d’espaces de noms, sélectionnez **Microsoft.Compute**, puis **Créer un quota**.

     ![Créer un quota](media/azure-stack-tutorial-tenant-vm/image06.png)

   - Sous **Créer un quota**, saisissez un nom pour le quota. Vous pouvez modifier ou accepter toutes les valeurs de quota qui s’affichent pour le quota que vous créez. Dans cet exemple, nous acceptons les paramètres par défaut et sélectionnons **OK**.

     ![Nom du quota](media/azure-stack-tutorial-tenant-vm/image07.png)

   - Sélectionnez **Microsoft.Compute** dans la liste d’espaces de noms, puis sélectionnez le quota que vous avez créé. Cette opération associe le quota au service de calcul.

     ![Sélectionner un quota](media/azure-stack-tutorial-tenant-vm/image08.png)

      Répétez ces étapes pour les services Réseau et Stockage. Lorsque vous avez terminé, sélectionnez **OK** dans **Quotas** pour enregistrer tous les quotas.

1. Sous **Nouveau plan**, sélectionnez **OK**.

1. Sous **Plan**, sélectionnez le nouveau plan, puis **Sélectionner**.

1. Sous **Nouvelle offre**, sélectionnez **Créer**. Une fois l’offre créée, une notification s’affiche.

1. Dans le menu du tableau de bord, sélectionnez **Offres**, puis cliquez sur l’offre que vous avez créée.

1. Sélectionnez **Changer l’état**, puis **Public**.

    ![État Public](media/azure-stack-tutorial-tenant-vm/image09.png)

## <a name="add-an-image"></a>Ajouter une image

Avant de pouvoir approvisionner des machines virtuelles, vous devez ajouter une image à la Place de marché Azure Stack. Vous pouvez ajouter l’image de votre choix, notamment des images Linux, à partir de la Place de marché Azure.

Si vous êtes dans un scénario connecté et que vous avez inscrit votre instance Azure Stack auprès d’Azure, vous pouvez télécharger l’image de machine virtuelle Windows Server 2016 à partir de la Place de marché Azure en suivant la procédure décrite dans la rubrique [Télécharger des éléments de la Place de marché à partir d’Azure dans Azure Stack](azure-stack-download-azure-marketplace-item.md).

Pour plus d’informations sur l’ajout de différents éléments à la Place de marché, consultez [Place de marché Azure Stack](azure-stack-marketplace.md).

## <a name="test-the-offer"></a>Tester l’offre

Maintenant que vous avez créé une offre, vous pouvez la tester. Vous allez vous connecter en tant qu’utilisateur, vous abonner à l’offre, puis ajouter une machine virtuelle.

1. **S’abonner à une offre**

   a. Connectez-vous au portail utilisateur avec un compte d’utilisateur et sélectionnez la vignette **Prendre un abonnement**.
   - Pour un système intégré, l’URL varie en fonction de la région et du nom de domaine externe de votre opérateur, et son format est https://portal.&lt;*région*&gt;.&lt;*FQDN*&gt;.
   - Si vous utilisez le Kit de développement Azure Stack, l’adresse du portail est https://portal.local.azurestack.external.

   ![Prendre un abonnement](media/azure-stack-tutorial-tenant-vm/image10.png)

   b. Sous **Prendre un abonnement**, saisissez un nom pour votre abonnement dans le champ **Nom d’affichage**. Sélectionnez **Offre**, puis choisissez une offre dans la liste **Choisir une offre**. Sélectionnez **Créer**.

   ![Créer une offre](media/azure-stack-tutorial-tenant-vm/image11.png)

   c. Pour voir l’abonnement, sélectionnez **Tous les services**, puis **Abonnements** sous la catégorie **GÉNÉRAL**. Sélectionnez votre nouvel abonnement pour voir les services qui en font partie.

   >[!NOTE]
   >Une fois que vous êtes abonné à une offre, vous devrez peut-être actualiser le portail pour voir les services qui font partie du nouvel abonnement.

1. **Approvisionner une machine virtuelle**

   À partir du portail utilisateur, vous pouvez approvisionner une machine virtuelle à l’aide du nouvel abonnement.

   a. Connectez-vous au portail utilisateur avec un compte d’utilisateur.
   - Pour un système intégré, l’URL varie en fonction de la région et du nom de domaine externe de votre opérateur, et son format est https://portal.&lt;*région*&gt;.&lt;*FQDN*&gt;.
     - Si vous utilisez le Kit de développement Azure Stack, l’adresse du portail est https://portal.local.azurestack.external.

   b.  Dans le tableau de bord, sélectionnez **+ Créer une ressource** > **Calcul** > **Windows Server 2016 Datacenter Evaluation**, puis sélectionnez **Créer**.

   c. Sous **Paramètres de base**, renseignez les informations suivantes :
      - Saisissez un **nom**
      - Saisissez un **nom d’utilisateur**
      - Saisissez un **mot de passe**
      - Sélectionnez un **Abonnement**
      - Créez ou sélectionnez un **groupe de ressources** 
      - Cliquez sur **OK** pour enregistrer ces informations.

   d. Sous **Choisir une taille**, sélectionnez **A1 Standard**, puis cliquez sur **Sélectionner**.  

   e. Sous **Paramètres**, sélectionnez **Réseau virtuel**.

   f. Sous **Choisir un réseau virtuel**, sélectionnez **Créer**.

   g. Sous **Créer un réseau virtuel**, acceptez toutes les valeurs par défaut, puis cliquez sur **OK**.

   h. Sélectionnez **OK** dans **Paramètres** pour enregistrer la configuration du réseau.

      i. Sous **Résumé**, sélectionnez **OK** pour créer la machine virtuelle.  

   j. Pour afficher la nouvelle machine virtuelle, sélectionnez **Toutes les ressources**. Recherchez la machine virtuelle et sélectionnez son nom dans les résultats de la recherche.

   
## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une offre
> * Ajouter une image
> * Tester l’offre

Passez au didacticiel suivant pour savoir comment :
> [!div class="nextstepaction"]
> [Mettre des bases de données SQL à la disposition de vos utilisateurs Azure Stack](azure-stack-tutorial-sql-server.md)
