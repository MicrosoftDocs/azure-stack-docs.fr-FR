---
title: Bonnes pratiques relatives à la validation Azure Stack
description: Découvrez les bonnes pratiques se rapportant à la validation en tant que service.
author: mattbriggs
ms.topic: article
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: b3d911a07c7a234ce2284055ff67a7ca46f567a2
ms.sourcegitcommit: 20d10ace7844170ccf7570db52e30f0424f20164
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79295310"
---
# <a name="azure-stack-validation-best-practices"></a>Bonnes pratiques relatives à la validation Azure Stack

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Cet article traite des bonnes pratiques associées à la gestion des ressources dans Validation en tant que service (VaaS). Pour une vue d’ensemble des ressources VaaS, consultez [Concepts clés de Validation en tant que service](azure-stack-vaas-key-concepts.md).

## <a name="solution-management"></a>Gestion de solution

### <a name="naming-convention-for-vaas-solutions"></a>Convention d’affectation de noms pour les solutions VaaS

Utilisez une convention d’affectation de noms cohérente pour toutes les solutions inscrites dans VaaS. Par exemple, le nom de la solution peut être construit à partir des propriétés de matériel ci-dessous :

|Nom du produit | Élément matériel unique 1 | Élément matériel unique 2 | Nom de solution
|---|---|---|---|
Ma solution XYZ |  Flash | My Switch X01 | MySolutionXYZ_AllFlash_MySwitchX01

### <a name="when-to-create-a-new-vaas-solution"></a>Quand créer une nouvelle solution VaaS

Utilisez une même solution VaaS lors de l’exécution de flux de travail sur une même référence (SKU) matériel. Une nouvelle solution VaaS doit être créée uniquement en cas de modification de la référence (SKU) matérielle.

## <a name="workflow-management"></a>Gestion de flux de travail

### <a name="naming-convention-for-vaas-workflows"></a>Convention d’affectation de noms pour les flux de travail VaaS

Utilisez une convention d’affectation de noms cohérente pour toutes les exécutions de flux de travail VaaS. Par exemple, créez un nom de flux de travail à partir des propriétés de build ci-dessous, comme suit :

|Numéro de build (majeur) | Date | Taille de la solution | Nom du flux de travail
|---|---|---| ---|
1808 | 081518 | 4NODE | 1808_081518_4NODE

## <a name="next-steps"></a>Étapes suivantes

- Découvrez les [concepts clés concernant la validation en tant que service](azure-stack-vaas-key-concepts.md)
