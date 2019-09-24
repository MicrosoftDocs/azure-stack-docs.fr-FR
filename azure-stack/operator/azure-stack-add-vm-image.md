---
title: Ajouter une image de machine virtuelle à Azure Stack | Microsoft Docs
description: Découvrez comment ajouter ou supprimer une image de machine virtuelle dans Azure Stack.
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
ms.date: 09/17/2019
ms.author: Justinha
ms.reviewer: kivenkat
ms.lastreviewed: 06/08/2018
ms.openlocfilehash: fef815ec23655638bbe4df1bcdccae42aeee13e2
ms.sourcegitcommit: 9f4c6e96f60b4c229316e7a4ab6e0e5ef0a9a232
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71061160"
---
# <a name="add-a-vm-image-to-azure-stack"></a>Ajouter une image de machine virtuelle à Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Dans Azure Stack, vous pouvez ajouter une image de machine virtuelle à la Place de marché afin de la proposer à vos utilisateurs. Les images s’ajoutent à l’aide de modèles Azure Resource Manager pour Azure Stack. Vous pouvez également ajouter des images de machine virtuelle à l’interface utilisateur de la Place de marché Azure comme élément de la Place de marché en utilisant le portail administrateur ou Windows PowerShell. Utilisez une image de la Place de marché Azure globale ou ajoutez une image de machine virtuelle personnalisée.

## <a name="add-a-vm-image-through-the-portal"></a>Ajouter une image de machine virtuelle par le biais du portail

> [!NOTE]  
> Avec cette méthode, vous devez créer l’élément de la Place de marché séparément.

Les images doivent pouvoir être référencées par un URI de stockage d’objets blob. Préparez une image de système d’exploitation Windows ou Linux au format VHD (pas VHDX), puis chargez cette image dans un compte de stockage Azure ou Azure Stack. Si votre image est déjà chargée dans le stockage d’objets blob dans Azure ou Azure Stack, ignorez l’étape 1.

