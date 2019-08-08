---
title: Collecter les journaux de diagnostic Azure Stack maintenant | Microsoft Docs
description: Comment configurer la collecte des journaux de diagnostic à la demande dans Azure Stack Aide et support.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 07/25/2019
ms.openlocfilehash: 25b72df875dd78e7ddea42c4505345f4225cc948
ms.sourcegitcommit: f6ea6daddb92cbf458f9824cd2f8e7e1bda9688e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68497321"
---
# <a name="collect-azure-stack-diagnostic-logs-now-on-demand"></a>Collecter les journaux de diagnostic Azure Stack maintenant (à la demande)

*S’applique à : systèmes intégrés Azure Stack*

Dans le cadre de la résolution des problèmes, les services de support technique Microsoft (CSS) peuvent avoir besoin d’analyser les journaux de diagnostic. Depuis la version 1907, les opérateurs Azure Stack peuvent télécharger les journaux de diagnostic à la demande vers un conteneur d’objets blob dans Azure dans **Aide et support.** . Si le portail n’est pas disponible, les opérateurs peuvent collecter les journaux avec Get-AzureStackLog via le point de terminaison privilégié (PEP). Cette rubrique décrit les deux méthodes de collecte des journaux de diagnostic à la demande.

## <a name="using-help-and-support"></a>Utilisation d’Aide et support

Pour résoudre un problème, CSS peut demander à l’opérateur Azure Stack de collecter les journaux de diagnostic à la demande pour une période donnée au cours de la semaine précédente. Dans ce cas, CSS fournira à l’opérateur une URL SAS pour le chargement de la collecte. Procédez comme suit pour configurer la collecte des journaux à la demande à l’aide de l’URL SAS fournie par CSS :

1. Ouvrez **Aide et support - Vue d’ensemble**, puis cliquez sur **Collecter les journaux maintenant**. 
1. Choisissez une fenêtre de 1à 4 heures sur les sept derniers jours. 
1. Choisissez le fuseau horaire local.
1. Entrez l’URL SAS fournie par CSS.

   ![Capture d’écran de la collecte de journaux à la demande](media/azure-stack-automatic-log-collection/collect-logs-now.png)

>[!NOTE]
>Si la collecte automatique des journaux de diagnostic est activée, **Aide et support** s’affiche lorsque la collecte des journaux est en cours. Si vous cliquez sur **Collecter les journaux maintenant** pour collecter les journaux à partir d’une heure précise alors que la collecte automatique des journaux est en cours, la collecte à la demande commence après la fin de la collecte automatique des journaux. 

## <a name="using-pep"></a>Utilisation du PEP

<!--how do you look up the PEP IP address. You look up the azurestackstampinfo.json--->

Les outils de diagnostic Azure Stack aident à garantir la simplicité et l’efficacité de la collecte des journaux. Le diagramme suivant illustre le fonctionnement des outils de diagnostic :

![Outils de diagnostic Azure Stack](media/azure-stack-diagnostics/get-azslogs.png)

### <a name="trace-collector"></a>Collecteur de traces

Le collecteur de traces est activé par défaut et s’exécute en continu en arrière-plan pour collecter tous les journaux d’activité de suivi d’événements pour Windows (ETW) à partir des services composants d’Azure Stack. Les journaux d’activité ETW sont stockés dans un partage local commun avec une durée de vie de cinq jours. Une fois cette limite atteinte, les fichiers les plus anciens sont supprimés à mesure que de nouveaux sont créés. La taille maximale autorisée par défaut pour chaque fichier est de 200 Mo. Une vérification de la taille a lieu toutes les deux minutes ; si le fichier actuel a une taille supérieure ou égale à 200 Mo, il est enregistré et un nouveau fichier est généré. Il existe également une limite de 8 Go portant sur la taille totale des fichiers générés par session d’événements.

### <a name="get-azurestacklog"></a>Get-AzureStackLog

Vous pouvez utiliser la cmdlet PowerShell Get-AzureStackLog pour collecter des journaux d’activité à partir de tous les composants dans un environnement Azure Stack. Elle les enregistre dans des fichiers zip à un emplacement défini par l’utilisateur. Si l’équipe de support technique Azure Stack a besoin de vos journaux d’activité pour résoudre un problème, elle peut vous demander Get-AzureStackLog.

