---
title: Déployer SQL Server sur Azure Stack HCI (AKS-HCI)
description: Cette rubrique fournit des conseils sur la façon de déployer SQL Server sur Azure Stack HCI.
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 01/11/2021
ms.openlocfilehash: 433a9b8b7bf6f57cac936b24d7654b94b8e8c050
ms.sourcegitcommit: f194f9ca4297864500e62d8658674a0625b29d1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102186804"
---
# <a name="deploy-sql-server-on-azure-stack-hci"></a>Déployer SQL Server sur Azure Stack HCI

>S’applique à : Azure Stack HCI, version 20H2 ; SQL Server (toutes les versions prises en charge)

Cette rubrique fournit des conseils sur la planification, la configuration et le déploiement de SQL Server sur le système d’exploitation Azure Stack HCI. Le système d’exploitation est une solution de cluster d’infrastructure hyperconvergée (HCI) qui héberge des charges de travail Windows et Linux virtualisées et leur stockage dans un environnement local hybride.

## <a name="solution-overview"></a>Vue d’ensemble de la solution
Azure Stack HCI offre une plateforme hautement disponible, économique et flexible permettant d’exécuter SQL Server et la fonctionnalité Espaces de stockage direct. Azure Stack HCI peut exécuter des charges de travail de traitement transactionnel en ligne (OLTP), l’entreposage de données et le Business Intelligence, ainsi que l’IA et l’analytique avancée sur les Big Data.

La flexibilité de la plateforme est particulièrement importante pour les bases de données stratégiques. Vous pouvez exécuter SQL Server sur des machines virtuelles qui utilisent Windows Server ou Linux, ce qui vous permet de consolider plusieurs charges de travail de base de données et d’ajouter des machines virtuelles à votre environnement Azure Stack HCI en fonction des besoins. Azure Stack HCI vous permet également d’intégrer SQL Server à Azure Site Recovery pour fournir une solution fiable et sécurisée pour la migration, la restauration et la protection basée sur le cloud des données de votre organisation.

## <a name="deploy-sql-server"></a>Déployer SQL Server
Cette section décrit de manière générale comment acquérir du matériel pour SQL Server sur Azure Stack HCI et utiliser Windows Admin Center pour gérer le système d’exploitation sur vos serveurs. Vous y trouverez des informations sur la configuration de SQL Server, sur la supervision et sur le réglage des performances, ainsi que sur l’utilisation de la haute disponibilité (HA) et des services hybrides Azure.

