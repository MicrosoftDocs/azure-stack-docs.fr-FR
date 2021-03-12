---
title: Get-AksHciCluster
author: jessicaguan
description: La commande PowerShell Get-AksHciCluster liste les clusters managés par Kubernetes, y compris l’hôte Azure Kubernetes Service.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 14d7f807ef04432e7ea8c3c57dbb4bb80c64731b
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "101874378"
---
# <a name="get-akshcicluster"></a>Get-AksHciCluster

## <a name="synopsis"></a>Synopsis
Lister les clusters managés par Kubernetes, y compris l’hôte Azure Kubernetes Service.

## <a name="syntax"></a>Syntaxe

```powershell
Get-AksHciCluster [-name <String>]
```

## <a name="description"></a>Description
Lister les clusters managés par Kubernetes, y compris l’hôte Azure Kubernetes Service.

## <a name="examples"></a>Exemples

### <a name="list-all-kubernetes-clusters"></a>Lister tous les clusters Kubernetes
```powershell
PS C:\> Get-AksHciCluster
```

## <a name="parameters"></a>Paramètres

### <a name="-name"></a>-name
Nom de votre cluster.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
