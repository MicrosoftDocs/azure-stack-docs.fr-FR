---
title: Notes de publication de la mise à jour 8 d’App Service sur Azure Stack Hub
description: Notes de publication de la mise à jour 8 d’App Service sur Azure Stack Hub, y compris les nouvelles fonctionnalités, les correctifs et les problèmes connus.
author: apwestgarth
manager: stefsch
ms.topic: article
ms.date: 11/17/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 4c89f139c2fc0f80a80fc70ab6d5842a7ffffd4f
ms.sourcegitcommit: 2562b86f47db20e2652d4636227afb9cfd0e03ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94785800"
---
# <a name="app-service-on-azure-stack-hub-update-8-release-notes"></a>Notes de publication de la mise à jour 8 d’App Service sur Azure Stack Hub

Ces notes de publication décrivent les nouvelles fonctionnalités, les correctifs et les problèmes connus dans la mise à jour 8 d’Azure App Service sur Azure Stack Hub. Les problèmes connus sont divisés en deux sections : les problèmes liés au processus de mise à niveau et les problèmes propres à la build (après installation).

> [!IMPORTANT]
> Appliquez la mise à jour 1910 à votre système intégré Azure Stack ou déployez le dernier Kit de développement Azure Stack (ASDK) avant de déployer Azure App Service 1.8.

## <a name="build-reference"></a>Référence de build

Le numéro de build de la mise à jour 8 d’App Service sur Azure Stack Hub est **86.0.2.13**.

## <a name="prerequisites"></a>Prérequis

Consultez les [Prérequis pour le déploiement d’App Service sur Azure Stack Hub](azure-stack-app-service-before-you-get-started.md).

Avant d'entamer la mise à niveau d'Azure App Service sur Azure Stack Hub vers la version 1.8 :

- Vérifiez que tous les rôles sont prêts dans la section Administration d’Azure App Service du portail d’administration d’Azure Stack Hub.

- Sauvegardez les secrets App Service à l'aide de l'Administration Azure App Service sur le portail d’administration Azure Stack Hub.

- Sauvegardez les bases de données App Service et MASTER :
  - AppService_Hosting
  - AppService_Metering
  - master

- Sauvegardez le partage de fichiers de contenu d’application du locataire.

  > [!Important]
  > Les opérateurs cloud sont responsables de la maintenance et du fonctionnement du serveur de fichiers et de SQL Server.  Le fournisseur de ressources ne gère pas ces ressources.  L'opérateur cloud est responsable de la sauvegarde des bases de données App Service et du partage des fichiers de contenu des locataires.

- Syndiquez l’**extension de script personnalisé** version **1.9.3** à partir de la Place de marché Azure Stack Hub.

## <a name="new-features-and-fixes"></a>Nouvelles fonctionnalités et correctifs

La mise à jour 8 d’Azure App Service sur Azure Stack Hub contient les améliorations et correctifs suivants :

- Mises à jour des **portails de locataire, d’administration et de fonctions d’App Service ainsi que des outils Kudu**. Cohérentes avec celles de la version du SDK du portail Azure Stack.

- Mise à jour du **runtime de fonctions Azure** vers la **version 1.0.12615**.

- Mises à jour du service principal afin d’améliorer la fiabilité et l’envoi de messages d’erreur, ce qui facilite le diagnostic des problèmes courants.

- **Mises à jour des outils et des frameworks d’applications suivants** :

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

Tous les nouveaux déploiements d’Azure App Service sur Azure Stack Hub utiliseront des disques managés pour l’ensemble des machines virtuelles et des groupes de machines virtuelles identiques. Tous les déploiements existants continueront à utiliser des disques non managés.

- **Protocole TLS 1.2 appliqué par des équilibreurs de charge frontaux**

**TLS 1.2** est désormais appliqué pour toutes les applications.

## <a name="known-issues-upgrade"></a>Problèmes connus (mise à niveau)

- La mise à niveau échoue si le cluster SQL Server Always On a basculé vers le nœud secondaire.

