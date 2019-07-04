---
title: Diagnostics dans Azure Stack
description: Comment collecter des fichiers journaux de diagnostics dans Azure Stack
services: azure-stack
author: justinha
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 05/29/2019
ms.author: justinha
ms.reviewer: adshar
ms.lastreviewed: 11/20/2018
ms.openlocfilehash: 58d06d20da6890474969318b3a7450975848c84a
ms.sourcegitcommit: ad2f2cb4dc8d5cf0c2c37517d5125921cff44cdd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67138882"
---
# <a name="azure-stack-diagnostics-tools"></a>Outils de diagnostics Azure Stack

Azure Stack est une grande collection de composants qui fonctionnent ensemble et interagissent. Tous ces composants génèrent leurs propres journaux d’activité. Cela peut compliquer le diagnostic des problèmes, notamment quand les erreurs proviennent de plusieurs composants Azure Stack en interaction.

Nos outils de diagnostic aident à garantir la simplicité d’utilisation et l’efficacité du mécanisme de collection de journaux. Le diagramme suivant illustre le fonctionnement des outils de collecte de journaux Azure Stack :

![Outils de diagnostic Azure Stack](media/azure-stack-diagnostics/get-azslogs.png)

## <a name="trace-collector"></a>Collecteur de traces

Le collecteur de traces est activé par défaut et s’exécute en continu en arrière-plan pour collecter tous les journaux d’activité de suivi d’événements pour Windows (ETW) à partir des services composants d’Azure Stack. Les journaux d’activité ETW sont stockés dans un partage local commun avec une durée de vie de cinq jours. Une fois cette limite atteinte, les fichiers les plus anciens sont supprimés à mesure que de nouveaux sont créés. La taille maximale autorisée par défaut pour chaque fichier est de 200 Mo. Une vérification de la taille a lieu toutes les deux minutes ; si le fichier actuel a une taille supérieure ou égale à 200 Mo, il est enregistré et un nouveau fichier est généré. Il existe également une limite de 8 Go portant sur la taille totale des fichiers générés par session d’événements.

## <a name="log-collection-tool"></a>Outil de collecte de journaux

Vous pouvez utiliser l’applet de commande PowerShell **Get-AzureStackLog** pour collecter des journaux d’activité à partir de tous les composants dans un environnement Azure Stack. Elle les enregistre dans des fichiers zip à un emplacement défini par l’utilisateur. Si l’équipe de support technique Azure Stack a besoin de vos journaux d’activité pour résoudre un problème, elle peut vous demander d’exécuter cet outil.

> [!CAUTION]
> Ces fichiers journaux peuvent contenir des informations d’identification personnelle. Pensez-y avant de publier des fichiers journaux publiquement.

Voici quelques exemples de types de journaux collectés :

* **Journaux d’activité de déploiement Azure Stack**
* **Journaux des événements Windows**
* **Journaux d’activité Panther**
* **Journaux d’activité de cluster**
* **Journaux de diagnostics de stockage**
* **Journaux d’activité ETW**

Ces fichiers sont collectés et enregistrés dans un partage par le Collecteur de traces. Vous pouvez ensuite utiliser l’applet de commande PowerShell **Get-AzureStackLog** pour les collecter en cas de nécessité.

### <a name="to-run-get-azurestacklog-on-azure-stack-integrated-systems"></a>Pour exécuter Get-AzureStackLog sur des systèmes intégrés Azure Stack

Pour exécuter l’outil de collection de journaux sur un système intégré, vous devez avoir accès au point de terminaison privilégié (PEP). Voici un exemple de script que vous pouvez exécuter à l’aide du point de terminaison privilégié pour collecter des journaux d’activité sur un système intégré :

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.

$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)

$shareCred = Get-Credential

$s = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2)  #provide the time that includes the period for your issue

Invoke-Command -Session $s {    Get-AzureStackLog -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if($s)
{
    Remove-PSSession $s
}
```

### <a name="run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system"></a>Exécuter Get-AzureStackLog sur un système ASDK (Kit de développement Azure Stack)

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
  > Cette procédure est utile quand vous ouvrez une demande de support Microsoft et que vous êtes invité à charger des journaux. Même si vous n’avez pas de partage SMB accessible à partir de votre machine virtuelle ERCS et que celle-ci n’a pas accès à Internet, vous pouvez créer un compte de stockage Blob sur votre système Azure Stack pour y transférer les journaux, et ensuite utiliser votre client pour récupérer ces journaux et les envoyer à Microsoft.  

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
* Les journaux d’activité de chaque rôle se trouvent à l’intérieur de fichiers zip individuels. Selon leur taille, les journaux d’activité d’un rôle qui sont collectés peuvent être divisés en plusieurs fichiers zip. Pour ce type de rôle, si vous souhaitez disposer de tous les fichiers journaux décompressés dans un dossier unique, utilisez un outil qui peut effectuer cette opération en blocs (7zip, par exemple). Sélectionnez tous les fichiers compressés du rôle, puis sélectionnez **Extract Here**. Cette opération permet de décompresser tous les fichiers journaux de ce rôle, au sein d’un dossier fusionné unique.
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
$ip = "<IP address of the PEP VM>" # You can also use the machine name instead of IP here.

$pwd= ConvertTo-SecureString "<cloud admin password>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<domain name>\CloudAdmin", $pwd)

$shareCred = Get-Credential

$s = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2)  #provide the time that includes the period for your issue

Invoke-Command -Session $s
{
   Invoke-AzureStackOnDemandLog -Generate -FilterByRole "<on-demand role name>" #Provide the supported on-demand role name : OEM, NC, SLB , Gateway
   Get-AzureStackLog -OutputSharePath "<external share address>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate

}

if($s)
{
   Remove-PSSession $s
}
```

## <a name="next-steps"></a>Étapes suivantes

[Dépannage de Microsoft Azure Stack](azure-stack-troubleshooting.md)
