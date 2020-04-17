---
title: Notes de publication App Service sur Azure Stack Hub Update 6
description: Découvrez le contenu de la mise à jour 6 d’App Service sur Azure Stack Hub, les problèmes connus et où la télécharger.
author: apwestgarth
manager: stefsch
ms.topic: article
ms.date: 06/24/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 08/20/2019
ms.openlocfilehash: d41455823c6905a947a703412664fc52ff45e1a8
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "77701120"
---
# <a name="app-service-on-azure-stack-hub-update-6-release-notes"></a>Notes de publication App Service sur Azure Stack Hub Update 6

Ces notes de publication décrivent les améliorations et les correctifs d'Azure App Service sur Azure Stack Hub Update 6, ainsi que les problèmes connus. Les problèmes connus ont été répartis selon qu’ils concernent le déploiement, le processus de mise à jour ou la build (après l’installation).

> [!IMPORTANT]
> Appliquez la mise à jour 1904 à votre système intégré Azure Stack Hub ou déployez le dernier kit de développement Azure Stack avant de déployer Azure App Service 1.6.


## <a name="build-reference"></a>Référence de build

Le numéro de build App Service sur Azure Stack Hub Update 6 est **82.0.1.50**.

### <a name="prerequisites"></a>Conditions préalables requises

Avant de passer au déploiement, consultez la [documentation Avant de commencer](azure-stack-app-service-before-you-get-started.md).

Avant d'entamer la mise à niveau d'Azure App Service sur Azure Stack Hub vers la version 1.6 :

- Vérifiez que tous les rôles sont prêts dans l’Administration Azure App Service sur le portail d’administration Azure Stack Hub.

- sauvegardez App Service et les bases de données master :
  - AppService_Hosting
  - AppService_Metering
  - Master

- Sauvegardez le partage de fichier de contenu d’application locataire ;

- Syndiquez **l’extension de script personnalisé** version **1.9.1** à partir de la Place de marché Azure

### <a name="new-features-and-fixes"></a>Nouvelles fonctionnalités et correctifs

Azure App Service sur Azure Stack Hub Update 6 contient les améliorations et correctifs suivants :

- Mises à jour des **portails Locataire, Administration et Functions d’App Service, ainsi que des outils Kudu**. Cohérence avec la version du SDK du portail Azure Stack Hub.

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

- Les rôles Worker ne peuvent pas atteindre le serveur de fichiers si App Service est déployé dans un réseau virtuel existant et si le serveur de fichiers est uniquement disponible sur le réseau privé, comme indiqué dans la documentation de déploiement d’Azure App Service sur Azure Stack Hub.

Si vous avez choisi de procéder au déploiement dans un réseau virtuel existant et une adresse IP interne pour vous connecter à votre serveur de fichiers, vous devez ajouter une règle de sécurité sortante, qui autorise le trafic SMB entre le sous-réseau Worker et le serveur de fichiers. Accédez au WorkersNsg dans le portail d’administration, puis ajoutez une règle de sécurité sortante comportant les propriétés suivantes :
 * Source : Toutes
 * Plage de ports source : : *
 * Destination : adresses IP
 * Plage d’adresses IP de destination : plage d’adresses IP de votre serveur de fichiers
 * Plage de ports de destination : 445
 * Protocole : TCP
 * Action : Autoriser
 * Priorité : 700
 * Nom : Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack-hub"></a>Problèmes connus des administrateurs cloud utilisant Azure App Service sur Azure Stack Hub

Reportez-vous à la documentation fournie dans les [Notes de publication Azure Stack Hub 1908](/azure-stack/operator/release-notes?view=azs-1908).

### <a name="known-issues-for-tenants-deploying-applications-on-azure-app-service-on-azure-stack-hub"></a>Problèmes connus pour les locataires déployant des applications sur Azure App Service dans Azure Stack Hub

- La fonctionnalité Centre de déploiement est grisée

Les locataires ne peuvent pas encore utiliser le Centre de déploiement, qui est une fonctionnalité ayant été publiée dans le cloud public fin 2018.  Les locataires peuvent toujours utiliser les méthodes de déploiement standard (FTP, Web Deploy, Git, etc.) par le biais du portail, de la CLI et de PowerShell.

- L’expérience utilisateur (classique) des options de déploiement et les options du portail Informations d’identification du déploiement ne sont pas disponibles

Pour accéder aux expériences utilisateur des options de déploiement et des informations d’identification du déploiement dans le déploiement Azure Stack Hub, les locataires doivent accéder au portail à l’aide d’une URL au format https://portal.&lt ;*region*&gt;.&lt;*FQDN*&gt; /?websitesExtension_oldvsts=true (à savoir [https://portal.local.azurestack.external/?websitesExtension_oldvsts=true](https://portal.local.azurestack.external/?websitesExtension_oldvsts=true) pour le Kit ASDK), puis accéder à leurs applications normalement.

- Azure Fonction Monitoring montre en permanence « Chargement en cours » dans le portail

Lorsque vous essayez d’analyser des fonctions individuelles, dans le portail utilisateur, vous ne verrez pas de journal d’invocation, ni le nombre de réussites ou le nombre d’erreurs.  Pour réactiver cette fonctionnalité, accédez à votre **Function App**, accédez à **Fonctionnalités de la plateforme** et accédez à **Paramètres de l’application**.  Ajouter un paramètre d’application -name **AzureWebJobsDashboard** et définissez la valeur sur la même que celle définie dans AzureWebJobsStorage.  Puis accédez à la vue d’analyse sur votre fonction et vous verrez les informations de surveillance.

## <a name="next-steps"></a>Étapes suivantes

- Pour une présentation d'Azure App Service, consultez [Vue d'ensemble d'Azure App Service sur Azure Stack Hub](azure-stack-app-service-overview.md).
- Pour plus d’informations sur la préparation au déploiement d’App Service sur Azure Stack Hub, consultez [Avant de commencer avec App Service sur Azure Stack Hub](azure-stack-app-service-before-you-get-started.md).
