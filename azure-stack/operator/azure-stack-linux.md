---
title: Ajouter des images Linux à Azure Stack
description: Découvrez comment ajouter des images Linux à Azure Stack.
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
ms.date: 05/21/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/16/2018
ms.openlocfilehash: 8e9617974984afac55c2b1542065ea0455da0acc
ms.sourcegitcommit: 5a720b17bd6a5aab44929c0247db8d512e0669ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67197144"
---
# <a name="add-linux-images-to-azure-stack"></a>Ajouter des images Linux à Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Vous pouvez déployer des machines virtuelles Linux sur Azure Stack en ajoutant une image Linux dans la Place de marché Azure Stack. Le moyen le plus simple d’ajouter une image Linux à Azure Stack est à travers la gestion de la Place de marché. Ces images ont été préparées et testées pour leur compatibilité avec Azure Stack.

## <a name="marketplace-management"></a>Gestion de la Place de marché

Pour télécharger des images Linux à partir de la Place de Marché Azure, utilisez l’article [Télécharger des éléments de la Place de Marché à partir d’Azure dans Azure Stack](azure-stack-download-azure-marketplace-item.md). Sélectionnez les images Linux que vous souhaitez proposer aux utilisateurs sur votre système Azure Stack.

Il existe des mises à jour fréquentes pour ces images ; aussi reportez-vous régulièrement à la gestion de la Place de marché pour les maintenir à jour.

## <a name="prepare-your-own-image"></a>Préparer votre propre image

Dans la mesure du possible, téléchargez les images disponibles via la gestion de la Place de marché. Ces images ont été préparées et testées pour Azure Stack.

### <a name="azure-linux-agent"></a>Agent Linux Azure

L’Agent Azure Linux (généralement appelé `WALinuxAgent` ou `walinuxagent`) est requis, et toutes les versions de l’agent ne fonctionnent pas sur Azure Stack. Les versions entre 2.2.20 et 2.2.35 ne sont pas prises en charge sur Azure Stack. Pour utiliser les dernières versions de l’agent qui sont postérieures à 2.2.35, appliquez le correctif logiciel 1901/1902, ou mettez à jour votre environnement Azure Stack avec la version 1903 (ou supérieure). Notez que [cloud-init](https://cloud-init.io/) n’est pas pris en charge sur Azure Stack pour l’instant.

| Build Azure Stack | Build Agent Linux Azure |
| ------------- | ------------- |
| 1.1901.0.99 ou versions antérieures | 2.2.20 |
| 1.1902.0.69  | 2.2.20  |
|  1.1901.3.105   | 2.2.35 ou versions plus récentes |
| 1.1902.2.73  | 2.2.35 ou versions plus récentes |
| 1.1903.0.35  | 2.2.35 ou versions plus récentes |
| Versions après la build 1903 | 2.2.35 ou versions plus récentes |
| Non pris en charge | 2.2.21-2.2.34 |

Vous pouvez préparer votre propre image Linux en appliquant les instructions suivantes :

* [Distributions CentOS](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
* [SLES et openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Serveur Ubuntu](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="add-your-image-to-the-marketplace"></a>Ajouter l’image à la Place de marché

Suivez [Ajoutez l’image à la Place de Marché](azure-stack-add-vm-image.md). Vérifiez que le paramètre `OSType` a la valeur `Linux`.

Une fois que vous avez ajouté l’image à la Place de marché, un élément de Place de marché est créé et les utilisateurs peuvent déployer une machine virtuelle Linux.

## <a name="next-steps"></a>Étapes suivantes

* [Télécharger des éléments de la Place de marché à partir d’Azure dans Azure Stack](azure-stack-download-azure-marketplace-item.md)
* [Vue d’ensemble de la Place de Marché Azure Stack](azure-stack-marketplace.md)
