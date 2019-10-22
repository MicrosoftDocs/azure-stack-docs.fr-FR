---
title: Télécharger des éléments de la Place de marché Azure et les publier sur Azure Stack | Microsoft Docs
description: Découvrez comment télécharger des éléments de la Place de marché à partir d’Azure et les publier sur Azure Stack.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: sethm
ms.reviewer: ihcherie
ms.lastreviewed: 12/10/2018
ms.openlocfilehash: 91314fcd33d3b4171dc7e9a3e2d78cdf07e2f50e
ms.sourcegitcommit: d159652f50de7875eb4be34c14866a601a045547
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72283560"
---
# <a name="download-existing-marketplace-items-from-azure-and-publish-to-azure-stack"></a>Télécharger des éléments existants de la Place de marché Azure et les publier sur Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

En tant qu’opérateur cloud, vous pouvez télécharger des éléments à partir de la Place de marché Azure et les rendre disponibles dans Azure Stack. Les éléments que vous pouvez choisir font partie d’une liste d’éléments de la Place de marché Azure testés au préalable et prenant en charge le travail avec Azure Stack. De nouveaux éléments sont fréquemment ajoutés à cette liste, consultez-la régulièrement pour voir de nouveaux contenus.

Il existe deux scénarios pour la connexion à la Place de marché Azure :

- **Un scénario connecté** : il demande que votre environnement Azure Stack soit connecté à internet. Vous utilisez le portail Azure Stack pour localiser et télécharger des éléments.
- **Un scénario déconnecté ou partiellement connecté** : il vous impose d’accéder à Internet avec l’outil de syndication de la Place de marché pour télécharger des éléments de la Place de marché. Ensuite, vous transférez vos téléchargements pour votre installation déconnectée de Azure Stack. Ce scénario utilise PowerShell.

Voir [Éléments Place de marché Azure pour Azure Stack](azure-stack-marketplace-azure-items.md) pour obtenir la liste complète des éléments de la Place de marché que vous pouvez télécharger. Pour obtenir la liste des ajouts, suppressions et mises à jour récents de la Place de marché Azure Stack, consultez l’article [Modifications de la Place de marché Azure Stack](azure-stack-marketplace-changes.md).

## <a name="connected-scenario"></a>Scénario connecté

Si Azure Stack se connecte à Internet, vous pouvez utiliser le portail d’administration pour télécharger des éléments de la Place de marché.

### <a name="prerequisites"></a>Prérequis

Votre déploiement Azure Stack doit être connecté à Internet et être [inscrit auprès d’Azure](azure-stack-registration.md).

### <a name="use-the-portal-to-download-marketplace-items"></a>Utiliser le portail pour télécharger les éléments de la Place de marché
  
1. Connectez-vous au portail d’administration d’Azure Stack.

