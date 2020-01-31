---
title: Ajouter une image de machine virtuelle personnalisée à Azure Stack Hub
description: Apprenez à ajouter ou supprimer une image de machine virtuelle personnalisée dans Azure Stack Hub.
author: sethmanheim
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 06/08/2018
ms.openlocfilehash: 359adfbe9083bd21368934426a54c887af2f9f2a
ms.sourcegitcommit: 959513ec9cbf9d41e757d6ab706939415bd10c38
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2020
ms.locfileid: "76889964"
---
# <a name="add-a-custom-vm-image-to-azure-stack-hub"></a>Ajouter une image de machine virtuelle personnalisée à Azure Stack Hub

Azure Stack Hub vous permet d'ajouter votre image de machine virtuelle personnalisée à la Place de marché pour la mettre à la disposition de vos utilisateurs. Vous pouvez ajouter des images de machine virtuelle à la Place de marché Azure Stack Hub via le portail administrateur ou Windows PowerShell. Utilisez une image de la Place de marché Azure globale en tant que base de votre image personnalisée, ou créez la vôtre à l’aide d’Hyper-V.

## <a name="step-1-create-the-custom-vm-image"></a>Étape 1 : Créer l’image de machine virtuelle personnalisée

### <a name="windows"></a>Windows

Créez un disque dur virtuel généralisé personnalisé. 

**Si le disque dur virtuel est extérieur à Azure**, suivez les étapes décrites dans [Charger un disque dur virtuel généralisé et l’utiliser pour créer des machines virtuelles dans Azure](/azure/virtual-machines/windows/upload-generalized-managed) pour appliquer correctement **Sysprep** sur votre disque dur virtuel et le rendre généralisé.

**Si le disque dur virtuel vient d’Azure**, avant de généraliser la machine virtuelle, veillez à effectuer les opérations suivantes :
1) Lorsque vous provisionnez la machine virtuelle sur Azure, utilisez PowerShell et provisionnez-la sans l’indicateur `-ProvisionVMAgent`. 
2) Supprimez toutes les extensions de machine virtuelle avec l’applet de commande **Remove-AzureRmVMExtension** de la machine virtuelle avant de généraliser la machine virtuelle dans Azure. Vous pouvez rechercher les extensions de machine virtuelle installées en accédant à Windows (C :) > WindowsAzure > Journaux > Plug-ins.

```Powershell
Remove-AzureRmVMExtension -ResourceGroupName winvmrg1 -VMName windowsvm -Name "CustomScriptExtension"
```                       
Publiez les éléments ci-dessus, suivez les instructions de [ce document](/azure/virtual-machines/windows/download-vhd) pour généraliser et télécharger correctement le disque dur virtuel avant de le porter sur Azure Stack Hub.

### <a name="linux"></a>Linux

**Si le disque dur virtuel est extérieur à Azure**, suivez les instructions appropriées pour généraliser le disque dur virtuel :

- [Distributions CentOS](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Red Hat Enterprise Linux](/azure/azure-stack/azure-stack-redhat-create-upload-vhd)
- [SLES ou openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Serveur Ubuntu](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

**Si le disque virtuel vient d’Azure**, suivez les instructions ci-après pour le généraliser et le télécharger :

1. Arrêtez le service **waagent** :

   ```bash
   sudo waagent -force -deprovision
   export HISTSIZE=0
   logout
   ```

   Les versions de l'agent Linux Azure compatibles avec Azure Stack Hub sont [répertoriées ici](azure-stack-linux.md#azure-linux-agent). Vérifiez que l'image préparée avec Sysprep possède une version de l'agent Linux Azure compatible avec Azure Stack Hub.

2. Arrêtez/libérez la machine virtuelle.

3. Téléchargez le VHD.

   1. Pour télécharger le fichier VHD, vous devez générer une URL de signature d’accès partagé (SAS). Un délai d’expiration est affecté à l’URL lors de sa génération.

   1. Dans le menu du panneau relatif à la machine virtuelle, sélectionnez **Disques**.

   1. Sélectionnez le disque de système d’exploitation de la machine virtuelle, puis **Exportation de disque**.

   1. Affectez au délai d’expiration de l’URL la valeur 36 000.

   1. Sélectionnez **Générer une URL**.

   1. Générez l’URL.
   
   1. Sous l’URL générée, sélectionnez **Télécharger le fichier de disque dur virtuel**.

   1. Vous devrez peut-être sélectionner **Enregistrer** dans le navigateur pour démarrer le téléchargement. Le nom par défaut du fichier VHD est _abcd_.

### <a name="considerations"></a>Considérations

Avant de charger l’image, il est important de tenir compte des éléments suivants :

- Azure Stack Hub prend uniquement en charge la génération une (1) des machines virtuelles au format VHD de disque dur fixe. Le format fixe structure linéairement le disque logique dans le fichier, de façon que l’offset du disque *X* soit stocké à l’offset de l’objet blob *X*. Un petit pied de page à la fin de l’objet blob décrit les propriétés du disque dur virtuel. Pour vérifier si votre disque est fixe, utilisez l’applet de commande PowerShell **Get-VHD**.

- Azure Stack Hub ne prend pas en charge les disques durs virtuels dynamiques. 

## <a name="step-2-upload-the-vm-image-to-a-storage-account"></a>Étape 2 : Charger l’image de machine virtuelle sur un compte de stockage

1. [Installez PowerShell pour Azure Stack Hub](azure-stack-powershell-install.md).  

2. Connectez-vous à Azure Stack Hub en tant qu'opérateur. Pour obtenir des instructions, consultez [Se connecter à Azure Stack Hub en tant qu'opérateur](azure-stack-powershell-configure-admin.md).

