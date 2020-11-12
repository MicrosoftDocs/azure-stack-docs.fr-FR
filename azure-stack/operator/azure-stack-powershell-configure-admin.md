---
title: Se connecter à Azure Stack Hub avec PowerShell
description: Apprenez à vous connecter à Azure Stack Hub avec PowerShell.
author: mattbriggs
ms.topic: article
ms.date: 10/19/2020
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 10/19/2020
ms.openlocfilehash: d99212c63e33060fbbb8eb483dd32e7c01d54ba1
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94545140"
---
# <a name="connect-to-azure-stack-hub-with-powershell"></a>Se connecter à Azure Stack Hub avec PowerShell

Vous pouvez configurer Azure Stack Hub de façon à utiliser PowerShell pour gérer les ressources, par exemple, pour créer des offres, des plans, des quotas et des alertes. Cette rubrique vous permet de configurer l’environnement de l’opérateur.

## <a name="prerequisites"></a>Prérequis

Effectuez les prérequis suivants à partir du [kit de développement Azure Stack (ASDK)](../asdk/asdk-connect.md#connect-with-rdp) ou à partir d’un client externe Windows si vous êtes [connecté au Kit de développement Azure Stack via un VPN (réseau privé virtuel)](../asdk/asdk-connect.md#connect-with-vpn).

- Installez les [modules Azure PowerShell compatibles avec Azure Stack Hub](powershell-install-az-module.md).  
- Téléchargez les [outils nécessaires pour utiliser Azure Stack Hub](azure-stack-powershell-download.md).  

## <a name="connect-with-azure-ad"></a>Connexion à Azure AD

Pour configurer l’environnement d’opérateur Azure Stack Hub avec PowerShell, exécutez l’un des scripts ci-dessous. Remplacez le tenantName Azure Active Directory (Azure AD) et les valeurs de point de terminaison Azure Resource Manager par la configuration de votre propre environnement.

[!include[Remove Account](../../includes/remove-account.md)]

```powershell  
    # Register an Azure Resource Manager environment that targets your Azure Stack Hub instance. Get your Azure Resource Manager endpoint value from your service provider.
    Add-AzEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

    # Set your tenant name.
    $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackAdmin").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack Hub cmdlets
    # can be easily targeted at your Azure Stack Hub instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantId
```

## <a name="connect-with-ad-fs"></a>Connexion à AD FS

Connectez-vous à l’environnement d’opérateur Azure Stack Hub à l’aide de PowerShell avec les services de fédération Active Directory (AD FS). Pour le Kit de développement Azure Stack, ce point de terminaison Azure Resource Manager est défini sur `https://adminmanagement.local.azurestack.external`. Pour obtenir le point de terminaison Azure Resource Manager pour des systèmes intégrés Azure Stack Hub, contactez votre fournisseur de services.

  ```powershell  
  # Register an Azure Resource Manager environment that targets your Azure Stack Hub instance. Get your Azure Resource Manager endpoint value from your service provider.
    Add-AzEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

  # Sign in to your environment.
  Login-AzureRmAccount -EnvironmentName "AzureStackAdmin"
  ```

[!Include [AD FS only supports interactive authentication with user identities](../includes/note-powershell-adfs.md)]

## <a name="test-the-connectivity"></a>Tester la connectivité

Vous avez terminé l’installation. Utilisez PowerShell pour créer des ressources dans Azure Stack Hub. Par exemple, vous pouvez créer un groupe de ressources pour une application et ajouter une machine virtuelle. Utilisez la commande suivante pour créer le groupe de ressources nommé **myResourceGroup**.

```powershell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Étapes suivantes

- [Développer des modèles pour Azure Stack Hub](../user/azure-stack-develop-templates.md).
- [Déployer des modèles avec PowerShell](../user/azure-stack-deploy-template-powershell.md).
  - [Informations de référence sur le module Azure Stack Hub](/powershell/azure/azure-stack/overview).
