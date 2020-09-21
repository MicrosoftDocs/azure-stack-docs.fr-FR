---
title: Gérer des clusters Azure Stack HCI – Windows Admin Center
description: Découvrez comment gérer vos clusters sur Azure Stack HCI à l’aide de Windows Admin Center.
ms.topic: how-to
author: v-dasis
ms.author: v-dasis
ms.reviewer: jgerend
ms.date: 07/21/2020
ms.openlocfilehash: d1b8556908da268bbd99c7aa9341128c9dc5be36
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90573783"
---
# <a name="manage-azure-stack-hci-clusters-using-windows-admin-center"></a>Gérer des clusters Azure Stack HCI avec Windows Admin Center

> S’applique à : Azure Stack HCI, version 20H2 ; Windows Server 2019

Windows Admin Center peut être utilisé pour gérer vos clusters Azure Stack HCI. Plus précisément, vous allez utiliser l’extension Gestionnaire de cluster dans Windows Admin Center pour gérer vos clusters.

## <a name="view-the-cluster-dashboard"></a>Afficher le tableau de bord du cluster

Le tableau de bord du cluster affiche des informations sur l’intégrité et les performances du cluster.

:::image type="content" source="media/manage-cluster/cluster-dashboard.png" alt-text="Écran du tableau de bord du cluster" lightbox="media/manage-cluster/cluster-dashboard.png":::

Pour afficher ces informations, sélectionnez le nom du cluster sous **Toutes les connexions** puis, sous **Outils** sur la gauche, sélectionnez **Tableau de bord**. Vous pouvez afficher les informations suivantes :

- Alertes d’événement de cluster
- Liste des serveurs joints au cluster
- Liste des machines virtuelles en cours d’exécution sur le cluster
- Liste des lecteurs de disque disponibles sur le cluster
- Liste des volumes disponibles sur le cluster
- Utilisation totale du processeur pour le cluster
- Utilisation totale de la mémoire pour le cluster
- Utilisation totale du stockage pour le cluster
- Nombre total d’opérations d’entrée/de sortie de cluster/seconde (IOPS)
- Latence moyenne du cluster en millisecondes

## <a name="change-cluster-storage-settings"></a>Modifier les paramètres de stockage du cluster

Vous pouvez modifier deux paramètres relatifs aux espaces de stockage direct qui peuvent être appliqués à votre cluster.

1. Dans Windows Admin Center, sélectionnez **Gestionnaire de cluster** à partir de la flèche déroulante du haut.
1. Sous **Outils**, sélectionnez **Paramètres** en bas.
1. Pour configurer le cache de stockage, sélectionnez **Espaces de stockage direct**, puis configurez les éléments suivants :

   - Pour le **cache persistant**, sélectionnez **Activé** ou **Désactivé**

   - Pour le **mode de cache pour HDD**, sélectionnez **Lecture seule**, **Écriture seule** ou **Lecture/écriture**

   - Pour le **mode de cache pour SSD**, sélectionnez **Lecture seule**, **Écriture seule** ou **Lecture/écriture**

        :::image type="content" source="media/manage-cluster/cluster-settings-ssd.png" alt-text="écran Espaces de stockage direct du cluster" lightbox="media/manage-cluster/cluster-settings-ssd.png":::

1. Pour utiliser la mémoire du serveur afin de mettre en cache les lectures fréquentes, sélectionnez **In-memory cache** (Cache en mémoire) et spécifiez la mémoire maximale à utiliser par serveur. Consultez également [Compréhension du cache dans Azure Stack HCI](../concepts/cache.md).

    :::image type="content" source="media/manage-cluster/cluster-settings-memory.png" alt-text="écran du cache en mémoire du cluster" lightbox="media/manage-cluster/cluster-settings-memory.png":::

## <a name="change-cluster-general-settings"></a>Modifier les paramètres généraux du cluster

Cinq paramètres généraux peuvent être appliqués à votre cluster. Ici, vous pouvez définir et gérer des points d’accès, le comportement d’arrêt du nœud, le chiffrement du trafic, l’équilibrage de charge de la machine virtuelle et le témoin de cluster.

1. Dans Windows Admin Center, sélectionnez **Gestionnaire de cluster** à partir de la flèche déroulante du haut.
1. Sous **Outils**, sélectionnez **Paramètres**.
1. Pour changer le nom du cluster, sélectionnez **Point d’accès** et entrez le nouveau nom.

    :::image type="content" source="media/manage-cluster/cluster-settings-access.png" alt-text="Scénario de cluster étendu actif/actif" lightbox="media/manage-cluster/cluster-settings-access.png":::

1. Pour contrôler le comportement d’arrêt du nœud, sélectionnez **Comportement d’arrêt du nœud** et vérifiez que la case est cochée. Vous déplacez ainsi d’abord toutes les machines virtuelles du nœud pour permettre un arrêt correct du nœud.

    :::image type="content" source="media/manage-cluster/cluster-settings-shutdown.png" alt-text="écran du comportement d’arrêt du nœud de cluster" lightbox="media/manage-cluster/cluster-settings-shutdown.png":::

