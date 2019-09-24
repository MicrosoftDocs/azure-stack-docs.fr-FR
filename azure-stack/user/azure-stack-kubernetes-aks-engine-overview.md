---
title: Qu’est-ce que le moteur AKS sur Azure Stack ? | Microsoft Docs
description: Découvrez comment utiliser l’outil en ligne de commande du moteur AKS pour déployer et gérer un cluster Kubernetes sur Azure et Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na (Kubernetes)
ms.devlang: nav
ms.topic: article
ms.date: 09/14/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/14/2019
ms.openlocfilehash: 0b1c28369fb1865778c68040894d100af9ea7fd6
ms.sourcegitcommit: 09d14eb77a43fd585e7e6be93c32fa427770adb6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/16/2019
ms.locfileid: "71019377"
---
# <a name="what-is-the-aks-engine-on-azure-stack"></a>Qu’est-ce que le moteur AKS sur Azure Stack ?

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Vous pouvez utiliser l’outil en ligne de commande du moteur AKS pour déployer et gérer un cluster Kubernetes sur Azure et Azure Stack. Utilisez le moteur AKS pour créer, mettre à niveau et mettre à l’échelle des clusters natifs Azure Resource Manager. Vous pouvez utiliser le moteur pour déployer un cluster dans des environnements à la fois connectés et déconnectés. Cet article fournit une vue d’ensemble du moteur AKS, des scénarios pris en charge pour l’utilisation du moteur avec Azure Stack et une présentation des opérations telles que le déploiement, la mise à niveau et la mise à l’échelle.

> [!IMPORTANT]
> Le moteur AKS est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview-of-the-aks-engine"></a>Vue d’ensemble du moteur AKS

Le [moteur AKS](https://github.com/Azure/aks-engine) fournit un outil en ligne de commande pour démarrer des clusters Kubernetes sur Azure et Azure Stack. En utilisant Azure Resource Manager, le moteur AKS permet de créer et gérer des clusters s’exécutant sur des machines virtuelles, des réseaux virtuels et d’autres ressources IaaS (infrastructure-as-a-service) dans Azure Stack.

## <a name="aks-engine-on-azure-stack-considerations"></a>Considérations relatives au moteur AKS sur Azure Stack

Avant d’utiliser le moteur AKS sur Azure Stack, il est important de comprendre les différences entre Azure Stack et Azure. Cette section identifie les fonctionnalités différentes et les principaux points à prendre en compte lors de l’utilisation d’Azure Stack avec le moteur AKS pour gérer votre cluster Kubernetes.

Pour plus d’informations sur les spécificités du moteur AKS sur Azure Stack et ses différences par rapport à Azure, consultez [Moteur AKS sur Azure Stack](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md).

## <a name="supported-scenarios-with-the-aks-engine"></a>Scénarios pris en charge avec le moteur AKS

Les scénarios suivants sont pris en charge par l’équipe du support technique Azure Stack :

1.  Le moteur AKS déploie tous les artefacts de cluster en suivant les instructions données dans cette documentation et en utilisant le [modèle suivant](https://github.com/Azure/aks-engine/tree/master/examples/azure-stack).
2.  Le moteur AKS déploie le cluster sur un réseau virtuel existant. Pour plus d’informations, consultez [Utilisation d’un réseau virtuel personnalisé avec le moteur AKS](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/custom-vnet.md).
3.  Opérations de [mise à niveau](azure-stack-kubernetes-aks-engine-upgrade.md) et de [mise à l’échelle](azure-stack-kubernetes-aks-engine-scale.md).

Pour plus d’informations sur le moteur AKS et Azure Stack, consultez [Stratégies de support pour le moteur AKS sur Azure Stack](azure-stack-kubernetes-ask-engine-support.md).

## <a name="install-the-aks-engine-and-deploy-a-kubernetes-cluster"></a>Installer le moteur AKS avant de déployer un cluster Kubernetes

Pour déployer un cluster Kubernetes avec le moteur AKS sur Azure Stack :

1. [Configurer les prérequis pour le moteur AKS](azure-stack-kubernetes-aks-engine-set-up.md)
2. Installez le moteur AKS sur une machine dotée d’un accès à votre environnement Azure Stack.
     - [Installer le moteur AKS sur Windows dans Azure Stack](azure-stack-kubernetes-aks-engine-deploy-windows.md)
     - [Installer le moteur AKS sur Linux dans Azure Stack](azure-stack-kubernetes-aks-engine-deploy-linux.md)
3. [Déployer un cluster Kubernetes avec le moteur AKS sur Azure Stack](azure-stack-kubernetes-aks-engine-deploy-cluster.md)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Configurer les prérequis pour le moteur AKS](azure-stack-kubernetes-aks-engine-set-up.md)