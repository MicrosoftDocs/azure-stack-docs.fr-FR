---
title: Point de terminaison privilégié Start-AzureStack pour Azure Stack Hub
description: Informations de référence sur le point de terminaison privilégié Azure Stack PowerShell - Start-AzureStack
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 2a8814182d518c7327a64324cdc844018e989f18
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86485847"
---
# <a name="start-azurestack"></a>Start-AzureStack

## <a name="synopsis"></a>Synopsis
Démarre tous les services Azure Stack Hub.

## <a name="syntax"></a>Syntaxe

```
Start-AzureStack [[-TimeoutInSecs] <Object>] [-AsJob]
```

## <a name="description"></a>Description
Démarre tous les services Azure Stack Hub.

## <a name="examples"></a>Exemples

### <a name="example-1"></a>Exemple 1
```powershell
PS C:\> 
```



## <a name="parameters"></a>Paramètres

### <a name="-timeoutinsecs"></a>-TimeoutInSecs
Durée maximale après laquelle l’exécution est arrêtée.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: 2400
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-asjob"></a>-AsJob


```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’accès au point de terminaison privilégié et sur son utilisation, consultez [Utiliser le point de terminaison privilégié dans Azure Stack Hub](../../operator/azure-stack-privileged-endpoint.md).
