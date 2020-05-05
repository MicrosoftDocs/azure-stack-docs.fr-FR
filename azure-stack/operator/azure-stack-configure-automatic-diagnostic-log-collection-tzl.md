---
title: Collecter de manière proactive les journaux de diagnostic quand une alerte est déclenchée sur Azure Stack Hub
description: Découvrez comment configurer la collecte proactive des journaux dans Azure Stack Hub Aide et support.
author: justinha
ms.topic: article
ms.date: 04/17/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 04/17/2020
ms.openlocfilehash: 636a3a62b60092e4869197432884533fac57c788
ms.sourcegitcommit: 355e21dd9b8c3f44e14abaae0b4f176443cf7495
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81625007"
---
# <a name="send-azure-stack-hub-diagnostic-logs-proactively"></a>Envoyer les journaux de diagnostic Azure Stack Hub de façon proactive

::: moniker range=">= azs-2002"

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

::: moniker-end
::: moniker range="<= azs-1910"

Nous vous recommandons de configurer la fonctionnalité de collecte automatique des journaux de diagnostic afin de simplifier la collecte des journaux et l’expérience du support technique. 
Si les conditions d’intégrité du système doivent être examinées, les journaux peuvent être automatiquement téléchargés à des fins d'analyse par les services de support technique Microsoft (CSS). 

## <a name="create-an-azure-blob-container-sas-url"></a>Créer une URL SAP de conteneur d'objets blob Azure 

Avant de configurer la collecte automatique des journaux, vous devez obtenir une signature d’accès partagé (SAP) pour un conteneur d’objets blob. Une signature d’accès partagé vous permet d'accorder l’accès aux ressources dans votre compte de stockage sans partager les clés de votre compte. 
Vous pouvez enregistrer les fichiers journaux Azure Stack Hub dans un conteneur d’objets blob dans Azure, puis fournir l’URL SAR dans laquelle CSS peut collecter les journaux. 

### <a name="prerequisites"></a>Prérequis

