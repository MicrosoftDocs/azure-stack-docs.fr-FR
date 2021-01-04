---
title: Principes de bases de l’administration d’Azure Stack Hub - MDC
titleSuffix: Azure Stack Hub
description: Découvrez les principes de bases de l’administration d’Azure Stack Hub. Consultez des informations de support supplémentaires et les étapes suivantes.
author: justinha
ms.topic: article
ms.date: 10/26/2020
ms.author: justinha
ms.reviewer: thoroet
ms.lastreviewed: 10/26/2020
ms.openlocfilehash: 5124494511d1e9c28bddce5cb42ba8e253c53807
ms.sourcegitcommit: 5fbc60b65d27c916ded7a95ba4102328d550c7e5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97598536"
---
# <a name="azure-stack-hub-administration-basics---modular-data-center-mdc"></a>Principes de bases de l’administration d’Azure Stack Hub - Modular Data Center (MDC)

Si vous débutez dans l’administration d’Azure Stack Hub, vous devez avoir connaissance de plusieurs choses. Cet article fournit une vue d’ensemble de votre rôle en tant qu’opérateur Azure Stack Hub, et explique ce que vous devez dire aux utilisateurs pour les aider à gagner en productivité.

## <a name="understand-the-builds"></a>Comprendre les builds

Si vous utilisez un système Azure Stack Hub intégré, les versions mises à jour d’Azure Stack Hub sont distribuées sous la forme de packages de mise à jour. Vous pouvez importer ces packages et les appliquer en utilisant la vignette **Mises à jour** dans le portail d’administration.

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