### <a name="step-1-acquire-hardware-from-the-azure-stack-hci-catalog"></a>Étape 1 : Acquérir du matériel à partir du catalogue Azure Stack HCI
Tout d’abord, vous devez vous procurer du matériel. Le moyen le plus simple est de localiser votre partenaire de matériel Microsoft préféré dans le [catalogue Azure Stack HCI](https://hcicatalog.azurewebsites.net) et d’acheter un système intégré sur lequel le système d’exploitation Azure Stack HCI est préinstallé. Dans le catalogue, vous pouvez appliquer un filtre de façon à afficher le matériel fournisseur qui est optimisé pour ce type de charge de travail.

Dans le cas contraire, vous devez déployer le système d’exploitation Azure Stack HCI sur votre propre matériel. Pour plus d’informations sur les options de déploiement Azure Stack HCI et sur l’installation de Windows Admin Center, consultez [Déployer le système d’exploitation Azure Stack HCI](./operating-system.md).

Ensuite, utilisez Windows Admin Center pour [créer un cluster Azure Stack HCI](./create-cluster.md).

### <a name="step-2-install-sql-server-on-azure-stack-hci"></a>Étape 2 : Installer SQL Server sur Azure Stack HCI
Vous pouvez installer SQL Server sur des machines virtuelles exécutant Windows Server ou Linux en fonction de vos besoins.

Pour obtenir des instructions sur l’installation de SQL Server, consultez les pages suivantes :
- [Guide d’installation SQL Server pour Windows](/sql/database-engine/install-windows/install-sql-server?preserve-view=true&view=sql-server-ver15).
- [Conseils d’installation pour SQL Server sur Linux](/sql/linux/sql-server-linux-setup?preserve-view=true&view=sql-server-ver15).

### <a name="step-3-monitor-and-performance-tune-sql-server"></a>Étape 3 : Superviser SQL Server et régler ses performances
Microsoft fournit un ensemble complet d’outils permettant de surveiller les événements dans SQL Server et de paramétrer la structure physique de la base de données. Le choix de l’outil dépend du type de supervision ou de paramétrage que vous souhaitez effectuer.

Pour garantir les performances et l’intégrité de vos instances SQL Server sur Azure Stack HCI, consultez la page sur [les outils de surveillance et de paramétrage des performances](/sql/relational-databases/performance/performance-monitoring-and-tuning-tools?preserve-view=true&view=sql-server-ver15).

Pour le paramétrage de SQL Server 2017 et SQL Server 2016, consultez [Options de configuration et mises à jour recommandées pour SQL Server 2017 et 2016 avec des charges de travail hautes performances](https://support.microsoft.com/help/4465518/recommended-updates-and-configurations-for-sql-server).

### <a name="step-4-use-sql-server-high-availability-features"></a>Étape 4 : Utiliser les fonctionnalités de haute disponibilité de SQL Server
Azure Stack HCI s’appuie sur le [clustering de basculement Windows Server avec SQL Server](/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) (WSFC) pour prendre en charge SQL Server s’exécutant sur des machines virtuelles en cas de défaillance matérielle. SQL Server offre également des [groupes de disponibilité Always On](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) (AG) qui fournissent une haute disponibilité au niveau de la base de données, conçue pour aider à résoudre les erreurs d’application et de logiciel. En plus des fonctionnalités WSFC et AG, Azure Stack HCI peut utiliser [une instance de cluster de basculement (FCI) Always On](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server), qui est basée sur la technologie [Espaces de stockage direct](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) pour le stockage partagé.

Ces options fonctionnent toutes avec le [témoin cloud](/windows-server/failover-clustering/deploy-cloud-witness) Microsoft Azure pour le contrôle de quorum. Nous vous recommandons d’utiliser les règles [d’antiaffinité](/windows-server/failover-clustering/cluster-affinity) de cluster dans WSFC pour les machines virtuelles placées sur différents nœuds physiques afin de maintenir le temps d’activité pour SQL Server en cas de défaillance de l’hôte lors de la configuration de groupes de disponibilité Always On.

### <a name="step-5-set-up-azure-hybrid-services"></a>Étape 5 : Configurer les services hybrides Azure
Il existe plusieurs services hybrides Azure que vous pouvez utiliser pour sécuriser vos applications et vos données SQL Server. [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) est une solution de reprise d’activité en tant que service (DRaaS). Pour plus d’informations sur l’utilisation de ce service pour protéger le back end SQL Server d’une application afin de maintenir les charges de travail en ligne, consultez [Configurer la récupération après sinistre pour SQL Server](/azure/site-recovery/site-recovery-sql).

Le service [Sauvegarde Azure](https://azure.microsoft.com/services/backup/) vous permet de définir des stratégies de sauvegarde pour protéger les charges de travail d’entreprise. Il prend en charge la sauvegarde et la restauration de la cohérence SQL Server. Pour plus d’informations sur la sauvegarde de vos données SQL locales, consultez [Installer un serveur de sauvegarde Azure](/azure/backup/backup-azure-microsoft-azure-backup).

Vous pouvez également utiliser la fonctionnalité de [sauvegarde managée SQL Server](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure?preserve-view=true&view=sql-server-ver15) dans SQL Server pour gérer les sauvegardes du Stockage Blob Azure.

Pour plus d’informations sur l’utilisation de cette option qui convient à l’archivage hors site, consultez : 

- [Tutoriel : Utiliser le service Stockage Blob Azure avec SQL Server 2016](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016?preserve-view=true&view=sql-server-ver15)
- [Démarrage rapide : Sauvegarde et restauration de SQL Server avec le service de stockage Blob Azure](/sql/relational-databases/tutorial-sql-server-backup-and-restore-to-azure-blob-storage-service?preserve-view=true&tabs=SSMS&view=sql-server-ver15)

Outre ces scénarios de sauvegarde, vous pouvez configurer d’autres services de base de données offerts par SQL Server, notamment [Azure Data Factory](/azure/machine-learning/team-data-science-process/move-sql-azure-adf) et [Azure Feature Pack pour Integration Services (SSIS)](/sql/integration-services/azure-feature-pack-for-integration-services-ssis?preserve-view=true&view=sql-server-ver15).

## <a name="next-steps"></a>Étapes suivantes
Pour plus d'informations sur l'utilisation de SQL Server, consultez :
- [Tutoriel : Bien démarrer avec le moteur de base de données](/sql/relational-databases/tutorial-getting-started-with-the-database-engine?preserve-view=true&view=sql-server-ver15)