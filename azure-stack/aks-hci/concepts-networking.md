---
title: Concepts - Réseaux dans Azure Kubernetes Service (AKS) sur Azure Stack HCI
description: En savoir plus sur les réseaux dans Azure Kubernetes Service (AKS) sur Azure Stack HCI, notamment sur les réseaux DHCP et IP statique, ainsi que sur les équilibreurs de charge.
author: abha
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: abha
ms.custom: fasttrack-edit
ms.openlocfilehash: a8e96783be3228aae525027855c1498b0269bd57
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "101874381"
---
# <a name="network-concepts-for-deploying-azure-kubernetes-service-aks-on-azure-stack-hci"></a>Concepts réseau relatifs au déploiement d’Azure Kubernetes Service (AKS) sur Azure Stack HCI

Dans une approche du développement d’applications axée sur les microservices basés sur un conteneur, les composants d’application doivent collaborer pour traiter leurs tâches. Kubernetes fournit diverses ressources qui permettent cette communication entre les applications. Vous pouvez vous connecter aux applications et les exposer en interne ou en externe. Pour créer des applications hautement disponibles, vous pouvez équilibrer leurs charges. 

Cet article présente les concepts fondamentaux relatifs à la fourniture d’un réseau pour vos applications dans AKS sur Azure Stack HCI. Il montre ensuite les différents modèles de déploiement et fournit des exemples permettant de choisir l’infrastructure réseau la mieux adaptée à votre déploiement AKS local.

## <a name="kubernetes-on-azure-stack-hci-basics"></a>Informations de base concernant Kubernetes sur Azure Stack HCI

Pour autoriser l’accès à vos applications ou pour que les composants d’application communiquent entre eux, Kubernetes fournit une couche d’abstraction à la mise en réseau virtuelle. Les nœuds Kubernetes sont connectés à un réseau virtuel et peuvent fournir la connectivité entrante et sortante pour les pods. Le composant *kube-proxy* s’exécute sur chaque nœud afin de fournir ces fonctionnalités de réseau.

Dans Kubernetes, les *services* regroupent logiquement les pods pour permettre un accès direct via une adresse IP ou un nom DNS, et sur un port spécifique. Vous pouvez également distribuer le trafic à l’aide d’un *équilibreur de charge*. 

La plateforme Azure Stack HCI permet également de simplifier les réseaux virtuels pour les clusters AKS sur Azure Stack HCI. Lorsque vous créez un cluster AKS sur Azure Stack HCI, une ressource d’équilibreur de charge sous-jacente `HAProxy` est créée puis configurée. Lorsque vous créez des applications sur vos clusters Kubernetes, les adresses IP sont configurées pour vos services Kubernetes.

## <a name="aks-on-azure-stack-hci-resources"></a>Ressources AKS sur Azure Stack HCI 

Pour simplifier la configuration réseau des charges de travail d’application, AKS sur Azure Stack HCI alloue des adresses IP aux objets suivants lors d’un déploiement : 

- **Serveur d’API de cluster Kubernetes** : le serveur d’API est un composant du plan de contrôle Kubernetes qui expose l’API Kubernetes. Le serveur d’API est le front-end du plan de contrôle Kubernetes. Les serveurs d’API se voient toujours allouer des adresses IP statiques, indépendamment du modèle de réseau sous-jacent.

- **Nœuds Kubernetes (sur les machines virtuelles)**  : un cluster Kubernetes se compose d’un ensemble d’ordinateurs de travail, appelés « nœuds », qui exécutent des applications conteneurisées. Chaque cluster comprend au moins un nœud Worker. Pour AKS sur Azure Stack HCI, les nœuds Kubernetes s’exécutent dans des machines virtuelles. Ces machines virtuelles sont créées sur votre cluster Azure Stack HCI. 

- **Services Kubernetes** : dans Kubernetes, les *services* regroupent logiquement les pods pour permettre un accès direct via une adresse IP ou un nom DNS, et sur un port spécifique. Vous pouvez également distribuer le trafic à l’aide d’un *équilibreur de charge*. Les services Kubernetes se voient toujours allouer des adresses IP statiques, indépendamment du modèle de réseau sous-jacent.

