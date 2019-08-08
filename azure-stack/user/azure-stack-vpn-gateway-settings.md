---
title: Configurer les paramètres de passerelle réseau VPN pour Azure Stack | Microsoft Docs
description: En savoir plus et configurer les paramètres des passerelles VPN que vous utilisez avec Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: fa8d3adc-8f5a-4b4f-8227-4381cf952c56
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: sethm
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: 53a423ebc8e9f503934bfd3df2f4962a7b584059
ms.sourcegitcommit: b3dac698f2e1834491c2f9af56a80e95654f11f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68658581"
---
# <a name="configure-vpn-gateway-settings-for-azure-stack"></a>Configurer les paramètres de passerelle réseau VPN pour Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Une passerelle VPN est un type de passerelle de réseau virtuel qui envoie le trafic chiffré entre votre réseau virtuel dans Azure Stack et une passerelle VPN distante. La passerelle VPN distante peut être dans Azure, un appareil dans votre centre de données ou un appareil dans un autre site. S’il y a une connexion réseau entre deux points de terminaison, vous pouvez établir une connexion VPN sécurisée de site à site (S2S) entre les deux réseaux.

Une connexion de passerelle VPN s’appuie sur la configuration de plusieurs ressources, contenant chacune des paramètres configurables. Cet article décrit les ressources et les paramètres relatifs à une passerelle VPN pour un réseau virtuel créé dans le modèle de déploiement Resource Manager. Vous trouverez les descriptions et les diagrammes de topologie de chaque solution de connexion dans [À propos la passerelle VPN pour Azure Stack](azure-stack-vpn-gateway-about-vpn-gateways.md).

## <a name="vpn-gateway-settings"></a>Paramètres de la passerelle VPN

### <a name="gateway-types"></a>Types de passerelle

Chaque réseau virtuel de Azure Stack prend en charge une passerelle de réseau virtuel unique, qui doit être du type **Vpn**.  Cela diffère d’Azure, qui prend également en charge d’autres types.

Lorsque vous créez une passerelle de réseau virtuel, vous devez vous assurer que le type de passerelle est adapté à votre configuration. Une passerelle VPN nécessite l’indicateur `-GatewayType Vpn`, par exemple :

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn
-VpnType RouteBased
```

### <a name="gateway-skus"></a>SKU de passerelle

Lorsque vous créez une passerelle de réseau virtuel, vous devez spécifier la référence SKU de passerelle que vous voulez utiliser. Sélectionnez les références SKU qui répondent à vos besoins en fonction des types de charges de travail, des débits, des fonctionnalités et des contrats de niveau de service.

Azure Stack propose les références SKU de passerelle VPN indiquées dans le tableau suivant.

| | Débit de passerelle VPN |Tunnels IPsec max de passerelle VPN |
|-------|-------|-------|
|**Référence De base**  | 100 Mbits/s  | 20    |
|**Référence Standard**   | 100 Mbits/s  | 20 |
|**Référence Hautes performances** | 200 Mbits/s | 10 |

### <a name="resizing-gateway-skus"></a>Redimensionnement des références SKU de passerelle

Azure Stack ne prend pas en charge un redimensionnement des références SKU entre les références SKU héritées prises en charge.

De même, Azure Stack ne gère pas les redimensionnements d’une référence SKU héritée prise en charge (**De base**, **Standard** et **Hautes performances**) vers une référence SKU plus récente prise en charge par Azure (**VpnGw1**, **VpnGw2** et **VpnGw3**).

### <a name="configure-the-gateway-sku"></a>Configuration de la référence SKU de passerelle

#### <a name="azure-stack-portal"></a>Portail Azure Stack

Si vous utilisez le portail Azure Stack pour créer une passerelle de réseau virtuel Resource Manager, vous pouvez sélectionner la référence SKU de la passerelle dans la liste déroulante. Ces options correspondent au type de passerelle et au type de VPN sélectionnés.

#### <a name="powershell"></a>PowerShell

L’exemple PowerShell suivant spécifie la `-GatewaySku` en tant que **VpnGw1** :

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig -GatewaySku VpnGw1
-GatewayType Vpn -VpnType RouteBased
```

### <a name="connection-types"></a>Types de connexion

Dans le modèle de déploiement de Resource Manager, chaque configuration nécessite un type spécifique de connexion de passerelle de réseau virtuel. Les valeurs de PowerShell pour Resource Manager disponibles pour `-ConnectionType` sont :

* **IPsec**

   L’exemple PowerShell ci-après crée une connexion S2S qui nécessite le type de connexion IPsec :

   ```powershell
   New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg
   -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local
   -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```

### <a name="vpn-types"></a>Types de VPN

Lorsque vous créez la passerelle de réseau virtuel d’une configuration de passerelle VPN, vous devez spécifier un type de VPN. Le type de VPN que vous choisissez dépend de la topologie de connexion que vous souhaitez créer. Un type VPN peut également dépendre du matériel utilisé. Les configurations S2S nécessitent un périphérique VPN. Certains périphériques VPN seront ne prennent en charge qu’un certain type de VPN.

