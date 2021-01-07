---
title: Présentation de la gestion de réseau Azure Stack Hub renforcé.
description: Découvrez la gestion de réseau pour l’appareil Azure Stack Hub renforcé.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: conceptual
ms.date: 10/14/2020
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: 3a6312a81632bffbb128049088119b30de79a732
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97867799"
---
# <a name="azure-stack-hub-ruggedized-network-introduction"></a>Présentation du réseau Azure Stack Hub renforcé

## <a name="network-design-overview"></a>Vue d’ensemble de la conception réseau

### <a name="physical-network-design"></a>Conception du réseau physique

La solution Azure Stack Hub renforcé nécessite une infrastructure physique résiliente et hautement disponible pour permettre la prise en charge de son fonctionnement et de ses services. Les liaisons montantes entre ToR et les commutateurs de bordure sont limitées à un média SFP + ou SFP28 et une vitesse de 1 Go, 10 Go ou 25 Go. Vérifiez la disponibilité auprès de votre fournisseur de matériel OEM.

Le diagramme suivant présente notre conception recommandée pour Azure Stack Hub renforcé.

 ![Réseau physique Azure Stack Hub renforcé](media/network-introduction/physical-network-design-basic.png) 

### <a name="logical-network-design"></a>Conception du réseau logique

La conception d’un réseau logique représente une abstraction de l’infrastructure réseau physique. Ils sont utilisés pour organiser et simplifier les affectations réseau pour les hôtes, les machines virtuelles et les services. Dans le cadre de la création d’un réseau logique, des sites réseau sont créés pour définir :
- les réseaux locaux virtuels (VLAN)
- les sous-réseaux IP
- les pairs sous-réseau IP / VLAN

Tous sont associés au réseau logique dans chaque emplacement physique.

Le tableau suivant montre les réseaux logiques et les plages de sous-réseau IPv4 associées à prendre en compte dans le cadre de la planification :

| **Réseau logique**   | **Description**  | **Taille** |
|-----------------------|------------------|----------|
| Adresse IP virtuelle (VIP) publique       | Azure Stack Hub renforcé utilise un total de 31 adresses de ce réseau. Huit adresses IP publiques sont utilisées pour un petit ensemble de services Azure Stack Hub renforcé. Les adresses restantes sont utilisées par des machines virtuelles de locataires. Si vous envisagez d’utiliser App Service et les fournisseurs de ressources SQL, 7 adresses supplémentaires sont utilisées. Les 15 adresses IP restantes sont réservées pour les futurs services Azure. | /26 (62 hôtes)-<br>/22 (1 022 hôtes)<br><br>Recommandé = /24 (254 hôtes) |
| Infrastructure du commutateur | Adresses IP de point à point pour le routage, les interfaces de gestion de commutateur dédiées et les adresses de bouclage attribuées au commutateur. | /26 |
| Infrastructure        | Utilisé pour la communication des composants internes d’Azure Stack Hub renforcé. | /24 |
| Privées               | Utilisé pour le réseau de stockage, les adresses IP virtuelles privées, les conteneurs d’infrastructure et d’autres fonctions internes. | /20 |
| Baseboard Management Controller (BMC) | Utilisé pour communiquer avec les contrôleurs de gestion de la carte de base sur les hôtes physiques. | /26 |

### <a name="network-infrastructure"></a>Infrastructure réseau

L’infrastructure réseau d’Azure Stack Hub renforcé se compose de plusieurs réseaux logiques configurés sur les commutateurs. Le diagramme suivant illustre ces réseaux logiques et la façon dont ils s’intègrent au Top-of-Rack (ToR), au contrôleur de gestion de la carte de base (BMC) et aux commutateurs de bordure (réseau clients).

Diagramme du réseau logique d’Azure Stack Hub renforcé :

 ![Réseau logique d’Azure Stack Hub renforcé](media/network-introduction/logical-network-design.png) 

#### <a name="bmc-network"></a>Réseau BMC

Ce réseau est conçu pour connecter tous les contrôleurs de gestion de carte de base (également appelés BMC ou processeurs de service) au réseau de gestion, par exemple iDRAC, iLO, iBMC, etc. Un seul compte BMC est utilisé pour communiquer avec n’importe quel nœud BMC. S’il est présent, le Hardware Lifecycle Host (HLH) se trouve sur ce réseau et peut fournir des logiciels spécifiques aux OEM pour la maintenance ou l’analyse du matériel.

Le HLH héberge également le déploiement de machines virtuelles (DVM). Le DVM est utilisé durant le déploiement d’Azure Stack Hub renforcé, puis est supprimé une fois le déploiement effectué. Le DVM requiert un accès internet dans des scénarios de déploiement connecté pour tester, valider et accéder à plusieurs composants. Ces composants peuvent être à l’intérieur et en dehors de votre réseau d’entreprise ; par exemple : NTP, DNS et Azure). Pour plus de détails sur les impératifs de connectivité, consultez la section NAT dans les informations relatives à l’intégration d’un pare-feu avec Azure Stack Hub renforcé.

#### <a name="private-network"></a>Réseau privé

Le réseau /20 (4 096 adresses IP hôtes) est privé pour la région Azure Stack Hub renforcé. Il ne s’étend pas au-delà des appareils de commutation de bordure de la région Azure Stack Hub renforcé. Ce réseau est divisé en plusieurs sous-réseaux, par exemple :

