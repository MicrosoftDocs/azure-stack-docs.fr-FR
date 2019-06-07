---
title: Calcul de la planification de la capacité Azure Stack | Microsoft Docs
description: Découvrez comment planifier la capacité pour les déploiements Azure Stack.
services: azure-stack
documentationcenter: ''
author: prchint
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: e549413798ffc3c06c95bfbcf50ab4929ffeaf63
ms.sourcegitcommit: 80775f5c5235147ae730dfc7e896675a9a79cdbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/03/2019
ms.locfileid: "66461094"
---
# <a name="azure-stack-compute"></a>Calcul Azure Stack

Les [tailles de machine virtuelle](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes) prises en charge dans Azure Stack sont un sous-ensemble de celles prises en charge dans Azure. Azure impose des limites de ressources à différents niveaux pour éviter la consommation excessive des ressources (au niveau du service ou du serveur local). Si aucune limite n’est appliquée à la consommation des locataires, la surconsommation de ressources par certains locataires aurait un impact négatif sur l’expérience des autres locataires. Pour la sortie réseau de la machine virtuelle, des limites de bande passante sont en place dans Azure Stack conformément aux limites d’Azure. Pour les ressources de stockage, Azure Stack implémente des limites d’IOPS de stockage pour éviter une consommation excessive de base des ressources par les locataires pour l’accès au stockage.

>[!IMPORTANT]
>[Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) ne tient pas compte ni ne garantit les performances des IOPS.

## <a name="vm-placement"></a>Placement des machines virtuelles

Dans Azure Stack, le placement des machines virtuelles des locataires est automatiquement effectué par le moteur de placement sur tous les hôtes disponibles. Les deux seules considérations lors du placement des machines virtuelles consistent à déterminer si la quantité de mémoire est suffisante sur l’hôte pour ce type de machine virtuelle et si les machines virtuelles font partie d’un [groupe à haute disponibilité](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) ou sont des [groupes de machines virtuelles identiques](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).  

Pour garantir la haute disponibilité d’un système de production à plusieurs machines virtuelles dans Azure Stack, ces machines virtuelles sont placées dans un groupe à haute disponibilité qui les répartit entre plusieurs domaines d’erreur. Un domaine d’erreur au sein d’un groupe à haute disponibilité est défini comme un nœud unique dans l’unité d’échelle. Azure Stack gère les groupes à haute disponibilité comportant trois domaines d’erreur maximum dans un souci de compatibilité avec Azure. Les machines virtuelles placées dans un groupe à haute disponibilité sont physiquement isolées les unes des autres grâce à une répartition aussi équilibrée que possible sur plusieurs domaines d’erreur, autrement dit, hôtes Azure Stack. En cas de défaillance matérielle, les machines virtuelles du domaine d’erreur défaillant sont redémarrées dans d’autres domaines d’erreur, si possible distincts de ceux des autres machines virtuelles du même groupe à haute disponibilité. Une fois l’hôte rétabli, les machines virtuelles sont rééquilibrées de façon à maintenir une haute disponibilité.  

Les groupes de machines virtuelles identiques utilisent des groupes à haute disponibilité sur le back-end et veillent à ce que chacune de leurs instances soient placées dans un domaine d’erreur différent. Cela signifie qu’ils utilisent des nœuds d’infrastructure Azure Stack distincts. Par exemple, dans un système Azure Stack à 4 nœuds, il peut arriver qu’un groupe de machines virtuelles identiques de 3 instances échoue lors de la création en raison de l’absence de la capacité de 4 nœuds pour placer 3 instances du groupe de machines virtuelles identiques sur 3 nœuds Azure Stack distincts. De plus, les nœuds Azure Stack peuvent être remplis à des niveaux différents avant la tentative de placement. 

Azure Stack ne surengage pas la mémoire. En revanche, un surengagement du nombre de cœurs physiques est autorisé. Étant donné que les algorithmes de placement ne considèrent pas le ratio de surprovisionnement cœurs virtuels/cœurs physiques comme un facteur déterminant, chaque hôte peut présenter un ratio différent. En tant que Microsoft, nous ne fournissons pas de conseils sur le ratio cœurs physiques/cœurs virtuels à cause des variations entre les charges de travail et les exigences de niveau de service. 

## <a name="azure-stack-memory"></a>Mémoire Azure Stack 

Azure Stack est conçu pour laisser s’exécuter les machines virtuelles correctement provisionnées. Par exemple, si un hôte est hors connexion en raison d’une défaillance matérielle, Azure Stack tente de redémarrer cette machine virtuelle sur un autre hôte. L’application de correctifs et mises à jour au logiciel Azure Stack est un second exemple. S’il est nécessaire de redémarrer un hôte physique, une tentative est effectuée pour déplacer les machines virtuelles qui s’exécutent sur cet hôte vers un autre hôte disponible dans la solution.   

Cette stratégie de gestion ou de migration des machines virtuelles est possible uniquement s’il existe une capacité de mémoire réservée à ces opérations de redémarrage ou de migration. Une partie de la mémoire hôte totale est réservée et indisponible pour le placement des machines virtuelles des locataires. 

