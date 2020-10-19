---
title: Ajouter une image de machine virtuelle personnalisée à Azure Stack Hub
description: Apprenez à ajouter ou supprimer une image de machine virtuelle personnalisée dans Azure Stack Hub.
author: sethmanheim
ms.topic: how-to
ms.date: 10/12/2020
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 9/8/2020
ms.openlocfilehash: 496d706b6ed930087207b24047d3409f29a53e53
ms.sourcegitcommit: 8122672409954815e472a5b251bb7319fab8f951
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92060121"
---
# <a name="add-and-remove-a-custom-vm-image-to-azure-stack-hub"></a>Ajouter et supprimer une image de machine virtuelle personnalisée dans Azure Stack Hub

Dans Azure Stack Hub, en tant qu’opérateur, vous pouvez ajouter votre image de machine virtuelle personnalisée à la Place de marché pour la mettre à la disposition de vos utilisateurs. Vous pouvez ajouter des images de machine virtuelle à la Place de marché Azure Stack Hub via le portail administrateur ou Windows PowerShell. Utilisez une image de la Place de marché Microsoft Azure globale en tant que base de votre image personnalisée, ou créez la vôtre à l’aide d’Hyper-V.

## <a name="add-an-image"></a>Ajouter une image

Vous trouverez des instructions sur l’ajout d’images généralisées et spécialisées dans la section **Compute** du guide de l’utilisateur. Vous pouvez créer une image généralisée avant d’offrir l’image à vos utilisateurs. Pour obtenir des instructions, consultez [Déplacer une machine virtuelle vers Azure Stack Hub](../user/vm-move-overview.md). Lorsque vous créez des images disponibles pour vos locataires, utilisez le portail d’administration Azure Stack Hub ou les points de terminaison d’administrateur plutôt que les points de terminaison du portail de l’utilisateur ou de l’annuaire du locataire.

Vous avez deux options pour mettre une image à la disposition de vos utilisateurs :

- **Offrir une image uniquement accessible via Azure Resource Manager**  
  Si vous ajoutez l’image via le portail d’administration Azure Stack Hub dans **Calcul** > **Images**, tous vos locataires peuvent accéder à l’image. Toutefois, vos utilisateurs doivent utiliser un modèle Azure Resource Manager pour y accéder. Elle ne sera pas visible dans votre Place de marché Azure Stack Hub.

- **Proposer une image via la Place de marché Azure Stack Hub**  
    Une fois que vous avez ajouté votre image par le biais du portail d’administration Azure Stack Hub, vous pouvez créer une offre de la Place de marché. Pour des instructions, consultez [Créer et publier un élément personnalisé de Place de marché Azure Stack Hub](azure-stack-create-and-publish-marketplace-item.md).

## <a name="add-a-platform-image"></a>Ajouter une image de plateforme

Pour ajouter une image de plateforme à Azure Stack Hub, utilisez le portail d’administrateur ou le point de terminaison Azure Stack Hub à l’aide de PowerShell. Vous devez commencer par créer un disque dur virtuel généralisé. Pour plus d’informations, consultez [Présentation de la procédure permettant de déplacer une machine virtuelle vers Azure Stack Hub](../user/vm-move-overview.md).

### <a name="portal"></a>[Portail](#tab/image-add-portal)

Ajoutez l’image de machine virtuelle en tant qu’opérateur Azure Stack Hub à l’aide du portail.

1. Connectez-vous à Azure Stack Hub en tant qu'opérateur. Sélectionnez **Tableau de bord** dans le volet de navigation de gauche.

