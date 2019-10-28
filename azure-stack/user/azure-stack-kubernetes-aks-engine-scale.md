---
title: Mettre à l’échelle un cluster Kubernetes sur Azure Stack | Microsoft Docs
description: Découvrez comment mettre à l’échelle un cluster Kubernetes sur Azure Stack.
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
ms.date: 09/25/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/25/2019
ms.openlocfilehash: b7d20327410ace5b5ad12d34a24ff474c1a384b0
ms.sourcegitcommit: 4a2318ad395b2a931833ccba4430d8d04cdd8819
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72780476"
---
# <a name="scale-a-kubernetes-cluster-on-azure-stack"></a>Mettre à l’échelle un cluster Kubernetes sur Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Vous pouvez mettre à l’échelle votre cluster avec le moteur AKS à l’aide de la commande **scale**. La commande **scale** réutilise votre fichier de configuration de cluster (`apimodel.json`) inclus dans le répertoire de sortie en tant qu’entrée pour un nouveau déploiement Azure Resource Manager. Le moteur exécute l’opération de mise à l’échelle sur le pool d’agents spécifié. Lorsque l’opération de mise à l’échelle est terminée, le moteur met à jour la définition du cluster dans ce même fichier `apimodel.json` afin de refléter le nouveau nombre de nœuds et donc la configuration de cluster mise à jour actuelle.

## <a name="scale-a-cluster"></a>Mettre à l’échelle un cluster

La commande `aks-engine scale` peut augmenter ou diminuer le nombre de nœuds dans un pool d’agents existant dans un cluster Kubernetes `aks-engine`. Les nœuds sont toujours ajoutés ou supprimés à la fin du pool d’agents. Les nœuds sont isolés et drainés avant la suppression.

### <a name="values-for-the-scale-command"></a>Valeurs de la commande scale

Les paramètres suivants sont utilisés par la commande scale pour rechercher votre fichier de définition de cluster afin de mettre à jour votre cluster.

| Paramètre | Exemples | Description |
| --- | --- | --- | 
| azure-env | AzureStackCloud | Lorsque vous utilisez Azure Stack, les noms d’environnement doivent être définis sur `AzureStackCloud`. | 
| location | local | Il s’agit de la région de votre instance Azure Stack. Pour un Kit de développement Azure Stack (ASDK), la région a la valeur `local`.  | 
| resource-group | kube-rg | Nom du groupe de ressources qui contient votre cluster. | 
| subscription-id |  | GUID de l’abonnement qui contient les ressources utilisées par votre cluster. Vérifiez que votre quota est suffisant sur votre abonnement pour la mise à l’échelle. | 
| client-id |  | ID client du principal du service utilisé lors de la création de votre cluster à partir du moteur AKS. | 
| client-secret |  | Secret du principal du service utilisé lors de la création de votre cluster. | 
| api-model | kube-rg/apimodel.json | Chemin de votre fichier de définition de cluster (apimodel.json). Exemple : _output/\<dnsPrefix>/apimodel.json | 
| -new-node-count | 9 | Nombre de nœuds souhaité. | 
| -master-FQDN |  | Nom de domaine complet maître. Nécessaire lors d’un scale-down. |
| identity-system | adfs | facultatif. Spécifiez votre solution de gestion des identités si vous utilisez Active Directory Federated Services (AD FS). |

Vous devez spécifier le paramètre **--azure-env** lors de la mise à l’échelle d’un cluster dans Azure Stack. Pour plus d’informations sur les paramètres et leurs valeurs utilisées dans la commande **scale** pour le moteur AKS, consultez [Échelle : paramètres](https://github.com/Azure/aks-engine/blob/master/docs/topics/scale.md#parameters).

### <a name="command-to-scale-your-cluster"></a>Commande pour mettre à l’échelle votre cluster

Pour mettre à l’échelle le cluster, vous exécutez la commande suivante :

```bash
aks-engine scale \
    --azure-env AzureStackCloud   \
    --location <for an ASDK is local> \
    --resource-group <cluster resource group>
    --subscription-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --client-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --client-secret xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --api-model <path to your apomodel.json file>
    --new-node-count <desired node count> \
    --master-FQDN <master FQDN> \
    --identity-system adfs # required if using AD FS
```

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le [moteur AKS sur Azure Stack](azure-stack-kubernetes-aks-engine-overview.md)
- [Mettre à niveau un cluster Kubernetes sur Azure Stack](azure-stack-kubernetes-aks-engine-upgrade.md)