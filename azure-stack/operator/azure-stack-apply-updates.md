---
title: Appliquer une mise à jour de fabricant d’ordinateurs (OEM) à Azure Stack | Microsoft Docs
description: Apprenez à appliquer une mise à jour de fabricant d’ordinateurs (OEM) à Azure Stack.
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
ms.date: 08/23/2019
ms.author: mabrigg
ms.lastreviewed: 08/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: 792790c2ae5c14e31914b64fc6e5d7eba11aacc0
ms.sourcegitcommit: 7968f9f0946138867323793be9966ee2ef99dcf4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2019
ms.locfileid: "70025934"
---
# <a name="apply-updates-in-azure-stack"></a>Effectuer des mises à jour dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Vous pouvez appliquer la mise à jour à l’aide du panneau **Mise à jour** de la Azure Stack. Cet article vous guide tout au long des étapes de mise à jour, de surveillance et de résolution des problèmes liés au processus de mise à jour. Vous pouvez utiliser le panneau de mise à jour pour afficher des informations sur les mises à jour, installer des mises à jour, surveiller la progression des mises à jour, vérifier l’historique des mises à jour et afficher la version actuelle du package OEM.

Vous pouvez gérer les mises à jour à partir du portail administrateur. Vous pouvez utiliser les **Mises à jour** dans le tableau de bord pour :

-   Afficher des informations importantes telles que la version actuelle.
-   Installer des mises à jour et surveiller la progression.
-   Examiner l’historique des mises à jour pour les mises à jour précédemment installées.
-   Voir la version actuelle du package OEM du cloud.

## <a name="determine-the-current-version"></a>Déterminer la version actuelle

Vous pouvez voir la version actuelle d’Azure Stack dans le panneau **Mise à jour**. Pour ouvrir :

1.  Ouvrez le portail administrateur Azure Stack.

2.  Sélectionnez **Tableau de bord**. La version actuelle apparaît dans le panneau **Mise à jour**.

    ![Vignette Mises à jour sur le tableau de bord par défaut](./media/azure-stack-update-apply/image1.png)

    Par exemple, dans cette image, la version est 1.1903.0.35.

## <a name="install-updates-and-monitor-progress"></a>Installer des mises à jour et surveiller la progression

1.  Ouvrez le portail administrateur Azure Stack.

2.  Sélectionnez **Tableau de bord**. Sélectionnez **Update**.

3.  Sélectionnez la mise à jour disponible que vous souhaitez appliquer. Si aucune mise à jour n’est marquée comme **Disponible**, vous devez [Préparer le package de mise à jour](azure-stack-update-prepare-package.md)

4.  Sélectionnez **Mettre à jour maintenant**.

    ![Détails de l’exécution de la mise à jour d'Azure Stack](./media/azure-stack-update-apply/image2.png)

5.  Vous pouvez afficher l’état de haut niveau à mesure que le processus de mise à jour effectue une itération dans les différents sous-systèmes d’Azure Stack. Exemples de sous-systèmes : hôtes physiques, Service Fabric, machines virtuelles d’infrastructure et services fournissant des portails administrateur et utilisateur. Tout au long du processus de mise à jour, le fournisseur de ressources de mise à jour transmet des détails supplémentaires sur la mise à jour, notamment le nombre d’étapes réussies et le nombre d’étapes en cours.

6.  Sélectionnez l’option **Télécharger le résumé** dans le panneau des détails de l’exécution de la mise à jour pour télécharger les journaux complets.

    Si vous rencontrez un problème lors de la surveillance de la mise à jour, vous pouvez utiliser le [point de terminaison privilégié](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint) pour surveiller la progression de l’exécution d’une mise à jour Azure Stack et reprendre une mise à jour ayant échoué à partir de la dernière étape réussie si le portail Azure Stack n’est plus disponible. Pour obtenir des instructions, consultez [Surveiller les mises à jour dans Azure Stack à l’aide de PowerShell](azure-stack-update-monitor.md).

    ![Détails de l’exécution de la mise à jour d'Azure Stack](./media/azure-stack-update-apply/image3.png)

7.  Une fois l’opération terminée, le fournisseur de ressources de mise à jour vous envoie une confirmation **Succeeded** (Réussite) pour indiquer la fin du processus de mise à jour et sa durée. De là, vous pouvez afficher des informations sur la totalité des mises à jour, les mises à jour disponibles ou les mises à jour installées à l’aide du filtre.

    ![azure-stack-update-apply](./media/azure-stack-update-apply/image4.png)

    Si la mise à jour échoue, le panneau **Mise à jour** affiche **Doit être surveillé**. Utilisez l’option **Télécharger les journaux d’activité complets** pour obtenir un état général de l’endroit où la mise à jour a échoué. La collecte des journaux Azure Stack facilite les diagnostics et le dépannage.

## <a name="review-update-history"></a>Consulter l'historique des mises à jour

1.  Ouvrez le portail d’administration.

2.  Sélectionnez **Tableau de bord**. Sélectionnez **Update**.

3.  Sélectionnez **Historique des mises à jour**.

    ![Historique des mises à jour d'Azure Stack](./media/azure-stack-update-apply/image7.png)

## <a name="next-steps"></a>Étapes suivantes

-   [Gérer les mises à jour dans Azure Stack - Vue d’ensemble](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates)  
-   [Stratégie de maintenance d’Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-servicing-policy)  
