---
title: Télécharger des éléments de la Place de marché Azure et les publier sur Azure Stack Hub
description: Découvrez comment télécharger des éléments de la Place de marché à partir d’Azure et les publier sur Azure Stack Hub.
author: sethmanheim
ms.topic: conceptual
ms.date: 12/16/2020
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 12/16/2020
zone_pivot_groups: state-connected-disconnected
ms.openlocfilehash: f0aafd572aa50760a7b326d5ca699e3f1331b3e9
ms.sourcegitcommit: f30e5178e0b4be4e3886f4e9f699a2b51286e2a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97620651"
---
# <a name="download-marketplace-items-to-azure-stack-hub"></a>Télécharger des éléments de la Place de marché vers Azure Stack Hub

En tant qu’opérateur cloud, vous pouvez télécharger des éléments vers Azure Stack Hub à partir de la Place de marché, et les rendre disponibles pour tous les utilisateurs dans l’environnement Azure Stack Hub. Les éléments que vous pouvez choisir font partie d’une liste d’éléments de la Place de marché Azure testés au préalable et prenant en charge Azure Stack Hub. De nouveaux éléments sont fréquemment ajoutés à cette liste, consultez-la régulièrement pour voir de nouveaux contenus.

Il existe deux scénarios pour télécharger des produits de la Place de marché :

- **Scénario déconnecté ou partiellement connecté** : vous devez accéder à Internet à l’aide de l’outil de syndication de la Place de marché pour télécharger des éléments de la Place de marché. Ensuite, vous transférez vos téléchargements pour votre installation déconnectée de Azure Stack Hub. Ce scénario utilise PowerShell.
- **Scénario connecté** : votre environnement Azure Stack Hub doit être connecté à Internet. Vous utilisez le portail administrateur Azure Stack Hub pour localiser et télécharger des éléments.

Consultez [Éléments Place de marché Azure pour Azure Stack Hub](azure-stack-marketplace-azure-items.md) pour obtenir la liste complète des éléments de la Place de marché que vous pouvez télécharger. Pour obtenir la liste des ajouts, suppressions et mises à jour récents de la Place de marché Azure Stack Hub, consultez l’article [Modifications de la Place de marché Azure Stack Hub](azure-stack-marketplace-changes.md).

> [!NOTE]
> Le catalogue est différent selon le cloud auquel votre système Azure Stack Hub est connecté. L’environnement cloud est déterminé par l’abonnement Azure que vous utilisez pour inscrire votre infrastructure Azure Stack Hub.

[!INCLUDE [Azure Stack Hub Operator Access Workstation](../includes/operator-note-owa.md)]

::: zone pivot="state-connected"
Un déploiement connecté vous permet d’utiliser le portail d’administration pour télécharger des éléments de la Place de marché.

## <a name="prerequisites"></a>Prérequis

Votre déploiement Azure Stack Hub doit disposer d’une connectivité à Internet et être inscrit auprès d’Azure.

## <a name="use-the-portal-to-download-marketplace-items"></a>Utiliser le portail pour télécharger les éléments de la Place de marché

1. Connectez-vous au portail administrateur Azure Stack Hub.

