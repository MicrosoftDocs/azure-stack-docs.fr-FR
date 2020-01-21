---
title: Créer et publier un élément de la Place de marché dans Azure Stack Hub | Microsoft Docs
description: Découvrez comment créer et publier un élément de la Place de marché Azure Stack Hub.
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
ms.date: 01/03/2020
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: aa308690caa875d2ab22ca0b987634c2d29795fa
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75882757"
---
# <a name="create-and-publish-a-custom-azure-stack-hub-marketplace-item"></a>Créer et publier un élément personnalisé de Place de marché Azure Stack Hub

Chaque élément publié sur la Place de marché Azure Stack Hub utilise le format Azure Gallery Package (.azpkg). L’outil *Azure Gallery Packager* vous permet de créer un package de galerie Azure personnalisé que vous pouvez charger sur la Place de marché Azure Stack Hub, et qui peut ensuite être téléchargé par les utilisateurs. Le processus de déploiement utilise un modèle Azure Resource Manager.

## <a name="marketplace-items"></a>Éléments du Marketplace

Les exemples de cet article montrent comment créer une offre de la Place de marché de machines virtuelles, de type Windows ou Linux.

## <a name="create-a-marketplace-item"></a>Créer un élément de Marketplace

> [!IMPORTANT]
> Avant de créer l’élément de Place de marché de machines virtuelles, chargez l’image de machine virtuelle personnalisée sur le portail Azure Stack Hub, en suivant les instructions de [Ajouter une image de machine virtuelle à Azure Stack Hub](azure-stack-add-vm-image.md). Ensuite, suivez les instructions de cet article pour empaqueter l’image (créer un fichier .azpkg) et la charger sur la Place de marché Azure Stack Hub.

Pour créer un élément de Place de marché personnalisé, procédez comme suit :

