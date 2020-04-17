---
title: Rechercher votre ID cloud
description: Rechercher votre ID cloud dans Azure Stack Hub Aide + support.
author: justinha
ms.topic: article
ms.date: 10/08/2019
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 10/08/2019
ms.openlocfilehash: e0045ae6bf76b6b4e5973f65c6c0a5f758e0d46b
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "79520620"
---
# <a name="find-your-cloud-id"></a>Rechercher votre ID cloud

Cette rubrique explique comment vous procurer l’ID cloud à l’aide du portail administrateur ou du point de terminaison privilégié (PEP). 

## <a name="use-the-administrator-portal"></a>Utiliser le portail d’administration

1. Ouvrez le portail administrateur. 
1. Cliquez sur **Region management** (Gestion des régions).

   ![Capture d’écran du tableau de bord](./media/azure-stack-automatic-log-collection/dashboard.png)

1. Cliquez sur **Properties** (Propriétés) et copiez l’**ID cloud de l’empreinte**.

   ![Capture d’écran des propriétés d’une région avec mise en évidence de l’ID cloud de l’empreinte](media/azure-stack-automatic-log-collection/region-properties-blade-with-stamp-cloud-id.png)


## <a name="use-the-privileged-endpoint"></a>Utiliser le point de terminaison privilégié

1. Ouvrez une session PowerShell avec élévation de privilèges et exécutez le script suivant. Remplacez l’adresse IP de la machine virtuelle PEP et les informations d’identification d’administrateur cloud en fonction des besoins de votre environnement. 

   ```powershell
   $ipAddress = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.

   $password = ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
   $cred = New-Object -TypeName System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $password)
   $session = New-PSSession -ComputerName $ipAddress -ConfigurationName PrivilegedEndpoint -Credential $cred

   $stampInfo = Invoke-Command -Session $session { Get-AzureStackStampInformation }
   if ($session) {
       Remove-PSSession -Session $session
   }

   $stampInfo.CloudID
   ```

## <a name="next-steps"></a>Étapes suivantes

* [Send logs proactively](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md) (Envoyer les journaux de manière proactive)
* [Send logs now](azure-stack-configure-on-demand-diagnostic-log-collection-portal-tzl.md) (Envoyer les journaux maintenant)






