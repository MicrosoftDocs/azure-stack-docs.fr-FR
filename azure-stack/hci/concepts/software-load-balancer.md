---
title: Équilibreur de charge logiciel (SLB) pour le SDN dans Azure Stack HCI
description: Cette rubrique présente l’équilibreur de charge logiciel pour le SDN (Software Defined Network) dans Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/8/2020
ms.openlocfilehash: 79588b25f76540aed1499123f7d39e4d57cf9ecd
ms.sourcegitcommit: 1621f2748b2059fd47ccacd48595a597c44ee63f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858973"
---
# <a name="what-is-software-load-balancer-slb-for-sdn"></a>Qu’est-ce que l’équilibreur de charge logiciel (\(SLB\)) pour le SDN ?

> S’applique à : Azure Stack HCI, version 20H2 ; Windows Server 2019

Les fournisseurs de services Cloud (CSP) et les entreprises qui déploient le [SDN dans Azure Stack HCI](software-defined-networking.md) peuvent utiliser un équilibreur de charge logiciel (SLB) pour distribuer uniformément le trafic réseau des locataires et des clients de locataires entre des ressources de réseau virtuel. L’équilibrage de charge logicielle (SLB) permet à plusieurs serveurs d’héberger une même charge de travail, ce qui assure une disponibilité et une scalabilité de haut niveau.

L’équilibreur de charge logiciel comprend les fonctionnalités suivantes :

- Services d’équilibrage de charge de couche 4 (L4) pour le trafic TCP/UDP nord/sud et est/ouest

- Équilibrage de la charge du trafic réseau public et du trafic réseau interne

- Prise en charge des adresses IP dynamiques sur des réseaux locaux virtuels (VLAN) et sur des réseaux virtuels que vous créez à l’aide de la virtualisation de réseau Hyper-V

- Prise en charge des sondes d’intégrité

- Compatibilité avec la mise à l’échelle dans le cloud, notamment les capacités de scale-out et de scale-up pour les multiplexeurs et les agents hôtes.

- Une périphérie unifiée multilocataire, grâce à son intégration fluide aux technologies SDN telles que la passerelle RAS, le pare-feu de centre de données et le réflecteur de route.

