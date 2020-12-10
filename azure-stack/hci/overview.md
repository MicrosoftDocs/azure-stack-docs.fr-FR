---
title: Vue d’ensemble de la solution Azure Stack HCI
description: Azure Stack HCI est une solution de cluster d’infrastructure hyperconvergée qui héberge des charges de travail Windows et Linux virtualisées et leur stockage dans un environnement local hybride. Les services hybrides Azure améliorent le cluster avec des fonctionnalités telles que la supervision basée sur le cloud, Site Recovery et les sauvegardes de machines virtuelles ainsi qu’une vue centralisée de tous vos déploiements Azure Stack HCI dans le portail Azure.
ms.topic: overview
author: khdownie
ms.author: v-kedow
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 12/7/2020
ms.openlocfilehash: c6fe98d9f597f40d0a5cefde151eb36736b81d7a
ms.sourcegitcommit: 61556b7b6e029e3a26a4b7ef97f0b13fbe7cd5a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96761692"
---
# <a name="azure-stack-hci-solution-overview"></a>Vue d’ensemble de la solution Azure Stack HCI

Azure Stack HCI est une solution de cluster d’infrastructure hyperconvergée qui héberge des charges de travail Windows et Linux virtualisées et leur stockage dans un environnement local hybride. Les services hybrides Azure améliorent le cluster avec des fonctionnalités telles que la supervision basée sur le cloud, Site Recovery et les sauvegardes de machines virtuelles ainsi qu’une vue centralisée de tous vos déploiements Azure Stack HCI dans le portail Azure. Vous pouvez gérer le cluster avec vos outils existants, notamment Windows Admin Center, System Center et PowerShell.

