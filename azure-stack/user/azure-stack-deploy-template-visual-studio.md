---
title: Déployer des modèles avec Visual Studio dans Azure Stack Hub | Microsoft Docs
description: Découvrez comment déployer des modèles avec Visual Studio dans Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 628da2ae-64cc-42e0-b8b7-a6a3724cb974
ms.service: azure-stack
ms.custom: vs-azure
ms.workload: azure-vs
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: 86cd483b2416dc613ac74cdca77e7bf3f94b1fe6
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76536281"
---
# <a name="deploy-templates-in-azure-stack-hub-using-visual-studio"></a>Déploiement de modèles dans Azure Stack Hub à l’aide de Visual Studio

Vous pouvez utiliser Visual Studio pour déployer des modèles Azure Resource Manager dans Azure Stack Hub.

## <a name="to-deploy-a-template"></a>Pour déployer un modèle

1. [Procédez à l’installation et à la connexion](azure-stack-install-visual-studio.md) d’Azure Stack Hub avec Visual Studio.
2. Ouvrez Visual Studio.
3. Sélectionnez **Fichier**, puis **Nouveau**. Dans **Nouveau projet**, sélectionnez **Groupe de ressources Azure**.
4. Entrez le **Nom** du nouveau projet, puis sélectionnez **OK**.
5. Dans **Sélectionner un modèle Azure**, choisissez **Démarrage rapide Azure Stack Hub** dans la liste déroulante.
6. Sélectionnez **101-create-storage-account**, puis **OK**.
7. Dans votre nouveau projet, développez le nœud **Modèles** dans **l’Explorateur de solutions** pour voir les modèles disponibles.
8. Dans **l’Explorateur de solutions**, choisissez le nom de votre projet, puis sélectionnez **Déployer**. Sélectionnez **Nouveau déploiement**.
9. Dans **Déployer vers le groupe de ressources**, utilisez la liste déroulante **Abonnement** pour sélectionner votre abonnement Microsoft Azure Stack Hub.
10. Dans la liste **Groupe de ressources**, choisissez un groupe de ressources existant ou créez-en un.
11. Dans la liste **Emplacement du groupe de ressources**, choisissez un emplacement, puis sélectionnez **Déployer**.
12. Dans **Modifier les paramètres**, fournissez des valeurs pour les paramètres (qui varient selon le modèle), puis sélectionnez **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

* [Déployer des modèles avec la ligne de commande](azure-stack-deploy-template-command-line.md)
* [Développer des modèles pour Azure Stack Hub](azure-stack-develop-templates.md)
