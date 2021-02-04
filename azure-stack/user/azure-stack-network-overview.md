---
title: Présentation de la gestion de réseau Azure Stack Hub
description: En savoir plus sur la gestion de réseau Azure Stack Hub
author: mattbriggs
ms.topic: conceptual
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: scottnap
ms.lastreviewed: 01/14/2020
ms.openlocfilehash: ca8c93e983290e925d3af38bef0799d11c4a745a
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99247606"
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