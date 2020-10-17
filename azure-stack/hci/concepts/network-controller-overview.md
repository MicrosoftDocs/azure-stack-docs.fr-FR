---
title: Vue d’ensemble du contrôleur de réseau dans Azure Stack HCI
description: Cette rubrique présente le contrôleur de réseau pour le SDN (Software Defined Network) dans Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/8/2020
ms.openlocfilehash: c207b436571d85d0902cd69a2168144e176919b1
ms.sourcegitcommit: 6a0f7f452998c404a80ca9d788dbf3cdf4d78b38
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91893898"
---
# <a name="what-is-network-controller"></a>Qu’est-ce que le contrôleur de réseau ?

> S’applique à Azure Stack HCI, version 20H2 ; Windows Server 2019

Le contrôleur de réseau est la pierre angulaire de la gestion SDN (Software Defined Networking). Il s’agit d’un rôle serveur hautement scalable qui fournit un point d’automatisation programmable et centralisé pour gérer, configurer, superviser et dépanner l’infrastructure de réseau virtuel.

À l’aide du contrôleur de réseau, vous pouvez automatiser la configuration et la gestion de l’infrastructure réseau au lieu d’effectuer une configuration manuelle des appareils et services réseau.

## <a name="how-network-controller-works"></a>Fonctionnement du contrôleur de réseau

Le contrôleur de réseau fournit une interface de programmation d’applications (API) qui lui permet de communiquer avec les appareils, services et composants réseau (API Southbound) et de les gérer ainsi qu’une deuxième API qui permet aux applications de gestion d’indiquer au contrôleur de réseau les paramètres réseau et les services dont elles ont besoin (API Northbound).

Avec l’API Southbound, le contrôleur de réseau peut gérer les appareils réseau et les services réseau et rassembler toutes les informations dont vous avez besoin sur le réseau. Le contrôleur de réseau supervise en permanence l’état des appareils et services réseau et s’assure que toute dérive de la configuration par rapport à l’état souhaité est corrigée.

L'API Northbound du contrôleur de réseau est implémentée comme une interface REST. Elle offre la possibilité de gérer le réseau de votre centre de données à partir d’applications de gestion. Pour la gestion, les utilisateurs peuvent recourir à l’API REST, directement ou par le biais de Windows PowerShell, ou des applications de gestion dotées d’une interface graphique utilisateur, comme Windows Admin Center ou System Center Virtual Machine Manager.

## <a name="network-controller-features"></a>Fonctionnalités du contrôleur de réseau

Le contrôleur de réseau vous permet de gérer les fonctionnalités SDN telles que les réseaux virtuels, les pare-feu, l’équilibreur de charge logiciel et la passerelle RAS. Voici quelques-unes de ses nombreuses fonctionnalités.

### <a name="virtual-network-management"></a>Gestion de réseau virtuel

Cette fonctionnalité du contrôleur de réseau vous permet de déployer et de configurer la virtualisation de réseau Hyper-V, de configurer des cartes réseau virtuelles sur des machines virtuelles individuelles et de stocker et distribuer des stratégies de réseau virtuel. Avec cette fonctionnalité, vous pouvez créer des réseaux virtuels et des sous-réseaux, attacher des machines virtuelles à ces réseaux et activer la communication entre les machines virtuelles du même réseau virtuel.

Le contrôleur de réseau prend en charge les réseaux VLAN, l’encapsulation NVGRE (Network Virtualization Generic Routing Encapsulation) et la technologie VXLAN (Virtual Extensible Local Area Network).

## <a name="firewall-management"></a>Gestion de pare-feu

Cette fonctionnalité du contrôleur de réseau vous permet de configurer et de gérer les règles de pare-feu d’autorisation et de refus du contrôle d’accès pour vos machines virtuelles de charge de travail pour à la fois le trafic réseau interne (est/ouest) et externe (nord/sud) dans votre centre de données. Les règles de pare-feu étant intégrées au port vSwitch des machines virtuelles de charge de travail, elles sont réparties sur vos charges de travail dans le centre de données et les accompagnent.

À l’aide de l’API Northbound, vous pouvez définir les règles de pare-feu pour le trafic entrant et sortant à partir des machines virtuelles de charge de travail. Vous pouvez également configurer chaque règle de pare-feu pour consigner le trafic qui a été autorisé ou refusé par la règle.

