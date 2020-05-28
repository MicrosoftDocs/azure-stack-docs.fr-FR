---
title: Différences propres à la mise en réseau Azure Stack Hub
description: Découvrez les différences et considérations de mise en réseau dans Azure Stack Hub.
author: mattbriggs
ms.date: 5/27/2020
ms.topic: article
ms.author: mabrigg
ms.reviewer: wamota
ms.lastreviewed: 07/10/2019
ms.openlocfilehash: 2a71320a660c7dbf74ad885226bb7fcbfa78b35f
ms.sourcegitcommit: cad40ae88212cc72f40c84a1c88143ea0abb65ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84111384"
---
# <a name="differences-and-considerations-for-azure-stack-hub-networking"></a>Différences et considérations de mise en réseau Azure Stack Hub

La mise en réseau Azure Stack Hub dispose d’un grand nombre des fonctionnalités fournies par la mise en réseau Azure. Toutefois, il existe des différences fondamentales que vous devez connaître avant de déployer un réseau Azure Stack Hub.

Cet article fournit une vue d’ensemble des considérations propres à la mise en réseau Azure Stack Hub et ses fonctionnalités. Pour en savoir plus sur les principales différences entre Azure Stack Hub et Azure, consultez l'article [Principales considérations](azure-stack-considerations.md).

## <a name="cheat-sheet-networking-differences"></a>Aide-mémoire : Différences de mise en réseau

