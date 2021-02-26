---
title: Créer des volumes dans Azure Stack HCI
description: Guide pratique pour créer des volumes dans Azure Stack HCI à l’aide de Windows Admin Center et de PowerShell.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 02/04/2021
ms.openlocfilehash: 9bb0ff34863f8262d5919e5eae6f735709097bf5
ms.sourcegitcommit: 283b1308142e668749345bf24b63d40172559509
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99570733"
---
# <a name="create-volumes-in-azure-stack-hci"></a>Créer des volumes dans Azure Stack HCI

> S’applique à : Azure Stack HCI, version 20H2

Cette rubrique explique comment créer des volumes sur un cluster Azure Stack HCI à l’aide de Windows Admin Center et de Windows PowerShell, comment utiliser des fichiers sur les volumes, et comment activer la déduplication et la compression des données sur les volumes. Pour apprendre à créer des volumes et à configurer la réplication pour des clusters étendus, consultez [Créer des volumes étendus](create-stretched-volumes.md).

## <a name="create-a-three-way-mirror-volume"></a>Créer un volume miroir triple

Pour créer un volume miroir triple à l’aide de Windows Admin Center :

1. Dans Windows Admin Center, connectez-vous à un cluster, puis sélectionnez **Volumes** dans le volet **Outils**.
2. Dans la page **Volumes**, sélectionnez l’onglet **Inventaire**, puis sélectionnez **Créer un volume**.
3. Dans le volet **Créer un volume**, entrez le nom du volume, puis laissez **Résilience** avec la valeur **Miroir triple**.
4. Dans **Size on HDD** (Taille sur le disque HDD), spécifiez la taille du volume. Par exemple, 5 To (téraoctets).
5. Sélectionnez **Create** (Créer).

Selon la taille, la création du volume peut prendre quelques minutes. Les notifications en haut à droite vous permettent de savoir quand le volume a été créé. Le nouveau volume apparaît dans la liste Inventaire.

Regardez une courte vidéo sur la création d’un volume miroir triple.

