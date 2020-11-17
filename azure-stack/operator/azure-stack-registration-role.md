---
title: Créer un rôle personnalisé pour l'inscription d'Azure Stack Hub
titleSuffix: Azure Stack Hub
description: Apprenez à créer un rôle personnalisé afin d'éviter d'utiliser un compte d'administrateur général pour l'inscription d'Azure Stack Hub.
author: BryanLa
ms.topic: how-to
ms.date: 08/05/2020
ms.author: bryanla
ms.reviewer: rtiberiu
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: 9348930a09a57ab25be867616c604e11603a82c0
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94545412"
---
# <a name="create-a-custom-role-for-azure-stack-hub-registration"></a>Créer un rôle personnalisé pour l'inscription d'Azure Stack Hub

> [!WARNING]
> Il s’agit d’une fonctionnalité présentant certains risques de sécurité. Utilisez-la dans des scénarios où vous voulez que des contraintes empêchent les modifications accidentelles de l’abonnement Azure. Quand ce rôle personnalisé est délégué à un utilisateur, celui-ci dispose de droits permettant de modifier les autorisations et d’élever les droits. Affectez à ce rôle uniquement des utilisateurs auxquels vous faites confiance.

Pendant l'inscription d'Azure Stack Hub, vous devez vous connecter avec un compte Azure Active Directory (Azure AD). Ce compte doit avoir les autorisations Azure AD et les autorisations d’abonnement Azure suivantes :

* **Autorisations d’inscription d’application dans votre locataire Azure AD :** Les administrateurs ont des autorisations d’inscription d’application. L’autorisation pour les utilisateurs est un paramètre global pour tous les utilisateurs du locataire. Pour voir ou changer le paramètre, consultez [Créer une application et un principal du service Azure AD pouvant accéder aux ressources](/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

    Le paramètre *L'utilisateur peut inscrire des applications* doit être défini sur **Oui** pour permettre à un compte d'utilisateur d'inscrire Azure Stack Hub. Si le paramètre d'inscription d'applications est défini sur **Non**, vous ne pouvez pas utiliser un compte d'utilisateur pour inscrire Azure Stack Hub. Vous devez utiliser un compte d'administrateur général.

* **Un ensemble d’autorisations d’abonnement Azure suffisantes :** Les utilisateurs avec le rôle Propriétaire ont les autorisations suffisantes. Pour les autres comptes, vous pouvez affecter l’ensemble d’autorisations en attribuant un rôle personnalisé comme indiqué dans les sections suivantes.

Au lieu d’utiliser un compte ayant des autorisations Propriétaire dans l’abonnement Azure, vous pouvez créer un rôle personnalisé afin d’attribuer des autorisations à un compte d’utilisateur qui a des privilèges moins élevés. Ce compte peut ensuite être utilisé pour l'inscription de votre instance d'Azure Stack Hub.

## <a name="create-a-custom-role-using-powershell"></a>Créer un rôle personnalisé avec PowerShell

Pour créer un rôle personnalisé, vous devez disposer de l’autorisation `Microsoft.Authorization/roleDefinitions/write` sur toutes les `AssignableScopes`, comme [Propriétaire](/azure/role-based-access-control/built-in-roles#owner) ou [Administrateur de l’accès utilisateur](/azure/role-based-access-control/built-in-roles#user-access-administrator). Utilisez le modèle JSON suivant pour simplifier la création du rôle personnalisé. Le modèle crée un rôle personnalisé qui permet l'accès en lecture et en écriture nécessaire à l'inscription d'Azure Stack Hub.

1. Créez un fichier JSON. Par exemple : `C:\CustomRoles\registrationrole.json`.
2. Ajoutez le code JSON suivant au fichier. Remplacez `<SubscriptionID>` par l’identifiant de votre abonnement Azure.

    ```json
    {
      "Name": "Azure Stack Hub registration role",
      "Id": null,
      "IsCustom": true,
      "Description": "Allows access to register Azure Stack Hub",
      "Actions": [
        "Microsoft.Resources/subscriptions/resourceGroups/write",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.AzureStack/registrations/*",
        "Microsoft.AzureStack/register/action",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Authorization/roleAssignments/write",
        "Microsoft.Authorization/roleAssignments/delete",
        "Microsoft.Authorization/permissions/read",
        "Microsoft.Authorization/locks/read",
        "Microsoft.Authorization/locks/write"
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
    Connect-AzAccount
    ```

4. Pour créer le rôle personnalisé, utilisez **New-AzRoleDefinition** en spécifiant le fichier de modèle JSON.

    ``` azurepowershell
    New-AzRoleDefinition -InputFile "C:\CustomRoles\registrationrole.json"
    ```

## <a name="assign-a-user-to-registration-role"></a>Affecter un utilisateur au rôle d’inscription

Une fois le rôle personnalisé d'inscription créé, attribuez-le au compte d'utilisateur qui sera utilisé pour l'inscription d'Azure Stack Hub.

1. Connectez-vous avec le compte qui a des autorisations suffisantes sur l’abonnement Azure pour déléguer des droits (comme [Propriétaire](/azure/role-based-access-control/built-in-roles#owner) ou [Administrateur de l’accès utilisateur](/azure/role-based-access-control/built-in-roles#user-access-administrator)).
2. Dans **Abonnements**, sélectionnez **Contrôle d’accès (IAM) > Ajouter une attribution de rôle**.
3. Dans **Rôle**, choisissez le rôle personnalisé que vous avez créé : *Rôle d'inscription d'Azure Stack Hub*.
4. Sélectionnez les utilisateurs que vous voulez affecter au rôle.
5. Sélectionnez **Enregistrer** pour affecter les utilisateurs sélectionnés au rôle.

    ![Sélectionnez les utilisateurs à affecter au rôle personnalisé dans le Portail Azure](media/azure-stack-registration-role/assign-role.png)

Pour plus d’informations sur l’utilisation de rôles personnalisés, consultez [Gérer les accès à l’aide du contrôle d’accès en fonction du rôle et du portail Azure](/azure/role-based-access-control/role-assignments-portal).

## <a name="next-steps"></a>Étapes suivantes

[Inscrire Azure Stack Hub auprès d'Azure](azure-stack-registration.md)
