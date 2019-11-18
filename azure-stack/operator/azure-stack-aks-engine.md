---
title: Ajouter les prérequis du moteur Azure Kubernetes Services (AKS) à la Place de marché Azure Stack | Microsoft Docs
description: Découvrez comment ajouter les prérequis du moteur AKS à la Place de marché Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/05/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: c21223ca8926a1e2b3ca1b06752a79a14adaeda1
ms.sourcegitcommit: 5c92a669007ab4aaffe4484f1d8836a40340dde1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73636876"
---
# <a name="add-the-azure-kubernetes-services-aks-engine-prerequisites-to-the-azure-stack-marketplace"></a>Ajouter les prérequis du moteur Azure Kubernetes Services (AKS) à la Place de marché Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Vous pouvez autoriser vos utilisateurs à configurer le moteur Azure Kubernetes Services (AKS) en ajoutant à Azure Stack les éléments décrits dans cet article. Vos utilisateurs peuvent alors déployer un cluster Kubernetes en une seule opération coordonnée. Cet article vous guide tout au long des étapes nécessaires pour mettre le moteur AKS à la disposition de vos utilisateurs dans des environnements connectés et déconnectés. Le moteur AKS s’appuie sur une identité du principal de service, et sur la Place de marché, d’une extension de script personnalisé et de l’image de base AKS.

Le [moteur AKS](https://github.com/Azure/aks-engine) utilise une image générée, l’image de base AKS. Chaque version du moteur AKS dépend d’une version d’image spécifique que vous pouvez rendre disponible dans Azure Stack. Consultez le tableau répertoriant les versions du moteur AKS et les versions Kubernetes correspondantes dans [Versions de Kubernetes prises en charge](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions).

> [!IMPORTANT]
> Le moteur AKS est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="check-your-users-service-offering"></a>Vérifier l'offre de services de votre utilisateur

Vos utilisateurs auront besoin d'un plan, d'une offre et d'un abonnement à Azure Stack avec suffisamment d'espace. Les utilisateurs voudront souvent déployer des clusters comportant jusqu'à six machines virtuelles, composées de trois nœuds principaux et de trois nœuds worker. Vous voudrez vous assurer qu'ils disposent d’un quota suffisamment élevé.

Si vous avez besoin de plus d'informations sur la planification et la mise en place d'une offre de services, voir [Vue d’ensemble de l’offre de services dans Azure Stack](service-plan-offer-subscription-overview.md)

## <a name="create-a-service-principal-and-credentials"></a>Créer un principal de service et des informations d’identification

Le cluster Kubernetes aura besoin des autorisations du principal du service (SPN) et des autorisations basées sur le rôle dans Azure Stack.

### <a name="create-an-spn-in-azure-ad"></a>Créer un SPN dans Azure AD

Si vous utilisez Azure Active Directory Federated Services (AD FS) pour votre service de gestion des identités, vous devez créer un principal de service pour les utilisateurs qui déploient un cluster Kubernetes. Créez un principal de service à l’aide d’une clé secrète client. Pour obtenir des instructions, voir [Créer un principal de service utilisant une clé secrète client](azure-stack-create-service-principals.md#create-a-service-principal-that-uses-a-client-secret-credential).

### <a name="create-an-spn-in-ad-fs"></a>Créer un SPN dans AD FS

Si vous utilisez AD FS (Active Directory Federated Services) pour votre service de gestion des identités, vous devez créer un principal de service pour les utilisateurs qui déploient un cluster Kubernetes. Créez un principal de service à l’aide d’une clé secrète client. Pour obtenir des instructions, consultez [Créer un principal de service à l’aide d’une clé secrète client](azure-stack-create-service-principals.md#create-a-service-principal-that-uses-client-secret-credentials).

## <a name="add-the-aks-base-image"></a>Ajouter l’image de base AKS

Vous pouvez ajouter l'image de base AKS à la Place de marché en vous procurant cet élément auprès d’Azure. Cependant, si votre instance Azure Stack est déconnectée, suivez les instructions de la rubrique [Télécharger des éléments de la Place de marché à partir d’Azure](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item?view=azs-1908#disconnected-or-a-partially-connected-scenario) pour ajouter l'élément. Ajoutez l’élément spécifié à l’étape 5.

Ajoutez l'élément suivant à la Place de marché :

1. Connectez-vous au [portail d’administration](https://adminportal.local.azurestack.external).

1. Sélectionnez **Tous les services**, puis, dans la catégorie **ADMINISTRATION**, sélectionnez **Gestion de la Place de marché**.

1. Sélectionnez **+ Ajouter à partir d’Azure**.

1. Entrez `AKS Base`.

1. Sélectionnez la version de l’image correspondant à la version du moteur AKS. La liste des versions d’image de base AKS et du moteur AKS est disponible dans la rubrique [Versions de Kubernetes prises en charge](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions). 

    Dans la liste, sélectionnez :
    - **Éditeur** : Azure Kubernetes Service
    - **Offre** : aks
    - **Version** : Image de base AKS 16.04-LTS Image Distro, octobre 2019 (2019.10.24 ou version mappée au moteur AKS)

1. Sélectionnez **Télécharger**.

## <a name="add-a-custom-script-extension"></a>Ajouter une extension de script personnalisé

Vous pouvez ajouter le script personnalisé à la Place de marché en vous procurant l'élément auprès d’Azure. Cependant, si votre instance Azure Stack est déconnectée, suivez les instructions de la rubrique [Télécharger des éléments de la Place de marché à partir d’Azure](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item?view=azs-1908#disconnected-or-a-partially-connected-scenario) pour ajouter l'élément.  Ajoutez l’élément spécifié à l’étape 5.

1. Ouvrez le [portail d’administration](https://adminportal.local.azurestack.external).

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

[Qu’est-ce que le moteur AKS sur Azure Stack ?](../user/azure-stack-kubernetes-aks-engine-overview.md)

[Vue d’ensemble de l’offre de services dans Azure Stack](service-plan-offer-subscription-overview.md)
