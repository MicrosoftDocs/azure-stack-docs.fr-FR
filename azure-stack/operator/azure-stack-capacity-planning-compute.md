---
title: Planification de la capacité de calcul Azure Stack Hub
description: Découvrez comment planifier la capacité pour des déploiements Azure Stack Hub.
author: ihenkel
ms.topic: article
ms.date: 07/16/2019
ms.author: inhenkel
ms.reviewer: prchint
ms.lastreviewed: 06/13/2019
ms.openlocfilehash: fc4fa70b21f3af47ba2a23894d8bc2b4e2233b17
ms.sourcegitcommit: 959513ec9cbf9d41e757d6ab706939415bd10c38
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2020
ms.locfileid: "76890049"
---
# <a name="azure-stack-hub-compute"></a>Calcul d’Azure Stack Hub

Les [tailles de machine virtuelle](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes) prises en charge sur Azure Stack Hub sont un sous-ensemble de celles prises en charge dans Azure. Azure impose des limites de ressources à différents niveaux pour éviter la consommation excessive des ressources (au niveau du service ou du serveur local). Si aucune limite n’est appliquée à la consommation des locataires, la surconsommation de ressources par certains locataires aurait un impact négatif sur l’expérience des autres locataires. Pour la sortie réseau de la machine virtuelle, il existe des limites de bande passante dans Azure Stack Hub correspondant aux limites d’Azure. Pour les ressources de stockage sur Azure Stack Hub, les limites d’IOPS de stockage évitent une surconsommation des ressources par les locataires pour l’accès au stockage.

