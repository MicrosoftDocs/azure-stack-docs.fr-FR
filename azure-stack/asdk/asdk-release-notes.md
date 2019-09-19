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
ms.date: 08/30/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 08/30/2019
ms.openlocfilehash: 98e43cfe0226e06ca936484a78da5a61915f5797
ms.sourcegitcommit: c46d913ebfa4cb6c775c5117ac5c9e87d032a271
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71101033"
---
# <a name="asdk-release-notes"></a>Notes de publication relatives à l’ASDK

Cet article fournit des informations sur des modifications, des correctifs et des problèmes connus en lien avec le Kit de développement Azure Stack (ASDK). Si vous n’êtes pas sûr de la version que vous exécutez, consultez le [portail pour vérifier](../operator/azure-stack-updates.md).

Tenez-vous informé des nouveautés concernant le kit ASDK en vous abonnant au [flux RSS](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

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

::: moniker range="azs-1905"
## <a name="build-11905040"></a>Build 1.1905.0.40

<!-- ### Changes -->

### <a name="new-features"></a>Nouvelles fonctionnalités

- Pour obtenir la liste des nouvelles fonctionnalités dans cette version, consultez [cette section](/azure-stack/operator/release-notes?view=azs-1905#whats-in-this-update-1905) des notes de publication Azure Stack.

### <a name="fixed-and-known-issues"></a>Problèmes connus et résolus

- Correction d’un problème dans lequel vous devez modifier le script PowerShell **RegisterWithAzure.psm1** pour pouvoir [inscrire le Kit ASDK](asdk-register.md).
- Pour obtenir la liste des autres problèmes Azure Stack corrigés dans cette version, consultez [cette section](/azure-stack/operator/release-notes?view=azs-1905#fixes-1905) des notes de publication Azure Stack.
- Pour obtenir la liste des problèmes connus, consultez [cet article](/azure-stack/operator/known-issues?view=azs-1905).
- Notez que les [correctifs logiciels d’Azure Stack](/azure-stack/operator/release-notes?view=azs-1905#hotfixes-1905) ne sont pas applicables au kit ASDK Azure Stack.
::: moniker-end
