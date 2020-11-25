---
title: Présentation de la mise en réseau de Modular Data Center (MDC)
description: Apprenez-en davantage sur la mise en réseau de l’appareil Azure Modular Datacenter.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: conceptual
ms.date: 12/31/2019
ms.lastreviewed: 12/31/2019
ms.openlocfilehash: 628e1faf5a5bbfcf0bc780ae770dbdc8a8d762f2
ms.sourcegitcommit: c89d8aa6d07d7aec002b58bd07a7976203aa760b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94674572"
---
# <a name="modular-datacenter-mdc-network-introduction"></a>Présentation du réseau MDC (Modular Data Center)

## <a name="package-content"></a>Contenu de package

La solution Azure Modular Datacenter (MDC) est fournie avec un équipement réseau supplémentaire référencé dans cet article. Cet équipement est utilisé pour connecter le conteneur à votre réseau.

Confirmez que les fibres optiques du commutateur correspondent à votre environnement.

| Quantité | Type | Modèle |
|-----|---------------|-------|
| 12 | 12x QSFP-40G | « SR » |
| 12 | SFP 12x + 10 Go | « SR » |
| 12 | SFP de 12x 1G | « SR » |
| 12 | 12x QSFP-40G | « LR » |
| 12 | SFP 12x + 10 Go | « LR » |
| 12 | SFP de 12x 1G | « LR » |

## <a name="network-design-overview"></a>Vue d’ensemble de la conception réseau

### <a name="physical-network-design"></a>Conception du réseau physique

La solution MDC requiert une infrastructure physique robuste et hautement disponible pour prendre en charge ses opérations et services. Les liaisons montantes entre ToR (Top-of-Rack) et les commutateurs de bordure sont limitées à un média SFP+ ou SFP28 et une vitesse de 1 Go, 10 Go ou 40 Go.

Le diagramme suivant représente notre conception recommandée pour MDC.

![Diagramme illustrant la conception recommandée pour le réseau physique.](media/network-introduction/physical-network-design.png)

### <a name="logical-network-design"></a>Conception du réseau logique

La conception d’un réseau logique représente une abstraction de l’infrastructure réseau physique. Ils sont utilisés pour organiser et simplifier les affectations réseau pour les hôtes, les machines virtuelles et les services. Dans le cadre de la création d’un réseau logique, des sites réseau sont créés pour définir :

- les réseaux locaux virtuels (VLAN).
- les sous-réseaux IP.
- les pairs sous-réseau IP / VLAN.

Tous ces VLAN et sous-réseaux sont associés au réseau logique dans chaque emplacement physique.

Le tableau suivant montre les réseaux logiques et les plages de sous-réseau IPv4 associées que vous devez planifier.

| **Réseau logique** | **Description** | **Taille** |
|---------------------|-----------------|----------|
| Adresse IP virtuelle (VIP) publique | MDC utilise un total de 31 adresses de ce réseau. Huit adresses IP publiques sont utilisées pour un petit ensemble de services MDC et le reste est utilisé par des machines virtuelles d’abonné. Si vous envisagez d’utiliser Azure App Service et les fournisseurs de ressources SQL, sept adresses supplémentaires sont utilisées. Les 15 adresses IP restantes sont réservées pour les futurs services Azure. | / 26 (62 hôtes) - /22 (1022 hôtes) <br><Br>Recommandé = /24 (254 hôtes) |
| Infrastructure du commutateur | Adresses IP de point à point pour le routage, les interfaces de gestion de commutateur dédiées et les adresses de bouclage attribuées au commutateur. | /26 |
| Infrastructure | Utilisée pour les composants internes MDC pour communiquer. | /24 |
| Privées | Utilisé pour le réseau de stockage, les adresses IP virtuelles privées, les conteneurs d’infrastructure et d’autres fonctions internes. | /20 |
| Baseboard Management Controller (BMC) | Utilisé pour communiquer avec les contrôleurs BMC sur les hôtes physiques. | /26 |
| Isilon | Utilisé pour communiquer avec le stockage Isilon. | 1x /25 ToR 1x /25 BMC (gestion) |

### <a name="network-infrastructure"></a>Infrastructure réseau

L’infrastructure réseau pour MDC se compose de plusieurs réseaux logiques configurés sur les commutateurs. Le diagramme suivant illustre ces réseaux logiques et la façon dont ils s’intègrent avec les commutateurs Top-of-Rack (TOR), BMC et de bordure (réseau clients).

![Diagramme illustrant la conception du réseau logique.](media/network-introduction/logical-network-design.png)

#### <a name="bmc-network"></a>Réseau BMC

Ce réseau est conçu pour connecter tous les BMC (également appelés processeurs de service) au réseau de gestion, par exemple iDRAC, iLO et iBMC. Un seul compte BMC est utilisé pour communiquer avec n’importe quel nœud BMC. S’il est présent, le Hardware Lifecycle Host (HLH) se trouve sur ce réseau et peut fournir des logiciels spécifiques aux OEM à des fins de maintenance ou de surveillance du matériel.

