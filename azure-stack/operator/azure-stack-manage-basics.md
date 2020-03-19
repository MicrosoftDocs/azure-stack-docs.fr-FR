---
title: Principes de bases de l’administration d’Azure Stack Hub
titleSuffix: Azure Stack Hub
description: Découvrez les principes de bases de l’administration d’Azure Stack Hub.
author: nicoalba
ms.topic: article
ms.date: 03/02/2020
ms.author: v-nialba
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 8f56dafbcc27e3ff4de9adcfbf5de27dea115bb3
ms.sourcegitcommit: 20d10ace7844170ccf7570db52e30f0424f20164
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79295064"
---
# <a name="azure-stack-hub-administration-basics"></a>Principes de bases de l’administration d’Azure Stack Hub

Si vous débutez dans l’administration d’Azure Stack Hub, vous devez avoir connaissance de plusieurs choses. Cet article fournit une vue d’ensemble de votre rôle en tant qu’opérateur Azure Stack Hub, et explique ce que vous devez dire aux utilisateurs pour les aider à gagner en productivité.

## <a name="understand-the-builds"></a>Comprendre les builds

Si vous utilisez un système Azure Stack Hub intégré, les versions mises à jour d’Azure Stack Hub sont distribuées sous la forme de packages de mise à jour. Vous pouvez importer ces packages et les appliquer en utilisant la vignette **Mises à jour** dans le portail d’administration.

## <a name="learn-about-available-services"></a>Découvrir les services disponibles

Prenez connaissance des services que vous pouvez mettre à la disposition de vos utilisateurs. Azure Stack Hub prend en charge un sous-ensemble de services Azure. La liste des services pris en charge continue d’évoluer.

### <a name="foundational-services"></a>Services fondamentaux

Par défaut, Azure Stack Hub comprend les services fondamentaux suivants quand vous le déployez :

- Calcul
- Stockage
- Mise en réseau
- Key Vault

Avec ces services fondamentaux, vous pouvez proposer une solution IaaS (infrastructure-as-a-service) à vos utilisateurs avec une configuration minimale.

### <a name="additional-services"></a>Services supplémentaires

Nous prenons en charge les services PaaS (platform-as-a-service) supplémentaires suivants :

- App Service
- Azure Functions
- Bases de données SQL et MySQL
- Kubernetes
- IoT Hub
- Event Hub

Ces services nécessitent une configuration supplémentaire avant de pouvoir être mis à disposition des utilisateurs. Pour plus d’informations, consultez **Tutoriels** et **Guides pratiques** > **Services d’offres** de notre [documentation pour les opérateurs Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/).

### <a name="service-roadmap"></a>Feuille de route des services

