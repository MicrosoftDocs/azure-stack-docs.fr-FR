---
title: Comprendre le quorum de cluster et de pool – Azure Stack HCI
description: Découvrez le quorum de cluster et de pool dans les espaces de stockage direct sur Azure Stack HCI, avec des exemples précis permettant de saisir toutes les subtilités.
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: 0503e9a97a2ca2b15447dbd837eeac9162b84654
ms.sourcegitcommit: 48a46142ea7bccd6c8a609e188dd7f3f6444f3c4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98561993"
---
# <a name="understanding-cluster-and-pool-quorum-on-azure-stack-hci"></a>Présentation du quorum de cluster et de pool dans Azure Stack HCI

> S’applique à : Azure Stack HCI, version 20H2 ; Windows Server 2019

Le [clustering de basculement Windows Server](/windows-server/failover-clustering/failover-clustering-overview) offre une haute disponibilité pour les charges de travail. Ces ressources sont considérées comme hautement disponibles si les nœuds qui les hébergent sont actifs. Toutefois, le cluster nécessite généralement plus de la moitié des nœuds en cours d’exécution. C’est ce que l’on appelle disposer d’un *quorum*.

Le quorum est conçu pour empêcher les scénarios *split-brain* qui peuvent se produire lorsqu’il existe une partition au sein du réseau et que les sous-ensembles de nœuds ne peuvent pas communiquer entre eux. Cela peut amener les deux sous-ensembles de nœuds à tenter de s’approprier la charge de travail et à écrire sur le même disque, ce qui peut entraîner de nombreux problèmes. Toutefois, un tel scénario peut être évité grâce au concept de quorum du clustering de basculement, qui force l’exécution d’un seul de ces groupes de nœuds. De cette façon, un seul de ces groupes reste en ligne.

Le quorum détermine le nombre d’échecs que le cluster peut supporter tout en restant en ligne. Le quorum est conçu pour gérer les problèmes de communication entre les sous-ensembles de nœuds du cluster. Il empêche plusieurs serveurs d’héberger simultanément un groupe de ressources et d’écrire sur un même disque en même temps. Grâce à ce concept de quorum, le cluster force le service de cluster à s’arrêter dans l’un des sous-ensembles de nœuds de sorte qu’il n’y ait qu’un seul véritable propriétaire pour chaque groupe de ressources. Une fois que les nœuds qui ont été arrêtés peuvent de nouveau communiquer avec le groupe de nœuds principal, ils rejoignent automatiquement le cluster et démarrent leur service de cluster.

Dans Windows Server 2019, deux composants du système disposent de leurs propres mécanismes de quorum :

- **Quorum de cluster** : celui-ci fonctionne au niveau du cluster (par exemple, vous pouvez perdre des nœuds sans pour autant que le cluster devienne indisponible).
- **Quorum de pool** : celui-ci fonctionne au niveau du pool lorsque les espaces de stockage direct sont activés (par exemple, vous pouvez perdre des nœuds et des lecteurs sans pour autant que le cluster devienne indisponible). Les pools de stockage ont été conçus pour être utilisés dans des scénarios cluster et non cluster, ce qui explique pourquoi ils disposent d’un mécanisme de quorum différent.

## <a name="cluster-quorum-overview"></a>Présentation du quorum de cluster

Le tableau ci-dessous donne une vue d’ensemble des résultats du quorum de cluster pour chaque scénario :

| Nœuds de serveur | Peut survivre à l’échec d’un nœud de serveur | Peut survivre à l’échec d’un nœud de serveur, puis d’un autre | Peut survivre à deux échecs de nœuds de serveur simultanés |
|--------------|-------------------------------------|---------------------------------------------------|----------------------------------------------------|
| 2            | 50/50                               | Non                                                | Non                                                 |
| 2 + témoin  | Oui                                 | Non                                                | Non                                                 |
| 3            | Oui                                 | 50/50                                             | Non                                                 |
| 3 + témoin  | Oui                                 | Oui                                               | Non                                                 |
| 4            | Oui                                 | Oui                                               | 50/50                                              |
| 4 + témoin  | Oui                                 | Oui                                               | Oui                                                |
| 5 et plus  | Oui                                 | Oui                                               | Oui                                                |

### <a name="cluster-quorum-recommendations"></a>Recommandations relatives au quorum de cluster

