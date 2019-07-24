---
title: Déployer un modèle à l’aide de PowerShell dans Azure Stack | Microsoft Docs
description: Déployez un modèle à l’aide de PowerShell dans Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 12fe32d7-0a1a-4c02-835d-7b97f151ed0f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 6824e6bfd0b6c824783c82041fb1a51ba8f5213f
ms.sourcegitcommit: 2063332b4d7f98ee944dd1f443847eea70eb5614
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68303114"
---
# <a name="deploy-a-template-using-powershell-in-azure-stack"></a>Déployer un modèle à l’aide de PowerShell dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Vous pouvez utiliser PowerShell pour déployer des modèles Azure Resource Manager dans Azure Stack. Cet article montre comment utiliser PowerShell pour déployer un modèle.

## <a name="run-azurerm-powershell-cmdlets"></a>Exécuter les applets de commande AzureRM PowerShell

Cet exemple utilise les applets de commande **AzureRM** PowerShell et un modèle stocké sur GitHub. Le modèle crée une machine virtuelle Windows Server 2012 R2 Datacenter.

>[!NOTE]
> Avant d’essayer cet exemple, vérifiez que vous avez [configuré PowerShell](azure-stack-powershell-configure-user.md) pour un utilisateur Azure Stack.

1. Accédez au [référentiel AzureStackGitHub](https://aka.ms/AzureStackGitHub) et recherchez le modèle **101-simple-windows-vm**. Enregistrez le modèle à cet emplacement : `C:\templates\azuredeploy-101-simple-windows-vm.json`.
2. Ouvrez une invite de commandes PowerShell avec élévation de privilèges.
3. Remplacez `username` et `password` dans le script suivant par votre nom d’utilisateur et votre mot de passe, puis exécutez le script :

    ```powershell
    # Set deployment variables
    $myNum = "001" # Modify this per deployment
    $RGName = "myRG$myNum"
    $myLocation = "local"

    # Create resource group for template deployment
    New-AzureRmResourceGroup -Name $RGName -Location $myLocation

    # Deploy simple IaaS template
    New-AzureRmResourceGroupDeployment `
        -Name myDeployment$myNum `
        -ResourceGroupName $RGName `
        -TemplateFile c:\templates\azuredeploy-101-simple-windows-vm.json `
        -NewStorageAccountName mystorage$myNum `
        -DnsNameForPublicIP mydns$myNum `
        -AdminUsername <username> `
        -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force) `
        -VmName myVM$myNum `
        -WindowsOSVersion 2012-R2-Datacenter
    ```

    >[!IMPORTANT]
    > Chaque fois que vous exécutez ce script, augmentez la valeur du paramètre `$myNum` pour éviter d’écraser votre déploiement.

4. Ouvrez le portail Azure Stack portal, sélectionnez **Parcourir**, puis sélectionnez **Machines virtuelles** pour rechercher votre nouvelle machine virtuelle (**myDeployment001**).

## <a name="next-steps"></a>Étapes suivantes

- [Déployer un modèle avec Visual Studio](azure-stack-deploy-template-visual-studio.md)
