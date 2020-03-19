---
title: Créer des passerelles VPN pour Azure Stack Hub
description: Créer et configurer des passerelles VPN pour Azure Stack Hub.
author: sethmanheim
ms.topic: conceptual
ms.date: 01/24/2020
ms.author: sethm
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: 35e17b6527b39bc12ad8f140b98a27fa6f4b69ac
ms.sourcegitcommit: 20d10ace7844170ccf7570db52e30f0424f20164
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79295256"
---
# <a name="create-vpn-gateways-for-azure-stack-hub"></a>Créer des passerelles VPN pour Azure Stack Hub

Avant de pouvoir envoyer du trafic réseau entre votre réseau virtuel Azure et votre site local, vous devez créer une passerelle de réseau virtuel pour votre réseau virtuel.

Une passerelle VPN est un type de passerelle de réseau virtuel qui envoie le trafic chiffré à travers une connexion publique. Vous pouvez utiliser des passerelles VPN pour échanger en toute sécurité du trafic entre un réseau virtuel dans Azure Stack Hub et un réseau virtuel dans Azure. Vous pouvez également envoyer en toute sécurité le trafic entre un réseau virtuel et un autre réseau connecté à un périphérique VPN.

Lorsque vous créez une passerelle de réseau virtuel, vous spécifiez le type de passerelle que vous voulez créer. Azure Stack Hub prend en charge un seul type de passerelle de réseau virtuel : le type **Vpn**.

Chaque réseau virtuel peut avoir deux passerelles de réseau virtuel, mais une seule de chaque type. Selon les paramètres que vous choisissez, vous pouvez créer plusieurs connexions à une passerelle VPN unique. Les connexions sur plusieurs sites sont un bon exemple.

Avant de créer et configurer des passerelles VPN pour Azure Stack Hub, voir [Considérations relatives à la gestion de réseau Azure Stack Hub](azure-stack-network-differences.md) pour savoir dans quelle mesure les configurations pour Azure Stack Hub diffèrent de celles pour Azure.

>[!NOTE]
>Dans Azure, le débit de la bande passante pour la référence SKU de passerelle VPN que vous choisissez doit être réparti entre toutes les connexions connectées à la passerelle. Toutefois, dans Azure Stack Hub, la valeur de la bande passante pour la référence (SKU) de la passerelle VPN est appliquée à chaque ressource de connexion connectée à la passerelle.
>
> Par exemple :
>
> * Dans Azure, la référence SKU de passerelle VPN de base peut prendre en charge environ 100 Mbits/s de débit agrégé. Si vous créez deux connexions pour cette passerelle VPN, dont une connexion avec une bande passante de 50 Mbits/s, 50 Mbits/s sont disponibles pour l’autre connexion.
> * Dans Azure Stack Hub, un débit de 100 Mbits/s est alloué à chaque connexion à la référence (SKU) de passerelle VPN de base.

## <a name="configuring-a-vpn-gateway"></a>Configuration d’une passerelle VPN

Une connexion par passerelle VPN s’appuie sur plusieurs ressources qui sont configurées avec des paramètres spécifiques. La plupart des ressources peuvent être configurées séparément, mais elles doivent être configurées dans un certain ordre dans certains cas.

### <a name="settings"></a>Paramètres

Les paramètres que vous choisissez pour chaque ressource sont essentiels à la création d’une connexion réussie.

Pour plus d’informations sur les ressources et paramètres spécifiques pour la passerelle VPN, voir [À propos des paramètres de la passerelle VPN pour Azure Stack Hub](azure-stack-vpn-gateway-settings.md). Cet article vous aide à comprendre :

* Les types de passerelle, les types de VPN et les types de connexion.
* Les sous-réseaux de passerelle, les passerelles de réseau local et les autres paramètres de ressource que vous souhaiterez peut-être prendre en compte.

### <a name="deployment-tools"></a>Outils de déploiement

Vous pouvez créer et configurer des ressources à l’aide d’un outil de configuration, comme le portail Azure. Vous pouvez ultérieurement décider de passer à un autre outil, tel que PowerShell, pour configurer des ressources supplémentaires ou modifier les ressources existantes, le cas échéant. Il n’est pour le moment pas possible de configurer toutes les ressources et tous les paramètres des ressources dans le portail Azure. Les instructions fournies dans les articles dédiés à chaque topologie de connexion indiquent si un outil de configuration spécifique est requis.

## <a name="connection-topology-diagrams"></a>Diagrammes de topologie de connexion

Il existe différentes configurations disponibles pour les connexions aux passerelles VPN. Déterminez la configuration qui correspond le mieux à vos besoins. Dans les sections ci-dessous, vous pouvez afficher des informations et des diagrammes de topologie sur les connexions de passerelle VPN suivantes :

* Modèle de déploiement disponible
* Outils de configuration disponibles
* Liens vous redirigeant directement vers un article, si disponibles

Les graphiques et les descriptions dans les sections suivantes peuvent vous aider à sélectionner une topologie de connexion répondant à vos besoins. Le graphique présente les principales topologies de base, mais il est possible de créer des configurations plus complexes à l’aide des diagrammes.

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a>Site à site et multi-sites (tunnel VPN IPsec/IKE)

