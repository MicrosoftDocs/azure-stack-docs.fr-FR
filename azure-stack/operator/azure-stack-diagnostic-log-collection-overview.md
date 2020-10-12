---
title: Collecte des journaux de diagnostic dans Azure Stack Hub
description: Découvrez-en plus sur la collecte des journaux de diagnostic dans Azure Stack Hub Aide et support.
author: myoungerman
ms.topic: article
ms.date: 08/24/2020
ms.author: v-myoung
ms.reviewer: shisab
ms.lastreviewed: 08/24/2020
ms.openlocfilehash: 88f2b267f493c05fdcd8a5419718d08d5b6efe37
ms.sourcegitcommit: 868887e4b13b1572f15004a9db2c334e60d8add2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91778237"
---
# <a name="diagnostic-log-collection-in-azure-stack-hub"></a>Collecte des journaux de diagnostic dans Azure Stack Hub

Azure Stack Hub est un ensemble de composants Windows et de services Azure locaux qui interagissent les uns avec les autres. Tous ces composants et services génèrent leur propre ensemble de journaux. Étant donné que le Support Microsoft utilise ces journaux pour identifier et résoudre efficacement vos problèmes, nous proposons la collecte des journaux de diagnostic. La collecte des journaux de diagnostic vous permet de collecter et de partager rapidement les journaux de diagnostic avec le Support Microsoft dans une interface utilisateur simple, qui ne nécessite pas PowerShell. Les journaux sont collectés, même si d’autres services d’infrastructure sont défaillants.  

Nous vous recommandons d’utiliser cette approche pour la collecte des journaux et d’[utiliser le point de terminaison privilégié (PEP)](azure-stack-get-azurestacklog.md) uniquement si le portail administrateur ou le panneau Aide et support n’est pas disponible. 

>[!NOTE]
>Azure Stack Hub doit être inscrit pour utiliser le regroupement des journaux de diagnostic. Si Azure Stack Hub n’est pas inscrit, utilisez le [point de terminaison privilégié (PEP)](azure-stack-get-azurestacklog.md) pour partager les journaux. 

## <a name="collection-options-and-data-handling"></a>Options de collecte et gestion des données

::: moniker range=">= azs-2005"

En fonction de la connectivité à Azure, Azure Stack Hub dispose des moyens appropriés pour collecter, enregistrer et envoyer des journaux de diagnostic à CSS. Si Azure Stack Hub peut se connecter à Azure, la méthode recommandée consiste à activer **Collection de journaux proactive**, qui charge automatiquement les journaux de diagnostic vers un blob de stockage contrôlé par Microsoft dans Azure lorsqu’une alerte critique est déclenchée. Vous pouvez également collecter les journaux à la demande à l’aide de **Envoyer des journaux maintenant** ou vous pouvez enregistrer les journaux localement si Azure Stack Hub est déconnecté d’Azure. 

Les sections suivantes décrivent chaque option et la façon dont vos données sont gérées dans chaque cas. 

::: moniker-end

La fonctionnalité de collecte des journaux de diagnostic propose deux façons d’envoyer des journaux :
* Envoyer des journaux de manière proactive
* Envoyer des journaux maintenant

Vous pouvez également enregistrer les journaux localement.

Le graphique suivant montre l’option à utiliser pour l’envoi des journaux de diagnostic dans chaque cas. 

![Organigramme montrant comment envoyer des journaux à Microsoft](media/azure-stack-help-and-support/send-logs-now-flowchart.png)

Les sections suivantes décrivent chaque option et la façon dont vos données sont gérées dans chaque cas. 

### <a name="send-logs-proactively"></a>Send logs proactively (Envoyer les journaux de manière proactive)

