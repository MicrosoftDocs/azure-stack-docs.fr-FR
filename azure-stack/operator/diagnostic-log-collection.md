---
title: Collecte des journaux de diagnostic
description: Découvrez la collecte des journaux de diagnostic.
author: PatAltimore
ms.topic: article
ms.date: 02/03/2021
ms.author: patricka
ms.reviewer: shisab
ms.lastreviewed: 02/03/2021
ms.openlocfilehash: ad5f0a7f6028249dba3d63490cdc3c91d7a45e72
ms.sourcegitcommit: 69c700a456091adc31e4a8d78e7a681dfb55d248
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100013215"
---
# <a name="diagnostic-log-collection"></a>Collecte des journaux de diagnostic

Vous pouvez partager des journaux de diagnostic créés par Azure Stack Hub. Ces journaux sont créés par les composants Windows et des services Azure locaux. Le Support Microsoft peut utiliser les journaux pour corriger ou identifier des problèmes liés à votre instance Azure Stack Hub.

Pour vous familiariser avec la collecte des journaux de diagnostic Azure Stack Hub, vous devez inscrire votre instance. Si vous n'avez pas inscrit Azure Stack Hub, utilisez [le point de terminaison privilégié (PEP)](azure-stack-get-azurestacklog.md) pour partager les journaux. 

::: moniker range=">= azs-2005"