| Service | Fonctionnalité | Azure (global) | Azure Stack Hub |
|--------------------------|----------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| DNS | Système DNS multilocataire | Prise en charge | Pas encore pris en charge |
|  | Enregistrements AAAA DNS | Prise en charge | Non pris en charge |
|  | Zones DNS par abonnement | 100 (par défaut)<br>Peut être augmenté à la demande. | 100 |
|  | Jeux d’enregistrements DNS par zone | 5000 (par défaut)<br>Peut être augmenté à la demande. | 5 000 |
|  | Serveurs de noms pour la délégation de zone | Azure fournit quatre serveurs de noms pour chaque zone utilisateur (locataire) créée. | Azure Stack Hub fournit deux serveurs de noms pour chaque zone utilisateur (locataire) créée. |
| Pare-feu Azure | Service de sécurité réseau | Pare-feu Azure est un service de sécurité réseau informatique géré qui protège vos ressources Réseau virtuel Azure. | Pas encore pris en charge. |
| Réseau virtuel | Peering de réseau virtuel | Connecter deux réseaux virtuels situés dans la même région par le biais du réseau principal Azure. | Pas encore pris en charge |
|  | Adresses IPv6 | Vous pouvez affecter une adresse IPv6 dans le cadre de la [Configuration de l’interface réseau](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-addresses#ip-address-versions). | Seul le protocole IPv4 est pris en charge. |
|  | Plan de protection DDoS | Prise en charge | Pas encore pris en charge. |
|  | Mise à l’échelle de configurations d’adresses IP définies | Prise en charge | Pas encore pris en charge. |
|  | Services d’accès privé (sous-réseau) | Prise en charge | Pas encore pris en charge. |
|  | Points de terminaison de service | Prise en charge d’une connexion interne (non Internet) vers les services Azure. | Pas encore pris en charge. |
|  | Stratégies de points de terminaison de service | Prise en charge | Pas encore pris en charge. |
|  | Tunnels de service | Prise en charge | Pas encore pris en charge.  |
| Network Security Group | Règles de sécurité augmentée | Prise en charge | Pris en charge. |
|  | Règles de sécurité effectives | Prise en charge | Pas encore pris en charge. |
|  | Groupes de sécurité d’application | Prise en charge | Pas encore pris en charge. |
|  | Protocoles de règle | TCP, UDP, ICMP, n’importe lequel | Uniquement TCP, UDP ou n’importe lequel |
| Passerelles de réseau virtuel | Passerelle VPN de point à site | Prise en charge | Pas encore pris en charge. |
|  | Passerelle de réseau virtuel à réseau virtuel | Prise en charge | Pas encore pris en charge. |
|  | Type de passerelle de réseau virtuel | Azure prend en charge VPN<br> ExpressRoute <br> Hyper Net. | Actuellement, Azure Stack Hub prend uniquement en charge le type VPN. |
|  | SKU de passerelle de réseau virtuel | Prise en charge de Basic, GW1, GW2, GW3, Standard High Performance, Ultra-High Performance. | Prise en charge des SKU Basic, Standard et High-Performance. |
|  | Type de VPN | Azure prend en charge les VPN basés sur des stratégies et ceux basés sur des routes. | Azure Stack Hub prend uniquement en charge les VPN basés sur des itinéraires. |
|  | Paramètres BGP | Azure prend en charge la configuration de l’adresse de peering BGP et le poids des pairs. | L’adresse de peering BGP et le poids des pairs sont configurés automatiquement dans Azure Stack Hub. Il n’existe aucun moyen pour l’utilisateur de configurer ces paramètres avec ses propres valeurs. |
|  | Site de passerelle par défaut | Azure prend en charge la configuration d’un site par défaut pour le tunneling forcé. | Pas encore pris en charge. |
|  | Redimensionnement de passerelle | Azure prend en charge le redimensionnement de la passerelle après le déploiement. | Le redimensionnement n’est pas pris en charge. |
|  | Configuration de la disponibilité | actif/actif | Actif/passif |
|  | UsePolicyBasedTrafficSelectors | Azure prend en charge les sélecteurs de trafic basés sur la stratégie (Policy-based) avec des connexions de passerelle basées sur l’itinéraire (Route-based). | Pas encore pris en charge. |
| Équilibrage de charge | SKU | Les équilibreurs de charge de base et standard sont pris en charge | Seul l’équilibreur de charge de base est pris en charge.<br>Cette propriété de référence SKU n'est pas prise en charge.<br>Le chemin de l’équilibreur de charge de la référence SKU De base ne peut pas avoir plus de 5 configurations IP front-end.  |
|  | Zones | Les zones de disponibilité sont prises en charge. | Pas encore pris en charge |
|  | Prise en charge des règles NAT pour les points de terminaison de service | Azure prend en charge les points de terminaison de service pour les règles NAT entrantes. | Azure Stack Hub ne prenant pas encore en charge les points de terminaison de service, vous ne pouvez pas les spécifier. |
|  | Protocol | Azure prend en charge la spécification GRE ou ESP. | La classe de protocole n’est pas prise en charge dans Azure Stack Hub. |
| Adresse IP publique | Version de l’adresse IP publique | Azure prend en charge IPv6 et IPv4. | Seul le protocole IPv4 est pris en charge. |
| | SKU | Azure prend en charge les références SKU De base et Standard. | Seule la référence SKU De base est prise en charge. |
| Interface réseau | Obtenir la table de routage effective | Prise en charge | Pas encore pris en charge. |
|  | Obtenir les ACL effectives | Prise en charge | Pas encore pris en charge. |
|  | Activer la mise en réseau accélérée | Prise en charge | Pas encore pris en charge. |
|  | transfert IP | Désactivé par défaut.  Peut être activé. | Vous ne pouvez pas activer/désactiver ce paramètre.  Activé par défaut. |
|  | Groupes de sécurité d’application | Prise en charge | Pas encore pris en charge. |
|  | Étiquette du nom DNS interne | Prise en charge | Pas encore pris en charge. |
|  | Version d’adresse IP privée | Les adresses IPv6 et IPv4 sont prises en charge. | Seul le protocole IPv4 est pris en charge. |
|  | Adresse MAC statique | Non pris en charge | Non pris en charge. Chaque système Azure Stack Hub utilise le même pool d’adresses MAC. |
| Network Watcher | Fonctionnalités de surveillance réseau de locataire Network Watcher | Prise en charge | Pas encore pris en charge. |
| CDN | Profils de réseau de distribution de contenu (CDN) | Prise en charge | Pas encore pris en charge. |
| passerelle d’application | Équilibrage de charge de couche 7 | Prise en charge | Pas encore pris en charge. |
| Traffic Manager | Routage du trafic entrant pour une fiabilité et des performances d’application optimales. | Prise en charge | Pas encore pris en charge. |
| ExpressRoute | Configurer une connexion privée rapide aux services cloud Microsoft à partir de votre infrastructure locale ou d’une installation de colocalisation. | Prise en charge | Prise en charge de la connexion d’Azure Stack Hub à un circuit ExpressRoute. |

## <a name="api-versions"></a>Versions d’API 

La mise en réseau Azure Stack Hub prend en charge les versions d’API suivantes : 

- 2018-11-01
- 2018-10-01
- 2018-08-01
- 01-07-2018
- 2018-06-01
- 2018-05-01
- 2018-04-01
- 2018-03-01
- 2018-02-01
- 2018-01-01
- 2017-11-01
- 2017-10-01

## <a name="next-steps"></a>Étapes suivantes

[DNS dans Azure Stack Hub](azure-stack-dns.md)
