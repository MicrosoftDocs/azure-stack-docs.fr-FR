---
title: Remplacer un composant matériel sur un nœud d’unité d’échelle Azure Stack | Microsoft Docs
description: Découvrez comment remplacer un composant matériel sur un système intégré Azure Stack.
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
ms.openlocfilehash: ea316ebdba51ea80fcd02382023ccb46447a8cc0
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2019
ms.locfileid: "64984822"
---
# <a name="replace-a-hardware-component-on-an-azure-stack-scale-unit-node"></a>Remplacer un composant matériel sur un nœud d’unité d’échelle Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Cet article décrit le processus général de remplacement des composants matériels qui ne sont pas échangeables à chaud. Les étapes de remplacement réelles varient en fonction du revendeur de votre matériel OEM. Pour obtenir des instructions détaillées propres à votre système intégré Azure Stack, consultez la documentation FRU (Field Replaceable Unit) de votre fournisseur.

Les composants non échangeables à chaud sont les suivants :

- UC*
- Mémoire*
- Contrôleur de gestion de la carte mère/carte de base (BMC)/carte vidéo
- Contrôleur de disque/adaptateur ou carte de bus hôte (HBA)/fond de panier
- Carte réseau (NIC)
- Disque de système d’exploitation*
- Lecteurs de données (ne prenant pas en charge l’échange à chaud, tels que les cartes complémentaires PCI-e)*

*Ces composants peuvent éventuellement supporter un échange à chaud, mais cela dépend de la manière dont le fournisseur les implémente. Pour obtenir des instructions détaillées, consultez la documentation FRU de votre fabricant OEM.

L’organigramme suivant illustre le processus FRU général de remplacement d’un composant matériel non échangeable à chaud.

![Organigramme montrant le flux de remplacement de composant](media/azure-stack-replace-component/replacecomponentflow.PNG)

* Cette action n’est peut-être pas requise. Elle dépend de l’état du matériel.

** Votre fournisseur de matériel OEM peut ou non échanger le composant et mettre à jour le microprogramme. Cela dépend de votre contrat de support.

## <a name="review-alert-information"></a>Examiner les informations sur l’alerte

Le système de contrôle de l’intégrité et de surveillance d’Azure Stack surveille l’intégrité des cartes réseau et des lecteurs de données contrôlés par les espaces de stockage direct. Il ne surveille pas d’autres composants matériels. Pour tous les autres composants matériels, des alertes sont générées dans la solution de supervision du matériel spécifique du fournisseur, qui s’exécute sur l’hôte de cycle de vie du matériel.  

## <a name="component-replacement-process"></a>Processus de remplacement de composant

Les étapes suivantes fournissent une vue d’ensemble du processus de remplacement de composant. Ne suivez pas ces étapes sans vous référer à la documentation FRU fournie par le fabricant OEM.

1. Utilisez l’action Arrêter pour arrêter normalement le nœud d’unité d’échelle. Cette action n’est peut-être pas requise. Elle dépend de l’état du matériel.

2. Dans le cas peu probable où l’action d’arrêt ne fonctionnerait pas, utilisez l’action [Vider](azure-stack-node-actions.md#drain) pour placer le nœud d’unité d’échelle en mode maintenance. Cette action n’est peut-être pas requise. Elle dépend de l’état du matériel.

   > [!NOTE]  
   > Dans tous les cas, un seul nœud peut être désactivé et mis hors tension en même temps sans endommager les espaces de stockage direct (S2D).

3. Une fois le nœud d’unité d’échelle en mode maintenance, utilisez l’action [Mettre hors tension](azure-stack-node-actions.md#scale-unit-node-actions). Cette action n’est peut-être pas requise. Elle dépend de l’état du matériel.

   > [!NOTE]  
   > Dans le cas peu probable où la mise hors tension ne fonctionnerait pas, utilisez l’interface web du contrôleur de gestion de la carte de base (BMC).

4. Remplacez le composant matériel endommagé. Votre fournisseur de matériel OEM peut ou non échanger le composant. Cela dépend de votre contrat de support technique.  
5. Mettez à jour le microprogramme. Suivez le processus de mise à jour du microprogramme spécifique de votre fournisseur à l’aide de l’hôte de cycle de vie du matériel pour vous assurer que le niveau de microprogramme approuvé est appliqué au composant matériel remplacé. Votre fournisseur de matériel OEM peut ou non effectuer cette opération. Cela dépend de votre contrat de support technique.  
6. Utilisez l’action [Réparation](azure-stack-node-actions.md#scale-unit-node-actions) afin de ramener le nœud d’unité d’échelle dans l’unité d’échelle.
7. Utilisez le point de terminaison privilégié pour [vérifier l’état de réparation du disque virtuel](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair). Avec de nouveaux lecteurs de données, une opération de réparation de stockage complète peut prendre plusieurs heures en fonction de la charge du système et de l’espace utilisé.
8. Une fois la réparation terminée, vérifiez que toutes les alertes actives ont été automatiquement fermées.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur le remplacement d’un disque physique échangeable à chaud, voir [Remplacer un disque](azure-stack-replace-disk.md).
- Pour plus d’informations sur le remplacement d’un nœud physique, voir [Remplacer un nœud d’unité d’échelle](azure-stack-replace-node.md).
