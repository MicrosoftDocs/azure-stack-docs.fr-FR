---
title: Configurer des connexions VPN site à site Azure Stack | Microsoft Docs
description: Découvrir la stratégie IPsec/IKE pour les connexions VPN site à site ou les connexions de réseau virtuel à réseau virtuel dans Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2019
ms.author: sethm
ms.lastreviewed: 01/19/2019
ms.openlocfilehash: eb3649beb2acc56663988d2710c633d7032262ba
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985927"
---
# <a name="configure-ipsecike-policy-for-site-to-site-vpn-connections"></a>Configurer la stratégie IPsec/IKE pour des connexions VPN site à site

Cet article décrit les étapes permettant de configurer une stratégie IPsec/IKE pour les connexions VPN site à site (S2S) dans Azure Stack.

>[!NOTE]
> Pour pouvoir utiliser cette fonctionnalité, vous devez exécuter Azure Stack **1809** ou version ultérieure.  Si vous utilisez actuellement une version antérieure à la version 1809, procédez à une mise à jour de votre système Azure Stack vers la dernière version avant de tenter d'utiliser cette fonctionnalité ou de suivre les étapes décrites dans cet article.

## <a name="ipsec-and-ike-policy-parameters-for-vpn-gateways"></a>Paramètres de stratégie IPsec et IKE pour les passerelles VPN

