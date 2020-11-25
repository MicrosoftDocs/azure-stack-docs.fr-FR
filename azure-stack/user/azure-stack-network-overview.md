---
title: Présentation de la gestion de réseau Azure Stack Hub
description: En savoir plus sur la gestion de réseau Azure Stack Hub
author: mattbriggs
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: mabrigg
ms.reviewer: scottnap
ms.lastreviewed: 01/14/2020
ms.openlocfilehash: 2cb8ede31a91ba05102c55591a4942f512bce0c8
ms.sourcegitcommit: c89d8aa6d07d7aec002b58bd07a7976203aa760b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94674470"
---
# <a name="introduction-to-azure-stack-hub-networking"></a>Présentation de la gestion de réseau Azure Stack Hub

Azure Stack Hub fournit plusieurs types de fonctionnalités de gestion de réseau qui peuvent être utilisées ensemble ou séparément :

- **Connectivité entre les ressources Azure Stack Hub**  
    Connectez les ressources Azure ensemble dans un réseau virtuel privé et sécurisé dans le cloud.
- **Connectivité Internet**  
    Communiquez vers et à partir de ressources Azure Stack Hub sur Internet.
- **Connectivité locale**  
    Connectez un réseau local à des ressources Azure Stack Hub via un réseau privé virtuel (VPN) sur Internet ou une connexion dédiée à Azure Stack Hub. 
    > [!IMPORTANT]
    > Vous devez créer une connexion de réseau privé virtuel ou d’adresse IP publique pour accéder aux ressources locales.
- **Équilibrage de charge et direction de trafic**  
    Équilibrez la charge du trafic sur les serveurs dans le même emplacement et dirigez le trafic vers les serveurs dans différents emplacements.
- **Sécurité**  
    Filtrez le trafic réseau entre les sous-réseaux ou des machines virtuelles du réseau.
- **Routage**  
    Utilisez le routage par défaut ou contrôlez entièrement le routage entre vos ressources Azure Stack Hub et locales.
- **Facilité de gestion**  
    Analysez et gérez vos ressources de gestion de réseau Azure Stack Hub.
- **Outils de déploiement et de configuration**  
    Utilisez un portail web ou des outils de ligne de commande multiplateforme pour déployer et configurer les ressources réseau.


## <a name="next-steps"></a>Étapes suivantes

* [Considérations relatives aux réseaux Azure Stack Hub](azure-stack-network-differences.md)