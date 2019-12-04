---
title: Notes de publication de ASDK | Microsoft Docs
description: Améliorations, correctifs et problèmes connus pour le Kit de développement Azure Stack (ASDK).
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: f5955751df89262441b7afc2381d34b6cc74691c
ms.sourcegitcommit: cefba8d6a93efaedff303d3c605b02bd28996c5d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74298743"
---
# <a name="asdk-release-notes"></a>Notes de publication relatives à l’ASDK

Cet article fournit des informations sur des modifications, des correctifs et des problèmes connus en lien avec le Kit de développement Azure Stack (ASDK). Si vous n’êtes pas sûr de la version que vous exécutez, consultez le [portail pour vérifier](../operator/azure-stack-updates.md).

Tenez-vous informé des nouveautés concernant le kit ASDK en vous abonnant au [flux RSS](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

::: moniker range="azs-1910"
## <a name="build-11910058"></a>Build 1.1910.0.58

### <a name="new-features"></a>Nouvelles fonctionnalités

- Pour obtenir la liste des problèmes résolus, des modifications et des nouvelles fonctionnalités de cette version, consultez les sections correspondantes dans les [notes de publication Azure Stack](../operator/release-notes.md).

### <a name="fixed-and-known-issues"></a>Problèmes connus et résolus

- Résolution d’un problème lié à la collecte des journaux et à leur stockage dans un conteneur d’objets blob de stockage Azure. La syntaxe de cette opération est la suivante :

  ```powershell
  Get-AzureStackLog -OutputSasUri "<Blob service SAS Uri>"
  ``` 

- Résolution d’un problème de déploiement selon lequel un service de spouleur à chargement lent empêche la suppression de certaines fonctionnalités Windows et nécessite un redémarrage.
- Pour obtenir la liste des problèmes connus d’Azure Stack de cette version, consultez l’article sur les [problèmes connus](../operator/known-issues.md).
- Notez que les correctifs logiciels d’Azure Stack ne sont pas applicables au kit ASDK.
::: moniker-end

::: moniker range="azs-1908"
  
## <a name="build-11908020"></a>Build 1.1908.0.20

### <a name="new-features"></a>Nouvelles fonctionnalités

- Pour obtenir la liste des nouvelles fonctionnalités dans cette version, consultez [cette section](/azure-stack/operator/release-notes?view=azs-1908#whats-new-1908) des notes de publication Azure Stack.

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>Problèmes connus et résolus

<!-- - For a list of Azure Stack issues fixed in this release, see [this section](/azure-stack/operator/release-notes?view=azs-1908#fixes-1908) of the Azure Stack release notes. -->
- Pour obtenir la liste des problèmes connus, consultez [cet article](/azure-stack/operator/known-issues?view=azs-1908).
- Notez que les correctifs logiciels d’Azure Stack ne sont pas applicables au kit ASDK.
::: moniker-end

::: moniker range="azs-1907"
## <a name="build-11907020"></a>Build 1.1907.0.20

### <a name="new-features"></a>Nouvelles fonctionnalités

- Pour obtenir la liste des nouvelles fonctionnalités dans cette version, consultez [cette section](/azure-stack/operator/release-notes?view=azs-1907#whats-in-this-update-1907) des notes de publication Azure Stack.

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>Problèmes connus et résolus

- Lorsque vous créez des ressources de machine virtuelle à l’aide de certaines images de la place de marché, vous ne pourrez peut-être pas effectuer le déploiement. Pour résoudre ce problème, vous pouvez cliquer sur le lien **Télécharger le modèle et les paramètres** dans la page de **résumé**, puis cliquer sur le bouton **Déployer** dans le panneau **Modèle**.
- Pour obtenir la liste des problèmes Azure Stack corrigés dans cette version, consultez [cette section](/azure-stack/operator/release-notes?view=azs-1907#fixes-1907) des notes de publication Azure Stack.
- Pour obtenir la liste des problèmes connus, consultez [cet article](/azure-stack/operator/known-issues?view=azs-1907).
- Notez que les [correctifs logiciels d’Azure Stack](/azure-stack/operator/release-notes?view=azs-1907#hotfixes-1907) ne sont pas applicables au kit ASDK Azure Stack.
::: moniker-end

::: moniker range="azs-1906"
## <a name="build-11906030"></a>Build 1.1906.0.30

### <a name="new-features"></a>Nouvelles fonctionnalités

- Pour obtenir la liste des nouvelles fonctionnalités dans cette version, consultez [cette section](/azure-stack/operator/release-notes?view=azs-1906#whats-in-this-update-1906) des notes de publication Azure Stack.

### <a name="changes"></a>Changements

- Ajout d’une machine virtuelle de prise en charge **AzS-SRNG01** qui héberge le service de collecte des journaux pour Azure Stack. Pour plus d’informations, consultez [Rôles des machines virtuelles](asdk-architecture.md).

### <a name="fixed-and-known-issues"></a>Problèmes connus et résolus

- Lorsque vous créez des ressources de machine virtuelle à l’aide de certaines images de la place de marché, vous ne pourrez peut-être pas effectuer le déploiement. Pour résoudre ce problème, vous pouvez cliquer sur le lien **Télécharger le modèle et les paramètres** dans la page de **résumé**, puis cliquer sur le bouton **Déployer** dans le panneau **Modèle**.
- Pour obtenir la liste des problèmes Azure Stack corrigés dans cette version, consultez [cette section](/azure-stack/operator/release-notes?view=azs-1906#fixes-1906) des notes de publication Azure Stack.
- Pour obtenir la liste des problèmes connus, consultez [cet article](/azure-stack/operator/known-issues?view=azs-1906).
- Notez que les [correctifs logiciels d’Azure Stack](/azure-stack/operator/release-notes?view=azs-1906#hotfixes-1906) ne sont pas applicables au kit ASDK Azure Stack.
::: moniker-end
