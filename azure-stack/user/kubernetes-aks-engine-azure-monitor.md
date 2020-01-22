---
title: Utiliser Azure Monitor pour conteneurs sur Azure Stack Hub | Microsoft Docs
description: Découvrez comment utiliser Azure Monitor pour conteneurs sur Azure Stack Hub.
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
ms.date: 11/15/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 11/15/2019
ms.openlocfilehash: 4fa741970a2fb8c82bab3c9f7eb16a3aefd31ecd
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75883114"
---
# <a name="use-azure-monitor-for-containers-on-azure-stack-hub"></a>Utiliser Azure Monitor pour conteneurs sur Azure Stack Hub

Vous pouvez utiliser [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/) pour conteneurs afin de superviser les conteneurs d’un cluster Kubernetes déployé avec un moteur AKS dans Azure Stack Hub. 

> [!IMPORTANT]
> Azure Monitor pour conteneurs sur Azure Stack Hub est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Vous pouvez examiner les performances d’un conteneur avec Azure Monitor en collectant des métriques sur la mémoire et le processeur à partir des contrôleurs, nœuds et conteneurs disponibles dans Kubernetes par le biais de l’API Metrics. Par ailleurs, le service collecte les journaux de conteneur. Vous pouvez utiliser ces journaux pour diagnostiquer les problèmes relatifs à votre cluster local à partir d’Azure. Une fois la supervision configurée à partir de vos clusters Kubernetes, ces métriques et ces journaux sont automatiquement collectés. Une version conteneurisée de l’agent Azure Monitor Log Analytics pour Linux collecte les journaux. Azure Monitor stocke les métriques et les journaux dans votre espace de travail Log Analytics, accessible dans votre abonnement Azure.

Il existe deux méthodes pour activer Azure Monitor sur votre cluster. Ces deux méthodes vous obligent à configurer un espace de travail Azure Monitor Log Analytics dans Azure.

## <a name="prerequisites"></a>Conditions préalables requises

Pour les deux méthodes, les [prérequis](https://github.com/Helm/charts/tree/master/incubator/azuremonitor-containers#pre-requisites) listés dans la page [Azure Monitor – Containers](https://github.com/Helm/charts/tree/master/incubator/azuremonitor-containers) doivent être respectés.

## <a name="method-one"></a>Méthode 1

Vous pouvez aussi utiliser le graphique [Helm](https://helm.sh/) pour installer les agents de supervision dans votre cluster. Suivez les instructions de l’article [Azure Monitor – Containers](https://github.com/Helm/charts/tree/master/incubator/azuremonitor-containers).

## <a name="method-two"></a>Méthode 2

Vous pouvez spécifier un **module complémentaire** dans le fichier JSON de spécification de cluster du moteur AKS. Le fichier est également appelé modèle d’API. Dans ce module complémentaire, spécifiez la version encodée en base64 des paramètres **WorkspaceGUID** et **WorkspaceKey** de l’espace de travail Azure Log Analytics dans lequel seront stockées les informations de supervision.

Les définitions d’API prises en charge pour le cluster Azure Stack Hub sont fournies dans cet exemple : [kubernetes-container-monitoring_existing_workspace_id_and_key.json](https://github.com/Azure/aks-engine/blob/master/examples/addons/container-monitoring/kubernetes-container-monitoring_existing_workspace_id_and_key.json). Recherchez en particulier la propriété **addons** dans **kubernetesConfig** :

```JSON  
 "orchestratorType": "Kubernetes",
       "kubernetesConfig": {
         "addons": [
           {
             "name": "container-monitoring",
             "enabled": true,
             "config": {
               "workspaceGuid": "<Azure Log Analytics Workspace Guid in Base-64 encoded>",
               "workspaceKey": "<Azure Log Analytics Workspace Key in Base-64 encoded>"
             }
           }
         ]
       }
```

## <a name="next-steps"></a>Étapes suivantes

- Consultez l'article [Le moteur AKS sur Azure Stack Hub](azure-stack-kubernetes-aks-engine-overview.md).  
- Consultez l’article [Vue d’ensemble d’Azure Monitor pour conteneurs](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview).
