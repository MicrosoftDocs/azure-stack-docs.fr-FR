---
title: Questions fréquentes (FAQ) sur la Place de marché Azure Stack Hub
titleSuffix: Azure Stack Hub
description: Questions fréquentes (FAQ) sur Windows Server dans la Place de marché Azure Stack Hub.
author: sethmanheim
ms.topic: article
ms.date: 11/09/2020
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 08/29/2019
ms.openlocfilehash: 0801f9530bc3f462e1ddfd0fbce15d193ea6343e
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94545716"
---
# <a name="azure-stack-hub-marketplace-faq"></a>Questions fréquentes (FAQ) sur la Place de marché Azure Stack Hub

Cet article répond à certaines questions fréquentes sur les produits de la [Place de marché Azure Stack Hub](azure-stack-marketplace.md).

## <a name="marketplace-items"></a>Éléments du Marketplace

### <a name="who-should-i-contact-for-support-issues-with-azure-stack-hub-marketplace-items"></a>Qui dois-je contacter en cas de problème de support concernant les produits de la Place de marché Azure Stack Hub ?

Le support de la Place de marché Azure s’étend aux produits de la Place de marché Azure Stack Hub. Les éditeurs sont chargés de fournir un support technique pour leurs produits qui figurent sur la Place de marché Azure Stack Hub. Pour plus d’informations sur le support à fournir pour les produits de la Place de marché Azure, consultez la [section consacrée au support](/azure/marketplace/marketplace-faq-publisher-guide#customer-support) dans les Questions fréquentes sur la Place de marché Azure.

### <a name="how-do-i-update-to-a-newer-windows-image"></a>Comment effectuer une mise à jour avec une image Windows plus récente ?

Déterminez d’abord si des modèles Azure Resource Manager font référence à des versions spécifiques. Si tel est le cas, mettez à jour ces modèles ou conservez les anciennes versions de l’image. Il est préférable d’utiliser **version: latest**.

Ensuite, si des groupes de machines virtuelles identiques font référence à une version spécifique, déterminez s’ils vont être mis à l’échelle plus tard, et décidez si les anciennes versions doivent être conservées ou non. Si aucune de ces conditions ne s’applique, supprimez les anciennes images de la Place de marché Azure Stack Hub avant de télécharger les plus récentes. Utilisez pour cela le panneau Gestion de la Place de marché si la version d’origine a été téléchargée de cette façon. Téléchargez ensuite la version plus récente.

### <a name="what-are-the-licensing-options-for-windows-server-images-on-azure-stack-hub-marketplace"></a>Quelles sont les options de licence pour les images Windows Server sur la Place de Marché Azure Stack Hub ?

Les images Windows Server proposées par Microsoft via la Place de marché Azure Stack Hub sont disponibles en deux versions. Une seule version de cette image peut être utilisée dans un environnement Azure Stack Hub.  

- **Paiement à l’utilisation (PAYG)**  : Ces images sont associées à des compteurs Windows plein tarif.
   À qui s’adresse cette option : clients Contrat Entreprise (EA) utilisant le *modèle de facturation Consommation* et fournisseurs de services cloud ne souhaitant pas utiliser de licence SPLA.
- **BYOL (apportez votre propre licence)**  : ces images sont associées à des compteurs de base.
   À qui s’adresse cette option : clients EA disposant d’une licence Windows Server et fournisseurs de services cloud utilisant une licence SPLA.

Azure Hybrid Use Benefit (AHUB) n’est pas pris en charge sur Azure Stack Hub. Les clients qui concèdent des licences par le biais du modèle « Capacité » doivent utiliser l’image BYOL. Si vous effectuez des tests avec le Kit de développement Azure Stack (ASDK), vous pouvez utiliser l’une de ces options.

### <a name="what-if-i-downloaded-the-wrong-version-to-offer-my-tenantsusers"></a>Que faire si je télécharge la mauvaise version et que j’offre celle-ci à mes clients/utilisateurs ?

Commencez par supprimer la version incorrecte de la place de marché. Attendez que l’opération se termine (ce qui est indiqué dans une notification, et non dans le panneau **Gestion de la Place de marché** ). Téléchargez ensuite la bonne version.

Si vous téléchargez les deux versions de l’image, seule la dernière version est visible pour les clients finals dans la Place de marché Azure Stack Hub.

### <a name="what-if-my-user-incorrectly-checked-the-i-have-a-license-box-in-previous-windows-builds-and-they-dont-have-a-license"></a>Que faire si l’un de mes utilisateurs a par erreur coché la case « J’ai une licence » dans les builds Windows précédentes alors qu’il n’a pas de licence ?

Vous pouvez changer l’attribut de modèle de licence pour passer de la licence BYOL au modèle PAYG en exécutant le script suivant :

```powershell
$vm= Get-Azvm -ResourceGroup "<your RG>" -Name "<your VM>"
$vm.LicenseType = "None"
Update-AzVM -ResourceGroupName "<your RG>" -VM $vm
```

Vous pouvez vérifier le type de licence de votre machine virtuelle en exécutant les commandes suivantes. Si le modèle de licence indique **Windows_Server** , vous êtes facturé selon le tarif BYOL. Sinon, vous êtes facturé sur la base du compteur Windows, conformément au modèle PAYG :

```powershell
$vm | ft Name, VmId,LicenseType,ProvisioningState
```

### <a name="what-if-i-have-an-older-image-and-my-user-forgot-to-check-the-i-have-a-license-box-or-we-use-our-own-images-and-we-do-have-enterprise-agreement-entitlement"></a>Que faire si j’ai une ancienne image et que l’un de mes utilisateurs a oublié de cocher la case « J’ai une licence » ou si j’utilise mes propres images et que je ne dispose pas du droit Contrat Entreprise ?

Vous pouvez changer l’attribut de modèle de licence pour passer au modèle BYOL en exécutant les commandes suivantes :

```powershell
$vm= Get-Azvm -ResourceGroup "<your RG>" -Name "<your VM>"
$vm.LicenseType = "Windows_Server"
Update-AzVM -ResourceGroupName "<your RG>" -VM $vm
```

### <a name="what-about-other-vms-that-use-windows-server-such-as-sql-or-machine-learning-server"></a>Qu’en est-il des autres machines virtuelles qui utilisent Windows Server, par exemple SQL ou Machine Learning Server ?

Ces images appliquent le paramètre **licenseType** , elles sont donc PAYG. Vous pouvez définir ce paramètre (voir la réponse précédente). Ceci ne s’applique qu’aux logiciels Windows Server. Les produits en couche comme SQL, qui vous obligent à apporter votre propre licence, ne sont pas concernés. Les licences PAYG ne s’appliquent pas aux produits logiciels en couche.

Vous pouvez uniquement changer la propriété **licenseType** pour les images SQL Server de la Place de marché Azure Stack Hub si la version est  **XX.X.20190410** ou ultérieure. Si vous exécutez une version antérieure des images SQL Server de la Place de marché Azure Stack Hub, vous ne pouvez pas changer l’attribut **licenseType**. Vous devez effectuer un redéploiement à l’aide des dernières images SQL Server de la Place de marché Azure Stack Hub.

### <a name="i-have-an-enterprise-agreement-ea-and-will-be-using-my-ea-windows-server-license-how-do-i-make-sure-images-are-billed-correctly"></a>J’ai un Contrat Entreprise (EA) et je vais utiliser ma licence Windows Server EA. Comment m’assurer que les images sont facturées correctement ?

Vous pouvez ajouter **licenseType : Windows_Server** dans un modèle Azure Resource Manager. Ce paramètre doit être ajouté à chaque bloc de ressource de machine virtuelle.

## <a name="activation"></a>Activation

Pour activer une machine virtuelle Windows Server sur Azure Stack Hub, les conditions suivantes doivent être remplies :

- Le fabricant OEM a défini le marqueur BIOS approprié sur chaque système hôte dans Azure Stack Hub.
- Windows Server 2012 R2 et Windows Server 2016 doivent utiliser l’[activation automatique de machine virtuelle](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)). Le service de gestion de clés (KMS) et d’autres services d’activation ne sont pas pris en charge sur Azure Stack Hub.