Pour plus d’informations, consultez [Fonctionnalités de l’équilibreur de charge logiciel](#bkmk_features) dans cette rubrique.

> [!NOTE]
> L’architecture multilocataire pour les VLAN n’est pas prise en charge par le contrôleur de réseau. Toutefois, vous pouvez utiliser des VLAN avec un SLB pour les charges de travail managées par le fournisseur de services, telles que l’infrastructure d’un centre de données et les serveurs web à haute densité.

À l’aide d’un équilibreur de charge logiciel, vous pouvez effectuer un scale-out de vos capacités d’équilibrage de charge en utilisant des machines virtuelles SLB sur les serveurs de calcul Hyper-V que vous utilisez pour vos autres charges de travail de machine virtuelle. Ainsi, l’équilibrage de charge logiciel prend en charge la création et la suppression rapides des points de terminaison d’équilibrage de charge qui sont requises pour les opérations des CSP. L’équilibrage de charge logiciel prend aussi en charge des dizaines de gigaoctets par cluster, fournit un modèle de provisionnement simple et peut aisément faire l’objet d’un scale-out et d’un scale-in.

## <a name="how-software-load-balancer-works"></a>Fonctionnement d’un équilibreur de charge logiciel

Un équilibreur de charge logiciel mappe des adresses IP virtuelles à des adresses IP dynamiques qui font partie d’un ensemble de ressources de service cloud dans le centre de données.

Les adresses IP virtuelles sont des adresses IP uniques qui fournissent un accès public à un pool de machines virtuelles à charge équilibrée. Par exemple, les adresses IP virtuelles sont des adresses IP qui sont exposées sur Internet afin que les locataires et les clients des locataires puissent se connecter aux ressources de locataire dans le centre de données cloud.

Les adresses IP dynamiques sont les adresses IP des machines virtuelles membres d’un pool à charge équilibrée situé derrière l’adresse IP virtuelle. Les adresses IP dynamiques sont affectées dans l’infrastructure cloud aux ressources de locataire.

Les adresses IP virtuelles sont situées dans le multiplexeur (MUX) SLB.  Le multiplexeur est constitué d’une ou de plusieurs machines virtuelles.  Le contrôleur de réseau fournit chaque adresse IP virtuelle à chaque multiplexeur, et chaque multiplexeur utilise à son tour BGP (Border Gateway Protocol) pour publier chaque adresse IP virtuelle sur les routeurs du réseau physique en tant que route /32.  Avec BGP, les routeurs de réseau physique peuvent :

- Découvrir qu’une adresse IP virtuelle est disponible sur chaque multiplexeur, même si les multiplexeurs se trouvent sur des sous-réseaux différents d’un réseau de couche 3

- Répartir la charge de chaque adresse IP virtuelle sur l’ensemble des multiplexeurs disponibles à l’aide du routage ECMP (Equal Cost Multi-Path)

- Détecter automatiquement la suppression ou la défaillance d’un multiplexeur et arrêter d’envoyer du trafic vers ce dernier

- Répartir la charge du multiplexeur défaillant ou supprimé entre les multiplexeurs sains

Quand du trafic public arrive en provenance d’Internet, le multiplexeur SLB examine le trafic, qui contient l’adresse IP virtuelle en tant que destination, puis mappe et réécrit le trafic pour qu’il arrive à une adresse IP dynamique spécifique. Pour le trafic réseau entrant, cette transaction est effectuée au cours d’un processus en deux étapes qui est réparti entre les machines virtuelles de multiplexeur et l’hôte Hyper-V dans lequel se trouve l’adresse IP dynamique de destination :

1. Équilibrage de charge : le multiplexeur utilise l’adresse IP virtuelle pour sélectionner une adresse IP dynamique, encapsule le paquet et transfère le trafic vers l’hôte Hyper-V où se trouve l’adresse IP dynamique.

2. Traduction d’adresses réseau (NAT) : l’hôte Hyper-V supprime l’encapsulation du paquet, convertit l’adresse IP virtuelle en adresse IP dynamique, remappe les ports et transfère le paquet à la machine virtuelle détenant l’adresse IP dynamique.

Le multiplexeur sait comment mapper les adresses IP virtuelles en adresses IP dynamiques correctes grâce aux stratégies d’équilibrage de charge que vous définissez à l’aide du contrôleur de réseau. Ces règles incluent le protocole, le port front-end, le port back-end et l’algorithme de distribution (5, 3 ou 2 tuples).

La NAT étant exécutée par l’hôte Hyper-V, quand les machines virtuelles de locataire répondent et envoient le trafic réseau sortant vers Internet ou des localisations de locataire distantes, le trafic contourne le multiplexeur et passe directement au routeur de périphérie à partir de l’hôte Hyper-V. Ce processus de contournement du multiplexeur est appelé « retour direct du serveur » (DSR).

Une fois établi le flux de trafic réseau initial, le trafic réseau entrant contourne complètement le multiplexeur SLB.

Dans l’illustration suivante, un ordinateur client exécute une requête DNS pour l’adresse IP d’un site SharePoint d’entreprise, en l’occurrence, une société fictive nommée Contoso. Le processus suivant se produit :

1. Le serveur DNS retourne l’adresse IP virtuelle 107.105.47.60 au client.

2. Le client envoie une requête HTTP à l’adresse IP virtuelle.

3. Le réseau physique dispose de plusieurs chemins pour atteindre l’adresse IP virtuelle située sur n’importe quel multiplexeur.  Chaque routeur franchi utilise ECMP pour choisir le segment suivant du chemin jusqu’à ce que la demande atteigne un multiplexeur.

4. Le multiplexeur qui reçoit la demande vérifie les stratégies configurées et constate qu’il y a deux adresses IP dynamiques disponibles, 10.10.10.5 et 10.10.20.5, sur un réseau virtuel pour communiquer la demande à l’adresse IP virtuelle107.105.47.60

5. Le multiplexeur sélectionne l’adresse IP dynamique 10.10.10.5 et encapsule les paquets à l’aide de la technologie VXLAN afin qu’il puisse les envoyer à l’hôte contenant l’adresse IP dynamique par le biais de l’adresse réseau physique de l’hôte.

6. L’hôte reçoit le paquet encapsulé et l’inspecte. Il supprime l’encapsulation et réécrit le paquet afin que la destination soit à présent l’adresse IP dynamique 10.10.10.5 à la place de l’adresse IP virtuelle, puis envoie le trafic à la machine virtuelle détenant l’adresse IP dynamique.

7. La demande atteint le site SharePoint de Contoso dans la batterie de serveurs 2. Le serveur génère une réponse et l’envoie au client, en utilisant sa propre adresse IP comme source.

8. L’hôte intercepte le paquet sortant dans le commutateur virtuel qui se souvient que le client, à présent la destination, a effectué la demande d’origine à destination de l’adresse IP virtuelle.  L’hôte réécrit la source du paquet en tant qu’adresse IP virtuelle afin que le client ne voie pas l’adresse IP dynamique.

9. L’hôte transfère le paquet directement à la passerelle par défaut du réseau physique ; celui-ci utilise sa table de routage standard pour transférer le paquet au client qui, enfin, reçoit la réponse.

:::image type="content" source="media/software-load-balancing/slb-process.jpg" alt-text="Processus de l’équilibrage de charge logiciel" border="false":::

### <a name="load-balancing-internal-datacenter-traffic"></a>Équilibrage de charge du trafic interne du centre de données

Lors de l’équilibrage de charge du trafic réseau interne au centre de données, par exemple entre des ressources de locataire qui s’exécutent sur des serveurs différents et qui sont membres du même réseau virtuel, le commutateur virtuel Hyper-V auquel les machines virtuelles sont connectées exécute la NAT.

Avec l’équilibrage de charge du trafic interne, la première demande est envoyée au multiplexeur et traitée par celui-ci, qui sélectionne l’adresse IP dynamique appropriée, vers laquelle il route le trafic. À partir de là, le flux de trafic établi contourne le multiplexeur et passe directement d’une machine virtuelle à l’autre.

### <a name="health-probes"></a>Sondes d’intégrité

L’équilibreur de charge logiciel incluent des sondes d’intégrité pour valider l’intégrité de l’infrastructure réseau, notamment les suivantes :

- Sonde TCP vers le port

- Sonde HTTP vers le port et l’URL

Contrairement à une appliance d’équilibrage de charge classique dans laquelle la sonde va de l’appliance à l’adresse IP dynamique, la sonde SLB part de l’hôte où se trouve l’adresse IP dynamique et va directement de l’agent hôte SLB à l’adresse IP dynamique, répartissant davantage le travail entre les hôtes.

## <a name="software-load-balancer-infrastructure"></a><a name="bkmk_infrastructure"></a>Infrastructure de l’équilibreur de charge logiciel
Avant de pouvoir configurer l’équilibreur de charge logiciel, vous devez déployer un contrôleur de réseau et une ou plusieurs machines virtuelles de multiplexeur SLB.

Vous devez également configurer les hôtes Azure Stack HCI avec le commutateur virtuel Hyper-V compatible SDN et vérifier que l’agent hôte SLB est en cours d’exécution. Les routeurs qui desservent les hôtes doivent prendre en charge le routage ECMP et le protocole BGP (Border Gateway Protocol) et doivent être configurés pour accepter les demandes de peering BGP provenant des multiplexeurs SLB.

La figure suivante fournit une vue d’ensemble de l’infrastructure SLB.

:::image type="content" source="media/software-load-balancing/slb-overview.png" alt-text="Processus de l’équilibrage de charge logiciel" border="false":::

Les sections suivantes fournissent des informations supplémentaires sur ces éléments de l’infrastructure de l’équilibreur de charge logiciel.

### <a name="network-controller"></a>Contrôleur réseau
Le contrôleur de réseau héberge le gestionnaire SLB et effectue les actions suivantes pour l’équilibreur de charge logiciel :

- Il traite les commandes SLB qui arrivent via l’API Northbound en provenance de Windows Admin Center, de System Center, de Windows PowerShell ou d’une autre application de gestion de réseau.

- Il calcule la stratégie de distribution aux hôtes Azure Stack HCI et aux multiplexeurs SLB.

- Il indique l’état d’intégrité de l’infrastructure de l’équilibreur de charge logiciel.

Vous pouvez utiliser Windows Admin Center ou Windows PowerShell pour installer et configurer le contrôleur de réseau ou une autre infrastructure SLB.

### <a name="slb-mux"></a>Multiplexeur SLB
Le multiplexeur SLB traite le trafic réseau entrant et mappe les adresses IP virtuelles aux adresses IP dynamiques, puis transfère le trafic à l’adresse IP dynamique appropriée. Chaque multiplexeur utilise également le protocole BGP pour publier les routes d’adresses IP virtuelles sur les routeurs de périphérie. La fonctionnalité Keep Alive de BGP notifie les multiplexeurs quand un multiplexeur échoue, ce qui permet aux multiplexeurs actifs de redistribuer la charge. Elle constitue l’essentiel de l’équilibrage de charge assuré par les équilibreurs de charge.

### <a name="slb-host-agent"></a>Agent hôte SLB
Quand vous déployez l’équilibreur de charge logiciel, vous devez utiliser Windows Admin Center, System Center, Windows PowerShell ou une autre application de gestion pour déployer l’agent hôte SLB sur chaque serveur hôte.

L’agent hôte SLB écoute les mises à jour de stratégie SLB à partir du contrôleur de réseau. L’agent hôte programme aussi les règles d’équilibrage de charge logiciel dans les commutateurs virtuels Hyper-V compatible SDN qui sont configurés sur l’ordinateur local.

### <a name="sdn-enabled-hyper-v-virtual-switch"></a>Commutateur virtuel Hyper-V compatible SDN
Pour qu’un commutateur virtuel soit compatible avec l’équilibreur de charge logiciel, l’extension VFP (Virtual Filtering Platform) doit être activée sur le commutateur virtuel. Cette opération est effectuée automatiquement par les scripts PowerShell de déploiement SDN, l’Assistant Déploiement de Windows Admin Center et le déploiement de System Center Virtual Machine Manager (SCVMM).

Pour plus d’informations sur l’activation de l’extension VFP sur des commutateurs virtuels, consultez les commandes Windows PowerShell [Get-VMSystemSwitchExtension](/powershell/module/hyper-v/get-vmsystemswitchextension?view=win10-ps) et [Enable-VMSwitchExtension](/powershell/module/hyper-v/enable-vmswitchextension?f=255&MSPPError=-2147217396&view=win10-ps).

Le commutateur virtuel Hyper-V compatible SDN effectue les actions suivantes pour l’équilibreur de charge logiciel :

- Il traite le chemin de données pour l’équilibreur de charge logiciel.

- Il reçoit le trafic réseau entrant provenant du multiplexeur.

- Il contourne le multiplexeur pour le trafic réseau sortant, qu’il envoie au routeur en utilisant le processus DSR.

### <a name="bgp-router"></a>Routeur BGP
Le routeur BGP effectue les actions suivantes pour l’équilibreur de charge logiciel :

- Il route le trafic entrant vers le multiplexeur en utilisant la technologie ECMP.

- Pour le trafic réseau sortant, il utilise la route fournie par l’hôte.

- Il écoute les mises à jour des routes pour les adresses IP virtuelles à partir du multiplexeur SLB.

- Il supprime les multiplexeurs SLB de la rotation SLB si Keep Alive échoue.

## <a name="software-load-balancer-features"></a><a name="bkmk_features"></a>Fonctionnalités de l’équilibreur de charge logiciel
Les sections suivantes décrivent certaines des fonctionnalités de l’équilibreur de charge logiciel.

### <a name="core-functionality"></a>Fonctionnalité principale

- L’équilibreur de charge logiciel fournit des services d’équilibrage de charge de couche 4 (L4) pour le trafic TCP/UDP nord/sud et est/ouest.

- Vous pouvez utiliser l’équilibreur de charge logiciel sur un réseau basé sur la virtualisation de réseau Hyper-V.

- Vous pouvez utiliser l’équilibreur de charge logiciel avec un réseau VLAN pour les machines virtuelles à adresse IP dynamique connectées à un commutateur virtuel Hyper-V compatible SDN.

- Une instance SLB peut gérer plusieurs locataires.

- L’équilibreur de charge logiciel et les adresses IP dynamiques prennent en charge un chemin de retour scalable et à faible latence, tel qu’implémenté par le processus DSR.

- Vous pouvez également recourir à l’équilibreur de charge logiciel quand vous utilisez une solution SET (Switch Embedded Teaming) ou SR-IOV (Single Root Input/Output Virtualization).

- L’équilibreur de charge logiciel prend en charge les protocoles Internet versions 6 (IPv6) et 4 (IPv4).

- Pour les scénarios de passerelle de site à site, l’équilibreur de charge logiciel fournit des fonctionnalités NAT afin de permettre à toutes les connexions de site à site d’utiliser une seule adresse IP publique.

### <a name="scale-and-performance"></a>Scalabilité et performances

- Compatibilité avec la mise à l’échelle dans le cloud, notamment les capacités de scale-out et de scale-up pour les multiplexeurs et les agents hôtes.

- Un module actif du contrôleur de réseau hébergeant le gestionnaire SLB peut prendre en charge huit instances de multiplexeur.

### <a name="high-availability"></a>Haute disponibilité

- Vous pouvez déployer l’équilibreur de charge logiciel sur plus de deux nœuds dans une configuration actif/actif.

- Vous pouvez ajouter et supprimer des multiplexeurs dans le pool de multiplexeurs sans affecter le service SLB. La disponibilité de l’équilibreur de charge logiciel est ainsi assurée quand un correctif est appliqué à des multiplexeurs individuels.

- Les instances de multiplexeur individuelles ont une durée de bon fonctionnement de 99 %.

- Les données de supervision de l’intégrité sont disponibles pour les entités de gestion.

## <a name="next-steps"></a>Étapes suivantes

Pour consulter des informations connexes, reportez-vous également à :

- [Vue d’ensemble du contrôleur de réseau](network-controller-overview.md)
- [Déployer le contrôleur de réseau à l’aide de Windows PowerShell](../deploy/network-controller-powershell.md)
- [SDN dans Azure Stack HCI](software-defined-networking.md)
