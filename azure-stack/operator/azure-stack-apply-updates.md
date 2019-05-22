---
title: Effectuer des mises à jour dans Azure Stack | Microsoft Docs
description: Découvrez comment importer et installer des mises à jour Microsoft pour un système Azure Stack intégré.
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
ms.date: 04/25/2019
ms.author: mabrigg
ms.reviewer: justini
ms.lastreviewed: 02/11/2019
ms.openlocfilehash: 124dc554e18f6d387a3e41d37809d43276094879
ms.sourcegitcommit: 0d8ccf2a32b08ab9bcbe13d54c7c3dce2379757f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "64490120"
---
# <a name="apply-updates-in-azure-stack"></a>Effectuer des mises à jour dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Vous pouvez utiliser la vignette **Mettre à jour** dans le portail d’administration afin d’appliquer des packages de mise à jour Microsoft ou OEM pour Azure Stack.

Si vous utilisez la version 1807 ou antérieure des systèmes intégrés, vous devez télécharger la mise à jour, importer les fichiers du package dans Azure Stack, puis installer la mise à jour. Pour obtenir des instructions, consultez [Procéder à la mise à jour d'Azure Stack en téléchargeant le package](#update-azure-stack-by-downloading-the-package)

Ces instructions de mise à niveau s'appliquent aux systèmes intégrés Azure Stack. Si vous utilisez le système de développement Azure Stack, vous devez télécharger le package d'installation de la version actuelle. Pour obtenir des instructions, consultez [Installer le Kit de développement Azure Stack](../asdk/asdk-install.md)

## <a name="update-azure-stack"></a>Mettre à jour Azure Stack

### <a name="select-and-apply-an-update-package"></a>Sélectionner et appliquer un package de mise à jour

1. Ouvrez le portail d'administration.

2. Sélectionnez **Tableau de bord**. Sélectionnez la vignette **Mise à jour**.

    ![Mise à jour d'Azure Stack disponible](media/azure-stack-apply-updates/azure-stack-updates-1901-dashboard.png)

3. Notez la version actuelle de votre instance d'Azure Stack. Vous pouvez procéder à une mise à jour vers la version complète suivante. Par exemple, si vous exécutez Azure Stack 1811, la publication suivante est la version 1901.

    ![La mise à jour d'Azure Stack s'applique](media/azure-stack-apply-updates/azure-stack-updates-1901-updateavailable.png)

4. Sélectionnez la version disponible suivante dans la liste Mises à jour. Vous pouvez sélectionner **Afficher** dans la colonne des notes de publication pour ouvrir la rubrique relative aux notes de publication de la version si vous souhaitez consulter les modifications apportées à celle-ci.

5. Sélectionnez Mettre à jour maintenant. La mise à jour démarre.

### <a name="review-update-history"></a>Consulter l'historique des mises à jour

1. Ouvrez le portail d'administration.

2. Sélectionnez **Tableau de bord**. Sélectionnez la vignette **Mise à jour**.

3. Sélectionnez **Historique des mises à jour**.

![Historique des mises à jour d'Azure Stack](media/azure-stack-apply-updates/azure-stack-update-history.PNG)

## <a name="update-azure-stack-by-downloading-the-package"></a>Procéder à la mise à jour d'Azure Stack en téléchargeant le package

Si vous utilisez la version 1807 ou antérieure des systèmes intégrés, vous devez télécharger la mise à jour, importer les fichiers du package dans Azure Stack, puis installer la mise à jour.

## <a name="download-the-update-package"></a>Télécharger la mise à jour

Lorsqu’une mise à jour Microsoft ou OEM pour Azure Stack est disponible, téléchargez le package vers un emplacement accessible à partir d’Azure Stack et vérifiez son contenu. Une mise à jour comprend généralement les fichiers suivants :

- Un fichier `<PackageName>.exe` auto-extractible. Ce fichier contient la charge utile pour la mise à jour, par exemple la dernière mise à jour cumulative pour Windows Server.

- Des fichiers `<PackageName>.bin` correspondants. Ces fichiers fournissent la compression de la charge utile qui est associée au fichier *PackageName*.exe.

- Un fichier `Metadata.xml`. Ce fichier contient des informations essentielles sur la mise à jour, par exemple l’éditeur, le nom, les prérequis, la taille et l’URL du chemin d’accès au support technique.

> [!IMPORTANT]  
> Après l’application du package de mise à jour Azure Stack 1901, le format des packages de mise à jour Azure Stack passera des formats .exe, .bin(s) et .xml aux formats .zip(s) et.xml. Les opérateurs Azure Stack ayant des horodatages connectés ne seront pas concernés par ce changement. Les opérateurs Azure Stack déconnectés importeront simplement le(s) fichier(s) .xml et .zip en utilisant le même processus que décrit ci-dessous.

## <a name="import-and-install-updates"></a>Importer et installer des mises à jour

La procédure suivante montre comment importer et installer des mises à jour dans le portail d’administration.

> [!IMPORTANT]  
> Nous vous recommandons vivement d’informer les utilisateurs de toutes les opérations de maintenance, et de planifier dans la mesure du possible les fenêtres de maintenance ordinaire pendant les heures creuses. Les opérations de maintenance peuvent affecter les opérations du portail et les charges de travail des utilisateurs.

1. Dans le portail d’administration, sélectionnez **Tous les services**. Ensuite, dans la catégorie **DONNÉES ET STOCKAGE**, sélectionnez **Comptes de stockage**. (Ou, dans la zone de filtre, commencez à taper **comptes de stockage** et sélectionnez l’option correspondante.)

    ![Indique où trouver les comptes de stockage dans le portail](media/azure-stack-apply-updates/ApplyUpdates1.png)

2. Dans la zone de filtre, tapez **mise à jour**, puis sélectionnez le compte de stockage **updateadminaccount**.

3. Dans les détails du compte de stockage, sous **Services**, sélectionnez **Objets Blob**.
 
    ![Indique comment obtenir les objets Blob pour le compte de stockage](media/azure-stack-apply-updates/ApplyUpdates3.png) 

4. Sous **Service BLOB**, sélectionnez **+ Conteneur** pour créer un conteneur. Entrez un nom (par exemple, *update-1811*), puis sélectionnez **OK**.
 
     ![Indique comment ajouter un conteneur dans le compte de stockage](media/azure-stack-apply-updates/ApplyUpdates4.png)

5. Une fois le conteneur créé, cliquez sur le nom du conteneur, puis cliquez sur **Charger** pour charger les fichiers de package dans le conteneur.
 
    ![Indique comment charger les fichiers de package](media/azure-stack-apply-updates/ApplyUpdates5.png)

6. Sous **Charger l’objet blob**, cliquez sur l’icône de dossier, accédez au fichier .exe de la mise à jour, puis cliquez sur **Ouvrir** dans la fenêtre de l’explorateur de fichiers.
  
7. Sous **Charger l’objet blob**, cliquez sur **Charger**.
  
    ![Indique où charger chaque fichier de package](media/azure-stack-apply-updates/ApplyUpdates6.png)

8. Répétez les étapes 6 et 7 pour les fichiers *PackageName*.bin et Metadata.xml. N’importez pas le fichier Notification supplémentaire.txt s’il est inclus.
9. Lorsque vous avez terminé, vous pouvez examiner les notifications (icône représentant une cloche dans le coin supérieur droit du portail). Les notifications doivent indiquer que le chargement est terminé.
10. Revenez à la vignette Mise à jour dans le tableau de bord. La vignette doit indiquer qu’une mise à jour est disponible. Cliquez sur la vignette pour vérifier la mise à jour récemment ajoutée.
11. Pour installer la mise à jour, sélectionnez le package marqué comme étant **Prêt**, cliquez dessus avec le bouton droit et sélectionnez **Mettre à jour maintenant** ou cliquez sur l’action **Mettre à jour maintenant** située en haut.
12. Lorsque vous cliquez sur la mise à jour lors de son installation, son état s’affiche dans la zone **Update run details** (Détails sur la mise à jour en cours). À partir de cet emplacement, vous pouvez également cliquer sur **Télécharger les journaux d’activité complets** pour télécharger les fichiers journaux.
13. Lorsque la mise à jour est terminée, la vignette Mise à jour affiche la version Azure Stack mise à jour.

Après avoir installé des mises à jour sur Azure Stack, vous pouvez les supprimer manuellement du compte de stockage. Périodiquement, Azure Stack vérifie la présence de packages de mise à jour plus anciens et les supprime du stockage. Azure Stack peut prendre deux semaines pour supprimer les anciens packages.

## <a name="next-steps"></a>Étapes suivantes

- [Gérer les mises à jour dans Azure Stack - Vue d’ensemble](azure-stack-updates.md)
- [Stratégie de maintenance d’Azure Stack](azure-stack-servicing-policy.md)
