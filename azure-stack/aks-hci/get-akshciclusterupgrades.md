---
title: Get-AksHciClusterUpgrades
author: jessicaguan
description: La commande PowerShell Get-AksHciClusterUpgrades obtient les mises à niveau Kubernetes disponibles pour un cluster Azure Kubernetes Service.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 3d5f4a6b67345baaa8df9b9eccc26d0a0503dafd
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "101874385"
---
# <a name="get-akshciclusterupgrades"></a>Get-AksHciClusterUpgrades

## <a name="synopsis"></a>Synopsis
Obtenir les mises à niveau Kubernetes disponibles pour un cluster Azure Kubernetes service.

## <a name="syntax"></a>Syntaxe

```powershell
Get-AksHciClusterUpgrades -name <String>
                          
```

## <a name="description"></a>Description
Obtenir les mises à niveau Kubernetes disponibles pour un cluster Azure Kubernetes service.

## <a name="examples"></a>Exemples

```powershell
PS C:\> Get-AksHciClusterUpgrades -name mycluster
```

## <a name="parameters"></a>Paramètres

### <a name="-name"></a>-name
Nom alphanumérique de votre cluster.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
