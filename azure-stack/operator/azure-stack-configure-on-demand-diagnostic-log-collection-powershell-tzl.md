---
title: Envoyer des journaux de diagnostic Azure Stack Hub à Azure à l’aide du point de terminaison privilégié
description: Découvrez comment envoyer des journaux de diagnostic Azure Stack Hub à Azure à l’aide du point de terminaison privilégié.
author: justinha
ms.topic: article
ms.date: 03/05/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 03/05/2020
ms.openlocfilehash: ca6240cb4f1e54c5e5fbfda79c46697909612063
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2020
ms.locfileid: "79520560"
---
# <a name="send-azure-stack-hub-diagnostic-logs-to-azure-using-the-privileged-endpoint-pep"></a>Envoyer des journaux de diagnostic Azure Stack Hub à Azure à l’aide du point de terminaison privilégié

Vous n’avez pas besoin de fournir un emplacement de stockage cible lors de l’utilisation de cette méthode. Si les paramètres **fromDate** et **toDate** ne sont pas fournis, la plage par défaut est de 4 heures à partir de l’heure d’exécution. 

Si vous le souhaitez, vous pouvez spécifier le paramètre **FilterByRole** ou **FilterByResourceProvider** pour inclure uniquement des journaux de fournisseurs de ressources à valeur ajoutée ou propres à un rôle. 

Voici un exemple de script que vous pouvez exécuter en utilisant le point de terminaison privilégié pour collecter des journaux sur un système intégré. 


```powershell
$ipAddress = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP address. 
 
$password = ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force 
$cred = New-Object -TypeName System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $password) 
 
$session = New-PSSession -ComputerName $ipAddress -ConfigurationName PrivilegedEndpoint -Credential $cred 
 
$fromDate = (Get-Date).AddHours(-8) # Optional. 
$toDate = (Get-Date).AddHours(-2) # Optional. Provide the time that includes the period for your issue 
 
Invoke-Command -Session $session {Send-AzureStackDiagnosticLog -FromDate $using:fromDate -ToDate $using:toDate} 
 
if ($session) { 
    Remove-PSSession -Session $session 
} 
```

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


## <a name="next-steps"></a>Étapes suivantes

[Utiliser le point de terminaison privilégié pour envoyer les journaux de diagnostic Azure Stack Hub](azure-stack-get-azurestacklog.md)