- **Réseau de stockage** : Réseau /25 (128 adresses IP) utilisé pour prendre en charge l’utilisation du trafic de stockage Spaces Direct et Server Message Block (SMB) ainsi que la migration dynamique de machine virtuelle.
- **Réseau IP virtuel interne** : Réseau /25 dédié aux adresses IP virtuelles internes uniquement pour l’équilibrage de charge logicielle.
- **Réseau de conteneurs** : Réseau /23 (512 adresses IP) dédié au trafic interne uniquement entre les conteneurs exécutant des services d’infrastructure

La taille du réseau privé est /20 (4 096 adresses IP) pour l’espace d’adressage IP privé. Ce réseau est privé pour le système Azure Stack Hub renforcé. Il n’est pas routé au-delà des appareils de commutation de bordure du système Azure Stack Hub renforcé, et peut être réutilisé sur plusieurs systèmes Azure Stack Hub renforcé. Bien que le réseau soit privé pour Azure Stack Hub renforcé, il ne doit pas chevaucher d’autres réseaux dans le centre de données. Pour obtenir des conseils sur l’espace d’adressage IP privé, nous vous recommandons de suivre le document RFC 1918.

L’espace d’adressage IP privé /20 est divisé en plusieurs réseaux qui permettent à l’infrastructure système d’Azure Stack Hub renforcé de s’exécuter sur des conteneurs dans les versions futures. Consultez les 1 910 notes de publication pour plus d’informations. Ce nouvel espace d’adresses IP privées favorise les efforts en cours visant à réduire l’espace d’adresses IP routable nécessaire avant le déploiement.

#### <a name="azure-stack-hub-ruggedized-infrastructure-network"></a>Réseau de l’infrastructure d’Azure Stack Hub renforcé

Le réseau /24 est dédié aux composants internes Azure Stack Hub renforcé pour leur permettre de communiquer et d’échanger des données entre eux. Ce sous-réseau peut être routable de manière externe à la solution Azure Stack Hub renforcé vers votre centre de données. Nous vous **déconseillons** d’utiliser des adresses IP routables publiques ou Internet sur ce sous-réseau. Ce réseau est annoncé jusqu’à la frontière, mais la plupart de ses adresses IP sont protégées par des listes ACL (listes de contrôle d’accès). Les adresses IP autorisées pour l’accès sont comprises dans une plage de petite taille, équivalant à un réseau /27. Les adresses IP hébergent des services tels que le point de terminaison privilégié (PEP) et la sauvegarde Azure Stack Hub renforcé.

#### <a name="public-vip-network"></a>Réseau d’adresse IP virtuelle publique

Le réseau d’adresses IP virtuelles publiques est affecté au contrôleur réseau dans Azure Stack Hub renforcé. Il ne s’agit pas d’un réseau logique sur le commutateur. Le SLB utilise le pool d’adresses et assigne des réseaux /32 pour les charges de travail clientes. Dans la table de routage du commutateur, ces adresses IP /32 sont publiées en tant qu’itinéraire disponible via BGP (Border Gateway Protocol). Ce réseau contient des adresses publiques accessibles de manière externe. L’infrastructure d’Azure Stack Hub renforcé réserve les 31 premières adresses de ce réseau d’adresses IP virtuelles publiques, tandis que les adresses restantes sont utilisées par des machines virtuelles de locataires. La taille réseau sur ce sous-réseau peut aller d’un minimum de /26 (64 hôtes) à un maximum de /22 (1022 hôtes). Nous vous recommandons de planifier un réseau /24.

#### <a name="switch-infrastructure-network"></a>Réseau d’infrastructure du commutateur

Le réseau /26 est le sous-réseau qui contient les sous-réseaux d’adresses IP /30 point-à-point routables, ainsi que les bouclages. Il s’agit de sous-réseaux /32 dédiés pour la gestion du commutateur intrabande et l’ID de routeur BGP. Cette plage d’adresses IP doit être routable en dehors de la solution Azure Stack Hub renforcé vers votre centre de données. Il peut s’agit d’adresses IP privées ou publiques.

#### <a name="switch-management-network"></a>Réseau de gestion du commutateur

Le réseau /29 (6 adresses IP hôtes) est dédié à la connexion des ports de gestion des commutateurs. Il autorise un accès hors bande pour le déploiement, la gestion et la résolution des problèmes. Il est calculé à partir du réseau d’infrastructure du commutateur mentionné ci-dessus.

## <a name="dns-design-overview"></a>Vue d’ensemble de la conception DNS

Pour permettre l’accès aux points de terminaison Azure Stack Hub renforcé (par exemple *portal*, *adminportal*, *management* et *adminmanagement*) depuis l’extérieur d’Azure Stack Hub renforcé, vous devez intégrer les services DNS Azure Stack Hub renforcé avec les serveurs DNS qui hébergent les zones DNS à utiliser dans Azure Stack Hub renforcé.

### <a name="azure-stack-hub-ruggedized-dns-namespace"></a>Espace de noms DNS d’Azure Stack Hub renforcé

Vous devez fournir certaines informations importantes relatives au DNS quand vous déployez Azure Stack Hub renforcé.

