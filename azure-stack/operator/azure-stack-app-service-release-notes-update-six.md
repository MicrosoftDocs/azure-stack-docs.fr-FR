---
title: Notes de publication d’App Service sur Azure Stack Update 6 | Microsoft Docs
description: Découvrez le contenu de la mise à jour 6 d’App Service sur Azure Stack, les problèmes connus et où la télécharger.
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
ms.date: 06/10/2019
ms.author: anwestg
ms.reviewer: ''
ms.openlocfilehash: d3464681463cfb66a368210beed79d5ef4c28739
ms.sourcegitcommit: af63214919e798901399fdffef09650de4176956
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2019
ms.locfileid: "66828317"
---
# <a name="app-service-on-azure-stack-update-6-release-notes"></a>Notes de publication d’App Service sur Azure Stack Update 6

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Ces notes de publication décrivent les améliorations et les correctifs apportés à Azure App Service sur Azure Stack Update 6, ainsi que les problèmes connus. Les problèmes connus ont été répartis selon qu’ils concernent le déploiement, le processus de mise à jour ou la build (après l’installation).

> [!IMPORTANT]
> Appliquez la mise à jour 1904 à votre système intégré Azure Stack ou déployez le dernier Kit de développement Azure Stack avant de déployer Azure App Service 1.6.


## <a name="build-reference"></a>Référence de build

Le numéro de build d’App Service sur Azure Stack Update 6 est **82.0.1.50**

### <a name="prerequisites"></a>Prérequis

Avant de passer au déploiement, consultez la [documentation Avant de commencer](azure-stack-app-service-before-you-get-started.md).

Avant de commencer la mise à niveau d’Azure App Service sur Azure Stack 1.6 :

- vérifiez que tous les rôles sont prêts dans l’Administration Azure App Service dans le portail d’administration Azure Stack ;

- sauvegardez App Service et les bases de données master :
  - AppService_Hosting
  - AppService_Metering
  - Master

- Sauvegardez le partage de fichier de contenu d’application locataire ;

- Syndiquez **l’extension de script personnalisé** version **1.9.1** à partir de la Place de marché Azure

### <a name="new-features-and-fixes"></a>Nouvelles fonctionnalités et correctifs

Azure App Service sur Azure Stack Update 6 contient les améliorations et correctifs suivants :

- Mises à jour des **portails Locataire, Administration et Functions d’App Service, ainsi que des outils Kudu**. Cohérentes avec celles de la version du SDK du portail Azure Stack.

- Mises à jour du **runtime d’Azure Functions** vers la **version 1.0.12299**.

- Mises à jour du service principal afin d’améliorer la fiabilité et l’envoi de messages d’erreur, ce qui facilite le diagnostic des problèmes courants.

- **Mises à jour des outils et frameworks d’applications suivants** :
  - ASP.NET Core 2.2.4
  - NodeJS 10.15.2
  - Zulu OpenJDK 8.36.0.1
  - Tomcat 7.0.81
  - Tomcat 8.5.37
  - Tomcat 9.0.14
  - PHP 5.6.39
  - PHP 7.0.33
  - PHP 7.1.25
  - PHP 7.2.13
  - Mise à jour de Kudu vers la version 81.10329.3844

- **Mises à jour du système d’exploitation sous-jacent pour tous les rôles** :
  - [Mise à jour cumulative d’avril 2019 pour Windows Server 2016 pour les systèmes x64 (KB4493473)](https://support.microsoft.com/help/4493473/windows-10-update-kb4493473)

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
 * Protocole : TCP
 * Action : AUTORISER
 * Priorité : 700
 * Nom : Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Problèmes connus des administrateurs cloud utilisant Azure App Service sur Azure Stack

Reportez-vous à la documentation des [notes de publication d’Azure Stack 1904](azure-stack-release-notes-1904.md).

### <a name="known-issues-for-tenants-deploying-applications-on-azure-app-service-on-azure-stack"></a>Problèmes connus pour les locataires déployant des applications sur Azure App Service dans Azure Stack

- La fonctionnalité Centre de déploiement est grisée

Les locataires ne peuvent pas encore utiliser le Centre de déploiement, qui est une fonctionnalité ayant été publiée dans le cloud public fin 2018.  Les locataires peuvent toujours utiliser les méthodes de déploiement standard (FTP, Web Deploy, Git, etc.) par le biais du portail, de la CLI et de PowerShell.

- L’expérience utilisateur (classique) des options de déploiement et les options du portail Informations d’identification du déploiement ne sont pas disponibles

Pour accéder aux expériences utilisateur des options de déploiement et des informations d’identification du déploiement dans le déploiement Azure Stack, les locataires doivent accéder au portail à l’aide d’une URL au format https://portal.&lt ;*region*&gt;.&lt;*FQDN*&gt; /?websitesExtension_oldvsts=true (à savoir [https://portal.local.azurestack.external/?websitesExtension_oldvsts=true](https://portal.local.azurestack.external/?websitesExtension_oldvsts=true) pour le Kit ASDK), puis accéder à leurs applications normalement.

## <a name="next-steps"></a>Étapes suivantes

- Pour une présentation d’Azure App Service, consultez [Vue d’ensemble d’App Service sur Azure Stack](azure-stack-app-service-overview.md).
- Pour plus d’informations sur la préparation au déploiement d’App Service on Azure Stack, consultez [Avant de commencer avec App Service sur Azure Stack](azure-stack-app-service-before-you-get-started.md).