> [!CAUTION]
> Ces fichiers journaux peuvent contenir des informations d’identification personnelle. Pensez-y avant de publier des fichiers journaux publiquement.

Voici quelques exemples de types de journaux collectés :

* **Journaux d’activité de déploiement Azure Stack**
* **Journaux des événements Windows**
* **Journaux d’activité Panther**
* **Journaux d’activité de cluster**
* **Journaux de diagnostics de stockage**
* **Journaux d’activité ETW**

Ces fichiers sont collectés et enregistrés dans un partage par le Collecteur de traces. Vous pouvez ensuite utiliser la cmdlet Get-AzureStackLog pour les collecter en cas de nécessité.

#### <a name="to-run-get-azurestacklog-on-azure-stack-integrated-systems"></a>Pour exécuter Get-AzureStackLog sur des systèmes intégrés Azure Stack

Pour exécuter Get-AzureStackLog sur un système intégré, vous devez avoir accès au point de terminaison privilégié (PEP). Voici un exemple de script que vous pouvez exécuter à l’aide du point de terminaison privilégié pour collecter des journaux d’activité sur un système intégré :

```powershell
$ipAddress = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.

$password = ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object -TypeName System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $password)

$shareCred = Get-Credential

$session = New-PSSession -ComputerName $ipAddress -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2) # Provide the time that includes the period for your issue

Invoke-Command -Session $session { Get-AzureStackLog -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if ($session) {
    Remove-PSSession -Session $session
}
```

#### <a name="run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system"></a>Exécuter Get-AzureStackLog sur un système ASDK (Kit de développement Azure Stack)

Suivez ces étapes pour exécuter `Get-AzureStackLog` sur un ordinateur hôte ASDK.

1. Connectez-vous en tant qu’**AzureStack\CloudAdmin** sur l’ordinateur hôte ASDK.
2. Ouvrez une nouvelle fenêtre PowerShell en tant qu’administrateur.
3. Exécutez l’applet de commande PowerShell **Get-AzureStackLog**.

#### <a name="examples"></a>Exemples

* Collecter tous les journaux d’activité pour tous les rôles :

  ```powershell
  Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred
  ```

* Collecter les journaux d’activité à partir des rôles VirtualMachines et BareMetal :

  ```powershell
  Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal
  ```

* Collecter les journaux d’activité à partir des rôles VirtualMachines et BareMetal, avec un filtrage de date pour les fichiers journaux pour les huit dernières heures :

  ```powershell
  Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)
  ```

* Collecter les journaux d’activité à partir des rôles VirtualMachines et BareMetal, avec un filtrage de date pour les fichiers journaux des 2 à 8 dernières heures :

  ```powershell
  Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
  ```

* Collectez les journaux et stockez-les dans le conteneur d’objets blob Stockage Azure spécifié. La syntaxe générale de cette opération est la suivante :

  ```powershell
  Get-AzureStackLog -OutputSasUri "<Blob service SAS Uri>"
  ```

  Par exemple :

  ```powershell
  Get-AzureStackLog -OutputSasUri "https://<storageAccountName>.blob.core.windows.net/<ContainerName><SAS token>"
  ```

  > [!NOTE]
  > Cette procédure est utile pour le chargement des journaux. Même si vous n’avez pas de partage SMB accessible ni d’accès à Internet, vous pouvez créer un compte de stockage Blob sur votre système Azure Stack pour y transférer les journaux, et ensuite utiliser votre client pour récupérer ces journaux.  

  Pour générer le jeton SAS du compte de stockage, les autorisations suivantes sont nécessaires :

  * Accès au service Stockage Blob
  * Accès au type de ressource du conteneur

  Pour générer une valeur d’URI SAP à utiliser pour le paramètre `-OutputSasUri`, effectuez ces étapes :

  1. Créez un compte Stockage en suivant les étapes [dans cet article](/azure/storage/common/storage-quickstart-create-account).
  2. Ouvrez une instance de l’Explorateur Stockage Azure.
  3. Connectez-vous au compte de stockage créé à l’étape 1.
  4. Accédez à **Conteneurs d’objets Blob** dans **Services de stockage**.
  5. Sélectionnez **Créer un conteneur**.
  6. Cliquez avec le bouton droit sur le nouveau conteneur, puis cliquez sur **Obtenir une signature d’accès partagé**.
  7. Sélectionnez une **Heure de début** et une **Heure de fin** valides, selon vos besoins.
  8. Pour les autorisations nécessaires, sélectionnez **Lecture**, **Écriture** et **Liste**.
  9. Sélectionnez **Créer**.
  10. Vous obtenez une signature d’accès partagé. Copiez la partie URL et fournissez-la au paramètre `-OutputSasUri`.