1. Téléchargez l’[outil Azure Gallery Packager](https://aka.ms/azsmarketplaceitem) et l’exemple de package de galerie Azure Stack Hub. Ce téléchargement comprend des modèles de machine virtuelle personnalisés. Extrayez le fichier .zip, puis sous le dossier **Custom VMs** (Machines virtuelles personnalisées), utilisez les modèles Linux ou Windows disponibles. Vous pouvez décider de réutiliser les modèles prédéfinis et de modifier les paramètres respectifs en fonction des détails de produit de l’élément à afficher sur votre portail Azure Stack Hub. Sinon, vous pouvez simplement réutiliser le fichier .azpkg disponible et ignorer les étapes suivantes pour personnaliser votre propre package de galerie.

2. Créez un modèle Azure Resource Manager ou utilisez nos exemples de modèles pour Windows/Linux. Ces exemples de modèles sont fournis dans le fichier .zip de l’outil Packager que vous avez téléchargé à l’étape 1. Vous pouvez utiliser le modèle et modifier les champs de texte, ou vous pouvez télécharger un modèle préconfiguré à partir de GitHub. Pour plus d’informations sur les modèles Azure Resource Manager, consultez [Modèles Azure Resource Manager](/azure/azure-resource-manager/resource-group-authoring-templates).

3. Le package de galerie doit contenir la structure suivante :

   ![Capture d’écran de la structure du package de galerie](media/azure-stack-create-and-publish-marketplace-item/gallerypkg1.png)

   La structure des fichiers des modèles de déploiement se présente comme suit :

   ![Capture d’écran de la structure des modèles de déploiement](media/azure-stack-create-and-publish-marketplace-item/gallerypkg2.png)

4. Remplacez les valeurs en surbrillance suivantes (celles avec des numéros) dans le modèle Manifest.json par la valeur que vous avez fournie lors du [chargement de votre image personnalisée](azure-stack-add-vm-image.md).

   > [!NOTE]  
   > Ne codez jamais en dur des secrets tels que des clés de produit, des mots de passe ou des informations d’identification de client dans le modèle Azure Resource Manager. Les fichiers modèles JSON sont accessibles sans authentification une fois qu’ils sont publiés dans la galerie. Stockez tous les secrets dans [Key Vault](/azure/azure-resource-manager/resource-manager-keyvault-parameter) et appelez-les à partir du modèle.

   Le modèle suivant est un exemple de fichier Manifest.json :

    ```json
    {
       "$schema": "https://gallery.azure.com/schemas/2015-10-01/manifest.json#",
       "name": "Test", (1)
       "publisher": "<Publisher name>", (2)
       "version": "<Version number>", (3)
       "displayName": "ms-resource:displayName", (4)
       "publisherDisplayName": "ms-resource:publisherDisplayName", (5)
       "publisherLegalName": "ms-resource:publisherDisplayName", (6)
       "summary": "ms-resource:summary",
       "longSummary": "ms-resource:longSummary",
       "description": "ms-resource:description",
       "longDescription": "ms-resource:description",
       "uiDefinition": {
          "path": "UIDefinition.json" (7)
          },
       "links": [
        { "displayName": "ms-resource:documentationLink", "uri": "http://go.microsoft.com/fwlink/?LinkId=532898" }
        ],
       "artifacts": [
          {
             "name": "<Template name>",
             "type": "Template",
             "path": "DeploymentTemplates\\<Template name>.json", (8)
             "isDefault": true
          }
       ],
       "categories":[ (9)
          "Custom",
          "<Template name>"
          ],
       "images": [{
          "context": "ibiza",
          "items": [{
             "id": "small",
             "path": "icons\\Small.png", (10)
             "type": "icon"
             },
             {
                "id": "medium",
                "path": "icons\\Medium.png",
                "type": "icon"
             },
             {
                "id": "large",
                "path": "icons\\Large.png",
                "type": "icon"
             },
             {
                "id": "wide",
                "path": "icons\\Wide.png",
                "type": "icon"
             }]
        }]
    }
    ```

    La liste suivante décrit les valeurs numérotées précédentes dans l’exemple de modèle :

    - (1) : Nom de l’offre.
    - (2) : Nom de l’éditeur, sans espace.
    - (3) : Version de votre modèle, sans espace.
    - (4) : Nom que les clients voient.
    - (5) : Nom de l’éditeur que les clients voient.
    - (6) : Raison sociale de l’éditeur.
    - (7) : Chemin vers l’emplacement de stockage de votre fichier **UIDefinition.json**.  
    - (8) : Chemin et nom de votre fichier de modèle principal JSON.
    - (9) : Noms des catégories dans lesquelles ce modèle est affiché.
    - (10) : Chemin et nom pour chaque icône.

5. Pour tous les champs qui référencent **ms-resource**, vous devez changer les valeurs appropriées dans le fichier **strings/resources.json** :

    ```json
    {
    "displayName": "<OfferName.PublisherName.Version>",
    "publisherDisplayName": "<Publisher name>",
    "summary": "Create a simple VM",
    "longSummary": "Create a simple VM and use it",
    "description": "<p>This is just a sample of the type of description you could create for your gallery item!</p><p>This is a second paragraph.</p>",
    "documentationLink": "Documentation"
    }
    ```

    ![Affichage du package](media/azure-stack-create-and-publish-marketplace-item/pkg1.png)![Affichage du package](media/azure-stack-create-and-publish-marketplace-item/pkg2.png)

6. Pour garantir la réussite du déploiement de la ressource, testez le modèle avec les [API Azure Stack Hub](../user/azure-stack-profiles-azure-resource-manager-versions.md).

7. Si votre modèle repose sur une image de machine virtuelle, suivez les instructions pour [ajouter une image de machine virtuelle à Azure Stack Hub](azure-stack-add-vm-image.md).

8. Enregistrez votre modèle Azure Resource Manager dans le dossier **/Contoso.TodoList/DeploymentTemplates/** .

9. Choisissez les icônes et le texte de votre article de Marketplace. Ajouter les icônes au dossier **Icônes** et le texte au fichier des **ressources** dans le dossier **Chaînes**. Utilisez la convention de dénomination **Petit**, **Moyen**, **Grand** et **Large** pour vos icônes. Vous trouverez une description détaillée sur la page [Référence : interface utilisateur de l’article de Place de marché](#reference-marketplace-item-ui).

    > [!NOTE]
    > Les quatre tailles d’icône (petite, moyenne, grande, large) sont requises pour créer correctement l’article de Marketplace.

10. Pour toute autre modification apportée à Manifest.json, consultez [Informations de référence : manifest.json d’un article de Place de marché](#reference-marketplace-item-manifestjson).

11. Quand vous avez terminé de modifier vos fichiers, convertissez-les en un fichier .azpkg. Vous effectuez la conversion à l’aide de l’outil **AzureGallery.exe** et de l’exemple de package de galerie que vous avez précédemment téléchargé. Exécutez la commande suivante :

    ```shell
    .\AzureGallery.exe package –m c:\<path>\<gallery package name>\manifest.json –o c:\Temp
    ```

    > [!NOTE]
    > Le chemin de sortie peut être n’importe quel chemin de votre choix et ne doit pas nécessairement se trouver sous le lecteur C :. Cependant, le chemin complet du fichier manifest.json et celui du package de sortie doivent exister. Par exemple, si le chemin de sortie est `C:\<path>\galleryPackageName.azpkg`, le dossier `C:\<path>` doit exister.
    >
    >

## <a name="publish-a-marketplace-item"></a>Publier un élément du Marketplace

1. Utilisez PowerShell ou l’Explorateur Stockage Azure pour charger votre article de Marketplace (.azpkg) sur le Stockage Blob Azure. Vous pouvez effectuer le chargement sur le stockage Azure Stack Hub local ou sur le Stockage Azure, qui est un emplacement temporaire pour le package. Assurez-vous que l’objet blob est accessible publiquement.

2. Pour importer le package de galerie dans Azure Stack Hub, la première étape consiste à vous connecter à distance à la machine virtuelle cliente, afin de copier le fichier que vous venez de créer dans votre cloud Azure Stack Hub.

3. Ajoutez un contexte :

    ```powershell
    $ArmEndpoint = "https://adminmanagement.local.azurestack.external"
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint $ArmEndpoint
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin"
    ```

4. Exécutez le script suivant pour importer la ressource dans votre galerie :

    ```powershell
    Add-AzsGalleryItem -GalleryItemUri `
    https://sample.blob.core.windows.net/<temporary blob name>/<offerName.publisherName.version>.azpkg –Verbose
    ```

5. Veillez à disposer d’un compte de stockage valide disponible pour stocker votre élément. Vous pouvez obtenir la valeur de `GalleryItemURI` auprès du portail d’administration Azure Stack Hub. Sélectionnez **Compte de stockage > Propriétés de l’objet blob -> URL**, avec l’extension .azpkg. Le compte de stockage est utilisé seulement de façon temporaire pour publier sur la Place de marché.

   Une fois que vous avez terminé votre package de galerie et que vous l’avez chargé avec **Add-AzsGalleryItem**, votre machine virtuelle personnalisée doit maintenant apparaître dans la Place de marché ainsi que dans la vue **Créer une ressource**. Notez que le package de galerie personnalisé n’est pas visible dans **Gestion de la Place de marché**.

   [![Élément de Place de marché personnalisé téléchargé](media/azure-stack-create-and-publish-marketplace-item/pkg6sm.png "Élément de Place de marché personnalisé téléchargé")](media/azure-stack-create-and-publish-marketplace-item/pkg6.png#lightbox)

6. Une fois que votre élément a été publié sur la Place de marché, vous pouvez supprimer le contenu du compte de stockage.

   > [!CAUTION]  
   > Tous les artefacts par défaut et vos artefacts personnalisés de la galerie sont désormais accessibles sans authentification sous les URL suivantes :  
   `https://adminportal.[Region].[external FQDN]:30015/artifact/20161101/[Template Name]/DeploymentTemplates/Template.json`
   `https://portal.[Region].[external FQDN]:30015/artifact/20161101/[Template Name]/DeploymentTemplates/Template.json`

6. Vous pouvez supprimer un article de Marketplace avec la cmdlet **Remove-AzureRMGalleryItem**. Par exemple :

   ```powershell
   Remove-AzsGalleryItem -Name <Gallery package name> -Verbose
   ```

   > [!NOTE]
   > L’interface utilisateur Marketplace peut présenter une erreur après la suppression d’un élément. Pour résoudre le problème, cliquez sur **Paramètres** sur le portail. Sélectionnez ensuite **Ignorer les modifications** sous **Personnalisation du portail**.
   >
   >

## <a name="reference-marketplace-item-manifestjson"></a>Référence : manifest.json d’un article de Place de marché

### <a name="identity-information"></a>Informations d’identité

| Name | Obligatoire | Type | Contraintes | Description |
| --- | --- | --- | --- | --- |
| Name |X |String |[A-Za-z0-9]+ | |
| Serveur de publication |X |String |[A-Za-z0-9]+ | |
| Version |X |String |[SemVer v2](https://semver.org/) | |

### <a name="metadata"></a>Métadonnées

| Name | Obligatoire | Type | Contraintes | Description |
| --- | --- | --- | --- | --- |
| DisplayName |X |String |Recommandation : 80 caractères |Le portail risque de ne pas afficher correctement le nom de votre élément s’il comporte plus de 80 caractères. |
| PublisherDisplayName |X |String |Recommandation : 30 caractères |Le portail risque de ne pas afficher correctement le nom de votre éditeur s’il comporte plus de 30 caractères. |
| PublisherLegalName |X |String |256 caractères maximum | |
| Résumé |X |String |60 à 100 caractères | |
| LongSummary |X |String |140 à 256 caractères |Non encore applicable dans Azure Stack Hub. |
| Description |X |[HTML](https://github.com/Azure/portaldocs/blob/master/gallery-sdk/generated/index-gallery.md#gallery-item-metadata-html-sanitization) |Entre 500 et 5 000 caractères | |

### <a name="images"></a>Images

La Marketplace utilise les icônes suivantes :

| Name | Largeur | Hauteur | Notes |
| --- | --- | --- | --- |
| Large |255 px |115 px |Toujours obligatoire |
| grand |115 px |115 px |Toujours obligatoire |
| Moyenne |90 px |90 px |Toujours obligatoire |
| Petite |40 px |40 px |Toujours obligatoire |
| Capture d'écran |533 px |324 px |Toujours obligatoire |

### <a name="categories"></a>Catégories

Chaque article de Marketplace doit être étiqueté avec une catégorie qui identifie l’endroit où il apparaît sur l’interface utilisateur du portail. Vous pouvez choisir l’une des catégories existantes d’Azure Stack Hub (**Calcul**, **Données + stockage**, etc.) ou une nouvelle.

### <a name="links"></a>Liens

Chaque article de Marketplace peut comporter différents liens vers du contenu supplémentaire. Les liens sont spécifiés comme une liste de noms et d’URI :

| Name | Obligatoire | Type | Contraintes | Description |
| --- | --- | --- | --- | --- |
| DisplayName |X |String |64 caractères maximum. | |
| Uri |X |URI | | |

### <a name="additional-properties"></a>Propriétés supplémentaires

En plus des métadonnées précédentes, les auteurs de la Marketplace peuvent fournir des données de paire clé-valeur personnalisées sous la forme suivante :

| Name | Obligatoire | Type | Contraintes | Description |
| --- | --- | --- | --- | --- |
| DisplayName |X |String |25 caractères maximum. | |
| Valeur |X |String |30 caractères maximum. | |

### <a name="html-sanitization"></a>Nettoyage du HTML

Pour tout champ autorisant le HTML, les [éléments et attributs suivants sont autorisés](https://github.com/Azure/portaldocs/blob/master/gallery-sdk/generated/index-gallery.md#gallery-item-metadata-html-sanitization) :

`h1, h2, h3, h4, h5, p, ol, ul, li, a[target|href], br, strong, em, b, i`

## <a name="reference-marketplace-item-ui"></a>Référence : IU d'article de Place de marché

Voici les icônes et le texte des éléments de Place de marché qui s’affichent sur le portail Azure Stack Hub.

### <a name="create-blade"></a>Panneau Créer

![Panneau Créer — Éléments de la Place de marché Azure Stack Hub](media/azure-stack-create-and-publish-marketplace-item/image1.png)

### <a name="marketplace-item-details-blade"></a>Panneau Détails de l’élément du Marketplace

![Panneau des détails de l’élément de la Place de marché Azure Stack Hub](media/azure-stack-create-and-publish-marketplace-item/image3.png)

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble de la Place de marché Azure Stack Hub](azure-stack-marketplace.md)
- [Télécharger des éléments de la Place de marché](azure-stack-download-azure-marketplace-item.md)
- [Format et structure des modèles Azure Resource Manager](/azure/azure-resource-manager/resource-group-authoring-templates)
