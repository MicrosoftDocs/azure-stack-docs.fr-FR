---
title: Point de terminaison privilégié Close-PrivilegedEndpoint pour Azure Stack Hub
description: Informations de référence sur le point de terminaison privilégié Azure Stack PowerShell - Close-PrivilegedEndpoint
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 948d8771be47ca9a594da75d8e4d138ceb6b894c
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82564009"
---
# <a name="set-telemetry"></a>Set-Telemetry

## <a name="synopsis"></a>Synopsis
Active ou désactive l’envoi des données de télémétrie à Microsoft.

## <a name="syntax"></a>Syntaxe

```
Set-Telemetry [-Disable] [-Enable] [-AsJob]
```

## <a name="description"></a>Description
L’applet de commande Set-Telemetry vous permet de spécifier si les données de télémétrie sont envoyées à Microsoft, en modifiant le paramètre correspondant dans le Registre.

Plus précisément, cette applet de commande configure la stratégie de groupe de domaine pour définir la valeur de télémétrie sur 0 dans le Registre et pour arrêter l’exécution du service Windows UTC sur l’ensemble des machines virtuelles et ordinateurs hôtes de l’infrastructure.

## <a name="examples"></a>Exemples

### <a name="example-1"></a>Exemple 1
```
Set-Telemetry -Enable
```

### <a name="example-2"></a>Exemple 2
```
Set-Telemetry -Disable
```

## <a name="parameters"></a>Paramètres

### <a name="-enable"></a>-Enable
Active l’envoi des données de télémétrie à Microsoft.

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

### <a name="-disable"></a>-Disable
Désactive l’envoi des données de télémétrie à Microsoft.

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

Pour plus d’informations sur l’accès au point de terminaison privilégié et sur son utilisation, consultez [Utiliser le point de terminaison privilégié dans Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint).