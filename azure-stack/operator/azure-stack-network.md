---
title: Considérations relatives à l’intégration au réseau pour les systèmes intégrés Azure Stack | Microsoft Docs
description: Découvrez ce que vous pouvez faire pour planifier l’intégration d’un système Azure Stack à plusieurs nœuds au réseau du centre de données.
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
ms.date: 02/12/2019
ms.author: mabrigg
ms.reviewer: wamota
ms.lastreviewed: 08/30/2018
ms.openlocfilehash: a839faa7ec5a93a506ad967f3449ee1788f1a21a
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618500"
---
# <a name="network-connectivity"></a>Connectivité réseau
Cet article fournit des informations sur l’infrastructure réseau d’Azure Stack qui vous aideront à déterminer la meilleure intégration possible d’Azure Stack dans votre environnement réseau existant. 

> [!NOTE]
> Pour résoudre les noms DNS externes d’Azure Stack (par exemple, www\.bing.com), vous devez fournir des serveurs DNS pour transférer les requêtes DNS. Pour plus d’informations sur les exigences d’Azure Stack relatives à DNS, consultez [Intégration d’Azure Stack au centre de données - DNS](azure-stack-integrate-dns.md).

## <a name="physical-network-design"></a>Conception du réseau physique
La solution Azure Stack requiert une infrastructure physique robuste et hautement disponible pour prendre en charge ses opérations et services. Les liaisons montantes entre ToR et les commutateurs de bordure sont limitées à un média SFP + ou SFP28 et une vitesse de 1 Go, 10 Go ou 25 Go. Vérifiez la disponibilité auprès de votre fournisseur de matériel OEM. Le diagramme suivant représente notre conception recommandée :

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
L’infrastructure réseau pour Azure Stack se compose de plusieurs réseaux logiques configurés sur les commutateurs. Le diagramme suivant illustre ces réseaux logiques, et la façon dont ils s’intègrent avec les commutateurs Top-of-Rack (TOR), Baseboard Management Controller (BMC) et de bordure (réseau clients).

![Diagramme de réseau logique et connexions de commutateurs](media/azure-stack-network/NetworkDiagram.png)

### <a name="bmc-network"></a>Réseau BMC
Ce réseau est conçu pour connecter tous les contrôleurs de gestion de carte de base (également appelés processeurs de service ; par exemple, iDRAC, iLO, iBMC, etc.) au réseau de gestion. S’il est présent, le Hardware Lifecycle Host (HLH) se trouve sur ce réseau et peut fournir des logiciels spécifiques aux OEM pour la maintenance ou l’analyse du matériel. 

