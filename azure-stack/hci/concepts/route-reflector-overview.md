---
title: Vue d’ensemble du réflecteur de route BGP dans Azure Stack HCI
description: Cette rubrique présente le réflecteur de route BGP pour le SDN (Software Defined Network) dans Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/8/2020
ms.openlocfilehash: ca130c60e8cf08484001f606c4d0f84d786ca16b
ms.sourcegitcommit: 1621f2748b2059fd47ccacd48595a597c44ee63f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858974"
---
# <a name="what-is-route-reflector"></a>Qu’est-ce que le réflecteur de route ?

> S’applique à : Azure Stack HCI, version 20H2 ; Windows Server 2019

Le réflecteur de route BGP (Border Gateway Protocol) est compris dans la [passerelle RAS (Remote Access Service)](gateway-overview.md) et fournit une alternative à la topologie à maillage complet BGP qui est nécessaire pour la synchronisation des routes entre les routeurs. Un réflecteur de route dans un déploiement SDN est une entité logique qui se trouve sur le plan de contrôle entre les passerelles RAS et le [contrôleur de réseau](network-controller-overview.md). Toutefois, il ne participe pas au routage du plan de données.

## <a name="how-route-reflector-works"></a>Fonctionnement du réflecteur de route

Avec la synchronisation à maillage complet, tous les routeurs BGP doivent se connecter à tous les autres routeurs de la topologie de routage. Toutefois, quand vous utilisez le réflecteur de route, celui-ci est le seul routeur qui se connecte à tous les autres routeurs, appelés clients du réflecteur de route BGP, ce qui simplifie la synchronisation des routes et réduit le trafic réseau. Le réflecteur de route apprend toutes les routes, calcule les meilleures routes et les redistribue à ses clients BGP.

Vous pouvez configurer les tunnels d’accès à distance d’un locataire individuel pour qu’ils se terminent sur plusieurs machines virtuelles de passerelle RAS. Cela offre une plus grande flexibilité pour les fournisseurs de services cloud (CSP) dans les cas où une machine virtuelle de passerelle RAS ne peut pas satisfaire à toutes les exigences de bande passante des connexions de locataire.

Toutefois, cette fonctionnalité introduit la complexité supplémentaire de la gestion des routes et suppose une synchronisation efficace des routes entre les sites distants de locataire et leurs ressources virtuelles dans le centre de données cloud. En outre, le fait de fournir aux locataires des connexions à plusieurs passerelles RAS introduit une complexité supplémentaire dans la configuration au niveau de l’entreprise, où chaque site locataire a des voisins de routage distincts.

Un réflecteur de route BGP dans le plan de contrôle résout ces problèmes et rend le déploiement de l’infrastructure interne du CSP transparent pour les locataires de l’entreprise.

- Quand vous ajoutez un nouveau locataire à votre centre de données, le contrôleur de réseau configure automatiquement la première passerelle RAS de locataire en tant que réflecteur de route.

- À chaque locataire correspond un réflecteur de route, qui réside sur l’une des machines virtuelles de passerelle RAS associées à ce locataire.

- Un réflecteur de route de locataire fait office de réflecteur de route pour toutes les machines virtuelles de passerelle RAS associées au locataire. Les passerelles de locataire autres que le réflecteur de route de passerelle RAS sont les clients du réflecteur de route. Le réflecteur de route effectue la synchronisation des routes entre tous les clients du réflecteur de route afin que le routage du chemin de données puisse effectivement se produire.

- Un réflecteur de route ne fournit pas de services pour la passerelle RAS sur laquelle il est configuré.

- Un réflecteur de route met à jour le contrôleur de réseau avec les itinéraires d’entreprise qui correspondent aux sites d’entreprise du locataire. Cela permet au contrôleur de réseau de configurer les stratégies de virtualisation de réseau Hyper-V requises sur le réseau virtuel du locataire pour un accès au chemin de données de bout en bout.

- Si les clients de votre entreprise utilisent le routage BGP dans l’espace d’adressage client, le réflecteur de route de la passerelle RAS est le seul voisin BGP externe (eBGP) pour tous les sites du locataire correspondant. C’est vrai, quels que soient les points de terminaison de tunnel du locataire d’entreprise. En d’autres termes, quelle que soit la machine virtuelle de passerelle RAS dans le centre de données CSP qui termine le tunnel VPN de site à site pour un site locataire, le pair eBGP pour tous les sites locataires est le réflecteur de route.

## <a name="next-steps"></a>Étapes suivantes

Pour consulter des informations connexes, reportez-vous également à :

- [Vue d’ensemble de la passerelle RAS](gateway-overview.md)
- [Architecture de déploiement de la passerelle RAS](/windows-server/networking/sdn/technologies/network-function-virtualization/ras-gateway-deployment-architecture)
- [RFC 4456 de l’IETF (Internet Engineering Task Force)  - BGP Route Reflection : An Alternative to Full Mesh Internal BGP](https://tools.ietf.org/html/rfc4456)