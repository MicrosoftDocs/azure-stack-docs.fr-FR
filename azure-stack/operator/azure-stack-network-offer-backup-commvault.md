---
title: Ajouter Commvault à la Place de marché Azure Stack Hub
description: Découvrez comment ajouter Commvault à la Place de marché Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/28/2019
ms.openlocfilehash: d2d12e032e639cb9b272affb4beed3a3a439b2cb
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76881705"
---
# <a name="add-commvault-to-the-azure-stack-hub-marketplace"></a>Ajouter Commvault à la Place de marché Azure Stack Hub

Cet article explique comment utiliser CommVault Live Sync pour mettre à jour une machine virtuelle de récupération située sur une unité d’échelle Azure Stack Hub distincte. Vous pouvez télécharger et proposer CommVault comme solution de sauvegarde et de réplication pour vos utilisateurs. 

## <a name="notes-for-commvault"></a>Notes pour CommVault

- Votre utilisateur doit installer le logiciel de sauvegarde et de réplication sur une machine virtuelle dans son abonnement Azure Stack Hub source. Azure Site Recovery et Sauvegarde Azure peuvent offrir un emplacement hors Stack pour stocker vos sauvegardes et images de récupération. Tous deux requièrent la création d’un coffre Recovery Services dans Azure avant de télécharger les images logicielles à installer sur votre Azure Stack Hub à partir des emplacements suivants : [Serveur de sauvegarde Azure](https://go.microsoft.com/fwLink/?LinkId=626082&clcid=0x0409) et [Azure Site Recovery](https://aka.ms/unifiedinstaller_eus).  
    
- Vous pouvez avoir besoin de licences pour un logiciel tiers (si vous avez choisi cette option).
- Vos utilisateurs peuvent avoir besoin d’une aide pour connecter leurs sources et cibles via une passerelle de réseau virtuel (VPN) ou une adresse IP publique sur l’hôte de sauvegarde et de réplication.
- Abonnement cloud Azure cible ou abonnement sur une cible de récupération Azure Stack Hub.
- Groupe de ressources cible et compte de stockage d’objets blob sur une cible de récupération Azure Stack Hub.
- Dans certaines solutions, vous devez créer des machines virtuelles dans l’abonnement cible qui doivent s’exécuter en continu pour recevoir les modifications du serveur source. Dans [Sauvegarder votre machine virtuelle sur Azure Stack Hub avec CommVault](../user/azure-stack-network-howto-backup-commvault.md), CommVault Live Sync crée les machines virtuelles de récupération cibles pendant la configuration initiale et les garde inactives (donc non facturables car non exécutées) jusqu’à ce que des modifications doivent être appliquées pendant un cycle de réplication.


## <a name="get-commvault-for-your-marketplace"></a>Se procurer CommVault pour votre Place de marché

1. Ouvrez le portail d’administration Azure Stack Hub.
2. Sélectionnez **Gestion de la Place de marché** > **Ajouter à partir d’Azure**.

    ![Commvault pour Azure Stack Hub](./media/azure-stack-network-offer-backup-commvault/get-commvault-for-marketplace.png)

3. Entrez `commvault`.
4. Sélectionnez **Commvault Trial**. Ensuite, sélectionnez **Télécharger**.


## <a name="next-steps"></a>Étapes suivantes

[Sauvegarder votre machine virtuelle sur Azure Stack Hub avec CommVault](../user/azure-stack-network-howto-backup-commvault.md)

[Vue d’ensemble de l’offre de services dans Azure Stack Hub](service-plan-offer-subscription-overview.md)
