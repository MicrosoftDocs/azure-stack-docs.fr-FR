---
title: Questions fréquentes (FAQ) sur Windows Server dans Azure Stack | Microsoft Docs
description: Liste des questions fréquentes sur Windows Server dans la Place de marché Azure Stack
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
ms.date: 05/31/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 11/12/2018
ms.openlocfilehash: ffb58392075521b81d9b0cd71928ac0db680991f
ms.sourcegitcommit: 80775f5c5235147ae730dfc7e896675a9a79cdbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/03/2019
ms.locfileid: "66459073"
---
# <a name="windows-server-in-azure-stack-marketplace-faq"></a>FAQ relative à Windows Server dans la Place de marché Azure Stack

Cet article répond à certaines questions fréquentes sur les images Windows Server dans la [Place de marché Azure Stack](azure-stack-marketplace.md).

## <a name="marketplace-items"></a>Éléments du Marketplace

### <a name="how-do-i-update-to-a-newer-windows-image"></a>Comment effectuer une mise à jour avec une image Windows plus récente ?

Déterminez d’abord si des modèles Azure Resource Manager font référence à des versions spécifiques. Si tel est le cas, mettez à jour ces modèles ou conservez les anciennes versions de l’image. Il est préférable d’utiliser **version: latest**.

Ensuite, si des groupes de machines virtuelles identiques font référence à une version spécifique, déterminez s’ils seront mis à l’échelle par la suite et décidez s’il faut conserver les anciennes versions. Si aucune de ces conditions ne s’applique, supprimez les anciennes images de la Place de marché avant de télécharger les plus récentes. Utilisez pour cela le panneau Gestion de la Place de marché si la version d’origine a été téléchargée de cette façon. Téléchargez ensuite la version plus récente.

### <a name="what-are-the-licensing-options-for-windows-server-marketplace-images-on-azure-stack"></a>Quelles sont les options de licence pour les images Place de Marché Windows Server sur Azure Stack ?

Les images Windows Server proposées par Microsoft par le biais de la Place de marché Azure Stack sont disponibles en deux versions :

- **Paiement à l’utilisation** : ces images sont associées à des compteurs Windows plein tarif.
   Public visé : clients Contrat Entreprise (EA) utilisant le *modèle de facturation Consommation* et fournisseurs de services cloud ne souhaitant pas utiliser de licence SPLA.
- **BYOL (apportez votre propre licence)**  : ces images sont associées à des compteurs de base.
   Public visé : clients EA disposant d’une licence Windows Server et fournisseurs de services cloud utilisant une licence SPLA.

Azure Hybrid Use Benefit (AHUB) n’est pas pris en charge sur Azure Stack. Les clients qui concèdent des licences par le biais du modèle « Capacité » doivent utiliser l’image BYOL. Si vous effectuez des tests avec le Kit de développement Azure Stack (ASDK), vous pouvez utiliser l’une de ces options.

### <a name="what-if-i-downloaded-the-wrong-version-to-offer-my-tenantsusers"></a>Que faire si je télécharge la mauvaise version et que j’offre celle-ci à mes clients/utilisateurs ?

Commencez par supprimer la version incorrecte à l’aide du panneau Gestion de la Place de marché. Attendez que l’opération se termine entièrement (ce qui est indiqué dans une notification, et non dans le panneau **Gestion de la Place de marché**). Téléchargez ensuite la bonne version.

### <a name="what-if-my-user-incorrectly-checked-the-i-have-a-license-box-in-previous-windows-builds-and-they-dont-have-a-license"></a>Que faire si l’un de mes utilisateurs a par erreur coché la case « J’ai une licence » dans les builds Windows précédentes alors qu’il n’a pas de licence ?