## <a name="software-load-balancer-management"></a>Gestion avec l’équilibreur de charge logiciel

Avec l’[équilibreur de charge logiciel](software-load-balancer.md), vous pouvez permettre à plusieurs serveurs d’héberger la même charge de travail, offrant ainsi une haute disponibilité et une scalabilité supérieure. À l’aide de l’équilibreur de charge logiciel, vous pouvez configurer et gérer l’équilibrage de charge, la traduction d’adresses réseau (NAT) de trafic entrant et l’accès sortant en direction d’Internet pour les charges de travail connectées aux réseaux virtuels et aux réseaux VLAN traditionnels.

## <a name="gateway-management"></a>Gestion de la passerelle

La [passerelle RAS (Remote Access Service)](gateway-overview.md) vous permet de déployer, de configurer et de gérer des machines virtuelles qui sont membres d’un pool de passerelles, en fournissant une connectivité réseau externe aux charges de travail de vos clients. Avec les passerelles, les types de connectivité suivants sont pris en charge entre vos réseaux virtuels et distants :

- Connectivité avec la passerelle de réseau privé virtuel (VPN) de site à site à l’aide d’IPsec
- Connectivité de la passerelle VPN de site à site à l’aide de l’encapsulation GRE (Generic Routing Encapsulation)
- Fonctionnalité de transfert de couche 3
 
Les connexions de passerelle prennent en charge le protocole BGP (Border Gateway Protocol) pour la gestion dynamique des routes.

## <a name="virtual-appliance-chaining"></a>Chaînage d’appliances virtuelles

Cette fonctionnalité du contrôleur de réseau vous permet de joindre des appliances de réseau virtuel à vos réseaux virtuels. Ces appliances peuvent être utilisées pour de nombreux services réseau, tels que la protection avec pare-feu avancée, l’équilibrage de charge, la détection et la prévention des intrusions. Vous pouvez ajouter des appliances virtuelles qui exécutent des fonctions de routage et de mise en miroir des ports définies par l’utilisateur. Avec le routage défini par l’utilisateur, l’appliance virtuelle est utilisée en tant que routeur entre les sous-réseaux virtuels du réseau virtuel. Avec la mise en miroir des ports, tout le trafic réseau entrant ou sortant au niveau du port supervisé est dupliqué et envoyé à une appliance virtuelle à des fins d’analyse.

Pour en découvrir plus sur les routes définies par l’utilisateur, consultez [Utiliser des équipements virtuels réseau sur un réseau virtuel](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn).

## <a name="network-controller-deployment-considerations"></a>Considérations relatives au déploiement du contrôleur de réseau

- Ne déployez pas le rôle serveur du contrôleur de réseau sur les hôtes physiques. Le contrôleur de réseau doit être déployé sur ses propres machines virtuelles dédiées.

- Vous pouvez déployer le contrôleur de réseau dans les environnements de domaine et dans les environnements n’appartenant pas à un domaine. Dans les environnements de domaine, le contrôleur de réseau authentifie les utilisateurs et les appareils réseau à l’aide de Kerberos, tandis que dans les environnements n’appartenant pas à un domaine, vous devez déployer des certificats pour l’authentification.

- Il est essentiel que les déploiements du contrôleur de réseau offrent une haute disponibilité et la possibilité d’effectuer aisément un scale-up ou un scale-down en fonction des besoins de votre centre de données. Utilisez au moins trois machines virtuelles afin de fournir une haute disponibilité pour l’application de contrôleur de réseau.

- Pour obtenir une disponibilité et une scalabilité élevées, le contrôleur de réseau s’appuie sur Service Fabric. Service Fabric fournit une plateforme de systèmes distribués pour la création d’applications scalables, fiables et faciles à gérer. [Découvrez-en plus sur le contrôleur de réseau en tant qu’application Service Fabric](/windows-server/networking/sdn/technologies/network-controller/network-controller-high-availability#network-controller-as-a-service-fabric-application).


## <a name="next-steps"></a>Étapes suivantes

Pour consulter des informations connexes, reportez-vous également à :

- [Planifier le déploiement du contrôleur de réseau](network-controller.md)
- [Déployer le contrôleur de réseau à l’aide de Windows PowerShell](../deploy/network-controller-powershell.md)
- [SDN dans Azure Stack HCI](software-defined-networking.md)