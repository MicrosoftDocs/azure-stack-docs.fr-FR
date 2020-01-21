---
title: Télécharger des éléments de la Place de marché Azure et les publier sur Azure Stack Hub | Microsoft Docs
description: Découvrez comment télécharger des éléments de la Place de marché à partir d’Azure et les publier sur Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/23/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 12/23/2018
ms.openlocfilehash: 80cf9d192be07f951ee959c7a83419bb16bd2bbb
ms.sourcegitcommit: d62400454b583249ba5074a5fc375ace0999c412
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76022956"
---
# <a name="download-marketplace-items-to-azure-stack-hub"></a>Télécharger des éléments de la Place de marché vers Azure Stack Hub 

En tant qu’opérateur cloud, vous pouvez télécharger des éléments vers Azure Stack Hub à partir de la Place de marché, et les rendre disponibles pour tous les utilisateurs dans l’environnement Azure Stack Hub. Les éléments que vous pouvez choisir font partie d’une liste d’éléments de la Place de marché Azure testés au préalable et prenant en charge Azure Stack Hub. De nouveaux éléments sont fréquemment ajoutés à cette liste, consultez-la régulièrement pour voir de nouveaux contenus.

Il existe deux scénarios pour télécharger des produits de la Place de marché :

- **Scénario connecté** : votre environnement Azure Stack Hub doit être connecté à Internet. Vous utilisez le portail administrateur Azure Stack Hub pour localiser et télécharger des éléments.
- **Scénario déconnecté ou partiellement connecté** : vous devez accéder à Internet à l’aide de l’outil de syndication de la Place de marché pour télécharger des éléments de la Place de marché. Ensuite, vous transférez vos téléchargements pour votre installation déconnectée de Azure Stack Hub. Ce scénario utilise PowerShell.

Consultez [Éléments Place de marché Azure pour Azure Stack Hub](azure-stack-marketplace-azure-items.md) pour obtenir la liste complète des éléments de la Place de marché que vous pouvez télécharger. Pour obtenir la liste des ajouts, suppressions et mises à jour récents de la Place de marché Azure Stack Hub, consultez l’article [Modifications de la Place de marché Azure Stack Hub](azure-stack-marketplace-changes.md).

> [!NOTE]
> Le catalogue est différent selon le cloud auquel votre système Azure Stack Hub est connecté. L’environnement cloud est déterminé par l’abonnement Azure que vous utilisez pour inscrire votre infrastructure Azure Stack Hub.

## <a name="connected-scenario"></a>Scénario connecté

Si Azure Stack Hub se connecte à Internet, vous pouvez utiliser le portail administrateur pour télécharger des éléments de la Place de marché.

### <a name="prerequisites"></a>Conditions préalables requises

Votre déploiement Azure Stack Hub doit disposer d’une connectivité à Internet et être inscrit auprès d’Azure.

### <a name="use-the-portal-to-download-marketplace-items"></a>Utiliser le portail pour télécharger les éléments de la Place de marché

1. Connectez-vous au portail administrateur Azure Stack Hub.

