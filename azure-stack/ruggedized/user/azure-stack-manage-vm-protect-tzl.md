---
title: Protéger des machines virtuelles déployées sur Azure Stack | Microsoft Docs
description: Découvrez comment créer un plan de récupération pour protéger les machines virtuelles déployées sur Azure Stack contre la perte de données et les temps d’arrêt non planifiés.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/16/2021
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 3/19/2018
ms.openlocfilehash: a31b5b4498911b008ec471beca24905466efee4b
ms.sourcegitcommit: 34babe5abf1bceee718011b5c5c25f75e1b03b0c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100562960"
---
# <a name="protect-vms-deployed-on-azure-stack-hub---ruggedized"></a>Protéger des machines virtuelles déployées sur Azure Stack Hub – Renforcé

Utilisez cet article comme guide pour développer un plan de protection des machines virtuelles que vos utilisateurs déploient sur Azure Stack Hub.

Pour assurer une protection contre la perte de données et les temps d’arrêt non planifiés, implémentez un plan de protection des données et de récupération d’urgence pour vos applications basées sur des machines virtuelles sur Azure Stack Hub. Le plan de protection implémenté dépend des besoins de l’entreprise et de la conception de l’application. Ce plan doit suivre un cadre établi par la stratégie globale de continuité d’activité et de récupération d’urgence de votre organisation. Pour une vue d’ensemble des considérations en matière de continuité et de reprise d’activité relatives à Azure Stack Hub, consultez [Azure Stack : Considérations relatives à la continuité et la reprise d’activité](https://azure.microsoft.com/resources/azure-stack-considerations-for-business-continuity-and-disaster-recovery/) est un bon point de départ.

## <a name="application-recovery-objectives"></a>Objectifs de récupération d’application

Déterminez le temps d’arrêt et la perte de données tolérables par votre organisation pour chaque application. En les quantifiant, vous pourrez créer un plan de récupération qui réduit l’impact d’une panne sur votre organisation. Pour chaque application, prenez en compte ce qui suit :

- **Objectif de délai de récupération (RTO)** \
    Le RTO est la durée maximale acceptable pendant laquelle une application peut être indisponible après un incident. Par exemple, si votre objectif RTO est de 90 minutes, vous devez être en mesure de remettre l’application en état de fonctionnement dans les 90 minutes suivant le début de la panne. Si vous avez un RTO faible, vous pouvez conserver un deuxième déploiement fonctionnant de manière continue en veille, pour vous protéger contre une panne régionale.

- **Objectif de point de récupération (RPO)** \
    Le RPO est la durée maximale de perte de données acceptable pendant une panne. Par exemple, si vous stockez des données dans une base de données unique qui est sauvegardée toutes les heures sans aucune réplication vers d’autres bases de données, vous risquez de perdre jusqu’à une heure de données.

Procédez à une évaluation pour définir les objectifs de délai et de point de récupération de chaque application.

Il existe une autre métrique importante, le **temps moyen de récupération** (MTTR), qui correspond à la durée moyenne nécessaire à la restauration de l’application après une défaillance. Il s’agit d’une valeur empirique pour le système. Si la métrique MTTR dépasse celle de l’objectif RTO, une défaillance du système entraînera une interruption inacceptable des opérations, car il ne sera pas possible de restaurer le système au sein de l’objectif RTO défini.

## <a name="protection-options-for-iaas-vms"></a>Options de protection des machines virtuelles IaaS

### <a name="backup-restore"></a>Sauvegarde-restauration

Le schéma de protection le plus courant pour les applications basées sur une machine virtuelle consiste à utiliser un logiciel de sauvegarde. En général, la sauvegarde d’une machine virtuelle inclut le système d’exploitation, la configuration du système d’exploitation, les fichiers binaires d’application et les données persistantes d’application à l’intérieur de la machine virtuelle. Dans le système d’exploitation invité, les sauvegardes sont créées à l’aide d’un agent permettant de capturer l’application, le système d’exploitation ou le système de fichiers/volumes. Une autre approche sans agent consiste à recourir à une intégration avec les API Azure Stack Hub pour lire les informations relatives à la configuration de la machine virtuelle et effectuer un instantané des disques attachés à cette machine virtuelle. Notez qu’Azure Stack Hub ne prend pas en charge la sauvegarde directement depuis l’hyperviseur.

### <a name="planning-your-backup-strategy"></a>Planification de votre stratégie de sauvegarde

La planification de votre stratégie de sauvegarde et la définition des exigences de mise à l’échelle commencent avec la quantification du nombre d’instances de machine virtuelle qui doivent être protégées. La sauvegarde de toutes les machines virtuelles du système peut ne pas être la solution la plus efficace pour protéger l’application. Avec Azure Stack Hub, les machines virtuelles d’un groupe identique ou d’un groupe à haute disponibilité ne doivent pas être sauvegardées au niveau des machines virtuelles. Ces machines virtuelles sont considérées comme éphémères, car le groupe de machines virtuelles peut faire l’objet d’un scale-in ou d’un scale-out. Idéalement, toutes les données durables devant être conservées se trouvent dans un référentiel distinct, comme une base de données ou un magasin d’objets. Si les applications déployées dans une architecture de scale-out contiennent des données devant être conservées et protégées, une sauvegarde au niveau de l’application s’impose à l’aide des fonctionnalités natives fournies par l’application ou d’un agent.

Considérations importantes à prendre en compte pour la sauvegarde de machines virtuelles sur Azure Stack :

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

Une autre approche de prise en charge de la récupération consiste à répliquer les données dans un autre environnement. Les données peuvent être étendues à l’application, comme la réplication de base de données, au système d’exploitation dans le système d’exploitation invité à l’aide d’un agent, ou au niveau de la machine virtuelle par intégration avec les API Azure Stack Hub. En cas de sinistre, un basculement vers l’emplacement secondaire est nécessaire. Le basculement peut se faire en mode natif par l’application comme avec les groupes de disponibilité SQL, au niveau du système d’exploitation invité à l’aide d’agents ou d’une technologie de cluster, ou au niveau de la machine virtuelle à l’aide d’un produit de protection.

### <a name="high-availabilityautomatic-failover"></a>Haute disponibilité/basculement automatique

Les applications qui prennent en charge la haute disponibilité en mode natif ou s’appuient sur un logiciel de cluster à des fins de haute disponibilité entre les nœuds peuvent être déployées sur un groupe de machines virtuelles dans une ou plusieurs instances Azure Stack Hub. Dans tous les cas, un certain niveau d’équilibrage de charge s’impose pour veiller à ce que le trafic des applications soit correctement acheminé. Dans cette configuration, l’application peut automatiquement récupérer après les pannes. Pour les pannes matérielles locales, l’infrastructure Azure Stack Hub implémente la haute disponibilité et la tolérance de panne dans l’infrastructure physique. Pour les pannes au niveau du calcul, Azure Stack Hub utilise plusieurs nœuds dans une unité d’échelle au sein d’une configuration N-1. Au niveau de la machine virtuelle, les groupes identiques et les groupes à haute disponibilité modélisent chaque nœud de l’unité d’échelle en tant que domaine d’erreur afin de garantir l’anti-affinité au niveau du nœud, de sorte que les échecs de nœud n’affectent pas une application distribuée.

### <a name="no-protection"></a>Absence de protection

Certaines applications peuvent ne pas présenter de données devant être conservées. Par exemple, les machines virtuelles utilisées pour le développement et le test ne doivent généralement pas être récupérées. Une application sans état qui peut être redéployée à partir d’un pipeline CI/CD en cas d’échec constitue un autre exemple. Il est important d’identifier les applications ne nécessitant pas de protection afin d’éviter les machines virtuelles inutilement protégées.

<!-- ## Recommended topologies

Important considerations for your Azure Stack deployment: -->

## <a name="next-steps"></a>Étapes suivantes

Cet article vous a donné des recommandations générales sur la protection des machines virtuelles utilisateur déployées sur Azure Stack. Pour plus d’informations sur l’utilisation des services Azure pour protéger les machines virtuelles des utilisateurs, voir :

- [Considérations relatives à la continuité d’activité et à la reprise d’activité](https://azure.microsoft.com/resources/azure-stack-considerations-for-business-continuity-and-disaster-recovery/)

### <a name="partner-products"></a>Produits de partenaires

- [Feuille de données de l’écosystème des partenaires d’intégration de centre de données Azure Stack](https://azure.microsoft.com/resources/azure-stack-datacenter-integration-partners/)
- Pour en savoir plus sur les produits de partenaires qui offrent une protection de machine virtuelle sur Azure Stack, consultez [Protéger des applications et des données sur Azure Stack](https://azure.microsoft.com/blog/protecting-applications-and-data-on-azure-stack/).