Vous pouvez utiliser un conteneur d’objets blob nouveau ou existant dans Azure. 
Pour créer un conteneur d’objets blob dans Azure, vous devez disposer au minimum du [rôle de contributeur de stockage d'objets blob](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) ou de l'[autorisation spécifique](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). 
Les administrateurs généraux disposent également de l’autorisation nécessaire. 

Pour connaître les meilleures pratiques en matière de choix des paramètres de compte de stockage de collecte de journaux, consultez [Meilleures pratiques pour la collecte automatique des journaux Azure Stack Hub](azure-stack-best-practices-automatic-diagnostic-log-collection.md). Pour plus d’informations sur les types de comptes de stockage, consultez [Vue d’ensemble des comptes de stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

### <a name="create-a-blob-storage-account"></a>Création d’un compte de stockage d’objets blob
 
1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Cliquez sur **Comptes de stockage** > **Ajouter**. 
1. Créez un conteneur d’objets blob avec les paramètres suivants :
   - **Abonnement**: Choisir votre abonnement Azure
   - **Groupe de ressources** : Spécifiez un groupe de ressources
   - **Nom du compte de stockage** : Spécifiez un nom de compte de stockage unique
   - **Emplacement** : Sélectionnez un centre de données conformément à la stratégie de votre entreprise
   - **Performances** : standard
   - **Type de compte** StorageV2 (v2 universel) 
   - **Réplication** : Stockage localement redondant (LRS)
   - **Niveau d’accès** : À froid

   ![Capture d’écran montrant les propriétés du conteneur d’objets blob](media/azure-stack-automatic-log-collection/azure-stack-log-collection-create-storage-account.png)

1. Cliquez sur **Vérifier + créer**, puis sur **Créer**.  

### <a name="create-a-blob-container"></a>Création d’un conteneur d’objets blob 

1. Une fois le déploiement réussi, cliquez sur **Accéder à la ressource**. Vous pouvez également épingler le compte de stockage au tableau de bord afin d'y accéder facilement. 
1. Cliquez sur **Explorateur Stockage (préversion)** , cliquez avec le bouton droit sur **Conteneurs d'objets blob**, puis cliquez sur **Créer un conteneur d’objets blob**. 
1. Entrez un nom pour le conteneur, puis cliquez sur **OK**.

## <a name="create-a-sas-url"></a>Créer un URL SAP

1. Cliquez avec le bouton droit sur le conteneur, puis cliquez sur **Obtenir une signature d’accès partagé**. 
   
   ![Capture d’écran montrant comment obtenir la signature d’accès partagé d’un conteneur d’objets blob](media/azure-stack-automatic-log-collection/get-sas.png)

1. Sélectionnez les propriétés suivantes :
   - Heure de début : vous pouvez reculer l'heure de début 
   - Heure d’expiration : deux ans
   - Fuseau horaire : UTC
   - Autorisations : lecture, écriture et énumération

   ![Capture d’écran montrant les propriétés de signature d’accès partagé](media/azure-stack-automatic-log-collection/sas-properties.png) 

1. Cliquez sur **Créer**.  

Copiez l’URL et entrez-la lorsque vous [configurez la collecte automatique des journaux](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md). 
Pour plus d’informations sur les URL SAP, consultez [Utilisation des signatures d’accès partagé (SAP)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1). 


## <a name="steps-to-configure-automatic-log-collection"></a>Procédure de configuration de la collecte automatique des journaux

Pour ajouter l'URL SAP à l’interface utilisateur de collecte des journaux, procédez comme suit : 

1. Connectez-vous au portail d’administration Azure Stack Hub.
1. Ouvrez **Aide et support - Vue d'ensemble**.
1. Cliquez sur **Paramètres de collecte automatique**.

   ![Capture d’écran montrant où activer la collecte des journaux dans Aide et support](media/azure-stack-automatic-log-collection/azure-stack-automatic-log-collection.png)

1. Définissez la collecte automatique des journaux sur **Activé**.
1. Entrez l’URL de signature d’accès partagé (SAP) du conteneur d’objets blob du compte de stockage.

   ![Capture d’écran montrant l’URL SAP blob](media/azure-stack-automatic-log-collection/azure-stack-enable-automatic-log-collection.png)

>[!NOTE]
>La collecte automatique des journaux peut être désactivée et réactivée à tout moment. La configuration de l’URL SAP ne change pas. Si la collecte automatique des journaux est réactivée, l’URL SAP entrée précédemment est soumise aux mêmes vérifications de validation et une URL SAP expirée sera rejetée. 


::: moniker-end

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

|Titre de l’alerte  |    FaultIdType|    
|-------------|------------|
|Impossible de se connecter au service distant |    UsageBridge.NetworkError|
|Échec de la mise à jour |    Urp.UpdateFailure    |          
|Infrastructure du fournisseur de ressources de stockage/dépendances non disponibles |    StorageResourceProviderDependencyUnavailable     |     
|Nœud non connecté au contrôleur|    ServerHostNotConnectedToController     |     
|Échec de publication de route |    SlbMuxRoutePublicationFailure |    
|Magasin de données interne du fournisseur de ressources de stockage non disponible |    StorageResourceProvider. DataStoreConnectionFail     |       
|Défaillance d’unité de stockage    | Microsoft.Health.FaultType.VirtualDisks.Detached     |      
|Le contrôleur d’intégrité ne peut pas accéder au compte de stockage | Microsoft.Health.FaultType.StorageError |    
|La connectivité à un disque physique a été perdue |    Microsoft.Health.FaultType.PhysicalDisk.LostCommunication     |    
|Le service Blob n’est pas exécuté sur un nœud |    StorageService.The.blob.service.is.not.running.on.a.node-Critical |    
|Rôle d’infrastructure défectueux |    Microsoft.Health.FaultType.GenericExceptionFault |        
|Erreurs du service de Table |    StorageService.Table.service.errors-Critical |              
|Un partage de fichiers est utilisé à plus de 80 % |    Microsoft.Health.FaultType.FileShare.Capacity.Warning.Infra |       
|Nœud d’unité d’échelle hors ligne    | FRP.Heartbeat.PhysicalNode |    
|Instance de rôle d’infrastructure non disponible |    FRP.Heartbeat.InfraVM    |    
|Instance de rôle d’infrastructure non disponible  |    FRP.Heartbeat.NonHaVm     |        
|Le rôle de l'infrastructure, Gestion de répertoires, a signalé des erreurs de synchronisation temporelle |    DirectoryServiceTimeSynchronizationError |       
|Pending external certificate expiration (Expiration imminente du certificat externe) |    CertificateExpiration.ExternalCert.Warning |
|Pending external certificate expiration (Expiration imminente du certificat externe) |    CertificateExpiration.ExternalCert.Critical |
|Impossible de provisionner des machines virtuelles pour une classe et une taille spécifiques en raison d’une capacité de mémoire insuffisante |    AzureStack.ComputeController.VmCreationFailure.LowMemory |
|Nœud inaccessible pour la sélection élective d’ordinateur virtuel |    AzureStack.ComputeController.HostUnresponsive |    
|Échec de la sauvegarde  | AzureStack.BackupController.BackupFailedGeneralFault |    
|La sauvegarde planifiée a été ignorée en raison d’un conflit avec des opérations qui ont échoué    | AzureStack.BackupController.BackupSkippedWithFailedOperationFault |    


## <a name="see-also"></a>Voir aussi

[Gestion des journaux et des données client Azure Stack Hub](azure-stack-data-collection.md)





