---
title: Documentation Azure Kubernetes Service sur Azure Stack HCI
description: Avant de commencer Azure Kubernetes Service sur Azure Stack HCI
ms.topic: conceptual
author: abhilashaagarwala
ms.author: abha
ms.date: 12/02/2020
ms.openlocfilehash: 71c842cf44963988da7926003646b246bf80f802
ms.sourcegitcommit: 8776cbe4edca5b63537eb10bcd83be4b984c374a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/13/2021
ms.locfileid: "98175734"
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

## <a name="network-requirements"></a>Conditions requises en matière de réseau 

Les impératifs suivants s’appliquent à un cluster Azure Stack HCI ainsi qu’à un cluster Windows Server 2019 Datacenter : 

 - Vérifiez que vous disposez d’un commutateur virtuel externe existant configuré si vous utilisez Windows Admin Center. Pour les clusters Azure Stack HCI, ce commutateur et son nom doivent être identiques sur tous les nœuds de cluster. 

 - Vérifiez que vous avez désactivé IPv6 sur toutes les cartes réseau. 

 - Pour un déploiement réussi, les nœuds de cluster Azure Stack HCI et les machines virtuelles du cluster Kubernetes doivent disposer d’une connectivité Internet externe.
  
 - Assurez-vous qu’il existe une connectivité réseau entre les hôtes Azure Stack HCI et les machines virtuelles du locataire.

 - La résolution de noms DNS est requise pour que tous les nœuds puissent communiquer entre eux. Pour la résolution de noms externes Kubernetes, utilisez les serveurs DNS fournis par le serveur DHCP au moment de l’obtention de l’adresse IP. Pour la résolution de noms internes Kubernetes, utilisez la solution DNS principale de Kubernetes par défaut. 

 - Dans cette préversion, nous ne fournissons la prise en charge que d’un seul VLAN pour l’ensemble du déploiement. 

 - Dans cette préversion, nous avons une prise en charge limitée des serveurs proxy pour les clusters Kubernetes créés via PowerShell. 
 
### <a name="ip-address-assignment"></a>Affectation d’adresses IP  
 
Dans le cadre d’un déploiement réussi d’AKS sur Azure Stack HCI, nous vous recommandons de configurer une plage de pool d’adresses IP virtuelles avec votre serveur DHCP. Il est également recommandé de configurer trois à cinq nœuds de plan de contrôle à haut niveau de disponibilité pour tous vos clusters de charge de travail. 

> [!NOTE]
> L’utilisation seule d’attributions d’adresses IP statiques n’est pas prise en charge. Vous devez configurer un serveur DHCP dans le cadre de cette préversion.

#### <a name="dhcp"></a>DHCP
Procédez comme suit lors de l’utilisation de DHCP pour attribuer des adresses IP à l’ensemble du cluster :  

 - Le réseau doit disposer d’un serveur DHCP disponible pour fournir des adresses TCP/IP aux machines virtuelles et aux ordinateurs hôtes de machines virtuelles. Le serveur DHCP doit également contenir des informations sur l’hôte NTP et DNS.
 
 - Nous vous recommandons également de disposer d’un serveur DHCP avec une étendue dédiée d’adresses IPv4 accessibles par le cluster Azure Stack HCI.
 
 - Les adresses IPv4 fournies par le serveur DHCP doivent être routables. De plus, le délai d’expiration du bail de ces adresses doit être de 30 jours pour éviter toute perte de connectivité IP en cas de mise à jour ou de reprovisionnement d’une machine virtuelle.  

Au minimum, vous devez réserver le nombre d’adresses DHCP suivantes :

| Type de cluster  | Nœud Plan de contrôle | Nœud Worker | Update | Équilibrage de charge  |
| ------------- | ------------------ | ---------- | ----------| -------------|
| Hôte AKS |  1  |  0  |  2  |  0  |
| Cluster de charge de travail  |  1 par nœud  | 1 par nœud |  5  |  1  |

Vous pouvez voir la façon dont le nombre d’adresses IP requises varie en fonction du nombre de clusters de charge de travail, du plan de contrôle et des nœuds Worker dans votre environnement. Nous vous recommandons de réserver des adresses IP 256 (sous-réseau/24) dans votre pool d’adresses IP DHCP.
  
    
#### <a name="vip-pool-range"></a>Plage du pool d’adresses IP virtuelles

Les pools d’adresses IP virtuelles (VIP) sont fortement recommandés pour un déploiement réussi d’AKS sur Azure Stack HCI. Les pools d’adresses IP virtuelles sont une plage d’adresses IP statiques réservées qui sont utilisées pour les déploiements à long terme afin de garantir que vos charges de travail de déploiement et d’application sont toujours accessibles. Actuellement, nous ne prenons en charge que les adresses IPv4. Vous devez donc vérifier que vous avez désactivé IPv6 sur toutes les cartes réseau. En outre, assurez-vous que vos adresses IP virtuelles ne font pas partie de la réserve d’adresses IP DHCP.

Au minimum, vous devez réserver une adresse IP par cluster (charge de travail et hôte AKS) et une adresse IP par service Kubernetes. Le nombre d’adresses IP requises dans la plage du pool d’adresses IP virtuelles varie selon le nombre de clusters de charge de travail et de services Kubernetes dont vous disposez dans votre environnement. Nous vous recommandons de réserver 16 adresses IP statiques pour votre déploiement AKS-HCI. 

Lors de la configuration de l’hôte AKS, utilisez les paramètres `-vipPoolStartIp` et `-vipPoolEndIp` dans `Set-AksHciConfig` pour créer un pool d’adresses IP virtuelles.

#### <a name="mac-pool-range"></a>Plage du pool d’adresses MAC
Nous vous recommandons d’avoir un minimum de 16 adresses MAC dans la plage pour autoriser plusieurs nœuds de plan de contrôle dans chaque cluster. Lors de la configuration de l’hôte AKS, utilisez les paramètres `-macPoolStart` et `-macPoolEnd` dans `Set-AksHciConfig` pour réserver des adresses MAC à partir du pool d’adresses Mac DHCP pour les services Kubernetes.
  
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

 - Machine Windows 10 ou Windows Server (nous ne prenons pas en charge l’exécution de Windows Admin Center sur le cluster Azure Stack HCI ou Windows Server 2019 Datacenter pour le moment)
 - 60 Go d’espace libre
 - Inscription auprès d’Azure
 - Se trouver dans le même domaine que le cluster Azure Stack HCI ou Windows Server 2019 Datacenter

## <a name="next-steps"></a>Étapes suivantes 

Une fois que vous avez rempli toutes les conditions préalables ci-dessus, vous pouvez configurer un hôte Azure Kubernetes Service sur Azure Stack HCI à l’aide de :
 - [Windows Admin Center](setup.md)
 - [PowerShell](setup-powershell.md)