1. [Chargez une image de machine virtuelle Windows dans Azure pour les déploiements Resource Manager](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/) ou, pour une image Linux, suivez les instructions fournies dans [Déployer des machines virtuelles Linux dans Azure Stack](azure-stack-linux.md). Avant de télécharger l’image, il est important de tenir compte des facteurs suivants :

   - Azure Stack prend seulement en charge les machines virtuelles de génération un (1) dans le format VHD de disque dur fixe. Le format fixe structure linéairement le disque logique dans le fichier de façon que l’offset du disque X soit stocké à l’offset de l’objet blob X. Un petit pied de page à la fin de l’objet blob décrit les propriétés du disque dur virtuel. Pour vérifier si votre disque est fixe, utilisez la commande PowerShell [Get-VHD](https://docs.microsoft.com/powershell/module/hyper-v/get-vhd?view=win10-ps).  

     > [!IMPORTANT]  
     >  Azure Stack ne prend pas en charge les disques durs virtuels de disque dynamique. Le redimensionnement d’un disque dynamique attaché à une machine virtuelle laisse la machine virtuelle dans un état d’échec. Pour résoudre ce problème, supprimez la machine virtuelle sans supprimer son disque (un objet blob VHD dans un compte de stockage). Ensuite, convertissez le disque dur virtuel d’un disque dynamique en disque fixe et recréez la machine virtuelle.

   - Il est préférable de charger une image dans le stockage d’objets blob Azure Stack plutôt que dans le stockage d’objets blob Azure, car l’envoi (push) d’une image dans le référentiel d’images Azure Stack est plus rapide.

   - Quand vous chargez l’[image de machine virtuelle Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/), pensez à remplacer l’étape **Se connecter à Azure** par l’étape [Configurer l’environnement PowerShell de l’opérateur Azure Stack](azure-stack-powershell-configure-admin.md).  

   - Prenez note de l’URI de stockage d’objets blob dans lequel vous chargez l’image. L’URI de stockage d’objets blob est au format suivant : *&lt;compte_stockage&gt;/&lt;conteneur_objets_blob&gt;/&lt;nom_disque_dur_virtuel_cible&gt;* .vhd.

   - Pour rendre le stockage Blob accessible de manière anonyme, accédez au conteneur d’objets blob du compte de stockage dans lequel l’image de machine virtuelle VHD a été chargée. Sélectionnez **Blob**, puis choisissez **Stratégie d’accès**. Si vous le souhaitez, vous pouvez générer une signature d’accès partagé pour le conteneur et l’inclure dans l’URI de l’objet blob. Cette étape vérifie que l’objet blob est utilisable. Si l’objet blob n’est pas accessible de manière anonyme, l’image de machine virtuelle est créée dans un état d’échec.

     ![Accéder aux objets blob du compte de stockage](./media/azure-stack-add-vm-image/image1.png)

     ![Définir un accès public pour les objets blob](./media/azure-stack-add-vm-image/image2.png)

2. Connectez-vous au Azure Stack en tant qu’opérateur. Dans le menu, sélectionnez **Tous les services** > **Images** sous **Calcul** > **Ajouter**.

3. Sous **Créer une image**, entrez le nom, l’abonnement, le groupe de ressources, l’emplacement, le disque du système d’exploitation, le type du système d’exploitation, l’URI de l’objet blob de stockage, le type de compte et la mise en cache de l’hôte. Ensuite, sélectionnez **Créer** pour démarrer la création de l’image de machine virtuelle.

   ![Démarrer la création de l’image](./media/azure-stack-add-vm-image/image4.png)

   Une fois que l’image de machine virtuelle a été créée, son état passe à **Réussi**.

4. Pour rendre l’image de machine virtuelle plus rapidement disponible dans l’interface utilisateur, il est préférable de [créer un élément de Place de marché](azure-stack-create-and-publish-marketplace-item.md).

## <a name="remove-a-vm-image-through-the-portal"></a>Supprimer une image de machine virtuelle par le biais du portail

1. Ouvrez le portail administrateur sur [https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external).

2. Sélectionnez **Gestion de la Place de marché**, puis sélectionnez la machine virtuelle à supprimer.

3. Cliquez sur **Supprimer**.

## <a name="add-a-vm-image-to-the-marketplace-by-using-powershell"></a>Ajouter une image de machine virtuelle à la Place de marché à l’aide de PowerShell

> [!Note]  
> Quand vous ajoutez une image, celle-ci est disponible uniquement pour les déploiements PowerShell et basés sur des modèles Azure Resource Manager. Pour mettre une image à la disposition des utilisateurs en tant qu’élément de la Place de marché, publiez cet élément en effectuant les étapes décrites dans cet article : [Créer et publier un élément de Place de Marché](azure-stack-create-and-publish-marketplace-item.md)

1. [Installez PowerShell pour Azure Stack](azure-stack-powershell-install.md).  

2. Connectez-vous à Azure Stack comme opérateur. Pour obtenir des instructions, consultez [Se connecter à Azure Stack comme opérateur](azure-stack-powershell-configure-admin.md).

3. Ouvrez PowerShell avec une invite de commandes avec élévation de privilèges et exécutez :

   ```powershell
    Add-AzsPlatformimage -publisher "<publisher>" `
      -offer "<offer>" `
      -sku "<sku>" `
      -version "<#.#.#>" `
      -OSType "<ostype>" `
      -OSUri "<osuri>"
   ```

   L’applet de commande **Add-AzsPlatformimage** spécifie les valeurs utilisées par les modèles Azure Resource Manager pour référencer l’image de machine virtuelle. Les valeurs incluent :
   - **publisher**  
     Par exemple : `Canonical`  
     Segment du nom de l’éditeur de l’image de machine virtuelle que les utilisateurs indiquent lors du déploiement de l’image. **Microsoft** est un exemple. N’incluez aucun espace ou autre caractère spécial dans ce champ.  
   - **offer**  
     Par exemple : `UbuntuServer`  
     Segment du nom de l’offre de l’image de machine virtuelle que les utilisateurs indiquent lors du déploiement de l’image. **WindowsServer** est un exemple. N’incluez aucun espace ou autre caractère spécial dans ce champ.  
   - **sku**  
     Par exemple : `14.04.3-LTS`  
     Segment du nom de référence SKU de l’image de machine virtuelle que les utilisateurs indiquent lors du déploiement de l’image. **Datacenter2016** est un exemple. N’incluez aucun espace ou autre caractère spécial dans ce champ.  
   - **version**  
     Par exemple : `1.0.0`  
     Version de l’image de machine virtuelle que les utilisateurs indiquent lors du déploiement de l’image. La version suit le format *\#.\#.\#* , **1.0.0** est un exemple. N’incluez aucun espace ou autre caractère spécial dans ce champ.  
   - **osType**  
     Par exemple : `Linux`  
     Type d’exploitation de l’image, qui doit être **Windows** ou **Linux**.  
   - **OSUri**  
     Par exemple : `https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
     Vous pouvez spécifier un URI de stockage d’objets blob comme `osDisk`.  

     Pour plus d’informations, consultez les informations de référence de PowerShell sur les applets de commande [Add-AzsPlatformimage](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage) et [New-DataDiskObject](https://docs.microsoft.com/powershell/module/Azs.Compute.Admin/New-DataDiskObject).

## <a name="add-a-custom-vm-image-to-the-marketplace-by-using-powershell"></a>Ajouter une image de machine virtuelle personnalisée à la Place de marché à l’aide de PowerShell
 
1. [Installez PowerShell pour Azure Stack](azure-stack-powershell-install.md).

   ```powershell
    # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

    $TenantID = Get-AzsDirectoryTenantId `
      -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
      -EnvironmentName AzureStackAdmin

    Add-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID
   ```

2. Si vous utilisez **Active Directory Federation Services (AD FS)** , utilisez l’applet de commande suivante :

   ```powershell
   # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
   $ArmEndpoint = "<Resource Manager endpoint for your environment>"

   # For Azure Stack Development Kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
   $GraphAudience = "<GraphAudience endpoint for your environment>"

   # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external
    ```

3. Connectez-vous à Azure Stack comme opérateur. Pour obtenir des instructions, consultez [Se connecter à Azure Stack comme opérateur](azure-stack-powershell-configure-admin.md).

4. Créez un compte de stockage dans Azure Stack ou Azure au niveau global pour stocker votre image de machine virtuelle personnalisée. Pour obtenir des instructions, consultez [Démarrage rapide : Charger, télécharger et lister des objets blob à l’aide du portail Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

5. Préparez une image de système d’exploitation Windows ou Linux au format VHD (et non VHDX), chargez l’image sur votre compte de stockage et obtenez l’URI où l’image de machine virtuelle peut être récupérée par PowerShell.  

   ```powershell
   Add-AzureRmAccount 
   -EnvironmentName "AzureStackAdmin" 
   -TenantId $TenantID
   ```
  
   >[!Note]
   > Si votre session expire, si votre mot de passe a changé ou si vous souhaitez simplement changer de compte, exécutez l’applet de commande suivante avant de vous connecter en utilisant Add-AzureRmAccount : `Remove-AzureRmAccount-Scope Process`

6. (Facultatif) Vous pouvez charger un tableau de disques de données dans le cadre de l’image de machine virtuelle. Créez vos disques de données à l’aide de l’applet de commande New-DataDiskObject. Ouvrez PowerShell à partir d’une invite de commandes avec élévation de privilèges et exécutez :

   ```powershell
    New-DataDiskObject -Lun 2 `
    -Uri "https://storageaccount.blob.core.windows.net/vhds/Datadisk.vhd"
   ```

7. Ouvrez PowerShell avec une invite de commandes avec élévation de privilèges et exécutez :

   ```powershell
    Add-AzsPlatformimage -publisher "<publisher>" -offer "<offer>" -sku "<sku>" -version "<#.#.#>" -OSType "<ostype>" -OSUri "<osuri>"
   ```

    Pour plus d’informations sur les applets de commande Add-AzsPlatformimage et New-DataDiskObject, consultez la [documentation sur les modules d’opérateur Azure Stack](https://docs.microsoft.com/powershell/module/) de Microsoft PowerShell.

## <a name="remove-a-vm-image-by-using-powershell"></a>Supprimer une image de machine virtuelle à l’aide de PowerShell

Si vous n’avez plus besoin de l’image de machine virtuelle que vous avez chargée, vous pouvez la supprimer sur la Place de marché à l’aide de l’applet de commande suivante :

1. [Installez PowerShell pour Azure Stack](azure-stack-powershell-install.md).

2. Connectez-vous à Azure Stack comme opérateur.

3. Ouvrez PowerShell avec une invite de commandes avec élévation de privilèges et exécutez :

   ```powershell  
   Remove-AzsPlatformImage `
    -publisher "<publisher>" `
    -offer "<offer>" `
    -sku "<sku>" `
    -version "<version>" `
   ```
   L’applet de commande **Remove-AzsPlatformImage** spécifie les valeurs utilisées par les modèles Azure Resource Manager pour référencer l’image de machine virtuelle. Les valeurs incluent :
   - **publisher**  
     Par exemple : `Canonical`  
     Segment du nom de l’éditeur de l’image de machine virtuelle que les utilisateurs indiquent lors du déploiement de l’image. **Microsoft** est un exemple. N’incluez aucun espace ou autre caractère spécial dans ce champ.  
   - **offer**  
     Par exemple : `UbuntuServer`  
     Segment du nom de l’offre de l’image de machine virtuelle que les utilisateurs indiquent lors du déploiement de l’image. **WindowsServer** est un exemple. N’incluez aucun espace ou autre caractère spécial dans ce champ.  
   - **sku**  
     Par exemple : `14.04.3-LTS`  
     Segment du nom de référence SKU de l’image de machine virtuelle que les utilisateurs indiquent lors du déploiement de l’image. **Datacenter2016** est un exemple. N’incluez aucun espace ou autre caractère spécial dans ce champ.  
   - **version**  
     Par exemple : `1.0.0`  
     Version de l’image de machine virtuelle que les utilisateurs indiquent lors du déploiement de l’image. La version suit le format *\#.\#.\#* , **1.0.0** est un exemple. N’incluez aucun espace ou autre caractère spécial dans ce champ.  
    
     Pour plus d’informations sur l’applet de commande Remove-AzsPlatformImage, consultez la [documentation sur les modules d’opérateur Azure Stack](https://docs.microsoft.com/powershell/module/) de Microsoft PowerShell.

## <a name="next-steps"></a>Étapes suivantes

[Approvisionner une machine virtuelle](../user/azure-stack-create-vm-template.md)
