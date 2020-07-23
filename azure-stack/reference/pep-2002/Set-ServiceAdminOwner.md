---
title: Point de terminaison privilégié Set-ServiceAdminOwner pour Azure Stack Hub
description: Informations de référence sur le point de terminaison privilégié Azure Stack PowerShell - Set-ServiceAdminOwner
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 5dcda1a862d347472bd7abb1f9d584af0a86e384
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86486170"
---
# <a name="set-serviceadminowner"></a>Set-ServiceAdminOwner

## <a name="synopsis"></a>Synopsis
Script pour mettre à jour l’administrateur de service.

## <a name="syntax"></a>Syntaxe

```
Set-ServiceAdminOwner [[-TimeoutInSecs] <Object>] [[-ServiceAdminOwnerUpn] <Object>] [-AsJob]
```

## <a name="description"></a>Description
Script pour mettre à jour l’UPN de l’administrateur de service.

## <a name="examples"></a>Exemples

### <a name="example-1"></a>Exemple 1
```
Set-ServiceAdminOwner -NewServiceAdminUpn "administrator@contoso.com" -TimeoutInSecs 1000
```

## <a name="parameters"></a>Paramètres

### <a name="-serviceadminownerupn"></a>-ServiceAdminOwnerUpn
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-timeoutinsecs"></a>-TimeoutInSecs
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: 1000
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
