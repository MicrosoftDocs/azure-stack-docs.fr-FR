---
title: Changer le propriétaire de facturation d’un abonnement utilisateur Azure Stack Hub
description: Découvrez comment changer le propriétaire de facturation d’un abonnement utilisateur Azure Stack Hub.
author: justinha
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: justinha
ms.reviewer: shnatara
ms.lastreviewed: 10/19/2019
ms.openlocfilehash: b02795cf0058a9d63947c6143b0721c544ff9811
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "77701579"
---
# <a name="change-the-billing-owner-for-an-azure-stack-hub-user-subscription"></a>Changer le propriétaire de facturation d’un abonnement utilisateur Azure Stack Hub

Les opérateurs Azure Stack Hub peuvent utiliser PowerShell pour changer le propriétaire de facturation d’un abonnement utilisateur. Vous pouvez par exemple modifier le propriétaire pour remplacer un utilisateur qui quitte votre entreprise.

Il existe deux types de *propriétaires* qui sont affectés à un abonnement :

- **Propriétaire de facturation** : par défaut, le propriétaire de facturation est le compte d’utilisateur qui obtient l’abonnement à partir d’une offre et qui possède donc la relation de facturation pour cet abonnement. Ce compte est également un administrateur de l’abonnement. Seul un compte d’utilisateur peut avoir cette désignation pour un abonnement. Un propriétaire de facturation est souvent un responsable d’équipe ou d’entreprise.

  Vous pouvez utiliser l’applet de commande PowerShell [Set-AzsUserSubscription](/powershell/module/azs.subscriptions.admin/set-azsusersubscription) pour modifier le propriétaire de facturation.  

- **Propriétaires ajoutés via des rôles RBAC** : le rôle **Propriétaire** peut être attribué à d’autres utilisateurs par le biais du [contrôle d’accès en fonction du rôle](azure-stack-manage-permissions.md) (RBAC). Vous pouvez ajouter autant de comptes d’utilisateur que vous le souhaitez en tant que propriétaires pour assister le propriétaire de facturation. Les propriétaires supplémentaires deviennent également administrateurs de l’abonnement et ont tous les privilèges pour l’abonnement, sauf l’autorisation nécessaire pour supprimer le propriétaire de facturation.

  Vous pouvez utiliser PowerShell pour gérer des propriétaires supplémentaires. Pour plus d’informations, consultez [cet article](/azure/role-based-access-control/role-assignments-powershell).

## <a name="change-the-billing-owner"></a>Modifier le propriétaire de facturation

Exécutez le script suivant pour modifier le propriétaire de facturation d’un abonnement utilisateur. L’ordinateur que vous utilisez pour exécuter le script doit se connecter à Azure Stack Hub et exécuter le module Azure Stack Hub PowerShell version 1.3.0 ou ultérieure. Pour plus d’informations, consultez l’article [Installer Azure Stack Hub PowerShell](azure-stack-powershell-install.md).

>[!NOTE]
>Dans une instance Azure Stack Hub mutualisée, le nouveau propriétaire doit se trouver dans le même répertoire que le propriétaire existant. Pour pouvoir accorder la propriété de l’abonnement à un utilisateur qui se trouve dans un autre annuaire, vous devez d’abord [inviter cet utilisateur dans votre annuaire](/azure/active-directory/b2b/add-users-administrator).

Remplacez les valeurs suivantes dans le script avant de l’exécuter :

- **$ArmEndpoint** : Point de terminaison Resource Manager pour votre environnement.
- **$TenantId** : Votre ID de locataire.
- **$SubscriptionId** : Votre ID d’abonnement.
- **$OwnerUpn** : Compte, par exemple **user\@example.com**, à ajouter en tant que nouveau propriétaire de facturation.

```powershell
# Set up Azure Stack Hub admin environment
Add-AzureRmEnvironment -ARMEndpoint $ArmEndpoint -Name AzureStack-admin
Add-AzureRmAccount -Environment AzureStack-admin -TenantId $TenantId

# Select admin subscription
$providerSubscriptionId = (Get-AzureRmSubscription -SubscriptionName "Default Provider Subscription").Id
Write-Output "Setting context to the Default Provider Subscription: $providerSubscriptionId"
Set-AzureRmContext -Subscription $providerSubscriptionId

# Change user subscription owner
$subscription = Get-AzsUserSubscription -SubscriptionId $SubscriptionId
$Subscription.Owner = $OwnerUpn
Set-AzsUserSubscription -InputObject $subscription
```

[!include[Remove Account](../../includes/remove-account.md)]

## <a name="next-steps"></a>Étapes suivantes

- [Gérer le contrôle d’accès en fonction du rôle](azure-stack-manage-permissions.md)
