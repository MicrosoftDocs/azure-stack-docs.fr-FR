---
title: Principes de bases de l’administration d’Azure Stack Hub
description: Découvrez les principes de bases de l’administration d’Azure Stack Hub.
author: justinha
ms.topic: article
ms.date: 05/29/2019
ms.author: justinha
ms.lastreviewed: 05/29/2019
ms.openlocfilehash: 53515145d4cbc1ad49ca25261ab65ac8bafdfbb5
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76882051"
---
# <a name="azure-stack-hub-administration-basics"></a>Principes de bases de l’administration d’Azure Stack Hub

Si vous débutez dans l’administration d’Azure Stack Hub, vous devez avoir connaissance de plusieurs choses. Cet article fournit une vue d’ensemble de votre rôle en tant qu’opérateur Azure Stack Hub, et explique ce que vous devez dire aux utilisateurs pour les aider à gagner en productivité.

## <a name="understand-the-builds"></a>Comprendre les builds

### <a name="integrated-systems"></a>Systèmes intégrés

Si vous utilisez un système Azure Stack Hub intégré, des versions mises à jour d’Azure Stack Hub sont distribuées sous la forme de packages de mise à jour. Vous pouvez importer ces packages et les appliquer en utilisant la vignette **Mises à jour** dans le portail d’administration.
 
### <a name="development-kit"></a>Kit de développement

Si vous utilisez le Kit de développement Azure Stack (ASDK), voir [Qu’est-ce qu’Azure Stack Hub ?](../asdk/asdk-what-is.md) pour en découvrir l’objectif et les limitations. Vous pouvez utiliser le Kit ASDK comme *bac à sable* dans lequel vous pouvez évaluer Azure Stack Hub, ainsi que développer et tester vos applications dans un environnement hors production. Pour plus d’informations sur le déploiement, consultez [Déploiement du Kit de développement Azure Stack](../asdk/asdk-install.md).

Comme Azure, nous innovons rapidement. Nous publierons régulièrement de nouvelles builds. Si vous utilisez le Kit ASDK et souhaitez passer à la build la plus récente, vous devez [redéployer Azure Stack Hub](../asdk/asdk-redeploy.md). Vous ne pouvez pas appliquer les packages de mises à jour. Ce processus prend du temps, mais l’avantage est que vous pouvez essayer les fonctionnalités les plus récentes. La documentation ASDK sur notre site web s’applique à la build la plus récente.

## <a name="learn-about-available-services"></a>Découvrir les services disponibles

Vous devez savoir quels services vous pouvez mettre à disposition de vos utilisateurs. Azure Stack Hub prend en charge un sous-ensemble de services Azure. La liste des services pris en charge continue d’évoluer.

**Services fondamentaux**

Par défaut, Azure Stack Hub comprend les « services fondamentaux » suivants quand vous le déployez :

- Calcul
- Stockage
- Mise en réseau
- Key Vault

Avec ces services fondamentaux, vous pouvez proposer une solution IaaS (Infrastructure-as-a-Service) à vos utilisateurs avec une configuration minimale.

**Services supplémentaires**

Actuellement, nous prenons en charge les services PaaS (Platform-as-a-Service) supplémentaires suivants :

- App Service
- Azure Functions
- Bases de données SQL et MySQL
- Kubernetes (en préversion)

Ces services nécessitent une configuration supplémentaire avant de pouvoir être mis à disposition des utilisateurs. Pour plus d’informations, voir les « Didacticiels » et les sections « Guides pratiques/Services d’offres » de notre documentation pour opérateur Azure Stack Hub.

**Feuille de route des services**

