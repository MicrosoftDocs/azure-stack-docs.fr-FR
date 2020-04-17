---
title: Étendre des volumes dans Azure Stack HCI
description: Comment redimensionner des volumes dans Azure Stack HCI à l’aide de Windows Admin Center et de PowerShell.
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: 703931b0dccb533b2b924847eb3302f0efa46d1a
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "79089299"
---
# <a name="extending-volumes-in-storage-spaces-direct"></a>Extension des volumes dans les espaces de stockage direct
> S’applique à : Windows Server 2019

Cette rubrique fournit des instructions pour le redimensionnement des volumes sur un cluster des [espaces de stockage direct](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) à l’aide de Windows Admin Center.

> [!WARNING]
> **Non pris en charge : redimensionnement du stockage sous-jacent utilisé par les espaces de stockage direct.** Si vous exécutez les espaces de stockage direct dans un environnement de stockage virtualisé, notamment dans Azure, le redimensionnement ou la modification des caractéristiques des périphériques de stockage utilisés par les machines virtuelles n’est pas pris en charge et entraîne l’inaccessibilité des données. Suivez plutôt les instructions de la section [Ajouter des serveurs ou des lecteurs](/windows-server/storage/storage-spaces/add-nodes) pour ajouter une capacité supplémentaire avant d’étendre les volumes.

Visionnez une courte vidéo sur la façon de redimensionner un volume.

