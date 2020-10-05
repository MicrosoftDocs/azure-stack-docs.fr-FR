---
title: Gérer SMB Multichannel
description: Servez-vous de cette rubrique pour utiliser SMB Multichannel afin d’augmenter le débit et la tolérance de panne réseau.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/30/2020
ms.openlocfilehash: b7dc0c8050bc9d813115d2c3ebd7716b87bd89fa
ms.sourcegitcommit: 69cfff119ab425d0fbb71e38d1480d051fc91216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91585960"
---
# <a name="manage-smb-multichannel"></a>Gérer SMB Multichannel

> S’applique à : Azure Stack HCI, version 20H2 ; Windows Server 2019 ; Windows Server 2016 ; Windows Server 2012 R2 ; Windows Server 2012 ; Windows 10

SMB Multichannel fait partie du protocole SMB 3.0 (Server Message Block), qui augmente les performances du réseau et la disponibilité des serveurs de fichiers.

SMB Multichannel permet aux serveurs de fichiers d’utiliser plusieurs connexions réseau simultanément. Cela facilite l’agrégation de la bande passante réseau et de la tolérance de panne réseau lorsque plusieurs chemins sont disponibles entre le client SMB 3.0 et le serveur SMB 3.0. Cela permet aux applications serveur de tirer pleinement parti de toute la bande passante réseau disponible et de les rendre plus résistantes aux défaillances du réseau.

SMB Multichannel fournit les fonctionnalités suivantes :

- **Débit accru.** Le serveur de fichiers peut transmettre simultanément des données supplémentaires à l’aide de plusieurs connexions pour des cartes réseau haut débit ou plusieurs cartes réseau.

- **Tolérance de panne réseau.** Lorsque les clients utilisent simultanément plusieurs connexions réseau, les clients peuvent continuer sans interruption malgré la perte d’une connexion réseau.

- **Configuration automatique.** SMB Multichannel découvre automatiquement plusieurs chemins d’accès réseau disponibles et ajoute dynamiquement des connexions en fonction des besoins.

## <a name="requirements-for-smb-multichannel"></a>Configuration requise pour SMB Multichannel

Dans la mesure où SMB Multichannel est activé par défaut, il n’est pas nécessaire d’installer des rôles, des services de rôle ou des fonctionnalités supplémentaires. Le client SMB détecte et utilise automatiquement plusieurs connexions réseau lorsque la configuration est identifiée. Toutefois, SMB Multichannel a les exigences suivantes :

- Au moins deux ordinateurs qui exécutent Azure Stack HCI, Windows Server ou le système d’exploitation Windows 10.