2. Vérifiez l’espace de stockage disponible avant de télécharger des éléments de la Place de marché. Plus tard, lorsque vous sélectionnez des éléments pour le téléchargement, vous pouvez comparer la taille du téléchargement avec votre capacité de stockage disponible. Si la capacité est limitée, envisagez des options pour [gérer l’espace disponible](azure-stack-manage-storage-shares.md#manage-available-space).

    Pour passer en revue l’espace disponible, dans **Gestion des régions**, sélectionnez la région que vous voulez explorer, puis accédez à **Fournisseurs de ressources** > **Stockage** :

    ![Passer en revue l’espace de stockage dans le portail administrateur Azure Stack](media/azure-stack-download-azure-marketplace-item/storage.png)

3. Ouvrez la Place de marché Azure Stack et connectez-vous à Azure. Pour ce faire, sélectionnez le service **Gestion de la Place de marché**, sélectionnez **Éléments de la place de marché**, puis sélectionnez **Ajouter à partir d’Azure** :

    ![Ajouter des éléments de la Place de marché à partir d’Azure](media/azure-stack-download-azure-marketplace-item/marketplace.png)

4. Le portail affiche la liste des éléments disponibles en téléchargement à partir de la Place de marché Azure. Vous pouvez filtrer des produits par nom, éditeur et/ou type de produit. Vous pouvez également cliquer sur chaque élément pour voir sa description et d’autres informations, notamment sa taille de téléchargement :

    ![Liste des éléments de la Place de marché Azure ](media/azure-stack-download-azure-marketplace-item/image03.PNG)

5. Sélectionnez les éléments que vous souhaitez, puis sélectionnez **Télécharger**. Le temps de téléchargement varie.

    ![Téléchargement d’un élément de la Place de marché Azure](media/azure-stack-download-azure-marketplace-item/image04.png)

    Une fois le téléchargement terminé, vous pouvez déployer le nouvel élément de la Place de marché en tant qu’opérateur ou utilisateur Azure Stack.

6. Pour déployer l’élément téléchargé, sélectionnez **+ Créer une ressource** puis recherchez le nouvel élément de Place de marché parmi les catégories. Cliquez ensuite sur l’élément pour commencer le processus de déploiement. Le processus varie pour les différents éléments de la Place de marché.

## <a name="disconnected-or-a-partially-connected-scenario"></a>Scénario déconnecté ou partiellement connecté

Si Azure Stack est en mode déconnecté, vous utilisez PowerShell et l’*outil de syndication de la Place de marché* pour télécharger les éléments de la Place de marché sur une machine avec une connexion Internet. Vous transférez alors les éléments vers votre environnement Azure Stack. Dans un environnement déconnecté, vous ne pouvez pas télécharger les éléments de la Place de marché en utilisant le portail Azure Stack.

L’outil de syndication Place de marché peut également être utilisé dans un scénario connecté.

Ce scénario comporte deux parties :

- **Partie 1 :** télécharger à partir de la Place de marché Azure. Sur l’ordinateur avec un accès Internet, vous configurez PowerShell, vous téléchargez l’outil de syndication, puis vous téléchargez les éléments à partir de la Place de marché Azure.  
- **Partie 2 :** Charger et publier sur la Place de marché Azure Stack. Vous déplacez les fichiers que vous avez téléchargés dans votre environnement Azure Stack, vous les importez dans Azure Stack, puis vous les publiez sur la Place de marché Azure.  

### <a name="prerequisites"></a>Prérequis

- Un environnement connecté (qui n’est pas nécessairement Azure Stack). Vous avez besoin d’une connectivité pour accéder à la liste des produits d’Azure avec leurs détails et pour tout télécharger localement. Une fois cette opération effectuée, le reste de la procédure ne nécessite aucune connectivité Internet. Elle crée un catalogue d’éléments que vous avez précédemment téléchargés, que vous pouvez utiliser dans votre environnement déconnecté.

- Une clé USB ou un lecteur externe pour se connecter à votre environnement déconnecté et transférer tous les artefacts nécessaires.

- Un environnement Azure Stack déconnecté avec les prérequis suivants :
  - Votre déploiement Azure Stack doit être [inscrit auprès d’Azure](azure-stack-registration.md).
  - L’ordinateur qui possède une connexion internet doit disposer du **module PowerShell pour Azure Stack version 1.2.11** ou une version ultérieure. S’ils ne sont pas déjà présents, [installez les modules PowerShell spécifiques à Azure Stack](azure-stack-powershell-install.md).
  - Pour activer l’importation d’un élément de la Place de marché téléchargé, l’[environnement PowerShell pour l’opérateur Azure Stack](azure-stack-powershell-configure-admin.md) doit être configuré.
  - Clonez le dépôt GitHub des [outils Azure Stack](https://github.com/Azure/AzureStack-Tools).

- Vous devez disposer d’un [compte de stockage](azure-stack-manage-storage-accounts.md) dans Azure Stack disposant d’un conteneur accessible publiquement (qui est un objet blob de stockage). Vous utilisez le conteneur comme un stockage temporaire pour les fichiers de la galerie des éléments de la Place de marché. Si vous n’êtes pas familiarisé avec les comptes de stockage et les conteneurs, consultez [Travailler avec des objets blob - Portail Azure](/azure/storage/blobs/storage-quickstart-blobs-portal) dans la documentation Azure.

- L’outil de syndication Place de marché est téléchargé lors de la première procédure.

- Vous pouvez installer [AzCopy](/azure/storage/common/storage-use-azcopy) pour bénéficier de performances de téléchargement optimales, mais ce n’est pas obligatoire.

Une fois que vous vous êtes inscrit, vous pouvez ignorer le message suivant qui apparaît dans le panneau Gestion de la Place de marché, car cela n’est pas pertinent pour l’utilisation en mode déconnecté :

[![Message de non-inscription](media/azure-stack-download-azure-marketplace-item/toolsmsgsm.png "Message de non-inscription")](media/azure-stack-download-azure-marketplace-item/toolsmsg.png#lightbox)

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>Utiliser l’outil de syndication Place de marché pour télécharger les éléments de la Place de marché

> [!IMPORTANT]
> Veillez à télécharger l’outil de syndication de la Place de marché chaque fois que vous téléchargez des éléments de Place de marché dans un scénario déconnecté. Des changements fréquents sont apportés à ce script et la version la plus récente doit être utilisée pour chaque téléchargement.

1. Sur un ordinateur avec une connexion Internet, ouvrez une console PowerShell en tant qu’administrateur.

2. Ajoutez le compte Azure que vous avez utilisé pour inscrire Azure Stack. Pour ajouter le compte, exécutez `Add-AzureRmAccount` sans aucun paramètre dans PowerShell. Vous êtes invité à entrer vos informations d’identification de compte Azure et vous devrez peut-être utiliser l’authentification à deux facteurs, en fonction de la configuration de votre compte.

   [!include[Remove Account](../../includes/remove-account.md)]

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

5. Importez le module de syndication et lancez l’outil en exécutant les commandes suivantes. Remplacez `Destination folder path` par un emplacement pour stocker les fichiers que vous téléchargez à partir de la Place de marché Azure.

   ```powershell  
   Import-Module .\Syndication\AzureStack.MarketplaceSyndication.psm1

   Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes"
   ```

   Notez que `Export-AzSOfflineMarketplaceItem` a un autre indicateur `-cloud` qui spécifie l’environnement cloud. Par défaut, il s’agit de **azurecloud**.

6. Quand l’outil s’exécute, vous voyez normalement un écran similaire à l’image suivante, avec la liste des éléments de Place de marché disponibles :

   [![Fenêtre contextuelle des éléments de la Place de marché Azure](media/azure-stack-download-azure-marketplace-item/image05.png "Éléments de la Place de marché Azure")](media/azure-stack-download-azure-marketplace-item/image05.png#lightbox)

7. Si vous n’avez pas installé les outils de Stockage Azure, vous recevez le message suivant. Pour pouvoir installer ces outils, veillez à télécharger [AzCopy ](/azure/storage/common/storage-use-azcopy#download-and-install-azcopy-on-windows) :

   ![Outils de stockage](media/azure-stack-download-azure-marketplace-item/vmnew1.png)

8. Sélectionnez l’élément que vous voulez télécharger, puis notez la **version**. Vous pouvez maintenir la touche **Ctrl** enfoncée pour sélectionner plusieurs images. Vous référencez la *version* lorsque vous importez l’élément dans la procédure suivante.

   Vous pouvez également filtrer la liste des images à l’aide de l’option **Ajouter des critères**.

9. Sélectionnez **OK**, puis lisez et acceptez les conditions juridiques.

10. Le temps de téléchargement dépend de la taille de l’élément. Une fois le téléchargement terminé, l’élément est disponible dans le dossier que vous avez spécifié dans le script. Le téléchargement comprend un fichier VHD (pour les machines virtuelles) ou un fichier .zip (pour les extensions de machine virtuelle). Il peut également inclure un package de la galerie au format *.azpkg*, autrement dit un simple fichier .zip.

11. Si le téléchargement échoue, vous pouvez réessayer en exécutant de nouveau l’applet de commande PowerShell suivante :

    ```powershell
    Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes"
    ```

    Avant de procéder à une nouvelle tentative, supprimez le dossier du produit dans lequel le téléchargement a échoué. Par exemple, si le script de téléchargement échoue lors du téléchargement sur `D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1`, supprimez le dossier `D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1`, puis réexécutez l’applet de commande.

### <a name="import-the-download-and-publish-to-azure-stack-marketplace-using-powershell"></a>Importer le téléchargement et le publier sur la Place de marché Azure Stack avec PowerShell

1. Vous devez déplacer les fichiers que vous avez [précédemment téléchargés](#use-the-marketplace-syndication-tool-to-download-marketplace-items) localement, afin de les rendre disponibles dans votre environnement Azure Stack. L’outil de syndication de la Place de marché doit également être disponible dans votre environnement Azure Stack, car vous devez utiliser cet outil pour effectuer l’opération d’importation.

   L’illustration suivante montre un exemple de structure de dossier. `D:\downloadfolder` contient tous les éléments de place de marché téléchargés. Chaque sous-dossier est un élément de la Place de marché (par exemple `microsoft.custom-script-linux-arm-2.0.3`), nommé d’après l’ID de produit. À l’intérieur de chaque sous-dossier se trouve le contenu téléchargé de l’élément de la Place de marché.

   [![Structure du répertoire de téléchargement de la Place de marché](media/azure-stack-download-azure-marketplace-item/mp1sm.png "Structure du répertoire de téléchargement de la Place de marché")](media/azure-stack-download-azure-marketplace-item/mp1.png#lightbox)

2. Pour configurer la session PowerShell de l’opérateur Azure Stack, suivez les instructions de [cet article](azure-stack-powershell-configure-admin.md).

3. Importez le module de syndication, puis lancez l’outil de syndication de place de marché en exécutant le script suivant :

   ```powershell
   $credential = Get-Credential -Message "Enter the azure stack operator credential:"
   Import-AzSOfflineMarketplaceItem -origin "marketplace content folder" -AzsCredential $credential
   ```

   Le paramètre `-origin` spécifie le dossier de plus haut niveau qui contient tous les produits téléchargés, par exemple `"D:\downloadfolder"`.

   Le paramètre `-AzsCredential` est facultatif. Il est utilisé pour renouveler le jeton d’accès s’il a expiré. Si le paramètre `-AzsCredential` n’est pas spécifié et que le jeton expire, vous êtes invité à entrer les informations d’identification de l’opérateur.

    > [!NOTE]  
    > AD FS prend en charge uniquement l’authentification interactive avec des identités d’utilisateurs. Si un objet d’identification est nécessaire, vous devez utiliser un SPN (nom de principal du service). Pour plus d’informations sur la configuration d’un principal de service avec Azure Stack et AD FS en tant que service de gestion des identités, voir [Gérer un principal de service AD FS](azure-stack-create-service-principals.md#manage-an-ad-fs-service-principal).

4. Une fois l’exécution du script terminée correctement, l’élément doit être disponible dans la Place de marché Azure Stack.

## <a name="next-steps"></a>Étapes suivantes

- [Ajouter une image de machine virtuelle personnalisée](azure-stack-add-vm-image.md)
- [Créer et publier un élément personnalisé de Place de marché](azure-stack-create-and-publish-marketplace-item.md)
