---
title: Collecter des journaux de diagnostic via le point de terminaison privilégié (PEP)
description: Découvrez comment collecter les journaux de diagnostic à la demande dans Azure Stack Hub en utilisant le portail d’administration ou un script PowerShell.
author: justinha
ms.custom: conteperfq4
ms.topic: article
ms.date: 09/02/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 09/02/2020
ms.openlocfilehash: 95e12f2f90cd7e33fb3a2cc3a5f016f35ac0e54f
ms.sourcegitcommit: a1e2003fb9c6dacdc76f97614ff5a26a5b197b49
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91623215"
---
# <a name="send-azure-stack-hub-diagnostic-logs-by-using-the-privileged-endpoint-pep"></a>Envoyer des journaux de diagnostic Azure Stack Hub à l’aide du point de terminaison privilégié

<!--how do you look up the PEP IP address. You look up the azurestackstampinfo.json--->

Pour exécuter Get-AzureStackLog sur un système intégré, vous devez avoir accès au point de terminaison privilégié (PEP). Voici un exemple de script que vous pouvez exécuter en utilisant le point de terminaison privilégié pour collecter des journaux. Si vous annulez une collecte de journaux en cours d’exécution pour en démarrer une nouvelle, attendez 5 minutes avant de démarrer la nouvelle collecte de journaux et entrez `Remove-PSSession -Session $session`.


```powershell
$ipAddress = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.

$password = ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object -TypeName System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $password)

$shareCred = Get-Credential

$session = New-PSSession -ComputerName $ipAddress -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2) # Provide the time that includes the period for your issue

Invoke-Command -Session $session { Get-AzureStackLog -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if ($session) {
    Remove-PSSession -Session $session
}
```

### <a name="examples"></a>Exemples

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

* Collectez les journaux des déploiements de locataires qui exécutent les clusters Kubernetes auto-managés (moteur AKS) sur Azure Stack. Les journaux Kubernetes doivent être stockés dans un compte de stockage de locataire dans un format qui permettra de leur appliquer l’intervalle de temps de la collection. 

  ```powershell
  Get-AzureStackLog -OutputPath <Path> -InputSasUri "<Blob Service Sas URI>" -FromDate "<Beginning of the time range>" -ToDate "<End of the time range>"
  ```

  Par exemple :

  ```powershell
  Get-AzureStackLog -OutputPath C:\KubernetesLogs -InputSasUri "https://<storageAccountName>.blob.core.windows.net/<ContainerName><SAS token>" -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2) 
  ```