| **Champ** | **Description** | **Exemple** |
|-----------|-----------------|-------------|
| Région                   | Localisation géographique de votre déploiement d’Azure Stack Hub renforcé. | est               |
| Nom du domaine externe     | Nom de la zone à utiliser pour votre déploiement d’Azure Stack Hub renforcé. | cloud.fabrikam.com |
| Nom de domaine interne     | Nom de la zone interne utilisée pour les services d’infrastructure dans Azure Stack Hub renforcé. Elle est intégrée au service d’annuaire et privée (inaccessible depuis l’extérieur du déploiement d’Azure Stack Hub renforcé). | azurestack.local   |
| Redirecteurs DNS           | Serveurs DNS utilisés pour transférer les requêtes DNS, les zones DNS et les enregistrements hébergés en dehors d’Azure Stack Hub renforcé (sur l’intranet d’entreprise ou sur l’Internet public). Vous pouvez modifier la valeur de Redirecteur DNS avec la cmdlet **Set-AzSDnsForwarder** après le déploiement. |                    |
| Préfixe de nommage (facultatif) | Préfixe de nommage que vous souhaitez affecter aux noms de machines des instances de rôle d’infrastructure Azure Stack Hub renforcé. S’il n’est pas fourni, la valeur par défaut est « azs ». | azs                |

Le nom de domaine complet (FQDN) de votre déploiement d’Azure Stack Hub renforcé et des points de terminaison correspond à la combinaison du paramètre de région et du paramètre de nom de domaine externe. D’après les valeurs des exemples du tableau précédent, le FQDN du déploiement d’Azure Stack Hub renforcé est : *east.cloud.fabrikam.com*

Ainsi, certains des points de terminaison pour ce déploiement ressembleraient, par exemple, aux URL suivantes :

- `https://portal.east.cloud.fabrikam.com`
- `https://adminportal.east.cloud.fabrikam.com`

Si vous souhaitez utiliser cet exemple d’espace de noms DNS pour un déploiement d’Azure Stack Hub renforcé, les conditions suivantes doivent être respectées :

- La zone fabrikam.com est inscrite auprès d’un bureau d’enregistrement de domaines, d’un serveur DNS d’entreprise interne ou des deux. L’inscription dépend de vos exigences en matière de résolution de noms.
- Le domaine enfant cloud.fabrikam.com existe sous la zone fabrikam.com.
- Les serveurs DNS qui hébergent les zones fabrikam.com et cloud.fabrikam.com sont accessibles à partir du déploiement d’Azure Stack Hub renforcé.

Pour résoudre les noms DNS des points de terminaison et des instances d’Azure Stack Hub renforcé en dehors d’Azure Stack Hub renforcé, vous devez intégrer les serveurs DNS. Les serveurs qui hébergent la zone DNS externe pour Azure Stack Hub renforcé sont inclus dans les serveurs DNS qui hébergent la zone parente à utiliser.

#### <a name="dns-name-labels"></a>Étiquettes de nom DNS

Azure Stack Hub renforcé prend en charge l’ajout d’une étiquette de nom DNS à une adresse IP publique afin de permettre la résolution de noms pour les adresses IP publiques. Les étiquettes DNS permettent aux utilisateurs d’accéder aux applications et services hébergés dans Azure Stack Hub renforcé à partir de leur nom. L’étiquette de nom DNS utilise un espace de noms légèrement différent que les points de terminaison d’infrastructure. Suivant l’exemple d’espace de noms précédent, l’espace de noms pour les étiquettes de nom DNS s’affiche comme suit : *\*.east.cloudapp.cloud.fabrikam.com*.

Si un locataire spécifie **MyApp** dans le champ de nom DNS d’une ressource d’adresse IP publique, il crée un enregistrement A pour **myapp** dans la zone **east.cloudapp.cloud.fabrikam.com** sur le serveur DNS externe Azure Stack Hub renforcé. Le nom de domaine complet obtenu est le suivant : *myapp.east.cloudapp.cloud.fabrikam.com*.

Si vous souhaitez tirer parti de cette fonctionnalité et utiliser cet espace de noms, vous devez intégrer les serveurs DNS. Vous devez inclure les serveurs qui hébergent la zone DNS externe pour Azure Stack Hub renforcé ainsi que les serveurs DNS qui hébergent la zone parente à utiliser. Dans la mesure où cet espace de noms est différent de celui utilisé pour les points de terminaison de service Azure Stack Hub renforcé, vous devez créer une règle de délégation ou de transfert conditionnel supplémentaire.

Pour plus d’informations sur le fonctionnement de l’étiquette de nom DNS, consultez « Utilisation de DNS dans Azure Stack Hub renforcé ».

### <a name="resolution-and-delegation"></a>Résolution et délégation

Deux types de serveur DNS sont disponibles :

- Un serveur DNS faisant autorité héberge les zones DNS. Il répond aux requêtes DNS pour les enregistrements de ces zones uniquement.
- Un serveur DNS récursif n’héberge pas de zones DNS. Il répond à toutes les requêtes DNS, en appelant des serveurs DNS faisant autorité pour rassembler les données dont il a besoin.

Azure Stack Hub renforcé comprend à la fois des serveurs DNS de référence et des serveurs DNS récursifs. Les serveurs récursifs sont utilisés pour résoudre les noms de tous les éléments à l’exception de ceux de la zone privée interne et de ceux de la zone DNS publique externe pour le déploiement d’Azure Stack Hub renforcé.

## <a name="resolving-external-dns-names-from-azure-stack-hub-ruggedized"></a>Résolution des noms DNS externes à partir d’Azure Stack Hub renforcé

