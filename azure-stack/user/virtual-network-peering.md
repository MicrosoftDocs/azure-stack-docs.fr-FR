---
title: Appairage de réseaux virtuels dans Azure Stack Hub
description: Découvrez comment utiliser l’appairage de réseaux virtuels pour connecter des réseaux virtuels dans Azure Stack Hub.
author: sethmanheim
ms.author: sethm
ms.date: 11/11/2020
ms.topic: conceptual
ms.reviewer: sranthar
ms.lastreviewed: 10/09/2020
ms.openlocfilehash: 16d7701161b8ec8c16aa3caecf5d5e291d6c0e99
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94548711"
---
# <a name="virtual-network-peering"></a>Appairage de réseaux virtuels

L’appairage de réseaux virtuels vous permet de connecter en toute transparence des réseaux virtuels dans un environnement Azure Stack Hub. Les réseaux virtuels apparaissent comme un seul réseau à des fins de connectivité. Le trafic entre les machines virtuelles utilise l’infrastructure SDN sous-jacente. À l’instar du trafic entre les machines virtuelles du même réseau, le trafic est acheminé uniquement via le réseau privé Azure Stack Hub.

Azure Stack Hub ne prend pas en charge le peering mondial, car le concept de « régions » ne s’applique pas.

Voici quelques-uns des avantages de l’appairage de réseaux virtuels :

- Connexion à latence faible et haut débit entre les ressources de différents réseaux virtuels.
- Possibilité pour les ressources d’un réseau virtuel de communiquer avec celles d’un autre réseau virtuel.
- Possibilité de transférer des données entre des réseaux virtuels entre différents abonnements et locataires Azure Active Directory.
- Aucune interruption des ressources dans un réseau virtuel lors de la création du peering ou après que le peering est créé.

Le trafic réseau entre les réseaux virtuels homologués est privé. Le trafic entre les réseaux virtuels est conservé dans la couche d’infrastructure. Aucun chiffrement et aucune connexion Internet publique, ni passerelle ne sont nécessaires pour que les réseaux virtuels communiquent.

## <a name="connectivity"></a>Connectivité

Pour les réseaux virtuels appairés, les ressources de l’un peuvent se connecter directement à celles du réseau virtuel appairé.

La latence du réseau entre des machines virtuelles de réseaux virtuels homologués dans la même région est la même que celle d’un seul réseau virtuel. Le débit du réseau repose sur la bande passante autorisée pour la machine virtuelle proportionnellement à sa taille. Aucune restriction de bande passante supplémentaire n’est appliquée au sein du peering.

Le trafic entre les machines virtuelles dans des réseaux virtuels appairés est acheminé directement via la couche SDN et non via une passerelle ou une connexion Internet publique.

Vous pouvez appliquer des groupes de sécurité réseau dans l’un ou l’autre des réseaux virtuels pour bloquer l’accès à d’autres réseaux virtuels ou sous-réseaux. Lors de la configuration de l’appairage de réseaux virtuels, vous pouvez ouvrir ou fermer les règles du groupe de sécurité réseau entre les réseaux virtuels. Si vous ouvrez totalement la connectivité entre les réseaux virtuels appairés, vous pouvez appliquer des groupes de sécurité réseau pour bloquer ou refuser certains accès. La connectivité entièrement ouverte est l’option par défaut. Pour en savoir plus sur les groupes de sécurité réseau, voir [Groupes de sécurité](/azure/virtual-network/security-overview).

## <a name="service-chaining"></a>Chaînage de services

Le chaînage de services vous permet de diriger le trafic d’un réseau virtuel vers l’appliance virtuelle ou la passerelle d’un réseau appairé via des itinéraires définis par l’utilisateur.

Pour activer le chaînage de services, configurez des itinéraires définis par l’utilisateur qui pointent vers des machines virtuelles de réseaux virtuels appairés en tant qu’adresse IP du *tronçon suivant*.

Vous pouvez également déployer des réseaux de type *hub-and-spoke* où le réseau virtuel hub héberge des composants d’infrastructure tels qu’une appliance virtuelle réseau ou une passerelle VPN. Tous les réseaux virtuels spoke peuvent ensuite être homologués avec le réseau virtuel hub. Le trafic transite par des appliances virtuelles réseau ou des passerelles VPN sur le réseau virtuel hub.

