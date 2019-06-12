---
title: Télécharger des éléments de la Place de marché à partir d’Azure | Microsoft Docs
description: L’opérateur de cloud peut télécharger des éléments de la Place de marché à partir d’Azure dans mon déploiement Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/10/2018
ms.openlocfilehash: 5b90bc5014d0f8ebfaed8532f62b7c4f3aee8667
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2019
ms.locfileid: "66691863"
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Télécharger des éléments de la Place de marché à partir d’Azure dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

En tant qu’opérateur cloud, vous pouvez télécharger des éléments à partir de la Place de marché Azure et les rendre disponibles dans Azure Stack. Les éléments que vous pouvez choisir font partie d’une liste d’éléments de la Place de marché Azure testés au préalable et prenant en charge le travail avec Azure Stack. De nouveaux éléments sont fréquemment ajoutés à cette liste, consultez-la régulièrement pour voir de nouveaux contenus. 

Il existe deux scénarios pour la connexion à la Place de marché Azure : 

- **Un scénario connecté** : il demande que votre environnement Azure Stack soit connecté à internet. Vous utilisez le portail Azure Stack pour localiser et télécharger des éléments. 
- **Un scénario déconnecté ou partiellement connecté** : il vous oblige à accéder à internet à l’aide de l’outil de syndication Place de marché pour télécharger des éléments de la Place de marché. Ensuite, vous transférez vos téléchargements pour votre installation déconnectée de Azure Stack. Ce scénario utilise PowerShell.

Consultez [Éléments Place de marché Azure pour Azure Stack](azure-stack-marketplace-azure-items.md) pour obtenir la liste des éléments de la Place de marché que vous pouvez télécharger.

## <a name="connected-scenario"></a>Scénario connecté

Si Azure Stack se connecte à internet, vous pouvez utiliser le portail d’administration pour télécharger des éléments de la Place de marché.

### <a name="prerequisites"></a>Prérequis

Votre déploiement Azure Stack doit être connecté à internet et être [inscrit auprès d’Azure](azure-stack-registration.md ).

### <a name="use-the-portal-to-download-marketplace-items"></a>Utiliser le portail pour télécharger les éléments de la Place de marché
  
1. Connectez-vous au portail d’administration d’Azure Stack.

