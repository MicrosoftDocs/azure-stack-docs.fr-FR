---
title: Décisions de déploiement déconnecté de Azure pour les systèmes intégrés Azure Stack | Microsoft Docs
description: Découvrez plus d’informations sur le déploiement déconnecté d’Azure pour les systèmes intégrés Azure Stack et les décisions de planification à prendre en compte.
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
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: wfayed
ms.lastreviewed: 12/11/2018
ms.openlocfilehash: 61d938ef1a3039c4b685c4df8f09577d9c7d3f50
ms.sourcegitcommit: a7207f4a4c40d4917b63e729fd6872b3dba72968
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71909281"
---
# <a name="azure-disconnected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Décisions relatives à la planification du déploiement déconnecté de Azure pour les systèmes intégrés Azure Stack
Une fois que vous avez décidé [comment intégrer Azure Stack dans votre environnement de cloud hybride](azure-stack-connection-models.md), vous pouvez finaliser vos décisions de déploiement Azure Stack.

Vous pouvez déployer et utiliser Azure Stack sans connexion à Internet. Cependant, avec un déploiement déconnecté, vous êtes limité à un magasin d’identités des services AD FS et au modèle de facturation basée sur la capacité. Comme que l’architecture multilocataire nécessite l’utilisation d’Azure Active Directory (Azure AD), elle n’est pas prise en charge pour les déploiements déconnectés.

Choisissez cette option si :
- Vous avez des restrictions de sécurité ou autres vous obligeant à déployer Azure Stack dans un environnement non connecté à Internet.
- Vous voulez empêcher l’envoi de données (notamment les données d’utilisation) à Azure.
- Vous voulez utiliser Azure Stack seulement comme solution de cloud privé, déployée sur votre intranet d’entreprise, et vous n’êtes pas intéressé par des scénarios hybrides.

> [!TIP]
> Parfois, ce type d’environnement est également nommé *scénario sous-marin*.

Un déploiement déconnecté ne vous empêche pas de connecter plus tard votre instance d’Azure Stack à Azure pour des scénarios hybrides de machine virtuelle de locataire. Autrement dit, vous ne disposez pas de connectivité à Azure lors du déploiement, ou vous ne souhaitez pas utiliser Azure AD comme magasin d’identités.

## <a name="features-that-are-impaired-or-unavailable-in-disconnected-deployments"></a>Fonctionnalités altérées ou non disponibles dans les déploiements déconnectés 
Azure Stack a été conçu pour fonctionner de façon optimale avec une connexion à Azure : il est donc important de noter que certaines fonctionnalités sont dégradées ou totalement indisponibles avec le mode déconnecté.

|Fonctionnalité|Impact dans le mode Déconnecté|
|-----|-----|
|Déploiement de machine virtuelle avec l’extension DSC pour configurer le post-déploiement de machine virtuelle|Altérée - L’extension DSC recherche le dernier WMF sur Internet.|
|Déploiement de machine virtuelle avec l’extension Docker pour exécuter des commandes Docker|Altérée - Docker recherche la dernière version sur Internet et cette recherche échoue.|
|Liens de documentation dans le portail de Azure Stack|Non disponible - Les liens, comme Donner votre avis, Aide et Démarrage rapide, utilisant une URL Internet, ne fonctionnent pas.|
|Correction/atténuation des alertes faisant référence à un guide de correction en ligne|Non disponible - Les liens de correction d’alerte utilisant une URL Internet ne fonctionnent pas.|
|Place de marché - Possibilité de sélectionner et d’ajouter des packages de la galerie directement à partir de la Place de marché Azure|Altérée - Quand vous déployez Azure Stack en mode déconnecté, vous ne pouvez pas utiliser le portail Azure Stack pour télécharger des éléments de la Place de marché. Toutefois, vous pouvez utiliser [l’outil de syndication de la Place de marché](azure-stack-download-azure-marketplace-item.md) pour télécharger des éléments de la Place de marché sur un ordinateur qui dispose d’une connexion Internet, puis les transférer vers votre environnement Azure Stack.|
|Utilisation des comptes de fédération Azure AD pour gérer un déploiement Azure Stack|Non disponible - Cette fonctionnalité nécessite une connexion à Azure. Des services de fédération Active Directory (AD FS) avec une instance de Active Directory local doivent être utilisés à la place.|
|App Services|Altérée - WebApps peut nécessiter un accès à Internet pour le contenu mis à jour.|
|Interface de ligne de commande (CLI)|Altérée - L’interface CLI a des fonctionnalités réduites pour l’authentification et le provisionnement des principaux de service.|
|Visual Studio - Cloud Discovery|Altérée - Cloud Discovery découvre des clouds différents ou ne fonctionne pas du tout.|
|Visual Studio - AD FS|Altérée - Seuls Visual Studio Enterprise et Visual Studio Code prennent en charge l’authentification AD FS.
Télémétrie|Non disponible - Les données de télémétrie pour Azure Stack ainsi que les packages de galerie de tiers qui dépendent des données de télémétrie.|
|Certificats|Non disponible - Une connexion à internet est nécessaire pour les services Liste de révocation de certificats (CRL) et Protocole OSCP (Online Certificate Status Protocol) dans le contexte de HTTPS.|
|Key Vault|Altérée - Un cas d’usage courant de Key Vault est une application lisant des secrets lors de l’exécution. Pour ce cas d’usage, l’application a besoin d’un principal de service dans l’annuaire. Dans Azure AD, les utilisateurs standard (non-administrateurs) sont autorisés par défaut à ajouter des principaux de service. Dans Azure AD (avec AD FS), ils ne le sont pas. Cette limitation constitue un obstacle à l’expérience de bout en bout, car un utilisateur doit toujours passer par un administrateur d’annuaire pour ajouter une application.

## <a name="learn-more"></a>En savoir plus
- Pour plus d’informations sur les cas d’usage, l’achat, les partenaires et les fabricants de matériel OEM, consultez la page produit [Azure Stack](https://azure.microsoft.com/overview/azure-stack/).
- Pour plus d’informations sur la feuille de route et la disponibilité géographique des systèmes intégrés Azure Stack, consultez le livre blanc : [Azure Stack : An extension of Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
- Pour plus d’informations sur l’empaquetage et les tarifs de Microsoft Azure Stack, [téléchargez le fichier PDF](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf). 

## <a name="next-steps"></a>Étapes suivantes
[Intégration au réseau du centre de données](azure-stack-network.md)
