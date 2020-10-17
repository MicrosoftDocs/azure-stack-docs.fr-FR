---
title: Vue d’ensemble de la passerelle RAS dans Azure Stack HCI
description: Cette rubrique présente la passerelle RAS pour le SDN (Software Defined Network) dans Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/8/2020
ms.openlocfilehash: a7f05f7e07960e83681a13c92f4653b0993668de
ms.sourcegitcommit: 1621f2748b2059fd47ccacd48595a597c44ee63f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858976"
---
# <a name="what-is-ras-gateway-for-software-defined-networking"></a>Qu’est-ce que la passerelle RAS pour le SDN (Software Defined Network) ?

> S’applique à Azure Stack HCI, version 20H2 ; Windows Server 2019

La passerelle RAS (Remote Access Service) est un routeur logiciel prenant en charge le protocole BGP (Border Gateway Protocol) conçu pour les fournisseurs de services Cloud (CSP) et les entreprises qui hébergent des réseaux virtuels multilocataires à l’aide de la virtualisation de réseau Hyper-V (HNV). Vous pouvez utiliser la passerelle RAS pour router le trafic réseau entre un réseau virtuel et un autre réseau, qu’il soit local ou distant.

La passerelle RAS nécessite le [contrôleur de réseau](network-controller-overview.md), qui effectue le déploiement de pools de passerelles, configure les connexions de locataire sur chaque passerelle et bascule les flux du trafic réseau vers une passerelle de secours en cas de défaillance de la passerelle.

  > [!NOTE]
  > Une architecture multilocataire désigne la possibilité pour une infrastructure cloud de prendre en charge les charges de travail des machines virtuelles de plusieurs locataires. Les charges, bien qu’isolées les unes des autres, sont exécutées dans la même infrastructure. Les multiples charges de travail d’un locataire seul peuvent être interconnectées et être gérées à distance mais ces systèmes n’offrent aucune interconnexion avec les charges de travail d’autres locataires et ces derniers ne peuvent pas eux-mêmes les gérer à distance.

## <a name="ras-gateway-features"></a>Fonctionnalités de la passerelle RAS

La passerelle RAS offre un certain nombre de fonctionnalités pour le réseau privé virtuel (VPN), le tunneling, le transfert et le routage dynamique.

### <a name="site-to-site-ipsec-vpn"></a>VPN IPSec de site à site

Cette fonctionnalité de la passerelle RAS vous permet de connecter deux réseaux à des localisations physiques différentes sur Internet à l’aide d’une connexion de réseau privé virtuel (VPN) de site à site (S2S). Il s’agit d’une connexion chiffrée utilisant le protocole VPN IKEv2.

Pour les fournisseurs de solutions cloud qui hébergent de nombreux locataires dans leur centre de données, la passerelle RAS fournit une solution de passerelle multilocataire qui permet aux locataires d’accéder à leurs ressources et de les gérer via des connexions VPN de site à site à partir de sites distants. La passerelle RAS permet le flux du trafic réseau entre les ressources virtuelles de votre centre de données et leur réseau physique.

### <a name="site-to-site-gre-tunnels"></a>Tunnels GRE de site à site

Les tunnels GRE (Generic Routing Encapsulation) permettent la connectivité entre les réseaux virtuels locataires et les réseaux externes. Étant donné que le protocole GRE est léger et que l’encapsulation GRE est prise en charge sur la plupart des appareils réseau, cette technologie constitue un choix idéal pour le tunneling là où le chiffrement des données n’est pas nécessaire.

La prise en charge de l’encapsulation GRE dans les tunnels S2S résout le problème de transfert entre les réseaux virtuels locataires et les réseaux externes locataires à l’aide d’une passerelle multilocataire.

### <a name="layer-3-forwarding"></a>Transfert de couche 3

Le transfert de couche 3 (L3) permet la connectivité entre l’infrastructure physique du centre de données et l’infrastructure virtualisée du cloud de virtualisation de réseau Hyper-V. À l’aide d’une connexion de transfert L3, les machines virtuelles du réseau locataire peuvent se connecter à un réseau physique via la passerelle SDN, qui est déjà configurée dans un environnement SDN (Software Defined Networking). Dans ce cas, la passerelle SDN sert de routeur entre le réseau virtualisé et le réseau physique.

### <a name="dynamic-routing-with-bgp"></a>Routage dynamique avec BGP

Le protocole BGP réduit le besoin de configuration manuelle de routage sur les routeurs, car il s’agit d’un protocole de routage dynamique qui, de plus, découvre automatiquement les itinéraires entre les sites qui sont connectés via des connexions VPN de site à site. Si votre organisation a plusieurs sites qui sont connectés à l’aide de routeurs BGP, tels que la passerelle RAS, le protocole BGP permet aux routeurs de calculer et d’utiliser automatiquement des routes valides entre eux en cas d’interruption ou de défaillance du réseau.

Le réflecteur de route BGP inclus dans la passerelle RAS fournit une alternative à la topologie à maillage complet BGP qui est nécessaire pour la synchronisation des routes entre les routeurs. Pour plus d’informations, consultez [Qu’est-ce que le réflecteur de route ?](route-reflector-overview.md).

## <a name="how-ras-gateway-works"></a>Fonctionnement de la passerelle RAS

La passerelle RAS route le trafic réseau entre le réseau physique et les ressources du réseau machines virtuelles, quelle que soit la localisation. Vous pouvez router le trafic réseau à la même localisation physique ou à plusieurs localisations différentes.

Vous pouvez déployer la passerelle RAS dans des pools à haute disponibilité qui utilisent plusieurs fonctionnalités à la fois. Les pools de passerelles contiennent plusieurs instances de la passerelle RAS pour offrir la haute disponibilité et le basculement.

Vous pouvez facilement effectuer un scale-up ou un scale-down d’un pool de passerelles en ajoutant ou en supprimant des machines virtuelles de passerelle dans le pool. La suppression ou l’ajout de passerelles n’interrompt pas les services fournis par un pool. Vous pouvez également ajouter et supprimer des pools de passerelles complets. Pour plus d’informations, consultez [Haute disponibilité de la passerelle RAS](/windows-server/networking/sdn/technologies/network-function-virtualization/ras-gateway-high-availability).

Chaque pool de passerelles fournit une redondance M+N. Cela signifie que « M » machines virtuelles de passerelle actives sont secondées par « N » machines virtuelles de passerelle de secours. La redondance M+N offre une plus grande flexibilité pour déterminer le niveau de fiabilité dont vous avez besoin quand vous déployez la passerelle RAS.

Vous pouvez affecter une adresse IP publique unique à tous les pools ou à une partie des pools. Cela réduit considérablement le nombre d’adresses IP publiques que vous devez utiliser, car vous pouvez ainsi obliger tous les locataires à se connecter au cloud sur une seule adresse IP.

## <a name="next-steps"></a>Étapes suivantes

Pour consulter des informations connexes, reportez-vous également à :

- [Architecture de déploiement de la passerelle RAS](/windows-server/networking/sdn/technologies/network-function-virtualization/ras-gateway-deployment-architecture)
- [Vue d’ensemble du contrôleur de réseau](network-controller-overview.md)
- [Planifier le déploiement du contrôleur de réseau](network-controller.md)
- [SDN dans Azure Stack HCI](software-defined-networking.md)