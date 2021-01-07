---
title: Déployer Kubernetes sur Azure Stack Hub à l’aide d’Active Directory Federated Services (AD FS)
description: Découvrez comment déployer Kubernetes sur Azure Stack Hub à l’aide d’Active Directory Federation Services (AD FS).
author: mattbriggs
ms.topic: article
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 3/12/2020
ms.openlocfilehash: 268157d491b1605ecce442cc4cb348e76657fec2
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97873943"
---
# <a name="deploy-kubernetes-to-azure-stack-hub-using-active-directory-federated-services"></a>Déployer Kubernetes sur Azure Stack Hub à l’aide d’Active Directory Federation Services

> [!NOTE]  
> Utilisez uniquement l’élément de Place de marché Kubernetes Azure Stack pour déployer des clusters en tant que preuve de concept. Pour les clusters Kubernetes pris en charge sur Azure Stack, utilisez [le moteur AKS](azure-stack-kubernetes-aks-engine-overview.md).

Vous pouvez suivre les étapes de cet article pour déployer et configurer les ressources pour Kubernetes. Utilisez ces étapes quand Active Directory Federation Services (AD FS) est votre service de gestion des identités.

## <a name="prerequisites"></a>Prérequis 

Pour commencer, assurez-vous que vous disposez des autorisations appropriées et que votre Azure Stack Hub est prêt.

1. Générez une paire de clés publique et privée SSH pour vous connecter à la machine virtuelle Linux sur Azure Stack Hub. Vous avec besoin de la clé publique lors de la création du cluster.

    Pour obtenir des instructions sur la génération d’une clé, voir [Génération d’une clé SSH](azure-stack-dev-start-howto-ssh-public-key.md).

1. Vérifiez que vous avez un abonnement valide dans votre portail de locataire Azure Stack Hub et que vous disposez d’un nombre suffisant d’adresses IP publiques pour ajouter de nouvelles applications.

    Vous ne pouvez pas déployer le cluster sur un abonnement **Administrateur** Azure Stack Hub. Vous devez utiliser un abonnement **Utilisateur**. 

1. Si Cluster Kubernetes ne figure pas dans votre Place de marché, contactez votre administrateur Azure Stack Hub.

## <a name="create-a-service-principal"></a>Créer un principal du service

Vous devez travailler avec votre administrateur Azure Stack Hub pour configurer votre principal du service si vous utilisez AD FS comme solution de gestion des identités. Le principal du service permet à votre application d’accéder aux ressources Azure Stack Hub.

1. Votre administrateur Azure Stack Hub vous fournit les informations relatives au principal du service. Les informations du principal de service doivent se présenter comme suit :

     ```Text  
       ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
       ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
       ClientSecret          : <your client secret>
       Thumbprint            : <often this value is empty>
       ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
       PSComputerName        : 192.168.200.224
       RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
     ```

2. Attribuez à votre nouveau principal de service un rôle de contributeur à votre abonnement. Pour obtenir des instructions, consultez [Attribuer un rôle](../operator/azure-stack-add-users-adfs.md).

## <a name="deploy-kubernetes"></a>Déployer Kubernetes

1. Ouvrez le portail Azure Stack Hub `https://portal.local.azurestack.external`.

1. Sélectionnez **Créer une ressource** > **Calcul** > **Cluster Kubernetes**. Sélectionnez **Create** (Créer).

    ![Capture d’écran montrant la page où créer un cluster Kubernetes.](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1. Concepts de base

1. Dans le volet Créer un cluster Kubernetes, sélectionnez **Bases**.

    ![Capture d’écran montrant où ajouter des informations de base sur votre cluster Kubernetes.](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. Sélectionnez votre ID **d’abonnement**.

1. Entrez le nom d’un nouveau groupe de ressources ou sélectionnez un groupe de ressources existant. Le nom de la ressource doit être alphanumérique et en minuscules.

1. Sélectionnez **l’emplacement** du groupe de ressources. Il s’agit de la région que vous avez choisie pour votre installation Azure Stack Hub.

### <a name="2-kubernetes-cluster-settings"></a>2. Paramètres de cluster Kubernetes

1. Dans le volet Créer un cluster Kubernetes, sélectionnez **Kubernetes Cluster Settings** (Paramètres de cluster Kubernetes).

    ![Capture d’écran montrant l’étape au cours de laquelle vous configurez les paramètres de cluster Kubernetes.](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings-adfs.png)

1. Entrez le **nom de l’utilisateur administrateur de la machine virtuelle Linux**. Nom d’utilisateur pour les machines virtuelles Linux qui font partie du cluster Kubernetes et de DVM.

1. Entrez la **Clé publique SSH** utilisée pour l’autorisation sur toutes les machines Linux, créée en même temps que le cluster Kubernetes et DVM.

1. Entrez le **Préfixe DNS du profil principal** qui est unique pour la région. Il doit s’agir d’un nom unique pour la région, tel que `k8s-12345`. Nous vous recommandons d’essayer de choisir le même préfixe que celui du nom du groupe de ressources.

    > [!NOTE]  
    > Pour chaque cluster, utilisez un préfixe DNS de profil principal nouveau et unique.

1. Sélectionnez la valeur du champ **Nombre de profils de pool maître Kubernetes**. Il s’agit du nombre de nœuds dans le pool maître. Elle peut être comprise entre 1 et 7. Cette valeur doit être un nombre impair.

1. Sélectionnez la valeur du champ **The VMSize of the Kubernetes master VMs** (Taille des machines virtuelles maîtres Kubernetes).

1. Sélectionnez la valeur du champ **Nombre de profils de pool de nœuds Kubernetes**. Il s’agit du nombre d’agents dans le cluster. 

1. Sélectionnez la valeur du champ **Taille des machines virtuelles nœud Kubernetes**. Cette opération spécifie la taille des machines virtuelles de nœud Kubernetes. 

1. Sélectionnez **ADFS** comme **système d’identité Azure Stack Hub** pour votre installation Azure Stack Hub.

1. Entrez **l’ID client du principal de service**. Celui-ci est utilisé par le fournisseur cloud d’Azure Kubernetes. ID de client identifié comme ID d’application lorsque votre administrateur Azure Stack Hub a créé le principal du service.

1. Entrez la **clé secrète client du principal du service**. Il s’agit de la clé secrète client fournie pour votre principal du service AD FS par votre administrateur Azure Stack Hub.

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