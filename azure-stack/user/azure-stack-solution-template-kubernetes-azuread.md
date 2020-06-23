---
title: Déployer Kubernetes sur Azure Stack Hub à l’aide d’Azure Active Directory (Azure AD)
description: Découvrez comment déployer Kubernetes sur Azure Stack Hub à l’aide d’Azure Active Directory (Azure AD).
author: mattbriggs
ms.topic: article
ms.date: 3/12/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 3/12/2020
ms.openlocfilehash: ee0410467a7e2608580da5c209a97f86a60b675a
ms.sourcegitcommit: 6306e0c2506106ad01ff50010f36466f3325d0a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84631195"
---
# <a name="deploy-kubernetes-to-azure-stack-hub-using-azure-active-directory"></a>Déployer Kubernetes sur Azure Stack Hub à l’aide d’Azure Active Directory

> [!Note]  
> Utilisez uniquement l’élément de Place de marché Kubernetes Azure Stack pour déployer des clusters en tant que preuve de concept. Pour les clusters Kubernetes pris en charge sur Azure Stack, utilisez [le moteur AKS](azure-stack-kubernetes-aks-engine-overview.md).

Vous pouvez suivre les étapes décrites dans cet article pour déployer et configurer les ressources pour Kubernetes lorsque vous utilisez Azure Active Directory (Azure AD) comme service de gestion des identités en une seule et même opération.

## <a name="prerequisites"></a>Prérequis

Pour commencer, assurez-vous que vous disposez des autorisations appropriées et que votre Azure Stack Hub est prêt.

