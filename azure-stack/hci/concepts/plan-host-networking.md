---
title: Planifier le réseau hôte pour Azure Stack HCI
description: Découvrez comment planifier le réseau hôte pour les clusters Azure Stack HCI
author: v-dasis
ms.topic: how-to
ms.date: 10/13/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 46f98ba8f5d2f33e0b5d9d85ee9c2469a098c17d
ms.sourcegitcommit: d835e211fe65dc54a0d49dfb21ca2465ced42aa4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92200482"
---
# <a name="plan-host-networking-for-azure-stack-hci"></a>Planifier le réseau hôte pour Azure Stack HCI

> S’applique à Azure Stack HCI, version 20H2

Cette rubrique décrit les points à prendre en compte et les conditions requises lors de la planification du réseau hôte dans les environnements de cluster Azure Stack HCI étendus et non étendus.

## <a name="traffic-types-supported"></a>Types de trafic pris en charge

Azure Stack HCI utilise le protocole SMB (Server Message Block). Le protocole SMB sur Azure Stack HCI prend en charge les types de trafic suivants :

- Couche de bus de stockage (SBL) – utilisée par les espaces de stockage direct ; trafic de priorité la plus élevée
- Volumes de cluster partagés (Cluster Shared Volumes)
- Migration dynamique (LM)
- Réplica de stockage (SR) – utilisé dans les clusters étendus
- Partages de fichiers (FS) – FS traditionnel et serveur de fichiers avec montée en puissance parallèle (SOFS)
- Pulsation (HB) du cluster
- Communication entre les clusters (jointures de nœuds, mises à jour de cluster, mises à jour du registre)

Le trafic SMB peut transiter via les protocoles suivants :

- Protocole TCP (Transport Control Protocol) – utilisé entre les sites
- Accès direct à la mémoire à distance (RDMA)

## <a name="traffic-bandwidth-allocation"></a>Allocation de bande passante au trafic

Le tableau suivant présente les allocations de bande passante pour différents types de trafic, où :

- Toutes les unités sont en Gbits/s
- Les valeurs s’appliquent aux clusters étendus et non étendus
- Le trafic SMB obtient 50 % de l’allocation de bande passante totale
- Le trafic de couche de bus de stockage/volume partagé de cluster (SBL/CSV) obtient 70 % des 50 % d’allocation restants
- Le trafic de migration dynamique (LM) obtient 15 % des 50 % d’allocation restants
- Le trafic de réplica de stockage (SR) obtient 14 % des 50 % d’allocation restants
- Le trafic de pulsation (HB) obtient 1 % des 50 % d’allocation restants
- *= doit utiliser la compression plutôt que RDMA si l’allocation de bande passante pour le trafic LM est < 5 Gbits/s

|Vitesse de la carte réseau|Bande passante associée|Réservation SMB de 50 %|% SBL/CSV|Bande passante SBL/CSV|% LM|Bande passante LM|% SR |Bande passante SR|% HB|Bande passante HB|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|10|20|10|70 %|7|14 %*|1,4*|14 %|1.4|2 %|0.2|
|25|50|25|70 %|17.5|15 %*|3,75*|14 %|3,5|1%|0,25|
|40|80| 40|70 %|28|15 %|6|14 %|5.6|1%|0.4|
|50|100|50|70 %|35|15 %|7.5|14 %|7|1%|0.5|
|100|200|100|70 %|70|15 %|15|14 %|14|1%|1|
|200|400|200|70 %|140|15 %|30|14 %|28|1%|2|

## <a name="rdma-considerations"></a>Considérations relatives à RDMA

L’accès direct à la mémoire à distance (RDMA) est un accès mémoire direct à partir de la mémoire d’un ordinateur à celle d’un autre ordinateur, sans impliquer le système d’exploitation d’aucun des ordinateurs. Il favorise un réseau à débit élevé et faible latence tout en réduisant au maximum l’utilisation du processeur, ce qui est particulièrement utile dans les clusters.

