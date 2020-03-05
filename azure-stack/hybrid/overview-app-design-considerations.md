---
title: Considérations relatives à la création d’applications hybrides dans Azure et Azure Stack
description: Considérations relatives à la création d’applications hybrides pour le cloud intelligent et la périphérie intelligente.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: adc185b290d0006ed3a32584a7e3d8473ba2aa6c
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77689917"
---
# <a name="hybrid-application-design-considerations"></a>Considérations relatives à la création d’applications hybrides 

Microsoft Azure est le seul cloud hybride cohérent. Il vous permet de réutiliser vos investissements en développement et de disposer d’applications couvrant le cloud Azure mondial, les clouds Azure souverains et Azure Stack, qui est une extension d’Azure dans votre centre de données. Les applications qui s’étendent sur des clouds sont également appelées *applications hybrides*.

Le [*Guide de l’architecture des applications Azure*](https://docs.microsoft.com/azure/architecture/guide) décrit une approche structurée pour concevoir des applications scalables, résilientes et hautement disponibles. Les considérations décrites dans le [*Guide de l’architecture des applications Azure*](https://docs.microsoft.com/azure/architecture/guide) s’appliquent également aux applications conçues pour un seul cloud et aux applications couvrant plusieurs clouds.

Cet article complète les [*piliers de la qualité logicielle*](https://docs.microsoft.com/azure/architecture/guide/pillars) décrits dans le [*Guide de l’architecture*](https://docs.microsoft.com/azure/architecture/guide/) [*des applications Azure*](https://docs.microsoft.com/azure/architecture/guide/), en se concentrant spécifiquement sur la conception d’applications hybrides. De plus, nous ajoutons un pilier de *placement*, car les applications hybrides ne sont pas exclusives à un seul cloud ou à un seul centre de données local.

Les scénarios hybrides varient considérablement avec les ressources qui sont disponibles pour le développement et englobent des considérations telles que la géographie, la sécurité, l’accès à Internet et bien d’autres. Ce guide ne peut certes pas énumérer vos considérations spécifiques, mais il peut fournir des recommandations clés et des bonnes pratiques à suivre. La réussite de la conception, de la configuration, du déploiement et de la maintenance d’une architecture d’application hybride implique de nombreuses considérations de conception qui vous sont probablement inconnues.

Ce document a pour but d’agréger les éventuelles questions qui peuvent survenir durant l’implémentation d’applications hybrides. Il propose des considérations (piliers) et les bonnes pratiques liées à leur utilisation. En répondant à ces questions durant la phase de conception, vous éviterez les problèmes qu’ils peuvent provoquer en production.

Pour l’essentiel, il s’agit de questions que vous devez prendre en compte avant de créer une application hybride. Pour commencer, vous devez :

-   Identifier et évaluer les composants d’application

-   Évaluer les composants d’application par rapport aux piliers

## <a name="evaluate-the-application-components"></a>Évaluer les composants d’application

Chaque composant d’une application a son propre rôle à l’échelle de l’application. Vous devez l’examiner en tenant compte de toutes les considérations relatives à la conception. Les exigences et les fonctionnalités de chaque composant doivent correspondre à ces considérations, ce qui permet de déterminer l’architecture de l’application.

Décomposez votre application en composants, en étudiant son architecture et en déterminant son contenu. Les composants peuvent également inclure d’autres applications avec lesquelles votre application interagit. Au fur et à mesure que vous identifiez les composants, évaluez les opérations hybrides envisagées en fonction de leurs caractéristiques, par exemple :

-   Quelle est la finalité du composant ?

-   Quelles sont les interdépendances entre les composants ?

Par exemple, une application peut avoir deux composants : un composant front-end et un composant back-end. Dans un scénario hybride, le composant front-end se trouve dans un cloud et le composant back-end se trouve dans l’autre. L'application

fournit des canaux de communication non seulement entre le composant front-end et l’utilisateur mais également entre le composant front-end et le composant back-end.

Un composant d’application est défini par de nombreuses formes et de nombreux scénarios. La tâche la plus importante consiste à les identifier, et à identifier leur emplacement dans le cloud ou leur emplacement local.

Les composants d’application usuels à inclure dans votre inventaire sont listés dans le tableau 1.

### <a name="table-1-common-app-components"></a>Tableau 1. Composants d’application usuels


| **Composant** | **Aide relative aux applications hybrides** |
| ---- | ---- |
| Connexions clientes | Votre application (quel que soit l’appareil) est accessible aux utilisateurs de différentes manières, à partir d’un seul point d’entrée, notamment dans les cas suivants :<br>-   Un modèle client-serveur dans lequel l’utilisateur doit installer un client pour le faire fonctionner avec l’application. Une application serveur accessible à partir d’un navigateur web.<br>-   Les connexions clientes peuvent inclure des notifications quand la connexion est interrompue, ou des alertes quand des frais d’itinérance peuvent s’appliquer. |
| Authentication  | Une authentification peut être obligatoire pour un utilisateur qui se connecte à l’application, ou pour un composant qui se connecte à un autre. |
| API  | Vous pouvez fournir aux développeurs un accès programmatique à votre application avec des ensembles d’API et des bibliothèques de classes. De plus, vous pouvez proposer une interface de connexion basée sur les normes Internet. Vous pouvez également utiliser des API pour décomposer une application en unités logiques de fonctionnement indépendantes. |
| Services  | Vous pouvez utiliser des services succincts pour fournir les fonctionnalités d’une application. Un service peut être le moteur sur lequel l’application s’exécute. |
| Files d’attente | Vous pouvez utiliser des files d’attente pour organiser le statut des cycles de vie et des états des composants de votre application. Ces files d’attente peuvent fournir des fonctionnalités de messagerie, de notification et de mise en mémoire tampon aux parties abonnées. |
| Stockage des données | Une application peut être sans état ou avec état. Les applications avec état ont besoin d’un stockage de données pouvant correspondre à de nombreux formats et volumes. |
| Mise en cache des données  | Un composant de mise en cache des données dans votre conception peut résoudre les problèmes de latence de manière stratégique, et jouer un rôle dans le déclenchement du mode rafale du cloud. |
| Ingestion de données | Les données peuvent être envoyées à une application de plusieurs manières : cela va des valeurs envoyées par l’utilisateur dans un formulaire web aux flux de données volumineux et continus. |
| Traitement des données | Vos tâches de traitement de données (par exemple les rapports, les analyses, les exportations par lot et la transformation de données) peuvent être effectuées à la source ou déchargées sur un composant distinct à l’aide d’une copie des données. |

## <a name="assess-app-components-for-pillars"></a>Évaluer les composants d’application par rapport aux piliers

À chaque composant, évaluez ses caractéristiques pour chaque pilier. Au fur et à mesure que vous évaluez chaque composant avec tous les piliers, vous pouvez découvrir des aspects que vous n’avez peut-être pas pris en compte et qui affectent la conception de l’application hybride. Le fait de prendre en compte ces considérations peut apporter une valeur ajoutée à l’optimisation de votre application. Le tableau 2 fournit une description de chaque pilier en ce qui concerne les applications hybrides.

### <a name="table-2-pillars"></a>Tableau 2. Piliers

| **Pilier** | **Description** |
| ----------- | --------------------------------------------------------- |
| Placement  | Positionnement stratégique des composants dans les applications hybrides. |
| Extensibilité  | Capacité d’un système à traiter une charge accrue. |
| Disponibilité  | Durée pendant laquelle une application hybride est fonctionnelle et opérationnelle. |
| Résilience | Capacité de récupération d’une application hybride. |
| Simplicité de gestion | Processus d’opérations assurant l’exécution d’un système en production. |
| Sécurité | Protection des applications hybrides et des données contre les menaces. |

## <a name="placement"></a>Placement

Le placement, par exemple le choix du centre de données, est au cœur des considérations à prendre concernant une application hybride.

Le placement est une tâche importante qui consiste à positionner les composants pour qu’ils puissent servir au mieux une application hybride. Par définition, les applications hybrides couvrent plusieurs emplacements allant du local au cloud et incluant différents clouds. Vous pouvez placer les composants de l’application dans des clouds de deux façons :

-   **Applications hybrides verticales**  
    Les composants d’application sont répartis entre différents emplacements. Chaque composant individuel peut avoir plusieurs instances situées à un seul emplacement.

-   **Applications hybrides horizontales**  
    Les composants d’application sont répartis entre différents emplacements. Chaque composant individuel peut avoir plusieurs instances couvrant plusieurs emplacements.
    
    Certains composants peuvent connaître leur emplacement, tandis que d’autres ignorent leur emplacement et leur placement. Cela est rendu possible grâce à une couche d’abstraction. Cette couche, avec un framework d’application moderne tel que celui des microservices, peut définir la manière dont l’application est servie par le placement des composants d’application fonctionnant sur les nœuds de différents clouds.

### <a name="placement-checklist"></a>Liste de contrôle de placement

**Vérifier les emplacements nécessaires.** Vérifiez que l’application, ou l’un de ses composants, fonctionne dans un cloud spécifique ou nécessite une certification pour ce dernier. Cela peut inclure des exigences de souveraineté de la part de votre entreprise ou dictées par des obligations légales. Déterminez également si des opérations locales sont nécessaires pour un emplacement ou des paramètres régionaux particuliers.

**Déterminer les dépendances de connectivité.** Les emplacements imposés et d’autres facteurs peuvent dicter les dépendances de connectivité entre vos composants. Quand vous placez les composants, déterminez la connectivité et la sécurité optimales pour leur communication entre eux. Les choix possibles sont les suivants : [*VPN*](https://docs.microsoft.com/azure/vpn-gateway/), [*ExpressRoute*](https://docs.microsoft.com/azure/expressroute/) et [*Connexions hybrides*](https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections).

**Évaluer les fonctionnalités de la plateforme.** Pour chaque composant d’application, vérifiez si le fournisseur de ressources nécessaire au composant d’application concerné est disponible dans le cloud, et si la bande passante peut prendre en charge les exigences de débit et de latence attendues.

**Planifier la portabilité.** Utilisez des frameworks d’application modernes, par exemple des conteneurs ou des microservices, pour planifier les opérations de déplacement et éviter les dépendances de services.

**Déterminer les exigences de souveraineté des données.** Les applications hybrides sont conçues pour prendre en charge l’isolement des données, par exemple dans un centre de données local. Passez en revue le placement de vos ressources pour optimiser la réussite de cette exigence.

**Planifier la latence.** Les opérations entre différents cloud peuvent introduire une distance physique entre les composants d’application. Déterminez les exigences nécessaires à la prise en charge de la latence.

**Contrôler les flux de trafic.** Gérez les pics d’utilisation et les communications appropriées et sécurisées pour les données relatives aux informations personnelles identifiables, quand le composant front-end y accède dans un cloud public.

## <a name="scalability"></a>Extensibilité

La scalabilité est la capacité d’un système à gérer une charge accrue sur une application, ce qui peut varier au fil du temps, car d’autres facteurs et d’autres forces affectent la taille de l’audience ainsi que la taille et l’étendue de l’application.

Pour plus d’informations sur ce pilier, consultez [*Scalabilité*](https://docs.microsoft.com/azure/architecture/guide/pillars#scalability) dans la description des piliers de la qualité logicielle.

Une approche basée sur une mise à l’échelle horizontale pour les applications hybrides permet d’ajouter plus d’instances afin de répondre à la demande, puis de les désactiver pendant les périodes plus calmes.

Dans les scénarios hybrides, le scale-out des composants individuels nécessite une attention supplémentaire quand ces composants sont répartis sur différents clouds. La mise à l’échelle d’une partie de l’application peut nécessiter la mise à l’échelle d’une autre partie. Par exemple, si le nombre de connexions clientes augmente mais que les services web de l’application ne font pas l’objet d’un scale out approprié, la charge de la base de données peut saturer l’application.

Certains composants d’application peuvent faire l’objet d’un scale out de manière linéaire, tandis que d’autres ont des dépendances de mise à l’échelle et peuvent être limités par l’étendue de ce dernier. Par exemple, un tunnel VPN fournissant une connectivité hybride pour les emplacements des composants d’application a une limite de scalabilité en ce qui concerne la bande passante et la latence. Comment est effectuée la mise à l’échelle des composants de l’application pour garantir le respect de ces exigences ?

### <a name="scalability-checklist"></a>Liste de contrôle de l’extensibilité

**Déterminer les seuils de mise à l’échelle.** Pour gérer les diverses dépendances de votre application, déterminez dans quelle mesure les composants d’application situés dans les différents clouds peuvent faire l’objet d’une mise à l’échelle indépendamment les uns des autres, tout en répondant aux exigences d’exécution de l’application. Les applications hybrides doivent souvent appliquer une mise à l’échelle à des zones particulières de l’application pour gérer une fonctionnalité qui interagit et affecte le reste de l’application. Par exemple, le dépassement d’un certain nombre d’instances front-end peut nécessiter une mise à l’échelle de l’instance back-end.

**Définir des planifications de mise à l’échelle.** Dans la mesure où la plupart des applications ont des pics d’activité, vous devez agréger leurs heures de pointe dans des planifications pour coordonner une mise à l’échelle optimale.

**Utiliser un système de supervision centralisé.** Les fonctionnalités de supervision de plateforme peuvent fournir une mise à l’échelle automatique. Toutefois, les applications hybrides ont besoin d’un système de supervision centralisé qui agrège l’intégrité et la charge du système. Un système de supervision centralisé peut lancer la mise à l’échelle d’une ressource à un emplacement et la mise à l’échelle d’une ressource dépendante à un autre emplacement. De plus, un système de supervision central peut suivre les clouds qui effectuent une mise à l’échelle automatique des ressources et les clouds qui n’en font pas.

**Exploiter les fonctionnalités de mise à l’échelle automatique (en fonction des disponibilités).** Si les fonctionnalités de mise à l’échelle automatique font partie de votre architecture, vous implémentez la mise à l’échelle automatique en configurant des seuils qui définissent le moment où un composant d’application doit faire l’objet d’un scale up, d’un scale out, d’un scale down ou d’un scale in. Voici un exemple de mise à l’échelle automatique : une connexion cliente fait l’objet d’une mise à l’échelle automatique dans un cloud pour permettre la gestion d’un accroissement de capacité. Toutefois, cela entraîne également la mise à l’échelle d’autres dépendances de l’application, réparties sur différents clouds. Les fonctionnalités de mise à l’échelle automatique de ces composants dépendants doivent être vérifiées.

Si la mise à l’échelle automatique n’est pas disponible, implémentez des scripts et d’autres ressources pour permettre une mise à l’échelle manuelle, déclenchée par les seuils définis dans le système de supervision centralisé.

**Déterminer la charge attendue en fonction de l’emplacement.** Il arrive que les applications hybrides qui gèrent les requêtes des clients reposent principalement sur un seul emplacement. Quand la charge des requêtes des clients dépasse un seuil,

vous pouvez ajouter des ressources supplémentaires à un autre emplacement pour répartir la charge des requêtes entrantes. Vérifiez que les connexions clientes peuvent gérer les charges accrues, et déterminez également des procédures automatisées pour permettre aux connexions clientes de gérer la charge.

## <a name="availability"></a>Disponibilité

La disponibilité est la durée pendant laquelle un système est fonctionnel et opérationnel. La disponibilité est mesurée en pourcentage de la durée de fonctionnement. Les erreurs d’application, les problèmes d’infrastructure et la charge système peuvent réduire la disponibilité.

Pour plus d’informations sur ce pilier, consultez [*Disponibilité*](/azure/architecture/framework/) dans la description des piliers de la qualité logicielle.

### <a name="availability-checklist"></a>Liste de contrôle de disponibilité

**Fournir une redondance pour la connectivité.** Les applications hybrides nécessitent une connectivité entre les clouds parmi lesquels elles sont réparties. Vous avez le choix entre différentes technologies pour la connectivité hybride. Ainsi, en plus de la technologie principale choisie, vous pouvez utiliser une autre technologie pour fournir une redondance avec des fonctionnalités de basculement automatique en cas de défaillance de la technologie principale.

**Classifier les domaines d’erreur.** Les applications à tolérance de panne nécessitent plusieurs domaines d’erreur. Les domaines d’erreur permettent d’isoler le point de défaillance, par exemple en cas de problème d’un seul disque dur local, d’un commutateur ToR (Top-of-Rack) ou de l’ensemble du centre de données. Dans une application hybride, un emplacement peut être classifié en tant que domaine d’erreur. Plus il existe d’exigences relatives à la disponibilité, plus vous devez évaluer la façon dont un seul domaine d’erreur doit être classifié.

**Classifier les domaines de mise à niveau.** Les domaines de mise à niveau permettent de garantir la disponibilité des instances de composants d’application, tandis que d’autres instances du même composant sont servies par des mises à jour ou des mises à niveau de fonctionnalités. Comme pour les domaines d’erreur, les domaines de mise à niveau peuvent être classifiés en fonction de leurs différents emplacements. Vous devez déterminer si un composant d’application peut être mis à niveau à un emplacement avant d’être mis à niveau à un autre emplacement, ou si d’autres configurations de domaine sont nécessaires. Un seul emplacement peut avoir plusieurs domaines de mise à niveau.

**Suivre les instances et la disponibilité.** Les composants d’application hautement disponibles peuvent être disponibles via l’équilibrage de charge et la réplication de données synchrone. Vous devez déterminer le nombre d’instances qui peuvent être hors connexion avant que le service ne soit interrompu.

**Implémenter l’auto-adaptation.** Au cas où un problème entraînerait une interruption de la disponibilité de l’application, une détection par un système de supervision peut lancer des activités d’auto-adaptation pour l’application, par exemple le vidage de l’instance défaillante et son redéploiement. Cela nécessite probablement une solution de supervision centrale, intégrée à un pipeline CI/CD (intégration continue/livraison continue) hybride. L’application est intégrée à un système de supervision pour identifier les problèmes qui peuvent nécessiter le redéploiement d’un composant d’application. Le système de supervision peut également déclencher un pipeline CI/CD hybride pour redéployer le composant d’application et éventuellement tout autre composant dépendant au même emplacement ou à d’autres emplacements.

**Gérer les contrats SLA.** La disponibilité est essentielle pour tous les contrats afin de maintenir la connectivité des services et applications que vous avez avec vos clients. Chaque emplacement sur lequel repose votre application hybride peut avoir son propre contrat SLA. Ces différents contrats SLA peuvent affecter le contrat SLA global de votre application hybride.

## <a name="resiliency"></a>Résilience

La résilience est la capacité d’un système et d’une application hybrides à effectuer une reprise d’activité et à continuer de fonctionner. L’objectif de la résilience est que l’application retrouve un état entièrement fonctionnel suite à une défaillance. Les stratégies de résilience incluent des solutions telles que la sauvegarde, la réplication et la reprise d’activité après sinistre.

Pour plus d’informations sur ce pilier, consultez [*Résilience*](https://docs.microsoft.com/azure/architecture/guide/pillars#resiliency) dans la description des piliers de la qualité logicielle.

### <a name="resiliency-checklist"></a>Liste de vérification de résilience

**Découvrir les dépendances de reprise après sinistre.** La reprise d’activité après sinistre dans un cloud peut nécessiter l’apport de changements aux composants d’application situés dans un autre cloud. Si un ou plusieurs composants d’un cloud font l’objet d’un basculement vers un autre emplacement, dans le même cloud ou dans un autre cloud, les composants dépendants doivent être informés de ces changements. Cela inclut également les dépendances de connectivité. La résilience nécessite un plan de récupération d’application entièrement testé pour chaque cloud.

**Établir un flux de récupération.** Une conception efficace du flux de récupération évalue la capacité des composants d’application à prendre en charge les mémoires tampons, les nouvelles tentatives, les échecs de tentatives de transfert de données et, si nécessaire, le repli vers un service ou un workflow de secours. Vous devez déterminer le mécanisme de sauvegarde à utiliser, sa procédure de restauration ainsi que sa fréquence de test. Vous devez également déterminer la fréquence des sauvegardes incrémentielles et complètes.

**Tester les récupérations partielles.** Une récupération partielle d’une partie de l’application permet de garantir aux utilisateurs que tout n’est pas perdu. Cette partie du plan doit garantir qu’une restauration partielle n’a aucun effet secondaire, par exemple dans le cas d’un service de sauvegarde et de restauration qui interagit avec l’application pour l’arrêter de manière appropriée avant l’exécution de la sauvegarde.

**Définir les instigateurs d’une reprise d’activité après sinistre et affecter une responsabilité.** Un plan de récupération doit décrire les personnes et les rôles qui peuvent lancer des actions de sauvegarde et de récupération, en plus du contenu à sauvegarder et restaurer.

**Comparer les seuils d’auto-adaptation à la reprise d’activité après sinistre.** Déterminez les capacités d’auto-adaptation d’une application pour le lancement de la récupération automatique ainsi que le délai nécessaire avant de considérer l’auto-adaptation comme un échec ou une réussite. Déterminez les seuils pour chaque cloud.

**Vérifier la disponibilité des fonctionnalités de résilience.** Déterminez la disponibilité des fonctionnalités et des capacités de résilience pour chaque emplacement. Si un emplacement ne fournit pas les fonctionnalités nécessaires, intégrez-le à un service centralisé offrant les fonctionnalités de résilience appropriées.

**Déterminer les temps d’arrêt.** Déterminez le temps d’arrêt prévu pour la maintenance de l’application dans son ensemble et des composants d’application.

**Documenter les procédures de résolution des problèmes.** Définissez les procédures de résolution des problèmes pour le redéploiement des ressources et des composants d’application.

## <a name="manageability"></a>Simplicité de gestion

Les considérations relatives à la gestion de vos applications hybrides sont essentielles pour la conception de votre architecture. Une application hybride correctement managée fournit une infrastructure sous forme de code qui permet l’intégration d’un code d’application cohérent dans un pipeline de développement commun. En implémentant des tests cohérents, que ce soit à l’échelle du système ou au niveau individuel, pour les changements apportés à l’infrastructure, vous pouvez garantir un déploiement intégré si ces changements passent les tests avec succès. Ainsi, ils pourront être fusionnés dans le code source.

Pour plus d’informations sur ce pilier, consultez [*DevOps*](/azure/architecture/framework/#devops) dans la description des piliers de la qualité logicielle.

### <a name="manageability-checklist"></a>Liste de contrôle de gestion

**Implémenter la supervision.** Utilisez un système de supervision centralisé des composants d’application répartis parmi les clouds pour fournir une vue agrégée de leur intégrité et de leur niveau de performance. Ce système comprend la supervision des composants d’application et des fonctionnalités de plateforme associées.

Déterminez les parties de l’application à superviser.

**Coordonner les stratégies.** Chaque emplacement couvert par une application hybride peut avoir sa propre stratégie, qui s’applique aux types de ressource, conventions de nommage, étiquettes et autres critères autorisés.

**Définir et utiliser les rôles.** En tant qu’administrateur de base de données, vous devez déterminer les autorisations nécessaires en fonction des différentes personnes (par exemple un propriétaire d’application, un administrateur de base de données et un utilisateur final) qui doivent accéder aux ressources de l’application. Vous devez configurer ces autorisations sur les ressources et au sein de l’application. Un système de contrôle d’accès en fonction du rôle (RBAC) vous permet de définir ces autorisations sur les ressources de l’application. La définition de ces droits d’accès peut s’avérer difficile quand toutes les ressources sont déployées dans un seul cloud, mais elle nécessite encore plus d’attention quand les ressources sont réparties entre plusieurs clouds. Les autorisations des ressources définies dans un cloud ne s’appliquent pas aux ressources définies dans un autre cloud.

**Utiliser des pipelines CI/CD.** Un pipeline CI/CD (intégration continue/déploiement continu) permet de fournir un processus cohérent pour la création et le déploiement d’applications qui couvrent plusieurs clouds. Il permet également de fournir une assurance qualité aux niveaux de l’infrastructure et de l’application. Ce pipeline permet de tester l’infrastructure et l’application dans un cloud, et de les déployer sur un autre cloud. Le pipeline vous permet même de déployer certains composants de votre application hybride sur un cloud et d’autres composants sur un autre cloud, ce qui constitue principalement la base du déploiement d’applications hybrides. Un système CI/CD est essentiel pour la gestion des dépendances entre les composants d’application durant l’installation, par exemple, dans le cas d’une application web qui nécessite une chaîne de connexion à la base de données.

**Gérer le cycle de vie.** Dans la mesure où les ressources d’une application hybride peuvent s’étendre sur plusieurs emplacements, la capacité de gestion du cycle de vie de chaque emplacement doit être agrégée en une seule unité de gestion du cycle de vie. Réfléchissez à leur mode de création, de mise à jour et de suppression.

**Examiner les stratégies de résolution des problèmes.** La résolution des problèmes liés à une application hybride implique un plus grand nombre de composants d’application que ceux de l’application qui s’exécute dans un seul cloud. En plus de la connectivité entre les clouds, l’application s’exécute sur deux plateformes au lieu d’une. L’une des tâches importantes dans la résolution des problèmes liés aux applications hybrides consiste à examiner les données agrégées de supervision de l’intégrité et du niveau de performance des composants d’application.

## <a name="security"></a>Sécurité

La sécurité est l’une des principales préoccupations de toute application cloud. Elle l’est encore plus pour les applications cloud hybrides.

Pour plus d’informations sur ce pilier, consultez [*Sécurité*](https://docs.microsoft.com/azure/architecture/guide/pillars#security) dans la description des piliers de la qualité logicielle.

### <a name="security-checklist"></a>Liste de contrôle de sécurité

**Envisager les violations de sécurité.** Si une partie de l’application est compromise, vérifiez que des solutions sont en place pour réduire la propagation de la violation de sécurité, non seulement à l’emplacement concerné mais également parmi les autres emplacements.

**Superviser les accès réseau autorisés.** Déterminez les stratégies d’accès réseau de l’application. Par exemple, accédez uniquement à l’application à partir d’un sous-réseau spécifique et n’autorisez que le nombre minimal de ports et de protocoles entre les composants pour le bon fonctionnement de l’application.

**Utiliser une authentification robuste.** Un schéma d’authentification robuste est essentiel pour la sécurité de votre application. Utilisez un fournisseur d’identité fédérée offrant des fonctionnalités de connexion à authentification unique et utilisant un ou plusieurs des schémas suivants : connexion avec nom d’utilisateur et mot de passe, clés publique et privée, authentification à deux facteurs ou multifacteur, et groupes de sécurité approuvés. Déterminez les ressources appropriées au stockage des données sensibles et autres secrets pour l’authentification de l’application, en plus des types de certificat et de leurs exigences.

**Utiliser le chiffrement.** Identifiez les zones de l’application qui utilisent le chiffrement, par exemple pour le stockage de données ou la communication et l’accès des clients.

**Utiliser des canaux sécurisés.** L’utilisation d’un canal sécurisé parmi les clouds est essentielle pour assurer les contrôles de sécurité et d’authentification, une protection en temps réel, la mise en quarantaine ainsi que d’autres services liés aux clouds.

**Définir et utiliser les rôles.** Implémentez des rôles pour les configurations de ressources et l’accès via une seule identité parmi les différents clouds. Déterminez les exigences du contrôle d’accès en fonction du rôle (RBAC) pour l’application et ses ressources de plateforme.

**Auditer votre système.** La supervision du système peut journaliser et agréger des données à partir des composants d’application et des opérations de plateforme cloud associées.

## <a name="summary"></a>Résumé

Cet article fournit une liste de contrôle des éléments importants à prendre en compte durant la création et de la conception de vos applications hybrides. Passez en revue ces piliers avant de déployer votre application. Ainsi, vous pourrez éviter les problèmes évoqués en cas d’interruption de production, et vous n’aurez pas à revoir éventuellement votre conception.

Cela peut sembler une tâche laborieuse au premier abord, mais vous obtiendrez facilement un retour sur investissement si vous concevez votre application en fonction de ces piliers.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les ressources suivantes :

-   [Cloud hybride](https://azure.microsoft.com/overview/hybrid-cloud/)
-   [Applications cloud hybrides](https://azure.microsoft.com/solutions/hybrid-cloud-app/)
-   [Développer des modèles Azure Resource Manager pour la cohérence du cloud](https://aka.ms/consistency)