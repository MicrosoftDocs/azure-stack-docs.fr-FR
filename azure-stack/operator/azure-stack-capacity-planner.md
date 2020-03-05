---
title: Azure Stack Hub Capacity Planner
description: Découvrez comment utiliser la feuille de calcul Azure Stack Hub Capacity Planner pour les déploiements.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 05/31/2019
ms.author: inhenkel
ms.reviewer: prchint
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: 51b9530fdd67826616f6117c3c95dbe48b45303f
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77701635"
---
# <a name="azure-stack-hub-capacity-planner"></a>Azure Stack Hub Capacity Planner

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

Cette feuille de calcul propose jusqu’à cinq exemples de définitions de matériel. Modifiez les détails pour qu’ils correspondent aux configurations système envisagées.

### <a name="hardware-selections-provided-by-authorized-hardware-partners"></a>Sélections de matériel proposées par les partenaires autorisés

Azure Stack Hub est fourni en tant que système intégré avec des logiciels installés par des partenaires de solutions. Ces derniers fournissent leurs propres versions officielles des outils de planification de capacité Azure Stack Hub. Utilisez ces outils pour déterminer définitivement la capacité des solutions.

### <a name="multiple-ways-to-model-computing-resources"></a>Multiples façons de modéliser les ressources de calcul

La modélisation des ressources dans Azure Stack Hub Capacity Planner varie selon les différentes tailles des machines virtuelles Azure Stack Hub. La plage des tailles de machines virtuelles s’étend de la plus petite, Basic 0, à la plus grande, Standard_Fsv2. Vous pouvez modéliser des allocations des ressources de calcul de deux manières différentes :

- Sélectionnez une offre de matériel spécifique pour voir quelles combinaisons de ressources correspondent.

- Créez une combinaison spécifique d’allocations de machine virtuelle, puis laissez la calculatrice de ressources Azure indiquer les références SKU matérielles disponibles qui peuvent prendre en charge cette configuration de machine virtuelle.

Cet outil permet d’allouer des ressources de machine virtuelle de deux façons : soit en tant que collection simple d’allocations de ressources de machine virtuelle, soit en tant que collection de jusqu’à six configurations de charge de travail. Chaque configuration de charge de travail peut contenir une allocation singulière de ressources de machine virtuelle disponibles. Les sections suivantes contiennent des instructions pas à pas pour créer et utiliser chacun de ces modèles d’allocation. Sur cette feuille de calcul, seules sont modifiables les valeurs contenues dans les cellules dont l’arrière-plan n’est pas ombré, ou dans les listes déroulantes de références SKU. Toute modification d’une cellule ombrée risque de perturber les calculs de ressources.

## <a name="definebyvmfootprint-instructions"></a>Instructions concernant l’utilisation de l’onglet DefineByVMFootprint

Pour créer un modèle utilisant une seule collection de diverses tailles et quantités de machines virtuelles, sélectionnez l’onglet **DefineByVMFootprint**, puis effectuez les étapes suivantes :

1. En haut à droite de cette feuille de calcul, utilisez les contrôles de zone de liste déroulante fournis pour sélectionner un nombre initial de serveurs (de 4 à 16) à installer dans chaque système matériel (référence SKU). Ce nombre de serveurs peut être modifié à tout moment pendant le processus de modélisation pour voir comment cela affecte les ressources globales disponibles pour votre modèle d’allocation de ressources.
2. Si vous souhaitez modéliser diverses allocations de ressources de machine virtuelle pour une configuration matérielle spécifique, accédez à la zone de liste de déroulante bleue juste sous l’étiquette **Référence SKU actuelle** dans l’angle supérieur droit de la page. Déroulez cette zone de liste, puis sélectionnez la référence SKU du matériel de votre choix.
3. Vous êtes maintenant prêt à commencer à ajouter des machines virtuelles de différentes tailles à votre modèle. Pour inclure un type particulier de machine virtuelle, entrez une quantité dans la zone au contour bleu à gauche de cette entrée de machine virtuelle.

   > [!NOTE]
   > Le stockage total de machine virtuelle fait référence à la capacité totale du disque de données de la machine virtuelle (nombre de disques pris en charge multiplié par la capacité maximale d’un disque (1 To)). Sur la base des indicateurs de configuration, nous avons renseigné la table Configurations de stockage disponibles afin de vous permettre de choisir votre niveau souhaité de ressource de stockage pour chaque machine virtuelle Azure Stack Hub. Notez toutefois que vous pouvez ajouter ou modifier le tableau Configurations de stockage disponibles si nécessaire. <br><br>Chaque machine virtuelle démarre avec un volume de stockage temporaire attribué initialement. Pour adapter l’approvisionnement dynamique de volume de stockage temporaire, vous pouvez le modifier dans le menu déroulant, ainsi que la quantité maximale autorisée de stockage temporaire.