La collecte proactive des journaux récupère et envoie automatiquement les journaux de diagnostic d’Azure Stack Hub à Microsoft avant que vous n’ouvriez un dossier de support. Ces journaux sont collectés uniquement quand une [alerte d’intégrité du système](#proactive-diagnostic-log-collection-alerts) est déclenchée et ne sont accessibles au support Microsoft que dans le contexte d’un cas de support.

La collecte proactive des journaux peut être désactivée et réactivée à tout moment. Effectuez les étapes suivantes pour configurer la collecte proactive des journaux.

1. Connectez-vous au portail d’administration Azure Stack Hub.
1. Ouvrez **Aide et support - Vue d'ensemble**.
1. Si la bannière apparaît, sélectionnez **Activer la collecte proactive des journaux**. Vous pouvez aussi sélectionner **Paramètres**, définir **Collecte proactive des journaux** sur **Activer**, puis sélectionner **Enregistrer**.

>[!NOTE]
>Si les paramètres d’emplacement des journaux sont configurés pour un partage de fichiers local, vérifiez que les stratégies de gestion du cycle de vie empêchent le stockage de partage d’atteindre son quota de taille. Azure Stack Hub n’analyse pas le partage de fichiers local ou n’applique aucune stratégie de rétention.   

### <a name="send-logs-now"></a>Envoyer des journaux maintenant

> [!TIP]
> Économisez du temps en utilisant la [collecte proactive des journaux ](#send-logs-proactively) plutôt que l’option Envoyer les journaux maintenant.

Envoyer les journaux maintenant est une option qui vous permet de collecter et de charger manuellement vos journaux de diagnostic à partir d’Azure Stack Hub, généralement avant d’ouvrir un cas de support.

Vous pouvez envoyer manuellement des journaux de diagnostic au Support Microsoft en utilisant le portail administrateur ou PowerShell. Si Azure Stack Hub est connecté à Azure, nous vous recommandons d’utiliser le portail administrateur, car il s’agit de la façon plus simple d’envoyer les journaux directement à Microsoft. Si le portail n’est pas disponible, vous devez plutôt envoyer les journaux avec PowerShell.

Pour envoyer les journaux maintenant :

1. Ouvrez **Help + support > Log Collection > Send logs now** (Aide + support > Collecte de journaux > Envoyer les journaux maintenant). 
1. Spécifiez l’heure de début et l’heure de fin de la collecte des journaux. 
1. Choisissez le fuseau horaire local.
1. Sélectionnez **Collect and Upload** (Collecter et charger).

Si vous êtes déconnecté d’Internet ou que vous voulez uniquement enregistrer les journaux localement, utilisez la méthode [Get-AzureStackLog](azure-stack-get-azurestacklog.md) pour envoyer les journaux. 

::: moniker range=">= azs-2005"

### <a name="save-logs-locally"></a>Enregistrer les journaux localement

Vous pouvez enregistrer des journaux dans un partage SMB (Server Message Block) local quand Azure Stack Hub est déconnecté d’Azure. Dans le panneau **Paramètres**, entrez le chemin d’accès et un nom d’utilisateur et un mot de passe avec l’autorisation d’écrire sur le partage. Au cours d’un cas de support, le Support Microsoft fournit des étapes détaillées sur la façon dont ces journaux locaux doivent être transférés. Si le portail d’administration n’est pas disponible, vous pouvez utiliser la cmdlet [Get-AzureStackLog](azure-stack-get-azurestacklog.md) pour enregistrer les journaux localement.

![Capture d’écran des options de collecte des journaux de diagnostic](media/azure-stack-help-and-support/save-logs-locally.png)

::: moniker-end

## <a name="bandwidth-considerations"></a>Remarques relatives à la bande passante

La taille moyenne de la collecte des journaux de diagnostic varie selon que la collecte est proactive ou manuelle. La taille moyenne de la **collecte proactive des journaux** est d’environ 2 Go. La taille de la collecte pour l’option **Send logs now** (Envoyer les journaux maintenant) dépend du nombre d’heures collectées.

Le tableau suivant répertorie les éléments à prendre en compte pour les environnements avec des connexions limitées à Azure.

| Connexion réseau | Impact |
|----|---|
| Connexion à faible bande passante/latence élevée | Le chargement des journaux prendra beaucoup de temps. |
| Connexion partagée | Le chargement peut également avoir un impact sur d'autres applications/utilisateurs partageant la connexion réseau. |
| Connexion limitée | Des frais supplémentaires peuvent être facturés par votre fournisseur de services Internet pour l'utilisation supplémentaire du réseau. |

## <a name="parameter-considerations"></a>Considérations relatives aux paramètres 

* Vous pouvez utiliser les paramètres **FromDate** et **ToDate** pour collecter des journaux d’activité pour une période donnée. Si vous ne spécifiez pas ces paramètres, les journaux sont collectés par défaut pour les quatre dernières heures.

* Utilisez le paramètre **FilterByNode** pour filtrer les journaux d’activité par nom d’ordinateur. Par exemple :

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByNode azs-xrp01
  ```

* Utilisez le paramètre **FilterByLogType** pour filtrer les journaux d’activité par type. Vous pouvez choisir de filtrer par fichier, par partage ou par événement Windows. Par exemple :

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByLogType File
  ```

* Utilisez le paramètre **FilterByResourceProvider** pour envoyer les journaux de diagnostic des fournisseurs de ressources à valeur ajoutée. La syntaxe générale est la suivante :
 
  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider <<value-add RP name>>
  ```
 
  Pour envoyer les journaux de diagnostic pour IoT Hub : 

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider IotHub
  ```
 
  Pour envoyer les journaux de diagnostic pour Event Hubs :

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider eventhub
  ```
 
  Pour envoyer les journaux de diagnostic pour Azure Stack Edge :

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvide databoxedge
  ```

* Utilisez le paramètre **FilterByRole** pour envoyer les journaux de diagnostic à partir des rôles VirtualMachines et BareMetal :

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByRole VirtualMachines,BareMetal
  ```

* Pour envoyer les journaux de diagnostic à partir des rôles VirtualMachines et BareMetal, avec un filtrage de date pour les fichiers journaux pour les huit dernières heures :

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)
  ```

* Pour envoyer les journaux de diagnostic à partir des rôles VirtualMachines et BareMetal, avec un filtrage de date pour les fichiers journaux des 2 à 8 dernières heures :

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
  ```

Faites gagner du temps au service clientèle en collectant de manière proactive les journaux de diagnostic lorsqu'une alerte est déclenchée sur Azure Stack Hub.

Si les conditions d’intégrité du système doivent être examinées, les journaux peuvent être automatiquement chargés à des fins d’analyse avant l’ouverture d’un cas de support auprès du support Microsoft.

>[!NOTE]
>Si vous êtes déconnecté d’Internet ou souhaitez uniquement enregistrer les journaux localement, utilisez la méthode [Get-AzureStackLog](azure-stack-get-azurestacklog.md) pour envoyer les journaux. 

## <a name="view-log-collection"></a>Afficher la collecte des journaux

L'historique des journaux collectés à partir d'Azure Stack Hub apparaît sur la page **Collecte des journaux** d'**Aide et support**, avec les dates et heures suivantes :

- **Time collected** (Heure de la collecte) : heure à laquelle l'opération de collecte des journaux a commencé.
- **État** : En cours ou Terminé.
- **Logs start** (Début des journaux) : date à laquelle vous souhaitez commencer à collecter des journaux.
- **Logs end** (Fin des journaux) : date de fin de la collecte.
- **Type** : s'il s'agit d'une collecte de journaux manuelle ou proactive.

![Collecte des journaux dans Aide et support](media/azure-stack-help-and-support/azure-stack-log-collection.png)

## <a name="proactive-diagnostic-log-collection-alerts"></a>Alertes de collecte proactive des journaux de diagnostic

Si elle est activée, la collecte de journaux proactive charge les journaux uniquement quand l’un des événements suivants est déclenché.

Par exemple, **Échec de la mise à jour** est une alerte qui déclenche la collecte proactive des journaux de diagnostic. Si elle est activée, les journaux de diagnostic sont capturés de manière proactive au cours d’un échec de mise à jour pour aider le support Microsoft à résoudre le problème. Les journaux de diagnostic sont collectés uniquement quand l’alerte pour **Échec de la mise à jour** est déclenchée.

| Titre de l’alerte | FaultIdType |
|---|---|
|Impossible de se connecter au service distant | UsageBridge.NetworkError|
|Échec de la mise à jour | Urp.UpdateFailure |
|Infrastructure du fournisseur de ressources de stockage/dépendances non disponibles |    StorageResourceProviderDependencyUnavailable |
|Nœud non connecté au contrôleur| ServerHostNotConnectedToController |  
|Échec de publication de route | SlbMuxRoutePublicationFailure |
|Magasin de données interne du fournisseur de ressources de stockage non disponible |    StorageResourceProvider. DataStoreConnectionFail |
|Défaillance d’unité de stockage | Microsoft.Health.FaultType.VirtualDisks.Detached |
|Le contrôleur d'intégrité n'a pas accès au compte de stockage | Microsoft.Health.FaultType.StorageError |
|La connectivité à un disque physique a été perdue | Microsoft.Health.FaultType.PhysicalDisk.LostCommunication |
|Le service Blob n’est pas exécuté sur un nœud | StorageService.The.blob.service.is.not.running.on.a.node-Critical |
|Rôle d’infrastructure défectueux | Microsoft.Health.FaultType.GenericExceptionFault |
|Erreurs du service de Table | StorageService.Table.service.errors-Critical |
|Un partage de fichiers est utilisé à plus de 80 % | Microsoft.Health.FaultType.FileShare.Capacity.Warning.Infra |
|Nœud d’unité d’échelle hors ligne | FRP.Heartbeat.PhysicalNode |
|Instance de rôle d’infrastructure non disponible | FRP.Heartbeat.InfraVM |
|Instance de rôle d’infrastructure non disponible  | FRP.Heartbeat.NonHaVm |
|Le rôle de l'infrastructure, Gestion de répertoires, a signalé des erreurs de synchronisation temporelle | DirectoryServiceTimeSynchronizationError |
|Pending external certificate expiration (Expiration imminente du certificat externe) | CertificateExpiration.ExternalCert.Warning |
|Pending external certificate expiration (Expiration imminente du certificat externe) | CertificateExpiration.ExternalCert.Critical |
|Impossible de provisionner des machines virtuelles pour une classe et une taille spécifiques en raison d’une capacité de mémoire insuffisante | AzureStack.ComputeController.VmCreationFailure.LowMemory |
|Nœud inaccessible pour la sélection élective d’ordinateur virtuel | AzureStack.ComputeController.HostUnresponsive |
|Échec de la sauvegarde  | AzureStack.BackupController.BackupFailedGeneralFault |
|La sauvegarde planifiée a été ignorée en raison d’un conflit avec des opérations qui ont échoué    | AzureStack.BackupController.BackupSkippedWithFailedOperationFault |

## <a name="how-the-data-is-handled"></a>Gestion des données

Si vous activez **Send logs proactively** (Envoyer les journaux de manière proactive), vous acceptez que Microsoft procède régulièrement et automatiquement à la collecte des journaux sur la seule base des alertes d’intégrité du système Azure Stack Hub. Vous reconnaissez et acceptez également le chargement et la conservation de ces journaux dans un compte de stockage Azure managé et contrôlé par Microsoft.

Les données sont exclusivement utilisées à des fins de dépannage des alertes d'intégrité du système, et non à des fins de marketing, de publicité ou à d'autres fins commerciales si vous n'y avez pas consenti. Les données peuvent être conservées pendant une période maximale de 90 jours, et toutes les données collectées par Microsoft sont traitées conformément à nos [pratiques de confidentialité standard](https://privacy.microsoft.com/).

Les données déjà collectées avec votre consentement ne sont pas affectées par la révocation de votre autorisation.

## <a name="see-also"></a>Voir aussi

[Gestion des journaux et des données client Azure Stack Hub](./azure-stack-data-collection.md)
