---
title: Créer une offre de service pour les utilisateurs
titleSuffix: Azure Stack Hub
description: Découvrez comment créer une offre de services dans Azure Stack Hub en utilisant des offres, des plans et des services.
author: BryanLa
ms.author: bryanla
ms.topic: tutorial
ms.date: 10/16/2019
ms.reviewer: shriramnat
ms.lastreviewed: 10/16/2019
ms.openlocfilehash: 9aa6104e3f6a93d55db82d4bd9ae21ef54601bf2
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76884173"
---
# <a name="create-a-service-offering-for-users-in-azure-stack-hub"></a>Créer une offre de service pour les utilisateurs dans Azure Stack Hub

Ce tutoriel a pour but de montrer à un opérateur comment créer une offre. Une offre met les services à la disposition des utilisateurs dans le cadre d’un abonnement. Une fois abonné, un utilisateur est autorisé à créer et à déployer des ressources dans les services spécifiés par l’offre.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer une offre.
> * Créer un plan.
> * Affecter des services et des quotas à un plan.
> * Affecter un plan à une offre.

## <a name="overview"></a>Vue d’ensemble

Une offre est constituée d’un ou de plusieurs plans. Une offre donne accès à un ou plusieurs services, en spécifiant le fournisseur de ressources correspondant à chaque service ainsi qu’un quota. Les plans peuvent être ajoutés à une offre en tant que plan de base ou étendre l’offre en tant que plan complémentaire. Pour plus d’informations, consultez [Vue d’ensemble des services, des plans, des offres et des abonnements](service-plan-offer-subscription-overview.md).

![Abonnements, offres et plans dans Azure Stack Hub](media/azure-stack-key-features/image4.png)

### <a name="resource-providers"></a>Fournisseurs de ressources

Un fournisseur de ressources prend en charge la création, le déploiement et la gestion de ses ressources en tant que services. Parmi les exemples courants, on peut citer le fournisseur de ressources Microsoft.Compute qui offre la possibilité de créer et de déployer des machines virtuelles. Consultez [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview) pour une présentation du modèle de gestion des ressources Azure.

Dans Azure Stack Hub, il existe deux catégories générales de fournisseurs de ressources : celles qui déploient des ressources en tant que services fondamentaux, et celles qui déploient des services à valeur ajoutée.

### <a name="foundational-services"></a>Services fondamentaux

>[!NOTE]
> Dans ce tutoriel, vous allez apprendre à créer une offre basée sur des services fondamentaux. 

Les services fondamentaux sont pris en charge par les fournisseurs de ressources suivants, qui sont disponibles en mode natif avec chaque installation d’Azure Stack Hub :

| Fournisseur de ressources | Exemples de ressources |
| ----------------- | ------------------|
| Microsoft.Compute | Machines virtuelles, disques, groupes de machines virtuelles identiques |
| Microsoft.KeyVault | Coffres de clés, secrets |
| Microsoft.Network | Réseaux virtuels, adresses IP publiques, équilibreurs de charge |
| Microsoft.Storage | Comptes de stockage, objets BLOB, files d’attente, tables |

### <a name="value-add-services"></a>Services à valeur ajoutée

>[!NOTE]
> Afin d’offrir un service à valeur ajoutée, le fournisseur de ressources correspondant doit d’abord être installé dans la Place de marché Azure Stack Hub. Une fois installées, ses ressources sont proposées aux utilisateurs de la même façon que les services de base. Reportez-vous à la section **Guides pratiques** de la table des matières pour la liste des fournisseurs de ressources acceptant des offres de services à valeur ajoutée.

Les services à valeur ajoutée sont pris en charge par les fournisseurs de ressources installés après le déploiement d’Azure Stack Hub. Voici quelques exemples :

| Fournisseur de ressources | Exemples de ressources |
| ----------------- | ------------------------- |
| Microsoft.Web | Applications de fonction App Service, applications web, applications API | 
| Microsoft.MySqlAdapter | Serveur d’hébergement MySQL, base de données MySQL | 
| Microsoft.SqlAdapter | Serveur d’hébergement SQL Server, base de données SQL Server |

::: moniker range=">=azs-1902"

## <a name="create-an-offer"></a>Créer une offre

Pendant le processus de création de l’offre, vous créez à la fois une offre et un plan. Le plan est utilisé comme plan de base de l’offre. Pendant la création du plan, vous spécifiez les services mis à disposition dans le plan et leurs quotas respectifs.

