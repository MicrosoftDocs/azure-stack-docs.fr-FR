---
title: Notions de base de l’administration de l’ASDK
description: Découvrez comment effectuer des tâches d’administration de base pour le Kit de développement Azure Stack (ASDK).
author: justinha
ms.topic: article
ms.date: 02/19/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 10/15/2019
ms.openlocfilehash: 70d2cfd7ce2dddd73117783c97003178281927d9
ms.sourcegitcommit: 5f4f0ee043ff994efaad44129ce49be43c64d5dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/16/2020
ms.locfileid: "84819377"
---
# <a name="asdk-admin-basics"></a>Notions de base de l’administration ASDK
Si vous débutez avec l’administration du kit de développement Azure Stack, vous devez prendre connaissance de plusieurs choses. Ce guide présente le rôle d’opérateur Azure Stack dans l’environnement d’évaluation. Si vous vous familiarisez avec ces informations, vos utilisateurs test seront très rapidement productifs.

Vous devez d’abord lire l’article [Qu’est-ce que le kit de développement Azure Stack ?](asdk-what-is.md) pour bien comprendre le rôle du kit de développement Azure Stack et ses limitations. Vous devez utiliser le kit de développement comme un « bac à sable », dans lequel vous pouvez évaluer Azure Stack, ainsi que développer et tester vos applications dans un environnement hors production. 

Comme Azure, Azure Stack innove constamment. De nouvelles builds du kit ASDK seront donc régulièrement publiées. Toutefois, vous ne pouvez pas mettre à niveau le kit ASDK comme vous le feriez pour les déploiements de systèmes intégrés Azure Stack. De fait, si vous souhaitez passer à la version la plus récente, vous devez entièrement [redéployer Azure Stack](asdk-redeploy.md). Vous ne pouvez pas appliquer les packages de mises à jour. Ce processus prend du temps, mais il vous permet toutefois d’essayer les fonctionnalités dès leur publication. 

## <a name="what-account-should-i-use"></a>Quel compte dois-je utiliser ?
Il faut prendre en compte certains points liés aux comptes pour gérer Azure Stack, C’est particulièrement vrai pour les déploiements utilisant les services de fédération Active Directory (AD FS) Windows Server plutôt qu’Azure Active Directory (Azure AD) comme fournisseur d’identité. Les considérations suivantes au sujet des comptes s’appliquent à la fois aux systèmes intégrés Azure Stack et aux déploiements ASDK :

|Compte|Azure AD|AD FS|
|-----|-----|-----|
|Administrateur local (.\Administrator)|Administrateur hôte ASDK|Administrateur hôte ASDK|
|AzureStack\AzureStackAdmin|Administrateur hôte ASDK<br><br>Peut être utilisé pour se connecter au portail d’administration Azure Stack<br><br>Accès pour afficher et administrer les anneaux Service Fabric|Administrateur hôte ASDK<br><br>Pas d’accès au portail d’administration Azure Stack<br><br>Accès pour afficher et administrer les anneaux Service Fabric<br><br>N’est plus propriétaire de l’abonnement du fournisseur par défaut (DPS)|
|AzureStack\CloudAdmin|Peut consulter et exécuter des commandes autorisées dans le point de terminaison privilégié|Peut consulter et exécuter des commandes autorisées dans le point de terminaison privilégié<br><br>Ne peut pas se connecter à l’hôte ASDK<br><br>Propriétaire de l’abonnement du fournisseur par défaut (DPS)|
|Administrateur général Azure AD|Utilisé au cours de l’installation<br><br>Propriétaire de l’abonnement du fournisseur par défaut (DPS)|Non applicable|
|

## <a name="what-tools-do-i-use-to-manage"></a>Quels outils dois-je utiliser pour la gestion ?
Vous pouvez utiliser le portail d’administration Azure Stack `https://adminportal.local.azurestack.external` ou PowerShell pour gérer Azure Stack. Le moyen le plus simple de découvrir les concepts de base est d’utiliser le portail. Si vous souhaitez utiliser PowerShell, vous devez installer [PowerShell pour Azure Stack](asdk-post-deploy.md#install-azure-stack-powershell) et [télécharger les outils Azure Stack à partir de GitHub](asdk-post-deploy.md#download-the-azure-stack-tools).

Azure Stack utilise Azure Resource Manager comme mécanisme de déploiement, de gestion et d’organisation sous-jacent. Si vous comptez gérer Azure Stack et assister les utilisateurs, vous devez connaître Azure Resource Manager. Pour plus d’informations, consultez le livre blanc [Getting Started with Azure Resource Manager](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf).

## <a name="your-typical-responsibilities"></a>Vos responsabilités classiques
Vos utilisateurs souhaitent utiliser des services. De leur point de vue, votre rôle principal est de mettre ces services à leur disposition. Le kit ASDK vous permet de connaître les services proposés et de savoir comment les rendre accessibles [en créant des plans, des offres et des quotas](../operator/azure-stack-tutorial-tenant-vm.md). Vous devez également ajouter des éléments tels que des images de machine virtuelle à la Place de Marché. Le moyen le plus simple est de [télécharger des éléments de la Place de marché](../operator/azure-stack-create-and-publish-marketplace-item.md) à partir d’Azure dans Azure Stack.

> [!NOTE]
> Si vous souhaitez tester vos plans, vos offres et vos services, vous devez utiliser le portail utilisateur `https://portal.local.azurestack.external`, et non le portail administrateur `https://adminportal.local.azurestack.external`.

En plus de fournir des services, vous devez effectuer toutes les tâches standard d’un opérateur Azure Stack pour veiller au bon fonctionnement du kit ASDK. Il s’agit notamment des tâches suivantes :
- Ajouter des comptes d’utilisateur pour les déploiements Azure AD ou AD FS.
- Affecter des rôles de contrôle d’accès en fonction du rôle (RBAC) (cela ne se limite pas aux seuls administrateurs).
- Surveiller l’intégrité de l’infrastructure.
- Gérer les ressources réseau et les ressources de stockage.
- Remplacer l’ordinateur hôte du kit de développement en cas de panne.

## <a name="where-to-get-support"></a>Où obtenir un support technique ?
Pour l'ADSK, vous pouvez poser des questions de support technique sur le [Forum MSDN Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack).

Vous pouvez également accéder aux forums en cliquant sur **Aide** (point d’interrogation) dans le coin supérieur droit du portail d’administration. Cliquez ensuite sur **Aide + support** pour ouvrir la **vue d'ensemble** Aide + support, qui contient un lien vers le forum. Les forums MSDN sont consultés régulièrement.  

> [!IMPORTANT]
> ASDK étant un environnement d’évaluation, le support Microsoft n’offre aucun support officiel.

## <a name="next-steps"></a>Étapes suivantes
[Déployer l’ASDK](asdk-install.md)