Azure Stack Hub continuera à prendre en charge de nouveaux services Azure. Pour obtenir la feuille de route prévue, consultez le livre blanc [Azure Stack Hub: An extension of Azure](https://go.microsoft.com/fwlink/?LinkId=842846&clcid=0x409) . Vous pouvez également consulter les nouvelles annonces dans les [billets de blog Azure Stack Hub](https://azure.microsoft.com/blog/tag/azure-stack-technical-preview).

## <a name="what-account-should-i-use"></a>Quel compte dois-je utiliser ?

Dans le cadre de la gestion d’Azure Stack Hub, vous devez prendre en considération certains points liés aux comptes, C’est particulièrement vrai pour les déploiements utilisant les services de fédération Active Directory (AD FS) Windows Server plutôt qu’Azure Active Directory (Azure AD) comme fournisseur d’identité.

| **Compte** | **Microsoft Azure** | **AD FS** |
|---|---|---|
| Administrateur local (.\Administrator) |   |
| Administrateur général Azure AD | Utilisé au cours de l’installation. <br> Propriétaire du fournisseur par défaut | Non applicable. |
| Compte pour le stockage étendu|   |   |
||

## <a name="what-tools-do-i-use-to-manage"></a>Quels outils dois-je utiliser pour la gestion ?

Vous pouvez utiliser le [portail administrateur](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-manage-portals?view=azs-2002) ou PowerShell pour gérer Azure Stack Hub. Le moyen le plus simple de découvrir les concepts de base est d’utiliser le portail. Si vous souhaitez utiliser PowerShell, des étapes de préparation sont nécessaires. Avant de commencer, vous souhaiterez peut-être vous familiariser avec l’utilisation de PowerShell sur Azure Stack Hub. Pour plus d’informations, consultez [Bien démarrer avec PowerShell dans Azure Stack Hub](https://review.docs.microsoft.com/en-us/azure-stack/user/azure-stack-powershell-overview?view=azs-2002).

Azure Stack Hub utilise Azure Resource Manager comme mécanisme de déploiement, de gestion et d’organisation sous-jacent. Si vous comptez gérer Azure Stack Hub et assister les utilisateurs, vous devez connaître Resource Manager. Consultez le livre blanc [Getting Started with Azure Resource Manager](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf) (Bien démarrer avec Azure Resource Manager).

## <a name="your-typical-responsibilities"></a>Vos responsabilités classiques

Vos utilisateurs souhaitent utiliser des services. De leur point de vue, votre rôle principal est de mettre ces services à leur disposition. Déterminez quels services vous voulez proposer et mettez ces services à disposition en créant des plans, des offres et des quotas. Pour plus d’informations, consultez [Vue d’ensemble de l’offre de services dans Azure Stack Hub](https://review.docs.microsoft.com/en-us/azure-stack/operator/service-plan-offer-subscription-overview?view=azs-2002).

Vous devez également ajouter des éléments à la [Place de marché Azure Stack Hub](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-marketplace?view=azs-2002). Le moyen le plus simple est de [Télécharger des éléments de la Place de marché à partir d’Azure dans Azure Stack Hub](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-download-azure-marketplace-item?view=azs-2002).

Si vous souhaitez tester vos plans, offres et services, vous pouvez utiliser le [portail utilisateur](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-manage-portals?view=azs-2002) (pas le portail administrateur).

En plus de fournir des services, vous devez effectuer toutes les tâches standard d’un opérateur pour veiller au bon fonctionnement d’Azure Stack Hub. Il s’agit notamment des tâches suivantes :

- Ajouter des comptes d’utilisateur pour le déploiement [Azure AD](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-add-new-user-aad?view=azs-2002) .
- [Définir des autorisations d’accès en utilisant le contrôle d’accès en fonction du rôle](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-manage-permissions?view=azs-2002) (Cette tâche n’est pas limitée aux administrateurs.)
- [Superviser l’intégrité de l’infrastructure](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-monitor-health?view=azs-2002).
- Gérer les ressources [réseau](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-viewing-public-ip-address-consumption?view=azs-2002) et de [stockage](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-manage-storage-accounts?view=azs-2002) .
- [Démarrer et arrêter Azure Stack Hub](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-start-and-stop?view=azs-2002&branch=release-tzl).
- [Fonctionnement du stockage étendu](https://review.docs.microsoft.com/en-us/azure-stack/tdc/extended-storage-operator-guide?view=azs-2002&branch=release-tzl).
- [Gérer IoT Hub](https://review.docs.microsoft.com/en-us/azure-stack/operator/iot-hub-rp-overview?toc=%2Fazure-stack%2Ftdc%2Ftoc.json&.bc=%2Fazure-stack%2Fbreadcrumb%2Ftoc.json&view=azs-2002&branch=release-tzl).
- [Gérer Event Hub](https://review.docs.microsoft.com/en-us/azure-stack/operator/event-hubs-rp-overview?toc=%2Fazure-stack%2Ftdc%2Ftoc.json&bc=%2Fazure-stack%2Fbreadcrumb%2Ftoc.json&view=azs-2002&branch=release-tzl).
- [Gérer App Service](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-app-service-overview?toc=%2Fazure-stack%2Ftdc%2Ftoc.json&bc=%2Fazure-stack%2Fbreadcrumb%2Ftoc.json&view=azs-2002&branch=release-tzl).
- Remplacer le matériel défectueux. Voici la liste des [parties remplaçables](https://review.docs.microsoft.com/en-us/azure-stack/tdc/cru-replaceable-parts?view=azs-2002&branch=release-tzl).
- [Obtenir de l’aide](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-help-and-support-overview?toc=%2Fazure-stack%2Ftdc%2Ftoc.json&bc=%2Fazure-stack%2Fbreadcrumb%2Ftoc.json&view=azs-2002&branch=release-tzl).

## <a name="operator-tasks"></a>Tâches des opérateurs

Voici une liste des tâches quotidiennes, hebdomadaires et mensuelles pour les opérateurs :

# <a name="daily"></a>[Quotidienne](#tab/daily)

1. Vérifiez les alertes.
2. Vérifier l’état de la sauvegarde.
3. Mettre à jour la signature de Defender (systèmes déconnectés).
4. Vérifier les événements et l’intégrité du système Isilon dans OneFS.
5. Vérifier la capacité d’Isilon.

# <a name="weekly"></a>[Hebdomadaire](#tab/weekly)

1. Vérifier la capacité.
2. Exécuter `isi status –verbose` dans une connexion Avocent.

# <a name="monthly"></a>[Mensuel](#tab/monthly)

1. Appliquer des packages de mises à jour mensuelles (Microsoft et OEM).
2. Valider la sauvegarde avec ASDK.
3. Gérer la Place de marché Azure Stack Hub (la garder actualisée).
4. Mettre à jour le microprogramme du commutateur et Avocent.
5. Récupérer la capacité de stockage.

# <a name="ondemand"></a>[À la demande](#tab/ondemand)

1. Appliquer une rotation des secrets.
2. Créer et mettre à jour des offres, des plans et des quotas.
3. Appliquer des packages de correctifs.
4. Appliquer des packages de correctifs.
5. Développer la capacité (nœuds et espace IP).
6. Exécuter `isi status –verbose` dans une connexion Avocent.
7. Restaurer les comptes de stockage.
8. Arrêter le système.
9. Collecter les journaux de diagnostic.

---

## <a name="what-to-tell-your-users"></a>Ce que vous devez dire à vos utilisateurs

Vous devez dire à vos utilisateurs comment utiliser des services dans Azure Stack Hub, se connecter à l’environnement et s’abonner aux offres. En plus de la documentation personnalisée que vous voudrez peut-être fournir à vos utilisateurs, vous pouvez les diriger vers la [Documentation pour les utilisateurs Azure Stack Hub](https://review.docs.microsoft.com/en-us/azure-stack/user/).

### <a name="understand-how-to-work-with-services-in-azure-stack-hub"></a>Comprendre comment utiliser des services dans Azure Stack Hub

Avant d’utiliser des services et de créer des applications dans Azure Stack Hub, vos utilisateurs doivent connaître certaines informations. Par exemple, il existe des exigences propres à PowerShell et aux versions d’API. Il existe également des différences de fonctionnalité entre un service dans Azure et le service équivalent dans Azure Stack Hub. Assurez-vous que vos utilisateurs lisent les articles suivants :

- [Différences entre Azure Stack Hub et Azure quand vous utilisez des services et créez des applications](https://review.docs.microsoft.com/en-us/azure-stack/user/azure-stack-considerations?view=azs-2002)
- [Fonctionnalités des machines virtuelles Azure Stack Hub](https://review.docs.microsoft.com/en-us/azure-stack/user/azure-stack-vm-considerations?view=azs-2002)
- [Stockage Azure Stack Hub : différences et considérations](https://review.docs.microsoft.com/en-us/azure-stack/user/azure-stack-acs-differences?view=azs-2002)

Les informations contenues dans ces articles résument les différences entre un service dans Azure et dans Azure Stack Hub. Elles complètent les informations disponibles pour un service Azure dans la documentation Azure globale.

### <a name="connect-to-azure-stack-hub-as-a-user"></a>Se connecter à Azure Stack Hub en tant qu’utilisateur

Vos utilisateurs souhaiteront savoir comment [accéder au portail utilisateur](https://review.docs.microsoft.com/en-us/azure-stack/user/azure-stack-use-portal?view=azs-2002) ou comment se connecter par le biais de PowerShell. Dans un environnement de systèmes intégrés, l’adresse du portail utilisateur varie selon le déploiement. Vous devrez fournir à vos utilisateurs l’URL correcte.

S’ils utilisent PowerShell, les utilisateurs devront peut-être inscrire des fournisseurs de ressources avant de pouvoir utiliser des services. Un fournisseur de ressources gère un service. Par exemple, le fournisseur de ressources réseau gère des ressources comme des réseaux virtuels, des interfaces réseau et des équilibreurs de charge. Ils doivent [installer](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-powershell-install?view=azs-2002) PowerShell, [télécharger des](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-powershell-download?view=azs-2002) modules complémentaires et [configurer](https://review.docs.microsoft.com/en-us/azure-stack/user/azure-stack-powershell-configure-user?view=azs-2002) PowerShell (qui inclut l’inscription des fournisseurs de ressources).

### <a name="subscribe-to-an-offer"></a>S’abonner à une offre

Pour utiliser des services, l’utilisateur doit d’abord [s’abonner à une offre](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-subscribe-plan-provision-vm?view=azs-2002) que vous avez créée en tant qu’opérateur.

## <a name="where-to-get-support"></a>Où obtenir un support technique ?

Si vous souhaitez obtenir des informations de support technique pour des versions d’Azure Stack Hub antérieures à la version 1905, consultez [Stratégie de maintenance d’Azure Stack Hub](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-servicing-policy?view=azs-2002).

Pour un système intégré, il existe un processus d’escalade et de résolution coordonné entre Microsoft et nos partenaires fabricants d’ordinateurs OEM.

En cas de problème au niveau des services cloud, le support technique s’effectue via les services de support technique Microsoft (CSS). Pour ouvrir une demande de support, sélectionnez l’icône d’aide et de support (point d’interrogation) en haut à droite du portail des administrateurs. Ensuite, sélectionnez **Aide + support** et **Nouvelle demande de support** sous la section **Support** .

En cas de problème de déploiement, de correctif et de mise à jour, de matériel (notamment les unités remplaçables sur site) ou de logiciels d’une marque de matériel, comme les logiciels exécutés sur l’hôte du cycle de vie du matériel, contactez d’abord votre fabricant d’ordinateurs OEM.

Pour tout autre problème, contactez Microsoft CSS.

## <a name="next-steps"></a>Étapes suivantes

- [Gestion des régions dans Azure Stack Hub](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-region-management?view=azs-2002)