- **Équilibreurs de charge HAProxy** : HAProxy (proxy à haute disponibilité) est un équilibreur de charge TCP/HTTP ainsi qu’un serveur proxy qui permet à un serveur web de répartir les requêtes entrantes entre plusieurs points de terminaison. Chaque cluster de charge de travail dans AKS-HCI comprend un équilibreur de charge HAProxy qui s’exécute au sein de sa propre machine virtuelle spécialisée.

- **Service Microsoft On-Premises Cloud** : il s’agit du fournisseur de cloud Azure Stack HCI qui permet d’exécuter Kubernetes sur un cluster Azure Stack HCI local. Le modèle réseau que suit votre cluster Azure Stack HCI détermine la méthode d’allocation d’adresses IP qui est utilisée pour cet objet.


## <a name="virtual-networks"></a>Réseaux virtuels

Dans AKS sur Azure Stack HCI, les réseaux virtuels sont utilisés pour allouer des adresses IP aux ressources Kubernetes qui en ont besoin, comme indiqué ci-dessus. Vous avez le choix entre deux modèles réseau, en fonction de l’architecture réseau AKS sur Azure Stack HCI que vous souhaitez. Notez que l’architecture de réseau virtuel qui est définie ici pour vos déploiements AKS sur Azure Stack HCI est différente de l’architecture réseau physique sous-jacente de votre centre de données.

- Réseau IP statique : le réseau virtuel alloue des adresses IP statiques au serveur d’API de cluster Kubernetes, aux nœuds Kubernetes, aux machines virtuelles sous-jacentes, aux équilibreurs de charge et aux services Kubernetes que vous exécutez sur votre cluster.

- Réseau DHCP : le réseau virtuel alloue des adresses IP dynamiques aux nœuds Kubernetes, aux machines virtuelles sous-jacentes et aux équilibreurs de charge à l’aide d’un serveur DHCP. Des adresses IP statiques sont toujours allouées au serveur d’API de cluster Kubernetes et à tous les services Kubernetes que vous exécutez sur votre cluster.

### <a name="virtual-ip-pool"></a>Pool d’adresses IP virtuelles
Les pools d’adresses IP virtuelles (VIP) sont indispensables à la réussite d’un déploiement AKS sur Azure Stack HCI. Un pool VIP est une plage d’adresses IP statiques réservées qui sont utilisées pour l’allocation d’adresses IP au serveur d’API de cluster Kubernetes, et pour l’allocation d’adresses IP externes aux services Kubernetes, dans le but de garantir l’accessibilité de vos applications à tout moment. Quel que soit votre modèle de réseau virtuel, vous devez provisionner un pool VIP pour le déploiement de votre hôte AKS. Le nombre d’adresses IP comprises dans le pool VIP dépend du nombre de clusters de charge de travail et de services Kubernetes qui sont exécutés dans votre déploiement, à la fois pour les modèles réseau statiques et DHCP. Cependant, le pool VIP diffère selon le modèle réseau :

- IP statique : si vous utilisez des IP statiques, vérifiez qu’elles proviennent bien du même sous-réseau. 
- DHCP : si votre réseau est configuré avec DHCP, vous devrez demander à votre administrateur réseau de réserver une plage d’IP pour le pool VIP.

### <a name="kubernetes-node-ip-pool"></a>Pool d’IP des nœuds Kubernetes
Comme nous l’avons vu plus haut, les nœuds Kubernetes s’exécutent sur des machines virtuelles spécialisées dans le cadre d’un déploiement AKS sur Azure Stack HCI. AKS sur Azure Stack HCI alloue des adresses IP à ces machines virtuelles afin de permettre aux nœuds Kubernetes de communiquer entre eux. Pour un modèle réseau DHCP, vous n’avez pas besoin de spécifier un pool VM de nœud Kubernetes, car les adresses IP des nœuds Kubernetes sont allouées dynamiquement par le serveur DHCP sur votre réseau. Pour un modèle de réseau basé sur les IP statiques, vous devez spécifier une plage de pool d’IP pour les nœuds Kubernetes. Le nombre d’adresses IP de cette plage dépend du nombre total de nœuds Kubernetes présents sur l’hôte AKS et sur les clusters Kubernetes de charge de travail.

### <a name="virtual-network-with-static-ip-networking"></a>Réseau virtuel IP statique