Dans le portail d’administration, vous pouvez consulter un graphique en secteurs qui indique la mémoire libre et utilisée dans Azure Stack. Le diagramme suivant montre la capacité de mémoire physique sur une unité d’échelle Azure Stack dans Azure Stack :

![Capacité de mémoire physique](media/azure-stack-capacity-planning/physical-memory-capacity.png)

La mémoire utilisée inclut plusieurs composants. Les composants suivants consomment la mémoire dans la section Utilisation du graphique en secteurs.  

- Utilisation ou réserve du système d’exploitation hôte : il s’agit de la mémoire utilisée par le système d’exploitation sur l’hôte, les tables de pages virtuelles, les processus qui s’exécutent sur le système d’exploitation hôte et le cache de mémoire des espaces de stockage direct. 
- Services d’infrastructure : il s’agit des machines virtuelles d’infrastructure qui composent Azure Stack. À compter de la version 1902 d’Azure Stack, cela implique 31 machines virtuelles qui occupent 242 Go + (4 Go x nombre de nœuds). Grâce à cette structure de service interne, de nouveaux services d’infrastructure pourront être intégrés à mesure qu’ils sont développés.
- Réserve de résilience : Azure Stack réserve une partie de la mémoire aux locataires lors d’une défaillance d’un seul hôte, ainsi que pendant l’application de correctifs ou de mises à jour pour garantir la réussite des migrations dynamiques des machines virtuelles. 
- Machines virtuelles de locataire : il s’agit des machines virtuelles de locataire créées par les utilisateurs Azure Stack. La mémoire est consommée, non seulement par les machines virtuelles en cours d’exécution, mais aussi par toutes les machines virtuelles qui ont atterri sur la structure fabric. Cela signifie que les machines virtuelles **en cours de création** ou **en état d’échec**, ou encore les machines virtuelles arrêtées depuis l’invité, consomment de la mémoire. En revanche, les machines virtuelles libérées à l’aide de l’option de libération correspondante ne consomment pas de mémoire d’Azure Stack. 

La meilleure façon de comprendre la consommation de mémoire sur le portail consiste à utiliser [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) pour voir l’impact des différentes charges de travail. Le calcul suivant est identique à celui utilisé par le planificateur.

Il permet d’obtenir la capacité de mémoire totale disponible qui peut être utilisée pour le placement des machines virtuelles des locataires. Cette capacité de mémoire est calculée pour l’intégralité de l’unité d’échelle Azure Stack. 


  Mémoire disponible pour le placement des machines virtuelles = Mémoire totale de l’hôte - Réserve de résilience - Mémoire utilisée par les machines virtuelles des locataires en cours d’exécution - Surcharge de l’infrastructure Azure Stack <sup>1</sup>

  Réserve de résilience = H + R * ((N-1) * H) + V * (N-2)

> Où :
> - H = taille de la mémoire d’un seul serveur
> - N = taille de l’unité d’échelle (nombre de serveurs)
> - R = réserve de système d’exploitation pour la surcharge du système d’exploitation, à savoir 0,15 dans cette formule<sup>2</sup>
> - V = taille de la plus grande machine virtuelle dans l’unité d’échelle

  <sup>1</sup> Surcharge de l’infrastructure Azure Stack = 242 Go + (4 Go x nombre de nœuds). Environ 31 machines virtuelles sont utilisées pour héberger l’infrastructure d’Azure Stack. Celles-ci consomment au total environ 242 Go + (4 Go x nombre de nœuds) de mémoire et 146 cœurs virtuels. Le nombre de machines virtuelles est lié à la nécessité de séparer les différents services pour répondre aux exigences de sécurité, d’évolutivité, de maintenance et d’action corrective. Grâce à cette structure de service interne, de nouveaux services d’infrastructure pourront être intégrés à mesure qu’ils sont développés. 

  <sup>2</sup> Réserve de système d’exploitation pour la surcharge = 15 % (0,15) de la mémoire de nœud. La valeur de la réserve de système d’exploitation est une estimation et varie en fonction de la capacité de mémoire physique du serveur et de la surcharge générale du système d’exploitation.


La valeur V, à savoir la taille de la plus grande machine virtuelle dans l’unité d’échelle, est basée dynamiquement sur la plus grande taille de mémoire de machine virtuelle de locataire. Par exemple, la plus grande taille de machine virtuelle peut être 7 Go ou 112 Go, ou toute autre taille de mémoire de machine virtuelle prise en charge par la solution Azure Stack. La modification de la plus grande machine virtuelle de la structure fabric Azure Stack entraîne une augmentation de la réserve de résilience en plus d’une augmentation de la mémoire de la machine virtuelle elle-même. 

> [!NOTE]
> Les exigences de planification de la capacité pour la mise en réseau sont minimes car seule la taille de l’adresse IP virtuelle publique est configurable. Pour plus d’informations sur la manière d’ajouter d’autres adresses IP publiques à Azure Stack, consultez [Ajouter des adresses IP publiques](azure-stack-add-ips.md).

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur le [stockage Azure Stack](azure-stack-capacity-planning-storage.md)