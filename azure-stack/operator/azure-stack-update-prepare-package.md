---
title: Préparer une mise à jour Azure Stack Hub
description: Apprenez à préparer un package de mise à jour Azure Stack Hub.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.lastreviewed: 09/10/2019
ms.reviewer: ppacent
ms.openlocfilehash: 1911a07c1a22cbb58b7b7794addadb9cf01f0993
ms.sourcegitcommit: 1fa0140481a483e5c27f602386fe1fae77ad29f7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78367333"
---
# <a name="prepare-an-azure-stack-hub-update-package"></a>Préparer une mise à jour Azure Stack Hub

Cet article fournit une vue d’ensemble de la préparation des mises à jour Azure Stack Hub dans le cadre de la mise à jour de votre environnement Azure Stack Hub. Ce processus se décompose ainsi :

- [Télécharger le package de mise à jour](#download-the-update-package)
- [Importation de la mise à jour dans votre environnement Azure Stack Hub via le portail administrateur Azure Stack Hub](#import-and-install-updates)

Sur les systèmes qui peuvent se connecter aux points de terminaison de mise à jour automatique, les mises à jour logicielles et les correctifs logiciels pour Azure Stack Hub sont automatiquement téléchargés et préparés. Sur les systèmes sans connectivité, mais aussi pour toutes les mises à jour provenant d’un fabricant OEM, le package de mise à jour doit être préparé comme cela est expliqué dans cette rubrique.  

Le tableau suivant indique quand les packages de mise à jour nécessitent une préparation manuelle et quand ils sont préparés automatiquement.

| Type de mise à jour | Connectivité | Action requise |
| --- | --- | --- |
| Mises à jour logicielles Azure Stack Hub | Connecté | La mise à jour est automatiquement téléchargée et préparée lorsque la mise à jour est appliquée. |
| Correctifs logiciels Azure Stack Hub | Connecté | La mise à jour est automatiquement téléchargée et préparée lorsque la mise à jour est appliquée. |
| Mises à jour du package OEM | Connecté | Le package de mise à jour doit être préparé. Suivez les étapes décrites dans cet article. |
| Mises à jour logicielles Azure Stack Hub | Connexion déconnectée ou faible | Le package de mise à jour doit être préparé. Suivez les étapes décrites dans cet article. |
| Correctifs logiciels Azure Stack Hub | Connexion déconnectée ou faible | Le package de mise à jour doit être préparé. Suivez les étapes décrites dans cet article. |
| Mises à jour du package OEM | Connexion déconnectée ou faible | Le package de mise à jour doit être préparé. Suivez les étapes décrites dans cet article. |

## <a name="download-the-update-package"></a>Télécharger la mise à jour
La mise à jour correspondant aux mises à jour et correctifs logiciels Azure Stack Hub est disponible via le panneau de mise à jour pour les systèmes connectés. Vous devez télécharger le package et le déplacer vers un emplacement accessible pour votre instance Azure Stack Hub si vous mettez à jour un package OEM ou si vous prenez en charge un système déconnecté. Vous devrez peut-être également télécharger le package et le transférer vers un emplacement accessible si vous exécutez un système avec une connexion intermittente.

Passez en revue le contenu du package. Une mise à jour comprend généralement les fichiers suivants :

-   **Un fichier \<NomPackage>.zip auto-extractible**. Ce fichier contient la charge utile pour la mise à jour.
- **Un fichier Metadata.xml**. Ce fichier contient des informations essentielles sur la mise à jour, par exemple l’éditeur, le nom, les prérequis, la taille et l’URL du chemin d’accès au support technique.

### <a name="automatic-download-and-preparation-for-update-packages"></a>Téléchargement et préparation automatiques des packages de mise à jour
Les mises à jour logicielles et les correctifs logiciels Azure Stack Hub sont préparés automatiquement pour les systèmes dotés d’une connectivité aux **points de terminaison de mise à jour automatique Azure Stack Hub** : https://*.azureedge.net et https://aka.ms/azurestackautomaticupdate. Pour plus d’informations sur la configuration de la connectivité aux **points de terminaison de mise à jour automatique Azure Stack Hub**, consultez les points de terminaison de **Correctifs et mises à jour** décrits dans [Intégration d’Azure Stack Hub à un pare-feu](https://docs.microsoft.com/azure-stack/operator/azure-stack-integrate-endpoints#ports-and-urls-outbound)

### <a name="where-to-download-azure-stack-hub-update-packages"></a>Où télécharger les mises à jour Azure Stack Hub

Les mises à jour Azure Stack Hub pour les [mises à jour complètes et express](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates#update-package-types) sont hébergées sur un point de terminaison Azure sécurisé. Les opérateurs Azure Stack Hub qui ont des instances connectées voient les [mises à jour Azure Stack Hub apparaître automatiquement dans le portail administrateur](https://docs.microsoft.com/azure-stack/operator/azure-stack-update-prepare-package#automatic-download-and-preparation-for-update-packages). Pour les systèmes déconnectés d’Internet ou qui disposent d’une connectivité Internet faible, les mises à jour peuvent être téléchargées à l’aide de l’[outil de téléchargement des mises à jour Azure Stack Hub](https://aka.ms/azurestackupdatedownload). Les mises à jour logicielles Azure Stack Hub peuvent contenir des mises à jour des services Azure Stack Hub ainsi que des mises à jour du système d’exploitation des unités d’échelle de votre instance d’Azure Stack Hub.

>[!NOTE]
>Le package de mise à jour et son contenu (comme les fichiers binaires, les scripts PowerShell, etc.) sont signés avec des certificats Microsoft. Toute falsification du package invalide la signature.


### <a name="where-to-download-azure-stack-hub-hotfix-packages"></a>Où télécharger les packages de correctifs logiciels Azure Stack Hub

Les packages des [correctifs logiciels Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates#update-package-types) sont hébergés dans le même point de terminaison Azure sécurisé que les mises à jour Azure Stack Hub. Les opérateurs Azure Stack Hub qui ont des instances connectées voient les [mises à jour Azure Stack Hub apparaître automatiquement dans le portail administrateur](https://docs.microsoft.com/azure-stack/operator/azure-stack-update-prepare-package#automatic-download-and-preparation-for-update-packages). Vous pouvez les télécharger en cliquant sur les liens incorporés dans chaque article correspondant de la Base de connaissances (par exemple [correctif logiciel Azure Stack Hub 1.1906.11.52](https://support.microsoft.com/help/4515650)). Vous trouverez des correctifs logiciels dans les notes de publication correspondant à votre version d’Azure Stack Hub.

### <a name="where-to-download-oem-update-packages"></a>Où télécharger les packages de mise à jour OEM
Votre fournisseur OEM publiera également des mises à jour, notamment pour les pilotes et le microprogramme. Même si ces mises à jour sont proposées sous forme de [mises à jour par package OEM](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates#update-package-types) distinctes par votre fournisseur de matériel, elles sont importées, installées et gérées de la même façon que les packages de mise à jour de Microsoft. Vous trouverez une liste de liens de contact des fournisseurs sur [Appliquer des mises à jour de fabricants d’ordinateurs (OEM) à Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-update-oem#oem-contact-information).

## <a name="import-and-install-updates"></a>Importer et installer des mises à jour

La procédure suivante montre comment importer et installer des mises à jour dans le portail d’administration.

> [!Important]  
> Informez les utilisateurs de toutes les opérations de maintenance, et de planifier dans la mesure du possible les fenêtres de maintenance ordinaire pendant les heures creuses. Les opérations de maintenance peuvent affecter les opérations du portail et les charges de travail des utilisateurs.

1.  Dans le portail d’administration, sélectionnez **Tous les services**. Ensuite, dans la catégorie **DONNÉES ET STOCKAGE**, sélectionnez **Comptes de stockage**. (Ou, dans la zone de filtre, commencez à taper **comptes de stockage** et sélectionnez l’option correspondante.)

    ![Mise à jour d’Azure Stack Hub](./media/azure-stack-update-prepare-package/image1.png) 

1.  Dans la zone de filtre, tapez **mise à jour**, puis sélectionnez le compte de stockage **updateadminaccount**.

2.  Dans les détails du compte de stockage, sous **Services**, sélectionnez **Objets Blob**.

    ![Mise à jour d’Azure Stack Hub - objets blob](./media/azure-stack-update-prepare-package/image2.png)

1.  Sous **Service blob**, sélectionnez **+ Conteneur** pour créer un conteneur. Entrez un nom (par exemple, *update-1811*), puis sélectionnez **OK**.

    ![Mise à jour d’Azure Stack Hub - conteneur](./media/azure-stack-update-prepare-package/image3.png)

1.  Une fois le conteneur créé, cliquez sur le nom du conteneur, puis cliquez sur **Charger** pour charger les fichiers de package dans le conteneur.

    ![Mise à jour d’Azure Stack Hub - chargement](./media/azure-stack-update-prepare-package/image4.png)

1.  Sous **Charger l’objet blob**, cliquez sur l’icône de dossier, accédez au fichier .zip de la mise à jour, puis cliquez sur **Ouvrir** dans la fenêtre de l’explorateur de fichiers.

2.  Sous **Charger l’objet blob**, cliquez sur **Charger**.

    ![Mise à jour d’Azure Stack Hub - chargement de l’objet blob](./media/azure-stack-update-prepare-package/image5.png)

1.  Répétez les étapes 6 et 7 pour le fichier Metadata.xml et tous les fichiers .zip supplémentaires dans le package de mise à jour. N’importez pas le fichier Notification supplémentaire.txt s’il est inclus.

2.  Lorsque vous avez terminé, vous pouvez examiner les notifications (icône représentant une cloche dans le coin supérieur droit du portail). Les notifications doivent indiquer que le chargement est terminé.

3.  Revenez au panneau Mise à jour dans le tableau de bord. Le panneau doit indiquer qu’une mise à jour est disponible. Cela indique que la mise à jour a été préparée avec succès. Cliquez sur le panneau pour vérifier la mise à jour récemment ajoutée.

4.  Pour installer la mise à jour, sélectionnez le package marqué comme étant **Prêt**, cliquez dessus avec le bouton droit et sélectionnez **Mettre à jour maintenant** ou cliquez sur l’action **Mettre à jour maintenant** située en haut.

5.  Lorsque vous cliquez sur la mise à jour lors de son installation, son état s’affiche dans la zone **Update run details** (Détails sur la mise à jour en cours). À partir de cet emplacement, vous pouvez également cliquer sur **Télécharger le résumé** pour télécharger les fichiers journaux. Les journaux des exécutions de mise à jour sont disponibles six mois après la fin de la tentative.

6.  Lorsque la mise à jour est terminée, le panneau Mise à jour affiche la version Azure Stack Hub mise à jour.

Après avoir installé des mises à jour sur Azure Stack Hub, vous pouvez les supprimer manuellement du compte de stockage. Périodiquement, Azure Stack Hub vérifie la présence de packages de mise à jour plus anciens et les supprime du stockage. Azure Stack Hub peut prendre deux semaines pour supprimer les anciens packages.

## <a name="next-steps"></a>Étapes suivantes

[Appliquer la mise à jour](azure-stack-apply-updates.md)
