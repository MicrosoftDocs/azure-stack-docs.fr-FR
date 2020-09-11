---
title: Ajouter les prérequis du moteur Azure Kubernetes Services (AKS) à la Place de marché Azure Stack Hub
description: Découvrez comment ajouter les prérequis du moteur AKS à la Place de marché Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 09/02/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/02/2020
ms.openlocfilehash: 60f291c089e28b4afb574d5a0d7a2d37ac304e88
ms.sourcegitcommit: b80d529ff47b15b8b612d8a787340c7b0f68165b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89472821"
---
# <a name="add-the-azure-kubernetes-services-aks-engine-prerequisites-to-the-azure-stack-hub-marketplace"></a>Ajouter les prérequis du moteur Azure Kubernetes Services (AKS) à la Place de marché Azure Stack Hub

Vous pouvez configurer le moteur Azure Kubernetes Service (AKS) pour vos utilisateurs. Ajoutez les éléments décrits dans cet article à votre instance Azure Stack Hub. Vos utilisateurs peuvent alors déployer un cluster Kubernetes en une seule opération coordonnée. Cet article vous guide tout au long des étapes nécessaires pour mettre le moteur AKS à la disposition de vos utilisateurs dans des environnements connectés et déconnectés. Le moteur AKS s’appuie sur une identité du principal de service, et sur la Place de marché, d’une extension de script personnalisé et de l’image de base AKS. Le moteur AKS vous demande d’exécuter [Azure Stack Hub 1910](release-notes.md?view=azs-1910) ou ultérieur.

## <a name="check-your-users-service-offering"></a>Vérifier l'offre de services de votre utilisateur

Vos utilisateurs ont besoin d’un plan, d’une offre et d’un abonnement à Azure Stack Hub avec suffisamment d’espace. Les utilisateurs voudront souvent déployer des clusters comportant jusqu'à six machines virtuelles, composées de trois nœuds principaux et de trois nœuds worker. Vous voudrez vous assurer qu'ils disposent d’un quota suffisamment élevé.

Si vous avez besoin de plus d’informations sur la planification et la mise en place d’une offre de services, consultez [Vue d’ensemble de l’offre de services dans Azure Stack Hub](service-plan-offer-subscription-overview.md)

## <a name="create-a-service-principal-and-credentials"></a>Créer un principal de service et des informations d’identification

Le cluster Kubernetes a besoin des autorisations du principal du service (SPN) et des autorisations basées sur le rôle dans Azure Stack Hub.

### <a name="create-an-spn-in-azure-ad"></a>Créer un SPN dans Azure AD

Si vous utilisez Azure Active Directory Federated Services (AD FS) pour votre service de gestion des identités, vous devez créer un principal de service pour les utilisateurs qui déploient un cluster Kubernetes. Créez un principal de service à l’aide d’une clé secrète client. Pour obtenir des instructions, voir [Créer un principal de service utilisant une clé secrète client](azure-stack-create-service-principals.md#create-a-service-principal-that-uses-a-client-secret-credential).

### <a name="create-an-spn-in-ad-fs"></a>Créer un SPN dans AD FS

Si vous utilisez AD FS (Active Directory Federated Services) pour votre service de gestion des identités, vous devez créer un principal de service pour les utilisateurs qui déploient un cluster Kubernetes. Créez un principal de service à l’aide d’une clé secrète client. Pour obtenir des instructions, consultez [Créer un principal de service à l’aide d’une clé secrète client](azure-stack-create-service-principals.md#create-a-service-principal-that-uses-client-secret-credentials).

## <a name="add-the-aks-base-image"></a>Ajouter l’image de base AKS

Vous pouvez ajouter l'image de base AKS à la Place de marché en vous procurant cet élément auprès d’Azure. Cependant, si votre instance Azure Stack Hub est déconnectée, suivez les instructions de la rubrique [Télécharger des éléments de la Place de marché à partir d’Azure](azure-stack-download-azure-marketplace-item.md?pivots=state-disconnected) pour ajouter l’élément. Ajoutez l’élément spécifié à l’étape 5.

Ajoutez l'élément suivant à la Place de marché :

1. Connectez-vous au portail d’administration `https://adminportal.local.azurestack.external`.

1. Sélectionnez **Tous les services**, puis, dans la catégorie **ADMINISTRATION**, sélectionnez **Gestion de la Place de marché**.

1. Sélectionnez **+ Ajouter à partir d’Azure**.

1. Entrez `AKS Base`.

1. Sélectionnez la version de l’image correspondant à la version du moteur AKS. La liste des versions d’image de base AKS et du moteur AKS est disponible dans la rubrique [Versions de Kubernetes prises en charge](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-aks-engine-versions). 

    Dans la liste, sélectionnez :
    - **Éditeur** : Azure Kubernetes Service
    - **Offre** : aks
    - **Name** : Distribution d’images de base AKS Ubuntu 16.04-LTS, août 2020 (2020.08.24 ou version mappée au moteur AKS)
    - **Version** : 2020.08.24

1. Sélectionnez **Télécharger**.

## <a name="add-a-custom-script-extension"></a>Ajouter une extension de script personnalisé

Vous pouvez ajouter le script personnalisé à la Place de marché en vous procurant l'élément auprès d’Azure. Cependant, si votre instance Azure Stack Hub est déconnectée, suivez les instructions de la rubrique [Télécharger des éléments de la Place de marché à partir d’Azure](azure-stack-download-azure-marketplace-item.md?pivots=state-disconnected) pour ajouter l’élément.  Ajoutez l’élément spécifié à l’étape 5.

1. Ouvrez le portail d’administration `https://adminportal.local.azurestack.external`.

1. Sélectionnez **Tous les services**, puis, dans la catégorie **ADMINISTRATION**, sélectionnez **Gestion de la Place de marché**.

1. Sélectionnez **+ Ajouter à partir d’Azure**.

1. Entrez `Custom Script for Linux`.

1. Sélectionnez le script avec le profil suivant :
   - **Offre** : Script personnalisé pour Linux 2.0
   - **Version** : 2.0.6 (ou la dernière version)
   - **Éditeur** : Microsoft Corp

     > [!Note]  
     > Plusieurs versions du script personnalisé pour Linux peuvent être répertoriées. Vous devez ajouter la dernière version de l’élément.

1. Sélectionnez **Télécharger**.

## <a name="next-steps"></a>Étapes suivantes

[Qu’est-ce que le moteur AKS sur Azure Stack Hub ?](../user/azure-stack-kubernetes-aks-engine-overview.md)

[Vue d’ensemble de l’offre de services dans Azure Stack Hub](service-plan-offer-subscription-overview.md)
