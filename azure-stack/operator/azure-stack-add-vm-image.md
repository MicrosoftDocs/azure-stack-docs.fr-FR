---
title: Ajouter une image de machine virtuelle personnalisée à Azure Stack | Microsoft Docs
description: Découvrez comment ajouter ou supprimer une image de machine virtuelle personnalisée dans Azure Stack.
services: azure-stack
documentationcenter: ''
author: Justinha
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: Justinha
ms.reviewer: kivenkat
ms.lastreviewed: 06/08/2018
ms.openlocfilehash: 91fdd5c0068638f3e597f72ce5aee50fe04b324c
ms.sourcegitcommit: b5eb024d170f12e51cc852aa2c72eabf26792d8d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72534043"
---
# <a name="add-a-custom-vm-to-azure-stack"></a>Ajouter une machine virtuelle personnalisée à Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Dans Azure Stack, vous pouvez ajouter votre image de machine virtuelle personnalisée à la Place de marché et la rendre disponible pour vos utilisateurs. Les images s’ajoutent à l’aide de modèles Azure Resource Manager pour Azure Stack. Vous pouvez aussi ajouter des images de machine virtuelle à l’interface utilisateur de la Place de marché Azure comme élément de Place de marché en utilisant le portail d’administration ou Windows PowerShell. Utilisez une image de la Place de marché Azure globale ou ajoutez votre propre image de machine virtuelle personnalisée.

## <a name="generalize-the-vm-image"></a>Généraliser l’image de machine virtuelle

### <a name="windows"></a>Windows

Créez un disque dur virtuel généralisé personnalisé. Si le disque dur virtuel est en dehors d’Azure, suivez les étapes décrites dans [Charger un disque dur virtuel généralisé et l’utiliser pour créer des machines virtuelles dans Azure](/azure/virtual-machines/windows/upload-generalized-managed) pour appliquer correctement **Sysprep** sur votre disque dur virtuel et le rendre généralisé.