1. Vérifiez que vous pouvez créer des applications dans votre client Azure Active Directory (Azure AD). Ces autorisations sont nécessaires pour le déploiement de Kubernetes.

    Pour obtenir des instructions sur la vérification de vos autorisations, voir [Vérifier les autorisations Azure Active Directory](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

1. Générez une paire de clés publique et privée SSH pour vous connecter à la machine virtuelle Linux sur Azure Stack Hub. Vous aurez besoin de la clé publique lors de la création du cluster.

    Pour obtenir des instructions sur la génération d’une clé, voir [Génération d’une clé SSH](azure-stack-dev-start-howto-ssh-public-key.md).

1. Vérifiez que vous avez un abonnement valide dans votre portail de locataire Azure Stack Hub et que vous disposez d’un nombre suffisant d’adresses IP publiques pour ajouter de nouvelles applications.

    Vous ne pouvez pas déployer le cluster sur un abonnement **Administrateur** Azure Stack Hub. Vous devez utiliser un abonnement **Utilisateur**. 

1. Si Cluster Kubernetes ne figure pas dans votre Place de marché, contactez votre administrateur Azure Stack Hub.

## <a name="create-a-service-principal"></a>Créer un principal du service

Créer un principal de service dans Azure. Le principal du service permet à votre application d’accéder aux ressources Azure Stack Hub.

1. Connectez-vous au [portail Azure](https://portal.azure.com) global.

1. Vérifiez que vous vous êtes connecté à l’aide du locataire Azure AD associé à l’instance Azure Stack Hub. Vous pouvez changer d’identification en cliquant sur l’icône de filtre dans la barre d’outils Azure.

    ![Sélectionner votre locataire AD](media/azure-stack-solution-template-kubernetes-deploy/tenantselector.png)

1. Créez une application Azure AD.

    a. Connectez-vous à votre compte Azure via le [portail Azure](https://portal.azure.com).  
    b. Sélectionnez **Azure Active Directory** > **Inscriptions d’applications** > **Nouvelle inscription**.  
    c. Fournissez un nom et une URL pour l’application.  
    d. Sélectionnez les **Types de comptes pris en charge**.  
    e.  Ajoutez `http://localhost` pour l’URI de l’application. Sélectionnez **Web** pour le type d’application que vous souhaitez créer. Après avoir défini les valeurs, sélectionnez **S’inscrire**.

1. Notez l’**ID d’application**. Vous aurez besoin de l’ID lors de la création du cluster. L’ID est référencé en tant qu’**ID client du principal de service**.

1. Dans le panneau du principal de service, sélectionnez **Nouveau secret client**. **Paramètres** > **Clés**. Vous devez générer une clé d’authentification pour le principal de service.

    a. Entrez la **Description**.

    b. Pour **Expire**, sélectionnez **N’expire jamais**.

    c. Sélectionnez **Ajouter**. Notez la chaîne de clé. Vous aurez besoin de la chaîne de clé lors de la création du cluster. La clé est référencée comme **Secret client du principal de service**.

## <a name="give-the-service-principal-access"></a>Accorder l’accès au principal de service

Accordez l’accès à votre abonnement au principal de service afin qu’il puisse créer des ressources.

1.  Connectez-vous au portail Azure Stack Hub `https://portal.local.azurestack.external/`.

1. Sélectionnez **Tous les services** > **Abonnements**.

1. Sélectionnez l’abonnement créé par votre opérateur pour utiliser le cluster Kubernetes.

1. Sélectionnez **Contrôle d’accès (IAM)**  > **Ajouter une attribution de rôle**.

1. Sélectionnez le rôle **Contributeur**.

1. Sélectionnez le nom de l’application créée pour votre principal de service. Vous devrez peut-être taper le nom dans la zone de recherche.

1. Cliquez sur **Enregistrer**.

## <a name="deploy-kubernetes"></a>Déployer Kubernetes

1. Ouvrez le portail Azure Stack Hub `https://portal.local.azurestack.external`.

1. Sélectionnez **Créer une ressource** > **Calcul** > **Cluster Kubernetes**. Cliquez sur **Créer**.

    ![Déployer un modèle de solution](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1. Concepts de base

1. Dans le volet Créer un cluster Kubernetes, sélectionnez **Bases**.

    ![Déployer un modèle de solution](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. Sélectionnez votre ID **d’abonnement**.

1. Entrez le nom d’un nouveau groupe de ressources ou sélectionnez un groupe de ressources existant. Le nom de la ressource doit être alphanumérique et en minuscules.

1. Sélectionnez **l’emplacement** du groupe de ressources. Il s’agit de la région que vous avez choisie pour votre installation Azure Stack Hub.

### <a name="2-kubernetes-cluster-settings"></a>2. Paramètres de cluster Kubernetes

1. Dans le volet Créer un cluster Kubernetes, sélectionnez **Kubernetes Cluster Settings** (Paramètres de cluster Kubernetes).

    ![Déployer un modèle de solution](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings-aad.png)

1. Entrez le **nom de l’utilisateur administrateur de la machine virtuelle Linux**. Nom d’utilisateur pour les machines virtuelles Linux qui font partie du cluster Kubernetes et de DVM.

1. Entrez la **Clé publique SSH** utilisée pour l’autorisation sur toutes les machines Linux, créée en même temps que le cluster Kubernetes et DVM.

1. Entrez le **Préfixe DNS du profil principal** qui est unique pour la région. Il doit s’agir d’un nom unique pour la région, tel que `k8s-12345`. Nous vous recommandons d’essayer de choisir le même préfixe que celui du nom du groupe de ressources.

    > [!Note]  
    > Pour chaque cluster, utilisez un préfixe DNS de profil principal nouveau et unique.

1. Sélectionnez la valeur du champ **Nombre de profils de pool maître Kubernetes**. Il s’agit du nombre de nœuds dans le pool maître. Elle peut être comprise entre 1 et 7. Cette valeur doit être un nombre impair.

1. Sélectionnez la valeur du champ **The VMSize of the Kubernetes master VMs** (Taille des machines virtuelles maîtres Kubernetes). Cette opération spécifie la taille des machines virtuelles maîtres Kubernetes. 

1. Sélectionnez la valeur du champ **Nombre de profils de pool de nœuds Kubernetes**. Il s’agit du nombre d’agents dans le cluster. 

1. Sélectionnez la valeur du champ **Taille des machines virtuelles nœud Kubernetes**. Cette opération spécifie la taille des machines virtuelles de nœud Kubernetes. 

1. Sélectionnez **Azure AD** comme **système d’identité Azure Stack Hub** pour votre installation Azure Stack Hub.

1. Entrez **l’ID client du principal de service**. Celui-ci est utilisé par le fournisseur cloud d’Azure Kubernetes. ID de client identifié comme ID d’application lorsque votre administrateur Azure Stack Hub a créé le principal du service.

1. Entrez la **clé secrète client du principal du service**. Il s’agit de la clé secrète client que vous définissez lors de la création de votre service.

1. Entrez la **version de Kubernetes**. Il s’agit de la version du fournisseur d’Azure Kubernetes. Azure Stack Hub publie une build Kubernetes personnalisée pour chaque version d’Azure Stack Hub.

### <a name="3-summary"></a>3. Résumé

1. Sélectionnez Résumé. Le panneau affiche un message de validation concernant vos paramètres de configuration de cluster Kubernetes.

    ![Déployer un modèle de solution](media/azure-stack-solution-template-kubernetes-deploy/04_preview.png)

2. Passez vos paramètres en revue.

3. Sélectionnez **OK** pour déployer votre cluster.

> [!TIP]  
>  Si vous avez des questions concernant votre déploiement, vous pouvez poster votre question ou lire la réponse si quelqu’un y a déjà répondu sur le [Forum Azure Stack Hub](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack).


## <a name="next-steps"></a>Étapes suivantes

[Se connecter à votre cluster](azure-stack-solution-template-kubernetes-deploy.md#connect-to-your-cluster)

[Activer le tableau de bord Kubernetes](azure-stack-solution-template-kubernetes-dashboard.md)
