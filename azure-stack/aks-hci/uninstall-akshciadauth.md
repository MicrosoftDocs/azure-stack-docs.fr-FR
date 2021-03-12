---
title: Uninstall-AksHciAdAuth
author: jessicaguan
description: La commande PowerShell Uninstall-AksHciAdAuth désinstalle l’authentification AD.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 4239d9c268d57315584381e02fa121848395e683
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "101874370"
---
# <a name="uninstall-akshciadauth"></a>Uninstall-AksHciAdAuth

## <a name="synopsis"></a>Synopsis
Désinstallez l’authentification Active Directory.

## <a name="syntax"></a>Syntaxe

```powershell
Uninstall-AksHciAdAuth -name <String>
```

## <a name="description"></a>Description
Désinstallez l’authentification Active Directory.

## <a name="examples"></a>Exemples

### <a name="example"></a>Exemple
```powershell
PS C:\> Uninstall-AksHciAksHciAdAuth -name myCluster
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
