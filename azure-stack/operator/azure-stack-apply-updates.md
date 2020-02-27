---
title: Installer des mises à jour d’Azure Stack Hub
description: Découvrez comment installer des mises à jour d’Azure Stack Hub.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 1/22/2020
ms.author: inhenkel
ms.lastreviewed: 09/10/2019
ms.reviewer: ppacent
ms.openlocfilehash: 61f4e147d587a50793169575eac8abc6d33fce28
ms.sourcegitcommit: 97806b43314d306e0ddb15847c86be2c92ae001e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77509447"
---
# <a name="install-azure-stack-hub-updates"></a>Installer des mises à jour d’Azure Stack Hub

Vous pouvez installer des packages de mise à jour à l’aide du panneau **Mise à jour** dans Azure Stack Hub. Cet article vous guide tout au long des étapes de mise à jour, de surveillance et de résolution des problèmes liés au processus de mise à jour. Accédez au panneau Mise à jour pour lire les informations sur les mises à jour, installer des mises à jour, superviser la progression des mises à jour, vérifier l’historique des mises à jour et connaître la version actuelle d’Azure Stack Hub et du package OEM.

Vous pouvez gérer les mises à jour à partir du portail administrateur et utiliser la section **Mises à jour** du tableau de bord pour :

- Afficher des informations importantes telles que la version actuelle.
- Installer des mises à jour et surveiller la progression.
- Examiner l’historique des mises à jour pour les mises à jour précédemment installées.
- Voir la version actuelle du package OEM du cloud.

## <a name="determine-the-current-version"></a>Déterminer la version actuelle

Vous pouvez voir la version actuelle d’Azure Stack Hub dans le panneau **Mises à jour**. Pour ouvrir :

1.  Ouvrez le portail administrateur Azure Stack Hub.

2.  Sélectionnez **Tableau de bord**. La version actuelle apparaît dans le panneau **Mise à jour**.

    ![Vignette Mises à jour sur le tableau de bord par défaut](./media/azure-stack-update-apply/image1.png)

    Par exemple, dans cette image, la version est 1.1903.0.35.

## <a name="install-updates-and-monitor-progress"></a>Installer des mises à jour et surveiller la progression

> [!Important]
> Avant d’appliquer des mises à jour dans Azure Stack Hub, assurez-vous que vous avez effectué **TOUTES** les étapes répertoriées dans la [liste de contrôle préalable à la mise à jour](release-notes-checklist.md) et que vous avez planifié une fenêtre de maintenance appropriée pour le type de mise à jour que vous appliquez.

1. Ouvrez le portail administrateur Azure Stack Hub.

2. Sélectionnez **Tableau de bord**. Sélectionnez **Update**.

3. Sélectionnez la mise à jour disponible que vous souhaitez installer. Si aucune mise à jour n’est marquée comme **Disponible**, vous devez [Préparer le package de mise à jour](azure-stack-update-prepare-package.md)

4. Sélectionnez **Mettre à jour maintenant**.

    ![Détails de l’exécution de la mise à jour d’Azure Stack Hub](./media/azure-stack-update-apply/image2.png)

5. Vous pouvez afficher l’état général à mesure que le processus de mise à jour effectue une itération dans les différents sous-systèmes d’Azure Stack Hub. Exemples de sous-systèmes : hôtes physiques, Service Fabric, machines virtuelles d’infrastructure et services fournissant des portails administrateur et utilisateur. Tout au long du processus de mise à jour, le fournisseur de ressources de mise à jour transmet des détails supplémentaires sur la mise à jour, notamment le nombre d’étapes réussies et le nombre d’étapes en cours.

6. Sélectionnez l’option **Télécharger le résumé** dans le panneau des détails de l’exécution de la mise à jour pour télécharger les journaux complets.

    Si vous rencontrez un problème pendant la supervision de la mise à jour, vous pouvez utiliser le [point de terminaison privilégié](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint) pour surveiller la progression de l’exécution d’une mise à jour d’Azure Stack Hub. Le point de terminaison privilégié vous permet également de reprendre l’exécution d’une mise à jour ayant échoué à partir de la dernière étape réussie, si vous n’avez plus accès au portail Azure Stack Hub. Pour obtenir des instructions, voir [Superviser les mises à jour dans Azure Stack Hub à l’aide de PowerShell](azure-stack-update-monitor.md).

    ![Détails de l’exécution de la mise à jour d’Azure Stack Hub](./media/azure-stack-update-apply/image3.png)

7. Une fois l’opération terminée, le fournisseur de ressources de mise à jour vous envoie une confirmation de **réussite** pour indiquer la fin du processus de mise à jour et sa durée. De là, vous pouvez afficher des informations sur la totalité des mises à jour, les mises à jour disponibles ou les mises à jour installées à l’aide du filtre.

    ![azure-stack-update-apply](./media/azure-stack-update-apply/image4.png)

    Si la mise à jour échoue, le panneau **Mise à jour** affiche **Doit être surveillé**. Utilisez l’option **Télécharger les journaux d’activité complets** pour obtenir un état général de l’endroit où la mise à jour a échoué. La collecte des journaux Azure Stack Hub facilite les diagnostics et le dépannage.

## <a name="review-update-history"></a>Consulter l'historique des mises à jour

1. Ouvrez le portail d’administration.

2. Sélectionnez **Tableau de bord**. Sélectionnez **Update**.

3. Sélectionnez **Historique des mises à jour**.

    ![Historique de mise à jour d’Azure Stack Hub](./media/azure-stack-update-apply/image7.png)

## <a name="next-steps"></a>Étapes suivantes

-   [Vue d’ensemble de la gestion des mises à jour dans Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates)  
-   [Stratégie de maintenance d’Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-servicing-policy)  
