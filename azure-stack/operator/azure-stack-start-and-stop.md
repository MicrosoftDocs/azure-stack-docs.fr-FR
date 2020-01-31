---
title: Démarrer et arrêter
titleSuffix: Azure Stack Hub
description: Découvrez comment démarrer et arrêter Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 3f3493d55d524de7c292f0052cc20d26b2fccc28
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76881202"
---
# <a name="start-and-stop-azure-stack-hub"></a>Démarrer et arrêter Azure Stack Hub

Suivez les procédures décrites dans cet article pour arrêter et redémarrer correctement les services Azure Stack Hub. L’*arrêt* met physiquement hors tension l’ensemble de l’environnement Azure Stack Hub. Le *démarrage* met sous tension tous les rôles d’infrastructure et rétablit l’état d’alimentation des ressources de locataire dans lequel elles se trouvaient avant l’arrêt.

## <a name="stop-azure-stack-hub"></a>Arrêter Azure Stack Hub

Pour arrêter Azure Stack Hub, procédez comme suit :

1. Préparez toutes les charges de travail exécutées sur les ressources de locataire de votre environnement Azure Stack Hub pour l’arrêt à venir.

2. Ouvrez une session de point de terminaison privilégié (PEP, Privileged Endpoint Session) à partir d’une machine ayant accès aux machines virtuelles ERCS Azure Stack Hub via le réseau. Pour obtenir des instructions, consultez [Utilisation du point de terminaison privilégié dans Azure Stack Hub](azure-stack-privileged-endpoint.md).

3. À partir de la session PEP, exécutez :

    ```powershell
      Stop-AzureStack
    ```

4. Attendez que tous les nœuds physiques Azure Stack Hub soient hors tension.

> [!Note]
> Vous pouvez vérifier l’état de l’alimentation d’un nœud physique en suivant les instructions du fabricant OEM qui a fourni votre matériel Azure Stack Hub.

## <a name="start-azure-stack-hub"></a>Démarrer Azure Stack Hub

Pour démarrer Azure Stack Hub, procédez comme suit. Suivez cette procédure quelle que soit la manière dont Azure Stack Hub s’est arrêté.

1. Mettez tous les nœuds physiques de votre environnement Azure Stack Hub sous tension. Vérifiez les instructions de mise sous tension des nœuds physiques en suivant les instructions du fabricant OEM qui vous a fourni le matériel Azure Stack Hub.

2. Attendez que les services d’infrastructure Azure Stack Hub démarrent. Le processus de démarrage des services d’infrastructure Azure Stack Hub peut prendre jusqu’à deux heures. Vous pouvez vérifier l’état de démarrage d’Azure Stack Hub à l’aide de la cmdlet [**Get-ActionStatus**](#get-the-startup-status-for-azure-stack-hub).

3. Vérifiez que toutes vos ressources de locataire ont retrouvé l’état dans lequel elles se trouvaient avant l’arrêt. Il se peut que le gestionnaire de charges de travail doive reconfigurer les charges de travail exécutées sur les ressources de locataire après le démarrage.

## <a name="get-the-startup-status-for-azure-stack-hub"></a>Obtenir l’état de démarrage d’Azure Stack Hub

Pour initier la routine de démarrage d'Azure Stack Hub, procédez comme suit :

1. Ouvrez une session de point de terminaison privilégié à partir d’une machine ayant accès aux machines virtuelles ERCS Azure Stack Hub via le réseau.

2. À partir de la session PEP, exécutez :

    ```powershell
      Get-ActionStatus Start-AzureStack
    ```

## <a name="troubleshoot-startup-and-shutdown-of-azure-stack-hub"></a>Résoudre les problèmes de démarrage et d’arrêt d’Azure Stack Hub

Si les services d’infrastructure et de locataire ne démarrent pas dans les deux heures qui suivent la mise sous tension de votre environnement Azure Stack Hub, procédez comme suit.

1. Ouvrez une session de point de terminaison privilégié à partir d’une machine ayant accès aux machines virtuelles ERCS Azure Stack Hub via le réseau.

2. Exécutez :

    ```powershell
      Test-AzureStack
      ```

3. Examinez la sortie et corrigez les erreurs liées à l’intégrité. Pour plus d’informations, consultez [Exécuter un test de validation d’Azure Stack Hub](azure-stack-diagnostic-test.md).

4. Exécutez :

    ```powershell
      Start-AzureStack
    ```

5. Si l’exécution de **Start-AzureStack** aboutit à un échec, contactez le Support Microsoft.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les [Outils de diagnostic Azure Stack Hub](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs)
