---
title: Connecter Azure Stack Hub à Azure à l’aide d’ExpressRoute
description: Découvrez comment connecter des réseaux virtuels dans Azure Stack Hub à des réseaux virtuels dans Azure à l’aide d’ExpressRoute.
author: sethmanheim
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 10/22/2019
ms.openlocfilehash: ef84604b46f2968be4c8d3b84f190a22e270cb21
ms.sourcegitcommit: d542b68b299b73e045f30916afb6018e365e9db6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975960"
---
# <a name="connect-azure-stack-hub-to-azure-using-azure-expressroute"></a>Connecter Azure Stack Hub à Azure à l'aide d’Azure ExpressRoute

Cet article explique comment connecter un réseau virtuel Azure Stack Hub à un réseau virtuel Azure à l’aide d’une connexion directe [Microsoft Azure ExpressRoute](/azure/expressroute/).

Vous pouvez utiliser cet article sous forme de didacticiel et utiliser les exemples pour configurer le même environnement de test. Ou bien, vous pouvez lire l’article comme une procédure pas-à-pas qui vous guide lors de la configuration de votre propre environnement ExpressRoute.

## <a name="overview-assumptions-and-prerequisites"></a>Vue d’ensemble, hypothèses et conditions préalables

Azure ExpressRoute vous permet d’étendre vos réseaux locaux au cloud de Microsoft via une connexion privée assurée par un fournisseur de connectivité. ExpressRoute n’est pas une connexion VPN établie via le réseau Internet public.

Pour en savoir plus sur ExpressRoute, consultez la rubrique [Présentation d’ExpressRoute](/azure/expressroute/expressroute-introduction).

### <a name="assumptions"></a>Hypothèses

Cet article suppose que vous avez :

* Une connaissance pratique de Azure.
* Une connaissance élémentaire d'Azure Stack Hub.
* Une connaissance élémentaire de la mise en réseau.

### <a name="prerequisites"></a>Prérequis

Pour connecter Azure Stack Hub et Azure à l’aide d’ExpressRoute, vous devez tenir compte des exigences suivantes :

* Un [circuit ExpressRoute](/azure/expressroute/expressroute-circuit-peerings) doit être configuré via un [fournisseur de connectivité](/azure/expressroute/expressroute-locations).
* Vous devez disposer d’un abonnement Azure pour créer un circuit ExpressRoute et des réseaux virtuels dans Azure.
* Un routeur qui doit :
  * prendre en charge les connexions VPN de site à site entre son interface LAN et la passerelle multi-locataire Azure Stack Hub.
  * être en mesure de créer plusieurs VRF (Virtual Routing and Forwarding) si plusieurs locataires sont ajoutés à votre déploiement Azure Stack Hub.
* Un routeur qui possède :
  * Un port WAN connecté au circuit ExpressRoute.
  * Un port de réseau local connecté à la passerelle multi-locataire d’Azure Stack Hub.

### <a name="expressroute-network-architecture"></a>Architecture réseau ExpressRoute

L’illustration suivante montre les environnements Azure Stack Hub et Azure après configuration d'ExpressRoute à l’aide des exemples de cet article :

![Réseau ExpressRoute](media/azure-stack-connect-expressroute/conceptual.svg)

L’illustration suivante montre comment plusieurs locataires se connectent à Azure à partir de l’infrastructure Azure Stack Hub, par le biais du routeur ExpressRoute :

![Connexions d’architecture mutualisées avec ExpressRoute](media/azure-stack-connect-expressroute/architecture.svg)

L’exemple de cet article utilise la même architecture mutualisée illustrée dans ce diagramme pour connecter Azure Stack Hub à Azure à l’aide du peering privé ExpressRoute. La connexion s’effectue grâce à une connexion VPN de site à site établie entre la passerelle de réseau virtuel dans Azure Stack Hub et un routeur ExpressRoute.

Les étapes de cet article vous expliquent comment créer une connexion de bout en bout entre deux réseaux virtuels, depuis deux locataires différents dans Azure Stack Hub, et les réseaux virtuels correspondants dans Azure. Le paramétrage de deux locataires est facultatif, vous pouvez également utiliser ces étapes pour un seul locataire.

