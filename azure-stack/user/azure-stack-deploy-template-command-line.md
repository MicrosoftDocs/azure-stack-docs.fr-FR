---
title: Déployer un modèle à partir de la ligne de commande dans Azure Stack | Microsoft Docs
description: Découvrez comment utiliser l’interface de ligne de commande (CLI) multiplateforme pour déployer des modèles sur Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: article
ms.date: 05/09/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: 92c9189f8144804f36e551ab89d8b4fc4c1f8598
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2019
ms.locfileid: "66691372"
---
# <a name="deploy-a-template-with-the-command-line-in-azure-stack"></a>Déployer un modèle à partir de la ligne de commande dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Utilisez l’interface de ligne de commande Azure CLI pour déployer des modèles Azure Resource Manager dans Azure Stack. Les modèles Azure Resource Manager déploient et provisionnent les ressources de l’application en une seule opération coordonnée.

## <a name="before-you-begin"></a>Avant de commencer

- [Procédez à l’installation et à la connexion](azure-stack-version-profiles-azurecli2.md) à Azure Stack avec Azure CLI.
- Télécharger les fichiers *azuredeploy.json* et *azuredeploy.parameters.json* à partir du [modèle Créer un exemple de compte de stockage](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account).

## <a name="deploy-template"></a>Déployer un modèle

Accédez au dossier dans lequel ces fichiers ont été téléchargés et exécutez la commande suivante pour déployer le modèle :

```azurecli
az group create "cliRG" "local" -f azuredeploy.json -d "testDeploy" -e azuredeploy.parameters.json
```

Cette commande déploie le modèle dans le groupe de ressources **cliRG** à l’emplacement par défaut d’Azure Stack POC.

## <a name="validate-template-deployment"></a>Valider le déploiement du modèle

Pour voir ce groupe de ressources et le compte de stockage associé, utilisez les commandes CLI suivantes :

```azurecli
az group list

az storage account list
```

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur le [déploiement de modèles en utilisant PowerShell](azure-stack-deploy-template-powershell.md).
