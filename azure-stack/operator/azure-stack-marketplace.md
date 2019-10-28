---
title: Vue d’ensemble de la Place de marché Azure Stack | Microsoft Docs
description: Vue d’ensemble de la Place de marché Azure Stack et de ses éléments.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 60871cbb-eed2-433c-a76d-d605c7aec06c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/12/2019
ms.author: sethm
ms.reviewer: ihcherie
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: da8fdf20446e5ed60271e236f44939d439118b4b
ms.sourcegitcommit: b5eb024d170f12e51cc852aa2c72eabf26792d8d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72534100"
---
# <a name="azure-stack-marketplace-overview"></a>Vue d’ensemble de la Place de Marché Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

La Place de marché Azure Stack est une collection de services, d’applications et de ressources personnalisées pour Azure Stack. Les ressources incluent des réseaux, des machines virtuelles, des stockages, etc. Utilisez la Place de marché Azure Stack pour créer des ressources et déployer de nouvelles applications, ou parcourir et sélectionner les éléments que vous souhaitez utiliser. Pour utiliser un élément de Place de Marché, les utilisateurs doivent s’abonner à une offre qui leur accorde l’accès à l’élément.

En tant qu’opérateur Azure Stack, c’est vous qui décidez des éléments à ajouter (publier) dans Place de Marché Azure Stack. Vous pouvez publier des éléments tels que des bases de données, des services d’application, et bien plus. La publication rend ces éléments visibles par tous les utilisateurs. Vous pouvez publier des éléments personnalisés que vous créez. Vous pouvez également publier des éléments à partir d’une [liste croissante d’éléments de Place de marché Azure](azure-stack-marketplace-azure-items.md). Quand vous publiez un élément sur la Place de Marché Azure Stack, les utilisateurs peuvent le voir dans les cinq minutes qui suivent.

> [!CAUTION]  
> Tous les artefacts d’élément, y compris les images et fichiers JSON, sont accessibles sans authentification une fois disponibles sur la Place de marché Azure Stack. Pour plus d’informations sur la publication d’éléments de marketplace personnalisés, consultez [Créer et publier un article de la Place de marché](azure-stack-create-and-publish-marketplace-item.md).

Pour ouvrir Place de marché, dans la console d’administration, sélectionnez **+ Créer une ressource**.

![Créer une ressource dans le portail d’administration Azure Stack](media/azure-stack-marketplace/marketplace1.png)

## <a name="marketplace-items"></a>Éléments du Marketplace

Un élément Place de Marché Azure Stack est un service, une application ou une ressource que vos utilisateurs peuvent télécharger et utiliser. Tous les éléments Place de Marché Azure Stack sont visibles par tous les utilisateurs, y compris les éléments d’administration comme les plans et les offres. L’affichage de ces éléments d’administration ne nécessite pas d’abonnement, mais ils ne fonctionnent pas avec les utilisateurs.

Chaque élément de Place de marché comporte :

* Modèle Azure Resource Manager pour l’approvisionnement de la ressource.
* Les métadonnées telles que les chaînes, les icônes et autres supports marketing.
* Des informations de mise en forme permettant d’afficher l’élément dans le portail.

Chaque élément publié sur la Place de marché Azure Stack utilise le format Azure Gallery Package (.azpkg). Ajoutez les ressources de déploiement ou de runtime (code, fichiers .zip avec logiciels, ou images de machine virtuelle) à Azure Stack séparément, et non dans le cadre de l’élément de Place de Marché.

Avec la version 1803 ou plus, Azure Stack convertit les images en fichiers partiellement alloués lors du téléchargement à partir d’Azure ou lorsque vous téléchargez des images personnalisées. Ce processus ajoute du temps lors de l’ajout d’une image, mais économise de l’espace et accélère le déploiement de ces images. La conversion s’applique uniquement aux nouvelles images. Les images existantes ne sont pas modifiées.

## <a name="next-steps"></a>Étapes suivantes

* [Télécharger des éléments existants de la Place de marché Azure et les publier sur Azure Stack](azure-stack-download-azure-marketplace-item.md)  
* [Créer et publier un élément personnalisé de Place de marché Azure Stack](azure-stack-create-and-publish-marketplace-item.md)
