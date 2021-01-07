---
title: Créer des comptes de stockage dans Azure Stack Hub
titleSuffix: Azure Stack Hub
description: Découvrez comment créer des comptes de stockage dans Azure Stack Hub.
author: PatAltimore
ms.topic: how-to
ms.date: 1/22/2020
ms.author: patricka
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: 953ba2c8ee1c5d5950b4f42b0771d5c0976c2d89
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97869540"
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