Azure Stack Hub continuera à prendre en charge de nouveaux services Azure. Pour obtenir la feuille de route prévue, voir le livre blanc [Azure Stack Hub: An extension of Azure](https://go.microsoft.com/fwlink/?LinkId=842846&clcid=0x409). Vous pouvez également consulter les nouvelles annonces dans les [billets de blog Azure Stack Hub](https://azure.microsoft.com/blog/tag/azure-stack-technical-preview).

## <a name="what-account-should-i-use"></a>Quel compte dois-je utiliser ?

Dans le cadre de la gestion d’Azure Stack Hub, vous devez prendre en considération certains points liés aux comptes, en particulier en ce qui concerne les déploiements utilisant les services de fédération Active Directory (AD FS) Windows Server plutôt qu’Azure Active Directory (Azure AD) comme fournisseur d’identité. Les considérations suivantes au sujet des comptes s’appliquent à tant aux systèmes intégrés Azure Stack Hub qu’aux déploiements ASDK :

|Compte|Azure AD|AD FS|
|-----|-----|-----|
|Administrateur local (. \Administrator)|Administrateur de l’hôte d’ASDK.|Administrateur de l’hôte d’ASDK.|
|AzureStack\AzureStackAdmin|Administrateur de l’hôte d’ASDK.<br><br>Peut être utilisé pour se connecter au portail administrateur Azure Stack Hub.<br><br>Accès pour visualiser et administrer les anneaux Service Fabric.|Administrateur de l’hôte d’ASDK.<br><br>Pas d’accès au portail administrateur Azure Stack Hub.<br><br>Accès pour visualiser et administrer les anneaux Service Fabric.<br><br>N’est plus propriétaire de l’abonnement du fournisseur par défaut.|
|AzureStack\CloudAdmin|Peut accéder à et exécuter des commandes autorisées dans le point de terminaison privilégié.|Peut accéder à et exécuter des commandes autorisées dans le point de terminaison privilégié.<br><br>Ne peut pas se connecter à l’hôte d’ASDK.<br><br>Propriétaire de l’abonnement du fournisseur par défaut (DPS).|
|Administrateur général Azure AD|Utilisé au cours de l’installation.<br><br>Propriétaire de l’abonnement du fournisseur par défaut (DPS).|Non applicable.|
|

## <a name="what-tools-do-i-use-to-manage"></a>Quels outils dois-je utiliser pour la gestion ?
 
Vous pouvez utiliser le [portail administrateur](azure-stack-manage-portals.md) ou PowerShell pour gérer Azure Stack Hub. Le moyen le plus simple de découvrir les concepts de base est d’utiliser le portail. Si vous souhaitez utiliser PowerShell, des étapes de préparation sont nécessaires. Avant de commencer, vous souhaiterez peut-être vous familiariser avec l’utilisation de PowerShell sur Azure Stack Hub. Pour plus d’informations, voir [Bien démarrer avec PowerShell dans Azure Stack Hub](../user/azure-stack-powershell-overview.md).

Azure Stack Hub utilise Azure Resource Manager comme mécanisme de déploiement, de gestion et d’organisation sous-jacent. Si vous comptez gérer Azure Stack Hub et assister les utilisateurs, vous pouvez vous familiariser avec Resource Manager. Consultez le livre blanc [Getting Started with Azure Resource Manager (Bien démarrer avec Azure Resource Manager)](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf).

## <a name="your-typical-responsibilities"></a>Vos responsabilités classiques

Vos utilisateurs souhaitent utiliser des services. De leur point de vue, votre rôle principal est de mettre ces services à leur disposition. Déterminez quels services vous voulez proposer, et mettez ces services à disposition en créant des plans, des offres et des quotas. Pour plus d’informations, voir [Vue d’ensemble de l’offre de services dans Azure Stack Hub](service-plan-offer-subscription-overview.md). 

Vous devez également ajouter des éléments à la [Place de marché Azure Stack Hub](azure-stack-marketplace.md). Le moyen le plus simple est de [Télécharger des éléments de la Place de marché à partir d’Azure dans Azure Stack Hub](azure-stack-download-azure-marketplace-item.md).

> [!NOTE]
> Si vous souhaitez tester vos plans, offres et services, vous pouvez utiliser le [portail utilisateur](azure-stack-manage-portals.md) (pas le portail administrateur).

En plus de fournir des services, vous devez effectuer toutes les tâches standard d’un opérateur pour veiller au bon fonctionnement d’Azure Stack Hub. Il s’agit notamment des tâches suivantes :

- Ajouter des comptes d’utilisateur (pour les déploiements [Azure AD](azure-stack-add-new-user-aad.md) ou [AD FS](azure-stack-add-users-adfs.md)).
- [Affecter des rôles de contrôle d’accès en fonction du rôle (RBAC)](azure-stack-manage-permissions.md) (Cette tâche n’est pas limitée aux seuls administrateurs.)
- [Superviser l’intégrité de l’infrastructure](azure-stack-monitor-health.md).
- Gérer les ressources [réseau](azure-stack-viewing-public-ip-address-consumption.md) et de [stockage](azure-stack-manage-storage-accounts.md).
- Remplacer le matériel défectueux. Par exemple, [remplacer un disque défaillant](azure-stack-replace-disk.md).

## <a name="what-to-tell-your-users"></a>Ce que vous devez dire à vos utilisateurs

Vous devez dire à vos utilisateurs comment utiliser des services dans Azure Stack Hub, se connecter à l’environnement et s’abonner aux offres. Outre la documentation personnalisée que vous voudrez peut-être fournir à vos utilisateurs, vous diriger ceux-ci vers la [Documentation pour les utilisateurs Azure Stack Hub](/azure-stack/user/).

**Utiliser des services dans Azure Stack Hub**

Avant d’utiliser des services et de créer des applications dans Azure Stack Hub, vos utilisateurs doivent connaître certaines informations. Par exemple, il existe des exigences propres à PowerShell et aux versions d’API. Il existe également des différences de fonctionnalité entre un service dans Azure et le service équivalent dans Azure Stack Hub. Assurez-vous que vos utilisateurs lisent les articles suivants :

- [Principales considérations relatives Utilisation de services ou génération d’applications pour Azure Stack Hub](../user/azure-stack-considerations.md)
- [Considérations relatives aux machines virtuelles dans Azure Stack Hub](../user/azure-stack-vm-considerations.md)
- [Stockage : Différences et considérations](../user/azure-stack-acs-differences.md)

Les informations contenues dans ces articles résument les différences entre un service dans Azure et dans Azure Stack Hub. Elles complètent les informations disponibles pour un service Azure dans la documentation Azure globale.

**Se connecter à Azure Stack Hub en tant qu’utilisateur**

Dans un environnement ASDK, si les utilisateurs se connectent à l’hôte ASDK autrement que via le Bureau à distance, ils peuvent configurer une connexion de réseau privé virtuel (VPN) pour se connecter à Azure Stack Hub. Voir [Se connecter à Azure Stack Hub](../asdk/asdk-connect.md).

Vos utilisateurs souhaiteront savoir comment [accéder au portail utilisateur](../user/azure-stack-use-portal.md) ou comment se connecter par le biais de PowerShell. Dans un environnement de systèmes intégrés, l’adresse du portail utilisateur varie selon le déploiement. Vous devrez fournir à vos utilisateurs l’URL correcte.

S’ils utilisent PowerShell, les utilisateurs devront peut-être inscrire des fournisseurs de ressources avant de pouvoir utiliser des services. Un fournisseur de ressources gère un service. Par exemple, le fournisseur de ressources réseau gère des ressources comme des réseaux virtuels, des interfaces réseau et des équilibreurs de charge. Ils doivent [installer](azure-stack-powershell-install.md) PowerShell, [télécharger](azure-stack-powershell-download.md) des modules complémentaires et [configurer](../user/azure-stack-powershell-configure-user.md) PowerShell (qui inclut l’inscription des fournisseurs de ressources).

**S’abonner à une offre**

Pour utiliser des services, un utilisateur doit d’abord [s’abonner à une offre](azure-stack-subscribe-plan-provision-vm.md) que vous avez créée en tant qu’opérateur.

## <a name="where-to-get-support"></a>Où obtenir un support technique ?

> [!Note]  
> Si vous souhaitez obtenir des informations de support technique pour des versions d’Azure Stack Hub antérieures à la version 1905, voir [Aide et support pour les versions d’Azure Stack Hub antérieures à la version 1905](azure-stack-servicing-policy.md).

### <a name="integrated-systems"></a>Systèmes intégrés

Pour un système intégré, il existe un processus d’escalade et de résolution coordonné entre Microsoft et nos partenaires fabricants d’ordinateurs OEM.

En cas de problème au niveau des services cloud, le support technique s’effectue via les services de support technique Microsoft (CSS). Pour ouvrir une demande de support, sélectionnez l’icône Aide et support (le point d’interrogation) dans le coin supérieur droit du portail d’administration, sélectionnez **Aide + support**, puis sélectionnez **Nouvelle demande de support** sous la section **Support**.

En cas de problème de déploiement, de correctif et de mise à jour, de matériel (y compris les unités remplaçables sur site) ou de logiciels d’une marque de matériel, comme les logiciels exécutés sur l’hôte du cycle de vie du matériel, contactez d’abord votre fabricant d’ordinateurs OEM.

Pour tout autre problème, contactez Microsoft CSS.

### <a name="azure-stack-development-kit-asdk"></a>Kit de développement Azure Stack (ASDK)

En ce qui concerne le Kit ASDK, vous pouvez poser des questions de support technique sur les [forums Microsoft](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). Pour accéder aux forums, sélectionnez l’icône Aide et support (le point d’interrogation) dans le coin supérieur droit du portail d’administration, sélectionnez **Aide + support**, puis sélectionnez **Forums MSDN** sous la section **Support**. Ces forums sont consultés régulièrement. Le Kit ASDK étant un environnement d’évaluation, les services de support technique Microsoft n’assurent aucun support officiel.

## <a name="next-steps"></a>Étapes suivantes

[Gestion des régions dans Azure Stack Hub](azure-stack-region-management.md)