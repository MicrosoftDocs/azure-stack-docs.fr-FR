---
title: Ajouter Commvault à la Place de marché Azure Stack Hub
description: Découvrez comment ajouter Commvault à la Place de marché Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: f035ce5d08e88a6fa5844e405a4fa5a335fa3e5c
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96525487"
---
# <a name="add-commvault-to-azure-stack-hub-marketplace"></a>Ajouter Commvault à la Place de marché Azure Stack Hub

Cet article explique comment utiliser l’offre CommVault Live Sync pour mettre à jour une machine virtuelle de récupération située sur une unité d’échelle Azure Stack Hub distincte. Vous pouvez télécharger et proposer CommVault comme solution de sauvegarde et de réplication pour vos utilisateurs.

## <a name="notes-for-commvault"></a>Notes pour CommVault

- Votre utilisateur doit installer le logiciel de sauvegarde et de réplication sur une machine virtuelle dans son abonnement Azure Stack Hub source. Azure Site Recovery et Sauvegarde Azure peuvent offrir un emplacement hors Stack pour stocker vos sauvegardes et images de récupération. Ces deux services demandent la création d’un coffre Recovery Services dans Azure avant de télécharger les images logicielles à installer sur votre instance Azure Stack Hub. Les images logicielles peuvent être téléchargées à partir de : [Serveur de sauvegarde Azure](https://go.microsoft.com/fwLink/?LinkId=626082&clcid=0x0409) et [Azure Site Recovery](https://aka.ms/unifiedinstaller_eus).  

- Vous pouvez avoir besoin de licences pour ces logiciels tiers (si vous avez choisi cette option).
- Vos utilisateurs peuvent avoir besoin d’une aide pour connecter leurs sources et cibles via une passerelle VPN ou une adresse IP publique sur l’hôte de sauvegarde et de réplication.
- Ciblez l’abonnement au cloud Azure ou un abonnement sur une cible de récupération Azure Stack Hub.
- Ciblez le groupe de ressources et le compte de stockage d’objets blob sur une cible de récupération Azure Stack Hub.
- Pour certaines solutions, vous devez créer dans l’abonnement cible des machines virtuelles qui doivent s’exécuter en continu afin de recevoir les modifications du serveur source. Dans [Sauvegarder votre machine virtuelle sur Azure Stack Hub avec CommVault](../user/azure-stack-network-howto-backup-commvault.md), CommVault Live Sync crée les machines virtuelles de récupération cibles pendant la configuration initiale et les garde inactives (donc non facturables car non exécutées) jusqu’à ce que des modifications doivent être appliquées pendant un cycle de réplication.

## <a name="get-commvault-for-your-marketplace"></a>Se procurer CommVault pour votre place de marché

1. Ouvrez le portail administrateur Azure Stack Hub.
2. Sélectionnez **Gestion de la Place de marché** > **Ajouter à partir d’Azure**.

    ![Commvault pour Azure Stack Hub](./media/azure-stack-network-offer-backup-commvault/get-commvault-for-marketplace.png)

3. Entrez `commvault`.
4. Sélectionnez **Commvault Trial**. Ensuite, sélectionnez **Télécharger**.

## <a name="next-steps"></a>Étapes suivantes

- [Sauvegarder votre machine virtuelle sur Azure Stack Hub avec CommVault](../user/azure-stack-network-howto-backup-commvault.md)
- [Vue d’ensemble de l’offre de services dans Azure Stack Hub](service-plan-offer-subscription-overview.md)