2. Vérifiez l’espace de stockage disponible avant de télécharger des éléments de la Place de marché. Plus tard, lorsque vous sélectionnez des éléments pour le téléchargement, vous pouvez comparer la taille du téléchargement avec votre capacité de stockage disponible. Si la capacité est limitée, réfléchissez aux options qui permettent de [gérer l’espace disponible](azure-stack-manage-storage-shares.md#manage-available-space).

   Pour passer en revue l’espace disponible : dans **Gestion des régions**, sélectionnez la région que vous souhaitez explorer, puis accédez à **Fournisseurs de ressources** > **Stockage** :

   ![Passer en revue l’espace de stockage dans le portail administrateur Azure Stack Hub](media/azure-stack-download-azure-marketplace-item/storage.png)

3. Ouvrez la Place de marché Azure Stack Hub, puis connectez-vous à Azure. Pour ce faire, sélectionnez le service **Gestion de la Place de marché** , sélectionnez **Éléments de la Place de marché**, puis sélectionnez **Ajouter à partir d’Azure** :

   ![Ajouter des éléments de la Place de marché à partir d’Azure](media/azure-stack-download-azure-marketplace-item/marketplace.png)

4. Chaque élément de ligne affiche également la version actuellement disponible. Si plusieurs versions d’un élément de la Place de marché sont disponibles, la colonne **Version** affiche **Multiple**. Vous pouvez cliquer sur chaque élément pour voir sa description et d’autres informations, notamment sa taille de téléchargement :

   ![Ajout à partir d’Azure](media/azure-stack-download-azure-marketplace-item/add-from-azure1.png)

5. Si la version d’un élément est affichée comme **Multiple**, vous pouvez sélectionner cet élément, puis choisir une version spécifique dans la liste déroulante du sélecteur de version qui en résulte :

   ![Ajout à partir d’Azure](media/azure-stack-download-azure-marketplace-item/add-from-azure3.png)

6. Sélectionnez l’élément souhaité, puis sélectionnez **Télécharger**. Les temps de téléchargement varient et dépendent de la connectivité réseau. Une fois le téléchargement effectué, vous pouvez déployer le nouvel élément de la Place de marché en tant qu’opérateur ou utilisateur Azure Stack Hub.

7. Pour déployer l’élément téléchargé, sélectionnez **+ Créer une ressource**, puis recherchez le nouvel élément de Place de marché parmi les catégories. Cliquez ensuite sur l’élément pour commencer le processus de déploiement. Le processus varie pour les différents éléments de la Place de marché.

## <a name="disconnected-or-a-partially-connected-scenario"></a>Scénario déconnecté ou partiellement connecté

Si la connectivité Internet d’Azure Stack Hub est limitée ou inexistante, vous pouvez utiliser PowerShell et l’ *outil de syndication de la Place de marché* pour télécharger les éléments de la Place de marché sur une machine connectée à Internet. Transférez ensuite les éléments vers votre environnement Azure Stack Hub. Dans un environnement déconnecté, vous ne pouvez pas télécharger des éléments de la Place de marché à l’aide du portail Azure Stack Hub.

L’outil de syndication Place de marché peut également être utilisé dans un scénario connecté.

Ce scénario comporte deux parties :

- **Partie 1** : Effectuez le téléchargement des éléments à partir de la Place de marché. Sur l’ordinateur ayant accès à Internet, configurez PowerShell, téléchargez l’outil de syndication, puis téléchargez les éléments de la Place de marché Azure.
- **Partie 2** : Effectuez le chargement et la publication sur la Place de marché Azure Stack Hub. Déplacez les fichiers que vous avez téléchargés vers votre environnement Azure Stack Hub, importez-les dans Azure Stack Hub, puis publiez-les sur la Place de marché Azure Stack Hub.

### <a name="prerequisites"></a>Conditions préalables requises

- Un environnement connecté (qui n’est pas nécessairement Azure Stack Hub). Vous avez besoin d’une connectivité pour accéder à la liste des produits d’Azure avec leurs détails et pour tout télécharger localement. Une fois cette opération effectuée, le reste de la procédure ne nécessite aucune connectivité Internet. Elle crée un catalogue d’éléments que vous avez précédemment téléchargés, que vous pouvez utiliser dans votre environnement déconnecté.

- Un média amovible pour vous connecter à votre environnement déconnecté et transférer tous les artefacts nécessaires.

- Un environnement Azure Stack Hub déconnecté avec les prérequis suivants :

  - Votre déploiement Azure Stack Hub doit être inscrit auprès d’Azure.

  - L’ordinateur connecté à Internet doit disposer du  **module PowerShell pour Azure Stack Hub version 1.2.11**  ou une version ultérieure. S’ils ne sont pas déjà présents, vous devez  [installer les modules PowerShell spécifiques d’Azure Stack Hub](azure-stack-powershell-install.md).

  - Pour permettre l’importation d’un élément téléchargé de la Place de marché, l’environnement [PowerShell pour l’opérateur Azure Stack Hub](azure-stack-powershell-configure-admin.md) doit être configuré.

  - Clonez le dépôt GitHub des [outils Azure Stack Hub](https://github.com/Azure/AzureStack-Tools) .

- Vous devez avoir un [compte de stockage](azure-stack-manage-storage-accounts.md) dans Azure Stack Hub, qui dispose d’un conteneur (objet blob de stockage) public. Vous utilisez le conteneur comme un stockage temporaire pour les fichiers de la galerie des éléments de la Place de marché. Si vous n’êtes pas familiarisé avec les comptes de stockage et les conteneurs, consultez [Travailler avec des objets BLOB - Portail Azure](/azure/storage/blobs/storage-quickstart-blobs-portal) dans la documentation Azure.

- L’outil de syndication Place de marché est téléchargé lors de la première procédure.

- Vous pouvez installer [AzCopy](/azure/storage/common/storage-use-azcopy) pour optimiser le niveau de performance du téléchargement, mais cela n’est pas obligatoire.

Une fois que vous vous êtes inscrit, vous pouvez ignorer le message suivant qui apparaît dans le panneau Gestion de la Place de marché, car cela n’est pas pertinent pour l’utilisation en mode déconnecté :

![Gestion de la Place de marché](media/azure-stack-download-azure-marketplace-item/toolsmsg.png)

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>Utiliser l’outil de syndication Place de marché pour télécharger les éléments de la Place de marché

> [!IMPORTANT]
> Veillez à télécharger l’outil de syndication de la Place de marché chaque fois que vous téléchargez des éléments de Place de marché dans un scénario déconnecté. Des changements fréquents sont apportés à ce script et la version la plus récente doit être utilisée pour chaque téléchargement.

1. Sur un ordinateur avec une connexion Internet, ouvrez une console PowerShell en tant qu’administrateur.

2. Ajoutez le compte Azure que vous avez utilisé pour inscrire Azure Stack Hub. Pour ajouter le compte, dans PowerShell, exécutez **Add-AzureRmAccount** sans aucun paramètre. Vous êtes invité à entrer vos informations d’identification de compte Azure, et vous devez éventuellement utiliser l’authentification à 2 facteurs, selon la configuration de votre compte.

   > [!NOTE]
   > Si votre session expire, si votre mot de passe a changé ou si vous souhaitez simplement changer de compte, exécutez l’applet de commande suivante avant de vous connecter en utilisant **Add-AzureRmAccount**: **Remove-AzureRmAccount-Scope Process**.

3. Si vous avez plusieurs abonnements, exécutez la commande suivante pour sélectionner celui que vous avez utilisé pour l’inscription :

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID 'Your Azure Subscription GUID' | Select-AzureRmSubscription
   $AzureContext = Get-AzureRmContext
   ```

4. Téléchargez la dernière version de l’outil de syndication de la Place de marché à l’aide du script suivant :

   ```powershell
   # Download the tools archive.
   [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
   invoke-webrequest https://github.com/Azure/AzureStack-Tools/archive/master.zip `
     -OutFile master.zip

   # Expand the downloaded files.
   expand-archive master.zip `
     -DestinationPath `
     -Force

   # Change to the tools directory.
   cd .\AzureStack-Tools-master
   ```

5. Importez le module de syndication, puis lancez l’outil en exécutant la commande suivante :

   ```powershell  
   Import-Module .\Syndication\AzureStack.MarketplaceSyndication.psm1
   ```

6. Pour exporter des éléments de la Place de marché, par exemple des images de machine virtuelle, des extensions ou des modèles de solution, exécutez la commande suivante. Remplacez le chemin du dossier de destination par un emplacement de stockage des fichiers que vous téléchargez à partir de la Place de marché Azure :

   ```powershell
   Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes" -azCopyDownloadThreads "[optional - AzCopy threads number]" -azureContext $AzureContext
   ```

   Pour exporter des fournisseurs de ressources ou des services PaaS, exécutez la commande suivante :

   ```powershell
   Export-AzSOfflineResourceProvider -destination "Destination folder path" -azCopyDownloadThreads "AzCopy threads number" -azureContext $AzureContext
   ```

   Le paramètre `-azCopyDownloadThreads` est facultatif. Vous devez uniquement vous en servir si vous disposez d’un réseau à faible bande passante et si vous utilisez le téléchargement Premium. Cette option spécifie le nombre d’opérations simultanées dans AzCopy. Si vous disposez d’un réseau à faible bande passante, vous pouvez spécifier un nombre inférieur pour éviter les problèmes causés par la concurrence des ressources. Vous trouverez plus d’informations dans [cet article Azure](/previous-versions/azure/storage/storage-use-azcopy#specify-the-number-of-concurrent-operations-to-start).

   Le paramètre `-azureContext` est également facultatif. Si vous ne spécifiez pas le contexte Azure, l’applet de commande utilise le contexte Azure par défaut.

7. Quand l’outil s’exécute, vous voyez normalement un écran similaire à l’image suivante, avec la liste des éléments de Place de marché disponibles :

   ![Éléments du Marketplace](media/azure-stack-download-azure-marketplace-item/tool1.png)

8. Si plusieurs versions d’un élément de la Place de marché sont disponibles, la colonne **Version** affiche **Plusieurs versions**. Si la version d’un élément correspond à **Plusieurs versions**, vous pouvez sélectionner cet élément, puis choisir une version spécifique dans la fenêtre de sélecteur de version résultante.

9. Sélectionnez l’élément que vous souhaitez télécharger, puis notez la **version**. Vous pouvez maintenir enfoncée la touche **Ctrl** pour sélectionner plusieurs images. Vous référencez la *version* quand vous importez l’élément dans la procédure qui suit.

    Vous pouvez également filtrer la liste des images à l’aide de l’option **Ajouter des critères** .

10. Si vous n’avez pas installé les outils de Stockage Azure, vous recevez le message suivant. Pour pouvoir installer ces outils, veillez à télécharger [AzCopy](/azure/storage/common/storage-use-azcopy#download-azcopy) :

    ![Outils de stockage](media/azure-stack-download-azure-marketplace-item/vmnew1.png)

11. Sélectionnez **OK**, puis passez en revue et acceptez les conditions juridiques.

12. Le temps de téléchargement dépend de la taille de l’élément. Une fois le téléchargement terminé, l’élément est disponible dans le dossier que vous avez spécifié dans le script. Le téléchargement comprend un fichier VHD (pour les machines virtuelles) ou un fichier .zip (pour les extensions de machine virtuelle et les fournisseurs de ressources). Il peut également inclure un package de galerie au format *.azpkg* , c’est-à-dire un simple fichier.zip.

13. Si le téléchargement échoue, vous pouvez réessayer en exécutant de nouveau l’applet de commande PowerShell suivante :

   ```powershell
   # for Marketplace items
   Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes"

   # for Resource providers
   Export-AzSOfflineResourceProvider -Destination "Destination folder path in quotes"
   ```

   Avant de procéder à une nouvelle tentative, supprimez le dossier du produit dans lequel le téléchargement a échoué. Par exemple, en cas d’échec du téléchargement vers **D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1**, supprimez le dossier **D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1** , puis réexécutez l’applet de commande.

### <a name="import-the-download-and-publish-to-azure-stack-hub-marketplace-using-powershell"></a>Importer le téléchargement et le publier sur la Place de marché Azure Stack Hub avec PowerShell

1. Vous devez déplacer localement les fichiers que vous avez [téléchargés](#use-the-marketplace-syndication-tool-to-download-marketplace-items) pour qu’ils soient disponibles dans votre environnement Azure Stack Hub. L’outil de syndication de la Place de marché doit également être disponible dans votre environnement Azure Stack Hub, car vous devez l’utiliser pour effectuer l’opération d’importation.

   L’image suivante montre un exemple de structure de dossiers. **D:\downloadfolder** contient tous les éléments de la Place de marché téléchargés. Chaque sous-dossier est un élément de la Place de marché (par exemple, **microsoft.custom-script-linux-arm-2.0.3**), nommé d’après l’ID produit. À l’intérieur de chaque sous-dossier se trouve le contenu téléchargé de l’élément de la Place de marché.

   ![Structure du répertoire de téléchargement de la Place de marché](media/azure-stack-download-azure-marketplace-item/mp1.png)

2. Suivez les instructions de [cet article](azure-stack-powershell-configure-admin.md) pour configurer la session PowerShell de l’opérateur Azure Stack Hub.

3. Importez le module de syndication, puis lancez l’outil de syndication de place de marché en exécutant le script suivant :

   ```powershell
   $credential = Get-Credential -Message "Enter the Azure Stack Hub operator credential:"
   Import-AzSOfflineMarketplaceItem -origin "marketplace content folder" -AzsCredential $credential
   ```

   Le paramètre `-origin` spécifie le dossier de premier niveau qui contient tous les produits téléchargés, par exemple `"D:\downloadfolder"`.

   Le paramètre `-AzsCredential` est facultatif. Il est utilisé pour renouveler le jeton d’accès s’il a expiré. Si le paramètre `-AzsCredential` n’est pas spécifié et si le jeton expire, vous êtes invité à entrer les informations d’identification de l’opérateur.

   > [!NOTE]
   > AD FS prend en charge uniquement l’authentification interactive avec des identités d’utilisateurs. Si un objet d’identification est nécessaire, vous devez utiliser un SPN (nom de principal du service). Pour plus d’informations sur la configuration d’un principal de service avec Azure Stack Hub et AD FS en tant que service de gestion des identités, consultez  [Gérer un principal de service AD FS](azure-stack-create-service-principals.md#manage-an-ad-fs-service-principal).

4. Une fois l’exécution du script effectuée correctement, l’élément doit être disponible dans la Place de marché Azure Stack Hub.