Tout le trafic RDMA hôte tire parti de SMB Direct. SMB Direct correspond au trafic SMB 3.0 envoyé via RDMA et il est multiplexé via le port 445. Au moins deux classes de trafic (TC) prenant en charge le contrôle de flux basé sur les priorités (PFC) doivent être utilisées pour que le trafic RDMA reste compatible avec la majorité des commutateurs physiques actuels et futurs du marché.

Le protocole iWARP (Internet Wide Area RDMA Protocol) exécute RDMA via TCP, tandis que RoCE (RDMA sur Ethernet convergé) évite l’utilisation du protocole TCP, mais nécessite des cartes réseau et des commutateurs physiques qui le prennent en charge. Pour les exigences de réseau convergé pour l’accès RDMA via RoCE, consultez le [Guide de déploiement Windows Server 2016 et 2019 avec RDMA](https://github.com/Microsoft/SDN/blob/master/Diagnostics/S2D%20WS2016_ConvergedNIC_Configuration.docx).

L’accès RDMA est activé par défaut pour tout le trafic est/ouest entre les nœuds de cluster d’un site sur le même sous-réseau. L’accès RDMA est désactivé et n’est pas pris en charge pour le trafic de cluster étendu nord/sud entre des sites situés sur des sous-réseaux différents.

Les exigences pour l’accès RDMA pour Azure Stack HCI sont les suivantes :

- Tout le trafic entre les sous-réseaux et entre les sites (clusters étendus) doit utiliser WinSock TCP. Tous les tronçons réseau intermédiaires se trouvent en dehors de la vue et du contrôle d’Azure Stack HCI.
- L’accès RDMA entre les sous-réseaux et entre les sites (clusters étendus) n’est pas pris en charge. L’utilisation de liaisons montantes et de plusieurs appareils réseau signifie l’existence de plusieurs points d’échec où cela peut devenir instable et affecter la prise en charge.
- Aucune carte réseau virtuelle supplémentaire n’est nécessaire pour le trafic de réplica de stockage pour les clusters étendus. Toutefois, à des fins de dépannage, il peut être utile de maintenir le trafic entre sites et le trafic entre sous-réseaux séparés du trafic RDMA est-ouest. Si SMB Direct ne peut pas être désactivé en mode natif entre sites ou entre sous-réseaux par flux, alors :
    - Une ou plusieurs cartes réseau virtuelles supplémentaires doivent être provisionnées pour le réplica de stockage.
    - L’accès RDMA des cartes réseau virtuelles du réplica de stockage doit être désactivé à l’aide de l’applet de commande PowerShell [Disable-NetAdapterRDMA](https://docs.microsoft.com/powershell/module/netadapter/disable-netadapterrdma), car il est par définition entre sites et entre sous-réseaux.
    - Les adaptateurs RDMA natifs nécessitent un vSwitch et des cartes réseau virtuelles pour la prise en charge du réplica de stockage, afin de répondre aux conditions de site/sous-réseau ci-dessus.
    - Les exigences en bande passante de l’accès RDMA intra-site exigent de connaître les pourcentages de bande passante par type de trafic, tels qu’indiqués dans la section **Allocation de bande passante au trafic** . Cela garantit que des réservations et des limites de bande passante appropriées peuvent être appliquées pour le trafic est/ouest (de nœud à nœud).
- Le trafic de réplica de stockage et de migration dynamique doit avoir une bande passante SMB limitée. Sinon, il pourrait consommer toute la bande passante et en priver le trafic de stockage de haute priorité. Pour plus d’informations, reportez-vous aux applets de commande PowerShell [Set-SmbBandwidthLimit](https://docs.microsoft.com/powershell/module/smbshare/set-smbbandwidthlimit) et [Set-SRNetworkConstraint](https://docs.microsoft.com/powershell/module/storagereplica/set-srnetworkconstraint).

> [!NOTE]
> Vous devez convertir les bits en octets quand vous utilisez l’applet de commande `Set-SmbBandwidthLimit`.

## <a name="node-interconnect-requirements"></a>Exigences d’interconnexion des nœuds

Cette section traite des exigences de mise en réseau spécifiques entre les nœuds serveur d’un site, appelées interconnexions. Des interconnexions de nœuds commutées ou non peuvent être utilisées et sont prises en charge :

- **Commutées :** les nœuds serveur sont généralement connectés les uns aux autres via des réseaux Ethernet qui utilisent des commutateurs réseau. Les commutateurs doivent être correctement configurés pour gérer la bande passante et le type de mise en réseau. Si vous utilisez RDMA qui implémente le protocole RoCE, le périphérique réseau et la configuration du commutateur sont importants.
- **Absence de commutateur :** les nœuds serveur peuvent également être interconnectés à l’aide de connexions Ethernet directes sans commutateur. Dans ce cas, chaque nœud serveur doit disposer d’une connexion directe avec tous les autres nœuds de cluster dans le même site.

### <a name="interconnects-for-2-3-node-clusters"></a>Interconnexions pour les clusters à 2-3 nœuds

Il s’agit des *configurations minimales requises pour l’interconnexion* pour les clusters à site unique comportant deux ou trois nœuds. Celles-ci s’appliquent à chaque nœud serveur :

- Une ou plusieurs cartes réseau 1 Gb à utiliser pour les fonctions de gestion
- Une ou plusieurs cartes réseau 10 Gb (ou plus) pour le trafic lié au stockage et à la charge de travail
- Plusieurs connexions réseau entre chaque nœud sont recommandées pour des questions de redondance et de performances

### <a name="interconnects-for-4-node-and-greater-clusters"></a>Interconnexions pour les clusters à 4 nœuds et plus

Il s’agit des *configurations minimales requises pour l’interconnexion* pour les clusters comportant au moins quatre nœuds, et pour les clusters hautes performances. Celles-ci s’appliquent à chaque nœud serveur :

- Une ou plusieurs cartes réseau 1 Gb à utiliser pour les fonctions de gestion.
- Une ou plusieurs cartes réseau 25 Gb (ou plus) pour le trafic lié au stockage et à la charge de travail. Nous vous recommandons d’utiliser au moins deux connexions réseau pour la redondance et les performances.
- Cartes réseau prenant en charge l’accès direct à la mémoire à distance (RDMA) : iWARP (recommandé) ou RoCE.

### <a name="site-to-site-requirements-stretched-cluster"></a>Exigences de site à site (cluster étendu)

Lors de la connexion entre des sites pour les clusters étendus, les exigences d’interconnexion au sein de chaque site s’appliquent toujours et des exigences supplémentaires concernant le trafic de réplica de stockage et la migration dynamique Hyper-V doivent être prises en compte :

- Au moins une connexion RDMA 1 Gb ou Ethernet/TCP entre les sites pour la réplication synchrone. Une connexion 25 Gb est recommandée.
- Un réseau entre les sites disposant de suffisamment de bande passante pour contenir votre charge de travail d’écriture d’E/S et une latence moyenne des allers-retour de 5 ms ou moins pour la réplication synchrone. La réplication asynchrone n’a pas de recommandation de latence.
- Si vous utilisez une connexion unique entre des sites, définissez des limites de bande passante SMB pour le réplica de stockage à l’aide de PowerShell. Pour plus d’informations, consultez [Set-SmbBandwidthLimit](/powershell/module/smbshare/set-smbbandwidthlimit).
- Si vous utilisez plusieurs connexions entre les sites, séparez le trafic entre les connexions. Par exemple, placez le trafic du réplica de stockage sur un réseau distinct du trafic de migration dynamique Hyper-V à l’aide de PowerShell. Pour plus d’informations, consultez [Set-SRNetworkConstraint](/powershell/module/storagereplica/set-srnetworkconstraint).

## <a name="network-port-requirements"></a>Configuration requise des ports réseau

Vérifiez que les ports réseau appropriés sont ouverts entre tous les nœuds serveur au sein d’un site et entre les sites (pour les clusters étendus). Vous avez besoin de règles de pare-feu et de routeur appropriées pour autoriser les protocoles ICMP, SMB (port 445, plus port 5445 pour SMB Direct) et WS-MAN (port 5985) à trafic bidirectionnel entre tous les serveurs du cluster.

Lorsque vous utilisez l’Assistant Création d’un cluster dans Windows Admin Center pour créer le cluster, l’Assistant ouvre automatiquement les ports de pare-feu appropriés sur chaque serveur du cluster pour le clustering de basculement, Hyper-V et le réplica de stockage. Si vous utilisez un pare-feu différent sur chaque serveur, ouvrez les ports suivants :

### <a name="failover-clustering-ports"></a>Ports de clustering de basculement

- ICMPv4 et ICMPv6
- Port TCP 445
- Ports dynamiques RPC
- Port TCP 135
- Port TCP 137
- Port TCP 3343
- Port UDP 3343

### <a name="hyper-v-ports"></a>Ports Hyper-V

- Port TCP 135
- Port TCP 80 (connectivité HTTP)
- Port TCP 443 (connectivité HTTPS)
- Port TCP 6600
- Port TCP 2179
- Ports dynamiques RPC
- Mappeur de point de terminaison RPC
- Port TCP 445

### <a name="storage-replica-ports-stretched-cluster"></a>Ports de réplica de stockage (cluster étendu)

- Port TCP 445
- TCP 5445 (si vous utilisez iWarp RDMA)
- Port TCP 5985
- ICMPv4 et ICMPv6 (si vous utilisez l’applet de commande PowerShell `Test-SRTopology`)

Il peut y avoir des ports supplémentaires non listés ci-dessus. Il s’agit des ports pour le fonctionnement de base d’Azure Stack HCI.

## <a name="network-switch-requirements"></a>Exigences concernant les commutateurs réseau

Cette section définit les exigences concernant les commutateurs physiques utilisés avec Azure Stack HCI. Ces exigences listent les spécifications du secteur, les normes de l’organisation et les protocoles qui sont obligatoires pour tous les déploiements Azure Stack HCI. Sauf mention contraire, la dernière version active (non remplacée) de la norme est exigée.

Ces exigences permettent de garantir des communications fiables entre les nœuds dans les déploiements de cluster Azure Stack HCI. Il est essentiel que les communications entre les nœuds soient fiables. Pour fournir le niveau de fiabilité requis pour Azure Stack HCI, les commutateurs doivent nécessairement :

- Être conformes aux spécifications, normes et protocoles applicables au secteur
- Fournir une visibilité sur les spécifications, les normes et les protocoles pris en charge par le commutateur
- Fournir des informations sur les fonctionnalités qui sont activées

Veillez à demander à votre fournisseur de commutateur si votre commutateur prend en charge les normes suivantes :

#### <a name="standard-ieee-8021q"></a>Standard : IEEE 802.1Q

Les commutateurs Ethernet doivent respecter la spécification IEEE 802.1Q qui définit les réseaux locaux virtuels. Ces deniers sont nécessaires pour plusieurs aspects d’Azure Stack HCI et sont exigés dans tous les scénarios.

#### <a name="standard-ieee-8021qbb"></a>Standard : IEEE 802.1Qbb

Les commutateurs Ethernet doivent respecter la spécification IEEE 802.1Qbb qui définit PFC (Priority Flow Control). PFC est exigé en cas d’utilisation de Data Center Bridging (DCB). Comme DCB peut être utilisé dans les scénarios RDMA RoCE et iWARP, 802.1Qbb est exigé dans tous les scénarios. Un minimum de trois priorités de classe de service (CoS) sont nécessaires sans rétrograder les capacités du commutateur ou la vitesse du port.

#### <a name="standard-ieee-8021qaz"></a>Standard : IEEE 802.1Qaz

Les commutateurs Ethernet doivent respecter la spécification IEEE 802.1Qaz qui définit ETS (Enhanced Transmission Selection). ETS est obligatoire en cas d’utilisation de DCB. Comme DCB peut être utilisé dans les scénarios RDMA RoCE et iWARP, 802.1Qaz est exigé dans tous les scénarios. Un minimum de trois priorités CoS sont nécessaires sans rétrograder les capacités du commutateur ou la vitesse du port.

#### <a name="standard-ieee-8021ab"></a>Standard : IEEE 802.1AB

Les commutateurs Ethernet doivent respecter la spécification IEEE 802.1AB qui définit le protocole LLDP (Link Layer Discovery Protocol). LLDP est nécessaire pour que Windows détecte la configuration du commutateur. La configuration des valeurs TLV (Type-Length-Values) LLDP doit être activée de manière dynamique. Ces commutateurs ne doivent pas nécessiter une configuration supplémentaire.

Par exemple, l’activation du sous-type 3 de la spécification 802.1 doit être automatiquement annoncée à tous les réseaux locaux virtuels disponibles sur les ports de commutateur.

#### <a name="tlv-requirements"></a>Spécifications TLV

LLDP permet aux organisations de définir et d’encoder leurs propres valeurs TLV personnalisés. Celles-ci sont appelées TLV spécifiques à l’organisation. Toutes les valeurs TLV spécifiques à l’organisation commencent par la valeur TLV LLDP de Type 127. Le tableau suivant indique les sous-types de TLV personnalisés spécifiques à l’organisation (Type de TLV 127) qui sont obligatoires et ceux qui sont facultatifs :

|Condition|Organisation|Sous-type TLV|
|-|-|-|
|Obligatoire|IEEE 802.1|Nom du réseau local virtuel (Sous-type = 3)|
|Obligatoire|IEEE 802.3|Taille de trame maximale (Sous-type = 4)|
|Facultatif|IEEE 802.1|ID de port de réseau local virtuel (Sous-type = 1)|
|Facultatif|IEEE 802.1|ID de port et de protocole de réseau local virtuel (Sous-type = 2)|
|Facultatif|IEEE 802.1|Agrégation de liens (Sous-type = 7)|
|Facultatif|IEEE 802.1|Notification de congestion (Sous-type = 8)|
|Facultatif|IEEE 802.1|Configuration ETS (Sous-type = 9)|
|Facultatif|IEEE 802.1|Recommandation ETS (Sous-type = A)|
|Facultatif|IEEE 802.1|Configuration PFC (Sous-type = B)|
|Facultatif|IEEE 802.1|EVB (Sous-type = D)|
|Facultatif|IEEE 802.3|Agrégation de liens (Sous-type = 3)|

> [!NOTE]
> Certaines des fonctionnalités facultatives listées peuvent s’avérer obligatoires dans le futur.

## <a name="next-steps"></a>Étapes suivantes

- Rafraîchissez votre connaissance des concepts de base du clustering de basculement. Consultez [Failover Clustering Networking Basics](https://techcommunity.microsoft.com/t5/failover-clustering/failover-clustering-networking-basics-and-fundamentals/ba-p/1706005?s=09)
- Rafraîchissez vos connaissances de l’utilisation de SET. Consultez [Accès direct à la mémoire à distance (Remote Direct Memory Access, RDMA) et association commutée/intégrée (Switched-Embedded Teaming, SET)](https://docs.microsoft.com/windows-server/virtualization/hyper-v-virtual-switch/rdma-and-switch-embedded-teaming).
- Pour le déploiement, consultez [Créer un cluster en utilisant Windows Admin Center](https://docs.microsoft.com/azure-stack/hci/deploy/create-cluster).
- Pour le déploiement, consultez [Créer un cluster en utilisant Windows PowerShell](https://docs.microsoft.com/azure-stack/hci/deploy/create-cluster-powershell).