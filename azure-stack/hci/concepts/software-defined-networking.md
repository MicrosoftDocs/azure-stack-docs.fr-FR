---
title: Mise en réseau SDN (Software Defined Networking) dans Azure Stack HCI
description: La mise en réseau SDN (Software Defined Networking) permet de configurer et de gérer de manière centralisée des réseaux et des services réseau tels que le basculement, le routage et l’équilibrage de charge dans votre centre de données.
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/29/2020
ms.openlocfilehash: e14d0475c941f568f08a027de9e71eb25f00a999
ms.sourcegitcommit: 69cfff119ab425d0fbb71e38d1480d051fc91216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91572651"
---
# <a name="software-defined-networking-sdn-in-azure-stack-hci"></a>Mise en réseau SDN (Software Defined Networking) dans Azure Stack HCI

> S’applique à Azure Stack HCI, version 20H2 ; Windows Server 2019

La mise en réseau SDN (Software Defined Networking) permet de configurer et de gérer de manière centralisée des réseaux et des services réseau tels que le basculement, le routage et l’équilibrage de charge dans votre centre de données. Vous pouvez utiliser SDN pour créer, sécuriser et connecter dynamiquement votre réseau afin de répondre aux besoins en constante évolution de vos applications. Le fonctionnement des réseaux de centres de données à l’échelle mondiale pour des services comme Microsoft Azure, qui effectue efficacement des dizaines de milliers de modifications de réseau chaque jour, est possible uniquement en raison de SDN.

Les éléments de réseau virtuel, tels que le [Commutateur virtuel Hyper-V](/windows-server/virtualization/hyper-v-virtual-switch/hyper-v-virtual-switch), la [Virtualisation de réseau Hyper-V](/windows-server/networking/sdn/technologies/hyper-v-network-virtualization/hyper-v-network-virtualization), [l’équilibrage de charge logiciel](/windows-server/networking/sdn/technologies/network-function-virtualization/software-load-balancing-for-sdn) et la [Passerelle RAS](/windows-server/networking/sdn/technologies/network-function-virtualization/ras-gateway-for-sdn) sont conçus pour être des éléments intégraux de votre infrastructure SDN. Vous pouvez également utiliser vos appareils existants compatibles SDN pour bénéficier d’une intégration plus poussée entre vos charges de travail exécutées dans des réseaux virtuels et le réseau physique.

Il existe trois principaux composants SDN sur Azure Stack HCI et vous pouvez choisir celui que vous souhaitez déployer : Contrôleur de réseau, équilibreur de charge logiciel et passerelle.

## <a name="network-controller"></a>Contrôleur réseau

Le [Contrôleur de réseau](/windows-server/networking/sdn/technologies/Software-Defined-Networking-Technologies#network-controller) fournit un point d’automatisation programmable et centralisé pour gérer, configurer, analyser et dépanner l’infrastructure de réseau virtuel dans votre centre de données. Il s’agit d’un rôle serveur hautement évolutif qui utilise Service Fabric pour fournir une haute disponibilité. Le contrôleur de réseau doit être déployé sur ses propres machines virtuelles dédiées.

Le déploiement du contrôleur de réseau active les fonctionnalités suivantes :

- créer deux réseaux virtuels en étoile et des sous-réseaux connecter des machines virtuelles aux sous-réseaux virtuels
- configurer et gérer la micro-segmentation pour les machines virtuelles connectées à des réseaux virtuels ou à des réseaux VLAN traditionnels
- joindre des appliances virtuelles à vos réseaux virtuels
- configurer des stratégies de qualité de service (QoS) pour les machines virtuelles jointes à des réseaux virtuels ou à des réseaux VLAN traditionnels.

Nous vous recommandons de [déployer le contrôleur de réseau pendant la création d’un cluster](../deploy/create-cluster.md#step-5-sdn-optional). Sinon, vous pouvez [déployer le contrôleur de réseau à l’aide de PowerShell](../deploy/network-controller-powershell.md) après la création d’un cluster Azure Stack HCI.

## <a name="software-load-balancing"></a>Équilibrage de charge logicielle

L’[équilibrage de charge logiciel](/windows-server/networking/sdn/technologies/network-function-virtualization/software-load-balancing-for-sdn) peut être utilisé pour distribuer uniformément le trafic réseau des clients entre plusieurs machines virtuelles. Il permet à plusieurs serveurs d’héberger la même charge de travail, offrant ainsi une haute disponibilité et une évolutivité supérieure. SLB utilise le [Border Gateway Protocol](/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp) pour publier des adresses IP virtuelles sur le réseau physique.

## <a name="gateway"></a>Passerelle

Les passerelles sont utilisées pour le routage du trafic réseau entre un réseau virtuel et un autre réseau, qu’il soit local ou distant. Les passerelles peuvent être utilisées pour :

- créer des connexions IPsec de site à site sécurisées entre des réseaux virtuels SDN et des réseaux clients externes sur Internet.
- créer des connexions de l’encapsulation de routage générique (GRE) entre les réseaux virtuels SDN et les réseaux externes. La différence entre les connexions de site à site et les connexions GRE est que ces dernières ne sont pas des connexions chiffrées. Pour plus d’informations sur les scénarios de connectivité GRE, consultez [Tunneling GRE dans Windows Server](/windows-server/remote/remote-access/ras-gateway/gre-tunneling-windows-server).
- Créez des connexions de couche 3 entre les réseaux virtuels SDN et les réseaux externes. Dans ce cas, la passerelle SDN agit simplement comme un routeur entre votre réseau virtuel et le réseau externe.

Les passerelles utilisent [Border Gateway Protocol](/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp) pour publier des points de terminaison GRE et établir des connexions point à point. Le déploiement SDN crée un pool de passerelles par défaut qui prend en charge tous les types de connexion. Dans ce pool, vous pouvez spécifier le nombre de passerelles réservées en mode veille en cas d’échec d’une passerelle active.

## <a name="next-steps"></a>Étapes suivantes

Pour consulter des informations connexes, reportez-vous également à :

- [Planifier une infrastructure de réseau défini par logiciel](plan-software-defined-networking-infrastructure.md)
- [Vue d’ensemble du SDN dans Windows Server](/windows-server/networking/sdn/software-defined-networking)
- [Déployer une infrastructure SDN (Software Defined Networking) avec des scripts](/windows-server/networking/sdn/deploy/deploy-a-software-defined-network-infrastructure-using-scripts)
