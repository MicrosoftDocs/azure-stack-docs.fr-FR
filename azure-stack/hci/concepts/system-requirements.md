---
title: Configuration requise pour Azure Stack HCI
description: Apprenez à choisir les serveurs, le stockage et les composants réseau d'Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/29/2020
ms.openlocfilehash: 2d4aebd0609dff744f4c8f6ae9113fba1b7b0ba9
ms.sourcegitcommit: 26901a61a44390bc9b7804c22018c213036e680d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96354154"
---
# <a name="system-requirements-for-azure-stack-hci"></a>Configuration requise pour Azure Stack HCI

> S’applique à : Azure Stack HCI, version 20H2

Utilisez cette rubrique afin d'évaluer la configuration requise pour les serveurs, le stockage et la mise en réseau d'Azure Stack HCI.

## <a name="server-requirements"></a>Configuration requise au niveau du serveur

Un cluster Azure Stack HCI standard requiert un minimum de deux serveurs et un maximum de 16 serveurs. Toutefois, les clusters peuvent être combinés à l'aide d'ensembles de clusters pour créer une plateforme HCI contenant des centaines de nœuds.

Tenez compte de ce qui suit pour les différents types de déploiements Azure Stack HCI :

- Les clusters étendus requièrent le déploiement de serveurs sur deux sites distincts. Les sites peuvent se trouver dans différents pays, dans différentes villes, à différents étages ou dans différentes pièces. Un cluster étendu nécessite un minimum de 4 serveurs (2 par site) et un maximum de 16 serveurs (8 par site).

- Il est recommandé que tous les serveurs soient du même modèle et proviennent du même fabricant, qu’ils utilisent des processeurs compatibles Intel Nehalem, de classe AMD EPYC 64 bits ou ultérieur avec la traduction d’adresses de deuxième niveau (SLAT). Un processeur Intel Xeon évolutif de 2e génération est nécessaire pour prendre en charge la mémoire persistante Intel Optane DC. Les processeurs doivent avoir au moins 1,4 GHz et être compatibles avec le jeu d’instructions x64.

- Veillez à ce que les serveurs disposent d’au moins 32 Go de RAM par nœud pour s’adapter au système d’exploitation du serveur, aux machines virtuelles et à d’autres applications ou charges de travail. En outre, autorisez 4 Go de RAM par téraoctet (To) de capacité de lecteur de cache sur chaque serveur pour les métadonnées des espaces de stockage direct.

- Vérifiez que la prise en charge de la virtualisation est activée dans le BIOS ou l’UEFI :
    - Assistance matérielle à la virtualisation. Cette virtualisation est disponible avec les processeurs qui incluent une option de virtualisation, plus spécifiquement les processeurs dotés de la technologie Intel VT (Intel Virtualization Technology) ou AMD-V (AMD Virtualization).
    - La prévention de l’exécution des données (DEP) appliquée par le matériel doit être disponible et activée. Pour les systèmes Intel, il s’agit du composant XD Bit (Execute Disable Bit). Pour les systèmes AMD, il s’agit du composant NX Bit (No Execute Bit).