La norme de protocole IPsec et IKE prend en charge un large éventail d’algorithmes de chiffrement dans différentes combinaisons. Pour voir quels sont les paramètres pris en charge dans Azure Stack, consultez  [Paramètres IPsec/IKE](azure-stack-vpn-gateway-settings.md#ipsecike-parameters). Ces informations peuvent vous aider à répondre aux exigences de conformité ou de sécurité.

Cet article fournit des instructions sur la création et la configuration d’une stratégie IPsec/IKE ainsi que sur son application à une connexion nouvelle ou existante.

## <a name="considerations"></a>Considérations

Notez les points importants suivants concernant l’utilisation de ces stratégies :

- La stratégie IPsec/IKE fonctionne uniquement sur les références SKU de passerelle *Standard* et *HighPerformance* (Basé sur itinéraires).

- Vous ne pouvez spécifier qu’ **une seule**  combinaison de stratégies pour une connexion donnée.

- Vous devez spécifier tous les algorithmes et paramètres pour IKE (mode principal) et IPsec (mode rapide). Vous n’êtes pas en droit de spécifier de stratégie partielle.

- Consulter les spécifications de votre fournisseur de périphérique VPN pour vous assurer que la stratégie est prise en charge sur vos périphériques VPN locaux. Les connexions site à site ne peuvent pas être établies si les stratégies sont incompatibles.

## <a name="part-1---workflow-to-create-and-set-ipsecike-policy"></a>Partie 1 - Workflow de création et de définition d’une stratégie IPsec/IKE

Cette section décrit le workflow nécessaire pour créer et mettre à jour la stratégie IPsec/IKE sur une connexion VPN site à site :

1. Créer un réseau virtuel et une passerelle VPN

2. Créer une passerelle de réseau local pour la connexion entre différents locaux

3. Créer une stratégie IPsec/IKE avec les algorithmes et les paramètres sélectionnés

4. Créer une connexion IPsec avec la stratégie IPsec/IKE

5. Ajouter/mettre à jour/supprimer une stratégie IPsec/IKE pour une connexion existante

Les instructions de cet article vous aident à préparer et configurer les stratégies IPsec/IKE, comme indiqué dans la figure suivante :

![Préparer et configurer les stratégies IPsec/IKE](media/azure-stack-vpn-s2s/site-to-site.png)

## <a name="part-2---supported-cryptographic-algorithms-and-key-strengths"></a>Partie 2 - Algorithmes de chiffrement pris en charge et forces des clés

Le tableau suivant liste les algorithmes de chiffrement pris en charge et les forces de clés configurables par les clients Azure Stack :

| IPsec/IKEv2                                          | Options                                                                  |
|------------------------------------------------------|--------------------------------------------------------------------------|
| Chiffrement IKEv2                                     | AES256, AES192, AES128, DES3, DES                                        |
| Intégrité IKEv2                                      | SHA384, SHA256, SHA1, MD5                                                |
| Groupe DH                                             | ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, aucun         |
| Chiffrement IPsec                                     | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, Aucun |
| Intégrité IPsec                                      | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5                       |
| Groupe PFS                                            | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, Aucun                         |
| Durée de vie de l’AS en mode rapide                                       | (Facultatif : les valeurs par défaut sont utilisées si rien n’est spécifié)<br />                         Secondes (entier ; min. 300 / 27 000 secondes par défaut)<br />                         Ko (entier ; min. 1 024 / 102 400 000 Ko par défaut) |
| Sélecteur de trafic                                     | Les sélecteurs de trafic basés sur des stratégies ne sont pas pris en charge dans Azure Stack.         |

- La configuration de votre périphérique VPN local doit correspondre aux algorithmes et paramètres suivants, spécifiés dans la stratégie IPsec/IKE Azure ou les contenir :

  - Algorithme de chiffrement IKE (Mode principal / Phase 1)
  - Algorithme d’intégrité IKE (Mode principal / Phase 1)
  - Groupe DH (Mode principal / Phase 1)
  - Algorithme de chiffrement IPsec (Mode rapide / Phase 2)
  - Algorithme d’intégrité IPsec (Mode rapide / Phase 2)
  - Groupe PFS (Mode rapide / Phase 2)
  - Les durées de vie de l’AS sont uniquement des spécifications locales, elles n’ont pas besoin de correspondre.

- Si GCMAES est utilisé pour l’algorithme de chiffrement IPsec, vous devez sélectionner le même algorithme GCMAES et la même longueur de clé pour l’intégrité IPsec. Par exemple, vous devez utiliser GCMAES128 pour les deux.

- Dans le tableau précédent :

  - IKEv2 correspond au Mode principal ou à la Phase 1
  - IPsec correspond au Mode rapide ou à la Phase 2
  - Groupe DH spécifie le groupe Diffie-Hellmen utilisé dans le Mode principal ou à la Phase 1
  - Groupe PFS spécifie le groupe Diffie-Hellmen utilisé dans le Mode rapide ou à la Phase 2

- La durée de vie de l’association de sécurité IKEv2 en mode principal est fixée à 28 800 secondes sur les passerelles VPN Azure Stack.

Le tableau suivant répertorie les groupes Diffie-Hellman correspondants pris en charge par la stratégie personnalisée :

| Groupe Diffie-Hellman | DHGroup   | PFSGroup      | Longueur de clé    |
|----------------------|-----------|---------------|---------------|
| 1                    | DHGroup1  | PFS1          | MODP 768 bits  |
| 2                    | DHGroup2  | PFS2          | MODP 1 024 bits |
| 14                   | DHGroup14<br/>DHGroup2048 | PFS2048       | MODP 2 048 bits |
| 19                   | ECP256    | ECP256        | ECP 256 bits   |
| 20                   | ECP384    | ECP384        | ECP 384 bits   |
| 24                   | DHGroup24 | PFS24         | MODP 2 048 bits |

Pour plus d’informations, consultez les [RFC3526](https://tools.ietf.org/html/rfc3526) et [RFC5114](https://tools.ietf.org/html/rfc5114).

## <a name="part-3---create-a-new-site-to-site-vpn-connection-with-ipsecike-policy"></a>Partie 3 - Créer une connexion VPN site à site avec une stratégie IPsec/IKE

Cette section vous guide tout au long des étapes de création d’une connexion VPN site à site avec une stratégie IPsec/IKE. Les étapes suivantes créent la connexion, comme indiqué dans la figure suivante :

![stratégie site à site](media/azure-stack-vpn-s2s/site-to-site.png)

Pour obtenir des instructions détaillées sur la création d’une connexion VPN site à site, consultez [Créer une connexion VPN site à site](/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell).

### <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que les prérequis suivants sont remplis :

- Un abonnement Azure. Si vous n’avez pas déjà un abonnement Azure, vous pouvez activer vos  [avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou vous inscrire pour obtenir un  [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).

- Applets de commande Azure Resource Manager PowerShell. Consultez  [Installer PowerShell pour Azure Stack](../operator/azure-stack-powershell-install.md) pour plus d’informations sur l’installation des applets de commande PowerShell.

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Étape 1 : création du réseau virtuel, de la passerelle VPN et de la passerelle de réseau local

#### <a name="1-declare-variables"></a>1. Déclarer des variables

Pour cet exercice, commencez par déclarer les variables suivantes. Veillez à remplacer les espaces réservés par vos propres valeurs durant la configuration dans un environnement de production :

```powershell
$Sub1 = "<YourSubscriptionName>"
$RG1 = "TestPolicyRG1"
$Location1 = "East US 2"
$VNetName1 = "TestVNet1"
$FESubName1 = "FrontEnd"
$BESubName1 = "Backend"
$GWSubName1 = "GatewaySubnet"
$VNetPrefix11 = "10.11.0.0/16"
$VNetPrefix12 = "10.12.0.0/16"
$FESubPrefix1 = "10.11.0.0/24"
$BESubPrefix1 = "10.12.0.0/24"
$GWSubPrefix1 = "10.12.255.0/27"
$DNS1 = "8.8.8.8"
$GWName1 = "VNet1GW"
$GW1IPName1 = "VNet1GWIP1"
$GW1IPconf1 = "gw1ipconf1"
$Connection16 = "VNet1toSite6"
$LNGName6 = "Site6"
$LNGPrefix61 = "10.61.0.0/16"
$LNGPrefix62 = "10.62.0.0/16"
$LNGIP6 = "131.107.72.22"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Se connecter à votre abonnement et créer un groupe de ressources

Pour utiliser les applets de commande Resource Manager, passez au mode PowerShell. Pour plus d’informations, consultez  [Se connecter à Azure Stack en tant qu’utilisateur avec PowerShell](azure-stack-powershell-configure-user.md).

Ouvrez la console PowerShell et connectez-vous à votre compte. Utilisez l’exemple suivant pour faciliter votre connexion :

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>3. Créer le réseau virtuel, la passerelle VPN et la passerelle de réseau local

L’exemple suivant crée le réseau virtuel **TestVNet1** avec trois sous-réseaux et la passerelle VPN. Quand vous substituez des valeurs, pensez toujours à nommer votre sous-réseau de passerelle de manière spécifique **GatewaySubnet**. Si vous le nommez autrement, la création de votre passerelle échoue.

```powershell
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1 = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1

$subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" `
-VirtualNetwork $vnet1

$gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 `
-Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku VpnGw1

New-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 `
-Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix `
$LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---create-a-site-to-site-vpn-connection-with-an-ipsecike-policy"></a>Étape 2 - Créer une connexion VPN site à site avec une stratégie IPsec/IKE

#### <a name="1-create-an-ipsecike-policy"></a>1. Créez une stratégie IPsec/IKE.

Cet exemple de script crée une stratégie IPsec/IKE avec les algorithmes et les paramètres suivants :

- IKEv2 : AES128, SHA1, DHGroup14
- IPsec : AES256, SHA256, aucun, durée de vie de l’association de sécurité de 14 400 secondes et 102 400 000 Ko

```powershell
$ipsecpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup none -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

Si vous utilisez des algorithmes GCMAES pour IPsec, vous devez utiliser les mêmes algorithme GCMAES et longueur de clé pour le chiffrement IPsec et l’intégrité IPsec.

#### <a name="2-create-the-site-to-site-vpn-connection-with-the-ipsecike-policy"></a>2. Créer la connexion VPN site à site avec la stratégie IPsec/IKE

Créez une connexion VPN site à site et appliquez la stratégie IPsec/IKE que vous avez créée auparavant.

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$lng6 = Get-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'Azs123'
```

> [!IMPORTANT]
> Une fois qu’une stratégie IPsec/IKE est spécifiée sur une connexion, la passerelle VPN Azure ne fait qu’envoyer ou accepter la proposition IPsec/IKE avec les algorithmes de chiffrement et puissances de clé spécifiés sur cette connexion particulière. Vérifiez que votre périphérique VPN local pour la connexion utilise ou accepte la combinaison de stratégies appropriée, sinon le tunnel VPN site à site ne sera pas établi.

## <a name="part-4---update-ipsecike-policy-for-a-connection"></a>Partie 4 - Mise à jour de la stratégie IPsec/IKE pour une connexion

La section précédente a expliqué comment gérer la stratégie IPsec/IKE pour une connexion site à site existante. La section ci-après décrit les opérations suivantes sur une connexion :

1. Afficher la stratégie IPsec/IKE d’une connexion
2. Ajouter la stratégie IPsec/IKE à une connexion ou la mettre à jour
3. Supprimer la stratégie IPsec/IKE d’une connexion

> [!NOTE]
> La stratégie IPsec/IKE est prise en charge uniquement sur les passerelles VPN  *Standard* et *HighPerformance*  (Basé sur itinéraires). Elle ne fonctionne pas sur la référence de passerelle *De base*.

### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1. Afficher la stratégie IPsec/IKE d’une connexion

L’exemple suivant montre comment configurer la stratégie IPsec/IKE sur une connexion. Les scripts se poursuivent également à partir des exercices précédents :

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

La dernière commande liste la stratégie IPsec/IKE actuelle configurée sur la connexion, le cas échéant. L’exemple suivant est un exemple de sortie pour la connexion :

```shell
SALifeTimeSeconds : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption : AES256
IpsecIntegrity : SHA256
IkeEncryption : AES128
IkeIntegrity : SHA1
DhGroup : DHGroup14
PfsGroup : None
```

Si aucune stratégie IPsec/IKE n’est configurée, la commande `$connection6.policy` obtient un retour vide. Cela ne signifie pas qu’IPsec/IKE n’est pas configuré sur la connexion. Cela signifie qu’il n’existe pas de stratégie IPsec/IKE personnalisée. La connexion réelle utilise la stratégie par défaut négociée entre votre périphérique VPN local et la passerelle VPN Azure.

### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2. Ajouter ou mettre à jour une stratégie IPsec/IKE pour une connexion

Les étapes permettant d’ajouter une nouvelle stratégie ou de mettre à jour une stratégie existante sur une connexion sont les mêmes : créez une stratégie, puis appliquez-la à la connexion.

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$newpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

$connection6.SharedKey = "AzS123"

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

Vous pouvez à nouveau établir la connexion pour vérifier si la stratégie est mise à jour :

```powershell
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

La sortie de la dernière ligne devrait être celle illustrée dans l’exemple suivant :

```shell
SALifeTimeSeconds : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption : AES256
IpsecIntegrity : SHA256
IkeEncryption : AES128
IkeIntegrity : SHA1
DhGroup : DHGroup14
PfsGroup : None
```

### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>3. Supprimer une stratégie IPsec/IKE d’une connexion

Une fois que vous avez supprimé la stratégie personnalisée d’une connexion, la passerelle VPN Azure rétablit la  [proposition IPsec/IKE par défaut](azure-stack-vpn-gateway-settings.md#ipsecike-parameters), puis renégocie avec votre périphérique VPN local.

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.SharedKey = "AzS123"
$currentpolicy = $connection6.IpsecPolicies[0]
$connection6.IpsecPolicies.Remove($currentpolicy)

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6
```

Vous pouvez utiliser ce script pour vérifier si la stratégie a été supprimée de la connexion.

## <a name="next-steps"></a>Étapes suivantes

- [Paramètres de configuration de la passerelle VPN pour Azure Stack](azure-stack-vpn-gateway-settings.md)
