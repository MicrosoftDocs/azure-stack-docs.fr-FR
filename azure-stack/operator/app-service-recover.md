---
title: Récupération pour App Service sur Azure Stack Hub
description: Découvrez la récupération d’urgence pour App Service sur Azure Stack Hub.
author: bryanla
ms.topic: article
ms.date: 03/21/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: ba57de42f882ce62d433768f4fd2a265b862459c
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86489757"
---
# <a name="app-service-recovery-on-azure-stack-hub"></a>Récupération pour App Service sur Azure Stack Hub

Cette rubrique fournit des instructions sur les actions à entreprendre pour la reprise d’activité après sinistre d’App Service.

Les actions suivantes sont nécessaires pour récupérer App Service sur Azure Stack Hub à partir d’une sauvegarde :
1. Restaurer les bases de données App Service.
2. Restaurer le contenu du partage du serveur de fichiers.
3. Restaurer les services et les rôles App Service.

Si le stockage Azure Stack Hub a été utilisé pour le stockage des applications de fonction, vous devez également prendre des mesures pour restaurer ces applications.

## <a name="restore-the-app-service-databases"></a>Restaurer les bases de données App Service
Les bases de données SQL Server pour App Service doivent être restaurées sur une instance SQL Server prête pour la production. 

Après la [préparation de l’instance SQL Server](azure-stack-app-service-before-you-get-started.md#prepare-the-sql-server-instance) devant héberger les bases de données App Service, effectuez ces étapes pour restaurer les bases de données à partir d’une sauvegarde :

1. Connectez-vous en tant qu’administrateur à l’instance SQL Server qui hébergera les bases de données App Service restaurées.
2. Pour restaurer les bases de données App Service à partir d’une invite de commandes exécutée avec des autorisations d’administrateur, utilisez les commandes suivantes :
    ```dos
    sqlcmd -U <SQL admin login> -P <SQL admin password> -Q "RESTORE DATABASE appservice_hosting FROM DISK='<full path to backup>' WITH REPLACE"
    sqlcmd -U <SQL admin login> -P <SQL admin password> -Q "RESTORE DATABASE appservice_metering FROM DISK='<full path to backup>' WITH REPLACE"
    ```
3. Vérifiez que les deux bases de données App Service ont été correctement restaurées, puis quittez SQL Server Management Studio.

> [!NOTE]
> Pour récupérer après un échec d’instance de cluster de basculement, consultez [Récupérer après un échec d’instance de cluster de basculement](/sql/sql-server/failover-clusters/windows/recover-from-failover-cluster-instance-failure?view=sql-server-2017). 

## <a name="restore-the-app-service-file-share-content"></a>Restaurer le contenu du partage de fichiers App Service
Après la [préparation du serveur de fichiers](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server) devant héberger le partage de fichiers App Service, vous devez restaurer le contenu du partage de fichiers du locataire à partir d’une sauvegarde. Vous pouvez utiliser n’importe quelle méthode disponible pour copier les fichiers à l’emplacement du nouveau partage de fichiers App Service. Cet exemple exécuté sur le serveur de fichiers utilise PowerShell et l’outil robocopy pour établir une connexion à un partage distant et copier les fichiers sur le partage :

```powershell
$source = "<remote backup storage share location>"
$destination = "<local file share location>"
net use $source /user:<account to use to connect to the remote share in the format of domain\username> *
robocopy /E $source $destination
net use $source /delete
```

En plus de copier le contenu du partage de fichiers, vous devez aussi réinitialiser les autorisations sur le partage de fichiers. Pour réinitialiser les autorisations, ouvrez une invite de commandes avec des autorisations d’administrateur sur le serveur de fichiers et exécutez le fichier **ReACL.cmd**. Le fichier **ReACL.cmd** se trouve avec les fichiers d’installation d’App Service dans le répertoire **BCDR**.

## <a name="restore-app-service-roles-and-services"></a>Restaurer les services et les rôles App Service
Une fois que le contenu du partage de fichiers et les bases de données App Service sont restaurés, vous devez restaurer les rôles et les services App Service à l’aide de PowerShell. Ces étapes restaurent les configurations du service et les secrets App Service.  

1. Connectez-vous à la machine virtuelle **CN0-VM** du contrôleur App Service en tant que **roleadmin**, en utilisant le même mot de passe que celui fourni lors de l’installation d’App Service. 
    > [!TIP]
    > Vous devez modifier le groupe de sécurité réseau de la machine virtuelle afin d’autoriser les connexions RDP. 
2. Copiez le fichier **SystemSecrets.JSON** sur la machine virtuelle locale du contrôleur. Vous devez entrer le chemin de ce fichier comme paramètre `$pathToExportedSecretFile` à l’étape suivante.
3. Exécutez les commandes suivantes dans une fenêtre de console PowerShell avec élévation de privilèges pour restaurer les services et les rôles App Service :

    ```powershell
    # Stop App Service services on the primary controller VM
    net stop WebFarmService
    net stop ResourceMetering
    net stop HostingVssService # This service was deprecated in the App Service 1.5 release and is not required after the App Service 1.4 release.

    # Restore App Service secrets. Provide the path to the App Service secrets file copied from backup. For example, C:\temp\SystemSecrets.json.
    # Press ENTER when prompted to reconfigure App Service from backup 

    # If necessary, use -OverrideDatabaseServer <restored server> with Restore-AppServiceStamp when the restored database server has a different address than backed-up deployment.
    # If necessary, use -OverrideContentShare <restored file share path> with Restore-AppServiceStamp when the restored file share has a different path from backed-up deployment.
    Restore-AppServiceStamp -FilePath $pathToExportedSecretFile 

    # Restore App Service roles
    Restore-AppServiceRoles

    # Restart App Service services
    net start WebFarmService
    net start ResourceMetering
    net start HostingVssService  # This service was deprecated in the App Service 1.5 release and is not required after the App Service 1.4 release.

    # After App Service has successfully restarted, and at least one management server is in ready state, synchronize App Service objects to complete the restore
    # Enter Y when prompted to get all sites and again for all ServerFarm entities.
    Get-AppServiceSite | Sync-AppServiceObject
    Get-AppServiceServerFarm | Sync-AppServiceObject
    ```

> [!TIP]
> Il est fortement recommandé de fermer cette session PowerShell à la fin de la commande.

## <a name="restore-function-apps"></a>Restaurer des applications de fonction 
App Service pour Azure Stack Hub ne prend pas en charge la restauration des applications utilisateur de locataire ou des données autres que le contenu du partage de fichiers. Toutes les autres données doivent être sauvegardées et restaurées en dehors des processus de sauvegarde et de restauration d’App Service. Si le stockage Azure Stack Hub a été utilisé pour le stockage des applications de fonction, vous pouvez restaurer des données perdues en procédant comme suit :

1. Créez un compte de stockage qui sera utilisé par l’application de fonction. Ce stockage peut être un stockage Azure Stack Hub, un stockage Azure ou tout autre stockage compatible.
2. Récupérez la chaîne de connexion du stockage.
3. Ouvrez le portail de la fonction et accédez à l’application de fonction.
4. Accédez à l’onglet **Fonctionnalités de la plateforme** et cliquez sur **Paramètres d’application**.
5. Changez **AzureWebJobsDashboard** et **AzureWebJobsStorage** par la nouvelle chaîne de connexion et cliquez sur **Enregistrer**.
6. Accédez à **Vue d’ensemble**.
7. Redémarrez l’application. Plusieurs tentatives peuvent être nécessaires pour résoudre toutes les erreurs.

## <a name="next-steps"></a>Étapes suivantes
[Vue d’ensemble d’App Service sur Azure Stack Hub](azure-stack-app-service-overview.md)
