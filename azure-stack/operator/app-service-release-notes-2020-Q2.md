---
title: Notes de publication App Service sur Azure Stack Hub 2002 T2
description: Découvrez le contenu de la mise à jour 2002 T2 d’App Service sur Azure Stack Hub, les problèmes connus et où la télécharger.
author: apwestgarth
manager: stefsch
ms.topic: article
ms.date: 05/05/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 04/30/2020
ms.openlocfilehash: c5e6ac0a2a500cf43cf94cbc40b2a95c58784d28
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94544715"
---
# <a name="app-service-on-azure-stack-hub-2020-q2-release-notes"></a>Notes de publication App Service sur Azure Stack Hub 2020 T2

Ces notes de publication décrivent les améliorations et les correctifs apportés à Azure App Service sur Azure Stack Hub 2020 T2, ainsi que les problèmes connus. Les problèmes connus ont été répartis selon qu’ils concernent le déploiement, le processus de mise à jour ou la build (après l’installation).

[!INCLUDE [Azure Stack Hub update reminder](../includes/app-service-hub-update-banner.md)]

## <a name="build-reference"></a>Référence de build

Le numéro de build d’App Service sur Azure Stack Hub 2020 T2 est **87.0.2.10**

## <a name="prerequisites"></a>Prérequis

Avant de passer au déploiement, consultez la [documentation Avant de commencer](azure-stack-app-service-before-you-get-started.md).

Avant de commencer à mettre à niveau Azure App Service sur Azure Stack vers la version 2020 T2 :

- Vérifiez que tous les rôles sont prêts dans l’Administration Azure App Service sur le portail d’administration Azure Stack Hub.

- Sauvegardez les secrets App Service à l'aide de l'Administration Azure App Service sur le portail d’administration Azure Stack Hub.

- sauvegardez App Service et les bases de données master :
  - AppService_Hosting
  - AppService_Metering
  - Master

- Sauvegardez le partage de fichier de contenu d’application locataire ;

  > [!Important]
  > Les opérateurs cloud sont responsables de la maintenance et du fonctionnement du serveur de fichiers et de SQL Server.  Le fournisseur de ressources ne gère pas ces ressources.  L'opérateur cloud est responsable de la sauvegarde des bases de données App Service et du partage des fichiers de contenu des locataires.

- Syndiquez l’ **extension de script personnalisé** version **1.9.3** à partir de la Place de marché Azure



## <a name="updates"></a>Mises à jour

La mise à jour T2 d’Azure App Service sur Azure Stack contient les améliorations et correctifs suivants :

- Mises à jour des **portails Locataire, Administration et Functions d’App Service, ainsi que des outils Kudu**. Cohérentes avec celles de la version du SDK du portail Azure Stack.

- Mise à jour du **runtime d'Azure Functions** vers la **version 1.0.13021**.

- Mises à jour du service principal afin d’améliorer la fiabilité et l’envoi de messages d’erreur, ce qui facilite le diagnostic des problèmes courants.

- **Mises à jour des outils et frameworks d’applications suivants** :
  - ASP.NET Framework 4.7.2
  - ASP.NET Core 3.1.3
  - Module ASP.NET Core v2 13.1.19331.0
  - PHP 7.4.2
  - Kudu mis à jour vers la version 86.20224.4450
  - NodeJS 
    - 8.17.0
    - 10.19.0
    - 12.13.0
    - 12.15.0
  - NPM
    - 5.6.0
    - 6.1.0
    - 6.12.0
    - 6.13.4
  
