---
title: Planifier une infrastructure de réseau défini par logiciel
description: Cette rubrique fournit des informations sur la planification d’un déploiement d’infrastructure de réseau défini par logiciel.
manager: grcusanz
ms.topic: conceptual
ms.assetid: ea7e53c8-11ec-410b-b287-897c7aaafb13
ms.author: anpaul
author: AnirbanPaul
ms.date: 10/16/2020
ms.openlocfilehash: 6df469fcc6997b1f56a552bc141692c7a8a49808
ms.sourcegitcommit: 301e571626f8e85556d9eabee3f385d0b81fdef4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92157680"
---
# <a name="plan-a-software-defined-network-infrastructure"></a>Planifier une infrastructure de réseau défini par logiciel

>S’applique à : Azure Stack HCI version 20H2, Windows Server 2019, Windows Server (canal semi-annuel), Windows Server 2016

Apprenez-en davantage sur la planification du déploiement d’une infrastructure de réseau défini par logiciel, notamment sur les conditions matérielles et logicielles préalables requises. Cette rubrique décrit les exigences de planification pour la configuration physique et logique du réseau, le routage, les passerelles, le matériel réseau, et bien plus. Elle aborde également des considérations relatives à l’extension d’une infrastructure de réseau défini par logiciel et à l’utilisation d’un déploiement par phases.

   > [!NOTE]
   > SDN n’est pas pris en charge sur les clusters étirés (sur plusieurs sites).

## <a name="prerequisites"></a>Prérequis
Une infrastructure de réseau défini par logiciel requiert plusieurs conditions matérielles et logicielles préalables, à savoir :
- **Groupes de sécurité et inscription dynamique de réseau défini par logiciel** . Vous devez préparer votre centre de données au déploiement du contrôleur de réseau qui nécessite un ensemble de machines virtuelles. Avant de déployer le contrôleur de réseau, vous devez configurer des groupes de sécurité et une inscription dynamique de réseau défini par logiciel.

    Pour en savoir plus sur le déploiement du contrôleur de réseau pour votre centre de données, consultez [Configuration requise pour le déploiement du contrôleur de réseau](/windows-server/networking/sdn/plan/installation-and-preparation-requirements-for-deploying-network-controller).

- **Réseau physique** . Pour configurer les réseaux locaux virtuels (VLAN), le routage et le protocole BGP (Border Gateway Protocol), vous devez avoir accès à vos périphériques réseau physiques. Cette rubrique contient des instructions pour la configuration manuelle du commutateur, et le choix entre une homologation BGP via des commutateurs/routeurs de couche 3 et une machine virtuelle faisant office de serveur de routage et d’accès distant (RRAS).

