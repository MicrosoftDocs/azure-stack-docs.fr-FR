---
title: Surveiller l’intégrité et les alertes dans Azure Stack | Microsoft Docs
description: Découvrez comment surveiller l’intégrité et les alertes dans Azure Stack.
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
ms.date: 10/26/2020
ms.author: mabrigg
ms.lastreviewed: 10/26/2020
ms.openlocfilehash: 4a14700a081e8c85f29a7edc69c471ba9bd3d249
ms.sourcegitcommit: 6db48bd8e6ccfaaa897713ad7eb2846a8d506358
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94885782"
---
# <a name="monitor-health-and-alerts-in-azure-stack"></a>Surveiller l’intégrité et les alertes dans Azure Stack

Azure Stack inclut des fonctionnalités de supervision d’infrastructure qui vous aident à visualiser les alertes et l’intégrité d’une région Azure Stack. La vignette **Gestion des régions** répertorie toutes les régions d’Azure Stack déployées. Elle est épinglée par défaut dans le portail d’administration pour l’abonnement fournisseur par défaut. La vignette montre le nombre d’alertes actives (critiques et d’avertissement) pour chaque région. La vignette est votre point d’entrée vers la fonctionnalité d’alerte et d’intégrité d’Azure Stack.

![Vignette Gestion des régions dans le portail d’administration Azure Stack](media/azure-stack-monitor-health/region-management.png)

## <a name="understand-health-in-azure-stack"></a>Présentation de l’intégrité dans Azure Stack

Le fournisseur de ressources d’intégrité gère l’intégrité et les alertes. Les composants d’infrastructure Azure Stack s’inscrivent auprès du fournisseur de ressources d’intégrité durant le déploiement et la configuration d’Azure Stack. Cette inscription permet de visualiser l’intégrité et les alertes pour chaque composant. L’intégrité dans Azure Stack est un concept simple. S’il existe des alertes pour une instance inscrite d’un composant, l’état d’intégrité de ce composant reflète la gravité de l’alerte active la plus sévère : avertissement ou critique.

## <a name="alert-severity-definition"></a>Définition de la gravité des alertes

Azure Stack déclenche des alertes avec seulement deux gravités : **avertissement** et **critique**.

- **Avertissement**  
  Un opérateur peut résoudre l’alerte d’avertissement de façon planifiée. En général, l’alerte n’affecte pas les charges de travail utilisateur.

- **Critique**  
  Un opérateur doit résoudre l’alerte critique de toute urgence. Ces alertes signalent des problèmes qui affectent actuellement ou qui affecteront bientôt les utilisateurs Azure Stack.


## <a name="view-and-manage-component-health-state"></a>Afficher et gérer l’état d’intégrité des composants

Vous pouvez afficher l’état d’intégrité des composants dans le portail administrateur et par le biais des API REST et de PowerShell.

Pour afficher l’état d’intégrité dans le portail, cliquez sur la région que vous souhaitez visualiser dans la vignette **Gestion des régions**. Vous pouvez afficher l’état d’intégrité des rôles d’infrastructure et des fournisseurs de ressources.

![Liste des rôles d’infrastructure](media/azure-stack-monitor-health/roles.png)

Vous pouvez cliquer sur un fournisseur de ressources ou un rôle d’infrastructure pour afficher des informations plus détaillées.

> [!WARNING]  
> Si vous cliquez sur un rôle d’infrastructure, puis sur l’instance de rôle, les options **Démarrer**, **Redémarrer** ou **Arrêter** s’affichent. N’utilisez pas ces actions quand vous appliquez des mises à jour à un système intégré. <!-- TZLASDKFIXAlso, do **not** use these options in an Azure Stack Development Kit (ASDK) environment. These options are only designed for an integrated systems environment, where there's more than one role instance per infrastructure role. Restarting a role instance (especially AzS-Xrp01) in the ASDK causes system instability.--> Pour obtenir une assistance en cas de problème, postez votre problème sur le [Forum Azure Stack](https://aka.ms/azurestackforum).
>

## <a name="view-alerts"></a>Afficher les alertes

