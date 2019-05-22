---
title: Remplacer un nœud d’unité d’échelle sur un système intégré Azure Stack | Microsoft Docs
description: Découvrez comment remplacer un nœud d’unité d’échelle physique sur un système intégré Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.lastreviewed: 12/06/2018
ms.openlocfilehash: 739e15b2cc95384a164f91f1b1267be256380a43
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2019
ms.locfileid: "64984864"
---
# <a name="replace-a-scale-unit-node-on-an-azure-stack-integrated-system"></a>Remplacer un nœud d’unité d’échelle sur un système intégré Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Cet article décrit la procédure générale permettant de remplacer un ordinateur physique (également appelé « nœud d’unité d’échelle ») sur un système intégré Azure Stack. Les étapes de remplacement de nœud d’unité d’échelle varient en fonction de votre fournisseur de matériel OEM. Pour obtenir des instructions détaillées propres à votre système, consultez la documentation FRU (Field Replaceable Unit) de votre fournisseur.

L’organigramme suivant illustre la procédure générale de la FRU pour remplacer un nœud d’unité d’échelle dans son ensemble.

![Organigramme du processus de remplacement d’un nœud](media/azure-stack-replace-node/replacenodeflow.png)

* Cette action n’est peut-être pas requise. Elle dépend de l’état du matériel.

> [!Note]  
> Si l’opération d’arrêt échoue, il est recommandé d’utiliser l’opération de vidage suivie de l’opération d’arrêt. Pour plus d’informations, consultez les opérations de nœud disponibles.  

## <a name="review-alert-information"></a>Examiner les informations sur l’alerte

Si un nœud d’unité d’échelle est arrêté, vous recevrez les alertes critiques suivantes :

- Nœud non connecté au contrôleur de réseau
- Nœud inaccessible pour la sélection élective d’ordinateur virtuel
- Nœud d’unité d’échelle hors ligne

![Liste des alertes en cas d’arrêt d’unité d’échelle](media/azure-stack-replace-node/nodedownalerts.png)

Si vous ouvrez l’alerte **Nœud d’unité d’échelle hors ligne**, la description de l’alerte contient le nœud d’unité d’échelle qui est inaccessible. Vous pouvez également recevoir des alertes supplémentaires dans la solution d’analyse propre à l’OEM qui est en cours d’exécution sur l’ordinateur hôte du cycle de vie du matériel.

![Détails de l’alerte de nœud hors ligne](media/azure-stack-replace-node/nodeoffline.png)

## <a name="scale-unit-node-replacement-process"></a>Procédure de remplacement d’un nœud d’unité d’échelle

Les étapes suivantes fournissent une vue d’ensemble de la procédure de remplacement d’un nœud d’unité d’échelle. Pour obtenir des instructions détaillées propres à votre système, consultez la documentation FRU (Field Replaceable Unit) de votre fournisseur OEM. Ne suivez pas ces étapes sans consulter la documentation fournie par votre fournisseur OEM.

1. Utilisez l’action **Arrêter** pour arrêter normalement le nœud d’unité d’échelle. Cette action n’est peut-être pas requise. Elle dépend de l’état du matériel. 

2. Dans le cas peu probable où l’action d’arrêt ne fonctionnerait pas, utilisez l’action [Vider](azure-stack-node-actions.md#drain) pour placer le nœud d’unité d’échelle en mode maintenance. Cette action n’est peut-être pas requise. Elle dépend de l’état du matériel.

   > [!NOTE]  
   > Dans tous les cas, un seul nœud peut être désactivé et mis hors tension en même temps sans endommager les espaces de stockage direct (S2D).

3. Une fois le nœud d’unité d’échelle en mode maintenance, utilisez l’action [Arrêter](azure-stack-node-actions.md#stop). Cette action n’est peut-être pas requise. Elle dépend de l’état du matériel.

   > [!NOTE]  
   > Dans le cas peu probable où la mise hors tension ne fonctionnerait pas, utilisez l’interface web du contrôleur de gestion de la carte de base (BMC).

4. Remplacez l’ordinateur physique. En règle générale, votre fournisseur de matériel OEM se charge de cette opération.
5. Utilisez l’action [Réparer](azure-stack-node-actions.md#repair) pour ajouter le nouvel ordinateur physique à l’unité d’échelle.
6. Utilisez le point de terminaison privilégié pour [vérifier l’état de réparation du disque virtuel](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair). Avec de nouveaux lecteurs de données, une opération de réparation de stockage complète peut prendre plusieurs heures en fonction de la charge du système et de l’espace utilisé.
7. Une fois la réparation terminée, vérifiez que toutes les alertes actives ont été automatiquement fermées.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur le remplacement d’un disque physique quand le système est sous tension, consultez [Remplacer un disque](azure-stack-replace-disk.md). 
- Pour plus d’informations sur le remplacement d’un composant matériel qui nécessite la mise hors tension du système, consultez [Remplacer un composant matériel](azure-stack-replace-component.md).
