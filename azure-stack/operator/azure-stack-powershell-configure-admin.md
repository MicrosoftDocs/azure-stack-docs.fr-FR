---
title: Connexion à Azure Stack en tant qu’opérateur à l’aide de PowerShell | Microsoft Docs
description: Apprenez à vous connecter à Azure Stack en tant qu’opérateur à l’aide de PowerShell
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
origin.date: 03/15/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: thoroet
ms.lastreviewed: 01/24/2019
ms.openlocfilehash: 9d49727538f89e9429c1ae979057e89c40dc0ce9
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "64308225"
---
# <a name="connect-to-azure-stack-with-powershell-as-an-operator"></a>Connectez-vous à Azure Stack en tant qu’opérateur à l’aide de PowerShell

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Vous pouvez configurer Azure Stack pour utiliser PowerShell en vue de gérer les ressources Azure Stack, par exemple créer des offres, des plans, des quotas et des alertes. Cette rubrique vous permet de configurer l’environnement de l’opérateur.

## <a name="prerequisites"></a>Prérequis

Effectuez les prérequis suivants à partir du [kit de développement](../asdk/asdk-connect.md#connect-with-rdp) ou à partir d’un client externe Windows si vous êtes [connecté à l’ASDK via un VPN (réseau privé virtuel)](../asdk/asdk-connect.md#connect-with-vpn). 

 - Installez les [modules Azure PowerShell compatibles avec Azure Stack](azure-stack-powershell-install.md).  
 - Téléchargez les [outils nécessaires pour utiliser Azure Stack](azure-stack-powershell-download.md).  

## <a name="connect-with-azure-ad"></a>Connexion à Azure AD

Configurez l’environnement de l’opérateur Azure Stack avec PowerShell. Exécutez l’un des scripts suivants : Remplacez le tenantName Azure Active Directory (Azure AD) et les valeurs de point de terminaison Azure Resource Manager par la configuration de votre propre environnement. 

```powershell  
    # Register an Azure Resource Manager environment that targets your Azure Stack instance. Get your Azure Resource Manager endpoint value from your service provider.
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

    # Set your tenant name
    $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackAdmin").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.partner.onmschina.cn"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantId
```

## <a name="connect-with-ad-fs"></a>Connexion à AD FS

Connectez-vous à l’environnement d’opérateur Azure Stack avec PowerShell avec Azure Active Directory Federated Services (Azure AD FS). Pour le kit de développement Azure Stack, ce point de terminaison Azure Resource Manager est défini sur `https://adminmanagement.local.azurestack.external`. Pour obtenir le point de terminaison Azure Resource Manager pour les systèmes intégrés Azure Stack, contactez votre fournisseur de services.


  ```powershell  
  # Register an Azure Resource Manager environment that targets your Azure Stack instance. Get your Azure Resource Manager endpoint value from your service provider.
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

  # Sign in to your environment
  Login-AzureRmAccount -EnvironmentName "AzureStackAdmin"
  ```

> [!Note]  
> AD FS prend en charge uniquement l’authentification interactive avec des identités d’utilisateurs. Si un objet d’identification est nécessaire, vous devez utiliser un SPN (nom de principal du service). Pour plus d'informations sur la configuration d'un principal de service avec Azure Stack et AD FS en tant que service de gestion des identités, consultez [Gérer le principal de service pour AD FS](azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs).

## <a name="test-the-connectivity"></a>Tester la connectivité

Vous avez terminé l’installation. Utilisez PowerShell pour créer des ressources dans Azure Stack. Par exemple, vous pouvez créer un groupe de ressources pour une application et ajouter une machine virtuelle. Utilisez la commande suivante pour créer le groupe de ressources nommé **myResourceGroup**.

```powershell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Étapes suivantes

- [Développer des modèles pour Azure Stack](../user/azure-stack-develop-templates.md)
- [Déployer des modèles avec PowerShell](../user/azure-stack-deploy-template-powershell.md)
  - [Informations de référence sur les modules Azure Stack](https://docs.microsoft.com/powershell/azure/azure-stack/overview)  
