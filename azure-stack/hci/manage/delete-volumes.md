---
title: Supprimer des volumes dans Azure Stack HCI
description: Guide pratique pour supprimer des volumes dans Azure Stack HCI à l’aide de Windows Admin Center.
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 03/05/2020
ms.openlocfilehash: ab99ed7a49fe2bf003245f139451895a85c4edbf
ms.sourcegitcommit: a77dea675af6500bdad529106f5782d86bec6a34
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "79025977"
---
# <a name="deleting-volumes-in-azure-stack-hci"></a>Suppression des volumes dans Azure Stack HCI

> S’applique à : Windows Server 2019

Cette rubrique fournit des instructions sur la suppression des volumes dans un cluster d’[espaces de stockage direct](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) à l’aide de Windows Admin Center.

Regardez une courte vidéo sur la suppression d’un volume.

> [!VIDEO https://www.youtube-nocookie.com/embed/DbjF8r2F6Jo]

## <a name="use-windows-admin-center-to-delete-a-volume"></a>Utiliser Windows Admin Center pour supprimer un volume

1. Dans Windows Admin Center, connectez-vous à un cluster d’espaces de stockage direct, puis sélectionnez **Volumes** dans le volet **Outils**.
2. Dans la page **Volumes**, sélectionnez l’onglet **Inventaire**, puis sélectionnez le volume à supprimer.
3. En haut de la page de détails des volumes, sélectionnez **Supprimer**.
4. Dans la boîte de dialogue de confirmation, cochez la case permettant de confirmer la suppression du volume, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir des instructions pas à pas sur d’autres tâches essentielles de la gestion du stockage, consultez également :

- [Planification des volumes dans les espaces de stockage direct](/windows-server/storage/storage-spaces/plan-volumes)
- [Création de volumes dans les espaces de stockage direct](/windows-server/storage/storage-spaces/create-volumes)
- [Extension des volumes dans les espaces de stockage direct](/windows-server/storage/storage-spaces/resize-volumes)