> [!VIDEO https://www.youtube-nocookie.com/embed/hqyBzipBoTI]

## <a name="extending-volumes-using-windows-admin-center"></a>Extension des volumes à l’aide de Windows Admin Center

1. Dans Windows Admin Center, connectez-vous à un cluster des espaces de stockage direct, puis sélectionnez **Volumes** dans le volet **Outils**.
2. Dans la page **Volumes**, sélectionnez l’onglet **Inventaire**, puis le volume à redimensionner.

    Dans la page sur les détails des volumes, la capacité de stockage du volume est indiquée. Vous pouvez également ouvrir la page sur les détails des volumes directement à partir du tableau de bord. Dans le tableau de bord, dans le volet Alertes, sélectionnez l’alerte, qui vous avertit si un volume ne dispose pas de suffisamment de capacité de stockage, puis **Accéder au volume**.

4. En haut de la page sur les détails des volumes, sélectionnez **Redimensionner**.
5. Entrez une nouvelle taille supérieure, puis sélectionnez **Redimensionner**.

    Dans la page sur les détails des volumes, la plus grande capacité de stockage du volume est indiquée et l’alerte sur le tableau de bord est effacée.

## <a name="extending-volumes-using-powershell"></a>Extension des volumes à l’aide de PowerShell

### <a name="capacity-in-the-storage-pool"></a>Capacité dans le pool de stockage

Avant de redimensionner un volume, vérifiez que vous disposez d’une capacité suffisante dans le pool de stockage pour prendre en charge sa nouvelle empreinte plus importante. Par exemple, lors du redimensionnement d’un volume miroir triple de 1 To à 2 To, son empreinte passe de 3 To à 6 To. Pour que le redimensionnement aboutisse, vous devez disposer d’au moins (6 - 3) = 3 To de capacité disponible dans le pool de stockage.

### <a name="familiarity-with-volumes-in-storage-spaces"></a>Connaissance des volumes dans les espaces de stockage

Dans les espaces de stockage direct, chaque volume est constitué de plusieurs objets empilés : le volume partagé de cluster (CSV), qui est un volume ; la partition ; le disque, qui est un disque virtuel ; un ou plusieurs niveaux de stockage (le cas échéant). Pour redimensionner un volume, vous devez redimensionner plusieurs de ces objets.

![volumes-in-smapi](media/extend-volumes/volumes-in-smapi.png)

Pour vous familiariser avec eux, essayez d’exécuter **Get-** avec le nom correspondant dans PowerShell.

Par exemple :

```PowerShell
Get-VirtualDisk
```

Pour suivre les associations entre les objets de la pile, dirigez une applet de commande **Get-** dans le code suivant.

Par exemple, voici comment se rendre d’un disque virtuel jusqu’à son volume :

```PowerShell
Get-VirtualDisk <FriendlyName> | Get-Disk | Get-Partition | Get-Volume 
```

### <a name="step-1--resize-the-virtual-disk"></a>Étape 1 : Redimensionner le disque virtuel

Le disque virtuel peut utiliser ou non des niveaux de stockage, selon la façon dont il a été créé.

Pour vérifier, exécutez l’applet de commande suivante :

```PowerShell
Get-VirtualDisk <FriendlyName> | Get-StorageTier 
```

Si l’applet de commande ne retourne rien, le disque virtuel n’utilise pas de niveaux de stockage.

#### <a name="no-storage-tiers"></a>Aucun niveau de stockage

Si le disque virtuel n’a pas de niveau de stockage, vous pouvez le redimensionner directement à l’aide de l’applet de commande **Resize-VirtualDisk**.

Indiquez la nouvelle taille dans le paramètre **-Size**.

```PowerShell
Get-VirtualDisk <FriendlyName> | Resize-VirtualDisk -Size <Size>
```

Quand vous redimensionnez le **disque virtuel**, le **disque** suit automatiquement et est également redimensionné.

![Resize-VirtualDisk](media/extend-volumes/Resize-VirtualDisk.gif)

#### <a name="with-storage-tiers"></a>Avec des niveaux de stockage

Si le disque virtuel utilise des niveaux de stockage, vous pouvez redimensionner chaque niveau séparément à l’aide de l’applet de commande **Resize-StorageTier**.

Obtenez les noms des niveaux de stockage en suivant les associations à partir du disque virtuel.

```PowerShell
Get-VirtualDisk <FriendlyName> | Get-StorageTier | Select FriendlyName
```

Ensuite, pour chaque niveau, indiquez la nouvelle taille dans le paramètre **-Size**.

```PowerShell
Get-StorageTier <FriendlyName> | Resize-StorageTier -Size <Size>
```

> [!TIP]
> Si vos niveaux sont des types de supports physiques différents (par exemple, **MediaType = SSD** et **MediaType = HDD**), vous devez vérifier que vous disposez d’une capacité suffisante pour chaque type de support dans le pool de stockage afin de prendre en charge la nouvelle empreinte plus importante de chaque niveau.

Quand vous redimensionnez les **niveaux de stockage**, le **disque virtuel** et le **disque** suivent automatiquement et sont également redimensionnés.

![Resize-StorageTier](media/extend-volumes/Resize-StorageTier.gif)

### <a name="step-2--resize-the-partition"></a>Étape 2 : Redimensionner la partition

Redimensionnez ensuite la partition à l’aide de l’applet de commande **Resize-Partition**. Le disque virtuel est censé avoir deux partitions : la première est réservée et ne doit pas être modifiée. Celle que vous devez redimensionner affiche **PartitionNumber = 2** et **Type = De base**.

Indiquez la nouvelle taille dans le paramètre **-Size**. Nous vous recommandons d’utiliser la taille prise en charge maximale, comme indiqué ci-dessous.

```PowerShell
# Choose virtual disk
$VirtualDisk = Get-VirtualDisk <FriendlyName>

# Get its partition
$Partition = $VirtualDisk | Get-Disk | Get-Partition | Where PartitionNumber -Eq 2

# Resize to its maximum supported size 
$Partition | Resize-Partition -Size ($Partition | Get-PartitionSupportedSize).SizeMax
```

Quand vous redimensionnez la **partition**, le **volume** et le **volume partagé de cluster** suivent automatiquement et sont également redimensionnés.

![Resize-Partition](media/extend-volumes/Resize-Partition.gif)

Et voilà !

> [!TIP]
> Vous pouvez vérifier que le volume a la nouvelle taille en exécutant **Get-Volume**.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir des instructions pas à pas sur d’autres tâches essentielles de la gestion du stockage, consultez également :

- [Planification des volumes dans les espaces de stockage direct](/windows-server/storage/storage-spaces/plan-volumes)
- [Création de volumes dans les espaces de stockage direct](/windows-server/storage/storage-spaces/create-volumes)
- [Suppression des volumes dans les espaces de stockage direct](/windows-server/storage/storage-spaces/delete-volumes)