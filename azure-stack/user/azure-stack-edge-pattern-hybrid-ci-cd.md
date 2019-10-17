---
title: Modèle DevOps pour la périphérie intelligente avec Azure Stack | Microsoft Docs
description: En savoir plus sur le modèle DevOps pour la périphérie intelligente avec Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/10/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 06/11/2019
ms.openlocfilehash: 04eff0f095f14d88443fc4b221799e63f523c82c
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72277012"
---
# <a name="devops-pattern"></a>Modèle DevOps

Développez à partir d’un seul emplacement et effectuez vos déploiements sur plusieurs cibles dans des environnements de développement, de test et de production qui peuvent se trouver dans votre centre de données local, vos clouds privés ou le cloud public.

## <a name="context-and-problem"></a>Contexte et problème

La continuité, la sécurité et la fiabilité du déploiement d’applications sont essentielles pour les organisations et critiques pour les équipes de développement.

Les applications nécessitent souvent du code refactorisé pour s’exécuter dans chaque environnement cible. Cela signifie qu’une application n’est pas complètement portable. Elle doit être mise à jour, testée et validée au fur et à mesure de son déplacement dans chaque environnement. Par exemple, le code écrit dans un environnement de développement doit ensuite être réécrit pour fonctionner dans un environnement de test, puis réécrit quand il arrive enfin dans un environnement de production. De plus, ce code est spécifiquement lié à l’hôte. Cela se traduit par une hausse des coûts et de la complexité de la maintenance de votre application. Chaque version de l’application est liée à chaque environnement. L’accroissement de la complexité et les problèmes de duplication augmentent les risques liés à la sécurité et à la qualité du code. De plus, vous ne pouvez pas facilement redéployer le code quand vous supprimez des hôtes défaillants ou quand vous déployez des hôtes supplémentaires pour gérer la hausse de la demande.

## <a name="solution"></a>Solution

Le modèle DevOps vous permet de générer, tester et déployer une application qui s’exécute dans plusieurs clouds. Ce modèle unit la pratique de l’intégration continue et de la livraison continue. Avec l’intégration continue, le code est généré et testé chaque fois qu’un membre de l’équipe commite un changement apporté à la gestion de versions. La livraison continue automatise chaque étape comprise entre une build et un environnement de production. Ensemble, ces opérations constituent un processus de mise en production qui prend en charge le déploiement sur divers environnements. Avec ce modèle, vous pouvez créer un brouillon de votre code et le déployer ensuite sur un environnement local, des clouds privés distincts et les clouds publics. Pour tenir compte des différences d’environnement, il vous suffit d’apporter des changements à un fichier config au lieu du code.

![Modèle DevOps](media/azure-stack-edge-pattern-hybrid-ci-cd/hybrid-ci-cd.png)

Avec un ensemble cohérent d’outils de développement dans les environnements locaux, de clouds privés et de clouds publics, vous pouvez implémenter une pratique d’intégration continue et de livraison continue. Les applications et services déployés à l’aide du modèle DevOps sont interchangeables et peuvent s’exécuter dans n’importe lequel de ces emplacements, en tirant parti des fonctionnalités et capacités d’un environnement local et du cloud public.

L’utilisation d’un pipeline de mise en production DevOps vous aide à :

-   Lancer une nouvelle build basée sur des validations de code dans un seul dépôt

-   Déployer automatiquement le code récemment généré sur le cloud public pour les tests d’acceptation utilisateur

-   Déployer automatiquement le code sur un cloud privé, une fois qu’il a réussi les tests

## <a name="issues-and-considerations"></a>Problèmes et considérations

Le modèle DevOps est destiné à assurer la cohérence entre les déploiements, quel que soit l’environnement cible. Toutefois, les fonctionnalités varient selon les environnements cloud et locaux. Tenez compte des éléments suivants :

-   Les fonctions, les points de terminaison, les services et autres ressources de votre déploiement sont-ils disponibles dans les emplacements de déploiement cibles ?

-   Les artefacts de configuration sont-ils stockés dans des emplacements accessibles parmi les différents clouds ?

-   Les paramètres de déploiement vont-ils fonctionner dans tous les environnements cibles ?

-   Les propriétés spécifiques aux ressources sont-elles disponibles dans tous les clouds cibles ?