## <a name="configure-azure-stack-hub"></a>Configurer Azure Stack Hub

Pour configurer l’environnement Azure Stack Hub pour le premier locataire, suivez la procédure ci-dessous. Si vous configurez plusieurs locataires, répétez ces étapes :

>[!NOTE]
>Ces étapes expliquent comment créer des ressources à l’aide du portail Azure Stack Hub, mais vous pouvez également utiliser PowerShell.

![Configuration réseau Azure Stack Hub](media/azure-stack-connect-expressroute/azure-stack-connect-expressrouteimage-image2.svg)

### <a name="before-you-begin"></a>Avant de commencer

Avant de commencer à configurer Azure Stack Hub, vous devez disposer de ce qui suit :

* Kit de déploiement Azure Stack Hub.
* Offre sur Azure Stack Hub à laquelle votre utilisateur peut souscrire. Pour plus d’informations, consultez [Vue d’ensemble des services, des plans, des offres et des abonnements](service-plan-offer-subscription-overview.md).

### <a name="create-network-resources-in-azure-stack-hub"></a>Créer des ressources réseau dans Azure Stack Hub

Suivez les procédures ci-dessous pour créer les ressources réseau nécessaires dans Azure Stack Hub pour un locataire.

#### <a name="create-the-virtual-network-and-vm-subnet"></a>Créer le réseau virtuel et le sous-réseau de machine virtuelle

1. Connectez-vous au portail utilisateur Azure Stack Hub.

2. Dans le portail, sélectionnez **+ Créer une ressource**.

3. Sous **Place de marché Azure**, sélectionnez **Mise en réseau**.

4. Sous **Sélection**, sélectionnez **Réseau virtuel**.

5. Sous **Créer un réseau virtuel**, dans les champs appropriés, entrez les valeurs indiquées dans le tableau suivant :

   |Champ  |Valeur  |
   |---------|---------|
   |Nom     |Tenant1VNet1         |
   |Espace d’adressage     |10.1.0.0/16|
   |Nom du sous-réseau     |Tenant1-Sub1|
   |Plage d’adresses de sous-réseau     |10.1.1.0/24|

6. Vous devriez voir l’abonnement que vous avez créé précédemment rempli dans le champ **Abonnement**. Pour les champs restants :

    * Sous **Groupe de ressources**, sélectionnez **Créer** pour créer un nouveau groupe de ressources ou, si vous en avez déjà un, sélectionnez **Utiliser l’existant**.
    * Vérifiez l’**emplacement** par défaut.
    * Cliquez sur **Créer**.
    * Cliquez sur **Épingler au tableau de bord** (facultatif).

#### <a name="create-the-gateway-subnet"></a>Créer le sous-réseau de passerelle

1. Sous **Réseau virtuel**, sélectionnez **Tenant1VNet1**.
1. Sous **PARAMÈTRES**, sélectionnez **Sous-réseaux**.
1. Sélectionnez **+ Sous-réseau de passerelle** pour ajouter un sous-réseau de passerelle au réseau virtuel.
1. Par défaut, le nom du sous-réseau est défini sur **GatewaySubnet**. Les sous-réseaux de passerelle sont des cas spéciaux et doivent utiliser ce nom pour fonctionner correctement.
1. Vérifiez que la **plage d’adresses** est **10.1.0.0/24**.
1. Cliquez sur **OK** pour créer le sous réseau de passerelle.

#### <a name="create-the-virtual-network-gateway"></a>Créer la passerelle de réseau virtuel

1. Dans le portail utilisateur Azure Stack Hub, cliquez sur **+ Créer une ressource**.
1. Sous **Place de marché Azure**, sélectionnez **Mise en réseau**.
1. Sélectionnez **Passerelle de réseau virtuel** dans la liste des ressources réseau.
1. Dans le champ **Nom**, entrez **GW1**.
1. Sélectionnez **Réseau virtuel**.
1. Sélectionnez **Tenant1VNet1** dans la liste déroulante.
1. Sélectionnez **Adresse IP publique**, puis **Choisir une adresse IP publique**, cliquez ensuite sur **Créer**.
1. Dans le champ **Nom**, saisissez **GW1-PiP**, puis cliquez sur **OK**.
1. Par défaut, le **type de VPN** doit être défini sur **Basé sur un itinéraire**. Conservez ce paramètre.
1. Vérifiez que l’**abonnement** et l’**emplacement** sont corrects. Cliquez sur **Créer**.

