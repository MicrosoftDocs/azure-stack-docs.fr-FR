---
title: Ajouter CommVault à la Place de marché Azure Stack | Microsoft Docs
description: Découvrez comment ajouter Commvault à la Place de marché Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/28/2019
ms.openlocfilehash: 787453550e9a8ed69aa9dfda0a03ea5e57c49d7a
ms.sourcegitcommit: ca358ea5c91a0441e1d33f540f6dbb5b4d3c92c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73802327"
---
# <a name="add-commvault-to-the-azure-stack-marketplace"></a>Ajouter Commvault à la Place de marché Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Cet article explique comment utiliser CommVault Live Sync pour mettre à jour une machine virtuelle de récupération située sur une unité d’échelle Azure Stack distincte. Vous pouvez télécharger et proposer CommVault comme solution de sauvegarde et de réplication pour vos utilisateurs. 

## <a name="notes-for-commvault"></a>Notes pour CommVault

- Votre utilisateur doit installer le logiciel de sauvegarde et de réplication sur une machine virtuelle dans son abonnement Azure Stack source. Azure Site Recovery et Sauvegarde Azure peuvent offrir un emplacement hors Stack pour stocker vos sauvegardes et images de récupération. Ils requièrent tous deux la création d’un coffre Recovery Services dans Azure avant de télécharger les images logicielles à installer sur votre instance Azure Stack à partir des emplacements suivants : [Serveur de sauvegarde Azure](https://go.microsoft.com/fwLink/?LinkId=626082&clcid=0x0409) et [Azure Site Recovery](https://aka.ms/unifiedinstaller_eus).  
    
- Vous pouvez avoir besoin de licences pour un logiciel tiers (si vous avez choisi cette option).
- Vos utilisateurs peuvent avoir besoin d’une aide pour connecter leurs sources et cibles via une passerelle de réseau virtuel (VPN) ou une adresse IP publique sur l’hôte de sauvegarde et de réplication.
- Abonnement cloud Azure cible ou abonnement sur une cible de récupération Azure Stack.
- Groupe de ressources cible et compte de Stockage Blob sur une cible de récupération Azure Stack.
- Dans certaines solutions, vous devez créer des machines virtuelles dans l’abonnement cible qui doivent s’exécuter en continu pour recevoir les modifications du serveur source. Dans [Sauvegarder votre machine virtuelle sur Azure Stack avec CommVault](../user/azure-stack-network-howto-backup-commvault.md), CommVault Live Sync crée les machines virtuelles de récupération cibles pendant la configuration initiale et les garde inactives (et donc non facturables car non exécutées) jusqu’à ce que les modifications soient appliquées pendant un cycle de réplication.


## <a name="get-commvault-for-your-marketplace"></a>Se procurer CommVault pour votre Place de marché

1. Ouvrez le portail d’administration Azure Stack.
2. Sélectionnez **Gestion de la Place de marché** > **Ajouter à partir d’Azure**.

    ![Commvault pour Azure Stack](./media/azure-stack-network-offer-backup-commvault/get-commvault-for-marketplace.png)

3. Entrez `commvault`.
4. Sélectionnez **Commvault Trial**. Ensuite, sélectionnez **Télécharger**.


## <a name="next-steps"></a>Étapes suivantes

[Sauvegarder votre machine virtuelle sur Azure Stack avec CommVault](../user/azure-stack-network-howto-backup-commvault.md)

[Vue d’ensemble de l’offre de services dans Azure Stack](service-plan-offer-subscription-overview.md)
