---
title: Utiliser PowerShell pour gérer les abonnements, les plans et les offres dans Azure Stack Hub
description: Découvrez comment utiliser PowerShell pour gérer les abonnements, les plans et les offres dans Azure Stack Hub.
author: PatAltimore
ms.topic: how-to
ms.date: 12/18/2020
ms.author: patricka
ms.lastreviewed: 12/18/2020
ms.reviewer: bganapa
ms.openlocfilehash: 3dcc3eae565f2e2a41078c2696a09f60ffa618b9
ms.sourcegitcommit: e13f27291bab236aac5d8b05401056961e9cc1e9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/19/2020
ms.locfileid: "97697864"
---
# <a name="use-powershell-to-manage-subscriptions-plans-and-offers-in-azure-stack-hub"></a>Utiliser PowerShell pour gérer les abonnements, les plans et les offres dans Azure Stack Hub

À l’aide de PowerShell, vous pouvez configurer et fournir des services en utilisant des offres, des plans et des abonnements. avec PowerShell. Pour obtenir des instructions sur la configuration de PowerShell sur Azure Stack Hub, consultez [Installer le module PowerShell Az pour Azure Stack Hub](powershell-install-az-module.md). Pour des informations sur la connexion à Azure Stack Hub à l’aide de PowerShell, consultez [Se connecter à Azure Stack Hub avec PowerShell](azure-stack-powershell-configure-admin.md).

Avant de commencer, vérifiez que le module PowerShell Azure Stack Hub est chargé. Dans une console PowerShell, tapez `Import-Module AzureStack`.

## <a name="create-a-plan"></a>Créer un plan

Vous devez définir des quotas quand vous créez un plan. Vous pouvez choisir des quotas existants ou créer d’autres quotas. Par exemple, pour créer des quotas de stockage, de calcul et de réseau, utilisez les applets de commande [New-AzsStorageQuota](/powershell/module/azs.storage.admin/new-azsstoragequota), [New-AzsComputeQuota](/powershell/module/azs.compute.admin/new-azscomputequota) et [New-AzsNetworkQuota](/powershell/module/azs.network.admin/new-azsnetworkquota) :

```powershell
$serviceQuotas  = @()
$serviceQuotas += (New-AzsStorageQuota -Name "Example storage quota with defaults").Id
$serviceQuotas += (New-AzsComputeQuota -Name "Example compute quota with defaults").Id
$serviceQuotas += (New-AzsNetworkQuota -Name "Example network quota with defaults").Id
```

Pour créer ou mettre à jour un plan de base ou d’extension, utilisez [New-AzsPlan](/powershell/module/azs.subscriptions.admin/new-azsplan).

```powershell
$testPlan = New-AzsPlan -Name "testplan" -ResourceGroupName "testrg" -QuotaIds $serviceQuotas -Description "Test plan"
```

## <a name="create-an-offer"></a>Créer une offre

Pour créer une offre, utilisez [New-AzsOffer](/powershell/module/azs.subscriptions.admin/new-azsoffer).

```powershell
New-AzsOffer -Name "testoffer" -ResourceGroupName "testrg" -BasePlanIds @($testPlan.Id)
```

Une fois que vous avez créé une offre, vous pouvez y ajouter des plans. Utilisez [Add-AzsPlanToOffer](/powershell/module/azs.subscriptions.admin/add-azsplantooffer). Le paramètre **-PlanLinkType** spécifie le type de plan.

```powershell
Add-AzsPlanToOffer -PlanName "addonplan" -PlanLinkType Addon -OfferName "testoffer" -ResourceGroupName "testrg" -MaxAcquisitionCount 18
```

Si vous souhaitez changer l’état d’une offre, utilisez l’applet de commande [Set-AzsOffer](/powershell/module/azs.subscriptions.admin/set-azsoffer).

```powershell
$offer = Get-AzsAdminManagedOffer -Name "testoffer" -ResourceGroupName "testrg"
$offer.state = "Public"
$offer | Set-AzsOffer -Confirm:$false
```

## <a name="create-subscription-to-an-offer"></a>Créer un abonnement à une offre

