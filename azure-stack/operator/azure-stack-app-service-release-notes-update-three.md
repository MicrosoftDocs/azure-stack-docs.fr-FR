---
title: Notes de publication d’App Service sur Azure Stack Update 3 | Microsoft Docs
description: Découvrez le contenu de la mise à jour 3 d’App Service sur Azure Stack, les problèmes connus et l’emplacement à partir duquel la télécharger.
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 03/25/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: sethm
ms.lastreviewed: 08/20/2018
ms.openlocfilehash: 5ea711d3d4ffff72279e745290c1c8d9d854298e
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "64306773"
---
# <a name="app-service-on-azure-stack-update-3-release-notes"></a>Notes de publication d’App Service sur Azure Stack Update 3

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Ces notes de publication décrivent les améliorations et les correctifs apportés à Azure App Service sur Azure Stack Update 3, ainsi que les problèmes connus. Les problèmes connus ont été répartis selon qu’ils concernent le déploiement, le processus de mise à jour ou la build (après l’installation).

> [!IMPORTANT]
> Appliquez la mise à jour 1807 à votre système intégré Azure Stack ou déployez le dernier Kit de développement Azure Stack avant de déployer Azure App Service 1.3.
>
>

## <a name="build-reference"></a>Référence de build

Le numéro de build d’App Service sur Azure Stack Update 3 est **74.0.13698.31**

### <a name="prerequisites"></a>Prérequis

Avant de passer au déploiement, consultez la [documentation Avant de commencer](azure-stack-app-service-before-you-get-started.md).

Avant de commencer la mise à niveau d’Azure App Service sur Azure Stack 1.3, vérifiez que tous les rôles sont prêts dans l’administration Azure App Service via le portail d’administration Azure Stack

![Statut du rôle App Service](media/azure-stack-app-service-release-notes-update-three/image01.png)

### <a name="new-features-and-fixes"></a>Nouvelles fonctionnalités et correctifs

Azure App Service sur Azure Stack Update 3 inclut les améliorations et correctifs suivants :

- Prise en charge de l’utilisation de SQL Server Always On pour les bases de données de fournisseurs de ressources Azure App Service.

- Ajout du nouveau paramètre d’environnement au script d’assistance Create-AADIdentityApp afin de faciliter le ciblage des différentes régions AAD.

- Mises à jour des **portails Locataire, Administration et Functions d’App Service, ainsi que des outils Kudu**. Cohérentes avec celles de la version du SDK du portail Azure Stack.

- Mises à jour du **runtime d’Azure Functions** à la **version 1.0.11820**.

- Mises à jour du service principal afin d’améliorer la fiabilité et l’envoi de messages d’erreur, ce qui facilite le diagnostic des problèmes courants.

- **Mises à jour des outils et frameworks d’applications suivants** :
  - Ajout d’ASP.NET Core 2.1.2
  - Ajout de NodeJS 10.0.0
  - Ajout de Zulu OpenJDK 8.30.0.1
  - Ajout de Tomcat 8.5.31 et 9.0.8
  - Ajout des versions suivantes de PHP :
    - 5.6.36
    - 7.0.30
    - 7.1.17
    - 7.2.5
  - Ajout de Wincache 2.0.0.8
  - Mise à jour de Git pour Windows vers la v 2.17.1.2
  - Mise à jour de Kudu vers la version 74.10611.3437
  
- **Mises à jour du système d’exploitation sous-jacent pour tous les rôles** :
  - [Mise à jour de la pile de maintenance pour Windows Server 2016 pour les systèmes x64 (KB4132216)](https://support.microsoft.com/help/4132216/servicing-stack-update-for-windows-10-1607-may-17-2018)
  - [Mise à jour cumulative 2018-07 pour Windows Server 2016 pour les systèmes x64 (KB4338822)](https://support.microsoft.com/help/4338822/windows-10-update-kb4338822)

### <a name="post-update-steps-optional"></a>Étapes post-mise à jour (facultatif)

Pour les clients qui cherchent à migrer vers une base de données autonome pour des déploiements existants d’Azure App Service sur Azure Stack, exécutez les étapes suivantes après la mise à jour d’Azure App Service sur Azure Stack 1.3 :

> [!IMPORTANT]
> Cette procédure prend environ 5 à 10 minutes.  Elle suppose de forcer l’arrêt des sessions de connexion à la base de données.  Planifier un temps d’arrêt pour la migration et la validation d’Azure App Service sur Azure Stack après la migration
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

1. Conversion d’une base de données en base de données partiellement autonome.  Cette étape entraîne des temps d’arrêt, car toutes les sessions actives doivent être arrêtées

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

- Les Workers ne peuvent pas atteindre le serveur de fichiers si App Service est déployé dans un réseau virtuel existant et si le serveur de fichiers est uniquement disponible sur le réseau privé.  Cela est aussi évoqué dans la documentation de déploiement d’Azure App Service sur Azure Stack.

Si vous avez choisi de procéder au déploiement dans un réseau virtuel existant et une adresse IP interne pour vous connecter à votre serveur de fichiers, vous devez ajouter une règle de sécurité sortante, qui autorise le trafic SMB entre le sous-réseau Worker et le serveur de fichiers. Pour ce faire, accédez au WorkersNsg dans le portail d’administration, puis ajoutez une règle de sécurité sortante comportant les propriétés suivantes :
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

Reportez-vous à la documentation des notes de publication d’Azure Stack 1807.

## <a name="next-steps"></a>Étapes suivantes

- Pour une présentation d’Azure App Service, consultez [Vue d’ensemble d’App Service sur Azure Stack](azure-stack-app-service-overview.md).
- Pour plus d’informations sur la préparation au déploiement d’App Service on Azure Stack, consultez [Avant de commencer avec App Service sur Azure Stack](azure-stack-app-service-before-you-get-started.md).
