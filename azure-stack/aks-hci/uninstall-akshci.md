---
title: Uninstall-AksHci
author: jessicaguan
description: La commande PowerShell Uninstall-AksHci supprime AKS sur Azure Stack HCI.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 89cd1bd48518f90dd4ce850e35e42c3106681823
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "101874423"
---
# <a name="uninstall-akshci"></a>Uninstall-AksHci

## <a name="synopsis"></a>Synopsis
Supprime Azure Kubernetes Service sur Azure Stack HCI.

## <a name="syntax"></a>Syntaxe

```powershell
Uninstall-AksHci [-skipConfigCleanup]
```

## <a name="description"></a>Description
Supprime Azure Kubernetes Service sur Azure Stack HCI. 

Si les commandes PowerShell sont exécutées sur un cluster où Windows Admin Center a été précédemment utilisé pour le déploiement, le module PowerShell vérifie l’existence du fichier de configuration de Windows Admin Center. Windows Admin Center place son fichier de configuration sur tous les nœuds. 

## <a name="example"></a>Exemple

### <a name="example"></a>Exemple
```powershell
PS C:\> Uninstall-AksHci
```

## <a name="parameters"></a>Paramètres

### <a name="-skipconfigcleanup"></a>-skipConfigCleanup
Ignore la suppression des configurations après la désinstallation. Si vous utilisez cet indicateur, votre ancienne configuration est conservée.

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
