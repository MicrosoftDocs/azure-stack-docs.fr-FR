---
title: Modèle de création d’une application qui effectue une mise multicloud à l’aide de données locales, sur Azure et Azure Stack Hub.
description: Découvrez comment utiliser Azure et Azure Stack Hub pour créer une application multicloud scalable qui utilise des données locales.
author: BryanLa
ms.service: azure-stack
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 75a72deac409013b3fcd77693c3a7ceb2d2497cc
ms.sourcegitcommit: 5c92a669007ab4aaffe4484f1d8836a40340dde1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73640178"
---
# <a name="cross-cloud-scaling-on-premises-data-pattern"></a>Modèle de mise à l’échelle multicloud (données locales)

Découvrez comment créer une application hybride qui s’étend sur Azure et Azure Stack Hub. Ce modèle vous montre également comment utiliser une source de données locale unique pour la conformité.

## <a name="context-and-problem"></a>Contexte et problème

De nombreuses organisations recueillent et stockent de grandes quantités de données clients sensibles. Souvent, elles ne sont pas en mesure de stocker des données sensibles dans le cloud public, en raison de leurs réglementations internes ou de la politique gouvernementale. Ces organisations souhaitent également tirer parti de la scalabilité du cloud public. Le cloud public peut gérer les pics de trafic saisonniers, ce qui permet aux clients de payer exactement le matériel dont ils ont besoin, quand ils en ont besoin.

## <a name="solution"></a>Solution

La solution tire parti des avantages de la conformité du cloud privé, en les associant à la scalabilité du cloud public. Le cloud hybride Azure Stack Hub et Azure offrent une expérience cohérente pour les développeurs. Cette cohérence leur permet d’appliquer leurs compétences à des environnements locaux et dans le cloud public.

Le guide de déploiement de la solution vous permet de déployer une application web identique sur un cloud public et privé. Vous pouvez également accéder à un réseau routable non-Internet hébergé sur le cloud privé. La charge des applications web est analysée. En cas d’augmentation significative du trafic, un programme modifie les enregistrements DNS pour rediriger le trafic vers le cloud public. Lorsque le trafic n’est plus significatif, les enregistrements DNS sont mis à jour pour rediriger le trafic vers le cloud privé.

[![Modèle de mise à l’échelle multicloud (données locales)](media/pattern-cross-cloud-scale-onprem-data/solution-architecture.png)](media/pattern-cross-cloud-scale-onprem-data/solution-architecture.png)

## <a name="components"></a>Composants

Cette solution utilise les composants suivants :

| Couche | Composant | Description |
|----------|-----------|-------------|
| Azure | Azure App Service | [Azure App Service](/azure/app-service/) vous permet de créer et d’héberger des applications web, des applications API RESTful et Azure Functions. Tout cela dans le langage de programmation de votre choix, sans devoir gérer l’infrastructure. |
| | Réseau virtuel Azure| [Le réseau virtuel Azure (VNet)](/azure/virtual-network/virtual-networks-overview) est le composant principal fondamental pour vos réseaux privés dans Azure. Le réseau virtuel permet à plusieurs types de ressources Azure, telles que les machines virtuelles, de communiquer de manière sécurisée entre elles, avec Internet et avec les réseaux locaux. La solution illustre également l’utilisation de composants de mise en réseau supplémentaires :<br>- sous-réseaux d’application et de passerelle<br>- passerelle réseau locale<br>- passerelle de réseau virtuel qui agit comme une connexion de passerelle VPN site à site<br>- adresse IP publique<br>- connexion VPN point à site<br>- Azure DNS pour l’hébergement des domaines DNS et la résolution de noms |
| | Azure Traffic Manager | [Azure Traffic Manager](/azure/traffic-manager/traffic-manager-overview) est un équilibreur de charge de trafic DNS. Il vous permet de contrôler la répartition du trafic utilisateur pour les points de terminaison de service dans différents centres de données. |
| | Azure Application Insights | [Application Insights](/azure/azure-monitor/app/app-insights-overview) est un service extensible de gestion des performances des applications (APM) permettant aux développeurs web de créer et de gérer des applications sur de multiples plateformes.|
| | Azure Functions | [Azure Functions](/azure/azure-functions/) vous permet d’exécuter votre code dans un environnement serverless et sans avoir à créer une machine virtuelle ou à publier une application web au préalable. |
| | Mise à l’échelle automatique Azure | La [mise à l’échelle automatique](/azure/azure-monitor/platform/autoscale-overview) est une fonctionnalité intégrée des services cloud, des machines virtuelles et des sites web. Cette fonctionnalité permet aux applications de s’exécuter au mieux lorsque la demande change. Les applications s’adaptent aux pics de trafic, vous informent de la modification des métriques et se mettent à l’échelle en fonction des besoins. |
| Azure Stack Hub | Calcul IaaS | Azure Stack Hub vous permet d’utiliser le modèle d’application, le portail libre-service et les API autorisés par Azure. L’infrastructure IaaS Azure Stack Hub offre une large gamme de technologies open source pour des déploiements de cloud hybride cohérents. L’exemple de solution utilise une machine virtuelle Windows Server pour SQL Server, par exemple.|
| | Azure App Service | Tout comme l’application web Azure, la solution utilise [Azure App Service sur Azure Stack Hub](/azure-stack/operator/azure-stack-app-service-overview) pour héberger l’application web. |
| | Mise en réseau | Le réseau virtuel Azure Stack Hub fonctionne exactement comme le réseau virtuel Azure. Il utilise un grand nombre des mêmes composants de mise en réseau, notamment les noms d’hôte personnalisés. 
| Azure DevOps Services | Inscription | Configurez rapidement l’intégration continue de build, de test et de déploiement. Pour plus d’informations, consultez [S’inscrire et se connecter à Azure DevOps](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops). |
| | Azure Pipelines | Utilisez [Azure Pipelines](/azure/devops/pipelines/agents/agents?view=azure-devops) pour l’intégration continue et la livraison continue. Azure Pipelines vous permet de gérer des définitions et des agents de build et de mise en production hébergés. |
| | Dépôt de code | Tirez parti de plusieurs dépôts de code pour simplifier votre pipeline de développement. Utilisez les dépôts de code existants dans GitHub, Bitbucket, Dropbox, OneDrive et Azure Repos. |

