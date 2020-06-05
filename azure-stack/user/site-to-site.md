---
title: Résoudre les problèmes de connexion VPN de site à site dans Azure Stack Hub
description: Procédures de résolution de problèmes à suivre après avoir configuré une connexion VPN de site à site entre un réseau local et un réseau virtuel Azure Stack Hub.
author: sethmanheim
ms.author: sethm
ms.date: 05/12/2020
ms.topic: article
ms.reviewer: sranthar
ms.lastreviewed: 05/12/2020
ms.openlocfilehash: 361fefb0cfac67d5d55c9b3391da68877d695da3
ms.sourcegitcommit: f4c2d5b87bc86ac4accb4d4df5b731b67d1a346c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2020
ms.locfileid: "84203117"
---
# <a name="troubleshoot-site-to-site-vpn-connections"></a>Résoudre les problèmes de connexion VPN de site à site

Cet article décrit les procédures de résolution de problèmes que vous pouvez suivre après avoir configuré une connexion VPN de site à site (S2S) entre un réseau local et un réseau virtuel Azure Stack Hub, lorsque la connexion cesse brusquement de fonctionner et que la reconnexion est impossible.

Si le problème lié à Azure Stack Hub n’est pas traité dans cet article, vous pouvez consulter le [forum MSDN Azure Stack Hub](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack).

Vous pouvez également envoyer une demande de support Azure. Consultez le [support technique Azure Stack Hub](../operator/azure-stack-manage-basics.md#where-to-get-support).

## <a name="initial-troubleshooting-steps"></a>Étapes de dépannage initiales

Les paramètres par défaut d’Azure Stack Hub pour IPsec/IKEV2 ont changé [à partir de la build 1910](../user/azure-stack-vpn-gateway-settings.md#ike-phase-1-main-mode-parameters) ; veuillez contacter votre opérateur Azure Stack Hub pour plus d’informations sur la version de build.

> [!IMPORTANT]
> Lors de l’utilisation d’un tunnel S2S, les paquets sont davantage encapsulés avec des en-têtes supplémentaires. Cette encapsulation augmente la taille globale du paquet. Dans ces scénarios, vous devez définir TCP **MSS** sur **1350**. Dans le cas où vos appareils VPN ne prendraient pas en charge la limitation de la taille maximale de segment, vous pouvez à la place définir l’unité de transmission maximale dans l’interface de tunnel sur **1400** octets. Pour plus d’informations, consultez [Réglage des performances TCPIP des réseaux virtuels](/azure/virtual-network/virtual-network-tcpip-performance-tuning).

- Vérifiez que la configuration VPN est basée sur le routage (IKEv2). Azure Stack Hub ne prend pas en charge les configurations (IKEv1) basées sur des stratégies.

- Vérifiez si vous utilisez un [appareil VPN et une version de système d’exploitation validés](/azure/vpn-gateway/vpn-gateway-about-vpn-devices#devicetable). Si l’appareil n’est pas un périphérique VPN validé, vous devez contacter son fabricant pour en vérifier la compatibilité.

- Vérifiez qu’il n’existe aucune plage d’adresses IP qui se chevauche entre le réseau virtuel Azure Stack Hub et le réseau local. Cela peut entraîner des problèmes de connectivité. 

- Vérifiez les adresses IP de pair VPN :

  - La définition de l’adresse IP dans l’objet **Passerelle de réseau local** dans Azure Stack Hub doit correspondre à l’adresse IP de l’appareil local.

  - La définition de l’adresse IP de la passerelle Azure Stack Hub qui est configurée sur l’appareil local doit correspondre à l’adresse IP de la passerelle Azure Stack Hub.

## <a name="status-not-connected---intermittent-disconnects"></a>État « Non connecté » – Déconnexions intermittentes

- Comparez la clé partagée de l’appareil VPN local et celle du VPN de réseau virtuel AzSH pour vous assurer que les clés correspondent. Pour afficher la clé partagée dans l’optique de la connexion VPN AzSH, utilisez l’une des méthodes suivantes :

  - **Portail du locataire Azure Stack Hub** : Accédez à la connexion de site à site de passerelle VPN que vous avez créée. Dans la section **Paramètres**, sélectionnez **Clés partagées**.

      :::image type="content" source="media/site-to-site/vpn-connection.png" alt-text="Connexion VPN":::

  - **Azure PowerShell**: Utilisez la commande PowerShell suivante :

      ```powershell
      Get-AzureRMVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group>
      ```

## <a name="status-connected--traffic-not-flowing"></a>État « connecté » – Le trafic ne s’écoule pas

- Recherchez et supprimez le routage défini par l’utilisateur (UDR) ou les groupes de sécurité réseau (NSG) sur le sous-réseau de passerelle, puis testez le résultat. Si le problème est résolu, validez les paramètres de l’itinéraire défini par l’utilisateur ou des groupes de sécurité réseau appliqués.

   Un itinéraire défini par l’utilisateur sur le sous-réseau de passerelle risque de limiter une partie du trafic et d’autoriser le reste du trafic. C’est pourquoi la connexion VPN peut sembler manquer de fiabilité pour une partie du trafic, et pas pour le reste du trafic.

- Vérifiez l’adresse d’interface externe de l’appareil VPN local. 

  - Si l’adresse IP de l’appareil VPN, accessible sur Internet, est incluse dans la définition du **réseau local** dans Azure Stack Hub, il se peut que vous subissiez des déconnexions occasionnelles.

  - L’interface externe de l’appareil doit être directement liée à Internet. Il ne doit y avoir aucune traduction d’adresses réseau (NAT) ni aucun pare-feu entre Internet et l’appareil.

  - Pour configurer le clustering de pare-feu dans le but d’obtenir une adresse IP virtuelle, vous devez détruire le cluster et exposer l’appliance VPN directement à une interface publique susceptible de s’interfacer avec la passerelle.

- Vérifiez que les sous-réseaux correspondent exactement.

  - Vérifiez que le ou les espaces d’adressage du réseau virtuel correspondent exactement entre le réseau virtuel Azure Stack Hub et les définitions locales.

  - Vérifiez que les sous-réseaux correspondent exactement dans **la passerelle réseau locale** et dans les définitions locales pour le réseau local.

## <a name="create-a-support-ticket"></a>Création d’un ticket de support

Si aucune des étapes précédentes ne résout votre problème, créez un [ticket de support](../operator/azure-stack-manage-basics.md#where-to-get-support) et utilisez l’[outil de collecte de journaux à la demande](../operator/azure-stack-configure-on-demand-diagnostic-log-collection.md) pour fournir des journaux.