Si le disque dur virtuel provient d’Azure, suivez les instructions de [Généraliser la machine virtuelle source en utilisant Sysprep](/azure/virtual-machines/windows/upload-generalized-managed#generalize-the-source-vm-by-using-sysprep) avant de la porter sur Azure Stack.

### <a name="linux"></a>Linux

Si le disque dur virtuel provient d’en dehors d’Azure, suivez les instructions appropriées pour généraliser le disque dur virtuel :

- [Distributions CentOS](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Red Hat Enterprise Linux](/azure/azure-stack/azure-stack-redhat-create-upload-vhd)
- [SLES ou openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Serveur Ubuntu](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Si le disque dur virtuel provient d’Azure, suivez ces instructions pour généraliser le disque dur virtuel :

1. Arrêtez le service **waagent** :

   ```bash
   # sudo waagent -force -deprovision
   # export HISTSIZE=0
   # logout
   ```

2. Arrêtez la machine virtuelle et téléchargez le disque dur virtuel. Si vous transférez votre disque dur virtuel depuis Azure, vous pouvez le faire avec l’exportation de disque, comme indiqué dans [Télécharger un VHD Windows à partir d’Azure](/azure/virtual-machines/windows/download-vhd).

Les versions de l’agent Linux Azure qui fonctionnent avec Azure Stack sont [répertoriées ici](azure-stack-linux.md#azure-linux-agent). Vérifiez que l’image préparée avec Sysprep possède une version de l’agent Linux Azure compatible avec Azure Stack.

### <a name="common-steps-for-both-windows-and-linux"></a>Étapes communes pour Windows et Linux

Avant de charger l’image, il est important de tenir compte des éléments suivants :

- Azure Stack prend en charge la génération d’une seule machine virtuelle dans le format VHD de disque fixe. Le format fixe structure linéairement le disque logique dans le fichier, de façon que l’offset du disque *X* soit stocké à l’offset de l’objet blob *X*. Un petit pied de page à la fin de l’objet blob décrit les propriétés du disque dur virtuel. Pour vérifier si votre disque est fixe, utilisez l’applet de commande PowerShell **Get-VHD**.

- Azure Stack ne prend pas en charge les disques durs virtuels de disque dynamique. Le redimensionnement d’un disque dynamique attaché à une machine virtuelle laisse la machine virtuelle dans un état d’échec. Pour résoudre ce problème, supprimez la machine virtuelle sans supprimer son disque (un objet blob VHD dans un compte de stockage). Ensuite, convertissez le disque dur virtuel d’un disque dynamique en disque fixe et recréez la machine virtuelle.

## <a name="add-a-vm-image-as-an-azure-stack-operator-using-the-portal"></a>Ajouter une image de machine virtuelle en tant qu’opérateur Azure Stack en utilisant le portail

1. Les images doivent pouvoir être référencées par un URI de stockage d’objets blob. Préparez une image de système d’exploitation Windows ou Linux au format VHD (pas VHDX), puis chargez cette image dans un compte de stockage Azure ou Azure Stack.

   - Si le disque dur virtuel est dans Azure, vous pouvez utiliser un outil comme [Azcopy ](/azure/storage/common/storage-use-azcopy) pour transférer directement le disque dur virtuel entre un compte de stockage Azure et votre compte de stockage Azure Stack si vous l’exécutez sur un cloud Azure Stack connecté.

   - Sur un cloud Azure Stack déconnecté, si votre disque dur virtuel est dans Azure, vous devez télécharger le disque dur virtuel sur une machine qui a une connectivité à la fois à Azure et à Azure Stack. Ensuite, vous copiez le disque dur virtuel sur cette machine à partir d’Azure avant de transférer le disque dur virtuel vers Azure Stack en utilisant un des [outils de transfert de données de stockage](../user/azure-stack-storage-transfer.md) courants qui peuvent être utilisés dans Azure et dans Azure Stack.

2. Vous pouvez suivre [cet exemple](/powershell/module/azurerm.compute/add-azurermvhd?view=azurermps-6.13.0) pour charger un disque dur virtuel vers un compte de stockage dans le portail d’administration Azure Stack.

   - Il est préférable de charger une image dans le stockage d’objets blob Azure Stack plutôt que dans Stockage Blob Azure, car l’envoi (push) d’une image dans le référentiel d’images Azure Stack est plus rapide.

   - Quand vous chargez l’[image de machine virtuelle Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/), pensez à remplacer l’étape **Se connecter à Azure** par l’étape [Configurer l’environnement PowerShell de l’opérateur Azure Stack](azure-stack-powershell-configure-admin.md).  

3. Prenez note de l’URI de stockage d’objets blob dans lequel vous chargez l’image. L’URI de stockage d’objets blob est au format suivant : *&lt;compte_stockage&gt;/&lt;conteneur_objets_blob&gt;/&lt;nom_disque_dur_virtuel_cible&gt;* .vhd.

4. Pour rendre le stockage Blob accessible de manière anonyme, accédez au conteneur d’objets blob du compte de stockage dans lequel l’image de machine virtuelle VHD a été chargée. Sélectionnez **Blob**, puis choisissez **Stratégie d’accès**. Si vous le souhaitez, vous pouvez générer une signature d’accès partagé pour le conteneur et l’inclure dans l’URI de l’objet blob. Cette étape vérifie que l’objet blob est utilisable. Si l’objet blob n’est pas accessible de manière anonyme, l’image de machine virtuelle est créée dans un état d’échec.

   ![Accéder aux objets blob du compte de stockage](./media/azure-stack-add-vm-image/image1.png)

   ![Définir un accès public pour les objets blob](./media/azure-stack-add-vm-image/image2.png)

5. Connectez-vous au Azure Stack en tant qu’opérateur. Dans le menu, sélectionnez **Tous les services** > **Images** sous **Calcul** > **Ajouter**.

6. Sous **Créer une image**, entrez le nom, l’abonnement, le groupe de ressources, l’emplacement, le disque du système d’exploitation, le type du système d’exploitation, l’URI de l’objet blob de stockage, le type de compte et la mise en cache de l’hôte. Ensuite, sélectionnez **Créer** pour démarrer la création de l’image de machine virtuelle.

   ![Démarrer la création de l’image](./media/azure-stack-add-vm-image/image4.png)

   Une fois que l’image de machine virtuelle a été créée, son état passe à **Réussi**.

7. Quand vous ajoutez une image, celle-ci est disponible seulement pour des modèles Azure Resource Manager et des déploiements PowerShell. Pour rendre une image disponible pour vos utilisateurs comme élément de Place de marché, publiez cet élément en suivant les étapes de l’article [Créer et publier un élément de Place de marché](azure-stack-create-and-publish-marketplace-item.md). Notez bien les valeurs pour **Éditeur**, **Offre**, **Référence SKU** et **Version**. Vous en avez besoin quand vous modifiez le modèle Resource Manager et le fichier Manifest.json dans votre fichier .azpkg personnalisé.

## <a name="remove-the-vm-image-as-an-azure-stack-operator-using-the-portal"></a>Supprimer l’image de machine virtuelle en tant qu’opérateur Azure Stack en utilisant le portail

1. Ouvrez le [portail d’administration Azure Stack](https://adminportal.local.azurestack.external).

2. Si l’image de machine virtuelle a un élément de Place de marché associé, sélectionnez **Gestion de la Place de marché**, puis sélectionnez l’élément de Place de marché de machines virtuelles que vous voulez supprimer.

3. Si l’image de machine virtuelle n’a d’élément de Place de marché associé, accédez à **Tous les services > Calcul > Images de machine virtuelle**, puis sélectionnez les points de suspension ( **...** ) en regard de l’image de machine virtuelle.

4. Sélectionnez **Supprimer**.

## <a name="add-a-vm-image-as-an-azure-stack-operator-using-powershell"></a>Ajouter une image de machine virtuelle en tant qu’opérateur Azure Stack en utilisant PowerShell

1. [Installez PowerShell pour Azure Stack](azure-stack-powershell-install.md).  

2. Connectez-vous à Azure Stack comme opérateur. Pour obtenir des instructions, consultez [Se connecter à Azure Stack comme opérateur](azure-stack-powershell-configure-admin.md).

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
     Par exemple : `Canonical`  
     Segment du nom de l’**éditeur** de l’image de machine virtuelle que les utilisateurs utilisent lors du déploiement de l’image. N’incluez aucun espace ou autre caractère spécial dans ce champ.  
   - **offer**  
     Par exemple : `UbuntuServer`  
     Segment du nom de l’**offre** de l’image de machine virtuelle que les utilisateurs utilisent lors du déploiement de l’image. N’incluez aucun espace ou autre caractère spécial dans ce champ.  
   - **sku**  
     Par exemple : `14.04.3-LTS`  
     Segment du nom de la **référence SKU** de l’image de machine virtuelle que les utilisateurs utilisent lors du déploiement de l’image. N’incluez aucun espace ou autre caractère spécial dans ce champ.  
   - **version**  
     Par exemple : `1.0.0`  
     Version de l’image de machine virtuelle que les utilisateurs indiquent lors du déploiement de l’image. La version suit le format *\#.\#.\#* , N’incluez aucun espace ou autre caractère spécial dans ce champ.  
   - **osType**  
     Par exemple : `Linux`  
     La valeur de **osType** de l’image, qui doit être **Windows** ou **Linux**.  
   - **OSUri**  
     Par exemple : `https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
     Vous pouvez spécifier un URI de stockage d’objets blob comme `osDisk`.  

     Pour plus d’informations, consultez les informations de référence sur PowerShell pour les applets de commande [Add-AzsPlatformimage](/powershell/module/azs.compute.admin/add-azsplatformimage) et [New-DataDiskObject](/powershell/module/Azs.Compute.Admin/New-DataDiskObject).

## <a name="remove-a-vm-image-as-an-azure-stack-operator-using-powershell"></a>Supprimer une image de machine virtuelle en tant qu’opérateur Azure Stack en utilisant PowerShell

Si vous n’avez plus besoin de l’image de machine virtuelle que vous avez chargée, vous pouvez la supprimer sur la Place de marché à l’aide de l’applet de commande suivante :

1. [Installez PowerShell pour Azure Stack](azure-stack-powershell-install.md).

2. Connectez-vous à Azure Stack comme opérateur.

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
     Par exemple : `Canonical`  
     Segment du nom de l’**éditeur** de l’image de machine virtuelle que les utilisateurs utilisent lors du déploiement de l’image. N’incluez aucun espace ou autre caractère spécial dans ce champ.  
   - **offer**  
     Par exemple : `UbuntuServer`  
     Segment du nom de l’**offre** de l’image de machine virtuelle que les utilisateurs utilisent lors du déploiement de l’image. N’incluez aucun espace ou autre caractère spécial dans ce champ.  
   - **sku**  
     Par exemple : `14.04.3-LTS`  
     Segment du nom de la **référence SKU** de l’image de machine virtuelle que les utilisateurs utilisent lors du déploiement de l’image. N’incluez aucun espace ou autre caractère spécial dans ce champ.  
   - **version**  
     Par exemple : `1.0.0`  
     Version de l’image de machine virtuelle que les utilisateurs indiquent lors du déploiement de l’image. La version suit le format *\#.\#.\#* , N’incluez aucun espace ou autre caractère spécial dans ce champ.  

     Pour plus d’informations sur l’applet de commande **Remove-AzsPlatformImage**, consultez la [documentation sur les modules Opérateur Azure Stack](/powershell/module/) de Microsoft PowerShell.

## <a name="next-steps"></a>Étapes suivantes

- [Créer et publier un élément personnalisé de Place de marché Azure Stack](azure-stack-create-and-publish-marketplace-item.md)
- [Approvisionner une machine virtuelle](../user/azure-stack-create-vm-template.md)
