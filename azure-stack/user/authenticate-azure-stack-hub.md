---
title: Guide pratique pour obtenir des informations d’authentification pour Azure Stack Hub | Microsoft Docs
description: Découvrez comment obtenir des informations d’authentification pour Azure Stack Hub
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/13/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/13/2019
ms.openlocfilehash: d04fcc37df7b610c85a2ba27a90f288bce5feae3
ms.sourcegitcommit: 7dd685fddf2f5d7a0c0a20fb8830ca5a061ed031
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76259900"
---
# <a name="get-authentication-information-for-azure-stack-hub"></a>Obtenir des informations d’authentification pour Azure Stack Hub

Pour s’authentifier auprès de Azure Stack Hub, vous devez fournir l’ID d’abonnement, l’ID de locataire et l’emplacement ainsi que le point de terminaison Resource Manager d’Azure Stack Hub. Vous pouvez obtenir ces valeurs à partir du [point de terminaison Resource Manager d’Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles-ruby?view=azs-1910#the-azure-stack-hub-resource-manager-endpoint) pour Azure Stack Hub. Vous pouvez aussi les obtenir en effectuant les étapes décrites dans cet article.

## <a name="values-needed-to-authenticate"></a>Valeurs nécessaires pour l’authentification

Les informations suivantes sont nécessaires :

-   **Identifiant d’abonnement**  

    Vous utilisez l’ID d’abonnement pour accéder à des offres dans Azure Stack Hub.

-   **Tenant ID**

    Un annuaire est un conteneur qui conserve des informations sur les utilisateurs, les applications, les groupes, et les principaux de service. Un locataire d’annuaire est une organisation, comme Microsoft ou votre propre entreprise.

-   **Lieu**

    L’emplacement, ou la région, est un ensemble de centres de données déployés dans un périmètre avec une latence définie et connectés via un réseau régional dédié à faible latence. Avec Azure Stack Hub, votre emplacement peut inclure un centre de. local au lieu d’une région Azure.

-   **Point de terminaison Resource Manager Azure Stack Hub**

    Microsoft Azure Resource Manager est un framework de gestion qui permet aux administrateurs de déployer, gérer et superviser les ressources Azure. Azure Resource Manager peut gérer ces tâches en groupe, plutôt qu’individuellement, dans une seule opération.

## <a name="get-the-subscription-id"></a>Obtenir l’ID d’abonnement

Pour obtenir l’ID d’abonnement :

1.  Connectez-vous au portail utilisateur Azure Stack Hub.

2.  Sélectionnez **Tous les services**.

    > ![Azure Stack Hub – informations d’authentification – ID d’abonnement – ID de locataire](./media/authenticate-azure-stack-hub/azure-stack-hub-auth-info.png)

3.  Sélectionnez **Abonnements**.

4.  Sélectionnez l’abonnement à utiliser.

5.  Copiez l’**ID d’abonnement** depuis **Vue d’ensemble**.

## <a name="get-the-tenant-id"></a>Obtenir l’ID de locataire

Pour obtenir l’ID de locataire :

1.  Connectez-vous au portail utilisateur Azure Stack Hub.

2.  Pointez sur votre nom d’utilisateur en haut à droite du panneau.

3.  L’**ID d’annuaire** est l’ID de locataire.

## <a name="get-the-azure-resource-manager-endpoint"></a>Obtenir le point de terminaison Azure Resource Manager

Le point de terminaison Azure Resource Manager est le point de terminaison des métadonnées pour le service de déploiement et de gestion pour Azure Stack Hub. Il fournit une couche de gestion qui vous permet de créer, mettre à jour et supprimer des ressources dans votre abonnement Azure.

Pour un système intégré, l’URL du point de terminaison Azure Resource Manager est :<br>`https://management.<location>.<fqdn>.com`

Pour faire en sorte que le point de terminaison de métadonnées pointe vers des propriétés comme Point de terminaison de la galerie, Point de terminaison Graph, Point de terminaison du portail, Point de terminaison de connexion et Audiences, l’URL est : `<ResourceManager>/metadata/endpoints?api-version=1.0`

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur l’utilisation du [Resource Manager d’Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles?view=azs-1910) avec Azure Stack Hub.
