---
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: include
ms.date: 08/04/2020
ms.reviewer: thoroet
ms.lastreviewed: 08/04/2020
ms.openlocfilehash: ac28bcee33cbe7cd54d8f94408620f6f4b0d9d11
ms.sourcegitcommit: 3e225b30a54159b6b8dbeb2f843a2e5a721b746e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91519412"
---
1. Connectez-vous au portail utilisateur Azure Stack Hub.

    Si vous êtes un opérateur cloud qui crée une image de plateforme, suivez les instructions de la rubrique [Ajouter une image de plateforme](/azure-stack/operator/azure-stack-add-vm-image#add-a-platform-image) pour ajouter le disque dur virtuel via le portail d’administration ou avec les points de terminaison d’administrateur.

2. Dans le portail utilisateur, sélectionnez **Tous les services** > **Images** > **Ajouter**.

3. Dans **Créer une image** :

    1. Tapez le **nom** de votre image.
    2. Sélectionnez votre **abonnement**.
    3. Créez ou ajoutez l’image à un **groupe de ressources**.
    4. Sélectionnez l’**emplacement**, également appelé « région », de votre kit ASDK.
    5. Sélectionnez un **type de système d’exploitation** qui correspond à votre image.
    6. Sélectionnez **Parcourir** puis accédez à votre compte de stockage, à votre conteneur et à votre disque dur virtuel. Choisissez **Sélectionner**.
    5. Sélectionnez le **Type de compte**.
        - Les **disques Premium (SSD)** sont associés à des disques SSD afin d’offrir des performances constantes et une faible latence. Ils proposent le meilleur rapport prix/performances et conviennent parfaitement aux applications d’E/S intensives et aux charges de travail de production.  
        - Les **disques standard (HDD)** sont associés à des lecteurs magnétiques et conviennent davantage aux applications dont les données sont rarement utilisées. Les disques redondants interzones sont associés à un stockage redondant interzone (ZRS) qui réplique vos données sur plusieurs zones, et restent disponibles même si une zone n’est pas disponible.

    8. Sélectionnez **Lecture/écriture** pour la récupération de l’hôte.
    9. Sélectionnez **Create** (Créer).

4. Une fois l’image créée, utilisez-la pour créer une machine virtuelle.
