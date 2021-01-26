---
title: Équilibrage de charge des machines virtuelles
description: Utilisez cette rubrique pour découvrir comment configurer la fonctionnalité d’équilibrage de charge des machines virtuelles dans Azure Stack HCI et Windows Server.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 1/14/2021
ms.openlocfilehash: 9caf8efdd8db46479cce3c5c4299fb5588c7d37a
ms.sourcegitcommit: 51ce5ba6cf0a377378d25dac63f6f2925339c23d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98224749"
---
# <a name="virtual-machine-load-balancing"></a>Équilibrage de charge des machines virtuelles

> S’applique à : Azure Stack HCI version 20H2, Windows Server 2019, Windows Server 2016

Les dépenses d’investissement nécessaires pour passer en production constituent un point majeur pour les déploiements HCI. Une certaine redondance est souvent ajoutée pour éviter toute sous-capacité pendant les pics de trafic en production, mais elle augmente les dépenses d’investissement. Cette redondance est souvent nécessaire, car certains serveurs du cluster hébergent davantage de machines virtuelles, tandis que d’autres serveurs sont sous-exploitées.

Activé par défaut dans Azure Stack HCI, Windows Server 2019 et Windows Server 2016, l’équilibrage de charge des machines virtuelles est une fonctionnalité qui vous permet d’optimiser l’utilisation des serveurs dans vos clusters. Il identifie les serveurs trop sollicités et effectue une migration dynamique des machines virtuelles depuis ces serveurs vers des serveurs sous-sollicités. Les stratégies d’échec, comme l’anti-affinité, les domaines d’erreur (sites) et les propriétaires possibles, sont honorées.

L’équilibrage de charge des machines virtuelles évalue la charge d’un serveur en fonction des données heuristiques suivantes :

- **Sollicitation de la mémoire actuelle :** La mémoire est la contrainte de ressource la plus courante sur un hôte Hyper-V.
- **Utilisation du processeur moyenne sur une fenêtre de cinq minutes :** Atténue le risque de sur-sollicitation d’un serveur dans le cluster.

## <a name="how-does-vm-load-balancing-work"></a>Comment fonctionne l’équilibrage de charge des machines virtuelles ?

L’équilibrage de charge des machines virtuelles se produit automatiquement quand vous ajoutez un nouveau serveur à votre cluster. Vous pouvez aussi le configurer pour qu’il s’effectue de manière récurrente et régulière.

### <a name="when-a-new-server-is-added-to-a-cluster"></a>Quand un nouveau serveur est ajouté à un cluster

Quand vous joignez un nouveau serveur à votre cluster, la fonctionnalité d’équilibrage de charge des machines virtuelles équilibre automatiquement la capacité des serveurs existants avec celle du serveur qui vient d’être ajouté en procédant dans l’ordre suivant :

1. La sollicitation de la mémoire et l’utilisation du processeur sont évaluées sur les serveurs existants du cluster.
2. Tous les serveurs qui dépassent le seuil sont identifiés.
3. Les serveurs dont la sollicitation de la mémoire et l’utilisation du processeur sont les plus élevées sont identifiés pour déterminer la priorité de l’équilibrage.
4. Des machines virtuelles sont migrées de manière dynamique (sans temps d’arrêt) depuis un serveur qui dépasse le seuil vers le serveur nouvellement ajouté au cluster.

:::image type="content" source="media/vm-load-balancing/server-added.png" alt-text="Image présentant un nouveau serveur en train d’être ajouté à un cluster" border="false"::: 

### <a name="recurring-load-balancing"></a>Équilibrage de charge récurrent

Par défaut, l’équilibrage de charge des machines virtuelles est configuré pour être périodique : la sollicitation de la mémoire et l’utilisation du processeur sur chaque serveur du cluster sont évaluées à des fins d’équilibrage toutes les 30 minutes. Le déroulement des étapes est le suivant :

1. La sollicitation de la mémoire et l’utilisation du processeur sont évaluées sur tous les serveurs du cluster.
2. Tous les serveurs qui dépassent le seuil et ceux situés en-dessous sont identifiés.
3. Les serveurs dont la sollicitation de la mémoire et l’utilisation du processeur sont les plus élevées sont identifiés pour déterminer la priorité de l’équilibrage.
4. Des machines virtuelles sont migrées de manière dynamique (sans temps d’arrêt) depuis un serveur qui dépasse le seuil vers un autre serveur qui se trouve sous le seuil minimal.