Vous pouvez envoyer des journaux de diagnostic au Support Microsoft de plusieurs façons. Selon votre connectivité à Azure, vos options sont les suivantes :
* [Envoyer les journaux de manière proactive (recommandé)](#send-logs-proactively)
* [Envoyer des journaux maintenant](#send-logs-now)
* [Enregistrer les journaux localement](#save-logs-locally)

L’organigramme montre l’option à utiliser pour l’envoi des journaux de diagnostic. Si Azure Stack Hub se connecte à Azure, activez la **Collecte proactive des journaux**. La collecte proactive des journaux charge automatiquement les journaux de diagnostic vers un blob de stockage contrôlé par Microsoft dans Azure quand une alerte critique est levée. Vous pouvez également collecter les journaux à la demande en utilisant l'option **Envoyer les journaux maintenant**. Pour un Azure Stack Hub s’exécutant dans un environnement déconnecté ou si vous rencontrez des problèmes de connectivité, choisissez l’option **Enregistrer les journaux localement**.

![Organigramme montrant comment envoyer des journaux à Microsoft](media/azure-stack-help-and-support/send-logs-now-flowchart.png)

::: moniker-end

## <a name="send-logs-proactively"></a>Send logs proactively (Envoyer les journaux de manière proactive)

La collecte proactive des journaux récupère et envoie automatiquement les journaux de diagnostic d’Azure Stack Hub à Microsoft avant que vous n’ouvriez un dossier de support. Ces journaux sont collectés uniquement quand une alerte d’intégrité du système est déclenchée et ne sont accessibles au support Microsoft que dans le contexte d’un cas de support.

::: moniker range=">= azs-2008"

Depuis Azure Stack Hub version 2008, la collecte proactive des journaux utilise un algorithme amélioré qui capture les journaux même dans des conditions d’erreur non visibles par un opérateur. Cela permet de garantir que les informations de diagnostic appropriées sont collectées au bon moment sans l’intervention d’un opérateur. Le Support Microsoft peut commencer à isoler et résoudre les problèmes plus tôt dans certains cas. Les améliorations initiales des algorithmes se concentrent sur les opérations de correction et de mise à jour.

Azure Stack Hub collecte des journaux pour des alertes et d’autres événements d’échec masqués que vous ne pouvez pas voir.

Azure Stack Hub collecte de manière proactive des journaux pour :

- La mise à jour a échoué.
- La mise à jour nécessite votre attention.

Quand un événement déclenche ces alertes, Azure Stack Hub envoie de manière proactive les journaux à Microsoft.

En outre, Azure Stack Hub envoie à Microsoft des journaux déclenchés par d’autres événements d’échec. Vous ne pouvez pas voir ces événements.

Il est recommandé d’activer la collecte de journaux proactive, car davantage d’opérations sont optimisées, et les avantages sont plus importants.

::: moniker-end

La collecte proactive des journaux peut être désactivée et réactivée à tout moment. Effectuez les étapes suivantes pour configurer la collecte proactive des journaux.

1. Connectez-vous au portail d’administration Azure Stack Hub.
1. Ouvrez **Aide et support - Vue d'ensemble**.
1. Si la bannière apparaît, sélectionnez **Activer la collecte proactive des journaux**. Vous pouvez aussi sélectionner **Paramètres**, définir **Collecte proactive des journaux** sur **Activer**, puis sélectionner **Enregistrer**.

> [!NOTE]
> Si les paramètres d’emplacement des journaux sont configurés pour un partage de fichiers local, vérifiez que les stratégies de gestion du cycle de vie empêchent le stockage de partage d’atteindre son quota de taille. Azure Stack Hub n’analyse pas le partage de fichiers local ou n’applique aucune stratégie de rétention.   

### <a name="how-the-data-is-handled"></a>Gestion des données

Vous acceptez que Microsoft procède régulièrement et automatiquement à la collecte des journaux sur la seule base des alertes d’intégrité du système Azure Stack Hub. Vous reconnaissez et acceptez également le chargement et la conservation de ces journaux dans un compte de stockage Azure managé et contrôlé par Microsoft.

Les données sont exclusivement utilisées à des fins de dépannage des alertes d'intégrité du système, et non à des fins de marketing, de publicité ou à d'autres fins commerciales si vous n'y avez pas consenti. Les données peuvent être conservées pendant une période maximale de 90 jours, et toutes les données collectées par Microsoft sont traitées conformément à nos [pratiques de confidentialité standard](https://privacy.microsoft.com/).

Les données déjà collectées avec votre consentement ne sont pas affectées par la révocation de votre autorisation.

Les journaux collectés à l’aide de la **collecte proactive des journaux** sont chargés sur un compte de stockage Azure managé et contrôlé par Microsoft. Microsoft peut accéder à ces journaux dans le contexte d'un cas de support et pour améliorer l'intégrité d'Azure Stack Hub.

## <a name="send-logs-now"></a>Envoyer des journaux maintenant

> [!TIP]
> Gagnez du temps en utilisant l'option [Envoyer les journaux de manière proactive](#send-logs-proactively) plutôt que l'option Envoyer les journaux maintenant.

Envoyer les journaux maintenant est une option qui vous permet de collecter et charger manuellement vos journaux de diagnostic à partir d’Azure Stack Hub, généralement avant d’ouvrir un cas de support.

Deux méthodes sont disponibles pour envoyer manuellement les journaux de diagnostic au Support Microsoft :
* [Portail administrateur (recommandé)](#send-logs-now-with-the-administrator-portal)
* [PowerShell](#send-logs-now-with-powershell)

Si Azure Stack Hub est connecté à Azure, nous vous recommandons d'utiliser le portail administrateur, car il s'agit de la façon plus simple d'envoyer les journaux directement à Microsoft. Si le portail n’est pas disponible, vous devez envoyer les journaux à l’aide de PowerShell.

### <a name="send-logs-now-with-the-administrator-portal"></a>Envoyer les journaux maintenant à l'aide du portail administrateur

Pour envoyer les journaux maintenant à l'aide du portail administrateur :

1. Ouvrez **Help + support > Log Collection > Send logs now** (Aide + support > Collecte de journaux > Envoyer les journaux maintenant). 
1. Spécifiez l’heure de début et l’heure de fin de la collecte des journaux. 
1. Choisissez le fuseau horaire local.
1. Sélectionnez **Collect and Upload** (Collecter et charger).

Si vous êtes déconnecté d’Internet ou voulez enregistrer les journaux uniquement localement, utilisez la méthode [Get-AzureStackLog](azure-stack-get-azurestacklog.md) pour envoyer les journaux.

### <a name="send-logs-now-with-powershell"></a>Envoyer les journaux maintenant à l'aide de PowerShell

Si vous utilisez la méthode **Envoyer les journaux maintenant** et que vous souhaitez utiliser PowerShell au lieu du portail administrateur, vous pouvez utiliser la cmdlet `Send-AzureStackDiagnosticLog` pour collecter et envoyer des journaux spécifiques.

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

  ::: moniker range=">= azs-2008"

  Pour envoyer les journaux de diagnostic pour SQL RP : 

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider SQLAdapter
  ```
  Pour envoyer les journaux de diagnostic pour MySQL RP : 

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider MySQLAdapter
  ```
  
  ::: moniker-end

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

> [!NOTE]
> Si vous êtes déconnecté d'Internet ou souhaitez uniquement enregistrer les journaux localement, utilisez la méthode [Get-AzureStackLog](azure-stack-get-azurestacklog.md) pour envoyer les journaux. 

### <a name="how-the-data-is-handled"></a>Gestion des données

En lançant la collecte des journaux de diagnostic à partir d’Azure Stack Hub, vous reconnaissez et acceptez de charger ces journaux et de les conserver dans un compte de stockage Azure managé et contrôlé par Microsoft. Le support Microsoft peut accéder à ces journaux immédiatement avec le cas de support sans avoir à contacter le client pour la collecte des journaux.

::: moniker range=">= azs-2005"

## <a name="save-logs-locally"></a>Enregistrer les journaux localement

Vous pouvez enregistrer des journaux dans un partage SMB (Server Message Block) local quand Azure Stack Hub est déconnecté d’Azure. Vous pouvez, par exemple, exécuter un environnement déconnecté. Si vous êtes connecté normalement mais rencontrez des problèmes de connectivité, vous pouvez enregistrer les journaux localement pour faciliter le dépannage.

 Dans le panneau **Paramètres**, entrez le chemin d’accès et un nom d’utilisateur et un mot de passe avec l’autorisation d’écrire sur le partage. Au cours d’un cas de support, le Support Microsoft fournit des étapes détaillées sur la façon dont ces journaux locaux doivent être transférés. Si le portail d’administration n’est pas disponible, vous pouvez utiliser la cmdlet [Get-AzureStackLog](azure-stack-get-azurestacklog.md) pour enregistrer les journaux localement.

![Capture d’écran des options de collecte des journaux de diagnostic](media/azure-stack-help-and-support/save-logs-locally.png)

::: moniker-end

## <a name="bandwidth-considerations"></a>Remarques relatives à la bande passante

La taille moyenne de la collecte des journaux de diagnostic varie selon que la collecte est proactive ou manuelle. La taille moyenne de la **collecte proactive des journaux** est d’environ 2 Go. La taille de la collecte pour l’option **Send logs now** (Envoyer les journaux maintenant) dépend du nombre d’heures collectées.

Le tableau suivant répertorie les éléments à prendre en compte pour les environnements avec des connexions limitées à Azure.

| Connexion réseau | Impact |
|----|---|
| Connexion à faible bande passante/latence élevée | Le chargement des journaux prendra beaucoup de temps. |
| Connexion partagée | Le chargement peut également avoir un impact sur d'autres applications/utilisateurs partageant la connexion réseau. |
| Connexion limitée | Il se peut que votre fournisseur de services Internet facture d’autres frais pour l'utilisation supplémentaire du réseau. |

## <a name="view-log-collection"></a>Afficher la collecte des journaux

L'historique des journaux collectés à partir d'Azure Stack Hub apparaît sur la page **Collecte des journaux** d'**Aide et support**, avec les dates et heures suivantes :

- **Time collected** (Heure de la collecte) : heure à laquelle l'opération de collecte des journaux a commencé.
- **État** : En cours ou Terminé.
- **Logs start** (Début des journaux) : date à laquelle vous souhaitez commencer à collecter des journaux.
- **Logs end** (Fin des journaux) : date de fin de la collecte.
- **Type** : s'il s'agit d'une collecte de journaux manuelle ou proactive.

![Collecte des journaux dans Aide et support](media/azure-stack-help-and-support/azure-stack-log-collection.png)

## <a name="see-also"></a>Voir aussi

[Gestion des journaux et des données client Azure Stack Hub](./azure-stack-data-collection.md)