Le HLH héberge également la machine virtuelle de déploiement (DVM). Le DVM est utilisé au cours du déploiement MDC et est supprimé lorsque le déploiement est terminé. Le DVM requiert un accès internet dans des scénarios de déploiement connecté pour tester, valider et accéder à plusieurs composants. Ces composants peuvent être à l’intérieur et en dehors de votre réseau d’entreprise. Par exemple, NTP, DNS (Domain Name System) et Azure. Pour plus d’informations sur les exigences de connectivité, consultez la section relative à la traduction d'adresses réseau (NAT) dans l’intégration du pare-feu MDC.

#### <a name="private-network"></a>Réseau privé

Le réseau /20 (adresses IP hôtes 4096) est privé pour la région MDC. Il ne s’étend pas au-delà des appareils de commutation de bordure de la région MDC. Ce réseau est divisé en plusieurs sous-réseaux, par exemple :

- **Réseau de stockage** : Réseau /25 (128 adresses IP) utilisé pour prendre en charge l’utilisation du trafic de stockage d’espaces de stockage direct et SMB (Server Message Block) ainsi que la migration dynamique de machine virtuelle.
- **Réseau IP virtuel interne** : Réseau /25 dédié aux adresses IP virtuelles internes uniquement pour l’équilibrage de charge logicielle.
- **Réseau de conteneurs** : Réseau/23 (512 adresses IP) dédié au trafic interne uniquement entre les conteneurs exécutant des services d’infrastructure.

La taille du réseau privé passe à /20 (4 096 adresses IP) d’espace d’adresses IP privées. Ce réseau est privé pour le système MDC. Il n’est pas acheminé au-delà des appareils de commutation de bordure du système MDC et peut être réutilisé sur plusieurs systèmes MDC. Ce réseau est privé pour MDC, mais il ne doit pas empiéter sur d’autres réseaux dans le centre de données. Pour obtenir des conseils sur l’espace d’adressage IP privé, nous vous recommandons de suivre le document RFC 1918.

L’espace d’adresses IP privées /20 est divisé en plusieurs réseaux qui permettent d’exécuter l’infrastructure MDC pour exécuter des conteneurs pour de futures mises en production. Pour plus d’informations, consultez les notes de publication de la version 1910. Ce nouvel espace d’adresses IP privées favorise les efforts en cours visant à réduire l’espace d’adresses IP routable nécessaire avant le déploiement.

#### <a name="mdc-infrastructure-network"></a>Réseau d'infrastructure MDC

Le réseau /24 est dédié aux composants MDC internes afin qu’ils puissent communiquer et échanger des données entre eux. Ce sous-réseau peut être routable en externe pour la solution MDC vers votre centre de données. Nous vous *déconseillons* d’utiliser des adresses IP routables publiques ou Internet sur ce sous-réseau. Ce réseau est annoncé jusqu’à la frontière, mais la plupart de ses adresses IP sont protégées par des listes de contrôle d’accès. Les adresses IP autorisées pour l’accès sont comprises dans une plage de petite taille, équivalant à un réseau /27. Les services hôtes des adresses IP comme le point de terminaison privilégié (PEP) et la sauvegarde MDC.

#### <a name="public-vip-network"></a>Réseau d’adresse IP virtuelle publique

Le réseau d’adresses IP virtuelles publiques est affecté au contrôleur réseau dans MDC. Il ne s’agit pas d’un réseau logique sur le commutateur. Le SLB utilise le pool d’adresses et assigne des réseaux /32 pour les charges de travail clientes. Dans la table de routage du commutateur, ces adresses IP /32 sont publiées en tant qu’itinéraire disponible via BGP (Border Gateway Protocol). Ce réseau contient des adresses publiques qui sont accessibles en externe. L’infrastructure MDC réserve les 31 premières adresses de ce réseau d’adresses IP virtuelles publiques tandis que les autres sont utilisées par les machines virtuelles clientes. La taille réseau sur ce sous-réseau peut aller d’un minimum de /26 (64 hôtes) à un maximum de /22 (1 022 hôtes). Nous vous recommandons de prévoir pour un réseau /24.

#### <a name="switch-infrastructure-network"></a>Réseau d’infrastructure du commutateur

Le réseau /26 est le sous-réseau qui contient les sous-réseaux d’adresses IP /30 point-à-point routables, ainsi que les bouclages. Il s’agit de sous-réseaux /32 dédiés pour la gestion du commutateur intrabande et l’ID de routeur BGP. Cette plage d’adresses IP doit être routable en dehors de la solution MDC pour votre centre de données. Il peut s’agit d’adresses IP privées ou publiques.

#### <a name="switch-management-network"></a>Réseau de gestion du commutateur

Le réseau /29 (6 adresses IP hôtes) est dédié à la connexion des ports de gestion des commutateurs. Il autorise un accès hors bande pour le déploiement, la gestion et la résolution des problèmes. Il est calculé à partir du réseau d’infrastructure du commutateur mentionné dans la section précédente.

#### <a name="isilon-network"></a>Réseau Isilon

Il existe deux réseaux /25. L’un réside sur le commutateur ToR et l’autre /25 est utilisé sur le commutateur BMC à des fins de gestion.

## <a name="dns-design-overview"></a>Vue d’ensemble de la conception DNS

Pour accéder aux points de terminaison MDC (*portail*, *adminportal*, *management* et *adminmanagement*, etc.) en dehors de MDC, vous devez intégrer les services DNS MDC aux serveurs DNS qui hébergent les zones DNS que vous souhaitez utiliser dans MDC.

### <a name="mdc-dns-namespace"></a>Espace de noms DNS MDC

