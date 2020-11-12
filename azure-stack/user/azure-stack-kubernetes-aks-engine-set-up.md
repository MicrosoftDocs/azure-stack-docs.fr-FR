---
title: Configurer les conditions préalables à l’utilisation du moteur AKS sur Azure Stack Hub
description: Établissez les conditions d’exécution du moteur AKS sur votre infrastructure Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 10/16/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 10/16/2020
ms.openlocfilehash: c703d9cd9c20e48dec6172e3e0e3c12fcc3a7e2a
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94543525"
---
# <a name="set-up-the-prerequisites-for-the-aks-engine-on-azure-stack-hub"></a>Configurer les prérequis pour le moteur AKS sur Azure Stack Hub

Vous pouvez installer le moteur AKS sur une machine virtuelle de votre environnement ou sur toute machine cliente dotée d’un accès à votre point de terminaison Azure Stack Hub Resource Manager. Vous avez besoin des éléments suivants avant d’exécuter le moteur : un serveur Ubuntu de base AKS et une extension de script personnalisé Linux disponibles dans votre abonnement, une identité de principal du service dotée d’un rôle de contributeur et une paire de clés privée/publique pour l’accès SSH à votre serveur Ubuntu. De plus, si vous utilisez le Kit de développement Azure Stack, votre machine doit approuver les certificats appropriés.

Si vous avez vos prérequis, vous pouvez commencer à [définir votre cluster](azure-stack-kubernetes-aks-engine-deploy-cluster.md).

Si vous êtes l’opérateur cloud pour Azure Stack Hub et souhaitez proposer le moteur AKS, suivez les instructions de la rubrique [Ajouter le moteur AKS à la Place de marché Azure Stack Hub](../operator/azure-stack-aks-engine.md).

## <a name="prerequisites-for-the-aks-engine"></a>Prérequis pour le moteur AKS

Pour utiliser le moteur AKS, vous devez disposer des ressources suivantes. Gardez à l’esprit que le moteur AKS est destiné à être utilisé par les locataires d’Azure Stack Hub pour déployer des clusters Kubernetes dans leur abonnement locataire. La seule implication nécessaire de l’opérateur Azure Stack Hub a trait au téléchargement d’éléments de la Place de marché et à la création d’une identité de principal du service. Vous trouverez plus d’informations dans le tableau suivant.

Votre opérateur cloud doit avoir les éléments suivants en place.

