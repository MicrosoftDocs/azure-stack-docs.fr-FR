---
title: Point de terminaison privilégié Get-AzureStackSupportConfiguration pour Azure Stack Hub
description: Informations de référence sur le point de terminaison privilégié Azure Stack PowerShell - Get-AzureStackSupportConfiguration
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 710201cc045b356037a54e6f7f0eeee63284b0d6
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563541"
---
# <a name="get-azurestacksupportconfiguration"></a>Get-AzureStackSupportConfiguration

## <a name="synopsis"></a>Synopsis
Obtient les paramètres de configuration du service de support.

## <a name="syntax"></a>Syntaxe

```
Get-AzureStackSupportConfiguration [-IncludeRegistrationObjectId] [-AsJob]
```

## <a name="description"></a>Description
Paramètres de configuration du service de support.

## <a name="examples"></a>Exemples

### <a name="example-1"></a>Exemple 1
L’exemple ci-dessous obtient les détails de l’inscription si le tampon a été inscrit ; sinon, la valeur Null.

```
PS  C:\> Get-AzureStackSupportConfiguration
```

## <a name="parameters"></a>Paramètres

### <a name="-includeregistrationobjectid"></a>-IncludeRegistrationObjectId
facultatif.
Nécessite une connectivité Internet.
Récupère l’ID d’objet de l’identité d’inscription.

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

## <a name="notes"></a>Notes
Nécessite une connectivité Internet sur la machine virtuelle de support.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’accès au point de terminaison privilégié et sur son utilisation, consultez [Utiliser le point de terminaison privilégié dans Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint).