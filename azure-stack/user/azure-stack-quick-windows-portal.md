---
title: Guide de démarrage rapide avec Azure Stack - Créer une machine virtuelle Windows
description: Guide de démarrage rapide avec Azure Stack - Créer une machine virtuelle Windows avec le portail
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: unknown
ms.custom: mvc
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 47a2e2090a4bc6f4d72f5288f31a6e1cb53bdac7
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985965"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-with-the-azure-stack-portal"></a>Démarrage rapide : créer une machine virtuelle Windows Server avec le portail Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Vous pouvez créer une machine virtuelle Windows Server 2016 dans le portail Azure Stack. Suivez les étapes décrites dans cet article pour créer et utiliser une machine virtuelle.

> [!NOTE]  
> Dans cet article, les images d’écran sont mises à jour pour correspondre à l’interface utilisateur présentée avec la version 1808 d’Azure Stack. Cette version ajoute la prise en charge de l’utilisation de *disques managés* à celle des disques non managés. Si vous utilisez une version antérieure, certaines images, comme la sélection de disques, sont différentes de celles qui sont affichées dans cet article.  


## <a name="sign-in-to-the-azure-stack-portal"></a>Se connecter au portail Azure Stack

Connectez-vous au portail Azure Stack. L’adresse du portail Azure Stack varie en fonction du produit Azure Stack auquel vous vous connectez :

* Pour le Kit de développement Azure Stack (ASDK), accédez à : https://portal.local.azurestack.external.
* Pour un système intégré Azure Stack, accédez à l’URL fournie par votre opérateur Azure Stack.

## <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle

1. Cliquez sur **+ Créer une ressource** > **Calcul** > **Windows Server 2016 Datacenter – Paiement à l’utilisation** > **Créer**. Si l’entrée **Windows Server 2016 Datacenter – Paiement à l’utilisation** n’apparaît pas, contactez votre opérateur Azure Stack. Demandez-lui de l’ajouter à la Place de Marché comme expliqué dans l’article [Ajouter l’image de machine virtuelle Windows Server 2016 à la Place de Marché Azure Stack](../operator/azure-stack-create-and-publish-marketplace-item.md).

    ![Procédure de création d’une machine virtuelle Windows dans le portail](media/azure-stack-quick-windows-portal/image01.png)
2. Sous **De base**, tapez un **Nom**, un **Nom d’utilisateur** et un **Mot de passe**. Choisissez un **Abonnement**. Créez un **Groupe de ressources** ou sélectionnez un groupe existant, sélectionnez un **Emplacement**, puis cliquez sur **OK**.

    ![Configurer les paramètres de base](media/azure-stack-quick-windows-portal/image02.png)
3. Sous **Taille** sélectionnez **D1 Standard**, puis cliquez sur **Sélectionner**.  
    ![Choisir la taille de la machine virtuelle](media/azure-stack-quick-windows-portal/image03.png)

4. Dans la page **Paramètres**, apportez les modifications souhaitées aux valeurs par défaut.
   - À compter de la version 1808 d’Azure Stack, vous pouvez désormais choisir d’utiliser des *disques managés* lorsque vous configurez le **Stockage**. Avant la version 1808, seuls les disques non managés peuvent être utilisés.  
   ![Configurer les paramètres des machines virtuelles](media/azure-stack-quick-windows-portal/image04.png)  
   Lorsque vos configurations sont prêtes, sélectionnez **OK** pour continuer.

5. Sous **Résumé**, cliquez sur **OK** pour créer la machine virtuelle.
    ![Afficher le résumé et créer la machine virtuelle](media/azure-stack-quick-windows-portal/image05.png)

6. Pour voir votre nouvelle machine virtuelle, cliquez sur **Toutes les ressources**, recherchez le nom de la machine virtuelle, puis cliquez sur son nom dans les résultats de la recherche.
    ![Voir la machine virtuelle](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous avez fini d’utiliser la machine virtuelle, supprimez-la tout comme ses ressources. Pour ce faire, sélectionnez le groupe de ressources dans la page de la machine virtuelle, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez déployé une machine virtuelle Windows Server de base. Pour en savoir plus sur les machines virtuelles Azure Stack, continuez avec [Considérations relatives aux machines virtuelles dans Azure Stack](azure-stack-vm-considerations.md).
