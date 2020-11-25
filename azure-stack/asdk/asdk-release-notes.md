---
title: Notes de publication relatives à l’ASDK
description: Améliorations, correctifs et problèmes connus pour le Kit de développement Azure Stack (ASDK).
author: sethmanheim
ms.topic: article
ms.date: 11/16/2020
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 08/10/2020
ms.openlocfilehash: 6eb124193f477eb4418909f6b8e79781e948d135
ms.sourcegitcommit: 6db48bd8e6ccfaaa897713ad7eb2846a8d506358
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94885748"
---
# <a name="asdk-release-notes"></a>Notes de publication relatives à l’ASDK

Cet article fournit des informations sur des modifications, des correctifs et des problèmes connus en lien avec le Kit de développement Azure Stack (ASDK). Si vous n’êtes pas sûr de la version que vous exécutez, consultez le [portail pour vérifier](../operator/azure-stack-updates.md).

Tenez-vous informé des nouveautés concernant le kit ASDK en vous abonnant au [flux RSS](https://aka.ms/asdkrss) ![RSS](./media/asdk-release-notes/feed-icon-14x14.png).

::: moniker range="azs-2008"
## <a name="build-12008059"></a>Build 1.2008.0.59

### <a name="new-features"></a>Nouvelles fonctionnalités

- Pour obtenir la liste des problèmes résolus, des modifications et des nouvelles fonctionnalités de cette version, consultez les sections correspondantes dans les [notes de publication Azure Stack](../operator/release-notes.md).

### <a name="fixed-and-known-issues"></a>Problèmes connus et résolus

::: moniker-end

::: moniker range="azs-2005"
## <a name="build-12005040"></a>Build 1.2005.0.40

### <a name="new-features"></a>Nouvelles fonctionnalités

- Pour obtenir la liste des problèmes résolus, des modifications et des nouvelles fonctionnalités de cette version, consultez les sections correspondantes dans les [notes de publication Azure Stack](../operator/release-notes.md).

### <a name="fixed-and-known-issues"></a>Problèmes connus et résolus

- Le mot de passe de certification de déchiffrement est une nouvelle option qui permet de spécifier le mot de passe destiné au certificat auto-signé (.pfx) contenant la clé privée nécessaire au déchiffrement des données de sauvegarde. Ce mot de passe est obligatoire uniquement si la sauvegarde est chiffrée à l’aide d’un certificat.
- Correction d’un problème qui provoquait l’échec de la récupération du cloud si le mot de passe du certificat externe d’origine a été modifié sur le système source à plusieurs nœuds. 
- Pour obtenir la liste des problèmes connus d’Azure Stack de cette version, consultez l’article sur les [problèmes connus](../operator/known-issues.md).
- Notez que les correctifs logiciels d’Azure Stack ne sont pas applicables au kit ASDK.

#### <a name="initial-configuration-fails-in-asdk"></a>Échec de la configuration initiale pour ASDK

- Lors du déploiement d’ASDK, vous pouvez recevoir les messages d’erreur **L’état de « Deployment-Phase0-DeployBareMetal » est « Erreur »** et **L’état de « Deployment-InitialSteps » est « Erreur »** .

- Solution de contournement :

1. Ouvrez le fichier sur C:\CloudDeployment\Roles\PhysicalMachines\Tests\BareMetal.Tests.ps1 dans n’importe quel éditeur avec un compteur de ligne, tel que PowerShell ISE.

2. Remplacez la ligne 822 par :

   ```powershell

   PartNumber = if($_.PartNumber) {$_.PartNumber.Trim()} else {""};

   ```  
::: moniker-end

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
