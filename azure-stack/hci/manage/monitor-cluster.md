---
title: Analyser les clusters Azure Stack HCI
description: Comment Analyser les clusters, les serveurs, les machines virtuelles, les lecteurs et les volumes Azure Stack HCI à l’aide de Windows Admin Center et de PowerShell.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 08/12/2020
ms.openlocfilehash: 9ce1dc258243e9e17458c1f70e5a852a0b56996a
ms.sourcegitcommit: a3e042c782a38ecf3baf7a64b1d492a655972f9a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88202041"
---
# <a name="monitor-azure-stack-hci-clusters"></a>Analyser les clusters Azure Stack HCI

> S’applique à : Azure Stack HCI, version 20H2 ; Windows Server 2019

Il existe trois façons d’analyser les clusters Azure Stack HCI et leurs composants sous-jacents suivants : Windows Admin Center, [Azure Monitor](azure-monitor.md) et PowerShell.

## <a name="monitor-using-windows-admin-center-dashboard"></a>Analyse à l’aide du tableau de bord Windows Admin Center

[Installez Windows Admin Center](/windows-server/manage/windows-admin-center/deploy/install) sur un ordinateur ou un serveur d’administration, puis ajoutez et connectez-vous au cluster Azure Stack HCI que vous souhaitez analyser. Les alertes critiques sont affichées en haut du tableau de bord Windows Admin Center dès que vous vous connectez. Par exemple, la capture d’écran ci-dessous indique que les mises à jour doivent être installées et que le cluster a une erreur de lecteur critique :

:::image type="content" source="media/monitor-cluster/dashboard-alert.png" alt-text="Exemple d’alertes de tableau de bord Windows Admin Center":::

## <a name="monitor-virtual-machines"></a>Surveillance des machines virtuelles

Il est important de comprendre l’intégrité des machines virtuelles sur lesquelles s’exécutent vos applications et bases de données. Si une machine virtuelle ne dispose pas de suffisamment de processeurs ou de mémoire pour les charges de travail en cours d’exécution, les performances peuvent être ralenties ou l’application peut devenir indisponible. Si une machine virtuelle répond à moins de trois pulsations pendant une période de cinq minutes ou plus, il peut y avoir un problème.

Pour analyser les machines virtuelles dans Windows Admin Center, cliquez sur **Machines virtuelles** dans le menu **Outils** à gauche. Pour afficher un inventaire complet des machines virtuelles en cours d’exécution sur le cluster, cliquez sur **Inventaire** en haut de la page. Vous verrez une table contenant des informations sur chaque machine virtuelle, notamment :

- **Nom :** nom de la machine virtuelle.
- **État :** Indique si la machine virtuelle est en cours d’exécution ou arrêtée.
- **Serveur hôte :** Indique le serveur du cluster sur lequel la machine virtuelle s’exécute.
- **Utilisation de l’UC :** Pourcentage du nombre total de ressources du processeur du cluster consommées par la machine virtuelle.
- **Sollicitation de la mémoire :** Pourcentage du nombre total de ressources de mémoire du cluster consommées par la machine virtuelle.
- **Demande de mémoire :** Quantité de mémoire attribuée (Go ou Mo) consommée par la machine virtuelle.
- **Mémoire attribuée :** Quantité totale de mémoire attribuée à la machine virtuelle.
- **Uptime :** Durée d’exécution de la machine virtuelle en jours : heures:minutes:secondes.
- **Pulsation :** Indique si le cluster peut communiquer avec la machine virtuelle.
- **État de la récupération d’urgence :** Indique si la machine virtuelle est connectée à Azure Site Recovery.

## <a name="monitor-servers"></a>Surveiller les serveurs

Vous pouvez analyser les serveurs hôtes qui composent un cluster Azure Stack HCI directement à partir de Windows Admin Center. Si les serveurs hôtes ne sont pas configurés avec suffisamment de processeur ou de mémoire pour fournir les ressources nécessaires aux machines virtuelles, il peut s’agir d’un goulot d’étranglement des performances. 

