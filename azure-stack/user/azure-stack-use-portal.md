---
title: Utiliser le portail Azure Stack | Microsoft Docs
description: Découvrez comment accéder au portail utilisateur et l’utiliser dans Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2019
ms.author: mabrigg
ms.reviewer: efemmano
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: 629056556b04a7b5d19c2463b619f3da3a70f7e0
ms.sourcegitcommit: 72d45bb935db0db172d4d7c37d8e48e79e25af64
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68376898"
---
# <a name="use-the-azure-stack-portal"></a>Utiliser le portail Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Utilisez le portail Azure Stack pour vous abonner à des offres publiques et utiliser les services proposés par ces offres. Si vous avez déjà utilisé le portail Azure global, le fonctionnement du site vous semblera familier.

## <a name="access-the-portal"></a>Accéder au portail

Votre opérateur Azure Stack (fournisseur de services ou administrateur au sein de votre organisation) vous permettra de connaitre l’URL correcte pour accéder au portail.

- Pour un système intégré, l’URL varie en fonction de la région et du nom de domaine externe de votre opérateur, et son format est https://portal.&lt;*région*&gt;.&lt;*FQDN*&gt;.
- Si vous utilisez le Kit de développement Azure Stack (ASDK), l’adresse du portail est https://portal.local.azurestack.external.
- Le fuseau horaire par défaut pour tous les déploiements Azure Stack est défini sur le Temps universel coordonné (UTC). Vous pouvez sélectionner un fuseau horaire lors de l’installation d’Azure Stack, mais il est automatiquement rétabli par défaut au format UTC lors de l’installation.

## <a name="customize-the-dashboard"></a>Personnaliser le tableau de bord

Le tableau de bord contient un ensemble de vignettes par défaut. Vous pouvez sélectionner **Modifier le tableau de bord** pour modifier le tableau de bord par défaut, ou sélectionnez **Nouveau tableau de bord** pour créer un tableau de bord personnalisé. Vous pouvez facilement personnaliser un tableau de bord en ajoutant ou supprimant des vignettes. Par exemple, pour ajouter une vignette Compute, sélectionnez **+ Créer une ressource**. cliquez avec le bouton de droite sur **Capacité de calcul**, puis sélectionnez **Épingler au tableau de bord**.

![Capture d’écran du portail utilisateur d’Azure Stack](media/azure-stack-use-portal/userportal.png)

Pour restaurer les paramètres d’origine du tableau de bord :
1.  Sélectionnez **Modifier le tableau de bord**. 
2.  Cliquez avec le bouton droit et sélectionnez **Rétablir l’état par défaut**.

## <a name="create-subscription-and-browse-available-resources"></a>Créer un abonnement et parcourir les ressources disponibles

Si vous n’avez pas encore d’abonnement, vous devez vous abonner à une offre. Après cela, vous pouvez parcourir les ressources disponibles. Pour rechercher et créer des ressources, utilisez l’une des approches suivantes :

- Sélectionnez la vignette **Place de marché** du tableau de bord.
- Dans la vignette **Toutes les ressources**, sélectionnez **Créer des ressources**.
- Dans le volet de navigation de gauche, sélectionnez **+ Créer une ressource**.

## <a name="learn-how-to-use-available-services"></a>Découvrir comment utiliser les services disponibles

Si vous avez besoin d’aide pour savoir comment utiliser les services disponibles, différentes options peuvent être à votre disposition.

- Votre organisation ou votre fournisseur de services peuvent fournir leur propre documentation, ce qui est généralement le cas s’ils offrent des services ou applications personnalisés.
- Les applications tierces ont leur propre documentation.
- Pour les services cohérents avec Azure, nous recommandons vivement de consulter la documentation d’Azure Stack. Pour accéder à la documentation de l’utilisateur Azure Stack, sélectionnez l’icône Aide ( **?** ), puis sélectionnez **Aide + Support**.

    ![Option Aide + support dans l’interface utilisateur](media/azure-stack-use-portal/HelpAndSupport.png)

    Nous vous suggérons en particulier de consulter les articles suivants pour commencer :

    - [Principales considérations relatives Utilisation des services ou des applications de génération pour Azure Stack](azure-stack-considerations.md).
    - La section **Utiliser les services** de la documentation comporte un article sur les considérations pour chaque service. La page relative aux considérations décrit les différences entre le service proposé dans Azure et le service équivalent dans Azure Stack. Pour obtenir un exemple, voir [Considérations relatives aux machines virtuelles](azure-stack-vm-considerations.md). D’autres informations propres à Azure Stack peuvent figurer dans la section **Utiliser les services**.

      Vous pouvez utiliser la documentation Azure en tant que référence générale pour un service, mais devez avoir conscience de ces différences. Comprenez que les liens de documentation dans la vignette **Tutoriels de prise en main** pointent sur la documentation Azure.

## <a name="get-support"></a>Obtenir de l’aide

Si vous avez besoin d’assistance, contactez votre organisation ou votre fournisseur de services pour obtenir de l’aide.

Si vous utilisez le Kit de développement Azure Stack (ASDK), le [Forum Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) est la seule source de support.

## <a name="next-steps"></a>Étapes suivantes

[Principales considérations relatives à l’utilisation de services ou à la création d’applications pour Azure Stack](azure-stack-considerations.md)
