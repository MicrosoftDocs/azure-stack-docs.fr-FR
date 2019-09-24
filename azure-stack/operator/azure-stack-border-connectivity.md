---
title: Connectivité frontalière et intégration réseau pour les systèmes intégrés Azure Stack | Microsoft Docs
description: Découvrez comment planifier la connectivité réseau frontalière des centres de données dans les systèmes intégrés Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2019
ms.author: mabrigg
ms.reviewer: wamota
ms.lastreviewed: 08/30/2018
ms.openlocfilehash: 142ea9b53d64e89157ce5c5556241b41275d430d
ms.sourcegitcommit: c196463492732218d2474d3a964f88e995272c80
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71094357"
---
# <a name="border-connectivity"></a>Connectivité de la bordure 
La planification de l’intégration au réseau est un prérequis important pour réussir le déploiement, l’exploitation et la gestion de systèmes intégrés Azure Stack. Pour commencer la planification de la connectivité frontalière, vous devez décider si vous voulez utiliser le routage dynamique avec le protocole BGP (Border Gateway Protocol). Pour cela, vous devez soit affecter un numéro de système autonome BGP 16 bits (public ou privé), soit utiliser un routage statique où une route statique par défaut est affectée aux appareils frontière.

> [!IMPORTANT]
> Les commutateurs TOR (Top Of Rack) nécessitent des liaisons montantes de couche 3 avec des adresses IP point à point (réseaux /30) configurées sur les interfaces physiques. L’utilisation de liaisons montantes de couche 2 avec des commutateurs TOR prenant en charge les opérations Azure Stack n’est pas autorisée.

## <a name="bgp-routing"></a>Routage BGP
L’utilisation d’un protocole de routage dynamique comme BGP garantit que votre système est toujours informé des changements de réseau et facilite l’administration. Pour une sécurité avancée, un mot de passe peut être défini sur le peering BGP entre le commutateur TOR et la frontière.

Comme indiqué dans le diagramme suivant, la publication de l’espace d’adressage IP privé sur le commutateur TOR est bloquée au moyen d’une liste de préfixes. Celle-ci refuse la publication du réseau privé et est appliquée comme carte de routage sur la connexion entre le commutateur TOR et la frontière.

L’équilibreur de charge logiciel (SLB, Software Load Balancer) en cours d’exécution dans la solution Azure Stack est appairé aux appareils TOR. Il peut donc publier dynamiquement les adresses IP virtuelles.

Pour garantir la récupération immédiate et transparente du trafic utilisateur en cas d’échec, le VPC ou MLAG configuré entre les appareils TOR permet l’utilisation de l’agrégation de liaisons multichâssis aux hôtes et de HSRP ou VRRP qui fournit la redondance réseau pour les réseaux IP.

![Routage BGP](media/azure-stack-border-connectivity/bgp-routing.png)

## <a name="static-routing"></a>Routage statique
Le routage statique nécessite une configuration supplémentaire pour les appareils qui se trouvent à la frontière. Il nécessite plus d’intervention manuelle et de gestion, ainsi qu’une analyse minutieuse avant toute modification. En fonction des changements apportés, les problèmes causés par une erreur de configuration peuvent accroître la durée de la restauration. Cette méthode de routage n’est pas recommandée, mais elle est prise en charge.

Pour intégrer Azure Stack dans votre environnement réseau à l’aide d’un routage statique, les quatre liens physiques entre la bordure et l’appareil TOR doivent être connectés. La haute disponibilité ne peut pas être garantie en raison du mode de fonctionnement du routage statique.

L’appareil situé à la limite doit être configuré avec des itinéraires statiques pointant vers le P2Ps des appareils Tor pour le trafic destiné au réseau *externe*, ou aux adresses IP virtuelles publiques et au réseau de l’*infrastructure*. Elle requiert des itinéraires statiques vers les réseaux *BMC* et *externe* pour le déploiement. Les opérateurs peuvent choisir de laisser des itinéraires statiques à la limite pour accéder à des ressources d’administration qui se trouvent sur le réseau *BMC*. L’ajout de routes statiques aux réseaux de *l’infrastructure des commutateurs* et de la *gestion des commutateurs*  est facultatif.

Les appareils TOR sont configurés à l’origine avec une route statique par défaut qui envoie tout le trafic aux appareils situés à la frontière. La seule exception à la règle par défaut concerne l’espace privé, qui est bloqué avec une liste ACL appliquée sur la connexion entre le TOR et la frontière.

Le routage statique s’applique seulement aux liaisons montantes entre le TOR et les commutateurs situés à la frontière. Le routage dynamique BGP est utilisé dans le rack, car il s’agit d’un outil essentiel pour l’équilibreur de charge logicielle (SLB) et pour d’autres composants. Il ne peut être ni désactivé ni supprimé.

![Routage statique](media/azure-stack-border-connectivity/static-routing.png)

<sup>\*</sup> Le réseau BMC est facultatif après le déploiement.

<sup>\*\*</sup> Le réseau d’infrastructure du commutateur est facultatif, car le réseau entier peut être inclus dans le réseau de gestion du commutateur.

<sup>\*\*\*</sup> Le réseau de gestion du commutateur est requis et peut être ajouté séparément à partir du réseau d’infrastructure du commutateur.

## <a name="transparent-proxy"></a>Proxy transparent
Si votre centre de données exige que l’ensemble du trafic utilise un proxy, vous devez configurer un *proxy transparent* pour traiter l’ensemble du trafic du rack afin de le gérer conformément à la stratégie, en séparant le trafic entre les zones de votre réseau.

> [!IMPORTANT]
> La solution Azure Stack ne prend pas en charge les proxies web normaux.  

Un proxy transparent (également appelé proxy d’interception, en ligne ou forcé) intercepte une communication normale sur la couche réseau sans nécessiter une configuration spéciale du client. Les clients ne doivent pas nécessairement être informés de l’existence du proxy.

![Proxy transparent](media/azure-stack-border-connectivity/transparent-proxy.png)

## <a name="next-steps"></a>Étapes suivantes
[Intégration DNS](azure-stack-integrate-dns.md)
