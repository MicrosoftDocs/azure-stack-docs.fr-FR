---
title: Machine virtuelle de processeur graphique (GPU) sur Azure Stack Hub
description: Référence pour l’environnement GPU dans Azure Stack Hub.
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: reference
ms.date: 10/20/2020
ms.reviewer: kivenkat
ms.lastreviewed: 07/07/2020
ms.openlocfilehash: 9289b1c2ae3119a03898e2d9c361bde4976a16f7
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94546920"
---
# <a name="graphics-processing-unit-gpu-virtual-machine-vm-on-azure-stack-hub"></a>Machine virtuelle du processeur graphique (GPU) sur Azure Stack Hub

*S’applique à : systèmes intégrés Azure Stack*

Cet article décrit les modèles de processeurs graphiques (GPU) pris en charge sur un système multi-nœuds Azure Stack Hub. Vous y trouverez également des instructions sur l’installation des pilotes utilisés avec les GPU. Le support GPU dans Azure Stack Hub permet de fournir des solutions telles que l’intelligence artificielle, la formation, l’inférence et la visualisation des données. L’accélérateur graphique AMD Radeon Instinct Mi25 peut être utilisé pour prendre en charge des applications gourmandes en graphiques, telles qu’Autodesk AutoCAD.

Vous pouvez choisir parmi trois modèles GPU dans la période de préversion publique. Ils sont disponibles dans les processeurs graphiques NVIDIA V100, NVIDIA T4 et AMD Mi25. Ces GPU physiques s’alignent sur les types de machines virtuelles de la série N Azure suivants, comme suit :
- [NCv3](/azure/virtual-machines/ncv3-series)
- [NVv4 (AMD MI25)](/azure/virtual-machines/nvv4-series)
- [NCasT4_v3](/azure/virtual-machines/nct4-v3-series)

