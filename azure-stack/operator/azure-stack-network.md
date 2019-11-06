---
title: Planification de l’intégration réseau pour Azure Stack | Microsoft Docs
description: Découvrez comment planifier l’intégration du centre de données avec des systèmes intégrés Azure Stack.
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
ms.date: 10/07/2019
ms.author: mabrigg
ms.reviewer: wamota
ms.lastreviewed: 06/04/2019
ms.openlocfilehash: 4894fb7184944095d968d08e2d668912a78119d4
ms.sourcegitcommit: ef7efcde76d1d7875ca1c882afebfd6a27f1c686
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72888050"
---
# <a name="network-integration-planning-for-azure-stack"></a>Planification de l’intégration réseau pour Azure Stack

Cet article fournit des informations sur l’infrastructure réseau d’Azure Stack qui vous aideront à déterminer la meilleure intégration possible d’Azure Stack dans votre environnement réseau existant. 

> [!NOTE]
> Pour résoudre les noms DNS externes d’Azure Stack (par exemple, www\.bing.com), vous devez fournir des serveurs DNS pour transférer les requêtes DNS. Pour plus d’informations sur les exigences d’Azure Stack relatives à DNS, consultez [Intégration d’Azure Stack au centre de données - DNS](azure-stack-integrate-dns.md).

## <a name="physical-network-design"></a>Conception du réseau physique

La solution Azure Stack requiert une infrastructure physique robuste et hautement disponible pour prendre en charge ses opérations et services. Les liaisons montantes entre ToR et les commutateurs de bordure sont limitées à un média SFP + ou SFP28 et une vitesse de 1 Go, 10 Go ou 25 Go. Vérifiez la disponibilité auprès de votre fournisseur de matériel OEM. Le diagramme suivant représente notre conception recommandée :

![Conception de réseau Azure Stack recommandée](media/azure-stack-network/recommended-design.png)


## <a name="logical-networks"></a>Réseaux logiques

Les réseaux logiques représentent une abstraction de l’infrastructure réseau physique sous-jacente. Ils sont utilisés pour organiser et simplifier les affectations réseau pour les hôtes, les machines virtuelles et les services. Lors de la création d’un réseau logique, des sites réseau sont créés pour définir les réseaux locaux virtuels (VLAN), les sous-réseaux IP et les paires sous-réseau IP/VLAN qui sont associés au réseau logique dans chaque emplacement physique.

Le tableau suivant montre les réseaux logiques et les plages de sous-réseau IPv4 associées à prendre en compte dans le cadre de la planification :

| Réseau logique | Description | Taille | 
| -------- | ------------- | ------------ | 
| Adresse IP virtuelle publique | Azure Stack utilise un total de 31 adresses de ce réseau. Huit adresses IP publiques sont utilisées pour un petit ensemble de services Azure Stack, et les adresses restantes sont utilisées par les machines virtuelles de locataire. Si vous envisagez d’utiliser App Service et les fournisseurs de ressources SQL, 7 adresses supplémentaires sont utilisées. Les 15 adresses IP restantes sont réservées pour les futurs services Azure. | / 26 (62 hôtes) - /22 (1022 hôtes)<br><br>Recommandé = /24 (254 hôtes) | 
| Infrastructure du commutateur | Adresses IP de point à point pour le routage, les interfaces de gestion de commutateur dédiées et les adresses de bouclage attribuées au commutateur. | /26 | 
| Infrastructure | Utilisé pour les composants internes de Azure Stack pour communiquer. | /24 |
| Privé | Utilisé pour le réseau de stockage et les adresses IP virtuelles privées. | /24 | 
| BMC | Utilisé pour communiquer avec les contrôleurs BMC sur les hôtes physiques. | /26 | 
| | | |

## <a name="network-infrastructure"></a>Infrastructure réseau

L’infrastructure réseau pour Azure Stack se compose de plusieurs réseaux logiques configurés sur les commutateurs. Le diagramme suivant illustre ces réseaux logiques et la façon dont ils s’intègrent avec les commutateurs Top-of-Rack (TOR), Baseboard Management Controller (BMC) et de bordure (réseau clients).

![Diagramme de réseau logique et connexions de commutateurs](media/azure-stack-network/NetworkDiagram.png)

### <a name="bmc-network"></a>Réseau BMC

