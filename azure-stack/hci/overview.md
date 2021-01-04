---
title: Vue d’ensemble de la solution Azure Stack HCI
description: Azure Stack HCI est une solution de cluster d’infrastructure hyperconvergée qui héberge des charges de travail Windows et Linux virtualisées et leur stockage dans un environnement local hybride. Les services hybrides Azure améliorent le cluster avec des fonctionnalités telles que la supervision basée sur le cloud, Site Recovery et les sauvegardes de machines virtuelles ainsi qu’une vue centralisée de tous vos déploiements Azure Stack HCI dans le portail Azure.
ms.topic: overview
author: khdownie
ms.author: v-kedow
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 12/15/2020
ms.openlocfilehash: 6eef8388367bb1bba3f419fba5b61c54fd729743
ms.sourcegitcommit: 32d77de1a554315f53473407279e464a72aa9aa1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97515008"
---
# <a name="azure-stack-hci-solution-overview"></a>Vue d’ensemble de la solution Azure Stack HCI

> S’applique à : Azure Stack HCI, version 20H2

Azure Stack HCI est une solution de cluster d’infrastructure hyperconvergée qui héberge des charges de travail Windows et Linux virtualisées et leur stockage dans un environnement local hybride. Les services hybrides Azure améliorent le cluster avec des fonctionnalités telles que la supervision basée sur le cloud, Site Recovery et les sauvegardes de machines virtuelles ainsi qu’une vue centralisée de tous vos déploiements Azure Stack HCI dans le portail Azure. Vous pouvez gérer le cluster avec vos outils existants, notamment Windows Admin Center et PowerShell.

