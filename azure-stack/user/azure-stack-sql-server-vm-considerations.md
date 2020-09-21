---
title: Optimiser les performances de SQL Server dans Azure Stack Hub
description: Cet article décrit les meilleures pratiques SQL Server pour améliorer les performances et optimiser SQL Server dans les machines virtuelles Azure Stack Hub.
author: bryanla
ms.topic: article
ms.date: 04/02/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 01/14/2020
ms.openlocfilehash: f222334bcc6535b82b105494d907c8a69a463073
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90573766"
---
# <a name="sql-server-best-practices-to-optimize-performance-in-azure-stack-hub"></a>Meilleures pratiques SQL Server pour optimiser les performances dans Azure Stack Hub

Cet article décrit les meilleurs pratiques pour optimiser SQL Server et améliorer les performances sur les machines virtuelles Microsoft Azure Stack Hub. Quand vous exécutez SQL Server sur des machines virtuelles Azure Stack Hub, utilisez les options de réglage des performances de base de données qui s’appliquent à SQL Server dans un environnement de serveur local. Les performances d’une base de données relationnelle dans un cloud Azure Stack Hub dépendent de nombreux facteurs, notamment la taille de la famille d’une machine virtuelle et la configuration des disques de données.

Lors de la création d’images SQL Server, [pensez à approvisionner vos machines virtuelles dans le portail Azure Stack Hub](/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). Téléchargez l’extension IaaS SQL à partir de la fonctionnalité Gestion de la Place de marché dans le portail administrateur Azure Stack Hub, et téléchargez les images de machine virtuelle SQL Server de votre choix. Il s’agit notamment de SQL Server 2016 SP1, SQL Server 2016 SP2 et SQL Server 2017.

> [!NOTE]  
> Si l’article explique comment approvisionner une machine virtuelle SQL Server à l’aide du portail Azure global, les instructions s’appliquent également à Azure Stack Hub, aux différences suivantes près : SSD n’est pas disponible pour le disque de système d’exploitation et la configuration du stockage présente des différences mineures.

