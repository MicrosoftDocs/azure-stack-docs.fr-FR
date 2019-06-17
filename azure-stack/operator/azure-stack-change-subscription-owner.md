---
title: Mettre à jour le propriétaire de l’abonnement utilisateur Azure Stack | Microsoft Docs
description: Modifiez le propriétaire de facturation pour les abonnements utilisateur Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: sethm
ms.reviewer: shnatara
ms.lastreviewed: 10/19/2018
ms.openlocfilehash: 99f995941c4e7b09af70dff9391aeceb9a59844d
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2019
ms.locfileid: "66691923"
---
# <a name="change-the-owner-for-an-azure-stack-user-subscription"></a>Modifier le propriétaire d’un abonnement utilisateur Azure Stack

Les opérateurs Azure Stack peuvent utiliser PowerShell pour modifier le propriétaire de facturation d’un abonnement utilisateur. Vous pouvez par exemple modifier le propriétaire pour remplacer un utilisateur qui quitte votre entreprise.

Il existe deux types de *propriétaires* qui sont affectés à un abonnement :

- **Propriétaire de facturation** : par défaut, le propriétaire de facturation est le compte d’utilisateur qui obtient l’abonnement à partir d’une offre et qui possède donc la relation de facturation pour cet abonnement. Ce compte est également un administrateur de l’abonnement. Seul un compte d’utilisateur peut avoir cette désignation pour un abonnement. Un propriétaire de facturation est souvent un responsable d’équipe ou d’entreprise.

  Vous pouvez utiliser l’applet de commande PowerShell [Set-AzsUserSubscription](/powershell/module/azs.subscriptions.admin/set-azsusersubscription) pour modifier le propriétaire de facturation.  

- **Propriétaires ajoutés via des rôles RBAC** : le rôle **Propriétaire** peut être attribué à d’autres utilisateurs par le biais du [contrôle d’accès en fonction du rôle](azure-stack-manage-permissions.md) (RBAC). Vous pouvez ajouter autant de comptes d’utilisateur que vous le souhaitez en tant que propriétaires pour assister le propriétaire de facturation. Les propriétaires supplémentaires deviennent également administrateurs de l’abonnement et ont tous les privilèges pour l’abonnement, sauf l’autorisation nécessaire pour supprimer le propriétaire de facturation.

  Vous pouvez utiliser PowerShell pour gérer des propriétaires supplémentaires. Pour plus d’informations, consultez [cet article](/azure/role-based-access-control/role-assignments-powershell).

## <a name="change-the-billing-owner"></a>Modifier le propriétaire de facturation

Exécutez le script suivant pour modifier le propriétaire de facturation d’un abonnement utilisateur. L’ordinateur que vous utilisez pour exécuter le script doit se connecter à Azure Stack et exécuter le module Azure Stack PowerShell version 1.3.0 ou ultérieure. Pour plus d’informations, consultez l’article [Installer Azure Stack PowerShell](azure-stack-powershell-install.md).

>[!NOTE]
>Dans une instance Azure Stack mutualisée, le nouveau propriétaire doit se trouver dans le même répertoire que le propriétaire existant. Pour pouvoir accorder la propriété de l’abonnement à un utilisateur qui se trouve dans un autre répertoire, vous devez d’abord [inviter cet utilisateur en tant qu’invité dans votre répertoire](/azure/active-directory/b2b/add-users-administrator).

Remplacez les valeurs suivantes dans le script avant de l’exécuter :

- **$ArmEndpoint** : Point de terminaison Resource Manager pour votre environnement.
- **$TenantId** : Votre ID de locataire.
- **$SubscriptionId** : Votre ID d’abonnement.
- **$OwnerUpn** : Compte, par exemple **user\@example.com**, à ajouter en tant que nouveau propriétaire de facturation.

```powershell
# Set up Azure Stack admin environment
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

## <a name="next-steps"></a>Étapes suivantes

- [Gérer le contrôle d’accès en fonction du rôle](azure-stack-manage-permissions.md)
