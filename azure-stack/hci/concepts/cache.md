---
title: Compréhension du cache dans Azure Stack HCI
description: Comment la mise en cache de lecture et d’écriture fonctionne dans les espaces de stockage direct et Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: 214e4fa6fde30d69d063326cc0c7548bb75b694a
ms.sourcegitcommit: 0e52f460295255b799bac92b40122a22bf994e27
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/21/2020
ms.locfileid: "86867009"
---
# <a name="understanding-the-cache-in-azure-stack-hci"></a>Compréhension du cache dans Azure Stack HCI

> S’applique à : Azure Stack HCI, version 20H2 ; Windows Server 2019

Les [espaces de stockage direct](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) se caractérisent par un cache coté serveur intégré destiné à maximiser les performances de stockage. Il s’agit d’un cache de lecture *et* d’écriture volumineux, persistant et en temps réel. Le cache est configuré automatiquement quand la fonctionnalité Espaces de stockage direct est activée. Dans la plupart des cas, aucune gestion manuelle n’est nécessaire.
Le fonctionnement du cache varie en fonction des types de lecteur utilisés.

La vidéo suivante explique en détail le fonctionnement de la mise en cache pour les espaces de stockage direct et évoque d’autres considérations de conception.

<strong>Considérations de conception avec les espaces de stockage direct</strong><br>(20 minutes)<br>
<iframe src="https://channel9.msdn.com/Blogs/windowsserver/Design-Considerations-for-Storage-Spaces-Direct/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="drive-types-and-deployment-options"></a>Types de lecteur et options de déploiement

Pour l’heure, les espaces de stockage direct fonctionnent avec quatre types de lecteurs :

|||
|----------------------|--------------------------|
|![PMem](media/choose-drives/pmem-100px.png)|**PMem** fait référence à la mémoire persistante, un nouveau type de stockage à faible latence et haute performance.|
|![NVMe](media/choose-drives/NVMe-100-px.png)|**NVMe** (Non-Volatile Memory Express) fait référence aux disques SSD qui se trouvent directement sur le bus PCIe. Les facteurs de forme courants sont les suivants : U.2 2,5 pouces, PCIe Add-In-Card (AIC) et M.2. NVMe offre un plus haut débit d’E/S par seconde (IOPS) et d’E/S avec une latence plus faible que tout autre type de lecteur actuellement pris en charge, sauf PMem.|
|![SSD](media/choose-drives/SSD-100-px.png)|**SSD** fait référence aux disques SSD qui se connectent par le biais de connecteurs SATA ou SAS conventionnels.|
|![HDD](media/choose-drives/HDD-100-px.png)|**HDD** fait référence aux lecteurs de disque dur magnétiques rotationnels qui offrent une capacité de stockage importante.|

Ceux-ci peuvent être combinés de différentes façons, que nous regroupons dans deux catégories : « 100 % flash » et « hybride ».

### <a name="all-flash-deployment-possibilities"></a>Possibilités de déploiement 100 % flash

L’objectif des déploiements 100 % flash est d’optimiser les performances de stockage. Ils n’incluent pas de lecteurs de disques durs rotatifs (HDD).

![Possibilités-de-déploiement-100 %-flash](media/cache/All-Flash-Deployment-Possibilities.png)

### <a name="hybrid-deployment-possibilities"></a>Possibilités de déploiement hybride

Les déploiements hybrides visent à équilibrer les performances et la capacité ou à optimiser la capacité. Ils incluent des lecteurs de disques durs rotatifs (HDD).

![Possibilités-de-déploiement-hybride](media/cache/Hybrid-Deployment-Possibilities.png)

## <a name="cache-drives-are-selected-automatically"></a>Les lecteurs de cache sont sélectionnés automatiquement

Dans les déploiements où cohabitent plusieurs types de lecteur, les espaces de stockage direct utilisent automatiquement tous les lecteurs dits « les plus rapides » pour la mise en cache. Les lecteurs restants sont utilisés pour la capacité.

La rapidité des lecteurs est déterminée selon la hiérarchie suivante.

![Hiérarchie-Types-Lecteur](media/cache/Drive-Type-Hierarchy.png)