#### <a name="create-the-local-network-gateway"></a>Créer la passerelle de réseau local

La ressource de passerelle de réseau local identifie la passerelle distante présente à l’autre extrémité de la connexion VPN. Pour cet exemple, l’extrémité distante de la connexion est la sous-interface LAN du routeur ExpressRoute. Pour le locataire 1 du diagramme précédent, l’adresse distante est 10.60.3.255.

1. Connectez-vous au portail utilisateur Azure Stack Hub, puis sélectionnez **+ Créer une ressource**.
1. Sous **Place de marché Azure**, sélectionnez **Mise en réseau**.
1. Sélectionnez **Passerelle de réseau local** dans la liste des ressources.
1. Dans le champ **Nom**, saisissez **ER-Router-GW**.
1. Pour le champ **Adresse IP**, reportez-vous à la figure précédente. L’adresse IP de la sous-interface LAN du routeur ExpressRoute pour le locataire 1 est 10.60.3.255. Pour votre propre environnement, entrez l’adresse IP de l’interface correspondante de votre routeur.
1. Dans le champ **Espace d’adressage**, entrez l’espace d’adressage des réseaux virtuels auxquels se connecter dans Azure. Les sous-réseaux pour le locataire 1 sont les suivants :

   * 192.168.2.0/24 est le réseau virtuel hub dans Azure.
   * 10.100.0.0/16 est le réseau virtuel Spoke dans Azure.

   > [!IMPORTANT]
   > Cet exemple suppose que vous utilisiez des itinéraires statiques pour la connexion VPN de site à site entre la passerelle Azure Stack Hub et le routeur ExpressRoute.

1. Vérifiez l’exactitude des valeurs des champs **Abonnement**, **Groupe de ressources** et **Emplacement**. Sélectionnez ensuite **Créer**.

#### <a name="create-the-connection"></a>Créer la connexion

1. Dans le portail utilisateur Azure Stack Hub, sélectionnez **+ Créer une ressource**.
1. Sous **Place de marché Azure**, sélectionnez **Mise en réseau**.
1. Sélectionnez **Connexion** dans la liste des ressources.
1. Sous **De base**, choisissez **Site à site (IPSec)** comme **Type de connexion**.
1. Sélectionnez **Abonnement**, **Groupe de ressources** et **Emplacement**. Cliquez sur **OK**.
1. Sous **Paramètres**, sélectionnez **Passerelle de réseau virtuel**, puis **GW1**.
1. Sélectionnez **Passerelle de réseau local**, puis **ER Router GW**.
1. Dans le champ **Nom de la connexion**, entrez **ConnectToAzure**.
1. Dans le champ **Clé partagée (PSK)** , entrez **abc123**, puis sélectionnez **OK**.
1. Sous **Résumé**, sélectionnez **OK**.

#### <a name="get-the-virtual-network-gateway-public-ip-address"></a>Obtenir l’adresse IP publique de la passerelle de réseau virtuel

Après avoir créé la passerelle de réseau virtuel, vous pouvez obtenir son adresse IP publique. Prenez note de cette adresse au cas où vous en auriez besoin plus tard pour votre déploiement. Selon votre déploiement, cette adresse est utilisée comme **Adresse IP interne**.

1. Dans le portail utilisateur Azure Stack Hub, sélectionnez **Toutes les ressources**.
1. Sous **toutes les ressources**, sélectionnez la passerelle de réseau virtuel, **GW1** dans l’exemple.
1. Sous **Passerelle de réseau virtuel**, sélectionnez **Vue d’ensemble** dans la liste des ressources. Vous pouvez également sélectionner **Propriétés**.
1. L’adresse IP que vous souhaitez noter est répertoriée sous **Adresse IP publique**. Pour l’exemple de configuration, cette adresse est 192.68.102.1.

#### <a name="create-a-virtual-machine-vm"></a>Créer une machine virtuelle

