---
title: Notes de publication App Service sur Azure Stack Hub Update 8
description: Découvrez le contenu de la mise à jour huit d’App Service sur Azure Stack Hub, les problèmes connus et l’emplacement à partir duquel la télécharger.
author: apwestgarth
manager: stefsch
ms.topic: article
ms.date: 03/05/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 82f43028253638f92866bb679a5ccb5478a5a56e
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "78935078"
---
# <a name="app-service-on-azure-stack-hub-update-8-release-notes"></a>Notes de publication App Service sur Azure Stack Hub Update 8

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

### <a name="known-issues-upgrade"></a>Problèmes connus (mise à niveau)

- La mise à niveau échoue si le cluster SQL Server Always On a basculé vers le nœud secondaire

Pendant la mise à niveau, un appel est effectué pour vérifier l’existence de la base de données à l’aide de la chaîne de connexion principale, et cet appel échoue car la session de connexion se trouvait sur le nœud principal précédent.

Effectuez l’une des actions suivantes, puis cliquez sur Réessayer dans le programme d’installation.

- Copiez la connexion appservice_hostingAdmin à partir du nœud SQL à présent secondaire ;

**OR**

- Basculez le cluster SQL vers le nœud actif précédent.

### <a name="post-deployment-steps"></a>Étapes de post-déploiement

> [!IMPORTANT]
> Si vous avez indiqué le fournisseur de ressources App Service avec une instance SQL Always On, vous DEVEZ [ajouter les bases de données appservice_hosting et appservice_metering à un groupe de disponibilité](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) et synchroniser les bases de données pour éviter toute perte de service en cas de basculement d’une base de données.

### <a name="known-issues-post-installation"></a>Problèmes connus (après l’installation)

- Les rôltes de travail ne peuvent pas atteindre le serveur de fichiers si App Service est déployé dans un réseau virtuel existant et si le serveur de fichiers est uniquement disponible sur le réseau privé, comme indiqué dans la documentation de déploiement d’Azure App Service sur Azure Stack

  Si vous avez choisi de procéder au déploiement dans un réseau virtuel existant et une adresse IP interne pour vous connecter à votre serveur de fichiers, vous devez ajouter une règle de sécurité sortante, qui autorise le trafic SMB entre le sous-réseau Worker et le serveur de fichiers. Accédez au WorkersNsg dans le portail d’administration, puis ajoutez une règle de sécurité sortante comportant les propriétés suivantes :
  - Source : Toutes
  - Plage de ports source : : *
  - Destination : adresses IP
  - Plage d’adresses IP de destination : plage d’adresses IP de votre serveur de fichiers
  - Plage de ports de destination : 445
  - Protocole : TCP
  - Action : Autoriser
  - Priorité : 700
  - Nom : Outbound_Allow_SMB445

- Les nouveaux déploiements d’Azure App Service sur Azure Stack Hub 1.8 exigent que les bases de données soient converties en bases de données autonomes.

En raison d’une régression dans cette version, les deux bases de données App Service (appservice_hosting et appservice_metering) doivent être converties en bases de données autonomes lorsqu’elles sont **récemment** déployées.  Ceci **N’A PAS** d’impact sur les déploiements **mis à niveau**.

> [!IMPORTANT]
> Cette procédure prend environ 5 à 10 minutes. Elle suppose de forcer l’arrêt des sessions de connexion à la base de données. Planifier un temps d'arrêt pour la migration et la validation d'Azure App Service sur Azure Stack Hub après la migration
>
>

1. Ajoutez les [bases de données App Service (appservice_hosting et appservice_metering) à un groupe de disponibilité](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database).

1. Activez la base de données autonome.

    ```sql

        sp_configure 'contained database authentication', 1;
        GO
        RECONFIGURE;
            GO
    ```

1. Convertissez une base de données en une base de données partiellement autonome. Cette étape entraîne des temps d’arrêt, car toutes les sessions actives doivent être arrêtées.

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

  Les nouveaux workers ne peuvent pas acquérir la chaîne de connexion de base de données exigée.  Pour remédier à cette situation, connectez-vous à l’une de vos instances de contrôleur, par exemple CN0-VM, puis exécutez le script PowerShell suivant :

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

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Problèmes connus des administrateurs cloud utilisant Azure App Service sur Azure Stack

Reportez-vous à la documentation des [notes de publication d’Azure Stack 1907](azure-stack-release-notes-1907.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour une présentation d’Azure App Service, consultez [Vue d’ensemble d’App Service sur Azure Stack](azure-stack-app-service-overview.md).
- Pour plus d’informations sur la préparation au déploiement d’App Service on Azure Stack, consultez [Avant de commencer avec App Service sur Azure Stack](azure-stack-app-service-before-you-get-started.md).
