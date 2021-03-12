---
title: Update-AksHciCluster
author: jessicaguan
description: La commande PowerShell Update-AksHciCluster met à jour un cluster Kubernetes managé avec une version plus récente de Kubernetes ou du système d’exploitation.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: a8fc5c1e87ed78891333af7a3a998767110428ea
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "101874371"
---
# <a name="update-akshcicluster"></a>Update-AksHciCluster

## <a name="synopsis"></a>Synopsis
Met à jour un cluster Kubernetes managé vers une version plus récente de Kubernetes ou du système d’exploitation.

## <a name="syntax"></a>Syntaxe

```powershell
Update-AksHciCluster -name <String>
                    [-kubernetesVersion <String>]
                    [-operatingSystem]
```

## <a name="description"></a>Description
Met à jour un cluster Kubernetes managé vers une version plus récente de Kubernetes ou du système d’exploitation.

## <a name="examples"></a>Exemples

### <a name="example"></a>Exemple
```powershell
PS C:\> Update-AksHciCluster -clusterName myCluster -kubernetesVersion v1.18.8 -operatingSystem
```

## <a name="parameters"></a>Paramètres

### <a name="-name"></a>-name
Nom du cluster Kubernetes.

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

### <a name="-kubernetesversion"></a>-kubernetesVersion
Version de Kubernetes vers laquelle vous souhaitez effectuer une mise à niveau.

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

### <a name="-operatingsystem"></a>-operatingSystem
Utilisez cet indicateur si vous souhaitez effectuer une mise à jour vers la version suivante du système d’exploitation.

```yaml
Type: System.Management.Automation.SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

