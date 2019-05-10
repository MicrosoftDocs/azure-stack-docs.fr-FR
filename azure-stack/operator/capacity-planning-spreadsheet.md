---
title: Tableur de planification de la capacité pour Azure Stack | Microsoft Docs
description: Découvrez le tableur de planification de la capacité pour les déploiements Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/06/2019
ms.author: jeffgilb
ms.reviewer: prchint
ms.lastreviewed: 12/11/2018
ms.openlocfilehash: da0740cd416af26f3e9c284c2ff405d6e06ffcb7
ms.sourcegitcommit: ccd86bd0862c45de1f6a4993f783ea2e186c187a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65172632"
---
# <a name="azure-stack-capacity-planner"></a>Azure Stack Capacity Planner
Azure Stack Capacity Planner est un tableur utilisé pour la planification de la capacité des ressources Azure Stack. Un planificateur de capacité vous permet de concevoir diverses allocations de ressources de calcul afin de voir comment celles-ci pourraient s’inscrire dans une offre de matériel. Vous trouverez ci-dessous des instructions détaillées pour l’utilisation de la calculatrice Azure Stack.

## <a name="worksheet-descriptions"></a>Descriptions de feuilles de calcul
Voici un bref récapitulatif des feuilles de calcul contenues dans le tableur Azure Stack Capacity Planner, que vous pouvez télécharger à partir de la page [https://aka.ms/azstackcapacityplanner](https://aka.ms/azstackcapacityplanner) :

|Nom de l'onglet|Description|
|-----|-----|
|Version-Disclaimer|Brève présentation de la finalité de la calculatrice, numéro de version et date de publication.|
|Instructions|Fournit des instructions détaillées pour l’utilisation d’Azure Stack Capacity Planner.|
|DefinedSolutionSKUs|Tableau multicolonne contenant jusqu’à cinq définitions de matériel. Les entrées figurant dans cette feuille sont des exemples. L’objectif est que l’utilisateur modifie des détails afin de trouver des configurations système envisageables pour une utilisation ou un achat.|
|DefineByVMFootprint|Trouvez la référence (SKU) de matériel appropriée en créant une collection de différentes tailles et quantités de machines virtuelles.|
|DefineByWorkloadFootprint|Trouvez la référence (SKU) de matériel appropriée en créant une collection de charges de travail Azure.|
|  |  |

## <a name="definedsolutionskus-instructions"></a>Instructions DefinedSolutionSKUs
Cette feuille de calcul contient jusqu'à cinq exemples de définitions de matériel. Modifiez des détails afin de trouver les configurations système envisageables pour une utilisation ou un achat.

### <a name="hardware-selections-provided-by-authorized-hardware-partners"></a>Sélections de matériel proposées par des partenaires autorisés
Azure Stack est fourni en tant que système intégré avec des logiciels installés par des partenaires de solutions. Ces partenaires ont leurs propres versions officielles des outils de planification de la capacité Azure Stack qui doivent être utilisés pour finaliser des discussions autour de la capacité des solutions.

### <a name="multiple-ways-to-model-computing-resources"></a>Multiples façons de modéliser les ressources de calcul
Les blocs de construction de base utilisés pour la modélisation des ressources à l’intérieur du planificateur Azure Stack sont les différentes tailles de machines virtuelles Azure Stack. La plage des tailles de machines virtuelles s’étend de la plus petite, « Basic 0 », à la plus grande , « Standard_Fsv2 ». Selon vos besoins, diverses quantités de machines virtuelles différentes peuvent être utilisées pour créer des allocations de ressources de calcul à l’aide de cet outil de deux façons.

1. L’utilisateur du planificateur sélectionne une offre de matériel, puis regarde quelles combinaisons de ressources pourraient s’inscrire dans celle-ci. 

2. Il crée une combinaison d’allocations de machine virtuelle, puis laisse la calculatrice de ressources Azure indiquer les références SKU matérielles disponibles capables de prendre en charge cette configuration de machine virtuelle.

Cet outil permet d’allouer des ressources de machine virtuelle de deux façons : soit en tant que collection simple d’allocations de ressources de machine virtuelle, soit en tant que collection de jusqu’à six configurations de charge de travail. Chaque configuration de charge de travail peut contenir une allocation singulière de ressources de machine virtuelle disponibles. Vous trouverez ci-dessous des informations détaillées sur la création et l’utilisation de chacun de ces modèles d’allocation. Seules les valeurs contenues dans des cellules dont l’arrière-plan n’est pas ombré, ou dans des listes déroulantes de références SKU sur cette feuille de calcul peuvent être modifiées. Toute modification de cellule ombrée risque de perturber les calculs de ressources.


## <a name="definebyvmfootprint-instructions"></a>Instructions concernant l’utilisation de l’onglet DefineByVMFootprint
Pour créer un modèle utilisant une collection simple de diverses tailles et quantités de machines virtuelles, sélectionnez l’onglet « DefineByVMFootprint », puis procédez comme suit.

1. En haut à droite de cette feuille de calcul, utilisez les contrôles de zone de liste déroulante fournis pour sélectionner un nombre initial de serveurs (de 4 à 16) à installer dans chaque système matériel (référence SKU). Ce nombre de serveurs peut être modifié à tout moment pendant le processus de modélisation pour voir comment cela affecte les ressources globales disponibles pour votre modèle d’allocation de ressources.
2. Si vous souhaitez modéliser diverses allocations de ressources de machine virtuelle pour une configuration matérielle spécifique, accédez à la zone de liste de déroulante bleue juste sous l’étiquette « Current SKU » (Référence SKU actuelle) dans l’angle supérieur droit de la page. Déroulez cette zone de liste, puis sélectionnez la référence SKU du matériel de votre choix.
3. Vous êtes maintenant prêt à commencer à ajouter des machines virtuelles de différentes tailles à votre modèle. Pour inclure un type particulier de machine virtuelle, entrez une quantité dans la zone au contour bleu à gauche de cette entrée de machine virtuelle.

   > [!NOTE]
   > Le stockage total de machine virtuelle fait référence à la capacité totale du disque de données de la machine virtuelle (nombre de disques pris en charge * la capacité maximale d’un disque (1 To)). Sur la base des indicateurs de configuration, nous avons rempli la table Configurations de stockage disponibles afin que vous puissiez choisir votre niveau souhaité de ressource de stockage pour chaque machine virtuelle Azure Stack. Notez toutefois que vous pouvez ajouter ou modifier le tableau Configurations de stockage disponibles si nécessaire.<br><br>Chaque machine virtuelle démarre avec un volume de stockage temporaire attribué initialement. Pour adapter l’approvisionnement dynamique de volume de stockage temporaire, celui-ci peut être modifié dans le menu déroulant, ainsi que la quantité maximale autorisée de stockage temporaire.

4. Lorsque vous ajoutez des machines virtuelles, des graphiques s’affichent, qui reflètent le changement des ressources en références SKU disponibles. Cela vous permet de voir l’effet des ajouts de différentes tailles et quantités de machines virtuelles durant le processus de modélisation. Une autre façon de suivre l’effet les modifications consiste à regarder les chiffres des volumes consommés et encore disponibles répertoriés directement dans la liste des machines virtuelles disponibles. Ces chiffres sont des valeurs estimées sur la base de la référence SKU de matériel actuellement sélectionnée.
5. Une fois que vous avez créé votre ensemble de machines virtuelles, vous pouvez trouver la référence SKU de matériel suggérée en cliquant sur le bouton « Référence SKU suggérée » dans l’angle supérieur droit de la page, juste sous l’étiquette « Current SKU » (Référence SKU actuelle). Ce bouton vous permet ensuite de modifier vos configurations de machine virtuelle et de voir quel matériel prend en charge chaque configuration.


## <a name="definebyworkloadfootprint-instructions"></a>Instructions concernant l’utilisation de l’onglet DefineByWorkloadFootprint
Pour créer un modèle utilisant une collection de charges de travail Azure Stack, sélectionnez l’onglet « DefineByWorkloadFootprint », puis procédez comme suit. Les charges de travail Azure Stack sont créées à l’aide de ressources de machine virtuelle disponibles.   

> [!TIP]
> Pour savoir comment modifier la taille de stockage fournie pour une machine virtuelle Azure Stack, consultez la remarque à l’étape 3 de la section précédente.

1. En haut à droite de cette page, utilisez les contrôles de zone de liste déroulante fournis pour sélectionner un nombre initial de serveurs (de 4 à 16) à installer dans chaque système matériel (référence SKU).
2. Si vous souhaitez modéliser diverses allocations de ressources de machine virtuelle pour une configuration matérielle spécifique, accédez à la zone de liste de déroulante bleue juste sous l’étiquette « Current SKU » (Référence SKU actuelle) dans l’angle supérieur droit de la page. Déroulez cette zone de liste, puis sélectionnez la référence SKU du matériel de votre choix.
3. Sélectionnez la taille de stockage appropriée pour chaque machine virtuelle Azure Stack de votre choix sur la page DefineByVMFootprint, comme décrit ci-dessus à l’étape trois des Instructions concernant l’utilisation de l’onglet DefineByWorkloadFootprint. La taille de stockage par machine virtuelle est définie dans la feuille DefineByVMFootprint.
4. En partant de la partie supérieure gauche de la page DefineByWorkloadFootprint, créez des configurations pour jusqu’à six types de charges de travail en entrant la quantité de chaque type de machine virtuelle contenue dans chaque charge de travail. Pour ce faire, vous devez entrer des valeurs numériques dans la colonne située juste sous le nom de la charge de travail concernée. Vous pouvez modifier les noms des charges de travail afin de refléter les types de charges de travail pris en charge par cette configuration particulière.
5. Vous pouvez inclure une quantité spécifique de chaque type de charge de travail en entrant une valeur au bas de cette colonne, directement sous l’étiquette « Quantité ».
6. Une fois les types et quantités de charges de travail créés, lorsque vous cliquez sur le bouton de référence SKU suggérée dans l’angle supérieur droit de la page, juste sous l’étiquette « Current SKU » (Référence SKU actuelle), la référence SKU la plus petite offrant suffisamment de ressources pour prendre en charge cette configuration globale des charges de travail s’affiche.
7. Une modélisation supplémentaire peut être accomplie en modifiant le nombre de serveurs sélectionnés pour une référence SKU de matériel, ou en modifiant les allocations ou quantités de machines virtuelles dans vos configurations de charge de travail. Les graphiques associés reflètent immédiatement la manière dont vos modifications affectent la consommation globale de ressources.
8. Lorsque vous êtes satisfait de vos modifications, un nouveau clic sur le bouton « Suggested SKU » a pour effet d’afficher la référence SKU suggérée pour votre nouvelle configuration.


## <a name="next-steps"></a>Étapes suivantes
Découvrez les [considérations relatives à l’intégration au centre de données pour Azure Stack](azure-stack-datacenter-integration.md)
