---
title: Qu’est-ce qu’Azure Kubernetes Service sur Azure Stack HCI ?
description: Azure Kubernetes Service sur Azure Stack HCI est une implémentation locale d’Azure Kubernetes Service (AKS), qui automatise l’exécution d’applications conteneurisées à grande échelle.
ms.topic: overview
author: jasongerend
ms.author: jgerend
ms.date: 09/22/2020
ms.openlocfilehash: fad630d143fee31c4b63b4008c02bef697475155
ms.sourcegitcommit: dabbe44c3208fbf989b7615301833929f50390ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90949372"
---
# <a name="what-is-azure-kubernetes-service-on-azure-stack-hci"></a>Qu’est-ce qu’Azure Kubernetes Service sur Azure Stack HCI ?

Azure Kubernetes Service sur Azure Stack HCI est une implémentation locale d’Azure Kubernetes Service (AKS), qui automatise l’exécution d’applications conteneurisées à grande échelle. Azure Kubernetes Service est maintenant en préversion sur Azure Stack HCI, ce qui vous permet de commencer à héberger des conteneurs Linux et Windows dans votre centre de données plus rapidement.

Pour commencer à utiliser le service Azure Kubernetes localement, [inscrivez-vous à la préversion](https://aka.ms/AKS-HCI-Evaluate) (aucun coût supplémentaire facturé pendant la préversion), puis consultez [Configurer Azure Kubernetes Service sur Azure Stack HCI](setup.md). Si vous préférez utiliser Azure Kubernetes Service pour orchestrer vos conteneurs basés sur le cloud, consultez [Azure Kubernetes Service dans Azure](/azure/aks/intro-kubernetes).

Les sections suivantes abordent certaines des raisons d’utiliser le Azure Kubernetes Service sur Azure Stack HCI, puis répondent à quelques questions courantes sur le service et sa prise en main. Pour en savoir plus sur les conteneurs, consultez [Windows et conteneurs](/virtualization/windowscontainers/about/).

## <a name="automate-management-of-containerized-applications"></a>Automatiser la gestion des applications en conteneur

Si Docker et Windows permettent de gérer manuellement quelques conteneurs, les applications utilisent souvent cinq, dix, voire des centaines de conteneurs, et c'est là qu'intervient l'orchestrateur Kubernetes.

Kubernetes est un orchestrateur open source pour l’automatisation de la gestion des conteneurs à grande échelle. Azure Kubernetes Service simplifie le déploiement local de Kubernetes en fournissant des assistants pour la configuration de Kubernetes et des modules complémentaires essentiels sur Azure Stack HCI, et pour la création de clusters Kubernetes afin d’accueillir vos charges de travail.

Voici quelques-unes des fonctionnalités fournies par la préversion d’Azure Kubernetes Service sur Azure Stack HCI qui vous permettent de :

- Déployer des applications en conteneur à grande échelle vers un cluster de machines virtuelles (appelé cluster Kubernetes) exécuté sur le cluster Azure Stack HCI
- Effectuer un basculement vers un nœud du cluster Kubernetes en cas d’échec
- Déployer et gérer les applications en conteneur Linux et Windows
- Planifier les charges de travail
- Surveiller l’intégrité
- Augmenter ou diminuer la taille des clusters en ajoutant ou en supprimant des nœuds dans le cluster Kubernetes
- Gérer la mise en réseau
- Découvrir les services
- Coordonner les mises à niveau d’application
- Affecter des pods à des nœuds de cluster avec l’affinité de nœud de cluster

Pour plus d’informations sur Kubernetes, consultez [Kubernetes.io](https://kubernetes.io).

## <a name="simplify-setting-up-kubernetes"></a>Simplifier la configuration de Kubernetes

Azure Kubernetes Service simplifie le processus de configuration de Kubernetes sur Azure Stack HCI et comprend les fonctionnalités suivantes :

- Assistant Windows Admin Center pour la configuration de Kubernetes et de ses dépendances (par exemple, kubeadm, kubelet, kubectl et un module complémentaire réseau Pod)
- Assistant Windows Admin Centers pour la création de clusters Kubernetes afin d’exécuter vos applications en conteneur
- Applets de commande PowerShell permettant de configurer Kubernetes et de créer des clusters Kubernetes, au cas où vous devriez plutôt écrire le script de configuration de l’hôte et de création du cluster Kubernetes

## <a name="view-and-manage-kubernetes-using-on-premises-tools-or-azure-arc"></a>Afficher et gérer des Kubernetes à l’aide d’outils locaux ou d’Azure Arc

Après que vous avez configuré Azure Kubernetes Service sur votre cluster Azure Stack HCI et créé un cluster Kubernetes, nous vous proposons deux méthodes pour gérer et surveiller votre infrastructure Kubernetes :

- **Localement à l’aide d’outils populaires tels que Kubectl et Kubernetes Dashboard** : utilisez une interface web open source pour déployer des applications sur un cluster Kubernetes, gérer des ressources de cluster, dépanner et afficher des applications en cours d’exécution.
- **Dans le Portail Azure à l’aide d’Azure Arc** : utilisez un service Azure pour gérer Azure Kubernetes Service et les clusters Kubernetes déployés dans vos environnements cloud et locaux. Vous pouvez utiliser Azure Arc pour ajouter et supprimer des clusters Kubernetes, ainsi que des nœuds dans un cluster Kubernetes, modifier les paramètres réseau et installer des modules complémentaires.
<br>Azure Arc vous permet également de gérer vos clusters Kubernetes avec d’autres services Azure, notamment :

  - Azure Monitor
  - Azure Policy
  - Contrôle d’accès en fonction du rôle

## <a name="run-linux-and-windows-containers"></a>Exécuter des conteneurs Linux et Windows

Azure Container Service prend entièrement en charge les conteneurs basés sur Linux et Windows. Lorsque vous créez un cluster Kubernetes sur Azure Stack HCI, vous pouvez choisir de créer des pools de nœuds (groupes de machines virtuelles identiques) pour exécuter des conteneurs Linux ou des conteneurs Windows, voire les deux. 

Azure Container Service crée les machines virtuelles Linux et Windows afin que vous n’ayez pas à gérer directement les systèmes d’exploitation Linux ou Windows.

## <a name="secure-your-container-infrastructure"></a>Sécuriser votre infrastructure de conteneur

Azure Kubernetes Service comprend un certain nombre de fonctionnalités pour vous aider à sécuriser votre infrastructure de conteneur :

- **Isolement basé sur l’hyperviseur pour les nœuds Worker** : chaque cluster Kubernetes s’exécute sur son propre ensemble de machines virtuelles dédiées et isolées de sorte que les locataires puissent partager la même infrastructure physique.
- **Images Linux et Windows gérées par Microsoft pour les nœuds Worker** : les nœuds Worker exécutent des images de machines virtuelles Linux et Windows créées par Microsoft pour adhérer aux meilleures pratiques en matière de sécurité. Microsoft actualise également ces images tous les mois avec les dernières mises à jour de sécurité.

Restez informé car la sécurité est un domaine d’investissement permanent pour la préversion d’Azure Kubernetes Service sur Azure Stack HCI.

## <a name="where-can-i-run-azure-kubernetes-service"></a>Où puis-je exécuter Azure Kubernetes Service ?

Azure Kubernetes Service est disponible sur les plateformes suivantes :

- Dans le cloud Azure via [Azure Kubernetes Service dans Azure](/azure/aks/intro-kubernetes)
- Localement via Azure Kubernetes Service sur Azure Stack HCI (sujet traité par le présent article)
- Localement dans un environnement Azure Stack Hub utilisant le [moteur AKS sur Azure Stack Hub](../user/azure-stack-kubernetes-aks-engine-overview.md).

## <a name="how-does-kubernetes-work-on-azure-stack-hci"></a>Comment Kubernetes fonctionne-t-il sur Azure Stack HCI ?

Azure Kubernetes Service fonctionne un peu différemment lorsqu’il est exécuté sur Azure Stack HCI que lors de son utilisation dans le cloud Azure :

- Le service Kubernetes dans Azure est un service hébergé dans lequel la majeure partie de l’infrastructure de gestion Kubernetes (plan de contrôle) est gérée pour vous. Le plan de contrôle et vos applications en conteneur s’exécutent sur des machines virtuelles Azure.
- Avec Azure Kubernetes Service sur Azure Stack HCI, vous configurez le service directement sur votre cluster Azure Stack HCI, ce qui vous permet de contrôler le plan de contrôle. Le plan de contrôle, vos applications en conteneur et Azure Kubernetes Service lui-même s’exécutent tous sur des machines virtuelles hébergées par votre cluster hyperconvergé.

Une fois Azure Kubernetes Service configuré sur votre cluster Azure Stack HCI, il fonctionne de la même façon qu’Azure Kubernetes Service hébergé : vous utilisez le service pour créer des clusters Kubernetes qui exécutent vos applications en conteneur. Ces clusters Kubernetes sont des groupes de machines virtuelles qui agissent en tant que nœuds Worker, en exécutant vos conteneurs d’application. Le cluster Kubernetes contient également un plan de contrôle, qui se compose de services système Kubernetes utilisés pour orchestrer les conteneurs d’application.

Voici quelques diagrammes simplifiés montrant comment les architectures Azure Kubernetes Service sont comparées lorsqu’elles sont exécutées dans Azure et Azure Stack HCI.

:::image type="content" source="media\overview\aks-azure-architecture.png" alt-text="Architecture d’Azure Kubernetes Service hébergé dans Azure, montrant comment les services de la plateforme et la majeure partie du plan de contrôle sont gérés par Azure, tandis que les clusters Kubernetes qui exécutent vos applications en conteneur sont gérés par le client." lightbox="media\overview\aks-azure-architecture.png":::

:::image type="content" source="media\overview\aks-hci-architecture.png" alt-text="Architecture d’Azure Kubernetes Service hébergé dans Azure, montrant comment les services de la plateforme et la majeure partie du plan de contrôle sont gérés par Azure, tandis que les clusters Kubernetes qui exécutent vos applications en conteneur sont gérés par le client." lightbox="media\overview\aks-hci-architecture.png":::

## <a name="what-you-need-to-get-started"></a>Ce dont vous avez besoin pour commencer

Les sections suivantes résument ce dont vous avez besoin pour faire fonctionner Azure Kubernetes Service sur Azure Stack HCI. Pour plus d’informations, consultez [Avant d’installer Azure Kubernetes Service sur Azure Stack HCI](system-requirements.md).

### <a name="on-your-windows-admin-center-system"></a>Sur votre système Windows Admin Center

Votre système de gestion Windows Admin Center présente les exigences suivantes :

- Windows 10 (nous ne prenons pas en charge les serveurs Windows Admin Center pour le moment)
- 60 Go d’espace libre
- Inscription auprès d’Azure
- Dans le même domaine que le cluster Azure Stack HCI

### <a name="on-the-azure-stack-hci-cluster-that-hosts-azure-kubernetes-service"></a>Sur le cluster Azure Stack HCI qui héberge Azure Kubernetes Service

Le cluster exécutant Azure Stack HCI, version 20H2 ou ultérieure présente les exigences suivantes :

- Quatre serveurs au maximum dans le cluster pour cette préversion
- 1 To de capacité disponible dans le pool de stockage pour le service Azure Kubernetes
- Au moins 30 Go de mémoire disponible pour l’exécution des machines virtuelles Azure Kubernetes Service
- Tous les serveurs du cluster doivent utiliser la région EN-US et la sélection de la langue pour cette préversion

Pour en savoir plus sur les conditions générales requises d’Azure Stack HCI, consultez [Avant de déployer Azure Stack HCI](../hci/deploy/before-you-start.md).

### <a name="the-network-configuration-for-azure-stack-hci"></a>Configuration réseau pour Azure Stack HCI

Le réseau connecté aux machines virtuelles du cluster Azure Stack HCI nécessite une étendue dédiée d’adresses DHCP IPv4 disponibles pour Azure Kubernetes Service et accessibles par les machines virtuelles du cluster Azure Stack HCI

Vous ne pouvez pas utiliser de balises VLAN sur votre réseau pour Azure Kubernetes Service sur Azure Stack HCI. Utilisez des ports d’accès (sans balise) sur vos commutateurs réseau pour le réseau utilisé par Azure Stack HCI et les machines virtuelles Azure Kubernetes Service.

## <a name="next-steps"></a>Étapes suivantes

Pour commencer à utiliser Azure Kubernetes Service sur Azure Stack HCI, consultez les articles suivants :

- [Examiner les conditions requises](system-requirements.md)
- [Configurer Azure Kubernetes Service sur Azure Stack HCI](create-kubernetes-cluster.md)
