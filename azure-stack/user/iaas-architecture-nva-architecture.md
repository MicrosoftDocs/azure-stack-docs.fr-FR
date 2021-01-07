---
title: Déployer des appliances virtuelles réseau hautement disponibles sur Azure Stack Hub
description: Apprenez à déployer des appliances virtuelles réseau hautement disponibles sur Azure Stack Hub.
author: mattbriggs
ms.topic: how-to
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 11/01/2019
ms.openlocfilehash: 7e928757447f47c9235690f38e62778a4594faf1
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97873773"
---
# <a name="deploy-highly-available-network-virtual-appliances-on-azure-stack-hub"></a>Déployer des appliances virtuelles réseau hautement disponibles sur Azure Stack Hub

Cet article explique comment déployer des appliances virtuelles réseau pour la haute disponibilité dans Azure Stack Hub. Une appliance virtuelle réseau est généralement utilisée pour contrôler le flux de trafic réseau à partir d’un réseau de périmètre, également appelé DMZ, vers d’autres réseaux ou sous-réseaux. L’article inclut des exemples d’architecture pour l’entrée ou la sortie uniquement, et pour les deux à la fois.

Différents fournisseurs proposent des appliances virtuelles réseau sur la [Place de marché Azure Stack Hub](../operator/azure-stack-marketplace-azure-items.md). N’hésitez pas à avoir recours à l’une de ces appliances pour bénéficier de performances optimales.

L’architecture possède les composants suivants :

## <a name="networking-and-load-balancing"></a>Mise en réseau et équilibrage de charge

-   **Réseau virtuel et sous-réseaux**. Chaque machine virtuelle Azure est déployée dans un réseau virtuel qui peut être segmenté en sous-réseaux. Créez un sous-réseau distinct pour chaque niveau.

-   **Équilibreur de charge de couche 7.** Application Gateway n’étant pas encore disponible sur Azure Stack Hub, des alternatives sont proposées sur la [Place de marché Azure Stack Hub ](../operator/azure-stack-marketplace-azure-items.md), par exemple : [Commutateur de contenu ADC Load Balancer KEMP LoadMaster](https://azuremarketplace.microsoft.com/marketplace/apps/kemptech.vlm-azure)/ [f5 Big-IP Virtual Edition](https://azuremarketplace.microsoft.com/marketplace/apps/f5-networks.f5-big-ip-best) ou [A10 vThunder ADC](https://azuremarketplace.microsoft.com/marketplace/apps/a10networks.vthunder-414-gr1)

-   **Équilibreurs de charge** : Utilisez [Azure Load Balancer](/azure/load-balancer/load-balancer-overview) pour répartir le trafic réseau de la couche Web vers la couche Entreprise, et de la couche Entreprise vers SQL Server.

-   **Groupes de sécurité réseau (NSG) :** Utilisez des groupes de sécurité réseau pour limiter le trafic réseau au sein du réseau virtuel. Par exemple, dans l'architecture à trois couches illustrée ici, la couche Base de données n'accepte pas le trafic en provenance du serveur web frontal, mais uniquement de la couche Entreprise et du sous-réseau de gestion.

-   **Itinéraires définis par l'utilisateur (UDR).** Utilisez des [*itinéraires définis par l'utilisateur*](/azure/virtual-network/virtual-networks-udr-overview/) (UDR) pour acheminer le trafic vers l'équilibreur de charge spécifique.

Cet article suppose une connaissance élémentaire de la mise en réseau Azure Stack Hub.

## <a name="architecture-diagrams"></a>Diagrammes d’architecture

Une appliance virtuelle réseau peut être déployée sur un réseau de périmètre dans de nombreuses architectures différentes. Par exemple, la figure suivante illustre l'utilisation d'une seule appliance virtuelle réseau pour l'entrée.

![Capture d’écran illustrant l’utilisation d’une appliance virtuelle réseau unique pour l’entrée.](./media/iaas-architecture-nva-architecture/iaas-architecture-nva-architecture-image1.svg)

Dans cette architecture, l’appliance virtuelle réseau assure une limite réseau sécurisée par la vérification de l’ensemble du trafic réseau entrant et sortant et la transmission uniquement du trafic répondant aux règles de sécurité du réseau. Le fait que tout le trafic réseau doive traverser l'appliance virtuelle réseau signifie que cette dernière est un point de défaillance unique au sein du réseau. Si celle-ci échoue, il n’existe aucun autre chemin d’accès pour le trafic réseau et tous les sous-réseaux principaux sont indisponibles.

Pour rendre une appliance virtuelle réseau hautement disponible, déployez plusieurs appliances virtuelles réseau dans un groupe à haute disponibilité.

Les architectures suivantes décrivent les ressources et la configuration nécessaire pour des appliances virtuelles réseau hautement disponibles :

| Solution | Avantages | Considérations |
| --- | --- | --- |
| Entrée avec appliances virtuelles réseau de couche 7 | Tous les nœuds d'appliance virtuelle réseau sont actifs. | Requiert une appliance virtuelle réseau capable de mettre fin aux connexions et d'utiliser la traduction d'adresses réseau source (SNAT).<br>Requiert un ensemble distinct d’appliances virtuelles réseau pour le trafic en provenance du réseau d’entreprise/d’Internet et d’Azure Stack Hub.<br>Peut uniquement être utilisé pour le trafic provenant de l’extérieur d’Azure Stack Hub.  |
| Sortie avec appliances virtuelles réseau de couche 7 | Tous les nœuds d'appliance virtuelle réseau sont actifs. | Requiert une appliance virtuelle réseau capable de mettre fin aux connexions et d'implémenter la traduction d'adresses réseau source (SNAT). |
| Entrée-sortie avec appliances virtuelles réseau de couche 7 | Tous les nœuds sont actifs.<br>Capable de gérer le trafic en provenance d’Azure Stack Hub. | Requiert une appliance virtuelle réseau capable de mettre fin aux connexions et d'utiliser la traduction d'adresses réseau source (SNAT).<br>Requiert un ensemble distinct d’appliances virtuelles réseau pour le trafic en provenance du réseau d’entreprise/d’Internet et d’Azure Stack Hub. |

