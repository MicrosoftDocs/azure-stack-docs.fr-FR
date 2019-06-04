---
title: Gérer les mises à jour dans Azure Stack - Vue d’ensemble | Microsoft Docs
description: Découvrez la gestion des mises à jour pour les systèmes intégrés Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2019
ms.author: mabrigg
ms.lastreviewed: 04/04/2019
ms.reviewer: justini
ms.openlocfilehash: 64ccb5ec0b24202aa847d5114ba35ae20af44cc8
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66268026"
---
# <a name="manage-updates-in-azure-stack-overview"></a>Gérer les mises à jour dans Azure Stack - Vue d’ensemble

*S’applique à : systèmes intégrés Azure Stack*

Les packages de mise à jour Microsoft pour les systèmes intégrés Azure Stack sont généralement mis en production chaque mois. Consultez votre fabricant de matériel OEM au sujet de son processus de notification pour vous assurer que votre organisation reçoive bien les notifications de mise à jour. Vous pouvez aussi rechercher dans cette bibliothèque de documentation sous **Vue d’ensemble** > **Notes de publication** des informations sur les versions dont le support est actif. 

Chaque version des mises à jour logicielles Microsoft est fournie sous forme de package de mise à jour unique. En tant qu’opérateur Azure Stack, vous pouvez importer des packages de mise à jour, les installer et superviser la progression de leur installation à partir du portail d’administration Azure Stack.

Votre fournisseur OEM publiera également des mises à jour, notamment pour les pilotes et le microprogramme. Même si ces mises à jour sont proposées sous forme de packages distincts par votre fournisseur, certaines sont importées, installées et gérées de la même façon que les packages de mise à jour de Microsoft.

Pour conserver votre système dans un état de prise en charge, vous devez maintenir Azure Stack à jour à un niveau de version spécifique. Pensez à passer en revue la [Stratégie de maintenance d’Azure Stack](azure-stack-servicing-policy.md).

> [!NOTE]
> Vous ne pouvez pas appliquer des packages de mise à jour Azure Stack au Kit de développement Azure Stack. Les packages de mise à jour sont conçues pour les systèmes intégrés. Pour plus d’informations, consultez [Redéployer l’ASDK](/azure-stack/asdk).

## <a name="the-update-resource-provider"></a>Le fournisseur de ressources de mise à jour

Azure Stack inclut un fournisseur de ressources de mise à jour qui gère l’application des mises à jour logicielles Microsoft. Ce fournisseur vérifie que les mises à jour sont appliquées sur tous les hôtes physiques, les applications et runtimes Service Fabric, et toutes les machines virtuelles d’infrastructure et leurs services associés.

Lors de l’installation des mises à jour, vous pouvez afficher l’état général à mesure que le processus de mise à jour cible les différents sous-systèmes dans Azure Stack (par exemple les hôtes physiques et les machines virtuelles d’infrastructure).

## <a name="plan-for-updates"></a>Planifier les mises à jour

Nous vous recommandons vivement d’informer les utilisateurs de toutes les opérations de maintenance, et de planifier dans la mesure du possible les fenêtres de maintenance ordinaire pendant les heures creuses. Les opérations de maintenance peuvent affecter les opérations de portail et les charges de travail de locataire.

Lors de la planification de votre fenêtre de maintenance, il est important d’examiner le type spécifique du package de mise à jour proposé par Microsoft, comme indiqué dans la note de publication correspondante. Hormis le correctif logiciel occasionnel, chaque package de mise à jour aura un type correspondant, **Complète** ou **Express**. Les packages de mise à jour Complète mettent à jour les systèmes d’exploitation hôtes physique dans l’unité d’échelle et nécessitent une plus grande fenêtre de maintenance. Les packages de mise à jour Express, quant à eux, sont limités et ne mettent pas à jour les systèmes d’exploitation hôtes physiques sous-jacents.

- Avant de démarrer l’installation de cette mise à jour, exécutez [Test-AzureStack](azure-stack-diagnostic-test.md) avec les paramètres suivants pour valider l’état de votre Azure Stack et résoudre les éventuels problèmes opérationnels détectés, y compris tous les avertissements et les échecs. Examinez aussi les alertes actives et résolvez toutes celles qui nécessitent une intervention.  

  ```powershell
  Test-AzureStack -Group UpdateReadiness
  ``` 

## <a name="using-the-update-tile-to-manage-updates"></a>Utilisation de la vignette Mise à jour pour gérer les mises à jour

Vous gérez les mises à jour à partir du portail administrateur. En tant qu’opérateur Azure Stack, vous pouvez utiliser la vignette Mise à jour dans le tableau de bord pour :

- Afficher des informations importantes telles que la version actuelle.
- Installer des mises à jour et surveiller la progression.
- Examiner l’historique des mises à jour pour les mises à jour précédemment installées.
- Voir la version actuelle du package OEM du cloud
 
## <a name="determine-the-current-version"></a>Déterminer la version actuelle

Vous pouvez afficher la version actuelle d’Azure Stack dans la vignette Mise à jour. Pour ouvrir la vignette :

1. Ouvrez le portail d’administration Azure Stack.
2. Sélectionnez **Tableau de bord**. La version actuelle apparaît dans la vignette **Mise à jour**. 

    ![Vignette Mises à jour sur le tableau de bord par défaut](./media/azure-stack-updates/image1.png)

    Par exemple, dans l’écran, la version est 1.1903.0.35.

## <a name="install-updates-and-monitor-progress"></a>Installer des mises à jour et surveiller la progression


1. Ouvrez le portail d’administration Azure Stack.
2. Sélectionnez **Tableau de bord**. Sélectionnez la vignette Mise à jour.
3. Sélectionnez **Mettre à jour maintenant**.

    ![Détails de l’exécution de la mise à jour d'Azure Stack](media/azure-stack-updates/azure-stack-update-button.png)

4.  Vous pouvez afficher l’état de haut niveau à mesure que le processus de mise à jour effectue une itération dans les différents sous-systèmes d’Azure Stack. Exemples de sous-systèmes : hôtes physiques, Service Fabric, machines virtuelles d’infrastructure et services fournissant des portails administrateur et utilisateur. Tout au long du processus de mise à jour, le fournisseur de ressources de mise à jour transmet des détails supplémentaires sur la mise à jour, notamment le nombre d’étapes réussies et le nombre d’étapes en cours d’exécution.

5. Sélectionnez l’option **Télécharger les journaux d’activité complets** dans le panneau des détails de l’exécution de la mise à jour pour télécharger les journaux complets.

    ![Détails de l’exécution de la mise à jour d'Azure Stack](media/azure-stack-updates/update-run-details.png)

6. Une fois l’opération terminée, le fournisseur de ressources de mise à jour vous envoie une confirmation **Succeeded** (Réussite) pour vous informer de la fin du processus de mise à jour et de sa durée. De là, vous pouvez afficher des informations sur la totalité des mises à jour, les mises à jour disponibles ou les mises à jour installées à l’aide du filtre.

    ![Détails de l’exécution de la mise à jour d'Azure Stack - réussite](media/azure-stack-updates/update-success.png)

   Si la mise à jour échoue, la vignette Mise à jour affiche **Doit être surveillé**. Utilisez l’option **Télécharger les journaux d’activité complets** pour obtenir un état de haut niveau de l’endroit où la mise à jour a pu échouer. La collecte des journaux Azure Stack facilite les diagnostics et le dépannage.

## <a name="next-steps"></a>Étapes suivantes

- [Stratégie de maintenance d’Azure Stack](azure-stack-servicing-policy.md) 
- [Gestion des régions dans Azure Stack](azure-stack-region-management.md)