- **Mises à jour du système d’exploitation sous-jacent pour tous les rôles** :
  - [Mise à jour cumulative 2020-04 de Windows Server 2016 pour systèmes x64 (KB4550929)](https://support.microsoft.com/help/4550929)
  - [Mise à jour 2020-04 de la pile de maintenance pour Windows Server 2016 pour systèmes x64 (KB4550994)](https://support.microsoft.com/help/4550994)

- **Les mises à jour cumulatives pour Windows Server sont désormais appliquées aux rôles de contrôleur dans le cadre du déploiement et de la mise à niveau**

- **Mise à jour des références SKU par défaut des machines virtuelles et des groupes identiques pour les nouveaux déploiements**  : Afin d'assurer la cohérence avec notre service cloud public, les nouveaux déploiements d'Azure App Service sur Azure Stack Hub utiliseront les références SKU suivantes pour les machines et groupes identiques sous-jacents destinés au fournisseur de ressources
  
  | Role | Nombre minimal de références (SKU) |
  | --- | --- |
  | Contrôleur | Standard_A4_v2 - (4 cœurs, 8 192 Mo) |
  | Gestion | Standard_D3_v2 - (4 cœurs, 14 336 Mo) |
  | Serveur de publication | Standard_A2_v2 - (2 cœurs, 4 096 Mo) |
  | FrontEnd | Standard_A4_v2 - (4 cœurs, 8 192 Mo) |
  | Worker partagé | Standard_A4_v2 - (4 cœurs, 8 192 Mo) |
  | Worker dédié de petite taille | Standard_A1_v2 - (1 cœur, 2048 Mo) |
  | Worker dédié de taille moyenne | Standard_A2_v2 - (2 cœurs, 4 096 Mo) |
  | Worker dédié de grande taille | Standard_A4_v2 - (4 cœurs, 8 192 Mo) |

Pour les déploiements d'ASDK, vous pouvez mettre à l’échelle les instances vers des références SKU inférieures afin de réduire le noyau et la validation de la mémoire, mais vous constaterez une dégradation des performances.

## <a name="issues-fixed-in-this-release"></a>Problèmes résolus dans cette version

- Désormais, la mise à niveau aboutit si le cluster SQL Server Always On a basculé vers le nœud secondaire
- Les nouveaux déploiements d’Azure App Service sur Azure Stack Hub n'exigent plus que les bases de données soient converties en bases de données autonomes
- L’ajout de Workers ou d’instances de rôle d’infrastructure supplémentaires s'effectue correctement sans intervention manuelle
- L’ajout de niveaux de Workers personnalisés s’effectue correctement sans intervention manuelle
- La suppression des niveaux de Workers personnalisés s'effectue sans erreurs du portail
- Les Workers ne sont plus marqués comme prêts si l’espace disque est insuffisant sur le disque local
- Délai d'expiration plus long pour récupérer le certificat Azure Resource Manager
- Le nombre de messages récupérés, à partir des journaux du serveur et affichés dans le portail d’administration, est limité pour rester inférieur à la taille maximale des requêtes Azure Resource Manager
- Problème de délai d'expiration provoquant des problèmes de démarrage du service d’utilisation
- Problème de déploiement de base de données résolu lors de la création de sites Orchard CMS
- Les contrôleurs sont désormais mis à jour avec les mises à jour cumulatives Windows dans le cadre du déploiement et de la mise à niveau
- App Service ne verrouille plus les opérations en cas d’échec de vérification des domaines personnalisés

## <a name="pre-update-steps"></a>Étapes préalables à la mise à jour

Passez en revue les [problèmes connus de la mise à jour](#known-issues-update) et prenez les mesures requises.

## <a name="post-deployment-steps"></a>Étapes de post-déploiement

> [!IMPORTANT]
> Si vous avez indiqué le fournisseur de ressources App Service avec une instance SQL Always On, vous DEVEZ [ajouter les bases de données appservice_hosting et appservice_metering à un groupe de disponibilité](/sql/database-engine/availability-groups/windows/availability-group-add-a-database) et synchroniser les bases de données pour éviter toute perte de service en cas de basculement d’une base de données.

## <a name="known-issues-update"></a>Problèmes connus (mise à jour)

- Dans les situations où un client a converti les bases de données appservice_hosting et appservice_metering en bases de données autonomes, la mise à niveau peut échouer si les connexions n’ont pas été correctement migrées vers les utilisateurs autonomes.

Les clients qui ont converti les bases de données appservice_hosting et appservice_metering en bases de données autonomes après le déploiement et n'ont pas correctement migré les connexions des bases de données vers les utilisateurs autonomes peuvent se heurter à des échecs de mise à niveau.  

Les clients doivent exécuter le script suivant sur l'instance SQL Server hébergeant appservice_hosting et appservice_metering avant de mettre à niveau l'installation Azure App Service sur Azure Stack Hub vers 2020 T2.  **Ce script est non destructeur et n’entraîne pas de temps d’arrêt**.

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

- Les locataires ne peuvent pas créer de plan App Service à l’aide du nouvel affichage de plan App Service dans le portail locataire

Lors de la création d’une application, les locataires peuvent créer des plans App Service lors du workflow de création d’application, lors de la modification du plan App Service pour une application en cours, ou via l'élément de Place de marché de plan App Service

- Les domaines personnalisés ne sont pas pris en charge dans les environnements déconnectés

App Service effectue une vérification de la propriété du domaine sur les points de terminaison DNS publics, car les domaines personnalisés ne sont pas pris en charge dans les scénarios déconnectés.

## <a name="next-steps"></a>Étapes suivantes

- Pour une présentation d’Azure App Service, consultez [Vue d’ensemble d’App Service sur Azure Stack](azure-stack-app-service-overview.md).
- Pour plus d’informations sur la préparation au déploiement d’App Service on Azure Stack, consultez [Avant de commencer avec App Service sur Azure Stack](azure-stack-app-service-before-you-get-started.md).
