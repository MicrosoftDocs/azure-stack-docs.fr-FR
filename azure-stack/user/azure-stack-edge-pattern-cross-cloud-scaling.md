---
title: Modèle de mise à l’échelle multicloud pour la périphérie intelligente avec Azure Stack | Microsoft Docs
description: En savoir plus sur le modèle de mise à l’échelle multicloud pour la périphérie intelligente avec Azure Stack
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
ms.date: 07/11/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 06/11/2019
ms.openlocfilehash: 2b97be7ecfffe83e560d32fbaac6480c8c17bb88
ms.sourcegitcommit: 51ec68b5e6dbf437aaca19a9f35ba07d2c402892
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67856301"
---
# <a name="cross-cloud-scaling-pattern"></a>Modèle de mise à l’échelle multicloud

Ajoutez automatiquement des ressources à une application existante pour faire face à une hausse de la charge.

## <a name="context-and-problem"></a>Contexte et problème

Votre application ne peut pas augmenter sa capacité pour répondre à une hausse inattendue de la demande. Les utilisateurs ne peuvent donc pas accéder à l’application pendant les pics d’utilisation. L’application peut répondre aux demandes d’un nombre fixe d’utilisateurs.

Les entreprises internationales ont besoin d’applications cloud sécurisées, fiables et disponibles. La réponse à la hausse de la demande et l’utilisation de l’infrastructure appropriée pour prendre en charge cette demande sont des aspects critiques. Les entreprises ont des difficultés à équilibrer les coûts et la maintenance en ce qui concerne la sécurité, le stockage et la disponibilité en temps réel des données métier.

Vous ne pourrez peut-être pas exécuter votre application dans le cloud public. Toutefois, ceci peut ne pas être économiquement faisable pour que l’entreprise puisse maintenir la capacité nécessaire dans son environnement local afin de gérer les pics de demande de l’application. Avec ce modèle, vous pouvez tirer parti de l’élasticité du cloud public pour votre solution locale.

## <a name="solution"></a>Solution

Le modèle de mise à l’échelle multicloud étend une application située dans un cloud local avec les ressources du cloud public. Le modèle se déclenche en réponse à une hausse ou une baisse de la demande, puis ajoute ou supprime respectivement des ressources dans le cloud. Ces ressources assurent une redondance, une disponibilité rapide et un routage géoconforme.

![Modèle de mise à l’échelle multicloud](media/azure-stack-edge-pattern-cross-cloud-scaling/cross-cloud-scaling.png)

> Remarque :  
> Ce modèle s’applique uniquement aux applications sans état.

Le modèle de mise à l’échelle multicloud comprend les composants suivants.

**Traffic Manager**  
**Dans le diagramme, il se situe en dehors du groupe de clouds publics, mais il doit pouvoir coordonner le trafic dans le centre de données local et dans le cloud public. L’équilibreur offre une haute disponibilité pour l’application en supervisant les points de terminaison et en assurant la redistribution du basculement en cas de besoin.

**DNS (Domain Name System)**  
Le DNS (Domain Name System) se charge de traduire (ou résoudre) un nom de site web ou de service en une adresse IP.

### <a name="cloud"></a>Cloud

**Serveur de builds hébergé**  
Environnement d’hébergement de votre pipeline de build.

**Ressources d’application**  
Les ressources d’application doivent pouvoir faire l’objet d’un scale in et d’un scale out, à l’image des groupes de machines virtuelles identiques et des conteneurs.

**Nom de domaine personnalisé**  
Utilisez un nom de domaine personnalisé pour le Glob de routage des requêtes.

**Adresses IP publiques**  
Les adresses IP publiques sont utilisées pour router le trafic entrant via Traffic Manager vers le point de terminaison des ressources d’application du cloud public.  

### <a name="local-cloud"></a>Cloud local

**Serveur de builds hébergé**  
Environnement d’hébergement de votre pipeline de build.

**Ressources d’application**  
Les ressources d’application doivent pouvoir faire l’objet d’un scale in et d’un scale out, à l’image des groupes de machines virtuelles identiques et des conteneurs.

**Nom de domaine personnalisé**  
Utilisez un nom de domaine personnalisé pour le Glob de routage des requêtes.

**Adresses IP publiques**  
Les adresses IP publiques sont utilisées pour router le trafic entrant via Traffic Manager vers le point de terminaison des ressources d’application du cloud public. 

## <a name="issues-and-considerations"></a>Problèmes et considérations


Prenez en compte les points suivants lorsque vous choisissez comment implémenter ce modèle :

### <a name="scalability-considerations"></a>Considérations relatives à l’extensibilité

Le composant clé de la mise à l’échelle multicloud repose sur la capacité à fournir une mise à l’échelle à la demande entre l’infrastructure cloud publique et locale, tout en garantissant un service cohérent et fiable, conformément aux exigences de la demande.

### <a name="availability-considerations"></a>Considérations relatives à la disponibilité

Vérifiez que les applications déployées en local sont configurées pour la haute disponibilité via la configuration matérielle locale et le déploiement de logiciels.

### <a name="manageability-considerations"></a>Considérations relatives à la facilité de gestion

Le modèle multicloud garantit une gestion fluide et l’accès à une interface familière entre les environnements.

#### <a name="when-to-use-this-pattern"></a>Quand utiliser ce modèle

Utilisez ce modèle :

-   Quand vous avez besoin d’augmenter la capacité de votre application pour faire face à des demandes inattendues ou régulières.

-   Quand vous ne souhaitez pas investir dans des ressources qui vont être utilisées uniquement durant les pics d’activité. Payez pour ce que vous utilisez.

Ce modèle n’est pas recommandé quand :

-   Votre solution impose aux utilisateurs de se connecter via Internet

-   Votre entreprise doit se conformer à des réglementations locales qui imposent à la connexion d’origine de provenir d’un appel local

-   Votre réseau est confronté à des goulots d’étranglement réguliers qui limitent les performances de la mise à l’échelle.

-   Votre environnement est déconnecté d’Internet et ne peut pas atteindre le cloud public.

## <a name="example"></a>Exemples

Découvrez comment créer une solution dans le cloud pour fournir un processus déclenché manuellement permettant de passer d’une application web hébergée sur Azure Stack à une application web hébergée sur Azure avec mise à l’échelle automatique via le gestionnaire de trafic, garantissant un utilitaire cloud flexible et évolutif en cas de charge.

[Créer des solutions de mise à l’échelle dans le cloud avec Azure](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-cloud-burst)

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les [modèles de conception de clouds hybrides pour Azure Stack](azure-stack-edge-pattern-overview.md)