Après avoir créé une offre, les utilisateurs ont besoin d’un abonnement à cette offre pour pouvoir l’utiliser. Les utilisateurs peuvent s’abonner à une offre de deux façons :

* En tant qu’opérateur cloud, vous pouvez créer un abonnement pour un utilisateur. Les abonnements créés peuvent être destinés à des offres publiques et privées.
* En tant qu’utilisateur, vous pouvez vous abonner à une offre publique.

Pour créer un abonnement pour un utilisateur en tant qu’opérateur cloud, utilisez [New-AzsUserSubscription](/powershell/module/azs.subscriptions.admin/new-azsusersubscription).

```powershell
New-AzsUserSubscription -Owner "user@contoso.com" -DisplayName "User subscription" -OfferId "/subscriptions/<Subscription ID>/resourceGroups/testrg/providers/Microsoft.Subscriptions.Admin/offers/testoffer"
```

Pour vous abonner à une offre publique en tant qu’utilisateur, utilisez [New-AzsSubscription](/powershell/module/azs.subscriptions/new-azssubscription). *New-AzsSubscription* requiert une connexion à l’environnement Azure Resource Manager de l’utilisateur. Effectuez les étapes décrites dans [Se connecter à Azure Stack Hub avec PowerShell](azure-stack-powershell-configure-admin.md), mais en utilisant le point de terminaison Azure Resource Manager de l’utilisateur. Par exemple : `Add-AzEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"`.

```powershell
$testOffer = Get-AzsOffer | Where-Object Name -eq "testoffer"
New-AzsSubscription -OfferId "User subscription" -OfferId $testOffer.Id -DisplayName "My subscription"
```

## <a name="delete-quotas-plans-offers-and-subscriptions"></a>Supprimer des quotas, plans, offres et abonnements

Plusieurs applets de commande PowerShell sont fournies pour supprimer des quotas, plans, offres et abonnements dans Azure Stack Hub. Les exemples suivants illustrent chacune de ces applets.

Utilisez [Remove-AzsUserSubscription](/powershell/module/azs.subscriptions.admin/remove-azsusersubscription) pour supprimer un abonnement d’une offre.

```powershell
Remove-AzsUserSubscription -TargetSubscriptionId "c90173b1-de7a-4b1d-8600-b8325ca1eab1e"
```

Pour supprimer un plan d’une offre, utilisez [Remove-AzsPlanFromOffer](/powershell/module/azs.subscriptions.admin/remove-azsplanfromoffer).

```powershell
Remove-AzsPlanFromOffer -PlanName "addonplan" -PlanLinkType Addon -OfferName "testoffer" -ResourceGroupName "testrg"
Remove-AzsPlanFromOffer -PlanName "testplan" -PlanLinkType Base -OfferName "testoffer" -ResourceGroupName "testrg"
```

Utilisez [Remove-AzsPlan](/powershell/module/azs.subscriptions.admin/remove-azsplan) pour supprimer un plan.

```powershell
Remove-AzsPlan -Name "testplan" -ResourceGroupName "testrg"
```

Utilisez [Remove-AzsOffer](/powershell/module/azs.subscriptions.admin/remove-azsoffer) pour supprimer une offre.

```powershell
Remove-AzsOffer -Name "testoffer" -ResourceGroupName "testrg"
```

Pour supprimer des quotas, utilisez [Remove-AzsStorageQuota](/powershell/module/azs.storage.admin/remove-azsstoragequota), [Remove-AzsComputeQuota](/powershell/module/azs.compute.admin/remove-azscomputequota) et [Remove-AzsNetworkQuota](/powershell/module/azs.network.admin/remove-azsnetworkquota).

```powershell
Remove-AzsStorageQuota -Name "Example storage quota with defaults"
Remove-AzsComputeQuota -Name "Example compute quota with defaults"
Remove-AzsNetworkQuota -Name "Example network quota with defaults"
```

## <a name="next-steps"></a>Étapes suivantes

- [Gestion des mises à jour dans Azure Stack Hub](./azure-stack-updates.md)