| Configuration requise | Description | Obligatoire | Instructions |
| --- | --- | --- | --- | --- |
| Azure Stack Hub 1910 ou ultérieur | Le moteur AKS nécessite Azure Stack Hub 1910 ou ultérieur. | Obligatoire | Si vous ne connaissez pas votre version d’Azure Stack Hub, prenez contact avec votre opérateur cloud. |
| Extension de script personnalisé Linux | Extension de script personnalisé Linux 2.0<br>Offre : Script personnalisé pour Linux 2.0<br>Version : 2.0.6 (ou la dernière version)<br>Éditeur : Microsoft Corp | Obligatoire | Si vous n’avez pas cet élément dans votre abonnement, contactez votre opérateur cloud. |
| Image Ubuntu de base AKS | Image de base AKS Ubuntu 16.04-LTS<br>Pour plus d’informations sur la dépendance de version, consultez [Correspondance du moteur avec la version de l’image de base](#matching-engine-to-base-image-version). | Obligatoire | Si vous n’avez pas cet élément dans votre abonnement, contactez votre opérateur cloud.<br> Si vous êtes l’opérateur cloud pour Azure Stack Hub et souhaitez proposer le moteur AKS, suivez les instructions de la rubrique [Ajouter le moteur AKS à la Place de marché Azure Stack Hub](../operator/azure-stack-aks-engine.md). |
| Identité de principal du service (SPN) |  Une application qui a besoin pour déployer ou configurer des ressources via Azure Resource Manager doit être représentée par un principal de service. | Obligatoire | Vous devrez peut-être contacter votre opérateur Azure Stack Hub pour cet élément.<br>Si vous utilisez une identité de principal de service Azure Active Directory (Azure AD), un accès à Internet est exigé par les machines virtuelles du cluster Kubernetes afin que le principal de service puisse s’authentifier auprès d’Azure AD. Sans accès Internet, le cluster Kubernetes ne sera pas fonctionnel.<br>Pour obtenir des instructions, consultez [Utiliser une identité d’application pour accéder à des ressources](../operator/azure-stack-create-service-principals.md). |
| Rôle de **contributeur** attribué (SPN) | Pour autoriser une application à accéder aux ressources de votre abonnement en utilisant son principal du service, vous devez attribuer à ce dernier un rôle pour une ressource spécifique. | Obligatoire | Pour obtenir des instructions, consultez [Attribuer un rôle](../operator/azure-stack-create-service-principals.md#assign-a-role). |


Vous pouvez définir les éléments suivants.

| Configuration requise | Description | Obligatoire | Instructions |
| --- | --- | --- | --- |
| Abonnement Azure Stack Hub | Vous accédez à vos offres Azure Stack Hub par le biais d’abonnements. Une offre contient les services à votre disposition. | Obligatoire | Pour être en mesure de déployer des charges de travail de locataire dans Azure Stack Hub, vous devez d’abord obtenir un [abonnement Azure Stack Hub](./azure-stack-subscribe-services.md). |
| Resource group | Un groupe de ressources est un conteneur réunissant les ressources associées d’une solution Azure. Si vous ne spécifiez pas de groupe de ressources existant, l’outil en crée un pour vous. | Facultatif | [Gérer les groupes de ressources Azure Resource Manager à l’aide du portail Azure](/azure/azure-resource-manager/manage-resource-groups-portal) |
| Clé publique privée | Pour utiliser une connexion SSH ouverte entre la machine de développement et la machine virtuelle serveur dans votre instance Azure Stack Hub qui héberge votre application web, il est parfois nécessaire de créer une paire clé privée-clé publique SSH (Secure Shell). | Obligatoire | Pour obtenir des instructions sur la génération d’une clé, voir [Génération d’une clé SSH](./azure-stack-dev-start-howto-ssh-public-key.md).|


> [!Note]  
> Vous pouvez également créer les prérequis pour le moteur AKS avec [Azure CLI pour Azure Stack Hub](./azure-stack-version-profiles-azurecli2.md) ou [Azure Stack Hub PowerShell](../operator/powershell-install-az-module.md).

## <a name="matching-engine-to-base-image-version"></a>Correspondance du moteur avec la version de l’image de base

Le moteur AKS déploie un système d’exploitation de serveur Ubuntu personnalisé sur chaque image de nœud de cluster : la **distribution d’images de base AKS Ubuntu 16.04-LTS**. Toute version du moteur AKS dépend d’une version d’image spécifique rendue disponible dans votre infrastructure Azure Stack Hub par votre opérateur Azure Stack Hub. Un tableau listant les versions du moteur AKS et les versions Kubernetes prises en charge correspondantes est disponible dans [Versions de Kubernetes prises en charge](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-aks-engine-versions). Par exemple, la version du moteur AKS `v0.55.0` dépend de la version `2020.08.24` de la distribution d’images de base AKS Ubuntu 16.04-LTS. Demandez à votre opérateur Azure Stack Hub de télécharger la version d’image spécifique à partir de la Place de marché Azure vers la Place de marché Azure Stack Hub.

Une erreur est déclenchée si l’image n’est pas disponible dans votre Place de marché Azure Stack Hub. Par exemple, si vous utilisez actuellement le moteur AKS version v0.55.0 et que la version `2020.08.24` de la distribution d’images de base AKS Ubuntu 16.04-LTS n’est pas disponible, l’erreur suivante s’affiche lors de l’exécution du moteur AKS : 

```Text  
The platform image 'microsoft-aks:aks:aks-ubuntu-1604-202003:2020.08.24' is not available. 
Verify that all fields in the storage profile are correct.
```

Vous pouvez vérifier la version actuelle de votre moteur AKS en exécutant la commande suivante :

```bash  
$ aks-engine version
Version: v0.55.0
GitCommit: 44a35c00c
GitTreeState: clean
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Déployer le moteur AKS sur Windows dans Azure Stack Hub](azure-stack-kubernetes-aks-engine-deploy-windows.md)  
> [Déployer le moteur AKS sur Linux dans Azure Stack Hub](azure-stack-kubernetes-aks-engine-deploy-linux.md)
