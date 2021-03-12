---
title: Proposer une machine virtuelle Red Hat pour Azure Stack Hub
titleSuffix: Azure Stack Hub
description: Apprenez à créer et à télécharger un disque dur virtuel (VHD) Azure contenant un système d'exploitation Red Hat Linux.
author: sethmanheim
ms.topic: article
ms.date: 2/18/2021
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 2/18/2021
ms.openlocfilehash: 4dc1280b0120c5c64dff6a273ce13ae97dbe980c
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "101840301"
---
# <a name="offer-a-red-hat-based-virtual-machine-for-azure-stack-hub"></a>Proposer une machine virtuelle Red Hat pour Azure Stack Hub

Cet article explique comment préparer une machine virtuelle Red Hat Enterprise Linux pour l’utiliser dans Azure Stack Hub. 

## <a name="offer-a-red-hat-based-vm"></a>Proposer une machine virtuelle Red Hat

Il existe deux façons de proposer une machine virtuelle Red Hat dans Azure Stack Hub :

- Vous pouvez proposer la machine via la Place de marché Azure Stack Hub.
    - Les images Red Hat Enterprise Linux sont une offre privée sur Azure Stack Hub. Pour mettre cette offre à disposition sous votre onglet **Gestion de la Place de marché**, vous devez [réaliser une enquête](https://forms.office.com/pages/responsepage.aspx?id=v4j5cvGGr0GRqy180BHbR_e32WQju3tMrgXNcUR94AVUNkJTWjdQRjc3TzFLREdGU0dIVFRUQ1JCSi4u).
    - Pour plus d’informations, consultez [Vue d’ensemble de la Place de marché Azure Stack Hub](azure-stack-marketplace.md).
- Vous pouvez ajouter votre propre image personnalisée à votre Azure Stack Hub, puis la proposer sur la Place de marché. 
    1. Vous devez avoir accès au cloud Red Hat.
    2. Pour savoir comment préparer l’image pour Azure et Azure Stack Hub, consultez [Préparer une machine virtuelle Red Hat pour Azure](/azure/virtual-machines/linux/redhat-create-upload-vhd).
    3. Pour savoir comment proposer votre image personnalisée sur la Place de marché Azure Stack Hub, consultez [Créer et publier un élément sur la Place de marché](azure-stack-create-and-publish-marketplace-item.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les hyperviseurs certifiés pour l’exécution de Red Hat Enterprise Linux, consultez le [site web de Red Hat](https://access.redhat.com/certified-hypervisors).