## <a name="ingress-with-layer-7-nvas"></a>Entrée avec appliances virtuelles réseau de couche 7

La figure suivante illustre une architecture à haute disponibilité qui implémente un réseau de périmètre d'entrée derrière un équilibreur de charge orienté Internet. Cette architecture est conçue pour fournir une connectivité HTTP ou HTTPS aux charges de travail Azure Stack Hub pour le trafic de couche 7 :

![Capture d'écran d'une description de carte générée automatiquement](./media/iaas-architecture-nva-architecture/iaas-architecture-nva-architecture-image2.svg)

L’avantage de cette architecture réside dans le fait que toutes les appliances virtuelles réseau sont actives et qu’en cas d’échec de l’une d’entre elles l’équilibreur de charge dirige le trafic réseau vers l’autre appliance virtuelle. Les deux appliances virtuelles réseau acheminent le trafic vers l’équilibreur de charge interne. Ainsi, le trafic est maintenu tant que l’une d’entre elles est active. Les appliances virtuelles réseau doivent arrêter le trafic SSL destiné aux machines virtuelles de la couche web. Ces appliances virtuelles réseau ne peuvent pas être étendues pour gérer le trafic du réseau d'entreprise car celui-ci requiert un autre ensemble dédié d'appliances virtuelles réseau possédant leurs propres itinéraires réseau.

## <a name="egress-with-layer-7-nvas"></a>Sortie avec appliances virtuelles réseau de couche 7

L’architecture Entrée avec appliances virtuelles réseau de couche 7 peut être étendue de manière à fournir un réseau de périmètre de sortie pour les requêtes provenant de la charge de travail Azure Stack Hub. L'architecture suivante est conçue pour assurer une haute disponibilité des appliances virtuelles réseau du réseau de périmètre pour le trafic de couche 7, tel que HTTP ou HTTPS :

![Capture d'écran d'une description de téléphone portable générée automatiquement](./media/iaas-architecture-nva-architecture/iaas-architecture-nva-architecture-image4.svg)

Dans cette architecture, tout le trafic en provenance d’Azure Stack Hub est acheminé vers un équilibreur de charge interne. Ce dernier répartit les requêtes sortantes entre un ensemble d’appliances virtuelles réseau. Celles-ci dirigent le trafic vers Internet à l’aide de leurs adresses IP publiques individuelles.

## <a name="ingress-egress-with-layer-7--nvas"></a>Entrée-sortie avec appliances virtuelles réseau de couche 7

Les deux architectures d'entrée et de sortie disposaient d'un réseau de périmètre distinct pour l'entrée et la sortie. L'architecture suivante montre comment créer un réseau de périmètre qui peut être utilisé à la fois en entrée et en sortie pour le trafic de couche 7, tel que HTTP ou HTTPS :

![Capture d'écran d'une description de publication sur les réseaux sociaux générée automatiquement](./media/iaas-architecture-nva-architecture/iaas-architecture-nva-architecture-image4.svg)

Dans l'architecture Entrée-sortie avec appliances virtuelles réseau de couche 7, les appliances virtuelles réseau traitent les demandes entrantes provenant d'un équilibreur de charge de couche 7. Les appliances virtuelles réseau traitent également des requêtes sortantes issues des machines virtuelles de la charge de travail du pool principal de l’équilibreur de charge. Comme le trafic entrant est acheminé avec un équilibreur de charge de couche 7 et que le trafic sortant est acheminé avec un SLB (équilibreur de charge Azure Stack Hub de base), les appliances virtuelles réseau sont responsables de la gestion de l’affinité de session. Autrement dit, l'équilibreur de charge de couche 7 gère le mappage des requêtes entrantes et sortantes afin de pouvoir envoyer la réponse qui convient au demandeur initial. Toutefois, l'équilibreur de charge interne n'a pas accès aux mappages de l'équilibreur de charge de couche 7 et utilise sa propre logique pour envoyer des réponses aux appliances virtuelles réseau. Il est possible que l'équilibreur de charge envoie une réponse à une appliance virtuelle réseau qui n'a pas initialement reçu la requête de l'équilibreur de charge de couche 7. Dans ce cas, les appliances virtuelles réseau doivent communiquer et se transférer la réponse entre elles afin que l'appliance virtuelle réseau appropriée puisse transmettre la réponse à l'équilibreur de charge de couche 7.

> [!NOTE]  
> Vous pouvez également résoudre le problème de routage asymétrique en s’assurant que les appliances virtuelles réseau effectuent la traduction des adresses réseau sources entrantes (SNAT). Cela remplacerait l’IP source d’origine du demandeur par une des adresses IP de l’appliance virtuelle réseau utilisée sur le flux entrant. Cela garantit de pouvoir utiliser plusieurs appliances virtuelles réseau à la fois, tout en conservant la symétrie de l’itinéraire.

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur les machines virtuelles Azure Stack Hub, voir [Fonctionnalités des machines virtuelles Azure Stack Hub](azure-stack-vm-considerations.md).  
- Pour plus d’informations sur les modèles Azure Cloud, consultez [Modèles de conception cloud](/azure/architecture/patterns).
