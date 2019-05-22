---
title: Créer et publier un article de Marketplace dans Azure Stack | Microsoft Docs
description: Créez et publiez un article de Marketplace dans Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 59e86e15289833d63b85314a84d0bb9e60dc5da8
ms.sourcegitcommit: ccd86bd0862c45de1f6a4993f783ea2e186c187a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65172567"
---
# <a name="create-and-publish-a-marketplace-item"></a>Créer et publier un article de la Place de marché

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

## <a name="create-a-marketplace-item"></a>Créer un élément de Marketplace

1. Téléchargez l’[outil Azure Gallery Packager](https://www.aka.ms/azurestackmarketplaceitem) et l’échantillon de Marketplace Azure Stack.
2. Ouvrez l’échantillon de Marketplace et renommez le dossier **SimpleVMTemplate**. Utilisez le même nom que votre article de Place de marché, par exemple, **Contoso.TodoList**. Ce dossier contient les éléments suivants :

   ```shell
   /Contoso.TodoList/
   /Contoso.TodoList/Manifest.json
   /Contoso.TodoList/UIDefinition.json
   /Contoso.TodoList/Icons/
   /Contoso.TodoList/Strings/
   /Contoso.TodoList/DeploymentTemplates/
   ```

3. [Créez un modèle Azure Resource Manager](/azure/azure-resource-manager/resource-group-authoring-templates) ou choisissez un modèle sur GitHub. L’article de Marketplace utilise ce modèle pour créer une ressource.

    > [!NOTE]  
    > Ne codez jamais en dur des secrets tels que des clés de produit, des mots de passe ou des informations d’identification de client dans le modèle Azure Resource Manager. Les fichiers modèles JSON sont accessibles sans authentification une fois qu’ils sont publiés dans la galerie. Stockez tous les secrets dans [Key Vault](/azure/azure-resource-manager/resource-manager-keyvault-parameter) et appelez-les à partir du modèle.

4. Pour vérifier que le déploiement de la ressource peut réussir, testez le modèle avec les API Microsoft Azure Stack.
5. Si votre modèle repose sur une image de machine virtuelle, suivez les instructions pour [ajouter une image de machine virtuelle sur Azure Stack](azure-stack-add-vm-image.md).
6. Enregistrez votre modèle Azure Resource Manager dans le dossier **/Contoso.TodoList/DeploymentTemplates/**.
7. Choisissez les icônes et le texte de votre article de Marketplace. Ajouter les icônes au dossier **Icônes** et le texte au fichier des **ressources** dans le dossier **Chaînes**. Utilisez la convention de dénomination **Petit**, **Moyen**, **Grand** et **Large** pour vos icônes. Vous trouverez une description détaillée sur la page [Référence : interface utilisateur de l’article de Place de marché](#reference-marketplace-item-ui).

   > [!NOTE]
   > Les quatre tailles d’icône (petite, moyenne, grande, large) sont requises pour créer correctement l’article de Marketplace.
   >
   >

8. Dans le fichier **Manifest.json**, remplacez le **nom** par celui de votre article de Place de marché. Modifiez également **l’éditeur** en indiquant votre nom ou votre société.
9. Sous **artefacts**, remplacez le **nom** et le **chemin d’accès** par les informations correctes du modèle Azure Resource Manager que vous avez inclus :

   ```json
   "artifacts": [
      {
          "name": "Your template name",
          "type": "Template",
          "path": "DeploymentTemplates\\your path",
          "isDefault": true
      }
   ```

10. Remplacez **Mes articles de Place de marché** par la liste des catégories dans lesquelles votre article de Marketplace doit apparaître :

    ```json
    "categories":[
    "My Marketplace Items"
    ],
    ```

11. Pour toute autre modification apportée à manifest.json, consultez la page [Référence : manifest.json d’un article de Place de marché](#reference-marketplace-item-manifestjson).

12. Pour créer un package dans un fichier .azpkg, ouvrez une invite de commandes et exécutez la commande suivante :

    ```shell
    AzureGalleryPackager.exe package -m <path to manifest.json> -o <output location for the package>
    ```

    > [!NOTE]
    > Le chemin d’accès complet au package de sortie doit exister. Par exemple, si le chemin de sortie est C:\MarketPlaceItem\votrepackage.azpkg, le dossier **C:\MarketPlaceItem doit exister**.
    >
    >

## <a name="publish-a-marketplace-item"></a>Publier un élément du Marketplace

1. Utilisez PowerShell ou l’Explorateur Stockage Azure pour charger votre article de Marketplace (.azpkg) sur le Stockage Blob Azure. Vous pouvez effectuer le chargement sur le stockage Azure Stack local ou sur le Stockage Azure, qui est un emplacement temporaire pour le package. Assurez-vous que l’objet blob est accessible publiquement.
2. Sur la machine virtuelle cliente de l’environnement Microsoft Azure Stack, assurez-vous que votre session PowerShell est configurée avec vos informations d’identification d’administrateur de services fédérés. Vous trouverez des instructions sur l’authentification de PowerShell dans Azure Stack sur la page [Déployer un modèle avec PowerShell](../user/azure-stack-deploy-template-powershell.md).
3. Lorsque vous utilisez [PowerShell 1.3.0](azure-stack-powershell-install.md) ou une version ultérieure, vous pouvez utiliser la cmdlet PowerShell **Add-AzsGalleryItem** pour publier l’élément de Place de marché sur Azure Stack. Avant d’utiliser PowerShell 1.3.0, utilisez la cmdlet **Add-AzureRMGalleryitem** à la place de la cmdlet **Add-AzsGalleryItem**. Par exemple, lorsque vous utilisez PowerShell 1.3.0 ou une version ultérieure :

   ```powershell
   Add-AzsGalleryItem -GalleryItemUri `
   https://sample.blob.core.windows.net/gallerypackages/Microsoft.SimpleTemplate.1.0.0.azpkg -Verbose
   ```

   | Paramètre | Description |
   | --- | --- |
   | SubscriptionID |ID d’abonnement d’administrateur. Vous pouvez le récupérer avec PowerShell. Si vous préférez le récupérer sur le portail, accédez à l’abonnement du fournisseur et copiez l’ID d’abonnement. |
   | GalleryItemUri |URI Blob du package de galerie déjà chargé sur le stockage. |
   | Apiversion |Définir sur **2015-04-01**. |

4. Accédez au portail. L’article de Place de marché apparaît maintenant sur le portail, en tant qu’opérateur ou qu’utilisateur. Le package peut mettre quelques minutes à s’afficher.

5. Votre article de Marketplace est maintenant enregistré sur la Marketplace Azure Stack. Vous pouvez choisir de le supprimer de votre emplacement de Stockage Blob.

    > [!CAUTION]  
    > Tous les artefacts par défaut et vos artefacts personnalisés de la galerie sont désormais accessibles sans authentification sous les URL suivantes :  
`https://adminportal.[Region].[external FQDN]:30015/artifact/20161101/[Template Name]/DeploymentTemplates/Template.json`
`https://portal.[Region].[external FQDN]:30015/artifact/20161101/[Template Name]/DeploymentTemplates/Template.json`
`https://systemgallery.blob.[Region].[external FQDN]/dev20161101-microsoft-windowsazure-gallery/[Template Name]/UiDefinition.json`

6. Vous pouvez supprimer un article de Marketplace avec la cmdlet **Remove-AzureRMGalleryItem**. Par exemple : 

   ```powershell
   Remove-AzsGalleryItem -Name Microsoft.SimpleTemplate.1.0.0  -Verbose
   ```

   > [!NOTE]
   > L’interface utilisateur Marketplace peut présenter une erreur après la suppression d’un élément. Pour résoudre le problème, cliquez sur **Paramètres** sur le portail. Sélectionnez ensuite **Ignorer les modifications** sous **Personnalisation du portail**.
   >
   >

## <a name="reference-marketplace-item-manifestjson"></a>Référence : manifest.json d’un article de Place de marché

### <a name="identity-information"></a>Informations d’identité

| Nom | Obligatoire | Type | Contraintes | Description |
| --- | --- | --- | --- | --- |
| Nom |X |Chaîne |[A-Za-z0-9]+ | |
| Publisher |X |Chaîne |[A-Za-z0-9]+ | |
| Version |X |Chaîne |[SemVer v2](https://semver.org/) | |

### <a name="metadata"></a>Métadonnées

| Nom | Obligatoire | Type | Contraintes | Description |
| --- | --- | --- | --- | --- |
| DisplayName |X |Chaîne |Recommandation : 80 caractères |Le portail risque de ne pas afficher correctement le nom de votre élément s’il comporte plus de 80 caractères. |
| PublisherDisplayName |X |Chaîne |Recommandation : 30 caractères |Le portail risque de ne pas afficher correctement le nom de votre éditeur s’il comporte plus de 30 caractères. |
| PublisherLegalName |X |Chaîne |256 caractères maximum | |
| Résumé |X |Chaîne |60 à 100 caractères | |
| LongSummary |X |Chaîne |140 à 256 caractères |Non encore applicable dans Azure Stack |
| Description |X |[HTML](https://github.com/Azure/portaldocs/blob/master/gallery-sdk/generated/index-gallery.md#gallery-item-metadata-html-sanitization) |Entre 500 et 5 000 caractères | |

### <a name="images"></a>Images

La Marketplace utilise les icônes suivantes :

| Nom | Largeur | Hauteur | Notes |
| --- | --- | --- | --- |
| Large |255 px |115 px |Toujours obligatoire |
| grand |115 px |115 px |Toujours obligatoire |
| Moyenne |90 px |90 px |Toujours obligatoire |
| Petite |40 px |40 px |Toujours obligatoire |
| Capture d'écran |533 px |32 px |Facultatif |

### <a name="categories"></a>Catégories

Chaque article de Marketplace doit être étiqueté avec une catégorie qui identifie l’endroit où il apparaît sur l’interface utilisateur du portail. Vous pouvez choisir l’une des catégories existantes d’Azure Stack (**Calcul**, **Données + stockage**, etc.) ou une nouvelle.

### <a name="links"></a>Liens

Chaque article de Marketplace peut comporter différents liens vers du contenu supplémentaire. Les liens sont spécifiés comme une liste de noms et d’URI :

| Nom | Obligatoire | Type | Contraintes | Description |
| --- | --- | --- | --- | --- |
| DisplayName |X |Chaîne |64 caractères maximum. | |
| Uri |X |URI | | |

### <a name="additional-properties"></a>Propriétés supplémentaires

En plus des métadonnées précédentes, les auteurs de la Marketplace peuvent fournir des données de paire clé-valeur personnalisées sous la forme suivante :

| Nom | Obligatoire | Type | Contraintes | Description |
| --- | --- | --- | --- | --- |
| DisplayName |X |Chaîne |25 caractères maximum. | |
| Valeur |X |Chaîne |30 caractères maximum. | |

### <a name="html-sanitization"></a>Nettoyage du HTML

Pour tout champ autorisant le HTML, les [éléments et attributs suivants sont autorisés](https://github.com/Azure/portaldocs/blob/master/gallery-sdk/generated/index-gallery.md#gallery-item-metadata-html-sanitization) :

`h1, h2, h3, h4, h5, p, ol, ul, li, a[target|href], br, strong, em, b, i`

## <a name="reference-marketplace-item-ui"></a>Référence : IU d'article de Place de marché

Voici les icônes et le texte des articles de Marketplace qui s’affichent sur le portail Azure Stack.

### <a name="create-blade"></a>Panneau Créer

![Panneau Créer](media/azure-stack-create-and-publish-marketplace-item/image1.png)

### <a name="marketplace-item-details-blade"></a>Panneau Détails de l’élément du Marketplace

![Panneau Détails de l’élément du Marketplace](media/azure-stack-create-and-publish-marketplace-item/image3.png)

## <a name="next-steps"></a>Étapes suivantes

* [Vue d’ensemble de la Place de Marché Azure Stack](azure-stack-marketplace.md)
* [Télécharger des éléments de la Place de marché](azure-stack-download-azure-marketplace-item.md)