2. Dans la liste **Fournisseurs de ressources**, sélectionnez **Calcul**.

   [![Sélectionnez Calcul](./media/azure-stack-add-vm-image/dash-small.png)](./media/azure-stack-add-vm-image/dash.png#lightbox)

3. Sélectionnez **Images de VM**, puis sélectionnez **Ajouter**.

   [![Ajouter une image de machine virtuelle](./media/azure-stack-add-vm-image/tca4-small.png)](./media/azure-stack-add-vm-image/tca4.png#lightbox)

4. Sous **Créer une image**, entrez l’**éditeur**, l’**offre**, la **référence SKU**, la **version** et l’URI d’objet blob de disque de système d’exploitation. Ensuite, sélectionnez **Créer** pour démarrer la création de l’image de machine virtuelle.

   [![IU de chargement indépendant d’image personnalisée](./media/azure-stack-add-vm-image/tca5-small.png)](./media/azure-stack-add-vm-image/tca5.png#lightbox)

   Une fois que l’image de machine virtuelle a été créée, son état passe à **Réussi**.

5. Quand vous ajoutez une image, celle-ci est disponible seulement pour des modèles Azure Resource Manager et des déploiements PowerShell. Pour rendre une image disponible pour vos utilisateurs comme élément de Place de marché, publiez cet élément en suivant les étapes de l’article [Créer et publier un élément de Place de marché](azure-stack-create-and-publish-marketplace-item.md). Notez bien les valeurs pour **Éditeur**, **Offre**, **Référence SKU** et **Version**. Vous en aurez besoin pour modifier le modèle Resource Manager et le fichier Manifest.json dans votre fichier .azpkg personnalisé.

### <a name="powershell"></a>[PowerShell](#tab/image-add-ps)

 Ajoutez une image de machine virtuelle en tant qu’opérateur Azure Stack Hub à l’aide de PowerShell.

1. [Installez PowerShell pour Azure Stack Hub](azure-stack-powershell-install.md).  

2. Connectez-vous à Azure Stack Hub en tant qu'opérateur. Pour obtenir des instructions, consultez [Se connecter à Azure Stack Hub en tant qu'opérateur](azure-stack-powershell-configure-admin.md).

3. Ouvrez PowerShell avec une invite de commandes avec élévation de privilèges et exécutez :

   ```powershell
    Add-AzsPlatformimage -publisher "<publisher>" `
      -offer "<Offer>" `
      -sku "<SKU>" `
      -version "<#.#.#>" `
      -OSType "<OS type>" `
      -OSUri "<OS URI>"
   ```

   L’applet de commande **Add-AzsPlatformimage** spécifie les valeurs utilisées par les modèles Azure Resource Manager pour référencer l’image de machine virtuelle. Les valeurs incluent :
   - **publisher**  
     Par exemple : `Canonical`  
     Segment du nom de l’**éditeur** de l’image de machine virtuelle que les utilisateurs utilisent lors du déploiement de l’image. N’incluez aucun espace ou autre caractère spécial dans ce champ.  
   - **offer**  
     Par exemple : `UbuntuServer`  
     Segment du nom de l’**offre** de l’image de machine virtuelle que les utilisateurs utilisent lors du déploiement de l’image. N’incluez aucun espace ou autre caractère spécial dans ce champ.  
   - **sku**  
     Par exemple : `14.04.3-LTS`  
     Segment de nom de la **référence SKU** de l’image de machine virtuelle dont les utilisateurs se servent au moment du déploiement de l’image. N’incluez aucun espace ou autre caractère spécial dans ce champ.  
   - **version**  
     Par exemple : `1.0.0`  
     Version de l’image de machine virtuelle que les utilisateurs indiquent lors du déploiement de l’image. La version suit le format **\#.\#.\#** , N’incluez aucun espace ou autre caractère spécial dans ce champ.  
   - **osType**  
     Par exemple : `Linux`  
     La valeur de **osType** de l’image, qui doit être **Windows** ou **Linux**.  
   - **OSUri**  
     Par exemple : `https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
     Vous pouvez spécifier un URI de stockage d’objets blob comme `osDisk`.  

     Pour plus de détails, consultez les informations de référence relatives à PowerShell concernant l’applet de commande [Add-AzsPlatformimage](/powershell/module/azs.compute.admin/add-azsplatformimage).

4. Quand vous ajoutez une image, celle-ci est disponible seulement pour des modèles Azure Resource Manager et des déploiements PowerShell. Pour rendre une image disponible pour vos utilisateurs comme élément de Place de marché, publiez cet élément en suivant les étapes de l’article [Créer et publier un élément de Place de marché](azure-stack-create-and-publish-marketplace-item.md). Notez bien les valeurs pour **Éditeur**, **Offre**, **Référence SKU** et **Version**. Vous en aurez besoin pour modifier le modèle Resource Manager et le fichier Manifest.json dans votre fichier .azpkg personnalisé.

---

## <a name="remove-a-platform-image"></a>Supprimer une image de plateforme

Vous pouvez supprimer une image de plateforme à l’aide du portail ou de PowerShell.

### <a name="portal"></a>[Portail](#tab/image-rem-portal)

Pour supprimer l’image de machine virtuelle en tant qu’opérateur Azure Stack Hub à l’aide du portail Azure Stack Hub, effectuez les étapes suivantes :

1. Ouvrez le [portail administrateur](https://portal.azure.com/signin/index) Azure Stack Hub.

2. Si l’image de machine virtuelle a un élément de Place de marché associé, sélectionnez **Gestion de la Place de marché**, puis sélectionnez l’élément de Place de marché de machines virtuelles que vous voulez supprimer.

3. Si l’image de machine virtuelle n’a d’élément de Place de marché associé, accédez à **Tous les services > Calcul > Images de machine virtuelle**, puis sélectionnez les points de suspension ( **...** ) en regard de l’image de machine virtuelle.

4. Sélectionnez **Supprimer**.

### <a name="powershell"></a>[PowerShell](#tab/image-rem-ps)

Pour supprimer l’image de machine virtuelle en tant qu’opérateur Azure Stack Hub à l’aide de PowerShell, effectuez les étapes suivantes :

1. [Installez PowerShell pour Azure Stack Hub](azure-stack-powershell-install.md).

2. Connectez-vous à Azure Stack Hub en tant qu'opérateur.

3. Ouvrez PowerShell avec une invite de commandes avec élévation de privilèges et exécutez :

   ```powershell  
   Remove-AzsPlatformImage `
    -publisher "<Publisher>" `
    -offer "<Offer>" `
    -sku "<SKU>" `
    -version "<Version>" `
   ```

   L’applet de commande **Remove-AzsPlatformImage** spécifie les valeurs utilisées par les modèles Azure Resource Manager pour référencer l’image de machine virtuelle. Les valeurs incluent :
   - **publisher**  
     Par exemple : `Canonical`  
     Segment du nom de l’**éditeur** de l’image de machine virtuelle que les utilisateurs utilisent lors du déploiement de l’image. N’incluez aucun espace ou autre caractère spécial dans ce champ.  
   - **offer**  
     Par exemple : `UbuntuServer`  
     Segment du nom de l’**offre** de l’image de machine virtuelle que les utilisateurs utilisent lors du déploiement de l’image. N’incluez aucun espace ou autre caractère spécial dans ce champ.  
   - **sku**  
     Par exemple : `14.04.3-LTS`  
     Segment de nom de la **référence SKU** de l’image de machine virtuelle dont les utilisateurs se servent au moment du déploiement de l’image. N’incluez aucun espace ou autre caractère spécial dans ce champ.  
   - **version**  
     Par exemple : `1.0.0`  
     Version de l’image de machine virtuelle que les utilisateurs indiquent lors du déploiement de l’image. La version suit le format **\#.\#.\#** , N’incluez aucun espace ou autre caractère spécial dans ce champ.  

     Pour plus d'informations sur le cmdlet **Remove-AzsPlatformImage**, consultez la [documentation sur les modules Opérateur Azure Stack Hub](/powershell/azure/azure-stack/overview) de Microsoft PowerShell.

---

## <a name="next-steps"></a>Étapes suivantes

- [Créer et publier un élément personnalisé de Place de marché Azure Stack Hub](azure-stack-create-and-publish-marketplace-item.md)
- [Approvisionner une machine virtuelle](../user/azure-stack-create-vm-template.md)