>[!IMPORTANT]
>[Azure Stack Hub Capacity Planner](https://aka.ms/azstackcapacityplanner) ne tient pas compte des performances d’IOPS et ne les garantit pas.

## <a name="vm-placement"></a>Placement des machines virtuelles

Le moteur de placement Azure Stack Hub effectue le placement des machines virtuelles locataires sur les hôtes disponibles.

Azure Stack Hub détermine le placement des machines virtuelles selon deux critères. En premier lieu, il vérifie qu’il y a suffisamment de mémoire sur l’hôte pour le type de machine virtuelle en question. En deuxième lieu, il regarde si les machines virtuelles font partie d’un [groupe à haute disponibilité](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) ou si elles constituent des [groupes de machines virtuelles identiques](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).

Pour garantir la haute disponibilité d’un système de production à plusieurs machines virtuelles dans Azure Stack Hub, ces machines virtuelles sont placées dans un groupe à haute disponibilité qui les répartit entre plusieurs domaines d’erreur. Un domaine d’erreur au sein d’un groupe à haute disponibilité est défini comme un nœud unique dans l’unité d’échelle. Azure Stack Hub prend ne charge les groupes à haute disponibilité avec un maximum de trois domaines d’erreur, pour rester cohérent avec Azure. Les machines virtuelles placées dans un groupe à haute disponibilité sont physiquement isolées les unes des autres grâce à une répartition aussi équilibrée que possible sur plusieurs domaines d’erreur, autrement dit, hôtes Azure Stack Hub. En cas de défaillance matérielle, les machines virtuelles du domaine d’erreur défaillant sont redémarrées dans d’autres domaines d’erreur, si possible distincts de ceux des autres machines virtuelles du même groupe à haute disponibilité. Une fois l’hôte rétabli, les machines virtuelles sont rééquilibrées de façon à maintenir une haute disponibilité.  

Les groupes de machines virtuelles identiques utilisent des groupes à haute disponibilité sur le back-end et veillent à ce que chacune de leurs instances soient placées dans un domaine d’erreur différent. Cela signifie qu’ils utilisent des nœuds d’infrastructure Azure Stack Hub distincts. Par exemple, dans un système Azure Stack Hub à quatre nœuds, il peut arriver que la création d’un groupe de machines virtuelles identiques de trois instances échoue en raison de l’absence de la capacité de quatre nœuds pour placer trois instances du groupe de machines virtuelles identiques sur trois nœuds Azure Stack Hub distincts. De plus, les nœuds Azure Stack Hub peuvent être remplis à des niveaux variables avant la tentative de placement. 

Azure Stack Hub ne surengage pas la mémoire. En revanche, un surengagement du nombre de cœurs physiques est autorisé. 

Étant donné que les algorithmes de placement ne considèrent pas le ratio de surprovisionnement cœurs virtuels/cœurs physiques comme un facteur déterminant, chaque hôte peut présenter un ratio différent. Microsoft ne fournit pas de conseils sur le ratio cœurs physiques/cœurs virtuels en raison des variations entre les charges de travail et les exigences de niveau de service. 

## <a name="consideration-for-total-number-of-vms"></a>Prise en compte du nombre total de machines virtuelles 

Un nouvel aspect doit être pris en compte pour planifier correctement la capacité d’Azure Stack Hub. Avec la mise à jour 1901 (et chaque mise à jour ultérieure), le nombre total de machines virtuelles pouvant être créées est limité. Cette limite est destinée à être temporaire et a pour but de garantir la stabilité de la solution. Nous recherchons actuellement la cause du problème de stabilité en présence d’un plus grand nombre de machines virtuelles, mais aucun échéancier n’a encore été déterminé pour y remédier. Il y a désormais une limite de 60 machines virtuelles par serveur, la limite totale pour la solution étant de 700. Par exemple, la limite pour 8 serveurs Azure Stack Hub serait de 480 machines virtuelles (8 * 60). Pour une solution Azure Stack Hub de 12 à 16 serveurs, la limite serait de 700 machines virtuelles. Cette limite a été définie en gardant à l’esprit toutes les considérations relatives à la capacité de calcul, telles que la réserve de résilience et le rapport virtuel/physique du processeur qu’un opérateur souhaite conserver. Pour plus d’informations, consultez la nouvelle version de l’outil de planification de la capacité. 

Si la limite de mise à l'échelle de la machine virtuelle a été atteinte, les codes d’erreur suivants sont retournés comme résultat : VMsPerScaleUnitLimitExceeded, VMsPerScaleUnitNodeLimitExceeded.

## <a name="considerations-for-deallocation"></a>Considérations relatives à la désallocation

Quand une machine virtuelle est dans l’état _désalloué_, les ressources mémoire ne sont pas utilisées. Cela permet de placer d’autres machines virtuelles dans le système. 

Si la machine virtuelle désallouée est ensuite redémarrée, l’allocation ou l’utilisation de la mémoire est traitée comme une nouvelle machine virtuelle placée dans le système, et la mémoire disponible est consommée. 

Si aucune mémoire n’est disponible, la machine virtuelle ne démarre pas.

## <a name="azure-stack-hub-memory"></a>Mémoire Azure Stack Hub 

Azure Stack Hub est conçu pour maintenir opérationnelles les machines virtuelles correctement approvisionnées. Par exemple, si un hôte est hors connexion en raison d’une défaillance matérielle, Azure Stack Hub tente de redémarrer cette machine virtuelle sur un autre hôte. Un deuxième exemple est celui de la correction et de la mise à jour des logiciels Azure Stack Hub. S’il est nécessaire de redémarrer un hôte physique, une tentative est effectuée pour déplacer les machines virtuelles qui s’exécutent sur cet hôte vers un autre hôte disponible dans la solution.   

Cette stratégie de gestion ou de migration des machines virtuelles est possible uniquement s’il existe une capacité de mémoire réservée à ces opérations de redémarrage ou de migration. Une partie de la mémoire hôte totale est réservée et indisponible pour le placement des machines virtuelles des locataires. 

Dans le portail d’administration, vous pouvez consulter un graphique en secteurs indiquant la mémoire libre et utilisée dans Azure Stack Hub. Le diagramme suivant montre la capacité de mémoire physique sur une unité d’échelle Azure Stack Hub dans Azure Stack Hub :

![Capacité de mémoire physique](media/azure-stack-capacity-planning/physical-memory-capacity.png)

La mémoire utilisée inclut plusieurs composants. Les composants suivants consomment la mémoire dans la section Utilisation du graphique en secteurs :  

 -  Utilisation ou réserve du système d’exploitation hôte : il s’agit de la mémoire utilisée par le système d’exploitation sur l’hôte, les tables de pages virtuelles, les processus qui s’exécutent sur le système d’exploitation hôte et le cache de mémoire des espaces de stockage direct. Dans la mesure où cette valeur dépend de la mémoire utilisée par les différents processus Hyper-V exécutés sur l’hôte, elle peut varier.
 - Services d’infrastructure : il s’agit des machines virtuelles d’infrastructure qui composent Azure Stack Hub. Depuis la version 1904 d’Azure Stack Hub, cela implique environ 31 machines virtuelles qui occupent 242 Go + (4 Go x nombre de nœuds) de mémoire. L’utilisation de la mémoire du composant Services d’infrastructure pourra varier, car nous essayons de rendre nos services d’infrastructure plus évolutifs et résilients.
 - Réserve de résilience : Azure Stack Hub réserve une partie de la mémoire aux locataires en cas de défaillance d’un seul hôte, ainsi que pendant l’application de correctifs ou de mises à jour pour garantir la réussite des migrations dynamiques de machines virtuelles.
 - Machines virtuelles de locataire : il s’agit des machines virtuelles de locataire créées par les utilisateurs Azure Stack Hub. La mémoire est consommée, non seulement par les machines virtuelles en cours d’exécution, mais aussi par toutes les machines virtuelles qui ont atterri sur la structure fabric. Cela signifie que les machines virtuelles qui ont l’état « Création en cours » ou « Échec » ou bien qui sont arrêtées à partir de l’invité consomment de la mémoire. En revanche, les machines virtuelles libérées à l’aide de l’option d’arrêt appropriée du portail, de PowerShell ou de CLI ne consomment pas de mémoire d’Azure Stack Hub.
 - Fournisseurs de ressources à valeur ajoutée : machines virtuelles déployées pour des fournisseurs de ressources à valeur ajoutée tels que SQL, MySQL, App Service, etc.


La meilleure façon de comprendre la consommation de mémoire sur le portail consiste à utiliser [Azure Stack Hub Capacity Planner](https://aka.ms/azstackcapacityplanner) pour voir l’impact des différentes charges de travail. Le calcul suivant est identique à celui utilisé par le planificateur.

Il permet d’obtenir la capacité de mémoire totale disponible qui peut être utilisée pour le placement des machines virtuelles des locataires. Cette capacité de mémoire est calculée pour l’intégralité de l’unité d’échelle Azure Stack Hub. 


  Mémoire disponible pour le placement des machines virtuelles = Mémoire totale de l’hôte - Réserve de résilience - Mémoire utilisée par les machines virtuelles des locataires en cours d’exécution - Surcharge de l’infrastructure Azure Stack Hub <sup>1</sup>

  Réserve de résilience = H + R * ((N-1) * H) + V * (N-2)

> Où :
> - H = taille de la mémoire d’un seul serveur
> - N = taille de l’unité d’échelle (nombre de serveurs)
> - R = réserve de système d’exploitation pour la surcharge du système d’exploitation, à savoir 0,15 dans cette formule<sup>2</sup>
> - V = taille de la plus grande machine virtuelle dans l’unité d’échelle

  <sup>1</sup> Surcharge de l’infrastructure Azure Stack Hub = 242 Go + (4 Go x nombre de nœuds). Quelque 31 machines virtuelles sont utilisées pour héberger l’infrastructure d’Azure Stack Hub. Celles-ci consomment au total environ 242 Go + (4 Go x nombre de nœuds) de mémoire et 146 cœurs virtuels. Le nombre de machines virtuelles est lié à la nécessité de séparer les différents services pour répondre aux exigences de sécurité, d’évolutivité, de maintenance et d’action corrective. Grâce à cette structure de service interne, de nouveaux services d’infrastructure pourront être intégrés à mesure qu’ils sont développés. 

  <sup>2</sup> Réserve de système d’exploitation pour la surcharge = 15 % (0,15) de la mémoire de nœud. La valeur de la réserve de système d’exploitation est une estimation et varie en fonction de la capacité de mémoire physique du serveur et de la surcharge générale du système d’exploitation.


La valeur V, à savoir la taille de la plus grande machine virtuelle dans l’unité d’échelle, est basée dynamiquement sur la plus grande taille de mémoire de machine virtuelle de locataire. Par exemple, la plus grande taille de machine virtuelle peut être 7 Go ou 112 Go, ou toute autre taille de mémoire de machine virtuelle prise en charge dans la solution Azure Stack Hub. La modification de la plus grande machine virtuelle de l’infrastructure Azure Stack Hub entraîne une augmentation de la réserve de résilience en plus d’une augmentation de la mémoire de la machine virtuelle proprement dite. 

## <a name="frequently-asked-questions"></a>Forum Aux Questions (FAQ)

**Q** : Mon locataire a déployé une nouvelle machine virtuelle. Combien de temps faut-il pour que la capacité restante soit reflétée dans le graphique de capacité sur le portail administrateur ?

**R** : Prenez en compte que le panneau de capacité est actualisé toutes les 15 minutes.

**Q** : Le nombre de machines virtuelles déployées sur mon infrastructure Azure Stack Hub n’a pas changé, mais la capacité fluctue. Pourquoi ?

**R** : La mémoire disponible pour le placement des machines virtuelles dépend de plusieurs facteurs, dont la réserve du système d’exploitation hôte. Cette valeur dépend de la mémoire utilisée par les différents processus Hyper-V exécutés sur l’hôte, qui n’est pas une valeur constante.

**Q** : Quel état doivent avoir les machines virtuelles locataires pour consommer de la mémoire ?

**R** : La mémoire est consommée, non seulement par les machines virtuelles en cours d’exécution, mais aussi par toutes les machines virtuelles qui ont atterri sur la structure fabric. Cela signifie que les machines virtuelles qui ont l’état « Création en cours » ou « Échec » ou bien qui sont arrêtées à partir de l’invité consomment de la mémoire, contrairement aux machines qui sont libérées à l’aide de l’option d’arrêt appropriée à partir du portail, de PowerShell ou de la CLI.

**Q** : J’ai une infrastructure Azure Stack Hub comportant quatre hôtes. Mon locataire a trois machines virtuelles qui consomment 56 Go de RAM (D5_v2) chacune. L’une de ces machines virtuelles est redimensionnée à 112 Go de RAM (D14_v2), et les rapports sur la mémoire disponibles dans le tableau de bord montrent un pic d’utilisation de 168 Go dans le panneau de capacité. Le redimensionnement ultérieur des deux autres machines virtuelles D5_v2 à D14_v2 a entraîné une augmentation de la RAM de 56 Go uniquement sur chacune. Pourquoi ?

**R** : La mémoire disponible est fonction de la réserve de résilience conservée par Azure Stack Hub. La réserve de résilience est fonction de la taille de la plus grande machine virtuelle sur le tampon Azure Stack Hub. Dans un premier temps, la machine virtuelle la plus grande sur le tampon avait une capacité de 56 Go de mémoire. Quand la machine virtuelle a été redimensionnée, la machine virtuelle la plus grande sur le tampon est passée à 112 Go de mémoire, ce qui a non seulement augmenté la mémoire utilisée par cette machine virtuelle locataire, mais également augmenté la réserve de résilience. Cela a entraîné une augmentation de 56 Go (augmentation de la mémoire de 56 Go à 112 Go sur la machine virtuelle locataire) ainsi qu’une augmentation de la mémoire de résilience de réserve qui est passée à 112 Go. Quand les autres machines virtuelles ont ensuite été redimensionnées, la taille de la plus grande machine virtuelle est restée à la taille de la machine virtuelle de 112 Go et par conséquent, il n’y a pas eu d’augmentation de la réserve de résilience. L’augmentation de la consommation de mémoire s’est limitée à l’augmentation de la mémoire de la machine virtuelle (56 Go). 


> [!NOTE]
> Les exigences de planification de la capacité pour la mise en réseau sont minimes car seule la taille de l’adresse IP virtuelle publique est configurable. Pour plus d’informations sur la manière d’ajouter des adresses IP publiques à Azure Stack Hub, voir [Ajouter des adresses IP publiques](azure-stack-add-ips.md).

## <a name="next-steps"></a>Étapes suivantes
Apprenez-en davantage sur le [Stockage Azure Stack Hub](azure-stack-capacity-planning-storage.md)