### <a name="how-can-i-verify-that-my-vm-is-activated"></a>Comment vérifier que ma machine virtuelle est activée ?

Exécutez la commande suivante à partir d’une invite de commandes avec élévation de privilèges :

```shell
slmgr /dlv
```

Si la machine virtuelle est correctement activée, c’est clairement indiqué et le nom d’hôte apparaît dans la sortie `slmgr`. Ne vous fiez pas aux filigranes à l’écran, car ils peuvent ne pas être à jour ou provenir d’une autre machine virtuelle derrière la vôtre.

### <a name="my-vm-isnt-set-up-to-use-avma-how-can-i-fix-it"></a>Comment corriger ma machine virtuelle si elle n’est pas configurée pour utiliser AVMA ?

Exécutez la commande suivante à partir d’une invite de commandes avec élévation de privilèges :

```shell
slmgr /ipk <AVMA key>
```

Consultez l’article [Activation automatique des machines virtuelles](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) pour obtenir les clés à utiliser pour votre image.

### <a name="i-create-my-own-windows-server-images-how-can-i-make-sure-they-use-avma"></a>Je crée mes propres images Windows Server. Comment m’assurer qu’elles utilisent AVMA ?

Nous vous recommandons d’exécuter la ligne de commande `slmgr /ipk` avec la clé appropriée avant de lancer la commande `sysprep`. Vous pouvez également inclure la clé AVMA dans n’importe quel fichier d’installation Unattend.exe.

### <a name="i-am-trying-to-use-my-windows-server-2016-image-created-on-azure-and-its-not-activating-or-using-kms-activation"></a>J’essaie d’utiliser mon image Windows Server 2016 créée sur Azure, mais elle ne s’active pas ou n’utilise pas l’activation KMS.

Exécutez la commande `slmgr /ipk`. Les images Azure peuvent ne pas revenir correctement à AVMA. Toutefois, si elles parviennent à atteindre le système Azure KMS, elles seront activées. Nous vous recommandons de vérifier que ces machines virtuelles sont configurées pour utiliser AVMA.

### <a name="i-have-performed-all-of-these-steps-but-my-vms-are-still-not-activating"></a>J’ai effectué toutes ces étapes, mais mes machines virtuelles ne sont toujours pas activées.

Contactez le fournisseur de votre matériel pour vérifier que les bons marqueurs BIOS ont été installés.

### <a name="what-about-earlier-versions-of-windows-server"></a>Qu’en est-il des versions antérieures de Windows Server ?

L’[activation automatique de machine virtuelle](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) n’est pas prise en charge dans les versions antérieures de Windows Server. Vous devez activer manuellement les machines virtuelles.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants :

- [Vue d’ensemble de la Place de marché Azure Stack Hub](azure-stack-marketplace.md)
- [Télécharger des éléments de la Place de marché à partir d’Azure vers Azure Stack Hub](azure-stack-download-azure-marketplace-item.md)