Ce modèle de réseau crée un réseau virtuel qui alloue des adresses IP statiques à tous les objets de votre déploiement. L’autre avantage que présente l’utilisation d’un réseau IP statique est que les déploiements et les charges de travail d’application de longue durée restent toujours accessibles. Nous vous recommandons de créer un modèle de réseau virtuel IP statique pour votre déploiement AKS sur Azure Stack HCI.

Vous devez spécifier les paramètres suivants lorsque vous définissez un réseau virtuel avec des configurations IP statique :

- Nom : nom de votre réseau virtuel.
- Préfixe d’adresse : préfixe d’adresse IP à utiliser pour votre sous-réseau.
- Passerelle : adresse IP de la passerelle par défaut du sous-réseau.
- Serveur DNS : tableau d’adresses IP pointant vers les serveurs DNS à utiliser pour le sous-réseau. Vous devez spécifier entre 1 et 3 serveurs.
- Pool d’adresses IP de nœud Kubernetes : plage continue d’adresses IP à utiliser pour vos machines virtuelles de nœud Kubernetes.
- Pool d’adresses IP virtuelles : plage continue d’adresses IP à utiliser pour le serveur d’API de cluster Kubernetes et pour les services Kubernetes qui exigent une adresse IP externe.
- ID vLAN : ID vLAN du réseau virtuel. Si vous l’omettez, le réseau virtuel ne sera pas étiqueté.

### <a name="virtual-network-with-dhcp-networking"></a>Réseau virtuel DHCP

Ce modèle réseau crée un réseau virtuel qui alloue des adresses IP dynamiques à tous les objets de votre déploiement.  

Vous devez spécifier les paramètres suivants lorsque vous définissez un réseau virtuel avec des configurations IP statique :

- Nom : nom de votre réseau virtuel.
- Pool d’adresses IP virtuelles : plage continue d’adresses IP à utiliser pour le serveur d’API de cluster Kubernetes et pour les services Kubernetes qui exigent une adresse IP externe.
- ID vLAN : ID vLAN du réseau virtuel. Si vous l’omettez, le réseau virtuel ne sera pas étiqueté.

## <a name="microsoft-on-premises-cloud-service"></a>Service Microsoft On-Premises Cloud

Microsoft On-Premises Cloud (MOC) est une nouvelle pile de gestion qui permet de gérer des machines virtuelles sur des clouds SDDC basés sur Azure Stack HCI et Windows Server. Le service MOC est constitué des éléments suivants :

- Une instance unique d’un service `cloud agent` hautement disponible qui est déployée sur un cluster. Cet agent s’exécute sur n’importe quel nœud du cluster Azure Stack HCI. 
- Un `node agent` qui s’exécute sur chaque nœud physique Azure Stack HCI. 

`-cloudserviceCIDR` est un paramètre de la commande `Set-AksHciConfig` qui est utilisé pour attribuer une IP virtuelle dans le but de garantir que nous disposons d’un service d’agent cloud à haut niveau de disponibilité.

L’allocation d’une adresse IP pour le service MOC dépend du modèle réseau sous-jacent qui est suivi par votre déploiement de clusters Azure Stack HCI. **L’allocation d’adresses IP pour le service MOC ne dépend pas de votre modèle de réseau virtuel. Elle dépend uniquement du réseau physique sous-jacent qui fournit des adresses IP aux nœuds de cluster Azure Stack HCI dans votre centre de données.**

### <a name="azure-stack-hci-cluster-nodes-with-a-dhcp-based-ip-address-allocation-model"></a>Nœuds de cluster Azure Stack HCI avec un modèle d’allocation d’adresses IP DHCP

Si des nœuds Azure Stack HCI reçoivent une adresse IP d’un serveur DHCP présent sur le réseau physique, vous n’avez pas besoin de fournir explicitement une adresse IP au service MOC.

### <a name="azure-stack-hci-cluster-nodes-with-a-static-ip-allocation-model"></a>Nœuds de cluster Azure Stack HCI avec un modèle d’allocation d’adresses IP statiques 

Si vos nœuds de cluster Azure Stack HCI se voient attribuer des adresses IP statiques, vous devez fournir explicitement une adresse IP pour le service cloud MOC. L’adresse IP du service MOC doit se trouver dans le même sous-réseau que les nœuds de cluster Azure Stack HCI. Pour attribuer explicitement une adresse IP au service MOC, utilisez le paramètre `-cloudserviceCIDR` dans la commande `Set-AksHciConfig`. Veillez à entrer une adresse IP au format CIDR, par exemple « 10.11.23.45/16 ».

## <a name="compare-network-models"></a>Comparer des modèles de réseaux

Les modèles DHCP et IP statique fournissent une connectivité réseau pour votre déploiement AKS sur Azure Stack HCI. Chacun présente toutefois des avantages et des inconvénients. À un niveau élevé, les considérations suivantes s’appliquent :

* **DHCP**
    - Ne garantit pas des adresses IP à longue durée de vie pour certains types de ressources dans un déploiement AKS sur Azure Stack HCI.
    - Prend en charge l’extension des adresses IP DHCP réservées si votre déploiement est plus volumineux que prévu.
    
* **Adresse IP statique**
    - Garantit des adresses IP à longue durée de vie pour toutes les ressources d’un déploiement AKS sur Azure Stack HCI.
    - Étant donné que l’extension automatique du pool IP de nœuds Kubernetes n’est pas prise en charge, vous ne pourrez peut-être pas créer de nouveaux clusters si vous avez épuisé le pool d’adresses IP de nœud Kubernetes.


Le tableau suivant compare l’allocation d’adresses IP aux ressources qui est utilisée par le modèle de réseau DHCP à celle qui est utilisée par le modèle de réseau IP statique :

| Fonctionnalité                                                                                   | Adresse IP statique   | DHCP |
|----------------------------------------------------------------------------------------------|-----------|-----------|
| Serveur d’API de cluster Kubernetes                                             | Attribuée statiquement à l’aide du pool VIP | Attribuée statiquement à l’aide du pool VIP |
| Nœuds Kubernetes (sur les machines virtuelles)                                                                         | Attribuée à l’aide du pool d’IP du nœud Kubernetes | Attribuée dynamiquement |
| Services Kubernetes                                          | Attribuée statiquement à l’aide du pool VIP | Attribuée statiquement à l’aide du pool VIP |
| VM de l’équilibreur de charge HAProxy                                          | Attribuée à l’aide du pool d’IP du nœud Kubernetes | Attribuée dynamiquement |
| Service Microsoft On-Premises Cloud                                               | Dépend de la configuration du réseau physique des nœuds de cluster Azure Stack HCI | Dépend de la configuration du réseau physique des nœuds de cluster Azure Stack HCI |
| Pool VIP                                             | Obligatoire | Obligatoire |
| Pool d’IP des nœuds Kubernetes | Obligatoire | Non pris en charge |


## <a name="minimum-ip-address-reservations-for-an-aks-on-azure-stack-hci-deployment"></a>Réservations d’adresses IP minimales pour un déploiement AKS sur Azure Stack HCI
Quel que soit votre modèle de déploiement, le nombre d’adresses IP réservées reste le même. Cette section indique le nombre d’adresses IP que vous devez réserver en fonction de votre modèle de déploiement AKS sur Azure Stack HCI.

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

Comme vous pouvez le voir, le nombre d’adresses IP nécessaires varie selon l’architecture AKS sur Azure Stack HCI et selon le nombre de services que vous exécutez sur votre cluster Kubernetes. Nous vous recommandons de réserver un total de 256 adresses IP (sous-réseau /24) pour votre déploiement.

### <a name="walking-through-an-example-deployment"></a>Exemple de déploiement

Jane est administratrice informatique et elle débute avec AKS sur Azure Stack HCI. Elle souhaite déployer deux clusters Kubernetes (clusters Kubernetes A et B) sur son cluster Azure Stack HCI. Elle souhaite également exécuter une application de vote sur son cluster. Cette application comprend trois instances de l’interface utilisateur front-end, qui s’exécutent sur les deux clusters et sur une instance de la base de données back-end.

- Le cluster Kubernetes A comprend trois nœuds de plan de contrôle et cinq nœuds Worker.
- Le cluster Kubernetes B comprend un nœud de plan de contrôle et trois nœuds Worker.
- 3 instances de l’interface utilisateur front-end (port 443)
- 1 instance de la base de données back-end (port 80)