> [!VIDEO https://www.youtube-nocookie.com/embed/o66etKq70N8]

## <a name="create-a-mirror-accelerated-parity-volume"></a>Créer un volume paritaire avec accélération par miroir

La parité avec accélération par miroir (MAP) réduit l’empreinte du volume sur le disque HDD. Par exemple, pour un volume miroir triple d’une taille de 10 téraoctets, vous avez besoin d’une empreinte de 30 téraoctets. Pour réduire la surcharge de l’empreinte, créez un volume paritaire avec accélération par miroir. Cela réduit l’empreinte de 30 téraoctets à 22 téraoctets, même avec seulement 4 serveurs, par une mise en miroir des 20 % de données les plus actifs. L’utilisation de la parité, qui est plus efficace pour la gestion de l’espace, sert à stocker le reste. Vous pouvez ajuster ce ratio de parité et de miroir pour obtenir un compromis niveau de performance/capacité qui convient à votre charge de travail. Par exemple, une parité de 90 % et un miroir de 10 % offrent un niveau de performance moins important. Toutefois, ils permettent de rationaliser davantage l’empreinte.

  >[!NOTE]
  >Les volumes de parité avec accélération par miroir nécessitent le système de fichiers résilient (ReFS).

Pour créer un volume paritaire avec accélération par miroir dans Windows Admin Center :

1. Dans Windows Admin Center, connectez-vous à un cluster, puis sélectionnez **Volumes** dans le volet **Outils**.
2. Dans la page Volumes, sélectionnez l’onglet **Inventaire**, puis sélectionnez **Créer un volume**.
3. Dans le volet **Créer un volume**, entrez le nom du volume.
4. Dans **Résilience**, sélectionnez **Mirror-accelerated parity** (Parité avec accélération par miroir).
5. Dans **Parity percentage** (Pourcentage de parité), sélectionnez le pourcentage de parité.
6. Sélectionnez **Create** (Créer).

Regardez une courte vidéo sur la création d’un volume paritaire avec accélération par miroir.

> [!VIDEO https://www.youtube-nocookie.com/embed/R72QHudqWpE]

## <a name="open-volume-and-add-files"></a>Ouvrir un volume et ajouter des fichiers

Pour ouvrir un volume et y ajouter des fichiers dans Windows Admin Center :

1. Dans Windows Admin Center, connectez-vous à un cluster, puis sélectionnez **Volumes** dans le volet **Outils**.
2. Dans la page **Volumes**, sélectionnez l’onglet **Inventaire**.
2. Dans la liste des volumes, sélectionnez le nom du volume à ouvrir.

    Dans la page de détails du volume, vous pouvez voir le chemin du volume.

4. En haut de la page, sélectionnez **Ouvrir**. Cela permet de lancer l’outil **​Fichiers** dans Windows Admin Center.
5. Accédez au chemin du volume. Ici, vous pouvez parcourir les fichiers du volume.
6. Sélectionnez **Charger**, puis sélectionnez un fichier à charger.
7. Utilisez le bouton **Précédent** du navigateur pour revenir au volet **Outils** dans Windows Admin Center.

Regardez une courte vidéo sur l’ouverture d’un volume et l’ajout de fichiers.

> [!VIDEO https://www.youtube-nocookie.com/embed/j59z7ulohs4]

## <a name="turn-on-deduplication-and-compression"></a>Activer la déduplication et la compression

La déduplication et la compression sont gérées par volume. La déduplication et la compression utilisent un modèle post-traitement, ce qui signifie que vous ne voyez aucun gain avant leur exécution. Une fois l’exécution effectuée, tous les fichiers sont affectés, même ceux déjà présents.

Pour en savoir plus, consultez [Activer le chiffrement, la déduplication et la compression des volumes](volume-encryption-deduplication.md).

## <a name="create-volumes-using-windows-powershell"></a>Créer des volumes à l’aide de Windows PowerShell

Commencez par lancer Windows PowerShell à partir du menu Démarrer de Windows. Nous vous recommandons d’utiliser l’applet de commande **New-Volume** afin de créer des volumes pour Azure Stack HCI. Elle offre l’expérience utilisateur la plus rapide et la plus simple. Cette seule applet de commande permet de créer automatiquement le disque virtuel, de le partitionner et de le formater. Elle permet également de créer le volume avec le nom correspondant, puis de l’ajouter aux volumes partagés de cluster, le tout en une seule étape très simple.

L’applet de commande **New-Volume** a quatre paramètres que vous devez toujours fournir :

- **FriendlyName :** Chaîne de votre choix, par exemple *« Volume1 »*
- **Système de fichiers :** **CSVFS_ReFS** (recommandé pour tous les volumes ; obligatoire pour les volumes de parité avec accélération par miroir) ou **CSVFS_NTFS**
- **StoragePoolFriendlyName :** Nom de votre pool de stockage, par exemple *« S2D sur NomCluster »*
- **Taille :** Taille du volume, par exemple *« 10TB »*

   > [!NOTE]
   > Windows, notamment PowerShell, utilise des nombres binaires (base 2) alors que les lecteurs sont souvent étiquetés à l’aide de nombres décimaux (base 10). Cela explique pourquoi un lecteur ayant « un téraoctet », soit 1 000 000 000 000 d’octets, apparaît dans Windows comme ayant environ « 909 Go ». Ceci est normal. Quand vous créez des volumes à l’aide de **New-Volume**, vous devez spécifier le paramètre **Size** en nombres binaires (base 2). Par exemple, si vous spécifiez « 909GB » ou « 0.909495TB », un volume d’environ 1 000 000 000 000 d’octets est créé.

### <a name="example-with-2-or-3-servers"></a>Exemple : Avec 2 ou 3 serveurs

Pour faciliter les choses, si votre déploiement comporte uniquement deux serveurs, la fonctionnalité Espaces de stockage direct utilise automatiquement la mise en miroir double pour la résilience. Si votre déploiement comporte uniquement trois serveurs, il utilise automatiquement la mise en miroir triple.

```PowerShell
New-Volume -FriendlyName "Volume1" -FileSystem CSVFS_ReFS -StoragePoolFriendlyName S2D* -Size 1TB
```

### <a name="example-with-4-servers"></a>Exemple : Avec 4 serveurs ou plus

Si vous disposez de quatre serveurs ou plus, vous pouvez utiliser le paramètre facultatif **ResiliencySettingName** pour choisir votre type de résilience.

-   **ResiliencySettingName :** Soit **Mirror**, soit **Parity**.

Dans l’exemple suivant, *« Volume2 »* utilise la mise en miroir triple et *« Volume3 »* utilise la double parité (souvent appelée « codage d’effacement »).

```PowerShell
New-Volume -FriendlyName "Volume2" -FileSystem CSVFS_ReFS -StoragePoolFriendlyName S2D* -Size 1TB -ResiliencySettingName Mirror
New-Volume -FriendlyName "Volume3" -FileSystem CSVFS_ReFS -StoragePoolFriendlyName S2D* -Size 1TB -ResiliencySettingName Parity
```

## <a name="using-storage-tiers"></a>Utilisation des niveaux de stockage

Dans les déploiements avec trois types de lecteur, un volume peut s’étendre sur les niveaux SSD et HDD pour résider partiellement sur chacun d’eux. De même, dans les déploiements avec quatre serveurs ou plus, un volume peut mélanger la mise en miroir et la double parité pour résider partiellement sur chacun d’eux.

Pour vous aider à créer de tels volumes, Azure Stack HCI fournit des modèles de niveau par défaut appelés **MirrorOn*MediaType*** et **NestedMirrorOn*MediaType*** (pour les performances), et **ParityOn*MediaType*** et **NestedParityOn*MediaType*** (pour la capacité), où *MediaType* est HDD ou SSD. Les modèles représentent des niveaux de stockage basés sur des types de médias et encapsulent les définitions de la mise en miroir triple sur les lecteurs plus rapides (le cas échéant), et de la double parité sur les lecteurs plus lents (le cas échéant).

Vous pouvez les voir en exécutant l’applet de commande **Get-StorageTier** sur n’importe quel serveur du cluster.

```PowerShell
Get-StorageTier | Select FriendlyName, ResiliencySettingName, PhysicalDiskRedundancy
```

Par exemple, si vous avez un cluster à deux nœuds avec uniquement des HDD, la sortie peut ressembler à ceci :

```PowerShell
FriendlyName      ResiliencySettingName PhysicalDiskRedundancy
------------      --------------------- ----------------------
NestedParityOnHDD Parity                                     1
Capacity          Mirror                                     1
NestedMirrorOnHDD Mirror                                     3
MirrorOnHDD       Mirror                                     1
```

Pour créer des volumes hiérarchisés, référencez ces modèles de niveaux à l’aide des paramètres **StorageTierFriendlyNames** et **StorageTierSizes** de l’applet de commande **New-Volume**. Par exemple, l’applet de commande suivante permet de créer un volume qui combine la mise en miroir triple et la double parité dans des proportions 30/70.

```PowerShell
New-Volume -FriendlyName "Volume1" -FileSystem CSVFS_ReFS -StoragePoolFriendlyName S2D* -StorageTierFriendlyNames MirrorOnHDD, Capacity -StorageTierSizes 300GB, 700GB
```

Répétez l’opération selon les besoins pour créer plusieurs volumes.

### <a name="nested-resiliency-volumes"></a>Volumes à résilience imbriquée

La résilience imbriquée s’applique uniquement aux clusters à deux serveurs ; vous ne pouvez pas utiliser la résilience imbriquée si votre cluster contient trois serveurs ou plus. La résilience imbriquée permet à un cluster à deux serveurs de résister à plusieurs défaillances matérielles en même temps sans perte de disponibilité du stockage, ce qui permet aux utilisateurs, applications et machines virtuelles de continuer à fonctionner sans interruption. Pour en savoir plus, consultez [Planifier des volumes : choix du type de résilience](../concepts/plan-volumes.md#choosing-the-resiliency-type).

#### <a name="create-nested-storage-tiers"></a>Créer des niveaux de stockage imbriqués

Pour créer un niveau NestedMirror :

```PowerShell
New-StorageTier -StoragePoolFriendlyName S2D* -FriendlyName NestedMirror -ResiliencySettingName Mirror -NumberOfDataCopies 4 -MediaType HDD -CimSession 2nodecluster
```

Pour créer un niveau NestedParity :

```PowerShell
New-StorageTier -StoragePoolFriendlyName S2D* -FriendlyName NestedParity -ResiliencySettingName Parity -NumberOfDataCopies 2 -PhysicalDiskRedundancy 1 -NumberOfGroups 1 -FaultDomainAwareness StorageScaleUnit -ColumnIsolation PhysicalDisk -MediaType HDD -CimSession 2nodecluster
```

#### <a name="create-nested-volumes"></a>Créer des volumes imbriqués

Pour créer un volume NestedMirror :

```PowerShell
New-Volume -StoragePoolFriendlyName S2D* -FriendlyName MyMirrorNestedVolume -StorageTierFriendlyNames NestedMirror -StorageTierSizes 500GB -CimSession 2nodecluster
```

Pour créer un volume NestedParity :

```PowerShell
New-Volume -StoragePoolFriendlyName S2D* -FriendlyName MyParityNestedVolume -StorageTierFriendlyNames NestedMirror,NestedParity -StorageTierSizes 200GB, 1TB -CimSession 2nodecluster
```

### <a name="storage-tier-summary-table"></a>Tableau de synthèse des niveaux de stockage

Les tableaux suivants récapitulent les niveaux de stockage qui peuvent être créés dans Azure Stack HCI.

**NumberOfNodes : 2**

| FriendlyName      | MediaType | ResiliencySettingName | NumberOfDataCopies | PhysicalDiskRedundancy | NumberOfGroups | FaultDomainAwareness | ColumnIsolation | Remarque         |
| ----------------- | :-------: | :-------------------: | :----------------: | :--------------------: |:--------------:| :------------------: | :-------------: | :----------: |
| MirrorOnHDD       | HDD       | Miroir                | 2                  | 1                      | 1              | StorageScaleUnit     | PhysicalDisk    | créé automatiquement |
| MirrorOnSSD       | SSD       | Miroir                | 2                  | 1                      | 1              | StorageScaleUnit     | PhysicalDisk    | créé automatiquement |
| MirrorOnSCM       | SCM       | Miroir                | 2                  | 1                      | 1              | StorageScaleUnit     | PhysicalDisk    | créé automatiquement |
| NestedMirrorOnHDD | HDD       | Miroir                | 4                  | 3                      | 1              | StorageScaleUnit     | PhysicalDisk    | manual       |
| NestedMirrorOnSSD | SSD       | Miroir                | 4                  | 3                      | 1              | StorageScaleUnit     | PhysicalDisk    | manual       |
| NestedMirrorOnSCM | SCM       | Miroir                | 4                  | 3                      | 1              | StorageScaleUnit     | PhysicalDisk    | manual       |
| NestedParityOnHDD | HDD       | Parité                | 2                  | 1                      | 1              | StorageScaleUnit     | PhysicalDisk    | manual       |
| NestedParityOnSSD | SSD       | Parité                | 2                  | 1                      | 1              | StorageScaleUnit     | PhysicalDisk    | manual       |
| NestedParityOnSCM | SCM       | Parité                | 2                  | 1                      | 1              | StorageScaleUnit     | PhysicalDisk    | manual       |

**NumberOfNodes : 3**

| FriendlyName      | MediaType | ResiliencySettingName | NumberOfDataCopies | PhysicalDiskRedundancy | NumberOfGroups | FaultDomainAwareness | ColumnIsolation | Remarque         |
| ----------------- | :-------: | :-------------------: | :----------------: | :--------------------: |:--------------:| :------------------: | :-------------: | :----------: |
| MirrorOnHDD       | HDD       | Miroir                | 3                  | 2                      | 1              | StorageScaleUnit     | PhysicalDisk    | créé automatiquement |
| MirrorOnSSD       | SSD       | Miroir                | 3                  | 2                      | 1              | StorageScaleUnit     | PhysicalDisk    | créé automatiquement |
| MirrorOnSCM       | SCM       | Miroir                | 3                  | 2                      | 1              | StorageScaleUnit     | PhysicalDisk    | créé automatiquement |

**NumberOfNodes : 4+**

| FriendlyName      | MediaType | ResiliencySettingName | NumberOfDataCopies | PhysicalDiskRedundancy | NumberOfGroups | FaultDomainAwareness | ColumnIsolation | Remarque         |
| ----------------- | :-------: | :-------------------: | :----------------: | :--------------------: |:--------------:| :------------------: | :-------------: | :----------: |
| MirrorOnHDD       | HDD       | Miroir                | 3                  | 2                      | 1              | StorageScaleUnit     | PhysicalDisk    | créé automatiquement |
| MirrorOnSSD       | SSD       | Miroir                | 3                  | 2                      | 1              | StorageScaleUnit     | PhysicalDisk    | créé automatiquement |
| MirrorOnSCM       | SCM       | Miroir                | 3                  | 2                      | 1              | StorageScaleUnit     | PhysicalDisk    | créé automatiquement |
| ParityOnHDD       | HDD       | Parité                | 1                  | 2                      | Auto           | StorageScaleUnit     | StorageScaleUnit| créé automatiquement |
| ParityOnSSD       | SSD       | Parité                | 1                  | 2                      | Auto           | StorageScaleUnit     | StorageScaleUnit| créé automatiquement |
| ParityOnSCM       | SCM       | Parité                | 1                  | 2                      | Auto           | StorageScaleUnit     | StorageScaleUnit| créé automatiquement |

## <a name="next-steps"></a>Étapes suivantes

Pour consulter des rubriques connexes et d’autres tâches de gestion du stockage, référez-vous également à :

- [Vue d’ensemble des espaces de stockage direct](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Planifier des volumes](../concepts/plan-volumes.md)
- [Étendre les volumes](extend-volumes.md)
- [Supprimer des volumes](delete-volumes.md)