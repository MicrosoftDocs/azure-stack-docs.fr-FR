---
title: Problèmes connus d’Azure Stack Hub pour Azure Stack Hub renforcé
description: Découvrez les problèmes connus d’Azure Stack Hub pour Azure Stack Hub renforcé.
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
ms.date: 12/08/2020
ms.author: sethm
ms.reviewer: sranthar
ms.lastreviewed: 12/08/2020
ms.openlocfilehash: 4b75024a1ff747d39f4f2194d0d86c3882475dee
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96941014"
---
# <a name="known-issues-in-azure-stack-hub-ruggedized"></a>Problèmes connus dans Azure Stack Hub renforcé 

Cet article liste les problèmes connus d’Azure Stack Hub renforcé. La liste est mise à jour lorsque de nouveaux problèmes sont identifiés.

## <a name="update"></a>Update

Pour plus d’informations sur les problèmes connus de mise à jour d’Azure Stack Hub, consultez [Résolution des problèmes des mises à jour dans Azure Stack Hub](../operator/azure-stack-troubleshooting.md#troubleshoot-azure-stack-hub-updates).

### <a name="update-failed-to-install-package-microsoftazurestackcomputeinstaller-to-ca-vm"></a>Échec de la mise à jour visant à installer Microsoft.AzureStack.Compute.Installer sur une machine virtuelle d’autorité de certification

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Lors de la mise à jour, un processus acquiert un verrou sur du nouveau contenu qui doit être copié sur une machine virtuelle d’autorité de certification. En cas d’échec de la mise à jour, le verrou est libéré.
- Correction : Reprenez la mise à jour.
- Occurrence : Rare

## <a name="portal"></a>Portail

### <a name="administrative-subscriptions"></a>Abonnements d’administration

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Les deux abonnements d’administration qui ont été introduits avec la version 1804 ne doivent pas être utilisés. Les types d’abonnements sont **Metering (Compteur)** et **Consumption (Consommation)** .
- Correction : Si vous disposez de ressources s’exécutant sur ces deux abonnements, recréez-les dans des abonnements utilisateur.
- Occurrence : Courant

## <a name="networking"></a>Mise en réseau

### <a name="network-security-groups"></a>Network Security Group

#### <a name="denyalloutbound-rule-cannot-be-created"></a>Impossible de créer la règle DenyAllOutbound

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Il n’est pas possible de créer une règle **DenyAllOutbound** explicite dans un groupe de sécurité réseau pendant la création d’une machine virtuelle. En effet, elle empêcherait toute communication nécessaire au bon déploiement de la machine virtuelle. Elle refuserait également les deux adresses IP essentielles requises pour déployer des machines virtuelles : Adresse IP DHCP : 169.254.169.254 et adresse IP DNS : 168.63.129.16.

- Correction : Autorisez le trafic sortant vers Internet lors de la création de la machine virtuelle, puis modifiez le groupe de sécurité réseau pour bloquer le trafic requis une fois la création de la machine virtuelle terminée.
- Occurrence : Courant

### <a name="virtual-network-gateway"></a>Passerelle de réseau virtuel

#### <a name="documentation"></a>Documentation

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Les liens vers la documentation dans la page de vue d’ensemble de la passerelle de réseau virtuel pointent vers la documentation spécifique à Azure au lieu d’Azure Stack Hub. Utilisez les liens suivants pour accéder à la documentation d’Azure Stack Hub :

  - [SKU de passerelle](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Connexions hautement disponibles](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Configurer le protocole BGP sur Azure Stack Hub](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [Circuits ExpressRoute](../operator/azure-stack-connect-expressroute.md)
  - [Spécifier des stratégies IPsec/IKE personnalisées](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

### <a name="load-balancer"></a>Load Balancer

#### <a name="load-balancer-directing-traffic-to-one-backend-vm-in-specific-scenarios"></a>Load Balancer dirigeant le trafic vers une machine virtuelle back-end dans des scénarios spécifiques

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge. 
- Cause : Lors de l’activation de l’**affinité de session** sur un équilibreur de charge, le hachage à 2 tuples utilise l’adresse IP physique au lieu des adresses IP privées affectées aux machines virtuelles. Dans les scénarios où le trafic dirigé vers l’équilibreur de charge arrive via un VPN, ou si toutes les machines virtuelles clientes (adresses IP sources) se trouvent sur le même nœud et que l’affinité de session est activée, tout le trafic est dirigé vers une seule machine virtuelle back-end.
- Occurrence : Courant