- Si vous avez deux nœuds, il est **obligatoire** de disposer d’un témoin.
- Si vous disposez de trois ou quatre nœuds, le témoin est **fortement recommandé**.
- Si vous avez accès à Internet, utilisez un **[témoin de cloud](/windows-server/failover-clustering/deploy-cloud-witness)** .
- Si vous êtes dans un environnement informatique qui comprend d’autres machines et partages de fichiers, utilisez un témoin de partage de fichiers.

## <a name="how-cluster-quorum-works"></a>Fonctionnement du quorum de cluster

Lorsque les nœuds échouent, ou lorsque certains sous-ensembles de nœuds perdent leur contact avec un autre sous-ensemble, les nœuds survivants doivent vérifier qu’ils constituent la *majorité* du cluster pour rester en ligne. S’ils ne peuvent pas le vérifier, ils seront mis hors connexion.

Toutefois, le concept de *majorité* ne fonctionne correctement que si le nombre total de nœuds du cluster est impair (par exemple, trois nœuds dans un cluster à cinq nœuds). Alors, qu’en est-il des clusters qui ont un nombre pair de nœuds (par exemple, un cluster à quatre nœuds) ?

Il existe deux façons pour le cluster de rendre le *nombre total de votes* impair :

1. Tout d’abord, il peut *augmenter ce nombre* en ajoutant un *témoin* avec un vote supplémentaire. Cela nécessite une configuration de la part de l’utilisateur.
2. Il peut également *diminuer ce nombre* en annulant le vote d’un nœud (cela se produit automatiquement lorsque c’est nécessaire).

Chaque fois que les nœuds survivants vérifient qu’ils sont *majoritaires*, la définition de *majorité* est mise à jour de manière à se trouver parmi les survivants. Cela permet au cluster de perdre un nœud, puis un autre, puis un autre, et ainsi de suite. Ce concept de *nombre total de votes* qui s’adapte après des échecs successifs est appelé ***quorum dynamique** _.

### <a name="dynamic-witness"></a>Témoin dynamique

Le témoin dynamique modifie le vote du témoin pour faire en sorte que le _nombre total de votes* soit impair. Si le nombre de votes est impair, le témoin ne dispose pas d’un vote. Si le nombre de votes est pair, le témoin dispose d’un vote. Le témoin dynamique réduit considérablement le risque que le cluster ne soit interrompu en raison d’un échec de témoin. Le cluster décide s’il faut utiliser le vote du témoin en fonction du nombre de nœuds votants disponibles dans le cluster.

Le quorum dynamique fonctionne avec un témoin dynamique comme décrit ci-dessous.

### <a name="dynamic-quorum-behavior"></a>Comportement du quorum dynamique

- Si vous avez un nombre de nœuds **pair** et aucun témoin, *un des nœuds voit son vote annulé*. Par exemple, seuls trois des quatre nœuds disposent d’un vote. Par conséquent, le *nombre total de votes* est de trois, et les deux survivants disposant d’un vote sont considérés comme formant une majorité.
- Si vous avez un nombre de nœuds **impair** et aucun témoin, *tous les nœuds disposent d’un vote*.
- Si vous disposez d’un nombre de nœuds **pair** et d’un témoin, *le témoin dispose d’un vote* et le nombre total devient impair.
- Si vous disposez d’un nombre de nœuds **impair** et d’un témoin, *le témoin ne disposera pas d’un vote*.

Le quorum dynamique permet d’attribuer dynamiquement un vote à un nœud afin d’éviter de perdre la majorité des votes et de permettre au cluster de s’exécuter avec un seul nœud (on appelle ce nœud « last-man standing » ou « dernier homme debout »). Prenons comme exemple un cluster à quatre nœuds. Supposons que le quorum nécessite 3 votes.

Dans ce cas, le cluster s’arrêterait si vous perdiez deux nœuds.

![Diagramme montrant quatre nœuds de cluster, chacun disposant d’un vote](media/quorum/dynamic-quorum-base.png)

Le quorum dynamique empêche ce problème de se produire. Le *nombre total de votes* nécessaires pour le quorum est désormais déterminé en fonction du nombre de nœuds disponibles. Ainsi, avec le quorum dynamique, le cluster restera opérationnel même si vous perdez trois nœuds.

![Diagramme montrant quatre nœuds de cluster, avec des nœuds qui échouent un par un, et le nombre de votes nécessaires s’ajustant après chaque échec](media/quorum/dynamic-quorum-step-through.png)

