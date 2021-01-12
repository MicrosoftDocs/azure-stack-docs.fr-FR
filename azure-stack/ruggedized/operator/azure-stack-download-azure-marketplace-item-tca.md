---
title: Télécharger des éléments de la Place de marché Azure et les publier sur Azure Stack | Microsoft Docs
description: Découvrez comment télécharger des éléments de la Place de marché à partir d’Azure et les publier sur Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/02/2020
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 01/02/2020
ms.openlocfilehash: a3d8fcc1c3ffa65539b233a8b89f55d57f61a5ef
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910616"
---
# <a name="download-marketplace-items-to-azure-stack-hub-ruggedized"></a>Télécharger des éléments de la Place de marché vers Azure Stack Hub (renforcé)

En tant qu’opérateur cloud, vous pouvez télécharger des éléments vers Azure Stack Hub à partir de la Place de marché, et les rendre disponibles pour tous les utilisateurs dans l’environnement Azure Stack Hub. Les éléments que vous pouvez choisir font partie d’une liste d’éléments de la Place de marché Azure testés au préalable et prenant en charge le travail avec Azure Stack. De nouveaux éléments sont fréquemment ajoutés à cette liste, consultez-la régulièrement pour voir de nouveaux contenus.

Il existe deux scénarios pour télécharger des produits de la Place de marché :

- **Scénario connecté** : votre environnement Azure Stack Hub doit être connecté à Internet. Vous utilisez le portail administrateur Azure Stack Hub pour localiser et télécharger des éléments.
- **Scénario déconnecté ou partiellement connecté** : vous devez accéder à Internet à l’aide de l’outil de syndication de la Place de marché pour télécharger des éléments de la Place de marché. Ensuite, vous transférez vos téléchargements pour votre installation déconnectée de Azure Stack. Ce scénario utilise PowerShell.

Voir [Éléments Place de marché Azure pour Azure Stack](../../operator/azure-stack-marketplace-azure-items.md) pour obtenir la liste complète des éléments de la Place de marché que vous pouvez télécharger. Pour obtenir la liste des ajouts, suppressions et mises à jour récents de la Place de marché Azure Stack, consultez l’article [Modifications de la Place de marché Azure Stack](../../operator/azure-stack-marketplace-changes.md).

> [!NOTE]
> Le catalogue est différent selon le cloud auquel votre système Azure Stack Hub est connecté. L’environnement cloud est déterminé par l’abonnement Azure que vous utilisez pour inscrire votre infrastructure Azure Stack Hub.

## <a name="connected-scenario"></a>Scénario connecté

Si Azure Stack Hub se connecte à Internet, vous pouvez utiliser le portail administrateur pour télécharger des éléments de la Place de marché.

### <a name="prerequisites"></a>Prérequis

Votre déploiement Azure Stack Hub doit disposer d’une connectivité à Internet et être [inscrit auprès d’Azure](registration-tzl.md).

### <a name="use-the-portal-to-download-marketplace-items"></a>Utiliser le portail pour télécharger les éléments de la Place de marché

1. Connectez-vous au portail administrateur Azure Stack Hub.

