---
title: Changer le propriétaire de facturation d’un abonnement utilisateur Azure Stack Hub
description: Découvrez comment changer le propriétaire de facturation d’un abonnement utilisateur Azure Stack Hub.
author: PatAltimore
ms.topic: conceptual
ms.date: 1/29/2021
ms.author: patricka
ms.reviewer: shnatara
ms.lastreviewed: 1/29/2021
ms.openlocfilehash: 40e4fbff8a04db2a6f2218d60d548df3c75236ba
ms.sourcegitcommit: e88f0a1f2f4ed3bb8442bfb7b754d8b3a51319b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99533880"
---
# <a name="change-the-billing-owner-for-an-azure-stack-hub-user-subscription"></a>Changer le propriétaire de facturation d’un abonnement utilisateur Azure Stack Hub

Les opérateurs Azure Stack Hub peuvent utiliser PowerShell pour changer le propriétaire de facturation d’un abonnement utilisateur. Vous pouvez par exemple modifier le propriétaire pour remplacer un utilisateur qui quitte votre entreprise.

Il existe deux types de *propriétaires* qui sont affectés à un abonnement :

- **Propriétaire de facturation** : par défaut, le propriétaire de facturation est le compte d’utilisateur qui obtient l’abonnement à partir d’une offre et qui possède donc la relation de facturation pour cet abonnement. Ce compte est également un administrateur de l’abonnement. Seul un compte d’utilisateur peut avoir cette désignation pour un abonnement. Un propriétaire de facturation est souvent un responsable d’équipe ou d’entreprise.

  Vous pouvez utiliser l’applet de commande PowerShell [Set-AzsUserSubscription](/powershell/module/azs.subscriptions.admin/set-azsusersubscription) pour modifier le propriétaire de facturation.  

- **Propriétaires ajoutés via des rôles RBAC** : le rôle **Propriétaire** peut être attribué à d’autres utilisateurs par le biais du [contrôle d’accès en fonction du rôle](azure-stack-manage-permissions.md) (RBAC). Vous pouvez ajouter autant de comptes d’utilisateur que vous le souhaitez en tant que propriétaires pour assister le propriétaire de facturation. Les propriétaires supplémentaires deviennent également administrateurs de l’abonnement et ont tous les privilèges pour l’abonnement, sauf l’autorisation nécessaire pour supprimer le propriétaire de facturation.

  Vous pouvez utiliser PowerShell pour gérer des propriétaires supplémentaires. Pour plus d’informations, consultez [cet article](/azure/role-based-access-control/role-assignments-powershell).

## <a name="change-the-billing-owner"></a>Modifier le propriétaire de facturation

Exécutez le script suivant pour modifier le propriétaire de facturation d’un abonnement utilisateur. L’ordinateur que vous utilisez pour exécuter le script doit se connecter à Azure Stack Hub et exécuter le module Azure Stack Hub PowerShell version 1.3.0 ou ultérieure. Pour plus d’informations, consultez l’article [Installer Azure Stack Hub PowerShell](powershell-install-az-module.md).

>[!NOTE]
>Dans une instance Azure Stack Hub mutualisée, le nouveau propriétaire doit se trouver dans le même répertoire que le propriétaire existant. Pour pouvoir accorder la propriété de l’abonnement à un utilisateur qui se trouve dans un autre annuaire, vous devez d’abord [inviter cet utilisateur dans votre annuaire](/azure/active-directory/b2b/add-users-administrator).

Remplacez les valeurs suivantes dans le script avant de l’exécuter :

- **$ArmEndpoint** : Point de terminaison Resource Manager pour votre environnement.
- **$TenantId** : Votre ID de locataire.
- **$SubscriptionId** : Votre ID d’abonnement.
- **$OwnerUpn** : Compte, par exemple **user\@example.com**, à ajouter en tant que nouveau propriétaire de facturation.

### <a name="az-modules"></a>[Modules Az](#tab/az)

```powershell
# Set up Azure Stack Hub admin environment
Add-AzEnvironment -ARMEndpoint $ArmEndpoint -Name AzureStack-admin
Connect-AzAccount -Environment AzureStack-admin -TenantId $TenantId

# Select admin subscription
$providerSubscriptionId = (Get-AzSubscription -SubscriptionName "Default Provider Subscription").Id
Write-Output "Setting context to the Default Provider Subscription: $providerSubscriptionId"
Set-AzContext -Subscription $providerSubscriptionId

# Change user subscription owner
$subscription = Get-AzsUserSubscription -SubscriptionId $SubscriptionId
$Subscription.Owner = $OwnerUpn
Set-AzsUserSubscription -InputObject $subscription
```

[!include[Remove Account](../includes/remove-account-az.md)]

### <a name="azurerm-modules"></a>[Modules AzureRM](#tab/azurerm)

```powershell
# Set up Azure Stack Hub admin environment
Add-AzureRmEnvironment -ARMEndpoint $ArmEndpoint -Name AzureStack-admin
Add-AzureRmAccount  -Environment AzureStack-admin -TenantId $TenantId

# Select admin subscription
$providerSubscriptionId = (Get-AzureRMSubscription -SubscriptionName "Default Provider Subscription").Id
Write-Output "Setting context to the Default Provider Subscription: $providerSubscriptionId"
Set-AzureRMContext -Subscription $providerSubscriptionId

# Change user subscription owner
$subscription = Get-AzureRMsUserSubscription -SubscriptionId $SubscriptionId
$Subscription.Owner = $OwnerUpn
Set-AzureRMsUserSubscription -InputObject $subscription
```
[!include[Remove Account](../includes/remove-account-azurerm.md)]
---




## <a name="next-steps"></a>Étapes suivantes

- [Gérer le contrôle d’accès en fonction du rôle](azure-stack-manage-permissions.md)