Le scénario ci-dessus s’applique à un cluster général où les espaces de stockage direct ne sont pas activés. Toutefois, lorsque les espaces de stockage direct sont activés, le cluster peut uniquement prendre en charge deux échecs de nœuds. Pour plus d’informations, consultez la section concernant le [quorum de pool](#pool-quorum-overview).

### <a name="examples"></a>Exemples

#### <a name="two-nodes-without-a-witness"></a>Deux nœuds sans témoin.
Le vote d’un nœud est annulé. La *majorité* est donc déterminée sur un total de **1 vote**. Si le nœud qui ne vote pas devient indisponible de façon inattendue, le survivant a un ratio de 1/1 et le cluster survit. Si le nœud qui vote devient indisponible de façon inattendue, le survivant a un ratio de 0/1 et le cluster devient lui aussi indisponible. Si le nœud qui vote est mis hors connexion normalement, le vote est transféré vers l’autre nœud, ce qui permet au cluster de survivre. **_C’est pour cette raison qu’il est essentiel de configurer un témoin._* _

![Explication du quorum dans le cas de deux nœuds sans témoin](media/quorum/2-node-no-witness.png)

- Peut survivre à une défaillance de serveur : _*Probabilité de 50 %**.
- Peut survivre à l’échec d’un serveur, puis d’un autre : **Non**.
- Peut survivre à deux échecs de serveur simultanés : **Non**.

#### <a name="two-nodes-with-a-witness"></a>Deux nœuds avec un témoin.
Les deux nœuds votent, ainsi que le témoin. La *majorité* est déterminée par rapport à un total de **3 votes**. Si l’un des nœuds devient indisponible, le survivant a un ratio de 2/3 et le cluster survit.

![Explication du quorum dans le cas de deux nœuds avec un témoin](media/quorum/2-node-witness.png)

- Peut survivre à un échec de serveur : **Oui**.
- Peut survivre à l’échec d’un serveur, puis d’un autre : **Non**.
- Peut survivre à deux échecs de serveur simultanés : **Non**.

#### <a name="three-nodes-without-a-witness"></a>Trois nœuds sans témoin.
Tous les nœuds votent. La *majorité* est donc déterminée par rapport à un total de **3 votes**. Si l’un des nœuds devient indisponible, les survivants ont un ratio de 2/3 et le cluster survit. Le cluster comprend alors deux nœuds et pas de témoin : cela correspond au scénario 1.

![Explication du quorum dans le cas de trois nœuds sans témoin](media/quorum/3-node-no-witness.png)

- Peut survivre à un échec de serveur : **Oui**.
- Peut survivre à l’échec d’un serveur, puis d’un autre : **50 % de chance**.
- Peut survivre à deux échecs de serveur simultanés : **Non**.

#### <a name="three-nodes-with-a-witness"></a>Trois nœuds avec un témoin.
Tous les nœuds votent, donc le témoin ne dispose pas encore d’un vote. La *majorité* est donc déterminée par rapport à un total de **3 votes**. Après un échec, le cluster comprend deux nœuds et un témoin : nous sommes revenus au scénario 2. À présent, les deux nœuds et le témoin votent.

![Explication du quorum dans le cas de trois nœuds avec un témoin](media/quorum/3-node-witness.png)

- Peut survivre à un échec de serveur : **Oui**.
- Peut survivre à l’échec d’un serveur, puis d’un autre : **Oui**.
- Peut survivre à deux échecs de serveur simultanés : **Non**.

#### <a name="four-nodes-without-a-witness"></a>Quatre nœuds sans témoin.
Le vote d’un nœud est annulé. La *majorité* est donc déterminée sur un total de **3 votes**. Après un échec, le cluster comprend trois nœuds : il s’agit du scénario 3.

![Explication du quorum dans le cas de quatre nœuds sans témoin](media/quorum/4-node-no-witness.png)

- Peut survivre à un échec de serveur : **Oui**.
- Peut survivre à l’échec d’un serveur, puis d’un autre : **Oui**.
- Peut survivre à deux échecs de serveur simultanés : **50 % de chance**.

#### <a name="four-nodes-with-a-witness"></a>Quatre nœuds avec un témoin.
Tous les nœuds votent, ainsi que le témoin. La *majorité* est donc déterminée par rapport à un total de **5 votes**. Après un échec, vous vous retrouvez dans le scénario 4. Après deux échecs simultanés, vous passez au scénario 2.

![Explication du quorum dans le cas de quatre nœuds avec un témoin](media/quorum/4-node-witness.png)

- Peut survivre à un échec de serveur : **Oui**.
- Peut survivre à l’échec d’un serveur, puis d’un autre : **Oui**.
- Peut survivre à deux échecs de serveur simultanés : **Oui**.

#### <a name="five-nodes-and-beyond"></a>Cinq nœuds et plus.
Tous les nœuds votent, ou tous sauf un, du moment que le total est impair. Les espaces de stockage direct ne peuvent pas gérer une situation dans laquelle plus de deux nœuds sont indisponibles. Par conséquent, aucun témoin n’est nécessaire.

![Explication du quorum dans le cas de cinq nœuds et plus](media/quorum/5-nodes.png)

- Peut survivre à un échec de serveur : **Oui**.
- Peut survivre à l’échec d’un serveur, puis d’un autre : **Oui**.
- Peut survivre à deux échecs de serveur simultanés : **Oui**.

Maintenant que nous savons comment fonctionne un quorum, intéressons-nous aux différents types de témoins de quorum.

### <a name="quorum-witness-types"></a>Types de témoins de quorum

Le clustering de basculement prend en charge trois types de témoins de quorum :

- **[Témoin cloud](/windows-server/failover-clustering/deploy-cloud-witness)**  : stockage Blob dans Azure accessible à tous les nœuds du cluster. Il conserve les informations de clustering dans un fichier witness.log, mais ne stocke pas de copie de la base de données de clusters.
- **Témoin de partage de fichiers** : partage de fichiers SMB configuré sur un serveur de fichiers exécutant Windows Server. Il conserve les informations de clustering dans un fichier witness.log, mais ne stocke pas de copie de la base de données de clusters.
- **Témoin de disque** : petit disque en cluster qui se trouve dans le groupe Stockage disponible du cluster. Ce disque est hautement disponible et peut basculer d’un nœud vers un autre. Il contient une copie de la base de données de cluster.  **_Les témoins de disque ne sont pas pris en charge par les espaces de stockage direct_* _.

## <a name="pool-quorum-overview"></a>Présentation du quorum de pool

Nous venons de parler du quorum de cluster, qui fonctionne au niveau du cluster. Maintenant, nous allons voir le quorum de pool qui fonctionne au niveau du pool (par exemple, vous pouvez perdre des nœuds et des lecteurs sans pour autant que le cluster devienne indisponible). Les pools de stockage ont été conçus pour être utilisés dans des scénarios cluster et non cluster, ce qui explique pourquoi ils disposent d’un mécanisme de quorum différent.

Le tableau ci-dessous donne une vue d’ensemble des résultats du quorum de pool pour chaque scénario :

| Nœuds de serveur | Peut survivre à l’échec d’un nœud de serveur | Peut survivre à l’échec d’un nœud de serveur, puis d’un autre | Peut survivre à deux échecs de nœuds de serveur simultanés |
|--------------|-------------------------------------|---------------------------------------------------|----------------------------------------------------|
| 2            | Non                                  | Non                                                | Non                                                 |
| 2 + témoin  | Oui                                 | Non                                                | Non                                                 |
| 3            | Oui                                 | Non                                                | Non                                                 |
| 3 + témoin  | Oui                                 | Non                                                | Non                                                 |
| 4            | Oui                                 | Non                                                | Non                                                 |
| 4 + témoin  | Oui                                 | Oui                                               | Oui                                                |
| 5 et plus  | Oui                                 | Oui                                               | Oui                                                |

## <a name="how-pool-quorum-works"></a>Fonctionnement du quorum de pool

Lorsque les lecteurs échouent, ou lorsque certains sous-ensembles de lecteurs perdent leur contact avec un autre sous-ensemble, les lecteurs survivants doivent vérifier qu’ils constituent la _majorité* du pool pour rester en ligne. S’ils ne peuvent pas le vérifier, ils seront mis hors connexion. Le pool est l’entité qui passe hors connexion ou qui reste en ligne selon qu’elle dispose de suffisamment de disques pour le quorum (50 % + 1). Le propriétaire de la ressource du pool (nœud de cluster actif) peut être ce « + 1 ».

Toutefois, le quorum de pool fonctionne différemment du quorum de cluster :

- Le pool utilise un nœud du cluster en tant que témoin pour obtenir une égalité. Ainsi, il permet au pool de survivre si la moitié des lecteurs ont échoué (ce nœud correspond au propriétaire de la ressource du pool).
- Le pool n’a pas de quorum dynamique.
- Le pool n’implémente pas sa propre version de suppression d’un vote.

### <a name="examples"></a>Exemples

#### <a name="four-nodes-with-a-symmetrical-layout"></a>Quatre nœuds avec une disposition symétrique.
Chacun des 16 lecteurs a un vote, et le nœud 2 a également un vote (puisqu’il s’agit du propriétaire de la ressource du pool). La *majorité* est donc déterminée par rapport à un total de **16 votes**. Si les nœuds 3 et 4 échouent, le sous-ensemble survivant comprendra 8 lecteurs, ainsi que le propriétaire de la ressource du pool, soit 9 votes sur 16. Par conséquent, le pool survit.

![Quorum de pool 1](media/quorum/pool-1.png)

- Peut survivre à un échec de serveur : **Oui**.
- Peut survivre à l’échec d’un serveur, puis d’un autre : **Oui**.
- Peut survivre à deux échecs de serveur simultanés : **Oui**.

#### <a name="four-nodes-with-a-symmetrical-layout-and-drive-failure"></a>Quatre nœuds avec une disposition symétrique et l’échec d’un lecteur.
Chacun des 16 lecteurs a un vote, et le nœud 2 a également un vote (puisqu’il s’agit du propriétaire de la ressource du pool). La *majorité* est donc déterminée par rapport à un total de **16 votes**. D’abord, le lecteur 7 connaît un échec. Si les nœuds 3 et 4 échouent, le sous-ensemble survivant comprendra 7 lecteurs, ainsi que le propriétaire de la ressource du pool, soit 8 votes sur 16. Par conséquent, le pool n’a pas la majorité et devient indisponible.

![Quorum de pool 2](media/quorum/pool-2.png)

- Peut survivre à un échec de serveur : **Oui**.
- Peut survivre à l’échec d’un serveur, puis d’un autre : **Non**.
- Peut survivre à deux échecs de serveur simultanés : **Non**.

#### <a name="four-nodes-with-a-non-symmetrical-layout"></a>Quatre nœuds avec une disposition non symétrique.
Chacun des 24 lecteurs a un vote, et le nœud 2 a également un vote (puisqu’il s’agit du propriétaire de la ressource du pool). La *majorité* est donc déterminée par rapport à un total de **24 votes**. Si les nœuds 3 et 4 échouent, le sous-ensemble survivant comprendra 8 lecteurs, ainsi que le propriétaire de la ressource du pool, soit 9 votes sur 24. Par conséquent, le pool n’a pas la majorité et devient indisponible.

![Quorum de pool 3](media/quorum/pool-3.png)

- Peut survivre à un échec de serveur : **Oui**.
- Peut survivre à l’échec d’un serveur, puis d’un autre : **Parfois** (il ne pourra pas survivre si les nœuds 3 et 4 échouent simultanément, mais il pourra survivre à tous les autres scénarios).
- Peut survivre à deux échecs de serveur simultanés : **Parfois** (il ne pourra pas survivre si les nœuds 3 et 4 échouent simultanément, mais il pourra survivre à tous les autres scénarios).

### <a name="pool-quorum-recommendations"></a>Recommandations relatives au quorum de pool

- Vérifiez que chaque nœud de votre cluster est symétrique (c’est-à-dire que chaque nœud a le même nombre de lecteurs)
- Activez le miroir triple ou la double parité pour tolérer les échecs de nœud et maintenir les disques virtuels en ligne.
- Si plus de deux nœuds échouent, ou si deux nœuds et un disque d’un autre nœud échouent, les volumes peuvent ne pas avoir accès aux trois copies de leurs données, et donc être mis hors connexion et devenir indisponibles. Il est recommandé de remettre les serveurs en ligne ou de remplacer rapidement les disques afin de garantir une résilience optimale pour toutes les données du volume.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les rubriques suivantes :

- [Configurer et gérer un quorum](/windows-server/failover-clustering/manage-cluster-quorum)
- [Configurer un témoin de cluster](../deploy/witness.md)
