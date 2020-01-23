---
title: Déployer un modèle à partir de la ligne de commande dans Azure Stack Hub | Microsoft Docs
description: Découvrez comment utiliser Azure CLI multiplateforme pour déployer des modèles sur Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: unknown
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: 25479837bd73816abae92badfcd03bb21317f84c
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76536349"
---
# <a name="deploy-a-template-with-the-command-line-in-azure-stack-hub"></a>Déployer un modèle à partir de la ligne de commande dans Azure Stack Hub

Vous pouvez utiliser Azure CLI pour déployer des modèles Azure Resource Manager dans Azure Stack Hub. Les modèles Azure Resource Manager déploient et configurent les ressources de l’application en une seule action coordonnée.

## <a name="deploy-template"></a>Déployer un modèle

1. Accédez au [dépôt AzureStack-QuickStart-Templates](https://aka.ms/AzureStackGitHub) et recherchez le modèle **101-create-storage-account**. Enregistrez le modèle (`azuredeploy.json`) et les fichiers de paramètres `(azuredeploy.parameters.json`) à un emplacement sur votre disque local, par exemple `C:\templates\`
2. Accédez au dossier dans lequel vous avez téléchargé les fichiers. 
3. [Procédez à l’installation et à la connexion](azure-stack-version-profiles-azurecli2.md) à Azure Stack Hub avec Azure CLI.
4. Mettez à jour la région et l’emplacement dans la commande suivante. Utilisez `local` pour le paramètre d’emplacement si vous utilisez le kit ASDK. Pour déployer le modèle, procédez comme suit :
    ```azurecli
    az group create --name testDeploy --location local
    az group deployment create --resource-group testDeploy --template-file ./azuredeploy.json --parameters ./azuredeploy.parameters.json
    ```

Cette commande déploie le modèle sur le groupe de ressources **testDeploy** dans votre instance Azure Stack Hub.

## <a name="validate-template-deployment"></a>Valider le déploiement du modèle

Pour examiner le groupe de ressources et le compte de stockage, exécutez les commandes CLI suivantes :

```azurecli
az group list
az storage account list
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [déployer des modèles à l’aide de PowerShell](azure-stack-deploy-template-powershell.md).