Consultez [Convertir des machines virtuelles Windows Server avec Azure Hybrid Benefit en mode paiement à l’utilisation](/azure/virtual-machines/windows/hybrid-use-benefit-licensing#powershell-1).

### <a name="what-if-i-have-an-older-image-and-my-user-forgot-to-check-the-i-have-a-license-box-or-we-use-our-own-images-and-we-do-have-enterprise-agreement-entitlement"></a>Que faire si j’ai une ancienne image et que l’un de mes utilisateurs a oublié de cocher la case « J’ai une licence » ou si j’utilise mes propres images et que je ne dispose pas du droit Contrat Entreprise ?

Consultez [Convertir une machine virtuelle existante pour utiliser Azure Hybrid Benefit pour Windows Server](/azure/virtual-machines/windows/hybrid-use-benefit-licensing#convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server). Notez qu’Azure Hybrid Benefit ne s’applique pas à Azure Stack, mais que l’effet de ce paramètre s’applique.

### <a name="what-about-other-vms-that-use-windows-server-such-as-sql-or-machine-learning-server"></a>Qu’en est-il des autres machines virtuelles qui utilisent Windows Server, par exemple SQL ou Machine Learning Server ?

Ces images appliquent le paramètre **licenseType**, donc elles sont basées sur le paiement à l’utilisation. Vous pouvez définir ce paramètre (voir la réponse précédente). Ceci ne s’applique qu’aux logiciels Windows Server. Les produits en couche comme SQL, qui vous obligent à apporter votre propre licence, ne sont pas concernés. Les licences avec paiement à l’utilisation ne s’appliquent pas aux produits logiciels en couche.

### <a name="i-have-an-enterprise-agreement-ea-and-will-be-using-my-ea-windows-server-license-how-do-i-make-sure-images-are-billed-correctly"></a>J’ai un Contrat Entreprise (EA) et je vais utiliser ma licence Windows Server EA. Comment m’assurer que les images sont facturées correctement ?

Vous pouvez ajouter **licenseType : Windows_Server** dans un modèle Azure Resource Manager. Ce paramètre doit être ajouté à chaque bloc de ressource de machine virtuelle.

## <a name="activation"></a>Activation

Pour activer une machine virtuelle Windows Server sur Azure Stack, les conditions suivantes doivent être remplies :

- Le fabricant OEM a défini le marqueur BIOS approprié sur chaque système hôte dans Azure Stack.
- Windows Server 2012 R2 et Windows Server 2016 doivent utiliser l’[activation automatique de machine virtuelle](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)). Le service de gestion de clés (KMS) et d’autres services d’activation ne sont pas pris en charge sur Azure Stack.

### <a name="how-can-i-verify-that-my-virtual-machine-is-activated"></a>Comment vérifier que ma machine virtuelle est activée ?

Exécutez la commande suivante à partir d’une invite de commandes avec élévation de privilèges :

```shell
slmgr /dlv
```

Si la machine virtuelle est correctement activée, c’est clairement indiqué et le nom d’hôte apparaît dans la sortie `slmgr`. Ne vous fiez pas aux filigranes à l’écran car ils peuvent soit ne pas être à jour, soit provenir d’une autre machine virtuelle derrière la vôtre.

### <a name="my-vm-is-not-set-up-to-use-avma-how-can-i-fix-it"></a>Comment réparer ma machine virtuelle si elle n’est pas configurée pour utiliser AVMA ?

Exécutez la commande suivante à partir d’une invite de commandes avec élévation de privilèges :

```shell
slmgr /ipk <AVMA key>
```

Consultez l’article [Activation automatique de machine virtuelle](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) pour obtenir les clés à utiliser pour votre image.

### <a name="i-create-my-own-windows-server-images-how-can-i-make-sure-they-use-avma"></a>Je crée mes propres images Windows Server. Comment m’assurer qu’elles utilisent AVMA ?

Nous vous recommandons d’exécuter la ligne de commande `slmgr /ipk` avec la clé appropriée avant de lancer la commande `sysprep`. Vous pouvez également inclure la clé AVMA dans n’importe quel fichier d’installation Unattend.exe.

### <a name="i-am-trying-to-use-my-windows-server-2016-image-created-on-azure-and-it-is-not-activating-or-using-kms-activation"></a>J’essaie d’utiliser mon image Windows Server 2016 créée sur Azure, mais elle ne s’active pas ou n’utilise pas l’activation KMS.

Exécutez la commande `slmgr /ipk`. Les images Azure peuvent ne pas revenir correctement à AVMA. Toutefois, si elles parviennent à atteindre le système Azure KMS, elles seront activées. Nous vous recommandons de vérifier que ces machines virtuelles sont définies de manière à utiliser AVMA.

### <a name="i-have-performed-all-of-these-steps-but-my-virtual-machines-are-still-not-activating"></a>J’ai effectué toutes ces étapes, mais mes machines virtuelles ne sont toujours pas activées.

Contactez le fournisseur de votre matériel pour vérifier que les bons marqueurs BIOS ont été installés.

### <a name="what-about-earlier-versions-of-windows-server"></a>Qu’en est-il des versions antérieures de Windows Server ?

L’[activation automatique de machine virtuelle](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) n’est pas prise en charge dans les versions antérieures de Windows Server. Vous devez activer manuellement les machines virtuelles.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants :

- [Vue d’ensemble de la Place de Marché Azure Stack](azure-stack-marketplace.md)
- [Télécharger des éléments de la Place de marché à partir d’Azure dans Azure Stack](azure-stack-download-azure-marketplace-item.md)
