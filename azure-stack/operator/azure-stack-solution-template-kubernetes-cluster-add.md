---
title: Ajouter Kubernetes sur la Place de marché Azure Stack | Microsoft Docs
description: Découvrez comment ajouter Kubernetes sur la Place de marché Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 06/18/2019
ms.openlocfilehash: 61d2739475a0593671e7a363671dd2859a6e6f24
ms.sourcegitcommit: 3f52cf06fb5b3208057cfdc07616cd76f11cdb38
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67316248"
---
# <a name="add-kubernetes-to-the-azure-stack-marketplace"></a>Ajouter Kubernetes sur la Place de marché Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

> [!note]  
> Kubernetes sur Azure Stack est en préversion. Un scénario Azure Stack déconnecté n'est actuellement pas pris en charge par la préversion. Utiliser uniquement l’élément de place de marché pour les scénarios de développement et de test.

Vous pouvez créer une offre Kubernetes et la mettre à disposition des utilisateurs en tant qu’élément de la Place de marché. Vos utilisateurs peuvent alors déployer Kubernetes en une seule opération coordonnée.

L’article suivant décrit comment utiliser un modèle Azure Resource Manager pour déployer et provisionner les ressources d’un cluster Kubernetes autonome. Avant de commencer, vérifiez les paramètres Azure Stack et les paramètres du locataire Azure global. Collectez les informations sur Azure Stack requises. Ajoutez les ressources nécessaires à votre locataire et à la Place de marché Azure Stack. Le cluster dépend d’un serveur Ubuntu, du script personnalisé et un élément Place de marché du cluster Kubernetes à ajouter à la Place de marché.

## <a name="create-a-plan-an-offer-and-a-subscription"></a>Créer un plan, une offre et un abonnement

Créez un plan, une offre et un abonnement pour l’élément Kubernetes de la Place de marché. Vous pouvez également utiliser un plan et une offre existants.

1. Connectez-vous au [portail d’administration](https://adminportal.local.azurestack.external).

1. Créez un plan de base. Pour obtenir des instructions, consultez [Créer un plan dans Azure Stack](azure-stack-create-plan.md).

1. Créez une offre. Pour obtenir des instructions, consultez [Créer une offre dans Azure Stack](azure-stack-create-offer.md).

1. Sélectionnez **Offres**et recherchez l’offre que vous venez de créer.

1. Sélectionnez **Vue d’ensemble** dans le panneau Offre.

1. Sélectionnez **Changer l’état**. Sélectionnez **Public**.

1. Sélectionnez **+ Créer une ressource** > **Offres et plans** > **Abonnement** pour créer un abonnement.

    a. Entrez un **nom d’affichage**.

    b. Entrez un **utilisateur**. Utilisez le compte Azure AD associé au locataire.

    c. Entrez une **description du fournisseur**

    d. Définissez le **locataire d’annuaire** sur le locataire Azure AD dans votre environnement Azure Stack. 

    e. Sélectionnez **Offre**. Sélectionnez le nom de l’offre que vous avez créée. Prenez note de l’ID d’abonnement.

## <a name="create-a-service-principal-and-credentials-in-ad-fs"></a>Créer un principal de service et des informations d’identification dans AD FS

Si vous utilisez AD FS (Active Directory Federated Services) pour votre service de gestion des identités, vous devez créer un principal de service pour les utilisateurs qui déploient un cluster Kubernetes. Créer un principal de service à l’aide d’une clé secrète client. Pour obtenir des instructions, consultez [Créer un principal de service à l’aide d’une clé secrète client](azure-stack-create-service-principals.md#create-a-service-principal-that-uses-client-secret-credentials).

## <a name="add-an-ubuntu-server-image"></a>Ajouter une image de serveur Ubuntu

Ajoutez l’image de serveur Ubuntu suivante sur la Place de marché :

1. Connectez-vous au [portail d’administration](https://adminportal.local.azurestack.external).

1. Sélectionnez **Tous les services**, puis, dans la catégorie **ADMINISTRATION**, sélectionnez **Gestion de la Place de marché**.

1. Sélectionnez **+ Ajouter à partir d’Azure**.

1. Entrez `Ubuntu Server`.

1. Sélectionnez la version la plus récente du serveur. Consultez la version complète et assurez-vous que vous avez la version la plus récente :
    - **Éditeur** : Canonical
    - **Offre** : UbuntuServer
    - **Version** : 16.04.201806120 (ou la dernière version)
    - **SKU** : 16.04-LTS

1. Sélectionnez **Télécharger**.

## <a name="add-a-custom-script-for-linux"></a>Ajouter un script personnalisé pour Linux

Ajoutez Kubernetes à partir de la Place de marché :

1. Ouvrez le [portail d’administration](https://adminportal.local.azurestack.external).

1. Sélectionnez **Tous les services**, puis, dans la catégorie **ADMINISTRATION**, sélectionnez **Gestion de la Place de marché**.

1. Sélectionnez **+ Ajouter à partir d’Azure**.

1. Entrez `Custom Script for Linux`.

1. Sélectionnez le script avec le profil suivant :
   - **Offre** : Script personnalisé pour Linux 2.0
   - **Version** : 2.0.6 (ou la dernière version)
   - **Éditeur** : Microsoft Corp

     > [!Note]  
     > Plusieurs versions d’un script personnalisé pour Linux peuvent être répertoriées. Vous devez ajouter la dernière version de l’élément.

1. Sélectionnez **Télécharger**.


## <a name="add-kubernetes-to-the-marketplace"></a>Ajouter Kubernetes sur la Place de marché

1. Ouvrez le [portail d’administration](https://adminportal.local.azurestack.external).

1. Sélectionnez **Tous les services** et, sous la catégorie **ADMINISTRATION**, sélectionnez **Gestion de la Place de marché**.

1. Sélectionnez **+ Ajouter à partir d’Azure**.

1. Entrez `Kubernetes`.

1. Sélectionnez `Kubernetes Cluster`.

1. Sélectionnez **Télécharger**.

    > [!note]  
    > L’affichage de l’élément sur la Place de marché peut prendre jusqu’à cinq minutes.

    ![kubernetes](../user/media/azure-stack-solution-template-kubernetes-deploy/marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes"></a>Mettre à jour ou supprimer Kubernetes 

Quand vous mettez à jour l’élément Kubernetes, vous supprimez l’élément existant sur la Place de marché. Suivez les instructions fournies dans cet article pour ajouter la mise à jour Kubernetes à la Place de marché.

Pour supprimer l’élément Kubernetes :

1. Connectez-vous en tant qu’opérateur à Azure Stack à l’aide de PowerShell. Pour plus d’informations, consultez [Se connecter en tant qu’opérateur à Azure Stack à l’aide de PowerShell](azure-stack-powershell-configure-admin.md).

2. Recherchez l’élément Cluster Kubernetes actuel dans la galerie.

    ```powershell  
    Get-AzsGalleryItem | Select Name
    ```
    
3. Notez le nom de l’élément actuel (par exemple, `Microsoft.AzureStackKubernetesCluster.0.3.0`)

4. Pour supprimer l’élément, utilisez l’applet de commande PowerShell suivante :

    ```powershell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.3.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>Étapes suivantes

[Déployer Kubernetes sur Azure Stack](../user/azure-stack-solution-template-kubernetes-deploy.md)

[Vue d’ensemble de l’offre de services dans Azure Stack](azure-stack-offer-services-overview.md)
