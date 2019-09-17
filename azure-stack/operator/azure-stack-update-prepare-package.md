---
title: Préparer un package de mise à jour Azure Stack | Microsoft Docs
description: Apprenez à préparer un package de mise à jour Azure Stack.
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
ms.date: 09/10/2019
ms.author: mabrigg
ms.lastreviewed: 09/10/2019
ms.reviewer: ppacent
ms.openlocfilehash: 515195e30aed9944b8e0cc0e371d08b54ea75189
ms.sourcegitcommit: 38f21e0bcf7b593242ad615c9d8ef8a1ac19c734
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70902669"
---
# <a name="prepare-an-azure-stack-update-package"></a>Préparer un package de mise à jour Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Cet article fournit une vue d’ensemble de la préparation des packages de mise à jour d’Azure Stack afin de pouvoir les utiliser pour mettre à jour votre environnement Azure Stack. Ce processus se décompose ainsi :

- [Télécharger le package de mise à jour](#download-the-update-package)
- [Importation du package de mise à jour dans votre environnement Azure Stack via le portail d’administration Azure Stack](#import-and-install-updates)

Sur les systèmes qui peuvent se connecter aux points de terminaison de mise à jour automatique, les mises à jour logicielles et les correctifs logiciels pour Azure Stack sont automatiquement téléchargés et préparés. Sur les systèmes sans connectivité, mais aussi pour toutes les mises à jour provenant d’un fabricant OEM, le package de mise à jour doit être préparé comme cela est expliqué dans cette rubrique.  

Le tableau suivant indique quand les packages de mise à jour nécessitent une préparation manuelle et quand ils sont préparés automatiquement.

| Type de mise à jour | Connectivité | Action requise |
| --- | --- | --- |
| Mises à jour logicielles d’Azure Stack | Connecté | La mise à jour est automatiquement téléchargée et préparée lorsque la mise à jour est appliquée. |
| Correctifs logiciels d’Azure Stack | Connecté | La mise à jour est automatiquement téléchargée et préparée lorsque la mise à jour est appliquée. |
| Mises à jour du package OEM | Connecté | Le package de mise à jour doit être préparé. Suivez les étapes décrites dans cet article. |
| Mises à jour logicielles d’Azure Stack | Connexion déconnectée ou faible | Le package de mise à jour doit être préparé. Suivez les étapes décrites dans cet article. |
| Correctifs logiciels d’Azure Stack | Connexion déconnectée ou faible | Le package de mise à jour doit être préparé. Suivez les étapes décrites dans cet article. |
| Mises à jour du package OEM | Connexion déconnectée ou faible | Le package de mise à jour doit être préparé. Suivez les étapes décrites dans cet article. |

## <a name="download-the-update-package"></a>Télécharger la mise à jour
Le package de mise à jour pour les mises à jour et les correctifs logiciels Azure Stack est disponible via le panneau de mise à jour pour les systèmes connectés. Vous devez télécharger le package et le déplacer vers un emplacement accessible pour votre instance Azure Stack si vous mettez à jour un package OEM ou si vous prenez en charge un système déconnecté. Vous devrez peut-être également télécharger le package et le transférer vers un emplacement accessible si vous exécutez un système avec une connexion intermittente.

Passez en revue le contenu du package. Une mise à jour comprend généralement les fichiers suivants :

-   **Un fichier \<NomPackage>.zip auto-extractible**. Ce fichier contient la charge utile pour la mise à jour.
- **Un fichier Metadata.xml**. Ce fichier contient des informations essentielles sur la mise à jour, par exemple l’éditeur, le nom, les prérequis, la taille et l’URL du chemin d’accès au support technique.

### <a name="automatic-download-and-preparation-for-update-packages"></a>Téléchargement et préparation automatiques des packages de mise à jour
Les mises à jour logicielles et les correctifs logiciels d’Azure Stack sont préparés automatiquement sur les systèmes avec une connectivité Internet aux **points de terminaison de mise à jour automatique d’Azure Stack** : https://*.azureedge.net et https://aka.ms/azurestackautomaticupdate. Pour plus d’informations sur la configuration de la connectivité aux **points de terminaison de mise à jour automatique Azure Stack**, consultez les points de terminaison de **Correctifs et mises à jour** décrits dans [Intégration d’Azure Stack à un pare-feu](https://docs.microsoft.com/azure-stack/operator/azure-stack-integrate-endpoints#ports-and-urls-outbound)

### <a name="where-to-download-azure-stack-update-packages"></a>Où télécharger les packages de mise à jour Azure Stack

Les mises à jour Azure Stack pour les [mises à jour Complètes et Express](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates#update-package-types) sont hébergées sur un point de terminaison Azure sécurisé. Les opérateurs Azure Stack avec des instances connectées voient les [mises à jour Azure Stack s’afficher automatiquement dans le portail d’administration](https://docs.microsoft.com/azure-stack/operator/azure-stack-update-prepare-package#automatic-download-and-preparation-for-update-packages). Pour les systèmes distants ou les systèmes disposant d’une connectivité Internet faible, les packages de mise à jour peuvent être téléchargés à l’aide de [l’outil de téléchargement des mises à jour Azure Stack](https://aka.ms/azurestackupdatedownload). Les packages de mises à jour logicielles Azure Stack peuvent contenir des mises à jour des services Azure Stack ainsi que des mises à jour du système d’exploitation des unités d’échelle de votre Azure Stack.

>[!NOTE]
>Le package de mise à jour et son contenu (comme les fichiers binaires, les scripts PowerShell, etc.) sont signés avec des certificats Microsoft. Toute falsification du package invalide la signature.


### <a name="where-to-download-azure-stack-hotfix-packages"></a>Où télécharger les packages de correctifs logiciels Azure Stack

Les packages pour les [correctifs logiciels Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates#update-package-types) sont hébergés dans le même point de terminaison Azure sécurisé que pour les mises à jour Azure Stack. Les opérateurs Azure Stack avec des instances connectées voient les [mises à jour Azure Stack s’afficher automatiquement dans le portail d’administration](https://docs.microsoft.com/azure-stack/operator/azure-stack-update-prepare-package#automatic-download-and-preparation-for-update-packages). Vous pouvez les télécharger en cliquant sur les liens incorporés dans chaque article correspondant de la Base de connaissances (par exemple, [Correctif logiciel Azure Stack 1.1906.11.52](https://support.microsoft.com/help/4515650)). Vous trouverez des correctifs logiciels dans les notes de publication correspondant à votre version d’Azure Stack.

### <a name="where-to-download-oem-update-packages"></a>Où télécharger les packages de mise à jour OEM
Votre fournisseur OEM publiera également des mises à jour, notamment pour les pilotes et le microprogramme. Même si ces mises à jour sont proposées sous forme de [mises à jour par package OEM](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates#update-package-types) distinctes par votre fournisseur de matériel, elles sont importées, installées et gérées de la même façon que les packages de mise à jour de Microsoft. Vous trouverez une liste de liens de contact des fournisseurs sur [Appliquer des mises à jour de fabricants d’ordinateurs (OEM) à Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-update-oem#oem-contact-information).

## <a name="import-and-install-updates"></a>Importer et installer des mises à jour

La procédure suivante montre comment importer et installer des mises à jour dans le portail d’administration.

> [!Important]  
> Informez les utilisateurs de toutes les opérations de maintenance, et de planifier dans la mesure du possible les fenêtres de maintenance ordinaire pendant les heures creuses. Les opérations de maintenance peuvent affecter les opérations du portail et les charges de travail des utilisateurs.

1.  Dans le portail d’administration, sélectionnez **Tous les services**. Ensuite, dans la catégorie **DONNÉES ET STOCKAGE**, sélectionnez **Comptes de stockage**. (Ou, dans la zone de filtre, commencez à taper **comptes de stockage** et sélectionnez l’option correspondante.)

    ![Mise à jour d'Azure Stack](./media/azure-stack-update-prepare-package/image1.png) 

1.  Dans la zone de filtre, tapez **mise à jour**, puis sélectionnez le compte de stockage **updateadminaccount**.

2.  Dans les détails du compte de stockage, sous **Services**, sélectionnez **Objets Blob**.

    ![Mise à jour d'Azure Stack - objet blob](./media/azure-stack-update-prepare-package/image2.png)

1.  Sous **Service blob**, sélectionnez **+ Conteneur** pour créer un conteneur. Entrez un nom (par exemple, *update-1811*), puis sélectionnez **OK**.

    ![Mise à jour d'Azure Stack - conteneur](./media/azure-stack-update-prepare-package/image3.png)

1.  Une fois le conteneur créé, cliquez sur le nom du conteneur, puis cliquez sur **Charger** pour charger les fichiers de package dans le conteneur.

    ![Mise à jour d’Azure Stack - télécharger](./media/azure-stack-update-prepare-package/image4.png)

1.  Sous **Charger l’objet blob**, cliquez sur l’icône de dossier, accédez au fichier .zip de la mise à jour, puis cliquez sur **Ouvrir** dans la fenêtre de l’explorateur de fichiers.

2.  Sous **Charger l’objet blob**, cliquez sur **Charger**.

    ![Mise à jour d’Azure Stack - télécharger un objet blob](./media/azure-stack-update-prepare-package/image5.png)

1.  Répétez les étapes 6 et 7 pour le fichier Metadata.xml et tous les fichiers .zip supplémentaires dans le package de mise à jour. N’importez pas le fichier Notification supplémentaire.txt s’il est inclus.

2.  Lorsque vous avez terminé, vous pouvez examiner les notifications (icône représentant une cloche dans le coin supérieur droit du portail). Les notifications doivent indiquer que le chargement est terminé.

3.  Revenez au panneau Mise à jour dans le tableau de bord. Le panneau doit indiquer qu’une mise à jour est disponible. Cela indique que la mise à jour a été préparée avec succès. Cliquez sur le panneau pour vérifier la mise à jour récemment ajoutée.

4.  Pour installer la mise à jour, sélectionnez le package marqué comme étant **Prêt**, cliquez dessus avec le bouton droit et sélectionnez **Mettre à jour maintenant** ou cliquez sur l’action **Mettre à jour maintenant** située en haut.

5.  Lorsque vous cliquez sur la mise à jour lors de son installation, son état s’affiche dans la zone **Update run details** (Détails sur la mise à jour en cours). À partir de cet emplacement, vous pouvez également cliquer sur **Télécharger le résumé** pour télécharger les fichiers journaux. Les journaux des exécutions de mise à jour sont disponibles six mois après la fin de la tentative.

6.  Lorsque la mise à jour est terminée, le panneau Mise à jour affiche la version Azure Stack mise à jour.

Après avoir installé des mises à jour sur Azure Stack, vous pouvez les supprimer manuellement du compte de stockage. Périodiquement, Azure Stack vérifie la présence de packages de mise à jour plus anciens et les supprime du stockage. Azure Stack peut prendre deux semaines pour supprimer les anciens packages.

## <a name="next-steps"></a>Étapes suivantes

[Appliquer la mise à jour](azure-stack-apply-updates.md)
