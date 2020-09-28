---
title: Appliquer des mises à jour à un fournisseur de ressources Azure Stack Hub
description: Découvrez comment appliquer une mise à jour de service à un fournisseur de ressources sur Azure Stack Hub.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/18/2019
ms.reviewer: jfggdl
ms.lastreviewed: 11/18/2019
ms.openlocfilehash: ff1a6258e682d3230ea7968a97b9e392991ca72b
ms.sourcegitcommit: 53b0dde60a6435936a5e0cb9e931245f262d637a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/23/2020
ms.locfileid: "91107113"
---
# <a name="how-to-update-an-azure-stack-hub-resource-provider"></a>Comment mettre à jour un fournisseur de ressources Azure Stack Hub

> [!IMPORTANT]
> Avant de poursuivre, consultez les dernières notes de publication du fournisseur de ressources pour en savoir plus sur les nouvelles fonctionnalités, les correctifs et les problèmes connus qui pourraient affecter votre déploiement. Les notes de publication peuvent également spécifier la version minimale d'Azure Stack Hub requise par le fournisseur de ressources.

Les fournisseurs de ressources installés à partir de la Place de marché nécessitent une maintenance régulière. La maintenance est effectuée par l’application régulière de mises à jour de service fournies par Microsoft. Les mises à jour peuvent inclure de nouvelles fonctionnalités et des correctifs.  

## <a name="check-for-updates"></a>Rechercher les mises à jour

Les fournisseurs de ressources sont mis à jour à l’aide de la même fonctionnalité que celle utilisée pour appliquer les mises à jour d’Azure Stack Hub.

1. Connectez-vous au portail d’administration Azure Stack Hub.
2. Sélectionnez le lien **Tous les services** sur la gauche, puis, dans la section **Administration**, sélectionnez **Mises à jour**.
   ![Page Tous les services](media/resource-provider-apply-updates/1-all-services.png)

3. Dans la page **Mises à jour**, vous trouvez des mises à jour pour les fournisseurs de ressources dans la section **Fournisseur de ressources**.

   [![Capture d’écran montrant la section du fournisseur de ressources.](media/resource-provider-apply-updates/3-update-available.png)](media/resource-provider-apply-updates/3-update-available.png#lightbox)

## <a name="apply-an-update"></a>Appliquer une mise à jour

Si une mise à jour est disponible pour un fournisseur de ressources donné :

1. Sélectionnez la ligne du fournisseur de ressources que vous souhaitez mettre à jour. Notez que le lien **Télécharger** en haut de la page est alors activé.
   [![Page des mises à jour disponibles](media/resource-provider-apply-updates/4-download.png)](media/resource-provider-apply-updates/3-update-available.png#lightbox)

2. Cliquez sur le lien **Télécharger** pour commencer le téléchargement du package d’installation du fournisseur de ressources. Sur la ligne du fournisseur de ressources, notez que la colonne **État** n’indique plus l’état « Disponible », mais « Téléchargement ».
3. Quand l’**État** prend la valeur « Prêt pour l’installation », le téléchargement est terminé. Notez que le lien **Installer maintenant** en haut de la page est à présent également activé.
4. Sélectionnez le lien **Installer maintenant**. Vous accédez à la page **Installer** pour le fournisseur de ressources. 
5. Sélectionnez le bouton **Installer** pour commencer l’installation.
6. Une notification « Installation en cours » s’affiche dans l’angle supérieur droit et vous revenez à la page **Mises à jour**. Sur la ligne du fournisseur de ressources, la colonne **État** change également pour indiquer « Installation ».
7. À la fin de l’installation, une autre notification indique si celle-ci a réussi ou échoué. Quand l’installation réussit, la **Version** est également mise à jour sur la page **Gestion de la Place de marché - Fournisseurs de ressources**.

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur la [fonctionnalité de mise à jour du tableau de bord de l’administrateur](azure-stack-apply-updates.md).
