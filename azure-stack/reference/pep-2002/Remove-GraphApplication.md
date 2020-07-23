---
title: Point de terminaison privilégié Remove-GraphApplication pour Azure Stack Hub
description: Informations de référence sur le point de terminaison privilégié Azure Stack PowerShell - Remove-GraphApplication
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: b91d363626bc600027b37a1b9c56e69f6c40c3aa
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86486388"
---
# <a name="remove-graphapplication"></a>Remove-GraphApplication

## <a name="synopsis"></a>Synopsis
Remove-GraphApplication est une fonction wrapper utilisée pour appeler des applets de commande Graph AD FS sur AD FS.

## <a name="syntax"></a>Syntaxe

```
Remove-GraphApplication [[-ApplicationIdentifier] <Object>] [-AsJob]
```

## <a name="description"></a>Description
Appelle Remove-GraphApplicationGroup sur AD FS pour supprimer l’application spécifiée sur la machine AD FS.

## <a name="examples"></a>Exemples

### <a name="example-1"></a>Exemple 1
```
Remove-GraphApplication -ApplicationIdentifier "Application-Identifier-123456"
```

## <a name="parameters"></a>Paramètres

### <a name="-applicationidentifier"></a>-ApplicationIdentifier
Identificateur de l’application à supprimer

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
