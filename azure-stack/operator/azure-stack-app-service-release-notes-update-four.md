---
title: Notes de publication d'App Service sur Azure Stack Hub Update 4
description: Découvrez les améliorations, les correctifs et les problèmes connus concernant la version Update 4 d'App Service pour Azure Stack Hub.
author: bryanla
manager: stefsch
ms.topic: article
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 08/20/2019
ms.openlocfilehash: 93ca14c17613229aea354e96a9e48be41c2ea5e2
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "77703483"
---
# <a name="app-service-on-azure-stack-hub-update-4-release-notes"></a>Notes de publication d'App Service sur Azure Stack Hub Update 4

Ces notes de publication décrivent les améliorations, les correctifs et les problèmes connus dans la mise à jour 4 d'Azure App Service sur Azure Stack Hub. Les problèmes connus ont été répartis en trois sections : ceux qui concernent directement le déploiement, ceux qui concernent le processus de mise à jour et ceux qui concernent la build (après l’installation).

> [!IMPORTANT]
> Appliquez la mise à jour 1809 à votre système intégré Azure Stack Hub, ou déployez le dernier Kit de développement Azure Stack Hub (ASDK) avant de déployer Azure App Service 1.4.

## <a name="build-reference"></a>Référence de build

Le numéro de build de la mise à jour 4 d'App Service sur Azure Stack Hub est **78.0.13698.5**

### <a name="prerequisites"></a>Prérequis

Avant d'entamer le déploiement, consultez les [Conditions préalables au déploiement d'App Service sur Azure Stack Hub](azure-stack-app-service-before-you-get-started.md).

Avant d'entamer la mise à niveau d'Azure App Service sur Azure Stack Hub vers la version 1.4 :

- Vérifiez que tous les rôles sont prêts dans la section Administration d'Azure App Service du portail d'administration d'Azure Stack Hub.

- Sauvegardez les bases de données App Service et MASTER :
  - AppService_Hosting
  - AppService_Metering
  - Master

- Sauvegardez le partage de fichiers de contenu d’application du locataire.

- Syndiquez l’**extension de script personnalisé** version **1.9** à partir de la Place de marché Azure.

### <a name="new-features-and-fixes"></a>Nouvelles fonctionnalités et correctifs

Azure App Service sur Azure Stack Hub Update 4 contient les améliorations et correctifs suivants :

- Résolution de la vulnérabilité de script de site à site [CVE 2018-8600](https://aka.ms/CVE20188600)

- Prise en charge de la version 2018-02-01 de l’API App Service

- Mises à jour des **portails Locataire, Administration et Functions d’App Service, ainsi que des outils Kudu**. Compatible avec la version SDK du portail Azure Stack Hub.

- Mises à jour du **runtime d’Azure Functions** à la **version 1.0.11959**.

- Mises à jour du service principal afin d’améliorer la fiabilité et l’envoi de messages d’erreur, ce qui facilite le diagnostic des problèmes courants.

- **Mises à jour des outils et des frameworks d’applications suivants** :
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

- Résolution du problème de validation de modèle lors du déploiement de Wordpress, d’un réseau de neurones profond (DNN) et d’éléments de la galerie du système de gestion de contenu Orchard.

- Résolution d'un problème de configuration lorsqu'Azure Stack Hub fait pivoter le certificat client Azure Resource Manager.

- Restauration de la fonctionnalité des paramètres de partage des ressources cross-origin sur le portail utilisateurs d'App Service.

- Le message d’erreur s’affiche désormais dans le portail administrateur d’App Service lorsque le plan de contrôle du fournisseur de ressources ne parvient pas à se connecter à l’instance de SQL Server configurée.

- Vérification de la spécification du point de terminaison dans la chaîne de connexion du stockage personnalisé en cas de spécification dans la nouvelle application de fonction.

### <a name="post-deployment-steps"></a>Étapes de post-déploiement

> [!IMPORTANT]  
> Si vous avez approvisionné le fournisseur de ressources App Service avec une instance SQL Always On, vous *devez* [ajouter les bases de données appservice_hosting et appservice_metering à un groupe de disponibilité](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database), puis les synchroniser pour éviter toute perte de service en cas de basculement d'une base de données.

### <a name="post-update-steps-optional"></a>Étapes post-mise à jour (facultatif)

Pour les clients désireux d'effectuer une migration vers une base de données autonome pour des déploiements existants d'Azure App Service sur Azure Stack Hub, suivez les étapes ci-dessous après la mise à jour d'Azure App Service sur Azure Stack Hub 1.4 :

> [!IMPORTANT]
> La procédure de migration prend environ 5 à 10 minutes. Elle suppose de forcer l’arrêt des sessions de connexion à la base de données. Planifiez un temps d'arrêt pour la migration et la validation d'Azure App Service sur Azure Stack Hub après la migration. Si vous avez déjà suivi ces étapes après la mise à jour d'Azure App Service sur Azure Stack Hub 1.3, vous pouvez ici les ignorer.

1. Ajoutez les [bases de données App Service (appservice_hosting et appservice_metering) à un groupe de disponibilité](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database).

1. Activez la base de données autonome.
    ```sql

        sp_configure 'contained database authentication', 1;
        GO
        RECONFIGURE;
            GO
    ```

1. La conversion d’une base de données en base de données partiellement autonome peut nécessiter des temps d’arrêt, car toutes les sessions actives doivent être arrêtées.

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

### <a name="known-issues-post-installation"></a>Problèmes connus (après l’installation)

- Les Workers ne peuvent pas atteindre le serveur de fichiers si App Service est déployé dans un réseau virtuel existant et si le serveur de fichiers est uniquement disponible sur le réseau privé. Ce problème est évoqué dans la documentation de déploiement d'Azure App Service sur Azure Stack Hub.

Si vous avez choisi de procéder au déploiement dans un réseau virtuel existant et avec une adresse IP interne pour vous connecter à votre serveur de fichiers, vous devez ajouter une règle de sécurité sortante, qui autorise le trafic SMB entre le sous-réseau worker et le serveur de fichiers. Accédez au WorkersNsg dans le portail administrateur, puis ajoutez une règle de sécurité sortante comportant les propriétés suivantes :

 * Source : Quelconque
 * Plage de ports source : : *
 * Destination : Adresses IP
 * Plage d’adresses IP de destination : plage d’adresses IP de votre serveur de fichiers
 * Plage de ports de destination : 445
 * Protocole : TCP
 * Action : Allow
 * Priorité : 700
 * Nom : Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack-hub"></a>Problèmes connus pour les administrateurs cloud utilisant Azure App Service sur Azure Stack Hub

Reportez-vous à la documentation fournie dans les [Notes de publication d'Azure Stack Hub 1809](azure-stack-update-1903.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour une présentation d'Azure App Service, consultez [Vue d'ensemble d'Azure App Service sur Azure Stack Hub](azure-stack-app-service-overview.md).
- Pour plus d'informations sur la préparation au déploiement d'App Service sur Azure Stack Hub, consultez [Conditions préalables au déploiement d'App Service sur Azure Stack Hub](azure-stack-app-service-before-you-get-started.md).
