---
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: include
ms.date: 08/04/2020
ms.reviewer: thoroet
ms.lastreviewed: 08/04/2020
ms.openlocfilehash: a9a926b8d926e919ece60e35dc24db6b642328df
ms.sourcegitcommit: 9a340b383dcf42c85bc6ec0d01ff3c9ae29dfe4c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89609889"
---
1. Connectez-vous au portail utilisateur Azure Stack Hub.

    Si vous êtes un opérateur cloud qui crée un disque de plateforme, suivez les instructions de la rubrique [Ajouter une image de plateforme](/azure-stack/operator/azure-stack-add-vm-image.md#add-a-platform-image) pour ajouter le disque dur virtuel via le portail d’administration ou avec les points de terminaison d’administrateur.

2. Dans le portail utilisateur, sélectionnez **Tous les services** > **Disques** > **Ajouter**.

3. Dans **Créer un disque géré** :

    1. Tapez le **nom** de votre image.
    2. Sélectionnez votre **abonnement**.
    3. Créez ou ajoutez l’image à un **groupe de ressources**.
    4. Sélectionnez l’**emplacement**, également appelé « région », de votre kit ASDK.
    5. Sélectionnez le **Type de compte**.
        - Les **disques Premium (SSD)** sont associés à des disques SSD afin d’offrir des performances constantes et une faible latence. Ils proposent le meilleur rapport prix/performances et conviennent parfaitement aux applications d’E/S intensives et aux charges de travail de production.  
        - Les **disques standard (HDD)** sont associés à des lecteurs magnétiques et conviennent davantage aux applications dont les données sont rarement utilisées. Les disques redondants interzones sont associés à un stockage redondant interzone (ZRS) qui réplique vos données sur plusieurs zones, et restent disponibles même si une zone n’est pas disponible.

    6. Sélectionnez **Objet blob de stockage** comme **Type de source**. Vous avez créé un disque à partir d’un objet blob dans un compte de stockage.
    7. Pour la source VHD, sélectionnez :
        1. L’abonnement source où se trouve le compte de stockage.
        1. Sélectionnez **Parcourir**, puis accédez à votre compte de stockage, à votre conteneur et à votre disque dur virtuel. Choisissez **Sélectionner**.
        1. Sélectionnez le **type de système d’exploitation** qui correspond au disque dur virtuel.
    8. Sélectionnez une **taille de disque (en Gio)** supérieure ou égale à celle de votre disque dur virtuel.
    9. Sélectionnez **Create** (Créer).

4. Une fois le disque créé, vous pouvez l’utiliser pour créer une machine virtuelle.