Pour tester le trafic de données via la connexion VPN, vous devez disposer de machines virtuelles afin d'envoyer et de recevoir des données dans le réseau virtuel d’Azure Stack Hub. Créez une machine virtuelle et déployez-la vers le sous-réseau de machine virtuelle de votre réseau virtuel.

1. Dans le portail utilisateur Azure Stack Hub, sélectionnez **+ Créer une ressource**.
1. Sous **Place de marché Azure**, sélectionnez **Compute**.
1. Dans la liste des images de machine virtuelle, sélectionnez l’image **Windows Server 2016 Datacenter Eval**.

   >[!NOTE]
   >Si l’image utilisée pour cet article n’est pas disponible, demandez à votre opérateur Azure Stack Hub de vous fournir une autre image Windows Server.

1. Dans le champ **Créer une machine virtuelle**, sélectionnez **Concepts de base**, puis saisissez **VM01** comme **Nom**.
1. Entrez un nom d’utilisateur et un mot de passe valides. Vous utiliserez ce compte pour vous connecter à la machine virtuelle une fois celle-ci créée.
1. Fournissez un **Abonnement**, un **Groupe de ressources** et un **Emplacement**. Sélectionnez **OK**.
1. Sous **Choisir une taille**, sélectionnez une taille de machine virtuelle pour l’instance, puis choisissez **Sélectionner**.
1. Sous **Paramètres**, vérifiez que :

   * Le réseau virtuel est **Tenant1VNet1**.
   * Le sous-réseau est défini sur **10.1.1.0/24**.

   Utilisez les paramètres par défaut et cliquez sur **OK**.

1. Sous **Résumé**, vérifiez la configuration de la machine virtuelle, puis cliquez sur **OK**.

Pour ajouter davantage de locataires, répétez les étapes suivies dans les sections suivantes :

