---
title: Exigences liées aux réseaux physiques pour Azure Stack HCI
description: Découvrez les exigences liées aux réseaux physiques pour Azure Stack HCI.
author: v-dasis
ms.topic: how-to
ms.date: 11/30/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: c8ea741534954235fc6de9e32d16602e0c369d6f
ms.sourcegitcommit: afdae61022037b5dba8345cb264049897e0aca8f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97051613"
---
# <a name="physical-network-requirements-for-azure-stack-hci"></a>Exigences liées aux réseaux physiques pour Azure Stack HCI

> S’applique à Azure Stack HCI, version 20H2

Cette rubrique décrit le réseau physique (structure) pour Azure Stack HCI, en particulier pour les commutateurs réseau, et les exigences liées.

> [!NOTE]
> Les exigences sont susceptibles de changer dans les versions à venir d’Azure Stack HCI.

## <a name="network-switches-for-azure-stack-hci"></a>Commutateurs réseau pour Azure Stack HCI

Microsoft teste Azure Stack HCI par rapport aux normes et aux protocoles identifiés dans la section **Exigences liées aux commutateurs réseau** ci-dessous. Bien que Microsoft ne certifie pas les commutateurs réseau, nous travaillons avec les fournisseurs pour identifier les appareils qui prennent en charge les exigences Azure Stack HCI.

