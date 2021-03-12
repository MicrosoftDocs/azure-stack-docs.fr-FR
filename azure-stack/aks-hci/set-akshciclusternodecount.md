---
title: Set-AksHciClusterNodeCount
author: jessicaguan
description: La commande PowerShell Set-AksHciClusterNodeCount met à l’échelle le nombre de nœuds de plan de contrôle ou de nœuds Worker dans un cluster.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 1dff2962ee1997d6eaa696f6e0eeadf7ef2d59e1
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "101874416"
---
# <a name="set-akshciclusternodecount"></a>Set-AksHciClusterNodeCount

## <a name="synopsis"></a>Synopsis
Mettre à l’échelle le nombre de nœuds de plan de contrôle ou de nœuds Worker dans un cluster.

## <a name="syntax"></a>Syntaxe

### <a name="scale-control-plane-nodes"></a>Mettre à l’échelle les nœuds de plan de contrôle
```powershell
Set-AksHciClusterNodeCount -name <String>
                           -controlPlaneNodeCount <int> 
```

### <a name="scale-worker-nodes"></a>Mettre à l’échelle les nœuds Worker
```powershell
Set-AksHciClusterNodeCount -name <String>
                           -linuxNodeCount <int>
                           -windowsNodeCount <int>
```

## <a name="description"></a>Description
Mettre à l’échelle le nombre de nœuds de plan de contrôle ou de nœuds Worker dans un cluster. Les nœuds de plan de contrôle et les nœuds Worker doivent être mis à l’échelle indépendamment.

## <a name="examples"></a>Exemples

### <a name="scale-control-plane-nodes"></a>Mettre à l’échelle les nœuds de plan de contrôle
```powershell
PS C:\> Set-AksHciClusterNodeCount -name myCluster -controlPlaneNodeCount 3
```

### <a name="scale-worker-nodes"></a>Mettre à l’échelle les nœuds Worker
```powershell
PS C:\> Set-AksHciClusterNodeCount -name myCluster -linuxNodeCount 2 -windowsNodeCount 2
```

## <a name="parameters"></a>Paramètres

### <a name="-name"></a>-name
Nom alphanumérique de votre cluster Kubernetes.

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

### <a name="-controlplanenodecount"></a>-controlPlaneNodeCount
Nombre de nœuds dans votre plan de contrôle. Il n'y a pas de valeur par défaut.

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: none
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-linuxnodecount"></a>-linuxNodeCount
Nombre de nœuds Linux dans votre cluster Kubernetes. 1 constitue la valeur par défaut.

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-proxyservercertfile"></a>-proxyServerCertFile
Certificat utilisé pour l’authentification auprès du serveur proxy.
 
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

### <a name="-windowsnodecount"></a>-windowsNodeCount
Nombre de nœuds Windows dans votre cluster Kubernetes. 1 constitue la valeur par défaut.

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
