---
title: Configurer les prérequis pour le moteur AKS sur Azure Stack | Microsoft Docs
description: Établissez les conditions d’exécution du moteur AKS sur votre infrastructure Azure Stack.
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
ms.openlocfilehash: b56c5a2be45e9f92630283a2b702f37471e80290
ms.sourcegitcommit: 534117888d9b7d6d363ebe906a10dcf0acf8b685
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72173098"
---
# <a name="set-up-the-prerequisites-for-the-aks-engine-on-azure-stack"></a>Configurer les prérequis pour le moteur AKS sur Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Vous pouvez installer le moteur AKS sur une machine virtuelle dans votre environnement ou sur toute machine cliente dotée d’un accès à votre point de terminaison Azure Stack Resource Manager. Vous avez besoin des éléments suivants avant d’exécuter le moteur : un serveur Ubuntu de base AKS et une extension de script personnalisé Linux disponibles dans votre abonnement, une identité de principal du service dotée d’un rôle de contributeur et une paire de clés privée/publique pour l’accès SSH à votre serveur Ubuntu. De plus, si vous utilisez le Kit de développement Azure Stack, votre machine doit approuver les certificats appropriés.

Si vous avez vos prérequis, vous pouvez commencer à [définir votre cluster](azure-stack-kubernetes-aks-engine-deploy-cluster.md).

Si vous êtes l'opérateur du cloud pour Azure Stack et souhaitez proposer le moteur AKS, suivez les instructions de la rubrique [Ajouter le moteur AKS à la Place de marché Azure Stack](../operator/azure-stack-aks-engine.md).

## <a name="prerequisites-for-the-aks-engine"></a>Prérequis pour le moteur AKS

Pour utiliser le moteur AKS, vous devez disposer des ressources suivantes. Gardez à l’esprit que le moteur AKS est destiné à être utilisé par les locataires d’Azure Stack pour déployer des clusters Kubernetes dans leur abonnement locataire. La seule implication nécessaire de l’opérateur Azure Stack a trait au téléchargement d’éléments de la place de marché et à la création d’une identité de principal du service. Vous trouverez plus d’informations dans le tableau suivant.

| Configuration requise | Description | Obligatoire | Instructions |
| --- | --- | --- | --- |
| Extension de script personnalisé Linux | Extension de script personnalisé Linux 2.0<br>Offre : Script personnalisé pour Linux 2.0<br>Version : 2.0.6 (ou la dernière version)<br>Éditeur : Microsoft Corp | Obligatoire | Si vous n’avez pas cet élément dans votre abonnement, contactez votre opérateur cloud. |
| Image Ubuntu de base AKS | Image de base AKS<br>Offre : aks<br>Version : 2019.09.19 (ou version plus récente)<br>Éditeur : microsoft-aks<br>Référence SKU : aks-ubuntu-1604-201909 | Obligatoire | Si vous n’avez pas cet élément dans votre abonnement, contactez votre opérateur cloud. Pour plus d’informations sur la dépendance de version, consultez [Correspondance du moteur avec la version de l’image de base](#matching-engine-to-base-image-version).<br> Si vous êtes l'opérateur du cloud pour Azure Stack et souhaitez proposer le moteur AKS, suivez les instructions de la rubrique [Ajouter le moteur AKS à la Place de marché Azure Stack](../operator/azure-stack-aks-engine.md). |
| Abonnement Azure Stack | Vous accédez à vos offres Azure Stack par le biais d’abonnements. Une offre contient les services à votre disposition. | Obligatoire | Pour être en mesure de déployer des charges de travail de locataire dans Azure Stack, vous devez d’abord obtenir un [abonnement Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-subscribe-services). |
| Identité de principal du service (SPN) |  Une application qui a besoin pour déployer ou configurer des ressources via Azure Resource Manager doit être représentée par un principal de service. | Obligatoire | Vous devrez peut-être contacter votre opérateur Azure Stack pour cet élément.  Pour obtenir des instructions, consultez [Utiliser une identité d’application pour accéder à des ressources](https://docs.microsoft.com/azure-stack/operator/azure-stack-create-service-principals). |
| Rôle de **contributeur** attribué (SPN) | Pour autoriser une application à accéder aux ressources de votre abonnement en utilisant son principal du service, vous devez attribuer à ce dernier un rôle pour une ressource spécifique. | Obligatoire | Pour obtenir des instructions, consultez [Attribuer un rôle](https://docs.microsoft.com/azure-stack/operator/azure-stack-create-service-principals#assign-a-role). |
| Resource group | Un groupe de ressources est un conteneur réunissant les ressources associées d’une solution Azure. Si vous ne spécifiez pas de groupe de ressources existant, l’outil en crée un pour vous. | Facultatif | [Gérer les groupes de ressources Azure Resource Manager à l’aide du portail Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-portal) |
| Clé publique privée | Pour utiliser une connexion SSH ouverte entre la machine de développement et la machine virtuelle serveur dans votre instance Azure Stack qui héberge votre application web, il est parfois nécessaire de créer une paire clé privée-clé publique SSH (Secure Shell). | Obligatoire | Pour obtenir des instructions sur la génération d’une clé, voir [Génération d’une clé SSH](https://docs.microsoft.com/azure-stack/user/azure-stack-dev-start-howto-ssh-public-key).|

> [!Note]  
> Vous pouvez également créer les prérequis pour le moteur AKS avec [Azure CLI pour Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles-azurecli2) ou [Azure Stack PowerShell](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install).

## <a name="matching-engine-to-base-image-version"></a>Correspondance du moteur avec la version de l’image de base

Le moteur AKS utilise une image générée, l’**image de base AKS**. Toute version du moteur AKS dépend d’une version d’image spécifique rendue disponible dans votre infrastructure Azure Stack par votre opérateur Azure Stack. Vous trouverez un tableau listant les versions du moteur AKS et les versions Kubernetes prises en charge correspondantes dans [Versions de Kubernetes prises en charge](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions). Par exemple, la version du moteur AKS `v0.41.2` dépend de la version `2019.09.19` de l’image de base AKS. Demandez à votre opérateur Azure Stack de télécharger la version d’image spécifique à partir de la Place de marché Azure vers la Place de marché Azure Stack.

Une erreur est déclenchée si l’image n’est pas disponible dans votre Place de marché Azure Stack. Par exemple, si vous utilisez actuellement le moteur AKS version v 0.41.2 et que la version de l’image de base AKS `2019.09.19` n’est pas disponible, l’erreur suivante s’affiche lors de l’exécution du moteur AKS : 

```Text  
The platform image 'microsoft-aks:aks:aks-ubuntu-1604-201908:2019.08.09' is not available. 
Verify that all fields in the storage profile are correct.
```

Vous pouvez vérifier la version actuelle de votre moteur AKS en exécutant la commande suivante :

```bash  
$ aks-engine version
Version: v0.39.1
GitCommit: 6fff62731
GitTreeState: clean
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Déployer le moteur AKS sur Windows dans Azure Stack](azure-stack-kubernetes-aks-engine-deploy-windows.md)  
> [Déployer le moteur AKS sur Linux dans Azure Stack](azure-stack-kubernetes-aks-engine-deploy-linux.md)