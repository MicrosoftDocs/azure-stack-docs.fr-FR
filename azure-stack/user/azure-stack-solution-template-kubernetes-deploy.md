---
title: Déployer Kubernetes pour utiliser des conteneurs Azure Stack Hub
description: Découvrez comment déployer Kubernetes pour utiliser des conteneurs avec Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 06/18/2019
ms.openlocfilehash: 5fa9c506b2e030adbf521191a623579f56f1ae0f
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77701766"
---
# <a name="deploy-kubernetes-to-use-containers-with-azure-stack-hub"></a>Déployer Kubernetes pour utiliser des conteneurs avec Azure Stack Hub

> [!Note]  
> Utilisez uniquement l’élément de Place de marché Kubernetes Azure Stack pour déployer des clusters en tant que preuve de concept. Pour les clusters Kubernetes pris en charge sur Azure Stack, utilisez [le moteur AKS](azure-stack-kubernetes-aks-engine-overview.md).

Vous pouvez suivre les étapes de cet article pour déployer et configurer les ressources pour Kubernetes en une seule opération coordonnée. Les étapes utilisent un modèle Azure Resource Manager. Vous devez collecter les informations nécessaires sur votre installation d’Azure Stack Hub, générer le modèle, puis le déployer sur votre cloud. Le modèle Azure Stack Hub n’utilise pas le même service AKS managé que celui offert dans Azure global.

## <a name="kubernetes-and-containers"></a>Kubernetes et conteneurs

Vous pouvez installer Kubernetes à l’aide de modèles Azure Resource Manager générés par le moteur AKS sur Azure Stack Hub. Un [Kubernetes](https://kubernetes.io) est un système open source permettant d’automatiser le déploiement, la mise à l’échelle et la gestion d’applications dans des conteneurs. Un [conteneur](https://www.docker.com/what-container) se trouve dans une image. L’image de conteneur est similaire à une machine virtuelle, avec cette différence que le conteneur inclut seulement les ressources nécessaires pour exécuter une application, comme le code, un runtime pour exécuter le code, des bibliothèques spécifiques et des paramètres.

Vous pouvez utiliser Kubernetes pour ce qui suit :

- Développer des applications hautement évolutives et extensibles, qui peuvent être déployées en quelques secondes. 
- Simplifier la conception de vos applications et améliorer leur fiabilité à l’aide de différentes applications Helm. [Helm](https://github.com/kubernetes/helm) est un outil d’empaquetage open source qui vous aide à installer et à gérer le cycle de vie d’applications Kubernetes.
- Surveillez et diagnostiquez facilement l’intégrité de vos applications.

Vous êtes facturé seulement pour l’utilisation des ressources de calcul qui est nécessaire aux nœuds où se trouve votre cluster. Pour plus d’informations, voir [Utilisation et facturation dans Azure Stack Hub](../operator/azure-stack-billing-and-chargeback.md).

## <a name="deploy-kubernetes-to-use-containers"></a>Déployer Kubernetes pour utiliser des conteneurs

Les étapes pour déployer un cluster Kubernetes sur Azure Stack Hub dépendent de votre service de gestion des identités. Vérifiez la solution de gestion des identités utilisée par votre installation d’Azure Stack Hub. Contactez votre administrateur Azure Stack Hub pour vérifier votre service de gestion des identités.

- **Azure Active Directory (Azure AD)**  
Pour obtenir des instructions sur l’installation du cluster lors de l’utilisation d’Azure AD, voir [Déployer Kubernetes sur Azure Stack Hub à l’aide d’Azure Active Directory (Azure AD)](azure-stack-solution-template-kubernetes-azuread.md).

- **Active Directory Federation Services (AD FS)**  
Pour obtenir des instructions sur l’installation du cluster lors de l’utilisation d’AD FS, voir [Déployer Kubernetes sur Azure Stack Hub à l’aide des services de fédération Active Directory (AD FS)](azure-stack-solution-template-kubernetes-adfs.md).

## <a name="connect-to-your-cluster"></a>Se connecter au cluster

Vous êtes maintenant prêt à vous connecter à votre cluster. Le maître se trouve dans votre groupe de ressources de cluster et est nommé `k8s-master-<sequence-of-numbers>`. Utilisez un client SSH pour vous connecter au maître. Sur le maître, vous pouvez utiliser **kubectl**, client de ligne de commande Kubernetes, pour gérer le cluster. Pour obtenir des instructions, consultez [Kubernetes.io](https://kubernetes.io/docs/reference/kubectl/overview).

Le gestionnaire de package **Helm** peut également s’avérer utile pour installer et déployer des applications sur votre cluster. Pour obtenir des instructions sur l’installation et l’utilisation de Helm avec votre cluster, consultez [helm.sh](https://helm.sh/).

## <a name="next-steps"></a>Étapes suivantes

[Activer le tableau de bord Kubernetes](azure-stack-solution-template-kubernetes-dashboard.md)

[Ajouter un cluster Kubernetes à la Place de marché (pour l’opérateur Azure Stack Hub)](../operator/azure-stack-solution-template-kubernetes-cluster-add.md)

[Déployer Kubernetes sur Azure Stack Hub à l’aide d’Azure Active Directory (Azure AD)](azure-stack-solution-template-kubernetes-azuread.md)

[Déployer Kubernetes sur Azure Stack Hub à l’aide des services de fédération Active Directory (AD FS)](azure-stack-solution-template-kubernetes-adfs.md)

[Kubernetes sur Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