Vous devez fournir certaines informations importantes relatives à DNS quand vous déployez la solution MDC.

| **Champ** | **Description** | **Exemple** |
|-----------|-----------------|-------------|
| Région | Emplacement géographique de votre déploiement MDC. | *est* |
| Nom de domaine externe | Nom de la zone à utiliser pour votre déploiement MDC. | *cloud.fabrikam.com* |
| Nom de domaine interne | Nom de la zone interne utilisée pour les services d’infrastructure dans le MDC. Elle est intégrée au service d’annuaire et privée (inaccessible en dehors du déploiement MDC). | *azurestack.local* |
| Redirecteurs DNS | Serveurs DNS utilisés pour transférer des requêtes DNS, des zones DNS et des enregistrements hébergés en dehors de MDC, sur l’intranet d’entreprise ou sur l’Internet public. Vous pouvez modifier la valeur de Redirecteur DNS avec la cmdlet **Set-AzSDnsForwarder** après le déploiement. | |
| Préfixe de nommage (facultatif) | Préfixe de nommage dont vous souhaitez doter les noms de machine d’instance de rôle de l’infrastructure MDC. S’il n’est pas fourni, la valeur par défaut est *azs*. | *azs* |

Le nom de domaine complet (FQDN) de votre déploiement MDC et des points de terminaison est la combinaison du paramètre de la région et du paramètre du nom de domaine externe. En utilisant les valeurs des exemples du tableau précédent, le nom de domaine complet pour ce déploiement MDC serait le suivant : *east.cloud.fabrikam.com*.

Certains des points de terminaison pour ce déploiement ressembleraient, par exemple, aux URL suivantes :

- `https://portal.east.cloud.fabrikam.com`
- `https://adminportal.east.cloud.fabrikam.com`

Pour utiliser cet exemple d’espace de noms DNS dans le cadre d’un déploiement MDC, les conditions suivantes sont requises :

- La zone fabrikam.com est inscrite auprès d’un bureau d’enregistrement de domaines, d’un serveur DNS d’entreprise interne ou des deux. L’inscription dépend de vos exigences en matière de résolution de noms.
- Le domaine enfant cloud.fabrikam.com existe sous la zone fabrikam.com.
- Les serveurs DNS qui hébergent les zones fabrikam.com et cloud.fabrikam.com peuvent être atteints à partir du déploiement MDC.

Pour résoudre les noms DNS des points de terminaison et des instances MDC en dehors de MDC, vous devez intégrer les serveurs DNS. Les serveurs qui hébergent la zone DNS externe pour MDC sont inclus avec les serveurs DNS qui hébergent la zone parente que vous souhaitez utiliser.

#### <a name="dns-name-labels"></a>Étiquettes de nom DNS

MDC prend en charge l’ajout d’une étiquette de nom DNS à une adresse IP publique pour permettre la résolution de noms pour les adresses IP publiques. Les étiquettes DNS permettent aux utilisateurs d’accéder aux applications et services hébergés dans MDC par nom. L’étiquette de nom DNS utilise un espace de noms légèrement différent que les points de terminaison d’infrastructure. Suivant l’exemple d’espace de noms précédent, l’espace de noms pour les étiquettes de nom DNS s’affiche comme suit : *\*.east.cloudapp.cloud.fabrikam.com*.

Si un abonné indique une valeur **MyApp** dans le champ d’étiquette de nom DNS d’une ressource d’adresse IP publique, il crée un enregistrement A pour myapp dans la zone *east.cloudapp.cloud.fabrikam.com* sur le serveur DNS externe MDC. Le FQDN qui en résulte est le suivant : *myapp.east.cloudapp.cloud.fabrikam.com*.

Si vous souhaitez tirer parti de cette fonctionnalité et utiliser cet espace de noms, vous devez intégrer les serveurs DNS. Les serveurs qui hébergent la zone DNS externe pour MDC sont inclus avec les serveurs DNS qui hébergent la zone parente que vous souhaitez utiliser. S’agissant d’un espace de noms autre que celui pour les points de terminaison de service MDC, vous devez créer une délégation supplémentaire ou une règle de transfert conditionnelle.

Pour plus d’informations sur le fonctionnement de l’étiquette de nom DNS, consultez « Utilisation de DNS » dans la documentation MDC.

### <a name="resolution-and-delegation"></a>Résolution et délégation

Deux types de serveur DNS sont disponibles :

- Un serveur DNS faisant autorité héberge les zones DNS. Il répond aux requêtes DNS pour les enregistrements de ces zones uniquement.
- Un serveur DNS récursif n’héberge pas de zones DNS. Il répond à toutes les requêtes DNS, en appelant des serveurs DNS faisant autorité pour rassembler les données dont il a besoin.

MDC comprend à la fois des serveurs DNS faisant autorité et des serveurs DNS récursifs. Les serveurs récursifs sont utilisés pour résoudre les noms de tous les éléments à l’exception de la zone privée interne et de la zone DNS publique externe pour ce déploiement MDC.

### <a name="resolve-external-dns-names-from-mdc"></a>Résoudre des noms DNS externes à partir de MDC

