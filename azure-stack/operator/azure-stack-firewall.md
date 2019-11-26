---
title: Intégration d’Azure Stack à un pare-feu pour les systèmes intégrés Azure Stack | Microsoft Docs
description: Découvrez l’intégration d’Azure Stack à un pare-feu pour les systèmes intégrés Azure Stack.
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
ms.date: 11/15/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 11/15/2019
ms.openlocfilehash: c2b6144311ce8f4309fdb968a500f6850080f309
ms.sourcegitcommit: f2a059f1be36f82adea8877f3f6e90d41ef3b161
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74162964"
---
# <a name="azure-stack-firewall-integration"></a>Intégration d’Azure Stack à un pare-feu
Nous vous recommandons d’utiliser un dispositif de pare-feu pour sécuriser Azure Stack. Les pare-feu peuvent contribuer à la défense contre les attaques par déni de service distribué (DDoS), à la détection des intrusions et à l'inspection de contenu. Mais ils peuvent également se transformer en goulot d'étranglement pour les services de stockage Azure (objets blobs, tables, files d'attente, etc.).

 Si un mode de déploiement déconnecté est utilisé, vous devez publier le point de terminaison AD FS. Pour plus d’informations, consultez [l’article sur l’identité de l’intégration au centre de données](azure-stack-integrate-identity.md).

Les points de terminaison Azure Resource Manager (administrateur), portail administrateur et Key Vault (administrateur) ne nécessitent aucune publication externe. Par exemple, en tant que fournisseur de services, vous pouvez limiter la surface d'attaque en vous contentant d'administrer Azure Stack depuis votre réseau, et non à partir d'Internet.

Pour les entreprises, le réseau externe peut être le réseau d’entreprise existant. Dans ce scénario, vous devez publier les points de terminaison pour exécuter Azure Stack à partir du réseau d'entreprise.

### <a name="network-address-translation"></a>Traduction d’adresses réseau
La traduction d’adresses réseau (NAT, Network Address Translation) est la méthode recommandée pour autoriser la machine virtuelle de déploiement (DVM, Deployment Virtual Machine) à accéder, d’une part, aux ressources externes et à Internet pendant le déploiement et, d’autre part, aux machines virtuelles ERCS (Emergency Recovery Console) ou au PEP (Privileged End Point) pendant l’inscription et le dépannage.

Vous pouvez également utiliser NAT à la place des adresses IP publiques sur le réseau externe ou des adresses IP virtuelles publiques. Toutefois, ceci n’est pas recommandé car NAT limite l’expérience utilisateur du locataire et accroît la complexité. Une option serait NAT 1:1, qui nécessite toujours une adresse IP publique par adresse IP d'utilisateur sur le pool. Une autre option serait NAT plusieurs:1, qui nécessite une règle NAT par adresse IP virtuelle d'utilisateur pour tous les ports qu'un utilisateur pourrait utiliser.

L’utilisation de NAT pour une adresse IP virtuelle publique présente des inconvénients. En voici quelques-uns :
- NAT ajoute une surcharge lors de la gestion des règles de pare-feu car les utilisateurs contrôlent leurs propres points de terminaison et leurs propres règles de publication dans la pile de mise en réseau logicielle (SDN). Les utilisateurs doivent contacter l’opérateur Azure Stack pour que leurs adresses IP virtuelles soient publiées et pour mettre à jour la liste des ports.
- Bien que l’utilisation de NAT limite l’expérience de l’utilisateur, elle donne à l’opérateur un contrôle total sur les demandes de publication.
- Pour les scénarios de cloud hybride avec Azure, tenez compte du fait qu’Azure ne prend pas en charge la configuration d’un tunnel VPN vers un point de terminaison à l’aide de NAT.

### <a name="ssl-interception"></a>Interception SSL
Actuellement, il est recommandé de désactiver tout type d’interception SSL (par exemple, pour le déplacement du processus de déchiffrement) sur l’ensemble du trafic Azure Stack. Si l’interception SSL venait à être prise en charge dans les futures mises à jour, nous fournirions des conseils sur la façon de l’activer pour Azure Stack.

## <a name="edge-firewall-scenario"></a>Scénario de pare-feu de périmètre
Dans un déploiement de périphérie, Azure Stack est déployé directement derrière le pare-feu ou le routeur de périphérie. Dans ces scénarios, le pare-feu peut se situer au-dessus de la frontière (scénario 1) s’il prend en charge les configurations de pare-feu actif-actif et actif-passif ou en agissant en tant qu’appareil frontière (scénario 2) où il prend uniquement en charge les configurations de pare-feu actif-actif avec prise en charge d’ECMP (Equal Cost Multi Path) avec BGP ou le routage statique pour le basculement.

Les adresses IP routables publiques sont spécifiées pour le pool d'adresses IP virtuelles publiques à partir du réseau externe au moment du déploiement. Dans un scénario de périphérie, il n’est pas recommandé d’utiliser des adresses IP routables publiques sur un autre réseau pour des raisons de sécurité. Ce scénario permet à un utilisateur de bénéficier d’une expérience cloud auto-contrôlée complète, comme dans un cloud public tel qu’Azure.  

![Exemple de pare-feu de périphérie Azure Stack](./media/azure-stack-firewall/firewallScenarios.png)

## <a name="enterprise-intranet-or-perimeter-network-firewall-scenario"></a>Scénario de pare-feu réseau de périmètre ou intranet d’entreprise
Dans un déploiement sur un réseau de périmètre ou intranet d’entreprise, Azure Stack est déployé sur un pare-feu multizone ou entre le pare-feu de périphérie et le pare-feu de réseau d’entreprise interne. Le trafic est ensuite distribué entre le réseau de périmètre sécurisé (ou DMZ) et les zones non sécurisées, comme décrit ci-dessous :

- **Zone sécurisée** : il s’agit du réseau interne qui utilise des adresses IP routables de l’entreprise ou internes. Le réseau sécurisé peut être divisé et avoir un accès sortant à internet par le biais de NAT sur le pare-feu. Vous pouvez généralement y accéder à partir de n’importe quel emplacement à l’intérieur de votre centre de données par le biais du réseau interne. Tous les réseaux Azure Stack doivent résider dans la zone sécurisée, à l’exception du pool d’adresses IP virtuelles publiques du réseau externe.
- **Zone du périmètre**. Les applications externes ou accessibles sur Internet comme les serveurs web sont généralement déployées sur le réseau de périmètre. Celui-ci est habituellement supervisé par un pare-feu pour éviter les attaques de type DDoS et les intrusions (piratage), mais il autorise également le trafic entrant spécifié à partir d’internet. Seul le pool d’adresses IP virtuelles publiques de réseau externe d’Azure Stack doit résider dans la zone DMZ.
- **Zone non sécurisée**. Il s’agit du réseau externe (Internet). Il **n’est pas** recommandé de déployer Azure Stack dans la zone non sécurisée.

![Exemple de réseau de périmètre Azure Stack](./media/azure-stack-firewall/perimeter-network-scenario.png)

## <a name="learn-more"></a>En savoir plus
Découvrez plus en détail [les ports et les protocoles utilisés par les points de terminaison Azure Stack](azure-stack-integrate-endpoints.md).

## <a name="next-steps"></a>Étapes suivantes
[Exigences relatives à l’infrastructure à clé publique d’Azure Stack](azure-stack-pki-certs.md)