Pour analyser les serveurs dans Windows Admin Center, cliquez sur **Serveurs** dans le menu **Outils** à gauche. Pour afficher un inventaire complet des serveurs dans le cluster, cliquez sur **Inventaire** en haut de la page. Vous verrez une table contenant des informations sur chaque serveur, notamment :

- **Nom :** Nom du serveur hôte dans le cluster.
- **État :** Indique si le serveur est en haut ou en bas.
- **Uptime :** Durée de fonctionnement du serveur.
- **Fabricant :** Le fabricant du matériel du serveur.
- **Modèle :** Modèle du serveur.
- **Numéro de série :** Numéro de série du serveur.
- **Utilisation de l’UC :** Pourcentage de processeurs du serveur hôte en cours d’utilisation. Aucun serveur du cluster ne doit utiliser plus de 85 % de son processeur pendant plus de 10 minutes. 
- **Utilisation de la mémoire :** Pourcentage de mémoire du serveur hôte en cours d’utilisation. Si un serveur dispose de moins de 100 Mo de mémoire disponible pendant 10 minutes ou plus, envisagez d’ajouter de la mémoire.

## <a name="monitor-volumes"></a>Superviser les volumes

Les volumes de stockage peuvent être rapidement remplis. Il est donc important de les analyser régulièrement afin d’éviter tout impact sur les applications. Pour analyser des volumes dans Windows Admin Center, cliquez sur **Volumes** dans le menu **Outils** à gauche. Pour afficher un inventaire complet des volumes de stockage sur le cluster, cliquez sur **Inventaire** en haut de la page. Vous verrez une table contenant des informations sur chaque volume, notamment :

- **Nom :** Nom du volume.
- **État :** « OK » indique que le volume est sain ; dans le cas contraire, un avertissement ou une erreur est signalé.
- **Système de fichiers :** Système de fichiers sur le volume (ReFS, CSVFS).
- **Résilience :** Indique si le volume est un miroir bidirectionnel, un miroir triple ou une parité à accélération miroir.
- **Taille :** Taille du volume (To/Go)
- **Pool de stockage :** Pool de stockage auquel le volume appartient.
- **Utilisation du stockage :** Pourcentage de la capacité de stockage du volume en cours d’utilisation.
- **IOPS :** Nombre d'opérations d’entrées/sorties par seconde.

## <a name="monitor-drives"></a>Analyser les lecteurs

Azure Stack HCI virtualise le stockage de manière à ce que la perte d’un lecteur individuel n’affecte pas de manière significative le cluster. Toutefois, les disques défaillants ont [besoin d’être remplacés](replace-drives.md) et les lecteurs peuvent avoir un impact sur les performances en remplissant ou en introduisant la latence. Si le système d’exploitation ne peut pas communiquer avec un lecteur, le lecteur peut être relâché ou déconnecté, son connecteur peut avoir échoué ou le lecteur lui-même peut avoir échoué. Windows retire automatiquement les lecteurs au bout de 15 minutes de perte de communication. 

Pour analyser des lecteurs dans Windows Admin Center, cliquez sur **Lecteurs** dans le menu **Outils** à gauche. Pour afficher un inventaire complet des lecteurs dans le cluster, cliquez sur **Inventaire** en haut de la page. Vous verrez une table contenant des informations sur chaque lecteur, notamment :

- **Numéro de série :** Numéro de série du lecteur.
- **État :** « OK » indique que le lecteur est sain ; dans le cas contraire, un avertissement ou une erreur est signalé.
- **Modèle :** Modèle du lecteur.
- **Taille :** Capacité totale du lecteur (To/Go).
- **Type :** Type de lecteur (SSD, HDD).
- **Utilisé pour :** Indique si le lecteur est utilisé pour le cache ou la capacité.
- **Emplacement :** L’adaptateur de stockage et le port auquel le lecteur est connecté.
- **Serveur :** Spécifie le nom du serveur auquel le lecteur est connecté.
- **Pool de stockage :** Pool de stockage auquel le lecteur appartient.
- **Utilisation du stockage :** Pourcentage de la capacité de stockage du lecteur en cours d’utilisation.

