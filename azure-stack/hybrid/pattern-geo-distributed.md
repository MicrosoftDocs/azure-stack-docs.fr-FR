---
title: Modèle d’application géodistribuée pour la périphérie intelligente utilisant Azure et Azure Stack Hub.
description: En savoir plus sur le modèle d’application géodistribuée pour la périphérie intelligente utilisant Azure et Azure Stack Hub.
author: BryanLa
ms.service: azure-stack
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: e1afb19c1ff7c32bbc15e40ce4fbe898be01ca0e
ms.sourcegitcommit: 5c92a669007ab4aaffe4484f1d8836a40340dde1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73640136"
---
# <a name="geo-distributed-pattern"></a>Modèle géodistribué

Découvrez comment fournir des points de terminaison d’application dans plusieurs régions et acheminer le trafic utilisateur en fonction de l’emplacement et des besoins de conformité.

## <a name="context-and-problem"></a>Contexte et problème

Les organisations situées dans des régions géographiques éloignées s’efforcent de distribuer et d’activer l’accès aux données de manière sécurisée et précise, tout en garantissant les niveaux de sécurité, de conformité et de performance nécessaires en fonction des utilisateurs, des emplacements et des appareils, indépendamment des frontières.

## <a name="solution"></a>Solution

Le modèle de routage de trafic géographique Azure Stack Hub (ou applications géodistribuées) permet de diriger le trafic vers des points de terminaison spécifiques en fonction de diverses métriques. La création d’un profil Traffic Manager avec configuration du routage et du point de terminaison en fonction de la région géographique permet de router le trafic vers les points de terminaison en fonction des exigences régionales, des réglementations organisationnelles et internationales et de vos besoins relatifs aux données.

![Modèle géodistribué](media/pattern-geo-distributed/geo-distribution.png)

## <a name="components"></a>Composants

**Traffic Manager**  
Dans le schéma, Traffic Manager se situe en dehors du cloud public, mais il doit pouvoir coordonner le trafic dans le centre de données local et dans le cloud public. L’équilibreur route le trafic vers des emplacements géographiques.

**DNS (Domain Name System)**  
Le DNS (Domain Name System) se charge de traduire (ou résoudre) un nom de site web ou de service en une adresse IP.

### <a name="public-cloud"></a>Cloud public

**Point de terminaison cloud**  
Les adresses IP publiques sont utilisées pour router le trafic entrant via Traffic Manager vers le point de terminaison des ressources d’application du cloud public.  

### <a name="local-clouds"></a>Clouds locaux

**Point de terminaison local**  
Les adresses IP publiques sont utilisées pour router le trafic entrant via Traffic Manager vers le point de terminaison des ressources d’application du cloud public.

## <a name="issues-and-considerations"></a>Problèmes et considérations

Prenez en compte les points suivants lorsque vous choisissez comment implémenter ce modèle :

### <a name="scalability"></a>Extensibilité

Le modèle gère le routage du trafic géographique plutôt que la mise à l’échelle pour répondre aux hausses du trafic. Toutefois, vous pouvez combiner ce modèle avec d’autres solutions Azure et locales. Par exemple, vous pouvez utiliser ce modèle avec le modèle de mise à l’échelle multicloud.

### <a name="availability"></a>Disponibilité

Vérifiez que les applications déployées en local sont configurées pour la haute disponibilité via la configuration matérielle locale et le déploiement de logiciels.

### <a name="manageability"></a>Facilité de gestion

Le modèle garantit une gestion transparente et l’accès à une interface familière entre les environnements.

## <a name="when-to-use-this-pattern"></a>Quand utiliser ce modèle

- Mon organisation a des filiales internationales qui imposent des stratégies de sécurité et de distribution régionales personnalisées.
- Chaque filiale de mon organisation extrait des données sur les employés, l’entreprise et les installations, ce qui nécessite la création de rapports d’activité en fonction de la réglementation locale et du fuseau horaire.
- Les exigences à grande échelle peuvent être satisfaites par la mise à l’échelle horizontale des applications, avec plusieurs déploiements dans une seule ou plusieurs régions, pour gérer les charges extrêmes.
- Les applications doivent être hautement disponibles et répondre aux requêtes des clients, même en cas de panne dans une seule région.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les sujets abordés dans cet article :
- Pour en savoir plus sur le fonctionnement de cet équilibreur de charge du trafic basé sur DNS, consultez la [présentation d’Azure Traffic Manager](/azure/traffic-manager/traffic-manager-overview).
- Consultez [Considérations relatives à la conception des applications hybrides](overview-app-design-considerations.md) pour en savoir plus sur les meilleures pratiques, et pour obtenir les réponses à d’autres questions.
- Pour plus d’informations sur l’ensemble du portefeuille de produits et de solutions, consultez [Famille de produits et de solutions d’Azure Stack ](/azure-stack).

Lorsque vous êtes prêt à tester l’exemple de solution, poursuivez avec le [Guide de déploiement d’une solution d’application géodistribuée](solution-deployment-guide-geo-distributed.md). Ce guide de déploiement fournit des instructions pas à pas sur le déploiement et sur le test de ses composants. Découvrez comment diriger le trafic vers des points de terminaison spécifiques en fonction de différentes métriques à l’aide du modèle d’application géodistribuée. En créant un profil Traffic Manager avec configuration du routage et du point de terminaison basée sur la géolocalisation, vous êtes certain que les informations sont dirigées vers les points de terminaison en fonction des exigences régionales, des réglementations organisationnelles et internationales et de vos besoins en matière de données.