Ce réseau est conçu pour connecter tous les contrôleurs de gestion de carte de base (également appelés BMC ou processeurs de service) au réseau de gestion, par exemple iDRAC, iLO, iBMC, etc. Un seul compte BMC est utilisé pour communiquer avec n’importe quel nœud BMC. S’il est présent, le Hardware Lifecycle Host (HLH) se trouve sur ce réseau et peut fournir des logiciels spécifiques aux OEM pour la maintenance ou l’analyse du matériel.

Le HLH héberge également le déploiement de machines virtuelles (DVM). Le DVM est utilisé au cours du déploiement Azure Stack et est supprimé lorsque le déploiement est terminé. Le DVM requiert un accès internet dans des scénarios de déploiement connecté pour tester, valider et accéder à plusieurs composants. Ces composants peuvent être à l’intérieur et en dehors de votre réseau d’entreprise ; par exemple : NTP, DNS et Azure). Pour plus d’informations sur les exigences de connectivité, consultez la [section NAT dans l’intégration du pare-feu Azure Stack](azure-stack-firewall.md#network-address-translation).

### <a name="private-network"></a>Réseau privé

Ce réseau /24 (adresses IP hôte 254) est privé pour la région Azure Stack (ne développez pas au-delà des appareils de commutation limite de la région Azure Stack) et est divisé en deux sous-réseaux :

- **Réseau de stockage** : Réseau /25 (adresse IP hôte 126) utilisé pour prendre en charge l’utilisation du trafic de stockage Spaces Direct et Server Message Block (SMB) et la migration dynamique de machine virtuelle.
- **Réseau IP virtuel interne** : Réseau /25 dédié aux adresses IP virtuelles internes uniquement pour l’équilibrage de charge logicielle.

### <a name="azure-stack-infrastructure-network"></a>Réseau d’infrastructure Azure Stack
Ce réseau /24 est dédié aux composants Azure Stack internes afin qu’ils puissent communiquer et échanger des données entre eux. Ce sous-réseau peut être routable à l’extérieur de la solution Azure Stack vers votre centre de données. Nous déconseillons l’utilisation d’adresses IP routables publiques ou Internet sur ce sous-réseau. Ce réseau est publié dans la bordure, mais la plupart de ses adresses IP sont protégées par des listes de contrôle d’accès (ACL). Les adresses IP pour lesquelles l’accès est autorisé se trouvent dans une plage de petite taille équivalente à un réseau/27 et à des services hôtes tels que le [ PEP (Privileged End Point)](azure-stack-privileged-endpoint.md) et le [stockage de sauvegarde Azure Stack](azure-stack-backup-reference.md).

### <a name="public-vip-network"></a>Réseau d’adresse IP virtuelle publique

Le réseau d’adresse IP virtuelle publique est affecté au contrôleur réseau dans Azure Stack. Il ne s’agit pas d’un réseau logique sur le commutateur. Le SLB utilise le pool d’adresses et assigne des réseaux /32 pour les charges de travail clientes. Dans la table de routage du commutateur, ces adresses IP 32 sont publiées en tant qu’itinéraire disponible via BGP. Ce réseau contient les adresses IP accessibles en externe ou publiques. L’infrastructure Azure Stack réserve les 31 premières adresses de ce réseau d’adresse IP virtuelle publique tandis que les autres sont utilisées par les machines virtuelles clientes. La taille réseau sur ce sous-réseau peut aller d’un minimum de /26 (64 hôtes) à un maximum de /22 (1022 hôtes). Nous vous recommandons de prévoir pour un réseau /24.

### <a name="switch-infrastructure-network"></a>Réseau d’infrastructure du commutateur

Ce réseau /26 est le sous-réseau contenant des sous-réseaux IP point à point routables /30 (2 adresses IP d’hôte) et les bouclages dédiés aux sous-réseaux /32 pour la gestion du commutateur intrabande et l’ID de routeur BGP. Cette plage d’adresses IP doit être routable en dehors de la solution Azure Stack pour votre centre de données. Il peut s’agit d’adresses IP privées ou publiques.

### <a name="switch-management-network"></a>Réseau de gestion du commutateur

Ce réseau /29 (6 adresses IP d’hôte) est dédié à la connexion des ports de gestion des commutateurs. Il autorise un accès hors bande pour le déploiement, la gestion et la résolution des problèmes. Il est calculé à partir du réseau d’infrastructure du commutateur mentionné ci-dessus.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la planification du réseau : [Connectivité de la bordure](azure-stack-border-connectivity.md).
