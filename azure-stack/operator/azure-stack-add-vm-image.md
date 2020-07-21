---
title: Ajouter une image de machine virtuelle personnalisée à Azure Stack Hub
description: Apprenez à ajouter ou supprimer une image de machine virtuelle personnalisée dans Azure Stack Hub.
author: sethmanheim
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 07/10/2020
ms.openlocfilehash: ebcb85ba88d90d43a1364b465c4f66e6851d09cb
ms.sourcegitcommit: 2be3dd5419b0d003a9598a42541ebb1d251aea3d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2020
ms.locfileid: "86390770"
---
# <a name="add-a-custom-vm-image-to-azure-stack-hub"></a>Ajouter une image de machine virtuelle personnalisée à Azure Stack Hub

Dans Azure Stack Hub, en tant qu’opérateur, vous pouvez ajouter votre image de machine virtuelle personnalisée à la Place de marché pour la mettre à la disposition de vos utilisateurs. Vous pouvez ajouter des images de machine virtuelle à la Place de marché Azure Stack Hub via le portail administrateur ou Windows PowerShell. Utilisez une image de la Place de marché Azure globale en tant que base de votre image personnalisée, ou créez la vôtre à l’aide d’Hyper-V.

Un utilisateur du portail des locataires dans Azure Stack Hub peut également ajouter une image de machine virtuelle personnalisée en suivant l’aide fournie à l’étape 1. Un utilisateur peut créer son image personnalisée en tant que VHD (disque dur virtuel) et charger l’image vers un compte de stockage sur Azure Stack Hub. Il peut ensuite créer une machine virtuelle à partir du VHD.

Les images personnalisées se présentent sous deux formes : **généralisées** et **spécialisées**.

- **Image généralisée**

  Une image de disque généralisée est une image qui a été préparée avec **Sysprep** pour supprimer toutes les informations uniques (par exemple les comptes d’utilisateur), ce qui lui permet d’être réutilisée pour créer plusieurs machines virtuelles. Il s’agit d’une option intéressante pour les éléments de la Place de marché.

- **Image spécialisée**

  Une image de disque spécialisée est une copie d’un VHD (disque dur virtuel) d’une machine virtuelle existante qui contient les comptes d’utilisateur, applications et autres données d’état de votre machine virtuelle d’origine. Il s’agit généralement du format dans lequel les machines virtuelles sont migrées vers Azure Stack Hub.

## <a name="step-1-create-the-custom-vm-image"></a>Étape 1 : Créer l’image de machine virtuelle personnalisée

### <a name="windows---create-a-custom-generalized-vhd"></a>Windows - Créer un VHD généralisé personnalisé

#### <a name="vhd-is-from-outside-azure"></a>Le VHD ne provient pas d’Azure

Suivez les étapes décrites dans [Charger un disque dur virtuel généralisé et l’utiliser pour créer des machines virtuelles dans Azure](/azure/virtual-machines/windows/upload-generalized-managed) afin d’appliquer correctement **Sysprep** à votre VHD et le rendre généralisé.

#### <a name="vhd-is-from-azure"></a>Le VHD provient d’Azure

Avant de généraliser la machine virtuelle, veillez à effectuer les opérations suivantes :

Avant la version 1910 d’Azure Stack :

- Lorsque vous provisionnez la machine virtuelle sur Azure, utilisez PowerShell et provisionnez-la sans l’indicateur `-ProvisionVMAgent`.
- Supprimez toutes les extensions de machine virtuelle avec l’applet de commande **Remove-AzureRmVMExtension** de la machine virtuelle avant de généraliser la machine virtuelle dans Azure. Vous pouvez rechercher les extensions de machine virtuelle installées en accédant à `Windows (C:) > WindowsAzure > Logs > Plugins`.

```powershell
Remove-AzureRmVMExtension -ResourceGroupName winvmrg1 -VMName windowsvm -Name "CustomScriptExtension"
```

Avec ou après la version 1910 d’Azure Stack :

