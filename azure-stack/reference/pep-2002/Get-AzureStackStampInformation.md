---
title: Point de terminaison privilégié Get-AzureStackStampInformation pour Azure Stack Hub
description: Informations de référence sur le point de terminaison privilégié Azure Stack PowerShell - Get-AzureStackStampInformation
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 3318ce344804174fe3252808d5857e93b8113e22
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563553"
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

Pour plus d’informations sur l’accès au point de terminaison privilégié et sur son utilisation, consultez [Utiliser le point de terminaison privilégié dans Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint).