---
title: Connectivité frontalière et intégration réseau pour les systèmes intégrés Azure Stack Hub
description: Apprenez à planifier la connectivité des réseaux frontaliers des centres de données dans les systèmes intégrés Azure Stack Hub.
author: PatAltimore
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: patricka
ms.reviewer: wamota
ms.lastreviewed: 11/15/2019
ms.openlocfilehash: 1377f04a9c746a41ed1965a2798a1dbfd3b0db21
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97871546"
---
# <a name="border-connectivity"></a>Connectivité de la bordure 
La planification de l'intégration réseau est une condition préalable importante pour réussir le déploiement, l'exploitation et la gestion de systèmes intégrés Azure Stack Hub. Pour commencer la planification de la connectivité frontalière, vous devez décider si vous voulez utiliser le routage dynamique avec le protocole BGP (Border Gateway Protocol). Pour cela, vous devez attribuer un numéro de système autonome (NSA) 16 bits, public ou privé, ou utiliser le routage statique.

> [!IMPORTANT]
> Les commutateurs TOR (Top Of Rack) nécessitent des liaisons montantes de couche 3 avec des adresses IP point à point (réseaux /30) configurées sur les interfaces physiques. L'utilisation de liaisons montantes de couche 2 avec des commutateurs TOR prenant en charge les opérations Azure Stack Hub n'est pas autorisée. L’appareil qui se trouve à la frontière peut prendre en charge le numéro de système autonome (NSA) BGP 32 bits.

## <a name="bgp-routing"></a>Routage BGP
L’utilisation d’un protocole de routage dynamique comme BGP garantit que votre système est toujours informé des changements de réseau et facilite l’administration. Pour une sécurité avancée, un mot de passe peut être défini sur le peering BGP entre le commutateur TOR et la frontière.

Comme indiqué dans le diagramme suivant, la publication de l’espace d’adressage IP privé sur le commutateur TOR est bloquée au moyen d’une liste de préfixes. Celle-ci refuse la publication du réseau privé et est appliquée comme carte de routage sur la connexion entre le commutateur TOR et la frontière.

L'équilibreur de charge logiciel (SLB, Software Load Balancer) exécuté au sein de la solution Azure Stack Hub est appairé aux appareils TOR. Il peut donc publier dynamiquement les adresses IP virtuelles.

Pour garantir la récupération immédiate et transparente du trafic utilisateur en cas d’échec, le VPC ou MLAG configuré entre les appareils TOR permet l’utilisation de l’agrégation de liaisons multichâssis aux hôtes et de HSRP ou VRRP qui fournit la redondance réseau pour les réseaux IP.

![Routage BGP](media/azure-stack-border-connectivity/bgp-routing.svg)

## <a name="static-routing"></a>Routage statique
Le routage statique nécessite une configuration supplémentaire pour les appareils qui se trouvent à la frontière. Il nécessite plus d’intervention manuelle et de gestion, ainsi qu’une analyse minutieuse avant toute modification. En fonction des changements apportés, les problèmes causés par une erreur de configuration peuvent accroître la durée de la restauration. Cette méthode de routage n’est pas recommandée, mais elle est prise en charge.

Pour intégrer Azure Stack Hub dans votre environnement réseau à l'aide d'un routage statique, les quatre liaisons physiques entre la frontière et l'appareil TOR doivent être connectées. La haute disponibilité ne peut pas être garantie en raison du mode de fonctionnement du routage statique.

L'appareil situé à la frontière doit être configuré avec des itinéraires statiques pointant vers les quatre adresses IP P2P définies entre le TOR et la frontière pour le trafic destiné aux différents réseaux au sein d'Azure Stack Hub, mais seul le réseau *externe* ou le réseau d'adresse IP virtuelle publique est requis pour l'opération. Des itinéraires statiques vers les réseaux *BMC* et *externe* sont requis pour le déploiement initial. Les opérateurs peuvent choisir de laisser des itinéraires statiques à la frontière pour accéder à des ressources d’administration qui se trouvent sur le réseau *BMC* et le réseau de l'*infrastructure*. L’ajout de routes statiques aux réseaux de *l’infrastructure des commutateurs* et de la *gestion des commutateurs*  est facultatif.

Les appareils TOR sont configurés avec un itinéraire statique par défaut qui envoie tout le trafic vers les appareils situés à la frontière. La seule exception à la règle par défaut concerne l’espace privé, qui est bloqué avec une liste ACL appliquée sur la connexion entre le TOR et la frontière.

Le routage statique s’applique seulement aux liaisons montantes entre le TOR et les commutateurs situés à la frontière. Le routage dynamique BGP est utilisé dans le rack, car il s’agit d’un outil essentiel pour l’équilibreur de charge logicielle (SLB) et pour d’autres composants. Il ne peut être ni désactivé ni supprimé.

![Routage statique](media/azure-stack-border-connectivity/static-routing.svg)

<sup>\*</sup> Le réseau BMC est facultatif après le déploiement.

<sup>\*\*</sup> Le réseau d’infrastructure du commutateur est facultatif, car le réseau entier peut être inclus dans le réseau de gestion du commutateur.

<sup>\*\*\*</sup> Le réseau de gestion du commutateur est requis et peut être ajouté séparément à partir du réseau d’infrastructure du commutateur.

## <a name="transparent-proxy"></a>Proxy transparent
Si votre centre de données exige que l’ensemble du trafic utilise un proxy, vous devez configurer un *proxy transparent* pour traiter l’ensemble du trafic du rack afin de le gérer conformément à la stratégie, en séparant le trafic entre les zones de votre réseau.

> [!IMPORTANT]
> La solution Azure Stack Hub ne prend pas en charge les proxies web normaux.  

Un proxy transparent (également appelé proxy d’interception, en ligne ou forcé) intercepte une communication normale sur la couche réseau sans nécessiter une configuration spéciale du client. Les clients ne doivent pas nécessairement être informés de l’existence du proxy.

![Proxy transparent](media/azure-stack-border-connectivity/transparent-proxy.svg)

L’interception du trafic SSL n’est [pas prise en charge](azure-stack-firewall.md#ssl-interception) et peut entraîner des échecs de service lors de l’accès aux points de terminaison. Le délai d’expiration maximal pris en charge pour communiquer avec les points de terminaison requis pour l’identité est de 60 secondes. 3 tentatives sont autorisées.

## <a name="next-steps"></a>Étapes suivantes
[Intégration DNS](azure-stack-integrate-dns.md)