:::image type="content" source="media/vm-load-balancing/periodic-balancing.png" alt-text="Image présentant un cluster actif en cours de rééquilibrage automatique" border="false"::: 

## <a name="configure-vm-load-balancing-using-windows-admin-center"></a>Configurer l’équilibrage de charge des machines virtuelles avec Windows Admin Center

Le moyen le plus simple de configurer l’équilibrage de charge des machines virtuelles consiste à utiliser Windows Admin Center. 

:::image type="content" source="media/vm-load-balancing/vm-load-balancing.png" alt-text="Configuration de l’équilibrage de charge des machines virtuelles avec Windows Admin Center" lightbox="media/vm-load-balancing/vm-load-balancing.png":::

1. Connectez-vous à votre cluster et accédez à **Outils > Paramètres**.

2. Sous **Paramètres**, sélectionnez **Équilibrage de charge des machines virtuelles**.

3. Sous **Équilibrer les machines virtuelles**, sélectionnez **Toujours** pour équilibrer la charge lors des jonctions de serveurs et toutes les 30 minutes, **Jonctions de serveurs** pour équilibrer la charge uniquement lors des jonctions de serveurs ou **Jamais** pour désactiver la fonctionnalité d’équilibrage de charge des machines virtuelles. Le paramètre par défaut est **Toujours**.

4. Sous **Intensité**, sélectionnez **Faible** pour migrer de manière dynamique les machines virtuelles quand le serveur est chargé à plus de 80 %, **Moyenne** pour effectuer une migration quand le serveur est chargé à plus de 70 % ou **Élevée** pour calculer la moyenne des serveurs dans le cluster avant d’effectuer une migration quand le serveur est plus élevé que la moyenne de plus de 5 %. Le paramètre par défaut est **Faible**.

## <a name="configure-vm-load-balancing-using-windows-powershell"></a>Configurer l’équilibrage de charge des machines virtuelles avec Windows PowerShell

Vous pouvez indiquer si et quand l’équilibrage de charge se produit à l’aide de la propriété commune de cluster `AutoBalancerMode`. Pour contrôler à quel moment équilibrer le cluster, exécutez la commande suivante dans PowerShell, en substituant une valeur indiquée dans le tableau ci-dessous :

```PowerShell
(Get-Cluster).AutoBalancerMode = <value>
```

|AutoBalancerMode |Comportement|
|-----------------|-----------|
| 0 | Désactivé |
| 1 | Équilibrer la charge lors d’une jonction de serveur |
| 2 (par défaut) | Équilibrer la charge lors d’une jonction de serveur et toutes les 30 minutes |

Vous pouvez aussi configurer l’intensité de l’équilibrage à l’aide de la propriété commune de cluster `AutoBalancerLevel`. Pour contrôler le seuil d’intensité, exécutez la commande suivante dans PowerShell, en substituant une valeur indiquée dans le tableau ci-dessous :

```PowerShell
(Get-Cluster).AutoBalancerLevel = <value>
```

| AutoBalancerLevel | Intensité | Comportement |
|-------------------|----------------|----------|
| 1 (par défaut) | Faible | Déplacer quand l’hôte est chargé à plus de 80 % |
| 2 | Moyenne | Déplacer quand l’hôte est chargé à plus de 70 % |
| 3 | Élevé | Calculer la moyenne des serveurs dans le cluster et déplacer quand l’hôte est de plus de 5 % au-dessus de la moyenne |

Pour vérifier la façon dont les propriétés `AutoBalancerLevel` et `AutoBalancerMode` sont définies, exécutez la commande suivante dans PowerShell :

```PowerShell
Get-Cluster | fl AutoBalancer*
```

## <a name="next-steps"></a>Étapes suivantes

Pour consulter des informations connexes, reportez-vous également à :

- [Gérer les machines virtuelles](vm.md)
- [Gérer les machines virtuelles avec PowerShell](vm-powershell.md)
- [Créer des règles d’affinité de machine virtuelle](vm-affinity.md)
