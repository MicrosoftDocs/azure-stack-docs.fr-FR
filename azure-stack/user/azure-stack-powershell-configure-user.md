---
title: Se connecter à Azure Stack Hub en tant qu’utilisateur avec PowerShell
description: Découvrez comment vous connecter à Azure Stack Hub avec PowerShell pour utiliser l’invite interactive ou écrire des scripts.
author: mattbriggs
ms.topic: article
ms.date: 11/22/2020
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 11/22/2020
ms.openlocfilehash: bc0eb1beeec831dee64f300aba4d977546a00058
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95518260"
---
# <a name="connect-to-azure-stack-hub-with-powershell-as-a-user"></a>Se connecter à Azure Stack Hub en tant qu’utilisateur avec PowerShell

Vous pouvez vous connecter à Azure Stack Hub à l’aide de PowerShell pour gérer les ressources Azure Stack Hub. Par exemple, vous pouvez utiliser PowerShell pour vous abonner à des offres, créer des machines virtuelles et déployer des modèles Azure Resource Manager.

Pour l’installation :
  - Vérifiez que vous disposez des éléments requis.
  - Connexion à Azure Active Directory (Azure AD) ou Active Directory Federation Services (AD FS). 
  - Inscrivez des fournisseurs de ressources.
  - Testez votre connectivité.

## <a name="prerequisites-to-connecting-with-powershell"></a>Prérequis pour vous connecter avec PowerShell

Configurez les prérequis suivants à partir du [kit de développement](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) ou à partir d’un client externe Windows si vous êtes [connecté via un VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn) :

* Installez les [modules Azure PowerShell compatibles avec Azure Stack Hub](../operator/powershell-install-az-module.md).
* Téléchargez les [outils nécessaires pour utiliser Azure Stack Hub](../operator/azure-stack-powershell-download.md).

Veillez à remplacer les variables de script suivantes par des valeurs de votre configuration Azure Stack Hub :

- **Nom du locataire Azure AD**  
  Nom de votre locataire Azure AD utilisé pour gérer Azure Stack Hub. Par exemple, yourdirectory.onmicrosoft.com.
- **Point de terminaison Azure Resource Manager**  
  Pour le kit de développement Azure Stack, cette valeur est définie sur `https://management.local.azurestack.external`. Pour obtenir cette valeur pour les systèmes Azure Stack Hub intégrés, contactez votre fournisseur de services.

## <a name="connect-to-azure-stack-hub-with-azure-ad"></a>Se connecter à Azure Stack Hub avec Azure AD

### <a name="az-modules"></a>[Modules Az](#tab/az1)

```powershell  
    Add-AzEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"
    # Set your tenant name
    $AuthEndpoint = (Get-AzEnvironment -Name "AzureStackUser").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack Hub cmdlets
    # can be easily targeted at your Azure Stack Hub instance.
    Add-AzAccount -EnvironmentName "AzureStackUser" -TenantId $TenantId
```
### <a name="azurerm-modules"></a>[Modules AzureRM](#tab/azurerm1)
 
```powershell  
    Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"
    # Set your tenant name
    $AuthEndpoint = (Get-AzureRMEnvironment -Name "AzureStackUser").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack Hub cmdlets
    # can be easily targeted at your Azure Stack Hub instance.
    Add-AzureRMAccount -EnvironmentName "AzureStackUser" -TenantId $TenantId
```

---


## <a name="connect-to-azure-stack-hub-with-ad-fs"></a>Se connecter à Azure Stack Hub avec AD FS

### <a name="az-modules"></a>[Modules Az](#tab/az2)

  ```powershell  
  # Register an Azure Resource Manager environment that targets your Azure Stack Hub instance
  Add-AzEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"

  # Sign in to your environment
  Login-AzAccount -EnvironmentName "AzureStackUser"
  ```
### <a name="azurerm-modules"></a>[Modules AzureRM](#tab/azurerm2)
 
  ```powershell  
  # Register an Azure Resource Manager environment that targets your Azure Stack Hub instance
  Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"

  # Sign in to your environment
  Login-AzureRMAccount -EnvironmentName "AzureStackUser"
  ```

---


## <a name="register-resource-providers"></a>Inscrire des fournisseurs de ressources

Les fournisseurs de ressources ne sont pas automatiquement inscrits pour les nouveaux abonnements utilisateur qui n’ont aucune ressource déployée par le biais du portail. Vous pouvez inscrire explicitement un fournisseur de ressources en exécutant le script suivant :

### <a name="az-modules"></a>[Modules Az](#tab/az3)

```powershell  
foreach($s in (Get-AzSubscription)) {
        Select-AzSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzResourceProvider -ListAvailable | Register-AzResourceProvider
    }
```
### <a name="azurerm-modules"></a>[Modules AzureRM](#tab/azurerm3)
 
```powershell  
foreach($s in (Get-AzureRMSubscription)) {
        Select-AzureRMSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRMResourceProvider -ListAvailable | Register-AzureRMResourceProvider
    }
```

---


[!Include [AD FS only supports interactive authentication with user identities](../includes/note-powershell-adfs.md)]

## <a name="test-the-connectivity"></a>Tester la connectivité

Une fois que tout est configuré, testez la connectivité à l’aide de PowerShell pour créer des ressources dans Azure Stack Hub. À des fins de test, créez un groupe de ressources pour une application et ajoutez une machine virtuelle. Utilisez la commande suivante pour créer le groupe de ressources nommé « MyResourceGroup » :

### <a name="az-modules"></a>[Modules Az](#tab/az4)
```powershell  
New-AzResourceGroup -Name "MyResourceGroup" -Location "Local"
```

### <a name="azurerm-modules"></a>[Modules AzureRM](#tab/azurerm4)
 
```powershell  
New-AzureRMResourceGroup -Name "MyResourceGroup" -Location "Local"
```

---


## <a name="next-steps"></a>Étapes suivantes

- [Développer des modèles pour Azure Stack Hub](azure-stack-develop-templates.md)
- [Déployer des modèles avec PowerShell](azure-stack-deploy-template-powershell.md)
- [Informations de référence sur le module PowerShell Azure Stack Hub](/powershell/azure/azure-stack/overview)
- Si vous souhaitez configurer PowerShell pour l’environnement d’opérateur cloud, consultez l’article [Configurer l’environnement PowerShell de l’opérateur Azure Stack Hub](../operator/azure-stack-powershell-configure-admin.md).
