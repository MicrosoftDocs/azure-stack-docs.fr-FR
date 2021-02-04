---
title: Déplacer une machine virtuelle spécialisée d’un emplacement local vers Azure Stack Hub
description: Découvrez comment déplacer une machine virtuelle spécialisée d’un emplacement local vers Azure Stack Hub.
author: mattbriggs
ms.topic: how-to
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 9/8/2020
ms.openlocfilehash: b5d46af0ebb5a1ae059ff7f778c8177e0ea4a04d
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99247844"
---
# <a name="move-a-specialized-vm-from-on-premises-to-azure-stack-hub"></a>Déplacer une machine virtuelle spécialisée d’un emplacement local vers Azure Stack Hub

Vous pouvez ajouter une image de machine virtuelle à partir de votre environnement local. Vous pouvez créer votre image en tant que disque dur virtuel (VHD) et la charger vers un compte de stockage dans votre instance Azure Stack Hub. Vous pouvez ensuite créer une machine virtuelle à partir du VHD.

Une image de disque spécialisée est une copie d’un VHD (disque dur virtuel) d’une machine virtuelle existante qui contient les comptes d’utilisateur, applications et autres données d’état de votre machine virtuelle d’origine. Il s’agit généralement du format dans lequel les machines virtuelles sont migrées vers Azure Stack Hub. Les VHD spécialisés conviennent parfaitement quand vous devez migrer des machines virtuelles locales vers Azure Stack Hub.

## <a name="how-to-move-an-image"></a>Procédure : déplacer une image

Recherchez la section propre à vos besoins lors de la préparation de votre disque dur virtuel.

#### <a name="windows-vm"></a>[Machine virtuelle Windows](#tab/port-win)

- Suivez les étapes décrites dans [Préparer un disque dur virtuel Windows ou un VHDX à charger sur Azure](/azure/virtual-machines/windows/prepare-for-upload-vhd-image) pour préparer correctement le disque dur virtuel. Vous devez utiliser un disque dur virtuel pour Azure Stack Hub.
   > [!NOTE]  
   > **Ne généralisez pas** la machine virtuelle à l’aide de Sysprep.
- Supprimez tous les outils et agents de virtualisation invités qui sont installés sur la machine virtuelle (notamment les outils VMware).
- Vérifiez que la machine virtuelle est configurée pour obtenir l’adresse IP et les paramètres DNS à partir de DHCP. De cette façon, le serveur obtient une adresse IP dans le réseau virtuel au démarrage.
- Vérifiez que le protocole RDP/SSH est activé et que le pare-feu autorise la communication.
- Pour déployer des extensions de machine virtuelle, assurez-vous que l’agent de machine virtuelle `.msi` est disponible. Pour obtenir des conseils, consultez [Vue d’ensemble de l’agent de machine virtuelle Azure](/azure/virtual-machines/extensions/agent-windows). Si l’agent de machine virtuelle n’est pas présent dans le VHD, le déploiement de l’extension se solde par un échec. Vous n’avez pas besoin de définir le profil du système d’exploitation au moment du provisionnement, ni de définir `$vm.OSProfile.AllowExtensionOperations = $true`.

#### <a name="linux-vm"></a>[Machine virtuelle Linux](#tab/port-linux)

#### <a name="generalize-the-vhd"></a>Généraliser le disque dur virtuel

Suivez les instructions appropriées pour préparer le disque dur virtuel pour votre système d’exploitation Linux :

- [Distributions CentOS](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Red Hat Enterprise Linux](../operator/azure-stack-redhat-create-upload-vhd.md)
- [SLES ou openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Serveur Ubuntu](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

> [!IMPORTANT]
> N’exécutez pas la dernière étape (`sudo waagent -force -deprovision`), car elle permet de généraliser le VHD.

#### <a name="identify-the-version-of-the-linux-agent"></a>Identifier la version de l’agent Linux

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

#### <a name="linux-agent-224-and-earlier-disable-the-linux-agent-provisioning"></a>Agent Linux 2.2.4 et versions antérieures, désactivation du provisionnement de l’agent Linux 

Pour désactiver le provisionnement de l’agent Linux avec un agent Linux antérieur à la version 2.2.4, définissez les paramètres suivants dans **/etc/waagent.conf** : `Provisioning.Enabled=n, and Provisioning.UseCloudInit=n`.

#### <a name="linux-agent-2245-and-later-disable-the-linux-agent-provisioning"></a>Agent Linux 2.2.45 et versions ultérieures, désactivation du provisionnement de l’agent Linux

Pour désactiver le provisionnement avec l’agent Linux 2.2.45 et les versions ultérieures, apportez les changements suivants à l’option de configuration :

`Provisioning.Enabled` et `Provisioning.UseCloudInit` sont désormais ignorés.

Dans cette version, il n’existe aucune option `Provisioning.Agent` pour désactiver complètement le provisionnement. Toutefois, vous pouvez ajouter le fichier de marqueur de provisionnement. Ainsi, avec les paramètres suivants, le provisionnement est ignoré :

1. Dans **/etc/waagent.conf** ajoutez cette option de configuration : `Provisioning.Agent=Auto`.
2. Pour vérifier que le provisionnement de walinuxagent est désactivé, exécutez `mkdir -p /var/lib/waagent && touch /var/lib/waagent/provisioned`.
3. Désactivez l’installation de cloud-init en exécutant ce qui suit :

   ```bash  
   touch /etc/cloud/cloud-init.disabled
   sudo sed -i '/azure_resource/d' /etc/fstab
   ```

4. Déconnectez-vous.

#### <a name="run-an-extension"></a>Exécuter une extension

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

---

## <a name="verify-your-vhd"></a>Vérifier votre disque dur virtuel

[!INCLUDE [Verify VHD](../includes/user-compute-verify-vhd.md)]

## <a name="upload-to-a-storage-account"></a>Charger vers un compte de stockage

[!INCLUDE [Upload to a storage account](../includes/user-compute-upload-vhd.md)]

## <a name="create-the-disk-in-azure-stack-hub"></a>Créer le disque dans Azure Stack Hub

[!INCLUDE [Create the disk in Azure Stack Hub](../includes/user-compute-create-disk.md)]

## <a name="next-steps"></a>Étapes suivantes

[Vue d’ensemble de la migration d’une machine virtuelle vers Azure Stack Hub](vm-move-overview.md)
