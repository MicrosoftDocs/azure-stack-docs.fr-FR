---
title: Démarrer et arrêter Azure Stack | Microsoft Docs
description: Découvrez comment démarrer et arrêter Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 43BF9DCF-F1B7-49B5-ADC5-1DA3AF9668CA
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 5072c40f4e8eba787e0a18c7c73a51111d0c847b
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71829266"
---
# <a name="start-and-stop-azure-stack"></a>Démarrer et arrêter Azure Stack
Suivez les procédures décrites dans cet article pour arrêter et redémarrer correctement les services Azure Stack. L’arrêt met physiquement hors tension l’ensemble de l’environnement Azure Stack. Le démarrage met sous tension tous les rôles d’infrastructure et rétablit l’état d’alimentation des ressources de locataire dans lequel elles se trouvaient avant l’arrêt.

## <a name="stop-azure-stack"></a>Arrêter Azure Stack 

Pour arrêter Azure Stack, procédez comme suit :

1. Préparez toutes les charges de travail exécutées sur les ressources de locataire de votre environnement Azure Stack pour l’arrêt à venir. 

2. Ouvrez une session PEP (Privileged Endpoint Session) à partir d’une machine ayant accès aux machines virtuelles ERCS Azure Stack via le réseau. Pour obtenir des instructions, voir [Utilisation du point de terminaison privilégié dans Azure Stack](azure-stack-privileged-endpoint.md).

3. À partir de la session PEP, exécutez :

    ```powershell
      Stop-AzureStack
    ```

4. Attendez que tous les nœuds physiques Azure Stack soient hors tension.

> [!Note]  
> Vous pouvez vérifier l’état de l’alimentation d’un nœud physique en suivant les instructions du fabricant OEM qui a fourni le matériel Azure Stack. 

## <a name="start-azure-stack"></a>Démarrer Azure Stack 

Pour démarrer Azure Stack, procédez comme suit. Suivez ces étapes quelle que soit la manière dont Azure Stack s’est arrêté.

1. Mettez tous les nœuds physiques de votre environnement Azure Stack sous tension. Découvrez comment mettre sous tension les nœuds physiques en consultant les instructions du fabricant OEM qui vous a fourni le matériel Azure Stack.

2. Attendez que les services d’infrastructure Azure Stack démarrent. Le démarrage des services d’infrastructure Azure Stack peut prendre jusqu’à deux heures. Vous pouvez vérifier l’état de démarrage d’Azure Stack à l’aide de la cmdlet [**Get-ActionStatus**](#get-the-startup-status-for-azure-stack).

3. Assurez-vous que toutes vos ressources de locataire retrouvent l’état dans lequel elles se trouvaient avant l’arrêt. Il se peut que le gestionnaire de charges de travail doive reconfigurer les charges de travail exécutées sur les ressources de locataire après le démarrage.

## <a name="get-the-startup-status-for-azure-stack"></a>Obtenir l’état de démarrage d’Azure Stack

Pour initier la routine de démarrage Azure Stack, procédez comme suit :

1. Ouvrez une session PEP (Privileged Endpoint Session) à partir d’une machine ayant accès aux machines virtuelles ERCS Azure Stack via le réseau.

2. À partir de la session PEP, exécutez :

    ```powershell
      Get-ActionStatus Start-AzureStack
    ```

## <a name="troubleshoot-startup-and-shutdown-of-azure-stack"></a>Résoudre les problèmes de démarrage et d’arrêt d’Azure Stack

Si les services d’infrastructure et de locataire ne démarrent pas dans les 2 heures qui suivent la mise sous tension de votre environnement Azure Stack, procédez comme suit. 

1. Ouvrez une session PEP (Privileged Endpoint Session) à partir d’une machine ayant accès aux machines virtuelles ERCS Azure Stack via le réseau.

2. Exécutez : 

    ```powershell
      Test-AzureStack
      ```

3. Examinez la sortie et corrigez les erreurs liées à l’intégrité. Pour plus d’informations, consultez [Exécuter un test de validation d’Azure Stack](azure-stack-diagnostic-test.md).

4. Exécutez :

    ```powershell
      Start-AzureStack
    ```

5. Si l’exécution de **Start-AzureStack** entraîne un échec, contactez les services du support technique Microsoft. 

## <a name="next-steps"></a>Étapes suivantes 

En savoir plus sur les [Outils de diagnostic Azure Stack](azure-stack-configure-on-demand-diagnostic-log-collection.md#using-pep-to-collect-diagnostic-logs)
