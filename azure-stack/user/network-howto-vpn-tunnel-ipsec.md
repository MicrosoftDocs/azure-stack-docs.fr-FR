---
title: Créer un tunnel VPN à l’aide d’IPSEC dans Azure Stack | Microsoft Docs
description: Découvrez comment créer un tunnel VPN à l’aide d’IPSEC dans Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 09/19/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/19/2019
ms.openlocfilehash: 762d3f631823d1acb8445148a164a18605fa3762
ms.sourcegitcommit: cc3534e09ad916bb693215d21ac13aed1d8a0dde
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73168228"
---
# <a name="how-to-create-a-vpn-tunnel-using-ipsec--in-azure-stack"></a>Créer un tunnel VPN à l’aide d’IPSEC dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Vous pouvez utiliser le modèle Azure Stack Resource Manager de cette solution pour connecter deux réseaux virtuels Azure Stack au sein du même environnement Azure Stack. Vous [ne pouvez pas connecter de réseaux virtuels Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-network-differences) à l’aide de la passerelle de réseau virtuel intégrée. Actuellement, vous devez utiliser des appliances virtuelles réseau pour créer un tunnel VPN entre deux réseaux virtuels Azure Stack. Le modèle de solution déploie deux machines virtuelles Windows Server 2016 avec RRAS installé. La solution configure les deux serveurs RRAS pour utiliser un tunnel S2SVPN IKEv2 entre les deux réseaux virtuels. Les règles UDR et NSG qui conviennent sont créées pour permettre le routage entre les sous-réseaux de chaque réseau virtuel désigné comme **interne**. 

Cette solution est la base qui permet de créer des tunnels VPN au sein d’une instance Azure Stack, de même qu'entre des instances Azure Stack et d’autres ressources telles que les réseaux locaux à l'aide de tunnels VPN S2S RRAS Windows.

Les modèles sont disponibles dans le référentiel GitHub [Azure intelligent Edge Patterns](https://github.com/Azure-Samples/azure-intelligent-edge-patterns). Le modèle se trouve dans le dossier **rras-gre-vnet-vnet**. 

![texte de remplacement](./media/azure-stack-network-howto-vpn-tunnel-ipsec/overview.png)

## <a name="requirements"></a>Configuration requise

- ASDK ou système intégré Azure Stack avec les dernières mises à jour appliquées. 
- Éléments de la Place de marché Azure Stack requis :
    -  Windows Server 2016 Datacenter ou Windows Server 2019 Datacenter (dernière version recommandée)
    -  Extension de script personnalisé

## <a name="things-to-consider"></a>Points importants à prendre en compte

- Un groupe de sécurité réseau est appliqué au sous-réseau de tunnel du modèle.  Il est recommandé de sécuriser le sous-réseau interne dans chaque réseau virtuel à l'aide d'un groupe de sécurité réseau supplémentaire.
- Une règle de refus RDP est appliquée au groupe de sécurité réseau et doit être définie sur Autoriser si vous souhaitez accéder aux machines virtuelles par le biais de l’adresse IP publique
- Cette solution ne prend pas en compte la résolution DNS
- La combinaison du nom du réseau virtuel et de vmName doit être inférieure à 15 caractères
- Ce modèle est conçu pour personnaliser les noms de réseaux virtuels pour VNet1 et VNet2
- Ce modèle utilise des fenêtres BYOL
- Lors de la suppression du groupe de ressources, actuellement sur (1907), vous devez manuellement détacher les groupes de sécurité réseau du sous-réseau du tunnel pour permettre la suppression du groupe de ressources
- Ce modèle utilise une machine virtuelle DS3v2.  Le service RRAS installe et exécute Windows Internal SQL Server.  Cela peut provoquer des problèmes de mémoire si la taille de votre machine virtuelle est trop petite.  Avant de réduire la taille de la machine virtuelle, vérifiez ses performances.
- Il ne s’agit pas d’une solution hautement disponible.  Si vous avez besoin d’une solution à plus haute disponibilité, vous pouvez ajouter une deuxième machine virtuelle, vous devez modifier manuellement l’itinéraire dans la table de route vers l’adresse IP interne de l’interface secondaire.  Vous devez également configurer les différents tunnels pour qu'ils se connectent.

## <a name="optional"></a>Facultatif

- Vous pouvez utiliser votre propre compte de stockage Blob et votre propre jeton SAS à l’aide des paramètres _artifactsLocation et _artifactsLocationSasToken
- Il existe deux sorties sur ce modèle INTERNALSUBNETREFVNET1 et INTERNALSUBNETREFVNET2, correspondant aux ID de ressource des sous-réseaux internes, si vous souhaitez l’utiliser dans un modèle de déploiement de style pipeline.

Ce modèle fournit des valeurs par défaut pour l’affectation de noms et l’adressage IP du réseau virtuel.  Il requiert un mot de passe pour l’administrateur (rrasadmin) et offre également la possibilité d’utiliser votre propre objet blob de stockage avec un jeton SAS.  Veillez à conserver ces valeurs dans les limites qui conviennent pour permettre au déploiement d'aboutir.  Le package PowerShell DSC est exécuté sur chaque machine virtuelle RRAS et installe le routage, ainsi que tous les services et fonctionnalités dépendants requis.  Ce DSC peut être davantage personnalisé, si besoin.  L’extension de script personnalisé exécute le script suivant et Add-Site2SiteIKE.ps1 configure le tunnel VPNS2S entre les deux serveurs RRAS avec une clé partagée.  Vous pouvez afficher la sortie détaillée de l’extension de script personnalisé pour consulter les résultats de la configuration du tunnel VPN.

![texte de remplacement](./media/azure-stack-network-howto-vpn-tunnel-ipsec/s2svpntunnel.png)

## <a name="next-steps"></a>Étapes suivantes

[Différences et considérations relatives aux réseaux Azure Stack](azure-stack-network-differences.md)  
[Configurer un tunnel VPN site à site multiple](network-howto-vpn-tunnel.md)  
[Créer un tunnel VPN à l’aide de GRE](network-howto-vpn-tunnel-gre.md)