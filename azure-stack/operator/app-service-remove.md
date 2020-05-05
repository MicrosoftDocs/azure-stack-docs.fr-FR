---
title: Supprimer Azure App Service d’Azure Stack Hub
description: Découvrez comment supprimer Azure App Service d’Azure Stack Hub.
author: apwestgarth
ms.topic: article
ms.date: 04/17/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 04/17/20207
ms.openlocfilehash: b371b319ae87bb112633b64d77d66f8916a13124
ms.sourcegitcommit: 355e21dd9b8c3f44e14abaae0b4f176443cf7495
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81636490"
---
# <a name="remove-azure-app-service-from-azure-stack-hub"></a>Supprimer Azure App Service d’Azure Stack Hub

Cet article explique comment supprimer d’Azure Stack Hub le fournisseur de ressources Azure App Service et les composants associés.

## <a name="remove-resource-provider"></a>Supprimer le fournisseur de ressources

> [!Important]
> Cette opération supprime toutes les ressources de locataire, supprime le service et les quotas de tous les plans et supprime le fournisseur de ressources Azure App Service dans son intégralité.  Si vous avez déployé le modèle Démarrage rapide de SQL Server et le serveur de fichiers à haut niveau de disponibilité App Service, ces ressources sont également supprimées car elles sont déployées sur le même groupe de ressources qu’Azure App Service sur Azure Stack Hub.

Pour supprimer Azure App Service d’Azure Stack Hub, effectuez l’opération suivante :

1. Supprimez le groupe de ressources qui contient les ressources Azure App Service sur Azure Stack Hub, par exemple AppService.local.

## <a name="remove-databases-and-file-share-content"></a>Supprimer les bases de données et le contenu du partage de fichiers

Vous ne devez suivre cette section que si votre serveur SQL et/ou votre serveur de fichiers est déployé hors empreinte ou dans un autre groupe de ressources, sinon passez à la section suivante.

### <a name="remove-databases-and-logins"></a>Supprimer les bases de données et les connexions

1. Si vous utilisez **SQL Server Always On**, supprimez les bases de données **AppService_Hosting** et **AppService_Metering** du groupe de disponibilité :

1. Exécutez le script SQL suivant pour supprimer les bases de données et les connexions.

   ```sql
   --******************************************************************
   /*
   Script to clean up App Service objects (databases and logins).
   */
   USE [master]
   GO

   DROP DATABASE [appservice_hosting]
   GO

   DROP DATABASE [appservice_metering]
   GO

   DECLARE @sql NVARCHAR(MAX) = N'';    
 
   SELECT @sql += '
   DROP LOGIN [' + name + '];' 
   from master.sys.sql_logins
   WHERE name LIKE  '%_hosting_%' OR 
   name LIKE  '%_metering_%' OR
   name LIKE  '%WebWorker_%';

   PRINT @sql;
   EXEC sp_executesql @sql;
   PRINT 'Completed';

   --******************************************************************
   ```

### <a name="remove-the-application-file-content-from-the-file-server"></a>Supprimer le contenu du fichier d’application du serveur de fichiers

1. Supprimez le partage de contenu de votre serveur de fichiers.

## <a name="next-steps"></a>Étapes suivantes

Pour effectuer une réinstallation, revenez à l’article [Prérequis pour le déploiement d’App Service sur Azure Stack Hub](azure-stack-app-service-before-you-get-started.md) article.
