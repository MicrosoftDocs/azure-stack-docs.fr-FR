---
title: Exigences liées aux réseaux d’hôtes pour Azure Stack HCI
description: Découvrez les exigences liées aux réseaux d’hôtes pour Azure Stack HCI.
author: v-dasis
ms.topic: how-to
ms.date: 11/25/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 3e31852e554c85ffab18aacaa336a007a97874f2
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98255297"
---
# <a name="host-network-requirements-for-azure-stack-hci"></a>Exigences liées aux réseaux d’hôtes pour Azure Stack HCI

> S’applique à Azure Stack HCI, version 20H2

Cette rubrique décrit la mise en réseau d’hôtes Azure Stack HCI et les exigences liées. Pour plus d’informations sur les architectures de centres de données et les connexions physiques entre les serveurs, consultez [Exigences liées aux réseaux physiques](physical-network-requirements.md).

## <a name="network-traffic-types"></a>Types de trafic réseau

Le trafic réseau Azure Stack HCI peut être classé selon son rôle prévu :

- **Trafic de calcul** : trafic en provenance ou à destination d’une machine virtuelle
- **Trafic de stockage** : trafic destiné aux espaces de stockage direct (S2D) via le protocole SMB (Server Message Block)
- **Trafic de gestion** : trafic important pour un administrateur dans une optique de gestion des clusters, par exemple Active Directory, Bureau à distance, Windows Admin Center et Windows PowerShell.

## <a name="selecting-a-network-adapter"></a>Choix d’une carte réseau

Pour Azure Stack HCI, nous devons choisir une carte réseau qui a obtenu la certification de centre de données défini par logiciel (SDDC) Windows Server avec la qualification supplémentaire (AQ) Standard ou Premium. Ce sont les adaptateurs qui prennent en charge les fonctionnalités les plus avancées de la plateforme et ont subi le plus de tests de la part de nos partenaires matériels. En règle générale, ce niveau de contrôle débouche sur une réduction des problèmes de qualité du matériel et des pilotes.

