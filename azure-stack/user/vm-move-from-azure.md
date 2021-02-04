---
title: Déplacer une machine virtuelle d’Azure vers Azure Stack Hub
description: Découvrez comment déplacer une machine virtuelle d’Azure vers Azure Stack Hub.
author: mattbriggs
ms.topic: how-to
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 9/8/2020
ms.openlocfilehash: 89dc2d9dc55dcfe74565ad6c998880bd1c991126
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99245743"
---
# <a name="move-a-vm-from-azure-to-azure-stack-hub"></a>Déplacer une machine virtuelle d’Azure vers Azure Stack Hub

Vous pouvez charger un disque dur virtuel (VHD) à partir d’une machine virtuelle créée dans Azure vers votre instance Azure Stack Hub.

## <a name="prepare-and-download-your-vhd-from-azure"></a>Préparer et télécharger votre disque dur virtuel à partir d’Azure

Recherchez la section propre à vos besoins lors de la préparation de votre disque dur virtuel.

#### <a name="windows---specialized"></a>[Windows - Spécialisé](#tab/win-spec)

- Suivez les étapes décrites dans l’article [Créer une machine virtuelle Windows à partir d’un disque spécialisé à l’aide de PowerShell](/azure/virtual-machines/windows/create-vm-specialized#prepare-the-vm) pour préparer le disque dur virtuel.
- Pour déployer des extensions de machine virtuelle, assurez-vous que l’agent de machine virtuelle .msi est disponible.  
  Pour plus d’informations et découvrir les étapes à suivre, consultez l’article [Vue d’ensemble de l’agent de machine virtuelle Azure](/azure/virtual-machines/extensions/agent-windows). Assurez-vous que l’extension est installée sur la machine virtuelle avant de déplacer cette dernière. Si l’agent de machine virtuelle n’est pas présent dans le VHD, le déploiement de l’extension se solde par un échec. Vous n’avez pas besoin de définir le profil du système d’exploitation au moment du provisionnement, ni de définir `$vm.OSProfile.AllowExtensionOperations = $true`.

#### <a name="windows---generalized"></a>[Windows - Généralisé](#tab/win-gen)

::: moniker range="<=azs-1910"
- Suivez les instructions de la rubrique [Télécharger un disque dur virtuel Windows à partir d’Azure](/azure/virtual-machines/windows/download-vhd) pour généraliser et télécharger correctement le disque dur virtuel avant de le déplacer vers Azure Stack Hub.
- Lorsque vous provisionnez la machine virtuelle sur Azure, utilisez PowerShell. Préparez-la sans l’indicateur `-ProvisionVMAgent`.
- Supprimez toutes les extensions de machine virtuelle avec l’applet de commande avant de généraliser la machine virtuelle dans Azure. Vous pouvez rechercher les extensions de machine virtuelle installées en accédant à `Windows (C:) > WindowsAzure > Logs > Plugins`.

Utilisez le module PowerShell Az :

```powershell  
Remove-AzVMExtension -ResourceGroupName winvmrg1 -VMName windowsvm -Name "CustomScriptExtension"
```

Utilisez le module PowerShell AzureRM :

```powershell  
Remove-AzureRmVMExtension -ResourceGroupName winvmrg1 -VMName windowsvm -Name "CustomScriptExtension"
```
::: moniker-end
::: moniker range=">=azs-2002"

Suivez les instructions de la rubrique [Télécharger un disque dur virtuel Windows à partir d’Azure](/azure/virtual-machines/windows/download-vhd) pour généraliser et télécharger correctement le disque dur virtuel avant de le déplacer vers Azure Stack Hub.
::: moniker-end

#### <a name="linux---specialized"></a>[Linux - Spécialisé](#tab/lin-spec)

- Avant de télécharger votre machine virtuelle Linux, suivez les instructions de la section « Préparer la machine virtuelle » de l’article [Créer une machine virtuelle Linux à partir d’un disque personnalisé avec Azure CLI](/azure/virtual-machines/linux/upload-vhd#prepare-the-vm)
- Suivez les étapes décrites dans l’article [Télécharger un disque dur virtuel Linux à partir d’Azure](/azure//virtual-machines/windows/download-vhd) pour préparer et télécharger le disque dur virtuel.
- Pour un disque dur virtuel spécialisé, veillez à utiliser la sémantique « attach » à l’aide de `-CreateOption Attach`. Un exemple est disponible dans l’article [Créer une machine virtuelle en utilisant un disque de système d’exploitation managé existant avec PowerShell (Windows)](/azure/virtual-machines/scripts/virtual-machines-powershell-sample-create-vm-from-managed-os-disks).

#### <a name="linux---generalized"></a>[Linux - Généralisé](#tab/lin-gen)

1. Arrêtez le service **waagent** :

   ```bash
   sudo waagent -force -deprovision
   export HISTSIZE=0
   logout
   ```

   Les versions de l’agent Linux Azure compatibles avec Azure Stack Hub [sont répertoriées ici](../operator/azure-stack-linux.md#azure-linux-agent). Assurez-vous que l’image sur laquelle vous avez exécuté Sysprep dispose d’une version de l’agent Linux Azure compatible avec Azure Stack Hub.

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

   1. Vous pouvez maintenant déplacer ce disque dur virtuel vers Azure Stack Hub.

> [!IMPORTANT]  
> Vous trouverez un script dans l’article [Exemple de script pour charger un disque dur virtuel spécialisé sur Azure et créer une machine virtuelle](/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script) afin de charger le VHD sur un compte de stockage utilisateur Azure Stack Hub et créer une machine virtuelle. Veillez à fournir `$urlOfUploadedImageVhd` en tant que compte de stockage Azure Stack Hub + l’URL du conteneur. Pour un VHD généralisé, veillez à utiliser la valeur `FromImage` quand vous définissez `-CreateOption FromImage`.

---

## <a name="verify-your-vhd"></a>Vérifier votre disque dur virtuel

[!INCLUDE [Verify VHD](../includes/user-compute-verify-vhd.md)]

## <a name="upload-to-a-storage-account"></a>Charger vers un compte de stockage

[!INCLUDE [Upload to a storage account](../includes/user-compute-upload-vhd.md)]

## <a name="create-the-vm"></a>Création de la machine virtuelle

Les images personnalisées se présentent sous deux formes : **spécialisées** et **généralisées**.

### <a name="specialized"></a>[Spécialisée](#tab/create-vm-spec)

[!INCLUDE [Create the disk in Azure Stack Hub](../includes/user-compute-create-disk.md)]

### <a name="generalized"></a>[Généralisée](#tab/create-vm-gen)

[!INCLUDE [Create the image in Azure Stack Hub](../includes/user-compute-create-image.md)]
---
## <a name="next-steps"></a>Étapes suivantes

[Vue d’ensemble de la migration d’une machine virtuelle vers Azure Stack Hub](vm-move-overview.md)