Le HLH héberge également le déploiement de machines virtuelles (DVM). Le DVM est utilisé au cours du déploiement Azure Stack et est supprimé lorsque le déploiement est terminé. Le DVM requiert un accès internet dans des scénarios de déploiement connecté pour tester, valider et accéder à plusieurs composants. Ces composants peuvent être à l’intérieur et en dehors de votre réseau d’entreprise ; par exemple NTP, DNS et Azure. Pour plus d’informations sur les exigences de connectivité, consultez la [section NAT dans l’intégration du pare-feu Azure Stack](azure-stack-firewall.md#network-address-translation). 

### <a name="private-network"></a>Réseau privé
Ce réseau /24 (adresses IP hôte 254) est privé pour la région Azure Stack (ne développez pas au-delà des appareils de commutation limite de la région Azure Stack) et est divisé en deux sous-réseaux :

- **Réseau de stockage**. Réseau /25 (adresse IP hôte 126) utilisé pour prendre en charge l’utilisation du trafic de stockage Spaces Direct et Server Message Block (SMB) et la migration dynamique de machine virtuelle. 
- **Réseau IP virtuel interne**. Réseau /25 dédié aux adresses IP virtuelles internes uniquement pour l’équilibrage de charge logicielle.

### <a name="azure-stack-infrastructure-network"></a>Réseau d’infrastructure Azure Stack
Ce réseau /24 est dédié aux composants Azure Stack internes afin qu’ils puissent communiquer et échanger des données entre eux. Ce sous-réseau nécessite des adresses IP routables, mais reste privé pour la solution à l’aide de listes de contrôle d’accès. Il n’est pas censé être acheminé au-delà des commutateurs frontière, à l’exception d’une petite plage équivalant en taille à un réseau /27 utilisé par certains de ces services quand ils nécessitent un accès à des ressources externes et/ou à Internet. 

### <a name="public-vip-network"></a>Réseau d’adresse IP virtuelle publique
Le réseau d’adresse IP virtuelle publique est affecté au contrôleur réseau dans Azure Stack. Il ne s’agit pas d’un réseau logique sur le commutateur. Le SLB utilise le pool d’adresses et assigne des réseaux /32 pour les charges de travail clientes. Dans la table de routage du commutateur, ces adresses IP 32 sont publiées en tant qu’itinéraire disponible via BGP. Ce réseau contient les adresses IP accessibles en externe ou publiques. L’infrastructure Azure Stack réserve les 31 premières adresses de ce réseau d’adresse IP virtuelle publique tandis que les autres sont utilisées par les machines virtuelles clientes. La taille réseau sur ce sous-réseau peut aller d’un minimum de /26 (64 hôtes) à un maximum de /22 (1022 hôtes). Nous vous recommandons de prévoir pour un réseau /24.

### <a name="switch-infrastructure-network"></a>Réseau d’infrastructure du commutateur
Ce réseau /26 est le sous-réseau contenant des sous-réseaux IP point à point routables /30 (2 adresses IP d’hôte) et les bouclages dédiés aux sous-réseaux /32 pour la gestion du commutateur intrabande et l’ID de routeur BGP. Cette plage d’adresses IP doit être routable en externe de la solution Azure Stack pour votre centre de données. Il peut s’agit d’adresses IP privées ou publiques.

### <a name="switch-management-network"></a>Réseau de gestion du commutateur
Ce réseau /29 (6 adresses IP d’hôte) est dédié à la connexion des ports de gestion des commutateurs. Il autorise un accès hors bande pour le déploiement, la gestion et la résolution des problèmes. Il est calculé à partir du réseau d’infrastructure du commutateur mentionné ci-dessus.

## <a name="publish-azure-stack-services"></a>Publier des services de Azure Stack
Vous devrez rendre les services de Azure Stack disponibles aux utilisateurs en dehors de Azure Stack. Azure Stack définit différents points de terminaison pour ses rôles d’infrastructure. Ces points de terminaison se voient assigner des adresses IP virtuelles du pool d’adresses IP publiques. Une entrée DNS est créée pour chaque point de terminaison dans la zone DNS externe spécifiée au moment du déploiement. Par exemple, le portail utilisateur se voit attribué l’entrée d’hôte DNS portal.*&lt;region>.&lt;fqdn>*.

### <a name="ports-and-urls"></a>Ports et URL
Pour rendre les services de Azure Stack (tels que les portails, Azure Resource Manager, DNS, etc.) disponibles pour les réseaux externes, vous devez autoriser le trafic entrant vers ces points de terminaison pour les URL, les ports et les protocoles spécifiques.
 
Dans un déploiement où un proxy transparent achemine par liaison montante les données à un serveur proxy traditionnel, vous devez autoriser des URL et des ports spécifiques pour les communications [entrantes](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound) et [sortantes](azure-stack-integrate-endpoints.md#ports-and-urls-outbound). Cela comprend les ports et les URL pour l’identité, la marketplace, les correctifs et les mises à jour, l’inscription ainsi que les données d’utilisation.

## <a name="next-steps"></a>Étapes suivantes
[Connectivité de la frontière](azure-stack-border-connectivity.md)
