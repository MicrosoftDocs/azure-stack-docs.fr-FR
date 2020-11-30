---
title: Utiliser le module de stratégie Azure Stack Hub
description: Apprenez à contraindre un abonnement Azure à se comporter comme un abonnement Azure Stack Hub.
author: sethmanheim
ms.topic: article
ms.date: 11/22/2020
ms.author: sethm
ms.lastreviewed: 11/22/2020
ms.openlocfilehash: 13d3e006d676e7e24f94741c59cb8837d5200d1d
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95518124"
---
# <a name="manage-azure-policy-using-the-azure-stack-hub-policy-module"></a>Gérer la stratégie Azure à l’aide du module de stratégie Azure Stack Hub

Le module de stratégie Azure Stack Hub vous permet de configurer un abonnement Azure avec la même gestion de versions et la même disponibilité de service qu’Azure Stack Hub. Le module utilise la cmdlet PowerShell [**New-AzPolicyDefinition**](/powershell/module/Az.resources/new-Azpolicydefinition) pour créer une stratégie Azure, ce qui limite les types de ressource et les services disponibles dans un abonnement. Vous créez ensuite une affectation de stratégie dans l’étendue appropriée à l’aide de la cmdlet [**New-AzPolicyAssignment**](/powershell/module/Az.resources/new-Azpolicyassignment). Après avoir configuré la stratégie, vous pouvez utiliser votre abonnement Azure pour développer des applications ciblées pour Azure Stack Hub.

## <a name="install-the-module"></a>Installer le module

1. Installez la version nécessaire du module PowerShell Az, comme indiqué à l’étape 1 de l’article [Installer PowerShell pour Azure Stack Hub](../operator/powershell-install-az-module.md).
2. [Téléchargez les outils Azure Stack Hub à partir de GitHub](../operator/azure-stack-powershell-download.md).
3. [Configurez PowerShell pour une utilisation avec Azure Stack Hub](azure-stack-powershell-configure-user.md).
4. Importez le module **AzureStack.Policy.psm1** :

   ```powershell
   Import-Module .\Policy\AzureStack.Policy.psm1
   ```

## <a name="apply-policy-to-azure-subscription"></a>Appliquer la stratégie à l’abonnement Azure

Vous pouvez utiliser les commandes suivantes pour appliquer une stratégie Azure Stack Hub par défaut à votre abonnement Azure. Avant d’exécuter ces commandes, remplacez `Azure subscription name` par le nom de votre abonnement Azure.

### <a name="az-modules"></a>[Modules Az](#tab/az1)

```powershell
Add-AzAccount
$s = Select-AzSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID
```
### <a name="azurerm-modules"></a>[Modules AzureRM](#tab/azurerm1)

```powershell
Add-AzureRMAccount
$s = Select-AzureRMSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzureRMPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRMPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID
```

---

## <a name="apply-policy-to-a-resource-group"></a>Appliquer la stratégie à un groupe de ressources

Vous souhaiterez peut-être appliquer des stratégies plus granulaires. Par exemple, vous pourriez avoir d’autres ressources en cours d’exécution dans le même abonnement. Vous pouvez délimiter l’application de la stratégie à un groupe de ressources spécifique, ce qui vous permet de tester vos applications pour Azure Stack Hub à l’aide de ressources Azure. Avant d’exécuter les commandes suivantes, remplacez `Azure subscription name` par le nom de votre abonnement Azure :

### <a name="az-modules"></a>[Modules Az](#tab/az2)

```powershell
Add-AzAccount
$rgName = 'myRG01'
$s = Select-AzSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName
```
### <a name="azurerm-modules"></a>[Modules AzureRM](#tab/azurerm2)
 
```powershell
Add-AzureRMAccount
$rgName = 'myRG01'
$s = Select-AzureRMSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzureRMPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRMPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName
```

---

## <a name="policy-in-action"></a>Stratégie en action

Une fois que vous avez déployé la stratégie Azure, vous recevez une erreur quand vous essayez de déployer une ressource interdite par la stratégie :

![Échec de déploiement de ressources en raison de la contrainte de stratégie](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>Étapes suivantes

* [Déployer des modèles avec PowerShell](azure-stack-deploy-template-powershell.md)
* [Déployer des modèles avec l’interface de ligne de commande Azure](azure-stack-deploy-template-command-line.md)
* [Déployer des modèles avec Visual Studio](azure-stack-deploy-template-visual-studio.md)