## <a name="issues-and-considerations"></a>Problèmes et considérations

Tenez compte des points suivants lors du choix de l’implémentation de cette solution :

### <a name="scalability"></a>Extensibilité 

Azure et Azure Stack Hub sont spécifiquement conçus pour prendre en charge des besoins des entreprises internationales modernes.

**Un cloud hybride sans contrainte**

Microsoft offre une intégration inégalée des ressources locales avec Azure Stack Hub et Azure dans une solution unifiée. Cette intégration élimine la nécessité de gérer plusieurs solutions ponctuelles et une combinaison de fournisseurs cloud. Avec la mise à l’échelle multicloud, la puissance d’Azure est à portée de mains. Connectez simplement votre instance Azure Stack Hub à Azure avec le « cloud bursting » et vos données et applications seront disponibles dans Azure si nécessaire.

- Vous n’avez ainsi plus besoin de créer ni de gérer un site secondaire de reprise d’activité après sinistre.
- Gagnez du temps et de l’argent en éliminant la sauvegarde sur bande et en hébergeant jusqu’à 99 ans de données de sauvegarde dans Azure.
- Migrez facilement les charges de travail Hyper-V, physiques (en préversion) et VMware (en préversion) dans Azure pour tirer parti des avantages économiques et de l’élasticité du cloud.
- Exécutez des rapports ou des analytiques de calcul intensif sur une copie répliquée de vos ressources locales dans Azure, sans impact sur les charges de travail de production.
- Opérez dans le cloud et exécutez des charges de travail locales dans Azure, avec des modèles de calcul plus importants si nécessaire. Le mode hybride vous offre la puissance dont vous avez besoin, quand vous en avez besoin.
- Créez des environnements de développement multiniveau dans Azure en quelques clics. Répliquez même les données de production en temps réel dans votre environnement de développement et de test afin de conserver une synchronisation en temps quasi-réel.

**Économies possibles grâce à la mise à l’échelle multicloud avec Azure Stack Hub**

Les économies réalisées sont le principal avantage de la migration en mode burst dans le cloud. Vous payez uniquement les ressources supplémentaires lorsqu’il y a une demande pour ces ressources. Vous ne payez plus de capacité supplémentaire inutile et n’avez plus à prédire les pics et les fluctuations de la demande.

**Réduire les charges à forte demande dans le cloud**

La mise à l’échelle multicloud peut être utilisée pour soutenir la charge liée au traitement. La charge est distribuée en déplaçant des applications de base vers le cloud public afin de libérer des ressources locales pour des applications stratégiques. Une application peut être appliquée au cloud privé, puis étendue au cloud public uniquement lorsque c’est nécessaire pour répondre à la demande.


### <a name="availability"></a>Disponibilité

Le déploiement à l’échelle mondiale a ses propres défis, tels que la connectivité variable et les réglementations gouvernementales différentes par région. Les développeurs peuvent développer une seule application et la déployer pour différentes raisons avec des exigences différentes.  Déployez votre application dans le cloud public Azure, puis déployez des instances ou des composants supplémentaires localement. Vous pouvez gérer le trafic entre toutes les instances à l’aide d’Azure.

### <a name="manageability"></a>Facilité de gestion

**Une approche de développement unique et cohérente**

Azure et Azure Stack Hub vous permettent d’utiliser un ensemble cohérent d’outils de développement au sein de l’organisation. Grâce à cette cohérence, il est plus facile d’implémenter une pratique d’intégration continue et de développement continu (CI/CD). De nombreux services et applications déployés dans Azure ou Azure Stack Hub sont interchangeables et peuvent s’exécuter dans les deux emplacements de façon fluide.

