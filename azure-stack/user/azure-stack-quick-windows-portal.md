---
title: Créer une machine virtuelle Windows avec le portail Azure Stack Hub
description: Découvrez comment créer une machine virtuelle Windows Server 2016 avec le portail Azure Stack Hub.
author: mattbriggs
ms.topic: quickstart
ms.date: 1/10/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 1/10/2020
ms.openlocfilehash: 0792d0dffa8a61194b0f6725aba9222d69c8634e
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76883291"
---
# <a name="quickstart-create-a-windows-server-vm-with-the-azure-stack-hub-portal"></a>Démarrage rapide : Créer une machine virtuelle Windows Server avec le portail Azure Stack Hub

Découvrez comment créer une machine virtuelle Windows Server 2016 avec le portail Azure Stack Hub.

> [!NOTE]  
> Les captures d’écran de cet article ont été mises à jour pour correspondre à l’interface utilisateur introduite avec Azure Stack Hub version 1808. Cette version ajoute la prise en charge de l’utilisation de *disques managés* à celle des disques non managés. Si vous utilisez une version antérieure, certaines images, par exemple la sélection des disques, sont différentes de celles qui sont affichées dans cet article.  


## <a name="sign-in-to-the-azure-stack-hub-portal"></a>Se connecter au portail Azure Stack Hub

Connectez-vous au portail Azure Stack Hub. L’adresse du portail Azure Stack Hub varie en fonction du produit Azure Stack Hub auquel vous vous connectez :

* Pour le Kit de développement Azure Stack (ASDK), accédez à : https://portal.local.azurestack.external.
* Pour un système intégré Azure Stack Hub, accédez à l’URL fournie par votre opérateur Azure Stack Hub.

## <a name="create-a-vm"></a>Créer une machine virtuelle

1. Sélectionnez **Créer une ressource** > **Calcul**. Recherchez ` Windows Server 2016 Datacenter – Pay as you use`.
    Si l’entrée **Windows Server 2016 Datacenter – Paiement à l’utilisation** n’apparaît pas, contactez votre opérateur cloud Azure Stack Hub et demandez-lui d’ajouter l’image à la Place de marché Azure Stack Hub. Pour obtenir des instructions, votre opérateur cloud peut se référer à [Créer et publier un élément personnalisé de Place de marché Azure Stack Hub](../operator/azure-stack-create-and-publish-marketplace-item.md).

    ![Windows Server 2016 Datacenter – Paiement à l’utilisation](./media/azure-stack-quick-windows-portal/image1.png)

1. Sélectionnez **Create** (Créer).

    ![Créer une ressource](./media/azure-stack-quick-windows-portal/image2.png)

1. Tapez un **Nom**, un **Type de disque**, un **Nom d’utilisateur** et un **Mot de passe** sous **Paramètres de base**. Choisissez un **Abonnement**. Créez un **Groupe de ressources** ou sélectionnez un groupe existant, sélectionnez un **Emplacement**, puis sélectionnez **OK**.

    ![Créer une machine virtuelle – Paramètres de base](./media/azure-stack-quick-windows-portal/image3.png)

1. Sélectionnez **D1_v2** sous **Taille**, puis choisissez **Sélectionner**.

    ![Créer une machine virtuelle – Taille](./media/azure-stack-quick-windows-portal/image4.png)

1. Dans la page **Paramètres**, apportez les modifications souhaitées aux valeurs par défaut. Vous devez configurer les ports entrants publics souhaités à partir de la liste déroulante associée. Quand vous avez terminé, cliquez sur **OK**.

    ![Créer une machine virtuelle – Paramètres](./media/azure-stack-quick-windows-portal/image5.png)

1. Sélectionnez **OK** sous **Résumé** pour créer la machine virtuelle.

    ![Créer une machine virtuelle – Résumé](./media/azure-stack-quick-windows-portal/image6.png)

1. Sélectionnez **Machines virtuelles** pour passer en revue votre nouvelle machine virtuelle. Recherchez le nom de la machine virtuelle, puis sélectionnez la machine virtuelle dans les résultats de la recherche.

![Créer une machine virtuelle – Rechercher une machine virtuelle](./media/azure-stack-quick-windows-portal/image7.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Une fois que vous avez fini d’utiliser la machine virtuelle, supprimez-la ainsi que ses ressources. Pour ce faire, sélectionnez le groupe de ressources dans la page de la machine virtuelle, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez déployé une machine virtuelle Windows Server de base. Pour en savoir plus sur les machines virtuelles Azure Stack Hub, continuez avec [Considérations relatives aux machines virtuelles dans Azure Stack Hub](azure-stack-vm-considerations.md).
