---
title: Modèle DevOps pour la périphérie intelligente avec Azure Stack | Microsoft Docs
description: En savoir plus sur le modèle DevOps pour la périphérie intelligente avec Azure Stack.
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
ms.openlocfilehash: deab520045d50acefb03691b9b127f99676061a0
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72277716"
---
# <a name="geo-distributed-pattern"></a>Modèle géodistribué

Fournissez des points de terminaison d’application dans plusieurs régions. Routez le trafic utilisateur en fonction de l’emplacement et des besoins de conformité.

## <a name="context-and-problem"></a>Contexte et problème

Les organisations situées dans des régions géographiques éloignées s’efforcent de distribuer et d’activer l’accès aux données de manière sécurisée et précise, tout en garantissant les niveaux de sécurité, de conformité et de performance nécessaires en fonction des utilisateurs, des emplacements et des appareils, indépendamment des frontières.

## <a name="solution"></a>Solution

Le modèle de routage de trafic géographique d’Azure Stack (ou applications géodistribuées) permet de diriger le trafic vers des points de terminaison spécifiques en fonction de diverses métriques. La création d’un profil Traffic Manager avec configuration du routage et du point de terminaison en fonction de la région géographique permet de router le trafic vers les points de terminaison en fonction des exigences régionales, des réglementations organisationnelles et internationales et de vos besoins relatifs aux données.

![Modèle géodistribué](media/azure-stack-edge-pattern-geo-distribution/geo-distribution.png)

**Traffic Manager**  
Dans le diagramme, il se situe en dehors du cloud public, mais il doit pouvoir coordonner le trafic dans le centre de données local et dans le cloud public. L’équilibreur route le trafic vers des emplacements géographiques.

**DNS (Domain Name System)**  
Le DNS (Domain Name System) se charge de traduire (ou résoudre) un nom de site web ou de service en une adresse IP.

### <a name="public-cloud"></a>Cloud public

**Point de terminaison cloud**  
Les adresses IP publiques sont utilisées pour router le trafic entrant via Traffic Manager vers le point de terminaison des ressources d’application du cloud public.  

### <a name="local-clouds"></a>Clouds locaux

**Point de terminaison local**  
Les adresses IP publiques sont utilisées pour router le trafic entrant via Traffic Manager vers le point de terminaison des ressources d’application du cloud public.

## <a name="issues-and-considerations"></a>Problèmes et considérations

Prenez en compte les points suivants quand vous choisissez comment implémenter ce modèle :

### <a name="scalability-considerations"></a>Considérations relatives à l’extensibilité

Le modèle gère le routage du trafic géographique plutôt que la mise à l’échelle pour répondre aux hausses du trafic. Toutefois, vous pouvez combiner ce modèle avec d’autres solutions Azure et locales. Par exemple, vous pouvez utiliser ce modèle avec le modèle de mise à l’échelle multicloud.

### <a name="availability-considerations"></a>Considérations relatives à la disponibilité

Vérifiez que les applications déployées en local sont configurées pour la haute disponibilité via la configuration matérielle locale et le déploiement de logiciels.

### <a name="manageability-considerations"></a>Considérations relatives à la facilité de gestion

Le modèle garantit une gestion transparente et l’accès à une interface familière entre les environnements.

## <a name="when-to-use-this-pattern"></a>Quand utiliser ce modèle

-   Mon organisation a des filiales internationales qui imposent des stratégies de sécurité et de distribution régionales personnalisées.

-   Chaque filiale de mon organisation extrait des données sur les employés, l’entreprise et les installations, ce qui nécessite la création de rapports d’activité en fonction de la réglementation locale et du fuseau horaire.

-   Les exigences à grande échelle peuvent être satisfaites par la mise à l’échelle horizontale des applications, avec plusieurs déploiements dans une seule ou plusieurs régions, pour gérer les charges extrêmes.

-   Les applications doivent être hautement disponibles et répondre aux requêtes des clients, même en cas de panne dans une seule région.

## <a name="example"></a>Exemples

Découvrez comment diriger le trafic vers des points de terminaison spécifiques en fonction de différentes mesures à l’aide du modèle d’applications géolocalisées. En créant un profil Traffic Manager avec configuration du routage et du point de terminaison basée sur la géolocalisation, vous êtes certain que les informations sont dirigées vers les points de terminaison en fonction des exigences régionales, des réglementations organisationnelles et internationales et de vos besoins en matière de données.

[Créer une solution d’application géolocalisée avec Azure et Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-geo-distributed)

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les [modèles de conception de clouds hybrides pour Azure Stack](azure-stack-edge-pattern-overview.md)
