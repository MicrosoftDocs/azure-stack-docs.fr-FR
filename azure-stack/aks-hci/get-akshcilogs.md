---
title: Get-AksHciLogs
author: jessicaguan
description: La commande PowerShell Get-AksHciLogs crée un dossier compressé avec les journaux de tous vos pods.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: ef78efb91a6a8b5c0c91e815cb8aedd0a57adfe8
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "101874404"
---
# <a name="get-akshcilogs"></a>Get-AksHciLogs

## <a name="synopsis"></a>Synopsis
Créer un dossier compressé avec les journaux de tous vos pods. 

## <a name="syntax"></a>Syntaxe

```powershell
Get-AksHciLogs
```

## <a name="description"></a>Description
Créer un dossier compressé avec les journaux de tous vos pods. Cette commande crée un dossier compressé de sortie, appelé `akshcilogs.zip` dans votre répertoire de travail AKS sur Azure Stack HCI. Le chemin complet du fichier `akshcilogs.zip` est la sortie après l’exécution de `Get-AksHciLogs` (par exemple, `C:\AksHci\0.9.6.3\akshcilogs.zip`, dans lequel `0.9.6.3` est le numéro de version de AKS sur Azure Stack HCI).

## <a name="examples"></a>Exemples

### <a name="example"></a>Exemple
```powershell
PS C:\> Get-AksHciLogs
```
