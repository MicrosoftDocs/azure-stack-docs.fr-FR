---
title: Point de terminaison privilégié Set-GraphApplication pour Azure Stack Hub
description: Informations de référence sur le point de terminaison privilégié Azure Stack PowerShell - Set-GraphApplication
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 44d1f75f243bb23e3a1823d96e7a3d877568abb9
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86486238"
---
# <a name="set-graphapplication"></a>Set-GraphApplication

## <a name="synopsis"></a>Synopsis
Set-GraphApplication est une fonction wrapper utilisée pour appeler des applets de commande Graph AD FS sur AD FS.

## <a name="syntax"></a>Syntaxe

```
Set-GraphApplication [-ClientCertificates <Object>] [-ClientRedirectUris <Object>]
 [-ApplicationIdentifier <Object>] [-ChangeClientSecret] [-ResetClientSecret] [-AsJob]
```

## <a name="description"></a>Description
Appelle Set-GraphApplication sur AD FS pour modifier une application sur la machine AD FS.

## <a name="examples"></a>Exemples

### <a name="example-1"></a>Exemple 1
```
New-GraphApplication -Name $ApplicationName -ClientRedirectUris $redirectUri -ClientCertificates $certificate
```

## <a name="parameters"></a>Paramètres

### <a name="-applicationidentifier"></a>-ApplicationIdentifier
 

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

### <a name="-clientredirecturis"></a>-ClientRedirectUris
URI de redirection utilisé pour créer une application dans AD FS.

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

### <a name="-clientcertificates"></a>-ClientCertificates
Certificat utilisé pour créer une application dans AD FS.

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

### <a name="-resetclientsecret"></a>-ResetClientSecret
 

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-changeclientsecret"></a>-ChangeClientSecret
 

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
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