L’appairage de réseaux virtuels permet de définir le tronçon suivant dans un itinéraire défini par l’utilisateur sur l’adresse IP d’une machine virtuelle du réseau virtuel appairé. Pour en savoir plus sur les routages définis par l’utilisateur, voir [Vue d’ensemble des routages définis par l’utilisateur](/azure/virtual-network/virtual-networks-udr-overview#user-defined). Pour découvrir comment créer une topologie de réseau hub-and-spoke, consultez [Implémenter une topologie de réseau hub-and-spoke dans Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="gateways-and-on-premises-connectivity"></a>Passerelles et connectivité locale

Chaque réseau virtuel, y compris un réseau virtuel appairé, peut avoir sa propre passerelle. Un réseau virtuel peut utiliser sa passerelle pour se connecter à un réseau local. Consultez la [documentation relative à la passerelle de réseau virtuel](/azure/vpn-gateway/).

Vous pouvez également configurer la passerelle du réseau virtuel appairé en tant que point de transit vers un réseau local. Dans ce cas, le réseau virtuel qui utilise une passerelle distante ne peut pas posséder sa propre passerelle. Un réseau virtuel ne possède qu’une seule passerelle. Il peut s’agir d’une passerelle locale ou distante dans le réseau virtuel appairé, comme dans l’illustration suivante :

:::image type="content" source="media/virtual-network-peering/virtual-network-gateway.png" alt-text="Topologie de la passerelle VPN":::

Notez qu’un objet **Connection** doit être créé dans la passerelle VPN avant d’activer les options **UseRemoteGateways** dans l’appairage.

## <a name="virtual-network-peering-configuration"></a>Configuration de l’appairage de réseaux virtuels

**Autoriser l’accès au réseau virtuel** : L’activation de la communication entre les réseaux virtuels permet aux ressources connectées à ceux-ci de communiquer entre elles avec les mêmes bande passante et latence que si elles étaient connectées au même réseau virtuel. Toutes les communications entre les ressources des deux réseaux virtuels sont acheminées via la couche SDN interne.  

Vous pouvez ne pas activer l’accès au réseau si vous avez appairé un réseau virtuel avec un autre réseau virtuel, mais souhaitez occasionnellement désactiver le flux de trafic entre les deux réseaux virtuels. Il se peut que vous trouviez plus pratique d’activer ou de désactiver les appairages que de les supprimer et de les recréer. Lorsque ce paramètre est désactivé, le trafic ne passe plus entre les réseaux virtuels appairés.

**Autoriser le trafic transféré :** Cochez cette case pour autoriser l’acheminement du trafic *transféré* par une appliance virtuelle réseau dans un réseau virtuel (ne provenant pas du réseau virtuel) vers ce réseau virtuel par le biais d’un peering. Par exemple, considérez trois réseaux virtuels nommés Spoke1, Spoke2 et Hub. Un peering existe entre chaque réseau virtuel spoke et le réseau virtuel Hub, mais les peerings n’existent pas entre les réseaux virtuels spoke. Une appliance virtuelle réseau est déployée dans le réseau virtuel Hub et des itinéraires définis par l’utilisateur sont appliqués à chaque réseau virtuel spoke acheminant le trafic entre les sous-réseaux via l’appliance virtuelle réseau. Si cette case n’est pas cochée pour le peering entre chaque réseau virtuel spoke et le réseau virtuel Hub, le trafic ne passe pas entre les réseaux virtuels spoke, car le hub ne transfère pas le trafic entre les réseaux virtuels. Si l’activation de cette fonctionnalité autorise le transfert du trafic via le peering, elle n’a pas pour effet de créer des itinéraires définis par l’utilisateur ou des appliances virtuelles. Les itinéraires définis par l’utilisateur et les appliances virtuelles sont créés séparément. Pour en savoir plus, voir [Itinéraires définis par l’utilisateur](/azure/virtual-network/virtual-networks-udr-overview#user-defined). Il est inutile de cocher ce paramètre si le trafic est transféré entre les réseaux virtuels par le biais d’une passerelle VPN.

**Autoriser le transit par passerelle :** Cochez cette case si une passerelle de réseau virtuel est attachée à ce réseau virtuel, et si vous souhaitez autoriser le trafic en provenance du réseau virtuel appairé via la passerelle. Par exemple, ce réseau virtuel peut être attaché à un réseau local via une passerelle de réseau virtuel. L’activation de cette case à cocher a pour effet d’autoriser que le trafic en provenance du réseau virtuel homologué passe par la passerelle attachée à ce réseau virtuel vers le réseau local. Si vous cochez cette case, le réseau virtuel homologué ne peut pas avoir de passerelle configurée. La case **Utiliser des passerelles distantes** doit être activée pour le réseau virtuel appairé lors de la configuration de l’appairage de l’autre réseau virtuel à ce réseau virtuel. Si vous laissez cette case à cocher désactivée (par défaut), le trafic en provenance du réseau virtuel appairé continue d’être acheminé vers ce réseau virtuel, mais ne peut pas passer par une passerelle de réseau virtuel attachée à ce réseau virtuel.

**:** cochez cette case pour autoriser que le trafic en provenance de ce réseau virtuel passe par une passerelle de réseau virtuel attachée au réseau virtuel avec lequel vous effectuez le peering. Par exemple, le réseau virtuel avec lequel vous effectuez le peering a une passerelle VPN attachée qui permet la communication avec un réseau local. L’activation de cette case à cocher a pour effet d’autoriser que le trafic en provenance de ce réseau virtuel passe par la passerelle VPN attachée au réseau virtuel homologué. Si vous cochez cette case, le réseau virtuel appairé doit avoir une passerelle de réseau virtuel attachée, et la case **Autoriser le transit par passerelle** doit être activée pour ce réseau. Si vous laissez cette case à cocher désactivée (par défaut), le trafic en provenance du réseau virtuel appairé peut toujours être acheminé vers ce réseau virtuel, mais ne peut pas passer par une passerelle de réseau virtuel attachée à ce réseau virtuel.

Vous ne pouvez pas utiliser de passerelles distantes si vous avez déjà configuré une passerelle dans votre réseau virtuel.

### <a name="permissions"></a>Autorisations

Lors de la création d’un appairage avec des réseaux virtuels dans différents abonnements et locataires Azure AD, vérifiez que le rôle **Contributeur** a été attribué aux comptes. En outre, il n’existe aucune fonctionnalité d’interface utilisateur pour l’appairage entre différents locataires Azure AD. Vous pouvez utiliser Azure CLI et PowerShell pour créer les appairages.

## <a name="virtual-network-peering-frequently-asked-questions-faq"></a>Questions fréquentes (FAQ) sur l’appairage de réseaux virtuels

### <a name="what-is-virtual-network-peering"></a>Présentation de l’appairage de réseaux virtuels

L’appairage de réseaux virtuels permet de connecter des réseaux virtuels. Une connexion de peering VNet entre réseaux virtuels vous permet d’acheminer le trafic entre eux de façon privée par le biais d’adresses IPv4. Les machines virtuelles dans les réseaux virtuels appairés peuvent communiquer entre elles comme si elles se trouvaient dans le même réseau. Il est également possible de créer des connexions d’appairage de réseaux virtuels dans plusieurs abonnements.

### <a name="does-azure-stack-hub-support-global-vnet-peering"></a>Azure Stack Hub prend-il en charge le peering mondial de réseaux virtuels ?

Azure Stack Hub ne prend pas en charge le peering mondial, car le concept de « régions » ne s’applique pas.

### <a name="on-which-azure-stack-hub-update-will-virtual-network-peering-be-available"></a>Sur quelle mise à jour Azure Stack Hub l’appairage de réseaux virtuels sera-t-il disponible ?

L’appairage de réseaux virtuels est disponible dans Azure Stack Hub à partir de la mise à jour 2008.

### <a name="can-i-peer-my-virtual-network-in-azure-stack-hub-to-a-virtual-network-in-azure"></a>Puis-je appairer mon réseau virtuel dans Azure Stack Hub à un réseau virtuel dans Azure ?

Non, l’appairage entre Azure et Azure Stack Hub n’est pas pris en charge pour l’instant.

### <a name="can-i-peer-my-virtual-network-in-azure-stack-hub1-to-a-virtual-network-in-azure-stack-hub2"></a>Puis-je appairer mon réseau virtuel dans Azure Stack Hub1 à un réseau virtuel dans Azure Stack Hub2 ?

Non, l’appairage ne peut être créé qu’entre des réseaux virtuels d’un système Azure Stack Hub. Pour plus d’informations sur la connexion de deux réseaux virtuels à partir de différents tampons, consultez [Établir une connexion de réseau virtuel à réseau virtuel dans Azure Stack Hub](azure-stack-network-howto-vnet-to-vnet-stacks.md).

### <a name="can-i-enable-peering-if-my-virtual-networks-belong-to-subscriptions-within-different-azure-active-directory-tenants"></a>Puis-je activer l’appairage si mes réseaux virtuels font partie d’abonnements de différents locataires Azure Active Directory ?

Oui. Il est possible d’établir un appairage de réseaux virtuels si vos abonnements appartiennent à différents locataires Azure Active Directory. Vous pouvez faire cela via PowerShell ou Azure CLI. Le portail n’est pas encore pris en charge.

### <a name="can-i-peer-my-virtual-network-with-a-virtual-network-in-a-different-subscription"></a>Puis-je appairer mon réseau virtuel à un réseau virtuel dans un autre abonnement ?

Oui. Vous pouvez appairer des réseaux virtuels de différents abonnements.

### <a name="are-there-any-bandwidth-limitations-for-peering-connections"></a>Des restrictions de bande passante s’appliquent-elles aux connexions de peering ?

Non. L’appairage de réseaux virtuels n’impose aucune restriction de bande passante. La bande passante n’est limitée que par les ressources de la machine virtuelle ou de calcul.

### <a name="my-virtual-network-peering-connection-is-in-an-initiated-state-why-cant-i-connect"></a>Ma connexion d’appairage de réseaux virtuels est à l’état *initié*. Pourquoi ne puis-je pas me connecter ?

Si votre connexion d’appairage est dans un état **Initié**, cela signifie que vous n’avez créé qu’un seul lien. Un lien bidirectionnel doit être créé afin d’établir une connexion réussie. Par exemple, pour appairer un réseau virtuel A à un réseau virtuel B, un lien doit être créé du réseau virtuel A au réseau virtuel B et du réseau virtuel B au réseau virtuel A. La création de ces deux liens modifie l’état qui devient alors **Connecté**.

### <a name="my-virtual-network-peering-connection-is-in-a-disconnected-state-why-cant-i-create-a-peering-connection"></a>Ma connexion d’appairage de réseaux virtuels se trouve dans l’état *Déconnecté*. Pourquoi ne puis-je pas créer une connexion d’appairage ?

Si votre connexion d’appairage de réseaux virtuels se trouve dans un état **Déconnecté**, un des liens créés a été supprimé. Pour rétablir une connexion d’appairage, supprimez le lien et créez-le de nouveau.

### <a name="is-virtual-network-peering-traffic-encrypted"></a>Le trafic de l’appairage de réseaux virtuels est-il chiffré ?

Non. Le trafic entre des ressources de réseaux virtuels appairés est privé et isolé. Il reste entièrement dans la couche SDN du système Azure Stack Hub.

### <a name="if-i-peer-vnet-a-to-vnet-b-and-i-peer-vnet-b-to-vnet-c-does-that-mean-vnet-a-and-vnet-c-are-peered"></a>Si j’appaire le réseau virtuel A au réseau virtuel B et que j’appaire le réseau virtuel B au réseau virtuel C, cela signifie-t-il que le réseau virtuel A et le réseau virtuel C sont appairés ?

Non. Le peering transitif n’est pas pris en charge. Vous devez appairer le réseau virtuel A et le réseau virtuel C.

## <a name="next-steps"></a>Étapes suivantes

- [À propos de Réseau virtuel Azure](/azure/virtual-network/virtual-networks-overview)
- [Présentation des itinéraires définis par l’utilisateur](/azure/virtual-network/virtual-networks-udr-overview#user-defined)
- [Topologie réseau hub-and-spoke dans Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)
