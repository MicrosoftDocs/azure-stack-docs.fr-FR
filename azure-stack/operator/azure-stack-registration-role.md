---
title: Guide pratique pour créer un rôle d’inscription pour Azure Stack
description: Comment créer un rôle personnalisé pour éviter d’utiliser un administrateur général pour l’inscription.
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
ms.date: 02/13/2019
ms.author: patricka
ms.reviewer: rtiberiu
ms.lastreviewed: 02/13/2019
ms.openlocfilehash: f5ccc5fc7a280cd8d0832edfe1be6f4ff35dba1d
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985343"
---
# <a name="create-a-registration-role-for-azure-stack"></a>Créer un rôle d’inscription pour Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Pour les scénarios où vous ne voulez pas accorder des autorisations de propriétaire dans l’abonnement Azure, vous pouvez créer un rôle personnalisé visant à affecter des autorisations à un compte d’utilisateur pour inscrire votre service Azure Stack.

> [!WARNING]
> Il s’agit d’une fonctionnalité présentant certains risques de sécurité. Utilisez-la dans des scénarios où vous voulez que des contraintes empêchent les modifications accidentelles de l’abonnement Azure. Quand ce rôle personnalisé est délégué à un utilisateur, celui-ci dispose de droits permettant de modifier les autorisations et d’élever les droits. Affectez à ce rôle uniquement des utilisateurs auxquels vous faites confiance.

Lors de l’inscription d’Azure Stack, le compte d’inscription nécessite les autorisations Azure Active Directory et les autorisations de l’abonnement Azure suivantes :

* **Autorisations d’inscription d’application dans votre locataire Azure Active Directory :** Les administrateurs ont des autorisations d’inscription d’application. L’autorisation pour les utilisateurs est un paramètre global pour tous les utilisateurs du locataire. Pour voir ou modifier le paramètre, consultez [Créer une application et un principal de service Azure AD qui peuvent accéder aux resources]((/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

    Le paramètre *L’utilisateur peut inscrire des applications* doit être défini sur **Oui** pour permettre à un compte d’utilisateur d’inscrire Azure Stack. Si le paramètre d’inscription d’applications est défini sur **Non**, vous ne pouvez pas utiliser un compte d’utilisateur : vous devez utiliser un compte d’administrateur général pour inscrire Azure Stack.

* **Un ensemble d’autorisations d’abonnement Azure suffisantes :** Les utilisateurs du groupe Propriétaires ont des autorisations suffisantes. Pour les autres comptes, vous pouvez affecter l’ensemble d’autorisations en attribuant un rôle personnalisé comme indiqué dans les sections suivantes.

## <a name="create-a-custom-role-using-powershell"></a>Créer un rôle personnalisé avec PowerShell

Pour créer un rôle personnalisé, vous devez avoir l’autorisation `Microsoft.Authorization/roleDefinitions/write` sur tous les `AssignableScopes`, comme [Propriétaire]((/azure/role-based-access-control/built-in-roles#owner) ou [Administrateur de l’accès utilisateur] ((/azure/role-based-access-control/built-in-roles#user-access-administrator). Utilisez le modèle JSON suivant pour simplifier la définition du rôle personnalisé. Le modèle crée un rôle personnalisé qui permet l’accès en lecture et en écriture nécessaire pour l’inscription d’Azure Stack.

1. Créez un fichier JSON. Par exemple, `C:\CustomRoles\registrationrole.json`
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

3. Dans PowerShell, connectez-vous à Azure pour utiliser Azure Resource Manager. Lorsque vous y êtes invité, authentifiez-vous avec un compte disposant d’autorisations suffisantes, comme [Propriétaire]((/azure/role-based-access-control/built-in-roles#owner) ou [Administrateur de l’accès utilisateur] ((/azure/role-based-access-control/built-in-roles#user-access-administrator).

    ```azurepowershell
    Connect-AzureRmAccount
    ```

4. Pour ajouter le rôle à l’abonnement, utilisez **New-AzureRmRoleDefinition** en spécifiant le fichier de modèle JSON.

    ``` azurepowershell
    New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\registrationrole.json"
    ```

## <a name="assign-a-user-to-registration-role"></a>Affecter un utilisateur au rôle d’inscription

Une fois le rôle personnalisé d’inscription créé, attribuez-le aux utilisateurs qui inscrivent Azure Stack.

1. Connectez-vous avec le compte qui a des autorisations suffisantes sur l’abonnement Azure pour déléguer des droits, comme [Propriétaire]((/azure/role-based-access-control/built-in-roles#owner) ou [Administrateur de l’accès utilisateur] ((/azure/role-based-access-control/built-in-roles#user-access-administrator).
2. Dans **Abonnements**, sélectionnez **Contrôle d’accès (IAM) > Ajouter une attribution de rôle**.
3. Dans **Rôle**, choisissez le rôle personnalisé que vous avez créé, *Rôle d’inscription Azure Stack*.
4. Sélectionnez les utilisateurs que vous voulez affecter au rôle.
5. Sélectionnez **Enregistrer** pour affecter les utilisateurs sélectionnés au rôle.

    ![Sélectionner des utilisateurs à affecter au rôle](media/azure-stack-registration-role/assign-role.png)

Pour plus d’informations sur l’utilisation de rôles personnalisés, consultez [Gérer les accès avec le contrôle d’accès en fonction du rôle et le portail Azure]((/azure/role-based-access-control/role-assignments-portal).

## <a name="next-steps"></a>Étapes suivantes

[Inscrire Azure Stack auprès d’Azure](azure-stack-registration.md)
