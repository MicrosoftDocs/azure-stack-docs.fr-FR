---
title: Modèle de mise à l’échelle multicloud dans Azure Stack Hub
description: Découvrez comment créer une application multicloud scalable sur Azure et Azure Stack Hub.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: a830f96e97c347cbbcc09a1b17f4836ecb6eb3e6
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "80891026"
---
# <a name="cross-cloud-scaling-pattern"></a>Modèle de mise à l’échelle multicloud

Ajoutez automatiquement des ressources à une application existante pour faire face à une hausse de la charge.

## <a name="context-and-problem"></a>Contexte et problème

Votre application ne peut pas augmenter sa capacité pour répondre à une hausse inattendue de la demande. En raison de ce manque de scalabilité, les utilisateurs ne peuvent donc pas accéder à l’application pendant les pics d’utilisation. L’application peut répondre aux demandes d’un nombre fixe d’utilisateurs.

Les entreprises internationales ont besoin d’applications cloud sécurisées, fiables et disponibles. La réponse à la hausse de la demande et l’utilisation de l’infrastructure appropriée pour prendre en charge cette demande sont des aspects critiques. Les entreprises ont des difficultés à équilibrer les coûts et la maintenance en ce qui concerne la sécurité, le stockage et la disponibilité en temps réel des données métier.

Vous ne pourrez peut-être pas exécuter votre application dans le cloud public. Toutefois, ceci peut ne pas être économiquement faisable pour que l’entreprise puisse maintenir la capacité nécessaire dans son environnement local afin de gérer les pics de demande de l’application. Avec ce modèle, vous pouvez tirer parti de l’élasticité du cloud public pour votre solution locale.

## <a name="solution"></a>Solution

Le modèle de mise à l’échelle multicloud étend une application située dans un cloud local avec les ressources du cloud public. Le modèle se déclenche en réponse à une hausse ou une baisse de la demande, puis ajoute ou supprime respectivement des ressources dans le cloud. Ces ressources assurent une redondance, une disponibilité rapide et un routage géoconforme.

![Modèle de mise à l’échelle multicloud](media/pattern-cross-cloud-scale/cross-cloud-scaling.png)

> [!NOTE]
> Ce modèle s’applique uniquement aux applications sans état de votre application.

## <a name="components"></a>Components

Le modèle de mise à l’échelle multicloud inclut les composants suivants.

### <a name="outside-the-cloud"></a>En dehors du cloud

#### <a name="traffic-manager"></a>Traffic Manager

Dans le diagramme, il se situe en dehors du groupe de clouds publics, mais il doit pouvoir coordonner le trafic dans le centre de données local et dans le cloud public. L’équilibreur offre une haute disponibilité pour l’application en supervisant les points de terminaison et en assurant la redistribution du basculement en cas de besoin.

#### <a name="domain-name-system-dns"></a>DNS (Domain Name System)

Le DNS (Domain Name System) se charge de traduire (ou résoudre) un nom de site web ou de service en une adresse IP.

### <a name="cloud"></a>Cloud

#### <a name="hosted-build-server"></a>Serveur de builds hébergé

Environnement d’hébergement de votre pipeline de build.

#### <a name="app-resources"></a>Ressources d’application

Les ressources d’application doivent pouvoir effectuer un scale-in et un scale-out, à l’image des groupes de machines virtuelles identiques et des conteneurs.

#### <a name="custom-domain-name"></a>Nom de domaine personnalisé

Utilisez un nom de domaine personnalisé pour le Glob de routage des requêtes.

#### <a name="public-ip-addresses"></a>Adresses IP publiques

Les adresses IP publiques sont utilisées pour router le trafic entrant via Traffic Manager vers le point de terminaison des ressources d’application du cloud public.  

### <a name="local-cloud"></a>Cloud local

#### <a name="hosted-build-server"></a>Serveur de builds hébergé

Environnement d’hébergement de votre pipeline de build.

#### <a name="app-resources"></a>Ressources d’application

Les ressources d’application doivent pouvoir effectuer un scale-in et un scale-out, à l’image des groupes de machines virtuelles identiques et des conteneurs.

#### <a name="custom-domain-name"></a>Nom de domaine personnalisé

Utilisez un nom de domaine personnalisé pour le Glob de routage des requêtes.

#### <a name="public-ip-addresses"></a>Adresses IP publiques

Les adresses IP publiques sont utilisées pour router le trafic entrant via Traffic Manager vers le point de terminaison des ressources d’application du cloud public.

## <a name="issues-and-considerations"></a>Problèmes et considérations

Prenez en compte les points suivants lorsque vous choisissez comment implémenter ce modèle :

### <a name="scalability"></a>Extensibilité

Le composant clé de la mise à l’échelle multicloud est la possibilité de fournir une mise à l’échelle à la demande. La mise à l’échelle doit se produire entre l’infrastructure cloud publique et locale et fournir un service cohérent et fiable à la demande.

### <a name="availability"></a>Disponibilité

Vérifiez que les applications déployées en local sont configurées pour la haute disponibilité via la configuration matérielle locale et le déploiement de logiciels.

### <a name="manageability"></a>Simplicité de gestion

Le modèle multicloud garantit une gestion fluide et l’accès à une interface familière entre les environnements.

## <a name="when-to-use-this-pattern"></a>Quand utiliser ce modèle

Utilisez ce modèle :

- Quand vous avez besoin d’augmenter la capacité de votre application pour faire face à des demandes inattendues ou régulières.
- Quand vous ne souhaitez pas investir dans des ressources qui vont être utilisées uniquement durant les pics d’activité. Payez pour ce que vous utilisez.

Ce modèle n’est pas recommandé quand :

- Votre solution impose aux utilisateurs de se connecter via Internet
- Votre entreprise doit se conformer à des réglementations locales qui imposent à la connexion d’origine de provenir d’un appel local
- Votre réseau est confronté à des goulots d’étranglement réguliers qui limitent les performances de la mise à l’échelle.
- Votre environnement est déconnecté d’Internet et ne peut pas atteindre le cloud public.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les sujets abordés dans cet article :

- Pour en savoir plus sur le fonctionnement de cet équilibreur de charge du trafic basé sur DNS, consultez la [présentation d’Azure Traffic Manager](/azure/traffic-manager/traffic-manager-overview).
- Consultez [Considérations relatives à la conception des applications hybrides](overview-app-design-considerations.md) pour en savoir plus sur les bonnes pratiques et obtenir des réponses à d’autres questions.
- Consultez [Famille de produits et de solutions Azure Stack](/azure-stack) pour en savoir plus sur l’ensemble du portefeuille de produits et de solutions.

Lorsque vous êtes prêt à tester l’exemple de solution, poursuivez avec le [Guide de déploiement de la solution de mise à l’échelle multicloud](solution-deployment-guide-cross-cloud-scaling.md). Ce guide de déploiement fournit des instructions pas à pas sur le déploiement et sur le test de ses composants. Découvrez comment créer une solution multicloud pour fournir un processus déclenché manuellement permettant de passer d’une application web hébergée sur Azure Stack Hub à une application web hébergée sur Azure. Découvrez également comment utiliser la mise à l’échelle automatique via Traffic Manager, en garantissant un utilitaire cloud flexible et scalable en cas de charge.