3. Les images doivent pouvoir être référencées par un URI de stockage d’objets blob. Préparez une image de système d'exploitation Windows ou Linux au format VHD (pas VHDX), puis chargez cette image sur un compte de stockage d'Azure Stack Hub.

   - Si le disque dur virtuel se trouve dans Azure, vous pouvez utiliser un outil comme [Azcopy ](/azure/storage/common/storage-use-azcopy) pour le transférer directement entre un compte de stockage Azure et votre compte de stockage Azure Stack Hub si vous l'exécutez sur une instance connectée d'Azure Stack Hub.

   - Sur une instance déconnectée d'Azure Stack Hub, si votre disque dur virtuel se trouve dans Azure, vous devez le télécharger sur une machine connectée à Azure et à Azure Stack Hub. Ensuite, vous copiez le disque dur virtuel sur cette machine à partir d'Azure avant de le transférer vers Azure Stack Hub à l'aide d'un des [outils de transfert de données de stockage](../user/azure-stack-storage-transfer.md) courants qui peuvent être utilisés sur Azure et Azure Stack Hub.

     L’outil utilisé dans cet exemple est la commande Add-AzureRmVHD, qui permet de charger un VHD sur un compte de stockage dans le portail Administrateur Azure Stack Hub.  

     ```powershell
     Add-AzureRmVhd -Destination "https://bash.blob.redmond.azurestack.com/sample/vhdtestingmgd.vhd" -LocalFilePath "C:\vhd\vhdtestingmgd.vhd" 
     ```

3. Prenez note de l’URI de stockage d’objets blob dans lequel vous chargez l’image. L’URI de stockage d’objets blob est au format suivant : *&lt;compte_stockage&gt;/&lt;conteneur_objets_blob&gt;/&lt;nom_disque_dur_virtuel_cible&gt;* .vhd.

4. Pour rendre le stockage Blob accessible de manière anonyme, accédez au conteneur d’objets blob du compte de stockage dans lequel l’image de machine virtuelle VHD a été chargée. Sélectionnez **Blob**, puis choisissez **Stratégie d’accès**. Si vous le souhaitez, vous pouvez générer une signature d’accès partagé pour le conteneur et l’inclure dans l’URI de l’objet blob. Cette étape vérifie que l’objet blob est utilisable. Si l’objet blob n’est pas accessible de manière anonyme, l’image de machine virtuelle est créée dans un état d’échec.

   ![Accéder aux objets blob du compte de stockage](./media/azure-stack-add-vm-image/tca1.png)

   ![Définir un accès public pour les objets blob](./media/azure-stack-add-vm-image/tca2.png)

   ![Définir un accès public pour les objets blob](./media/azure-stack-add-vm-image/tca3.png)
   

## <a name="step-3-option-1-add-the-vm-image-as-an-azure-stack-hub-operator-using-the-portal"></a>Étape 3, option 1 : Ajouter l'image de machine virtuelle en tant qu'opérateur Azure Stack Hub à l'aide du portail

1. Connectez-vous à Azure Stack Hub en tant qu'opérateur. Dans le menu, sélectionnez **Tous les services** > **Calcul** sous **Images de machine virtuelle** > **Ajouter**.

   ![IU de chargement indépendant d’image personnalisée](./media/azure-stack-add-vm-image/tca4.png)

2. Sous **Créer une image**, entrez l’éditeur, l’offre, le SKU, la version et l’URI d’objet blob de disque de système d’exploitation. Ensuite, sélectionnez **Créer** pour démarrer la création de l’image de machine virtuelle.
   
   ![IU de chargement indépendant d’image personnalisée](./media/azure-stack-add-vm-image/tca5.png)

   Une fois que l’image de machine virtuelle a été créée, son état passe à **Réussi**.
   