Azure Stack Hub continuera à prendre en charge de nouveaux services Azure. Pour obtenir la feuille de route prévue, voir le livre blanc [Azure Stack Hub: An extension of Azure](https://go.microsoft.com/fwlink/?LinkId=842846&clcid=0x409). Vous pouvez également consulter les nouvelles annonces dans les [billets de blog Azure Stack Hub](https://azure.microsoft.com/blog/tag/azure-stack-technical-preview).

## <a name="what-account-should-i-use"></a>Quel compte dois-je utiliser ?

Dans le cadre de la gestion d’Azure Stack Hub, vous devez prendre en considération certains points liés aux comptes, C’est particulièrement vrai pour les déploiements utilisant les services de fédération Active Directory (AD FS) Windows Server plutôt qu’Azure Active Directory (Azure AD) comme fournisseur d’identité.

| **Compte** | **Microsoft Azure** | **AD FS** |
|---|---|---|
| Administrateur local (.\Administrator) |   |
| Administrateur général Azure AD | Utilisé au cours de l’installation. <br> Propriétaire du fournisseur par défaut | Non applicable. |
| Compte pour le stockage étendu|   |   |
||

## <a name="what-tools-do-i-use-to-manage"></a>Quels outils dois-je utiliser pour la gestion ?

Vous pouvez utiliser le [portail administrateur](../../operator/azure-stack-manage-portals.md) ou PowerShell pour gérer Azure Stack Hub. Le moyen le plus simple de découvrir les concepts de base est d’utiliser le portail. Si vous souhaitez utiliser PowerShell, des étapes de préparation sont nécessaires. Avant de commencer, vous souhaiterez peut-être vous familiariser avec l’utilisation de PowerShell sur Azure Stack Hub. Pour plus d’informations, voir [Bien démarrer avec PowerShell dans Azure Stack Hub](../../user/azure-stack-powershell-overview.md).

Azure Stack Hub utilise Azure Resource Manager comme mécanisme de déploiement, de gestion et d’organisation sous-jacent. Si vous comptez gérer Azure Stack Hub et assister les utilisateurs, vous devez connaître Resource Manager. Consultez le livre blanc [Getting Started with Azure Resource Manager (Bien démarrer avec Azure Resource Manager)](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf).

## <a name="your-typical-responsibilities"></a>Vos responsabilités classiques

Vos utilisateurs souhaitent utiliser des services. De leur point de vue, votre rôle principal est de mettre ces services à leur disposition. Déterminez quels services vous voulez proposer et mettez ces services à disposition en créant des plans, des offres et des quotas. Pour plus d’informations, voir [Vue d’ensemble de l’offre de services dans Azure Stack Hub](../../operator/service-plan-offer-subscription-overview.md).

Vous devez également ajouter des éléments à la [Place de marché Azure Stack Hub](../../operator/azure-stack-marketplace.md). Le moyen le plus simple est de [Télécharger des éléments de la Place de marché à partir d’Azure dans Azure Stack Hub](../../operator/azure-stack-download-azure-marketplace-item.md).

Si vous souhaitez tester vos plans, offres et services, vous pouvez utiliser le [portail utilisateur](../../operator/azure-stack-manage-portals.md) (pas le portail administrateur).

En plus de fournir des services, vous devez effectuer toutes les tâches standard d’un opérateur pour veiller au bon fonctionnement d’Azure Stack Hub. Il s’agit notamment des tâches suivantes :

- Ajouter des comptes d’utilisateur pour le déploiement [Azure AD](../../operator/azure-stack-add-new-user-aad.md).
- [Définir des autorisations d’accès en utilisant le contrôle d’accès en fonction du rôle](../../operator/azure-stack-manage-permissions.md) (Cette tâche n’est pas limitée aux administrateurs.)
- [Superviser l’intégrité de l’infrastructure](../../operator/azure-stack-monitor-health.md).
- Gérer les ressources [réseau](../../operator/azure-stack-viewing-public-ip-address-consumption.md) et de [stockage](../../operator/azure-stack-manage-storage-accounts.md).
- [Démarrer et arrêter Azure Stack Hub](../../operator/azure-stack-start-and-stop.md).
- [Fonctionnement du stockage étendu](../../user/azure-stack-network-howto-extend-datacenter.md).
- [Gérer IoT Hub](https://docs.microsoft.com/azure/iot-hub/about-iot-hub).
- [Gérer Event Hub](https://docs.microsoft.com/azure-stack/operator/event-hubs-rp-overview?toc=%2Fazure-stack%2Ftdc%2Ftoc.json&bc=%2Fazure-stack%2Fbreadcrumb%2Ftoc.json&view=azs-2002&branch=release-tzl).
- [Gérer App Service](https://docs.microsoft.com/azure-stack/operator/azure-stack-app-service-overview?toc=%2Fazure-stack%2Ftdc%2Ftoc.json&bc=%2Fazure-stack%2Fbreadcrumb%2Ftoc.json&view=azs-2002&branch=release-tzl).
- Remplacer le matériel défectueux. Voici la liste des [parties remplaçables](../../operator/azure-stack-replace-component.md).
- [Obtenir de l’aide](https://docs.microsoft.com/azure-stack/operator/azure-stack-help-and-support-overview?toc=%2Fazure-stack%2Ftdc%2Ftoc.json&bc=%2Fazure-stack%2Fbreadcrumb%2Ftoc.json&view=azs-2002&branch=release-tzl).

## <a name="operator-tasks"></a>Tâches des opérateurs

Voici une liste des tâches quotidiennes, hebdomadaires et mensuelles pour les opérateurs :

# <a name="daily"></a>[Tous les jours](#tab/daily)

1. Vérifiez les alertes.
2. Vérifier l’état de la sauvegarde.
3. Mettre à jour la signature de Defender (systèmes déconnectés).
4. Vérifier les événements et l’intégrité du système Isilon dans OneFS.
5. Vérifier la capacité d’Isilon.

# <a name="weekly"></a>[Hebdomadaire](#tab/weekly)

1. Vérifier la capacité.
2. Exécuter `isi status –verbose` dans une connexion Avocent.

# <a name="monthly"></a>[Tous les mois](#tab/monthly)

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

Vous devez dire à vos utilisateurs comment utiliser des services dans Azure Stack Hub, se connecter à l’environnement et s’abonner aux offres. Outre la documentation personnalisée que vous voudrez peut-être fournir à vos utilisateurs, vous diriger ceux-ci vers la [Documentation pour les utilisateurs Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/).

### <a name="understand-how-to-work-with-services-in-azure-stack-hub"></a>Utiliser des services dans Azure Stack Hub

Avant d’utiliser des services et de créer des applications dans Azure Stack Hub, vos utilisateurs doivent connaître certaines informations. Par exemple, il existe des exigences propres à PowerShell et aux versions d’API. Il existe également des différences de fonctionnalité entre un service dans Azure et le service équivalent dans Azure Stack Hub. Assurez-vous que vos utilisateurs lisent les articles suivants :

- [Différences entre Azure Stack Hub et Azure quand vous utilisez des services et créez des applications](../../user/azure-stack-considerations.md)
- [Fonctionnalités des machines virtuelles Azure Stack Hub](../../user/azure-stack-vm-considerations.md)
- [Stockage Azure Stack Hub : différences et considérations](../../user/azure-stack-acs-differences.md)

Les informations contenues dans ces articles résument les différences entre un service dans Azure et dans Azure Stack Hub. Elles complètent les informations disponibles pour un service Azure dans la documentation Azure globale.

### <a name="connect-to-azure-stack-hub-as-a-user"></a>Se connecter à Azure Stack Hub en tant qu’utilisateur

Vos utilisateurs souhaiteront savoir comment [accéder au portail utilisateur](../../user/azure-stack-use-portal.md) ou comment se connecter par le biais de PowerShell. Dans un environnement de systèmes intégrés, l’adresse du portail utilisateur varie selon le déploiement. Vous devrez fournir à vos utilisateurs l’URL correcte.

S’ils utilisent PowerShell, les utilisateurs devront peut-être inscrire des fournisseurs de ressources avant de pouvoir utiliser des services. Un fournisseur de ressources gère un service. Par exemple, le fournisseur de ressources réseau gère des ressources comme des réseaux virtuels, des interfaces réseau et des équilibreurs de charge. Ils doivent [installer](../../operator/powershell-install-az-module.md) PowerShell, [télécharger](../../operator/azure-stack-powershell-download.md) des modules complémentaires et [configurer](../../user/azure-stack-powershell-configure-user.md) PowerShell (qui inclut l’inscription des fournisseurs de ressources).

### <a name="subscribe-to-an-offer"></a>S’abonner à une offre

Pour utiliser des services, un utilisateur doit d’abord [s’abonner à une offre](../../operator/azure-stack-subscribe-plan-provision-vm.md) que vous avez créée en tant qu’opérateur.

## <a name="where-to-get-support"></a>Où obtenir un support technique ?

Si vous souhaitez obtenir des informations de support technique pour des versions d’Azure Stack Hub antérieures à la version 1905, consultez [Stratégie de maintenance Azure Stack Hub](../../operator/azure-stack-servicing-policy.md).

Pour un système intégré, il existe un processus d’escalade et de résolution coordonné entre Microsoft et nos partenaires fabricants d’ordinateurs OEM.

En cas de problème au niveau des services cloud, le support technique s’effectue via les services de support technique Microsoft (CSS). Pour ouvrir une demande de support, sélectionnez l’icône d’aide et de support (point d’interrogation) en haut à droite du portail des administrateurs. Sélectionnez ensuite **Aide + support** et **Nouvelle demande de support** sous la section **Support**.

En cas de problème de déploiement, de correctif et de mise à jour, de matériel (notamment les unités remplaçables sur site) ou de logiciels d’une marque de matériel, comme les logiciels exécutés sur l’hôte du cycle de vie du matériel, contactez d’abord votre fabricant d’ordinateurs OEM.

Pour tout autre problème, contactez Microsoft CSS.

## <a name="next-steps"></a>Étapes suivantes

- [Gestion des régions dans Azure Stack Hub](../../operator/azure-stack-region-management.md)