Ces exigences sont également publiées dans [Spécifications et stratégies du Programme de compatibilité matérielle Windows](https://docs.microsoft.com/windows-hardware/design/compatibility/whcp-specifications-policies).  Sélectionnez **Télécharger les spécifications et les stratégies, version 1809**. Ouvrez le fichier zip, puis **WHCP-Components-Peripherals-Specification-1809.pdf**. Enfin, consultez la section **Device.Network.Switch.SDDC**.

> [!IMPORTANT]
> Il est possible que d’autres commutateurs réseau utilisant des technologies et des protocoles non répertoriés ici fonctionnent. Microsoft ne peut néanmoins pas garantir qu’ils seront compatibles avec Azure Stack HCI et ne pourra peut-être pas aider à résoudre les problèmes qui se produisent.

Lorsque vous achetez des commutateurs réseau, contactez votre fournisseur pour vérifier que les appareils répondent à toutes les exigences d’Azure Stack HCI. Les fournisseurs suivants (classés par ordre alphabétique) ont confirmé que leurs commutateurs respectent ces exigences :

|Fournisseur|10 GbE|25 GbE|100 GbE|
|-----|-----|-----|-----|
|Dell|[Série S41xx](https://www.dell.com/learn/us/en/45/shared-content~data-sheets~en/documents~dell-emc-networking-s4100-series-spec-sheet.pdf)|[Série S52xx](https://www.delltechnologies.com/resources/en-us/asset/data-sheets/products/networking/dell_emc_networking-s5200_on_spec_sheet.pdf)|[Série S52xx](https://www.delltechnologies.com/resources/en-us/asset/data-sheets/products/networking/dell_emc_networking-s5200_on_spec_sheet.pdf)|
|Lenovo|[G8272](https://lenovopress.com/tips1267-lenovo-rackswitch-g8272), [NE1032](https://lenovopress.com/lp0605-thinksystem-ne1032-rackswitch)|[NE2572](https://lenovopress.com/lp0608-lenovo-thinksystem-ne2572-rackswitch) |[NE10032](https://lenovopress.com/lp0609-lenovo-thinksystem-ne10032-rackswitch) |

> [!IMPORTANT]
> Microsoft met à jour cette liste chaque fois que les fournisseurs de commutateurs réseau l’informent de modifications.

Si votre commutateur n’est pas indiqué, contactez le fournisseur pour vérifier que votre modèle et la version du système d’exploitation respectent les exigences formulées dans la section suivante.

## <a name="network-switch-requirements"></a>Exigences concernant les commutateurs réseau

Dans cette section sont indiqués les standards industriels obligatoires pour les commutateurs réseau utilisés dans tous les déploiements Azure Stack HCI. Ces standards permettent de garantir la fiabilité des communications entre les nœuds dans les déploiements de clusters Azure Stack HCI.

> [!NOTE]
> Ethernet est requis par les cartes réseau utilisées pour le trafic de calcul, de stockage et de gestion. Pour plus d’informations, consultez [Exigences liées aux réseaux d’hôtes](host-network-requirements.md).

Voici les standards et spécifications IEEE obligatoires :

### <a name="standard-ieee-8021q"></a>Standard : IEEE 802.1Q

Les commutateurs Ethernet doivent respecter la spécification IEEE 802.1Q qui définit les réseaux locaux virtuels. Ces deniers sont nécessaires pour plusieurs aspects d’Azure Stack HCI et sont exigés dans tous les scénarios.

### <a name="standard-ieee-8021qbb"></a>Standard : IEEE 802.1Qbb

Les commutateurs Ethernet doivent respecter la spécification IEEE 802.1Qbb qui définit PFC (Priority Flow Control). PFC est exigé en cas d’utilisation de Data Center Bridging (DCB). Comme DCB peut être utilisé dans les scénarios RDMA RoCE et iWARP, 802.1Qbb est exigé dans tous les scénarios. Un minimum de trois priorités de classe de service (CoS) sont nécessaires sans rétrograder les capacités du commutateur ni la vitesse des ports. Au moins l’une de ces classes de trafic doit assurer une communication sans perte.

### <a name="standard-ieee-8021qaz"></a>Standard : IEEE 802.1Qaz

Les commutateurs Ethernet doivent respecter la spécification IEEE 802.1Qaz qui définit ETS (Enhanced Transmission Selection). ETS est obligatoire en cas d’utilisation de DCB. Comme DCB peut être utilisé dans les scénarios RDMA RoCE et iWARP, 802.1Qaz est exigé dans tous les scénarios. Un minimum de trois priorités CoS sont nécessaires sans rétrograder les capacités du commutateur ou la vitesse du port.

> [!NOTE]
> L’infrastructure hyperconvergée présente une dépendance élevée vis-à-vis de la communication Est-Ouest de couche 2. La configuration ETS est donc nécessaire. Microsoft ne teste pas Azure Stack HCI avec la valeur DSCP (Differentiated Services Code Point). 

### <a name="standard-ieee-8021ab"></a>Standard : IEEE 802.1AB

Les commutateurs Ethernet doivent respecter la spécification IEEE 802.1AB qui définit le protocole LLDP (Link Layer Discovery Protocol). LLDP est nécessaire à Azure Stack HCI et permet de résoudre les problèmes liés aux configurations des réseaux physiques.

La configuration des valeurs TLV (Type-Length-Values) LLDP doit être activée de manière dynamique. Les commutateurs ne doivent pas demander de configuration supplémentaire au-delà de l’activation d’une valeur TLV spécifique. Par exemple, l’activation du sous-type 3 de la spécification 802.1 doit être automatiquement annoncée à tous les réseaux locaux virtuels disponibles sur les ports de commutateur.

### <a name="custom-tlv-requirements"></a>Exigences TLV personnalisées

LLDP permet aux organisations de définir et d’encoder leurs propres valeurs TLV personnalisés. Celles-ci sont appelées TLV spécifiques à l’organisation. Toutes les valeurs TLV spécifiques à l’organisation commencent par la valeur TLV LLDP de Type 127. Dans le tableau suivant figurent les sous-types de valeurs TLV personnalisées propres à l’organisation (TLV de type 127) obligatoires :

|Version requise|Organisation|Sous-type TLV|
|-----|-----|-----|-----|
|20H2 et versions ultérieures|IEEE 802.1|Nom du réseau local virtuel (Sous-type = 3)|
|20H2 et versions ultérieures|IEEE 802.3|Taille de trame maximale (Sous-type = 4)|

## <a name="network-traffic-and-architecture"></a>Trafic réseau et architecture

Cette section est principalement destinée aux administrateurs réseau.

Azure Stack HCI peut fonctionner dans différentes architectures de centres de données, notamment à deux niveaux (Spine-Leaf) et à trois niveaux (Core-Aggregation-Access). Cette section fait davantage référence aux concepts de la topologie Spine-Leaf, couramment utilisée avec les charges de travail dans une infrastructure hyperconvergée comme Azure Stack HCI.

## <a name="network-models"></a>Modèles de réseaux

Le trafic réseau peut être classé selon sa direction. Les environnements de réseau de zone de stockage (SAN) traditionnels sont majoritairement de type Nord-Sud : le trafic circule d’un niveau de calcul vers un niveau de stockage en franchissant une limite de couche 3 (IP). L’infrastructure hyperconvergée est plus massivement de type Est-Ouest. Dans ce cas, une part importante du trafic reste au sein d’une limite de couche 2 (VLAN).

> [!IMPORTANT]
>Il est vivement recommandé que tous les nœuds de cluster d’un site se trouvent physiquement dans le même rack et soit connectés aux mêmes commutateurs ToR.

### <a name="north-south-traffic-for-azure-stack-hci"></a>Trafic Nord-Sud pour Azure Stack HCI

Le trafic Nord-Sud présente les caractéristiques suivantes :

- Il va d’un commutateur ToR vers la branche centrale ou inversement.
- Il quitte le rack physique ou franchit une limite de couche 3 (IP).
- Il comprend le trafic de gestion (PowerShell, Windows Admin Center), le trafic de calcul (machine virtuelle) et le trafic de cluster étendu intersite.
- Il utilise un commutateur Ethernet pour la connectivité au réseau physique.

### <a name="east-west-traffic-for-azure-stack-hci"></a>Trafic Est-Ouest pour Azure Stack HCI

Le trafic Est-Ouest présente les caractéristiques suivantes :

- Il reste dans les commutateurs ToR et les limites de la couche 2 (VLAN).
- Il comprend le trafic de stockage ou le trafic Migration dynamique entre les nœuds du même cluster et (dans le cas d’un cluster étendu) du même site.
- Il peut utiliser un commutateur Ethernet (commuté) ou une connexion directe (sans commutateur) (cf. les deux sections suivantes).

## <a name="using-switches"></a>Avec commutateurs

Le trafic Nord-Sud impose d’utiliser des commutateurs. Outre le recours à un commutateur Ethernet compatible avec les protocoles requis pour Azure Stack HCI, l’aspect le plus important est le bon dimensionnement de l’infrastructure réseau.

Il est impératif de comprendre la bande passante « non bloquante » de la structure que les commutateurs Ethernet peuvent prendre en charge et de réduire (ou de préférence supprimer) le surabonnement du réseau.

Le surabonnement et les points de congestion courants (par exemple le [groupe d’agrégation de liens à plusieurs châssis](https://en.wikipedia.org/wiki/Multi-chassis_link_aggregation_group) utilisé pour la redondance de chemin) peuvent être éliminés par une utilisation appropriée des sous-réseaux et des réseaux VLAN. Consultez également [Exigences liées aux réseaux d’hôtes](host-network-requirements.md).

Contactez votre fournisseur de réseau ou l’équipe de support réseau pour vérifier que vos commutateurs réseau sont bien dimensionnés pour la charge de travail que vous prévoyez d’exécuter.

## <a name="using-switchless"></a>Sans commutateurs

Azure Stack HCI prend en charge les connexions sans commutateur (directes) pour le trafic Est-Ouest avec toutes les tailles de cluster, à condition que chacun des nœuds du cluster dispose d’une connexion redondante à tous les nœuds. C’est ce que l’on appelle une connexion à « maillage complet ».

> [!NOTE]
>Les avantages des déploiements sans commutateur diminuent avec les clusters de plus de trois nœuds en raison du nombre de cartes réseau requises.

### <a name="advantages-of-switchless-connections"></a>Avantages des connexions sans commutateur

- Aucun achat de commutateur n’est nécessaire pour le trafic Est-Ouest. Il faut à l’inverse un commutateur pour le trafic Nord-Sud. Cela peut entraîner une réduction des dépenses d’investissement, en fonction toutefois du nombre de nœuds du cluster.
- Étant donné qu’il n’y a pas de commutateur, la configuration est limitée à l’hôte, ce qui peut réduire le nombre potentiel d’étapes de configuration nécessaires. Cette valeur diminue à mesure que la taille du cluster augmente.

### <a name="disadvantages-of-switchless-connections"></a>Inconvénients des connexions sans commutateur

- Plus le nombre de nœuds du cluster augmente, plus le coût des cartes réseau risque de dépasser le coût lié au recours à des commutateurs réseau.
- Une planification supplémentaire est nécessaire pour les schémas d’adressage IP et de sous-réseau.
- Ces connexions n’offrent qu’un accès au stockage local. Ni le trafic des machines virtuelles, ni le trafic de gestion, ni le reste du trafic nécessitant un accès Nord-Sud ne peuvent utiliser ces adaptateurs.
- Ces connexions n’évoluent généralement pas bien au-delà de clusters à trois nœuds.

## <a name="next-steps"></a>Étapes suivantes

- Pour d’informations sur les exigences liées aux cartes réseau et aux hôtes, consultez [Exigences liées aux réseaux d’hôtes](host-network-requirements.md).
- Rafraîchissez votre connaissance des concepts de base du clustering de basculement. Consultez [Notions de base sur les réseaux de clustering de basculement](https://techcommunity.microsoft.com/t5/failover-clustering/.failover-clustering-networking-basics-and-fundamentals/ba-p/1706005?s=09).
- Rafraîchissez vos connaissances de l’utilisation de SET. Consultez [Accès direct à la mémoire à distance (RDMA) et SET (Switch Embedded Teaming)](https://docs.microsoft.com/windows-server/virtualization/.hyper-v-virtual-switch/rdma-and-switch-embedded-teaming).
- Pour le déploiement, consultez [Création d’un cluster avec Windows Admin Center](https://docs.microsoft.com/azure-stack/hci/deploy/create-cluster).
- Pour le déploiement, consultez [Création d’un cluster avec Windows PowerShell](https://docs.microsoft.com/azure-stack/hci/deploy/create-cluster-powershell).