- Les étapes précédentes ne s’appliquent pas aux VHD déplacés d’Azure vers une instance d’Azure Stack Hub version 1910 ou ultérieure.

Suivez les instructions de [cet article](/azure/virtual-machines/windows/download-vhd) pour généraliser et télécharger correctement le disque dur virtuel avant de le porter sur Azure Stack Hub.

### <a name="windows---specialized"></a>Windows - Spécialisé

Suivez les étapes décrites [ici](/azure/virtual-machines/windows/create-vm-specialized#prepare-the-vm) pour préparer correctement le VHD.
Pour déployer des extensions de machine virtuelle, vérifiez que le fichier .msi de l’agent de machine virtuelle disponible [dans cet article](/azure/virtual-machines/extensions/agent-windows#manual-installation) est installé sur la machine virtuelle avant le déploiement de celle-ci. Si l’agent de machine virtuelle n’est pas présent dans le VHD, le déploiement de l’extension se solde par un échec. Vous n’avez pas besoin de définir le profil du système d’exploitation au moment du provisionnement, ni de définir `$vm.OSProfile.AllowExtensionOperations = $true`.

### <a name="linux---generalized"></a>Linux - Généralisé

#### <a name="vhd-from-outside-azure"></a>Le VHD ne provient pas d’Azure

Si le disque dur virtuel provient d’en dehors d’Azure, suivez les instructions appropriées pour généraliser le disque dur virtuel :

- [Distributions CentOS](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
- [SLES ou openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Serveur Ubuntu](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

#### <a name="vhd-from-azure"></a>Le VHD provient d’Azure

Si le VHD provient d’Azure, suivez les instructions ci-après pour le généraliser et le télécharger :

1. Arrêtez le service **waagent** :

   ```bash
   sudo waagent -force -deprovision
   export HISTSIZE=0
   logout
   ```

   Les versions de l’agent Linux Azure compatibles avec Azure Stack Hub [sont répertoriées ici](azure-stack-linux.md#azure-linux-agent). Vérifiez que l'image préparée avec Sysprep possède une version de l'agent Linux Azure compatible avec Azure Stack Hub.

2. Arrêtez/libérez la machine virtuelle.

3. Téléchargez le VHD.

   1. Pour télécharger le fichier VHD, générez une URL de signature d’accès partagé (SAS). Un délai d’expiration est affecté à l’URL lors de sa génération.

   1. Dans le menu du panneau relatif à la machine virtuelle, sélectionnez **Disques**.

   1. Sélectionnez le disque de système d’exploitation de la machine virtuelle, puis **Exportation de disque**.

   1. Affectez au délai d’expiration de l’URL la valeur 36 000.

   1. Sélectionnez **Générer une URL**.

   1. Générez l’URL.

   1. Sous l’URL générée, sélectionnez **Télécharger le fichier de disque dur virtuel**.

   1. Vous devrez peut-être sélectionner **Enregistrer** dans le navigateur pour démarrer le téléchargement. Le nom par défaut du fichier VHD est **abcd**.

   1. Vous pouvez maintenant déplacer ce VHD vers Azure Stack Hub.

> [!IMPORTANT]  
> Vous trouverez un script dans l’article [Exemple de script pour charger un disque dur virtuel spécialisé sur Azure et créer une machine virtuelle](/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script) afin de charger le VHD sur un compte de stockage utilisateur Azure Stack Hub et créer une machine virtuelle. Veillez à fournir `$urlOfUploadedImageVhd` en tant que compte de stockage Azure Stack Hub + l’URL du conteneur. Pour un VHD généralisé, veillez à utiliser la valeur `FromImage` quand vous définissez `-CreateOption FromImage`.

### <a name="linux---specialized"></a>Linux - Spécialisé

Les VHD spécialisés ne doivent pas être utilisés en tant que VHD de base pour un élément de la Place de marché. À la place, utilisez des VHD généralisés. Toutefois, les VHD spécialisés conviennent parfaitement quand vous devez migrer des machines virtuelles locales vers Azure Stack Hub

#### <a name="vhd-from-outside-azure"></a>Le VHD ne provient pas d’Azure

Étape 1 : Suivez les instructions appropriées pour rendre le VHD adapté à Azure. Utilisez cet article jusqu’à l’étape d’installation de l’agent Linux, puis passez à l’étape 2 avant d’installer l’agent :

- [Distributions CentOS](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
- [SLES ou openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Serveur Ubuntu](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

> [!IMPORTANT]
> N’exécutez pas la dernière étape (`sudo waagent -force -deprovision`), car elle permet de généraliser le VHD.

Étape 2 : Si un VHD spécialisé Linux est déplacé de l’extérieur d’Azure vers Azure Stack Hub, effectuez les actions suivantes pour exécuter les extensions de machine virtuelle et désactiver le provisionnement :

Pour identifier la version d’agent Linux installée dans l’image de machine virtuelle source, exécutez les commandes suivantes. Le numéro de version qui décrit le code de provisionnement est `WALinuxAgent-`, et non `Goal state agent` :

```bash
waagent -version
```

Par exemple :

```bash
waagent -version
WALinuxAgent-2.2.45 running on centos 7.7.1908
Python: 2.7.5
Goal state agent: 2.2.46
```

Pour désactiver le provisionnement de l’agent Linux avec un agent Linux antérieur à la version 2.2.4, définissez les paramètres suivants dans **/etc/waagent.conf** : `Provisioning.Enabled=n, and Provisioning.UseCloudInit=n`.

Dans les scénarios où vous souhaitez exécuter des extensions :

1. Définissez le paramètre suivant dans **/etc/waagent.conf** :

   - `Provisioning.Enabled=n`
   - `Provisioning.UseCloudInit=n`

2. Pour vérifier que le provisionnement de walinuxagent est désactivé, exécutez `mkdir -p /var/lib/waagent && touch /var/lib/waagent/provisioned`

3. Si vous avez cloud-init dans votre image, désactivez cloud-init :

   ```bash
   touch /etc/cloud/cloud-init.disabled
   sudo sed -i '/azure_resource/d' /etc/fstab
   ```

4. Exécutez une déconnexion.

Pour désactiver le provisionnement avec l’agent Linux 2.2.45 et les versions ultérieures, apportez les changements suivants à l’option de configuration :

- `Provisioning.Enabled` et `Provisioning.UseCloudInit` sont désormais ignorés.

Dans cette version, il n’existe aucune option `Provisioning.Agent` pour désactiver complètement le provisionnement. Toutefois, vous pouvez ajouter le fichier de marqueur de provisionnement. Ainsi, avec les paramètres suivants, le provisionnement est ignoré :

1. Dans **/etc/waagent.conf** ajoutez cette option de configuration : `Provisioning.Agent=Auto`.
2. Pour vérifier que le provisionnement de walinuxagent est désactivé, exécutez `mkdir -p /var/lib/waagent && touch /var/lib/waagent/provisioned`.
3. Désactivez l’installation de cloud-init en exécutant ce qui suit :

   ```bash
   touch /etc/cloud/cloud-init.disabled
   sudo sed -i '/azure_resource/d' /etc/fstab
   ```

4. Déconnectez-vous.

#### <a name="vhd-from-azure"></a>Le VHD provient d’Azure

Vous pouvez utiliser cette [aide](/azure/virtual-machines/linux/upload-vhd#requirements) pour préparer le VHD.

> [!IMPORTANT]  
> Vous pouvez utiliser l’exemple PowerShell suivant pour charger le VHD sur un compte de stockage utilisateur Azure Stack Hub :

```powershell  
# Provide values for the variables
$resourceGroup = 'myResourceGroup'
$location = 'Orlando'
$storageaccount = 'mystorageaccount'
$storageType = 'Standard_LRS'
$storageaccounturl = 'https://resourcegrpabc.blob.orlando.azurestack.corp.microsoft.com/container'
$localPath = 'C:\Users\Public\Documents\Hyper-V\VHDs\generalized.vhd'
$vhdName = 'myUploadedVhd.vhd'

New-AzResourceGroup -Name $resourceGroup -Location $location
New-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storageAccount -Location $location `
  -SkuName $storageType -Kind "Storage"
$urlOfUploadedImageVhd = ($storageaccounturl + '/' + $vhdName)
Add-AzVhd -ResourceGroupName $resourceGroup -Destination $urlOfUploadedImageVhd `
    -LocalFilePath $localPath
```

Pour un VHD spécialisé, veillez à utiliser la sémantique « attach » à l’aide de `-CreateOption Attach`, [comme dans cet exemple](/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks), pour créer une machine virtuelle à partir de ce VHD.

### <a name="considerations"></a>Considérations

Avant de charger l’image, il est important de tenir compte des éléments suivants :

- Azure Stack Hub prend uniquement en charge les machines virtuelles de 1ère génération au format VHD de disque dur fixe. Le format fixe structure linéairement le disque logique dans le fichier, de façon que l’offset du disque **X** soit stocké à l’offset de l’objet blob **X**. Un petit pied de page à la fin de l’objet blob décrit les propriétés du disque dur virtuel. Pour vérifier si votre disque est fixe, utilisez l’applet de commande PowerShell **Get-VHD**.

- Azure Stack Hub ne prend pas en charge les disques durs virtuels dynamiques.

## <a name="step-2-upload-a-storage-account"></a>Étape 2 : Charger un compte de stockage

Chargez l’image de machine virtuelle sur un compte de stockage en tant qu’opérateur Azure Stack Hub.

1. [Installez PowerShell pour Azure Stack Hub](azure-stack-powershell-install.md).  

2. Connectez-vous à Azure Stack Hub en tant qu'opérateur. Pour obtenir des instructions, consultez [Se connecter à Azure Stack Hub en tant qu'opérateur](azure-stack-powershell-configure-admin.md).

3. Les images doivent être référencées par un URI de stockage d’objets blob. Préparez une image de système d'exploitation Windows ou Linux au format VHD (pas VHDX), puis chargez cette image sur un compte de stockage d'Azure Stack Hub.

   - Si le VHD se trouve dans Azure, vous pouvez utiliser un outil tel qu’[Azcopy](/azure/storage/common/storage-use-azcopy) pour le transférer directement entre un compte de stockage Azure et votre compte de stockage Azure Stack Hub, si vous l’exécutez sur une instance connectée d’Azure Stack Hub.

   - Sur une instance déconnectée d’Azure Stack Hub, si votre disque dur virtuel se trouve dans Azure, vous devez le télécharger sur une machine connectée à Azure et à Azure Stack Hub. Copiez ensuite le VHD sur cette machine à partir d’Azure avant de le transférer vers Azure Stack Hub à l’aide d’un des [outils de transfert de données de stockage](../user/azure-stack-storage-transfer.md) courants qui peuvent être utilisés sur Azure et Azure Stack Hub.

     L’outil utilisé dans cet exemple est la commande **Add-AzureRmVHD**, qui permet de charger un VHD sur un compte de stockage dans le portail Administrateur Azure Stack Hub :

     ```powershell
     Add-AzureRmVhd -Destination "https://bash.blob.redmond.azurestack.com/sample/vhdtestingmgd.vhd" -LocalFilePath "C:\vhd\vhdtestingmgd.vhd"
     ```

4. Prenez note de l’URI de stockage d’objets blob dans lequel vous chargez l’image. L’URI du Stockage Blob est au format suivant : **&lt;compte_stockage&gt;/&lt;conteneur_objets_blob&gt;/&lt;nom_VHD_cible&gt;*.vhd**.

5. Pour rendre le stockage Blob accessible de manière anonyme, accédez au conteneur d’objets blob du compte de stockage dans lequel l’image de machine virtuelle VHD a été chargée. Sélectionnez **Blob**, puis choisissez **Stratégie d’accès**. Si vous le souhaitez, vous pouvez générer une signature SAS (signature d’accès partagé) pour le conteneur, et l’inclure dans l’URI de l’objet blob. Cette étape permet de garantir que l’objet blob est utilisable. Si l’objet blob n’est pas accessible de manière anonyme, l’image de machine virtuelle est créée dans un état d’échec.

   ![Accéder aux objets blob du compte de stockage](./media/azure-stack-add-vm-image/tca1.png)

   ![Définir un accès public pour les objets blob](./media/azure-stack-add-vm-image/tca2.png)

   ![Définir un accès public pour les objets blob](./media/azure-stack-add-vm-image/tca3.png)

6. Vous pouvez également utiliser les étapes précédentes pour charger l’image vers un compte de stockage sur le portail utilisateur (en vous connectant en tant qu’utilisateur) et créer une machine virtuelle directement à partir de celle-ci. Dans ce cas, il s’agit d’un VHD personnalisé qui n’est pas disponible sur la Place de marché. Vous n’avez pas non plus besoin de suivre l’étape 3.

## <a name="step-3-option-1-add-using-the-portal"></a>Étape 3, option 1 : Ajouter à l’aide du portail

Ajoutez l’image de machine virtuelle en tant qu’opérateur Azure Stack Hub à l’aide du portail.

1. Connectez-vous à Azure Stack Hub en tant qu'opérateur. Dans le menu, sélectionnez **Tous les services** > **Calcul** sous **Images de machine virtuelle** > **Ajouter**.

   ![IU de chargement indépendant d’image personnalisée](./media/azure-stack-add-vm-image/tca4.png)

2. Sous **Créer une image**, entrez l’**éditeur**, l’**offre**, la **référence SKU**, la **version** et l’URI d’objet blob de disque de système d’exploitation. Ensuite, sélectionnez **Créer** pour démarrer la création de l’image de machine virtuelle.

   ![IU de chargement indépendant d’image personnalisée](./media/azure-stack-add-vm-image/tca5.png)

   Une fois que l’image de machine virtuelle a été créée, son état passe à **Réussi**.

3. Quand vous ajoutez une image, celle-ci est disponible seulement pour des modèles Azure Resource Manager et des déploiements PowerShell. Pour rendre une image disponible pour vos utilisateurs comme élément de Place de marché, publiez cet élément en suivant les étapes de l’article [Créer et publier un élément de Place de marché](azure-stack-create-and-publish-marketplace-item.md). Notez bien les valeurs pour **Éditeur**, **Offre**, **Référence SKU** et **Version**. Vous en aurez besoin pour modifier le modèle Resource Manager et le fichier Manifest.json dans votre fichier .azpkg personnalisé.

## <a name="step-3-option-2-add-using-powershell"></a>Étape 3, option 2 : Ajouter à l’aide de PowerShell

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

## <a name="remove-using-the-portal"></a>Supprimer à l’aide du portail

Pour supprimer l’image de machine virtuelle en tant qu’opérateur Azure Stack Hub à l’aide du portail Azure Stack Hub, effectuez les étapes suivantes :

1. Ouvrez le [portail administrateur](https://adminportal.local.azurestack.external) Azure Stack Hub.

2. Si l’image de machine virtuelle a un élément de Place de marché associé, sélectionnez **Gestion de la Place de marché**, puis sélectionnez l’élément de Place de marché de machines virtuelles que vous voulez supprimer.

3. Si l’image de machine virtuelle n’a d’élément de Place de marché associé, accédez à **Tous les services > Calcul > Images de machine virtuelle**, puis sélectionnez les points de suspension ( **...** ) en regard de l’image de machine virtuelle.

4. Sélectionnez **Supprimer**.

## <a name="remove-using-powershell"></a>Supprimer à l’aide de PowerShell

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

## <a name="next-steps"></a>Étapes suivantes

- [Créer et publier un élément personnalisé de Place de marché Azure Stack Hub](azure-stack-create-and-publish-marketplace-item.md)
- [Approvisionner une machine virtuelle](../user/azure-stack-create-vm-template.md)
