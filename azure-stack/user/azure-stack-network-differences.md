---
title: Différences et considérations concernant la mise en réseau Azure Stack | Microsoft Docs
description: Découvrez les différences et les éléments à prendre en compte lors de l’utilisation de la mise en réseau dans Azure Stack.
services: azure-stack
keywords: ''
author: mattbriggs
manager: femila
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-stack
ms.author: mabrigg
ms.reviewer: scottnap
ms.lastreviewed: 06/04/2019
ms.openlocfilehash: de39645d1414fc852b98036e7071befcf5262cb1
ms.sourcegitcommit: cf9440cd2c76cc6a45b89aeead7b02a681c4628a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/03/2019
ms.locfileid: "66469179"
---
# <a name="considerations-for-azure-stack-networking"></a>Considérations relatives à la mise en réseau Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

La gestion réseau Azure Stack dispose d’un grand nombre des fonctionnalités fournies par la gestion réseau Azure. Toutefois, il existe des différences fondamentales que vous devez connaître avant de déployer un réseau Azure Stack.

Cet article fournit une vue d’ensemble de considérations propres à la gestion réseau Azure Stack et ses fonctionnalités. Pour en savoir plus sur les principales différences entre Azure Stack et Azure, consultez l’article [Principales considérations](azure-stack-considerations.md).

## <a name="cheat-sheet-networking-differences"></a>Aide-mémoire : Différences de mise en réseau

