---
title: Gérer des clusters Azure Stack HCI – Windows Admin Center
description: Découvrez comment gérer vos clusters sur Azure Stack HCI à l’aide de Windows Admin Center.
ms.topic: how-to
author: v-dasis
ms.author: v-dasis
ms.reviewer: jgerend
ms.date: 01/22/2021
ms.openlocfilehash: 4b6e7537a1111ed2e38d1783acf7197a4249b2ac
ms.sourcegitcommit: e772df8ac78c86d834a68d1a8be83b7f738019b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98772098"
---
# <a name="manage-azure-stack-hci-clusters-using-windows-admin-center"></a>Gérer des clusters Azure Stack HCI avec Windows Admin Center

> S’applique à : Azure Stack HCI, version 20H2 ; Windows Server 2019

Windows Admin Center peut être utilisé pour gérer vos clusters Azure Stack HCI. Plus précisément, vous allez utiliser la fonctionnalité Gestionnaire de cluster dans Windows Admin Center pour gérer vos clusters.

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

## <a name="change-storage-settings"></a>Modifier les paramètres de stockage

Vous pouvez choisir d’utiliser la mémoire du serveur afin de mettre en cache les lectures fréquentes et de spécifier la mémoire maximale à utiliser par serveur. Pour plus d’informations, consultez [Présentation du cache dans Azure Stack HCI](../concepts/cache.md).

1. Dans Windows Admin Center, sélectionnez **Gestionnaire de cluster** à partir de la flèche déroulante du haut.
1. Sous **Outils**, sélectionnez **Paramètres** en bas.
1. Sélectionnez **Mémoire en cache** et entrez le nouveau nom.

    :::image type="content" source="media/manage-cluster/cluster-settings-memory.png" alt-text="écran du cache en mémoire du cluster" lightbox="media/manage-cluster/cluster-settings-memory.png":::

1. Vous pouvez modifier le nom du pool de stockage que la fonctionnalité Espaces de stockage direct utilise. Sélectionnez **Pools de stockage** et entrez le nouveau nom. Cela s’applique aux clusters étendus.

    :::image type="content" source="media/manage-cluster/cluster-settings-storage-pools.png" alt-text="Écran Pool de stockage du cluster" lightbox="media/manage-cluster/cluster-settings-storage-pools.png":::

1. Vous pouvez modifier les paramètres de la fonctionnalité Espaces de stockage direct. Sélectionnez **Espaces de stockage direct** et modifiez les paramètres suivants en fonction des besoins :

    - **Persistent cache (Cache permanent)**  : activez ou désactivez le cache permanent.
    - **Cache mode for HDD (Mode de cache pour HDD)**  : changez le mode de cache pour les lecteurs HDD.
    - **Cache mode for SSD (Mode de cache pour SSD)**  : changez le cache pour les lecteurs SSD.

    :::image type="content" source="media/manage-cluster/cluster-settings-storage-spaces-direct.png" alt-text="écran Espaces de stockage direct du cluster" lightbox="media/manage-cluster/cluster-settings-storage-spaces-direct.png":::

## <a name="change-cluster-settings"></a>Modifier les paramètres du cluster

Plusieurs paramètres généraux peuvent être appliqués à votre cluster. Ici, vous pouvez définir et gérer des points d’accès, le comportement d’arrêt du nœud, le chiffrement du trafic, l’équilibrage de charge de la machine virtuelle et le témoin de cluster.

1. Dans Windows Admin Center, sélectionnez **Gestionnaire de cluster** à partir de la flèche déroulante du haut.
1. Sous **Outils**, sélectionnez **Paramètres**.
1. Pour changer le nom du cluster, sélectionnez **Point d’accès** et entrez le nouveau nom.

    :::image type="content" source="media/manage-cluster/cluster-settings-access.png" alt-text="Scénario de cluster étendu actif/actif" lightbox="media/manage-cluster/cluster-settings-access.png":::

