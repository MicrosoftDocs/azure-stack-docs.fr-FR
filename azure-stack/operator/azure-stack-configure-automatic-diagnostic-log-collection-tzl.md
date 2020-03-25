---
title: Collecter de manière proactive les journaux de diagnostic quand une alerte est déclenchée sur Azure Stack Hub
description: Découvrez comment configurer la collecte proactive des journaux dans Azure Stack Hub Aide et support.
author: justinha
ms.topic: article
ms.date: 02/12/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 02/12/2020
ms.openlocfilehash: 0c6bfe4dba2b3795e1069419d90624c8e3b55cda
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2020
ms.locfileid: "79520542"
---
# <a name="send-azure-stack-hub-diagnostic-logs-proactively"></a>Envoyer les journaux de diagnostic Azure Stack Hub de façon proactive

Vous pouvez gagner du temps avec le support client en collectant de manière proactive les journaux de diagnostic quand une alerte est déclenchée sur Azure Stack Hub.
Si les conditions d’intégrité du système doivent être examinées, les journaux peuvent être automatiquement chargés à des fins d’analyse avant l’ouverture d’un cas de support auprès des services de support technique Microsoft (CSS). 

## <a name="steps-to-configure-proactive-log-collection"></a>Procédure de configuration de la collecte proactive des journaux

Suivez cette procédure pour configurer la collecte proactive des journaux. La collecte automatique des journaux peut être désactivée et réactivée à tout moment.  

1. Connectez-vous au portail d’administration Azure Stack Hub.
1. Ouvrez **Aide et support - Vue d'ensemble**.
1. Si la bannière s’affiche, cliquez sur **Enable proactive log collection** (Activer la collecte proactive des journaux). 

   ![Capture d’écran montrant où activer la collecte des journaux dans Aide et support](media/azure-stack-help-and-support/banner-enable-automatic-log-collection.png)


   Vous pouvez aussi cliquer sur **Settings** (Paramètres), définir **Proactive log collection** (Collecte proactive des journaux) sur **Enable** (Activer), puis cliquer sur **Save** (Enregistrer).

   ![Capture d’écran montrant où activer la collecte des journaux dans Aide et support](media/azure-stack-help-and-support/settings-enable-automatic-log-collection.png)


## <a name="view-log-collection"></a>Afficher la collecte des journaux

L’historique des journaux collectés à partir d'Azure Stack Hub s'affiche sur la page **Collecte de journaux** dans Aide et support, avec les dates et heures suivantes :

- **Time collected** (Heure de la collecte) : heure à laquelle l'opération de collecte des journaux a commencé
- **État** : En cours ou terminé
- **Logs start** (Début des journaux) : date à laquelle vous souhaitez commencer à collecter des journaux
- **Logs end** (Fin des journaux) : date de fin de la collecte
- **Type** : S’il s’agit d’une collecte de journaux manuelle ou proactive 


![Capture d’écran montrant les collectes de journaux](media/azure-stack-help-and-support/azure-stack-log-collection.png)


## <a name="proactive-diagnostic-log-collection-alerts"></a>Alertes de collecte proactive des journaux de diagnostic 

Si elle est activée, la collecte de journaux proactive charge les journaux uniquement quand l’un des événements suivants est déclenché. 

Par exemple, **Échec de la mise à jour** est une alerte qui déclenche la collecte proactive des journaux de diagnostic. Si elle est activée, les journaux de diagnostic sont capturés de manière proactive au cours d’un échec de mise à jour pour aider les services de support technique Microsoft (CSS) à résoudre le problème. Les journaux de diagnostic sont collectés uniquement quand l’alerte pour **Échec de la mise à jour** est déclenchée. 

|Titre de l’alerte  | FaultIdType|    
|-------------|------------|
|Impossible de se connecter au service distant |  UsageBridge.NetworkError|
|Échec de la mise à jour |    Urp.UpdateFailure   |          
|Infrastructure du fournisseur de ressources de stockage/dépendances non disponibles |  StorageResourceProviderDependencyUnavailable     |     
|Nœud non connecté au contrôleur|  ServerHostNotConnectedToController   |     
|Échec de publication de route |    SlbMuxRoutePublicationFailure | 
|Magasin de données interne du fournisseur de ressources de stockage non disponible |    StorageResourceProvider. DataStoreConnectionFail     |       
|Défaillance d’unité de stockage | Microsoft.Health.FaultType.VirtualDisks.Detached   |      
|Le contrôleur d’intégrité ne peut pas accéder au compte de stockage | Microsoft.Health.FaultType.StorageError |    
|La connectivité à un disque physique a été perdue |    Microsoft.Health.FaultType.PhysicalDisk.LostCommunication    |    
|Le service Blob n’est pas exécuté sur un nœud | StorageService.The.blob.service.is.not.running.on.a.node-Critical | 
|Rôle d’infrastructure défectueux |    Microsoft.Health.FaultType.GenericExceptionFault |        
|Erreurs du service de Table | StorageService.Table.service.errors-Critical |              
|Un partage de fichiers est utilisé à plus de 80 % |    Microsoft.Health.FaultType.FileShare.Capacity.Warning.Infra |       
|Nœud d’unité d’échelle hors ligne | FRP.Heartbeat.PhysicalNode |  
|Instance de rôle d’infrastructure non disponible | FRP.Heartbeat.InfraVM   |    
|Instance de rôle d’infrastructure non disponible  |    FRP.Heartbeat.NonHaVm     |        
|Le rôle de l'infrastructure, Gestion de répertoires, a signalé des erreurs de synchronisation temporelle |  DirectoryServiceTimeSynchronizationError |     
|Pending external certificate expiration (Expiration imminente du certificat externe) |  CertificateExpiration.ExternalCert.Warning |
|Pending external certificate expiration (Expiration imminente du certificat externe) |  CertificateExpiration.ExternalCert.Critical |
|Impossible de provisionner des machines virtuelles pour une classe et une taille spécifiques en raison d’une capacité de mémoire insuffisante |  AzureStack.ComputeController.VmCreationFailure.LowMemory |
|Nœud inaccessible pour la sélection élective d’ordinateur virtuel |  AzureStack.ComputeController.HostUnresponsive | 
|Échec de la sauvegarde  | AzureStack.BackupController.BackupFailedGeneralFault |    
|La sauvegarde planifiée a été ignorée en raison d’un conflit avec des opérations qui ont échoué  | AzureStack.BackupController.BackupSkippedWithFailedOperationFault |   


## <a name="see-also"></a>Voir aussi

[Gestion des journaux et des données client Azure Stack Hub](azure-stack-data-collection.md)