Azure Stack HCI version 20H2 est un nouveau système d’exploitation désormais disponible en préversion publique et [téléchargeable ici](https://azure.microsoft.com/products/azure-stack/hci/hci-download/). Il est destiné aux clusters locaux exécutant des charges de travail virtualisées, avec des connexions cloud hybrides intégrées. En tant que tel, Azure Stack HCI est fourni sous la forme d’un service Azure et facturé sur un abonnement Azure. Azure Stack HCI offre également la possibilité d’héberger Azure Kubernetes Service. Pour plus d’informations, consultez [Azure Kubernetes Service sur Azure Stack HCI](../aks-hci/overview.md).

Prenez quelques minutes pour regarder la vidéo sur les fonctionnalités générales d’Azure Stack HCI :

> [!VIDEO https://www.youtube.com/embed/fw8RVqo9dcs]

Azure Stack HCI est par essence une solution qui combine les éléments suivants :

- Matériel validé d’un partenaire OEM
- Système d’exploitation Azure Stack HCI
- Windows Admin Center
- Services Azure

:::image type="content" source="media/overview/azure-stack-hci-solution.png" alt-text="Le système d’exploitation Azure Stack HCI s’exécute sur le matériel validé, est géré par Windows Admin Center et se connecte à Azure" border="false":::

Azure Stack HCI version 20H2 fournit de nouvelles fonctionnalités absentes de Windows Server, telles que la possibilité d’utiliser Windows Admin Center pour créer un cluster hyperconvergé qui utilise des espaces de stockage direct avec, à la clé, de meilleurs rapports prix/performances en matière de stockage. Il est notamment possible d’étendre le cluster sur plusieurs sites afin de pouvoir effectuer un basculement automatique entre ces derniers. Pour plus d’informations, consultez [Nouveautés d’Azure Stack HCI](#whats-new-in-azure-stack-hci).

## <a name="use-cases-for-azure-stack-hci"></a>Cas d’utilisation d’Azure Stack HCI

Il existe de nombreux cas d’utilisation d’Azure Stack HCI, même s’il n’est pas destiné à des charges de travail non virtualisées. Les clients choisissent souvent Azure Stack HCI dans les scénarios suivants :

### <a name="data-center-consolidation-and-modernization"></a>Consolidation et modernisation du centre de données

L’actualisation et la consolidation des hôtes de virtualisation vieillissants avec Azure Stack HCI peuvent améliorer la scalabilité et faciliter la gestion et la sécurisation de votre environnement. Il est également possible de mettre hors service le stockage SAN hérité pour réduire l’empreinte et le coût total de possession. Les opérations et l’administration des systèmes sont simplifiées grâce à des interfaces et des outils unifiés ainsi qu’à un point unique de prise en charge.

### <a name="remote-and-branch-offices"></a>Succursales et bureaux distants

Avec des solutions de cluster à deux serveurs qui commencent à moins de 20 000 $ par localisation, Azure Stack HCI est un excellent moyen de moderniser les succursales et les bureaux distants, les magasins et les sites. La résilience imbriquée novatrice permet aux volumes de rester en ligne et accessibles même si plusieurs défaillances matérielles se produisent en même temps. La technologie de témoin de cloud vous permet d’utiliser Azure en tant que système de départage léger pour le quorum du cluster, ce qui empêche les conditions de Split-Brain sans le coût d’un troisième hôte. Les clients peuvent également avoir une vue centralisée des déploiements Azure Stack HCI à distance dans le portail Azure.

### <a name="virtual-desktops"></a>Bureaux virtuels

De nombreuses organisations souhaitent héberger les bureaux virtuels localement afin de bénéficier d’une latence faible et de préserver la souveraineté des données. Azure Stack HCI peut fournir des performances de type local.

### <a name="high-performance-virtualized-workloads"></a>Charges de travail virtualisées hautes performances

Azure Stack HCI peut fournir des performances optimales pour les bases de données SQL Server et autres charges de travail virtualisées sensibles aux performances qui nécessitent des millions d’IOPS de stockage ou de transactions de base de données par seconde, offrant en permanence une faible latence avec des disques SSD NVMe, une pile RDMA robuste et une mémoire persistante.

## <a name="azure-integration-benefits"></a>Avantages de l’intégration à Azure

Particulièrement bien placé dans le domaine de l’infrastructure hybride, Azure Stack HCI vous permet de tirer parti de l’interopérabilité entre les ressources cloud et locales et d’effectuer des opérations de supervision, de sécurisation et de sauvegarde en mode natif dans le cloud.

Une fois que vous avez inscrit votre cluster Azure Stack HCI auprès d’Azure, vous pouvez utiliser le portail Azure aux fins suivantes :

- **Surveillance :** Affichez tous vos clusters Azure Stack HCI dans une vue globale unique où vous pouvez les rassembler par groupe de ressources et les étiqueter.
- **Facturation :** Payez pour Azure Stack HCI par le biais de votre abonnement Azure (notez qu’il n’y a aucun coût pendant la préversion publique).

Nous travaillons d’arrache-pied à la création d’autres fonctionnalités, donc ne manquez pas de vous tenir informé.

Vous pouvez également vous abonner à d’autres services hybrides Azure :

- **Azure Site Recovery** pour la haute disponibilité et la reprise d’activité en tant que service (DRaaS).
- **Azure Monitor**, hub centralisé permettant de suivre l’évolution de vos applications, de votre réseau et de votre infrastructure, avec des analyses avancées optimisées par l’IA.
- **Témoin de cloud**, pour utiliser Azure en tant que système de départage léger pour le quorum du cluster.
- **Sauvegarde Azure** pour la protection des données hors site et la protection contre les rançongiciels.
- **Azure Update Management** pour l’évaluation des mises à jour et les déploiements de mises à jour destinées à des machines virtuelles Windows s’exécutant dans Azure et localement.
- **Carte réseau Azure** pour connecter des ressources locales à vos machines virtuelles Azure via un réseau privé virtuel (VPN) point à site.
- **Azure File Sync** pour synchroniser votre serveur de fichiers avec le cloud.

Pour plus d’informations, consultez [Connexion de Windows Server aux services Azure hybrides](/windows-server/manage/windows-admin-center/azure/index).

## <a name="why-azure-stack-hci"></a>Pourquoi Azure Stack HCI ?

Azure Stack HCI est une pile de virtualisation intégrée haut de gamme reposant sur des technologies éprouvées qui ont déjà été déployées à grande échelle, notamment Hyper-V, les espaces de stockage direct et les solutions de réseau à définition logicielle (SDN, software-defined networking). Les clients choisissent Azure Stack HCI pour de nombreuses raisons, notamment :

- Les administrateurs de serveur et Hyper-V le connaissent, ce qui leur permet de tirer parti des concepts et compétences existants en matière de virtualisation et de stockage.
- Il fonctionne avec les processus de centre de données existants et les outils tels que Microsoft System Center, Active Directory, la stratégie de groupe et les scripts PowerShell.
- Il fonctionne avec des outils de sauvegarde, de sécurité et de supervision tiers répandus.
- La flexibilité de l’offre matérielle permet aux clients de choisir le fournisseur qui propose les meilleurs service et support dans leur zone géographique.
- Le support conjoint entre Microsoft et le fournisseur de matériel améliore l’expérience utilisateur.
- Des mises à jour fluides et complètes permettent de rester à jour.
- Un écosystème flexible et large offre aux professionnels de l’informatique la souplesse dont ils ont besoin pour créer une solution qui répond le mieux à leurs besoins.

## <a name="what-you-need-for-azure-stack-hci"></a>Ce dont vous avez besoin pour Azure Stack HCI

Pour commencer, voici ce dont vous avez besoin :

- Un cluster de deux serveurs ou plus référencés dans le [Catalogue Azure Stack HCI](https://aka.ms/azurestackhcicatalog), acheté auprès du partenaire fournisseur de matériel Microsoft de votre choix
- Un [abonnement Azure](https://azure.microsoft.com/)
- Une connexion Internet pour chaque serveur du cluster qui peut se connecter via le trafic HTTPS sortant au point de terminaison suivant au moins tous les 30 jours : *-azurestackhci-usage.azurewebsites.net
- Pour les clusters étendus sur plusieurs sites, vous devez disposer d’au moins une connexion de 1 Go entre les sites (une connexion RDMA de 25 Go est recommandée), avec une latence moyenne de 5 ms aller-retour si vous souhaitez effectuer une réplication synchrone dans laquelle les écritures se produisent simultanément sur les deux sites.
- Si vous pensez utiliser SDN (Software Defined Networking), vous avez besoin d’un disque dur virtuel (VHD) pour le système d’exploitation Azure Stack HCI pour créer des machines virtuelles de contrôleur de réseau (voir [Planifier le déploiement du contrôleur de réseau](concepts/network-controller.md))

Pour plus d’informations, consultez [Configuration système requise](concepts/system-requirements.md). Pour obtenir la configuration requise d’Azure Kubernetes Service sur Azure Stack HCI, consultez [Configuration requise d’AKS sur Azure Stack HCI](../aks-hci/overview.md#what-you-need-to-get-started).

## <a name="hardware-partners"></a>Partenaires fournisseurs de matériel

Vous pouvez acheter des solutions Azure Stack HCI validées auprès du partenaire Microsoft de votre choix pour être opérationnel sous un délai rapide de conception et de création. Les partenaires Microsoft offrent également un point de contact unique pour les services d’implémentation et de support technique. Vous pouvez acheter des nœuds validés ou un système intégré, qui comprend le système d’exploitation Azure Stack HCI préinstallé et les extensions partenaires pour les mises à jour du pilote et du microprogramme.

Visitez la page [Solutions Azure Stack HCI](https://azure.microsoft.com/overview/azure-stack/hci) ou parcourez le [Catalogue Azure Stack HCI](https://aka.ms/azurestackhcicatalog) pour voir plus de 70 solutions Azure Stack HCI disponibles auprès de partenaires Microsoft comme ASUS, Axellio, Blue Chip, DataON, Dell EMC, Fujitsu, HPE, Hitachi, Huawei, Lenovo, NEC, primeLine Solutions, QCT, SecureGUARD et Supermicro.

## <a name="software-partners"></a>Partenaires logiciels

De nombreux partenaires Microsoft travaillent sur des logiciels qui étendent les fonctionnalités d’Azure Stack HCI tout en permettant aux administrateurs informatiques d’utiliser des outils familiers. Par exemple, Altaro, fournisseur international de solutions de sauvegarde et Microsoft Gold Partner, s’est engagé à prendre en charge Azure Stack HCI dans sa solution de sauvegarde de machine virtuelle Altaro. Ainsi, les clients et les fournisseurs de services managés peuvent sauvegarder les machines virtuelles s’exécutant sur Azure Stack HCI gratuitement jusqu’à fin juin 2021. [Découvrez-en plus sur cette annonce](http://www.altaro.com/news/single/News-Altaro-applies-its-expertise-in-Hyper-V-backup-to-support-Microsoft.php).

## <a name="licensing-billing-and-pricing"></a>Licences, facturation et tarifs

La facturation d’Azure Stack HCI est basée sur des frais d’abonnement mensuels par cœur de processeur physique, et non sur une licence pour la durée des droits de propriété intellectuelle. Quand les clients se connectent à Azure, le nombre de cœurs utilisés est automatiquement chargé et évalué à des fins de facturation. Le coût ne varie pas en fonction de la consommation au-delà des cœurs de processeur physique ; ainsi, l’ajout de machines virtuelles n’entraîne pas de surcoût et les clients capables d’exécuter des environnements virtuels plus denses sont récompensés.

Les clients peuvent acheter des serveurs validés auprès d’un partenaire fournisseur de matériel sur lesquels le système d’exploitation Azure Stack HCI est préinstallé, ou ils peuvent acheter des serveurs complets validés auprès d’un fabricant OEM, puis s’abonner au service Azure Stack HCI et télécharger le système d’exploitation Azure Stack HCI à partir du [portail Azure](https://azure.microsoft.com/products/azure-stack/hci/).

## <a name="management-tools"></a>Outils d'administration

Avec Azure Stack HCI, vous disposez de droits d’administrateur complets sur le cluster et pouvez gérer directement toutes ses technologies :

- [Hyper-V](/windows-server/virtualization/hyper-v/hyper-v-on-windows-server)
- [Espaces de stockage direct](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [SDN (Software Defined Network)](/windows-server/networking/sdn/)
- [Clustering de basculement](/windows-server/failover-clustering/failover-clustering-overview)

Pour gérer ces technologies, vous pouvez utiliser les outils de gestion suivants :

- [Windows Admin Center](/windows-server/manage/windows-admin-center/overview)
- [System Center](https://www.microsoft.com/cloud-platform/system-center)
- [PowerShell](/powershell/)
- Autres outils de gestion comme le [Gestionnaire de serveur](/windows-server/administration/server-manager/server-manager) et les composants logiciels enfichables MMC
- Outils non-Microsoft comme 5Nine Manager

## <a name="the-azure-stack-family"></a>La famille Azure Stack

Azure Stack HCI fait partie de la famille Azure et Azure Stack, et utilise les mêmes logiciels de calcul, de stockage et de réseau à définition logicielle qu’Azure Stack Hub. Vous trouverez ci-dessous un rapide résumé des différentes solutions. Pour plus d’informations, consultez [Comparaison de l’écosystème Azure Stack](../operator/compare-azure-azure-stack.md).

- [Azure](https://azure.microsoft.com) - Utilisez les services de cloud public pour migrer des ressources de calcul à la demande et en libre-service, moderniser des applications existantes et générer de nouvelles applications cloud natives.
- [Azure Stack Edge](/azure/databox-online/data-box-edge-overview)- Accélérez les charges de travail Machine Learning et exécutez des applications en conteneur ou des charges de travail virtualisées en local, sur une appliance gérée par le cloud.
- [Azure Stack HCI](https://azure.microsoft.com/overview/azure-stack/hci) - Exécutez des applications virtualisées localement, remplacez et consolidez une infrastructure de serveur vieillissante et connectez-vous à Azure pour les services cloud.
- [Azure Stack Hub](../operator/azure-stack-overview.md) - Exécutez des applications cloud en local, en étant déconnecté, ou pour satisfaire à des exigences réglementaires, en utilisant des services Azure cohérents.

:::image type="content" source="media/overview/azure-family-updated.png" alt-text="Diagramme des solutions de la famille Azure Stack" border="false":::

## <a name="compare-windows-server-and-azure-stack-hci"></a>Comparer Windows Server et Azure Stack HCI

De nombreux clients se demandent si c’est Windows Server ou Azure Stack HCI qui est mieux adapté à leurs besoins. Le tableau suivant vous aide à déterminer le produit qui convient pour votre organisation. Windows Server et Azure Stack HCI offrent la même expérience utilisateur de haute qualité avec une feuille de route des nouvelles versions.

| Windows Server | Azure Stack HCI |
| --------------- | --------------- |
| Meilleur serveur traditionnel et invité | Meilleur hôte de virtualisation pour un centre de données à définition logicielle, y compris les espaces de stockage direct |
| S’exécute en tout lieu, à l’aide d’un modèle de gestion de licences logicielles traditionnel | S’exécute sur le matériel du fournisseur de votre choix, mais est fourni en tant que service Azure et est facturé sur votre compte Azure |
| Deux options d’installation : Serveur avec expérience utilisateur ou Server Core | Basé sur un noyau de serveur peu personnalisé |

### <a name="when-to-use-windows-server"></a>Quand utiliser Windows Server

| Windows Server | Azure Stack HCI |
| --------------- | --------------- |
| Windows Server est un système d’exploitation hautement polyvalent, avec des dizaines de rôles et des centaines de fonctionnalités, y compris des droits d’invité. | Azure Stack HCI n’inclut pas de droits d’invité et est destiné à être utilisé dans le cadre d’une architecture hyperconvergée moderne. |
| Utilisez Windows Server pour exécuter des machines virtuelles ou pour des installations complètes englobant tous les rôles serveur traditionnels, y compris Active Directory, les services de fichiers, DNS, DHCP, Internet Information Services (IIS), hôte de conteneur/invité, SQL Server, Exchange Server, service Guardian hôte et bien plus encore. | Conçu en tant qu’hôte de virtualisation Hyper-V, Azure Stack HCI est concédé sous licence uniquement pour exécuter un petit nombre de rôles serveur directement ; tous les autres rôles doivent s’exécuter à l’intérieur des machines virtuelles. |

### <a name="when-to-use-azure-stack-hci"></a>Quand utiliser Azure Stack HCI

| Windows Server | Azure Stack HCI |
| --------------- | --------------- |
| Windows Server peut s’exécuter localement ou dans le cloud, mais il ne s’agit pas d’une offre hyperconvergé complète.| Utilisez Azure Stack HCI pour exécuter des machines virtuelles localement, éventuellement étendues sur deux sites et avec des connexions aux services hybrides Azure. Il s’agit d’un moyen simple de moderniser et de sécuriser vos centres de données et succursales, d’obtenir des performances optimales pour les bases de données SQL Server et d’exécuter des bureaux virtuels localement afin de bénéficier d’une latence faible et de préserver la souveraineté des données.|
| Windows Server est un formidable couteau suisse polyvalent pour tous les rôles Windows Server, virtualisés ou non. | Utilisez Azure Stack HCI pour virtualiser des applications d’entreprise classiques comme Exchange, SharePoint et SQL Server, et pour virtualiser des rôles Windows Server comme Serveur de fichiers, DNS, DHCP, IIS et Active Directory. Comprend un accès illimité à toutes les fonctionnalités Hyper-V, comme les machines virtuelles dotées d’une protection maximale.|
| De nombreux déploiements Windows Server s’exécutent sur du matériel vieillissant. | Utilisez Azure Stack HCI pour utiliser une infrastructure à définition logicielle au lieu de baies de stockage ou d’appliances réseau vieillissantes, sans reconcevoir toute l’architecture. Les fonctionnalités Hyper-V, Espaces de stockage direct et Software-Defined Networking (SDN) intégrées sont directement accessibles et gérables. Exécutez les applications dans des machines virtuelles Windows ou Linux.|

## <a name="compare-azure-stack-hub-and-azure-stack-hci"></a>Comparer Azure Stack Hub et Azure Stack HCI

Alors que votre organisation poursuit déjà sa transformation numérique, vous trouvez que vous pouvez changer plus rapidement en utilisant les services de cloud public pour créer des architectures modernes et actualiser les applications héritées. Pourtant, un grand nombre de charges de travail doivent rester locales, à cause d’obstacles technologiques et réglementaires. Utilisez ce tableau pour déterminer quelle stratégie cloud hybride Microsoft fournit ce dont vous avez besoin là où vous en avez besoin, en mettant à votre disposition les innovations du cloud pour les charges de travail où qu’elles soient.

| Azure Stack Hub | Azure Stack HCI |
| --------------- | --------------- |
| Nouvelles qualifications, processus novateurs | Mêmes qualifications, processus familiers |
| Services Azure dans votre centre de données | Connecter votre centre de données aux services Azure |

### <a name="when-to-use-azure-stack-hub"></a>Quand utiliser Azure Stack Hub

| Azure Stack Hub | Azure Stack HCI |
| --------------- | --------------- |
| Utilisez Azure Stack Hub pour bénéficier d’une fonctionnalité Infrastructure-as-a-Service (IaaS), avec une isolation renforcée, un suivi précis de l’utilisation et une facturation interne pour plusieurs locataires colocalisés. Idéal pour les fournisseurs de services et les clouds privés d’entreprise. Modèles issus de la Place de marché Azure. | Azure Stack HCI ne permet pas d’appliquer ni de fournir une multilocation de manière native. |
| Utilisez Azure Stack Hub pour développer et exécuter des applications qui s’appuient sur des services Platform-as-a-Service (PaaS) comme Web Apps, Functions ou Event Hubs en local. Ces services s’exécutent sur Azure Stack Hub exactement comme dans Azure, en fournissant un environnement de développement et d’exécution hybride cohérent. | Azure Stack HCI n’exécute pas de services PaaS en local. |
| Utilisez Azure Stack Hub pour moderniser le déploiement et le fonctionnement d’applications avec des pratiques DevOps comme l’infrastructure en tant que code, l’intégration continue et le déploiement continu (CI/CD), ainsi que des fonctionnalités utiles comme les extensions de machine virtuelle cohérentes avec Azure. Idéal pour les équipes de développement et DevOps. | Azure Stack HCI n’inclut aucun outil DevOps de manière native. |

### <a name="when-to-use-azure-stack-hci"></a>Quand utiliser Azure Stack HCI

| Azure Stack Hub | Azure Stack HCI |
| --------------- | --------------- |
| Azure Stack Hub nécessite au moins 4 nœuds et ses propres commutateurs réseau. | Utilisez Azure Stack HCI pour obtenir l’empreinte mémoire minimale pour les bureaux et branches. Commencez avec seulement 2 nœuds serveur et un réseau dos à dos sans commutateur à des fins de simplicité et d’abordabilité de la charge maximale. Les offres de matériel commencent à 4 lecteurs, 64 Go de mémoire, bien en dessous de 10 000 $/nœud. |
| Azure Stack Hub limite la configurabilité Hyper V et l’ensemble des fonctionnalités à des fins de cohérence avec Azure. | Utilisez Azure Stack HCI pour une virtualisation Hyper-V simple pour des applications d’entreprise classiques comme Exchange, SharePoint et SQL Server, et pour virtualiser des rôles Windows Server comme Serveur de fichiers, DNS, DHCP, IIS et Active Directory. Accès illimité à toutes les fonctionnalités Hyper-V, comme les machines virtuelles dotées d’une protection maximale.|
| Azure Stack Hub n’expose pas ces technologies d’infrastructure. | Utilisez Azure Stack HCI pour utiliser une infrastructure à définition logicielle au lieu de baies de stockage ou d’appliances réseau vieillissantes, sans reconcevoir toute l’architecture. Les fonctionnalités Hyper-V, Espaces de stockage direct et Software-Defined Networking (SDN) intégrées sont directement accessibles et gérables. |

## <a name="whats-new-in-azure-stack-hci"></a>Nouveautés d’Azure Stack HCI

Windows Admin Center version 2009 ajoute un nombre de fonctionnalités dans Azure Stack HCI, notamment :

- **Fonctionnalités d’hébergement Azure Kubernetes Service**: Vous pouvez maintenant installer une préversion d’[Azure Kubernetes Service sur Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/).
- **Ajout du réseau SDN (software defined networking) dans l’Assistant de création de cluster** : L’Assistant de création de cluster comprend maintenant l’option de déployer la fonctionnalité de contrôleur de réseau [SDN (Software Defined Networking)](concepts/software-defined-networking.md) pendant la [création du cluster](deploy/create-cluster.md#step-5-sdn-optional).

Pour plus d’informations sur les nouvelles fonctionnalités dans Windows Admin Center, consultez le [blog Windows Admin Center](https://techcommunity.microsoft.com/t5/windows-admin-center-blog/bg-p/Windows-Admin-Center-Blog).

Les clusters qui exécutent Azure Stack HCI version 20H2 ont les nouvelles fonctionnalités suivantes par rapport aux solutions basées sur Windows Server 2019 :

- **Nouvelles fonctionnalités dans Windows Admin Center** : avec la possibilité de créer et de mettre à jour des clusters hyperconvergés par le biais d’une interface utilisateur intuitive, Azure Stack HCI est plus facile à utiliser que jamais.
- **Clusters étendus pour le basculement automatique** : le clustering multisite avec réplication du réplica de stockage et basculement automatique des machines virtuelles fournit une reprise d’activité et une continuité des activités natives pour les clusters qui utilisent des espaces de stockage direct.
- **Règles d’affinité et d’anti-affinité** : à l’image des zones de disponibilité dans Azure, ces règles permettent de conserver les machines virtuelles et le stockage ensemble ou séparés dans des clusters avec plusieurs domaines d’erreur, tels que les clusters étendus.
- **Intégration du portail Azure** : l’expérience du portail Azure pour Azure Stack HCI est conçue pour afficher tous vos clusters Azure Stack HCI du monde entier, avec de nouvelles fonctionnalités en développement.
- **Accélération GPU pour des charges de travail hautes performances** : les applications IA/ML peuvent tirer parti de l’amélioration des performances avec les GPU.
- **Chiffrement BitLocker** : vous pouvez désormais utiliser BitLocker pour chiffrer le contenu des volumes de données sur Azure Stack HCI, en aidant les administrations et les autres clients à respecter les normes telles que FIPS 140-2 et HIPAA.
- **Amélioration de la vitesse de réparation des volumes des espaces de stockage direct** : Réparez les volumes rapidement et de façon fluide.

Windows Admin Center version 20H2 fournit également une interface utilisateur de mise à jour de cluster pour les clusters basés sur Windows Server, y compris les solutions d’origine Azure Stack HCI. Et même si vous pouvez utiliser l’Assistant Création de cluster avec Windows Server, il ne peut pas créer de clusters Windows Server avec des espaces de stockage direct ; pour cela, vous avez besoin du système d’exploitation Azure Stack HCI.

## <a name="roles-you-can-run-without-virtualizing"></a>Rôles que vous pouvez exécuter sans virtualisation

Azure Stack HCI étant conçu en tant qu’hôte de virtualisation dans lequel vous exécutez toutes vos charges de travail sur des machines virtuelles, les conditions d’Azure Stack HCI ne vous permettent d’exécuter que ce qui est nécessaire à l’hébergement de machines virtuelles.

Vous pouvez donc exécuter les rôles serveur suivants :

- Hyper-V
- Contrôleur réseau et autres composants nécessaires pour le réseau à définition logicielle

Toutefois, les autres rôles et applications doivent s’exécuter à l’intérieur des machines virtuelles. Notez que vous pouvez exécuter les utilitaires, applications et services nécessaires à la gestion et à l’intégrité des machines virtuelles hébergées.

## <a name="video-based-learning"></a>Apprentissage vidéo

La vidéo sur le réseau étendu Azure est disponible ici :

- [Connectivité transparente à Azure avec Windows Server et le réseau hybride](https://www.youtube.com/watch?v=do2_4Y2p9dk)

Vidéos sur la version d’origine d’Azure Stack HCI basée sur Windows Server 2019 :

- [Vue d’ensemble d’Azure Stack et d’Azure Stack HCI](https://aka.ms/AzureStackOverviewVideo)
- [Microsoft Ignite Live 2019 - Getting Started with Azure Stack HCI](https://www.youtube.com/watch?v=vueHIBqNIEU)
- [Discover Azure Stack HCI](https://www.youtube.com/watch?v=4aGZK0Ndmh8&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=13&t=0s)
- [Modernize your retail stores or branch offices with Azure Stack HCI](https://www.youtube.com/watch?v=-JzLhjfkhmM&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=9&t=0s)
- [What’s new for Azure Stack HCI: 45 things in 45 minutes](https://www.youtube.com/watch?v=C5J4IEnlS_E&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=12&t=0s)
- [Jumpstart your Azure Stack HCI deployment](https://www.youtube.com/watch?v=gxaPJLrWy5w&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=11&t=0s)
- [The case of the shrinking data: Data Deduplication in Azure Stack HCI](https://www.youtube.com/watch?v=fmm4iDbDiY4&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=23&t=0s)
- [Dave Kawula’s notes from the field on Azure Stack HCI](https://www.youtube.com/watch?v=OXv7fLlz0ew&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=2&t=0s)

Voici une vidéo Hybrid Cloud Virtual Event :

- [Azure Stack HCI | Hybrid Cloud Virtual Event](https://www.youtube.com/watch?v=nxpoEva-R2Y)

## <a name="next-steps"></a>Étapes suivantes

- [Télécharger Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/)
- [Utiliser Azure Stack HCI avec Windows Admin Center](get-started.md)
