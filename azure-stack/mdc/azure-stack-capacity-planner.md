---
title: Azure Stack Hub Capacity Planner | Microsoft Docs
description: Découvrez comment utiliser Azure Stack Hub Capacity Planner pour afficher les allocations de ressources de calcul pour les déploiements Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: prchint
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: 6928c301ae2c5985a9db52bff109c874024327cb
ms.sourcegitcommit: 5fbc60b65d27c916ded7a95ba4102328d550c7e5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97598281"
---
# <a name="azure-stack-hub-capacity-planner---modular-data-center-mdc"></a>Azure Stack Hub Capacity Planner - Modular Data Center (MDC)

Azure Stack Hub Capacity Planner est une feuille de calcul qui montre comment différentes allocations de ressources de calcul conviennent à une sélection d’offres de matériel. 

## <a name="worksheet-descriptions"></a>Descriptions de feuilles de calcul
Le tableau suivant décrit chaque feuille de calcul dans Azure Stack Hub Capacity Planner, que vous pouvez télécharger depuis [https://aka.ms/azstackcapacityplanner](https://aka.ms/azstackcapacityplanner). 

|Nom de la feuille de calcul|Description|
|-----|-----|
|Version-Disclaimer|Finalité de la calculatrice, numéro de version et date de publication.|
|Instructions|Instructions détaillées pour modéliser la planification de la capacité pour une collection de machines virtuelles.|
|DefinedSolutionSKUs|Tableau contenant jusqu’à cinq définitions de matériel. Les entrées sont des exemples. Modifiez les détails pour qu’ils correspondent aux configurations système envisagées.|
|DefineByVMFootprint|Recherchez la référence SKU du matériel approprié en comparant les configurations avec différentes tailles et quantités de machines virtuelles.|
|DefineByWorkloadFootprint|Trouvez la référence SKU de matériel appropriée en créant une collection de charges de travail Azure Stack Hub.|
|  |  |

## <a name="definedsolutionskus-instructions"></a>Instructions DefinedSolutionSKUs
Cette feuille de calcul contient jusqu'à cinq exemples de définitions de matériel. Modifiez les détails pour qu’ils correspondent aux configurations système envisagées.

### <a name="hardware-selections-provided-by-authorized-hardware-partners"></a>Sélections de matériel proposées par les partenaires autorisés
Azure Stack Hub est fourni en tant que système intégré avec des logiciels installés par des partenaires de solutions. Ces derniers fournissent leurs propres versions officielles des outils de planification de capacité Azure Stack Hub. Utilisez ces outils pour déterminer définitivement la capacité des solutions.

### <a name="multiple-ways-to-model-computing-resources"></a>Multiples façons de modéliser les ressources de calcul
La modélisation des ressources dans Azure Stack Hub Capacity Planner varie selon les différentes tailles des machines virtuelles Azure Stack Hub. La plage des tailles de machines virtuelles s’étend de la plus petite, Basic 0, à la plus grande, Standard_Fsv2. Vous pouvez modéliser des allocations des ressources de calcul de deux manières différentes :

- Sélectionnez une offre de matériel spécifique pour voir quelles combinaisons de ressources correspondent. 

- Créez une combinaison spécifique d’allocations de machine virtuelle, puis laissez la calculatrice de ressources Azure indiquer les références SKU matérielles disponibles capables de prendre en charge cette configuration de machine virtuelle.

Cet outil permet d’allouer des ressources de machine virtuelle de deux façons : soit en tant que collection simple d’allocations de ressources de machine virtuelle, soit en tant que collection de jusqu’à six configurations de charge de travail. Chaque configuration de charge de travail peut contenir une allocation singulière de ressources de machine virtuelle disponibles. Les sections suivantes contiennent des instructions pas à pas pour créer et utiliser chacun de ces modèles d’allocation. Seules les valeurs contenues dans des cellules dont l’arrière-plan n’est pas ombré, ou dans des listes déroulantes de références SKU sur cette feuille de calcul peuvent être modifiées. Toute modification d’une cellule ombrée risque de perturber les calculs de ressources.


## <a name="definebyvmfootprint-instructions"></a>Instructions concernant l’utilisation de l’onglet DefineByVMFootprint
Pour créer un modèle utilisant une collection simple de diverses tailles et quantités de machines virtuelles, sélectionnez l’onglet **DefineByVMFootprint**, puis effectuez les étapes suivantes :

1. En haut à droite de cette feuille de calcul, utilisez les contrôles de zone de liste déroulante fournis pour sélectionner un nombre initial de serveurs (de 4 à 16) à installer dans chaque système matériel (référence SKU). Ce nombre de serveurs peut être modifié à tout moment pendant le processus de modélisation pour voir comment cela affecte les ressources globales disponibles pour votre modèle d’allocation de ressources.
2. Si vous souhaitez modéliser diverses allocations de ressources de machine virtuelle pour une configuration matérielle spécifique, accédez à la zone de liste de déroulante bleue juste sous l’étiquette **Référence SKU actuelle** dans l’angle supérieur droit de la page. Déroulez cette zone de liste, puis sélectionnez la référence SKU du matériel de votre choix.
3. Vous êtes maintenant prêt à commencer à ajouter des machines virtuelles de différentes tailles à votre modèle. Pour inclure un type particulier de machine virtuelle, entrez une quantité dans la zone au contour bleu à gauche de cette entrée de machine virtuelle.

   > [!NOTE]
   > Le stockage total de machine virtuelle fait référence à la capacité totale du disque de données de la machine virtuelle (nombre de disques pris en charge multiplié par la capacité maximale d’un disque (1 To)). Sur la base des indicateurs de configuration, nous avons renseigné la table Configurations de stockage disponibles afin de vous permettre de choisir votre niveau souhaité de ressource de stockage pour chaque machine virtuelle Azure Stack Hub. Notez toutefois que vous pouvez ajouter ou modifier le tableau Configurations de stockage disponibles si nécessaire.<br><br>Chaque machine virtuelle démarre avec un volume de stockage temporaire attribué initialement. Pour adapter l’approvisionnement dynamique de volume de stockage temporaire, vous pouvez le modifier dans le menu déroulant, ainsi que la quantité maximale autorisée de stockage temporaire.

4. Lorsque vous ajoutez des machines virtuelles, des graphiques s’affichent, qui reflètent le changement des ressources en références SKU disponibles. Cela vous permet de voir l’effet des ajouts de différentes tailles et quantités de machines virtuelles durant le processus de modélisation. Une autre façon de suivre l’effet les modifications consiste à regarder les chiffres des volumes **consommés** et **encore disponibles** répertoriés directement dans la liste des machines virtuelles disponibles. Ces chiffres sont des valeurs estimées sur la base de la référence SKU de matériel actuellement sélectionnée.
5. Une fois que vous avez créé votre ensemble de machines virtuelles, vous pouvez trouver la référence SKU de matériel suggérée en sélectionnant **Référence SKU suggérée** dans l’angle supérieur droit de la page, juste sous l’étiquette **Référence SKU actuelle**. Ce bouton vous permet ensuite de modifier vos configurations de machine virtuelle et de voir quel matériel prend en charge chaque configuration.


## <a name="definebyworkloadfootprint-instructions"></a>Instructions concernant l’utilisation de l’onglet DefineByWorkloadFootprint
Pour créer un modèle utilisant une collection de charges de travail Azure Stack Hub, sélectionnez l’onglet **DefineByWorkloadFootprint**, puis procédez comme suit. Vous créez des charges de travail Azure Stack Hub à l’aide des ressources de machine virtuelle disponibles.   

> [!TIP]
> Pour modifier la taille de stockage fournie pour une machine virtuelle Azure Stack Hub, consultez la remarque à l’étape 3 de la section précédente.

1. En haut à droite de cette feuille de calcul, utilisez les contrôles de zone de liste déroulante fournis pour sélectionner un nombre initial de serveurs (de 4 à 16) à installer dans chaque système matériel (référence SKU).
2. Si vous souhaitez modéliser diverses allocations de ressources de machine virtuelle pour une configuration matérielle spécifique, accédez à la zone de liste de déroulante bleue juste sous l’étiquette **Référence SKU actuelle** dans l’angle supérieur droit de la page. Déroulez cette zone de liste, puis sélectionnez la référence SKU du matériel de votre choix.
3. Sélectionnez la taille de stockage appropriée pour chaque machine virtuelle Azure Stack Hub de votre choix sur la page **DefineByVMFootprint**, comme décrit à l’étape 3 de la section précédente. La taille de stockage par machine virtuelle est définie dans la feuille DefineByVMFootprint.
4. En partant de la partie supérieure gauche de la page **DefineByWorkloadFootprint**, créez des configurations pour jusqu’à six types de charges de travail. Entrez la quantité de chaque type de machine virtuelle contenue dans chaque charge de travail. Pour ce faire, vous devez entrer des valeurs numériques dans la colonne située juste sous le nom de la charge de travail concernée. Vous pouvez modifier les noms des charges de travail afin de refléter les types de charges de travail pris en charge par cette configuration particulière.
5. Vous pouvez inclure une quantité spécifique de chaque type de charge de travail en entrant une valeur au bas de cette colonne, directement sous l’étiquette **Quantité**.
6. Lorsque vous avez créé les types de charge de travail et les quantités, sélectionnez **Référence SKU suggérée** dans l’angle supérieur droit de la page, directement sous l’étiquette **Référence SKU actuelle**. Cette opération affiche la plus petite référence SKU disposant de suffisamment de ressources pour prendre en charge cette configuration globale de charges de travail.
7. Vous pouvez appliquer une modélisation supplémentaire en modifiant le nombre de serveurs sélectionnés pour une référence SKU de matériel, ou en modifiant les allocations ou quantités de machines virtuelles dans vos configurations de charge de travail. Les graphiques associés reflètent immédiatement la manière dont vos modifications affectent la consommation globale de ressources.
8. Lorsque vous êtes satisfait de vos modifications, sélectionnez **Référence SKU suggérée** pour afficher la référence SKU suggérée pour votre nouvelle configuration. Vous pouvez également sélectionner le menu déroulant pour sélectionner votre référence SKU souhaitée.

## <a name="next-steps"></a>Étapes suivantes
Découvrez les [considérations relatives à l’intégration au centre de données pour Azure Stack Hub](../operator/azure-stack-datacenter-integration.md).
