---
title: Notes de publication App Service sur Azure Stack Hub 2020 T3
description: Découvrez ce qui contient la version 2020 Q3 d'App Service sur Azure Stack Hub, les problèmes connus liés à celle-ci et où la télécharger.
author: apwestgarth
manager: stefsch
ms.topic: article
ms.date: 11/17/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 10/23/2020
ms.openlocfilehash: f9d575a91c55379326beaac2d76b3400172c8dd0
ms.sourcegitcommit: 2562b86f47db20e2652d4636227afb9cfd0e03ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94785870"
---
# <a name="app-service-on-azure-stack-hub-2020-q3-release-notes"></a>Notes de publication App Service sur Azure Stack Hub 2020 T3

Ces notes de publication décrivent les améliorations et les correctifs apportés à Azure App Service sur Azure Stack Hub 2020 T3, ainsi que les problèmes connus. Les problèmes connus ont été répartis selon qu’ils concernent le déploiement, le processus de mise à jour ou la build (après l’installation).

[!INCLUDE [Azure Stack Hub update reminder](../includes/app-service-hub-update-banner.md)]

## <a name="build-reference"></a>Référence de build

Le numéro de build d’App Service sur Azure Stack Hub 2020 T3 est **89.0.2.15**

## <a name="prerequisites"></a>Prérequis

Avant de passer au déploiement, consultez la [documentation Avant de commencer](azure-stack-app-service-before-you-get-started.md).

Avant de commencer à mettre à niveau Azure App Service sur Azure Stack vers la version 2020 T3 :

- Vérifiez que tous les rôles sont prêts dans l’Administration Azure App Service sur le portail d’administration Azure Stack Hub.

- Sauvegardez les secrets App Service à l'aide de l'Administration Azure App Service sur le portail d’administration Azure Stack Hub.

- sauvegardez App Service et les bases de données master :
  - AppService_Hosting
  - AppService_Metering
  - Master

- Sauvegardez le partage de fichier de contenu d’application locataire ;

  > [!Important]
  > Les opérateurs cloud sont responsables de la maintenance et du fonctionnement du serveur de fichiers et de SQL Server.  Le fournisseur de ressources ne gère pas ces ressources.  L'opérateur cloud est responsable de la sauvegarde des bases de données App Service et du partage des fichiers de contenu des locataires.

- Syndiquez l’**extension de script personnalisé** version **1.9.3** à partir de la Place de marché Azure

## <a name="updates"></a>Mises à jour

La mise à jour T3 d’Azure App Service sur Azure Stack contient les améliorations et correctifs suivants :

- Mises à jour des **portails Locataire, Administration et Functions d’App Service, ainsi que des outils Kudu**. Cohérentes avec celles de la version du SDK du portail Azure Stack.

- Ajout de l’expérience de création plein écran pour les applications web et de fonction

- Nouvelle expérience du portail Azure Functions qui est à présent cohérente avec Web Apps

- Mise à jour du **runtime Azure Functions** vers la **version 1.0.13154**.

- Mises à jour du service principal afin d’améliorer la fiabilité et l’envoi de messages d’erreur, ce qui facilite le diagnostic des problèmes courants.

- **Mises à jour des outils et frameworks d’applications suivants** :
  - ASP.NET Core 2.1.22
  - ASP.NET Core 2.2.14
  - ASP.NET Core 3.1.8
  - Module ASP.NET Core v2 13.1.19331.0
  - Azul OpenJDK
    - 8.42.0.23
    - 8.44.0.11
    - 11.35.15
    - 11.37.17
  - Curl 7.55.1
  - Git pour Windows 2.28.0.1
  - MSDeploy 3.5.90702.36
  - NodeJS
    - 14.10.1
  - NPM
    - 6.14.8
  - PHP 7.4.5
  - Tomcat
    - 8.5.47
    - 8.5.51
    - 9.0.273
    - 9.0.31
  - Kudu mis à jour vers la version 90.21005.4823