Afin de résoudre les noms DNS pour les points de terminaison en dehors de MDC (par exemple : www.bing.com), vous devez fournir des serveurs DNS que MDC peut utiliser pour transférer les requêtes DNS pour lesquelles MDC ne fait pas autorité. Les serveurs DNS vers lesquels MDC transfère les requêtes sont requis dans la feuille de calcul de déploiement (dans le champ du redirecteur DNS). Indiquez au moins deux serveurs dans ce champ à des fins de tolérance de panne. En l’absence de ces valeurs, le déploiement MDC échoue. Vous pouvez modifier les valeurs de Redirecteur DNS avec la cmdlet **Set-AzSDnsForwarder** après le déploiement.

## <a name="firewall-design-overview"></a>Vue d’ensemble de conception du pare-feu

Nous vous recommandons d’utiliser un dispositif de pare-feu pour sécuriser MDC. Les pare-feu peuvent contribuer à la défense contre les attaques par déni de service distribué (DDoS), à la détection des intrusions et à l'inspection de contenu. Ils peuvent également se transformer en goulot d'étranglement pour les services de stockage Azure (objets blobs, tables, files d'attente, etc.).

Si un mode de déploiement déconnecté est utilisé, vous devez publier le point de terminaison des services de fédération Active Directory (AD FS). Pour plus d’informations, consultez l’article sur l’identité de l’intégration au centre de données.

Les points de terminaison Azure Resource Manager (administrateur), portail administrateur et Azure Key Vault (administrateur) ne nécessitent aucune publication externe. Par exemple, en tant que fournisseur de services, vous pouvez limiter la surface d'attaque en vous contentant d'administrer MDC depuis votre réseau et non à partir d'Internet.

Pour les entreprises, le réseau externe peut être le réseau d’entreprise existant. Dans ce scénario, vous devez publier les points de terminaison pour exécuter MDC à partir du réseau d'entreprise.

### <a name="network-address-translation"></a>Traduction d’adresses réseau

Nous vous recommandons d’utiliser la méthode NAT pour permettre au DVM d’accéder aux ressources externes lors du déploiement. Nous vous recommandons également NAT pour les machines virtuelles de la console de récupération d’urgence (ERCS) ou PEP au cours de l’inscription et de la résolution des problèmes.

Vous pouvez également utiliser NAT à la place des adresses IP publiques sur le réseau externe ou des adresses IP virtuelles publiques. Nous déconseillons cette option car NAT limite l’expérience utilisateur du locataire et accroît la complexité. Une option serait NAT 1:1, qui nécessite toujours une adresse IP publique par adresse IP d'utilisateur sur le pool. Une autre option serait NAT plusieurs:1, qui nécessite une règle NAT par adresse IP virtuelle d'utilisateur pour tous les ports qu'un utilisateur pourrait utiliser.

L’utilisation de NAT pour une adresse IP virtuelle publique présente des inconvénients. En voici quelques-uns :

- Surcharge lors de la gestion des règles de pare-feu car les utilisateurs contrôlent leurs propres points de terminaison et leurs propres règles de publication dans la pile de mise en réseau logicielle. Les utilisateurs doivent contacter l’opérateur MDC pour que leurs adresses IP virtuelles soient publiées et pour mettre à jour la liste des ports.
- Bien que l’utilisation de NAT limite l’expérience de l’utilisateur, elle donne à l’opérateur un contrôle total sur les demandes de publication.
- Pour les scénarios de cloud hybride avec Azure, tenez compte du fait qu’Azure ne prend pas en charge la configuration d’un tunnel VPN vers un point de terminaison à l’aide de NAT.

### <a name="ssl-interception"></a>Interception SSL

Actuellement, nous vous recommandons de désactiver toute interception SSL (par exemple le déchargement du déchiffrement) sur tout le trafic MDC. Si l’interception est prise en charge dans les futures mises à jour, nous fournissons des conseils sur la façon de l’activer pour MDC.

### <a name="edge-deployment-firewall-scenario"></a>Scénario de pare-feu de déploiement de périphérie

Dans un déploiement de périphérie, MDC est déployé directement derrière le pare-feu ou le routeur de périphérie. Dans ces scénarios, il est pris en charge pour que le pare-feu se trouve au-dessus de la bordure (scénario 1) où il prend en charge les configurations de pare-feu actif/actif et actif/passif. Il peut également agir en tant que périphérique de bordure (scénario 2), où il prend uniquement en charge la configuration du pare-feu actif/actif. Le scénario 2 s’appuie sur plusieurs chemins d’accès de coût égal avec le routage BGP ou statique pour le basculement.

Les adresses IP routables publiques sont spécifiées pour le pool d'adresses IP virtuelles publiques à partir du réseau externe au moment du déploiement. Dans un scénario de périphérie, les adresses IP routables ne sont *pas* recommandées sur un autre réseau pour des raisons de sécurité. Ce scénario permet à un utilisateur de bénéficier d’une expérience cloud auto-contrôlée complète, comme dans un cloud public tel qu’Azure.

 ![Diagramme illustrant les scénarios de pare-feu de périmètre MDC.](media/network-introduction/edge-firewall-scenario-60.png) 

### <a name="enterprise-intranet-or-perimeter-network-firewall-scenario"></a>Scénario de pare-feu réseau de périmètre ou intranet d’entreprise

Dans un déploiement sur un réseau de périmètre ou intranet d’entreprise, MDC est déployé sur un pare-feu multizone ou entre le pare-feu de périmètre et le pare-feu de réseau d’entreprise interne. Le trafic est ensuite distribué entre le réseau de périmètre sécurisé (ou DMZ) et les zones non sécurisées, comme décrit :

- **Zone sécurisée** : Le réseau interne qui utilise des adresses IP routables de l’entreprise ou internes. Le réseau sécurisé peut être divisé. Il peut disposer d’un accès sortant à Internet via le pare-feu NAT. Il est normalement accessible à partir de votre centre de données via le réseau interne. Tous les réseaux MDC doivent résider dans la zone sécurisée, à l’exception du pool d’adresses IP virtuelles publiques du réseau externe.
- **Zone du périmètre** : Les applications externes ou accessibles sur Internet comme les serveurs web sont généralement déployées sur le réseau de périmètre. Celui-ci est normalement analysé par un pare-feu pour éviter les attaques de type DDoS et les intrusions (piratage), mais il autorise également le trafic entrant spécifié à partir d’internet. Seul le pool d’adresses IP virtuelles publiques de réseau externe MDC doit résider dans la zone DMZ.
- **Zone non sécurisée** : Il s’agit du réseau externe, l’Internet. Nous vous *déconseillons* de déployer MDC dans la zone non sécurisée.

![Diagramme illustrant un scénario de pare-feu de réseau de périmètre.](media/network-introduction/perimeter-network-firewall-scenario-50.png)

## <a name="vpn-design-overview"></a>Vue d’ensemble de la conception VPN

Bien que le VPN soit un concept d’utilisateur, il existe des considérations importantes qu’un propriétaire de solution et un opérateur doivent connaître.

Avant de pouvoir envoyer du trafic réseau entre votre réseau virtuel Azure et votre site local, vous devez créer une passerelle de réseau virtuel pour votre réseau virtuel.

Une passerelle VPN est un type de passerelle de réseau virtuel qui envoie le trafic chiffré à travers une connexion publique. Vous pouvez utiliser des passerelles VPN pour envoyer en toute sécurité le trafic entre un réseau virtuel dans MDC et un réseau virtuel dans Azure. Vous pouvez également envoyer en toute sécurité le trafic entre un réseau virtuel et un autre réseau connecté à un périphérique VPN.

Lorsque vous créez une passerelle de réseau virtuel, vous spécifiez le type de passerelle que vous voulez créer. MDC prend en charge un seul type de passerelle réseau virtuel : le type Vpn.

Chaque réseau virtuel peut avoir deux passerelles de réseau virtuel, mais une seule de chaque type. Selon les paramètres que vous choisissez, vous pouvez créer plusieurs connexions à une passerelle VPN unique. Les connexions sur plusieurs sites constituent un bon exemple.

Avant de créer et de configurer des passerelles VPN pour MDC, consultez les considérations relatives à la mise en réseau MDC. Vous y découvrirez comment les configurations pour MDC sont diffèrentes d’Azure.

Dans Azure, le débit de la bande passante pour la référence SKU de passerelle VPN que vous choisissez doit être réparti entre toutes les connexions connectées à la passerelle. Toutefois, dans MDC, la valeur de la bande passante pour la référence SKU de la passerelle VPN est appliquée à chaque ressource de connexion connectée à la passerelle. Par exemple :

- Dans Azure, la référence SKU de passerelle VPN de base peut prendre en charge environ 100 Mbits/s de débit agrégé. Si vous créez deux connexions pour cette passerelle VPN, dont une connexion avec une bande passante de 50 Mbits/s, 50 Mbits/s sont disponibles pour l’autre connexion.
- Dans MDC, un débit de 100 Mbits/s est alloué à chaque connexion à la référence SKU de passerelle VPN de base.

### <a name="vpn-types"></a>Types de VPN

Lorsque vous créez la passerelle de réseau virtuel d’une configuration de passerelle VPN, vous devez spécifier un type de VPN. Le type de VPN que vous choisissez dépend de la topologie de connexion que vous souhaitez créer. Un type VPN peut également dépendre du matériel utilisé. Les configurations site à site requièrent un périphérique VPN. Certains périphériques VPN seront ne prennent en charge qu’un certain type de VPN.

>[!IMPORTANT]
> Pour l’instant, MDC prend uniquement en charge le type de VPN basé sur le routage. Si votre appareil ne gère que les VPN basés sur les stratégies, les connexions à ces appareils à partir de MDC ne sont pas prises en charge. En outre, pour le moment, Azure Stack ne gère pas l’utilisation des sélecteurs de trafic reposant sur les stratégies pour les passerelles basées sur le routage, car les configurations de stratégies IPSec/IKE personnalisées ne sont pas prises en charge.

- **PolicyBased** : Les VPN basés sur des stratégies chiffrent et acheminent des paquets via les tunnels IPsec, en fonction des stratégies IPsec. Les stratégies sont configurées avec les combinaisons de préfixes d’adresses entre votre réseau local et le réseau virtuel MDC. La stratégie ou le sélecteur de trafic prennent généralement la forme d’une liste d’accès dans la configuration de périphérique VPN. **PolicyBased** est pris en charge dans Azure, mais non dans MDC.
- **RouteBased** : Les VPN basés sur l’itinéraire utilisent des itinéraires configurés dans le transfert IP ou la table de routage. Les itinéraires acheminent des paquets vers les interfaces de tunnel correspondantes. Les interfaces de tunnel chiffrent ou déchiffrent ensuite les paquets se trouvant dans et hors des tunnels. La stratégie, ou sélecteur de trafic, des VPN **RouteBased** est configurée comme universelle (ou utilise des caractères génériques). Par défaut, ils ne sont pas modifiables. Le VPN **RouteBased** a pour valeur **RouteBased**.

### <a name="configure-a-vpn-gateway"></a>Configurer une passerelle VPN

Une connexion par passerelle VPN s’appuie sur plusieurs ressources qui sont configurées avec des paramètres spécifiques. La plupart des ressources peuvent être configurées séparément, mais elles doivent être configurées dans un certain ordre dans certains cas.

#### <a name="settings"></a>Paramètres

Les paramètres que vous choisissez pour chaque ressource sont essentiels à la création d’une connexion réussie.

Cet article vous aide à comprendre :

- Les types de passerelle, les types de VPN et les types de connexion.
- Les sous-réseaux de passerelle, les passerelles de réseau local et les autres paramètres de ressource que vous souhaiterez peut-être prendre en compte.

#### <a name="connection-topology-diagrams"></a>Diagrammes de topologie de connexion

Il existe différentes configurations disponibles pour les connexions aux passerelles VPN. Déterminez la configuration qui correspond le mieux à vos besoins. Dans les sections ci-dessous, vous pouvez afficher des informations et des diagrammes de topologie sur les connexions de passerelle VPN suivantes :

- Modèle de déploiement disponible
- Outils de configuration disponibles
- Liens vous redirigeant directement vers un article, si disponibles

Les graphiques et les descriptions dans les sections suivantes peuvent vous aider à sélectionner une topologie de connexion répondant à vos besoins. Le graphiques présentent les principales topologies de base, mais il est possible de créer des configurations plus complexes à l’aide des diagrammes.

#### <a name="site-to-site-and-multisite-ipsecike-vpn-tunnel"></a>Site à site et multi-sites (tunnel VPN IPsec/IKE)

##### <a name="site-to-site"></a>De site à site

Une connexion par passerelle VPN site à site (IKEv2) est une connexion sur un tunnel VPN IPsec/IKE (IKEv2). Ce type de connexion requiert un appareil VPN local disposant d’une adresse IP publique. L’appareil VPN ne peut pas se trouver derrière un traducteur d'adresses réseau. Les connexions site à site peuvent être utilisées pour les configurations hybrides et entre les différents locaux.

##### <a name="multisite"></a>Multisite

Une connexion multi-site est une variante de la connexion site à site. Vous créez plusieurs connexions VPN à partir de votre passerelle de réseau virtuel et vous vous connectez généralement à plusieurs sites locaux. Lorsque vous travaillez avec plusieurs connexions, vous devez utiliser un type de VPN basé sur la route (équivalent d’une passerelle dynamique pour les réseaux virtuels classiques). Chaque réseau virtuel ne pouvant disposer que d’une seule passerelle de réseau virtuel, toutes les connexions passant par la passerelle partagent la bande passante disponible.

### <a name="gateway-skus"></a>Références SKU de passerelle

Lorsque vous créez une passerelle de réseau virtuel pour MDC, vous spécifiez la référence SKU de passerelle que vous voulez utiliser. Les références SKU de passerelle VPN suivantes sont prises en charge :

- De base
- Standard
- Hautes performances

La sélection d’une référence SKU de passerelle supérieure alloue plus de processeurs et de bande passante réseau à la passerelle. Par conséquent, la passerelle peut prendre en charge un débit réseau plus élevé sur le réseau virtuel.

MDC ne prend pas en charge la référence SKU de passerelle Ultra Performance, utilisée exclusivement avec Azure ExpressRoute. Prenez en compte les points suivants lorsque vous sélectionnez la référence SKU :

- MDC ne prend pas en charge les passerelles basées sur les stratégies.
- Le protocole BGP n’est pas pris en charge pour la référence SKU De base.
- Les configurations de passerelles VPN et ExpressRoute coexistantes ne sont pas prises en charge dans MDC.

#### <a name="gateway-availability"></a>Disponibilité de la passerelle

Les scénarios haute disponibilité ne peuvent être configurés que sur la référence SKU de la connexion de la passerelle hautes performances. Contrairement à Azure, qui offre la disponibilité grâce à des configurations actif/actif et actif/passif, MDC prend uniquement en charge la configuration actif/passif.

#### <a name="failover"></a>Basculement

Il existe trois machines virtuelles d’infrastructure de passerelle mutualisée dans MDC. Deux de ces machines virtuelles sont en mode actif. La troisième machine virtuelle est en mode redondant. Les machines virtuelles actives permettent d’établir des connexions VPN. La machine virtuelle redondante accepte uniquement les connexions VPN en cas de basculement. Si une machine virtuelle de passerelle active n’est plus disponible, la connexion VPN bascule vers la machine virtuelle redondante quelques instants (quelques secondes) après la perte de la connexion.

### <a name="estimated-aggregate-throughput-by-sku"></a>Débit agrégé estimé par SKU

Le tableau suivant présente les types de passerelle et le débit total estimé par référence de passerelle.

| Type de passerelle | Débit de passerelle VPN (1) | Tunnels IPsec max de passerelle VPN (2) |
|--------------|----------------------------|---------------------------------------|
| Référence SKU De base (3) | 100 Mbits/s | 20 |
| Référence SKU standard | 100 Mbits/s | 20 |
| Référence hautes performances | 200 Mbits/s | 10 |

Notes de la table :

(1) Le débit du VPN n’est pas garanti pour les connexions intersites via Internet. Il s’agit de la mesure du débit maximal possible.

(2) Le nombre maximal de tunnels est le total par déploiement MDC pour tous les abonnements.

(3) Le routage BGP n’est pas pris en charge pour la référence SKU de base.

>[!IMPORTANT]
> Vous ne pouvez créer qu’une seule connexion VPN de site à site entre deux déploiements MDC. Cette restriction est liée au fait que la plateforme n’autorise qu’une seule connexion VPN à la même adresse IP. Dans la mesure où MDC s’appuie sur la passerelle mutualisée qui utilise une seule adresse IP publique pour toutes les passerelles VPN dans le système MDC, il ne peut y avoir qu’une seule connexion VPN entre deux systèmes MDC.
>
> Cette limitation s’applique également à la connexion de plusieurs connexions VPN de site à site à une passerelle VPN qui utilise une seule adresse IP. MDC n’autorise pas la création de plusieurs ressources de passerelle de réseau local à l’aide de la même adresse IP.

### <a name="ipsecike-parameters"></a>Paramètres IPsec/IKE

Lorsque vous configurez une connexion VPN dans MDC, vous devez configurer la connexion à chaque extrémité. Si vous configurez une connexion VPN entre MDC et un périphérique matériel, ce périphérique peut vous demander des paramètres supplémentaires. Par exemple, le périphérique peut demander un commutateur ou un routeur agissant comme passerelle VPN.

Contrairement à Azure, qui prend en charge plusieurs offres en tant qu’initiateur et répondeur, MDC ne prend en charge qu’une seule offre par défaut. Si vous devez utiliser différents paramètres IPsec/IKE pour travailler avec votre appareil VPN, d’autres paramètres sont disponibles pour vous permettre de configurer votre connexion manuellement.

#### <a name="ike-phase-1-main-mode-parameters"></a>Paramètres IKE Phase 1 (Mode principal)

| **Propriété** | **Valeur** |
|--------------|-----------|
| Version IKE | IKEv2 |
| Groupe Diffie-Hellman | ECP384 |
| Méthode d'authentification | Clé prépartagée |
| Chiffrement et algorithmes de hachage | AES256, SHA384 |
| Durée de vie de l’AS (durée) | 28 800 secondes |

#### <a name="ike-phase-2-quick-mode-parameters"></a>Paramètres IKE Phase 2 (Mode rapide)

| **Propriété** | **Valeur** |
|--------------|-----------|
| Version IKE | IKEv2 |
| Chiffrement et algorithmes de hachage (Chiffrement) | GCMAES256 |
| Chiffrement et algorithmes de hachage (Authentification) | GCMAES256 |
| Durée de vie de l’AS (durée) | 27 000 secondes |
| Durée de vie de l’AS (kilo-octets) | 33 553 408 |
| PFS (Perfect Forward Secrecy) | ECP384 |
| Détection d’homologue mort | Prise en charge |

### <a name="configure-custom-ipsecike-connection-policies"></a>Configurer des stratégies de connexion IPsec/IKE personnalisées

La norme de protocole IPsec et IKE prend en charge un large éventail d’algorithmes de chiffrement dans différentes combinaisons. Pour voir quels sont les paramètres pris en charge dans MDC afin de répondre aux exigences de conformité ou de sécurité, consultez les paramètres IPsec/IKE.

Cet article fournit des instructions sur la création et la configuration d’une stratégie IPsec/IKE ainsi que sur son application à une connexion nouvelle ou existante.

#### <a name="considerations"></a>Considérations

Notez les points importants suivants lorsque vous utilisez stratégies :

- La stratégie IPsec/IKE fonctionne uniquement sur les références SKU de passerelle Standard et Hautes performances (Basé sur itinéraires).
- Vous ne pouvez spécifier qu’une seule combinaison de stratégies pour une connexion donnée.
- Vous devez spécifier tous les algorithmes et paramètres pour IKE (mode principal) et IPsec (mode rapide). Vous n’êtes pas en droit de spécifier de stratégie partielle.
- Consulter les spécifications de votre fournisseur de périphérique VPN pour vous assurer que la stratégie est prise en charge sur vos périphériques VPN locaux. Les connexions site à site ne peuvent pas être établies si les stratégies sont incompatibles.

#### <a name="workflow-to-create-and-set-ipsecike-policy"></a>Workflow de création et de définition d’une stratégie IPsec/IKE

Cette section décrit le workflow nécessaire pour créer et mettre à jour la stratégie IPsec/IKE sur une connexion VPN site à site.

1. Créer un réseau virtuel et une passerelle VPN
1. Créer une passerelle de réseau local pour la connexion entre différents locaux
1. Créer une stratégie IPsec/IKE avec les algorithmes et les paramètres sélectionnés
1. Créer une connexion IPsec avec la stratégie IPsec/IKE
1. Ajouter, mettre à jour ou supprimer une stratégie IPsec/IKE pour une connexion existante

#### <a name="supported-cryptographic-algorithms-and-key-strengths"></a>Algorithmes de chiffrement pris en charge et forces des clés

Le tableau suivant répertorie les algorithmes de chiffrement et les puissances de clé pris en charge et configurables par les clients MDC.

| **IPsec/IKEv2** | **Options** |
|-----------------|-------------|
| Chiffrement IKEv2 | AES256, AES192, AES128, DES3, DES |
| Intégrité IKEv2 | SHA384, SHA256, SHA1, MD5 |
| Groupe DH | ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, aucun |
| Chiffrement IPsec | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, Aucun |
| Intégrité IPsec | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5 |
| Groupe PFS | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, Aucun |
| Durée de vie de l’AS en mode rapide | (Facultatif : les valeurs par défaut sont utilisées si rien n’est spécifié) |
| | Secondes (entier, min. 300 / 27 000 secondes par défaut) |
| | Ko (entier, min. 1024 / 102 400 000 Ko par défaut) |
| Sélecteur de trafic | Les sélecteurs de trafic basés sur des stratégies ne sont pas pris en charge dans MDC. |

La configuration de votre périphérique VPN local doit correspondre aux algorithmes et paramètres suivants, spécifiés dans la stratégie IPsec/IKE Azure ou les contenir :

- Algorithme de chiffrement IKE (Mode principal/Phase 1).
- Algorithme d’intégrité IKE (Mode principal/Phase 1).
- Groupe DH (Mode principal/Phase 1).
- Algorithme de chiffrement IPsec (Mode rapide/Phase 2).
- Algorithme d’intégrité IPsec (Mode rapide/Phase 2).
- Groupe PFS (Mode rapide/Phase 2).
- Les durées de vie de l’AS sont uniquement des spécifications locales. Elles ne doivent pas correspondre.

Si GCMAES est utilisé pour l’algorithme de chiffrement IPsec, vous devez sélectionner le même algorithme GCMAES et la même longueur de clé pour l’intégrité IPsec. Par exemple, utilisez GCMAES128 pour les deux.

Dans le tableau précédent :

- IKEv2 correspond au Mode principal ou à la Phase 1.
- IPsec correspond au Mode rapide ou à la Phase 2.
- Groupe DH spécifie le groupe Diffie-Hellman utilisé dans le Mode principal ou à la Phase 1.
- Groupe PFS spécifie le groupe Diffie-Hellman utilisé dans le Mode rapide ou à la Phase 2.
- La durée de vie de l’AS en mode principal IKEv2 est fixée à 28 800 secondes pour les passerelles VPN MDC.

Le tableau suivant répertorie les groupes Diffie-Hellman correspondants pris en charge par la stratégie personnalisée.

| **Groupe Diffie-Hellman** | **DHGroup** | **PFSGroup** | **Longueur de clé** |
|--------------------------|-------------|--------------|----------------|
| 1 | DHGroup1 | PFS1 | MODP 768 bits |
| 2 | DHGroup2 | PFS2 | MODP 1 024 bits |
| 14 | DHGroup14 | PFS2048 | MODP 2 048 bits |
| | DHGroup2048 | | |
| 19 | ECP256 | ECP256 | ECP 256 bits |
| 20 | ECP384 | ECP384 | ECP 384 bits |
| 24 | DHGroup24 | PFS24 | MODP 2 048 bits |

### <a name="connect-mdc-to-azure-by-using-azure-expressroute"></a>Connecter MDC à Azure à l’aide d’Azure ExpressRoute

#### <a name="overview-assumptions-and-prerequisites"></a>Vue d’ensemble, hypothèses et conditions préalables

Azure ExpressRoute vous permet d’étendre vos réseaux locaux dans le cloud Microsoft. Vous utilisez une connexion privée fournie par un fournisseur de connectivité. ExpressRoute n’est pas une connexion VPN établie via le réseau Internet public.

Pour en savoir plus sur ExpressRoute, consultez la rubrique Présentation d’ExpressRoute.

#### <a name="assumptions"></a>Hypothèses

Cet article suppose d’avoir :

- Une connaissance pratique d’Azure.
- Une connaissance élémentaire de MDC.
- Une connaissance élémentaire de la mise en réseau.

#### <a name="prerequisites"></a>Prérequis

Pour connecter MDC et Azure à l’aide d’ExpressRoute, vous devez disposer de ce qui suit :

- Un circuit ExpressRoute approvisionné via un fournisseur de connectivité.
- Un abonnement Azure pour créer un circuit ExpressRoute et des réseaux virtuels dans Azure.
- Un routeur qui doit :
  - prendre en charge les connexions VPN de site à site entre son interface LAN et la passerelle mutualisée Azure Stack.
  - être en mesure de créer plusieurs VRF (Virtual Routing and Forwarding) en présence de plusieurs locataires dans votre déploiement MDC.
- Un routeur qui possède :
  - Un port WAN connecté au circuit ExpressRoute.
  - Un port réseau local connecté à la passerelle mutualisée MDC.

#### <a name="expressroute-network-architecture"></a>Architecture réseau ExpressRoute

Le diagramme suivant illustre les environnements MDC et Azure une fois que vous avez configuré ExpressRoute à l’aide des exemples de cet article.

 ![Diagramme illustrant l’architecture réseau ExpressRoute.](media/network-introduction/expressroute-network-architecture-60.png)

