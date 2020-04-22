---
title: Modèles hybrides et exemples de solutions pour Azure et Azure Stack Hub
description: Vue d’ensemble des modèles hybrides et des exemples de solutions pour l’apprentissage et la création de solutions hybrides sur Azure et Azure Stack Hub.
author: BryanLa
ms.topic: overview
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: d56957c139728898117eab5f8a3a35c98722c0d2
ms.sourcegitcommit: 4138a2a15f78e7db38b3a29acc963a71937146fd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "80812303"
---
# <a name="hybrid-patterns-and-solution-examples-for-azure-and-azure-stack"></a>Modèles hybrides et exemples de solutions pour Azure et Azure Stack

Microsoft fournit des produits et des solutions Azure et Azure Stack sous la forme d’un écosystème Azure cohérent. La famille Microsoft Azure Stack est une extension d’Azure.

## <a name="the-hybrid-cloud-and-hybrid-apps"></a>Cloud hybride et applications hybrides

Azure Stack permet de mettre en place un *cloud hybride*, apportant ainsi l’agilité du cloud computing à votre environnement local et à sa périphérie. Azure Stack Hub, Azure Stack HCI et Azure Stack Edge étendent Azure à partir du cloud dans vos centres de centres de données souverains, dans vos filiales, sur le terrain et au-delà. Avec cet ensemble diversifié de fonctionnalités, vous pouvez :

- Réutiliser le code et exécuter des applications cloud natives de façon cohérente dans Azure et dans vos environnements locaux.
- Exécuter des charges de travail virtualisées traditionnelles avec des connexions facultatives aux services Azure.
- Transférer des données dans le cloud ou les conserver dans votre centre de données souverain pour maintenir la conformité.
- Exécuter des charges de travail d’apprentissage automatique accélérée par le matériel, en conteneur ou virtualisées, le tout à la périphérie intelligente.

Les applications qui s’étendent sur des clouds sont également appelées *applications hybrides*. Vous pouvez créer des applications de cloud hybride dans Azure et les déployer dans votre centre de données connecté ou déconnecté, quel que soit l’endroit où celui-ci est situé.

Les scénarios d’applications hybrides varient considérablement selon les ressources qui sont disponibles pour le développement. Ils couvrent également des considérations comme la géographie, la sécurité, l’accès à Internet, etc. Bien que les modèles et les solutions décrits ici puissent ne pas répondre à toutes les exigences, ils fournissent des directives et des exemples à explorer et à réutiliser lors de l’implémentation de solutions hybrides.

## <a name="design-patterns"></a>Modèles de conception

Les modèles de conception permettent de se passer des conseils de conception reproductibles généralisés, issus de scénarios et d’expériences clients réels. Un modèle est abstrait, ce qui lui permet de s’appliquer à différents types de scénarios ou à des activités verticales. Chaque modèle documente le contexte et le problème, et fournit une vue d’ensemble d’un exemple de solution. L’exemple de solution est conçu comme une implémentation possible du modèle.

Il existe deux types d’articles de modèles :

- Modèle unique : fournit des conseils de conception pour un seul scénario à usage général.
- Multimodèle : fournit des conseils de conception là où l’application de plusieurs modèles est utilisée. Ce modèle est souvent nécessaire pour résoudre des scénarios plus complexes ou des problèmes spécifiques à un secteur d’activité.

## <a name="solution-deployment-guides"></a>Guides de déploiement de solutions

Des guides de déploiement pas à pas vous aident à déployer un exemple de solution. Le guide peut également faire référence à un exemple de code associé, stocké dans le [dépôt d’exemples de solutions](https://github.com/Azure-Samples/azure-intelligent-edge-patterns) de GitHub.

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Famille de produits et de solutions Azure Stack](/azure-stack) pour en savoir plus sur l’ensemble du portefeuille de produits et de solutions.
- Pour plus d’informations sur chacun d’eux, explorez les sections « Modèles » et « Guides de déploiement de solutions » de la table des matières.
- Découvrez les [considérations relatives à la conception d’applications hybrides](overview-app-design-considerations.md) pour passer en revue les piliers de la qualité logicielle permettant de concevoir, de déployer et d’utiliser des applications hybrides.
- [Configurez un environnement de développement sur Azure Stack](../user/azure-stack-dev-start.md) et [déployez votre première application](../user/azure-stack-dev-start-deploy-app.md) sur Azure Stack.
