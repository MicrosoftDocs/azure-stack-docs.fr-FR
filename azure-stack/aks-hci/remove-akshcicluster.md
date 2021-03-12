---
title: Remove-AksHciCluster
description: La commande PowerShell Remove-AksHciCluster supprime un cluster Kubernetes managé.
author: jessicaguan
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 868aa8a739c69ad6e29d73192f91c9bda73a3195
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "101874419"
---
# <a name="remove-akshcicluster"></a>Remove-AksHciCluster

## <a name="synopsis"></a>Synopsis
Supprimer un cluster Kubernetes managé.

## <a name="syntax"></a>Syntaxe

### <a name="delete-a-managed-kubernetes-cluster"></a>Supprimer un cluster Kubernetes managé
```powershell
Remove-AksHciCluster -name 
                    [-force]   
```

## <a name="description"></a>Description
Supprimer un cluster Kubernetes managé.

## <a name="examples"></a>Exemples

### <a name="delete-an-existing-managed-kubernetes-cluster"></a>Supprimer un cluster Kubernetes managé existant
```powershell
PS C:\> Remove-AksHciCluster -name myCluster
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
