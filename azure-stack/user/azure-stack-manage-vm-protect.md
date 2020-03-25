---
title: Protéger des machines virtuelles déployées sur Azure Stack Hub
description: Découvrez comment créer un plan de récupération pour protéger des machines virtuelles déployées sur Azure Stack Hub contre la perte de données et les temps d’arrêt non planifiés.
author: mattbriggs
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: mabrigg
ms.reviewer: hectorl
ms.lastreviewed: 3/5/2020
ms.openlocfilehash: 913d0eeed1ba2cfce0b062385a4f544919889f43
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2020
ms.locfileid: "79512607"
---
# <a name="protect-vms-deployed-on-azure-stack-hub"></a>Protéger des machines virtuelles déployées sur Azure Stack Hub

Utilisez cet article comme guide pour réussir à développer une stratégie de protection des données et de reprise d’activité pour les machines virtuelles IaaS déployées par l’utilisateur sur Azure Stack Hub.

Pour assurer une protection contre la perte de données et les temps d’arrêt prolongés, implémentez un plan de sauvegarde et récupération ou de reprise d’activité pour les applications utilisateur et leurs données. Chaque application doit être évaluée dans le cadre de la stratégie complète de continuité d’activité et reprise d’activité (BCDR) de votre organisation. [Azure Stack Hub : Considérations relatives à la continuité et la reprise d’activité](https://aka.ms/azurestackbcdrconsiderationswp) est un bon point de départ.

## <a name="considerations-for-protecting-iaas-vms"></a>Considérations relatives à la protection des machines virtuelles IaaS

### <a name="roles-and-responsibilities"></a>Les rôles et responsabilités

Tout d’abord, veillez à bien comprendre les rôles et responsabilités attribués aux propriétaires et aux opérateurs des applications dans le contexte de la protection et de la récupération.

Les utilisateurs sont responsables de la protection des machines virtuelles. Les opérateurs sont responsables du maintien en ligne et de l’intégrité d’Azure Stack Hub. Azure Stack Hub inclut un service qui sauvegarde les données de services internes à partir de services d’infrastructure mais n’inclut **aucune** donnée utilisateur, comme les machines virtuelles créées par l’utilisateur, les comptes de stockage et leurs données utilisateur ou d’application, ou les bases de données utilisateur.


| Propriétaire/Architecte d’application   | Opérateur Azure Stack Hub  |
|---    |---    |
| <ul><li>Alignez l’architecture des applications sur les principes de conception cloud.</li></br><li>Moderniser les applications traditionnelles en fonction des besoins, afin de les préparer à l’environnement cloud.</li></br><li>Définir l’objectif de délai de récupération (RTO) et l’objectif de point de récupération (RPO) acceptables pour l’application.</li></br><li>Identifier les ressources d’application et les référentiels de données qui ont besoin d’être protégés.</li></br><li>Implémenter une méthode de récupération des données et des applications qui correspond au mieux à l’architecture de l’application et aux exigences du client.</li></ul>     | <ul><li>Identifier les objectifs de continuité d’activité et de reprise d’activité de l’organisation.</li></br><li>Déployer suffisamment d’instances Azure Stack Hub pour atteindre les objectifs de continuité d’activité et de reprise d’activité de l’organisation.</li></br><li>Concevoir et utiliser l’infrastructure de protection des données/applications.</li></br><li>Fournir des solutions gérées ou un accès en libre-service aux services de protection.</li></br><li>Travailler avec les propriétaires et architectes d’applications pour comprendre la conception des applications et recommander des stratégies de protection.</li></br><li>Permettre la sauvegarde de l’infrastructure à des fins de réparation de service et de récupération cloud.</li></ul>    |

## <a name="sourcetarget-combinations"></a>Combinaisons source/cible

Les utilisateurs qui doivent se protéger contre une panne de centre de donnes ou de site peuvent utiliser une autre infrastructure Azure Stack Hub ou Azure pour assurer une haute disponibilité ou une récupération rapide. Avec un emplacement principal et un emplacement secondaire, les utilisateurs peuvent déployer des applications dans une configuration active/active ou active/passive dans deux environnements. Pour les charges de travail moins critiques, les utilisateurs peuvent utiliser la capacité de l’emplacement secondaire pour effectuer une restauration à la demande des applications à partir d’une sauvegarde.

Un ou plusieurs clouds Azure Stack Hub peuvent être déployés dans un centre de données. Pour survivre à un incident catastrophique, le déploiement d’au moins un cloud Azure Stack Hub dans un centre de données différent garantit la possibilité de basculer des charges de travail et de réduire les temps d’arrêt non planifiés. Si vous n’avez qu’un seul cloud Azure Stack Hub, vous devez envisager d’utiliser le cloud public Azure comme cloud de récupération. L’emplacement d’exécution de votre application est déterminé par la législation gouvernementale, les stratégies d’entreprise et les exigences de latence strictes. Vous avez la possibilité de déterminer l’emplacement de récupération approprié par application. Par exemple, vous pouvez faire en sorte que les applications d’un même abonnement sauvegardent les données dans un autre centre de données et dans un autre abonnement, en répliquant les données dans le cloud public Azure.

## <a name="application-recovery-objectives"></a>Objectifs de récupération d’application

Les propriétaires d’applications sont principalement responsables de la détermination de la quantité de temps d’arrêt et de perte de données que l’application et l’organisation peuvent tolérer. En quantifiant les temps d’arrêt et les pertes de données acceptables, vous pouvez créer un plan de récupération qui réduit l’impact d’un sinistre sur votre organisation. Pour chaque application, tenez compte des éléments suivants :

 - **Objectif de délai de récupération (RTO)**  
Le RTO est la durée maximale acceptable pendant laquelle une application peut être indisponible après un incident. Par exemple, si votre objectif RTO est de 90 minutes, vous devez être en mesure de remettre l’application en état de fonctionnement dans les 90 minutes suivant le début de la panne. Si vous avez un RTO faible, vous pouvez conserver un deuxième déploiement fonctionnant de manière continue en veille, pour vous protéger contre une panne régionale.
 - **Objectif de point de récupération (RPO)**  
Le RPO est la durée maximale de perte de données acceptable pendant une panne. Par exemple, si vous stockez des données dans une base de données unique qui est sauvegardée toutes les heures sans aucune réplication vers d’autres bases de données, vous risquez de perdre jusqu’à une heure de données.

Il existe une autre métrique, le *temps moyen de récupération* (MTTR), qui correspond à la durée moyenne nécessaire à la restauration de l’application après une défaillance. Il s’agit d’une valeur empirique pour le système. Si la métrique MTTR dépasse celle de l’objectif RTO, une défaillance du système entraîne une interruption inacceptable des opérations, car il n’est pas possible de restaurer le système au sein de l’objectif RTO défini.

## <a name="protection-options"></a>Options de protection 

### <a name="backup-restore"></a>Sauvegarde-restauration

La sauvegarde de vos applications et jeux de données vous permet de récupérer rapidement après un temps d’arrêt dû à une altération des données, à des suppressions accidentelles ou à des sinistres. Pour les applications basées sur des machines virtuelles IaaS, vous pouvez utiliser un agent dans l’invité pour protéger les données d’application, la configuration du système d’exploitation et les données stockées sur les volumes. 

#### <a name="backup-using-in-guest-agent"></a>Sauvegarder à l’aide d’un agent dans l’invité

La sauvegarde d’une machine virtuelle à l’aide d’un agent de système d’exploitation invité inclut généralement la capture de la configuration du système d’exploitation, des fichiers/dossiers, des disques, des binaires d’application ou des données d’application. 

La récupération d’une application à partir d’un agent nécessite de recréer manuellement la machine virtuelle, d’installer le système d’exploitation et d’installer l’agent invité. À ce stade, les données peuvent être restaurées dans le système d’exploitation invité ou directement dans l’application.

#### <a name="backup-using-disk-snapshot-for-stopped-vms"></a>Sauvegarde à l’aide d’un instantané de disque pour les machines virtuelles arrêtées

Certains produits de sauvegarde peuvent protéger la configuration des machines virtuelles IaaS et les disques attachés à une machine virtuelle arrêtée. Ils s’intègrent aux API Azure Stack Hub pour capturer la configuration des machines virtuelles et créer des instantanés de disque. Si un temps d’arrêt planifié est possible pour l’application, vérifiez que la machine virtuelle est arrêtée avant de démarrer le workflow de sauvegarde.  

#### <a name="backup-using-disk-snapshot-snapshot-for-running-vms"></a>Sauvegarde à l’aide d’un instantané de disque les machines virtuelles en cours d’exécution

> [!Important]  
> L’utilisation d’instantanés de disque n’est actuellement pas prise en charge pour les machines virtuelles en cours d’exécution. La création d’un instantané d’un disque attaché à une machine virtuelle en cours d’exécution peut dégrader les performances ou avoir un impact sur la disponibilité du système d’exploitation ou de l’application dans la machine virtuelle. Il est recommandé d’utiliser un agent dans l’invité pour protéger l’application si un temps d’arrêt planifié n’est pas possible. 

### <a name="vms-in-a-scale-set-or-availability-group"></a>Machines virtuelles dans un groupe identique ou dans un groupe de disponibilité

Les machines virtuelles incluses dans un groupe identique ou un groupe de disponibilité considérées comme des ressources éphémères ne doivent pas être sauvegardées au niveau de la machine virtuelle, particulièrement si l’application est sans état. Pour les applications avec état déployées dans un groupe identique ou de disponibilité, envisagez de protéger les données d’application (par exemple, une base de données ou un volume dans un pool de stockage). 

### <a name="replicationmanual-failover"></a>Réplication/basculement manuel

Pour les applications qui nécessitent une perte de données minimale ou un temps d’arrêt minimal, la réplication des données peut être activée au niveau de l’application ou du système d’exploitation invité pour répliquer les données à un autre emplacement. Certaines applications, comme Microsoft SQL Server, prennent en charge la réplication de façon native. Si l’application ne prend pas en charge la réplication, vous pouvez utiliser un logiciel dans le système d’exploitation invité pour répliquer des disques ou une solution partenaire qui s’installe en tant qu’agent dans le système d’exploitation invité.

Avec cette approche, l’application est déployée dans un cloud et les données sont répliquées dans l’autre cloud local ou sur Azure. Quand un basculement est déclenché, l’application située dans la cible doit être démarrée et attachée aux données répliquées pour pouvoir commencer à traiter les demandes.
 
### <a name="high-availabilityautomatic-failover"></a>Haute disponibilité/basculement automatique

Pour les applications sans état qui ne peuvent tolérer que quelques secondes ou minutes de temps d’arrêt, envisagez une configuration à haute disponibilité. Les applications à haute disponibilité sont conçues pour être déployées à plusieurs emplacements dans une topologie active/active dans laquelle toutes les instances peuvent traiter des demandes. Pour les pannes matérielles locales, l’infrastructure Azure Stack Hub implémente la haute disponibilité dans le réseau physique à l’aide de deux commutateurs TOR (Top of Rack). Pour les pannes au niveau du calcul, Azure Stack Hub utilise plusieurs nœuds dans une unité d’échelle. Au niveau de la machine virtuelle, vous pouvez utiliser des groupes identiques en combinaison avec des domaines d’erreur pour vous assurer que les pannes de nœud n’interrompent pas votre application. La même application doit alors être déployée sur un emplacement secondaire dans la même configuration. Pour rendre l’application active/active, un équilibreur de charge ou un système DNS peuvent être utilisés pour diriger les demandes vers toutes les instances disponibles.

### <a name="no-recovery"></a>Aucune récupération

Certaines applications de votre environnement peuvent ne pas avoir besoin d’être protégées contre des temps d’arrêt non planifiés ou une perte de données. Par exemple, les machines virtuelles utilisées pour le développement et le test n’ont généralement pas besoin d’être récupérées. C’est à vous de décider si vous souhaitez ou non protéger une application ou un jeu de données spécifique.

## <a name="recommended-topologies"></a>Topologies recommandées

Considérations importantes pour votre déploiement Azure Stack Hub :

|     | Recommandation | Commentaires |
|-------------------------------------------------------------------------------------------------|-----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Sauvegarder/restaurer des machines virtuelles vers une cible de sauvegarde externe déjà déployée dans votre centre de données | Recommandé | Tirez parti de l’infrastructure de sauvegarde existante et des capacités opérationnelles. Veillez à dimensionner l’infrastructure de sauvegarde pour qu’elle soit prête à protéger les instances de machine virtuelle supplémentaires. Assurez-vous que l’infrastructure de sauvegarde ne se trouve pas juste à côté de votre source. Vous pouvez restaurer des machines virtuelles du compte Azure Stack Hub source dans une instance secondaire Azure Stack Hub ou Azure. |
| Sauvegarder/restaurer des machines virtuelles vers une cible de sauvegarde externe dédiée à Azure Stack Hub | Recommandé | Vous pouvez acheter une nouvelle infrastructure de sauvegarde ou une infrastructure de sauvegarde dédiée à l’approvisionnement pour Azure Stack Hub. Assurez-vous que l’infrastructure de sauvegarde ne se trouve pas juste à côté de votre source. Vous pouvez restaurer des machines virtuelles du compte Azure Stack Hub source dans une instance secondaire Azure Stack Hub ou Azure. |
| Sauvegarder/restaurer des machines virtuelles directement vers Azure global ou un fournisseur de services fiable | Recommandé | Tant que vous pouvez satisfaire à vos exigences en matière de réglementation et de confidentialité des données, vous pouvez stocker vos sauvegardes dans Azure global ou un fournisseur de services fiable. Dans l’idéal, le fournisseur de services exécute également Azure Stack Hub pour que votre expérience opérationnelle soit cohérente lorsque vous effectuez une restauration. |
| Répliquer/basculer des machines virtuelles vers une instance Azure Stack Hub distincte | Recommandé | Dans le cas d’un basculement, vous devez avoir un deuxième cloud Azure Stack Hub totalement opérationnel afin d’éviter des temps d’arrêt prolongés. |
| Répliquer/basculer des machines virtuelles directement vers Azure ou un fournisseur de services fiable | Recommandé | Tant que vous pouvez satisfaire à vos exigences obligatoires et en matière de confidentialité des données, vous pouvez répliquer vos données dans Azure global ou un fournisseur de services fiable. Dans l’idéal, le fournisseur de services exécute également Azure Stack Hub pour que votre expérience opérationnelle soit cohérente après un basculement. |
| Déployez une cible de sauvegarde sur le même cloud Azure Stack Hub que celui qui héberge également toutes les applications protégées par la même cible de sauvegarde. | Cible autonome : Non recommandé </br> Cible qui réplique les données de sauvegarde en externe : Recommandé | Si vous choisissez de déployer une appliance de sauvegardes sur Azure Stack Hub (à des fins d’optimisation de la restauration opérationnelle), vous devez vous assurer que toutes les données sont copiées en continu dans un emplacement de sauvegarde externe. |
| Déployer l’appliance de sauvegarde physique dans le même rack que celui où la solution Azure Stack Hub est installée | Non pris en charge | Vous ne pouvez actuellement pas connecter d’autres appareils à des commutateurs TOR qui ne font pas partie de la solution d’origine. |

## <a name="next-steps"></a>Étapes suivantes

Cet article vous a fourni des recommandations générales sur la protection des machines virtuelles utilisateur déployées sur Azure Stack Hub. Pour plus d’informations sur l’utilisation des services Azure pour protéger les machines virtuelles des utilisateurs, voir :

- [Azure Stack IaaS – quatrième partie – Protéger ce que vous avez](https://azure.microsoft.com/blog/azure-stack-iaas-part-four/)
- [Considérations relatives à la continuité d’activité et à la reprise d’activité](https://aka.ms/azurestackbcdrconsiderationswp)

### <a name="azure-backup-server"></a>Azure Backup Server
 - [Utiliser le service Sauvegarde Azure pour sauvegarder des fichiers et applications sur Azure Stack Hub](https://docs.microsoft.com/azure/backup/backup-mabs-files-applications-azure-stack)
 - [Prise en charge du serveur de sauvegarde Azure pour Azure Stack Hub](https://docs.microsoft.com/azure/backup/ ) 
 
 ### <a name="azure-site-recovery"></a>Azure Site Recovery
 - [Prise en charge d’Azure Site Recovery pour Azure Stack Hub](https://docs.microsoft.com/azure/site-recovery/)  
 
 ### <a name="partner-products"></a>Produits de partenaires
 - [Feuille de données de l’écosystème des partenaires d’intégration de centre de données Azure Stack Hub](https://aka.ms/azurestackbcdrpartners)
