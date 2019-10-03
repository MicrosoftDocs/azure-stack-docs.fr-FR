---
title: Créer une machine virtuelle Windows avec le portail Azure Stack | Microsoft Docs
description: Découvrez comment créer une machine virtuelle Windows Server 2016 avec le portail Azure Stack.
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 10/02/2019
ms.author: mabrigg
ms.custom: mvc
ms.reviewer: kivenkat
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 95fef782ca7efe09f7c93fbf0e28e81ed34d8166
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71823927"
---
# <a name="quickstart-create-a-windows-server-vm-with-the-azure-stack-portal"></a>Démarrage rapide : Créer une machine virtuelle Windows Server avec le portail Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Découvrez comment créer une machine virtuelle Windows Server 2016 à l’aide du portail Azure Stack.

> [!NOTE]  
> Les captures d’écran de cet article ont été mises à jour pour correspondre à l’interface utilisateur introduite avec Azure Stack version 1808. Cette version ajoute la prise en charge de l’utilisation de *disques managés* à celle des disques non managés. Si vous utilisez une version antérieure, certaines images, par exemple la sélection des disques, sont différentes de celles qui sont affichées dans cet article.  


## <a name="sign-in-to-the-azure-stack-portal"></a>Se connecter au portail Azure Stack

Connectez-vous au portail Azure Stack. L’adresse du portail Azure Stack varie en fonction du produit Azure Stack auquel vous vous connectez :

* Pour le Kit de développement Azure Stack (ASDK), accédez à : https://portal.local.azurestack.external.
* Pour un système intégré Azure Stack, accédez à l’URL fournie par votre opérateur Azure Stack.

## <a name="create-a-vm"></a>Créer une machine virtuelle

1. Cliquez sur **+ Créer une ressource** > **Calcul** > **Windows Server 2016 Datacenter – Paiement à l’utilisation** > **Créer**. <br> Si vous ne voyez pas l’entrée **Windows Server 2016 Datacenter - Paiement à l’utilisation**, contactez votre opérateur Azure Stack et demandez-lui de l’ajouter à la place de marché, comme expliqué dans l’article [Ajouter l’image de machine virtuelle Windows Server 2016 sur la Place de marché Azure Stack](../operator/azure-stack-create-and-publish-marketplace-item.md).

    ![Étapes de création d’une machine virtuelle Windows dans le portail](media/azure-stack-quick-windows-portal/image01.png)

2. Sous **De base**, tapez un **Nom**, un **Nom d’utilisateur** et un **Mot de passe**. Choisissez un **Abonnement**. Créez un **Groupe de ressources** ou sélectionnez un groupe existant, sélectionnez un **Emplacement**, puis cliquez sur **OK**.

    ![Configurer les paramètres de base](media/azure-stack-quick-windows-portal/image02.png)

3. Sous **Taille**, sélectionnez **D1 Standard**, puis cliquez sur **Sélectionner**.  

    ![Choisir la taille de la machine virtuelle](media/azure-stack-quick-windows-portal/image03.png)

4. Dans la page **Paramètres**, apportez les modifications souhaitées aux valeurs par défaut.
   - À compter de la version 1808 d’Azure Stack, vous pouvez désormais choisir d’utiliser des *disques managés* lorsque vous configurez le **Stockage**. Avant la version 1808, seuls les disques non managés pouvaient être utilisés.  

   ![Configurer les paramètres de la machine virtuelle](media/azure-stack-quick-windows-portal/image04.png)  

   Lorsque vos configurations sont prêtes, sélectionnez **OK** pour continuer.

5. Sous **Résumé**, cliquez sur **OK** pour créer la machine virtuelle.
    ![Afficher le récapitulatif et créer la machine virtuelle](media/azure-stack-quick-windows-portal/image05.png)

6. Pour voir votre nouvelle machine virtuelle, cliquez sur **Toutes les ressources**, recherchez le nom de la machine virtuelle, puis sélectionnez-le dans les résultats de la recherche.

    ![Voir la machine virtuelle](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Une fois que vous avez fini d’utiliser la machine virtuelle, supprimez-la ainsi que ses ressources. Pour ce faire, sélectionnez le groupe de ressources dans la page de la machine virtuelle, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez déployé une machine virtuelle Windows Server de base. Pour en savoir plus sur les machines virtuelles Azure Stack, continuez avec [Considérations relatives aux machines virtuelles dans Azure Stack](azure-stack-vm-considerations.md).