3. Quand vous ajoutez une image, celle-ci est disponible seulement pour des modèles Azure Resource Manager et des déploiements PowerShell. Pour rendre une image disponible pour vos utilisateurs comme élément de Place de marché, publiez cet élément en suivant les étapes de l’article [Créer et publier un élément de Place de marché](azure-stack-create-and-publish-marketplace-item.md). Notez bien les valeurs pour **Éditeur**, **Offre**, **Référence SKU** et **Version**. Vous en aurez besoin pour modifier le modèle Resource Manager et le fichier Manifest.json dans votre fichier .azpkg personnalisé.

## <a name="step-3-option-2-add-a-vm-image-as-an-azure-stack-hub-operator-using-powershell"></a>Étape 3, option 2 : Ajouter une image de machine virtuelle en tant qu'opérateur Azure Stack Hub à l'aide de PowerShell

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
     Segment du nom de la **référence SKU** de l’image de machine virtuelle que les utilisateurs utilisent lors du déploiement de l’image. N’incluez aucun espace ou autre caractère spécial dans ce champ.  
   - **version**  
     Par exemple : `1.0.0`  
     Version de l’image de machine virtuelle que les utilisateurs indiquent lors du déploiement de l’image. La version suit le format *\#.\#.\#* , N’incluez aucun espace ou autre caractère spécial dans ce champ.  
   - **osType**  
     Par exemple : `Linux`  
     La valeur de **osType** de l’image, qui doit être **Windows** ou **Linux**.  
   - **OSUri**  
     Par exemple : `https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
     Vous pouvez spécifier un URI de stockage d’objets blob comme `osDisk`.  

     Pour plus de détails, consultez les informations de référence relatives à PowerShell concernant l’applet de commande [Add-AzsPlatformimage](/powershell/module/azs.compute.admin/add-azsplatformimage).
     
4. Quand vous ajoutez une image, celle-ci est disponible seulement pour des modèles Azure Resource Manager et des déploiements PowerShell. Pour rendre une image disponible pour vos utilisateurs comme élément de Place de marché, publiez cet élément en suivant les étapes de l’article [Créer et publier un élément de Place de marché](azure-stack-create-and-publish-marketplace-item.md). Notez bien les valeurs pour **Éditeur**, **Offre**, **Référence SKU** et **Version**. Vous en avez besoin quand vous modifiez le modèle Resource Manager et le fichier Manifest.json dans votre fichier .azpkg personnalisé.

## <a name="remove-the-vm-image-as-an-azure-stack-hub-operator-using-the-portal"></a>Supprimer l'image de machine virtuelle en tant qu'opérateur Azure Stack Hub à l'aide du portail

1. Ouvrez le [portail administrateur](https://adminportal.local.azurestack.external) Azure Stack Hub.

2. Si l’image de machine virtuelle a un élément de Place de marché associé, sélectionnez **Gestion de la Place de marché**, puis sélectionnez l’élément de Place de marché de machines virtuelles que vous voulez supprimer.

3. Si l’image de machine virtuelle n’a d’élément de Place de marché associé, accédez à **Tous les services > Calcul > Images de machine virtuelle**, puis sélectionnez les points de suspension ( **...** ) en regard de l’image de machine virtuelle.

4. Sélectionnez **Supprimer**.

## <a name="remove-a-vm-image-as-an-azure-stack-hub-operator-using-powershell"></a>Supprimer une image de machine virtuelle en tant qu'opérateur Azure Stack Hub à l'aide de PowerShell

Si vous n’avez plus besoin de l’image de machine virtuelle que vous avez chargée, vous pouvez la supprimer sur la Place de marché à l’aide de l’applet de commande suivante :

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
     Segment du nom de la **référence SKU** de l’image de machine virtuelle que les utilisateurs utilisent lors du déploiement de l’image. N’incluez aucun espace ou autre caractère spécial dans ce champ.  
   - **version**  
     Par exemple : `1.0.0`  
     Version de l’image de machine virtuelle que les utilisateurs indiquent lors du déploiement de l’image. La version suit le format *\#.\#.\#* , N’incluez aucun espace ou autre caractère spécial dans ce champ.  

     Pour plus d'informations sur le cmdlet **Remove-AzsPlatformImage**, consultez la [documentation sur les modules Opérateur Azure Stack Hub](/powershell/module/) de Microsoft PowerShell.

## <a name="next-steps"></a>Étapes suivantes

- [Créer et publier un élément personnalisé de Place de marché Azure Stack Hub](azure-stack-create-and-publish-marketplace-item.md)
- [Approvisionner une machine virtuelle](../user/azure-stack-create-vm-template.md)