La liste des alertes actives pour chaque région Azure Stack est disponible directement à partir du panneau **Gestion des régions**. La première vignette dans la configuration par défaut est la vignette **Alertes**, qui fournit un récapitulatif des alertes critiques et des avertissements pour la région. Vous pouvez épingler la vignette Alertes au tableau de bord (comme n’importe quelle autre vignette sur ce panneau) pour pouvoir y accéder rapidement.

![Vignette Alertes qui montre un avertissement dans le portail d’administration Azure Stack](media/azure-stack-monitor-health/alerts.png)

 Pour afficher une liste de toutes les alertes actives pour la région, sélectionnez la partie supérieure de la vignette **Alertes**. Pour afficher une liste filtrée des alertes (Critique ou Avertissement), sélectionnez l’élément de ligne **Critique** ou **Avertissement** dans la vignette.

Le panneau **Alertes** permet de filtrer à la fois sur le niveau de gravité (Critique ou Avertissement) et sur l'état (Actif ou Fermé). L’affichage par défaut montre toutes les alertes actives. Toutes les alertes fermées sont supprimées du système après sept jours.

>[!Note]
>Si une alerte reste active mais n’a pas été mise à jour depuis plus d’une journée, vous pouvez exécuter [Test-AzureStack](../../operator/azure-stack-diagnostic-test.md) et fermer l’alerte si aucun problème n’est signalé.

![Volet Filtre permettant de filtrer par statut Critique ou Avertissement dans le portail d’administration Azure Stack](media/azure-stack-monitor-health/alert-view.png)

L’action **API de vue** montre l’API REST utilisée pour générer la vue de liste. Cette action permet de se familiariser rapidement avec la syntaxe d’API REST que vous pouvez utiliser pour interroger les alertes. Vous pouvez utiliser cette API dans l’automation ou pour l’intégration avec vos solutions existantes de création de tickets, de création de rapports et de surveillance de centre de données.

Vous pouvez cliquer sur une alerte spécifique pour en afficher les détails. Les détails d’une alerte montrent tous les champs associés à celle-ci, et permettent d’accéder rapidement au composant affecté et à la source de l’alerte. Par exemple, l’alerte suivante se produit si l’une des instances de rôle d’infrastructure bascule hors connexion ou est inaccessible. Vous pouvez rechercher le [lien de renvoi](aks-refer-table-tzl.md) dans la section **Correction** des détails de l’alerte pour afficher l’article.

![Panneau Détails de l’alerte dans le portail d’administration Azure Stack](media/azure-stack-monitor-health/alert-detail.png)

## <a name="repair-alerts"></a>Alertes Réparer

Vous pouvez sélectionner **Réparer** dans certaines alertes.

Quand elle est sélectionnée, l’action **Réparer** effectue des étapes spécifiques à l’alerte pour essayer de résoudre le problème. Une fois sélectionné, l’état de l’action **Réparer** est disponible sous la forme d’une notification de portail.

![Action Réparer l’alerte en cours d’exécution](media/azure-stack-monitor-health/repair-in-progress.png)

L’action **Réparer** signale la réussite ou l’échec de l’action dans le même panneau de notification du portail.  Si une action **Réparer** échoue pour une alerte, vous pouvez la réexécuter à partir des détails de l’alerte. Si l’action **Réparer** aboutit, **ne la réexécutez pas**.

![Action Réparer terminée](media/azure-stack-monitor-health/repair-completed.png)

Une fois l’instance de rôle d’infrastructure de nouveau en ligne, cette alerte se ferme automatiquement. De nombreuses alertes, mais pas toutes, se ferment automatiquement quand le problème sous-jacent est résolu. Les alertes qui proposent un bouton d’action Réparer se ferment automatiquement si Azure Stack résout le problème. Pour toutes les autres alertes, sélectionnez **Fermer l’alerte** après avoir effectué les étapes de correction. Si le problème persiste, Azure Stack génère une nouvelle alerte. Si vous résolvez le problème, l’alerte reste fermée et ne nécessite aucune étape supplémentaire.

## <a name="next-steps"></a>Étapes suivantes

[Gérer les mises à jour dans Azure Stack](../../operator/azure-stack-updates.md)

[Gestion des régions dans Azure Stack](../../operator/azure-stack-region-management.md)
