---
title: Protéger des machines virtuelles déployées sur Azure Stack Hub
description: Découvrez comment créer un plan de récupération pour protéger des machines virtuelles déployées sur Azure Stack Hub contre la perte de données et les temps d’arrêt non planifiés.
author: mattbriggs
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: hectorl
ms.lastreviewed: 3/19/2019
ms.openlocfilehash: 824352a27ae91b2bf0a351b9dc280c83bfb9d19a
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77703959"
---
# <a name="protect-vms-deployed-on-azure-stack-hub"></a>Protéger des machines virtuelles déployées sur Azure Stack Hub

Utilisez cet article comme guide pour élaborer un plan de protection des machines virtuelles que vos utilisateurs déploient sur Azure Stack Hub.


Pour assurer une protection contre la perte de données et les temps d’arrêt non planifiés, vous devez implémenter un plan de sauvegarde et récupération ou de récupération d’urgence pour les applications utilisateur et leurs données. Si ce plan peut être propre à chaque application, il suit un cadre établi par la stratégie globale de continuité d’activité et de récupération d’urgence de votre organisation. [Azure Stack Hub : Considérations relatives à la continuité et la reprise d’activité](https://aka.ms/azurestackbcdrconsiderationswp) est un bon point de départ.

## <a name="azure-stack-hub-infrastructure-recovery"></a>Récupération d’infrastructure Azure Stack Hub

Les utilisateurs sont responsables de la protection de leurs machines virtuelles, de manière distincte des services d’infrastructure Azure Stack Hub.

Le plan de récupération des services d’infrastructure Azure Stack Hub n’inclut **pas** la récupération des machines virtuelles utilisateur, des comptes de stockage ou des bases de données. En tant que propriétaire de l’application, vous êtes responsable de l’implémentation d’un plan de récupération pour vos applications et données.

Au cas où le cloud Azure Stack Hub resterait hors connexion pendant une durée prolongée ou serait définitivement irrécupérable, vous devez disposer d’un plan de récupération qui :

* assure un temps d’arrêt minimal ;
* maintient les machines virtuelles critiques, notamment les serveurs de base de données, en cours d’exécution ;
* permet aux applications de continuer à répondre aux demandes utilisateur.

L’opérateur du cloud Azure Stack Hub est chargé de créer un plan de récupération pour les services et l’infrastructure Azure Stack Hub sous-jacents. Pour en savoir plus, voir [Récupérer des données suites à une perte catastrophique](../operator/azure-stack-backup-recover-data.md).

## <a name="considerations-for-iaas-vms"></a>Considérations relatives aux machines virtuelles IaaS
Le système d’exploitation installé dans la machine virtuelle IaaS limitera les produits que vous pouvez utiliser pour protéger les données qu’elle contient. Pour les machines virtuelles IaaS basées sur Windows, vous pouvez utiliser les produits Microsoft et partenaires pour protéger les données. Pour les machines virtuelles IaaS basées sur Linux, la seule option consiste à utiliser les produits partenaires. Reportez-vous à [cette feuille de données pour connaître tous les partenaires de continuité d’activité et de récupération d’urgence proposant des produits validés pour Azure Stack Hub](https://aka.ms/azurestackbcdrpartners).

## <a name="sourcetarget-combinations"></a>Combinaisons source/cible

Chaque cloud Azure Stack Hub est déployé dans un seul centre de données. Un environnement distinct est requis afin que vous puissiez récupérer vos applications. L’environnement de récupération peut être un autre cloud Azure Stack Hub dans un autre centre de données ou sur le cloud public Azure. Vos exigences en matière de confidentialité et de souveraineté des données déterminent l’environnement de récupération pour votre application. Lorsque vous activez la protection pour chaque application, vous avez la possibilité de choisir une option de récupération spécifique pour chacune d’elles. Vous pouvez avoir des applications dans un seul abonnement pour sauvegarder des données dans un autre centre de données. Dans un autre abonnement, vous pouvez répliquer des données dans le cloud public Azure.

Planifiez votre stratégie de récupération d’urgence et de sauvegarde récupération pour chaque application afin de déterminer la cible pour chaque application. Un plan de récupération permet à l’organisation de dimensionner correctement la capacité de stockage requise localement et de prévoir la consommation dans le cloud public.

|  | Azure global | Azure Stack Hub déployé dans un centre de données de fournisseur de services cloud et géré par un fournisseur de services cloud | Azure Stack Hub déployé dans un centre de données de client et géré par un client |
|------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------|
| **Azure Stack Hub déployé dans un centre de données de fournisseur de services cloud et géré par un fournisseur de services cloud** | Les machines virtuelles utilisateur sont déployées sur le compte Azure Stack Hub géré par le fournisseur de services cloud.<br><br>Les machines virtuelles utilisateur sont restaurées à partir de la sauvegarde ou basculées directement sur Azure. | Le fournisseur de services cloud gère les instances principales et secondaires d’Azure Stack Hub dans ses propres centres de données.<br><br>Les machines virtuelles utilisateur sont restaurées ou basculées entre les deux instances Azure Stack Hub. | Le fournisseur de services cloud gère Azure Stack Hub dans le site principal.<br><br>Le centre de données du client est la cible de la restauration ou du basculement. |
| **Azure Stack Hub déployé dans un centre de données de client et géré par un client** | Les machines virtuelles utilisateur sont déployées sur le compte Azure Stack Hub géré par le client.<br><br>Les machines virtuelles utilisateur sont restaurées à partir de la sauvegarde ou basculées directement sur Azure. | Le client gère Azure Stack Hub dans le site principal.<br><br>Le centre de données du fournisseur de services cloud est la cible de la restauration ou du basculement. | Le client gère les instances principales et secondaires d’Azure Stack Hub dans ses propres centres de données.<br><br>Les machines virtuelles utilisateur sont restaurées ou basculées entre les deux instances Azure Stack Hub. |

![Combinaisons source/cible](media/azure-stack-manage-vm-backup/vm_backupdataflow_01.png)

## <a name="app-recovery-objectives"></a>Objectifs de récupération d’application

Déterminez le temps d’arrêt et la perte de données tolérables par votre organisation pour chaque application. En les quantifiant, vous pourrez créer un plan de récupération qui réduit l’impact d’une panne sur votre organisation. Pour chaque application, tenez compte des éléments suivants :

 - **Objectif de délai de récupération (RTO)**  
Le RTO est la durée maximale acceptable pendant laquelle une application peut être indisponible après un incident. Par exemple, si votre objectif RTO est de 90 minutes, vous devez être en mesure de remettre l’application en état de fonctionnement dans les 90 minutes suivant le début de la panne. Si vous avez un RTO faible, vous pouvez conserver un deuxième déploiement fonctionnant de manière continue en veille, pour vous protéger contre une panne régionale.
 - **Objectif de point de récupération (RPO)**  
Le RPO est la durée maximale de perte de données acceptable pendant une panne. Par exemple, si vous stockez des données dans une base de données unique qui est sauvegardée toutes les heures sans aucune réplication vers d’autres bases de données, vous risquez de perdre jusqu’à une heure de données.

Les objectifs RTO et RPO sont des exigences de l’entreprise. Procédez à une évaluation des risques pour définir les RTO et RPO de l’application.

Il existe une autre métrique, le **temps moyen de récupération** (MTTR), qui correspond à la durée moyenne nécessaire à la restauration de l’application après une défaillance. Il s’agit d’une valeur empirique pour le système. Si la métrique MTTR dépasse celle de l’objectif RTO, une défaillance du système entraînera une interruption inacceptable des opérations, car il ne sera pas possible de restaurer le système au sein de l’objectif RTO défini.

### <a name="backup-restore"></a>Sauvegarde-restauration

Le schéma de protection le plus courant pour les applications basées sur une machine virtuelle consiste à utiliser un logiciel de sauvegarde. En général, la sauvegarde d’une machine virtuelle inclut le système d’exploitation, la configuration du système d’exploitation, les fichiers binaires d’application et les données d’application. Les sauvegardes sont créées en prenant un cliché instantané des volumes, des disques ou de la machine virtuelle entière. Avec Azure Stack Hub, vous avez la possibilité de sauvegarder à partir du contexte du système d’exploitation invité ou à partir du stockage Azure Stack Hub et des API de calcul. Azure Stack Hub ne prend pas en charge les sauvegardes au niveau de l’hyperviseur.
 
![Sauvegarde-restauration](media/azure-stack-manage-vm-backup/vm_backupdataflow_03.png)

La récupération de l’application nécessite la restauration d’une ou de plusieurs machines virtuelles dans le même cloud ou un nouveau cloud. Vous pouvez cibler un cloud dans votre centre de données ou le cloud public. Le choix du cloud n’appartient qu’à vous et dépend de vos exigences de souveraineté et de confidentialité des données.

 - RTO : temps d’arrêt mesuré en heures
 - RPO : perte de données variable (selon la fréquence de sauvegarde)
 - Topologie du déploiement : actif/passif

#### <a name="planning-your-backup-strategy"></a>Planification de votre stratégie de sauvegarde

La planification de votre stratégie de sauvegarde et la définition des exigences de mise à l’échelle commencent avec la quantification du nombre d’instances de machine virtuelle qui doivent être protégées. La stratégie qui consiste à sauvegarder toutes les machines virtuelles sur tous les serveurs de l’environnement est très courante. Toutefois, avec Azure Stack Hub, certaines machines virtuelles doivent être sauvegardées. Par exemple, les machines virtuelles dans un groupe identique sont considérées comme des ressources éphémères qui peuvent apparaître et disparaître, sans que vous soyez prévenu. Toutes les données durables qui doivent être protégées sont stockées dans un référentiel distinct, comme une base de données ou un magasin d’objets.

Considérations importantes pour la sauvegarde de machines virtuelles sur Azure Stack Hub :

 - **Regroupement en catégories**
    - Imaginez un modèle où les utilisateurs choisissent la sauvegarde de machine virtuelle.
    - Définissez un contrat de niveau de service (SLA) de récupération en fonction de la priorité des applications ou de l’impact sur l’activité.
 - **Mettre à l'échelle**
    - Tenez compte des sauvegardes échelonnées lorsque vous intégrez un grand nombre de nouvelles machines virtuelles (si la sauvegarde est requise).
    - Évaluez les produits de sauvegarde qui peuvent capturer et transmettre des données de sauvegarde de manière efficace pour réduire le contenu de la ressource sur la solution.
    - Évaluez les produits de sauvegarde qui stockent de manière efficace les données de sauvegarde à l’aide de sauvegardes incrémentielles ou différentielles pour réduire le besoin de sauvegardes complètes sur toutes les machines virtuelles de l’environnement.
 - **Restauration**
    - Les produits de sauvegarde peuvent restaurer des disques virtuels, des données d’application dans une machine virtuelle existante, ou la ressource de machine virtuelle entière et les disques virtuels associés. Le schéma de restauration dont vous avez besoin dépend de la façon dont vous envisagez de restaurer l’application. Par exemple, il peut être plus facile de redéployer un serveur SQL à partir d’un modèle, puis de restaurer les bases de données au lieu de restaurer la machine virtuelle entière ou un ensemble de machines virtuelles.

### <a name="replicationmanual-failover"></a>Réplication/basculement manuel

Pour prendre en charge la haute disponibilité, il existe une autre approche, qui consiste à répliquer les machines virtuelles de votre application dans un autre cloud et à opter pour un basculement manuel. La réplication du système d’exploitation, des fichiers binaires d’application et des données d’application peut être effectuée au niveau de la machine virtuelle ou du système d’exploitation invité. Le basculement est géré à l’aide de logiciels supplémentaires qui ne font pas partie de l’application.

Avec cette approche, l’application est déployée dans un cloud et sa machine virtuelle est répliquée dans l’autre. Lorsqu’un basculement est déclenché, les machines virtuelles secondaires doivent être démarrées dans le second cloud. Dans certains cas, le basculement crée les machines virtuelles et y attache des disques. Ce processus peut prendre un certain temps, surtout avec une application à plusieurs niveaux exigeant une séquence de démarrage particulière. Il peut être nécessaire de passer par d’autres étapes pour que l’application soit prête à traiter les requêtes.

![Réplication-basculement manuel](media/azure-stack-manage-vm-backup/vm_backupdataflow_02.png)

 - RTO : temps d’arrêt mesuré en minutes
 - RPO : perte de données variable (selon la fréquence de réplication)
 - Topologie du déploiement : en veille actif/passif
 
### <a name="high-availabilityautomatic-failover"></a>Haute disponibilité/basculement automatique

Pour les applications pour lesquelles votre activité peut tolérer quelques secondes ou minutes de temps d’arrêt et une perte de données minimale, vous devez envisager une configuration de haute disponibilité. Les applications de haute disponibilité sont conçues pour récupérer rapidement et automatiquement après les pannes. Pour les pannes matérielles locales, l’infrastructure Azure Stack Hub implémente la haute disponibilité dans le réseau physique à l’aide de deux commutateurs TOR (Top of Rack). Pour les pannes au niveau du calcul, Azure Stack Hub utilise plusieurs nœuds dans une unité d’échelle. Au niveau de la machine virtuelle, vous pouvez utiliser des groupes identiques en combinaison avec des domaines d’erreur pour vous assurer que les pannes de nœud n’interrompent pas votre application.

En combinaison avec des groupes identiques, votre application doit prendre en charge la haute disponibilité en mode natif ou l’utilisation du logiciel de clustering. Par exemple, Microsoft SQL Server prend en charge la haute disponibilité en mode natif pour les bases de données à l’aide du mode de validation synchrone. Toutefois, si vous pouvez uniquement prendre en charge la réplication asynchrone, quelques données seront perdues. Les applications peuvent également être déployées dans un cluster de basculement où le logiciel de clustering gère le basculement automatique de l’application.

Avec cette approche, l’application n’est active que dans un cloud, mais le logiciel est déployé dans plusieurs clouds. Les autres clouds sont en mode veille, prêts à démarrer l’application lorsque le basculement est déclenché.

 - RTO : temps d’arrêt mesuré en secondes
 - RPO : perte de données minimale
 - Topologie du déploiement : en veille actif/actif

### <a name="fault-tolerance"></a>Tolérance de panne

La disponibilité du service d’infrastructure et la redondance physique d’Azure Stack Hub protègent uniquement contre les pannes/défaillances matérielles liées, par exemple, à un disque, à l’alimentation, à un port réseau ou à un nœud. Toutefois, si votre application doit rester toujours disponible et sans jamais perdre de données, il vous faudra implémenter la tolérance de panne en natif dans votre application ou utiliser des logiciels supplémentaires pour activer la tolérance de panne.

Tout d’abord, vous devez vous assurer que les machines virtuelles de l’application sont déployées à l’aide de groupes identiques pour les protéger des pannes au niveau du nœud. Pour vous protéger contre la mise hors connexion du cloud, la même application doit déjà être déployée dans un autre cloud, afin qu’elle puisse continuer à traiter les requêtes sans interruption. Ce modèle est généralement appelé déploiement actif-actif.

Gardez à l’esprit que chaque cloud Azure Stack Hub est indépendant des autres ; les clouds sont donc toujours considérés comme actifs du point de vue de l’infrastructure. Dans ce cas, plusieurs instances actives de l’application sont déployées dans un ou plusieurs clouds actifs.

 - RTO : aucun temps d’arrêt
 - RPO : Aucune perte de données
 - Topologie du déploiement : actif/actif

### <a name="no-recovery"></a>Aucune récupération

Certaines applications de votre environnement peuvent ne pas avoir besoin d’être protégées contre des temps d’arrêt non planifiés ou une perte de données. Par exemple, les machines virtuelles utilisées pour le développement et le test ne doivent généralement pas être récupérées. C’est à vous de décider si vous souhaitez protéger une application ou une machine virtuelle spécifique. Azure Stack Hub n’offre pas de sauvegarde ou réplication des machines virtuelles à partir de l’infrastructure sous-jacente. Comme dans Azure, vous devez choisir la protection de chaque machine virtuelle dans chacun de vos abonnements.

 - RTO : irrécupérable
 - RPO : perte complète des données

## <a name="recommended-topologies"></a>Topologies recommandées

Considérations importantes pour votre déploiement Azure Stack Hub :

|     | Recommandation | Commentaires |
|-------------------------------------------------------------------------------------------------|-----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Sauvegarder/restaurer des machines virtuelles vers une cible de sauvegarde externe déjà déployée dans votre centre de données | Recommandé | Tirez parti de l’infrastructure de sauvegarde existante et des capacités opérationnelles. Veillez à dimensionner l’infrastructure de sauvegarde pour qu’elle soit prête à protéger les instances de machine virtuelle supplémentaires. Assurez-vous que l’infrastructure de sauvegarde ne se trouve pas juste à côté de votre source. Vous pouvez restaurer des machines virtuelles du compte Azure Stack Hub source dans une instance secondaire Azure Stack Hub ou Azure. |
| Sauvegarder/restaurer des machines virtuelles vers une cible de sauvegarde externe dédiée à Azure Stack Hub | Recommandé | Vous pouvez acheter une nouvelle infrastructure de sauvegarde ou une infrastructure de sauvegarde dédiée à l’approvisionnement pour Azure Stack Hub. Assurez-vous que l’infrastructure de sauvegarde ne se trouve pas juste à côté de votre source. Vous pouvez restaurer des machines virtuelles du compte Azure Stack Hub source dans une instance secondaire Azure Stack Hub ou Azure. |
| Sauvegarder/restaurer des machines virtuelles directement vers Azure global ou un fournisseur de services fiable | Recommandé | Tant que vous pouvez satisfaire à vos exigences en matière de réglementation et de confidentialité des données, vous pouvez stocker vos sauvegardes dans Azure global ou un fournisseur de services fiable. Dans l’idéal, le fournisseur de services exécute également Azure Stack Hub pour que votre expérience opérationnelle soit cohérente lorsque vous effectuez une restauration. |
| Répliquer/basculer des machines virtuelles vers une instance Azure Stack Hub distincte | Recommandé | Dans le cas d’un basculement, vous devez avoir un deuxième cloud Azure Stack Hub totalement opérationnel afin d’éviter des temps d’arrêt prolongés. |
| Répliquer/basculer des machines virtuelles directement vers Azure ou un fournisseur de services fiable | Recommandé | Tant que vous pouvez satisfaire à vos exigences obligatoires et en matière de confidentialité des données, vous pouvez répliquer vos données dans Azure global ou un fournisseur de services fiable. Dans l’idéal, le fournisseur de services exécute également Azure Stack Hub pour que votre expérience opérationnelle soit cohérente après un basculement. |
| Déployer la cible de sauvegarde sur le même cloud Azure Stack Hub avec vos données d’application | Non recommandé | Évitez de stocker des sauvegardes dans le même cloud Azure Stack Hub. Un temps d’arrêt non planifié du cloud peut vous priver de vos données principales et données de sauvegarde. Si vous choisissez de déployer une cible de sauvegarde en tant qu’appliance virtuelle (à des fins d’optimisation de la sauvegarde et de la restauration), vous devez vous assurer que toutes les données sont copiées en continu dans un emplacement de sauvegarde externe. |
| Déployer l’appliance de sauvegarde physique dans le même rack que celui où la solution Azure Stack Hub est installée | Non pris en charge | Vous ne pouvez actuellement pas connecter d’autres appareils à des commutateurs TOR qui ne font pas partie de la solution d’origine. |

## <a name="next-steps"></a>Étapes suivantes

Cet article vous a fourni des recommandations générales sur la protection des machines virtuelles utilisateur déployées sur Azure Stack Hub. Pour plus d’informations sur l’utilisation des services Azure pour protéger les machines virtuelles des utilisateurs, voir :

- [Considérations relatives à la continuité d’activité et à la reprise d’activité](https://aka.ms/azurestackbcdrconsiderationswp)

### <a name="azure-backup-server"></a>Azure Backup Server
 - [Utiliser le service Sauvegarde Azure pour sauvegarder des fichiers et applications sur Azure Stack Hub](https://docs.microsoft.com/azure/backup/backup-mabs-files-applications-azure-stack)
 - [Prise en charge du serveur de sauvegarde Azure pour Azure Stack Hub](https://docs.microsoft.com/azure/backup/ ) 
 
 ### <a name="azure-site-recovery"></a>Azure Site Recovery
 - [Prise en charge d’Azure Site Recovery pour Azure Stack Hub](https://docs.microsoft.com/azure/site-recovery/)  
 
 ### <a name="partner-products"></a>Produits de partenaires
 - [Feuille de données de l’écosystème des partenaires d’intégration de centre de données Azure Stack Hub](https://aka.ms/azurestackbcdrpartners)

Pour en savoir plus sur les produits de partenaires qui offrent une protection de machine virtuelle sur Azure Stack Hub, voir [Protéger d’applications et de données sur Azure Stack Hub](https://azure.microsoft.com/blog/protecting-applications-and-data-on-azure-stack/).