| de diffusion en continu | Fonctionnalité | Azure (global) | Azure Stack |
|--------------------------|----------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| DNS | Système DNS multilocataire | Pris en charge | Pas encore pris en charge |
|  | Enregistrements AAAA DNS | Pris en charge | Non pris en charge |
|  | Zones DNS par abonnement | 100 (par défaut)<br>Peut être augmenté à la demande. | 100 |
|  | Jeux d’enregistrements DNS par zone | 5000 (par défaut)<br>Peut être augmenté à la demande. | 5 000 |
|  | Serveurs de noms pour la délégation de zone | Azure fournit quatre serveurs de noms pour chaque zone utilisateur (locataire) créée. | Azure Stack fournit deux serveurs de noms pour chaque zone utilisateur (locataire) créée. |
| Réseau virtuel | Homologation de réseaux virtuels | Connecter deux réseaux virtuels situés dans la même région par le biais du réseau principal Azure. | Pas encore pris en charge |
|  | Adresses IPv6 | Vous pouvez affecter une adresse IPv6 dans le cadre de la [Configuration de l’interface réseau](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-addresses#ip-address-versions). | Seul le protocole IPv4 est pris en charge. |
|  | Plan de protection DDoS | Pris en charge | Pas encore pris en charge. |
|  | Mise à l’échelle de configurations d’adresses IP définies | Pris en charge | Pas encore pris en charge. |
|  | Services d’accès privé (sous-réseau) | Pris en charge | Pas encore pris en charge. |
|  | Points de terminaison de service | Prise en charge d’une connexion interne (non Internet) vers les services Azure. | Pas encore pris en charge. |
|  | Stratégies de points de terminaison de service | Pris en charge | Pas encore pris en charge. |
|  | Tunnels de service | Pris en charge | Pas encore pris en charge.  |
| Network Security Group | Règles de sécurité augmentée | Pris en charge | Pas encore pris en charge. |
|  | Règles de sécurité effectives | Pris en charge | Pas encore pris en charge. |
|  | Groupes de sécurité d’application | Pris en charge | Pas encore pris en charge. |
| Passerelles de réseau virtuel | Passerelle VPN de point à site | Pris en charge | Pas encore pris en charge. |
|  | Passerelle de réseau virtuel à réseau virtuel | Pris en charge | Pas encore pris en charge. |
|  | Type de passerelle de réseau virtuel | Azure prend en charge VPN<br> ExpressRoute <br> Hyper Net | Azure Stack prend uniquement en charge le type VPN pour l’instant. |
|  | SKU de passerelle de réseau virtuel | Prise en charge de Basic, GW1, GW2, GW3, Standard High Performance, Ultra-High Performance. | Prise en charge des SKU Basic, Standard et High-Performance. |
|  | Type de VPN | Azure prend en charge les types Policy-based (basé sur la stratégie) et Route-based (basé sur l’itinéraire). | Azure Stack prend uniquement en charge Route-based. |
|  | Paramètres BGP | Azure prend en charge la configuration de l’adresse d’homologation BGP et le poids des pairs. | L’adresse d’homologation BGP et le poids des pairs sont configurés automatiquement dans Azure Stack. Il n’existe aucun moyen pour l’utilisateur de configurer ces paramètres avec ses propres valeurs. |
|  | Site de passerelle par défaut | Azure prend en charge la configuration d’un site par défaut pour le tunneling forcé. | Pas encore pris en charge. |
|  | Redimensionnement de passerelle | Azure prend en charge le redimensionnement de la passerelle après le déploiement. | Redimensionnement non pris en charge. |
|  | Configuration active/active | Pris en charge | Pas encore pris en charge. |
|  | UsePolicyBasedTrafficSelectors | Azure prend en charge les sélecteurs de trafic basés sur la stratégie (Policy-based) avec des connexions de passerelle basées sur l’itinéraire (Route-based). | Pas encore pris en charge. |
| Équilibrage de charge | SKU | Les équilibreurs de charge de base et standard sont pris en charge | Seul l’équilibreur de charge de base est pris en charge.  Cette propriété de référence SKU n'est pas prise en charge. |
|  | Zones | Les zones de disponibilité sont prises en charge. | Pas encore pris en charge |
|  | Prise en charge des règles NAT pour les points de terminaison de service | Azure prend en charge les points de terminaison de service pour les règles NAT entrantes. | Azure Stack ne prenant pas encore en charge les points de terminaison de service, ces éléments ne peuvent pas être spécifiés. |
|  | Protocole | Azure prend en charge la spécification GRE ou ESP. | La classe de protocole n’est pas prise en charge dans Azure Stack. |
| Adresse IP publique | Version de l’adresse IP publique | Azure prend en charge IPv6 et IPv4 | Seul le protocole IPv4 est pris en charge. |
| Interface réseau | Obtenir la table de routage effective | Pris en charge | Pas encore pris en charge. |
|  | Obtenir les ACL effectives | Pris en charge | Pas encore pris en charge. |
|  | Activer la mise en réseau accélérée | Pris en charge | Pas encore pris en charge. |
|  | transfert IP | Désactivé par défaut.  Peut être activé. | La modification de ce paramètre n’est pas prise en charge.  Activé par défaut. |
|  | Groupes de sécurité d’application | Pris en charge | Pas encore pris en charge. |
|  | Étiquette du nom DNS interne | Pris en charge | Pas encore pris en charge. |
|  | Version d’adresse IP privée | Les adresses IPv6 et IPv4 sont prises en charge. | Seul le protocole IPv4 est pris en charge. |
|  | Adresse MAC statique | Non pris en charge | Non pris en charge. Chaque système Azure Stack utilise le même pool d’adresses MAC. |
| Network Watcher | Fonctionnalités de surveillance réseau de locataire Network Watcher | Pris en charge | Pas encore pris en charge. |
| CDN | Profils de réseau de distribution de contenu (CDN) | Pris en charge | Pas encore pris en charge. |
| passerelle d’application | Équilibrage de charge de couche 7 | Pris en charge | Pas encore pris en charge. |
| Traffic Manager | Routage du trafic entrant pour une fiabilité et des performances d’application optimales. | Pris en charge | Pas encore pris en charge. |
| ExpressRoute | Configurer une connexion privée rapide aux services cloud Microsoft à partir de votre infrastructure locale ou d’une installation de colocalisation. | Pris en charge | Prise en charge de la connexion d’Azure Stack à un circuit Express Route. |

## <a name="next-steps"></a>Étapes suivantes

[DNS dans Azure Stack](azure-stack-dns.md)
