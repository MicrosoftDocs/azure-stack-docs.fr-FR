---
title: Préparer le déploiement d’Azure Stack HCI
description: Guide pratique pour préparer le déploiement d’Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 08/03/2020
ms.openlocfilehash: fc12f638970e82c293a9143c398892a88049f6cd
ms.sourcegitcommit: 952d26ad08fcc28ad3ad83e27644e61497623a44
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87889184"
---
# <a name="before-you-deploy-azure-stack-hci"></a>Avant le déploiement d’Azure Stack HCI

> S’applique à : Azure Stack HCI, version 20H2

Dans ce guide pratique, vous allez apprendre à effectuer les opérations suivantes :

- Déterminer si votre matériel satisfait aux exigences de base pour les clusters Azure Stack HCI standard (site unique) ou étendus (deux sites)
- Veiller à ne pas dépasser les spécifications matérielles maximales prises en charge
- Collecter les informations requises pour un déploiement réussi
- Installer Windows Admin Center sur un PC ou un serveur de gestion

## <a name="determine-hardware-requirements"></a>Déterminer la configuration matérielle requise

Microsoft recommande d’acheter une solution matérielle/logicielle Azure Stack HCI validée, proposée par nos partenaires. Ces solutions sont conçues, assemblées et validées dans le respect de notre architecture de référence pour garantir compatibilité et fiabilité, ce qui vous permet d’être opérationnel rapidement. Vérifiez que les systèmes, les composants, les appareils et les pilotes que vous utilisez sont certifiés Windows Server 2019 selon le catalogue Windows Server. Visitez le site web [Solutions Azure Stack HCI](https://azure.microsoft.com/overview/azure-stack/hci) pour connaître les solutions validées.

### <a name="server-requirements"></a>Configuration requise au niveau du serveur

- Un cluster Azure Stack HCI standard requiert un minimum de 2 serveurs et un maximum de 16 serveurs. Toutefois, les clusters peuvent être combinés à l’aide d’ensembles de clusters pour créer une plateforme HCI contenant des centaines de nœuds.
- Les clusters étendus requièrent le déploiement de serveurs sur deux sites distincts. Les sites peuvent se trouver dans différents pays, dans différentes villes, à différents étages ou dans différentes pièces. Un cluster étendu nécessite un minimum de 4 serveurs (2 par site) et un maximum de 16 serveurs (8 par site).
- Il est recommandé que tous les serveurs soient du même modèle et proviennent du même fabricant, qu’ils utilisent des processeurs compatibles Intel Nehalem, de classe AMD EPYC 64 bits ou ultérieur avec la traduction d’adresses de deuxième niveau (SLAT). Un processeur Intel Xeon scalable de 2e génération est nécessaire pour prendre en charge la mémoire persistante Intel Optane DC. Les processeurs doivent avoir au moins 1,4 GHz et être compatibles avec le jeu d’instructions x64.
- Veillez à ce que les serveurs disposent d’au moins 32 Go de RAM par nœud pour s’adapter au système d’exploitation du serveur, aux machines virtuelles et à d’autres applications ou charges de travail. En outre, autorisez 4 Go de RAM par téraoctet (To) de capacité de lecteur de cache sur chaque serveur pour les métadonnées des espaces de stockage direct.
- Vérifiez que la prise en charge de la virtualisation est activée dans le BIOS ou l’UEFI :
    - Assistance matérielle à la virtualisation. Cette virtualisation est disponible avec les processeurs qui incluent une option de virtualisation, plus spécifiquement les processeurs dotés de la technologie Intel VT (Intel Virtualization Technology) ou AMD-V (AMD Virtualization).
    - La prévention de l’exécution des données (DEP) appliquée par le matériel doit être disponible et activée. Pour les systèmes Intel, il s’agit du composant XD Bit (Execute Disable Bit). Pour les systèmes AMD, il s’agit du composant NX Bit (No Execute Bit).
- Vous pouvez utiliser n’importe quel périphérique de démarrage pris en charge par Windows Server, qui [contient à présent SATADOM](https://cloudblogs.microsoft.com/windowsserver/2017/08/30/announcing-support-for-satadom-boot-drives-in-windows-server-2016/). Le miroir RAID 1 n’est **pas** obligatoire, mais il est pris en charge pour le démarrage. Une taille minimale de 200 Go est recommandée.
- Pour obtenir des spécifications supplémentaires relatives aux fonctionnalités pour Hyper-V, consultez [Configuration système requise pour Hyper-V sur Windows Server](/windows-server/virtualization/hyper-v/system-requirements-for-hyper-v-on-windows).

### <a name="networking-requirements"></a>Configuration requise du réseau

Un cluster Azure Stack HCI requiert une connexion réseau fiable à bande passante élevée et à faible latence entre chaque nœud serveur. Vous devriez vérifier les éléments suivants :

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

### <a name="domain-requirements"></a>Exigences relatives aux domaines

Il n’existe aucune exigence particulière au niveau fonctionnel du domaine pour Azure Stack HCI : il suffit d’une version du système d’exploitation pour votre contrôleur de domaine qui est toujours prise en charge. Si vous ne l’avez pas déjà fait, nous vous recommandons d’activer la fonctionnalité de Corbeille Active Directory.

### <a name="interconnect-requirements-between-nodes"></a>Exigences d’interconnexion entre les nœuds

Cette section traite des exigences de mise en réseau spécifiques entre les nœuds serveur d’un site, appelées interconnexions. Des interconnexions de nœuds commutées ou non peuvent être utilisées et sont prises en charge :

- **Commutées :** les nœuds serveur sont généralement connectés les uns aux autres via des réseaux Ethernet qui utilisent des commutateurs réseau. Les commutateurs doivent être correctement configurés pour gérer la bande passante et le type de mise en réseau. Si vous utilisez RDMA qui implémente le protocole RoCE, le périphérique réseau et la configuration du commutateur sont importants.
- **Absence de commutateur :** les nœuds serveur peuvent également être interconnectés à l’aide de connexions Ethernet directes sans commutateur. Dans ce cas, chaque nœud serveur doit disposer d’une connexion directe avec tous les autres nœuds de cluster dans le même site.

#### <a name="interconnects-for-2-3-node-clusters"></a>Interconnexions pour les clusters à 2-3 nœuds

Il s’agit des *configurations minimales requises pour l’interconnexion* pour les clusters à site unique comportant deux ou trois nœuds. Celles-ci s’appliquent à chaque nœud serveur :

- Une ou plusieurs cartes réseau 1 Gb à utiliser pour les fonctions de gestion
- Une ou plusieurs cartes réseau 10 Gb (ou plus) pour le trafic lié au stockage et à la charge de travail
- Plusieurs connexions réseau entre chaque nœud sont recommandées pour des questions de redondance et de performances

#### <a name="interconnects-for-4-node-and-greater-clusters"></a>Interconnexions pour les clusters à 4 nœuds et plus

Il s’agit des *configurations minimales requises pour l’interconnexion* pour les clusters comportant au moins quatre nœuds, et pour les clusters hautes performances. Celles-ci s’appliquent à chaque nœud serveur :

- Une ou plusieurs cartes réseau 1 Gb à utiliser pour les fonctions de gestion.
- Une ou plusieurs cartes réseau 25 Gb (ou plus) pour le trafic lié au stockage et à la charge de travail. Nous vous recommandons d’utiliser au moins deux connexions réseau pour la redondance et les performances.
- Cartes réseau prenant en charge l’accès direct à la mémoire à distance (RDMA) : iWARP (recommandé) ou RoCE.

### <a name="site-to-site-requirements-stretched-cluster"></a>Exigences de site à site (cluster étendu)

Lors de la connexion entre des sites pour les clusters étendus, les exigences d’interconnexion au sein de chaque site s’appliquent toujours et des exigences supplémentaires concernant le trafic de réplica de stockage et la migration dynamique Hyper-V doivent être prises en compte :

- Au moins une connexion RDMA 1 Gb ou Ethernet/TCP entre les sites pour la réplication synchrone. Une connexion RDMA 25 Gb est recommandée.
- Un réseau entre les sites disposant de suffisamment de bande passante pour contenir votre charge de travail d’écriture d’E/S et une latence moyenne des allers-retour de 5 ms ou moins pour la réplication synchrone. La réplication asynchrone n’a pas de recommandation de latence.
- Si vous utilisez une connexion unique entre des sites, définissez des limites de bande passante SMB pour le réplica de stockage à l’aide de PowerShell. Pour plus d’informations, consultez [Set-SmbBandwidthLimit](/powershell/module/smbshare/set-smbbandwidthlimit?view=win10-ps).
- Si vous utilisez plusieurs connexions entre les sites, séparez le trafic entre les connexions. Par exemple, placez le trafic du réplica de stockage sur un réseau distinct du trafic de migration dynamique Hyper-V à l’aide de PowerShell. Pour plus d’informations, consultez [Set-SRNetworkConstraint](/powershell/module/storagereplica/set-srnetworkconstraint?view=win10-ps).

### <a name="network-port-requirements"></a>Configuration requise des ports réseau

Vérifiez que les ports réseau appropriés sont ouverts entre tous les nœuds serveur au sein d’un site et entre les sites (pour les clusters étendus). Vous avez besoin de règles de pare-feu et de routeur appropriées pour autoriser les protocoles ICMP, SMB (port 445, plus port 5445 pour SMB Direct) et WS-MAN (port 5985) à trafic bidirectionnel entre tous les serveurs du cluster.

Lorsque vous utilisez l’Assistant Création d’un cluster dans Windows Admin Center pour créer le cluster, l’Assistant ouvre automatiquement les ports de pare-feu appropriés sur chaque serveur du cluster pour le clustering de basculement, Hyper-V et le réplica de stockage. Si vous utilisez un pare-feu logiciel différent sur chaque serveur, ouvrez les ports suivants :

#### <a name="failover-clustering-ports"></a>Ports de clustering de basculement

- ICMPv4 et ICMPv6
- Port TCP 445
- Ports dynamiques RPC
- Port TCP 135
- Port TCP 137
- Port TCP 3343
- Port UDP 3343

#### <a name="hyper-v-ports"></a>Ports Hyper-V

- Port TCP 135
- Port TCP 80 (connectivité HTTP)
- Port TCP 443 (connectivité HTTPS)
- Port TCP 6600
- Port TCP 2179
- Ports dynamiques RPC
- Mappeur de point de terminaison RPC
- Port TCP 445

#### <a name="storage-replica-ports-stretched-cluster"></a>Ports de réplica de stockage (cluster étendu)

- Port TCP 445
- TCP 5445 (si vous utilisez iWarp RDMA)
- Port TCP 5985
- ICMPv4 et ICMPv6 (si vous utilisez Test-SRTopology)

Il peut y avoir des ports supplémentaires non listés ci-dessus. Il s’agit des ports pour le fonctionnement de base d’Azure Stack HCI.

### <a name="storage-requirements"></a>Exigences de stockage

- Azure Stack HCI fonctionne avec les lecteurs SATA, SAS, NVMe ou à mémoire persistante en attachement direct qui sont physiquement attachés à un seul serveur chacun.
- Chaque serveur du cluster doit avoir le même modèle, la même taille et le même nombre de lecteurs, avec les mêmes tailles de secteur sur tous les disques. Les lecteurs peuvent être internes au serveur ou dans un boîtier externe connecté à un seul serveur.
- Chaque serveur du cluster doit avoir des volumes dédiés pour les journaux, avec un stockage de journal au moins aussi rapide que le stockage de données. Les clusters étendus nécessitent au moins deux volumes : l’un pour les données répliquées et l’autre pour les données de journal.
- SCSI Enclosure Services (SES) est requis pour le mappage et l’identification des emplacements. Chaque boîtier externe doit présenter un identificateur unique (ID unique). **NON PRIS EN CHARGE :** Les cartes de contrôleur RAID ou le stockage SAN (Fibre Channel, iSCSI, FCoE), les boîtiers SAS partagés connectés à plusieurs serveurs, ou toute forme de MPIO (Multipath IO) où les lecteurs sont accessibles par plusieurs chemins d’accès. Les cartes HBA doivent implémenter le mode de transfert simple.
- Pour plus d’informations, consultez la rubrique sur le [choix des lecteurs](../concepts/choose-drives.md) ou la [configuration matérielle requise pour les espaces de stockage direct](/windows-server/storage/storage-spaces/storage-spaces-direct-hardware-requirements).
- Pour obtenir les options disponibles pour les volumes et la résilience, consultez la rubrique [Planification des volumes dans les espaces de stockage direct](/windows-server/storage/storage-spaces/plan-volumes#choosing-the-resiliency-type).
- Si vous déployez également des machines virtuelles et un stockage virtualisé, consultez [Utilisation des espaces de stockage direct dans les clusters de machines virtuelles invités](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm).

## <a name="review-maximum-supported-hardware-specifications"></a>Consulter les spécifications matérielles maximales prises en charge

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

## <a name="gather-information"></a>Collecter des informations

Pour préparer le déploiement, rassemblez les informations suivantes sur votre environnement :

- **Noms des serveurs :** familiarisez-vous avec les stratégies d’attribution de noms de votre organisation pour les ordinateurs, les fichiers, les chemins d’accès et autres ressources. Vous devrez configurer plusieurs serveurs, chacun avec des noms uniques.
- **Nom de domaine :** familiarisez-vous avec les stratégies de votre organisation relatives à l’attribution de noms de domaine et à la jonction de domaine. Vous allez joindre les serveurs à votre domaine, et vous devrez spécifier le nom de domaine.
- **Adresses IP statiques :** Azure Stack HCI requiert des adresses IP statiques pour le trafic de stockage et de charge de travail (VM) et ne prend pas en charge l’attribution d’adresses IP dynamiques via DHCP pour ce réseau à haut débit. Vous pouvez utiliser DHCP pour la carte réseau de gestion, sauf si vous en utilisez deux en équipe, auquel cas vous devez là encore utiliser des adresses IP statiques. Demandez à votre administrateur quelle adresse IP vous devez utiliser pour chaque serveur du cluster.
- **Mise en réseau RDMA :** il existe deux types de protocoles RDMA : iWarp et RoCE. Notez lequel est utilisé par vos cartes réseau et, si RoCE est utilisé, notez également la version (v1 ou v2). Pour RoCE, notez également le modèle de votre commutateur « Top of the rack ».
- **ID du réseau local virtuel** : notez l’ID de réseau local virtuel à utiliser pour les cartes réseau sur les serveurs, le cas échéant. Pour l’obtenir, contactez votre administrateur réseau.
- **Nom des sites :** Pour les clusters étendus, deux sites sont utilisés pour la récupération d’urgence. Vous pouvez configurer des sites à l’aide d’Active Directory Domain Services ou l’Assistant Création d’un cluster peut les configurer automatiquement pour vous. Pour plus d’informations sur la configuration des sites, consultez votre administrateur de domaine. Ou pour en savoir plus, consultez [Présentation d’Active Directory Domain Services](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

## <a name="install-windows-admin-center"></a>Installer Windows Admin Center

Windows Admin Center est une application basée sur un navigateur déployée localement qui permet de gérer Azure Stack HCI. Le plus simple est d’[installer Windows Admin Center](/windows-server/manage/windows-admin-center/deploy/install) sur un PC de gestion local (mode bureau), même si vous pouvez aussi l’installer sur un serveur (mode service).

Si vous installez Windows Admin Center sur un serveur, les tâches qui requièrent CredSSP, telles que la création de clusters et l’installation des mises à jour et des extensions, vous demandent d’utiliser un compte membre du groupe d’administrateurs de passerelles sur le serveur Windows Admin Center. Pour plus d’informations, consultez les deux premières sections dans [Configurer le contrôle d’accès utilisateur et les autorisations](/windows-server/manage/windows-admin-center/configure/user-access-control#gateway-access-role-definitions).

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour apprendre à déployer le système d’exploitation Azure Stack HCI.
> [!div class="nextstepaction"]
> [Déployer le système d’exploitation Azure Stack HCI](operating-system.md)