1. Connectez-vous au portail administrateur avec un compte admin cloud.

    - Pour un système intégré, l’URL varie en fonction de la région et du nom de domaine externe de votre opérateur. L’URL utilise le format `https://adminportal.<region>.<FQDN>`.
    - Si vous utilisez le Kit de développement Azure Stack, l’URL est `https://adminportal.local.azurestack.external`.

    Sélectionnez ensuite **+ Créer une ressource** > **Offres + plans** > **Offre**.

    ![Nouvelle offre dans le portail d’administration Azure Stack Hub](media/tutorial-offer-services/1-create-resource-offer.png)

1. Dans **Créer une offre** sous l'onglet **Concepts de base**, entrez un **Nom d'affichage**, **Nom de ressource**, et sélectionnez ou créez un **Groupe de ressources**. Le nom d’affichage correspond au nom convivial de l’offre. Seul l’opérateur cloud peut voir le nom de la ressource qui est utilisé par les administrateurs pour travailler avec une offre comme ressource Azure Resource Manager.

   ![Nom d’affichage dans le portail d’administration Azure Stack Hub](media/tutorial-offer-services/2-create-new-offer.png)

1. Sélectionnez l’onglet **Plans de base**, puis **Créer un plan** pour créer un nouveau plan. Le plan est également ajouté à l’offre en tant que plan de base.

   ![Ajouter un plan dans le portail d’administration Azure Stack Hub](media/tutorial-offer-services/3-create-new-offer-base-plans.png)

1. Dans **Nouveau plan** sous l'onglet **Concepts de base**, entrez un **Nom d'affichage** et un **Nom de ressource**. Le nom d’affichage correspond au nom convivial du plan, que les utilisateurs voient. Seul l’opérateur cloud peut voir le nom de la ressource qui est utilisé par les opérateurs cloud pour travailler avec le plan comme ressource Azure Resource Manager. **Groupe de ressources** sera défini sur le groupe de ressources spécifié pour l’offre.

   ![Nom d’affichage des plans dans le portail d’administration Azure Stack Hub](media/tutorial-offer-services/4-create-new-plan-basics.png)

1. Sélectionnez l'onglet **Services** : la liste des services disponibles auprès des fournisseurs de ressources installés s'affiche. Sélectionnez **Microsoft.Compute**, **Microsoft.Network** et **Microsoft.Storage**.

   ![Services des plans dans le portail d’administration Azure Stack Hub](media/tutorial-offer-services/5-create-new-plan-services.png)

1. Sélectionnez l'onglet **Quotas** et la liste des services que vous avez activés pour ce plan apparaît. Sélectionnez **Créer nouveau** pour spécifier un quota personnalisé pour **Microsoft.Compute**. **Nom du quota** est obligatoire ; vous pouvez accepter ou modifier chaque valeur de quota. Sélectionnez **OK** lorsque vous avez terminé, puis répétez ces étapes pour les autres services.

   ![Créer un quota de calcul dans le portail d’administration Azure Stack Hub](media/tutorial-offer-services/6-create-new-plan-quotas.png)

1. Sélectionnez l’onglet **Vérifier + créer**. Vous devez voir une bannière verte « Validation réussie » en haut, indiquant que le nouveau plan de base est prêt à être créé. Sélectionnez **Create** (Créer). Vous devez également voir une notification indiquant que le plan a été créé.

   ![Créer un plan dans le portail d’administration Azure Stack Hub](media/tutorial-offer-services/7-create-new-plan-review-create.png)

1. De retour dans l’onglet **Plans de base** de la page **Créer une offre**, vous pouvez constater que le plan a été créé. Assurez-vous que le nouveau plan est sélectionné pour inclusion dans l'offre comme plan de base, puis sélectionnez **Vérifier + créer**.

   ![Ajouter un plan de base dans le portail d’administration Azure Stack Hub](media/tutorial-offer-services/8-create-new-offer-base-plans-done.png)

1. L’onglet **Vérifier+ créer** doit contenir le message « Validation réussie » dans sa partie supérieure. Passez en revue les informations liées à « Concepts de base » et « Plans de base », puis sélectionnez **Créer** quand vous êtes prêt.

   ![Créer une offre dans le portail d’administration Azure Stack Hub](media/tutorial-offer-services/9-create-new-offer-review-create.png)

1. La page « Votre déploiement est en cours » s’affiche d'abord, puis « Votre déploiement est terminé » une fois l'offre déployée. Sélectionnez le nom de l’offre sous la colonne **Ressource**.

   ![Déploiement d’offre terminé dans le portail d’administration Azure Stack Hub](media/tutorial-offer-services/10-offer-deployment-complete.png)

1. Notez la bannière indiquant que votre offre est toujours privée, ce qui empêche les utilisateurs de s'y abonner. Rendez-la publique en sélectionnant **Changer l’état**, puis **Public**.

    ![État public dans le portail d’administration Azure Stack Hub](media/tutorial-offer-services/11-offer-change-state.png)

::: moniker-end

