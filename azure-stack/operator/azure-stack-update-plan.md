---
title: Planifier une mise à jour Azure Stack | Microsoft Docs
description: Apprenez à planifier une mise à jour d’Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2019
ms.author: mabrigg
ms.lastreviewed: 08/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: 60515f257dc8ab04e351d1353919a749e9cf4224
ms.sourcegitcommit: 79ead51be63c372b23b7fca6ffeaf95fd44de786
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71687960"
---
# <a name="plan-for-an-azure-stack-update"></a>Planifier une mise à jour d’Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Vous pouvez préparer votre Azure Stack pour que le processus de mise à jour se déroule aussi facilement que possible, afin qu’il ait un impact minimal sur vos utilisateurs. Dans cet article, passez en revue les étapes pour informer les utilisateurs des interruptions de service possibles et les étapes à suivre pour préparer une mise à jour.

## <a name="notify-your-users-of-maintenance-operations"></a>Notifiez vos utilisateurs de toute opération de maintenance

Informez les utilisateurs de toutes les opérations de maintenance, et de planifier dans la mesure du possible les fenêtres de maintenance ordinaire pendant les heures creuses. Les opérations de maintenance peuvent affecter les opérations de portail et les charges de travail de locataire.

## <a name="prepare-for-an-azure-stack-update"></a>Se préparer à la mise à jour d’Azure Stack

Vous pouvez vous préparer à la mise à jour en vous assurant que vous avez appliqué tous les correctifs logiciels, les correctifs de sécurité et les mises à jour OEM, validé l’intégrité de votre instance Azure Stack, vérifié la capacité disponible et passé en revue le package de mise à jour.

1. Passer en revue les problèmes connus. Pour obtenir des instructions, consultez [Problèmes connus d’Azure Stack](https://docs.microsoft.com/azure-stack/operator/release-notes).

2. Passer en revue les mises à jour de sécurité. Pour une liste de mises à jour, consultez [Mises à jour de sécurité d’Azure Stack](https://docs.microsoft.com/azure-stack/operator/release-notes-security-updates).

3. Avant de démarrer l’installation de cette mise à jour, exécutez [Test-AzureStack](https://docs.microsoft.com/azure-stack/operator/azure-stack-diagnostic-test) pour valider l’état de votre Azure Stack et résoudre les éventuels problèmes opérationnels détectés, y compris tous les avertissements et les échecs. Examinez aussi les alertes actives et résolvez toutes celles qui nécessitent une action.

    1. Ouvrez une session PEP (Privileged Endpoint Session) à partir d’une machine ayant accès aux machines virtuelles ERCS Azure Stack via le réseau. Pour obtenir des instructions sur l’utilisation du point de terminaison privilégié, voir [Utilisation du point de terminaison privilégié dans Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint).

    2. Test-AzureStack -Group UpdateReadiness

    3. Examinez la sortie et corrigez les erreurs liées à l’intégrité. Pour plus d’informations, consultez [Exécuter un test de validation d’Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-diagnostic-test).

4. Résoudre les problèmes. Vous pouvez consulter la rubrique Surveiller l’intégrité et les alertes pour obtenir des conseils sur le problème signalé. Pour plus d’instructions, voir [Surveiller l’intégrité et les alertes dans Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-monitor-health).

5. Appliquer les derniers correctifs. Pour obtenir la liste des derniers correctifs, consultez la section Correctifs logiciels des notes de publication. Répétez les étapes 3 et 4 après avoir appliqué le correctif logiciel le plus récent.

6. Vérifiez que votre package OEM est compatible avec la version d’Azure Stack que vous mettez à jour. Si votre package OEM n’est pas compatible avec la version d’Azure Stack que vous mettez à jour, vous devrez effectuer une mise à jour du package OEM avant d’exécuter une mise à jour Azure Stack. Pour plus d’instructions, consultez « Appliquer des mises à jour de fabricants d’ordinateurs (OEM) à Azure Stack. » Répétez les étapes 3 et 4 après avoir appliqué la mise à jour du package OEM.

7. Exécutez l’outil de planification de la capacité. Pour obtenir une vue d’ensemble et des instructions sur l'utilisation de l’outil, consultez [Vue d’ensemble de la planification de la capacité pour Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-capacity-planning-overview).

8. Vérifiez le package de mise à jour. Lors de la planification de votre fenêtre de maintenance, il est important d’examiner le type spécifique du package de mise à jour proposé par Microsoft, comme indiqué dans la note de publication.

## <a name="next-steps"></a>Étapes suivantes

[Appliquez la mise à jour d’Azure Stack](azure-stack-apply-updates.md).
