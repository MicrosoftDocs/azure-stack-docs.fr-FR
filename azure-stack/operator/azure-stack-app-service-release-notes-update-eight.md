---
title: Notes de publication de la mise à jour 8 d’App Service sur Azure Stack Hub | Microsoft Docs
description: Découvrez le contenu de la mise à jour huit d’App Service sur Azure Stack Hub, les problèmes connus et l’emplacement à partir duquel la télécharger.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/13/2020
ms.author: anwestg
ms.reviewer: ''
ms.openlocfilehash: 5da3ec3d838deabebbe70b04ad66d58241a42b5d
ms.sourcegitcommit: e47dc5fe9e59010ea3dbb9cb31abe15cfb821262
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76124699"
---
# <a name="app-service-on-azure-stack-hub-update-8-release-notes"></a>Notes de publication de la mise à jour 8 d’App Service sur Azure Stack Hub

Ces notes de publication décrivent les améliorations et les correctifs que la mise à jour 8 d’Azure Stack Hub apporte à Azure App Service, ainsi que les problèmes connus. Les problèmes connus ont été répartis selon qu’ils concernent le déploiement, le processus de mise à jour ou la build (après l’installation).

> [!IMPORTANT]
> Appliquez la mise à jour 1910 à votre système intégré Azure Stack ou déployez le dernier kit de développement Azure Stack avant de déployer Azure App Service 1.8.


## <a name="build-reference"></a>Référence de build

Le numéro de build d’App Service sur la mise à jour 8 d’Azure Stack Hub est **86.0.2.13**

### <a name="prerequisites"></a>Conditions préalables requises

Avant de passer au déploiement, consultez la [documentation Avant de commencer](azure-stack-app-service-before-you-get-started.md).

Avant de commencer à mettre à niveau Azure App Service sur Azure Stack vers la version 1.8 :

- vérifiez que tous les rôles sont prêts dans l’Administration Azure App Service dans le portail d’administration Azure Stack ;

- sauvegardez App Service et les bases de données master :
  - AppService_Hosting
  - AppService_Metering
  - Master

- Sauvegardez le partage de fichier de contenu d’application locataire ;

- Syndiquez l’**extension de script personnalisé** version **1.9.3** à partir de la Place de marché Azure

### <a name="new-features-and-fixes"></a>Nouvelles fonctionnalités et correctifs

Azure App Service sur la mise à jour 8 d’Azure Stack contient les améliorations et correctifs suivants :

- Mises à jour des **portails Locataire, Administration et Functions d’App Service, ainsi que des outils Kudu**. Cohérentes avec celles de la version du SDK du portail Azure Stack.

- Met à jour le **runtime d’Azure Functions** vers la **version 1.0.12615**.

- Mises à jour du service principal afin d’améliorer la fiabilité et l’envoi de messages d’erreur, ce qui facilite le diagnostic des problèmes courants.

- **Mises à jour des outils et frameworks d’applications suivants** :
  - ASP.NET Core 3.1.0
  - ASP.NET Core 3.0.1
  - ASP.NET Core 2.2.8
  - Module ASP.NET Core v2 13.1.19331.0
  - Azul OpenJDK 8.38.0.13
  - Tomcat 7.0.94
  - Tomcat 8.5.42
  - Tomcat 9.0.21
  - PHP 7.1.32
  - PHP 7.2.22
  - PHP 7.3.9
  - Kudu mis à jour vers la version 85.11024.4154
  - MSDeploy 3.5.80916.15
  - NodeJS 10.16.3
  - NPM 6.9.0
  - Git pour Windows 2.19.1.0

- **Mises à jour du système d’exploitation sous-jacent pour tous les rôles** :
  - [Mise à jour cumulative 2019-12 de Windows Server 2016 pour systèmes x64 (KB4530689)](https://support.microsoft.com/help/4530689)

- **Prise en charge des disques managés pour les nouveaux déploiements**

Tous les nouveaux déploiements d’Azure App Service sur Azure Stack Hub utiliseront des disques managés pour l’ensemble des machines virtuelles et des groupes de machines virtuelles identiques.  Tous les déploiements existants continueront à utiliser des disques non managés.

- **Protocole TLS 1.2 appliqué par des équilibreurs de charge frontaux**

À partir de cette mise à jour le protocole **TLS 1.2** sera appliqué à toutes les applications.

### <a name="post-deployment-steps"></a>Étapes de post-déploiement

> [!IMPORTANT]
> Si vous avez indiqué le fournisseur de ressources App Service avec une instance SQL Always On, vous DEVEZ [ajouter les bases de données appservice_hosting et appservice_metering à un groupe de disponibilité](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) et synchroniser les bases de données pour éviter toute perte de service en cas de basculement d’une base de données.

### <a name="known-issues-post-installation"></a>Problèmes connus (après l’installation)

- Les rôltes de travail ne peuvent pas atteindre le serveur de fichiers si App Service est déployé dans un réseau virtuel existant et si le serveur de fichiers est uniquement disponible sur le réseau privé, comme indiqué dans la documentation de déploiement d’Azure App Service sur Azure Stack

Si vous avez choisi de procéder au déploiement dans un réseau virtuel existant et une adresse IP interne pour vous connecter à votre serveur de fichiers, vous devez ajouter une règle de sécurité sortante, qui autorise le trafic SMB entre le sous-réseau Worker et le serveur de fichiers. Accédez au WorkersNsg dans le portail d’administration, puis ajoutez une règle de sécurité sortante comportant les propriétés suivantes :
 * Source : Quelconque
 * Plage de ports source : : *
 * Destination : Adresses IP
 * Plage d’adresses IP de destination : plage d’adresses IP de votre serveur de fichiers
 * Plage de ports de destination : 445
 * Protocole : TCP
 * Action : Allow
 * Priorité : 700
 * Nom : Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Problèmes connus des administrateurs cloud utilisant Azure App Service sur Azure Stack

Reportez-vous à la documentation des [notes de publication d’Azure Stack 1907](azure-stack-release-notes-1907.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour une présentation d’Azure App Service, consultez [Vue d’ensemble d’App Service sur Azure Stack](azure-stack-app-service-overview.md).
- Pour plus d’informations sur la préparation au déploiement d’App Service on Azure Stack, consultez [Avant de commencer avec App Service sur Azure Stack](azure-stack-app-service-before-you-get-started.md).
