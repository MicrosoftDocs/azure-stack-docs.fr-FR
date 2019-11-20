---
title: Créer des comptes de stockage dans Azure Stack | Microsoft Docs
titleSuffix: Azure Stack
description: Découvrez comment créer des comptes de stockage dans Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/2/2019
ms.author: mabrigg
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: 449d9e39b650e6f7ccd91f4703709ea033e7a5dc
ms.sourcegitcommit: ca358ea5c91a0441e1d33f540f6dbb5b4d3c92c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73802350"
---
# <a name="create-storage-accounts-in-azure-stack"></a>Créer des comptes de stockage dans Azure Stack

Les comptes de stockage dans Azure Stack incluent les services Blob et Table ainsi que l’espace de noms unique pour vos objets de données de stockage. Par défaut, les données de votre compte sont uniquement accessibles par vous, le propriétaire du compte de stockage.

1. Sur l’ordinateur Azure Stack POC, connectez-vous à `https://adminportal.local.azurestack.external` en tant [qu’administrateur](../asdk/asdk-connect.md), puis cliquez sur **+ Créer une ressource** > **Données + stockage** > **Compte de stockage**.

   ![Créer votre compte de stockage dans le portail administrateur Azure Stack](media/azure-stack-provision-storage-account/image01.png)

2. Dans le panneau **Créer un compte de stockage**, tapez un nom pour votre compte de stockage. Créer un **Groupe de ressources** ou sélectionnez un groupe existant, puis cliquez sur **Créer** pour créer le compte de stockage.

   ![Passer en revue votre compte de stockage dans le portail administrateur Azure Stack](media/azure-stack-provision-storage-account/image02.png)

3. Pour voir votre nouveau compte de stockage, cliquez sur **Toutes les ressources**, puis recherchez le compte de stockage et cliquez sur son nom.

    ![Votre nom de compte de stockage dans le portail administrateur Azure Stack](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>Étapes suivantes

- [Utiliser les modèles Azure Resource Manager](../user/azure-stack-arm-templates.md)
- [Découvrir les comptes de stockage Azure](/azure/storage/common/storage-create-storage-account)
- [Télécharger le guide de validation du stockage ACS (Azure-Consistent Storage) d’Azure Stack](https://aka.ms/azurestacktp1doc)
