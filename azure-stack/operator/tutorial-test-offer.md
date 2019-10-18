---
title: Testez une offre de services Azure Stack.
description: Découvrez comment tester une offre de service en créant un abonnement et en déployant des ressources.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: tutorial
ms.date: 10/13/2019
ms.reviewer: shriramnat
ms.lastreviewed: 10/06/2019
ms.openlocfilehash: 63c8e37c19b46f5cabe197dd55875e9bcbd5cb12
ms.sourcegitcommit: d159652f50de7875eb4be34c14866a601a045547
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286823"
---
# <a name="tutorial-test-a-service-offering"></a>Didacticiel : Tester une offre de service

Dans le didacticiel précédent, vous avez créé une offre pour les utilisateurs. Ce didacticiel vous montre comment tester cette offre, en l’utilisant pour créer un abonnement. Vous créez et déployez ensuite des ressources sur les services de base qui sont autorisés par l’abonnement.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Création d’un abonnement
> * Création et déploiement de ressources

## <a name="prerequisites"></a>Prérequis

Avant de commencer ce didacticiel, vous devez remplir les conditions préalables suivantes :

- Suivez le didacticiel [Offrir un service aux utilisateurs](tutorial-offer-services.md). Dans ce didacticiel, vous allez apprendre à créer l’offre utilisée à titre d’exemple.

- L’offre à laquelle vous êtes abonné dans ce didacticiel permet le déploiement d’une ressource de machine virtuelle. Pour tester le déploiement de machine virtuelle, vous devez publier une image de machine virtuelle sur la Place de marché Azure Stack, en la téléchargeant depuis la Place de marché Azure. Pour plus de détails, consultez [Télécharger des éléments de la Place de marché à partir d’Azure dans Azure Stack](azure-stack-download-azure-marketplace-item.md). 

## <a name="subscribe-to-the-offer"></a>S’abonner à l’offre

1. Se connecter au portail utilisateur avec un compte d’utilisateur 

   - Pour un système intégré, l’URL varie en fonction de la région et du nom de domaine externe de votre opérateur, dans le format https://portal.&lt;*région*&gt;.&lt;*FQDN*&gt;.
   - Si vous utilisez le Kit de développement Azure Stack, l’adresse du portail est https://portal.local.azurestack.external.

1. Sélectionnez la vignette **Prendre un abonnement**.

   ![Prendre un abonnement](media/tutorial-test-offer/1-get-subscription.png)

1. Sous **Prendre un abonnement**, saisissez un nom pour votre nouvel abonnement dans le champ **Nom d’affichage**. Sélectionnez **Offre**, puis choisissez l'offre que vous avez créée dans le didacticiel précédent, dans la liste **Choisir une offre**. Sélectionnez **Create** (Créer).

   ![Créer une offre](media/tutorial-test-offer/2-create-subscription.png)

1. Pour voir l’abonnement, sélectionnez **Tous les services**, puis **Abonnements** sous la catégorie **GÉNÉRAL**. Sélectionnez votre nouvel abonnement pour afficher l’offre à laquelle il est associé, ainsi que ses propriétés.

   >[!NOTE]
   >Une fois que vous êtes abonné à une offre, vous devrez peut-être actualiser le portail pour voir les services qui font partie du nouvel abonnement.

::: moniker range=">=azs-1902"
## <a name="deploy-a-storage-account-resource"></a>Déployer une ressource d’un compte de stockage

À partir du portail utilisateur, vous approvisionnez un compte de stockage à l’aide du nouvel abonnement que vous avez créé à la section précédente.

1. Connectez-vous au portail utilisateur avec un compte d’utilisateur.

1. Sélectionnez **+Créer une ressource** > **Données + Stockage** > **Compte de stockage - blob, fichier, table, file d’attente**.

1. Dans le volet **Créer un compte de stockage**, indiquez les informations suivantes :
  
   - Saisissez un **nom**
   - Sélectionnez votre nouvel **Abonnement**
   - Sélectionnez un **Groupe de ressources** (ou créez-en un). 
   - Sélectionnez **Créer** pour créer le compte de stockage.

1. Une fois le déploiement démarré, vous revenez au tableau de bord. Pour afficher le nouveau compte de stockage, sélectionnez **Toutes les ressources**. Recherchez le compte de stockage et sélectionnez son nom dans les résultats de la recherche. À partir de là, vous pouvez gérer le compte de stockage et son contenu.

## <a name="deploy-a-virtual-machine-resource"></a>Déployez une ressource de machine virtuelle

