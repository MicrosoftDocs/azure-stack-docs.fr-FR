---
title: Types de quotas dans Azure Stack | Microsoft Docs
description: Affichez et modifiez les différents types de quotas disponibles pour les services et les ressources dans Azure Stack.
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
origin.date: 03/27/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: xiaofmao
ms.lastreviewed: 12/07/2018
ms.openlocfilehash: 848b2a0c912a00a2185d7e4b7b8d8446bc1f6aca
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "64308891"
---
# <a name="quota-types-in-azure-stack"></a>Types de quotas dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Les [quotas](azure-stack-plan-offer-quota-overview.md#plans) définissent les limites de ressources qu’un abonnement utilisateur peut approvisionner ou consommer. Par exemple, un quota peut autoriser un utilisateur de créer jusqu’à cinq machines virtuelles. Chaque ressource peut avoir ses propres types de quotas.

> [!IMPORTANT]
> Il peut s’écouler jusqu’à deux heures avant que les nouveaux quotas ne soient disponibles sur le portail de l’utilisateur ou qu’un quota modifié ne soit appliqué.

## <a name="compute-quota-types"></a>Types de quotas de capacité de traitement (compute)

| **Type** | **Valeur par défaut** | **Description** |
| --- | --- | --- |
| Nombre maximal de machines virtuelles | 50 | Nombre maximal de machines virtuelles qu’un abonnement peut créer à cet emplacement. |
| Nombre maximal de cœurs de machine virtuelle | 100 | Nombre maximal de cœurs qu’un abonnement peut créer à cet emplacement (par exemple, une machine virtuelle A3 a quatre cœurs). |
| Nombre maximal de groupes à haute disponibilité | 10 | Nombre maximal de groupes à haute disponibilité qui peuvent être créés à cet emplacement. |
| Nombre maximal de groupes de machines virtuelles identiques | 100 | Nombre maximal de groupes de machines virtuelles identiques qui peuvent être créés à cet emplacement. |
| Capacité maximale (en Go) du disque managé standard | 2 048 | Capacité maximale des disques managés standard qui peuvent être créés dans cet emplacement. |
| Capacité maximale (en Go) du disque managé Premium | 2 048 | Capacité maximale des disques managés Premium qui peuvent être créés dans cet emplacement. |

> [!NOTE]  
> La capacité maximale d’un disque non managé (objets blob par page) est distincte du quota des disques managés. Vous pouvez définir cette valeur dans la section **Quotas de stockage**.

## <a name="storage-quota-types"></a>Types de quotas de stockage

| **Item** | **Valeur par défaut** | **Description** |
| --- | --- | --- |
| Capacité maximale (Go) |2 048 |Capacité de stockage totale (incluant les objets blob et tous les instantanés, tables et files d’attente associés) qui peut être consommée par un abonnement à cet emplacement. |
| Nombre total de comptes de stockage |20 |Nombre maximal de comptes de stockage qu’un abonnement peut créer à cet emplacement. |

> [!NOTE]  
> La capacité maximale des disques managés est distincte du quota de stockage total. Vous pouvez définir cette valeur dans la section **Quotas de calcul**.

## <a name="network-quota-types"></a>Types de quotas pour les réseaux

| **Item** | **Valeur par défaut** | **Description** |
| --- | --- | --- |
| Nombre maximal d’adresses IP publiques |50 |Nombre maximal d’adresses IP publiques qu’un abonnement peut créer à cet emplacement. |
| Nombre maximal de réseaux virtuels |50 |Nombre maximal de réseaux virtuels qu’un abonnement peut créer à cet emplacement. |
| Nombre maximal de passerelles de réseau virtuel |1 |Nombre maximal de passerelles de réseau virtuel qu’un abonnement peut créer à cet emplacement. |
| Nombre maximal de connexions réseau |2 |Nombre maximal de connexions réseau (point à point ou site à site) qu’un abonnement peut créer pour toutes les passerelles de réseau virtuel à cet emplacement. |
| Nombre maximal d’équilibreurs de charge |50 |Nombre maximal d’équilibreurs de charge qu’un abonnement peut créer à cet emplacement. |
| Nombre maximal de cartes réseau |100 |Nombre maximal d’interfaces réseau qu’un abonnement peut créer à cet emplacement. |
| Nombre maximal de groupes de sécurité réseau |50 |Nombre maximal de groupes de sécurité réseau qu’un abonnement peut créer à cet emplacement. |

