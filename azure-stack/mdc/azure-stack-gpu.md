---
title: Machines virtuelles GPU sur Azure Stack | Microsoft Docs
description: Référence du calcul GPU dans Azure Stack.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2020
ms.author: justinha
ms.reviewer: kivenkat
ms.lastreviewed: 01/02/2020
ms.openlocfilehash: c80817f7fe92916e1d83ae7bc1e83290d25e2906
ms.sourcegitcommit: e4e2cc6a68f02c3e856f58ca5ee51b3313c7ff8f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92183413"
---
# <a name="gpu-vms-on-azure-stack"></a>Machines virtuelles GPU sur Azure Stack 

*S’applique à : systèmes intégrés Azure Stack* 

Cette rubrique explique comment gérer des machines virtuelles GPU sur Azure Stack Hub.


## <a name="partitioned-gpu-vm-size"></a>Taille des machines virtuelles GPU partitionnées 

Les machines virtuelles de la série NVv4 sont alimentées par des GPU [AMD Radeon Instinct MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25). Avec la série NVv4, Azure Stack Hub introduit des machines virtuelles avec des GPU partiels. Cette taille peut être utilisée pour les applications graphiques accélérées GPU et les bureaux virtuels. Les machines virtuelles NVv4 prennent actuellement en charge uniquement le système d’exploitation invité Windows. 

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | GPU | Mémoire GPU : Gio | Disques de données max. | Nombre max de cartes réseau | 
| --- | --- | --- | --- | --- | --- | --- | --- |   
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 | 

## <a name="patch-and-update-fru-behavior-of-vms"></a>Patch et mise à jour, comportement FRU des machines virtuelles 

Les machines virtuelles GPU subissent des temps d’arrêt pendant les opérations telles que les correctifs et les mises à jour (PnU), ainsi que le remplacement de matériel (FRU) d’Azure Stack Hub. Le tableau suivant présente l’état de la machine virtuelle comme observé au cours de ces activités, ainsi que l’action manuelle que l’utilisateur peut effectuer pour que ces machines virtuelles soient à nouveau disponibles après ces opérations. 

| Opération | PnU - mise à jour Express | PnU - mise à jour complète, mise à jour OEM | FRU | 
| --- | --- | --- | --- | 
| État de la machine virtuelle  | Non disponible pendant et après la mise à jour sans opération de démarrage manuelle | Non disponible pendant la mise à jour. Disponible après le démarrage avec une opération manuelle | Non disponible pendant la mise à jour. Disponible après le démarrage avec une opération manuelle| 
| Opération manuelle | Si la machine virtuelle doit être disponible pendant la mise à jour et si des partitions GPU sont disponibles, la machine virtuelle peut être redémarrée à partir du portail en cliquant sur le bouton Redémarrer. La machine virtuelle doit être redémarrée après la mise à jour à partir du portail à l’aide du bouton « Redémarrer » | Impossible de rendre la machine virtuelle disponible pendant la mise à jour. Après la fin de la mise à jour, la machine virtuelle doit être arrêtée-désallouée à l’aide du bouton « Arrêter » et la sauvegarde doit être démarrée à l’aide du bouton « Démarrer » | Impossible de rendre la machine virtuelle disponible pendant la mise à jour. Après la fin de la mise à jour, la machine virtuelle doit être arrêtée-désallouée à l’aide du bouton « Arrêter » et la sauvegarde doit être démarrée à l’aide du bouton « Démarrer ».| 

## <a name="guest-driver-installation"></a>Installation du pilote invité 

[Ce](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-amd-driver-setup) document passe en revue la configuration du pilote invité AMD à l’intérieur de la machine virtuelle compatible GPU-P NVv4, ainsi que les étapes de vérification de l’installation du pilote. 

## <a name="next-steps"></a>Étapes suivantes 

[Fonctionnalités des machines virtuelles Azure Stack](azure-stack-vm-considerations.md) 
