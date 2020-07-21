---
title: Utiliser la fonctionnalité de carte réseau Azure pour connecter un serveur à un réseau virtuel Azure
description: Cet article présente les exigences et les étapes à respecter pour utiliser la fonctionnalité de carte réseau Azure afin de connecter un serveur à un réseau virtuel Azure.
ms.topic: article
author: thomasmaurer
ms.author: thmaure
ms.date: 07/14/2020
ms.openlocfilehash: 1bec00f972cd0cfb1b56aeae831dd1ba2914ab33
ms.sourcegitcommit: 2be3dd5419b0d003a9598a42541ebb1d251aea3d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2020
ms.locfileid: "86393627"
---
# <a name="use-azure-network-adapter-to-connect-a-server-to-an-azure-virtual-network"></a>Utiliser la fonctionnalité de carte réseau Azure pour connecter un serveur à un réseau virtuel Azure

>S’applique à : Windows Server 2019, Windows Server 2016, Windows Server 2012 R2

Un grand nombre de charges de travail exécutées en local et dans des environnements multiclouds nécessitent des connexions aux machines virtuelles qui s’exécutent dans Microsoft Azure. Pour connecter un serveur à un réseau virtuel Azure, plusieurs options s’offrent à vous, notamment le VPN site à site, le routage Azure ExpressRoute et le VPN point à site.

Windows Admin Center et la fonctionnalité de carte réseau Azure offrent une expérience utilisateur en un clic pour connecter le serveur à votre réseau virtuel à l’aide d’une connexion [VPN point à site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal). Le processus automatise la configuration de la passerelle de réseau virtuel et du client VPN local.

## <a name="when-to-use-azure-network-adapter"></a>Quand utiliser la fonctionnalité de carte réseau Azure
Les connexions VPN point à site liées à la fonctionnalité de carte réseau Azure sont utiles quand vous souhaitez vous connecter à votre réseau virtuel à partir d’un lieu distant, par exemple une succursale, un magasin ou tout autre endroit. Vous pouvez également utiliser la fonctionnalité de carte réseau Azure à la place d’un VPN site à site quand vous devez connecter seulement quelques serveurs à un réseau virtuel. Les connexions liées à la fonctionnalité de carte réseau Azure ne nécessitent pas de périphérique VPN ou d’adresse IP publique.

### <a name="requirements"></a>Spécifications
L’utilisation de la fonctionnalité de carte réseau Azure pour se connecter à un réseau virtuel nécessite les éléments suivants :
- Un compte Azure avec au moins un abonnement actif.
- Un réseau virtuel existant.
- Un accès à Internet pour les serveurs cibles à connecter au réseau virtuel Azure.
- Une connexion entre Windows Admin Center et Azure.
  Pour en savoir plus, consultez [Configuration de l’intégration à Azure](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/azure-integration).
- La dernière version de Windows Admin Center.
  Pour en savoir plus, consultez [Windows Admin Center](https://www.microsoft.com/windows-server/windows-admin-center).

> [!NOTE]
> Il n’est pas nécessaire d’installer Windows Admin Center sur le serveur que vous souhaitez connecter à Azure. Toutefois, vous pouvez le faire dans un scénario basé sur un seul serveur.

## <a name="add-an-azure-network-adapter-to-a-server"></a>Ajouter la fonctionnalité de carte réseau Azure à un serveur
Pour configurer la fonctionnalité de carte réseau Azure, accédez à l’extension réseau correspondante dans Windows Admin Center.

Dans Windows Admin Center :
1. Accédez au serveur hébergeant les machines virtuelles que vous souhaitez ajouter à la fonctionnalité de carte réseau Azure.
1. Sous **Outils**, sélectionnez **Réseaux**.
1. Sélectionnez **Add Azure Network Adapter** (Ajouter une carte réseau Azure).
1. Dans le volet **Add Azure Network Adapter**, entrez les informations nécessaires suivantes, puis sélectionnez **Créer** :
    - **Abonnement**
    - **Lieu**
    - **Réseau virtuel**
    - **Sous-réseau de passerelle** (s’il n’existe pas)
    - **Référence SKU de passerelle** (si elle n’existe pas)
    - **Espace d’adressage du client**

        Le pool d’adresses des clients est une plage d’adresses IP privées que vous spécifiez. Les clients qui se connectent via un réseau virtuel de point à site reçoivent de façon dynamique une adresse IP de cette plage. Utilisez une plage d’adresses IP privées qui ne chevauche ni l’emplacement local à partir duquel vous vous connectez, ni le réseau virtuel auquel vous souhaitez vous connecter. Nous vous recommandons d’utiliser des adresses IP qui se trouvent dans les plages désignées pour les réseaux privés (10.x.x.x, 192.168.x.x ou 172.16.0.0 à 172.31.255.255).

    - **Certificat d’authentification**

        Azure utilise des certificats pour authentifier les clients qui se connectent à un réseau virtuel via une connexion VPN point à site. Les informations de clé publique du certificat racine sont chargées dans Azure. Le certificat racine est ensuite considéré comme « approuvé » par Azure pour une connexion point à site au réseau virtuel. Les certificats clients doivent être générés à partir du certificat racine approuvé et installé sur le serveur client. Le certificat client permet d’authentifier le client quand il lance une connexion au réseau virtuel.
    
        Pour en savoir plus, consultez la section « Configurer le type d’authentification » dans [Configurer une connexion VPN point à site à un réseau virtuel à l’aide de l’authentification par certificat Azure native : Portail Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal).

    :::image type="content" source="media/azure-network-adapter/add-azure-network-adapter.png" alt-text="Volet d’ajout de fonctionnalité de carte réseau Azure dans Windows Admin Center.":::

> [!NOTE]
> Les appliances réseau, par exemple Passerelle VPN et Application Gateway, qui s’exécutent au sein d’un réseau virtuel entraînent des frais supplémentaires. Pour en savoir plus, consultez [Tarification de Réseau virtuel](https://azure.microsoft.com/pricing/details/virtual-network/).

S’il n’existe aucune passerelle Réseau virtuel Azure, Windows Admin Center en crée une à votre place. Le processus de configuration peut prendre jusqu’à 25 minutes. Une fois la carte réseau Azure créée, vous pouvez commencer à accéder aux machines virtuelles du réseau virtuel directement à partir de votre serveur.

Si vous n’avez plus besoin de la connectivité, sous **Réseaux**, sélectionnez la carte réseau Azure à déconnecter. Dans le menu supérieur, sélectionnez **Déconnecter**, puis, dans la fenêtre indépendante **Disconnect VPN Confirmation** (Confirmation de la déconnexion du VPN), sélectionnez **Oui**.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur le service Réseau virtuel Azure, consultez également :

- [FAQ sur les réseaux virtuels Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)
