---
title: Gérer la capacité de mémoire physique dans Azure Stack Hub
description: Apprenez à surveiller et gérer la mémoire physique et la capacité dans Azure Stack Hub.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: b52775083f05be4aac3de4fd5d3f72808bcc7569
ms.sourcegitcommit: 1fa0140481a483e5c27f602386fe1fae77ad29f7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78368071"
---
# <a name="manage-physical-memory-capacity-in-azure-stack-hub"></a>Gérer la capacité de mémoire physique dans Azure Stack Hub

Pour augmenter la capacité totale de mémoire disponible dans Azure Stack Hub, vous pouvez ajouter de la mémoire. Dans Azure Stack Hub, votre serveur physique est également appelé *nœud d'unité d'échelle*. Tous les nœuds d’unité d’échelle qui sont membres d’une seule unité d’échelle doivent avoir la même quantité de mémoire.

> [!note]  
> Avant de continuer, consultez la documentation de votre fabricant de matériel pour voir s’il prend en charge une mise à niveau de la mémoire physique. Votre contrat de support avec votre fabricant de matériel OEM peut exiger que le fabricant effectue le positionnement du rack de serveurs physiques et la mise à jour du microprogramme de l’appareil.

L’organigramme suivant illustre la procédure générale d’ajout de mémoire à chaque nœud d’unité d’échelle.

![Processus d’ajout de mémoire dans chaque nœud d’unité d’échelle](media/azure-stack-manage-storage-physical-capacity/process-to-add-memory-to-scale-unit.png)

## <a name="add-memory-to-an-existing-node"></a>Ajouter de la mémoire à un nœud existant
Les étapes suivantes fournissent une vue d’ensemble globale du processus d’ajout de mémoire.

> [!Warning]
> Ne suivez pas ces étapes sans consulter la documentation fournie par votre fournisseur OEM.
> 
> [!Warning]
> L’unité d’échelle doit être arrêtée dans son ensemble, car la mise à niveau propagée de la mémoire n’est pas prise en charge.

1. Arrêtez Azure Stack Hub en suivant la procédure décrite dans l'article [Démarrer et arrêter Azure Stack Hub](azure-stack-start-and-stop.md).
2. Mettez la mémoire à niveau sur chaque ordinateur physique à l’aide de la documentation de votre fabricant de matériel.
3. Démarrez Azure Stack Hub en suivant la procédure décrite dans l'article [Démarrer et arrêter Azure Stack Hub](azure-stack-start-and-stop.md).

## <a name="next-steps"></a>Étapes suivantes

 - Pour apprendre à gérer les comptes de stockage dans Azure Stack Hub, consultez [Gérer les compte de stockage dans Azure Stack Hub](azure-stack-manage-storage-accounts.md).
 - Pour apprendre à surveiller et gérer la capacité de stockage de votre déploiement Azure Stack Hub, consultez [Gérer la capacité de stockage d'Azure Stack Hub](azure-stack-manage-storage-shares.md).
