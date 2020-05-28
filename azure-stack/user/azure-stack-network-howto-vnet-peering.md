---
title: Connecter deux infrastructures Azure Stack Hub à l’aide de VNet Peering
description: Découvrez comment connecter deux infrastructures Azure Stack Hub à l’aide de VNet Peering.
author: mattbriggs
ms.topic: how-to
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/03/2019
ms.openlocfilehash: 428c95a4f9d387cd298a1965f165278dfdc5a763
ms.sourcegitcommit: cad40ae88212cc72f40c84a1c88143ea0abb65ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84111980"
---
# <a name="vnet-peering-in-azure-stack-hub-with-vms"></a>VNET Peering dans Azure Stack Hub avec des machines virtuelles

Vous pouvez connecter deux réseaux virtuels Azure Stack Hub l’un à l’autre au sein du même environnement Azure Stack Hub. Il n’est actuellement pas possible de connecter des réseaux virtuels Azure Stack Hub à l’aide de la [passerelle de réseau virtuel](https://docs.microsoft.com/azure-stack/user/azure-stack-network-differences) intégrée. Vous devez utiliser des appliances virtuelles réseau pour créer un tunnel VPN entre deux réseaux virtuels Azure Stack Hub. Dans les références de modèle de cet article, deux machines virtuelles Windows Server 2016 sont déployées et RRAS y est installé. Les deux serveurs RRAS sont configurés pour implémenter un tunnel IKEv2 S2SVPN entre deux réseaux virtuels. Les règles de groupe de sécurité réseau et de route définie par l’utilisateur appropriées sont créées pour permettre le routage entre les sous-réseaux sur chaque réseau virtuel désigné comme **interne**. 

Ce modèle de déploiement est la base qui permet de créer des tunnels VPN au sein d’une instance Azure Stack Hub ainsi qu’entre des instances Azure Stack Hub et d’autres ressources, telles que des réseaux locaux, avec l’utilisation de tunnels VPN S2S Windows RRAS. 

Vous trouverez les modèles dans le dépôt GitHub [Azure Intelligent Edge Patterns](https://github.com/Azure-Samples/azure-intelligent-edge-patterns
). Le modèle se trouve dans le dossier **S2SVPNTunnel**.

![texte de remplacement](./media/azure-stack-network-howto-vnet-peering/overview.svg)

## <a name="requirements"></a>Spécifications

- Un déploiement avec les dernières mises à jour appliquées. 
- Éléments requis de la Place de marché Azure Stack Hub :
    -  Windows Server 2016 Datacenter (dernière version recommandée)
    -  Extension de script personnalisé

## <a name="things-to-consider"></a>Points importants à prendre en compte

- Un groupe de sécurité réseau est appliqué au sous-réseau de tunnel du modèle. Il est recommandé de sécuriser le sous-réseau interne dans chaque réseau virtuel à l'aide d'un groupe de sécurité réseau supplémentaire.
- Une règle de refus RDP est appliquée au groupe de sécurité réseau de tunnel et doit être définie sur Autoriser si vous souhaitez accéder aux machines virtuelles par le biais de l’adresse IP publique.
- Cette solution ne prend pas en compte la résolution DNS.
- La combinaison du nom du réseau virtuel et du nom de machine virtuelle doit être inférieure à 15 caractères.
- Ce modèle est conçu pour personnaliser les noms de réseaux virtuels pour VNet1 et VNet2.
- Ce modèle utilise Windows BYOL.
- Lors de la suppression du groupe de ressources, actuellement sur (1907), vous devez manuellement détacher les groupes de sécurité réseau du sous-réseau du tunnel pour permettre la suppression du groupe de ressources
- Ce modèle utilise une machine virtuelle DS3v2. Le service RRAS installe et exécute le serveur Windows SQL Server interne. Cela peut provoquer des problèmes de mémoire si la taille de votre machine virtuelle est trop petite. Avant de réduire la taille de la machine virtuelle, vérifiez ses performances.
- Il ne s’agit pas d’une solution hautement disponible. Si vous avez besoin d’une solution de type Haute disponibilité améliorée, vous pouvez ajouter une deuxième machine virtuelle. Vous devez modifier manuellement la route dans la table de route vers l’adresse IP interne de l’interface secondaire. Vous devez également configurer les différents tunnels pour qu'ils se connectent.

## <a name="options"></a>Options

- Vous pouvez utiliser votre propre compte de stockage blob et votre propre jeton SAP à l’aide des paramètres _artifactsLocation et _artifactsLocationSasToken.
- Il existe deux sorties sur ce modèle, INTERNALSUBNETREFVNET1 et INTERNALSUBNETREFVNET2, qui correspondent aux ID de ressource des sous-réseaux internes, si vous souhaitez utiliser ceci dans un modèle de déploiement de type pipeline.

Le modèle fournit des valeurs par défaut pour l’affectation de noms et l’adressage IP au réseau virtuel. Il requiert un mot de passe pour l’administrateur (rrasadmin) et offre également la possibilité d’utiliser votre propre blob de stockage avec un jeton SAS. Veillez à conserver ces valeurs dans les limites qui conviennent pour permettre au déploiement d'aboutir. Le package PowerShell DSC est exécuté sur chaque machine virtuelle RRAS et installe le routage ainsi que tous les services et fonctionnalités dépendants requis. Ce DSC peut être davantage personnalisé, si besoin. L’extension de script personnalisé exécute le script suivant et `Add-Site2Site.ps1` configure le tunnel VPNS2S entre les deux serveurs RRAS avec une clé partagée. Vous pouvez afficher la sortie détaillée de l’extension de script personnalisé pour consulter les résultats de la configuration du tunnel VPN.

![texte de remplacement](./media/azure-stack-network-howto-vnet-peering/s2svpntunnels2.svg)

## <a name="next-steps"></a>Étapes suivantes

[Différences et considérations relatives aux réseaux Azure Stack Hub](azure-stack-network-differences.md)  