Pour identifier un adaptateur disposant d’une qualification Standard ou Premium, examinez l’entrée [Windows Server Catalog](https://www.windowsservercatalog.com/) correspondante et la version du système d’exploitation applicable. Voici un exemple de notation de la qualification Premium :

:::image type="content" source="media/plan-networking/windows-certified.png" alt-text="Certifié Windows" lightbox="media/plan-networking/windows-certified.png":::

## <a name="overview-of-key-network-adapter-capabilities"></a>Vue d’ensemble des principales fonctionnalités des cartes réseau

Voici les principales fonctionnalités des cartes réseau exploitées par Azure Stack HCI :

- File d’attente multiple de machines virtuelles dynamique (Dynamic VMMQ, ou d.VMMQ)
- Accès direct à la mémoire à distance (RDMA, Remote Direct Memory Access)
- RDMA invité
- SET (Switch Embedded Teaming)

### <a name="dynamic-vmmq"></a>Dynamic VMMQ

Toutes les cartes réseau disposant de la qualification Premium prennent en charge la technologie Dynamic VMMQ. Cette technologie impose d’utiliser la fonctionnalité Switch Embedded Teaming.

**Types de trafic applicables** : calcul

**Certifications requises** : Premium

Dynamic VMMQ est une technologie intelligente côté récepteur qui s’appuie sur ses prédécesseurs, File d’attente d’ordinateurs virtuels (VMQ), Virtual Receive Side Scaling (vRSS) et VMMQ, pour offrir trois améliorations principales :

- Optimisation de l’efficacité des hôtes à l’aide de cœurs de processeur
- Réglage automatique du traitement du trafic réseau sur les cœurs de processeur, permettant ainsi aux machines virtuelles de respecter et de maintenir le débit attendu
- Possibilité pour les charges de travail « en rafale » de recevoir le volume de trafic attendu

Pour plus d’informations sur la fonctionnalité Dynamic VMMQ, consultez le billet de blog [Accélérations synthétiques](https://techcommunity.microsoft.com/t5/networking-blog/synthetic-accelerations-in-a-nutshell-windows-server-2019/ba-p/653976).

### <a name="rdma"></a>RDMA

La fonctionnalité RDMA consiste en un déchargement de la pile réseau vers la carte réseau permettant au trafic de stockage SMB de contourner le système d’exploitation pour le traitement.

La technologie RDMA permet une mise en réseau à débit élevé et faible latence avec une utilisation minimale des ressources de processeur des hôtes. Ces ressources peuvent alors servir à exécuter des machines virtuelles ou des conteneurs supplémentaires.

**Types de trafic applicables** : stockage des hôtes

**Certifications requises** : Standard

Tous les adaptateurs possédant la qualification Standard ou Premium prennent en charge l’Accès direct à la mémoire à distance (RDMA). Il s’agit du choix de déploiement recommandé pour les charges de travail de stockage d’Azure Stack HCI. Il peut éventuellement être activé pour celles des machines virtuelles (via le protocole SMB). Consultez la section **RDMA invité** plus loin.

Azure Stack HCI prend en charge la fonctionnalité RDMA avec l’implémentation de protocole iWARP ou RoCE.

> [!IMPORTANT]
> Les adaptateurs RDMA fonctionnent uniquement avec d’autres adaptateurs RDMA qui implémentent le même protocole RDMA (iWARP ou RoCE).

Toutes les cartes réseau des fournisseurs ne prennent pas en charge la fonctionnalité RDMA. Le tableau suivant présente les fournisseurs qui proposent des adaptateurs RDMA certifiés Premium (par ordre alphabétique). Toutefois, il existe des fabricants de matériel qui ne figurent pas dans cette liste et prennent également en charge la fonctionnalité RDMA. Pour vérifier la prise en charge RDMA, consultez [Windows Server Catalog](https://www.windowsservercatalog.com/).

> [!NOTE]
> InfiniBand (IB) n’est pas pris en charge avec Azure Stack HCI.

|Fournisseur de cartes réseau|iWARP|RoCE|
|----|----|----|
|Broadcom|Non|Oui|
|Chelsio|Oui|Non|
|Intel|Oui|Oui (certains modèles)|
|Marvell (QLogic/Cavium)|Oui|Oui|
|NVIDIA (Mellanox)|Non|Oui|

> [!NOTE]
> Tous les adaptateurs des fournisseurs ne prennent pas en charge la fonctionnalité RDMA. Vérifiez la prise en charge RDMA avec le fournisseur de votre carte réseau.

Pour plus d’informations sur le déploiement de la fonctionnalité RDMA, téléchargez le document Word sur le [référentiel GitHub SDN](https://github.com/Microsoft/SDN/blob/master/Diagnostics/S2D%20WS2016_ConvergedNIC_Configuration.docx).

#### <a name="internet-wide-area-rdma-protocol-iwarp"></a>Protocole iWARP (Internet Wide Area RDMA Protocol)

iWARP utilise le protocole TCP (Transmission Control Protocol) et peut éventuellement être amélioré avec les standards DCB (Data Center Bridging) PFC (Priority-based Flow Control) et ETS (Enhanced Transmission Service).

Nous vous recommandons d’utiliser le protocole iWARP dans les conditions suivantes :

- Vous avez peu d’expérience réseau (voire n’en avez aucune) ou ne maîtrisez pas la gestion des commutateurs réseau.
- Vous ne contrôlez pas vos commutateurs ToR.
- Ce ne sera pas vous qui gérerez la solution après le déploiement.
- Vous avez déjà effectué des déploiements via le protocole iWARP.
- Vous ne savez pas quelle option choisir.

#### <a name="rdma-over-converged-ethernet-roce"></a>RoCE (RDMA over Converged Ethernet)

Le protocole RoCE utilise le protocole UDP (User Datagram Protocol). Les standards Data Center Bridging PFC et ETS sont nécessaires pour assurer sa fiabilité.

Nous vous recommandons d’utiliser le protocole RoCE dans les conditions suivantes :

- Votre centre de données comporte déjà des déploiements effectués via le protocole RoCE.
- Vous avez connaissance de vos besoins en matière de réseau physique.

### <a name="guest-rdma"></a>RDMA invité

La fonctionnalité RDMA invité permet aux charges de travail SMB pour machines virtuelles de bénéficier des avantages offerts par la technologie RDMA sur les hôtes.

**Types de trafic applicables** : calcul

**Certifications requises** : Premium

 Voici les principaux avantages de la fonctionnalité RDMA invité :

- Déchargement du processeur sur la carte réseau pour le traitement du trafic réseau
- Latence extrêmement faible
- Débit élevé

Pour plus d’informations, notamment sur le déploiement de la fonctionnalité RDMA invité, téléchargez le document Word sur le [référentiel GitHub SDN](https://github.com/Microsoft/SDN/blob/master/Diagnostics/S2D%20WS2016_ConvergedNIC_Configuration.docx).

### <a name="switch-embedded-teaming-set"></a>SET (Switch Embedded Teaming)

Switch Embedded Teaming (SET) est une technologie d’association logicielle incluse dans le système d’exploitation Windows Server depuis Windows Server 2016. Elle n’est pas dépendante du type de carte réseau utilisé.

**Types de trafic applicables** : calcul, stockage et gestion

**Certifications requises :** aucune (intégrée au système d’exploitation)

La fonctionnalité SET est la seule technologie d’association prise en charge par Azure Stack HCI. Autre technologie d’association souvent utilisée avec Windows Server, l’équilibrage de charge/basculement (LBFO) n’est pas pris en charge avec Azure Stack HCI. Pour plus d’informations sur la technologie LBFO dans Azure Stack HCI, consultez le billet de blog [Association dans Azure Stack HCI](https://techcommunity.microsoft.com/t5/networking-blog/teaming-in-azure-stack-hci/ba-p/1070642). La fonctionnalité SET fonctionne bien indifféremment avec le trafic de calcul, de stockage et de gestion.

La technologie SET est importante pour Azure Stack HCI, car il s’agit de la seule technologie d’association qui donne accès aux fonctionnalités suivantes :

- Association d’adaptateurs RDMA (si nécessaire)
- RDMA invité
- Dynamic VMMQ
- Autres fonctionnalités clés d’Azure Stack HCI (cf. [Association dans Azure Stack HCI](https://techcommunity.microsoft.com/t5/networking-blog/teaming-in-azure-stack-hci/ba-p/1070642))

La technologie SET offre des fonctionnalités supplémentaires par rapport à LBFO, notamment des améliorations en matière de qualité et de niveau de performance. Il est pour cela nécessaire d’utiliser des adaptateurs symétriques (identiques) : l’association d’adaptateurs asymétriques n’est pas prise en charge. Les cartes réseau symétriques sont celles qui présentent les mêmes caractéristiques :

- Marque (fournisseur)
- Modèle (version)
- Vitesse (débit)
- configuration

Le moyen le plus simple d’identifier si les adaptateurs sont symétriques est de regarder si la vitesse est identique et si la description de l’interface correspond. Ils ne peuvent différer que par le chiffre indiqué dans la description. Utilisez la cmdlet [`Get-NetAdapterAdvancedProperty`](/powershell/module/netadapter/get-netadapteradvancedproperty) pour vérifier que la configuration signalée présente les mêmes valeurs de propriété.

Dans le tableau suivant figure un exemple de descriptions d’interface qui ne diffèrent que par le chiffre :

|Nom|Description de l'interface|Vitesse de liaison|
|----|----|----|
|NIC1|Carte réseau 1|25 Gbits|
|NIC2|Carte réseau 2|25 Gbits|
|NIC3|Carte réseau 3|25 Gbits|
|NIC4|Carte réseau 4|25 Gbits|

### <a name="rdma-traffic-considerations"></a>Considérations relatives au trafic RDMA

Si vous implémentez les standards DCB (Data Center Bridging), vous devez veiller à ce que la configuration PFC et ETS soit correctement implémentée sur chacun des ports réseau, y compris les commutateurs réseau. Les standards DCB sont obligatoires pour le protocole RoCE et facultatifs pour le protocole iWARP.

Pour plus d’informations sur le déploiement de la fonctionnalité RDMA, téléchargez le document Word sur le [référentiel GitHub SDN](https://github.com/Microsoft/SDN/blob/master/Diagnostics/S2D%20WS2016_ConvergedNIC_Configuration.docx).

Les implémentations d’Azure Stack HCI basées sur le protocole RoCE impliquent la configuration de trois classes de trafic PFC, en comptant la classe de trafic par défaut, sur l’ensemble de la structure et tous les hôtes :

#### <a name="cluster-traffic-class"></a>Classe de trafic de cluster

Cette classe de trafic garantit une bande passante réservée suffisante pour les pulsations de cluster :

- Obligatoire : oui
- Compatible PFC : Non
- Priorité de trafic recommandée : Priorité 7
- Réservation de bande passante recommandée :
    - Réseaux RDMA de 10 GbE ou moins = 2 %
    - Réseaux RDMA de 25 GbE ou plus = 1 %

#### <a name="rdma-traffic-class"></a>Classe de trafic RDMA

Cette classe de trafic garantit une bande passante réservée suffisante pour les communications RDA sans perte avec SMB Direct :

- Obligatoire : oui
- Compatible PFC : Oui
- Priorité de trafic recommandée : Priorité 3 ou 4
- Réservation de bande passante recommandée : 50 %

#### <a name="default-traffic-class"></a>Classe de trafic par défaut

Cette classe de trafic couvre tous les autres types de trafic non définis dans le cluster ni dans les classes de trafic RDMA, notamment le trafic des machines virtuelles et le trafic de gestion :

- Requis : Par défaut (aucune configuration nécessaire sur l’hôte)
- PFC (Priority-based Flow Control) activé : Non
- Classe de trafic recommandée : Par défaut (Priorité 0)
- Réservation de bande passante recommandée : Par défaut (aucune configuration nécessaire sur l’hôte)

## <a name="storage-traffic-models"></a>Modèles de trafic de stockage

En tant que protocole de stockage d’Azure Stack HCI, le protocole SMB offre de nombreux avantages, notamment SMB Multichannel. SMB Multichannel sort du cadre de cette rubrique. Cependant, il est important de comprendre que le trafic est multiplexé sur chacune des liaisons utilisables par SMB Multichannel.

> [!NOTE]
>Nous vous recommandons d’utiliser plusieurs sous-réseaux et réseaux VLAN pour séparer le trafic de stockage dans Azure Stack HCI.

Prenons l’exemple d’un cluster à quatre nœuds. Chaque serveur possède deux ports de stockage (à gauche et à droite). Étant donné que chaque adaptateur se trouve sur le même sous-réseau et le même réseau VLAN, SMB Multichannel répartit les connexions entre toutes les liaisons disponibles. Par conséquent, le port situé à gauche sur le premier serveur (192.168.1.1) établit une connexion avec le port de gauche du deuxième serveur (192.168.1.2). Le port qui se trouve à droite sur le premier serveur (192.168.1.12) établit une connexion avec le port de droite du deuxième serveur. Des connexions similaires sont établies pour le troisième et le quatrième serveurs.

Cela crée toutefois des connexions inutiles et provoque une congestion au niveau de l’interconnexion (groupe d’agrégation de liens à plusieurs châssis, ou MC-LAG) qui connecte les commutateurs ToR (marqués d’un X). Consultez le diagramme suivant :

:::image type="content" source="media/plan-networking/four-node-cluster-1.png" alt-text="Cluster à quatre nœuds – Même sous-réseau" lightbox="media/plan-networking/four-node-cluster-1.png":::

L’approche recommandée consiste à utiliser des sous-réseaux et des réseaux VLAN distincts pour chaque ensemble d’adaptateurs. Dans le diagramme suivant, les ports de droite utilisent désormais le sous-réseau 192.168.2.x /24 et VLAN2. Cela permet au trafic des ports de gauche de rester sur TOR1 et à celui des ports de droite de rester sur TOR2. Consultez le diagramme suivant :

:::image type="content" source="media/plan-networking/four-node-cluster-2.png" alt-text="Cluster à quatre nœuds – Sous-réseaux différents" lightbox="media/plan-networking/four-node-cluster-2.png":::

## <a name="traffic-bandwidth-allocation"></a>Allocation de bande passante au trafic

Le tableau ci-dessous donne des exemples d’allocations de bande passante de différents types de trafic, avec des vitesses d’adaptateur courantes, dans Azure Stack HCI. Notez qu’il s’agit d’un exemple de solution convergée dans laquelle tous les types de trafic (calcul, stockage et gestion) s’exécutent sur les mêmes adaptateurs physiques et sont associés à l’aide de la fonctionnalité SET.

Dans la mesure où ce cas d’usage est celui qui impose le plus de contraintes, il constitue une bonne ligne de base. Toutefois, en prenant en compte les permutations du nombre d’adaptateurs et des vitesses, il doit être considéré comme un exemple et non comme une exigence de prise en charge.

Cet exemple repose sur les hypothèses suivantes :

- Il y a deux adaptateurs par équipe.
- Le trafic SBL (Storage Bus Layer), le trafic de Volume partagé de cluster (CSV) et le trafic Hyper-V (Migration dynamique) :

    - utilisent les mêmes adaptateurs physiques ;
    - utilisent le protocole SMB.
- Le protocole SMB reçoit une allocation de bande passante de 50 % avec Data Center Bridging.
    - Le trafic SBL/CSV, prioritaire, reçoit 70 % de la réservation de bande passante SMB.
    - La Migration dynamique, limitée à l’aide de la cmdlet `Set-SMBBandwidthLimit`, reçoit 29 % de la bande passante restante.
        - Si la bande passante disponible pour la Migration dynamique est supérieure ou égale à 5 Gbit/s et que les cartes réseau sont compatibles, optez pour la fonctionnalité RDMA. Utilisez pour cela la cmdlet suivante :

            ```Powershell
            Set-VMHost VirtualMachineMigrationPerformanceOption SMB
            ```

        - Si la bande passante disponible pour la Migration dynamique est inférieure à 5 Gbit/s, appliquez une compression pour réduire les durées d’indisponibilité. Utilisez pour cela la cmdlet suivante :

            ```Powershell
            Set-VMHost -VirtualMachineMigrationPerformanceOption Compression
            ```

 - Si vous utilisez la fonctionnalité RDMA avec Migration dynamique, veillez à ce que le trafic Migration dynamique ne puisse pas consommer toute la bande passante allouée à la classe de trafic RDMA à l’aide d’une limite de bande passante SMB. Attention : cette cmdlet prend une entrée en octets par seconde (o/s), tandis que les cartes réseau sont indiquées en bits par seconde (bits/s). Utilisez la cmdlet suivante pour définir une limite de bande passante de 6 Gbit/s par exemple :

    ```Powershell
    Set-SMBBandwidthLimit -Category LiveMigration -BytesPerSecond 750MB
    ```

    > [!NOTE]
    >750 Mo/s dans cet exemple équivaut à 6 Gbit/s.

Voici le tableau d’allocation des exemples de bande passante :

|Vitesse de la carte réseau|Bande passante associée|Réservation de bande passante SMB**|% SBL/CSV|Bande passante SBL/CSV|% Migration dynamique|Bande passante Migration dynamique max.|% pulsations|Bande passante des pulsations|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|10 Gbits/s|20 Gbits/s|10 Gbits/s|70 %|7 Gbits/s|*|*|2 %|200 Mbits/s|
|25 Gbits|50 Gbit/s|25 Gbits|70 %|17,5 Gbit/s|29 %|7,25 Gbit/s|1%|250 Mbit/s|
|40 Gbits/s|80 Gbit/s|40 Gbits/s|70 %|28 Gbit/s|29 %|11,6 Gbit/s|1%|400 Mbit/s|
|50 Gbit/s|100 Gbits/s|50 Gbit/s|70 %|35 Gbit/s|29 %|14,5 Gbit/s|1%|500 Mbits/s|
|100 Gbits/s|200 Gbits/s|100 Gbits/s|70 %|70 Gbit/s|29 %|29 Gbit/s|1%|1 Gbit/s|
|200 Gbits/s|400 Gbit/s|200 Gbits/s|70 %|140 Gbit/s|29 %|58 Gbit/s|1%|2 Gbit/s|

\* Utilisez la compression plutôt que la fonctionnalité RDMA si l’allocation de bande passante pour le trafic Live Migration est inférieure à 5 Gbit/s.

** 50 % est un exemple de réservation de bande passante pour ce cas de figure.

## <a name="stretched-cluster-considerations"></a>Considérations relatives aux clusters étendus

Les clusters étendus assurent une récupération d’urgence sur plusieurs centres de données. Dans sa forme la plus simple, un réseau en cluster Azure Stack HCI étendu se présente ainsi :

:::image type="content" source="media/plan-networking/stretched-cluster.png" alt-text="Cluster étendu" lightbox="media/plan-networking/stretched-cluster.png":::

Les clusters étendus présentent les exigences et caractéristiques suivantes :

- La fonctionnalité RDMA, limitée à un seul site, n’est pas prise en charge sur différents sites ou sous-réseaux.
- Les serveurs du même site doivent se trouver dans le même rack et la même limite de couche 2.
- La communication entre les sites franchit une limite de couche 3 ; les topologies de couche 2 étendues ne sont pas prises en charge.

- Si un site utilise la fonctionnalité RDMA pour ses adaptateurs de stockage, ces derniers doivent se trouver sur un sous-réseau et un réseau VLAN distincts qui ne peuvent pas être acheminés entre les sites. De ce fait, le réplica de stockage ne peut pas utiliser la fonctionnalité RDMA sur plusieurs sites.
- Les adaptateurs utilisés pour la communication entre sites présentent les caractéristiques suivantes :

    - Ils peuvent être physiques ou virtuels (carte réseau virtuelle hôte). S’ils sont virtuels, vous devez provisionner une carte réseau virtuelle dans son propre sous-réseau et son propre réseau VLAN par carte réseau physique.
    - Ils doivent se trouver sur leur propre sous-réseau et leur propre réseau VLAN qui peuvent être acheminés entre les sites.
    - La fonctionnalité RDMA doit être désactivée à l’aide de la cmdlet `Disable-NetAdapterRDMA`. Nous vous recommandons de demander explicitement au réplica de stockage d’utiliser des interfaces spécifiques avec la cmdlet `Set-SRNetworkConstraint`.
    - Ils doivent satisfaire à toutes les exigences supplémentaires liées au réplica de stockage.
-   En cas de basculement vers un autre site, vous devez veiller à ce que la bande passante disponible soit suffisante pour l’exécution des charges de travail sur l’autre site. Une solution sûre consiste à provisionner des sites à 50 % de leur capacité disponible. Ce n’est pas une condition stricte si vous pouvez tolérer un niveau de performance inférieur pendant un basculement.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les exigences liées aux commutateurs réseau et aux réseaux physiques, consultez [Exigences liées aux réseaux physiques](physical-network-requirements.md).
- Rafraîchissez votre connaissance des concepts de base du clustering de basculement. Consultez [Failover Clustering Networking Basics](https://techcommunity.microsoft.com/t5/failover-clustering/failover-clustering-networking-basics-and-fundamentals/ba-p/1706005?s=09)
- Rafraîchissez vos connaissances de l’utilisation de SET. Consultez [Accès direct à la mémoire à distance (Remote Direct Memory Access, RDMA) et association commutée/intégrée (Switched-Embedded Teaming, SET)](/windows-server/virtualization/hyper-v-virtual-switch/rdma-and-switch-embedded-teaming).
- Pour le déploiement, consultez [Créer un cluster en utilisant Windows Admin Center](../deploy/create-cluster.md).
- Pour le déploiement, consultez [Créer un cluster en utilisant Windows PowerShell](../deploy/create-cluster-powershell.md).