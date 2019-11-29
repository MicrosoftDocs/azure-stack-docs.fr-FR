---
title: Déplacer votre cluster d’élément de la Place de marché vers le moteur AKS sur Azure Stack | Microsoft Docs
description: Découvrez comment déplacer votre cluster d’élément de la Place de marché vers le moteur AKS sur Azure Stack.
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
ms.date: 11/21/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: 1aa7c403e9491bf8b081b712620a3e984e5dc68a
ms.sourcegitcommit: 0b783e262ac87ae67929dbd4c366b19bf36740f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74310235"
---
# <a name="move-your-marketplace-item-cluster-to-the-aks-engine-on-azure-stack"></a>Déplacer votre cluster d’élément de la Place de marché vers le moteur AKS sur Azure Stack

L’élément de Place de marché Kubernetes Azure Stack utilise un modèle Azure Resource Manager pour déployer une machine virtuelle de déploiement afin de télécharger et d’installer le moteur AKS et de générer le modèle d’API d’entrée utilisé pour décrire le cluster, une fois que le moteur AKS est exécuté sur la machine virtuelle et que le cluster est déployé. Cet article explique comment accéder au moteur AKS et aux fichiers correspondants afin de pouvoir l’utiliser pour effectuer des opérations de mise à jour et de mise à l’échelle sur votre cluster Kubernetes.

## <a name="access-aks-engine-in-the-dvm"></a>Accéder au moteur AKS dans la machine virtuelle de déploiement

Une fois que le déploiement initié par l’élément de Place de marché Kubernetes Azure Stack s’est achevé correctement, vous pouvez trouver le moteur AKS utilisé pour déployer le cluster installé dans la machine virtuelle de déploiement créée dans le groupe de ressources que vous avez spécifié pour le cluster. Cette machine virtuelle ne fait pas partie du cluster Kubernetes, elle est créée dans son propre réseau virtuel. Voici les étapes à suivre pour rechercher la machine virtuelle et trouver le moteur AKS à l’intérieur de celle-ci :

1.  Ouvrez le portail utilisateur Azure Stack et recherchez le groupe de ressources que vous avez spécifié pour le cluster Kubernetes.
2.  Dans le groupe de ressources, recherchez la machine virtuelle de déploiement. Le nom commence par le préfixe **vmd-** .
3.  Sélectionnez la machine virtuelle de déploiement. Dans Vue d’ensemble,** recherchez l’adresse IP publique. Utilisez cette adresse et votre application console, telle que Putty, pour établir une session SSH sur la machine virtuelle.
4.  Dans votre session sur la machine virtuelle de déploiement, le moteur AKS est disponible à l’emplacement suivant : `./var/lib/waagent/custom-script/download/0/bin/aks-engine`
5.  Localisez le fichier `.json` décrivant les clusters utilisés comme entrée dans le moteur AKS. Le fichier est situé à l’emplacement `/var/lib/waagent/custom-script/download/0/bin/azurestack.json`. Notez que le fichier contient les informations d’identification de principal de service utilisées pour déployer votre cluster. Si vous décidez de conserver le fichier, veillez à transférer le fichier vers un magasin protégé.
6.  Localisez le répertoire de sortie généré par le moteur AKS à l’emplacement `/var/lib/waagent/custom-script/download/0/_output/<resource group name>`. Dans ce répertoire, trouvez la sortie `apimodel.json` à l’emplacement `/var/lib/waagent/custom-script/download/0/bin/apimodel.json`. Le répertoire et le fichier `apimodel.json` contiennent l’ensemble des certificats générés, clés et informations d’identification dont vous avez besoin pour déployer le cluster Kubernetes. Stockez ces ressources dans un emplacement sécurisé.
7.  Recherchez le fichier de configuration Kubernetes, souvent désigné sous le nom de fichier **kubeconfig**, au chemin d’accès :, où  correspond à votre identificateur d’emplacement Azure Stack. Ce fichier est utile si vous envisagez de configurer **kubectl** pour accéder à votre cluster Kubernetes.

## <a name="use-the-aks-engine-with-your-newly-created-cluster"></a>Utiliser le moteur AKS avec le cluster que vous venez de créer

Une fois que vous avez trouvé le fichier aks-engine, le fichier apimodel.json d’entrée, le répertoire de sortie et le fichier apimodel.json de sortie, stockez-les dans un emplacement sécurisé. Vous pouvez utiliser le binaire du moteur AKS et la sortie `apimodel.json` sur des machines virtuelles Linux.

1.  Pour continuer à utiliser le moteur AKS, afin d’effectuer des opérations telles que la **mise à niveau** et la **mise à l’échelle**, copiez le fichier binaire **aks-engine** sur la machine cible. Si vous utilisez la même machine **vmd-** dans un répertoire.

2.  Créez un répertoire avec le nom du cluster ou un autre nom mnémotechnique qui fait référence au nouveau cluster et enregistrez-y le fichier apimodel.json de sortie. Assurez-vous qu’il est situé dans un emplacement protégé, car ce fichier contient des informations d’identification. Après cela, vous pouvez exécuter le moteur AKS pour exécuter des opérations telles que la [mise à l’échelle](azure-stack-kubernetes-aks-engine-scale.md) ou la [mise à niveau](azure-stack-kubernetes-aks-engine-upgrade.md).

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [Le moteur AKS sur Azure Stack](azure-stack-kubernetes-aks-engine-overview.md)  
- [Résoudre les problèmes du moteur AKS sur Azure Stack](azure-stack-kubernetes-aks-engine-troubleshoot.md)  

