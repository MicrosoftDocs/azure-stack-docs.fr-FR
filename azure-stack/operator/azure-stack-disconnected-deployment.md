---
title: Décisions de déploiement déconnecté de Azure pour les systèmes intégrés Azure Stack | Microsoft Docs
description: Déterminez les décisions relatives à la planification du déploiement pour les déploiements à plusieurs nœuds de Azure Stack connectés à Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2019
ms.author: mabrigg
ms.reviewer: wfayed
ms.lastreviewed: 12/11/2018
ms.openlocfilehash: a3986bcdff911fb70957dcb7529a07f77b586c0a
ms.sourcegitcommit: b79a6ec12641d258b9f199da0a35365898ae55ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67131512"
---
# <a name="azure-disconnected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Décisions relatives à la planification du déploiement déconnecté de Azure pour les systèmes intégrés Azure Stack
Une fois que vous avez décidé [comment vous allez intégrer Azure Stack dans votre environnement de cloud hybride](azure-stack-connection-models.md), vous pouvez finaliser vos décisions de déploiement de Azure Stack.

Vous pouvez déployer et utiliser Azure Stack sans connexion à Internet. Toutefois, avec un déploiement déconnecté, vous êtes limité à un magasin d’identités AD FS et au modèle de facturation basée sur la capacité. Étant donné que l’architecture multilocataire nécessite l’utilisation d’Azure Active Directory (Azure AD), elle n’est pas prise en charge pour les déploiements déconnectés. 

Choisissez cette option si :
- Vous disposez de restrictions de sécurité (ou autre) vous obligeant à déployer Azure Stack dans un environnement non connecté à internet.
- Vous voulez empêcher l’envoi de données (y compris les données d’utilisation) à Azure.
- Vous souhaitez utiliser Azure Stack uniquement comme solution de cloud privé, déployée sur votre intranet d’entreprise, et vous n’êtes pas intéressé par des scénarios hybrides.

> [!TIP]
> Parfois, ce type d’environnement est également nommé *scénario sous-marin*.

Un déploiement déconnecté ne vous empêche pas de connecter plus tard votre instance de Azure Stack à Azure pour des scénarios hybrides de machine virtuelle de locataire. Autrement dit, vous ne disposez pas de connectivité à Azure lors du déploiement, ou vous ne souhaitez pas utiliser Azure AD comme magasin d’identités.

## <a name="features-that-are-impaired-or-unavailable-in-disconnected-deployments"></a>Fonctionnalités altérées ou non disponibles dans les déploiements déconnectés 
Azure Stack a été conçu pour fonctionner de façon optimale avec une connexion à Azure ; il est donc important de noter que certaines fonctionnalités sont altérées ou totalement indisponibles avec le mode Déconnecté. 

|Fonctionnalité|Impact dans le mode Déconnecté|
|-----|-----|
|Déploiement de machine virtuelle avec l’extension DSC pour configurer le post-déploiement de machine virtuelle|Altérée - L’extension DSC recherche le dernier WMF sur internet.|
|Déploiement de machine virtuelle avec l’extension Docker pour exécuter des commandes Docker|Altérée - Docker recherche la dernière version sur internet et cette recherche échoue.|
|Liens de documentation dans le portail de Azure Stack|Non disponible - Les liens, tels que Donner votre avis, Aide, Démarrage rapide, etc., utilisant une URL internet, ne fonctionnent pas.|
|Correction/atténuation des alertes faisant référence à un guide de correction en ligne|Non disponible - Les liens de correction d’alerte utilisant une URL internet ne fonctionnent pas.|
|Place de marché - Possibilité de sélectionner et d’ajouter des packages de galerie directement à partir de la Place de marché Azure|Altérée - Lorsque vous déployez Azure Stack en mode déconnecté (sans connexion Internet), vous ne pouvez pas utiliser le portail Azure Stack pour télécharger des éléments de la Place de marché. Toutefois, vous pouvez utiliser [l’outil de syndication de la Place de marché](azure-stack-download-azure-marketplace-item.md) pour télécharger des éléments de la Place de marché sur un ordinateur qui dispose d’une connexion Internet, puis les transférer vers votre environnement Azure Stack.|
|Utilisation des comptes de fédération Azure Active Directory pour gérer un déploiement de Azure Stack|Non disponible - Cette fonctionnalité nécessite une connexion à Azure. Des services de fédération Active Directory (AD FS) avec une instance de Active Directory local doivent être utilisés à la place.|
|App Services|Altérée - WebApps peut nécessiter un accès à Internet pour le contenu mis à jour.|
|Interface de ligne de commande (CLI)|Altérée - L’interface CLI affiche des fonctionnalités réduites en matière d’authentification et de provisionnement pour les principaux de service.|
|Visual Studio - Cloud Discovery|Altérée - Cloud Discovery détecte des clouds différents ou ne fonctionne pas du tout.|
|Visual Studio - AD FS|Altérée - Seul Visual Studio Enterprise prend en charge AD FS.
Télémétrie|Non disponible - Les données de télémétrie pour Azure Stack ainsi que les packages de galerie tiers qui dépendent des données de télémétrie.|
|Certificats|Non disponible - Une connexion à internet est nécessaire pour les services Liste de révocation de certificats (CRL) et Protocole OSCP (Online Certificate Status Protocol) dans le cadre du protocole HTTPS.|
|Coffre de clés|Altérée - Un cas d’usage courant de Key Vault est d’avoir une application lisant les secrets lors de l’exécution. Pour cela, l’application a besoin d’un principal de service dans le répertoire. Dans Azure Active Directory, les utilisateurs standard (non-administrateurs) sont autorisés par défaut à ajouter des principaux de service. Dans Active Directory (utilisant ADFS), ils ne peuvent pas. Cela constitue un obstacle à l’expérience de bout en bout, car un utilisateur doit toujours passer par un administrateur de répertoire pour ajouter une application.| 

## <a name="learn-more"></a>En savoir plus
- Pour plus d’informations sur les cas d’usage, l’achat, les partenaires et les fabricants de matériel OEM, consultez la page produit [Azure Stack](https://azure.microsoft.com/overview/azure-stack/).
- Pour plus d’informations sur la feuille de route et la disponibilité géographique des systèmes intégrés Azure Stack, consultez le livre blanc : [Azure Stack : An extension of Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
- Pour en savoir plus sur l’empaquetage et la tarification de Microsoft Azure Stack, [téléchargez le fichier PDF](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf). 

## <a name="next-steps"></a>Étapes suivantes
[Intégration au réseau du centre de données](azure-stack-network.md)
