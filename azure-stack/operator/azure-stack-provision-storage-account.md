---
title: Comptes de stockage dans Azure Stack | Microsoft Docs
description: Découvrez comment créer un compte de stockage Azure Stack.
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
ms.openlocfilehash: 60336477f1dec9618fd6cc439e9d2f5d098b3399
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71829379"
---
# <a name="storage-accounts-in-azure-stack"></a>Comptes de stockage dans Azure Stack

Les comptes de stockage dans Azure Stack incluent les services Blob et Table ainsi que l’espace de noms unique pour vos objets de données de stockage. Par défaut, les données de votre compte sont uniquement accessibles par vous, le propriétaire du compte de stockage.

1. Sur l’ordinateur Azure Stack POC, connectez-vous à `https://adminportal.local.azurestack.external` en tant [qu’administrateur](../asdk/asdk-connect.md), puis cliquez sur **+ Créer une ressource** > **Données + stockage** > **Compte de stockage**.

   ![Créer votre compte de stockage](media/azure-stack-provision-storage-account/image01.png)
2. Dans le panneau **Créer un compte de stockage**, tapez un nom pour votre compte de stockage. Créer un **Groupe de ressources** ou sélectionnez un groupe existant, puis cliquez sur **Créer** pour créer le compte de stockage.

   ![Examiner votre compte de stockage](media/azure-stack-provision-storage-account/image02.png)
3. Pour voir votre nouveau compte de stockage, cliquez sur **Toutes les ressources**, puis recherchez le compte de stockage et cliquez sur son nom.

    ![Nom de votre compte de stockage](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>Étapes suivantes
[Utiliser les modèles Azure Resource Manager](../user/azure-stack-arm-templates.md)

[Découvrir les comptes de stockage Azure](/azure/storage/common/storage-create-storage-account)

[Télécharger le guide de validation du stockage ACS (Azure-Consistent Storage) d’Azure Stack](https://aka.ms/azurestacktp1doc)
