---
title: Superviser les clusters Azure Kubernetes Service sur Azure Stack HCI
description: Superviser et afficher les données de journalisation des clusters Azure Kubernetes Service sur Azure Stack HCI
author: v-susbo
ms.topic: how-to
ms.date: 01/26/2021
ms.author: v-susbo
ms.reviewer: ''
ms.openlocfilehash: 728f23954d99f6e8cd9373467ed8d104a457e636
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "101874391"
---
# <a name="monitor-aks-on-azure-stack-hci-clusters"></a>Superviser les clusters AKS sur Azure Stack HCI

Il existe deux types de solutions de supervision et de journalisation disponibles pour AKS sur Azure Stack HCI. Voici les deux solutions mises en comparaison : 

| Solution  | Connectivité Azure  | Prise en charge et service  | Coût | Déploiement |
| ------- |  ------------  | ---------  | --------------  | ---------------- |
| Azure Monitor | Exige la connexion du cluster AKS sur Azure Stack HCI à Azure par le biais d’Azure Arc pour Kubernetes | Prise en charge intégrale gérée par Microsoft | Exige l’inscription au service Azure Monitor |  Utilisation d’Azure Arc pour la [supervision des clusters](#monitor-clusters-using-azure-monitor) |
| Supervision et journalisation locales | N’exige pas de connectivité Azure | Prise en charge en tant que logiciel Open Source par Microsoft (pas de contrat de support ni de SLA), par la communauté et/ou par des fournisseurs externes  | Dépend du fournisseur | Piloté par les clients, consultez [Surveiller les clusters avec la supervision locale](#monitor-clusters-using-on-premises-monitoring) |

## <a name="monitor-clusters-using-azure-monitor"></a>Superviser les clusters avec Azure Monitor
Pour utiliser Azure Monitor avec les clusters AKS sur Azure Stack HCI, suivez les étapes décrites dans les deux rubriques ci-dessous : 

- [Connecter votre cluster à Azure en utilisant Azure Arc pour Kubernetes](./connect-to-arc.md)  
- [Activer Azure Monitor sur un cluster Kubernetes avec Azure Arc](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-enable-arc-enabled-clusters) 

## <a name="monitor-clusters-using-on-premises-monitoring"></a>Surveiller les clusters avec la supervision locale

La supervision de l’intégrité, des performances et de l’utilisation de ressources des charges de travail et des nœuds de plan de contrôle sur votre cluster est capitale lors de l’exécution d’applications en production. Pour savoir comment configurer une solution de supervision locale, consultez l’[installation de Prometheus et de Grafana](https://github.com/microsoft/AKS-HCI-Apps/tree/main/Monitoring). Cette solution de supervision comporte les deux outils suivants : 

- **Prometheus** est un kit de ressources de supervision et d’alerte que vous pouvez utiliser pour surveiller les charges de travail conteneurisées. Prometheus utilise différents types de collecteurs et d’agents pour collecter des métriques et les stocker dans une base de données que vous pouvez interroger et dont vous pouvez consulter les rapports. 

- **Grafana** est un outil utilisé pour afficher, interroger et visualiser des métriques dans les tableaux de bord Grafana. Il est préconfiguré pour utiliser Prometheus comme source de données. 

### <a name="install-monitoring-tools"></a>Installer les outils de supervision

Pour configurer rapidement une solution de supervision simple dans votre environnement, exécutez le script PowerShell [Setup-Monitoring.ps1](https://github.com/microsoft/AKS-HCI-Apps/tree/main/Monitoring#easy-steps-to-setup-monitoring-to-use-local-port-forward-to-access-grafana). Le script comprend toutes les étapes de configuration nécessaires à la mise en place d’une solution de supervision rapidement opérationnelle. 

Si vous souhaitez inclure un contrôleur d’entrée avec Grafana, suivez les étapes vous permettant d’[utiliser un contrôleur d’entrée pour accéder à Grafana](https://github.com/microsoft/AKS-HCI-Apps/tree/main/Monitoring#detailed-steps-to-setup-monitoring-to-use-ingress-controller-to-access-grafana). Un contrôleur d’entrée est un logiciel fournissant un proxy inversé, un routage du trafic configurable et un arrêt TLS (Transport Layer Security) pour les services Kubernetes.

### <a name="on-premises-logging"></a>Journalisation locale

La journalisation est capitale dans la résolution des problèmes et les diagnostics. La solution de journalisation dans AKS sur Azure Stack HCI est basée sur Elasticsearch, Fluent Bit et Kibana (EFK). Ces composants sont tous déployés en tant que conteneurs : 

- Fluent Bit est le processeur et redirecteur de journal qui collecte les données et les journaux à partir de différentes sources, puis les met en forme, les unifie et les stocke dans Elasticsearch. 
- Elasticsearch est un moteur de recherche et d’analytique distribué, capable de stocker de manière centralisée les journaux des recherches rapides et de l’analytique données.  
- Kibana fournit des visualisations interactives dans un tableau de bord web. Cet outil vous permet d’afficher et d’interroger les journaux stockés dans Elasticsearch, puis de les visualiser dans des graphes et des tableaux de bord.

Pour configurer une solution de journalisation locale, consultez les étapes suivantes qui permettent de [configurer la journalisation pour accéder à Kibana](https://github.com/microsoft/AKS-HCI-Apps/tree/main/Logging#easy-steps-to-setup-logging-to-use-local-port-forward-to-access-kibana). Cet article comprend tous les composants nécessaires pour collecter, agréger et interroger les journaux de conteneurs sur le cluster. 

Pour connaître les étapes de configuration avancée, consultez la [journalisation Windows](https://github.com/microsoft/AKS-HCI-Apps/tree/main/Logging#detailed-steps-to-setup-logging).

## <a name="next-steps"></a>Étapes suivantes

- [Déployer une application Linux sur un cluster Kubernetes](./deploy-linux-application.md).
- [Déployer une application Windows Server sur un cluster Kubernetes](./deploy-windows-application.md).
- [Concepts de base de Kubernetes](kubernetes-concepts.md).