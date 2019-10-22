---
title: Déployer un modèle à l’aide de PowerShell dans Azure Stack | Microsoft Docs
description: Déployez un modèle à l’aide de PowerShell dans Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/23/2019
ms.openlocfilehash: 38c3c428443afa251c8a6185929bfe0b80680b86
ms.sourcegitcommit: 7226979ece29d9619c959b11352be601562b41d3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2019
ms.locfileid: "72304063"
---
# <a name="deploy-a-template-using-powershell-in-azure-stack"></a>Déployer un modèle à l’aide de PowerShell dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Vous pouvez utiliser PowerShell pour déployer des modèles Azure Resource Manager dans Azure Stack. Cet article montre comment utiliser PowerShell pour déployer un modèle.

## <a name="run-azurerm-powershell-cmdlets"></a>Exécuter les applets de commande AzureRM PowerShell

Cet exemple utilise les applets de commande **AzureRM** PowerShell et un modèle stocké sur GitHub. Le modèle crée une machine virtuelle Windows Server 2012 R2 Datacenter.

>[!NOTE]
> Avant d’essayer cet exemple, vérifiez que vous avez [configuré PowerShell](azure-stack-powershell-configure-user.md) pour un utilisateur Azure Stack.

1. Accédez au [dépôt AzureStack-QuickStart-Templates](https://aka.ms/AzureStackGitHub) et recherchez le modèle **101-simple-windows-vm**. Enregistrez le modèle à cet emplacement : `C:\templates\azuredeploy-101-simple-windows-vm.json`.
2. Ouvrez une invite de commandes PowerShell avec élévation de privilèges.
3. Remplacez `username` et `password` dans le script suivant par votre nom d’utilisateur et votre mot de passe, puis exécutez le script :

    ```powershell
    # Set deployment variables
    $myNum = "001" # Modify this per deployment
    $RGName = "myRG$myNum"
    $myLocation = "yourregion" # local for the ASDK

    # Create resource group for template deployment
    New-AzureRmResourceGroup -Name $RGName -Location $myLocation

    # Deploy simple IaaS template
    New-AzureRmResourceGroupDeployment `
        -Name myDeployment$myNum `
        -ResourceGroupName $RGName `
        -TemplateUri <path>\AzureStack-QuickStart-Templates\101-vm-windows-create\azuredeploy.json `
        -AdminUsername <username> `
        -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force)
    ```

    >[!IMPORTANT]
    > Chaque fois que vous exécutez ce script, augmentez la valeur du paramètre `$myNum` pour éviter d’écraser votre déploiement.

4. Ouvrez le portail Azure Stack portal, sélectionnez **Parcourir**, puis sélectionnez **Machines virtuelles** pour rechercher votre nouvelle machine virtuelle (**myDeployment001**).

## <a name="cancel-a-running-template-deployment"></a>Annuler le déploiement d’un modèle en cours d’exécution

Pour annuler le déploiement d’un modèle en cours d’exécution, utilisez la cmdlet PowerShell `Stop-AzureRmResourceGroupDeployment`.

## <a name="next-steps"></a>Étapes suivantes

- [Déployer un modèle avec Visual Studio](azure-stack-deploy-template-visual-studio.md)
