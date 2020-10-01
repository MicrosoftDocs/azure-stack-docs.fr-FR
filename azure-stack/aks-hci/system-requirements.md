---
title: Documentation Azure Kubernetes Service sur Azure Stack HCI
description: Avant de commencer Azure Kubernetes Service sur Azure Stack HCI
ms.topic: conceptual
author: abhilashaagarwala
ms.author: abha
ms.date: 09/22/2020
ms.openlocfilehash: 4c91dad12e4d6680ca0887da2e6ca314cdbd64ee
ms.sourcegitcommit: 3e225b30a54159b6b8dbeb2f843a2e5a721b746e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91519384"
---
# <a name="system-requirements-for-azure-kubernetes-service-on-azure-stack-hci"></a>Configuration requise pour Azure Kubernetes Service sur Azure Stack HCI

> S’applique à : Azure Stack HCI

Cet article décrit la configuration requise pour la configuration d’Azure Kubernetes Service sur Azure Stack HCI et son utilisation pour créer des clusters Kubernetes. Pour obtenir une vue d’ensemble de Azure Kubernetes Service sur Azure Stack HCI, consultez [vue d’ensemble AKS sur Azure Stack HCI](overview.md).

## <a name="determine-hardware-requirements"></a>Déterminer la configuration matérielle requise