Pour plus d’informations, consultez [Développer des modèles Azure Resource Manager de cohérence du cloud](https://docs.microsoft.com/azure/azure-resource-manager/templates-cloud-consistency).

De plus, prenez en compte les points suivants pour décider du mode d’implémentation de ce modèle :

### <a name="scalability-considerations"></a>Considérations relatives à l’extensibilité

Les systèmes d’automation du déploiement constituent le point de contrôle clé des modèles DevOps. Les implémentations peuvent varier. La sélection de la taille de serveur appropriée dépend de la taille de la charge de travail attendue. La mise à l’échelle des machines virtuelles coûte plus cher que celle des conteneurs. Toutefois, pour mettre des conteneurs à l’échelle, votre processus de génération doit s’exécuter avec des conteneurs.

### <a name="availability-considerations"></a>Considérations relatives à la disponibilité

Dans le contexte d’un modèle DevOps, la disponibilité signifie la capacité à récupérer les informations d’état associées à votre workflow, par exemple les résultats des tests, les dépendances du code ou d’autres artefacts. Pour évaluer les exigences de disponibilité, tenez compte des deux mesures courantes :

-   Le RTO (objectif de délai de récupération) spécifie la durée pendant laquelle vous pouvez vous passer d’un système.

-   Le RPO (objectif de point de récupération) indique la quantité de données que vous pouvez vous permettre de perdre si une interruption de service affecte le système.

Dans la pratique, le RTO et le RPO impliquent une redondance et une sauvegarde. Dans le cloud Azure mondial, la disponibilité n’est pas une question de récupération du matériel, elle fait partie intégrante d’Azure. Vous devez plutôt assurer la maintenance de l’état de vos systèmes DevOps. Avec Azure Stack, la récupération du matériel peut être un facteur à prendre en compte.

Il existe d’autres éléments importants à prendre en compte durant la conception du système utilisé pour l’automation du déploiement. Il s’agit du contrôle d’accès et de la gestion appropriée des droits nécessaires au déploiement des services sur les environnements cloud. Quels sont les droits nécessaires pour créer, supprimer ou modifier des déploiements ? Par exemple, vous avez généralement besoin d’un ensemble de droits pour créer un groupe de ressources dans Azure et d’un autre ensemble de droits pour déployer les services sur le groupe de ressources.

### <a name="manageability-considerations"></a>Considérations relatives à la facilité de gestion

La conception d’un système basé sur le modèle DevOps doit prendre en compte l’automatisation, la journalisation et les alertes pour chaque service du portefeuille. Utilisez des services partagés, une équipe d’application, ou les deux, et suivez également les stratégies de sécurité et la gouvernance.

Déployez les environnements de production et les environnements de développement/test dans des groupes de ressources distincts sur Azure ou Azure Stack. Vous pouvez ensuite superviser les ressources de chaque environnement et totaliser les coûts de facturation par groupe de ressources. Vous pouvez également supprimer des ressources comme un tout, ce qui est pratique pour les déploiements de test.

## <a name="when-to-use-this-pattern"></a>Quand utiliser ce modèle

Utilisez ce modèle :

-   Vous pouvez développer du code dans un environnement qui répond aux besoins de vos développeurs et le déployer sur un environnement spécifique à votre solution, où il peut être difficile de développer un nouveau code.

-   Vous pouvez utiliser le code et les outils souhaités par vos développeurs, du moment que ces derniers peuvent suivre le processus d’intégration continue et de livraison continue du modèle DevOps.

Ce modèle n’est pas recommandé :

-   Si vous ne pouvez pas automatiser les tâches relatives à l’infrastructure, au provisionnement des ressources, à la configuration, aux identités et à la sécurité

-   Si les équipes n’ont pas accès aux ressources cloud hybrides pour implémenter une approche CI/CD (intégration continue/développement continu)

## <a name="example"></a>Exemples

Découvrez comment déployer une application sur Azure et Azure Stack à l’aide d’un pipeline d’intégration continue/de livraison continue (CI/CD) hybride.

[Déployer des applications sur Azure et Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-pipeline)

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les [modèles de conception de clouds hybrides pour Azure Stack](azure-stack-edge-pattern-overview.md)
