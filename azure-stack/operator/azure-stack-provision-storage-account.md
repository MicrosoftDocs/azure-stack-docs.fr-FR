---
title: Créer des comptes de stockage dans Azure Stack Hub
titleSuffix: Azure Stack Hub
description: Découvrez comment créer des comptes de stockage dans Azure Stack Hub.
author: ihenkel
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: inhenkel
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: 87e58fc05794363e630a2b8a474f3a0bdb174bb4
ms.sourcegitcommit: b2173b4597057e67de1c9066d8ed550b9056a97b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77491949"
---
# <a name="create-storage-accounts-in-azure-stack-hub"></a>Créer des comptes de stockage dans Azure Stack Hub

Les comptes de stockage dans Azure Stack Hub incluent les services Blob et Table ainsi que l’espace de noms unique pour vos objets de données de stockage. Par défaut, les données de votre compte sont uniquement accessibles par vous, le propriétaire du compte de stockage.

1. Sur l’ordinateur Azure Stack Hub POC, connectez-vous à `https://adminportal.local.azurestack.external` en tant [qu’administrateur](../asdk/asdk-connect.md), puis cliquez sur **+ Créer une ressource** > **Données + stockage** > **Compte de stockage**.

   ![Créer votre compte de stockage dans le portail administrateur Azure Stack Hub](media/azure-stack-provision-storage-account/image01.png)

2. Dans le panneau **Créer un compte de stockage**, tapez un nom pour votre compte de stockage. Créer un **Groupe de ressources** ou sélectionnez un groupe existant, puis cliquez sur **Créer** pour créer le compte de stockage.

   ![Passer en revue votre compte de stockage dans le portail administrateur Azure Stack Hub](media/azure-stack-provision-storage-account/image02.png)

3. Pour voir votre nouveau compte de stockage, cliquez sur **Toutes les ressources**, puis recherchez le compte de stockage et cliquez sur son nom.

    ![Votre nom de compte de stockage dans le portail administrateur Azure Stack Hub](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>Étapes suivantes

- [Utiliser les modèles Azure Resource Manager](../user/azure-stack-arm-templates.md)
- [Découvrir les comptes de stockage Azure](/azure/storage/common/storage-create-storage-account)
- [Télécharger le guide de validation du stockage ACS (Azure-Consistent Storage) d’Azure Stack Hub](https://aka.ms/azurestacktp1doc)