Un pipeline CI/CD hybride peut vous aider à :
- Lancer une nouvelle build basée sur des validations de code dans votre dépôt de code.
- Déployer automatiquement votre code nouvellement généré sur Azure pour le test d’acceptation des utilisateurs.
- Une fois que votre code a réussi le test, effectuez automatiquement le déploiement sur Azure Stack Hub.

**Une solution de gestion des identités unique et cohérente**

Azure Stack Hub fonctionne avec Azure Active Directory et Services ADFS (ADFS). Azure Stack Hub fonctionne avec Azure Active Directory dans les scénarios connectés. Pour les environnements qui n’ont pas de connectivité, vous pouvez utiliser ADFS comme solution déconnectée. Les principaux de service sont utilisés pour accorder l’accès aux applications, ce qui leur permet de déployer ou de configurer des ressources via Azure Resource Manager. 

### <a name="security"></a>Sécurité

**Garantir la conformité et la souveraineté des données**

Azure Stack Hub vous permet d’exécuter le même service dans plusieurs pays, comme avec un cloud public. Le déploiement de la même application dans les centres de données de chaque pays permet la satisfaction des exigences relatives à la souveraineté des données. Cette fonctionnalité garantit que les données personnelles sont conservées dans les limites de chaque pays.

**Azure Stack Hub - Posture de sécurité**

Il n’existe pas de posture de sécurité sans processus de maintenance solide et continu. Pour cette raison, Microsoft a investi dans un moteur d’orchestration qui applique les correctifs et les mises à jour de façon fluide à toute l’infrastructure.

Grâce à des partenariats avec des partenaires OEM Azure Stack Hub, Microsoft étend la même posture de sécurité aux composants OEM. Tels que l’hôte du cycle de vie du matériel et le logiciel s’exécutant sur celui-ci. Ce partenariat garantit qu’Azure Stack Hub a une posture de sécurité uniforme et solide sur toute l’infrastructure. À leur tour, les clients peuvent créer et sécuriser leurs charges de travail d’application.

**Utiliser des principaux de service via PowerShell, l’interface CLI et le Portail Azure**

Pour accorder l’accès aux ressources à un script ou à une application, configurez une identité pour votre application et authentifiez l’application avec ses propres informations d’identification. Cette identité est connue sous le nom de principal du service et vous permet d’effectuer les opérations suivantes :

- Attribuez à l’identité de l’application des autorisations différentes de vos propres autorisations et restreintes aux besoins propres à l’application.
- Utilisez un certificat pour l’authentification lors de l’exécution d’un script sans assistance. 

Pour plus d’informations sur la création d’un principal de service et l’utilisation d’un certificat pour les informations d’identification, consultez [Utiliser une identité d’application pour accéder aux ressources](/azure-stack/operator/azure-stack-create-service-principals).

## <a name="when-to-use-this-pattern"></a>Quand utiliser ce modèle 

- Mon organisation utilise une approche DevOps ou en a planifié une dans un avenir proche.
- Je souhaite implémenter des pratiques CI/CD dans mon implémentation d’Azure Stack Hub et dans le cloud public.
- Je souhaite consolider le pipeline CI/CD dans des environnements cloud et locaux.
- Je souhaite développer des applications de façon fluide à l’aide de services cloud ou locaux.
- Je souhaite tirer parti de compétences de développement cohérentes pour les applications cloud et locales.
- J’utilise Azure, mais j’ai des développeurs qui travaillent dans un cloud Azure Stack Hub local.
- Mes applications locales subissent des pics de demande lors de fluctuations saisonnières, cycliques ou imprévisibles.
- Je dispose de composants locaux et je souhaite utiliser le cloud pour les mettre à l’échelle de manière fluide.
- Je souhaite avoir la scalabilité du cloud, mais je souhaite que mon application s’exécute en local autant que possible.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les sujets abordés dans cet article :
- Pour une présentation de l’utilisation de ce modèle, consultez [Mettre à l’échelle dynamiquement des applications entre les centres de données et le cloud public](https://www.youtube.com/watch?v=2lw8zOpJTn0).
- Consultez [Considérations relatives à la conception des applications hybrides](overview-app-design-considerations.md) pour en savoir plus sur les meilleures pratiques, et pour obtenir les réponses à d’autres questions.
- Ce modèle utilise la famille de produits Azure Stack, notamment Azure Stack Hub. Pour plus d’informations sur l’ensemble du portefeuille de produits et de solutions, consultez [Famille de produits et de solutions d’Azure Stack ](/azure-stack).

Lorsque vous êtes prêt à tester l’exemple de solution, poursuivez avec le [Guide de déploiement de la solution de mise à l’échelle multicloud (données locales)](solution-deployment-guide-cross-cloud-scaling-onprem-data.md). Ce guide de déploiement fournit des instructions pas à pas sur le déploiement et sur le test de ses composants.