---
title: Restart-AksHci
author: jessicaguan
description: La commande PowerShell Restart-AksHci redémarre AKS sur Azure Stack HCI et supprime tous les clusters Kubernetes déployés.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 358ca36b085b21c6ecdbea7a024d9e72e0ca0597
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "101874417"
---
# <a name="restart-akshci"></a>Restart-AksHci

## <a name="synopsis"></a>Synopsis
Redémarrer Azure Kubernetes Service sur Azure Stack HCI et supprimer tous les clusters Kubernetes déployés.

## <a name="syntax"></a>Syntaxe

### <a name="restart-aks-hci"></a>Redémarrer AKS-HCI
```powershell
Restart-AksHci
```

## <a name="description"></a>Description
L’opération de redémarrage d’Azure Kubernetes Service sur Azure Stack HCI supprime tous vos clusters Kubernetes, le cas échéant, et l’hôte Azure Kubernetes Service. Elle désinstalle également les agents et services Azure Kubernetes Service sur Azure Stack HCI des nœuds. Elle repasse ensuite par les étapes du processus d’installation d’origine jusqu’à ce que l’hôte soit recréé. La configuration d’Azure Kubernetes Service sur Azure Stack HCI que vous avez effectuée à l’aide de Set-AksHciConfig et les images VHDX téléchargées sont conservées.

## <a name="examples"></a>Exemples

### <a name="example"></a>Exemple
```powershell
PS C:\> Restart-AksHci
```