## <a name="view-an-existing-quota"></a>Afficher un quota existant

Il existe deux façons d’afficher un quota existant :

### <a name="plans"></a>Plans

1. Dans le volet de navigation gauche du portail d’administration, sélectionnez **Plans**.
2. Sélectionnez le plan pour lequel vous souhaitez afficher les détails, en cliquant sur son nom.
3. Dans le panneau qui s’ouvre, sélectionnez **Services et quotas**.
4. Sélectionnez le quota que vous aimeriez voir en cliquant dessus dans la colonne **Nom**.

    [![Quotas](media/azure-stack-quota-types/quotas1sm.png "Afficher les quotas")](media/azure-stack-quota-types/quotas1.png#lightbox)

### <a name="resource-providers"></a>Fournisseurs de ressources

1. Sur le tableau de bord par défaut du portail administrateur, recherchez la vignette **Fournisseurs de ressources**.
2. Sélectionnez le service avec le quota que vous voulez afficher, par exemple **Compute**, **Réseau** ou **Stockage**.
3. Cliquez sur **Quotas**, puis sélectionnez le quota que vous voulez afficher.

## <a name="edit-a-quota"></a>Modifier un quota

Il existe deux façons différentes de modifier un quota :

### <a name="edit-a-plan"></a>Modifier un plan

1. Dans le volet de navigation gauche du portail d’administration, sélectionnez **Plans**.
2. Sélectionnez le plan pour lequel vous souhaitez modifier un quota, en cliquant sur son nom.
3. Dans le panneau qui s’ouvre, sélectionnez **Services et quotas**.
4. Sélectionnez le quota que vous aimeriez modifier en cliquant dessus dans la colonne **Nom**.
    [![Quotas](media/azure-stack-quota-types/quotas1sm.png "Afficher les quotas")](media/azure-stack-quota-types/quotas1.png#lightbox)

5. Dans le panneau qui s’ouvre, sélectionnez **Modifier dans Compute**, **Modifier dans Réseau**, ou **Modifier dans Stockage**.
    ![Quotas](media/azure-stack-quota-types/quotas3.png "Afficher les quotas")

Vous pouvez également suivre cette procédure pour modifier un quota :

1. Dans le tableau de bord par défaut du portail d’administration, recherchez la vignette **Fournisseurs de ressources**.
2. Sélectionnez le service avec le quota que vous voulez modifier, par exemple **Calculs**, **Réseau** ou **Stockage**.
3. Cliquez ensuite sur **Quotas**, puis sélectionnez le quota que vous voulez modifier.
4. Dans le volet **Définir des quotas de stockage**, **Définir des quotas de calcul**, ou **Définir des quotas de réseau** (selon le type de quota que vous avez choisi de modifier), changez les valeurs, puis sélectionnez **Enregistrer**.

### <a name="edit-original-configuration"></a>Modifier la configuration d’origine
  
Vous pouvez choisir de modifier la configuration d’origine d’un quota au lieu d’[utiliser un plan d’extension](create-add-on-plan.md). Lorsque vous modifiez un quota, la nouvelle configuration s’applique automatiquement à tous les plans qui utilisent ce quota et à tous les abonnements existants qui utilisent ces plans. La modification d’un quota est différente de celle d’un plan d’extension visant à fournir un quota modifié, auquel un utilisateur choisit de s’abonner.

Les nouvelles valeurs pour le quota s’appliquent globalement à tous les plans qui utilisent le quota modifié et à tous les abonnements existants qui utilisent ces plans.

## <a name="next-steps"></a>Étapes suivantes

- [Découvrez plus d’informations sur les plans, les offres et les quotas.](azure-stack-plan-offer-quota-overview.md)
- [Créez des quotas lors de la création d’un plan.](azure-stack-create-plan.md)

<!-- Update_Description: add edit a quota -->