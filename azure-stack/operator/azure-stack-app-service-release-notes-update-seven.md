---
title: Notes de publication App Service sur Azure Stack Hub Update 7
description: Découvrez le contenu de la mise à jour 7 d’App Service sur Azure Stack Hub, les problèmes connus et l’emplacement à partir duquel la télécharger.
author: apwestgarth
manager: stefsch
ms.topic: article
ms.date: 10/11/2019
ms.author: anwestg
ms.reviewer: ''
ms.openlocfilehash: d4ddc7d0b6190c7f855b222079f8d13cb5d6e14e
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76874363"
---
# <a name="app-service-on-azure-stack-hub-update-7-release-notes"></a>Notes de publication App Service sur Azure Stack Hub Update 7

Ces notes de publication décrivent les améliorations et les correctifs apportés à Azure App Service sur Azure Stack Hub Update 7, ainsi que les problèmes connus. Les problèmes connus ont été répartis selon qu’ils concernent le déploiement, le processus de mise à jour ou la build (après l’installation).

> [!IMPORTANT]
> Appliquez la mise à jour 1907 à votre système intégré Azure Stack Hub ou déployez le dernier kit de développement Azure Stack avant de déployer Azure App Service 1.7.


## <a name="build-reference"></a>Référence de build

Le numéro de build d’App Service sur Azure Stack Hub Update 7 est **84.0.2.10**.

### <a name="prerequisites"></a>Conditions préalables requises

Avant de passer au déploiement, consultez la [documentation Avant de commencer](azure-stack-app-service-before-you-get-started.md).

Avant de commencer la mise à niveau d’Azure App Service sur Azure Stack Hub vers la version 1.7 :

- Vérifiez que tous les rôles sont prêts dans l’Administration Azure App Service sur le portail d’administration Azure Stack Hub.

- sauvegardez App Service et les bases de données master :
  - AppService_Hosting
  - AppService_Metering
  - Master

- Sauvegardez le partage de fichier de contenu d’application locataire ;

- Syndiquez l’**extension de script personnalisé** version **1.9.3** à partir de la Place de marché Azure

### <a name="new-features-and-fixes"></a>Nouvelles fonctionnalités et correctifs

Azure App Service sur Azure Stack Hub Update 7 contient les améliorations et correctifs suivants :

- Résolution pour [CVE-2019-1372](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-1372) : Vulnérabilité liée à l’exécution de code à distance

- Mises à jour des **portails Locataire, Administration et Functions d’App Service, ainsi que des outils Kudu**. Cohérence avec la version du SDK du portail Azure Stack Hub.

- Mises à jour du **runtime d’Azure Functions** vers la **version 1.0.12582**.

- Mises à jour du service principal afin d’améliorer la fiabilité et l’envoi de messages d’erreur, ce qui facilite le diagnostic des problèmes courants.

- **Mises à jour des outils et frameworks d’applications suivants** :
  - ASP.NET Core 2.2.46
  - Zul OpenJDK 8.38.0.13
  - Tomcat 7.0.94
  - Tomcat 8.5.42
  - Tomcat 9.0.21
  - PHP 5.6.40
  - PHP 7.3.6
  - Mise à jour de Kudu vers la version 82.10503.3890

- **Mises à jour du système d’exploitation sous-jacent pour tous les rôles** :
  - [Mise à jour cumulative d’août 2019 pour Windows Server 2016 sur systèmes x64 (KB4512495)](https://support.microsoft.com/help/4512495)

- **Restrictions d’accès désormais activées dans le portail utilisateur** :
  - À compter de cette version, les utilisateurs peuvent configurer des restrictions d’accès pour leurs applications web/API/Functions, conformément à la documentation publiée, [Restrictions d’accès dans Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions). **REMARQUE** : Azure App Service sur Azure Stack Hub ne prend pas en charge les points de terminaison de service.

- **Restauration de la fonctionnalité Options de déploiement (classiques)**  :
  - Les utilisateurs peuvent de nouveau se servir de la fonctionnalité Options de déploiement (classiques) pour configurer le déploiement de leurs applications à partir des dépôts GitHub, Bitbucket, Dropbox, OneDrive, locaux et externes, et pour définir les informations d’identification de déploiement de leurs applications.

- Configuration correcte de la **supervision d’Azure Functions**.

- **Comportement de Windows Update** : nous avons pris en compte certains commentaires de clients pour changer la configuration de Windows Update sur les rôles App Service à partir de la version Update 7 :
  - Il y a trois modes :
    - **Désactivé** : le service Windows Update est désactivé. Windows est mis à jour avec la base de connaissances fournie avec les versions d’Azure App Service sur Azure Stack Hub.
    - **Automatique** : le service Windows Update est activé et Windows Update détermine le mode et le moment de la mise à jour.
    - **Managé** : le service Windows Update est désactivé. Azure App Service lance un cycle Windows Update durant le processus OnStart du rôle individuel.

  **Nouveaux** déploiements : le service Windows Update est désactivé par défaut.

  Déploiements **existants** : si vous avez modifié le paramètre sur le contrôleur, la valeur passe de **false** à **désactivé** et une valeur **true** précédente passe à **automatique**

### <a name="post-deployment-steps"></a>Étapes de post-déploiement

> [!IMPORTANT]
> Si vous avez indiqué le fournisseur de ressources App Service avec une instance SQL Always On, vous DEVEZ [ajouter les bases de données appservice_hosting et appservice_metering à un groupe de disponibilité](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) et synchroniser les bases de données pour éviter toute perte de service en cas de basculement d’une base de données.

### <a name="known-issues-post-installation"></a>Problèmes connus (après l’installation)

- Les rôles Worker ne peuvent pas atteindre le serveur de fichiers si App Service est déployé dans un réseau virtuel existant et si le serveur de fichiers est uniquement disponible sur le réseau privé, comme indiqué dans la documentation de déploiement d’Azure App Service sur Azure Stack Hub.

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

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack-hub"></a>Problèmes connus des administrateurs cloud utilisant Azure App Service sur Azure Stack Hub

Reportez-vous à la documentation des [notes de publication 1907 d’Azure Stack Hub](azure-stack-release-notes-1907.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour une présentation d’Azure App Service, consultez [Vue d’ensemble d’Azure App Service sur Azure Stack Hub](azure-stack-app-service-overview.md).
- Pour plus d’informations sur la préparation au déploiement d’App Service sur Azure Stack Hub, consultez [Avant de commencer avec App Service sur Azure Stack Hub](azure-stack-app-service-before-you-get-started.md).
