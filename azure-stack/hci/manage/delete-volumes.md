---
title: Supprimer des volumes dans Azure Stack HCI
description: Guide pratique pour supprimer des volumes dans Azure Stack HCI à l’aide de Windows Admin Center et de PowerShell.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 07/21/2020
ms.openlocfilehash: 7a90948cccb75bb8bb1578101d530c46852e99d6
ms.sourcegitcommit: 0e52f460295255b799bac92b40122a22bf994e27
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/21/2020
ms.locfileid: "86866520"
---
# <a name="deleting-volumes-in-azure-stack-hci"></a>Suppression des volumes dans Azure Stack HCI

> S’applique à : Azure Stack HCI, version 20H2 ; Windows Server 2019

Cette rubrique fournit des instructions sur la suppression des volumes dans un cluster Azure Stack HCI à l’aide de Windows Admin Center.

Regardez une courte vidéo sur la suppression d’un volume à l’aide de Windows Admin Center.

> [!VIDEO https://www.youtube-nocookie.com/embed/DbjF8r2F6Jo]

## <a name="use-windows-admin-center-to-delete-a-volume"></a>Utiliser Windows Admin Center pour supprimer un volume

1. Dans Windows Admin Center, connectez-vous à un cluster d’espaces de stockage direct, puis sélectionnez **Volumes** dans le volet **Outils**.
2. Dans la page **Volumes**, sélectionnez l’onglet **Inventaire**, puis sélectionnez le volume à supprimer.
3. En haut de la page de détails des volumes, sélectionnez **Supprimer**.
4. Dans la boîte de dialogue de confirmation, cochez la case permettant de confirmer la suppression du volume, puis sélectionnez **Supprimer**.

## <a name="delete-volumes-using-powershell"></a>Supprimer des volumes à l’aide de PowerShell

Utilisez l’applet de commande **Remove-VirtualDisk** pour supprimer des volumes dans les espaces de stockage direct. Cette applet de commande permet de supprimer l’objet **VirtualDisk** et de rendre l’espace qu’il utilisait au pool de stockage qui expose l’objet **VirtualDisk**.

Tout d’abord, lancez PowerShell sur votre PC de gestion et exécutez l’applet de commande **VirtualDisk** avec le paramètre **CimSession**, qui est le nom d’un nœud de serveur ou de cluster d’espaces de stockage direct, par exemple *clustername.microsoft.com* :

```PowerShell
Get-VirtualDisk -CimSession clustername.microsoft.com
```

Une liste de valeurs possibles pour le paramètre **-FriendlyName**, qui correspondent aux noms de volumes sur votre cluster, est alors retournée.

### <a name="example"></a>Exemple

Pour supprimer un volume en miroir appelé *volume1*, exécutez la commande suivante dans PowerShell :

```PowerShell
Remove-VirtualDisk -FriendlyName "Volume1"
```

Vous êtes invité à confirmer que vous souhaitez exécuter l’action et effacer toutes les données contenues dans le volume. Choisissez O ou N.

   > [!WARNING]
   > Cette action est irréversible. Cet exemple supprime définitivement un objet **VirtualDisk** du volume.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir des instructions pas à pas sur d’autres tâches essentielles de la gestion du stockage, consultez également :

- [Planifier des volumes](../concepts/plan-volumes.md)
- [Créer des volumes](create-volumes.md)
- [Étendre les volumes](extend-volumes.md)