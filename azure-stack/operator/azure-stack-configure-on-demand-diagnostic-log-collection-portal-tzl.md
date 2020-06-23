---
title: Envoyer les journaux de diagnostic Azure Stack Hub maintenant
description: Découvrez comment collecter les journaux de diagnostic à la demande dans Azure Stack Hub en utilisant le portail administrateur ou un script PowerShell.
author: justinha
ms.topic: article
ms.date: 03/30/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 03/30/2020
ms.openlocfilehash: 2c741440deb92be81497b34a7e485d4628501100
ms.sourcegitcommit: 5f4f0ee043ff994efaad44129ce49be43c64d5dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/16/2020
ms.locfileid: "84819405"
---
# <a name="send-azure-stack-hub-diagnostic-logs-now"></a>Envoyer les journaux de diagnostic Azure Stack Hub maintenant

::: moniker range=">= azs-2002"

Avant de demander une assistance, les opérateurs Azure Stack peuvent envoyer des journaux de diagnostic à la demande au support Microsoft en utilisant le portail administrateur ou PowerShell. Si Azure Stack Hub est connecté à Azure, l’option **Send logs now** (Envoyer les journaux maintenant) dans le portail administrateur est recommandée, car il s’agit de la méthode la plus simple pour envoyer les journaux directement à Microsoft. Si le portail n’est pas disponible, les opérateurs doivent alors [envoyer désormais les journaux avec Send-AzureStackDiagnosticLog](azure-stack-configure-on-demand-diagnostic-log-collection-powershell-tzl.md). 

Si vous êtes déconnecté d’Internet ou souhaitez uniquement enregistrer les journaux localement, utilisez la méthode [Get-AzureStackLog](azure-stack-get-azurestacklog.md) pour envoyer les journaux. L’organigramme suivant montre l’option à utiliser pour l’envoi des journaux de diagnostic dans chaque cas. 

![Organigramme montrant comment envoyer des journaux à Microsoft](media/azure-stack-help-and-support/send-logs-now-flowchart.png)

>[!NOTE]
>En guise d’alternative à la collecte de journaux à la demande, vous pouvez simplifier le processus de résolution des problèmes en [collectant les journaux de diagnostic de façon proactive](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md). Si les conditions d’intégrité du système doivent être examinées, les journaux sont automatiquement chargés à des fins d’analyse avant l’ouverture d’un cas auprès du support Microsoft. Si la collecte proactive des journaux est activée, **Aide et support** s’affiche quand la collecte des journaux est en cours. Si vous cliquez sur **Send logs now** (Envoyer les journaux maintenant) pour collecter les journaux à partir d’une heure précise alors que la collecte proactive des journaux est en cours, la collecte à la demande commence après la fin de la collecte proactive des journaux.

Spécifiez l’heure de début et de fin de la collecte des journaux, puis cliquez sur **Collect and Upload** (Collecter et charger). 

![Capture d’écran de l’option permettant d’envoyer des journaux maintenant](media/azure-stack-help-and-support/send-logs-now.png)


::: moniker-end
::: moniker range="<= azs-1910"
## <a name="use-help-and-support-to-collect-diagnostic-logs-on-demand"></a>Utiliser Aide et support pour collecter les journaux de diagnostic à la demande

Pour résoudre un problème, le support Microsoft peut demander à un opérateur Azure Stack Hub de collecter les journaux de diagnostic à la demande pour une période donnée au cours de la semaine précédente. Dans ce cas, le support Microsoft fournira à l’opérateur une URL SAS pour le chargement de la collecte. 
Procédez comme suit pour configurer la collecte des journaux à la demande à l’aide de l’URL SAS fournie par le support Microsoft :

1. Ouvrez **Aide et support - Vue d’ensemble**, puis cliquez sur **Collecter les journaux maintenant**. 
1. Choisissez une fenêtre de 1à 4 heures sur les sept derniers jours. 
1. Choisissez le fuseau horaire local.
1. Entrez l’URL SAS fournie par le support Microsoft.

   ![Capture d’écran de la collecte de journaux à la demande](media/azure-stack-automatic-log-collection/collect-logs-now.png)

>[!NOTE]
>Si la collecte automatique des journaux de diagnostic est activée, **Aide et support** s’affiche lorsque la collecte des journaux est en cours. Si vous cliquez sur **Collecter les journaux maintenant** pour collecter les journaux à partir d’une heure précise alors que la collecte automatique des journaux est en cours, la collecte à la demande commence après la fin de la collecte automatique des journaux. 


::: moniker-end


## <a name="next-steps"></a>Étapes suivantes

[Utiliser le point de terminaison privilégié pour envoyer les journaux de diagnostic Azure Stack Hub](azure-stack-configure-on-demand-diagnostic-log-collection-powershell-tzl.md)

