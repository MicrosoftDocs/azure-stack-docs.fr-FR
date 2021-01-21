---
title: Rechercher votre ID cloud
description: Rechercher votre ID cloud dans Azure Stack Hub Aide + support.
author: PatAltimore
ms.topic: article
ms.date: 10/08/2019
ms.author: patricka
ms.reviewer: shisab
ms.lastreviewed: 10/08/2019
ms.openlocfilehash: 6da70761e8a37967d3debdff28b67447596c632a
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98255960"
---
# <a name="find-your-cloud-id"></a>Rechercher votre ID cloud

Cette rubrique explique comment obtenir votre ID de cloud à l’aide du portail Administrateur ou du point de terminaison privilégié (PEP). L’ID de cloud est l’ID unique pour le suivi des données de support chargées à partir d’une unité d’échelle spécifique. Lorsque les journaux de diagnostic sont chargés à des fins d’analyse de support, l’ID de cloud indique la façon dont les journaux sont associés à cette unité d’échelle.

## <a name="use-the-administrator-portal"></a>Utiliser le portail d’administration

1. Ouvrez le portail administrateur. 
1. Sélectionnez **Region management** (Gestion des régions).

   ![Capture d’écran du tableau de bord](./media/azure-stack-automatic-log-collection/dashboard.png)

1. Sélectionnez **Properties** (Propriétés) et copiez l’**ID de cloud de l’empreinte**.

   ![Capture d’écran des propriétés d’une région avec mise en évidence de l’ID cloud de l’empreinte](media/azure-stack-automatic-log-collection/region-properties-blade-with-stamp-cloud-id.png)


## <a name="use-the-privileged-endpoint"></a>Utiliser le point de terminaison privilégié

1. Ouvrez une session PowerShell avec élévation de privilèges et exécutez le script suivant. Remplacez l’adresse IP de la machine virtuelle PEP et les informations d’identification d’administrateur de cloud en fonction des besoins de votre environnement. 

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

* [Send logs proactively (Envoyer les journaux de manière proactive)](./diagnostic-log-collection.md#send-logs-proactively)
* [Envoyer des journaux maintenant](./diagnostic-log-collection.md#send-logs-now)