---
title: Déplacer une machine virtuelle généralisée d’un emplacement local vers Azure Stack Hub
description: Découvrez comment déplacer une machine virtuelle généralisée d’un emplacement local vers Azure Stack Hub.
author: mattbriggs
ms.topic: how-to
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 9/8/2020
ms.openlocfilehash: 105beaa0805fe0aea1aacfce8bd22f3bd01714b1
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97872872"
---
# <a name="move-a-generalized-vm-from-on-premises-to-azure-stack-hub"></a>Déplacer une machine virtuelle généralisée d’un emplacement local vers Azure Stack Hub

Vous pouvez ajouter une image de machine virtuelle à partir de votre environnement local. Vous pouvez créer votre image en tant que disque dur virtuel (VHD) et la charger vers un compte de stockage dans votre instance Azure Stack Hub. Vous pouvez ensuite créer une machine virtuelle à partir du VHD.

Une image de disque généralisée est une image qui a été préparée avec **Sysprep** pour supprimer toutes les informations uniques (par exemple les comptes d’utilisateur), ce qui lui permet d’être réutilisée pour créer plusieurs machines virtuelles. Les disques durs virtuels généralisés sont un outil idéal lors de la création d’images que l’opérateur cloud Azure Stack Hub envisage d’utiliser en tant qu’éléments de la place de marché.

## <a name="how-to-move-an-image"></a>Procédure : déplacer une image

Recherchez la section propre à vos besoins lors de la préparation de votre disque dur virtuel.

#### <a name="windows-vm"></a>[Machine virtuelle Windows](#tab/port-win)

Suivez les étapes décrites dans [Préparer un disque dur virtuel ou un VHDX Windows à charger sur Azure](/azure/virtual-machines/windows/prepare-for-upload-vhd-image) pour généraliser correctement votre disque dur virtuel avant le chargement. Vous devez utiliser un disque dur virtuel pour Azure Stack Hub.

#### <a name="linux-vm"></a>[Machine virtuelle Linux](#tab/port-linux)

Suivez les instructions appropriées pour généraliser le disque dur virtuel pour votre système d’exploitation Linux :

- [Distributions CentOS](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Red Hat Enterprise Linux](../operator/azure-stack-redhat-create-upload-vhd.md)
- [SLES ou openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Serveur Ubuntu](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

---

## <a name="verify-your-vhd"></a>Vérifier votre disque dur virtuel

[!INCLUDE [Verify VHD](../includes/user-compute-verify-vhd.md)]
## <a name="upload-to-a-storage-account"></a>Charger vers un compte de stockage

[!INCLUDE [Upload to a storage account](../includes/user-compute-upload-vhd.md)]

## <a name="create-the-image-in-azure-stack-hub"></a>Créer l’image dans Azure Stack Hub

[!INCLUDE [Create the image in Azure Stack Hub](../includes/user-compute-create-image.md)]

## <a name="next-steps"></a>Étapes suivantes

[Vue d’ensemble de la migration d’une machine virtuelle vers Azure Stack Hub](vm-move-overview.md)
