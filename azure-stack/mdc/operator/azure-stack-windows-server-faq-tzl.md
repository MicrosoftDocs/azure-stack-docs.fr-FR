---
title: Questions fréquentes (FAQ) sur Windows Server dans Azure Stack - MDC | Microsoft Docs
description: Lisez les questions fréquentes (FAQ) sur la Place de marché Azure Stack pour Windows Server lors de l’exploitation d’un Modular Data Center (MDC).
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2020
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 10/26/2020
ms.openlocfilehash: 64ccefb0a6644d2cd613a39d8beaaf1f82ee9fc0
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910786"
---
# <a name="windows-server-in-azure-stack-marketplace-faq---modular-data-center-mdc"></a>Questions fréquentes (FAQ) relatives à Windows Server dans la Place de marché Azure Stack - Modular Data Center (MDC)

Cet article répond à certaines questions fréquentes sur les images Windows Server dans la [Place de marché Azure Stack Hub](../../operator/azure-stack-marketplace.md).

## <a name="faqs"></a>Foire aux questions

**Quelles sont les options de licence pour les images Place de Marché Windows Server sur Azure Stack ?**

Les utilisateurs d’Azure Stack Hub renforcé et de MDC peuvent utiliser gratuitement Windows Server en tant que système d’exploitation invité.

Les images Windows Server proposées par Microsoft par le biais de la place de marché Azure Stack sont disponibles en deux versions. Une seule version de cette image à la fois peut être utilisée dans un environnement Azure Stack.

- **Paiement à l’utilisation** : Ces images ne doivent pas être utilisées sur Azure Stack Hub renforcé ou MDC.

- **BYOL (apportez votre propre licence)**  : Ces images peuvent être utilisées sur Azure Stack Hub renforcé et MDC.

**Qu’en est-il des autres machines virtuelles qui utilisent Windows Server, par exemple SQL Server ?**

La licence logicielle Windows Server s’applique uniquement au système d’exploitation Windows. Les produits en couche comme SQL, qui vous obligent à apporter votre propre licence, ne sont pas concernés.

**Comment effectuer une mise à jour avec une image Windows plus récente ?**

Déterminez d’abord si des modèles Azure Resource Manager font référence à des versions spécifiques. Si tel est le cas, mettez à jour ces modèles ou conservez les anciennes versions de l’image. Il est préférable d’utiliser **version: latest**.

Ensuite, si des groupes de machines virtuelles identiques font référence à une version spécifique, déterminez s’ils vont être mis à l’échelle plus tard, et décidez si les anciennes versions doivent être conservées ou non. Si aucune de ces conditions ne s’applique, supprimez les anciennes images de la Place de marché avant de télécharger les plus récentes. Utilisez pour cela le panneau Gestion de la Place de marché si la version d’origine a été téléchargée de cette façon. Téléchargez ensuite la version plus récente.

**Que faire si je télécharge la mauvaise version et que j’offre celle-ci à utilisateurs ?**

Commencez par supprimer la version incorrecte de la place de marché. Attendez que l’opération se termine (ce qui est indiqué dans une notification, et non dans le panneau **Gestion de la Place de marché**). Téléchargez ensuite la bonne version.

Si vous téléchargez les deux versions de l’image, seule la dernière version est visible pour les clients finaux dans la galerie de la place de marché.

**Comment activer la machine virtuelle Windows Server ?**

Pour activer une machine virtuelle Windows Server sur Azure Stack, la condition suivante doit être remplie :

- Windows Server 2012 R2 et Windows Server 2016 doivent utiliser l’[activation automatique de machine virtuelle](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)). Le service de gestion de clés (KMS) et d’autres services d’activation ne sont pas pris en charge sur Azure Stack.

**Comment vérifier que ma machine virtuelle est activée ?**

Exécutez la commande suivante à partir d’une invite de commandes avec élévation de privilèges :

```shell
slmgr /dlv
```

Si la machine virtuelle est correctement activée, c’est clairement indiqué et le nom d’hôte apparaît dans la sortie slmgr. Ne vous fiez pas aux filigranes à l’écran, car ils peuvent soit ne pas être à jour, soit provenir d’une autre machine virtuelle derrière la vôtre.

**Comment réparer ma machine virtuelle si elle n’est pas configurée pour utiliser AVMA ?**

Exécutez la commande suivante à partir d’une invite de commandes avec élévation de privilèges :

```shell
slmgr /ipk <AVMA key>
```

Consultez l’article [Activation automatique de machine virtuelle](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) pour obtenir les clés à utiliser pour votre image.

**Je crée mes propres images Windows Server. Comment m’assurer qu’elles utilisent AVMA ?**

Nous vous recommandons d’exécuter commande `slmgr /ipk` avec la clé appropriée avant de lancer la commande `sysprep`. Vous pouvez également inclure la clé AVMA dans n’importe quel fichier d’installation Unattend.exe.

**J’essaie d’utiliser mon image Windows Server 2016 créée sur Azure, mais elle ne s’active pas ou n’utilise pas l’activation KMS.**

Exécutez la commande `slmgr /ipk`. Les images Azure peuvent ne pas revenir correctement à AVMA. Toutefois, si elles parviennent à atteindre le système Azure KMS, elles seront activées. Nous vous recommandons de vérifier que ces machines virtuelles sont définies de manière à utiliser AVMA.

**J’ai effectué toutes ces étapes, mais mes machines virtuelles ne sont toujours pas activées.**

Contactez le support Microsoft pour vérifier que les bons marqueurs BIOS ont été installés.

**Qu’en est-il des versions antérieures de Windows Server ?**

L’[activation automatique de machine virtuelle](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) n’est pas prise en charge dans les versions de Windows Server antérieures à la version 2012 R2. Vous devez activer manuellement les machines virtuelles à l’aide de clés MAK.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants :

- [Vue d’ensemble de la Place de Marché Azure Stack](../../operator/azure-stack-marketplace.md)
- [Télécharger des éléments de la Place de marché à partir d’Azure dans Azure Stack](azure-stack-download-azure-marketplace-item-tca.md)
