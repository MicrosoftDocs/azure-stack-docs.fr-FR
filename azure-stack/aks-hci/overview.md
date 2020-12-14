---
title: Qu’est-ce qu’Azure Kubernetes Service sur Azure Stack HCI ?
description: Azure Kubernetes Service sur Azure Stack HCI est une implémentation locale d’Azure Kubernetes Service (AKS), qui automatise l’exécution d’applications conteneurisées à grande échelle.
ms.topic: overview
author: v-susbo
ms.author: v-susbo
ms.date: 12/02/2020
ms.openlocfilehash: a7d97d2454e9d77c1760180b46be9ce219d6af68
ms.sourcegitcommit: 0efffe1d04a54062a26d5c6ce31a417f511b9dbf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2020
ms.locfileid: "96612656"
---
# <a name="what-is-azure-kubernetes-service-on-azure-stack-hci"></a>Qu’est-ce qu’Azure Kubernetes Service sur Azure Stack HCI ?
> S’applique à : AKS sur Azure Stack HCI, AKS Runtime sur Windows Server 2019 Datacenter

Azure Kubernetes Service sur Azure Stack HCI est une implémentation locale d’Azure Kubernetes Service (AKS), qui automatise l’exécution d’applications conteneurisées à grande échelle. Azure Kubernetes Service est maintenant en préversion sur Azure Stack HCI et Windows Server 2019 Datacenter, ce qui vous permet de commencer à héberger des conteneurs Linux et Windows dans votre centre de données plus rapidement.