1. Pour contrôler le comportement d’arrêt du nœud, sélectionnez **Comportement d’arrêt du nœud** et vérifiez que la case est cochée. Vous déplacez ainsi d’abord toutes les machines virtuelles du nœud pour permettre un arrêt correct du nœud.

    :::image type="content" source="media/manage-cluster/cluster-settings-shutdown.png" alt-text="écran du comportement d’arrêt du nœud de cluster" lightbox="media/manage-cluster/cluster-settings-shutdown.png":::

1. Pour chiffrer les connexions SMB utilisées pour envoyer des données entre des nœuds de cluster, sélectionnez **Chiffrement du trafic de cluster**, puis **Chiffrer** dans les zones de liste déroulante pour les éléments suivants :

   - **Trafic principal** : chiffre le trafic envoyé via NetFT (carte virtuelle de cluster) sur le port 3343

   - **Storage traffic (Trafic du stockage)**  : chiffre le trafic du volume partagé de cluster et au niveau du bus de stockage.

        :::image type="content" source="media/manage-cluster/cluster-settings-encryption.png" alt-text="écran de chiffrement du trafic de cluster" lightbox="media/manage-cluster/cluster-settings-encryption.png":::

1. Pour équilibrer automatiquement la charge des machines virtuelles sur le cluster, sélectionnez **Équilibrage de charge des machines virtuelles**, puis procédez comme suit :

   - Pour **Équilibrer les machines virtuelles**, sélectionnez l’action appropriée
   - Pour **Intensité**, sélectionnez le comportement approprié

     Pour plus d’informations sur la façon dont cela fonctionne, consultez [Vue d’ensemble de l’équilibrage de charge des machines virtuelles](/windows-server/failover-clustering/vm-load-balancing-overview).

        :::image type="content" source="media/manage-cluster/cluster-settings-vm-load.png" alt-text="écran de l’équilibrage de charge des machines virtuelles de cluster" lightbox="media/manage-cluster/cluster-settings-vm-load.png":::

1. Pour sélectionner un type de témoin de quorum, sélectionnez **Témoin**, puis pour **Type de témoin**, sélectionnez l’une des options suivantes :

   - **Témoin de cloud** pour utiliser une ressource cloud Azure comme témoin
   - **Témoin de disque** pour utiliser une ressource de disque en tant que témoin (ne pas utiliser pour les clusters étendus)
   - **Témoin de partage de fichiers** pour utiliser un partage de fichiers comme témoin

        Pour plus d’informations sur la configuration d’un témoin, consultez [Configurer un témoin de cluster](witness.md). Consultez également [Présentation du quorum de cluster et de pool dans Azure Stack HCI](../concepts/quorum.md).

        :::image type="content" source="media/manage-cluster/cluster-settings-witness.png" alt-text="écran du témoin de cluster" lightbox="media/manage-cluster/cluster-settings-witness.png":::

1. Pour utiliser des règles d’affinité afin de contrôler le placement des machines virtuelles entre les serveurs hôtes et les sites, sélectionnez **Règles d’affinité**, puis cliquez sur **Créer une règle**. Pour plus d’informations sur la configuration de règles, consultez [Créer des règles d’affinité entre serveur et site pour les machines virtuelles](vm-affinity.md).

    :::image type="content" source="media/manage-cluster/affinity-rules.png" alt-text="Écran Règles d’affinité de cluster" lightbox="media/manage-cluster/affinity-rules.png":::

1. Pour sélectionner la quantité de données à envoyer à Microsoft pour les diagnostics, sélectionnez **Données de diagnostic**, puis l’une des options suivantes :

    - **Diagnostic data off (Security) (Données de diagnostic désactivées (sécurité))**  : aucune donnée n’est envoyée.
    - **Required (Basic) (Requis (de base))**  : des données minimales sont envoyées pour que les éléments soient sécurisés et à jour.
    - **Optional (Full) (Facultatif (complet))**  : toutes les données applicables sont envoyées.

    :::image type="content" source="media/manage-cluster/cluster-diagnostic-data.png" alt-text="Écran Diagnostics de données de cluster" lightbox="media/manage-cluster/cluster-diagnostic-data.png":::

