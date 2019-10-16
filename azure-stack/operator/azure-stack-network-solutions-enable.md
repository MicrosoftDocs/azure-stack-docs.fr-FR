---
title: Proposer une solution réseau dans Azure Stack avec Fortinet FortiGate | Microsoft Docs
description: Découvrez comment activer la solution réseau dans Azure Stack avec Fortinet FortiGate
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 09/30/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/30/2019
ms.openlocfilehash: 27012c491054043f45004d76787538091864577c
ms.sourcegitcommit: b2d19e12a50195bb8925879ee75c186c9604f313
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71962711"
---
# <a name="offer-a-network-solution-in-azure-stack-with-fortinet-fortigate"></a>Proposer une solution réseau dans Azure Stack avec Fortinet FortiGate

Vous pouvez ajouter le pare-feu Fortigate nouvelle génération dans votre Place de marché Azure Stack. FortiGate permet à vos utilisateurs de créer des solutions réseau telles qu’un réseau privé virtuel (VPN) pour Azure Stack et l’appairage de réseaux virtuels. Une appliance virtuelle réseau contrôle le flux du trafic réseau depuis un réseau de périmètre vers d’autres réseaux ou sous-réseaux. 

Pour plus d’informations sur FortiGate dans la Place de marché Azure, consultez [Solution de machine virtuelle unique avec le pare-feu Fortigate de nouvelle génération](https://azuremarketplace.microsoft.com/marketplace/apps/fortinet.fortinet-FortiGate-singlevm).

## <a name="download-the-required-azure-stack-marketplace-items"></a>Télécharger les éléments de la Place de marché Azure Stack requis

1.  Ouvrez le portail administrateur Azure Stack.

2.  Sélectionnez **Marketplace management** (Gestion de la Place de marché), puis **Add from Azure** (Ajouter depuis Azure).

3. Tapez `Forti` dans la zone de recherche, puis sélectionnez **Download** (Télécharger) pour récupérer les dernières versions disponibles des éléments suivants : 
    - Fortinet FortiGate-VM For Azure BYOL
    - FortiGate NGFW - Single VM Deployment (BYOL)

    ![Azure Stack FortiGate Fortinet](./media/azure-stack-network-solutions-enable/azure-stack-marketplace-FortiGate-fortinet.png)

2.  Attendez que les éléments de la Place de marché aient l’état **Downloaded** (Téléchargé). Le téléchargement des éléments peut prendre plusieurs minutes.

    ![Azure Stack FortiGate Fortinet](./media/azure-stack-network-solutions-enable/image4.png)

## <a name="next-steps"></a>Étapes suivantes

[Configurer un VPN pour Azure Stack à l’aide de l’appliance virtuelle réseau FortiGate](../user/azure-stack-network-howto-vnet-to-onprem.md)  
[Guide pratique pour connecter deux réseaux virtuels via l’appairage](../user/azure-stack-network-howto-vnet-to-vnet.md)  
[Comment établir une connexion de réseau virtuel à réseau virtuel avec l’appliance virtuelle réseau Fortinet FortiGate](../user/azure-stack-network-howto-vnet-to-vnet-stacks.md)  