4. Lorsque vous ajoutez des machines virtuelles, des graphiques s’affichent, qui reflètent le changement des ressources en références SKU disponibles. Ces graphiques vous permettent de voir l’effet des ajouts de différentes tailles et quantités de machines virtuelles durant le processus de modélisation. Une autre façon de suivre l’effet les modifications consiste à regarder les chiffres des volumes **consommés** et **encore disponibles** répertoriés directement dans la liste des machines virtuelles disponibles. Ces chiffres sont des valeurs estimées sur la base de la référence SKU de matériel actuellement sélectionnée.
5. Lorsque vous avez créé votre ensemble de machines virtuelles, vous pouvez trouver la référence SKU matérielle suggérée en sélectionnant **Référence SKU suggérée**. Ce bouton se trouve en haut à droite de la page, juste en dessous de l’étiquette **Référence SKU actuelle**. Ce bouton vous permet ensuite de modifier vos configurations de machine virtuelle et de voir quel matériel prend en charge chaque configuration.

## <a name="definebyworkloadfootprint-instructions"></a>Instructions concernant l’utilisation de l’onglet DefineByWorkloadFootprint

Pour créer un modèle utilisant une collection de charges de travail Azure Stack Hub, sélectionnez l’onglet **DefineByWorkloadFootprint** et suivez cette séquence d’étapes. Vous créez des charges de travail Azure Stack Hub à l’aide des ressources de machine virtuelle disponibles.

> [!TIP]
> Pour changer la taille de stockage fournie pour une machine virtuelle Azure Stack Hub, consultez la remarque à l’étape 3 de la section précédente.

1. En haut à droite de cette feuille de calcul, utilisez les contrôles de zone de liste déroulante fournis pour sélectionner un nombre initial de serveurs (de 4 à 16) à installer dans chaque système matériel (référence SKU).
2. Si vous souhaitez modéliser diverses allocations de ressources de machine virtuelle pour une configuration matérielle spécifique, accédez à la zone de liste de déroulante bleue juste sous l’étiquette **Référence SKU actuelle** dans l’angle supérieur droit de la page. Déroulez cette zone de liste, puis sélectionnez la référence SKU du matériel de votre choix.
3. Sélectionnez la taille de stockage appropriée pour chaque machine virtuelle Azure Stack Hub de votre choix sur la page **DefineByVMFootprint**. Ce processus est décrit à l’étape 3 de la section précédente. La taille de stockage par machine virtuelle est définie dans la feuille DefineByVMFootprint.
4. En partant de la partie supérieure gauche de la page **DefineByWorkloadFootprint**, créez des configurations pour jusqu’à six types de charges de travail. Entrez la quantité de chaque type de machine virtuelle contenue dans chaque charge de travail. Pour ce faire, vous devez entrer des valeurs numériques dans la colonne située juste sous le nom de la charge de travail concernée. Vous pouvez modifier les noms des charges de travail afin de refléter les types de charges de travail pris en charge par cette configuration particulière.
5. Vous pouvez inclure une quantité spécifique de chaque type de charge de travail en entrant une valeur au bas de cette colonne, directement sous l’étiquette **Quantité**.
6. Lorsque vous avez créé les types de charge de travail et les quantités, sélectionnez **Référence SKU suggérée** dans l’angle supérieur droit de la page, directement sous l’étiquette **Référence SKU actuelle**. La plus petite référence SKU disposant de suffisamment de ressources pour prendre en charge cette configuration globale de charges de travail s’affiche.
7. Vous pouvez appliquer une modélisation supplémentaire en modifiant le nombre de serveurs sélectionnés pour une référence SKU de matériel, ou en modifiant les allocations ou quantités de machines virtuelles dans vos configurations de charge de travail. Les graphiques associés reflètent immédiatement la manière dont vos modifications affectent la consommation globale de ressources.
8. Lorsque vous êtes satisfait de vos modifications, sélectionnez **Référence SKU suggérée** pour afficher la référence SKU suggérée pour votre nouvelle configuration. Vous pouvez également sélectionner le menu déroulant pour sélectionner votre référence SKU souhaitée.

## <a name="next-steps"></a>Étapes suivantes

Découvrez les [considérations relatives à l’intégration au centre de données pour Azure Stack Hub](azure-stack-datacenter-integration.md).
