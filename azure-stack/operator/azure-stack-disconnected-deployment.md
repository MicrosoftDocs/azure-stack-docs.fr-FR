---
title: Décisions pour le déploiement Azure déconnecté de systèmes intégrés Azure Stack Hub
description: Familiarisez-vous avec le déploiement déconnecté Azure des systèmes intégrés Azure Stack Hub ainsi qu'avec les décisions à prendre en compte en termes de planification.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 11/01/2019
ms.author: inhenkel
ms.reviewer: wfayed
ms.lastreviewed: 11/01/2019
ms.openlocfilehash: b03d00d4f90a16bbb1a299902097e23607e488b8
ms.sourcegitcommit: 97806b43314d306e0ddb15847c86be2c92ae001e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77510008"
---
# <a name="azure-disconnected-deployment-planning-decisions-for-azure-stack-hub-integrated-systems"></a>Décisions relatives à la planification du déploiement déconnecté Azure des systèmes intégrés Azure Stack Hub
Une fois que vous avez décidé [comment intégrer Azure Stack Hub dans votre environnement cloud hybride](azure-stack-connection-models.md), vous pouvez finaliser vos décisions de déploiement d'Azure Stack Hub.

Vous pouvez déployer et utiliser Azure Stack Hub sans connexion à Internet. Cependant, avec un déploiement déconnecté, vous êtes limité à un magasin d’identités des services AD FS et au modèle de facturation basée sur la capacité. Comme que l’architecture multilocataire nécessite l’utilisation d’Azure Active Directory (Azure AD), elle n’est pas prise en charge pour les déploiements déconnectés.

Choisissez cette option si :
- Vous êtes soumis à des restrictions de sécurité ou autres qui vous obligent à déployer Azure Stack Hub dans un environnement non connecté à Internet.
- Vous voulez empêcher l’envoi de données (notamment les données d’utilisation) à Azure.
- Vous souhaitez seulement utiliser Azure Stack Hub en tant que solution de cloud privé déployée sur votre intranet d'entreprise, et les scénarios hybrides ne vous intéressent pas.

> [!TIP]
> Parfois, ce type d’environnement est également nommé *scénario sous-marin*.

Un déploiement déconnecté ne vous empêche pas de connecter ultérieurement votre instance d'Azure Stack Hub à Azure pour les scénarios hybrides de machine virtuelle de locataire. Autrement dit, vous ne disposez pas de connectivité à Azure lors du déploiement, ou vous ne souhaitez pas utiliser Azure AD comme magasin d’identités.

## <a name="features-that-are-impaired-or-unavailable-in-disconnected-deployments"></a>Fonctionnalités altérées ou non disponibles dans les déploiements déconnectés 
Azure Stack Hub a été conçu pour fonctionner de façon optimale avec une connexion à Azure : il est donc important de noter que certaines fonctionnalités sont dégradées ou totalement indisponibles avec le mode déconnecté.

|Fonctionnalité|Impact dans le mode Déconnecté|
|-----|-----|
|Déploiement de machine virtuelle avec l’extension DSC pour configurer le post-déploiement de machine virtuelle|Altérée - L’extension DSC recherche le dernier WMF sur Internet.|
|Déploiement de machine virtuelle avec l’extension Docker pour exécuter des commandes Docker|Altérée - Docker recherche la dernière version sur Internet et cette recherche échoue.|
|Liens de documentation sur le portail Azure Stack Hub|Non disponible - Les liens, comme Donner votre avis, Aide et Démarrage rapide, utilisant une URL Internet, ne fonctionnent pas.|
|Correction/atténuation des alertes faisant référence à un guide de correction en ligne|Non disponible - Les liens de correction d’alerte utilisant une URL Internet ne fonctionnent pas.|
|Place de marché - Possibilité de sélectionner et d’ajouter des packages de la galerie directement à partir de la Place de marché Azure|Altérée - Lorsque vous déployez Azure Stack Hub en mode déconnecté, vous ne pouvez pas utiliser le portail Azure Stack Hub pour télécharger des éléments de la Place de marché. Toutefois, vous pouvez utiliser l'[outil de syndication de la Place de marché](azure-stack-download-azure-marketplace-item.md) pour télécharger des éléments de la Place de marché sur un ordinateur qui dispose d'une connexion Internet, puis les transférer vers votre environnement Azure Stack Hub.|
|Utilisation des comptes de fédération Azure AD pour gérer un déploiement Azure Stack Hub|Non disponible - Cette fonctionnalité nécessite une connexion à Azure. Des services de fédération Active Directory (AD FS) avec une instance de Active Directory local doivent être utilisés à la place.|
|App Services|Altérée - WebApps peut nécessiter un accès à Internet pour le contenu mis à jour.|
|Interface de ligne de commande (CLI)|Altérée - L’interface CLI a des fonctionnalités réduites pour l’authentification et le provisionnement des principaux de service.|
|Visual Studio - Cloud Discovery|Altérée - Cloud Discovery découvre des clouds différents ou ne fonctionne pas du tout.|
|Visual Studio - AD FS|Altérée - Seuls Visual Studio Enterprise et Visual Studio Code prennent en charge l’authentification AD FS.
Télémétrie|Non disponible - Les données de télémétrie relatives à Azure Stack Hub ainsi que les packages de galerie de tiers qui dépendent des données de télémétrie.|
|Certificats|Non disponible - Une connexion à internet est nécessaire pour les services Liste de révocation de certificats (CRL) et Protocole OSCP (Online Certificate Status Protocol) dans le contexte de HTTPS.|
|Key Vault|Altérée - Un cas d’usage courant de Key Vault est une application lisant des secrets lors de l’exécution. Pour ce cas d’usage, l’application a besoin d’un principal de service dans l’annuaire. Dans Azure AD, les utilisateurs standard (non-administrateurs) sont autorisés par défaut à ajouter des principaux de service. Dans Azure AD (avec AD FS), ils ne le sont pas. Cette limitation constitue un obstacle à l’expérience de bout en bout, car un utilisateur doit toujours passer par un administrateur d’annuaire pour ajouter une application.

## <a name="learn-more"></a>En savoir plus
- Pour plus d’informations sur les cas d’utilisation, l’achat, les partenaires et les fabricants de matériel OEM, voir la page du produit [Azure Stack Hub](https://azure.microsoft.com/overview/azure-stack/).
- Pour plus d’informations sur la feuille de route et la disponibilité géographique des systèmes intégrés Azure Stack Hub, voir le livre blanc : [Azure Stack Hub: An extension of Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
- Pour plus d’informations sur l’empaquetage et les tarifs de Microsoft Azure Stack Hub, [téléchargez le fichier PDF](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf). 

## <a name="next-steps"></a>Étapes suivantes
[Intégration au réseau du centre de données](azure-stack-network.md)