2. Vérifiez l’espace de stockage disponible avant de télécharger des éléments de la Place de marché. Plus tard, lorsque vous sélectionnez des éléments pour le téléchargement, vous pouvez comparer la taille du téléchargement avec votre capacité de stockage disponible. Si la capacité est limitée, envisagez des options pour [gérer l’espace disponible](azure-stack-manage-storage-shares.md#manage-available-space).

   Pour passer en revue l’espace disponible, dans **Gestion des régions**, sélectionnez la région que vous voulez explorer, puis accédez à **Fournisseurs de ressources** > **Stockage** :

   ![Passer en revue l’espace de stockage dans le portail administrateur Azure Stack Hub](media/azure-stack-download-azure-marketplace-item/storage.png)

3. Ouvrez la Place de marché Azure Stack Hub, puis connectez-vous à Azure. Pour ce faire, sélectionnez le service **Gestion de la Place de marché**, sélectionnez **Éléments de la place de marché**, puis sélectionnez **Ajouter à partir d’Azure** :

   ![Ajouter des éléments de la Place de marché à partir d’Azure](media/azure-stack-download-azure-marketplace-item/marketplace.png)

4. Chaque élément de ligne affiche également la version actuellement disponible. Si plusieurs versions d’un élément de la Place de marché sont disponibles, la colonne **Version** affiche **Multiple**. Vous pouvez cliquer sur chaque élément pour voir sa description et d’autres informations, notamment sa taille de téléchargement :

   ![Capture d’écran montrant les versions disponibles d’un élément de la place de marché.](media/azure-stack-download-azure-marketplace-item/add-from-azure1.png)

5. Si la version d’un élément est affichée comme **Multiple**, vous pouvez sélectionner cet élément, puis choisir une version spécifique dans la liste déroulante du sélecteur de version qui en résulte. Notez que Microsoft a désormais la possibilité d’ajouter des attributs qui empêchent les administrateurs de télécharger des produits de la Place de marché qui ne sont pas compatibles avec leurs environnements Azure Stack, en raison de différents propriétés, comme la version Azure Stack ou le modèle de facturation. Seul Microsoft peut ajouter ces attributs :

   [![Ajouter à partir d’Azure](media/azure-stack-download-azure-marketplace-item/add-from-azure3sm.png "Plusieurs versions")](media/azure-stack-download-azure-marketplace-item/add-from-azure3.png#lightbox)

6. Sélectionnez les éléments que vous souhaitez, puis sélectionnez **Télécharger**. Les temps de téléchargement varient et dépendent de la connectivité réseau. Une fois le téléchargement effectué, vous pouvez déployer le nouvel élément de la Place de marché en tant qu’opérateur ou utilisateur Azure Stack Hub.

7. Pour déployer l’élément téléchargé, sélectionnez **+ Créer une ressource** puis recherchez le nouvel élément de Place de marché parmi les catégories. Cliquez ensuite sur l’élément pour commencer le processus de déploiement. Le processus varie pour les différents éléments de la Place de marché.
::: zone-end

::: zone pivot="state-disconnected"
Lorsque la connectivité Internet d’Azure Stack Hub est limitée ou inexistante, utilisez PowerShell et l’*outil de syndication de la Place de marché* pour télécharger les éléments de la Place de marché sur une machine connectée à Internet. Transférez ensuite les éléments vers votre environnement Azure Stack Hub. Dans un environnement déconnecté, vous ne pouvez pas télécharger des éléments de la Place de marché à l’aide du portail Azure Stack Hub.

L’outil de syndication Place de marché peut également être utilisé dans un scénario connecté.

Ce scénario comporte deux parties :

- **Partie 1** : Effectuez le téléchargement des éléments à partir de la Place de marché. Sur l’ordinateur ayant accès à Internet, configurez PowerShell, téléchargez l’outil de syndication, puis téléchargez les éléments de la Place de marché Azure.
- **Partie 2** : Effectuez le chargement et la publication sur la Place de marché Azure Stack Hub. Déplacez les fichiers que vous avez téléchargés dans votre environnement Azure Stack Hub, puis publiez-les sur la Place de marché Azure Stack Hub.

## <a name="prerequisites"></a>Prérequis

- Un environnement connecté (qui n’est pas nécessairement Azure Stack Hub). Vous avez besoin d’une connectivité pour accéder à la liste des produits d’Azure avec leurs détails et pour tout télécharger localement. Une fois cette opération effectuée, le reste de la procédure ne nécessite aucune connectivité Internet. Elle crée un catalogue d’éléments que vous avez précédemment téléchargés, que vous pouvez utiliser dans votre environnement déconnecté.

- Un média amovible pour vous connecter à votre environnement déconnecté et transférer tous les artefacts nécessaires.

- Un environnement Azure Stack Hub déconnecté avec les prérequis suivants :

  - Votre déploiement Azure Stack Hub doit être inscrit auprès d’Azure.

  - L’ordinateur qui est connecté à Internet doit disposer du **module PowerShell pour Azure Stack Hub version 1.2.11** ou une version ultérieure. S’ils ne sont pas déjà présents, vous devez [installer les modules PowerShell propres à Azure Stack Hub](powershell-install-az-module.md).

  - Pour permettre l’importation d’un élément de la Place de marché téléchargé, l’[environnement PowerShell pour l’opérateur Azure Stack Hub](azure-stack-powershell-configure-admin.md) doit être configuré.
  - .NET Framework 4.7 ou version ultérieure.

Téléchargez le module **Azs.Syndication.Admin** à partir de PowerShell Gallery en utilisant la commande ci-dessous :

### <a name="az-modules"></a>[Modules Az](#tab/az1)

  ```powershell
  Install-Module -Name Azs.Syndication.Admin -AllowPrerelease -PassThru
  ```

### <a name="azurerm-modules"></a>[Modules AzureRM](#tab/azurerm1)

  ```powershell
  Install-Module -Name Azs.Syndication.Admin -RequiredVersion 0.1.140
  ```

---

Une fois que vous avez inscrit Azure Stack, vous pouvez ignorer le message suivant qui s’affiche dans le panneau Gestion de la Place de marché, car il ne concerne pas l’utilisation en mode déconnecté :

![Gestion de la Place de marché](media/azure-stack-download-azure-marketplace-item/toolsmsg.png)

## <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>Utiliser l’outil de syndication Place de marché pour télécharger les éléments de la Place de marché

> [!IMPORTANT]
> Veillez à télécharger l’outil de syndication de la Place de marché chaque fois que vous téléchargez des éléments de Place de marché dans un scénario déconnecté. Des changements fréquents sont apportés à cet outil et la version la plus récente doit être utilisée pour chaque téléchargement.

### <a name="az-modules"></a>[Modules Az](#tab/az2)

1. Sur un ordinateur avec une connexion Internet, ouvrez une console PowerShell en tant qu’administrateur.

2. Connectez-vous au cloud Azure et au locataire Azure AD Directory appropriés à l’aide du compte Azure que vous avez utilisé pour inscrire Azure Stack Hub. Pour ajouter le compte, dans PowerShell, exécutez la cmdlet `Login-AzAccount` :

   ```powershell  
   Login-AzAccount -Environment AzureCloud -Tenant '<mydirectory>.onmicrosoft.com'
   ```

   Vous êtes invité à entrer vos informations d’identification de compte Azure, et vous devez éventuellement utiliser l’authentification à 2 facteurs, selon la configuration de votre compte.

   > [!NOTE]
   > Si votre session expire, si votre mot de passe a changé ou si vous souhaitez changer de compte, exécutez la cmdlet suivante avant de vous connecter à l’aide de la cmdlet `Add-AzRmAccount` : `RemoveAzAccount -Scope Process`.

3. Si vous avez plusieurs abonnements, exécutez la commande suivante pour sélectionner celui que vous avez utilisé pour l’inscription :

   ```powershell  
   Get-AzSubscription -SubscriptionID 'Your Azure Subscription GUID' | Select-AzSubscription
   ```

4. Si vous ne l’avez pas déjà fait dans les prérequis, téléchargez la dernière version de l’outil de syndication de la Place de marché :

   ```powershell
   Install-Module -Name Azs.Syndication.Admin -AllowPrerelease -PassThru
   ```

5. Pour sélectionner des éléments de la Place de marché, par exemple des images de machine virtuelle, des extensions ou des modèles de solution à télécharger, exécutez la commande suivante :

   ```powershell
   $products = Select-AzsMarketplaceItem
   ```

   Cela permet d’afficher un tableau contenant toutes les inscriptions d’Azure Stack qui sont disponibles dans l’abonnement sélectionné. Choisissez l’inscription qui correspond à l’environnement Azure Stack dans lequel vous téléchargez les éléments de la Place de marché, puis sélectionnez **OK**.

     ![Capture d’écran montrant une liste de toutes les inscriptions d’Azure Stack disponibles dans l’abonnement sélectionné.](media/azure-stack-download-azure-marketplace-item/select-registration.png)

   Vous devez maintenant voir un deuxième tableau qui liste tous les éléments de la Place de marché qui peuvent être téléchargés. Sélectionnez l’élément que vous voulez télécharger, puis notez la **version**. Vous pouvez maintenir la touche **Ctrl** enfoncée pour sélectionner plusieurs images.
     ![Capture d’écran montrant une autre liste de toutes les inscriptions d’Azure Stack disponibles dans l’abonnement sélectionné.](media/azure-stack-download-azure-marketplace-item/select-products.png)
  
   Vous pouvez également filtrer la liste des images à l’aide de l’option **Ajouter des critères**.
   ![Sélectionner des inscriptions Azure Stack](media/azure-stack-download-azure-marketplace-item/select-products-with-filter.png)

   Une fois vos sélections effectuées, sélectionnez OK.

6. Les ID des éléments de la Place de marché que vous avez sélectionnés pour le téléchargement sont enregistrés dans la variable `$products`. Utilisez la commande ci-dessous pour télécharger les éléments sélectionnés. Remplacez le chemin du dossier de destination par un emplacement de stockage dans lequel placer les fichiers que vous téléchargez à partir de la Place de marché Azure :

    ```powershell
    $products | Export-AzsMarketplaceItem  -RepositoryDir "Destination folder path in quotes"
    ```

7. Le temps de téléchargement dépend de la taille de l’élément. Une fois le téléchargement terminé, l’élément est disponible dans le dossier que vous avez spécifié dans le script. Le téléchargement comprend un fichier VHD (pour les machines virtuelles) ou un fichier .zip (pour les extensions de machine virtuelle et les fournisseurs de ressources). Il peut également inclure un package de galerie au format *.azpkg*, c’est-à-dire un fichier .zip.

8. Si le téléchargement échoue, vous pouvez réessayer en exécutant de nouveau l’applet de commande PowerShell suivante :

    ```powershell
    $products | Export-AzsMarketplaceItem  -RepositoryDir "Destination folder path in quotes"
    ```

9. Vous devez également exporter localement le module **Azs.Syndication.Admin** pour le copier sur l’ordinateur à partir duquel vous importez des éléments de la Place de marché afin de les envoyer vers Azure Stack Hub.

   > [!NOTE]
   > Le dossier de destination pour l’exportation de ce module doit être différent de l’emplacement vers lequel vous avez exporté les éléments de la Place de marché.

    ```powershell
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name Azs.Syndication.Admin -Path "Destination folder path in quotes" -Force
    ```

### <a name="azurerm-modules"></a>[Modules AzureRM](#tab/azurerm2)

1. Sur un ordinateur avec une connexion Internet, ouvrez une console PowerShell en tant qu’administrateur.

2. Connectez-vous au cloud Azure et au locataire Azure AD Directory appropriés à l’aide du compte Azure que vous avez utilisé pour inscrire Azure Stack Hub. Pour ajouter le compte, dans PowerShell, exécutez la cmdlet `Add-AzureRMureRmAccount` :

   ```powershell  
   Login-AzureRMAccount -Environment AzureCloud -Tenant '<mydirectory>.onmicrosoft.com'
   ```

   Vous êtes invité à entrer vos informations d’identification de compte Azure, et vous devez éventuellement utiliser l’authentification à 2 facteurs, selon la configuration de votre compte.

   > [!NOTE]
   > Si votre session expire, si votre mot de passe a changé ou si vous souhaitez changer de compte, exécutez la cmdlet suivante avant de vous connecter à l’aide de la cmdlet `Add-AzureRMRmAccount` : `RemoveAzAccount -Scope Process`.

3. Si vous avez plusieurs abonnements, exécutez la commande suivante pour sélectionner celui que vous avez utilisé pour l’inscription :

   ```powershell  
   Get-AzureRMSubscription -SubscriptionID 'Your Azure Subscription GUID' | Select-AzureRMSubscription
   ```

4. Si vous ne l’avez pas déjà fait dans les prérequis, téléchargez la dernière version de l’outil de syndication de la Place de marché :

   ```powershell
   Install-Module -Name Azs.Syndication.Admin -RequiredVersion 0.1.140
   ```

5. Pour sélectionner des éléments de la Place de marché, par exemple des images de machine virtuelle, des extensions ou des modèles de solution à télécharger, exécutez la commande suivante :

   ```powershell
   $products = Select-AzureRMsMarketplaceItem
   ```

   Cela permet d’afficher un tableau contenant toutes les inscriptions d’Azure Stack qui sont disponibles dans l’abonnement sélectionné. Choisissez l’inscription qui correspond à l’environnement Azure Stack dans lequel vous téléchargez les éléments de la Place de marché, puis sélectionnez **OK**.

     ![Capture d’écran montrant une liste de toutes les inscriptions d’Azure Stack disponibles dans l’abonnement sélectionné.](media/azure-stack-download-azure-marketplace-item/select-registration.png)

   Vous devez maintenant voir un deuxième tableau qui liste tous les éléments de la Place de marché qui peuvent être téléchargés. Sélectionnez l’élément que vous voulez télécharger, puis notez la **version**. Vous pouvez maintenir la touche **Ctrl** enfoncée pour sélectionner plusieurs images.
     ![Capture d’écran montrant une autre liste de toutes les inscriptions d’Azure Stack disponibles dans l’abonnement sélectionné.](media/azure-stack-download-azure-marketplace-item/select-products.png)
  
   Vous pouvez également filtrer la liste des images à l’aide de l’option **Ajouter des critères**.
   ![Sélectionner des inscriptions Azure Stack](media/azure-stack-download-azure-marketplace-item/select-products-with-filter.png)

   Une fois vos sélections effectuées, sélectionnez OK.

6. Les ID des éléments de la Place de marché que vous avez sélectionnés pour le téléchargement sont enregistrés dans la variable `$products`. Utilisez la commande ci-dessous pour télécharger les éléments sélectionnés. Remplacez le chemin du dossier de destination par un emplacement de stockage dans lequel placer les fichiers que vous téléchargez à partir de la Place de marché Azure :

    ```powershell
    $products | Export-AzsMarketplaceItem  -RepositoryDir "Destination folder path in quotes"
    ```

7. Le temps de téléchargement dépend de la taille de l’élément. Une fois le téléchargement terminé, l’élément est disponible dans le dossier que vous avez spécifié dans le script. Le téléchargement comprend un fichier VHD (pour les machines virtuelles) ou un fichier .zip (pour les extensions de machine virtuelle et les fournisseurs de ressources). Il peut également inclure un package de galerie au format *.azpkg*, c’est-à-dire un fichier .zip.

8. Si le téléchargement échoue, vous pouvez réessayer en exécutant de nouveau l’applet de commande PowerShell suivante :

    ```powershell
    $products | Export-AzsMarketplaceItem  -RepositoryDir "Destination folder path in quotes"
    ```

9. Vous devez également exporter localement le module **Azs.Syndication.Admin** pour le copier sur l’ordinateur à partir duquel vous importez des éléments de la Place de marché afin de les envoyer vers Azure Stack Hub.

   > [!NOTE]
   > Le dossier de destination pour l’exportation de ce module doit être différent de l’emplacement vers lequel vous avez exporté les éléments de la Place de marché.

    ```powershell
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name Azs.Syndication.Admin -Path "Destination folder path in quotes" -Force
    ```

---



## <a name="import-the-download-and-publish-to-azure-stack-hub-marketplace-using-powershell"></a>Importer le téléchargement et le publier sur la Place de marché Azure Stack Hub avec PowerShell

1. Vous devez déplacer les fichiers que [vous avez téléchargés](#use-the-marketplace-syndication-tool-to-download-marketplace-items) localement vers un ordinateur qui est connecté à votre environnement Azure Stack Hub. L’outil de syndication de la Place de marché doit également être disponible dans votre environnement Azure Stack Hub, car vous devez l’utiliser pour effectuer l’opération d’importation.

   L’illustration suivante montre un exemple de structure de dossier. **D:\downloadfolder** contient tous les éléments de la Place de marché qui ont été téléchargés. Chaque sous-dossier est un élément de la Place de marché (par exemple,**microsoft.custom-script-linux-arm-2.0.3**), nommé d’après l’ID produit. À l’intérieur de chaque sous-dossier se trouve le contenu téléchargé de l’élément de la Place de marché.

   ![Structure du répertoire de téléchargement de la Place de marché](media/azure-stack-download-azure-marketplace-item/mp1.png)

2. Pour configurer la session PowerShell de l’opérateur Azure Stack Hub, suivez les instructions de [cet article](azure-stack-powershell-configure-admin.md).

3. Connectez-vous à Azure Stack Hub avec une identité qui dispose d’un accès de propriétaire à l’« Abonnement au fournisseur par défaut ».

4. Importez le module de syndication, puis lancez l’outil de syndication de place de marché en exécutant le script suivant :

    ```powershell
    Import-AzsMarketplaceItem -RepositoryDir "Source folder path in quotes"
    ```

5. Une fois l’exécution du script terminée, les éléments de la Place de marché doivent être disponibles dans la Place de marché Azure Stack Hub.
::: zone-end
