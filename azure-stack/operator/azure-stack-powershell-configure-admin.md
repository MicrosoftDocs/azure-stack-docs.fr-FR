---
title: Se connecter à Azure Stack Hub avec PowerShell
description: Apprenez à vous connecter à Azure Stack Hub avec PowerShell.
author: mattbriggs
ms.topic: article
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 2/1/2021
ms.openlocfilehash: ab38cc4b00da140a529df43ac49789d7eab80327
ms.sourcegitcommit: e88f0a1f2f4ed3bb8442bfb7b754d8b3a51319b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99534179"
---
# <a name="connect-to-azure-stack-hub-with-powershell"></a>Se connecter à Azure Stack Hub avec PowerShell

Vous pouvez configurer Azure Stack Hub de façon à utiliser PowerShell pour gérer les ressources, par exemple, pour créer des offres, des plans, des quotas et des alertes. Cette rubrique vous permet de configurer l’environnement de l’opérateur.

## <a name="prerequisites"></a>Prérequis

Effectuez les prérequis suivants à partir du [kit de développement Azure Stack (ASDK)](../asdk/asdk-connect.md#connect-with-rdp) ou à partir d’un client externe Windows si vous êtes [connecté au Kit de développement Azure Stack via un VPN (réseau privé virtuel)](../asdk/asdk-connect.md#connect-with-vpn).

- Installez les [modules Azure PowerShell compatibles avec Azure Stack Hub](powershell-install-az-module.md).  
- Téléchargez les [outils nécessaires pour utiliser Azure Stack Hub](azure-stack-powershell-download.md).  

## <a name="connect-with-azure-ad"></a>Connexion à Azure AD

Pour configurer l’environnement d’opérateur Azure Stack Hub avec PowerShell, exécutez l’un des scripts ci-dessous. Remplacez le tenantName Azure Active Directory (Azure AD) et les valeurs de point de terminaison Azure Resource Manager par la configuration de votre propre environnement.

### <a name="az-modules"></a>[Modules Az](#tab/az1)

[!include[Remove Account](../includes/remove-account-az.md)]

```powershell  
    # Register an Azure Resource Manager environment that targets your Azure Stack Hub instance. Get your Azure Resource Manager endpoint value from your service provider.
    Add-AzEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

    # Set your tenant name.
    $AuthEndpoint = (Get-AzEnvironment -Name "AzureStackAdmin").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack Hub cmdlets
    # can be easily targeted at your Azure Stack Hub instance.
    Connect-AzAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantId
```
### <a name="azurerm-modules"></a>[Modules AzureRM](#tab/azurerm1)

[!include[Remove Account](../includes/remove-account-azurerm.md)]

```powershell  
    # Register an Azure Resource Manager environment that targets your Azure Stack Hub instance. Get your Azure Resource Manager endpoint value from your service provider.
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
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

---


## <a name="connect-with-ad-fs"></a>Connexion à AD FS

Connectez-vous à l’environnement d’opérateur Azure Stack Hub à l’aide de PowerShell avec les services de fédération Active Directory (AD FS). Pour le Kit de développement Azure Stack, ce point de terminaison Azure Resource Manager est défini sur `https://adminmanagement.local.azurestack.external`. Pour obtenir le point de terminaison Azure Resource Manager pour des systèmes intégrés Azure Stack Hub, contactez votre fournisseur de services.

### <a name="az-modules"></a>[Modules Az](#tab/az2)

  ```powershell  
  # Register an Azure Resource Manager environment that targets your Azure Stack Hub instance. Get your Azure Resource Manager endpoint value from your service provider.
    Add-AzEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

  # Sign in to your environment.
  Connect-AzAccount -EnvironmentName "AzureStackAdmin"
  ```

### <a name="azurerm-modules"></a>[Modules AzureRM](#tab/azurerm2)

```powershell  
# Register an Azure Resource Manager environment that targets your Azure Stack Hub instance. Get your Azure Resource Manager endpoint value from your service provider.
  Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
    -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
    -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

# Sign in to your environment.
Login-AzureRmAccount -EnvironmentName "AzureStackAdmin"
```

---

[!Include [AD FS only supports interactive authentication with user identities](../includes/note-powershell-adfs.md)]

## <a name="test-the-connectivity"></a>Tester la connectivité

Vous avez terminé l’installation. Utilisez PowerShell pour créer des ressources dans Azure Stack Hub. Par exemple, vous pouvez créer un groupe de ressources pour une application et ajouter une machine virtuelle. Utilisez la commande suivante pour créer le groupe de ressources nommé **myResourceGroup**.

### <a name="az-modules"></a>[Modules Az](#tab/az3)
```powershell  
New-AzResourceGroup -Name "MyResourceGroup" -Location "Local"
```

### <a name="azurerm-modules"></a>[Modules AzureRM](#tab/azurerm3)

```powershell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

---


## <a name="next-steps"></a>Étapes suivantes

- [Utiliser PowerShell pour gérer les abonnements, les plans et les offres dans Azure Stack Hub](azure-stack-powershell-plan-offer.md)
- [Développer des modèles pour Azure Stack Hub](../user/azure-stack-develop-templates.md).
- [Déployer des modèles avec PowerShell](../user/azure-stack-deploy-template-powershell.md).
- [Informations de référence sur le module Azure Stack Hub](/powershell/azure/azure-stack/overview).