::: moniker range="<=azs-1901"

## <a name="create-an-offer-1901-and-earlier"></a>Créer une offre (1901 et versions antérieures)

Pendant le processus de création de l’offre, vous créez à la fois une offre et un plan. Le plan est utilisé comme plan de base de l’offre. Pendant la création du plan, vous spécifiez les services mis à disposition dans le plan et leurs quotas respectifs.

1. Connectez-vous au portail administrateur avec un compte admin cloud.

    - Pour un système intégré, l’URL varie en fonction de la région et du nom de domaine externe de votre opérateur, dans le format `https://adminportal.<region>.<FQDN>`.
    - Si vous utilisez le Kit de développement Azure Stack, l’URL est <https://adminportal.local.azurestack.external>.

    Sélectionnez ensuite **+ Créer une ressource** > **Offres + plans** > **Offre**.

    ![Nouvelle offre dans le portail d’administration Azure Stack Hub](media/tutorial-offer-services/image01.png)

1. Dans **Nouvelle offre**, renseignez le **Nom d’affichage** et le **Nom de la ressource**, puis sélectionnez un **Groupe de ressources** nouveau ou existant. Le nom d’affichage correspond au nom convivial de l’offre. Seul l’opérateur cloud peut voir le nom de la ressource qui est utilisé par les administrateurs pour travailler avec une offre comme ressource Azure Resource Manager.

   ![Nom d’affichage dans le portail d’administration Azure Stack Hub](media/tutorial-offer-services/image02.png)

1. Sélectionnez **Plans de base**, puis dans la section **Plan**, sélectionnez **Ajouter** pour ajouter un nouveau plan à l’offre.

   ![Ajouter un plan dans le portail d’administration Azure Stack Hub](media/tutorial-offer-services/image03.png)

1. Dans la section **Nouveau plan**, renseignez le **Nom d’affichage** et le **Nom de la ressource**. Le nom d’affichage correspond au nom convivial du plan, que les utilisateurs voient. Seul l’opérateur cloud peut voir le nom de la ressource qui est utilisé par les opérateurs cloud pour travailler avec le plan comme ressource Azure Resource Manager.

   ![Nom d’affichage des plans dans le portail d’administration Azure Stack Hub](media/tutorial-offer-services/image04.png)

1. Sélectionnez **Services**. Dans la liste des services, sélectionnez **Microsoft.Compute**, **Microsoft.Network** et **Microsoft.Storage**. Cliquez sur **Sélectionner** pour ajouter ces services au plan.

   ![Services des plans dans le portail d’administration Azure Stack Hub](media/tutorial-offer-services/image05.png)

1. Cliquez sur **Quotas**, puis sélectionnez le premier service pour lequel vous voulez créer un quota. Pour un quota IaaS, utilisez l’exemple suivant comme guide pour configurer des quotas pour les services Calcul, Réseau et Stockage.

    - Commencez par créer un quota pour le service Calcul. Dans la liste d’espaces de noms, sélectionnez **Microsoft.Compute**, puis **Créer un quota**.
    
      ![Créer un quota](media/tutorial-offer-services/image06.png)

   - Sous **Créer un quota**, saisissez un nom pour le quota. Vous pouvez modifier ou accepter toutes les valeurs de quota qui s’affichent. Dans cet exemple, nous acceptons les paramètres par défaut et sélectionnons **OK**.
   
      ![Nom du quota](media/tutorial-offer-services/image07.png)
       
    - Sélectionnez **Microsoft.Compute** dans la liste d’espaces de noms, puis sélectionnez le quota que vous avez créé. Cette étape associe le quota au service de calcul.
    
      ![Sélectionner un quota](media/tutorial-offer-services/image08.png)
        
        Répétez ces étapes pour les services Réseau et Stockage. Lorsque vous avez terminé, sélectionnez **OK** dans **Quotas** pour enregistrer tous les quotas.

1. Sous **Nouveau plan**, sélectionnez **OK**.

1. Sous **Plan**, sélectionnez le nouveau plan, puis **Sélectionner**.

1. Sous **Nouvelle offre**, sélectionnez **Créer**. Une fois l’offre créée, une notification s’affiche.

1. Dans le menu du tableau de bord, sélectionnez **Offres**, puis cliquez sur l’offre que vous avez créée.

1. Sélectionnez **Changer l’état**, puis **Public**.

    ![État Public](media/tutorial-offer-services/image09.png)

::: moniker-end
 
## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créez une offre.
> * Créez un plan.
> * Affecter des services et des quotas à un plan.
> * Affecter un plan à une offre.

Passez au tutoriel suivant pour savoir comment :
> [!div class="nextstepaction"]
> [Tester les services proposés dans ce tutoriel](tutorial-test-offer.md)