Microsoft recommande d’acheter une solution matérielle/logicielle Azure Stack HCI validée, proposée par nos partenaires. Ces solutions sont conçues, assemblées et validées dans le respect de notre architecture de référence pour garantir compatibilité et fiabilité, ce qui vous permet d’être opérationnel rapidement. Vérifiez que les systèmes, les composants, les appareils et les pilotes que vous utilisez sont certifiés Windows Server 2019 selon le catalogue Windows Server. Visitez le site web [Solutions Azure Stack HCI](https://azure.microsoft.com/overview/azure-stack/hci) pour connaître les solutions validées.

### <a name="general-requirements"></a>Conditions générales

Pour que le service Azure Kubernetes Service sur Azure Stack HCI fonctionne de façon optimale dans un environnement Active Directory, assurez-vous que les conditions suivantes sont remplies : 

 - Vérifiez que la synchronisation de l’heure est configurée et que la divergence n’est pas supérieure à 2 minutes sur tous les nœuds de cluster et le contrôleur de domaine. Pour plus d’informations sur la configuration de la synchronisation de l’heure, consultez le [Service de temps Windows](/windows-server/networking/windows-time-service/windows-time-service-top). 

 - Assurez-vous que le ou les comptes d’utilisateur qui ajoutent, mettent à jour et gèrent Azure Kubernetes Service sur des clusters Azure Stack HCI disposent des autorisations appropriées dans Active Directory. Si vous utilisez des unités d’organisation (UO) pour gérer les stratégies de groupe pour les serveurs et les services, les autorisations liste, lecture, modification et suppression de tous les objets de l’unité d’organisation sont requises pour le ou les comptes d’utilisateur. 

 - Nous vous recommandons d’utiliser une UO distincte pour les serveurs et les services auxquels vous ajoutez Azure Kubernetes Service sur des clusters Azure Stack HCI. Cela vous permettra de contrôler l’accès et les autorisations avec plus de granularité.

 - Si vous utilisez des modèles GPO sur des conteneurs dans Active Directory, assurez-vous que le déploiement de AKS-HCI est exempté de cette stratégie. La sécurisation renforcée des serveurs sera disponible dans une version préliminaire ultérieure.

### <a name="compute-requirements"></a>Exigences de calcul

 - Un cluster Azure Stack HCI avec un maximum de quatre serveurs dans le cluster. Nous recommandons que chaque serveur du cluster ait au moins 24 cœurs d’UC et au moins 512 Go de RAM.

 - Bien que vous puissiez techniquement exécuter le service Azure Kubernetes Service sur un seul nœud Azure Stack HCI Server, nous vous déconseillons de le faire.

 - Les autres exigences de calcul pour le service Azure Kubernetes Service sur Azure Stack HCI sont conformes aux exigences de Azure Stack HCI. Pour plus d’informations sur les exigences pour le serveur Azure Stack HCI, visitez [Exigences Azure Stack HCI](../hci/deploy/before-you-start.md) .  

 - Pour cette version préliminaire, vous devez installer le système d’exploitation Azure Stack HCI sur chaque serveur dans le cluster à l’aide des sélections de la région en-US et de la langue. Leur modification après l’installation n’est pas suffisante pour l’instant.

### <a name="network-requirements"></a>Conditions requises en matière de réseau 

Azure Kubernetes Service sur Azure Stack HCI requiert une connexion réseau fiable à bande passante élevée et à faible latence entre chaque nœud serveur. Vous devriez vérifier les éléments suivants : 

 - Vérifiez que vous disposez d’un commutateur virtuel externe existant configuré si vous utilisez Windows Admin Center. Pour les clusters Azure Stack HCI, ce commutateur doit être le même sur tous les nœuds du cluster. 

 - Vérifiez que vous avez désactivé IPv6 sur toutes les cartes réseau. 

 - Le réseau doit disposer d’un serveur DHCP disponible pour fournir des adresses TCP/IP aux machines virtuelles et aux ordinateurs hôtes de machines virtuelles. Le serveur DHCP doit également contenir des informations sur l’hôte NTP et DNS. 

 - Nous vous recommandons également de disposer d’un serveur DHCP avec une étendue dédiée d’adresses IPv4 accessibles par le cluster Azure Stack HCI. Par exemple, vous pouvez réserver 10.0.1.1 pour la passerelle par défaut, 10.0.1.2 à 10.0.1.102 pour les services Kubernetes et utiliser 10.0.1.103-10.0.1.254 pour les machines virtuelles du cluster Kubernetes. 

 - Les adresses IPv4 fournies par le serveur DHCP doivent être routables et avoir un délai d’expiration de bail de 7 jours pour éviter la perte de connectivité IP en cas de mise à jour ou de reconfiguration des machines virtuelles.  

 - Nous vous déconseillons d’avoir des balises VLAN. Utilisez des ports d’accès ou non balisés sur vos commutateurs réseau de clusters Azure Stack HCI. 

 - Nous vous déconseillons d’utiliser un pool d’adresses IP virtuelles statiques dédié pour le pool d’adresses IP virtuelles de l’équilibreur de charge lors de l’installation. Le pool d’adresses IP DHCP est utilisé pour les machines virtuelles, alors que le pool d’adresses IP virtuelles est utilisé pour l’équilibrage de charge et doit être routable. Le pool d’adresses IP DHCP n’a pas besoin d’être routable vers l’Internet externe.

 - La résolution de noms DNS est requise pour que tous les nœuds puissent communiquer entre eux. Pour la résolution de noms externes Kubernetes, nous utilisons les serveurs DNS fournis par le serveur DHCP lors de l’obtention de l’adresse IP. Pour la résolution de noms interne Kubernetes, nous utilisons la solution basée sur DNS cœur Kubernetes par défaut. 

 - Pour cette version préliminaire, nous ne prenons pas en charge l’utilisation d’un serveur proxy pour connecter le Windows Admin Center, les nœuds de cluster Azure Stack HCI et le service Azure Kubernetes Service sur des nœuds de cluster Azure Stack HCI à Internet.

### <a name="network-port-and-url-requirements"></a>Configuration requise des ports réseau et URL 

Lorsque vous créez un cluster Azure Kubernetes sur Azure Stack HCI, nous ouvrons automatiquement les ports de pare-feu suivants sur chaque serveur du cluster. 


| Port de pare-feu               | Description         | 
| ---------------------------- | ------------ | 
| 45000           | Port de serveur wssdagent GPRC           |
| 45001             | Port d’authentification wssdagent GPRC  | 
| 55000           | Port de serveur wssdcloudagent GPRC           |
| 55001             | Port d’authentification wssdcloudagent GPRC  | 


Des exceptions d’URL de pare-feu sont nécessaires pour l’ordinateur de Windows Admin Center et tous les nœuds dans le cluster Azure Stack HCI. 

| URL        | Port | Service | Notes |
| ---------- | ---- | --- | ---- |
https://helm.sh/blog/get-helm-sh/  | 443 | Télécharger l’agent, WAC | Utilisé pour télécharger les fichiers binaires Helm 
https://storage.googleapis.com/  | 443 | Cloud Init | Télécharger des fichiers binaires Kubernetes 
https://azurecliprod.blob.core.windows.net/ | 443 | Cloud Init | Téléchargement des fichiers binaires et des conteneurs 
https://aka.ms/installazurecliwindows | 443 | WAC | Téléchargement d’Azure CLI 
*.api.cdp.microsoft.com, *.dl.delivery.mp.microsoft.com, *.emdl.ws.microsoft.com | 80, 443 | Télécharger l’agent | Téléchargement des métadonnées 
*.dl.delivery.mp.microsoft.com, *.do.dsp.mp.microsoft.com. | 80, 443 | Télécharger l’agent | Téléchargement d’images VHD 
ecpacr.azurecr.io | 443 | Kubernetes | Téléchargement des images de conteneur 

### <a name="storage-requirements"></a>Exigences de stockage 

Les implémentations de stockage suivantes sont prises en charge par Azure Kubernetes Service sur Azure Stack HCI : 

|  Nom                         | Type de stockage | Capacité requise |
| ---------------------------- | ------------ | ----------------- |
| Clusters Azure Stack HCI          | CSV          | 1 To              |
| Noeud simple Azure Stack HCI | Stockage attaché directement | 500 Go|

### <a name="review-maximum-supported-hardware-specifications"></a>Consulter les spécifications matérielles maximales prises en charge 

Les déploiements Azure Kubernetes Service sur Azure Stack HCI qui dépassent les spécifications suivantes ne sont pas pris en charge : 

| Ressource                     | Maximale |
| ---------------------------- | --------|
| Serveurs physiques par cluster | 4       |
| Clusters Kubernetes            | 4       |
| Nombre total d'ordinateurs virtuels          | 200     |

### <a name="windows-admin-center"></a>Windows Admin Center 

Windows Admin Center est l’interface utilisateur permettant de créer et de gérer Azure Kubernetes Service sur Azure Stack HCI. Pour utiliser Windows Admin Center avec Azure Kubernetes Service sur Azure Stack HCI, vous devez respecter tous les critères de la liste ci-dessous. 

#### <a name="on-your-windows-admin-center-system"></a>Sur votre système Windows Admin Center

L’ordinateur qui exécute la passerelle Windows Admin Center doit : 

 - Windows 10 (nous ne prenons pas en charge les serveurs Windows Admin Center pour le moment)
 - 60 Go d’espace libre
 - Inscrite auprès d’Azure
 - Dans le même domaine que le cluster Azure Stack HCI

## <a name="next-steps"></a>Étapes suivantes 

Une fois que vous avez rempli toutes les conditions préalables ci-dessus, vous pouvez configurer un hôte Azure Kubernetes Service sur Azure Stack HCI à l’aide de :
 - [Windows Admin Center](setup.md)
 - [PowerShell](setup-powershell.md)