2. Vérifiez l’espace de stockage disponible avant de télécharger des éléments de la Place de marché. Plus tard, lorsque vous sélectionnez des éléments pour le téléchargement, vous pouvez comparer la taille du téléchargement avec votre capacité de stockage disponible. Si la capacité est limitée, envisagez des options pour [gérer l’espace disponible](../../operator/azure-stack-manage-storage-shares.md#manage-available-space).

   Pour passer en revue l’espace disponible, dans **Gestion des régions**, sélectionnez la région que vous voulez explorer, puis accédez à **Fournisseurs de ressources** > **Stockage** :

   ![Passer en revue l’espace de stockage dans le portail administrateur Azure Stack](media/azure-stack-download-azure-marketplace-item-tzl/storage.png)

3. Ouvrez la Place de marché Azure Stack Hub, puis connectez-vous à Azure. Pour ce faire, sélectionnez le service **Gestion de la Place de marché**, sélectionnez **Éléments de la place de marché**, puis sélectionnez **Ajouter à partir d’Azure** :

   ![Ajouter des éléments de la Place de marché à partir d’Azure](media/azure-stack-download-azure-marketplace-item-tzl/marketplace.png)

4. Chaque élément de ligne affiche également la version actuellement disponible. Si plusieurs versions d’un élément de la Place de marché sont disponibles, la colonne **Version** affiche **Multiple**. Vous pouvez cliquer sur chaque élément pour voir sa description et d’autres informations, notamment sa taille de téléchargement :

   ![Capture d’écran montrant la page « Ajouter à partir d’Azure » avec plusieurs entrées de version mises en surbrillance.](media/azure-stack-download-azure-marketplace-item-tzl/add-from-azure1.png)

5. Si la version d’un élément est affichée comme **Multiple**, vous pouvez sélectionner cet élément, puis choisir une version spécifique dans la liste déroulante du sélecteur de version qui en résulte :

   ![Ajout à partir d’Azure](media/azure-stack-download-azure-marketplace-item-tzl/add-from-azure3.png)

6. Sélectionnez les éléments que vous souhaitez, puis sélectionnez **Télécharger**. Les temps de téléchargement varient et dépendent de la connectivité réseau. Une fois le téléchargement terminé, vous pouvez déployer le nouvel élément de la Place de marché en tant qu’opérateur ou utilisateur Azure Stack.

7. Pour déployer l’élément téléchargé, sélectionnez **+ Créer une ressource** puis recherchez le nouvel élément de Place de marché parmi les catégories. Cliquez ensuite sur l’élément pour commencer le processus de déploiement. Le processus varie pour les différents éléments de la Place de marché.

## <a name="disconnected-or-a-partially-connected-scenario"></a>Scénario déconnecté ou partiellement connecté

Dans un environnement déconnecté, vous ne pouvez pas télécharger les éléments de la Place de marché depuis Azure. Vous devez utiliser l’outil de syndication hors connexion pour télécharger les éléments de la Place de marché sur un disque local, puis charger ces éléments dans votre instance Azure Stack Hub à partir de cet emplacement.

Vous pouvez [télécharger les outils de syndication hors connexion ici](https://aka.ms/azsSyndicationtool).

### <a name="download-marketplace-items-from-azure"></a>Télécharger des éléments de la Place de marché depuis Azure

#### <a name="prerequisites"></a>Prérequis

- Azure PowerShell installé sur l’ordinateur.

- ID d’abonnement Azure, groupe de ressources de l’inscription et nom de l’inscription utilisés pour inscrire votre instance Azure Stack Hub. Ces informations sont disponibles sous l’onglet **Propriétés** du panneau **Gestion des régions** au sein du portail opérateur Azure Stack Hub.

- Nom d’utilisateur et mot de passe du compte doté des privilèges de propriétaire/contributeur pour l’ID d’abonnement Azure.

- Disque local ou chemin d’accès réseau vers lequel les fichiers hors connexion peuvent être écrits.

#### <a name="download-items"></a>Télécharger des éléments

1. Ouvrez PowerShell et accédez au dossier extrait.

2. Exécutez le script PowerShell **Invoke-AzSMarketplaceDownload.ps1** :

    ```powershell
    .\Invoke-AzSMarketplaceDownload.ps1 -RegistrationSubscriptionId '<subscription ID>' ` 
       -RegistrationResourceGroup 'azurestack' -RegistrationName '<registration name>' `
       -TenantName mytenant.onmicrosoft.com -DownloadFolder 'F:\offlineSyndication'
    ```

    Si vous êtes déjà connecté via Azure PowerShell, vous pouvez également passer le contexte Azure :

    ```powershell
    Add-AzureRmAccount -Environment AzureCloud -Tenant mytenant.onmicrosoft.com 
    .\Invoke-AzSMarketplaceDownload.ps1 -RegistrationResourceGroup 'azurestack' -RegistrationName '<registration name>' -DownloadFolder 'F:\offlineSyndication' -AzureContext $(Get-AzureRMContext)
    ```

    Si vous ne transmettez pas le contexte Azure, vous êtes invité à vous connecter.

3. Une fenêtre s’affiche pour vous permettre de sélectionner le produit que vous souhaitez télécharger. Utilisez Ctrl + clic pour sélectionner plusieurs éléments.

4. Sélectionnez **OK**. Cela télécharge l’élément de la Place de marché, ainsi que ses dépendances, le cas échéant.

### <a name="upload-marketplace-items-to-azure-stack-hub"></a>Charger des éléments de la Place de marché vers Azure Stack Hub

#### <a name="prerequisites"></a>Prérequis

- Point de terminaison Resource Manager d’administrateur Azure Stack et locataire d’annuaire.

- Accès aux éléments de la Place de marché hors connexion.

#### <a name="upload-items"></a>Charger des éléments

1. Ouvrez PowerShell et accédez au dossier extrait.

2. Exécutez le script PowerShell **Invoke-AzSMarketplaceUpload.ps1** :

    ```powershell
    .\Invoke-AzsMarketplaceUpload.ps1 -AzureStackCloudName "AzureStack-Admin" -AzureStackAdminARMEndpoint https://adminmanagement.<region>.<fqdn> -TenantName mytenant.onmicrosoft.com -DownloadFolder F:\offlineSyndication
    ```

    Vous pouvez également configurer vous-même l’environnement Azure Stack dans Azure PowerShell, vous authentifier auprès du point de terminaison Resource Manager d’administrateur et passer le contexte au script :

    ```powershell
    Add-AzureRmEnvironment -Name Redmond-Admin -ARMEndpoint https://adminmanagement.redmond.azurestack.corp.microsoft.com

    Add-AzureRmAccount -Environment Redmond-Admin

    .\Invoke-AzsMarketplaceUpload.ps1 -DownloadFolder F:\Downloads\offlining -AzureContext $(Get-AzureRmContext)
    ```

    Cette procédure permet de charger les éléments de la Place de marché dans l’instance Azure Stack Hub spécifiée.