2.  Vérifiez l’espace de stockage disponible avant de télécharger des éléments de la Place de marché. Plus tard, lorsque vous sélectionnez des éléments pour le téléchargement, vous pouvez comparer la taille du téléchargement avec votre capacité de stockage disponible. Si la capacité est limitée, envisagez des options pour [gérer l’espace disponible](azure-stack-manage-storage-shares.md#manage-available-space). 

    Pour consulter l’espace disponible, dans **Gestion des régions**, sélectionnez la région que vous souhaitez explorer, puis accédez à **Fournisseurs de ressources** > **Stockage** :

    ![Revoir l’espace de stockage](media/azure-stack-download-azure-marketplace-item/storage.png)

3. Ouvrez Azure Stack Marketplace et connectez-vous à Azure. Pour ce faire, sélectionnez **Éléments de la Place de marché**, puis sélectionnez **Ajouter à partir d’Azure** :

    ![Ajout à partir d’Azure](media/azure-stack-download-azure-marketplace-item/marketplace.png)

    Le portail affiche la liste des éléments disponibles en téléchargement à partir de la Place de marché Azure. Vous pouvez filtrer des produits par nom, éditeur et/ou type de produit. Vous pouvez également cliquer sur chaque élément pour voir sa description et d’autres informations, notamment sa taille de téléchargement :

    ![Liste de la Place de marché](media/azure-stack-download-azure-marketplace-item/image03.PNG)

4. Sélectionnez les éléments que vous souhaitez, puis sélectionnez **Télécharger**. Le temps de téléchargement varie.

    ![Message de téléchargement](media/azure-stack-download-azure-marketplace-item/image04.png)

    Une fois le téléchargement terminé, vous pouvez déployer le nouvel élément de Place de marché en tant qu’opérateur ou utilisateur Azure Stack.

5. Pour déployer l’élément téléchargé, sélectionnez **+ Créer une ressource** puis recherchez le nouvel élément de Place de marché parmi les catégories. Cliquez ensuite sur l’élément pour commencer le processus de déploiement. Le processus varie pour les différents éléments de la Place de marché. 

## <a name="disconnected-or-a-partially-connected-scenario"></a>Scénario déconnecté ou partiellement connecté

Si Azure Stack est en mode déconnecté et sans connexion internet, vous utilisez PowerShell et l’*outil de syndication Place de marché* pour télécharger les éléments de la Place de marché sur une machine avec une connexion internet. Vous transférez alors les éléments vers votre environnement Azure Stack. Dans un environnement déconnecté, vous ne pouvez pas télécharger les éléments de la Place de marché en utilisant le portail Azure Stack. 

L’outil de syndication Place de marché peut également être utilisé dans un scénario connecté. 

Ce scénario comporte deux parties :
- **Partie 1 :** télécharger à partir de la Place de marché Azure. Sur l’ordinateur avec un accès internet, vous configurez PowerShell, téléchargez l’outil de syndication et puis vous téléchargez les éléments à partir de la Place de marché Azure.  
- **Partie 2 :** charger et publier dans la Place de marché Azure Stack. Vous déplacez les fichiers que vous avez téléchargés dans votre environnement Azure Stack, vous les importez dans Azure Stack, puis vous les publiez dans la Place de marché Azure.  


### <a name="prerequisites"></a>Prérequis
- Votre déploiement Azure Stack doit être [inscrit auprès d’Azure](azure-stack-registration.md ).  

- L’ordinateur qui possède une connexion internet doit disposer du **module PowerShell pour Azure Stack version 1.2.11** ou une version ultérieure. S’il est déjà présent, [installez des modules PowerShell spécifiques à Azure Stack](azure-stack-powershell-install.md).  

- Pour activer l’importation d’un élément de la Place de marché téléchargé, l’[environnement PowerShell pour l’opérateur Azure Stack](azure-stack-powershell-configure-admin.md) doit être configuré.  

- Vous devez disposer d’un [compte de stockage](azure-stack-manage-storage-accounts.md) dans Azure Stack disposant d’un conteneur accessible publiquement (qui est un objet blob de stockage). Vous utilisez le conteneur comme un stockage temporaire pour les fichiers de la galerie des éléments de la Place de marché. Si vous n’êtes pas familiarisé avec les comptes de stockage et les conteneurs, consultez [Travailler avec des objets blob - portail Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) dans la documentation Azure.

- L’outil de syndication Place de marché est téléchargé lors de la première procédure. 

- Vous pouvez installer [AzCopy](/azure/storage/common/storage-use-azcopy) pour bénéficier de performances de téléchargement optimales, mais cette opération n’est pas obligatoire.

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>Utiliser l’outil de syndication Place de marché pour télécharger les éléments de la Place de marché

1. Sur un ordinateur avec une connexion Internet, ouvrez une console PowerShell en tant qu’administrateur.

2. Ajoutez le compte Azure que vous avez utilisé pour inscrire Azure Stack. Pour ajouter le compte, exécutez `Add-AzureRmAccount` sans aucun paramètre dans PowerShell. Vous êtes invité à entrer vos informations d’identification de compte Azure et vous devrez peut-être utiliser l’authentification à 2 facteurs en fonction de la configuration de votre compte.

3. Si vous avez plusieurs abonnements, exécutez la commande suivante pour sélectionner celui que vous avez utilisé pour l’inscription :  

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
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
     -DestinationPath . `
     -Force

   # Change to the tools directory.
   cd .\AzureStack-Tools-master

   ```

5. Importez le module de syndication et lancez l’outil en exécutant les commandes suivantes. Remplacez `Destination folder path` avec un emplacement pour stocker les fichiers que vous téléchargez à partir de la Place de marché Azure.   

   ```powershell  
   Import-Module .\Syndication\AzureStack.MarketplaceSyndication.psm1

   Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes" 
   ```

   Notez que `Export-AzSOfflineMarketplaceItem` a un autre indicateur `-cloud` qui spécifie l’environnement cloud. Par défaut, il s’agit de **azurecloud**.

6. Lorsque vous exécutez les outils, vous devriez voir un écran similaire à l’image suivante, avec la liste des éléments de la Place de marché disponibles :

   [![Fenêtre contextuelle des éléments de la Place de marché Azure](media/azure-stack-download-azure-marketplace-item/image05.png "Éléments de la Place de marché Azure")](media/azure-stack-download-azure-marketplace-item/image05.png#lightbox)

7. Sélectionnez l’élément que vous souhaitez télécharger, puis notez la *version*. Vous pouvez maintenir la touche *Ctrl* enfoncée pour sélectionner plusieurs images. Vous référencez la *version* lorsque vous importez l’élément dans la procédure suivante. 
   
   Vous pouvez également filtrer la liste des images à l’aide de l’option **Ajouter des critères**.

8. Sélectionnez **OK**, puis lisez et acceptez les conditions juridiques. 

9. Le temps de téléchargement dépend de la taille de l’élément. Une fois le téléchargement terminé, l’élément est disponible dans le dossier que vous avez spécifié dans le script. Le téléchargement comprend un fichier VHD (pour les machines virtuelles) ou un fichier .zip (pour les extensions de machine virtuelle). Il peut également inclure un package de la galerie au format *.azpkg*, autrement dit un simple fichier .zip.

10. Si le téléchargement échoue, vous pouvez réessayer en exécutant de nouveau l’applet de commande PowerShell suivante :

    ```powershell
    Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes"
    ```

    Avant de procéder à une nouvelle tentative, supprimez le dossier du produit dans lequel le téléchargement a échoué. Par exemple, si le script de téléchargement échoue lors du téléchargement sur `D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1`, supprimez le dossier `D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1`, puis réexécutez l’applet de commande.
 
### <a name="import-the-download-and-publish-to-azure-stack-marketplace-1811-and-higher"></a>Importer le téléchargement et le publier dans la Place de marché Azure Stack (1811 et versions supérieures)

1. Vous devez déplacer les fichiers que vous avez [précédemment téléchargés](#use-the-marketplace-syndication-tool-to-download-marketplace-items) localement, afin de les rendre disponibles dans votre environnement Azure Stack. L’outil de syndication de la Place de marché doit également être disponible dans votre environnement Azure Stack, car il vous est utile pour effectuer l’opération d’importation.

   L’illustration suivante montre un exemple de structure de dossier. `D:\downloadfolder` contient tous les éléments de place de marché téléchargés. Chaque sous-dossier est un élément de place de marché (par exemple, `microsoft.custom-script-linux-arm-2.0.3`), nommé par l’ID de produit. À l’intérieur de chaque sous-dossier se trouve le contenu téléchargé de l’élément de place de marché.

   [![Structure du répertoire de téléchargement de la Place de marché](media/azure-stack-download-azure-marketplace-item/mp1sm.png "Structure du répertoire de téléchargement de la Place de marché")](media/azure-stack-download-azure-marketplace-item/mp1.png#lightbox)

2. Pour configurer la session PowerShell de l’opérateur Azure Stack, suivez les instructions de [cet article](azure-stack-powershell-configure-admin.md). 

3. Importez le module de syndication, puis lancez l’outil de syndication de place de marché en exécutant le script suivant :

   ```powershell
   $credential = Get-Credential -Message "Enter the azure stack operator credential:"
   Import-AzSOfflineMarketplaceItem -origin "marketplace content folder" -AzsCredential $credential
   ```

   Le paramètre `-origin` spécifie le dossier de plus haut niveau qui contient tous les produits téléchargés ; par exemple, `"D:\downloadfolder"`.

   Le paramètre `-AzsCredential` est facultatif. Il est utilisé pour renouveler le jeton d’accès, s’il a expiré. Si le paramètre `-AzsCredential` n’est pas spécifié et que le jeton expire, vous êtes invité à entrer les informations d’identification de l’opérateur.

    > [!Note]  
    > AD FS prend en charge uniquement l’authentification interactive avec des identités d’utilisateurs. Si un objet d’identification est nécessaire, vous devez utiliser un SPN (nom de principal du service). Pour plus d’informations sur la configuration d’un principal de service avec Azure Stack et AD FS en tant que service de gestion des identités, consultez [Gérer le principal de service pour AD FS](azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs).

4. Une fois l’exécution du script terminée correctement, l’élément doit être disponible dans la Place de marché Azure Stack.

### <a name="import-the-download-and-publish-to-azure-stack-marketplace-1809-and-lower"></a>Importer le téléchargement et le publier dans la Place de marché Azure Stack (1809 et versions inférieures)

1. Les fichiers pour les images de machine virtuelle ou les modèles de solution que vous avez [précédemment téléchargés](#use-the-marketplace-syndication-tool-to-download-marketplace-items) doivent être accessibles localement dans votre environnement Azure Stack.  

2. Utilisez le portail d’administration pour télécharger le package d’élément de Place de marché (fichier .azpkg) et l’image du disque dur virtuel (fichier.vhd) pour le stockage d’objets Blob d’Azure Stack. Le téléchargement du package et des fichiers disque les rendent disponibles pour Azure Stack afin que vous puissiez publier l’élément ultérieurement sur la Place de marché Azure Stack.

   Le téléchargement nécessite un compte de stockage avec un conteneur publiquement accessible (voir la configuration requise pour ce scénario).  
   1. Dans le portail d’administration d’Azure Stack, accédez à **Tous les services**, puis dans la catégorie **DONNÉES + STOCKAGE**, sélectionnez **Comptes de stockage** .  
   
   2. Sélectionnez un compte de stockage de votre abonnement, puis sous **SERVICE BLOB**, sélectionnez **Conteneurs**.  
      [![Service BLOB](media/azure-stack-download-azure-marketplace-item/blob-service.png "Service BLOB")](media/azure-stack-download-azure-marketplace-item/blob-service.png#lightbox)  
   
   3. Sélectionnez le conteneur que vous souhaitez utiliser, puis sélectionnez **Télécharger** pour ouvrir le volet **Télécharger des objets blob**.  
      [![Conteneur](media/azure-stack-download-azure-marketplace-item/container.png "Conteneur")](media/azure-stack-download-azure-marketplace-item/container.png#lightbox)  
   
   4. Dans le volet Charger l’objet blob, recherchez le package et les fichiers disque que vous souhaitez charger dans le stockage, puis sélectionnez **Charger**: [![Charger](media/azure-stack-download-azure-marketplace-item/uploadsm.png "Charger")](media/azure-stack-download-azure-marketplace-item/upload.png#lightbox)  

   5. Les fichiers que vous téléchargez apparaissent dans le volet du conteneur. Sélectionnez un fichier, puis copiez l’URL à partir du volet **Propriétés d’objets blob**. Vous allez utiliser cette URL à l’étape suivante lorsque vous importez l’élément de Place de marché dans Azure Stack.  Dans l’image suivante, le conteneur est *blob-test-storage* et le fichier est *Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg*.  L’URL du fichier est *https://testblobstorage1.blob.local.azurestack.external/blob-test-storage/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg* .  
      [![Propriétés d’objets blob](media/azure-stack-download-azure-marketplace-item/blob-storagesm.png "Propriétés d’objets blob")](media/azure-stack-download-azure-marketplace-item/blob-storage.png#lightbox)  

3. Importez l’image VHD dans Azure Stack à l’aide de la cmdlet **Add-AzsPlatformimage**. Lorsque vous utilisez cette cmdlet, remplacez les valeurs *publisher*, *offer* et les autres valeurs de paramètre par les valeurs de l’image que vous importez. 

   Vous pouvez obtenir les valeurs *publisher*, *offer* et *sku* de l’image à partir du fichier texte téléchargé avec le fichier AZPKG. Le fichier texte est stocké dans l’emplacement de destination. La valeur *version* est la version indiquée lors du téléchargement de l’élément à partir d’Azure au cours de la procédure précédente. 
 
   Dans l’exemple de script suivant, les valeurs pour la machine virtuelle Server Core (Windows Server 2016 Datacenter) sont utilisées. La valeur pour *-Osuri* est un exemple de chemin d’accès à l’emplacement de stockage des objets blob de l’élément.

   Comme alternative, vous pouvez utiliser la [procédure décrite dans cet article](azure-stack-add-vm-image.md#add-a-vm-image-through-the-portal) pour importer l’image .VHD avec le portail Azure.

   ```powershell  
   Add-AzsPlatformimage `
    -publisher "MicrosoftWindowsServer" `
    -offer "WindowsServer" `
    -sku "2016-Datacenter-Server-Core" `
    -osType Windows `
    -Version "2016.127.20171215" `
    -OsUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.vhd"  
   ```

   **À propos des modèles de solution :** certains modèles peuvent inclure un petit fichier .VHD d’environ 3 Mo portant le nom **fixed3.vhd**. Vous n’avez pas besoin d’importer ce fichier dans Azure Stack. Fixed3.vhd.  Ce fichier est inclus avec certains modèles de solution afin de répondre aux exigences de publication pour Place de marché Azure.

   Consultez la description des modèles et téléchargez puis importez des exigences supplémentaires comme des disques durs virtuels requis pour fonctionner avec le modèle de solution.  
   
   **À propos des extensions :** lorsque vous travaillez avec des extensions d’image de machine virtuelle, utilisez les paramètres suivants :
   - *Publisher*
   - *Type*
   - *Version*  

   Vous n’utilisez pas *Offre* pour les extensions.   


4.  Utilisez PowerShell pour publier l’élément de Place de marché sur Azure Stack en utilisant l’applet de commande **Add-AzsGalleryItem**. Par exemple :  
    ```powershell  
    Add-AzsGalleryItem `
     -GalleryItemUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg" `
     -Verbose
    ```
5. Une fois que l’élément de la galerie est publié, il est disponible en vue de son utilisation. Pour confirmer la publication de l’élément de la galerie, accédez à **Tous les services**, puis dans la catégorie **GÉNÉRAL**, sélectionnez **Place de 
   marché**.  Si vous avez téléchargé un modèle de solution, veillez également à ajouter toute image VHD dépendante pour ce modèle solution.  
[![Afficher la Place de marché](media/azure-stack-download-azure-marketplace-item/view-marketplacesm.png "Afficher la Place de marché")](media/azure-stack-download-azure-marketplace-item/view-marketplace.png#lightbox)  

Avec la version 1.3.0 de Azure Stack PowerShell, vous pouvez maintenant ajouter des extensions de machine virtuelle. Par exemple :

```powershell
Add-AzsVMExtension -Publisher "Microsoft" -Type "MicroExtension" -Version "0.1.0" -ComputeRole "IaaS" -SourceBlob "https://github.com/Microsoft/PowerShell-DSC-for-Linux/archive/v1.1.1-294.zip" -SupportMultipleExtensions -VmOsType "Linux"
```

## <a name="next-steps"></a>Étapes suivantes

[Créer et publier un élément de Place de Marché](azure-stack-create-and-publish-marketplace-item.md)