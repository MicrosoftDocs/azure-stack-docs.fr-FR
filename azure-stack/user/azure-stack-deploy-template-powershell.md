---
title: Déployer un modèle à l’aide de PowerShell dans Azure Stack Hub
description: Déployez un modèle à l’aide de PowerShell dans Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/23/2019
ms.openlocfilehash: a5bd582cd93a95f662a8acc2094e6a62a7ecdf50
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94546903"
---
# <a name="deploy-a-template-using-powershell-in-azure-stack-hub"></a>Déployer un modèle à l’aide de PowerShell dans Azure Stack Hub

Vous pouvez utiliser PowerShell pour déployer des modèles Azure Resource Manager dans Azure Stack Hub. Cet article montre comment utiliser PowerShell pour déployer un modèle.

## <a name="run-az-powershell-cmdlets"></a>Exécuter des cmdlets Az PowerShell

Cet exemple utilise les cmdlets **Az** PowerShell et un modèle stocké sur GitHub. Le modèle crée une machine virtuelle Windows Server 2012 R2 Datacenter.

>[!NOTE]
> Avant d’essayer cet exemple, vérifiez que vous avez [configuré PowerShell](azure-stack-powershell-configure-user.md) pour un utilisateur Azure Stack Hub.

1. Accédez au [dépôt AzureStack-QuickStart-Templates](https://aka.ms/AzureStackGitHub) et recherchez le modèle **101-simple-windows-vm**. Enregistrez le modèle à cet emplacement : `C:\templates\azuredeploy-101-simple-windows-vm.json`.
2. Ouvrez une invite de commandes PowerShell avec élévation de privilèges.
3. Remplacez `username` et `password` dans le script suivant par votre nom d’utilisateur et votre mot de passe, puis exécutez le script :

    ```powershell
    # Set deployment variables
    $myNum = "001" # Modify this per deployment
    $RGName = "myRG$myNum"
    $myLocation = "yourregion" # local for the ASDK

    # Create resource group for template deployment
    New-AzResourceGroup -Name $RGName -Location $myLocation

    # Deploy simple IaaS template
    New-AzResourceGroupDeployment `
        -Name myDeployment$myNum `
        -ResourceGroupName $RGName `
        -TemplateUri <path>\AzureStack-QuickStart-Templates\101-vm-windows-create\azuredeploy.json `
        -AdminUsername <username> `
        -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force)
    ```

    >[!IMPORTANT]
    > Chaque fois que vous exécutez ce script, augmentez la valeur du paramètre `$myNum` pour éviter d’écraser votre déploiement.

4. Ouvrez le portail Azure Stack Hub, sélectionnez **Parcourir**, puis **Machines virtuelles** pour rechercher votre nouvelle machine virtuelle (**myDeployment001**).

## <a name="cancel-a-running-template-deployment"></a>Annuler le déploiement d’un modèle en cours d’exécution

Pour annuler le déploiement d’un modèle en cours d’exécution, utilisez la cmdlet PowerShell `Stop-AzResourceGroupDeployment`.

## <a name="next-steps"></a>Étapes suivantes

- [Déployer un modèle avec Visual Studio](azure-stack-deploy-template-visual-studio.md)