> [!IMPORTANT]  
> Le support GPU Azure Stack Hub est actuellement disponible en préversion publique. Pour participer à la préversion, remplissez le formulaire sur [aka.ms/azurestackhubgpupreview](https://aka.ms/azurestackhubgpupreview).
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="ncv3"></a>NCv3

Les machines virtuelles de série NCv3 sont optimisées par les GPU NVIDIA Tesla V100. Les clients peuvent tirer parti de ces GPU mis à jour pour les charges de travail HPC traditionnelles telles que la modélisation de gisements, le séquençage de l’ADN, l’analyse des protéines, les simulations de Monte-Carlo, etc. 

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | GPU | Mémoire GPU : Gio | Disques de données max. | Nombre max de cartes réseau |
|---|---|---|---|---|---|---|---|---|
| Standard_NC6s_v3    | 6  | 112 | 736  | 1 | 16 | 12 | 4 |
| Standard_NC12s_v3   | 12 | 224 | 1474 | 2 | 32 | 24 | 8 |
| Standard_NC24s_v3   | 24 | 448 | 2948 | 4 | 64 | 32 | 8 |

## <a name="nvv4"></a>NVv4

Les machines virtuelles de la série NVv4 sont alimentées par des GPU [AMD Radeon Instinct MI25](https://www.amd.com/en/products/professional-graphics/instinct-MI25). Avec la série NVv4, Azure Stack Hub introduit des machines virtuelles avec des GPU partiels. Cette taille peut être utilisée pour les applications graphiques accélérées GPU et les bureaux virtuels. Les machines virtuelles NVv4 prennent actuellement en charge uniquement le système d’exploitation invité Windows. 

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | GPU | Mémoire GPU : Gio | Disques de données max. | Nombre max de cartes réseau | 
| --- | --- | --- | --- | --- | --- | --- | --- |   
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 | 

## <a name="ncast4_v3"></a>NCasT4_v3

| Taille | Processeurs virtuels | Mémoire : Gio | GPU | Mémoire GPU : Gio | Disques de données max. | Nombre max de cartes réseau | 
| --- | --- | --- | --- | --- | --- | --- |
| Standard_NC4as_T4_v3 |4 |28 | 1 | 16 | 8 | 4 | 
| Standard_NC8as_T4_v3 |8 |56 | 1 | 16 | 16 | 8 | 
| Standard_NC16as_T4_v3 |16 |112 | 1 | 16 | 32 | 8 | 
| Standard_NC64as_T4_v3 |64 |448 | 4 | 64 | 32 | 8 |

## <a name="patch-and-update-fru-behavior-of-vms"></a>Correctif et mise à jour, comportement FRU des machines virtuelles 

Les machines virtuelles GPU subissent des temps d’arrêt pendant les opérations telles que les correctifs et les mises à jour (PnU), ainsi que le remplacement de matériel (FRU) d’Azure Stack Hub. Le tableau suivant présente l’état de la machine virtuelle comme observé au cours de ces activités, ainsi que l’action manuelle que l’utilisateur peut effectuer pour que ces machines virtuelles soient à nouveau disponibles après ces opérations. 

| Opération | PnU - mise à jour Express | PnU - mise à jour complète, mise à jour OEM | FRU | 
| --- | --- | --- | --- | 
| État de la machine virtuelle  | Non disponible pendant et après la mise à jour sans opération de démarrage manuelle | Non disponible pendant la mise à jour. Disponible après le démarrage avec une opération manuelle | Non disponible pendant la mise à jour. Disponible après le démarrage avec une opération manuelle| 
| Opération manuelle | Si la machine virtuelle doit être disponible pendant la mise à jour et si des partitions GPU sont disponibles, la machine virtuelle peut être redémarrée à partir du portail en cliquant sur le bouton **Redémarrer**. Redémarrez la machine virtuelle après la mise à jour à partir du portail à l’aide du bouton **Redémarrer** | Impossible de rendre la machine virtuelle disponible pendant la mise à jour. Après la fin de la mise à jour, la machine virtuelle doit être arrêtée-libérée à l’aide du bouton **Arrêter** et la sauvegarde doit être démarrée à l’aide du bouton « Démarrer » | Impossible de rendre la machine virtuelle disponible pendant la mise à jour. Après la fin de la mise à jour, la machine virtuelle doit être arrêtée-libérée à l’aide du bouton **Arrêter** et la sauvegarde doit être démarrée à l’aide du bouton **Démarrer**.| 

## <a name="guest-driver-installation"></a>Installation du pilote invité

### <a name="amd-mi25"></a>Mi25 AMD

L’article [Installer des pilotes GPU AMD sur des machines virtuelles série N exécutant Windows](/azure/virtual-machines/windows/n-series-amd-driver-setup) fournit des instructions sur l’installation du pilote pour l’AMD Radeon Instinct Mi25 à l’intérieur de la machine virtuelle compatible GPU-P NVv4, ainsi que les étapes de vérification de l’installation du pilote. Cette extension fonctionne uniquement en mode connecté.

### <a name="nvidia"></a>NVIDIA

Les pilotes NVIDIA doivent être installés à l’intérieur de la machine virtuelle pour les charges de travail CUDA ou GRID à l’aide du GPU.

#### <a name="use-case-graphicsvisualization"></a>Cas d’usage : graphiques/visualisation

Ce scénario requiert l’utilisation de pilotes GRID. Les pilotes GRID peuvent être téléchargés via le Hub d’applications NVIDIA, à condition que vous disposiez des licences requises. Les pilotes GRID requièrent également un serveur de licences GRID avec les licences GRID appropriées avant d’utiliser les pilotes GRID sur la machine virtuelle. Cette peut être utilisée pour apprendre à configurer le serveur de licences.

#### <a name="use-case-computecuda"></a>Cas d’usage : calcul/CUDA

Les pilotes GRID et NVIDIA CUDA doivent être installés manuellement sur la machine virtuelle. Les pilotes Tesla CUDA peuvent être obtenus à partir du [site web de téléchargement](https://www.nvidia.com/Download/index.aspx) NVIDIA. Les pilotes CUDA n’ont pas besoin d’un serveur de licences.

## <a name="next-steps"></a>Étapes suivantes

- [Installer les pilotes GPU NVIDIA sur les machines virtuelles série N exécutant Linux](/azure/virtual-machines/linux/n-series-driver-setup)
- [Fonctionnalités des machines virtuelles Azure Stack](azure-stack-vm-considerations.md)