## <a name="change-hyper-v-settings"></a>Modifier les paramètres Hyper-V

Plusieurs paramètres d’hôte Hyper-V peuvent être appliqués à votre cluster.

1. Dans Windows Admin Center, sélectionnez **Gestionnaire de cluster** à partir de la flèche déroulante du haut.
1. Sous **Outils**, sélectionnez **Paramètres**.
1. Sélectionnez **Général**, puis utilisez les paramètres suivants :

   - **Chemin du disque dur virtuel** : spécifiez le dossier par défaut pour le stockage des fichiers de disque dur virtuel.

   - **Chemin de la machine virtuelle** : spécifiez le dossier par défaut pour le stockage des fichiers de configuration de machine virtuelle.

        :::image type="content" source="media/manage-cluster/cluster-settings-hyperv.png" alt-text="écran des paramètres Hyper-V du cluster" lightbox="media/manage-cluster/cluster-settings-hyperv.png":::

1. Pour autoriser la redirection des ressources et appareils locaux à partir de machines virtuelles, sélectionnez **Mode de session étendue**. Notez que les connexions en mode de session étendue nécessitent un système d’exploitation invité pris en charge.

    :::image type="content" source="media/manage-cluster/cluster-settings-session.png" alt-text="écran du mode de session étendue Hyper-V du cluster" lightbox="media/manage-cluster/cluster-settings-session.png":::

1. Pour permettre aux machines virtuelles de répartir des nœuds NUMA physiques, sélectionnez **Répartition NUMA**. La répartition NUMA (Non-Uniform Memory Architecture) peut fournir une machine virtuelle avec plus de mémoire que ce qui est disponible sur un seul nœud NUMA.

    :::image type="content" source="media/manage-cluster/cluster-settings-numa.png" alt-text="écran de répartition NUMA du cluster" lightbox="media/manage-cluster/cluster-settings-numa.png":::

1. Pour spécifier le nombre de machines virtuelles qui peuvent être déplacées simultanément pendant l’exécution (migration dynamique), sélectionnez **Migration dynamique**, un nombre, puis spécifiez les éléments suivants :

   - Pour **Protocole d’authentification**, sélectionnez **CredSSP** ou **Kerberos**.

   - Pour **Options de performance**, sélectionnez **Compression** ou **SMB**. Les données compressées sont envoyées via une connexion TCP/IP.

   - Cochez la case **Utiliser n’importe quel réseau** pour utiliser n’importe quel réseau disponible sur un nœud pour effectuer la migration

        :::image type="content" source="media/manage-cluster/cluster-settings-live-migration.png" alt-text="écran Migration dynamique du cluster" lightbox="media/manage-cluster/cluster-settings-live-migration.png":::

1. Pour spécifier le nombre de migrations de stockage pouvant être effectuées en même temps, sélectionnez **Migration de stockage**, puis un nombre.

    :::image type="content" source="media/manage-cluster/cluster-settings-storage-migration.png" alt-text="écran Migration de stockage du cluster" lightbox="media/manage-cluster/cluster-settings-storage-migration.png":::

## <a name="register-the-cluster-with-azure"></a>Inscrire le cluster auprès d’Azure

Pour inscrire ou annuler l’inscription de votre cluster auprès d’Azure, sélectionnez **Inscription Azure Stack HCI**. Pour plus d’informations sur la procédure à suivre, consultez [Connecter Azure Stack HCI à Azure](../deploy/register-with-azure.md).

:::image type="content" source="media/manage-cluster/cluster-registration.png" alt-text="Écran d’inscription du cluster à Azure" lightbox="media/manage-cluster/cluster-registration.png":::

## <a name="next-steps"></a>Étapes suivantes

Pour superviser votre cluster, consultez [Superviser Azure Stack HCI avec Azure Monitor](azure-monitor.md).