- **Ordinateurs hôtes de calcul physique** . Ces ordinateurs hôtes exécutent la technologie Hyper-V, et sont requis pour héberger l’infrastructure de réseau défini par logiciel et les machines virtuelles de locataire. Pour permettre à ces ordinateurs hôtes de produire des performances optimales, un matériel réseau spécifique est requis, comme décrit dans la section [Matériel réseau](#network-hardware).

## <a name="physical-and-logical-network-configuration"></a>Configuration physique et logique du réseau
Chaque hôte de calcul physique requiert une connexion réseau via une ou plusieurs cartes réseau connectées à un port commuté physique. Un [réseau local virtuel (VLAN)](https://en.wikipedia.org/wiki/Virtual_LAN) de couche 2 prend en charge des réseaux divisés en plusieurs segments de réseau logique.

>[!TIP]
>Utilisez un VLAN 0 pour les réseaux logiques en mode d’accès ou non étiqueté.

>[!IMPORTANT]
>Une mise en réseau définie par le logiciel Windows Server 2016 prend en charge l’adressage IPv4 pour la sous-couche et la surcouche. IPv6 n’est pas pris en charge. Windows Server 2019 prend en charge les adressages IPv4 et IPv6.

### <a name="logical-networks"></a>Logical networks
Cette section traite des exigences de planification de l’infrastructure de réseau défini par logiciel pour le réseau logique de gestion et le réseau logique du fournisseur de virtualisation de réseau Hyper-V (HNV). Elle comprend des détails sur l’approvisionnement de réseaux logiques supplémentaires pour l’utilisation de passerelles et d’un équilibreur de charge logiciel (Software Load Balancer, SLB), ainsi qu’un exemple de topologie de réseau.

#### <a name="management-and-hnv-provider"></a>Fournisseurs de gestion et de HNV
Tous les hôtes de calcul physique doivent accéder au réseau logique de gestion et au réseau logique du fournisseur de HNV. Pour la planification des adresses IP, chaque ordinateur hôte de calcul physique doit avoir au moins une adresse IP affectée à partir du réseau logique de gestion. Le contrôleur de réseau a besoin d’une adresse IP réservée de ce réseau pour servir d’adresse IP REST (Representational State Transfer).

Le réseau du fournisseur de HNV fait office de réseau physique sous-jacent pour le trafic de locataire est/ouest (interne-interne), le trafic de locataire nord/sud (interne-externe), ainsi que pour l’échange d’informations d’homologation BGP avec le réseau physique.

Un serveur DHCP peut attribuer automatiquement des adresses IP pour le réseau de gestion, ou vous pouvez attribuer manuellement des adresses IP statiques. La pile de réseau défini par logiciel affecte automatiquement des adresses IP pour le réseau logique du fournisseur de HNV pour les ordinateurs hôtes Hyper-V individuels à partir d’un pool d’adresses IP. Le contrôleur de réseau spécifie et gère le pool d’adresses IP.

>[!NOTE]
>Le contrôleur de réseau affecte une adresse IP de fournisseur de HNV à un hôte de calcul physique uniquement après que l’agent hôte du contrôleur de réseau a reçu la stratégie réseau pour une machine virtuelle locataire spécifique.

| Si…                                                    | Alors…                                               |
| :------------------------------------------------------- | :---------------------------------------------------- |
| Les réseaux logiques utilisent des VLAN,                          | l’hôte de calcul physique doit se connecter à un port commuté relié en mode trunk ayant accès aux VLAN. Il est important de noter que le filtrage VLAN ne soit pas être activé sur les cartes réseau physiques de l’ordinateur hôte.|
| Vous utilisez une association commutée/intégrée (Switched-Embedded Teaming, SET) et disposez de plusieurs membres d’association de type carte d’interface réseau (Network Interface Card, NIC), tels que des cartes réseau,| vous devez connecter tous les membres d’association NIC pour cet ordinateur hôte particulier au même domaine de diffusion de couche 2.|
| L’hôte de calcul physique exécute des machines virtuelles d’infrastructure supplémentaires, telles qu’un contrôleur de réseau, le SLB/MUX ou une passerelle, | assurez-vous que le réseau logique de gestion dispose de suffisamment d’adresses IP pour chaque machine virtuelle hébergée. Assurez-vous également que le réseau logique du fournisseur de HNV dispose de suffisamment d’adresses IP à allouer aux machines virtuelles de l’infrastructure SLB/MUX et de passerelle. Bien que la réservation IP soit gérée par le contrôleur de réseau, l’échec de la réservation d’une nouvelle adresse IP en raison d’une indisponibilité peut entraîner des adresses IP en double sur votre réseau.|

Pour plus d’informations sur la virtualisation de réseau Hyper-V (HNV) que vous pouvez utiliser pour virtualiser des réseaux dans un déploiement de réseau défini par logiciel Microsoft, consultez [Virtualisation de réseau Hyper-V](/windows-server/networking/sdn/technologies/hyper-v-network-virtualization/hyper-v-network-virtualization).

#### <a name="gateways-and-the-software-load-balancer-slb"></a>Passerelles et équilibreur de charge logiciel (SLB)
Vous devez créer et approvisionner des réseaux logiques supplémentaires pour utiliser les passerelles et le SLB. Veillez à obtenir les préfixes d’adresse IP, ID de VLAN et adresses IP de passerelle appropriés pour ces réseaux.

|                                |                     |
| :----------------------------- | :------------------ |
| **Réseau logique d’adresses IP virtuelles publiques** | Le réseau logique d’adresses IP virtuelles publiques doit utiliser des préfixes de sous-réseau IP routables en dehors de l’environnement cloud (généralement routables sur Internet). Il s’agit des adresses IP frontales utilisées par les clients externes pour accéder aux ressources des réseaux virtuels, dont l’adresse IP virtuelle frontale pour la passerelle site à site. Vous n’avez pas besoin d’attribuer un VLAN à ce réseau. |
| **Réseau logique d’adresses IP virtuelles privées** | Le réseau logique d’adresses IP virtuelles privées ne doit pas obligatoirement être routable en dehors du cloud. Cela est dû au fait que seules des adresses IP virtuelles accessibles à partir de clients de cloud interne l’utilisent, par exemple des services privés. Vous n’avez pas besoin d’attribuer un VLAN à ce réseau. |
| **Réseau logique d’adresses IP virtuelles d’encapsulation générique de routage (GRE)** | Le réseau d’adresses IP virtuelles VIP d’encapsulation générique de routage (GRE) est un sous-réseau qui existe uniquement pour définir des adresses IP virtuelles. Les adresses IP virtuelles sont attribuées à des machines virtuelles de passerelle s’exécutant sur votre structure de réseau défini par logiciel pour un type de connexion GRE site à site (S2S). Vous n’avez pas besoin de préconfigurer ce réseau dans vos commutateurs ou routeurs physiques, ou de lui attribuer un VLAN. |

#### <a name="sample-network-topology"></a>Exemple de topologie de réseau
Modifiez les exemples de préfixes de sous-réseau IP et d’ID de VLAN pour votre environnement.

| Nom du réseau | Subnet | Mask | ID du réseau local virtuel en mode trunk | Passerelle | Réservation (exemples) |
| :----------------------- | :------------ | :------- | :---------------------------- | :-------------- | :------------------------------------------- |
| Gestion              | 10.184.108.0 |    24   |          7                   | 10.184.108.1   | 10.184.108.1 – Routeur<br> 10.184.108.4 – Contrôleur de réseau<br> 10.184.108.10 – Hôte de calcul 1<br> 10.184.108.11 – Hôte de calcul 2<br> 10.184.108.X – Hôte de calcul X |
| Fournisseur de HNV             |  10.10.56.0  |    23    |          11                |  10.10.56.1    | 10.10.56.1 – Routeur<br> 10.10.56.2 – SLB/MUX1<br> 10.10.56.5 – Passerelle1 |
| Adresse IP virtuelle publique               |  41.40.40.0  |    27    |          NA                |  41.40.40.1    | 41.40.40.1 – Routeur<br> 41.40.40.3 – Adresse IP virtuelle de connexion de site à site IPSec |
| Adresse IP virtuelle privée              |  20.20.20.0  |    27    |          NA                |  20.20.20.1    | 20.20.20.1 – GW par défaut (routeur) |
| Adresse IP virtuelle GRE                  |  31.30.30.0  |    24    |          NA                |  31.30.30.1    | 31.30.30.1 – GW par défaut |

## <a name="routing-infrastructure"></a>Infrastructure de routage
Les informations de routage \(telles que le tronçon suivant\) pour les sous-réseaux d’adresses IP virtuelles sont publiées par les passerelles SLB/MUX et de serveur d’accès à distance (Remote Access Server, RAS) dans le réseau physique à l’aide d’une homologation BGP interne. Les réseaux logiques d’adresses IP virtuelles n’ont pas de VLAN attribué et ne sont pas préconfigurés dans le commutateur de couche 2 (par exemple, le commutateur TOR).

Vous devez créer un homologue BGP sur le routeur qu’utilise votre infrastructure de réseau défini par logiciel pour recevoir des itinéraires pour les réseaux logiques d’adresses IP virtuelles publiés par les passerelles SLB/MUX et RAS. L’homologation BGP ne doit avoir lieu que dans un sens (de la passerelle SLB/MUX ou RAS vers l’homologue BGP externe). Au-dessus de la première couche de routage, vous pouvez utiliser des itinéraires statiques ou un autre protocole de routage dynamique, tel que OSPF (Open Shortest Path First). Toutefois, comme indiqué précédemment, le préfixe de sous-réseau IP pour les réseaux logiques d’adresses IP virtuelles doit être routable à partir du réseau physique vers l’homologue BGP externe.

L’homologation BGP est généralement configurée dans un commutateur ou un routeur managé dans le cadre de l’infrastructure réseau. L’homologue BGP peut également être configuré sur un serveur Windows Server avec le rôle RAS installé en mode Routage uniquement. L’homologue de routeur BGP dans l’infrastructure réseau doit être configuré pour utiliser ses propres numéros de système autonome (ASN) et autoriser l’homologation à partir d’un ASN affecté aux composants de réseau défini par logiciel \(passerelles SLB/MUX et RAS\).

Vous devez obtenir les informations suivantes de votre routeur physique ou de l’administrateur réseau contrôlant celui-ci :
- ASN du routeur
- Adresse IP du routeur

>[!NOTE]
>Les ASN de quatre octets ne sont pas pris en charge par le SLB/MUX. Vous devez allouer des ASN de deux octets au SLB/MUX et au routeur auquel il se connecte. Vous pouvez utiliser des ASN à quatre octets ailleurs dans votre environnement.

Vous ou votre administrateur réseau devez configurer l’homologue de routeur BGP pour qu’il accepte les connexions à partir de l’adresse IP ou de l’adresse de sous-réseau du réseau logique du fournisseur de HNV que votre passerelle RAS et vos SLB/MUX utilisent.

Pour plus d’informations, consultez [Border Gateway Protocol (BGP)](/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp).

## <a name="default-gateways"></a>Passerelles par défaut
Les machines configurées pour se connecter à plusieurs réseaux, telles que les hôtes physiques, les SLB/MUX et les machines virtuelles de passerelle, ne doivent avoir qu’une seule passerelle par défaut configurée. Utilisez les passerelles par défaut suivantes pour les ordinateurs hôtes et les machines virtuelles d’infrastructure :
- Pour des ordinateurs hôtes Hyper-V, utilisez le réseau de gestion comme passerelle par défaut.
- Pour des machines virtuelles de contrôleur de réseau, utilisez le réseau de gestion comme passerelle par défaut.
- Pour des machines virtuelles SLB/MUX, utilisez le réseau de gestion comme passerelle par défaut.
- Pour des machines virtuelles de passerelle, utilisez le réseau du fournisseur de HNV comme passerelle par défaut. Cette valeur doit être définie sur la carte réseau frontale des machines virtuelles de passerelle.

## <a name="network-hardware"></a>Matériel réseau
Cette section décrit la configuration requise de déploiement de matériel réseau pour les cartes réseau et les commutateurs physiques.

### <a name="network-interface-cards-nics"></a>Cartes d’interface réseau (NIC)
Les cartes réseau que vous utilisez dans vos ordinateurs hôtes Hyper-V et ordinateurs hôtes de stockage requièrent des fonctionnalités spécifiques pour atteindre des performances optimales.

L’accès direct à la mémoire à distance (Remote Direct Memory Access, RDMA) est une technique de contournement du noyau qui permet de transférer de grandes quantités de données sans utiliser le processeur de l’ordinateur hôte, ce qui a pour effet de libérer celui-ci pour d’autres tâches. L’association commutée/intégrée (Switched-Embedded Teaming, SET) est une autre solution d’association de NIC que vous pouvez utiliser dans des environnements incluant Hyper-V et la pile de réseau défini par logiciel. La technologie SET intègre certaines fonctionnalités d’association de NIC dans le commutateur virtuel Hyper-V.

Pour plus d’informations, consultez [Accès direct à la mémoire à distance (Remote Direct Memory Access, RDMA) et association commutée/intégrée (Switched-Embedded Teaming, SET)](/windows-server/virtualization/hyper-v-virtual-switch/rdma-and-switch-embedded-teaming).

Pour tenir compte de la surcharge de trafic de réseau virtuel du client occasionnée par les en-têtes d’encapsulation VXLAN ou NVGRE, l’unité de transmission maximale (MTU) du réseau de structure de couche 2 (commutateurs et hôtes) doit être définie sur une valeur supérieure ou égale à 1674 octets \(incluant les en-têtes Ethernet de couche 2\).

Les NIC qui prennent en charge le nouveau mot clé de carte avancé *EncapOverhead* définissent automatiquement la MTU via l’agent hôte du contrôleur de réseau. Les cartes réseau qui ne prennent pas en charge le nouveau mot clé *EncapOverhead* doivent définir la taille de MTU manuellement sur chaque hôte physique à l’aide du mot clé *JumboPacket* \(ou de son équivalent\).

### <a name="switches"></a>Commutateurs
Lorsque vous sélectionnez un commutateur et un routeur physiques pour votre environnement, assurez-vous qu’il prennent en charge les fonctionnalités suivantes :
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

### <a name="switch-configuration-examples"></a>Exemples de configuration de commutateur
Pour faciliter la configuration de votre commutateur ou routeur physique, un ensemble d’exemples de fichiers de configuration pour un éventail de modèles de commutateur et de fournisseurs est disponible dans le [Dépôt GitHub pour réseau défini par logiciel Microsoft](https://github.com/microsoft/SDN/tree/master/SwitchConfigExamples). Un fichier Lisez-moi détaillé et des commandes d’interface de ligne de commande (CLI) testées pour des commutateurs spécifiques sont fournis.

## <a name="compute"></a>Calcul
Le système d’exploitation approprié doit être installé sur tous les ordinateurs hôtes Hyper-V, être activé pour Hyper-V et utiliser un commutateur virtuel Hyper-V externe avec au moins une carte physique connectée au réseau logique de gestion. L’hôte doit être accessible via une adresse IP de gestion affectée à la vNIC de l’hôte de gestion.

Vous pouvez utiliser n’importe quel type de stockage compatible avec Hyper-V, partagé ou local.

> [!TIP]
> Il est pratique d’utiliser le même nom pour tous vos commutateurs virtuels, mais ce n’est pas obligatoire. Si vous envisagez d’utiliser des scripts pour le déploiement, consultez le commentaire associé à la variable `vSwitchName` dans le fichier config.psd1.

### <a name="host-compute-requirements"></a>Exigences de calcul de l’ordinateur hôte
La configuration matérielle et logicielle minimale requise pour les quatre hôtes physiques utilisés dans l’exemple de déploiement est illustrée ci-dessous.

Hôte|Configuration matérielle requise|Configuration logicielle requise|
--------|-------------------------|-------------------------
|Hôte Hyper-V physique|Processeur quadruple cœur, 2,66 GHz<br> 32 Go de RAM<br> 300 Go d’espace disque<br> Carte réseau physique 1 Go/s (ou plus rapide)|Système d’exploitation : Comme défini dans<br> « S’applique à » au début de cette rubrique.<br> Rôle Hyper-V installé|

### <a name="sdn-infrastructure-vm-role-requirements"></a>Configuration requise pour le rôle de machine virtuelle d’infrastructure de réseau défini par logiciel
Voici la configuration requise pour les rôles de machine virtuelle.

Rôle|Configuration requise pour les processeurs virtuels|Besoins en mémoire|Configuration requise pour le disque|
--------|-----------------------------|-----------------------|--------------------------
|Contrôleur de réseau (trois nœuds)|4 processeurs virtuels|4 Go minimum<br> (8 Go recommandés)|75 Go pour le lecteur de système d’exploitation
|SLB/MUX (trois nœuds)|8 processeurs virtuels|8 Go recommandés|75 Go pour le lecteur de système d’exploitation
|Passerelle du serveur d’accès à distance<br> (un seul pool de trois passerelles<br> de nœud, deux actives et une passive)|8 processeurs virtuels|8 Go recommandés|75 Go pour le lecteur de système d’exploitation
|Routeur BGP de passerelle RAS<br> pour l’homologation SLB/MUX<br> (vous pouvez également utiliser le commutateur TOR<br> en tant que routeur BGP)|2 processeurs virtuels|2 Go|75 Go pour le lecteur de système d’exploitation|

Si vous utilisez System Center – Virtual Machine Manager (VMM) pour le déploiement, des ressources de machine virtuelle d’infrastructure supplémentaires sont requises pour VMM et des infrastructures autres que de réseau défini par logiciel. Pour en savoir plus, consultez [Configuration système requise pour System Center Virtual Machine Manager](/system-center/vmm/system-requirements?preserve-view=true&view=sc-vmm-2019).

## <a name="extending-your-infrastructure"></a>Extension de votre infrastructure
La configuration requise en termes de dimensionnement et de ressources de votre infrastructure dépend des machines virtuelles de charge de travail du locataire que vous envisagez d’héberger. La configuration requise pour le processeur, la mémoire et le disque pour les machines virtuelles d’infrastructure (par exemple : contrôleur de réseau, SLB, passerelle, etc.) est définie dans le tableau précédent. Vous pouvez ajouter des machines virtuelles d’infrastructure pour la mise à l’échelle en fonction des besoins. Toutefois, les machines virtuelles de locataire s’exécutant sur des hôtes Hyper-V ont leurs propres exigences en matière de processeur, de mémoire et de disque que vous devez prendre en considération.

Lorsque les machines virtuelles de charge de travail du locataire commencent à consommer trop de ressources sur les hôtes Hyper-V physiques, vous pouvez étendre votre infrastructure en ajoutant des hôtes physiques. Vous pouvez utiliser Windows Admin Center, VMM ou des scripts PowerShell pour créer des ressources de serveur via le contrôleur de réseau. La méthode à utiliser dépend de la façon dont vous avez déployé initialement l’infrastructure. Si vous devez ajouter des adresses IP pour le réseau du fournisseur de HNV, vous pouvez créer des sous-réseaux logiques (avec les pools d’adresses IP correspondants) que les hôtes peuvent utiliser.

## <a name="phased-deployment"></a>Déploiement par phases
En fonction de vos besoins, il se peut que vous deviez déployer un sous-ensemble de l’infrastructure SDN. Par exemple, si vous souhaitez uniquement héberger des charges de travail de locataire dans votre centre de données et qu’aucune communication externe n’est requise, vous pouvez déployer un contrôleur de réseau et ignorer le déploiement du SLB/MUX et des machines virtuelles de passerelle. La rubrique suivante décrit la configuration requise de l’infrastructure de fonctionnalités réseau pour un déploiement en phases de l’infrastructure de réseau défini par logiciel.

Fonctionnalité|Conditions requises pour le déploiement|Conditions requises en matière de réseau|
--------|-------------------------|-------------------------
|Gestion de réseau logique<br> Listes de contrôle d’accès (ACL) (pour un réseau VLAN)<br> Qualité de service (QoS) (pour les réseaux VLAN)<br>|Contrôleur réseau|Aucun|
|Réseau virtuel<br> Itinéraire défini par l’utilisateur<br> Listes de contrôle d’accès (pour réseau virtuel)<br> Sous-réseaux chiffrés<br> QoS (pour les réseaux virtuels)<br> Peering de réseau virtuel|Contrôleur réseau|VLAN HNV PA, sous-réseau, routeur|
|NAT entrante/sortante<br> Équilibrage de la charge.|Contrôleur réseau<br> SLB/MUX|BGP sur réseau HNV PA<br> Sous-réseaux d’adresses IP virtuelles privées et publiques|
|Connexions de passerelle GRE|Contrôleur réseau<br> Passerelle|BGP sur réseau HNV PA<br> Sous-réseau d’adresses IP virtuelles GRE|
|Connexions de passerelle IPSec|Contrôleur réseau<br> SLB/MUX<br> Passerelle|BGP sur réseau HNV PA<br> Sous-réseau d’adresses IP virtuelles publiques|
|Connexions de passerelle L3|Contrôleur réseau<br> Passerelle|VLAN de locataire, sous-réseau, routeur<br> BGP sur VLAN de locataire facultatif.|

## <a name="next-steps"></a>Étapes suivantes
Pour consulter des informations connexes, reportez-vous également à :
- [Configuration requise pour le déploiement du contrôleur de réseau](/windows-server/networking/sdn/plan/installation-and-preparation-requirements-for-deploying-network-controller)
- [SDN dans Azure Stack HCI](./software-defined-networking.md)