---
title: Notes de publication d’App Service sur Azure Stack Update 4 | Microsoft Docs
description: Découvrez le contenu de la mise à jour 4 d’App Service sur Azure Stack, les problèmes connus et l’emplacement à partir duquel la télécharger.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: b65e7c3b1cf381db3e36567c9f9ccf71a2150cd7
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66269137"
---
# <a name="app-service-on-azure-stack-update-4-release-notes"></a>Notes de publication d’App Service sur Azure Stack Update 4

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Ces notes de publication décrivent les améliorations et les correctifs apportés à Azure App Service sur Azure Stack Update 4, ainsi que les problèmes connus. Les problèmes connus ont été répartis selon qu’ils concernent le déploiement, le processus de mise à jour ou la build (après l’installation).

> [!IMPORTANT]
> Appliquez la mise à jour 1809 à votre système intégré Azure Stack ou déployez le dernier Kit de développement Azure Stack avant de déployer Azure App Service 1.4.
>
>

## <a name="build-reference"></a>Référence de build

Le numéro de build d’App Service sur Azure Stack Update 4 est **78.0.13698.5**

### <a name="prerequisites"></a>Prérequis

Avant de passer au déploiement, consultez la [documentation Avant de commencer](azure-stack-app-service-before-you-get-started.md).

Avant de commencer la mise à niveau d’Azure App Service sur Azure Stack 1.4 :

- vérifiez que tous les rôles sont prêts dans l’Administration Azure App Service dans le portail d’administration Azure Stack ;

- sauvegardez App Service et les bases de données master :
  - AppService_Hosting
  - AppService_Metering
  - Master

- Sauvegardez le partage de fichier de contenu d’application locataire ;

- Syndiquez la version 1.9 de l’extension de script personnalisé à partir d’Azure Marketplace

### <a name="new-features-and-fixes"></a>Nouvelles fonctionnalités et correctifs

Azure App Service sur Azure Stack Update 4 inclut les améliorations et correctifs suivants :

