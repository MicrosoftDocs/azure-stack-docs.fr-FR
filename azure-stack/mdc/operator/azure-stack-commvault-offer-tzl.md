---
title: Proposer l'élément Commvault de la Place de marché dans Azure Stack - MDC
description: Déployez Commvault à partir de la Place de marché Azure Stack pour un Modular Data Center (MDC).
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2020
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 10/26/2020
ms.openlocfilehash: c2f98725a93dbd471f257d8a038a989d9f892c68
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910837"
---
# <a name="offer-commvault-marketplace-item-in-azure-stack---modular-data-center-mdc"></a>Proposer l'élément Commvault de la Place de marché dans Azure Stack - Modular Data Center (MDC)

*S’applique à : Modular Data Center, Azure Stack Hub renforcé*

CommVault prend en charge la sauvegarde et la restauration des types de ressources suivants sur Azure Stack Hub :

- Sauvegarde au niveau des machines virtuelles
  - Machine virtuelle IaaS
  - Disques non managés
  - Disques managés
  - Pour plus d'informations, consultez [Sauvegarder des machines virtuelles](https://documentation.commvault.com/commvault/v11/article?p=86503.htm).

- Sauvegarde du compte de stockage
  - Objet blob
  - Pour plus d'informations, consultez [Présentation de Stockage Blob Azure](https://documentation.commvault.com/commvault/v11/article?p=30063.htm).

- Sauvegarde basée sur un agent
  - Système d'exploitation invité - Windows et Linux
  - Application - SQL, MySQL
  - Pour plus d'informations, consultez [Agents de sauvegarde](https://documentation.commvault.com/commvault/v11/article?p=14333.htm).

Vous pouvez déployer Commvault sur une machine externe et protéger les ressources Azure Stack Hub à distance. En outre, vous pouvez déployer CommVault en tant qu'appliance virtuelle sur Azure Stack Hub. Pour plus d'informations, consultez la documentation relative à [Azure Stack Hub](https://documentation.commvault.com/commvault/v11/article?p=86486.htm) sur le site de Commvault. Pour référence, Commvault publie également la [liste complète des fonctionnalités de Microsoft Azure](https://documentation.commvault.com/commvault/v11/article?p=109795_1.htm).

## <a name="deploy-from-azure-stack-hub-marketplace"></a>Déployer à partir de la Place de marché Azure Stack Hub

CommVault publie une image BYOL sur la Place de marché Azure, et active l'image à des fins de syndication sur Azure Stack Hub. La version minimale requise pour sauvegarder des machines virtuelles sur Azure Stack est la version SP16. Si vous envisagez d'utiliser une appliance virtuelle, veillez à procéder à une mise à jour vers la version SP16 (dernière [version à long terme prise en charge](https://documentation.commvault.com/commvault/v11/article?p=2617.htm)) ou vers la version SP17 (dernière version standard disponible).

| Cloud        | Version | Disponible à des fins de syndication | Prochaine mise à jour |
|--------------|---------|---------------------------|-------------|
| Azure public | SP13    | Oui                       | TBD         |
| Azure Gov    | SP13    | TBD                       | TBD         |

## <a name="download-from-azure-marketplace"></a>Télécharger à partir de la Place de marché Azure

Les opérateurs Azure Stack Hub peuvent télécharger des éléments sur la Place de marché Azure Stack locale pour les environnements connectés et déconnectés. Dans un environnement connecté, l'opérateur peut parcourir la liste des éléments disponibles à ajouter à partir d'Azure :

![Ajout à partir d’Azure](media/azure-stack-commvault-offer-tzl/add-from-azure.png)

## <a name="upload-and-publish-manually"></a>Charger et publier manuellement

Dans les environnements déconnectés, l'élément doit être téléchargé à partir d'Azure, puis chargé manuellement vers Azure Stack Hub. Pour plus d'informations, consultez les [instructions complètes relatives aux environnements connectés et déconnectés](../../operator/azure-stack-download-azure-marketplace-item.md).

## <a name="deployment-considerations"></a>Points à prendre en considération pour le déploiement

- Déployer d'une machine externe vers Azure Stack Hub
- Déployer en tant qu'appliance virtuelle sur Azure Stack Hub
- Bibliothèque de disques et bibliothèque de clouds
- Prise en compte de la ligne de vue du réseau
- Isolement au niveau de l'abonnement

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la protection des machines virtuelles IaaS, consultez Protection des machines virtuelles sur Azure Stack Hub.
