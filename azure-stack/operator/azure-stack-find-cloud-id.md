---
title: Rechercher votre ID cloud
description: Rechercher votre ID cloud dans Azure Stack Hub Aide + support.
author: justinha
ms.topic: article
ms.date: 10/08/2019
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 10/08/2019
ms.openlocfilehash: 647f731d1d82c41d26142ff88e56b8ec0717f5ef
ms.sourcegitcommit: dbc6739584aa407b26e4ad4921d967b7b608de38
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2020
ms.locfileid: "90038809"
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

* [Send logs proactively (Envoyer les journaux de manière proactive)](./azure-stack-configure-automatic-diagnostic-log-collection.md?view=azs-2002)
* [Envoyer des journaux maintenant](./azure-stack-configure-on-demand-diagnostic-log-collection-portal.md?view=azs-2002)
