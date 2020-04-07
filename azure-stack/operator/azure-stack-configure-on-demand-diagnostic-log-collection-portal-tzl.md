---
title: Envoyer les journaux de diagnostic Azure Stack Hub maintenant
description: Découvrez comment collecter les journaux de diagnostic à la demande dans Azure Stack Hub en utilisant le portail administrateur ou un script PowerShell.
author: justinha
ms.topic: article
ms.date: 03/30/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 03/30/2020
ms.openlocfilehash: 2a46c031be648eedf42e204496ed2a123c926dd9
ms.sourcegitcommit: b65952127f39c263b162aad990e4d5b265570a7f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80402717"
---
# <a name="send-azure-stack-hub-diagnostic-logs-now"></a>Envoyer les journaux de diagnostic Azure Stack Hub maintenant

Avant de demander une assistance, les opérateurs Azure Stack peuvent envoyer des journaux de diagnostic à la demande aux services de support technique Microsoft (CSS) en utilisant le portail administrateur ou PowerShell. Si Azure Stack Hub est connecté à Azure, l’option **Send logs now** (Envoyer les journaux maintenant) dans le portail administrateur est recommandée, car il s’agit de la méthode la plus simple pour envoyer les journaux directement à Microsoft. Si le portail n’est pas disponible, les opérateurs doivent alors [envoyer désormais les journaux avec Send-AzureStackDiagnosticLog](azure-stack-configure-on-demand-diagnostic-log-collection-powershell-tzl.md). 

Si vous êtes déconnecté d’Internet ou souhaitez uniquement enregistrer les journaux localement, utilisez la méthode [Get-AzureStackLog](azure-stack-get-azurestacklog.md) pour envoyer les journaux. L’organigramme suivant montre l’option à utiliser pour l’envoi des journaux de diagnostic dans chaque cas. 

![Organigramme montrant comment envoyer des journaux à Microsoft](media/azure-stack-help-and-support/send-logs-now-flowchart.png)

>[!NOTE]
>En guise d’alternative à la collecte de journaux à la demande, vous pouvez simplifier le processus de résolution des problèmes en [collectant les journaux de diagnostic de façon proactive](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md). Si les conditions d’intégrité du système doivent être examinées, les journaux sont automatiquement chargés à des fins d’analyse avant l’ouverture d’un cas de support auprès des services CSS. Si la collecte proactive des journaux est activée, **Aide et support** s’affiche quand la collecte des journaux est en cours. Si vous cliquez sur **Send logs now** (Envoyer les journaux maintenant) pour collecter les journaux à partir d’une heure précise alors que la collecte proactive des journaux est en cours, la collecte à la demande commence après la fin de la collecte proactive des journaux.

Spécifiez l’heure de début et de fin de la collecte des journaux, puis cliquez sur **Collect and Upload** (Collecter et charger). 

![Capture d’écran de l’option permettant d’envoyer des journaux maintenant](media/azure-stack-help-and-support/send-logs-now.png)

## <a name="next-steps"></a>Étapes suivantes

[Utiliser le point de terminaison privilégié pour envoyer les journaux de diagnostic Azure Stack Hub](azure-stack-configure-on-demand-diagnostic-log-collection-powershell-tzl.md)