- Résolution de la vulnérabilité de script de site à site [CVE 2018-8600](https://aka.ms/CVE20188600)

- Prise en charge de la version 2018-02-01 de l’API App Service

- Mises à jour des **portails Locataire, Administration et Functions d’App Service, ainsi que des outils Kudu**. Cohérentes avec celles de la version du SDK du portail Azure Stack.

- Mises à jour du **runtime d’Azure Functions** à la **version 1.0.11959**.

- Mises à jour du service principal afin d’améliorer la fiabilité et l’envoi de messages d’erreur, ce qui facilite le diagnostic des problèmes courants.

- **Mises à jour des outils et frameworks d’applications suivants** :
  - Ajout de NodeJS 10.6.0
  - Ajout de NPM 6.1.0
  - Ajout de Zulu OpenJDK 8.31.0.2
  - Ajout de Tomcat 8.5.34 et 9.0.12
  - Ajout des versions suivantes de PHP :
    - 5.6.37
    - 7.0.31
    - 7.1.20
    - 7.2.8
  - Mise à jour des versions de Python :
    - 2.7.15
    - 3.6.6
  - Mise à jour de Git pour Windows vers la v 2.17.1.2
  - Mise à jour de Kudu vers la version 78.11022.3613
  
- **Mises à jour du système d’exploitation sous-jacent pour tous les rôles** :
  - [Mise à jour cumulative 2018-10 pour Windows Server 2016 pour les systèmes x64 (KB4462928)](https://support.microsoft.com/help/4462928/windows-10-update-kb4462928)

- Résolution du problème de validation de modèle lors du déploiement de Wordpress ; d’un réseau de neurones profond (DNN) et d’éléments de la galerie du système de gestion de contenu Orchard

- Résolution d’un problème de configuration lorsqu’Azure Stack effectue la rotation du certificat de client Azure Resource Manager

- Restauration des fonctionnalités dans les paramètres de partage des ressources Cross-Origin (CORS) dans le portail du locataire App Service

- Affichage d’un message d’erreur dans l’interface utilisateur du portail d’administration d’App Service lorsque le plan de contrôle du fournisseur de ressources ne parvient pas à se connecter à l’instance de SQL Server configurée

- Vérification de la spécification du point de terminaison dans la chaîne de connexion du stockage personnalisé en cas de spécification dans la nouvelle application de fonction

### <a name="post-deployment-steps"></a>Étapes de post-déploiement

> [!IMPORTANT]  
> Si vous avez indiqué le fournisseur de ressources App Service avec une instance SQL Always On, vous DEVEZ [ajouter les bases de données appservice_hosting et appservice_metering à un groupe de disponibilité](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) et synchroniser les bases de données pour éviter toute perte de service en cas de basculement d’une base de données.

### <a name="post-update-steps-optional"></a>Étapes post-mise à jour (facultatif)

Pour les clients qui cherchent à migrer vers une base de données autonome pour des déploiements existants d’Azure App Service sur Azure Stack, exécutez les étapes suivantes après la mise à jour d’Azure App Service sur Azure Stack 1.4 :

> [!IMPORTANT]
> La procédure de migration prend environ 5 à 10 minutes.  Elle suppose de forcer l’arrêt des sessions de connexion à la base de données.  Planifiez un temps d’arrêt pour la migration et la validation d’Azure App Service sur Azure Stack après la migration.  Si vous avez terminé ces étapes après la mise à jour d’Azure App Service sur Azure Stack 1.3, ces étapes ne sont pas requises.
>
>

1. Ajouter les [bases de données AppService (appservice_hosting et appservice_metering) à un groupe de disponibilité](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database)

1. Activer la base de données autonome
    ```sql

        sp_configure 'contained database authentication', 1;
        GO
        RECONFIGURE;
            GO
    ```

1. Lors de la conversion d’une base de données en base de données partiellement autonome, des temps d’arrêt peuvent survenir, car toutes les sessions actives doivent être arrêtés

    ```sql
        /******** [appservice_metering] Migration Start********/
            USE [master];

            -- kill all active sessions
            DECLARE @kill varchar(8000) = '';  
            SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'  
            FROM sys.dm_exec_sessions
            WHERE database_id  = db_id('appservice_metering')

            EXEC(@kill);

            USE [master]  
            GO  
            ALTER DATABASE [appservice_metering] SET CONTAINMENT = PARTIAL  
            GO  

        /********[appservice_metering] Migration End********/

        /********[appservice_hosting] Migration Start********/

            -- kill all active sessions
            USE [master];

            DECLARE @kill varchar(8000) = '';  
            SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'  
            FROM sys.dm_exec_sessions
            WHERE database_id  = db_id('appservice_hosting')

            EXEC(@kill);

            -- Convert database to contained
            USE [master]  
            GO  
            ALTER DATABASE [appservice_hosting] SET CONTAINMENT = PARTIAL  
            GO  

            /********[appservice_hosting] Migration End********/
    '''

1. Migrate Logins to Contained Database Users

    ```sql
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

Valider

1. Vérifier si la fonction d’autonomie est activée sur SQL Server

    ```sql
        sp_configure  @configname='contained database authentication'
    ```

1. Vérifier le comportement autonome existant
    ```sql
        SELECT containment FROM sys.databases WHERE NAME LIKE (SELECT DB_NAME())
    ```

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

Reportez-vous à la documentation des [notes de publication d’Azure Stack 1809](azure-stack-update-1903.md)

## <a name="next-steps"></a>Étapes suivantes

- Pour une présentation d’Azure App Service, consultez [Vue d’ensemble d’App Service sur Azure Stack](azure-stack-app-service-overview.md).
- Pour plus d’informations sur la préparation au déploiement d’App Service on Azure Stack, consultez [Avant de commencer avec App Service sur Azure Stack](azure-stack-app-service-before-you-get-started.md).