## <a name="add-performance-counters"></a>Ajouter des compteurs de performance

Utilisez l’outil Analyseur de performances dans Windows Admin Center afin d’afficher et de comparer des compteurs de performances pour Windows, des applications ou des appareils en temps réel.

1. Sélectionnez **Analyseur de performances** dans le menu **Outils** à gauche.
1. Cliquez sur **Espace de travail vide** pour démarrer un nouvel espace de travail, ou sur **Restaurer précédent** pour restaurer un espace de travail précédent.
1. Si vous créez un espace de travail, cliquez sur le bouton **Ajouter un compteur** et sélectionnez un ou plusieurs serveurs sources à superviser, ou sélectionnez l’ensemble du cluster.
1. Sélectionnez l’objet et l’instance que vous souhaitez superviser ainsi que le type de compteur et de graphique à utiliser pour l’affichage des informations de performances dynamiques.
1. Enregistrez l’espace de travail en choisissant **Enregistrer > Enregistrer sous** dans le menu supérieur.
 
Par exemple, la capture d’écran ci-dessous montre un compteur de performances appelé « Utilisation de la mémoire » qui affiche des informations sur la mémoire sur un cluster à deux nœuds.

:::image type="content" source="media/monitor-cluster/performance-monitor.png" alt-text="Exemple de compteur de performances en temps réel dans Windows Admin Center":::

## <a name="query-and-process-performance-history-with-powershell"></a>Historique des performances de requête et de processus avec PowerShell

Vous pouvez également analyser des clusters Azure Stack HCI à l’aide des cmdlets PowerShell qui renvoient des informations sur le cluster et ses composants. Consultez [Historique des performances pour les espaces de stockage direct](/windows-server/storage/storage-spaces/performance-history).

## <a name="use-the-health-service-feature"></a>Utiliser les fonctionnalités du service de contrôle d'intégrité

Toute erreur du service de contrôle d’intégrité sur le cluster doit être étudiée. Consultez [Service de contrôle d’intégrité dans Windows Server](/windows-server/failover-clustering/health-service-overview) pour savoir comment exécuter des rapports et identifier des erreurs.

## <a name="troubleshoot-health-and-operational-states"></a>Détecter un problème relatif aux états opérationnels et d'intégrité

Pour comprendre les états d’intégrité et opérationnels des pools de stockage, des disques virtuels et des lecteurs, consultez [Résoudre des problèmes relatifs aux espaces de stockage et aux états opérationnels et d’intégrité des espaces de stockage direct](/windows-server/storage/storage-spaces/storage-spaces-states).

## <a name="monitor-performance-using-storage-qos"></a>Analyser les performances à l’aide de la qualité de service du stockage

La qualité de service (QoS) du stockage offre un moyen d’analyser et de gérer de manière centralisée les e/s de stockage pour les machines virtuelles afin d’atténuer les problèmes bruyants du voisin et de fournir des performances cohérentes. Consultez [Qualité de service du stockage](/windows-server/storage/storage-qos/storage-qos-overview).

## <a name="set-up-alerts-in-azure-monitor"></a>Configurer des alertes dans Azure Monitor

Azure Stack HCI s’intègre à Azure Monitor pour permettre aux utilisateurs de [configurer des alertes](azure-monitor.md#setting-up-alerts-using-windows-admin-center) et être avertis si le processeur, la capacité du disque et les seuils d’utilisation de la mémoire sont dépassés, si les pulsations des machines virtuelles ne sont pas retournées ou si une erreur critique du système ou une défaillance du service de contrôle d’intégrité est survenue.

## <a name="next-steps"></a>Étapes suivantes

Pour consulter des informations connexes, reportez-vous également à :

- [Bien démarrer avec Azure Stack HCI et Windows Admin Center](../get-started.md)
- [Superviser Azure Stack HCI avec Azure Monitor](azure-monitor.md)