> [!IMPORTANT]  
> Pour l’instant, Azure Stack prend uniquement en charge le type de VPN basé sur le routage. Si votre appareil ne gère que les VPN basés sur les stratégies, les connexions à ces appareils à partir d’Azure Stack ne sont pas prises en charge.  
>
> En outre, pour le moment, Azure Stack ne gère pas l’utilisation des sélecteurs de trafic reposant sur les stratégies pour les passerelles basées sur le routage, car les configurations de stratégies IPSec/IKE personnalisées ne sont pas prises en charge.

* **PolicyBased** : les VPN basés sur les stratégies chiffrent et acheminent les paquets par le biais des tunnels IPsec basés sur les stratégies IPsec qui sont configurées avec les combinaisons de préfixes d’adresses entre votre réseau local et le réseau virtuel Azure Stack. La stratégie ou le sélecteur de trafic prennent généralement la forme d’une liste d’accès dans la configuration de périphérique VPN.

  >[!NOTE]
  >Le type **PolicyBased** est pris en charge dans Azure, mais non dans Azure Stack.

* **RouteBased** : les VPN RouteBased basés sur le routage utilisent des itinéraires qui sont configurés dans la table de transfert IP ou de routage pour acheminer les paquets vers leurs interfaces de tunnel correspondantes. Les interfaces de tunnel chiffrent ou déchiffrent ensuite les paquets se trouvant dans et hors des tunnels. La stratégie, ou sélecteur de trafic, des VPN **RouteBased** est configurée comme universelle (ou utilise des caractères génériques). Par défaut, ils ne sont pas modifiables. Le VPN **RouteBased** a pour valeur **RouteBased**.

L’exemple PowerShell suivant spécifie la `-VpnType` en tant que **RouteBased**. Lorsque vous créez une passerelle, vous devez vous assurer que le type `-VpnType` est adapté à votre configuration.

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig
-GatewayType Vpn -VpnType RouteBased
```

### <a name="gateway-requirements"></a>Conditions requises de la passerelle

Le tableau suivant répertorie la configuration requise pour les passerelles VPN.

| |Passerelle VPN basée sur des stratégies de base | Passerelle VPN basée sur des itinéraires de base | Passerelle VPN basée sur des itinéraires standard | Passerelle VPN à hautes performances basée sur des itinéraires|
|--|--|--|--|--|
| **Connectivité de site à site (connectivité S2S)** | Non pris en charge | Configuration du VPN basé sur les itinéraires | Configuration du VPN basé sur les itinéraires | Configuration du VPN basé sur les itinéraires |
| **Méthode d’authentification**  | Non pris en charge | Clé prépartagée pour la connectivité S2S  | Clé prépartagée pour la connectivité S2S  | Clé prépartagée pour la connectivité S2S  |
| **Nombre maximal de connexions de site à site**  | Non pris en charge | 20 | 20| 10|
|**Prise en charge de routage actif (BGP)** | Non pris en charge | Non pris en charge | Pris en charge | Pris en charge |

### <a name="gateway-subnet"></a>Sous-réseau de passerelle

Avant de créer votre passerelle VPN, vous devez d’abord créer un sous-réseau de passerelle. Le sous-réseau de passerelle comporte les adresses IP utilisées par les machines virtuelles et les services de passerelle de réseau virtuel. Lors de la création de votre passerelle de réseau virtuel, les machines virtuelles de passerelle sont déployées dans le sous-réseau de passerelle et configurées avec les paramètres de passerelle VPN requis. Ne déployez aucun autre élément (des machines virtuelles supplémentaires, par exemple) dans le sous-réseau de passerelle.

>[!IMPORTANT]
>Pour fonctionner correctement, le sous-réseau de passerelle doit être nommé **SousRéseau_Passerelle** . Azure Stack utilise ce nom pour identifier le sous-réseau dans lequel déployer les machines virtuelles et les services de passerelle de réseau virtuel.

Lorsque vous créez le sous-réseau de passerelle, vous spécifiez le nombre d’adresses IP que contient le sous-réseau. Les adresses IP dans le sous-réseau de passerelle sont allouées aux machines virtuelles et aux services de passerelle. Certaines configurations nécessitent plus d’adresses IP que d’autres. Prenez connaissance des instructions relatives à la configuration que vous souhaitez créer et vérifier que le sous-réseau de passerelle que vous souhaitez créer respecte ces instructions.

En outre, vous devez vous assurer que votre sous-réseau de passerelle dispose de suffisamment d’adresses IP pour gérer les futures configurations supplémentaires. Bien qu’il vous soit possible de créer un sous-réseau de passerelle aussi petit que /29, nous vous recommandons de créer un sous-réseau de taille /28 ou supérieure (/28, /27, /26, etc.). Ainsi, si vous ajoutez des fonctionnalités par la suite, vous n’aurez pas à détruire votre passerelle, puis à supprimer et à recréer le sous-réseau de passerelle pour autoriser d’autres adresses IP.

L’exemple PowerShell Resource Manager suivant montre un sous-réseau de passerelle nommé **GatewaySubnet**. Vous pouvez voir que la notation CIDR spécifie une taille /27, ce qui permet d’avoir un nombre suffisamment élevé d’adresses IP pour la plupart des configurations actuelles.

```powershell
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