À partir du portail utilisateur, vous approvisionnez une machine virtuelle à l’aide du nouvel abonnement que vous avez créé à la section précédente.

1. Connectez-vous au portail utilisateur avec un compte d’utilisateur.

1. Sélectionnez **+Créer une ressource** > **Calculer** > **\<nom-image\>** , où « nom-image » est le nom de la machine virtuelle que vous avez téléchargée préalablement.
1. Sous **Créer une machine virtuelle** / **Paramètres de base**, fournissez les informations suivantes :
  
   - Entrez un **Nom** pour la machine virtuelle.
   - Entrez un **Nom d'utilisateur** pour le compte administrateur.
   - Pour les machines virtuelles Linux, sélectionnez « Mot de passe » comme **Type d'authentification**.
   - Entrez la même chaîne de caractères dans **Mot de passe** et **Confirmer le mot de passe** pour le compte administrateur.
   - Sélectionnez votre nouvel **Abonnement**.
   - Sélectionnez un **Groupe de ressources** (ou créez-en un). 
   - Sélectionnez **OK** pour valider ces informations et poursuivre.

1. Dans **Choisir une taille**, filtrez la liste si nécessaire, sélectionnez une référence de machine virtuelle, puis choisissez **Sélectionner**.  
1. Dans **Paramètres**, spécifiez le(s) port(s) à ouvrir sous **Sélectionner des ports d'entrée publics**, puis sélectionnez **OK**.
   > [!NOTE]
   > La sélection de « RDP(3389) », par exemple, vous permet de vous connecter à distance à la machine virtuelle lorsqu’elle fonctionne.
1. Sous **Résumé**, passez en revue vos choix, puis sélectionnez **OK** pour créer la machine virtuelle.  
1. Une fois le déploiement démarré, vous revenez au tableau de bord. Pour afficher la nouvelle machine virtuelle, sélectionnez **Toutes les ressources**. Recherchez la machine virtuelle et sélectionnez son nom dans les résultats de la recherche. À partir de là, vous pouvez accéder à la machine virtuelle et la gérer.
   > [!NOTE]
   > Le déploiement complet et le démarrage de la machine virtuelle peuvent prendre plusieurs minutes. Une fois prête à l’utilisation, la machine passe à l’[état](/azure/virtual-machines/windows/states-lifecycle) « En cours d’exécution ».

::: moniker-end

::: moniker range="<=azs-1901"
## <a name="deploy-a-virtual-machine-resource-1901-and-earlier"></a>Déployez une ressource de machine virtuelle (1901 et versions antérieures)

À partir du portail utilisateur, vous approvisionnez une machine virtuelle à l’aide du nouvel abonnement.

1. Connectez-vous au portail utilisateur avec un compte d’utilisateur.

1. Dans le tableau de bord, sélectionnez **+Créer une ressource** > **Calcul** > **Windows Server 2016 Datacenter Evaluation**, puis sélectionnez **Créer**.

1. Sous **Paramètres de base**, renseignez les informations suivantes :
  
   - Saisissez un **nom**
   - Saisissez un **nom d’utilisateur**
   - Saisissez un **mot de passe**
   - Choisissez votre nouvel **Abonnement**
   - Créez ou sélectionnez un **groupe de ressources** 
   - Cliquez sur **OK** pour enregistrer ces informations.

1. Sous **Choisir une taille**, sélectionnez **A1 Standard**, puis cliquez sur **Sélectionner**.  
1. Sous **Paramètres**, sélectionnez **Réseau virtuel**.

1. Sous **Choisir un réseau virtuel**, sélectionnez **Créer**.

1. Sous **Créer un réseau virtuel**, acceptez toutes les valeurs par défaut, puis cliquez sur **OK**.

1. Sélectionnez **OK** dans **Paramètres** pour enregistrer la configuration du réseau.

1. Sous **Résumé**, sélectionnez **OK** pour créer la machine virtuelle.  

1. Pour afficher la nouvelle machine virtuelle, sélectionnez **Toutes les ressources**. Recherchez la machine virtuelle et sélectionnez son nom dans les résultats de la recherche.
::: moniker-end

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Création d’un abonnement
> * Création et déploiement de ressources 

Découvrez ensuite comment déployer des fournisseurs de ressources pour les services complémentaires. Ils vous permettent d’offrir encore plus de services aux utilisateurs de vos offres :

- [Offre SQL sur Azure Stack](azure-stack-sql-resource-provider.md)
- [Offre MySQL sur Azure Stack](azure-stack-mysql-resource-provider.md)
- [Offre App Service sur Azure Stack](azure-stack-app-service-overview.md)