### <a name="parameter-considerations-for-both-asdk-and-integrated-systems"></a>Considérations relatives aux paramètres du kit ASDK et des systèmes intégrés

* Les paramètres **OutputSharePath** et **OutputShareCredential** sont utilisés pour stocker les journaux d’activité dans un emplacement spécifié par l’utilisateur.

* Vous pouvez utiliser les paramètres **FromDate** et **ToDate** pour collecter des journaux d’activité pour une période donnée. Si vous ne spécifiez pas ces paramètres, les journaux d’activité sont collectés pour les quatre dernières heures par défaut.

* Utilisez le paramètre **FilterByNode** pour filtrer les journaux d’activité par nom d’ordinateur. Par exemple :

    ```powershell
    Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByNode azs-xrp01
    ```

* Utilisez le paramètre **FilterByLogType** pour filtrer les journaux d’activité par type. Vous pouvez choisir de filtrer par fichier, partage ou événement Windows. Par exemple :

    ```powershell
    Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByLogType File
    ```

* Vous pouvez utiliser le paramètre **TimeOutInMinutes** pour définir le délai d’expiration pour la collecte des journaux. Il est défini sur 150 (2,5 heures) par défaut.
* La collecte de journaux de fichiers de vidage est désactivée par défaut. Pour l’activer, utilisez le paramètre booléen **IncludeDumpFile**.
* Actuellement, vous pouvez utiliser le paramètre **FilterByRole** pour filtrer la collecte de journaux en fonction des rôles suivants :

  |   |   |   |    |
  | - | - | - | -  |
  |ACS                   |CA                             |HRP                            |OboService|
  |ACSBlob               |CacheService                   |IBC                            |OEM|
  |ACSDownloadService    |Calcul                        |InfraServiceController         |OnboardRP|
  |ACSFabric             |IPC                            |KeyVaultAdminResourceProvider  |PXE|
  |ACSFrontEnd           |CRP                            |KeyVaultControlPlane           |QueryServiceCoordinator|
  |ACSMetrics            |DeploymentMachine              |KeyVaultDataPlane              |QueryServiceWorker|
  |ACSMigrationService   |DiskRP                         |KeyVaultInternalControlPlane   |SeedRing|
  |ACSMonitoringService  |Domaine                         |KeyVaultInternalDataPlane      |SeedRingServices|
  |ACSSettingsService    |ECE                            |KeyVaultNamingService          |SLB|
  |ACSTableMaster        |EventAdminRP                   |MDM                            |SQL|
  |ACSTableServer        |EventRP                        |MetricsAdminRP                 |SRP   |
  |ACSWac                |ExternalDNS                    |MetricsRP                      |Stockage|
  |ADFS                  |FabricRing                     |MetricsServer                  |StorageController   |
  |ApplicationController |FabricRingServices             |MetricsStoreService            |URP   |
  |ASAppGateway          |FirstTierAggregationService    |MonAdminRP                     |UsageBridge|
  |AzureBridge           |FRP                            |MonRP                          |VirtualMachines   |
  |AzureMonitor          |Passerelle                        |NC                             |WAS|
  |BareMetal             |HealthMonitoring               |NonPrivilegedAppGateway        |WASPUBLIC|
  |BRP                   |HintingServiceV2               |NRP                            |   |
  |   |   |   |    |

### <a name="additional-considerations"></a>Considérations supplémentaires

