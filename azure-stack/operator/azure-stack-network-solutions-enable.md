---
title: Ajouter Fortinet FortiGate à la Place de marché Azure Stack Hub
description: Découvrez comment ajouter Fortinet FortiGate à votre Place de marché Azure Stack Hub, ce qui permet aux utilisateurs de créer des solutions réseau.
author: mattbriggs
ms.topic: how-to
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/30/2019
ms.openlocfilehash: a145a95207fb923d736582f79a315ae625d7223b
ms.sourcegitcommit: 53b0dde60a6435936a5e0cb9e931245f262d637a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/23/2020
ms.locfileid: "91107015"
---
# <a name="offer-a-network-solution-in-azure-stack-hub-with-fortinet-fortigate"></a>Proposer une solution réseau dans Azure Stack Hub avec Fortinet FortiGate

Vous pouvez ajouter le pare-feu nouvelle génération (NGFW) FortiGate à votre Place de marché Azure Stack Hub. FortiGate permet à vos utilisateurs de créer des solutions réseau telles qu’un réseau privé virtuel (VPN) pour Azure Stack Hub et VNET Peering. Une appliance virtuelle réseau contrôle le flux du trafic réseau depuis un réseau de périmètre vers d’autres réseaux ou sous-réseaux.

Pour plus d’informations sur FortiGate dans la Place de marché Azure, consultez [Solution de machine virtuelle unique avec le pare-feu de nouvelle génération Fortinet Fortigate](https://azuremarketplace.microsoft.com/marketplace/apps/fortinet.fortinet-FortiGate-singlevm).

## <a name="download-the-required-azure-stack-hub-marketplace-items"></a>Télécharger les éléments nécessaires de la Place de marché Azure Stack Hub

1. Ouvrez le portail administrateur Azure Stack Hub.

2. Sélectionnez **Marketplace management** (Gestion de la Place de marché), puis **Add from Azure** (Ajouter depuis Azure).

3. Tapez `Forti` dans la zone de recherche, puis sélectionnez **Download** (Télécharger) pour récupérer les dernières versions disponibles des éléments suivants :
    - Fortinet FortiGate-VM For Azure BYOL
    - FortiGate NGFW - Single VM Deployment (BYOL)

    ![Capture d’écran montrant les éléments téléchargés disponibles.](./media/azure-stack-network-solutions-enable/azure-stack-marketplace-FortiGate-fortinet.png)

4. Attendez que les éléments de votre Place de marché aient l’état **Downloaded** (Téléchargé). Le téléchargement des éléments peut prendre plusieurs minutes.

    ![FortiGate Fortinet Azure Stack Hub](./media/azure-stack-network-solutions-enable/image4.png)

## <a name="next-steps"></a>Étapes suivantes

- [Configurer un VPN pour Azure Stack Hub à l’aide de l’appliance virtuelle réseau FortiGate](../user/azure-stack-network-howto-vnet-to-onprem.md)  
- [Guide pratique pour connecter deux réseaux virtuels via l’appairage](../user/azure-stack-network-howto-vnet-to-vnet.md)  
- [Comment établir une connexion de réseau virtuel à réseau virtuel avec l’appliance virtuelle réseau Fortinet FortiGate](../user/azure-stack-network-howto-vnet-to-vnet-stacks.md)  