### <a name="site-to-site"></a>De site à site

Une connexion par passerelle VPN *site à site* (S2S) est une connexion sur un tunnel VPN IPsec/IKE (IKEv2). Ce type de connexion nécessite un appareil VPN local disposant d’une adresse IP publique. Cet appareil ne peut pas se trouver derrière un traducteur d’adresses réseau. Les connexions S2S peuvent être utilisées pour les configurations hybrides et entre différents locaux.

![Exemple de configuration d’une connexion VPN de site à site](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="multi-site"></a>Multi-sites

Une connexion *multi-site* est une variante de la connexion site à site. Vous créez plusieurs connexions VPN à partir de votre passerelle de réseau virtuel, généralement en vous connectant à plusieurs sites locaux. Lorsque vous travaillez avec plusieurs connexions, vous devez utiliser un type de VPN basé sur la route (équivalent d’une passerelle dynamique pour les réseaux virtuels classiques). Chaque réseau virtuel ne pouvant disposer que d’une seule passerelle de réseau virtuel, toutes les connexions passant par la passerelle partagent la bande passante disponible.

![Exemple de connexion multisites de passerelle VPN Azure](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-multisite-connection-diagram.png)

## <a name="gateway-skus"></a>SKU de passerelle

Lorsque vous créez une passerelle de réseau virtuel pour Azure Stack Hub, vous spécifiez la référence (SKU) de passerelle que vous voulez utiliser. Les références SKU de passerelle VPN suivantes sont prises en charge :

* De base
* standard
* Hautes performances

Lorsque vous sélectionnez une référence SKU de passerelle supérieure, par exemple Standard à la place de De base, ou HighPerformance à la place de Standard ou De base, un plus grand nombre de processeurs et une bande passante réseau plus importante sont alloués à la passerelle. Par conséquent, la passerelle peut prendre en charge un débit réseau plus élevé sur le réseau virtuel.

Azure Stack Hub ne prend pas en charge la référence (SKU) de passerelle Ultra-Performance utilisée exclusivement avec ExpressRoute.

Prenez en compte les éléments suivants lorsque vous sélectionnez la référence SKU :

* Azure Stack Hub ne prend pas en charge les passerelles basées sur des stratégies.
* Le protocole de passerelle frontière (BGP) n’est pas pris en charge pour la référence SKU de base.
* Les configurations coexistantes de passerelle VPN et d’ExpressRoute ne sont pas prises en charge dans Azure Stack Hub.

## <a name="gateway-availability"></a>Disponibilité de la passerelle

Les scénarios haute disponibilité ne peuvent être configurés que sur la connexion SKU de la **passerelle hautes performances**. À la différence d’Azure, disponible dans des configurations de déploiement actif/actif et actif/passif, Azure Stack Hub ne prend en charge que la configuration de déploiement actif/passif.

### <a name="failover"></a>Basculement

Azure Stack Hub contient trois machines virtuelles d’infrastructure de passerelle mutualisée. Deux de ces machines virtuelles sont en mode actif et la troisième est en mode redondant. Les machines virtuelles actives permettent la création de connexions VPN et la machine virtuelle redondante accepte uniquement les connexions VPN en cas de basculement. Si une machine virtuelle de passerelle active n’est plus disponible, la connexion VPN bascule vers la machine virtuelle redondante quelques instants (quelques secondes) après la perte de la connexion.

## <a name="estimated-aggregate-throughput-by-sku"></a>Débit agrégé estimé par SKU

Le tableau suivant présente les types de passerelle et le débit total estimé par référence SKU de passerelle :

|| Débit de passerelle VPN (1) | Tunnels IPsec max de passerelle VPN (2) |
|-------|-------|-------|
|**Référence (SKU) de base** **(3)** | 100 Mbits/s | 20 |
|**Référence Standard** | 100 Mbits/s | 20 |
|**Référence Hautes performances** | 200 Mbits/s | 10 |

### <a name="table-notes"></a>Notes de la table

**(1)** Le débit VPN n’est pas garanti pour les connexions intersites via Internet. Il s’agit de la mesure du débit maximal possible.  
**(2)** Le nombre maximal de tunnels est le total par déploiement Azure Stack Hub pour tous les abonnements.  
**(3)** Le routage BGP n’est pas pris en charge pour la référence SKU de base.

>[!NOTE]
>Vous ne pouvez créer qu’une seule connexion VPN site à site entre deux déploiements Azure Stack Hub. En effet, la plateforme n’autorise qu’une seule connexion VPN à la même adresse IP. Dans la mesure où Azure Stack exploite la passerelle mutualisée qui utilise une seule adresse IP publique pour toutes les passerelles VPN dans le système Azure Stack Hub, il ne peut y avoir qu’une seule connexion VPN entre deux systèmes Azure Stack Hub. Cette limitation s’applique également à la connexion de plusieurs connexions VPN de site à site à une passerelle VPN qui utilise une seule adresse IP. Azure Stack Hub n’autorise pas la création de plus d’une ressource de passerelle de réseau local à l’aide de la même adresse IP.

## <a name="next-steps"></a>Étapes suivantes

* [Paramètres de configuration de la passerelle VPN pour Azure Stack Hub](azure-stack-vpn-gateway-settings.md)