Azure Stack HCI,version 20H2 est désormais [disponible en téléchargement](https://azure.microsoft.com/products/azure-stack/hci/hci-download/). Il est destiné aux clusters locaux exécutant des charges de travail virtualisées, avec des connexions cloud hybrides intégrées. En tant que tel, Azure Stack HCI est fourni sous la forme d’un service Azure et facturé sur un abonnement Azure. Azure Stack HCI offre également la possibilité d’héberger Azure Kubernetes Service. Pour plus d’informations, consultez [Azure Kubernetes Service sur Azure Stack HCI](../aks-hci/overview.md).

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
- **Facturation :** Payez l’utilisation d’Azure Stack HCI dans le cadre de votre abonnement Azure.

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

- Un cluster de deux serveurs ou plus référencés dans le [Catalogue Azure Stack HCI](https://hcicatalog.azurewebsites.net), acheté auprès du partenaire fournisseur de matériel Microsoft de votre choix
- Un [abonnement Azure](https://azure.microsoft.com/)
- Une connexion Internet pour chaque serveur du cluster qui peut se connecter via le trafic HTTPS sortant aux points de terminaison Azure connus au moins tous les 30 jours
- Pour les clusters étendus sur plusieurs sites, vous devez disposer d’au moins une connexion de 1 Go entre les sites (une connexion RDMA de 25 Go est recommandée), avec une latence moyenne de 5 ms aller-retour si vous souhaitez effectuer une réplication synchrone dans laquelle les écritures se produisent simultanément sur les deux sites.
- Si vous pensez utiliser SDN (Software Defined Networking), vous avez besoin d’un disque dur virtuel (VHD) pour le système d’exploitation Azure Stack HCI pour créer des machines virtuelles de contrôleur de réseau (voir [Planifier le déploiement du contrôleur de réseau](concepts/network-controller.md))

Pour plus d’informations, consultez [Configuration système requise](concepts/system-requirements.md). Pour obtenir la configuration requise d’Azure Kubernetes Service sur Azure Stack HCI, consultez [Configuration requise d’AKS sur Azure Stack HCI](../aks-hci/overview.md#what-you-need-to-get-started).

## <a name="hardware-partners"></a>Partenaires fournisseurs de matériel

Vous pouvez acheter des solutions Azure Stack HCI validées auprès du partenaire Microsoft de votre choix pour être opérationnel sous un délai rapide de conception et de création. Les partenaires Microsoft offrent également un point de contact unique pour les services d’implémentation et de support technique. Vous pouvez acheter des nœuds validés ou un système intégré, qui comprend le système d’exploitation Azure Stack HCI préinstallé et les extensions partenaires pour les mises à jour du pilote et du microprogramme.

Visitez la page [Solutions Azure Stack HCI](https://azure.microsoft.com/overview/azure-stack/hci) ou parcourez le [Catalogue Azure Stack HCI](https://hcicatalog.azurewebsites.net) pour voir plus de 70 solutions Azure Stack HCI disponibles auprès de partenaires Microsoft comme ASUS, Axellio, Blue Chip, DataON, Dell EMC, Fujitsu, HPE, Hitachi, Huawei, Lenovo, NEC, primeLine Solutions, QCT, SecureGUARD et Supermicro.

## <a name="software-partners"></a>Partenaires logiciels

De nombreux partenaires Microsoft travaillent sur des logiciels qui étendent les fonctionnalités d’Azure Stack HCI tout en permettant aux administrateurs informatiques d’utiliser des outils familiers. Pour plus d’informations, consultez [Utility applications for Azure Stack HCI](concepts/utility-applications.md) (Utilitaires pour Azure Stack HCI).

## <a name="licensing-billing-and-pricing"></a>Licences, facturation et tarifs

La facturation d’Azure Stack HCI est basée sur des frais d’abonnement mensuels par cœur de processeur physique, et non sur une licence pour la durée des droits de propriété intellectuelle. Quand les clients se connectent à Azure, le nombre de cœurs utilisés est automatiquement chargé et évalué à des fins de facturation. Le coût ne varie pas en fonction de la consommation au-delà des cœurs de processeur physique ; ainsi, l’ajout de machines virtuelles n’entraîne pas de surcoût et les clients capables d’exécuter des environnements virtuels plus denses sont récompensés.

Les clients peuvent acheter auprès d’un partenaire fournisseur de matériel des serveurs validés sur lesquels le système d’exploitation Azure Stack HCI est préinstallé, ou ils peuvent acheter des serveurs complets validés auprès d’un fabricant OEM, puis s’abonner au service Azure Stack HCI et [télécharger le système d’exploitation Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/).

## <a name="the-azure-stack-family"></a>La famille Azure Stack

Azure Stack HCI fait partie de la famille Azure et Azure Stack, et utilise les mêmes logiciels de calcul, de stockage et de réseau à définition logicielle qu’Azure Stack Hub. Vous trouverez ci-dessous un rapide résumé des différentes solutions. Pour plus d’informations, consultez [Comparaison de l’écosystème Azure Stack](../operator/compare-azure-azure-stack.md).

- [Azure](https://azure.microsoft.com) - Utilisez les services de cloud public pour migrer des ressources de calcul à la demande et en libre-service, moderniser des applications existantes et générer de nouvelles applications cloud natives.
- [Azure Stack Edge](/azure/databox-online/data-box-edge-overview)- Accélérez les charges de travail Machine Learning et exécutez des applications en conteneur ou des charges de travail virtualisées en local, sur une appliance gérée par le cloud.
- [Azure Stack HCI](https://azure.microsoft.com/overview/azure-stack/hci) - Exécutez des applications virtualisées localement, remplacez et consolidez une infrastructure de serveur vieillissante et connectez-vous à Azure pour les services cloud.
- [Azure Stack Hub](../operator/azure-stack-overview.md) - Exécutez des applications cloud en local, en étant déconnecté, ou pour satisfaire à des exigences réglementaires, en utilisant des services Azure cohérents.

:::image type="content" source="media/overview/azure-family-updated.png" alt-text="Diagramme des solutions de la famille Azure Stack" border="false":::

## <a name="whats-new-in-azure-stack-hci"></a>Nouveautés d’Azure Stack HCI

Windows Admin Center version 2009 ajoute un nombre de fonctionnalités dans Azure Stack HCI, notamment :

- **Fonctionnalités d’hébergement Azure Kubernetes Service**: Vous pouvez maintenant installer une préversion d’[Azure Kubernetes Service sur Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/).
- **Ajout du réseau SDN (software defined networking) dans l’Assistant de création de cluster** : L’Assistant de création de cluster comprend maintenant l’option de déployer la fonctionnalité de contrôleur de réseau [SDN (Software Defined Networking)](concepts/software-defined-networking.md) pendant la [création du cluster](deploy/create-cluster.md#step-5-sdn-optional).
- **Améliorations de l’accès direct à la mémoire à distance (RDMA) dans l’Assistant Création de cluster** : L’Assistant de création de cluster peut désormais configurer RDMA pour les cartes réseau iWARP et RoCE, notamment Data Center Bridging (DCB).

Pour plus d’informations sur les nouvelles fonctionnalités, consultez l’[annonce relative à la disponibilité générale de l’extension de création de cluster dans Windows Admin Center](https://techcommunity.microsoft.com/t5/windows-admin-center-blog/announcing-general-availability-of-the-cluster-creation/ba-p/1978332).

Les clusters qui exécutent Azure Stack HCI version 20H2 ont les nouvelles fonctionnalités suivantes par rapport aux solutions basées sur Windows Server 2019 :

- **Nouvelles fonctionnalités dans Windows Admin Center** : avec la possibilité de créer et de mettre à jour des clusters hyperconvergés par le biais d’une interface utilisateur intuitive, Azure Stack HCI est plus facile à utiliser que jamais.
- **Clusters étendus pour le basculement automatique** : le clustering multisite avec réplication du réplica de stockage et basculement automatique des machines virtuelles fournit une reprise d’activité et une continuité des activités natives.
- **Règles d’affinité et d’anti-affinité** : à l’image des zones de disponibilité dans Azure, ces règles permettent de conserver les machines virtuelles et le stockage ensemble ou séparés dans des clusters avec plusieurs domaines d’erreur, tels que les clusters étendus.
- **Intégration du portail Azure** : l’expérience du portail Azure pour Azure Stack HCI est conçue pour afficher tous vos clusters Azure Stack HCI du monde entier, avec de nouvelles fonctionnalités en développement.
- **Accélération GPU pour des charges de travail hautes performances** : les applications IA/ML peuvent tirer parti de l’amélioration des performances avec les GPU.
- **Chiffrement BitLocker** : vous pouvez désormais utiliser BitLocker pour chiffrer le contenu des volumes de données sur Azure Stack HCI, en aidant les administrations et les autres clients à respecter les normes telles que FIPS 140-2 et HIPAA.
- **Amélioration de la vitesse de réparation des volumes des espaces de stockage direct** : Réparez les volumes rapidement et de façon fluide.

Pour en savoir plus sur les nouveautés d’Azure Stack HCI 20H2, [regardez cette vidéo](https://www.youtube.com/watch?v=DPG7wGhh3sAa) de Microsoft Inspire.

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
- [Créer un cluster Azure Stack HCI et l’inscrire auprès d’Azure](deploy/deployment-quickstart.md)
- [Utiliser Azure Stack HCI avec Windows Admin Center](get-started.md)
