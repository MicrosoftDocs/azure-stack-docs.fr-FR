---
title: Installer un microprogramme à l’aide de l’iDRAC
description: Découvrez comment installer le microprogramme à l’aide de l’iDRAC
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 5b42b0a1f6be6e9fdf8110854e37f602d25b18ad
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97392634"
---
# <a name="installing-firmware-using-the-idrac-interface"></a>Installation du microprogramme à l’aide de l’interface de l’iDRAC

L’iDRAC (integrated Dell Remote Access Card) vous permet de mettre à jour des microprogrammes (un par un) à distance à l’aide de la fonctionnalité **Update and Rollback**. Cela fonctionne même si le serveur est en cours d’exécution.

Accédez à la page de [documentation Integrated System for Microsoft Azure Stack Hub 14G](https://www.dell.com/support/home/product-support/product/cloud-for-microsoft-azure-stack14g/docs) pour télécharger la matrice de prise en charge la plus récente, qui contient une liste des versions de microprogramme prises en charge.

Appliquez la procédure suivante pour mettre à jour le microprogramme d’un seul appareil à l’aide de l’interface web de l’iDRAC.

**Étapes**

1.  Accédez à **Maintenance** \**System Update**. La page **Firmware Update** s’affiche.

    ![](media/image-85.png)

2.  Sous l’onglet **Update**, sélectionnez **Local** comme emplacement de fichier.

3.  Sélectionnez **Browse**, sélectionnez l’image du microprogramme pour le composant requis, puis **Upload**.

4.  Une fois le chargement terminé, la section **Update Details** affiche chaque fichier de microprogramme qui est chargé dans l’iDRAC ainsi que son état. Si le fichier image du microprogramme est valide et a été chargé correctement, la colonne **Contents** affiche une icône (+) en regard du nom du fichier image du microprogramme. Développez le nom pour afficher les informations **Device Name**, **Current** et **Available firmware version**.

5.  Sélectionnez le fichier de microprogramme requis et effectuez l’une des opérations suivantes :

    -   Pour les images de microprogramme qui ne nécessitent pas de redémarrage du système hôte, sélectionnez **Install**.

    -   Pour les images de microprogramme qui nécessitent un redémarrage du système hôte, sélectionnez **Install and Reboot** ou **Install Next Reboot**.

    -   Pour annuler la mise à jour du microprogramme, sélectionnez **Cancel**.

6.  Pour afficher la page **Job Queue**, sélectionnez **Job Queue**. Utilisez cette page pour afficher et gérer les mises à jour de microprogramme intermédiaires, ou sélectionnez

    **OK** pour actualiser la page actuelle et afficher l’état de la mise à jour du microprogramme.
    
