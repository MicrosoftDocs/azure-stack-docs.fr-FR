---
title: Collecte de données d’Azure Stack HCI
description: Cette rubrique décrit la conception et les stratégies qui concernent les données de diagnostic collectées par Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 12/10/2020
ms.openlocfilehash: 91f578bcb0dc3a9d2debd086e6ed95cb591de90d
ms.sourcegitcommit: afdae61022037b5dba8345cb264049897e0aca8f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97053131"
---
# <a name="azure-stack-hci-data-collection"></a>Collecte de données d’Azure Stack HCI

> S’applique à : Azure Stack HCI, version 20H2

Cette rubrique décrit les données qui doivent être collectées pour garantir qu’Azure Stack HCI reste sécurisé, à jour et fonctionne comme prévu pour la mise à disposition générale (GA) en décembre 2020.

Les données décrites ci-dessous sont nécessaire pour permettre à Microsoft de fournir Azure Stack HCI. Ces données sont collectées une fois par jour, et les événements de collecte de données peuvent être consultés dans les journaux d’événements. Azure Stack HCI collecte le minimum de données nécessaire pour garantir que vos clusters restent à jour, sécurisés et opérationnels.

   > [!IMPORTANT]
   > Les données décrites ci-dessous, qui sont collectées par Azure Stack HCI, sont indépendantes des données de diagnostic Windows, qui peuvent être configurées pour divers niveaux de collecte. Dans Azure Stack HCI, le paramètre par défaut pour la collecte des données de diagnostic Windows est Sécurité (désactivé), ce qui signifie que les données de diagnostic Windows ne sont en aucun cas envoyées, à moins que l’administrateur modifie les paramètres de données de diagnostic. Pour plus d’informations, consultez [Configurer les données de diagnostic Windows dans votre organisation](/windows/privacy/configure-windows-diagnostic-data-in-your-organization). Microsoft est un contrôleur indépendant des données de diagnostic Windows collectées dans le cadre d’Azure Stack HCI. Microsoft traite les données de diagnostic Windows dans le respect de la [déclaration de confidentialité Microsoft](https://privacy.microsoft.com/privacystatement).

## <a name="data-collection-and-residency"></a>Collecte et résidence des données

Ces données Azure Stack HCI : 

- ne sont pas envoyées à Microsoft tant que le produit n’est pas inscrit auprès d’Azure. Cette collecte de données cesse dès la désinscription d’Azure Stack HCI.
- sont journalisées dans le canal d’événements Microsoft-AzureStack-HCI/Analytic. 
- sont au format JSON, ce qui permet aux administrateurs système d’examiner et analyser les données envoyées.
- sont stockées aux États-Unis dans un centre de données sécurisé géré par Microsoft.

## <a name="data-retention"></a>Conservation des données

Une fois ces données collectées par Azure Stack HCI, elles sont conservées pendant 90 jours. Les données agrégées, anonymisées, peuvent être conservées plus longtemps.

## <a name="what-data-is-collected"></a>Quelles sont les données collectées ?

Voici ce qu’Azure Stack HCI collecte :

- Informations sur les serveurs comme la version du système d’exploitation, le modèle de processeur, le nombre de cœurs de processeur, la taille de mémoire, l’identificateur de cluster et le hachage de l’ID de matériel
- Liste des fonctionnalités serveur Azure Stack HCI installées (par exemple, BitLocker)
- Informations nécessaires au calcul de la fiabilité du système d’exploitation Azure Stack HCI
- Informations nécessaires au calcul de la fiabilité des données de collecte relatives à l’intégrité
- Informations collectées à partir du journal des événements pour des erreurs spécifiques, telles qu’un téléchargement de mises à jour avorté
- Informations pour le calcul de la fiabilité du stockage
- Informations pour le calcul de la fiabilité des disques physiques
- Informations pour le calcul de la fiabilité du chiffrement des volumes
- Informations pour le calcul de la fiabilité et des performances de la réparation des espaces de stockage
- Informations pour valider la sécurité du système d’exploitation Azure Stack HCI
- Informations pour calculer la fiabilité de l’état de l’antivirus/du logiciel anti-programme malveillant du système d’exploitation Azure Stack HCI
- Informations pour mettre en corrélation la fiabilité des composants réseau
- Informations pour mettre en corrélation les performances réseau
- Informations pour mettre en corrélation la fiabilité des mises à jour et des installations
- Informations pour mesurer la fiabilité d’Hyper-V
- Informations pour mesurer/mettre en corrélation la fiabilité des composants de clustering
- Informations pour suivre la réussite de la fonctionnalité de mise à jour adaptée aux clusters
- Informations pour mesurer/mettre en corrélation la fiabilité de la fonctionnalité de reprise d’activité après sinistre
- Informations pour décrire les limites de bande passante SMB appliquées aux serveurs Azure Stack HCI

## <a name="view-this-data"></a>Afficher ces données

1. Activez le journal analytique à l’aide de la commande PowerShell suivante :

   ```PowerShell
   wevtutil sl Microsoft-AzureStack-HCI/Analytic /e:True
   ```

2. Affichez le journal pour voir les données collectées :

   ```PowerShell
   Get-WinEvent -LogName Microsoft-AzureStack-HCI/Analytic -Oldest
   ```

3. Mettez en forme les données pour l’exportation :

   ```PowerShell
   Get-WinEvent -LogName Microsoft-AzureStack-HCI/Analytic -Oldest `
   | Where-Object Id -eq 802 `
   | ForEach-Object { 
       [pscustomobject] @{
           TimeCreated = $_.TimeCreated 
           EventName=$_.Properties[0].Value 
           Value=$_.Properties[1].Value 
       } 
   }
   ```
 
Le résultat suivant doit ressembler à ce qui suit :

```shell
TimeCreated            EventName                                                  Value
-----------            ---------                                                  -----
11/16/2020 10:36:28 AM Microsoft.AzureStack.HCI.Diagnostic.Core                   {"OEMName":"Microsoft Corporation"...
11/16/2020 10:36:28 AM Microsoft.AzureStack.HCI.Diagnostic.ProductFeatures        {"InstalledFeatures":["Server-Core...
11/16/2020 10:36:28 AM Microsoft.AzureStack.HCI.Diagnostic.OSReliability          {"DailyDirtyRestarts":0,"WeeklyDir...
11/16/2020 10:36:28 AM Microsoft.AzureStack.HCI.Diagnostic.DiagnosticHealth       {"DailySuccessfulDiagnosticUploads...
11/16/2020 10:36:28 AM Microsoft.AzureStack.HCI.Diagnostic.ErrorSummary           {"ErrorSummary":[{"EventName":"Win...
11/16/2020 10:36:29 AM Microsoft.AzureStack.HCI.Diagnostic.VolumeSummary          {"VolumeCount":2,"HealthyVolumeCou...
11/16/2020 10:36:29 AM Microsoft.AzureStack.HCI.Diagnostic.DiskSummary            {"DiskCount":33,"Summary":[]}
11/16/2020 10:36:29 AM Microsoft.AzureStack.HCI.Diagnostic.BitlockerVolumeSummary {"BitlockerVolumeCount":0,"Summary...
11/16/2020 10:36:29 AM Microsoft.AzureStack.HCI.Diagnostic.StorageErrors          {"ErrorSummary":[{"EventName":"Sto...
11/16/2020 10:36:29 AM Microsoft.AzureStack.HCI.Diagnostic.StorageRepairSummary   {"DailyRepairStartCount":0,"Weekly...
11/16/2020 10:36:29 AM Microsoft.AzureStack.HCI.Diagnostic.TrustedPlatformModule  {"Manufacturer":"MSFT","Manufactur...
11/16/2020 10:36:29 AM Microsoft.AzureStack.HCI.Diagnostic.MicrosoftDefender      {"AMEngineVersion":"1.1.17600.5","...
11/16/2020 10:36:30 AM Microsoft.AzureStack.HCI.Diagnostic.NetworkInfo            {"NetworkDirect":true,"NetworkDire...
11/16/2020 10:36:30 AM Microsoft.AzureStack.HCI.Diagnostic.NetworkAdapterSummary  {"NetworkAdapterGroup":[{"DriverNa...
11/16/2020 10:36:30 AM Microsoft.AzureStack.HCI.Diagnostic.OSDeploy               {"OSInstallType":0}
11/16/2020 10:36:30 AM Microsoft.AzureStack.HCI.Diagnostic.ClusterProperties      {"Id":"fd2fc061-b924-4d61-a45b-3b3...
11/16/2020 10:36:30 AM Microsoft.AzureStack.HCI.Diagnostic.DisasterRecovery       {"IsDisasterRecoveryEnabled":false...
```