1. Pour chiffrer les connexions SMB utilisées pour envoyer des données entre des nœuds de cluster, sélectionnez **Chiffrement du trafic de cluster**, puis **Chiffrer** dans les zones de liste déroulante pour les éléments suivants :

   - **Trafic principal** : chiffre le trafic envoyé via NetFT (carte virtuelle de cluster) sur le port 3343

   - **Trafic du serveur** : chiffre le trafic de volume partagé de cluster et de couche de bus de stockage

        :::image type="content" source="media/manage-cluster/cluster-settings-encryption.png" alt-text="écran de chiffrement du trafic de cluster" lightbox="media/manage-cluster/cluster-settings-encryption.png":::

1. Pour équilibrer automatiquement la charge des machines virtuelles sur le cluster, sélectionnez **Équilibrage de charge des machines virtuelles**, puis procédez comme suit :

   - Pour **Équilibrer les machines virtuelles**, sélectionnez l’action appropriée
   - Pour **Intensité**, sélectionnez le comportement approprié

     Pour plus d’informations sur la façon dont cela fonctionne, consultez [Vue d’ensemble de l’équilibrage de charge des machines virtuelles](/windows-server/failover-clustering/vm-load-balancing-overview).

        :::image type="content" source="media/manage-cluster/cluster-settings-vm-load.png" alt-text="écran de l’équilibrage de charge des machines virtuelles de cluster" lightbox="media/manage-cluster/cluster-settings-vm-load.png":::

1. Pour sélectionner un type de témoin de quorum, sélectionnez **Témoin**, puis l’une des options suivantes :

   - **Témoin de cloud** pour utiliser une ressource cloud Azure comme témoin
   - **Témoin de disque** pour utiliser une ressource de disque en tant que témoin (ne pas utiliser pour les clusters étendus)
   - **Témoin de partage de fichiers** pour utiliser un partage de fichiers comme témoin

        Pour plus d’informations, consultez [Présentation du quorum de cluster et de pool dans Azure Stack HCI](../concepts/quorum.md).

        :::image type="content" source="media/manage-cluster/cluster-settings-witness.png" alt-text="écran du témoin de cluster" lightbox="media/manage-cluster/cluster-settings-witness.png":::

## <a name="change-cluster-hyper-v-settings"></a>Modifier les paramètres Hyper-V du cluster

Cinq paramètres d’hôte Hyper-V peuvent être appliqués à votre cluster.

1. Dans Windows Admin Center, sélectionnez **Gestionnaire de cluster** à partir de la flèche déroulante du haut.
1. Sous **Outils**, sélectionnez **Paramètres**.
1. Sélectionnez **Général**, puis utilisez les paramètres suivants :

   - **Chemin du disque dur virtuel** : spécifiez le dossier par défaut pour le stockage des fichiers de disque dur virtuel.

   - **Chemin de la machine virtuelle** : spécifiez le dossier par défaut pour le stockage des fichiers de configuration de machine virtuelle.

   - **Type de planificateur d’hyperviseur** : sélectionnez **Planificateur principal** ou **Planificateur classique**. Cela détermine la façon dont l’hyperviseur planifie les processus virtuels à exécuter sur des processeurs physiques qui utilisent le multithreading simultané (également appelé SMT ou hyper-threading).

        :::image type="content" source="media/manage-cluster/cluster-settings-hyperv.png" alt-text="écran des paramètres Hyper-V du cluster" lightbox="media/manage-cluster/cluster-settings-hyperv.png":::

1. Pour autoriser la redirection des ressources et appareils locaux à partir de machines virtuelles, sélectionnez **Mode de session étendue**. Notez que les connexions en mode de session étendue nécessitent un système d’exploitation invité pris en charge.

    :::image type="content" source="media/manage-cluster/cluster-settings-session.png" alt-text="écran du mode de session étendue Hyper-V du cluster" lightbox="media/manage-cluster/cluster-settings-session.png":::

1. Pour permettre aux machines virtuelles de répartir des nœuds NUMA physiques, sélectionnez **Répartition NUMA**. La répartition NUMA (Non-Uniform Memory Architecture) peut fournir une machine virtuelle avec plus de mémoire que ce qui est disponible sur un seul nœud NUMA.

    :::image type="content" source="media/manage-cluster/cluster-settings-numa.png" alt-text="écran de répartition NUMA du cluster" lightbox="media/manage-cluster/cluster-settings-numa.png":::

1. Pour spécifier le nombre de machines virtuelles qui peuvent être déplacées simultanément pendant l’exécution (migration dynamique), sélectionnez **Migration dynamique**, un nombre, puis spécifiez les éléments suivants :

   - Pour **Protocole d’authentification**, sélectionnez **CredSSP** ou **Kerberos**.

   - Pour **Option de performance**, sélectionnez **Compression** ou **SMB**. Les données compressées sont envoyées via une connexion TCP/IP.

   - Cochez la case **Utiliser n’importe quel réseau** pour utiliser n’importe quel réseau disponible sur un nœud pour effectuer la migration

        :::image type="content" source="media/manage-cluster/cluster-settings-liv-migration.png" alt-text="écran Migration dynamique du cluster" lightbox="media/manage-cluster/cluster-settings-liv-migration.png":::

1. Pour spécifier le nombre de migrations de stockage pouvant être effectuées en même temps, sélectionnez **Migration de stockage**, puis un nombre.

    :::image type="content" source="media/manage-cluster/cluster-settings-sto-migration.png" alt-text="écran Migration de stockage du cluster" lightbox="media/manage-cluster/cluster-settings-sto-migration.png":::

## <a name="next-steps"></a>Étapes suivantes

- Pour superviser votre cluster, consultez [Superviser Azure Stack HCI avec Azure Monitor](azure-monitor.md).
