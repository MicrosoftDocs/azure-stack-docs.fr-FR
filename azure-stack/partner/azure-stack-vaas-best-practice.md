---
title: Meilleures pratiques de Validation Azure Stack | Microsoft Docs
description: Cet article traite des meilleures pratiques concernant la validation en tant que service.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 30b7a5327a709fb35c3c3360f4bb0246e9a5f75f
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "64310013"
---
# <a name="best-practices-for-validation-as-a-service"></a>Meilleures pratiques concernant la validation en tant que service

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Cet article traite des meilleures pratiques concernant la gestion des ressources dans Validation en tant que Service (VaaS). Pour une vue d’ensemble des ressources VaaS, consultez [Concepts clés de Validation en tant que service](azure-stack-vaas-key-concepts.md).

## <a name="solution-management"></a>Gestion de solution

### <a name="naming-convention-for-vaas-solutions"></a>Convention d’affectation de noms pour les solutions VaaS

Utilisez une convention d’affectation de noms cohérente pour toutes les solutions inscrites dans VaaS. Par exemple, le nom de la solution peut être construit à partir des propriétés de matériel ci-dessous :

|Nom du produit | Élément matériel unique 1 | Élément matériel unique 2 | Nom de la solution
|---|---|---|---|
Ma solution XYZ |  Flash | My Switch X01 | MySolutionXYZ_AllFlash_MySwitchX01

### <a name="when-to-create-a-new-vaas-solution"></a>Quand créer une nouvelle solution VaaS

Utilisez une même solution VaaS lors de l’exécution de flux de travail sur une même référence (SKU) matériel. Une nouvelle solution VaaS doit être créée uniquement en cas de modification de la référence (SKU) matériel.

## <a name="workflow-management"></a>Gestion de flux de travail

### <a name="naming-convention-for-vaas-workflows"></a>Convention d’affectation de noms pour les flux de travail VaaS

Utilisez une convention d’affectation de noms cohérente pour toutes les exécutions de flux de travail VaaS. Par exemple, créez un nom de flux de travail à partir des propriétés de build ci-dessous, comme suit :

|Numéro de build (majeur) | Date | Taille de la solution | Nom du flux de travail
|---|---|---| ---|
1808 | 081518 | 4NODE | 1808_081518_4NODE

## <a name="next-steps"></a>Étapes suivantes

- Découvrez les [concepts clés concernant la validation en tant que service](azure-stack-vaas-key-concepts.md)
