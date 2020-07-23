---
title: Point de terminaison privilégié Get-AzureStackStampInformation pour Azure Stack Hub
description: Informations de référence sur le point de terminaison privilégié Azure Stack PowerShell - Get-AzureStackStampInformation
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: ec2fb2a7e23cbce90d0ab8ebd75ed33a3e446013
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86487207"
---
# <a name="get-azurestackstampinformation"></a>Get-AzureStackStampInformation

## <a name="synopsis"></a>Synopsis
Obtient les informations du tampon.

## <a name="syntax"></a>Syntaxe

```
Get-AzureStackStampInformation [-AsJob]
```

## <a name="description"></a>Description
Importe le module client ECE et démarre un plan d’action sur le rôle Cloud sur le service ECE.

## <a name="examples"></a>Exemples

### <a name="example-1"></a>Exemple 1
```
Get-AzureStackStampInformation
```

## <a name="parameters"></a>Paramètres

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