Pour commencer à utiliser le service Azure Kubernetes localement, [inscrivez-vous à la préversion](https://aka.ms/AKS-HCI-Evaluate) (aucun coût supplémentaire facturé pendant la préversion), puis [configurez Azure Kubernetes Service sur Azure Stack HCI](setup.md). Si vous préférez utiliser Azure Kubernetes Service pour orchestrer vos conteneurs basés sur le cloud, consultez [Azure Kubernetes Service dans Azure](/azure/aks/intro-kubernetes).

Les sections suivantes abordent certaines des raisons d’utiliser le Azure Kubernetes Service sur Azure Stack HCI, puis répondent à quelques questions courantes sur le service et sa prise en main. Pour en savoir plus sur les conteneurs, consultez [Windows et conteneurs](/virtualization/windowscontainers/about/) et pour en savoir plus sur Kubernetes, consultez [Concepts de base de Kubernetes](kubernetes-concepts.md) ou [Kubernetes.io](https://kubernetes.io).

## <a name="use-aks-to-automate-management-of-containerized-applications"></a>Utiliser AKS pour automatiser la gestion des applications en conteneur

Si Docker et Windows permettent de gérer manuellement quelques conteneurs, les applications utilisent souvent cinq, dix, voire des centaines de conteneurs, et c'est là qu'intervient l'orchestrateur Kubernetes.

Kubernetes est un orchestrateur open source pour l’automatisation de la gestion des conteneurs à grande échelle. Azure Kubernetes Service simplifie le déploiement local de Kubernetes en fournissant des assistants pour la configuration de Kubernetes et des modules complémentaires essentiels sur Azure Stack HCI, et pour la création de clusters Kubernetes afin d’accueillir vos charges de travail.

Voici quelques-unes des fonctionnalités fournies par la préversion d’Azure Kubernetes Service sur Azure Stack HCI qui vous permettent de :

- Déployer des applications en conteneur à grande échelle vers des clusters Kubernetes exécutés sur le cluster Azure Stack HCI
- Déployer et gérer les applications en conteneur Linux et Windows
- Déployer AKS sur Azure Stack HCI à l’aide de Windows Admin Center ou de PowerShell
- Augmenter ou diminuer la taille des clusters en ajoutant ou en supprimant des nœuds dans le cluster Kubernetes
- Gérer le stockage et la mise en réseau sur votre cluster Kubernetes
- Fournir des mises à jour automatiques pour votre déploiement Kubernetes
- Mettre à niveau vers la dernière version de Kubernetes disponible
- Utiliser les services Azure populaires via Azure Arc pour Kubernetes

## <a name="simplify-setting-up-kubernetes"></a>Simplifier la configuration de Kubernetes

Azure Kubernetes Service simplifie le processus de configuration de Kubernetes sur Azure Stack HCI et de Windows Server 2019 Datacenter, et comprend les fonctionnalités suivantes :

- Assistant Windows Admin Center pour la configuration de Kubernetes et de ses dépendances (par exemple, kubeadm, kubelet, kubectl et un module complémentaire réseau pod)
- Assistant Windows Admin Centers pour la création de clusters Kubernetes afin d’exécuter vos applications en conteneur
- Applets de commande PowerShell permettant de configurer Kubernetes et de créer des clusters Kubernetes, au cas où vous devriez plutôt écrire le script de configuration de l’hôte et de création du cluster Kubernetes

## <a name="view-and-manage-kubernetes-using-on-premises-tools-or-azure-arc"></a>Afficher et gérer des Kubernetes à l’aide d’outils locaux ou d’Azure Arc

Après que vous avez configuré Azure Kubernetes Service localement et créé un cluster Kubernetes, nous vous proposons deux méthodes pour gérer et surveiller votre infrastructure Kubernetes :

- **Localement à l’aide d’outils populaires tels que Kubectl et Kubernetes Dashboard** : utilisez une interface web open source pour déployer des applications sur un cluster Kubernetes, gérer des ressources de cluster, dépanner et afficher des applications en cours d’exécution.
- **Dans le Portail Azure à l’aide d’Azure Arc** : utilisez Azure Arc pour gérer les applications déployées au sommet des clusters Kubernetes dans vos environnements cloud et locaux. 
<br>Azure Arc vous permet également de gérer vos clusters Kubernetes avec d’autres services Azure, notamment :

  - Azure Monitor
  - Azure Policy
  - Contrôle d’accès en fonction du rôle

## <a name="run-linux-and-windows-containers"></a>Exécuter des conteneurs Linux et Windows

Azure Container Service prend entièrement en charge les conteneurs basés sur Linux et Windows. Lorsque vous créez un cluster Kubernetes sur Azure Stack HCI, vous pouvez choisir de créer des pools de nœuds (groupes de nœuds de cluster Kubernetes identiques) pour exécuter des conteneurs Linux ou des conteneurs Windows, voire les deux. 

Azure Container Service crée les nœuds Linux et Windows afin que vous n’ayez pas à gérer directement les systèmes d’exploitation Linux ou Windows.

## <a name="secure-your-container-infrastructure"></a>Sécuriser votre infrastructure de conteneur

Azure Kubernetes Service comprend un certain nombre de fonctionnalités pour vous aider à sécuriser votre infrastructure de conteneur :

- **Isolement basé sur l’hyperviseur pour les nœuds Worker** : chaque cluster Kubernetes s’exécute sur son propre ensemble de machines virtuelles dédiées et isolées de sorte que les locataires puissent partager la même infrastructure physique.
- **Images Linux et Windows gérées par Microsoft pour les nœuds Worker** : les nœuds Worker exécutent des images de machines virtuelles Linux et Windows créées par Microsoft pour adhérer aux meilleures pratiques en matière de sécurité. Microsoft actualise également ces images tous les mois avec les dernières mises à jour de sécurité.

Restez informé car la sécurité est un domaine d’investissement permanent pour la préversion d’Azure Kubernetes Service sur Azure Stack HCI.

## <a name="where-can-i-run-azure-kubernetes-service"></a>Où puis-je exécuter Azure Kubernetes Service ?

Azure Kubernetes Service est disponible sur les plateformes suivantes :

- Dans le cloud Azure via [Azure Kubernetes Service dans Azure](/azure/aks/intro-kubernetes)
- Localement via Azure Kubernetes Service sur Azure Stack HCI (sujet traité par le présent article)
- Localement via le runtime Azure Kubernetes Service sur Windows Server (cet article s’applique également à AKS sur Windows Server)
- Localement dans un environnement Azure Stack Hub utilisant le [moteur AKS sur Azure Stack Hub](../user/azure-stack-kubernetes-aks-engine-overview.md).

## <a name="how-does-kubernetes-work-on-azure-stack-hci"></a>Comment Kubernetes fonctionne-t-il sur Azure Stack HCI ?

Azure Kubernetes Service fonctionne un peu différemment lorsqu’il est exécuté sur Azure Stack HCI que lors de son utilisation dans le cloud Azure :

- Le service Kubernetes dans Azure est un service hébergé dans lequel la majeure partie de l’infrastructure de gestion Kubernetes (plan de contrôle) est gérée pour vous. Le plan de contrôle et vos applications en conteneur s’exécutent sur des machines virtuelles Azure.
- Avec Azure Kubernetes Service sur Azure Stack HCI, vous configurez le service directement sur votre cluster Azure Stack HCI, ce qui vous permet de contrôler le plan de contrôle. Le plan de contrôle, vos applications en conteneur et Azure Kubernetes Service lui-même s’exécutent tous sur des machines virtuelles hébergées par votre cluster hyperconvergé.

Une fois Azure Kubernetes Service configuré sur votre cluster Azure Stack HCI, il fonctionne de la même façon qu’Azure Kubernetes Service hébergé : vous utilisez le service pour créer des clusters Kubernetes qui exécutent vos applications en conteneur. Ces clusters Kubernetes sont des groupes de machines virtuelles qui agissent en tant que nœuds Worker, en exécutant vos conteneurs d’application. Le cluster Kubernetes contient également un plan de contrôle, qui se compose de services système Kubernetes utilisés pour orchestrer les conteneurs d’application.

Voici quelques diagrammes simplifiés montrant comment les architectures Azure Kubernetes Service sont comparées lorsqu’elles sont exécutées dans Azure et Azure Stack HCI.

:::image type="content" source="media\overview\aks-azure-architecture.png" alt-text="Architecture d’Azure Kubernetes Service hébergé dans Azure, montrant comment les services de la plateforme et la majeure partie du plan de contrôle sont gérés par Azure, tandis que les clusters Kubernetes qui exécutent vos applications en conteneur sont gérés par le client." lightbox="media\overview\aks-azure-architecture.png":::

:::image type="content" source="media\overview\aks-hci-architecture.png" alt-text="Architecture d’Azure Kubernetes Service sur Azure Stack HCI, montrant comment tout fonctionne au sommet du cluster Azure Stack HCI, notamment la plateforme Azure Kubernetes Service, le plan de contrôle et les clusters Kubernetes qui permettent l’exécution de vos applications en conteneur." lightbox="media\overview\aks-hci-architecture.png":::

## <a name="what-you-need-to-get-started"></a>Ce dont vous avez besoin pour commencer

Les sections suivantes résument ce dont vous avez besoin pour faire fonctionner Azure Kubernetes Service sur Azure Stack HCI. Pour plus d’informations, consultez [Avant d’installer Azure Kubernetes Service sur Azure Stack HCI](system-requirements.md).

### <a name="on-your-windows-admin-center-system"></a>Sur votre système Windows Admin Center

La configuration requise pour votre ordinateur exécutant la passerelle Windows Admin Center est la suivante :

- Une machine Windows 10 (pour le moment, nous ne prenons pas en charge l’exécution de Windows Admin Center sur Azure Stack HCI ou Windows Server 2019 Datacenter).
- 60 Go d’espace libre
- Inscription auprès d’Azure
- Dans le même domaine que le cluster Azure Stack HCI ou Windows Server 2019 Datacenter

### <a name="on-the-azure-stack-hci-cluster-or-windows-server-2019-datacenter-failover-cluster-that-hosts-azure-kubernetes-service"></a>Sur le cluster Azure Stack HCI ou le cluster de basculement Windows Server 2019 Datacenter qui héberge Azure Kubernetes Service

Le cluster Azure Stack HCI ou le cluster de basculement Windows Server 2019 Datacenter présente les exigences suivantes :

- Quatre serveurs au maximum dans le cluster pour cette préversion
- 1 To de capacité disponible dans le pool de stockage pour le service Azure Kubernetes
- Au moins 30 Go de mémoire disponible pour l’exécution des machines virtuelles Azure Kubernetes Service
- Tous les serveurs du cluster doivent utiliser la région EN-US et la sélection de la langue pour cette préversion

Pour connaître la configuration système requise d’Azure Stack HCI, consultez [Configuration système requise d’Azure Stack HCI](../hci/concepts/system-requirements.md).

### <a name="the-network-configuration-for-azure-stack-hci"></a>Configuration réseau pour Azure Stack HCI

Le réseau connecté aux machines virtuelles du cluster Azure Stack HCI ou or Windows Server 2019 Datacenter nécessite une étendue dédiée d’adresses DHCP IPv4 disponibles pour Azure Kubernetes Service et accessibles par les machines virtuelles du cluster Azure Stack HCI ou Windows Server 2019 Datacenter.

## <a name="next-steps"></a>Étapes suivantes

Pour commencer à utiliser Azure Kubernetes Service sur Azure Stack HCI, consultez les articles suivants :

- [Examiner les conditions requises](system-requirements.md)
- [Configurer Azure Kubernetes Service sur Azure Stack HCI](create-kubernetes-cluster.md)
