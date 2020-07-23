---
title: Sauvegarder App Service sur Azure Stack Hub
description: Découvrez comment sauvegarder des services App Service sur Azure Stack Hub.
author: bryanla
ms.topic: article
ms.date: 04/23/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: 3b12c794550c3d39809369ae86117d9c684e1a53
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86489791"
---
# <a name="back-up-app-service-on-azure-stack-hub"></a>Sauvegarder App Service sur Azure Stack Hub

Ce document fournit des instructions sur la sauvegarde d’App Service sur Azure Stack Hub.

> [!IMPORTANT]
> App Service sur Azure Stack Hub n’est pas sauvegardé dans le cadre de la [sauvegarde de l’infrastructure Azure Stack Hub](azure-stack-backup-infrastructure-backup.md). En tant qu’opérateur Azure Stack Hub, vous devez prendre des mesures pour garantir la récupération d’App Service au besoin.

Quand vous planifiez la reprise d’activité après sinistre, vous devez prendre en compte ces quatre composants principaux d’Azure App Service sur Azure Stack Hub :
1. L’infrastructure du fournisseur de ressources, les rôles serveur, les niveaux de worker, etc. 
2. Les secrets App Service.
3. Les bases de données App Service hébergées et de mesure contrôlées par SQL Server.
4. Le contenu de la charge de travail utilisateur d’App Service stocké dans le partage de fichiers App Service.

## <a name="back-up-app-service-secrets"></a>Sauvegarder les secrets App Service
Quand vous restaurez App Service à partir d’une sauvegarde, vous devez fournir les clés App Service qui ont été utilisées au moment du déploiement initial. Ces clés doivent être enregistrées dès la fin du déploiement d’App Service et stockées à un emplacement sûr. La configuration de l’infrastructure du fournisseur de ressources est recréée à partir de la sauvegarde quand vous effectuez la récupération d’App Service à l’aide des cmdlets PowerShell prévues à cet effet.

À partir du portail d’administration, effectuez les étapes suivantes pour sauvegarder les secrets App Service : 

1. Connectez-vous au portail administrateur Azure Stack Hub en tant qu’administrateur du service.

2. Accédez à **App Service** -> **Secrets**. 

3. Sélectionnez **Télécharger les secrets**.

   ![Télécharger les secrets dans le portail d’administration Azure Stack Hub](./media/app-service-back-up/download-secrets.png)

4. Quand les secrets sont prêts à être téléchargés, cliquez sur **Enregistrer** et stockez le fichier des secrets App Service (**SystemSecrets.JSON**) à un emplacement sûr. 

   ![Enregistrer les secrets dans le portail d’administration Azure Stack Hub](./media/app-service-back-up/save-secrets.png)

> [!NOTE]
> Répétez ces étapes à chaque changement des secrets App Service.

## <a name="back-up-the-app-service-databases"></a>Sauvegarder les bases de données App Service
Pour restaurer App Service, vous avez besoin des sauvegardes des bases de données **Appservice_hosting** et **Appservice_metering**. Nous vous recommandons d’utiliser des plans de maintenance SQL Server ou le serveur de sauvegarde Azure pour vous assurer que ces bases de données sont régulièrement sauvegardées et stockées à un emplacement sûr. Toutefois, vous pouvez utiliser toute autre méthode qui garantit la création régulière et la disponibilité de sauvegardes SQL.

Si vous êtes connecté au serveur SQL Server, vous pouvez sauvegarder manuellement ces bases de données à l’aide des commandes PowerShell suivantes :

  ```powershell
  $s = "<SQL Server computer name>"
  $u = "<SQL Server login>" 
  $p = read-host "Provide the SQL admin password"
  sqlcmd -S $s -U $u -P $p -Q "BACKUP DATABASE appservice_hosting TO DISK = '<path>\hosting.bak'"
  sqlcmd -S $s -U $u -P $p -Q "BACKUP DATABASE appservice_metering TO DISK = '<path>\metering.bak'"
  ```

> [!NOTE]
> Si vous devez sauvegarder des bases de données SQL AlwaysOn, suivez [ces instructions](/sql/database-engine/availability-groups/windows/configure-backup-on-availability-replicas-sql-server?view=sql-server-2017). 

Une fois que vous avez sauvegardé toutes les bases de données, copiez les fichiers .bak et les secrets App Service à un emplacement sûr.

## <a name="back-up-the-app-service-file-share"></a>Sauvegarder le partage de fichiers App Service
App Service stocke les informations d’application du locataire dans le partage de fichiers. Le partage de fichiers doit être sauvegardé régulièrement, ainsi que les bases de données App Service pour limiter le risque de perte de données en cas de restauration.

Pour sauvegarder le contenu du partage de fichiers App Service, utilisez le serveur de sauvegarde Azure ou une autre méthode de votre choix permettant de copier régulièrement le contenu du partage de fichiers au même emplacement où vous avez enregistré précédemment toutes les informations de récupération.

Par exemple, effectuez ces étapes pour utiliser Robocopy à partir d’une session de console Windows PowerShell (et non PowerShell ISE) :

```powershell
$source = "<file share location>"
$destination = "<remote backup storage share location>"
net use $destination /user:<account to use to connect to the remote share in the format of domain\username> *
robocopy $source $destination
net use $destination /delete
```

## <a name="next-steps"></a>Étapes suivantes
[Restaurer App Service sur Azure Stack Hub](app-service-recover.md)
