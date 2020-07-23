---
title: Point de terminaison privilégié Get-GraphApplication pour Azure Stack Hub
description: Informations de référence sur le point de terminaison privilégié Azure Stack PowerShell - Get-GraphApplication
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: a100b974a3d622c0b9bb51d850d9a8435d72f549
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86487088"
---
# <a name="get-graphapplication"></a>Get-GraphApplication

## <a name="synopsis"></a>Synopsis
Get-GraphApplication est une fonction wrapper qui permet d’obtenir des informations sur l’application Graph ayant le nom ou l’identificateur spécifié.

## <a name="syntax"></a>Syntaxe

```
Get-GraphApplication [[-ApplicationIdentifier] <Object>] [-ApplicationName <Object>] [-AsJob]
```

## <a name="description"></a>Description
Appelle Get-GraphApplicationGroup sur la machine AD FS pour obtenir les informations sur l’application Graph.

## <a name="examples"></a>Exemples

### <a name="example-1"></a>Exemple 1
```
Get-GraphApplication -ApplicationName $ApplicationName
```

## <a name="parameters"></a>Paramètres

### <a name="-applicationidentifier"></a>-ApplicationIdentifier
Identificateur de l’application inscrite auprès d’AD FS.

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

### <a name="-applicationname"></a>-ApplicationName
Nom de l’application inscrite auprès d’AD FS.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
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
