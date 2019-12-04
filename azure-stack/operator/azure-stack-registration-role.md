---
title: Créer un rôle personnalisé pour l’inscription Azure Stack
titleSuffix: Azure Stack
description: Découvrez comment créer un rôle personnalisé pour éviter d’utiliser l’administrateur général pour l’inscription Azure Stack.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2019
ms.author: patricka
ms.reviewer: rtiberiu
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: 0cfbec17b2aef1f6a14615d4b69d8a5e9347e913
ms.sourcegitcommit: 284f5316677c9a7f4c300177d0e2a905df8cb478
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74465426"
---
# <a name="create-a-custom-role-for-azure-stack-registration"></a>Créer un rôle personnalisé pour l’inscription Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

> [!WARNING]
> Il s’agit d’une fonctionnalité présentant certains risques de sécurité. Utilisez-la dans des scénarios où vous voulez que des contraintes empêchent les modifications accidentelles de l’abonnement Azure. Quand ce rôle personnalisé est délégué à un utilisateur, celui-ci dispose de droits permettant de modifier les autorisations et d’élever les droits. Affectez à ce rôle uniquement des utilisateurs auxquels vous faites confiance.

Pendant l’inscription Azure Stack, vous devez vous connecter avec un compte Azure Active Directory (Azure AD). Ce compte doit avoir les autorisations Azure AD et les autorisations d’abonnement Azure suivantes :

* **Autorisations d’inscription d’application dans votre locataire Azure AD :** Les administrateurs ont des autorisations d’inscription d’application. L’autorisation pour les utilisateurs est un paramètre global pour tous les utilisateurs du locataire. Pour voir ou changer le paramètre, consultez [Créer une application et un principal du service Azure AD pouvant accéder aux ressources](/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

    Le paramètre *L’utilisateur peut inscrire des applications* doit être défini sur **Oui** pour permettre à un compte d’utilisateur d’inscrire Azure Stack. Si le paramètre d’inscription d’applications est défini sur **Non**, vous ne pouvez pas utiliser un compte d’utilisateur pour inscrire Azure Stack – vous devez utiliser un compte d’administrateur général.

* **Un ensemble d’autorisations d’abonnement Azure suffisantes :** Les utilisateurs avec le rôle Propriétaire ont les autorisations suffisantes. Pour les autres comptes, vous pouvez affecter l’ensemble d’autorisations en attribuant un rôle personnalisé comme indiqué dans les sections suivantes.

Au lieu d’utiliser un compte ayant des autorisations Propriétaire dans l’abonnement Azure, vous pouvez créer un rôle personnalisé afin d’attribuer des autorisations à un compte d’utilisateur qui a des privilèges moins élevés. Ce compte peut ensuite être utilisé pour votre inscription Azure Stack.

## <a name="create-a-custom-role-using-powershell"></a>Créer un rôle personnalisé avec PowerShell

Pour créer un rôle personnalisé, vous devez disposer de l’autorisation `Microsoft.Authorization/roleDefinitions/write` sur toutes les `AssignableScopes`, comme [Propriétaire](/azure/role-based-access-control/built-in-roles#owner) ou [Administrateur de l’accès utilisateur](/azure/role-based-access-control/built-in-roles#user-access-administrator). Utilisez le modèle JSON suivant pour simplifier la création du rôle personnalisé. Le modèle crée un rôle personnalisé qui permet l’accès en lecture et en écriture nécessaire pour l’inscription d’Azure Stack.

1. Créez un fichier JSON. Par exemple : `C:\CustomRoles\registrationrole.json`.
2. Ajoutez le code JSON suivant au fichier. Remplacez `<SubscriptionID>` par l’identifiant de votre abonnement Azure.

    ```json
    {
      "Name": "Azure Stack registration role",
      "Id": null,
      "IsCustom": true,
      "Description": "Allows access to register Azure Stack",
      "Actions": [
        "Microsoft.Resources/subscriptions/resourceGroups/write",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.AzureStack/registrations/*",
        "Microsoft.AzureStack/register/action",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Authorization/roleAssignments/write",
        "Microsoft.Authorization/roleAssignments/delete",
        "Microsoft.Authorization/permissions/read"
      ],
      "NotActions": [
      ],
      "AssignableScopes": [
        "/subscriptions/<SubscriptionID>"
      ]
    }
    ```

3. Dans PowerShell, connectez-vous à Azure pour utiliser Azure Resource Manager. Quand vous y êtes invité, authentifiez-vous avec un compte disposant d’autorisations suffisantes, comme [Propriétaire](/azure/role-based-access-control/built-in-roles#owner) ou [Administrateur de l’accès utilisateur](/azure/role-based-access-control/built-in-roles#user-access-administrator).

    ```azurepowershell
    Connect-AzureRmAccount
    ```

4. Pour créer le rôle personnalisé, utilisez **New-AzureRmRoleDefinition** en spécifiant le fichier de modèle JSON.

    ``` azurepowershell
    New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\registrationrole.json"
    ```

## <a name="assign-a-user-to-registration-role"></a>Affecter un utilisateur au rôle d’inscription

Une fois le rôle personnalisé d’inscription créé, attribuez-le au compte d’utilisateur qui sera utilisé pour l’inscription Azure Stack.

1. Connectez-vous avec le compte qui a des autorisations suffisantes sur l’abonnement Azure pour déléguer des droits (comme [Propriétaire](/azure/role-based-access-control/built-in-roles#owner) ou [Administrateur de l’accès utilisateur](/azure/role-based-access-control/built-in-roles#user-access-administrator)).
2. Dans **Abonnements**, sélectionnez **Contrôle d’accès (IAM) > Ajouter une attribution de rôle**.
3. Dans **Rôle**, choisissez le rôle personnalisé que vous avez créé : *Rôle d’inscription Azure Stack*.
4. Sélectionnez les utilisateurs que vous voulez affecter au rôle.
5. Sélectionnez **Enregistrer** pour affecter les utilisateurs sélectionnés au rôle.

    ![Sélectionnez les utilisateurs à affecter au rôle personnalisé dans le Portail Azure](media/azure-stack-registration-role/assign-role.png)

Pour plus d’informations sur l’utilisation de rôles personnalisés, consultez [Gérer les accès à l’aide du contrôle d’accès en fonction du rôle et du portail Azure](/azure/role-based-access-control/role-assignments-portal).

## <a name="next-steps"></a>Étapes suivantes

[Inscrire Azure Stack auprès d’Azure](azure-stack-registration.md)
