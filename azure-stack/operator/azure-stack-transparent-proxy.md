---
title: Proxy transparent pour les systèmes intégrés Azure Stack Hub
description: Vue d’ensemble d’un proxy transparent dans les systèmes intégrés Azure Stack Hub.
author: PatAltimore
ms.topic: conceptual
ms.date: 01/25/2021
ms.author: patricka
ms.reviewer: sranthar
ms.lastreviewed: 01/25/2021
ms.openlocfilehash: 974f40364b4eed13bd7440b35596597312c98624
ms.sourcegitcommit: 283b1308142e668749345bf24b63d40172559509
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99577355"
---
# <a name="transparent-proxy-for-azure-stack-hub"></a>Proxy transparent pour Azure Stack Hub

Un proxy transparent (également appelé proxy d’interception, en ligne ou forcé) intercepte une communication normale sur la couche réseau sans nécessiter de configuration spéciale du client. Les clients ne doivent pas nécessairement être informés de l’existence du proxy.

Si votre centre de données exige que l’ensemble du trafic utilise un proxy, configurez un proxy transparent pour traiter l’ensemble du trafic conformément à la stratégie en séparant le trafic entre les zones de votre réseau.

## <a name="traffic-types"></a>Types de trafic

Le trafic sortant d’Azure Stack Hub est catégorisé comme trafic de locataire ou trafic d’infrastructure.

Le trafic de locataire est généré par les locataires au moyen de machines virtuelles, d’équilibreurs de charge, de passerelles VPN, de services d’application, etc.