* [Créer le réseau virtuel et le sous-réseau de machine virtuelle](#create-the-virtual-network-and-vm-subnet)
* [Créer le sous-réseau de passerelle](#create-the-gateway-subnet)
* [Créer la passerelle de réseau virtuel](#create-the-virtual-network-gateway)
* [Créer la passerelle de réseau local](#create-the-local-network-gateway)
* [Créer la connexion](#create-the-connection)
* [Créer une machine virtuelle](#create-a-virtual-machine)

Si vous prévoyez d’utiliser le locataire 2 en exemple, n’oubliez pas de changer les adresses IP pour éviter les chevauchements.

### <a name="configure-the-nat-vm-for-gateway-traversal"></a>Configurer la machine virtuelle NAT pour la traversée de la passerelle

> [!IMPORTANT]
> Cette section concerne uniquement les déploiements ASDK. La traduction d’adresses réseau (NAT) n’est pas nécessaire pour les déploiements à plusieurs nœuds.

Le Kit de développement Azure Stack (ASDK) est autonome et isolé du réseau sur lequel est déployé l’hôte physique. Le réseau VIP auquel les passerelles sont connectées n’est pas externe : il est masqué derrière un routeur qui procède à la traduction d’adresses réseau (NAT).

Le routeur est l’hôte ASDK qui exécute le rôle Services de routage et d’accès à distance (RRAS). Vous devez configurer la traduction d’adresses réseau sur l’hôte ASDK pour permettre à la connexion VPN de site à site de se connecter aux deux extrémités.

#### <a name="configure-the-nat"></a>Configurer NAT

1. Connectez-vous à l’ordinateur hôte Azure Stack Hub avec votre compte Administrateur.
1. Exécutez le script dans un environnement PowerShell ISE avec élévation de privilèges. Le script renvoie votre **adresse BGPNAT externe**.

   ```powershell
   Get-NetNatExternalAddress
   ```

1. Pour configurer le NAT, copiez et modifiez le script PowerShell suivant. Modifiez le script pour remplacer `External BGPNAT address` et `Internal IP address` par les valeurs d’exemple suivantes :

   * Pour *l’adresse BGPNAT externe*, utilisez 10.10.0.62
   * Pour *l’adresse IP interne*, utilisez 192.168.102.1

   Exécutez le script suivant depuis une session PowerShell ISE avec élévation de privilèges :

   ```powershell
   $ExtBgpNat = 'External BGPNAT address'
   $IntBgpNat = 'Internal IP address'

   # Designate the external NAT address for the ports that use the IKE authentication.
   Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 499 `
      -PortEnd 501
   Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 4499 `
      -PortEnd 4501
   # Create a static NAT mapping to map the external address to the Gateway public IP address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel.
   Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 500 `
      -InternalPort 500
   # Configure NAT traversal which uses port 4500 to  establish the complete IPSEC tunnel over NAT devices.
   Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 4500 `
      -InternalPort 4500
   ```

## <a name="configure-azure"></a>Configuration d’Azure

Après avoir terminé la configuration d’Azure Stack Hub, vous pouvez déployer les ressources Azure. La figure suivante représente un exemple de réseau virtuel locataire dans Azure. Vous pouvez utiliser n’importe quel nom et schéma d’adressage pour désigner votre réseau virtuel dans Azure. Toutefois, les plages d’adresses des réseaux virtuels dans Azure et Azure Stack Hub doivent être uniques et ne doivent pas se chevaucher :

![Réseaux virtuels Azure](media/azure-stack-connect-expressroute/azurearchitecture.svg)

Les ressources que vous déployez dans Azure sont semblables aux ressources déployées dans Azure Stack Hub. Vous déployez les composants suivants :

* Des réseaux virtuels et des sous-réseaux
* Un sous-réseau de passerelle
* Une passerelle de réseau virtuel
* Une connexion
* Un circuit ExpressRoute

L’infrastructure réseau Azure donnée en exemple est configurée de la façon suivante :

* Un modèle de réseau virtuel Hub (192.168.2.0/24) et Spoke (10.100.0.0./16) standard. Pour plus d’informations sur une topologie réseau hub-and-spoke, consultez [Implémenter une topologie réseau hub-and-spoke dans Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke).
* Les charges de travail sont déployées dans le réseau virtuel Spoke et le circuit ExpressRoute est connecté au réseau virtuel Hub.
* Les deux réseaux virtuels sont connectés à l’aide du peering de réseau virtuel.

### <a name="configure-the-azure-vnets"></a>Configurer les réseaux virtuels Azure

1. Connectez-vous au portail Azure avec vos informations d’identification Azure.
1. Créez le réseau virtuel Hub en utilisant la plage d’adresses 192.168.2.0/24.
1. Créez un sous-réseau à l’aide de la plage d’adresses 192.168.2.0/25 et ajoutez un sous-réseau de passerelle à l’aide de la plage d’adresses 192.168.2.128/27.
1. Créez le réseau virtuel Spoke et le sous-réseau en utilisant la plage d’adresses 10.100.0.0/16.

Pour en savoir plus sur la création de réseaux virtuels dans Azure, consultez la rubrique [Créer un réseau virtuel](/azure/virtual-network/manage-virtual-network#create-a-virtual-network).

### <a name="configure-an-expressroute-circuit"></a>Configurer un circuit ExpressRoute

1. Passez en revue la configuration requise d’ExpressRoute dans [Configuration requise pour ExpressRoute et liste de contrôle](/azure/expressroute/expressroute-prerequisites).

1. Suivez les étapes décrites dans la rubrique [Création et modification d’un circuit ExpressRoute](/azure/expressroute/expressroute-howto-circuit-portal-resource-manager) pour créer un circuit ExpressRoute via votre abonnement Azure.

   >[!NOTE]
   >Donnez la clé de service pour votre circuit à votre service afin qu’ils puissent configurer votre circuit ExpressRoute à leur extrémité.

1. Suivez les étapes décrites dans la rubrique [Créer et modifier le peering pour un circuit ExpressRoute](/azure/expressroute/expressroute-howto-routing-portal-resource-manager) pour configurer le peering privé sur le circuit ExpressRoute.

### <a name="create-the-virtual-network-gateway"></a>Créer la passerelle de réseau virtuel

Suivez les étapes décrites dans la rubrique [Configurer une passerelle de réseau virtuel pour ExpressRoute à l’aide de PowerShell](/azure/expressroute/expressroute-howto-add-gateway-resource-manager) pour créer une passerelle de réseau virtuel pour ExpressRoute dans le réseau virtuel Hub.

### <a name="create-the-connection"></a>Créer la connexion

Pour connecter le circuit ExpressRoute au réseau virtuel Hub, suivez les étapes décrites dans [Connecter un réseau virtuel à un circuit ExpressRoute](/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager).

### <a name="peer-the-vnets"></a>Homologuer les réseaux virtuels

Effectuez le peering des réseaux virtuels Hub et Spoke en suivant les étapes décrites dans la rubrique [Créer un peering de réseaux virtuels - Resource Manager - Même abonnement](/azure/virtual-network/virtual-networks-create-vnetpeering-arm-portal). Lors de la configuration de VNet Peering, assurez-vous d’utiliser les options suivantes :

* De Hub à Spoke : **Autoriser le transit par passerelle**.
* De Spoke à Hub : **Utiliser la passerelle distante par défaut**.

### <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle

Déployez les machines virtuelles de votre charge de travail dans le réseau virtuel Spoke.

Répétez ces étapes pour connecter d’autres réseaux virtuels locataires dans Azure via leurs circuits ExpressRoute respectifs.

## <a name="configure-the-router"></a>Configuration du routeur

Vous pouvez utiliser le diagramme suivant de la configuration du routeur ExpressRoute comme guide pour configurer votre routeur ExpressRoute. Cette illustration représente deux locataires (Tenant 1 et Tenant 2) avec leurs circuits ExpressRoute respectifs. Chaque locataire est connecté à son propre VRF (Virtual Routing and Forwarding) du côté LAN et WAN du routeur ExpressRoute. Cette configuration garantit l’isolation de bout en bout entre les deux clients. Notez les adresses IP utilisées dans les interfaces du routeur pendant que vous suivez l’exemple de configuration.

![Configuration du routeur ExpressRoute](media/azure-stack-connect-expressroute/endtoend.svg)

Vous pouvez utiliser n’importe quel routeur qui prend en charge le VPN IKEv2 et BGP pour mettre fin à la connexion VPN de site à site établie depuis Azure Stack Hub. Le même routeur est utilisé pour se connecter à Azure via un circuit ExpressRoute.

L’exemple de configuration du routeur des services d’agrégation Cisco ASR 1000 ci-après prend en charge l’infrastructure réseau représentée dans le diagramme *Configuration du routeur ExpressRoute*.

```shell
ip vrf Tenant 1
 description Routing Domain for PRIVATE peering to Azure for Tenant 1
 rd 1:1
!
ip vrf Tenant 2
 description Routing Domain for PRIVATE peering to Azure for Tenant 2
 rd 1:5
!
crypto ikev2 proposal V2-PROPOSAL2
description IKEv2 proposal for Tenant 1
encryption aes-cbc-256
 integrity sha256
 group 2
crypto ikev2 proposal V4-PROPOSAL2
description IKEv2 proposal for Tenant 2
encryption aes-cbc-256
 integrity sha256
 group 2
!
crypto ikev2 policy V2-POLICY2
description IKEv2 Policy for Tenant 1
match fvrf Tenant 1
 match address local 10.60.3.255
 proposal V2-PROPOSAL2
description IKEv2 Policy for Tenant 2
crypto ikev2 policy V4-POLICY2
 match fvrf Tenant 2
 match address local 10.60.3.251
 proposal V4-PROPOSAL2
!
crypto ikev2 profile V2-PROFILE
description IKEv2 profile for Tenant 1
match fvrf Tenant 1
 match address local 10.60.3.255
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 1
!
crypto ikev2 profile V4-PROFILE
description IKEv2 profile for Tenant 2
 match fvrf Tenant 2
 match address local 10.60.3.251
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 2
!
crypto ipsec transform-set V2-TRANSFORM2 esp-gcm 256
 mode tunnel
crypto ipsec transform-set V4-TRANSFORM2 esp-gcm 256
 mode tunnel
!
crypto ipsec profile V2-PROFILE
 set transform-set V2-TRANSFORM2
 set ikev2-profile V2-PROFILE
!
crypto ipsec profile V4-PROFILE
 set transform-set V4-TRANSFORM2
 set ikev2-profile V4-PROFILE
!
interface Tunnel10
description S2S VPN Tunnel for Tenant 1
 ip vrf forwarding Tenant 1
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.211
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf Tenant 1
 tunnel protection ipsec profile V2-PROFILE
!
interface Tunnel20
description S2S VPN Tunnel for Tenant 2
 ip vrf forwarding Tenant 2
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.213
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf VNET3
 tunnel protection ipsec profile V4-PROFILE
!
interface GigabitEthernet0/0/1
 description PRIMARY ExpressRoute Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/1.100
description Primary WAN interface of Tenant 1
 description PRIMARY ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.1 255.255.255.252
!
interface GigabitEthernet0/0/1.102
description Primary WAN interface of Tenant 2
 description PRIMARY ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.17 255.255.255.252
!
interface GigabitEthernet0/0/2
 description BACKUP ExpressRoute Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/2.100
description Secondary WAN interface of Tenant 1
 description BACKUP ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.5 255.255.255.252
!
interface GigabitEthernet0/0/2.102
description Secondary WAN interface of Tenant 2
description BACKUP ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.21 255.255.255.252
!
interface TenGigabitEthernet0/1/0
 description Downlink to ---Port 1/47
 no ip address
!
interface TenGigabitEthernet0/1/0.211
 description LAN interface of Tenant 1
description Downlink to --- Port 1/47.211
 encapsulation dot1Q 211
 ip vrf forwarding Tenant 1
 ip address 10.60.3.255 255.255.255.254
!
interface TenGigabitEthernet0/1/0.213
description LAN interface of Tenant 2
 description Downlink to --- Port 1/47.213
 encapsulation dot1Q 213
 ip vrf forwarding Tenant 2
 ip address 10.60.3.251 255.255.255.254
!
router bgp 65530
 bgp router-id <removed>
 bgp log-neighbor-changes
 description BGP neighbor config and route advertisement for Tenant 1 VRF
 address-family ipv4 vrf Tenant 1
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.254 mask 255.255.255.254
  network 192.168.1.0 mask 255.255.255.252
  network 192.168.1.4 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65100
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 1
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.254 remote-as 4232570301
  neighbor 10.60.3.254 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.254 activate
  neighbor 10.60.3.254 route-map BLOCK-ALL out
  neighbor 192.168.1.2 remote-as 12076
  neighbor 192.168.1.2 description PRIMARY ER peer for Tenant 1 to Azure
  neighbor 192.168.1.2 ebgp-multihop 5
  neighbor 192.168.1.2 activate
  neighbor 192.168.1.2 soft-reconfiguration inbound
  neighbor 192.168.1.2 route-map Tenant 1-ONLY out
  neighbor 192.168.1.6 remote-as 12076
  neighbor 192.168.1.6 description BACKUP ER peer for Tenant 1 to Azure
  neighbor 192.168.1.6 ebgp-multihop 5
  neighbor 192.168.1.6 activate
  neighbor 192.168.1.6 soft-reconfiguration inbound
  neighbor 192.168.1.6 route-map Tenant 1-ONLY out
  maximum-paths 8
 exit-address-family
 !
description BGP neighbor config and route advertisement for Tenant 2 VRF
address-family ipv4 vrf Tenant 2
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.250 mask 255.255.255.254
  network 192.168.1.16 mask 255.255.255.252
  network 192.168.1.20 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65300
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 2
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.250 remote-as 4232570301
  neighbor 10.60.3.250 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.250 activate
  neighbor 10.60.3.250 route-map BLOCK-ALL out
  neighbor 192.168.1.18 remote-as 12076
  neighbor 192.168.1.18 description PRIMARY ER peer for Tenant 2 to Azure
  neighbor 192.168.1.18 ebgp-multihop 5
  neighbor 192.168.1.18 activate
  neighbor 192.168.1.18 soft-reconfiguration inbound
  neighbor 192.168.1.18 route-map VNET-ONLY out
  neighbor 192.168.1.22 remote-as 12076
  neighbor 192.168.1.22 description BACKUP ER peer for Tenant 2 to Azure
  neighbor 192.168.1.22 ebgp-multihop 5
  neighbor 192.168.1.22 activate
  neighbor 192.168.1.22 soft-reconfiguration inbound
  neighbor 192.168.1.22 route-map VNET-ONLY out
  maximum-paths 8
 exit-address-family
!
ip forward-protocol nd
!
ip as-path access-list 1 permit ^$
ip route vrf Tenant 1 10.1.0.0 255.255.0.0 Tunnel10
ip route vrf Tenant 2 10.1.0.0 255.255.0.0 Tunnel20
!
ip prefix-list BLOCK-ALL seq 5 deny 0.0.0.0/0 le 32
!
route-map BLOCK-ALL permit 10
 match ip address prefix-list BLOCK-ALL
!
route-map VNET-ONLY permit 10
 match as-path 1
!
```

## <a name="test-the-connection"></a>Tester la connexion

Lorsque la connexion de site à site et le circuit ExpressRoute sont établis, testez votre connexion.

Effectuez les tests ping suivants :

* Connectez-vous à l’une des machines virtuelles de votre réseau virtuel Azure et effectuez un test ping sur la machine virtuelle créée dans Azure Stack Hub.
* Connectez-vous à l’une des machines virtuelles que vous avez créées dans Azure Stack Hub, puis effectuez un test ping sur la machine virtuelle que vous avez créée dans le réseau virtuel Azure.

>[!NOTE]
>Pour vérifier que vous envoyez bien le trafic via les connexions site à site et ExpressRoute, vous devez effectuer un test ping aux deux extrémités avec l’adresse IP dédiée de la machine virtuelle, et non avec son adresse IP virtuelle.

### <a name="allow-icmp-in-through-the-firewall"></a>Autoriser la transmission du protocole ICMP via le pare-feu

Par défaut, Windows Server 2016 n’autorise pas la transmission des paquets ICMP entrants via le pare-feu. Pour chaque machine virtuelle que vous utilisez dans le cadre de tests ping, vous devez autoriser les paquets ICMP entrants. Pour créer une règle de pare-feu pour ICMP, exécutez l’applet de commande suivante dans une fenêtre PowerShell avec élévation de privilèges :

```powershell
# Create ICMP firewall rule.
New-NetFirewallRule `
  -DisplayName "Allow ICMPv4-In" `
  -Protocol ICMPv4
```

### <a name="ping-the-azure-stack-hub-vm"></a>Effectuer un test ping sur une machine virtuelle Azure Stack Hub

1. Connectez-vous au portail utilisateur Azure Stack Hub.

1. Recherchez la machine virtuelle que vous avez créée, puis sélectionnez-la.

1. Sélectionnez **Connecter**.

1. À partir d’une invite de commandes Windows ou de PowerShell avec élévation de privilèges, entrez **ipconfig/all**. Notez l’adresse IPv4 retournée en sortie.

1. Effectuez un test ping sur cette adresse IPv4 à partir de la machine virtuelle située dans le réseau virtuel Azure.

   Dans l’environnement représenté en exemple, l’adresse IPv4 provient du sous-réseau 10.1.1.x/24. Dans votre environnement, l’adresse peut être différente. Toutefois, elle doit se trouver dans le sous-réseau que vous avez créé pour le sous-réseau du réseau virtuel locataire.

### <a name="view-data-transfer-statistics"></a>Afficher les statistiques de transfert de données

Si vous souhaitez connaître le volume de trafic qui transite via votre connexion, accédez au portail utilisateur Azure Stack Hub. Pour savoir si les données de votre test ping sont passées par les connexions VPN et ExpressRoute, vous pouvez également consulter les statistiques de transfert de données :

1. Connectez-vous au portail utilisateur Azure Stack Hub et sélectionnez **Toutes les ressources**.
1. Accédez au groupe de ressources pour lequel votre passerelle VPN a été créée et sélectionnez le type d’objet **Connexions**.
1. Sélectionnez la connexion **ConnectToAzure** dans la liste.
1. Dans **Connexions** > **Vue d’ensemble**, vous pouvez visualiser les statistiques de **données entrantes** et de **données sortantes**. Vous devriez voir des valeurs non nulles.

## <a name="next-steps"></a>Étapes suivantes

[Déployer des applications sur Azure et Azure Stack Hub](https://github.com/Azure-Samples/azure-intelligent-edge-patterns/tree/master/hybrid-devops)
