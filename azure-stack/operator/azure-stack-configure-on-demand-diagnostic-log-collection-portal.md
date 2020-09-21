---
title: Envoyer les journaux de diagnostic Azure Stack Hub maintenant
description: Découvrez comment collecter les journaux de diagnostic à la demande dans Azure Stack Hub en utilisant le portail administrateur ou un script PowerShell.
author: myoungerman
ms.topic: article
ms.date: 08/24/2020
ms.author: v-myoung
ms.reviewer: shisab
ms.lastreviewed: 08/24/2020
ms.openlocfilehash: 3d4725a832aa1ba88c0ecc5e2b435696cba3bbcd
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90573154"
---
# <a name="send-azure-stack-hub-diagnostic-logs-now"></a>Envoyer les journaux de diagnostic Azure Stack Hub maintenant

::: moniker range=">= azs-2002"

Avant de demander une assistance, les opérateurs Azure Stack peuvent envoyer des journaux de diagnostic à la demande au support Microsoft en utilisant le portail administrateur ou PowerShell. Si Azure Stack Hub est connecté à Azure, l’option **Send logs now** (Envoyer les journaux maintenant) dans le portail administrateur est recommandée, car il s’agit de la méthode la plus simple pour envoyer les journaux directement à Microsoft. Si le portail n’est pas disponible, les opérateurs doivent alors [envoyer désormais les journaux avec Send-AzureStackDiagnosticLog](./azure-stack-configure-on-demand-diagnostic-log-collection-powershell.md?view=azs-2002). 

Si vous êtes déconnecté d’Internet ou souhaitez uniquement enregistrer les journaux localement, utilisez la méthode [Get-AzureStackLog](azure-stack-get-azurestacklog.md) pour envoyer les journaux. L’organigramme suivant montre l’option à utiliser pour l’envoi des journaux de diagnostic dans chaque cas. 

![Organigramme montrant comment envoyer des journaux à Microsoft](media/azure-stack-help-and-support/send-logs-now-flowchart.png)

>[!NOTE]
>En guise d’alternative à la collecte de journaux à la demande, vous pouvez simplifier le processus de résolution des problèmes en [collectant les journaux de diagnostic de façon proactive](./azure-stack-configure-automatic-diagnostic-log-collection.md?view=azs-2002). Si les conditions d’intégrité du système doivent être examinées, les journaux sont automatiquement chargés à des fins d’analyse avant l’ouverture d’un cas auprès du support Microsoft. Si la collecte proactive des journaux est activée, **Aide et support** s’affiche quand la collecte des journaux est en cours. Si vous cliquez sur **Send logs now** (Envoyer les journaux maintenant) pour collecter les journaux à partir d’une heure précise alors que la collecte proactive des journaux est en cours, la collecte à la demande commence après la fin de la collecte proactive des journaux.

Spécifiez l’heure de début et de fin de la collecte des journaux, puis cliquez sur **Collect and Upload** (Collecter et charger). 

![Capture d’écran de l’option permettant d’envoyer des journaux maintenant](media/azure-stack-help-and-support/send-logs-now.png)


::: moniker-end
::: moniker range=">= azs-2005"
## <a name="save-logs-locally"></a>Enregistrer les journaux localement

Vous pouvez enregistrer des journaux dans un partage SMB local lorsque Azure Stack Hub est déconnecté d’Azure. Dans le panneau **Paramètres**, entrez le chemin d’accès et un nom d’utilisateur et un mot de passe avec l’autorisation d’écrire sur le partage. Au cours d’un cas de support, le Support Microsoft fournit des étapes détaillées sur la façon dont ces journaux locaux doivent être transférés. Si le portail d’administration n’est pas disponible, vous pouvez utiliser la cmdlet [Get-AzureStackLog](azure-stack-get-azurestacklog.md) pour enregistrer les journaux localement.

![Capture d’écran des options de collecte des journaux de diagnostic](media/azure-stack-help-and-support/save-logs-locally.png)

::: moniker-end
::: moniker range="<= azs-1910"
## <a name="use-help-and-support-to-collect-diagnostic-logs-on-demand"></a>Utiliser Aide et support pour collecter les journaux de diagnostic à la demande

Pour résoudre un problème, le support Microsoft peut demander à un opérateur Azure Stack Hub de collecter les journaux de diagnostic à la demande pour une période donnée au cours de la semaine précédente. Dans ce cas, le support Microsoft fournira à l’opérateur une URL SAS pour le chargement de la collecte. 
Procédez comme suit pour configurer la collecte des journaux à la demande à l’aide de l’URL SAS fournie par le support Microsoft :

1. Ouvrez **Aide + Support > Collecte de journaux > Collecter les journaux maintenant**. 
1. Choisissez une fenêtre de 1à 4 heures sur les sept derniers jours. 
1. Choisissez le fuseau horaire local.
1. Entrez l’URL SAS fournie par le support Microsoft.

   ![Capture d’écran de la collecte de journaux à la demande](media/azure-stack-automatic-log-collection/collect-logs-now.png)

>[!NOTE]
>Si la collecte automatique des journaux de diagnostic est activée, **Aide et support** s’affiche lorsque la collecte des journaux est en cours. Si vous cliquez sur **Collecter les journaux maintenant** pour collecter les journaux à partir d’une heure précise alors que la collecte automatique des journaux est en cours, la collecte à la demande commence après la fin de la collecte automatique des journaux. 


::: moniker-end


## <a name="next-steps"></a>Étapes suivantes

[Utiliser le point de terminaison privilégié pour envoyer les journaux de diagnostic Azure Stack Hub](./azure-stack-configure-on-demand-diagnostic-log-collection-powershell.md?view=azs-2002)