* L’exécution de cette commande peut prendre un certain temps, en fonction des données du ou des rôles collectées par les journaux d’activité. Les facteurs qui entrent en compte sont la durée spécifiée pour la collecte de journaux et le nombre de nœuds de l’environnement Azure Stack.
* Une fois la collecte de journaux en cours, vérifiez le dossier créé dans le paramètre **OutputSharePath** spécifié dans la commande.
* Les journaux d’activité de chaque rôle se trouvent à l’intérieur de fichiers zip individuels. Selon leur taille, les journaux d’activité d’un rôle qui sont collectés peuvent être divisés en plusieurs fichiers zip. Pour ce type de rôle, si vous souhaitez disposer de tous les fichiers journaux décompressés dans un dossier unique, utilisez un outil qui peut effectuer cette opération en blocs. Sélectionnez tous les fichiers compressés du rôle, puis sélectionnez **Extract Here**. Cette opération permet de décompresser tous les fichiers journaux de ce rôle, au sein d’un dossier fusionné unique.
* Un fichier nommé **Get-AzureStackLog_Output.log** est également créé dans le dossier qui contient les fichiers journaux compressés. Ce fichier est un journal de la sortie de la commande, qui peut être utilisé pour résoudre des problèmes lors de la collection de journaux. Parfois, le fichier journal inclut des entrées `PS>TerminatingError` qui peuvent être ignorées, sauf si des fichiers journaux attendus sont manquants après l’exécution de la collecte des journaux.
* Pour examiner un échec spécifique, vous aurez peut-être besoin des journaux d’activité de plusieurs composants.

  * Les journaux système et des événements pour toutes les machines virtuelles de l’infrastructure sont collectés dans le rôle **VirtualMachines**.
  * Les journaux système et des événements pour tous les hôtes sont collectés dans le rôle **BareMetal**.
  * Les journaux des événements Hyper-V et de cluster de basculement sont collectés dans le rôle **Storage**.
  * Les journaux d’activité ACS sont collectés dans les rôles **Storage** et **ACS**.

> [!NOTE]
> Nous appliquons des limites de taille et d’âge aux journaux d’activité collectés, car il est essentiel de garantir une utilisation efficace de votre espace de stockage afin de s’assurer que vous ne vous retrouverez pas submergé de journaux d’activité. Toutefois, lorsque vous diagnostiquez un problème, vous avez parfois besoin de journaux d’activité qui n’existent plus, à cause de ces limites. Par conséquent, nous vous **recommandons vivement** de décharger vos journaux dans un espace de stockage externe (un compte de stockage dans Azure, un dispositif de stockage local supplémentaire, etc.) toutes les 8 à 12 heures, et de les conserver pendant 1 à 3 mois, en fonction de vos besoins. Vérifiez également que l’emplacement de stockage est chiffré.

### <a name="invoke-azurestackondemandlog"></a>Invoke-AzureStackOnDemandLog

Vous pouvez utiliser l’applet de commande **Invoke-AzureStackOnDemandLog** et générer des journaux à la demande pour certains rôles (voir la liste à la fin de cette section). Les journaux générés par cette applet de commande ne sont pas présents par défaut dans le lot de journaux que vous recevez lorsque vous exécutez l’applet de commande **Get-AzureStackLog**. Par ailleurs, il est recommandé de collecter ces journaux uniquement lorsque l’équipe du support Microsoft en fait la demande.

Actuellement, vous pouvez utiliser le paramètre `-FilterByRole` pour filtrer la collecte de journaux en fonction des rôles suivants :

* OEM
* NC
* SLB
* Passerelle

#### <a name="example-of-collecting-on-demand-logs"></a>Exemple de collecte de journaux à la demande

```powershell
$ipAddress = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.

$password = ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object -TypeName System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $password)

$shareCred = Get-Credential

$session = New-PSSession -ComputerName $ipAddress -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2) # Provide the time that includes the period for your issue

Invoke-Command -Session $session {
   Invoke-AzureStackOnDemandLog -Generate -FilterByRole "<on-demand role name>" # Provide the supported on-demand role name e.g. OEM, NC, SLB, Gateway
   Get-AzureStackLog -OutputSharePath "<external share address>" -OutputShareCredential $using:shareCred -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate
}

if ($session) {
   Remove-PSSession -Session $session
}
```