Pendant la mise à niveau, un appel est effectué pour vérifier l’existence de la base de données à l’aide de la chaîne de connexion principale, et cet appel échoue car la session de connexion se trouvait sur le nœud principal précédent.

Effectuez l’une des actions suivantes, puis sélectionnez Réessayer dans le programme d’installation.

- Copiez la connexion `appservice_hostingAdmin` à partir du nœud SQL à présent secondaire ;

    **OR**

- Basculez le cluster SQL vers le nœud actif précédent.

## <a name="post-deployment-steps"></a>Étapes de post-déploiement

> [!IMPORTANT]
> Si vous avez approvisionné le fournisseur de ressources App Service avec une instance SQL Always On, vous DEVEZ [ajouter les bases de données appservice_hosting et appservice_metering à un groupe de disponibilité](/sql/database-engine/availability-groups/windows/availability-group-add-a-database), puis les synchroniser pour éviter toute perte de service en cas de basculement d’une base de données.

## <a name="known-issues-post-installation"></a>Problèmes connus (après l’installation)

- Les rôles Worker ne peuvent pas atteindre le serveur de fichiers si App Service est déployé dans un réseau virtuel existant et si le serveur de fichiers est uniquement disponible sur le réseau privé, comme indiqué dans la documentation de déploiement d’Azure App Service sur Azure Stack Hub.

  Si vous avez choisi de procéder au déploiement dans un réseau virtuel existant et une adresse IP interne pour vous connecter à votre serveur de fichiers, vous devez ajouter une règle de sécurité sortante, qui autorise le trafic SMB entre le sous-réseau Worker et le serveur de fichiers. Accédez au WorkersNsg dans le portail administrateur, puis ajoutez une règle de sécurité sortante comportant les propriétés suivantes :

  - Source : Quelconque
  - Plage de ports source : : *
  - Destination : Adresses IP
  - Plage d’adresses IP de destination : plage d’adresses IP de votre serveur de fichiers
  - Plage de ports de destination : 445
  - Protocole : TCP
  - Action : Allow
  - Priorité : 700
  - Nom : Outbound_Allow_SMB445

