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
ms.date: 05/16/2019
ms.author: mabrigg
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: 77d4963d2e3e468cb2de1e41a5c483e0339a3449
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782460"
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