- Vous pouvez utiliser n’importe quel périphérique de démarrage pris en charge par Windows Server, qui [contient à présent SATADOM](https://cloudblogs.microsoft.com/windowsserver/2017/08/30/announcing-support-for-satadom-boot-drives-in-windows-server-2016/). Le miroir RAID 1 n’est **pas** obligatoire, mais il est pris en charge pour le démarrage. Une taille minimale de 200 Go est recommandée.

- Pour obtenir des spécifications supplémentaires relatives aux fonctionnalités pour Hyper-V, consultez [Configuration système requise pour Hyper-V sur Windows Server](/windows-server/virtualization/hyper-v/system-requirements-for-hyper-v-on-windows).

## <a name="storage-requirements"></a>Exigences de stockage

Azure Stack HCI fonctionne avec les lecteurs SATA, SAS, NVMe ou à mémoire persistante en attachement direct qui sont physiquement attachés à un seul serveur chacun.

Pour obtenir les meilleurs résultats, respectez ce qui suit :

- Chaque serveur de cluster doit présenter les mêmes types de lecteurs et le même nombre de chaque type. Il est également recommandé (mais pas obligatoire) d’opter pour des lecteurs de même taille et de même modèle. Les lecteurs peuvent être internes au serveur ou dans un boîtier externe connecté à un seul serveur. Pour plus d’informations, consultez la page sur les [Considérations relatives à la symétrie de lecteur](drive-symmetry-considerations.md).

- Chaque serveur du cluster doit avoir des volumes dédiés pour les journaux, avec un stockage de journal au moins aussi rapide que le stockage de données. Les clusters étendus nécessitent au moins deux volumes : l’un pour les données répliquées et l’autre pour les données de journal.

- SCSI Enclosure Services (SES) est requis pour le mappage et l’identification des emplacements. Chaque boîtier externe doit présenter un identificateur unique (ID unique). 

   > [!IMPORTANT]
   > **NON PRIS EN CHARGE :** Les cartes de contrôleur RAID ou le stockage SAN (Fibre Channel, iSCSI, FCoE), les boîtiers SAS partagés connectés à plusieurs serveurs, ou toute forme de MPIO (Multipath IO) où les lecteurs sont accessibles par plusieurs chemins d’accès. Les cartes HBA doivent implémenter le mode de transfert simple.

## <a name="networking-requirements"></a>Configuration requise du réseau

Un cluster Azure Stack HCI requiert une connexion réseau fiable à bande passante élevée et à faible latence entre chaque nœud serveur.

- Vérifiez qu’au moins une carte réseau est disponible et dédiée à la gestion du cluster.
- Vérifiez que les commutateurs physiques de votre réseau sont configurés pour autoriser le trafic sur tous les réseaux locaux virtuels que vous allez utiliser.

Il existe plusieurs types de communication qui transitent entre les nœuds serveur :

- Communication entre les clusters (jointures de nœuds, mises à jour de cluster, mises à jour du registre)
- Pulsations de cluster
- Trafic redirigé du Volume partagé de cluster (CSV)
- Trafic de migration dynamique pour les machines virtuelles

Avec les espaces de stockage direct, il existe un trafic réseau supplémentaire à prendre en compte :

- Storage Bus Layer (SBL) : étendues, ou données, réparties entre les nœuds
- Intégrité : surveillance et gestion des objets (nœuds, lecteurs, cartes réseau, service de cluster)

Pour les clusters étendus, le trafic du réplica de stockage supplémentaire est également transmis entre les sites. Le trafic Storage Bus Layer (SBL) et de Volume partagé de cluster (CSV) ne passe pas entre les sites, uniquement entre les nœuds serveur de chaque site.

Pour plus d’informations sur la mise en réseau d’hôtes et les exigences associées, consultez [Exigences liées aux réseaux d’hôtes](host-network-requirements.md).

## <a name="software-defined-networking-sdn-requirements"></a>Configuration requise pour la mise en réseau SDN (Software Defined Networking)

Lorsque vous créez un cluster Azure Stack HCI à l'aide de Windows Admin Center, vous avez la possibilité de déployer le contrôleur de réseau pour activer la mise en réseau SDN (Software Defined Networking). Si vous envisagez d’utiliser SDN sur Azure Stack HCI :

- Assurez-vous que les serveurs hôtes disposent d’au moins 50-100 Go d’espace libre pour créer les machines virtuelles du contrôleur de réseau.

- Vous devez copier un disque dur virtuel (VHD) du système d’exploitation Azure Stack HCI sur le premier nœud du cluster afin de créer les machines virtuelles du contrôleur de réseau. Vous pouvez préparer le disque dur virtuel à l’aide de [Sysprep](/windows-hardware/manufacture/desktop/sysprep-process-overview) ou en exécutant le script [Convert-WindowsImage](https://gallery.technet.microsoft.com/scriptcenter/Convert-WindowsImageps1-0fe23a8f) pour convertir un fichier .iso en un disque dur virtuel.

Pour plus d’informations sur la préparation de l’utilisation de SDN dans Azure Stack HCI, consultez [Planifier une infrastructure réseau à définition logicielle](plan-software-defined-networking-infrastructure.md) et [Planifier le déploiement de contrôleur de réseau](../concepts/network-controller.md).

   > [!NOTE]
   > SDN n’est pas pris en charge sur les clusters étirés (sur plusieurs sites).

### <a name="sdn-hardware-requirements"></a>Configuration matérielle requise pour la mise en réseau SDN

Cette section décrit la configuration matérielle requise pour les cartes réseau et les commutateurs physiques lors de la planification d'un environnement SDN.

#### <a name="network-interface-cards-nics"></a>Cartes d’interface réseau

Les cartes réseau que vous utilisez dans vos ordinateurs hôtes Hyper-V et ordinateurs hôtes de stockage requièrent des fonctionnalités spécifiques pour atteindre des performances optimales.

L’accès direct à la mémoire à distance (Remote Direct Memory Access, RDMA) est une technique de contournement du noyau qui permet de transférer de grandes quantités de données sans utiliser le processeur de l’ordinateur hôte, ce qui a pour effet de libérer celui-ci pour d’autres tâches. L’association commutée/intégrée (Switched-Embedded Teaming, SET) est une autre solution d’association de NIC que vous pouvez utiliser dans des environnements incluant Hyper-V et la pile de réseau défini par logiciel. La technologie SET intègre certaines fonctionnalités d’association de NIC dans le commutateur virtuel Hyper-V.

Pour plus d’informations, consultez [Accès direct à la mémoire à distance (Remote Direct Memory Access, RDMA) et association commutée/intégrée (Switched-Embedded Teaming, SET)](/windows-server/virtualization/hyper-v-virtual-switch/rdma-and-switch-embedded-teaming).

Pour tenir compte de la surcharge de trafic de réseau virtuel du client occasionnée par les en-têtes d’encapsulation VXLAN ou NVGRE, l’unité de transmission maximale (MTU) du réseau de structure de couche 2 (commutateurs et hôtes) doit être définie sur une valeur supérieure ou égale à 1674 octets \(incluant les en-têtes Ethernet de couche 2\).

Les NIC qui prennent en charge le nouveau mot clé de carte avancé *EncapOverhead* définissent automatiquement la MTU via l’agent hôte du contrôleur de réseau. Les cartes réseau qui ne prennent pas en charge le nouveau mot clé *EncapOverhead* doivent définir la taille de MTU manuellement sur chaque hôte physique à l’aide du mot clé *JumboPacket* \(ou de son équivalent\).

#### <a name="switches-and-routers"></a>Commutateurs et routeurs

Lorsque vous sélectionnez un commutateur et un routeur physiques pour votre environnement SDN, assurez-vous qu'ils prennent en charge les fonctionnalités suivantes :
- Paramètres de MTU de port commuté \(requis\)
- MTU définie sur >= 1674 octets \(Y compris l’en-tête L2-Ethernet\)
- Protocoles L3 \(requis\)
- Routage multichemin à coût égal (Equal-Cost Multi-Path, ECMP)
- ECMP basé sur BGP \(IETF RFC 4271\)\-

Les implémentations doivent prendre en charge les instructions MUST dans les normes IETF suivantes :
- RFC 2545 : [Extensions multiprotocoles BGP-4 pour le routage inter-domaines IPv6](https://tools.ietf.org/html/rfc2545)
- RFC 4760 : [Extensions multiprotocoles pour BGP-4](https://tools.ietf.org/html/rfc4760)
- RFC 4893 : [Prise en charge de BGP pour l’espace de numéro AS de quatre octets](https://tools.ietf.org/html/rfc4893)
- RFC 4456 : [Réflexion d’itinéraire BGP : alternative au BGP interne de maille complète (Internal BGP, IBGP)](https://tools.ietf.org/html/rfc4456)
- RFC 4724 : [Mécanisme de redémarrage correct pour BGP](https://tools.ietf.org/html/rfc4724)

Les protocoles d’étiquetage suivants sont requis :
- VLAN – Isolation des différents types de trafic
- Tronc 802.1q

Les éléments suivants fournissent un contrôle de lien :
- Qualité de service \(QoS\) \(Contrôle de flux basé sur la priorité (Priority Flow Control, PFC) requis uniquement en cas d’utilisation de RoCE\)
- Sélection du trafic améliorée \(802.1Qaz\)
- Contrôle de flux basé sur la priorité (Priority Flow Control, PFC) \(802.1p/Q and 802.1Qbb\)

Les éléments suivants assurent la disponibilité et la redondance :
- Disponibilité du commutateur (obligatoire)
- Un routeur hautement disponible est requis pour exécuter les fonctions de passerelle. Vous pouvez implémenter cela à l’aide d’un commutateur\routeur multichâssis ou de technologies telles que le protocole VRRP (Virtual Router Redundancy Protocol).

### <a name="domain-requirements"></a>Exigences relatives aux domaines

Il n’existe aucune exigence particulière au niveau fonctionnel du domaine pour Azure Stack HCI : il suffit d’une version du système d’exploitation pour votre contrôleur de domaine qui est toujours prise en charge. Si vous ne l’avez pas déjà fait, nous vous recommandons d’activer la fonctionnalité de Corbeille Active Directory. Pour en savoir plus, consultez [Présentation d'Active Directory Domain Services](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

## <a name="maximum-supported-hardware-specifications"></a>Spécifications matérielles maximales prises en charge

Les déploiements Azure Stack HCI qui dépassent les spécifications suivantes ne sont pas pris en charge :

| Ressource                     | Maximale |
| ---------------------------- | --------|
| Serveurs physiques par cluster | 16      |
| Machines virtuelles par hôte                 | 1 024   |
| Disques par machine virtuelle (SCSI)          | 256     |
| Stockage par cluster          | 4 Po    |
| Stockage par serveur           | 400 To  |
| Processeurs logiques par hôte  | 512     |
| RAM par hôte                 | 24 To   |
| RAM par machine virtuelle                   | 12 To (machine virtuelle de 2e génération) ou 1 To (1e génération)|
| Processeurs virtuels par hôte  | 2 048   |
| Processeurs virtuels par machine virtuelle    | 240 (machine virtuelle de 2e génération) ou 64 (1e génération)|

## <a name="next-steps"></a>Étapes suivantes

Pour consulter des informations connexes, reportez-vous également à :

- [Choisir des lecteurs](choose-drives.md)
- [Configuration matérielle requise pour les espaces de stockage direct](/windows-server/storage/storage-spaces/storage-spaces-direct-hardware-requirements)
