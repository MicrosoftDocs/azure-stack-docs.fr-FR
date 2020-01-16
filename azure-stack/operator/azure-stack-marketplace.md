---
title: Vue d’ensemble de la Place de marché Azure Stack Hub | Microsoft Docs
description: Vue d’ensemble de la Place de marché Azure Stack Hub et de ses éléments.
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
ms.date: 01/07/2020
ms.author: sethm
ms.reviewer: ihcherie
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: aea624bc47dec82133e3a438c21f984509492ceb
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75882281"
---
# <a name="azure-stack-hub-marketplace-overview"></a>Vue d’ensemble de la Place de marché Azure Stack Hub

La Place de marché Azure Stack Hub est un regroupement de services, d’applications et de ressources personnalisés pour Azure Stack Hub. Les ressources incluent des réseaux, des machines virtuelles, des stockages, etc. Utilisez la Place de marché Azure Stack Hub pour créer des ressources et déployer de nouvelles applications, ou parcourez et choisissez les éléments à utiliser. Pour utiliser un élément de Place de Marché, les utilisateurs doivent s’abonner à une offre qui leur accorde l’accès à l’élément.

En tant qu’opérateur Azure Stack Hub, vous décidez des éléments à ajouter (publier) sur la Place de marché Azure Stack Hub. Vous pouvez publier des éléments tels que des bases de données, des services d’application, et bien plus. La publication rend ces éléments visibles par tous les utilisateurs. Vous pouvez publier des éléments personnalisés que vous créez. Vous pouvez également publier des éléments à partir d’une [liste croissante d’éléments de Place de marché Azure](azure-stack-marketplace-azure-items.md). Quand vous publiez un élément sur la Place de marché Azure Stack Hub, les utilisateurs peuvent le voir dans les cinq minutes qui suivent.

> [!CAUTION]  
> Tous les artefacts d’élément de la galerie, notamment les images et les fichiers JSON, sont accessibles sans authentification, une fois qu’ils sont disponibles sur la Place de marché Azure Stack Hub. Pour plus d’informations sur la publication d’éléments de marketplace personnalisés, consultez [Créer et publier un article de la Place de marché](azure-stack-create-and-publish-marketplace-item.md).

Pour ouvrir Place de marché, dans la console d’administration, sélectionnez **+ Créer une ressource**.

![Créer une ressource dans le portail administrateur Azure Stack Hub](media/azure-stack-marketplace/marketplace1.png)

## <a name="marketplace-items"></a>Éléments du Marketplace

Un élément de la Place de marché Azure Stack Hub est un service, une application ou une ressource que vos utilisateurs peuvent télécharger et utiliser. Tous les éléments de la Place de marché Azure Stack Hub sont visibles par tous les utilisateurs, notamment les éléments d’administration tels que les plans et les offres. L’affichage de ces éléments d’administration ne nécessite pas d’abonnement, mais ils ne fonctionnent pas avec les utilisateurs.

Chaque élément du Marketplace comporte :

* Modèle Azure Resource Manager pour l’approvisionnement de la ressource.
* Les métadonnées telles que les chaînes, les icônes et autres supports marketing.
* Des informations de mise en forme permettant d’afficher l’élément dans le portail.

Chaque élément publié sur la Place de marché Azure Stack Hub utilise le format Azure Gallery Package (.azpkg). Ajoutez des ressources de déploiement ou de runtime (code, fichiers .zip avec logiciels ou images de machine virtuelle) à Azure Stack Hub séparément, et non dans le cadre de l’élément de Place de marché.

Avec la version 1803 et les versions ultérieures, Azure Stack Hub convertit les images en fichiers partiellement alloués au moment de leur téléchargement à partir d’Azure, ou quand vous chargez des images personnalisées. Ce processus ajoute du temps lors de l’ajout d’une image, mais économise de l’espace et accélère le déploiement de ces images. La conversion s’applique uniquement aux nouvelles images. Les images existantes ne sont pas modifiées.

## <a name="next-steps"></a>Étapes suivantes

* [Télécharger des éléments de Place de marché existants depuis Azure, et les publier sur Azure Stack Hub](azure-stack-download-azure-marketplace-item.md)  
* [Créer et publier un élément personnalisé de Place de marché Azure Stack Hub](azure-stack-create-and-publish-marketplace-item.md)
