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
ms.date: 07/25/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 07/25/2019
ms.openlocfilehash: 3f11a7b5066d0b50d85a40be1df47dfe1a5ade38
ms.sourcegitcommit: 7968f9f0946138867323793be9966ee2ef99dcf4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2019
ms.locfileid: "70025842"
---
# <a name="asdk-release-notes"></a>Notes de publication relatives à l’ASDK

Cet article fournit des informations sur des modifications, des correctifs et des problèmes connus en lien avec le Kit de développement Azure Stack (ASDK). Si vous n’êtes pas sûr de la version que vous exécutez, consultez le [portail pour vérifier](../operator/azure-stack-updates.md).

Tenez-vous informé des nouveautés concernant le kit ASDK en vous abonnant au [flux RSS](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-11907020"></a>Build 1.1907.0.20

### <a name="new-features"></a>Nouvelles fonctionnalités

- Pour obtenir la liste des nouvelles fonctionnalités dans cette version, consultez [cette section](../operator/azure-stack-release-notes-1907.md#whats-in-this-update) des notes de publication Azure Stack.

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>Problèmes connus et résolus

- Lorsque vous créez des ressources de machine virtuelle à l’aide de certaines images de la place de marché, vous ne pourrez peut-être pas effectuer le déploiement. Pour résoudre ce problème, vous pouvez cliquer sur le lien **Télécharger le modèle et les paramètres** dans la page de **résumé**, puis cliquer sur le bouton **Déployer** dans le panneau **Modèle**.
- Pour obtenir la liste des problèmes Azure Stack corrigés dans cette version, consultez [cette section](../operator/azure-stack-release-notes-1907.md#fixes) des notes de publication Azure Stack.
- Pour obtenir la liste des problèmes connus, consultez [cet article](../operator/azure-stack-release-notes-known-issues-1907.md).
- Notez que les [correctifs logiciels d’Azure Stack](../operator/azure-stack-release-notes-1907.md#hotfixes) ne sont pas applicables au kit ASDK Azure Stack.

## <a name="build-11906030"></a>Build 1.1906.0.30

### <a name="new-features"></a>Nouvelles fonctionnalités

- Pour obtenir la liste des nouvelles fonctionnalités dans cette version, consultez [cette section](../operator/azure-stack-release-notes-1906.md#whats-in-this-update) des notes de publication Azure Stack.

### <a name="changes"></a>Changements

- Ajout d’une machine virtuelle de prise en charge **AzS-SRNG01** qui héberge le service de collecte des journaux pour Azure Stack. Pour plus d’informations, consultez [Rôles des machines virtuelles](asdk-architecture.md).

### <a name="fixed-and-known-issues"></a>Problèmes connus et résolus

- Lorsque vous créez des ressources de machine virtuelle à l’aide de certaines images de la place de marché, vous ne pourrez peut-être pas effectuer le déploiement. Pour résoudre ce problème, vous pouvez cliquer sur le lien **Télécharger le modèle et les paramètres** dans la page de **résumé**, puis cliquer sur le bouton **Déployer** dans le panneau **Modèle**.
- Pour obtenir la liste des problèmes Azure Stack corrigés dans cette version, consultez [cette section](../operator/azure-stack-release-notes-1906.md#fixes) des notes de publication Azure Stack.
- Pour obtenir la liste des problèmes connus, consultez [cet article](../operator/azure-stack-release-notes-known-issues-1906.md).
- Notez que les [correctifs logiciels d’Azure Stack](../operator/azure-stack-release-notes-1906.md#hotfixes) ne sont pas applicables au kit ASDK Azure Stack.

## <a name="build-11905040"></a>Build 1.1905.0.40

<!-- ### Changes -->

### <a name="new-features"></a>Nouvelles fonctionnalités

- Pour obtenir la liste des nouvelles fonctionnalités dans cette version, consultez [cette section](../operator/azure-stack-release-notes-1905.md#whats-in-this-update) des notes de publication Azure Stack.

### <a name="fixed-and-known-issues"></a>Problèmes connus et résolus

- Correction d’un problème dans lequel vous devez modifier le script PowerShell **RegisterWithAzure.psm1** pour pouvoir [inscrire le Kit ASDK](asdk-register.md).
- Pour obtenir la liste des autres problèmes Azure Stack corrigés dans cette version, consultez [cette section](../operator/azure-stack-release-notes-1905.md#fixes) des notes de publication Azure Stack.
- Pour obtenir la liste des problèmes connus, consultez [cet article](../operator/azure-stack-release-notes-known-issues-1905.md).
- Notez que les [correctifs logiciels d’Azure Stack](../operator/azure-stack-release-notes-1905.md#hotfixes) ne sont pas applicables au kit ASDK Azure Stack.

## <a name="build-11904036"></a>Build 1.1904.0.36

<!-- ### Changes -->

### <a name="new-features"></a>Nouvelles fonctionnalités

- Pour obtenir la liste des nouvelles fonctionnalités dans cette version, consultez [cette section](../operator/azure-stack-release-notes-1904.md#whats-in-this-update) des notes de publication Azure Stack.

### <a name="fixed-and-known-issues"></a>Problèmes connus et résolus

- En raison d’un délai d’expiration du principal du service lors de l’exécution du script d’inscription, vous devez, pour réussir à [inscrire le kit ASDK](asdk-register.md), modifier le script PowerShell **RegisterWithAzure.psm1**. Effectuez les actions suivantes :

  1. Sur l’ordinateur hôte du kit ASDK, ouvrez le fichier **C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1** dans un éditeur avec élévation de privilèges.
  2. À la fin de la ligne 1249, ajoutez un paramètre `-TimeoutInSeconds 1800`. Cet ajout est exigé en raison d’un délai d’expiration du principal du service lors de l’exécution du script d’inscription. La ligne 1249 doit maintenant ressembler à ceci :

     ```powershell
      $servicePrincipal = Invoke-Command -Session $PSSession -ScriptBlock { New-AzureBridgeServicePrincipal -RefreshToken $using:RefreshToken -AzureEnvironment $using:AzureEnvironmentName -TenantId $using:TenantId -TimeoutInSeconds 1800 }
      ```

- Résolution du problème de connexion VPN identifié dans la version 1902.

- Pour obtenir la liste des autres problèmes Azure Stack corrigés dans cette version, consultez [cette section](../operator/azure-stack-release-notes-1904.md#fixes) des notes de publication Azure Stack.
- Pour obtenir la liste des problèmes connus, consultez [cet article](../operator/azure-stack-release-notes-known-issues-1904.md).
- Notez que les [correctifs logiciels d’Azure Stack](../operator/azure-stack-release-notes-1904.md#hotfixes) ne sont pas applicables au kit ASDK Azure Stack.