D’après le tableau ci-dessus, elle devra réserver :

- 3 adresses IP pour son hôte AKS (1 IP pour le nœud de plan de contrôle et 2 IP pour l’exécution des mises à jour)
- 3 adresses IP pour les nœuds de plan de contrôle du cluster A (1 IP par nœud de plan de contrôle)
- 5 adresses IP pour les nœuds Worker du cluster A (1 IP par nœud de plan de contrôle)
- 6 adresses IP de plus pour le cluster A (5 IP pour l’exécution des mises à jour et 1 IP pour l’équilibreur de charge)
- 1 adresse IP pour les nœuds de plan de contrôle du cluster B (1 IP par nœud de plan de contrôle)
- 3 adresses IP pour les nœuds Worker du cluster B (1 IP par nœud de plan de contrôle)
- 6 adresses IP de plus pour le cluster B (5 IP pour l’exécution des mises à jour et 1 IP pour l’équilibreur de charge)
- 2 adresses IP pour les serveurs d’API des clusters Kubernetes (1 IP par cluster Kubernetes)
- 3 adresses IP pour le service Kubernetes (1 IP par instance de l’interface utilisateur front-end, puisqu’elles utilisent toutes le même port. La base de données back-end peut utiliser l’une des 3 adresses IP, car elle se trouve sur un autre port).

Comme nous l’avons vu ci-dessus, Jane a besoin d’un total de 32 adresses IP. Jane doit donc réserver un sous-réseau /26 pour son réseau virtuel. 

### <a name="splitting-reserved-ip-addresses-based-on-a-static-ip-network-model"></a>Répartition des adresses IP réservées en fonction d’un modèle de réseau IP statique

Même si le nombre total d’adresses IP réservées reste le même, le modèle de déploiement détermine la répartition de ces adresses IP entre les groupes IP. Comme nous l’avons vu précédemment, le modèle de réseau IP statique comprend deux pools d’adresses IP :

- Pool d’IP de nœud Kubernetes : pour les machines virtuelles de nœud Kubernetes et la machine virtuelle de l’équilibreur de charge. Ce pool d’IP comprend également l’adresse IP qui est nécessaire à l’exécution des mises à jour.
- Pool d’adresses IP virtuelles : pour le serveur d’API et les services Kubernetes

À l’aide de l’exemple ci-dessus, Jane doit encore répartir ces adresses IP entre les pools VIP et les pools d’adresses IP de nœud Kubernetes :
- 5 (2 pour le serveur d’API des clusters Kubernetes et 3 pour les services Kubernetes) sur les 32 adresses IP de son pool VIP.
- 27 (toutes les adresses IP des nœuds Kubernetes et des machines virtuelles sous-jacentes, des machines virtuelles d’équilibreur de charge et des opérations de mise à jour) pour son pool d’adresses IP de nœud Kubernetes.

### <a name="splitting-reserved-ip-addresses-based-on-a-dhcp-network-model"></a>Répartition des adresses IP réservées en fonction d’un modèle de réseau DHCP

Même si le nombre total d’adresses IP réservées reste le même, le modèle de déploiement détermine la répartition de ces adresses IP entre les groupes IP. Comme nous l’avons vu précédemment, le modèle de réseau DHCP comprend 1 pool d’adresses IP :

- Pool d’adresses IP virtuelles : pour le serveur d’API et les services Kubernetes

À l’aide de l’exemple ci-dessus :
- Jane doit réserver un total de 32 adresses IP ou un sous-réseau /26 sur son serveur DHCP. 
- Elle doit réserver 5 adresses IP (2 pour le serveur d’API des clusters Kubernetes et 3 pour les services Kubernetes) sur les 32 adresses IP de son pool VIP. 

## <a name="next-steps"></a>Étapes suivantes

Dans cette rubrique, vous avez vu les concepts fondamentaux relatifs à la fourniture d’un réseau pour vos applications dans AKS sur Azure Stack HCI. À présent, vous pouvez :

- [Configurer les paramètres de proxy pour AKS sur Azure Stack HCI](./proxy.md)
- [Configuration système requise](./system-requirements.md)