Par exemple, si vous avez des lecteurs NVMe et des disques SSD, les NVMe assureront la mise en cache pour les disques SSD.

Si vous avez des disques SSD et des lecteurs de disque dur (HDD), les SSD assureront la mise en cache pour les HDD.

   >[!NOTE]
   > Les lecteurs de cache ne contribuent pas à la capacité de stockage utilisable. Toutes les données stockées dans le cache sont aussi stockées ailleurs ou le seront une fois qu’elles auront été supprimées du stockage temporaire (« de-staging »). Cela signifie que la capacité totale de stockage brute de votre déploiement correspond à la somme de vos lecteurs de capacité uniquement.

Quand tous les lecteurs sont du même type, aucun cache n’est configuré automatiquement. Vous avez la possibilité de configurer des lecteurs plus endurants pour la mise en cache de lecteurs moins endurants du même type. Pour en savoir plus, consultez la section [Configuration manuelle](#manual-configuration).

   >[!TIP]
   > Dans les déploiements constitués exclusivement de dispositifs NVMe ou de disques SSD, particulièrement à une très petite échelle, le fait qu’aucun lecteur ne soit « sacrifié » pour le cache peut améliorer l’efficacité du stockage de façon significative.

## <a name="cache-behavior-is-set-automatically"></a>Le comportement du cache est défini automatiquement

Le comportement du cache est déterminé automatiquement selon le ou les types de lecteurs pour lesquels s’effectue la mise en cache. Lors de la mise en cache pour les disques SSD (par exemple, NVMe mettant en cache pour des disques SSD), seules les écritures sont mises en cache. Quand la mise en cache concerne des lecteurs de disque dur (par exemple, disques SSD assurant la mise en cache pour des HDD), aussi bien les lectures que les écritures sont mises en cache.

![Comportement-lecture-écriture-cache](media/cache/Cache-Read-Write-Behavior.png)

### <a name="write-only-caching-for-all-flash-deployments"></a>Mise en cache d’écriture seule pour les déploiements 100 % flash

Dans le cas de la mise en cache pour les disques SSD (NVMe ou SSD), seules les écritures sont mises en cache. Avec cette méthode, les lecteurs de capacité s'usent moins rapidement. En effet, vous pouvez regrouper de nombreuses écritures et réécritures dans le cache, et ne les déstocker que lorsque c'est nécessaire. Le trafic cumulatif sur les lecteurs de capacité est ainsi réduit, ce qui permet de prolonger leur durée de vie. C’est pour cette raison que nous vous recommandons d’opter pour des lecteurs de [plus grande endurance, optimisés en écriture](http://whatis.techtarget.com/definition/DWPD-device-drive-writes-per-day) pour le cache. Les lecteurs de capacité peuvent avoir une endurance en écriture relativement faible.

Les lectures ne sont pas mises en cache, étant donné qu'elles n'affectent pas la durée de vie des lecteurs Flash de manière significative, et que les disques SSD offrent généralement une faible latence dans ce domaine. Aussi, elles arrivent directement des lecteurs de capacité (sauf quand les données ont été écrites trop récemment et qu'elles n'ont pas encore été déstockées du cache). Le cache est ainsi entièrement dédié aux écritures, ce qui maximise son efficacité.

Par conséquent, les caractéristiques d'écriture, comme la latence, sont dictées par les lecteurs de cache, alors que les caractéristiques de lecture sont dictées par les lecteurs de capacité. Dans les deux cas, les caractéristiques sont cohérentes, prévisibles et uniformes.

### <a name="readwrite-caching-for-hybrid-deployments"></a>Mise en cache de lecture/écriture pour les déploiements hybrides

Quand la mise en cache concerne les lecteurs de disque dur (HDD), à la fois les lectures *et* les écritures sont mises en cache pour offrir une latence comparable à celle de la technologie flash (souvent ~10 fois supérieure) pour les deux. Le cache de lecture stocke les données lues récemment et fréquemment pour permettre un accès rapide et limiter autant que possible le trafic aléatoire à destination des HDD. (En raison des délais de recherche et de rotation, la latence et le temps perdu causés par l'accès aléatoire à un disque dur sont considérables.) Les écritures sont mises en cache pour absorber les pics de trafic, et comme précédemment, vous pouvez regrouper les écritures et réécritures afin de réduire au maximum le trafic cumulatif sur les lecteurs de capacité.

Les espaces de stockage direct font appel à un algorithme qui annule l'aspect aléatoire des écritures avant de les déstocker du cache. Cela permet d'émuler un schéma d'E/S d'apparence séquentielle au niveau du lecteur, même quand les E/S réelles provenant de la charge de travail (par exemple, des machines virtuelles) sont aléatoires. Cela optimise les IOPS et le débit à destination des HDD.

### <a name="caching-in-deployments-with-drives-of-all-three-types"></a>Mise en cache dans les déploiements avec les trois types de lecteur

Quand les trois types de lecteur sont présents, les lecteurs NVMe assurent la mise en cache pour les disques SSD et les HDD. Le comportement est conforme à celui décrit précédemment : seules les écritures sont mises en cache pour les disques SSD, et aussi bien les lectures que les écritures sont mises en cache pour les HDD. La charge de la mise en cache pour les HDD est répartie de façon équitable entre les lecteurs de cache.

## <a name="summary"></a>Résumé

Ce tableau récapitule les lecteurs utilisés pour la mise en cache et la capacité, et rappelle les comportements de cache associés à chaque déploiement.

| Déploiement     | Lecteurs de cache                        | Lecteurs de capacité | Comportement de cache (par défaut)  |
| -------------- | ----------------------------------- | --------------- | ------------------------- |
| NVMe uniquement         | Aucun (facultatif : configuration manuelle) | NVMe            | Écriture seule (si configuré)  |
| SSD uniquement          | Aucun (facultatif : configuration manuelle) | SSD             | Écriture seule (si configuré)  |
| NVMe + SSD       | NVMe                                | SSD             | Écriture seule                  |
| NVMe + HDD       | NVMe                                | HDD             | Lecture + écriture                |
| SSD + HDD        | SSD                                 | HDD             | Lecture + écriture                |
| NVMe + SSD + HDD | NVMe                                | SSD + HDD       | Lecture + écriture pour les HDD, écriture seule pour les SSD  |

## <a name="server-side-architecture"></a>Architecture côté serveur

Le cache est mis en œuvre côté lecteur : les différents lecteurs formant le cache au niveau d'un serveur sont reliés à un ou plusieurs lecteurs de capacité au sein de ce même serveur.

Étant donné que le cache est situé sous le reste de la pile de stockage à définition logicielle Windows, les concepts d'espaces de stockage et de tolérance de panne ne sont pas implémentés, ni nécessaires. Vous pouvez voir cela comme des lecteurs « hybrides » (en partie flash, en partie disque) qui sont créés, puis présentés à Windows. Comme avec un véritable lecteur hybride, le mouvement en temps réel des données à chaud et à froid entre les portions plus rapides et plus lentes du support physique est presque invisible de l'extérieur.

Étant donné que la résilience dans les espaces de stockage direct relève au moins du niveau serveur (à savoir que les copies des données sont toujours écrites sur différents serveurs, avec au moins une copie par serveur), les données en cache bénéficient de la même résilience que les données qui ne sont pas en cache.

![Architecture-coté-serveur-du-cache](media/cache/Cache-Server-Side-Architecture.png)

Par exemple, dans le cas d’une mise en miroir triple, les données sont écrites en trois exemplaires sur différents serveurs, où elles sont placées dans le cache. Qu’elles soient par la suite supprimées du stockage temporaire ou non, il existe toujours trois copies.

## <a name="drive-bindings-are-dynamic"></a>Les liaisons de lecteurs sont dynamiques

La liaison entre un lecteur de cache et un lecteur de capacité peut avoir un ratio allant de 1:1 à 1:12 et au-delà. Il s’ajuste dynamiquement chaque fois que des lecteurs sont ajoutés ou supprimés, notamment en cas de scale-up ou à la suite de défaillances. Cela signifie que vous pouvez ajouter des lecteurs de cache ou des lecteurs de capacité de façon indépendante, quand vous le souhaitez.

![Liaison-dynamique](media/cache/Dynamic-Binding.gif)

Pour des raisons de symétrie, nous vous recommandons de faire en sorte que le nombre de lecteurs de capacité soit un multiple du nombre de lecteurs de cache. Par exemple, si vous avez quatre lecteurs de cache, vous obtiendrez des performances plus équilibrées avec huit lecteurs de capacité plutôt qu'avec sept ou neuf.

## <a name="handling-cache-drive-failures"></a>Gestion des défaillances de lecteur de cache

En cas de panne d'un lecteur de cache, les écritures qui n'ont pas encore été déstockées de ce dernier sont perdues *pour le serveur local*, autrement dit il ne reste que les autres copies (sur les autres serveurs). Comme après n’importe quelle autre défaillance de lecteur, les espaces de stockage peuvent récupérer automatiquement en consultant les copies restantes.

Pendant une courte période, les lecteurs de capacité qui étaient liés au lecteur de cache perdu apparaissent comme étant non sains. Une fois la liaison du cache rétablie et les données réparées (le tout automatiquement), ils retrouvent leur état sain.

Ce scénario souligne l’importance de disposer d’au moins deux lecteurs de cache par serveur pour préserver le niveau de performance.

![Gestion-défaillances](media/cache/Handling-Failure.gif)

Vous pouvez alors remplacer le lecteur de cache comme n’importe quel autre lecteur.

   >[!NOTE]
   > Vous pouvez être amené à mettre le système hors tension pour remplacer un lecteur NVMe ayant un facteur de forme AIC (Add-In Card) ou M.2.

## <a name="relationship-to-other-caches"></a>Relation avec les autres caches

La pile de stockage à définition logicielle Windows compte plusieurs autres caches non liés. Tel est le cas du cache en écriture différée des espaces de stockage ou du cache de lecture en mémoire d’un volume partagé de cluster.

Dans le cas des espaces de stockage direct, le comportement par défaut du cache en écriture différée des espaces de stockage ne doit pas être modifié. Par exemple, certains paramètres de l’applet de commande **New-volume**, comme **-WriteCacheSize**, ne doivent pas être utilisés.

En revanche, vous êtes libre de choisir d’utiliser ou non le cache d’un volume partagé de cluster. Il est désactivé par défaut dans les espaces de stockage direct, mais il n'entre pas en conflit avec le nouveau cache décrit dans cette rubrique. Dans certains scénarios, il peut offrir des gains de performances précieux. Pour plus d’informations, consultez [Guide pratique pour activer le cache de volume partagé de cluster](/windows-server/failover-clustering/failover-cluster-csvs#enable-the-csv-cache-for-read-intensive-workloads-optional).

## <a name="manual-configuration"></a>Configuration manuelle

Dans la plupart des déploiements, aucune configuration manuelle n’est pas nécessaire. Cependant, si vous en avez besoin, consultez les sections suivantes.

Si vous devez apporter des modifications au modèle de dispositif de cache après l’installation, modifiez le document des composants de prise en charge du Service de contrôle d’intégrité, comme indiqué dans [Vue d’ensemble du Service de contrôle d’intégrité](/windows-server/failover-clustering/health-service-overview#supported-components-document).

### <a name="specify-cache-drive-model"></a>Spécifier le modèle de lecteur de cache

Dans les déploiements où tous les lecteurs sont de même type (par exemple, uniquement NVMe ou uniquement SSD), aucun cache n'est configuré, car Windows ne distingue pas automatiquement les caractéristiques telles que l'endurance en écriture sur des lecteurs de même type.

Pour utiliser les lecteurs les plus endurants pour la mise en cache et les lecteurs les moins endurants pour la capacité, vous pouvez spécifier le modèle de lecteur à utiliser avec le paramètre **-CacheDeviceModel** de l'applet de commande **Enable-ClusterS2D**. Dès lors que la fonctionnalité Espaces de stockage direct est activée, tous les lecteurs de ce modèle sont utilisés pour la mise en cache.

   >[!TIP]
   > Veillez à ce que la chaîne de modèle soit exactement la même que celle figurant dans la sortie de **Get-PhysicalDisk**.

####  <a name="example"></a>Exemple

Pour commencer, obtenez la liste des disques physiques :

```PowerShell
Get-PhysicalDisk | Group Model -NoElement
```

Voici un exemple de sortie :

```
Count Name
----- ----
    8 FABRIKAM NVME-1710
   16 CONTOSO NVME-1520
```

Entrez ensuite la commande suivante en spécifiant le modèle de dispositif de cache :

```PowerShell
Enable-ClusterS2D -CacheDeviceModel "FABRIKAM NVME-1710"
```

Vous pouvez vérifier que les lecteurs que vous avez choisis sont utilisés pour le cache en exécutant **Get-PhysicalDisk** dans PowerShell et en vérifiant que la propriété **Usage** indique bien **"Journal"** .

### <a name="manual-deployment-possibilities"></a>Possibilités de déploiement manuel

La configuration manuelle offre les possibilités de déploiement suivantes :

![Possibilités-de-déploiement-inhabituelles](media/cache/Exotic-Deployment-Possibilities.png)

### <a name="set-cache-behavior"></a>Définir le comportement du cache

Il est possible de remplacer le comportement par défaut du cache. Par exemple, vous pouvez le configurer de façon à mettre en cache les lectures même dans un déploiement 100 % flash. Nous vous déconseillons de modifier le comportement par défaut, à moins que vous soyez certain qu’il n’est pas adapté à votre charge de travail.

Pour remplacer le comportement, utilisez l’applet de commande **Set-ClusterStorageSpacesDirect** et ses paramètres **-CacheModeSSD** et **-CacheModeHDD**. Le paramètre **CacheModeSSD** définit le comportement du cache quand la mise en cache concerne des disques SSD. Le paramètre **CacheModeSSD** définit le comportement du cache quand la mise en cache concerne des lecteurs de disque dur (HDD). Cette opération peut être effectuée à tout moment dès lors que la fonctionnalité Espaces de stockage direct est activée.

Vous pouvez utiliser **Get-ClusterStorageSpacesDirect** pour vérifier que le comportement est défini.

#### <a name="example"></a>Exemple

Tout d’abord, obtenez les paramètres de la fonctionnalité Espaces de stockage direct :

```PowerShell
Get-ClusterStorageSpacesDirect
```

Voici un exemple de sortie :

```
CacheModeHDD : ReadWrite
CacheModeSSD : WriteOnly
```

Ensuite, procédez comme suit :

```PowerShell
Set-ClusterStorageSpacesDirect -CacheModeSSD ReadWrite

Get-ClusterS2D
```

Voici un exemple de sortie :

```
CacheModeHDD : ReadWrite
CacheModeSSD : ReadWrite
```

## <a name="sizing-the-cache"></a>Dimensionnement du cache

La taille du cache doit être définie en fonction des besoins de votre charge de travail ou de vos applications (les données lues ou écrites activement à un instant T).

Cela est particulièrement important dans les déploiements hybrides constitués de lecteurs de disque dur. Si les plages de travail actives sont plus volumineuses que votre cache ou si elles dérivent trop vite, le nombre de lectures manquées risque d'augmenter et il faudra déstocker les écritures plus agressivement, ce qui nuira aux performances globales.

Vous pouvez utiliser l’utilitaire intégré Analyseur de performances (PerfMon. exe) de Windows pour inspecter le taux d’absences dans le cache. Plus particulièrement, vous pouvez comparer la valeur **Cache Miss Reads/sec** au niveau du décompte **Cluster Storage Hybrid Disk** et le nombre total d'E/S par seconde en lecture de votre déploiement. Chaque « disque hybride » correspond à un lecteur de capacité.

Par exemple, 2 lecteurs de cache liés à 4 lecteurs de capacité donnent 4 instances d’objet « disque hybride » par serveur.

![Analyseur-performances](media/cache/PerfMon.png)

Il n'y a pas de règle universelle, mais si vous constatez qu'il manque trop de lectures au niveau du cache, ce dernier est probablement trop petit et vous devez envisager d'ajouter des lecteurs de cache. Vous pouvez ajouter des lecteurs de cache ou des lecteurs de capacité de façon indépendante, quand vous le souhaitez.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le stockage additionnel, consultez également :

- [Tolérance de panne et efficacité du stockage](fault-tolerance.md)
- [Quorum de pool et de cluster](quorum.md)
