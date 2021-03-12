---
title: Documentation Azure Kubernetes Service sur Azure Stack HCI
description: Avant de commencer Azure Kubernetes Service sur Azure Stack HCI
ms.topic: conceptual
author: abhilashaagarwala
ms.author: abha
ms.date: 02/02/2021
ms.openlocfilehash: 16d4e7b1de239ee1b08aa696696796fa6f12dff7
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "101839740"
---
# <a name="system-requirements-for-azure-kubernetes-service-on-azure-stack-hci"></a>Configuration requise pour Azure Kubernetes Service sur Azure Stack HCI

> S’applique à : Azure Stack HCI, Windows Server 2019 Datacenter

Cet article décrit les conditions nécessaires à la configuration d’Azure Kubernetes Service sur Azure Stack HCI ou sur Windows Server 2019 Datacenter, et à son utilisation pour créer des clusters Kubernetes. Pour obtenir une vue d’ensemble de Azure Kubernetes Service sur Azure Stack HCI, consultez [vue d’ensemble AKS sur Azure Stack HCI](overview.md).

## <a name="determine-hardware-requirements"></a>Déterminer la configuration matérielle requise

Microsoft recommande d’acheter une solution matérielle/logicielle Azure Stack HCI validée, proposée par nos partenaires. Ces solutions sont conçues, assemblées et validées dans le respect de notre architecture de référence pour garantir compatibilité et fiabilité, ce qui vous permet d’être opérationnel rapidement. Vérifiez que les systèmes, les composants, les appareils et les pilotes que vous utilisez sont certifiés Windows Server 2019 selon le catalogue Windows Server. Visitez le site web [Solutions Azure Stack HCI](https://azure.microsoft.com/overview/azure-stack/hci) pour connaître les solutions validées.

## <a name="general-requirements"></a>Conditions générales

Pour qu’Azure Kubernetes Service sur Azure Stack HCI ou Windows Server 2019 Datacenter fonctionne de manière optimale dans un environnement Active Directory, vous devez vérifier que les conditions suivantes sont remplies : 

 - Vérifiez que la synchronisation de l’heure est configurée et que la divergence n’est pas supérieure à 2 minutes sur tous les nœuds de cluster et le contrôleur de domaine. Pour plus d’informations sur la configuration de la synchronisation de l’heure, consultez [Service de temps Windows](/windows-server/networking/windows-time-service/windows-time-service-top). 

 - Vérifiez que le ou les comptes d’utilisateur qui ajoutent, mettent à jour et gèrent Azure Kubernetes Service sur Azure Stack HCI ou Windows Server 2019 Datacenter disposent des autorisations appropriées dans Active Directory. Si vous utilisez des unités d’organisation (UO) pour gérer les stratégies de groupe pour les serveurs et les services, les autorisations liste, lecture, modification et suppression de tous les objets de l’unité d’organisation sont requises pour le ou les comptes d’utilisateur. 

 - Nous vous recommandons d’utiliser une UO distincte pour les serveurs et les services auxquels vous ajoutez vos clusters Azure Kubernetes Service sur Azure Stack HCI ou Windows Server 2019 Datacenter. Cela vous permettra de contrôler l’accès et les autorisations avec plus de granularité.

 - Si vous utilisez des modèles GPO sur des conteneurs dans Active Directory, assurez-vous que le déploiement de AKS-HCI est exempté de la stratégie. La sécurisation renforcée des serveurs sera disponible dans une version préliminaire ultérieure.

## <a name="compute-requirements"></a>Exigences de calcul

 - Un cluster Azure Stack HCI ou un cluster de basculement Windows Server 2019 Datacenter avec quatre serveurs au maximum dans le cluster. Nous recommandons que chaque serveur du cluster ait au moins 24 cœurs de processeur et au moins 256 Go de RAM.

 - Bien que vous puissiez techniquement exécuter Azure Kubernetes Service sur un seul nœud Windows Server 2019 Datacenter, nous vous déconseillons de le faire. Toutefois, vous pouvez exécuter Azure Kubernetes Service sur un seul nœud Windows Server 2019 Datacenter à des fins d’évaluation.

 - Les autres exigences de calcul pour le service Azure Kubernetes Service sur Azure Stack HCI sont conformes aux exigences de Azure Stack HCI. Pour plus d’informations sur les exigences relatives au serveur Azure Stack HCI, consultez [Configuration requise pour Azure Stack HCI](../hci/concepts/system-requirements.md#server-requirements).

 - Pour cette version préliminaire, vous devez installer le système d’exploitation Azure Stack HCI sur chaque serveur dans le cluster à l’aide des sélections de la région en-US et de la langue. Leur modification après l’installation n’est pas suffisante pour l’instant.

## <a name="general-network-requirements"></a>Configuration réseau requise générale 

Les impératifs suivants s’appliquent à un cluster Azure Stack HCI ainsi qu’à un cluster Windows Server 2019 Datacenter : 

 - Vérifiez que vous disposez d’un commutateur virtuel externe existant configuré si vous utilisez Windows Admin Center. Pour les clusters Azure Stack HCI, ce commutateur et son nom doivent être identiques sur tous les nœuds de cluster. 

 - Vérifiez que vous avez désactivé IPv6 sur toutes les cartes réseau. 

 - Pour un déploiement réussi, les nœuds de cluster Azure Stack HCI et les machines virtuelles du cluster Kubernetes doivent disposer d’une connectivité Internet externe.
 
 - Vérifiez que tous les sous-réseaux que vous définissez pour le cluster sont routables entre eux et vers Internet.
  
 - Assurez-vous qu’il existe une connectivité réseau entre les hôtes Azure Stack HCI et les machines virtuelles du locataire.

 - La résolution de noms DNS est requise pour que tous les nœuds puissent communiquer entre eux. 

## <a name="ip-address-assignment"></a>Affectation d’adresses IP  

Dans AKS sur Azure Stack HCI, les réseaux virtuels sont utilisés pour allouer des adresses IP aux ressources Kubernetes qui en ont besoin, comme indiqué ci-dessus. Vous avez le choix entre deux modèles réseau, en fonction de l’architecture réseau AKS sur Azure Stack HCI que vous souhaitez. 

> [!NOTE]
 > L’architecture réseau virtuel définie ici pour vos déploiements AKS sur Azure Stack HCI est différente de l’architecture réseau physique sous-jacente dans votre centre de centres.

- Réseau IP statique : le réseau virtuel alloue des adresses IP statiques au serveur d’API de cluster Kubernetes, aux nœuds Kubernetes, aux machines virtuelles sous-jacentes, aux équilibreurs de charge et aux services Kubernetes que vous exécutez sur votre cluster.

- Réseau DHCP : le réseau virtuel alloue des adresses IP dynamiques aux nœuds Kubernetes, aux machines virtuelles sous-jacentes et aux équilibreurs de charge à l’aide d’un serveur DHCP. Des adresses IP statiques sont toujours allouées au serveur d’API de cluster Kubernetes et à tous les services Kubernetes que vous exécutez sur votre cluster.

### <a name="minimum-ip-address-reservation"></a>Réservation d’adresse IP minimale

Au minimum, vous devez réserver le nombre suivant d’adresses IP pour votre déploiement :

| Type de cluster  | Nœud Plan de contrôle | Nœud Worker | Pour les opérations de mise à jour | Équilibrage de charge  |
| ------------- | ------------------ | ---------- | ----------| -------------|
| Hôte AKS |  1 IP |  N/D  |  2 IP |  N/D  |
| Cluster de charge de travail  |  1 IP par nœud  | 1 IP par nœud |  5 IP  |  1 IP |

En outre, vous devez réserver le nombre suivant d’adresses IP pour votre pool d’adresses IP virtuelles :

| Type de ressource  | Nombre d’adresses IP 
| ------------- | ------------------
| Serveur d’API de cluster |  1 par cluster 
| Services Kubernetes  |  1 par service  

Comme vous pouvez le voir, le nombre d’adresses IP requises est variable en fonction de l’architecture AKS sur Azure Stack HCI et du nombre de services que vous exécutez sur votre cluster Kubernetes. Nous vous recommandons de réserver un total de 256 adresses IP (sous-réseau /24) pour votre déploiement.

Pour plus d’informations sur la configuration réseau requise, consultez [Concepts relatifs aux réseaux dans AKS sur Azure Stack HCI](./concepts-networking.md).

### <a name="network-port-and-url-requirements"></a>Configuration requise des ports réseau et URL 

Au moment de la création d’un cluster Azure Kubernetes sur Azure Stack HCI, les ports de pare-feu suivants sont automatiquement ouverts sur chaque serveur du cluster. 


| Port de pare-feu               | Description     | 
| ---------------------------- | ------------ | 
| 45000           | Port de serveur wssdagent GPRC     |
| 45001             | Port d’authentification wssdagent GPRC  | 
| 55000           | Port de serveur wssdcloudagent GPRC      |
| 65 000            | Port d’authentification wssdcloudagent GPRC  | 


Des exceptions d’URL de pare-feu sont nécessaires pour l’ordinateur de Windows Admin Center et tous les nœuds dans le cluster Azure Stack HCI. 

| URL        | Port | Service | Notes |
| ---------- | ---- | --- | ---- |
https://helm.sh/blog/get-helm-sh/  | 443 | Télécharger l’agent, WAC | Utilisé pour télécharger les fichiers binaires Helm 
https://storage.googleapis.com/  | 443 | Cloud Init | Télécharger des fichiers binaires Kubernetes 
https://azurecliprod.blob.core.windows.net/ | 443 | Cloud Init | Téléchargement des fichiers binaires et des conteneurs 
https://aka.ms/installazurecliwindows | 443 | WAC | Téléchargement d’Azure CLI 
https://:443 | 443 | TCP | Permet de prendre en charge les agents Azure Arc  
*.blob.core.windows.net | 443 | TCP | Requis pour les téléchargements
*.api.cdp.microsoft.com, *.dl.delivery.mp.microsoft.com, *.emdl.ws.microsoft.com | 80, 443 | Télécharger l’agent | Téléchargement des métadonnées 
*.dl.delivery.mp.microsoft.com, *.do.dsp.mp.microsoft.com. | 80, 443 | Télécharger l’agent | Téléchargement d’images VHD 
ecpacr.azurecr.io | 443 | Kubernetes | Téléchargement des images de conteneur 
git://:9418 | 9418 | TCP | Permet de prendre en charge les agents Azure Arc 

## <a name="storage-requirements"></a>Exigences de stockage 

Les implémentations de stockage suivantes sont prises en charge par Azure Kubernetes Service sur Azure Stack HCI : 

|  Nom                         | Type de stockage | Capacité requise |
| ---------------------------- | ------------ | ----------------- |
| Clusters Azure Stack HCI          | CSV          | 1 To              |
| Cluster de basculement Windows Server 2019 Datacenter          | CSV          | 1 To              |
| Serveur Windows Server 2019 Datacenter à un seul nœud | Stockage attaché directement | 500 Go|

## <a name="review-maximum-supported-hardware-specifications"></a>Consulter les spécifications matérielles maximales prises en charge 

Les déploiements Azure Kubernetes Service sur Azure Stack HCI qui dépassent les spécifications suivantes ne sont pas pris en charge : 

| Ressource                     | Maximale |
| ---------------------------- | --------|
| Serveurs physiques par cluster | 4       |
| Clusters Kubernetes            | 4       |
| Nombre total d'ordinateurs virtuels          | 200     |

## <a name="windows-admin-center-requirements"></a>Impératifs liés à Windows Admin Center

Windows Admin Center est l’interface utilisateur permettant de créer et de gérer Azure Kubernetes Service sur Azure Stack HCI. Pour utiliser Windows Admin Center avec Azure Kubernetes Service sur Azure Stack HCI, vous devez respecter tous les critères de la liste ci-dessous. 

La machine qui exécute la passerelle Windows Admin Center doit respecter les conditions suivantes : 

 - Ordinateur Windows 10 ou Windows Server
 - [Inscription auprès d’Azure](/windows-server/manage/windows-admin-center/azure/azure-integration)
 - Se trouver dans le même domaine que le cluster Azure Stack HCI ou Windows Server 2019 Datacenter

## <a name="next-steps"></a>Étapes suivantes 

Une fois que vous avez rempli toutes les conditions préalables ci-dessus, vous pouvez configurer un hôte Azure Kubernetes Service sur Azure Stack HCI à l’aide de :
 - [Windows Admin Center](setup.md)
 - [PowerShell](setup-powershell.md)
