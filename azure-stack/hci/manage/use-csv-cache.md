---
title: Utiliser le cache de lecture en mémoire du volume partagé de cluster (CSV) avec Azure Stack HCI
description: Cette rubrique explique comment utiliser la mémoire système pour booster les performances.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/04/2020
ms.openlocfilehash: 84d5292c3f812402027b310954a021752276a799
ms.sourcegitcommit: 01dcda15d88c8d44b4918e2f599daca462a8e3d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/05/2020
ms.locfileid: "89493799"
---
# <a name="use-the-csv-in-memory-read-cache-with-azure-stack-hci"></a>Utiliser le cache de lecture en mémoire du volume partagé de cluster (CSV) avec Azure Stack HCI

> S’applique à : Azure Stack HCI, version 20H2 ; Windows Server 2019

Cette rubrique explique comment utiliser la mémoire système pour booster les performances d’Azure Stack HCI.

Azure Stack HCI est compatible avec le cache de lecture en mémoire du volume partagé de cluster (CSV). L’utilisation de la mémoire système pour mettre en cache les lectures peut améliorer les performances des applications comme Hyper-V, qui utilisent des E/S non mises en mémoire tampon pour accéder aux fichiers VHD ou VHDX. (Les E/S non mises en mémoire tampon sont des opérations qui ne sont pas mises en cache par le gestionnaire de cache Windows.)

Étant donné que le cache en mémoire est sur un serveur local, il améliore le caractère local des données : les lectures récentes sont mises en cache en mémoire sur le même hôte que celui où est exécutée la machine virtuelle, ce qui réduit la fréquence des lectures sur le réseau. Cela entraîne une latence plus faible et de meilleures performances de stockage.

Notez que le cache de lecture en mémoire du volume partagé de cluster est différent du [cache de pool de stockage](../concepts/cache.md) dans Azure Stack HCI.

## <a name="planning-considerations"></a>Considérations en matière de planification

Le cache de lecture en mémoire est plus efficace pour les charges de travail nécessitant beaucoup de lectures, telles que VDI (Virtual Desktop Infrastructure). À l’inverse, si la charge de travail est très gourmande en écriture, le cache peut introduire une surcharge et doit être désactivé.

Vous pouvez utiliser jusqu’à 80 % de la mémoire physique totale pour le cache de lecture en mémoire du volume partagé de cluster. Veillez à conserver suffisamment de mémoire pour vos machines virtuelles.

  > [!NOTE]
  > Certains outils de microtest tels que DISKSPD et [VM Fleet](https://github.com/Microsoft/diskspd/tree/master/Frameworks/VMFleet) peuvent produire de plus mauvais résultats lorsque le cache de lecture en mémoire du volume partagé de cluster est activé. Par défaut, VM Fleet crée 1 VHDX de 10 Gio par machine virtuelle : environ 1 Tio en tout pour 100 machines virtuelles, puis effectue des lectures et écritures *uniformément aléatoires*. Contrairement aux charges de travail réelles, les lectures ne suivent pas un modèle prévisible ou répétitif, de sorte que le cache en mémoire n’est pas efficace et crée uniquement une surcharge.

## <a name="configuring-the-in-memory-read-cache"></a>Configuration du cache de lecture en mémoire

Le cache de lecture en mémoire du volume partagé de cluster (CSV) est disponible dans Azure Stack HCI, Windows Server 2019 et Windows Server 2016 avec les mêmes fonctionnalités. Dans Azure Stack HCI et Windows Server 2019, il est activé par défaut avec 1 Gio alloué. Dans Windows Server 2016, il est désactivé par défaut.

| Version du SE          | Taille du cache de volume partagé de cluster (CSV) par défaut |
|---------------------|------------------------|
| Azure Stack HCI     | 1 Gio                  |
| Windows Server 2019 | 1 Gio                  |
| Windows Server 2016 | 0 (désactivé)           |

Pour voir la quantité de mémoire allouée à l’aide de PowerShell, exécutez :

```PowerShell
(Get-Cluster).BlockCacheSize
```

La valeur retournée est exprimée en Mio par serveur. Par exemple, `1024` représente 1 Gio.

Pour modifier la quantité de mémoire allouée, modifiez cette valeur à l’aide de PowerShell. Par exemple, pour allouer 2 Gio par serveur, exécutez :

```PowerShell
(Get-Cluster).BlockCacheSize = 2048
```

Pour que les modifications prennent effet immédiatement, mettez en pause puis relancez vos volumes partagés de cluster ou déplacez-les entre des serveurs. Par exemple, utilisez ce fragment PowerShell pour déplacer chaque volume partagé de cluster vers un autre nœud de serveur puis dans le sens inverse :

```PowerShell
Get-ClusterSharedVolume | ForEach {
    $Owner = $_.OwnerNode
    $_ | Move-ClusterSharedVolume
    $_ | Move-ClusterSharedVolume -Node $Owner
}
```

## <a name="next-steps"></a>Étapes suivantes

Pour consulter des informations connexes, reportez-vous également à :

- [Comprendre le cache de pool de stockage](../concepts/cache.md)
- [Utiliser des volumes partagés de cluster dans un cluster de basculement](/windows-server/failover-clustering/failover-cluster-csvs#enable-the-csv-cache-for-read-intensive-workloads-optional)