Pour permettre la résolution des noms DNS des points de terminaison en dehors d’Azure Stack Hub renforcé (par exemple www.bing.com), vous devez fournir des serveurs DNS qu’Azure Stack Hub renforcé peut utiliser afin de transférer les requêtes DNS pour lesquelles Azure Stack Hub ne fait pas autorité. Les serveurs DNS vers lesquels Azure Stack Hub renforcé transfère les requêtes sont obligatoires dans la feuille de travail de déploiement (dans le champ du redirecteur DNS). Indiquez au moins deux serveurs dans ce champ à des fins de tolérance de panne. En l’absence de ces valeurs, le déploiement d’Azure Stack Hub renforcé ne peut pas réussir. Vous pouvez modifier les valeurs de Redirecteur DNS avec la cmdlet **Set-AzSDnsForwarder** après le déploiement.

## <a name="firewall-design-overview"></a>Vue d’ensemble de conception du pare-feu

Il est recommandé d’utiliser un pare-feu pour sécuriser Azure Stack Hub renforcé. Les pare-feu peuvent contribuer à la défense contre les attaques par déni de service distribué (DDoS), à la détection des intrusions et à l'inspection de contenu. Mais ils peuvent également se transformer en goulot d'étranglement pour les services de stockage Azure (objets blobs, tables, files d'attente, etc.).

Si un mode de déploiement déconnecté est utilisé, vous devez publier le point de terminaison AD FS. Pour plus d’informations, consultez l’article sur l’identité de l’intégration au centre de données.

Les points de terminaison Azure Resource Manager (administrateur), portail administrateur et Key Vault (administrateur) ne nécessitent aucune publication externe. Par exemple, en tant que fournisseur de services, vous pouvez limiter la surface d’attaque en administrant uniquement Azure Stack Hub renforcé depuis l’intérieur de votre réseau plutôt qu’à partir d’Internet.

Pour les entreprises, le réseau externe peut être le réseau d’entreprise existant. Dans ce scénario, vous devez publier des points de terminaison pour exécuter Azure Stack Hub renforcé à partir du réseau d’entreprise.

### <a name="network-address-translation"></a>Traduction d’adresses réseau

La traduction d’adresses réseau (NAT, Network Address Translation) est la méthode recommandée pour permettre à la machine virtuelle de déploiement (DVM) d’accéder aux ressources externes lors du déploiement. Également pour les machines virtuelles de la console de récupération d’urgence (ERCS) ou le point de terminaison privilégié (PEP) au cours de l’inscription et de la résolution des problèmes. 

Vous pouvez également utiliser NAT à la place des adresses IP publiques sur le réseau externe ou des adresses IP virtuelles publiques. Toutefois, ceci n’est pas recommandé car NAT limite l’expérience utilisateur du locataire et accroît la complexité. Une option serait NAT 1:1, qui nécessite toujours une adresse IP publique par adresse IP d'utilisateur sur le pool. Une autre option serait NAT plusieurs:1, qui nécessite une règle NAT par adresse IP virtuelle d'utilisateur pour tous les ports qu'un utilisateur pourrait utiliser.

L’utilisation de NAT pour une adresse IP virtuelle publique présente des inconvénients. En voici quelques-uns :
- Surcharge lors de la gestion des règles de pare-feu car les utilisateurs contrôlent leurs propres points de terminaison et leurs propres règles de publication dans la pile de mise en réseau logicielle (SDN). Les utilisateurs doivent contacter l’opérateur Azure Stack Hub renforcé pour obtenir la publication de leurs adresses IP virtuelles et la mise à jour de la liste des ports. 
- Bien que l’utilisation de NAT limite l’expérience de l’utilisateur, elle donne à l’opérateur un contrôle total sur les demandes de publication.
- Pour les scénarios de cloud hybride avec Azure, tenez compte du fait qu’Azure ne prend pas en charge la configuration d’un tunnel VPN vers un point de terminaison à l’aide de NAT.

### <a name="ssl-interception"></a>Interception SSL

Il est recommandé de désactiver tout type d’interception SSL (par exemple le déplacement du déchiffrement) sur l’ensemble du trafic Azure Stack Hub renforcé. Si l’interception SSL est prise en charge dans les futures mises à jour, nous fournirons des conseils d’aide sur la façon de l’activer pour Azure Stack Hub renforcé.

###  <a name="edge-deployment-firewall-scenario"></a>Scénario de pare-feu de déploiement de périphérie

Dans le cadre d’un déploiement de périphérie, Azure Stack Hub renforcé est déployé directement derrière le routeur ou le pare-feu de périphérie. Dans ces scénarios, il est pris en charge pour que le pare-feu se trouve au-dessus de la bordure (scénario 1) où il prend en charge les configurations de pare-feu actif/actif et actif/passif. Il peut également agir en tant que périphérique de bordure (scénario 2), où il prend uniquement en charge la configuration du pare-feu actif/actif. Le scénario 2 s’appuie sur plusieurs chemins d’accès (ECMP) de coût égal avec le routage BGP ou statique pour le basculement.

Les adresses IP routables publiques sont spécifiées pour le pool d'adresses IP virtuelles publiques à partir du réseau externe au moment du déploiement. Dans un scénario de périphérie, les adresses IP routables ne sont **pas** recommandées sur un autre réseau pour des raisons de sécurité. Ce scénario permet à un utilisateur de bénéficier d’une expérience cloud auto-contrôlée complète, comme dans un cloud public tel qu’Azure.

 ![Scénario de pare-feu de périphérie pour Azure Stack Hub renforcé](media/network-introduction/edge-firewall-scenario-60.png) 

### <a name="enterprise-intranet-or-perimeter-network-firewall-scenario"></a>Scénario de pare-feu réseau de périmètre ou intranet d’entreprise

Dans un déploiement au sein d’un intranet ou d’un périmètre d’entreprise, Azure Stack Hub renforcé est déployé sur un pare-feu multizone ou entre le pare-feu de périphérie et le pare-feu du réseau d’entreprise interne. Le trafic est ensuite distribué entre le réseau de périmètre sécurisé (ou DMZ) et les zones non sécurisées, comme décrit ci-dessous :

- **Zone sécurisée** : Le réseau interne qui utilise des adresses IP routables de l’entreprise ou internes. Le réseau sécurisé peut être divisé. Il peut disposer d’un accès sortant à Internet via le pare-feu NAT. Il est normalement accessible à partir de votre centre de données via le réseau interne. Tous les réseaux Azure Stack Hub renforcé doivent résider dans la zone sécurisée, à l’exception du pool d’adresses IP virtuelles publiques du réseau externe.
- **Zone du périmètre**. Les applications externes ou accessibles sur Internet comme les serveurs web sont généralement déployées sur le réseau de périmètre. Celui-ci est normalement analysé par un pare-feu pour éviter les attaques de type DDoS et les intrusions (piratage), mais il autorise également le trafic entrant spécifié à partir d’internet. Seul le pool d’adresses IP virtuelles publiques du réseau externe d’Azure Stack Hub renforcé doit résider dans la zone DMZ.
- **Zone non sécurisée**. Il s’agit du réseau externe, l’Internet. Le déploiement d’Azure Stack Hub renforcé sur la zone non sécurisée **n’est pas** recommandé.

 ![Scénario du pare-feu du réseau de périmètre](media/network-introduction/perimeter-network-firewall-scenario-50.png) 

## <a name="vpn-design-overview"></a>Vue d’ensemble de la conception VPN

Bien que le VPN soit un concept d’utilisateur, il existe des considérations importantes qu’un propriétaire de solution et un opérateur doivent connaître.

Avant de pouvoir envoyer du trafic réseau entre votre réseau virtuel Azure et votre site local, vous devez créer une passerelle de réseau virtuel pour votre réseau virtuel.

Une passerelle VPN est un type de passerelle de réseau virtuel qui envoie le trafic chiffré à travers une connexion publique. Vous pouvez utiliser des passerelles VPN pour sécuriser le trafic entre un réseau virtuel dans Azure Stack Hub renforcé et un réseau virtuel dans Azure. Vous pouvez également envoyer en toute sécurité le trafic entre un réseau virtuel et un autre réseau connecté à un périphérique VPN.

Lorsque vous créez une passerelle de réseau virtuel, vous spécifiez le type de passerelle que vous voulez créer. Azure Stack Hub renforcé prend en charge un seul type de passerelle de réseau virtuel : le type **Vpn**.

Chaque réseau virtuel peut avoir deux passerelles de réseau virtuel, mais une seule de chaque type. Selon les paramètres que vous choisissez, vous pouvez créer plusieurs connexions à une passerelle VPN unique. Les connexions sur plusieurs sites sont un bon exemple.

Avant de créer et de configurer des passerelles VPN pour Azure Stack Hub renforcé, passez en revue les considérations relatives à la gestion de réseau Azure Stack Hub renforcé. Vous découvrirez en quoi les configurations d’Azure Stack Hub renforcé diffèrent de celles d’Azure.

Dans Azure, le débit de la bande passante pour la référence SKU de passerelle VPN que vous choisissez doit être réparti entre toutes les connexions connectées à la passerelle. Toutefois, dans Azure Stack Hub renforcé, la valeur de la bande passante pour la référence SKU de la passerelle VPN est appliquée à chaque ressource de connexion connectée à la passerelle. Par exemple : 

- Dans Azure, la référence SKU de passerelle VPN de base peut prendre en charge environ 100 Mbits/s de débit agrégé. Si vous créez deux connexions pour cette passerelle VPN, dont une connexion avec une bande passante de 50 Mbits/s, 50 Mbits/s sont disponibles pour l’autre connexion. 
- Dans Azure Stack Hub renforcé, un débit de 100 Mbits/s est alloué à chaque connexion à la référence SKU de la passerelle VPN de base.

### <a name="vpn-types"></a>Types de VPN

Lorsque vous créez la passerelle de réseau virtuel d’une configuration de passerelle VPN, vous devez spécifier un type de VPN. Le type de VPN que vous choisissez dépend de la topologie de connexion que vous souhaitez créer. Un type VPN peut également dépendre du matériel utilisé. Les configurations S2S nécessitent un périphérique VPN. Certains périphériques VPN seront ne prennent en charge qu’un certain type de VPN. 

>[!IMPORTANT]
> Azure Stack Hub renforcé prend en charge uniquement les VPN basés sur le routage. Si votre appareil prend en charge uniquement les VPN basés sur des stratégies, les connexions à ces appareils à partir d’Azure Stack Hub renforcé ne sont pas prises en charge. De plus, Azure Stack Hub renforcé ne prend pas en charge l’utilisation des sélecteurs de trafic basés sur des stratégies pour les passerelles basées sur le routage, car les configurations de stratégies IPsec/IKE personnalisées ne sont pas prises en charge.

- **PolicyBased** : Les VPN basés sur des stratégies chiffrent et acheminent des paquets via les tunnels IPsec, en fonction des stratégies IPsec. Les stratégies sont configurées avec les combinaisons de préfixes d’adresses entre votre réseau local et le VNet (réseau virtuel) Azure Stack Hub renforcé. La stratégie ou le sélecteur de trafic prennent généralement la forme d’une liste d’accès dans la configuration de périphérique VPN.  **PolicyBased** est pris en charge dans Azure, mais pas dans Azure Stack Hub renforcé. 
- **RouteBased** : Les VPN basés sur l’itinéraire utilisent des itinéraires configurés dans le transfert IP ou la table de routage. Les itinéraires acheminent des paquets vers les interfaces de tunnel correspondantes. Les interfaces de tunnel chiffrent ou déchiffrent ensuite les paquets se trouvant dans et hors des tunnels. La stratégie, ou sélecteur de trafic, des VPN **RouteBased** est configurée comme universelle (ou utilise des caractères génériques). Par défaut, ils ne sont pas modifiables. Le VPN **RouteBased** a pour valeur **RouteBased**.

### <a name="configuring-a-vpn-gateway"></a>Configuration d’une passerelle VPN

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

Les graphiques et les descriptions dans les sections suivantes peuvent vous aider à sélectionner une topologie de connexion répondant à vos besoins. Le graphique présente les principales topologies de base, mais il est possible de créer des configurations plus complexes à l’aide des diagrammes.

#### <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a>Site à site et multi-sites (tunnel VPN IPsec/IKE)

##### <a name="site-to-site"></a>De site à site

Une connexion par passerelle VPN *site à site* (S2S) est une connexion sur un tunnel VPN IPsec/IKE (IKEv2). Ce type de connexion nécessite un appareil VPN local disposant d’une adresse IP publique. L’appareil VPN ne peut pas se trouver derrière un traducteur d'adresses réseau. Les connexions S2S peuvent être utilisées pour les configurations hybrides et entre différents locaux.

##### <a name="multi-site"></a>Multi-sites

Une connexion *multi-site* est une variante de la connexion site à site. Vous créez plusieurs connexions VPN à partir de votre passerelle de réseau virtuel, généralement en vous connectant à plusieurs sites locaux. Lorsque vous travaillez avec plusieurs connexions, vous devez utiliser un type de VPN basé sur la route (équivalent d’une passerelle dynamique pour les réseaux virtuels classiques). Chaque réseau virtuel ne pouvant disposer que d’une seule passerelle de réseau virtuel, toutes les connexions passant par la passerelle partagent la bande passante disponible.

### <a name="gateway-skus"></a>Références SKU de passerelle

Quand vous créez une passerelle de réseau virtuel pour Azure Stack Hub renforcé, vous spécifiez la référence SKU de passerelle à utiliser. Les références SKU de passerelle VPN suivantes sont prises en charge :

- De base
- Standard
- Hautes performances

La sélection d’une référence SKU de passerelle supérieure alloue plus de processeurs et de bande passante réseau à la passerelle. Par conséquent, la passerelle peut prendre en charge un débit réseau plus élevé sur le réseau virtuel.

Azure Stack Hub renforcé ne prend pas en charge la référence SKU de passerelle Ultra-performance, qui est utilisée exclusivement avec ExpressRoute.

Prenez en compte les éléments suivants lorsque vous sélectionnez la référence SKU :

- Azure Stack Hub renforcé ne prend pas en charge les passerelles basées sur des stratégies.
- Le protocole BGP n’est pas pris en charge pour la référence SKU De base.
- Les configurations de passerelle ExpressRoute-VPN coexistantes ne sont pas prises en charge dans Azure Stack Hub renforcé.

#### <a name="gateway-availability"></a>Disponibilité de la passerelle

Vous pouvez configurer uniquement les scénarios de haute disponibilité sur la référence SKU de connexion **Passerelle hautes performances**. À la différence d’Azure, qui offre une disponibilité dans les configurations de déploiement actif/actif et actif/passif, Azure Stack Hub renforcé prend en charge uniquement la configuration de déploiement actif/passif.

#### <a name="failover"></a>Basculement

Il existe trois machines virtuelles d’infrastructure de passerelle multilocataire dans Azure Stack Hub renforcé. Deux de ces machines virtuelles sont en mode actif et la troisième est en mode redondant. Les machines virtuelles actives permettent la création de connexions VPN et la machine virtuelle redondante accepte uniquement les connexions VPN en cas de basculement. Si une machine virtuelle de passerelle active n’est plus disponible, la connexion VPN bascule vers la machine virtuelle redondante quelques instants (quelques secondes) après la perte de la connexion.

### <a name="estimated-aggregate-throughput-by-sku"></a>Débit agrégé estimé par SKU

Le tableau suivant présente les types de passerelle et le débit total estimé par référence SKU de passerelle :

|                          | **Débit de passerelle VPN (1)** | **Tunnels IPsec max de passerelle VPN (2)** |
|--------------------------|--------------------------------|---------------------------------------|
| **Référence (SKU) de base** **(3)**    | 100 Mbits/s                       | 20                                    |
| **Référence Standard**         | 100 Mbits/s                       | 20                                    |
| **Référence hautes performances** | 200 Mbits/s                       | 10                                    |

Notes de la table

**(1)** - Le débit du VPN n’est pas garanti pour les connexions entre différents locaux via Internet. Il s’agit de la mesure du débit maximal possible.  
**(2)** - Le nombre maximal de tunnels correspond au nombre total par déploiement d’Azure Stack Hub renforcé pour tous les abonnements.  
**(3)** - Le routage BGP n’est pas pris en charge pour la référence SKU de base.  

>[!IMPORTANT]
> Vous ne pouvez créer qu’une seule connexion VPN site à site entre deux déploiements d’Azure Stack Hub renforcé. En effet, la plateforme n’autorise qu’une seule connexion VPN à la même adresse IP. Dans la mesure où Azure Stack renforcé tire profit de la passerelle multilocataire, qui utilise une seule adresse IP publique pour toutes les passerelles VPN dans le système Azure Stack Hub renforcé, il ne peut exister qu’une seule connexion VPN entre deux systèmes Azure Stack Hub renforcé. 
>
> Cette limitation s’applique également à la connexion de plusieurs connexions VPN de site à site à une passerelle VPN qui utilise une seule adresse IP. Azure Stack Hub renforcé ne permet pas la création de plusieurs ressources de passerelle de réseau local à l’aide de la même adresse IP.** 


### <a name="ipsecike-parameters"></a>Paramètres IPsec/IKE

Quand vous configurez une connexion VPN dans Azure Stack Hub renforcé, vous devez la configurer aux deux extrémités. Si vous configurez une connexion VPN entre Azure Stack Hub renforcé et un appareil, ce dernier peut vous demander des paramètres supplémentaires. Par exemple, un commutateur ou un routeur agissant comme passerelle VPN.

Contrairement à Azure, qui prend en charge plusieurs offres en tant qu’initiateur et répondeur, Azure Stack Hub renforcé ne prend en charge qu’une seule offre par défaut. Si vous devez utiliser différents paramètres IPSec/IKE pour travailler avec votre appareil VPN, d’autres paramètres sont disponibles pour vous permettre de configurer votre connexion manuellement.

#### <a name="ike-phase-1-main-mode-parameters"></a>Paramètres IKE Phase 1 (Mode principal)

| **Propriété**                    | **Valeur**      |
|---------------------------------|----------------|
| Version IKE                     | IKEv2          |
| Groupe Diffie-Hellman            | ECP384         |
| Méthode d'authentification           | Clé prépartagée |
| Chiffrement et algorithmes de hachage | AES256, SHA384 |
| Durée de vie de l’AS (durée)              | 28 800 secondes |

#### <a name="ike-phase-2-quick-mode-parameters"></a>Paramètres IKE Phase 2 (Mode rapide)

| **Propriété**                                     | **Valeur**      |
|--------------------------------------------------|----------------|
| Version IKE                                      | IKEv2          |
| Chiffrement et algorithmes de hachage (Chiffrement)     | GCMAES256      |
| Chiffrement et algorithmes de hachage (Authentification) | GCMAES256      |
| Durée de vie de l’AS (durée)                               | 27 000 secondes |
| Durée de vie de l’AS (kilo-octets)                          | 33 553 408     |
| PFS (Perfect Forward Secrecy)                    | ECP384         |
| Détection d’homologue mort                              | Prise en charge      |

### <a name="configure-custom-ipsecike-connection-policies"></a>Configurer des stratégies de connexion IPSec/IKE personnalisées

La norme de protocole IPsec et IKE prend en charge un large éventail d’algorithmes de chiffrement dans différentes combinaisons. Pour voir quels sont les paramètres pris en charge dans Azure Stack Hub renforcé afin de répondre aux impératifs de conformité ou de sécurité, consultez les informations relatives aux paramètres IPsec/IKE.

Cet article fournit des instructions sur la création et la configuration d’une stratégie IPsec/IKE ainsi que sur son application à une connexion nouvelle ou existante.

#### <a name="considerations"></a>Considérations

Notez les points importants suivants concernant l’utilisation de ces stratégies :

- La stratégie IPsec/IKE fonctionne uniquement sur les références SKU de passerelle *Standard* et *HighPerformance* (Basé sur itinéraires).
- Vous pouvez uniquement spécifier **une** combinaison de stratégie pour une connexion donnée.
- Vous devez spécifier tous les algorithmes et paramètres pour IKE (mode principal) et IPsec (mode rapide). Vous n’êtes pas en droit de spécifier de stratégie partielle.
- Consulter les spécifications de votre fournisseur de périphérique VPN pour vous assurer que la stratégie est prise en charge sur vos périphériques VPN locaux. Les connexions site à site ne peuvent pas être établies si les stratégies sont incompatibles.

#### <a name="workflow-to-create-and-set-ipsecike-policy"></a>Workflow de création et de définition d’une stratégie IPsec/IKE

Cette section décrit le workflow nécessaire pour créer et mettre à jour la stratégie IPsec/IKE sur une connexion VPN site à site :

1.  Créer un réseau virtuel et une passerelle VPN
2.  Créer une passerelle de réseau local pour la connexion entre différents locaux
3.  Créer une stratégie IPsec/IKE avec les algorithmes et les paramètres sélectionnés
4.  Créer une connexion IPsec avec la stratégie IPsec/IKE
5.  Ajouter/mettre à jour/supprimer une stratégie IPsec/IKE pour une connexion existante

#### <a name="supported-cryptographic-algorithms-and-key-strengths"></a>Algorithmes de chiffrement pris en charge et forces des clés

Le tableau suivant liste les algorithmes de chiffrement pris en charge et les forces de clés configurables par les clients Azure Stack Hub renforcé :

| **IPsec/IKEv2**  | **Options** |
|------------------|-------------|
| Chiffrement IKEv2 | AES256, AES192, AES128, DES3, DES                                                                                                                         |
| Intégrité IKEv2  | SHA384, SHA256, SHA1, MD5                                                                                                                                 |
| Groupe DH         | ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, aucun                                                                                          |
| Chiffrement IPsec | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, Aucun                                                                                  |
| Intégrité IPsec  | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5                                                                                                        |
| Groupe PFS        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, Aucun                                                                                                          |
| Durée de vie de l’AS en mode rapide   | (Facultatif : les valeurs par défaut sont utilisées si rien n’est spécifié)                                                                                                      |
|                  | Secondes (entier ; min. 300 / 27 000 secondes par défaut)                                                                                                         |
|                  | Ko (entier ; min. 1 024 / 102 400 000 Ko par défaut)                                                                                                      |
| Sélecteur de trafic | Les sélecteurs de trafic basés sur des stratégies ne sont pas pris en charge dans Azure Stack Hub renforcé.                                                                                           |

La configuration de votre périphérique VPN local doit correspondre aux algorithmes et paramètres suivants, spécifiés dans la stratégie IPsec/IKE Azure ou les contenir :

 - Algorithme de chiffrement IKE (Mode principal/Phase 1).
 - Algorithme d’intégrité IKE (Mode principal/Phase 1).
 - Groupe DH (Mode principal/Phase 1).
 - Algorithme de chiffrement IPsec (Mode rapide/Phase 2).
 - Algorithme d’intégrité IPsec (Mode rapide/Phase 2).
 - Groupe PFS (Mode rapide/Phase 2).
 - Les durées de vie de l’AS sont uniquement des spécifications locales et n’ont pas besoin de correspondre.

Si GCMAES est utilisé pour l’algorithme de chiffrement IPsec, vous devez sélectionner le même algorithme GCMAES et la même longueur de clé pour l’intégrité IPsec. Par exemple : utilisation de GCMAES128 pour les deux.

Dans le tableau précédent :

- IKEv2 correspond au Mode principal ou à la Phase 1.
- IPsec correspond au Mode rapide ou à la Phase 2.
- Groupe DH spécifie le groupe Diffie-Hellmen utilisé dans le Mode principal ou à la Phase 1.
- Groupe PFS spécifie le groupe Diffie-Hellmen utilisé dans le Mode rapide ou à la Phase 2.
- La durée de vie de l’association de sécurité IKEv2 en mode principal est fixée à 28 800 secondes sur les passerelles VPN Azure Stack Hub renforcé.

Le tableau suivant répertorie les groupes Diffie-Hellman correspondants pris en charge par la stratégie personnalisée :

| **Groupe Diffie-Hellman** | **DHGroup**          | **PFSGroup** | **Longueur de clé** |
|--------------------------|----------------------|--------------|----------------|
| 1                        | DHGroup1             | PFS1         | MODP 768 bits   |
| 2                        | DHGroup2             | PFS2         | MODP 1 024 bits  |
| 14                       | DHGroup14            | PFS2048      | MODP 2 048 bits  |
|                          | DHGroup2048          |              |                |
| 19                       | ECP256               | ECP256       | ECP 256 bits    |
| 20                       | ECP384               | ECP384       | ECP 384 bits    |
| 24                       | DHGroup24            | PFS24        | MODP 2 048 bits  |

### <a name="connect-azure-stack-hub-ruggedized-to-azure-using-azure-expressroute"></a>Connecter Azure Stack Hub renforcé à Azure à l’aide d’Azure ExpressRoute

#### <a name="overview-assumptions-and-prerequisites"></a>Vue d’ensemble, hypothèses et conditions préalables

Azure ExpressRoute vous permet d’étendre vos réseaux locaux dans le cloud Microsoft. Vous utilisez une connexion privée fournie par un fournisseur de connectivité. ExpressRoute n’est pas une connexion VPN établie via le réseau Internet public.

Pour en savoir plus sur ExpressRoute, consultez la rubrique Présentation d’ExpressRoute.

#### <a name="assumptions"></a>Hypothèses

Cet article suppose que vous avez :

- Une connaissance pratique de Azure.
- Une connaissance élémentaire d’Azure Stack Hub renforcé.
- Une connaissance élémentaire de la mise en réseau.

#### <a name="prerequisites"></a>Prérequis

Pour connecter Azure Stack Hub renforcé et Azure à l’aide d’ExpressRoute, vous devez tenir compte des impératifs suivants :

- Un circuit ExpressRoute doit être configuré via un fournisseur de connectivité.
- Vous devez disposer d’un abonnement Azure pour créer un circuit ExpressRoute et des réseaux virtuels dans Azure.
- Vous devez disposer d’un routeur qui prend en charge ce qui suit :
  - Connexions VPN site à site entre l’interface LAN du routeur et la passerelle multilocataire Azure Stack Hub renforcé.
  - Création de plusieurs services VRF (Virtual Routing and Forwarding) s’il existe plusieurs locataires dans votre déploiement d’Azure Stack Hub renforcé.
- Un routeur qui possède :
  - Un port WAN connecté au circuit ExpressRoute.
  - Un port LAN connecté à la passerelle multilocataire Azure Stack Hub renforcé.

#### <a name="expressroute-network-architecture"></a>Architecture réseau ExpressRoute

L’illustration suivante montre les environnements Azure Stack Hub renforcé et Azure, une fois la configuration d’ExpressRoute effectuée à l’aide des exemples de cet article :

 ![Architecture réseau ExpressRoute](media/network-introduction/expressroute-network-architecture-60.png) 

L’illustration suivante montre comment plusieurs locataires se connectent à Azure à partir de l’infrastructure Azure Stack Hub renforcé, via le routeur ExpressRoute :

 ![Architecture réseau ExpressRoute multilocataire](media/network-introduction/expressroute-network-architecture-multi-tenant-60.png) 