Dans les images de machine virtuelle, dans SQL Server, vous ne pouvez utiliser que l’approche BYOL (apportez votre propre licence). Pour Windows Server, le modèle de licence par défaut est celui assorti du paiement à l’utilisation (PAYG). Pour des informations détaillées sur le modèle de licence Windows Server dans une machine virtuelle, voir [FAQ concernant Windows Server sur la Place de marché Azure Stack Hub](../operator/azure-stack-windows-server-faq.md#what-about-other-vms-that-use-windows-server-such-as-sql-or-machine-learning-server).  

Cet article se concentre sur l’obtention des *meilleures* performances pour SQL Server sur des machines virtuelles Azure Stack Hub. Si votre charge de travail est moindre, vous n’aurez peut-être pas besoin de toutes les optimisations recommandées. Tenez compte de vos besoins de performances et de vos modèles de charges de travail lors de l’évaluation de ces recommandations.

> [!NOTE]  
> Pour obtenir de l’aide sur les performances de SQL Server dans des machines virtuelles Azure, consultez [cet article](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).

## <a name="checklist-for-sql-server-best-practices"></a>Check-list des meilleures pratiques SQL Server

La liste de contrôle suivante a trait aux performances optimales de SQL Server sur des machines virtuelles Azure Stack Hub :


|Domaine|Optimisations|
|-----|-----|
|Taille de la machine virtuelle |[DS3](azure-stack-vm-sizes.md) ou supérieure pour l’édition SQL Server Entreprise<br><br>[DS2](azure-stack-vm-sizes.md) ou supérieure pour l’édition SQL Server Standard et l’édition Web|
|Stockage |Utiliser une famille de machines virtuelles qui prend en charge le [stockage Premium](azure-stack-acs-differences.md).|
|Disques |Utiliser au minimum deux disques de données (un pour les fichiers journaux et l’autre pour le fichier de données et TempDB) et choisir la taille des disques en fonction de vos besoins en capacité. Définir les emplacements par défaut des fichiers de données sur ces disques pendant l’installation de SQL Server.<br><br>Éviter d’utiliser des disques de système d’exploitation ou temporaires pour le stockage ou la journalisation des bases de données.<br>Entrelacer plusieurs disques de données Azure pour obtenir un débit d’E/S plus élevé à l’aide d’espaces de stockage.<br><br>Formatez avec des tailles d’allocation documentées.|
|E/S|Activer l’initialisation de fichiers instantanée pour les fichiers de données.<br><br>Limiter la croissance automatique des bases de données avec des incréments fixes relativement petits (64 à 256 Mo).<br><br>Désactiver la réduction automatique sur la base de données.<br><br>Configurer les emplacements par défaut des fichiers de sauvegarde et de base de données sur des disques de données, pas sur le disque de système d’exploitation.<br><br>Activer les pages verrouillées.<br><br>Appliquer les mises à jour cumulatives et les Service Packs SQL Server.|
|Spécifique aux fonctionnalités|Sauvegarder directement sur un stockage d’objets blob (si pris en charge par la version de SQL Server en cours d’utilisation).|
|||

Pour plus d’informations sur *comment* et *pourquoi* effectuer ces optimisations, passez en revue les détails et les instructions fournies dans les sections suivantes.

## <a name="vm-size-guidance"></a>Conseils liés à la taille des machines virtuelles

Pour les applications sensibles aux performances, les [tailles de machines virtuelles](azure-stack-vm-sizes.md) suivantes sont recommandées :

- **SQL Server Enterprise Edition :** DS3 ou supérieure

- **Édition SQL Server Standard et édition Web :** DS2 ou supérieure

Avec Azure Stack Hub, il n’existe aucune différence de performances entre les séries de machines virtuelles DS et DS_v2.

## <a name="storage-guidance"></a>Conseils liés au stockage

Les machines virtuelles de la série DS (ainsi que de la série DSv2) dans Azure Stack Hub fournissent les débits (IOPS) maximaux de disque de système d’exploitation et de disque de données. Une machine virtuelle de la série DS ou DSv2 fournit jusqu’à 1 000 IOPS pour le disque de système d’exploitation et jusqu’à 2 300 IOPS par disque de données, quel que soit le type ou la taille du disque choisi.

Le débit des disques de données est déterminé de façon unique en fonction de la série de famille de machines virtuelles. Vous pouvez [consulter cet article](azure-stack-vm-sizes.md) pour identifier le débit des disques de données par série de famille de machines virtuelles.

> [!NOTE]  
> Pour les charges de travail de production, sélectionnez une machine virtuelle de série DSv2 ou DS pour fournir le maximum d’IOPS possible sur le disque de système d’exploitation et les disques de données.

Quand vous créez un compte de stockage dans Azure Stack Hub, l’option de géoréplication est sans effet, car cette fonctionnalité n’est pas disponible dans Azure Stack Hub.

## <a name="disks-guidance"></a>Conseils liés aux disques

Il existe trois types de disques principaux sur une machine virtuelle Azure Stack Hub :

- **Disque de système d’exploitation :** Quand vous créez une machine virtuelle Azure Stack Hub, la plateforme attache au moins un disque (désigné par la lettre **C**) à la machine virtuelle en tant que disque de système d’exploitation. Ce disque est un disque dur virtuel (VHD) stocké en tant qu’objet blob de pages dans le stockage.

- **Disque temporaire :** Les machines virtuelles Azure Stack Hub contiennent un autre disque, appelé disque temporaire (lecteur **D**). C’est un disque du nœud qui peut être utilisé comme un espace de travail temporaire.

- **Disques de données :** vous pouvez attacher des disques supplémentaires à votre machine virtuelle en tant que disques de données ; ces disques sont également stockés en tant qu’objets blob de pages.

Les sections suivantes décrivent des recommandations pour l’utilisation de ces disques différents.

### <a name="operating-system-disk"></a>Disque de système d’exploitation

Un disque de système d’exploitation est un disque dur virtuel (VHD) que vous pouvez amorcer et monter comme version d’exécution d’un système d’exploitation. Il est désigné par la lettre de lecteur **C**.

### <a name="temporary-disk"></a>Disque temporaire

Le disque de stockage temporaire, désigné par la lettre **D**, n’est pas persistant. Ne stockez pas de données précieuses sur le lecteur **D**. Cela inclut les fichiers de base de données utilisateur et les fichiers journaux de transactions utilisateur.

Nous vous recommandons de stocker TempDB sur un disque de données, car chaque disque de données fournit au maximum 2 300 IOPS.

### <a name="data-disks"></a>Disques de données

- **Utilisation de disques de données pour les fichiers journaux et de données.** Si vous n’utilisez pas l’entrelacement de disques, utilisez deux disques de données à partir d’une machine virtuelle qui prend en charge le stockage Premium, l’un pour contenir les fichiers journaux et l’autre pour contenir les fichiers TempDB et de données. Chaque disque de données fournit un nombre d’IOPS dépendant de la famille de la machine virtuelle, comme décrit dans [Tailles de machine virtuelle prises en charge dans Azure Stack Hub](azure-stack-vm-sizes.md). Si vous utilisez une technique d’entrelacement de disques, comme des espaces de stockage, placez tous les fichiers journaux et de données sur le même disque (y compris TempDB). Cette configuration vous donne le nombre maximal d’IOPS utilisables par SQL Server, quel que soit le fichier qui en a besoin à un moment donné.

> [!NOTE]  
> Lorsque vous configurez une machine virtuelle SQL Server dans le portail, vous avez la possibilité de modifier votre configuration de stockage. Selon votre configuration, Azure Stack Hub configure un ou plusieurs disques. Plusieurs disques sont combinés en un pool de stockage unique. Les fichiers journaux et de données se trouvent dans cette configuration.

- **Entrelacement de disques :** Pour augmenter le débit, vous pouvez ajouter des disques de données et utiliser l’entrelacement de disques. Pour déterminer le nombre de disques de données dont vous avez besoin, analysez le nombre d’IOPS nécessaires pour vos fichiers journaux, ainsi que pour vos fichiers de données et TempDB. Notez que les limites d’IOPS sont exprimées par disque de données, en fonction de la famille de série de la machine virtuelle et non de la taille de la machine virtuelle. Toutefois, les limites de bande passante réseau sont basées sur la taille de la machine virtuelle. Pour plus de détails, voir les tableaux des [tailles de machine virtuelle dans Azure Stack Hub](azure-stack-vm-sizes.md). Respectez les recommandations suivantes :

  - Pour Windows Server 2012 ou version ultérieure, utilisez des [espaces de stockage](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831739(v=ws.11)) en respectant les consignes suivantes :

    1. Définissez l’intervalle (taille de bande) sur 64 Ko (65 536 octets) pour les charges de travail OLTP (traitement transactionnel en ligne) et sur 256 Ko (262 144 octets) pour les charges de travail d’entrepôt de données, afin d’éviter qu’un alignement incorrect de la partition n’impacte les performances. Ces paramètres doivent être définis avec PowerShell.

    2. Nombre de colonnes définies = nombre de disques physiques. Utilisez PowerShell quand vous configurez plus de huit disques (et non l’interface utilisateur du gestionnaire de serveur).

       Par exemple, la commande PowerShell suivante crée un pool de stockage avec un entrelacement de 64 Ko et 2 colonnes :

       ```powershell  
       $PoolCount = Get-PhysicalDisk -CanPool $True
       $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

       New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple -UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false
       ```

- Déterminez le nombre de disques associés à votre pool de stockage en fonction de vos attentes en matière de charge. N’oubliez pas que les différentes tailles de machines virtuelles autorisent différents nombres de disques de données attachés. Pour plus d’informations, voir les [Tailles de machine virtuelle prises en charge dans Azure Stack Hub](azure-stack-vm-sizes.md).
- Pour obtenir le nombre maximal d’IOPS possible pour les disques de données, il est recommandé d’ajouter le nombre maximal de disques de données pris en charge par votre [taille de machine virtuelle](azure-stack-vm-sizes.md) et d’utiliser l’entrelacement de disques.
- **Taille d’unité d’allocation NTFS :** Lors du formatage du disque de données, nous vous recommandons d’utiliser une taille d’unité d’allocation de 64 Ko pour les fichiers de données et les fichiers journaux ainsi que pour TempDB.
- **Pratiques de gestion des disques :** lors de la suppression d’un disque de données, arrêtez le service SQL Server. En outre, ne changez pas les paramètres de cache sur les disques, car cette opération n’améliore pas les performances.

> [!WARNING]  
> Si le service SQL n’est pas arrêté au cours de ces opérations, cela peut provoquer une altération de la base de données.

## <a name="io-guidance"></a>Recommandations liées aux E/S

- Envisagez d’activer l’initialisation instantanée des fichiers pour réduire le temps requis pour l’allocation initiale des fichiers. Pour tirer parti de l’initialisation instantanée des fichiers, vous devez accorder l’autorisation **SE_MANAGE_VOLUME_NAME** au compte de service SQL Server (MSSQLSERVER) et l’ajouter à la stratégie de sécurité **Effectuer des tâches de maintenance sur les volumes**. Si vous utilisez une image de plateforme SQL Server pour Azure, le compte de service par défaut (**NT Service\MSSQLSERVER**) n’est pas ajouté à la stratégie de sécurité **Effectuer des tâches de maintenance sur les volumes**. En d’autres termes, l’initialisation instantanée des fichiers n’est pas activée dans une image de plateforme SQL Server pour Azure. Après avoir ajouté le compte de service SQL Server à la stratégie de sécurité **Effectuer les tâches de maintenance de volume** , redémarrez le service SQL Server. Des considérations de sécurité liées à l’utilisation de cette fonctionnalité peuvent exister. Pour plus d’informations, consultez [Initialisation des fichiers de base de données](/sql/relational-databases/databases/database-instant-file-initialization?view=sql-server-ver15).
- **autogrow** est un plan d’urgence en cas de croissance plus rapide que prévu. Ne gérez pas la croissance de vos données et journaux quotidiennement avec la croissance automatique. En cas d’utilisation de la croissance automatique, augmentez préalablement le fichier à l’aide du commutateur **Taille**.
- Vérifiez que la fonctionnalité de réduction automatique ( **autoshrink** ) est désactivée afin d’éviter une surcharge inutile susceptible d’affecter négativement les performances.
- Configurez les emplacements par défaut du fichier de sauvegarde et du fichier de base de données. Utilisez les suggestions de cet article, puis apportez les modifications souhaitées dans la fenêtre Propriétés du serveur. Pour obtenir des instructions, consultez [Afficher ou modifier les emplacements par défaut des fichiers de données et des fichiers journaux (SQL Server Management Studio)](/sql/database-engine/configure-windows/view-or-change-the-default-locations-for-data-and-log-files?view=sql-server-ver15). La capture d’écran suivante montre comment effectuer ces modifications :

    > ![Afficher ou changer les emplacements par défaut](./media/sql-server-vm-considerations/image1.png)

- Activez les pages verrouillées pour réduire les activités d’E/S et de pagination. Pour plus d’informations, consultez [Activer l’option Lock Pages in Memory (Windows)](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows?view=sql-server-ver15).

- Envisagez de compresser tous les fichiers de données lors des transferts à destination et à partir d’Azure Stack Hub, y compris les sauvegardes.

## <a name="feature-specific-guidance"></a>Conseils spécifiques aux fonctionnalités

Certains déploiements peuvent bénéficier de plus grands avantages en termes de performances à l’aide de techniques de configuration avancées. La liste suivante présente certaines fonctionnalités SQL Server qui peuvent vous aider à améliorer les performances :

- **Sauvegardez sur** **Stockage Azure.** Lors de sauvegardes de SQL Server s’exécutant sur des machines virtuelles Azure Stack Hub, vous pouvez utiliser l’option Sauvegarde SQL Server vers une URL. Cette fonctionnalité est disponible à partir de SQL Server 2012 SP1 CU2, et recommandée pour la sauvegarde vers les disques de données attachés.

    Durant la sauvegarde ou la restauration à l’aide du stockage Azure, suivez les suggestions indiquées dans [Meilleures pratiques et dépannage de sauvegarde SQL Server vers une URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting?view=sql-server-ver15) et [Restauration à partir de sauvegardes stockées dans Microsoft Azure](/sql/relational-databases/backup-restore/restoring-from-backups-stored-in-microsoft-azure?view=sql-server-2017). Vous pouvez également automatiser ces sauvegardes en utilisant la [Sauvegarde automatisée pour SQL Server dans les machines virtuelles Azure](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup).

-   **Sauvegardez vers le stockage Azure Stack Hub.** Vous pouvez effectuer une sauvegarde vers le stockage Azure Stack Hub de la même manière qu’une sauvegarde vers Stockage Azure. Quand vous créez une sauvegarde dans SQL Server Management Studio (SSMS), vous devez entrer manuellement les informations de configuration. Vous ne pouvez pas utiliser SSMS pour créer le conteneur de stockage ou la signature d’accès partagé. SSMS se connecte uniquement à des abonnements Azure, pas à des abonnements Azure Stack Hub. Au lieu de cela, vous devez créer le compte de stockage, le conteneur et la signature d’accès partagé dans le portail Azure Stack Hub ou avec PowerShell.


    ![Sauvegarde SQL Server](./media/sql-server-vm-considerations/image3.png)

    > [!NOTE]  
    > La signature d’accès partagé est le jeton SAP provenant du portail Azure Stack Hub, sans le caractère initial « ? » dans la chaîne. Si vous utilisez la fonction de copie à partir du portail, vous devez supprimer le caractère initial « ? » pour que le jeton fonctionne dans SQL Server.

    Une fois la destination de sauvegarde configurée dans SQL Server, vous pouvez effectuer une sauvegarde vers le stockage d’objets blob Azure Stack Hub.

## <a name="next-steps"></a>Étapes suivantes

[Utilisation de services ou génération d’applications pour Azure Stack Hub](azure-stack-considerations.md)
