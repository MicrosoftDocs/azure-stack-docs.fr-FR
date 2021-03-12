---
title: Get-AksHciCredential
author: jessicaguan
description: La commande PowerShell Get-AksHciCredential accède à votre cluster à l’aide de kubectl.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 50f99f6e5a64a1a0b687c0dfdc42b407b4106500
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "101874406"
---
# <a name="get-akshcicredential"></a>Get-AksHciCredential

## <a name="synopsis"></a>Synopsis
Accéder à vos clusters avec `kubectl`. Cette opération utilise le fichier _kubeconfig_ du cluster spécifié comme fichier _kubeconfig_ par défaut pour `kubectl`.

## <a name="syntax"></a>Syntaxe

```powershell
Get-AksHciCredential -name <String>
                    [-outputLocation <String>]
                    [-adAuth]
```

## <a name="description"></a>Description
Accéder à vos clusters à l’aide de kubectl.

## <a name="examples"></a>Exemples

### <a name="access-your-cluster-using-kubectl"></a>Accéder à vos clusters à l’aide de kubectl.
```powershell
PS C:\> Get-AksHciCredential -name myCluster
```

## <a name="parameters"></a>Paramètres

### <a name="-name"></a>-name
Nom du cluster.

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

### <a name="-outputlocation"></a>-outputLocation
Emplacement auquel vous voulez que le fichier kubeconfig soit téléchargé. La valeur par défaut est `%USERPROFILE%\.kube`.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: %USERPROFILE%\.kube
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-adauth"></a>-adAuth
Utilisez cet indicateur pour obtenir la version SSO Active Directory de kubeconfig.

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