- Les nouveaux déploiements d’Azure App Service sur Azure Stack Hub 1.8 exigent que les bases de données soient converties en bases de données autonomes.

    En raison d’une régression dans cette version, les deux bases de données App Service (appservice_hosting et appservice_metering) doivent être converties en bases de données autonomes lorsqu’elles sont **récemment** déployées.  Ceci **N’A PAS** d’impact sur les déploiements **mis à niveau**.

    > [!IMPORTANT]
    > Cette procédure prend environ 5 à 10 minutes. Elle suppose de forcer l’arrêt des sessions de connexion à la base de données. Planifiez un temps d'arrêt pour la migration et la validation d'Azure App Service sur Azure Stack Hub après la migration.

    1. Ajoutez les [bases de données App Service (appservice_hosting et appservice_metering) à un groupe de disponibilité](/sql/database-engine/availability-groups/windows/availability-group-add-a-database).

    1. Activez la base de données autonome.

        ```sql

        sp_configure 'contained database authentication', 1;
        GO
        RECONFIGURE;
            GO
        ```

    1. Convertissez la base de données en une base de données partiellement autonome. Cette étape entraîne des temps d’arrêt, car toutes les sessions actives doivent être arrêtées.

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
        ```

    1. Migrez les connexions vers les utilisateurs de base de données autonome.

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

    **Procéder à la validation**

    1. Vérifiez si la fonction d’autonomie est activée dans SQL Server.

        ```sql
        sp_configure  @configname='contained database authentication'
        ```

    1. Vérifiez le comportement d’autonomie existant.

        ```sql
        SELECT containment FROM sys.databases WHERE NAME LIKE (SELECT DB_NAME())
        ```

- Impossible d’effectuer un scale out des workers

  Les nouveaux workers ne peuvent pas acquérir la chaîne de connexion de base de données exigée.  Pour remédier à cette situation, connectez-vous à l’une de vos instances de contrôleur (par exemple, CN0-VM), puis exécutez le script PowerShell suivant :

    ```powershell

    [System.Reflection.Assembly]::LoadWithPartialName("Microsoft.Web.Hosting")
    $siteManager = New-Object Microsoft.Web.Hosting.SiteManager

    $builder = New-Object System.Data.SqlClient.SqlConnectionStringBuilder -ArgumentList (Get-AppServiceConnectionString -Type Hosting)
    $conn = New-Object System.Data.SqlClient.SqlConnection -ArgumentList $builder.ToString()

    $siteManager.RoleServers | Where-Object {$_.IsWorker} | ForEach-Object {
        $worker = $_
        $dbUserName = "WebWorker_" + $worker.Name

        if (!$siteManager.ConnectionContexts[$dbUserName]) {
            $dbUserPassword = [Microsoft.Web.Hosting.Common.Security.PasswordHelper]::GenerateDatabasePassword()

            $conn.Open()
            $command = $conn.CreateCommand()
            $command.CommandText = "CREATE USER [$dbUserName] WITH PASSWORD = '$dbUserPassword'"
            $command.ExecuteNonQuery()
            $conn.Close()

            $conn.Open()
            $command = $conn.CreateCommand()
            $command.CommandText = "ALTER ROLE [WebWorkerRole] ADD MEMBER [$dbUserName]"
            $command.ExecuteNonQuery()
            $conn.Close()

            $builder.Password = $dbUserPassword
            $builder["User ID"] = $dbUserName

            $siteManager.ConnectionContexts.Add($dbUserName, $builder.ToString())
        }
    }

    $siteManager.CommitChanges()

    ```

## <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack-hub"></a>Problèmes connus pour les administrateurs cloud utilisant Azure App Service sur Azure Stack Hub

Reportez-vous à la documentation fournie dans les [Notes de publication d’Azure Stack Hub 1907](./release-notes.md?view=azs-1907&preserve-view=true).

- Les locataires ne peuvent pas créer de plan App Service à l’aide du nouvel affichage de plan App Service dans le portail locataire

Lors de la création d’une application, les locataires peuvent créer des plans App Service lors du workflow de création d’application, lors de la modification du plan App Service pour une application en cours, ou via l'élément de Place de marché de plan App Service

- Les domaines personnalisés ne sont pas pris en charge dans les environnements déconnectés

App Service effectue une vérification de la propriété du domaine sur les points de terminaison DNS publics, car les domaines personnalisés ne sont pas pris en charge dans les scénarios déconnectés.

- Dans certains cas, les Workers ne satisfont pas aux contrôles d'intégrité (espace disque insuffisant).

Dans certains cas, lorsqu'un nombre élevé de sites est alloué à un Worker ou qu'un site traite beaucoup de requêtes, le Worker génère un grand nombre de fichiers journaux d'exécution sous C:\DWAS\LogFiles.  Cela est dû à un bogue au niveau de la logique de nettoyage de ces fichiers journaux.  

Pour résoudre ce problème, accédez à distance au Worker et effacez le contenu du dossier.

Ce problème a été résolu dans [App Service sur Azure Stack Hub 2020 Q3](app-service-release-notes-2020-Q3.md). Nous encourageons donc les clients procéder au plus vite à une mise à niveau vers la version 2020 Q3.

> [!IMPORTANT]
> Pour pouvoir effectuer la mise à jour vers Azure App Service sur Azure Stack Hub 2020 Q3, vous **devez** procéder à une mise à niveau vers Azure Stack Hub 2008.

## <a name="next-steps"></a>Étapes suivantes

- Pour une présentation d’Azure App Service, consultez [Vue d’ensemble d’Azure App Service et d’Azure Functions sur Azure Stack Hub](azure-stack-app-service-overview.md).
- Pour plus d'informations sur la préparation au déploiement d'App Service sur Azure Stack Hub, consultez [Conditions préalables au déploiement d'App Service sur Azure Stack Hub](azure-stack-app-service-before-you-get-started.md).