> [!IMPORTANT]
> Lorsque vous travaillez avec des sous-réseaux de passerelle, évitez d’associer un groupe de sécurité réseau (NSG) au sous-réseau de passerelle. Si vous associez un groupe de sécurité réseau à ce sous-réseau, votre passerelle VPN cessera peut-être de fonctionner normalement. Pour plus d’informations sur les groupes de sécurité réseau, consultez [Présentation du groupe de sécurité réseau ?](/azure/virtual-network/virtual-networks-nsg).

### <a name="local-network-gateways"></a>Passerelles de réseau local

Lorsque vous créez une configuration de passerelle VPN dans Azure, la passerelle du réseau local représente souvent votre emplacement local. Dans Azure Stack, elle représente n’importe quel périphérique VPN distant se situant en dehors d’Azure Stack. Cet appareil peut être un appareil VPN de votre centre de données (ou d’un centre de données distant) ou bien d’une passerelle VPN dans Azure.

Vous donnez un nom à la passerelle de réseau local (l’adresse IP publique de l’appareil VPN) et vous spécifiez les préfixes d’adresse se trouvant dans l’emplacement local. Azure examine les préfixes d’adresse de destination pour le trafic réseau, consulte la configuration que vous avez spécifiée pour votre passerelle de réseau local, et route les paquets en conséquence.

L’exemple PowerShell ci-après crée une nouvelle passerelle de réseau local :

```powershell
New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

Parfois, vous devez modifier les paramètres de passerelle de réseau local. C’est le cas, par exemple, lorsque vous ajoutez ou modifiez la plage d’adresses, ou lorsque l’adresse IP du périphérique VPN change. Pour plus d’informations, consultez [Modification des paramètres de passerelle de réseau local à l’aide de PowerShell](/azure/vpn-gateway/vpn-gateway-modify-local-network-gateway).

## <a name="ipsecike-parameters"></a>Paramètres IPsec/IKE

Lorsque vous configurez une connexion VPN dans Azure Stack, vous devez configurer la connexion à chaque extrémité. Si vous configurez une connexion VPN entre Azure Stack et un périphérique matériel tel qu’un commutateur ou un routeur agissant comme une passerelle VPN, ce périphérique peut demander des paramétrages supplémentaires.

Contrairement à Azure, qui prend en charge plusieurs offres en tant qu’initiateur et répondeur, Azure Stack ne prend en charge qu’une seule offre par défaut.  Si vous devez utiliser différents paramètres IPSec/IKE pour travailler avec votre appareil VPN, d’autres paramètres sont disponibles pour vous permettre de configurer votre connexion manuellement.  Pour plus d’informations, consultez [Configurer la stratégie IPsec/IKE pour des connexions VPN site à site](azure-stack-vpn-s2s.md).

### <a name="ike-phase-1-main-mode-parameters"></a>Paramètres IKE Phase 1 (Mode principal)

| Propriété              | Valeur|
|-|-|
| Version IKE           | IKEv2 |
|Groupe Diffie-Hellman   | Groupe 2 (1 024 bits) |
| Méthode d'authentification | Clé prépartagée |
|Chiffrement et algorithmes de hachage | AES256, SHA256 |
|Durée de vie de l’AS (durée)     | 28 800 secondes|

### <a name="ike-phase-2-quick-mode-parameters"></a>Paramètres IKE Phase 2 (Mode rapide)

| Propriété| Valeur|
|-|-|
|Version IKE |IKEv2 |
|Chiffrement et algorithmes de hachage (Chiffrement)     | GCMAES256|
|Chiffrement et algorithmes de hachage (Authentification) | GCMAES256|
|Durée de vie de l’AS (durée)  | 27 000 secondes  |
|Durée de vie de l’AS (kilo-octets) | 33 553 408     |
|PFS (Perfect Forward Secrecy) |Aucun (e) (voir remarque 1) |
|Détection d’homologue mort | Pris en charge|  

* *Remarque 1 :*  Avant la version 1807, Azure Stack utilisait la valeur PFS2048 pour la propriété PFS (Perfect Forward Secrecy).

## <a name="next-steps"></a>Étapes suivantes

* [Connexion d’Azure Stack à Azure à l’aide d’ExpressRoute](../operator/azure-stack-connect-expressroute.md)