- Au moins l’une des configurations suivantes :

    - Plusieurs cartes réseau

    - Une ou plusieurs cartes réseau prenant en charge la mise à l’échelle côté réception (RSS)

    - Plusieurs cartes réseau associées (voir [Association de cartes réseau](#nic-teaming))

    - Une ou plusieurs cartes réseau prenant en charge l’accès direct à la mémoire à distance (RDMA)

## <a name="configure-smb-multichannel"></a>Configurer SMB Multichannel

Cette section décrit certaines options de configuration pour le déploiement de SMB Multichannel au moyen d’un groupe de cartes réseau. Ces configurations ne sont que des exemples de configurations. Il existe de nombreuses autres configurations possibles qui ne sont pas incluses dans cette rubrique.

### <a name="single-rss-capable-network-adapter"></a>Une seule carte réseau compatible RSS

Dans cette configuration classique, un client SMB et un serveur SMB sont configurés à l’aide d’une seule carte réseau 10 Gigabit Ethernet (10 GbE). Lorsque SMB est déployé sans SMB Multichannel et qu’une seule session SMB est établie, SMB crée une seule connexion TCP/IP. Avec un seul cœur de processeur, cette configuration conduit fondamentalement à la congestion, en particulier quand de nombreuses opérations d’e/s de petite taille sont effectuées. Par conséquent, le risque d’un goulot d’étranglement de performances est important.

La plupart des cartes réseau actuelles offrent une fonctionnalité appelée « mise à l’échelle côté réception » (RSS), qui permet à plusieurs connexions d’être réparties automatiquement sur plusieurs cœurs de processeur. Toutefois, si vous utilisez une seule connexion, RSS ne peut pas vous aider. Lorsque vous utilisez SMB Multichannel avec une carte réseau prenant en charge RSS, SMB crée plusieurs connexions TCP/IP pour cette session particulière. Cette configuration évite un goulot d’étranglement potentiel sur un seul cœur de processeur si de nombreuses opérations d’e/s de petite taille sont requises.

### <a name="multiple-network-adapters"></a>Plusieurs cartes réseau

Dans cette configuration, un client SMB et un serveur SMB sont configurés à l’aide de plusieurs cartes réseau 10 GbE. Lorsque SMB est déployé sans SMB Multichannel et qu’une seule session SMB est établie, SMB utilise l’une des cartes réseau disponibles pour créer une seule connexion TCP/IP. Dans ce scénario, il n’est pas possible d’agréger la bande passante de plusieurs cartes réseau. par exemple, vous ne pouvez pas atteindre 2 gigabits par seconde (Gbits/s) quand vous utilisez deux cartes réseau de 1 GbE. Il y a également un risque d’échec si la carte réseau sélectionnée est déconnectée ou désactivée.

Lorsque SMB est déployé avec SMB Multichannel, SMB crée plusieurs connexions TCP/IP pour une seule session avec au moins une ou plusieurs connexions par interface si les cartes réseau sont compatibles avec RSS. Cette configuration permet à SMB d’utiliser la bande passante de carte réseau combinée qui est disponible et permet au client SMB de continuer sans interruption en cas de défaillance d’une carte réseau.

### <a name="nic-teaming"></a>Association de cartes réseau

Azure Stack HCI et Windows Server prennent en charge la possibilité de combiner plusieurs cartes réseau en une seule carte réseau à l’aide d’une fonctionnalité appelée Association de cartes réseau. Bien qu’une équipe fournisse toujours la tolérance de panne, lorsque SMB est déployé sans SMB Multichannel, SMB ne crée qu’une seule connexion TCP/IP par équipe. Cette configuration mène à des limitations à la fois dans le nombre de cœurs de processeur engagés et dans l’utilisation maximale de la bande passante de l’équipe.

Lorsque SMB est déployé avec SMB Multichannel, SMB crée plusieurs connexions TCP/IP pour une seule session afin d’obtenir un meilleur équilibre entre les cœurs de processeur et une meilleure utilisation de la bande passante disponible. L’Association de cartes réseau continue à offrir la fonctionnalité de basculement, ce qui fonctionne plus rapidement qu’en utilisant SMB Multichannel seul. Nous recommandons également l’Association de cartes réseau, car elle offre des fonctionnalités de basculement à d’autres charges de travail qui ne reposent pas sur SMB, car ces charges de travail ne peuvent pas tirer parti des fonctionnalités de basculement de SMB Multichannel.

Si vous utilisez un ensemble de cartes réseau dédiées pour le trafic des espaces de stockage direct, comme c’est parfois le cas avec Azure Stack HCI, l’Association de ces cartes réseau de stockage est strictement facultative, elle n’offre aucun avantage significatif ni aucun inconvénient.

>[!IMPORTANT]
>Sur Windows Server 2012 R2 et les versions antérieures, n’utilisez pas l’Association de cartes réseau si vous envisagez de vous servir des fonctionnalités RDMA des cartes réseau. Sur ces systèmes d’exploitation, une équipe de cartes réseau compatible RDMA est toujours signalée comme non compatible RDMA, car l’Association désactive la fonctionnalité RDMA de la carte réseau.

### <a name="single-or-multiple-rdma-capable-network-adapters"></a>Une ou plusieurs cartes réseau compatibles RDMA

SMB Multichannel détecte les fonctionnalités RDMA des cartes réseau, ce qui active la fonctionnalité SMB Direct appelée SMB Direct sur RDMA. Sans SMB Multichannel, SMB utilise des connexions TCP/IP standard avec les cartes réseau compatibles RDMA, où toutes les cartes réseau fournissent une pile TCP/IP qui existe côte à côte avec la nouvelle pile RDMA.

Lorsque SMB est déployé avec SMB Multichannel, SMB détecte la fonctionnalité RDMA d’une carte réseau et crée plusieurs connexions RDMA pour cette seule session, avec deux connexions RDMA par interface. Cette configuration permet à SMB de tirer parti du débit élevé, de la faible latence et de la faible utilisation du processeur offerte par les cartes réseau prenant en charge RDMA. Elle offre également une tolérance de panne lorsque vous utilisez plusieurs interfaces RDMA.

>[!IMPORTANT]
>Après la création d’une connexion RDMA, la connexion TCP/IP pour la négociation du protocole d’origine n’est plus utilisée. Toutefois, cette connexion est conservée en cas d’échec d’une autre connexion RDMA.

### <a name="smb-multichannel-rdma-capable-network-adapters-and-nic-teaming-compatibility"></a>SMB Multichannel, cartes réseau compatibles RDMA et compatibilité d’association de cartes réseau

Le tableau suivant récapitule les différentes fonctionnalités disponibles lorsque vous combinez SMB Multichannel, RDMA (SMB Direct) et l’Association de cartes réseau.

| Configuration                                           | Débit | Tolérance de panne pour SMB | Tolérance de panne pour non SMB | Utilisation plus basse de l’UC |
|:-------------------------------------------------------:|:----------:|:-----------------------:|:---------------------------:|:---------------------:|
| Carte réseau unique (pas RSS)                         | *          |                         |                             |                       |
| Cartes réseau multiples (pas RSS)                      | **         | *                       |                             |                       |
| Cartes réseau multiples (pas RSS) avec Association de cartes réseau     | **         | **                      | *                           |                       |
| Carte réseau unique avec RSS                         | *          |                         |                             |                       |
| Cartes réseau multiples avec RSS                      | **         | *                       |                             |                       |
| Cartes réseau multiples avec RSS et Association de cartes réseau      | **         | **                      | *                           |                       |
| Carte réseau unique compatible RDMA                     | *          |                         |                             | *                     |
| Cartes réseau multiples compatibles RDMA                  | ***        | *                       |                             | *                     |
| Cartes réseau multiples compatibles RDMA avec Association de cartes réseau | ***        | **                      | *                           | *                     |

Si vous exécutez Windows Server 2016 ou une version ultérieure, la solution idéale consiste à utiliser plusieurs cartes réseau compatibles RDMA et à combiner l’Association de cartes réseau avec SMB Multichannel. Cette combinaison offre le meilleur débit, fournit une tolérance aux pannes pour les applications qui utilisent SMB et d’autres protocoles et a l’impact le plus faible sur le processeur.

Comme indiqué ci-dessus, lorsque vous utilisez des cartes réseau compatibles RDMA sous Windows Server 2012 R2 ou une version antérieure, l’Association de cartes réseau n’est pas une bonne option, car elle désactive la fonctionnalité RDMA de la carte réseau.

### <a name="example-configurations-without-smb-multichannel"></a>Exemples de configurations sans SMB Multichannel

Si vous envisagez d’utiliser une seule carte réseau sans RSS, vous ne bénéficiez pas de plusieurs connexions réseau et, par conséquent, SMB Multichannel n’est pas utilisé. En outre, si vous envisagez d’utiliser des cartes réseau de différentes vitesses, SMB Multichannel sélectionne automatiquement la carte réseau la plus rapide. Cela est dû au fait que les cartes réseau qui sont du même type (par exemple, RDMA, RSS ou aucune des deux) et qui ont la même vitesse sont utilisées simultanément par SMB Multichannel. Les cartes réseau plus lentes sont inactives.

## <a name="disable-smb-multichannel"></a>Désactiver SMB Multichannel
En règle générale, vous n’avez pas besoin de désactiver SMB Multichannel. Toutefois, si vous souhaitez désactiver SMB Multichannel, par exemple dans un environnement de test, utilisez les procédures Windows PowerShell suivantes.

Tout d’abord, connectez-vous à l’un des serveurs en ouvrant une session PowerShell :

```PowerShell
Enter-PSSession <server-name>
```

Pour désactiver SMB Multichannel côté serveur, utilisez l’applet de commande suivante :

```PowerShell
Set-SmbServerConfiguration -EnableMultiChannel $false
```

Pour désactiver SMB Multichannel côté client, utilisez l’applet de commande suivante :

```PowerShell
Set-SmbClientConfiguration -EnableMultiChannel $false
```

>[!NOTE]
>La désactivation de SMB Multichannel sur le client ou le serveur empêche les deux systèmes de l’utiliser.

## <a name="re-enable-smb-multichannel"></a>Réactiver SMB Multichannel

Si vous avez désactivé SMB Multichannel et que vous souhaitez le réactiver, utilisez les procédures suivantes.

Pour désactiver SMB Multichannel côté serveur, utilisez l’applet de commande suivante :

```PowerShell
Set-SmbServerConfiguration -EnableMultiChannel $true
```

Pour réactiver SMB Multichannel côté client, utilisez l’applet de commande suivante :

```PowerShell
Set-SmbClientConfiguration -EnableMultiChannel $true
```

>[!NOTE]
>Vous devez réactiver SMB Multichannel sur le client et le serveur pour recommencer à l’utiliser.

## <a name="test-smb-multichannel"></a>Tester SMB Multichannel

Cette section décrit certains scénarios de test de SMB Multichannel, qui comprend la comparaison d’une copie de fichiers avec et ensuite sans SMB Multichannel et le fait de provoquer intentionnellement l’échec d’une carte réseau lors d’une opération de copie de fichiers.

### <a name="compare-a-file-copy-with-and-without-smb-multichannel"></a>Comparer une copie de fichiers avec et sans SMB Multichannel

Pour mesurer l’augmentation du débit fourni par SMB Multichannel, effectuez les tests suivants. Redémarrez le serveur et le client avant chaque test pour vous assurer qu’ils fonctionnent dans des conditions similaires.

1. Configurez SMB Multichannel en utilisant l’une des configurations décrites précédemment.

1. Mesurez la durée nécessaire à l’exécution d’une opération de copie de fichiers de longue durée en utilisant SMB Multichannel.

1. Désactiver SMB Multichannel. Pour obtenir des instructions, consultez [Désactiver SMB Multichannel](#disable-smb-multichannel).

1. Mesurez la durée nécessaire à l’exécution de la même copie de fichiers sans SMB Multichannel.

1. Réactiver SMB Multichannel. Pour obtenir des instructions, consultez [Réactiver SMB Multichannel](#re-enable-smb-multichannel).

1. Comparez les deux résultats.

>[!IMPORTANT]
>Pour éviter l’impact sur les performances de la mise en cache, commencez par copier une grande quantité de données qui dépasse la taille de la mémoire disponible. Ensuite, effectuez à nouveau l’opération de copie, la première opération de copie servant de mise en route. Ne prenez en compte que la deuxième opération de copie.

### <a name="cause-one-of-the-network-adapters-to-fail-during-a-file-copy-with-smb-multichannel"></a>Provoquer l’échec de l’une des cartes réseau pendant une copie de fichiers avec SMB Multichannel

Pour confirmer la fonction de basculement de SMB Multichannel :

1. Assurez-vous que SMB Multichannel fonctionne dans une configuration à plusieurs cartes réseau.

1. Exécutez une opération de copie de fichiers de longue durée.

1. Pendant l’exécution de l’opération de copie de fichiers, simulez une défaillance de l’un des chemins d’accès réseau en déconnectant l’un des câbles ou en désactivant l’une des cartes réseau.

1. Confirmez que la copie de fichiers continue à utiliser la carte réseau restante et fonctionne sans erreur de copie de fichier.

Assurez-vous qu’aucune autre charge de travail n’utilise le chemin d’accès à la connexion réseau déconnectée. Cette précaution permet d’éviter une défaillance possible dans les charges de travail qui ne tirent pas parti de SMB Multichannel.

## <a name="verify-that-smb-multichannel-works"></a>Vérifier le fonctionnement de SMB Multichannel

Utilisez la procédure suivante pour vérifier le fonctionnement de SMB Multichannel.

1. Pour vérifier que la configuration de la carte réseau est correcte, tapez la commande suivante dans Windows PowerShell sur le serveur SMB et le client SMB.

   ```PowerShell
   Get-NetAdapter
   Get-NetAdapterRSS
   Get-NetAdapterRDMA
   Get-NetAdapterHardwareInfo
   ```

2. Pour vérifier que SMB Multichannel est activé, vérifiez que SMB identifie correctement les cartes réseau et que les fonctionnalités RSS et RDMA de la carte réseau sont correctement identifiées, procédez comme suit :

   Sur le client SMB, tapez la commande suivante dans Windows PowerShell :

   ```PowerShell
   Get-SmbClientConfiguration | Select EnableMultichannel
   Get-SmbClientNetworkInterface
   ```

   Sur le serveur SMB, tapez la commande suivante dans Windows PowerShell :

   ```PowerShell
   Get-SmbServerConfiguration | Select EnableMultichannel
   Get-SmbServerNetworkInterface
   ```

3. Sur le client SMB, effectuez une opération de copie de fichiers de longue durée qui crée une session en cours avec le serveur SMB. Pendant que l’opération de copie s’exécute, tapez la commande suivante dans Windows PowerShell pour vérifier que la connexion utilise la bonne version de SMB et que SMB Multichannel fonctionne.

   ```PowerShell
   Get-SmbConnection
   Get-SmbMultichannelConnection
   Get-SmbMultichannelConnection -IncludeNotSelected
   ```

## <a name="next-steps"></a>Étapes suivantes

Pour consulter des informations connexes, reportez-vous également à :

- [Vue d’ensemble du partage de fichiers à l’aide du protocole SMB 3 dans Windows Server](/windows-server/storage/file-server/file-server-smb-overview)
- [Réseaux de clusters à plusieurs cartes réseau et SMB Multichannel simplifiés](/windows-server/failover-clustering/smb-multichannel)
- [SMB Direct](/windows-server/storage/file-server/smb-direct)
