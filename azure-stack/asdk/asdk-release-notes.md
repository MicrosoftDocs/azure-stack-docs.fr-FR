---
title: Notes de publication relatives à l’ASDK
description: Améliorations, correctifs et problèmes connus pour le Kit de développement Azure Stack (ASDK).
author: sethmanheim
ms.topic: article
ms.date: 04/06/2020
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 03/18/2020
ms.openlocfilehash: 84a29b2e596f0dc595b89330502345cc7f263533
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86489995"
---
# <a name="asdk-release-notes"></a>Notes de publication relatives à l’ASDK

Cet article fournit des informations sur des modifications, des correctifs et des problèmes connus en lien avec le Kit de développement Azure Stack (ASDK). Si vous n’êtes pas sûr de la version que vous exécutez, consultez le [portail pour vérifier](../operator/azure-stack-updates.md).

Tenez-vous informé des nouveautés concernant le kit ASDK en vous abonnant au [flux RSS](https://docs.microsoft.com/api/search/rss?search=ASDK+release+notes&locale=en-us#) ![RSS](./media/asdk-release-notes/feed-icon-14x14.png).

::: moniker range="azs-2002"
## <a name="build-12002035"></a>Build 1.2002.0.35

### <a name="new-features"></a>Nouvelles fonctionnalités

- Pour obtenir la liste des problèmes résolus, des modifications et des nouvelles fonctionnalités de cette version, consultez les sections correspondantes dans les [notes de publication Azure Stack](../operator/release-notes.md).

### <a name="fixed-and-known-issues"></a>Problèmes connus et résolus

- Le mot de passe de certification de déchiffrement est une nouvelle option qui permet de spécifier le mot de passe destiné au certificat auto-signé (.pfx) contenant la clé privée nécessaire au déchiffrement des données de sauvegarde. Ce mot de passe est obligatoire uniquement si la sauvegarde est chiffrée à l’aide d’un certificat.
- Pour obtenir la liste des problèmes connus d’Azure Stack de cette version, consultez l’article sur les [problèmes connus](../operator/known-issues.md).
- Notez que les correctifs logiciels d’Azure Stack ne sont pas applicables au kit ASDK.

#### <a name="sql-vm-provision-fails-in-asdk"></a>Le provisionnement de la machine virtuelle SQL échoue dans ASDK

- Champ d’application : Ce problème concerne le kit ASDK 2002.
- Cause : Lors de la création d’une machine virtuelle SQL dans le kit ASDK 2002, vous pouvez recevoir le message d’erreur **L’extension avec le serveur de publication « Microsoft.SqlServer.Management », le type « SqlIaaSAgent » et la version du gestionnaire de types « 2.0 » est introuvable dans le dépôt d’extensions.** **SqlIaaSAgent** 2.0 n’existe pas dans Azure Stack Hub.
::: moniker-end

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

- Pour obtenir la liste des nouvelles fonctionnalités dans cette version, consultez [cette section](../operator/release-notes.md?view=azs-1908#whats-new-2) des notes de publication Azure Stack.

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>Problèmes connus et résolus

<!-- - For a list of Azure Stack issues fixed in this release, see [this section](../operator/release-notes.md?view=azs-1908#fixes-1) of the Azure Stack release notes. -->
- Pour obtenir la liste des problèmes connus, consultez [cet article](../operator/known-issues.md?view=azs-1908).
- Notez que les correctifs logiciels d’Azure Stack ne sont pas applicables au kit ASDK.
::: moniker-end

::: moniker range="azs-1907"
## <a name="build-11907020"></a>Build 1.1907.0.20

### <a name="new-features"></a>Nouvelles fonctionnalités

- Pour obtenir la liste des nouvelles fonctionnalités dans cette version, consultez [cette section](../operator/release-notes.md?view=azs-1907#whats-in-this-update) des notes de publication Azure Stack.

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>Problèmes connus et résolus

- Lorsque vous créez des ressources de machine virtuelle à l’aide de certaines images de la place de marché, vous ne pourrez peut-être pas effectuer le déploiement. Pour résoudre ce problème, vous pouvez cliquer sur le lien **Télécharger le modèle et les paramètres** dans la page de **résumé**, puis cliquer sur le bouton **Déployer** dans le panneau **Modèle**.
- Pour obtenir la liste des problèmes Azure Stack corrigés dans cette version, consultez [cette section](../operator/release-notes.md?view=azs-1907#fixes-3) des notes de publication Azure Stack.
- Pour obtenir la liste des problèmes connus, consultez [cet article](../operator/known-issues.md?view=azs-1907).
- Notez que les [correctifs logiciels d’Azure Stack](../operator/release-notes.md?view=azs-1907#hotfixes-3) ne sont pas applicables au kit ASDK Azure Stack.
::: moniker-end