* Collecter les journaux pour les fournisseurs de ressources à valeur ajoutée. La syntaxe générale est la suivante :
 
  ```powershell
  Get-AzureStackLog -FilterByResourceProvider <<value-add RP name>>
  ```
 
  Pour collecter les journaux pour IoT Hub : 

  ```powershell
  Get-AzureStackLog -FilterByResourceProvider iothubServiceHealth
  ```
 
  Pour collecter les journaux pour Event Hubs :

  ```powershell
  Get-AzureStackLog -FilterByResourceProvider eventhub
  ```
 
  Pour collecter les journaux pour Azure Stack Edge :

  ```powershell
  Get-AzureStackLog -FilterByResourceProvide databoxedge
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
  > Cette procédure est utile pour le chargement des journaux. Même si vous n’avez pas de partage SMB accessible ou d’accès à Internet, vous pouvez créer un compte de stockage d’objets blob sur votre système Azure Stack Hub pour y transférer les journaux, puis utiliser votre client pour récupérer ces journaux.  

  Pour générer le jeton SAS du compte de stockage, les autorisations suivantes sont nécessaires :

  * Accès au service Stockage Blob.
  * Accès au type de ressource Conteneur.

  Pour générer une valeur d’URI de signature d’accès partagé à utiliser pour le paramètre `-OutputSasUri`, effectuez ces étapes :

  1. Créez un compte de stockage en suivant les étapes [de cet article](/azure/storage/common/storage-quickstart-create-account).
  2. Ouvrez une instance de l’Explorateur Stockage Azure.
  3. Connectez-vous au compte de stockage créé à l’étape 1.
  4. Accédez à **Conteneurs d’objets Blob** dans **Services de stockage**.
  5. Sélectionnez **Créer un conteneur**.
  6. Cliquez avec le bouton droit sur le nouveau conteneur, puis cliquez sur **Obtenir une signature d’accès partagé**.
  7. Sélectionnez une **Heure de début** et une **Heure de fin** valides, selon vos besoins.
  8. Pour les autorisations nécessaires, sélectionnez **Lecture**, **Écriture** et **Liste**.
  9. Sélectionnez **Create** (Créer).
  10. Vous obtenez une signature d’accès partagé. Copiez la partie URL et spécifiez-la pour le paramètre `-OutputSasUri`.

### <a name="parameter-considerations"></a>Considérations relatives aux paramètres

* Les paramètres **OutputSharePath** et **OutputShareCredential** sont utilisés pour stocker les journaux d’activité dans un emplacement spécifié par l’utilisateur.
* Vous pouvez utiliser les paramètres **FromDate** et **ToDate** pour collecter des journaux d’activité pour une période donnée. Si vous ne spécifiez pas ces paramètres, les journaux sont collectés par défaut pour les quatre dernières heures.
* Utilisez le paramètre **FilterByNode** pour filtrer les journaux d’activité par nom d’ordinateur. Par exemple :

    ```powershell
    Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByNode azs-xrp01
    ```

* Utilisez le paramètre **FilterByLogType** pour filtrer les journaux d’activité par type. Vous pouvez choisir de filtrer par fichier, par partage ou par événement Windows. Par exemple :

    ```powershell
    Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByLogType File
    ```

* Vous pouvez utiliser le paramètre **TimeOutInMinutes** pour définir le délai d’expiration pour la collecte des journaux. Il est défini par défaut sur 150 (2,5 heures).
* La collecte de journaux de fichiers de vidage est désactivée par défaut. Pour l’activer, utilisez le paramètre booléen **IncludeDumpFile**.
* Actuellement, vous pouvez utiliser le paramètre **FilterByRole** pour filtrer la collecte de journaux en fonction des rôles suivants :

:::row:::
   :::column span="":::

      ACS

      ACSBlob

      ACSDownloadService

      ACSFabric

      ACSFrontEnd

      ACSMetrics

      ACSMigrationService

      ACSMonitoringService

      ACSSettingsService

      ACSTableMaster

      ACSTableServer

      ACSWac

      ADFS

      ApplicationController

      ASAppGateway

      AzureBridge

      AzureMonitor

      BareMetal

      BRP

      CA

      CacheService

      Calcul

      IPC

      CRP

      DeploymentMachine

      DiskRP

      Domain

   :::column-end:::
   :::column span="":::

      ECE

      EventAdminRP

      EventRP

      ExternalDNS

      FabricRing

      FabricRingServices

      FirstTierAggregationService

      FRP

      Passerelle

      HealthMonitoring

      HintingServiceV2

      HRP

      IBC

      InfraServiceController

      KeyVaultAdminResourceProvider

      KeyVaultControlPlane

      KeyVaultDataPlane

      KeyVaultInternalControlPlane

      KeyVaultInternalDataPlane

      KeyVaultNamingService

      GESTION DES APPAREILS MOBILES

      MetricsAdminRP

      MetricsRP

      MetricsServer

      MetricsStoreService

      MonAdminRP

      MonRP

   :::column-end:::
   :::column span="":::

      NC

      NonPrivilegedAppGateway

      NRP

      OboService

      OEM

      OnboardRP

      Environnement PXE

      QueryServiceCoordinator

      QueryServiceWorker

      SeedRing

      SeedRingServices

      SLB

      SQL

      SRP

      Stockage

      StorageController

      URP

      SupportBridgeController

      SupportRing

      SupportRingServices

      SupportBridgeRP

      UsageBridge

      VirtualMachines

      WAS

      WASPUBLIC
   
   :::column-end:::
:::row-end:::

### <a name="additional-considerations-on-diagnostic-logs"></a>Considérations supplémentaires sur les journaux de diagnostic

* L’exécution de cette commande peut prendre un certain temps, en fonction des données du ou des rôles collectées par les journaux d’activité. Des facteurs déterminants sont aussi la durée spécifiée pour la collecte de journaux et le nombre de nœuds de l’environnement Azure Stack Hub.
* Une fois la collecte de journaux en cours, vérifiez le dossier créé dans le paramètre **OutputSharePath** spécifié dans la commande.
* Les journaux d’activité de chaque rôle se trouvent à l’intérieur de fichiers zip individuels. Selon leur taille, les journaux d’activité d’un rôle qui sont collectés peuvent être divisés en plusieurs fichiers zip. Pour ce type de rôle, si vous voulez disposer de tous les fichiers journaux décompressés dans un même dossier, utilisez un outil qui peut effectuer cette opération en bloc. Sélectionnez tous les fichiers compressés du rôle, puis sélectionnez **Extraire ici**. Tous les fichiers journaux de ce rôle sont décompressés dans un même dossier fusionné.
* Un fichier nommé **Get-AzureStackLog_Output.log** est également créé dans le dossier qui contient les fichiers journaux compressés. Ce fichier est un journal de la sortie de la commande, qui peut être utilisé pour résoudre des problèmes lors de la collection de journaux. Parfois, le fichier journal inclut des entrées `PS>TerminatingError` qui peuvent être ignorées, sauf si des fichiers journaux attendus sont manquants après l’exécution de la collecte des journaux.
* Pour examiner un échec spécifique, vous aurez peut-être besoin des journaux d’activité de plusieurs composants.

  * Les journaux système et des événements pour toutes les machines virtuelles de l’infrastructure sont collectés dans le rôle **VirtualMachines**.
  * Les journaux système et des événements pour tous les hôtes sont collectés dans le rôle **BareMetal**.
  * Les journaux des événements Hyper-V et de cluster de basculement sont collectés dans le rôle **Storage**.
  * Les journaux d’activité ACS sont collectés dans les rôles **Storage** et **ACS**.

> [!NOTE]
> Des limites de taille et d’âge sont appliquées aux journaux collectés, car il est essentiel de garantir une utilisation efficace de votre espace de stockage et d’éviter d’être submergé par les journaux. Cependant, quand vous diagnostiquez un problème, vous avez parfois besoin de journaux qui n’existent plus en raison de ces limites. Par conséquent, nous vous **recommandons vivement** de décharger vos journaux dans un espace de stockage externe (un compte de stockage dans Azure, un dispositif de stockage local supplémentaire, etc.) toutes les 8 à 12 heures, et de les conserver pendant 1 à 3 mois, en fonction de vos besoins. Vous devez aussi vérifier que l’emplacement de ce stockage est chiffré.

### <a name="invoke-azurestackondemandlog"></a>Invoke-AzureStackOnDemandLog

Vous pouvez utiliser l’applet de commande **Invoke-AzureStackOnDemandLog** et générer des journaux à la demande pour certains rôles (voir la liste à la fin de cette section). Les journaux générés par cette applet de commande ne sont pas présents par défaut dans le lot de journaux que vous recevez quand vous exécutez l’applet de commande **Get-AzureStackLog**. Par ailleurs, il est recommandé de collecter ces journaux seulement quand l’équipe du support Microsoft en fait la demande.

Actuellement, vous pouvez utiliser le paramètre `-FilterByRole` pour filtrer la collecte de journaux en fonction des rôles suivants :

* OEM
* NC
* SLB
* Passerelle

#### <a name="example-of-collecting-on-demand-diagnostic-logs"></a>Exemple de collecte de journaux de diagnostic à la demande

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

### <a name="how-diagnostic-log-collection-using-the-pep-works"></a>Fonctionnement de la collecte des journaux de diagnostic à l’aide du point de terminaison privilégié

Les outils de diagnostic Azure Stack Hub aident à garantir la simplicité et l’efficacité de la collecte des journaux. Le diagramme suivant illustre le fonctionnement des outils de diagnostic :

![Diagramme du workflow des outils de diagnostic Azure Stack Hub](media/azure-stack-diagnostics/get-azslogs.png)


#### <a name="trace-collector"></a>Collecteur de traces

Le collecteur de traces est activé par défaut et s’exécute en continu en arrière-plan pour collecter tous les journaux d’activité de suivi d’événements pour Windows (ETW) à partir des services composants d’Azure Stack Hub. Les journaux de suivi d’événements Windows sont stockés dans un partage local commun, avec une limite de conservation de cinq jours. Une fois cette limite atteinte, les fichiers les plus anciens sont supprimés à mesure que de nouveaux sont créés. Par défaut, la taille maximale autorisée pour chaque fichier est de 200 Mo. Une vérification de la taille se produit toutes les deux minutes ; si le fichier actuel a une taille supérieure ou égale à 200 Mo, il est enregistré et un nouveau fichier est généré. Il existe également une limite de 8 Go portant sur la taille totale des fichiers générés par session d’événements.

#### <a name="get-azurestacklog"></a>Get-AzureStackLog

Vous pouvez utiliser la cmdlet PowerShell Get-AzureStackLog pour collecter des journaux d’activité à partir de tous les composants dans un environnement Azure Stack Hub. Elle les enregistre dans des fichiers zip à un emplacement défini par l’utilisateur. Si l’équipe de support technique Azure Stack Hub a besoin de vos journaux d’activité pour résoudre un problème, elle peut vous demander d’exécuter l’applet de commande Get-AzureStackLog.

> [!CAUTION]
> Ces fichiers journaux peuvent contenir des informations d’identification personnelle. Pensez-y avant de publier des fichiers journaux publiquement.

Voici quelques exemples de types de journaux collectés :

* **Journaux d’activité de déploiement d’Azure Stack Hub**
* **Journaux des événements Windows**
* **Journaux d’activité Panther**
* **Journaux d’activité de cluster**
* **Journaux de diagnostics de stockage**
* **Journaux d’activité ETW**

Ces fichiers sont collectés et enregistrés dans un partage par le Collecteur de traces. Vous pouvez ensuite utiliser la cmdlet Get-AzureStackLog pour les collecter en cas de nécessité.

