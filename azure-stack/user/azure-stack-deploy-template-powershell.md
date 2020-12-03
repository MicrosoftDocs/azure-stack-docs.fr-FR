---
title: Déployer un modèle à l’aide de PowerShell dans Azure Stack Hub
description: Déployez un modèle à l’aide de PowerShell dans Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: d2ba72b65f8c8b1efe11356b217bd6beb218a375
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96525334"
---
# <a name="deploy-a-template-using-powershell-in-azure-stack-hub"></a>Déployer un modèle à l’aide de PowerShell dans Azure Stack Hub

Vous pouvez utiliser PowerShell pour déployer des modèles Azure Resource Manager dans Azure Stack Hub. Cet article montre comment utiliser PowerShell pour déployer un modèle.

## <a name="run-powershell-cmdlets"></a>Exécuter des applets de commande PowerShell

### <a name="az-modules"></a>[Modules Az](#tab/az)

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

### <a name="azurerm-modules"></a>[Modules AzureRM](#tab/azurerm)

Cet exemple utilise les applets de commande **AzureRM** PowerShell et un modèle stocké sur GitHub. Le modèle crée une machine virtuelle Windows Server 2012 R2 Datacenter.

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
    New-AzureRMResourceGroup -Name $RGName -Location $myLocation

    # Deploy simple IaaS template
    New-AzureRMResourceGroupDeployment `
        -Name myDeployment$myNum `
        -ResourceGroupName $RGName `
        -TemplateUri <path>\AzureStack-QuickStart-Templates\101-vm-windows-create\azuredeploy.json `
        -AdminUsername <username> `
        -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force)
    ```

    >[!IMPORTANT]
    > Chaque fois que vous exécutez ce script, augmentez la valeur du paramètre `$myNum` pour éviter d’écraser votre déploiement.

4. Ouvrez le portail Azure Stack Hub, sélectionnez **Parcourir**, puis **Machines virtuelles** pour rechercher votre nouvelle machine virtuelle (**myDeployment001**).

---
## <a name="cancel-a-running-template-deployment"></a>Annuler le déploiement d’un modèle en cours d’exécution

Pour annuler le déploiement d’un modèle en cours d’exécution, utilisez la cmdlet PowerShell `Stop-AzResourceGroupDeployment`.

## <a name="next-steps"></a>Étapes suivantes

- [Déployer un modèle avec Visual Studio](azure-stack-deploy-template-visual-studio.md)