- **Mises à jour du système d’exploitation sous-jacent pour tous les rôles** :
  - [Mise à jour cumulative 2020-10 de Windows Server 2016 pour systèmes x64 (KB4580346)](https://support.microsoft.com/help/4580346)
  - [Mise à jour 2020-09 de la pile de maintenance pour Windows Server 2016 pour systèmes x64 (KB4576750)](https://support.microsoft.com/help/4576750)
  - Defender Definition 1.325.755.0

- **Les mises à jour cumulatives pour Windows Server sont désormais appliquées aux rôles de contrôleur dans le cadre du déploiement et de la mise à niveau**

## <a name="issues-fixed-in-this-release"></a>Problèmes résolus dans cette version

- Les locataires peuvent à présent créer un plan App Service à l’aide du nouvel affichage Plan App Service dans le portail des locataires

- Les locataires peuvent gérer des certificats pour leurs applications dans le portail des locataires

- La surveillance des fonctions peut à présent récupérer des données à partir de points de terminaison de stockage appliquant TLS 1.2

- Déplacement de l’étape d’attente Serveurs d’administration en dehors de l’étape de déploiement cloud pendant l’installation pour améliorer la fiabilité du déploiement et de la mise à niveau

- Problème lié au fait que les Workers ne parviennent pas à effectuer l'exercice de contrôle d'intégrité car la taille du dossier du fichier journal du runtime du Worker ne respecte pas la limite de quota lorsqu'une erreur survient au niveau de la logique de nettoyage.  Le problème lié à la logique de nettoyage a été résolu dans cette mise à jour.

## <a name="pre-update-steps"></a>Étapes préalables à la mise à jour

Passez en revue les [problèmes connus de la mise à jour](#known-issues-update) et prenez les mesures requises.

## <a name="post-deployment-steps"></a>Étapes de post-déploiement

> [!IMPORTANT]
> Si vous avez indiqué le fournisseur de ressources App Service avec une instance SQL Always On, vous DEVEZ [ajouter les bases de données appservice_hosting et appservice_metering à un groupe de disponibilité](/sql/database-engine/availability-groups/windows/availability-group-add-a-database) et synchroniser les bases de données pour éviter toute perte de service en cas de basculement d’une base de données.

## <a name="known-issues-update"></a>Problèmes connus (mise à jour)

- Dans les situations où un client a converti les bases de données appservice_hosting et appservice_metering en bases de données autonomes, la mise à niveau peut échouer si les connexions n’ont pas été correctement migrées vers les utilisateurs autonomes.

Les clients qui ont converti les bases de données appservice_hosting et appservice_metering en bases de données autonomes après le déploiement et n'ont pas correctement migré les connexions des bases de données vers les utilisateurs autonomes peuvent se heurter à des échecs de mise à niveau.  

Les clients doivent exécuter le script suivant sur l’instance SQL Server hébergeant appservice_hosting et appservice_metering avant de mettre à niveau l’installation Azure App Service sur Azure Stack Hub vers 2020 T3.  **Ce script est non destructeur et n’entraîne pas de temps d’arrêt**.

Ce script doit être exécuté dans les conditions suivantes

- Par un utilisateur disposant des privilèges d’administrateur système, par exemple le compte d'administrateur système (SA) SQL ;
- Si vous utilisez SQL Always On, assurez-vous que le script est exécuté à partir de l’instance SQL contenant toutes les connexions App Service sous la forme :

    - appservice_hosting_FileServer
    - appservice_hosting_HostingAdmin
    - appservice_hosting_LoadBalancer
    - appservice_hosting_Operations
    - appservice_hosting_Publisher
    - appservice_hosting_SecurePublisher
    - appservice_hosting_WebWorkerManager
    - appservice_metering_Common
    - appservice_metering_Operations
    - Toutes les connexions WebWorker, qui se présentent sous la forme WebWorker_<instance ip address>

```sql
        USE appservice_hosting
        IF EXISTS(SELECT * FROM sys.databases WHERE Name=DB_NAME() AND containment = 1)
        BEGIN
        DECLARE @username sysname ;  
        DECLARE user_cursor CURSOR  
        FOR
            SELECT dp.name
            FROM sys.database_principals AS dp  
            JOIN sys.server_principals AS sp
                ON dp.sid = sp.sid  
                WHERE dp.authentication_type = 1 AND dp.name NOT IN ('dbo','sys','guest','INFORMATION_SCHEMA');
            OPEN user_cursor  
            FETCH NEXT FROM user_cursor INTO @username  
                WHILE @@FETCH_STATUS = 0  
                BEGIN  
                    EXECUTE sp_migrate_user_to_contained
                    @username = @username,  
                    @rename = N'copy_login_name',  
                    @disablelogin = N'do_not_disable_login';  
                FETCH NEXT FROM user_cursor INTO @username  
            END  
            CLOSE user_cursor ;  
            DEALLOCATE user_cursor ;
            END
        GO

        USE appservice_metering
        IF EXISTS(SELECT * FROM sys.databases WHERE Name=DB_NAME() AND containment = 1)
        BEGIN
        DECLARE @username sysname ;  
        DECLARE user_cursor CURSOR  
        FOR
            SELECT dp.name
            FROM sys.database_principals AS dp  
            JOIN sys.server_principals AS sp
                ON dp.sid = sp.sid  
                WHERE dp.authentication_type = 1 AND dp.name NOT IN ('dbo','sys','guest','INFORMATION_SCHEMA');
            OPEN user_cursor  
            FETCH NEXT FROM user_cursor INTO @username  
                WHILE @@FETCH_STATUS = 0  
                BEGIN  
                    EXECUTE sp_migrate_user_to_contained
                    @username = @username,  
                    @rename = N'copy_login_name',  
                    @disablelogin = N'do_not_disable_login';  
                FETCH NEXT FROM user_cursor INTO @username  
            END  
            CLOSE user_cursor ;  
            DEALLOCATE user_cursor ;
            END
        GO
```

## <a name="known-issues-post-installation"></a>Problèmes connus (après l’installation)

- Les rôltes de travail ne peuvent pas atteindre le serveur de fichiers si App Service est déployé dans un réseau virtuel existant et si le serveur de fichiers est uniquement disponible sur le réseau privé, comme indiqué dans la documentation de déploiement d’Azure App Service sur Azure Stack

  Si vous avez choisi de procéder au déploiement dans un réseau virtuel existant et une adresse IP interne pour vous connecter à votre serveur de fichiers, vous devez ajouter une règle de sécurité sortante, qui autorise le trafic SMB entre le sous-réseau Worker et le serveur de fichiers. Accédez au WorkersNsg dans le portail d’administration, puis ajoutez une règle de sécurité sortante comportant les propriétés suivantes :
  - Source : Quelconque
  - Plage de ports source : : *
  - Destination : Adresses IP
  - Plage d’adresses IP de destination : plage d’adresses IP de votre serveur de fichiers
  - Plage de ports de destination : 445
  - Protocole : TCP
  - Action : Allow
  - Priorité : 700
  - Nom : Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Problèmes connus des administrateurs cloud utilisant Azure App Service sur Azure Stack

- Les domaines personnalisés ne sont pas pris en charge dans les environnements déconnectés

App Service effectue une vérification de la propriété du domaine sur les points de terminaison DNS publics, car les domaines personnalisés ne sont pas pris en charge dans les scénarios déconnectés.

## <a name="next-steps"></a>Étapes suivantes

- Pour une présentation d’Azure App Service, consultez [Vue d’ensemble d’App Service sur Azure Stack](azure-stack-app-service-overview.md).
- Pour plus d’informations sur la préparation au déploiement d’App Service on Azure Stack, consultez [Avant de commencer avec App Service sur Azure Stack](azure-stack-app-service-before-you-get-started.md).