Le trafic d’infrastructure est généré à partir de la première plage `/27` du pool d’adresses IP virtuelles publiques affecté aux services d’infrastructure (identité, correctif et mise à jour, métriques d’utilisation, syndication de Place de marché, inscription, collecte de journaux, Windows Defender, etc.). Le trafic de ces services est acheminé vers les [points de terminaison Azure](azure-stack-integrate-endpoints.md#ports-and-urls-outbound). Azure n’accepte pas le trafic modifié par un proxy ni le trafic TLS/SSL intercepté. C’est la raison pour laquelle Azure Stack Hub ne prend pas en charge une configuration de proxy natif.

Quand vous configurez un proxy transparent, vous pouvez choisir d’envoyer tout le trafic sortant ou uniquement le trafic d’infrastructure par le biais du proxy.

## <a name="partner-integration"></a>Intégration des partenaires

Microsoft a conclu un partenariat avec les principaux fournisseurs de proxy du secteur pour valider les scénarios de cas d’usage d’Azure Stack Hub avec une configuration de proxy transparent. Le diagramme suivant montre un exemple de configuration d’un réseau Azure Stack Hub avec des proxys haute disponibilité. Les appareils proxy externes doivent être placés au nord des appareils frontière.

![Diagramme du réseau avec un proxy avant les appareils frontière](./media/azure-stack-transparent-proxy/proxy.svg)

 De plus, les appareils frontière doivent être configurés pour acheminer le trafic d’Azure Stack Hub de l’une des manières suivantes :
- Acheminer tout le trafic sortant d’Azure Stack Hub vers les appareils proxy
- Acheminez tout le trafic sortant de la première plage `/27` du pool d’adresses IP virtuelles Azure Stack Hub vers les appareils proxy par le biais du routage basé sur des stratégies.  

Pour obtenir un exemple de configuration frontière, consultez la section [Exemple de configuration frontière](#example-border-configuration) dans cet article.

Passez en revue les documents suivants pour obtenir les configurations validées de proxy transparent avec Azure Stack Hub : 

- [Configurer un proxy transparent Check Point Security Gateway](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk171559)
- [Configurer le pare-feu Sophos XG en tant que proxy transparent](https://community.sophos.com/xg-firewall/f/recommended-reads/124106/xg-firewall-integration-with-azure-stack-hub)

Dans les scénarios où le trafic sortant d’Azure Stack Hub doit transiter par un proxy explicite, les appareils Sophos et Check Point proposent une fonctionnalité de mode double qui autorise des plages de trafic spécifiques à passer par le mode transparent, tandis que d’autres plages peuvent être configurées pour passer par le mode explicite. Grâce à cette fonctionnalité, vous pouvez configurer ces appareils proxy de manière à envoyer uniquement le trafic d’infrastructure par le bais du proxy transparent et tout le trafic de locataire par le bais du proxy explicite.

> [!IMPORTANT]
> L’interception du trafic SSL n’est pas prise en charge et peut entraîner des échecs de service lors de l’accès aux points de terminaison. Le délai d’expiration maximal pris en charge pour communiquer avec les points de terminaison requis pour l’identité est de 60 secondes. 3 tentatives sont autorisées. Pour plus d’informations, consultez [Intégration d’un pare-feu à Azure Stack Hub](azure-stack-firewall.md#ssl-interception).

## <a name="example-border-configuration"></a>Exemple de configuration de frontière

La solution repose sur le routage basé sur des stratégies (PBR), lequel utilise un jeu de critères défini par l’administrateur et implémenté par une liste de contrôle d’accès (ACL). Cette liste catégorise le trafic qui est dirigé vers l’adresse IP de tronçon suivant des appareils proxy implémentés dans un route-map, et non dans un routage normal basé uniquement sur l’adresse IP de destination. Le trafic réseau d’infrastructure spécifique pour les ports 80 et 443 est acheminé des appareils frontière vers le déploiement du proxy transparent. Le proxy transparent effectue le filtrage d’URL, *aucun trafic autorisé* n’étant supprimé.

L’exemple de configuration suivant est destiné à un châssis Cisco Nexus 9508. 

Dans ce scénario, les réseaux d’infrastructure source qui nécessitent un accès à Internet sont les suivants :

- Adresse IP virtuelle publique - Première plage /27
- Réseau d’infrastructure - Dernière plage /27
- Réseau BMC - Dernière plage /27

Les sous-réseaux suivants reçoivent le traitement du routage basé sur des stratégies (PBR) dans ce scénario :

| Réseau | Plage d’adresses IP | Sous-réseau recevant le traitement PBR
|---------|----------|-------------------------------
| Pool d’adresses IP virtuelles | Première plage /27 de 172.21.107.0/27 | 172.21.107.0/27 = 172.21.107.1 à 172.21.107.30
| Réseau d’infrastructure | Dernière plage /27 de 172.21.7.0/24 | 172.21.7.224/27 = 172.21.7.225 à 172.21.7.254
| Réseau BMC | Dernière plage /27 de 10.60.32.128/26 | 10.60.32.160/27 = 10.60.32.161 à 10.60.32.190

### <a name="configure-border-device"></a>Configurer un appareil frontière

Activez PBR en entrant la commande `feature pbr`. 

```
****************************************************************************
PBR Configuration for Cisco Nexus 9508 Chassis
PBR Enivronment configured to use VRF08
The test rack has is a 4-node Azure Stack stamp with 2x TOR switches and 1x BMC switch. Each TOR switch 
has a single uplink to the Nexus 9508 chassis using BGP for routing. In this example the test rack 
is in it's own VRF (VRF08)
****************************************************************************
!
feature pbr
!

<Create VLANs that the proxy devices will use for inside and outside connectivity>

!
VLAN 801
name PBR_Proxy_VRF08_Inside
VLAN 802
name PBR_Proxy_VRF08_Outside
!
interface vlan 801
description PBR_Proxy_VRF08_Inside
no shutdown
mtu 9216
vrf member VRF08
no ip redirects
ip address 10.60.3.1/29
!
interface vlan 802
description PBR_Proxy_VRF08_Outside
no shutdown
mtu 9216
vrf member VRF08
no ip redirects
ip address 10.60.3.33/28
!
!
ip access-list PERMITTED_TO_PROXY_ENV1
100 permit tcp 172.21.107.0/27 any eq www
110 permit tcp 172.21.107.0/27 any eq 443
120 permit tcp 172.21.7.224/27 any eq www
130 permit tcp 172.21.7.224/27 any eq 443
140 permit tcp 10.60.32.160/27 any eq www
150 permit tcp 10.60.32.160/27 any eq 443
!
!
route-map TRAFFIC_TO_PROXY_ENV1 pbr-statistics
route-map TRAFFIC_TO_PROXY_ENV1 permit 10
  match ip address PERMITTED_TO_PROXY_ENV1
  set ip next-hop 10.60.3.34 10.60.3.35
!
!
interface Ethernet1/1
  description DownLink to TOR-1:TeGig1/0/47
  mtu 9100
  logging event port link-status
  vrf member VRF08
  ip address 192.168.32.193/30
  ip policy route-map TRAFFIC_TO_PROXY_ENV1
  no shutdown
!
interface Ethernet2/1
  description DownLink to TOR-2:TeGig1/0/48
  mtu 9100
  logging event port link-status
  vrf member VRF08
  ip address 192.168.32.205/30
  ip policy route-map TRAFFIC_TO_PROXY_ENV1
  no shutdown
!

<Interface configuration for inside/outside connections to proxy devices. In this example there are 2 firewalls>

!
interface Ethernet1/41
  description management interface for Firewall-1
  switchport
  switchport access vlan 801
  no shutdown
!
interface Ethernet1/42
  description Proxy interface for Firewall-1
  switchport
  switchport access vlan 802
  no shutdown
!
interface Ethernet2/41
  description management interface for Firewall-2
  switchport
  switchport access vlan 801
  no shutdown
!
interface Ethernet2/42
  description Proxy interface for Firewall-2
  switchport
  switchport access vlan 802
  no shutdown
!

<BGP network statements for VLAN 801-802 subnets and neighbor statements for R023171A-TOR-1/R023171A-TOR-2> 

!
router bgp 65000
!
vrf VRF08
address-family ipv4 unicast
network 10.60.3.0/29
network 10.60.3.32/28
!
neighbor 192.168.32.194
  remote-as 65001
  description LinkTo 65001:R023171A-TOR-1:TeGig1/0/47
  address-family ipv4 unicast
    maximum-prefix 12000 warning-only
neighbor 192.168.32.206
  remote-as 65001
  description LinkTo 65001:R023171A-TOR-2:TeGig1/0/48
  address-family ipv4 unicast
    maximum-prefix 12000 warning-only
!
!
```

Créez la liste de contrôle d’accès à utiliser pour identifier le trafic qui obtiendra le traitement PBR. Il s’agit du trafic web (port HTTP 80 et port HTTPS 443) provenant des hôtes/sous-réseaux dans le rack de test qui obtient le service proxy, comme détaillé dans cet exemple. Par exemple, le nom de la liste de contrôle d’accès est **PERMITTED_TO_PROXY_ENV1**.

```
ip access-list PERMITTED_TO_PROXY_ENV1
100 permit tcp 172.21.107.0/27 any eq www <<HTTP traffic from CL04 Public Admin VIPs leaving test rack>>
110 permit tcp 172.21.107.0/27 any eq 443 <<HTTPS traffic from CL04 Public Admin VIPs leaving test rack>>
120 permit tcp 172.21.7.224/27 any eq www <<HTTP traffic from CL04 INF-pub-adm leaving test rack>>
130 permit tcp 172.21.7.224/27 any eq 443 <<HTTPS traffic from CL04 INF-pub-adm leaving test rack>>
140 permit tcp 10.60.32.160/27 any eq www <<HTTP traffic from DVM and HLH leaving test rack>>
150 permit tcp 10.60.32.160/27 any eq 443 <<HTTPS traffic from DVM and HLH leaving test rack>>
```

Le cœur de la fonctionnalité PBR est implémenté par le route-map **TRAFFIC_TO_PROXY_ENV1**. L’option **pbr-statistics** est ajoutée pour permettre l’affichage des statistiques de correspondance de stratégie afin de vérifier le nombre de paquets qui reçoivent ou non le transfert PBR. La séquence route-map 10 permet le traitement PBR du trafic correspondant au critère **PERMITTED_TO_PROXY_ENV1** de la liste de contrôle d’accès. Ce trafic est transféré aux adresses IP de tronçon suivant de `10.60.3.34` et `10.60.3.35`, qui sont les adresses IP virtuelles des appareils proxy primaire/secondaire dans notre exemple de configuration.

```
!
route-map TRAFFIC_TO_PROXY_ENV1 pbr-statistics
route-map TRAFFIC_TO_PROXY_ENV1 permit 10
  match ip address PERMITTED_TO_PROXY_ENV1
  set ip next-hop 10.60.3.34 10.60.3.35
```

Les listes de contrôle d’accès sont utilisées comme critères de correspondance pour le route-map **TRAFFIC_TO_PROXY_ENV1**. Quand le trafic correspond à la liste de contrôle d’accès **PERMITTED_TO_PROXY_ENV1**, PBR remplace la table de routage normale et transmet à la place le trafic aux adresses IP de tronçon suivant listées.
 
La stratégie PBR **TRAFFIC_TO_PROXY_ENV1** est appliquée au trafic qui entre dans l’appareil frontière des hôtes CL04 et des adresses IP virtuelles publiques, et de HLH et DVM dans le rack de test.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’intégration d’un pare-feu, consultez [Intégration d’un pare-feu à Azure Stack Hub](azure-stack-firewall.md).
