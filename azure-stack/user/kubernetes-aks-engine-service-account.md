---
title: Activation de la projection du volume de jeton de compte de service pour le moteur AKS sur Azure Stack Hub
description: Découvrir comment activer la projection du volume de jeton de compte de service pour le moteur AKS sur Azure Stack Hub
author: mattbriggs
ms.topic: article
ms.date: 10/23/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 10/23/2020
ms.openlocfilehash: 5e712577bb01f7f084c24eadfd3931ebb67f23d9
ms.sourcegitcommit: 716ca50bd198fd51a4eec5b40d5247f6f8c16530
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92906180"
---
# <a name="enabling-service-account-token-volume-projection-for-the-aks-engine-on-azure-stack-hub"></a>Activation de la projection du volume de jeton de compte de service pour le moteur AKS sur Azure Stack Hub

Istio est une couche de maillage de service open source configurable qui connecte, supervise et sécurise les conteneurs dans un cluster Kubernetes. Istio 1.3 et les versions ultérieures utilisent une fonctionnalité de Kubernetes appelée *projection du volume de jeton de compte de service*. Cette fonctionnalité n’est pas activée par défaut dans les clusters Kubernetes déployés par le moteur AKS. Dans cet article, vous trouverez les propriétés JSON de modèle d’API dans l’élément `apiServerConfig` qui affiche les indicateurs de serveur d’API Kubernetes nécessaires afin d’activer la projection du volume de jeton de compte de service pour votre cluster.

Pour plus d’informations sur la projection du volume de jeton de compte de service, consultez [Projection du volume de jeton de compte de service](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection).

## <a name="enable-service-account-token-volume-projection"></a>Activer la projection du volume de jeton de compte de service

Pour activer la projection du volume de jeton de compte de service, ajoutez les paramètres suivants dans votre fichier JSON de modèle d’API. 

```json
{
    "kubernetesConfig": {
        "apiServerConfig": {
            "--service-account-api-audiences": "api,istio-ca",
            "--service-account-issuer": "kubernetes.default.svc",
            "--service-account-signing-key-file": "/etc/kubernetes/certs/apiserver.key"
        }
    }
}
```

> [!Note]  
> Vous devrez peut-être ajuster `--service-account-api-audiences` et `--service-account-issuer` à votre cas d’usage spécifique.

Pour obtenir un exemple complet de modèle d’API, reportez-vous à [istio.json](https://github.com/Azure/aks-engine/blob/master/examples/service-mesh/istio.json).

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [le moteur AKS sur Azure Stack Hub](azure-stack-kubernetes-aks-engine-overview.md)
- [Mettre à niveau un cluster Kubernetes sur Azure Stack Hub](azure-stack-kubernetes-aks-engine-upgrade.md)
