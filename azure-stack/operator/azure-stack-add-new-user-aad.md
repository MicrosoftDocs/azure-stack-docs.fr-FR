---
title: Ajouter un nouveau compte client Azure Stack dans Azure Active Directory | Microsoft Docs
description: Après le déploiement du Kit de développement Microsoft Azure Stack, vous devez créer au moins un compte d’utilisateur locataire pour pouvoir explorer le portail locataire.
services: azure-stack
documentationcenter: ''
author: patricka
manager: femila
editor: ''
ms.assetid: a75d5c88-5b9e-4e9a-a6e3-48bbfa7069a7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: patricka
ms.reviewer: unknown
ms.lastreviewed: 09/17/2018
ms.openlocfilehash: 52fb4074a476cb907f02628933b83d82b6ee4984
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985754"
---
# <a name="add-a-new-azure-stack-tenant-account-in-azure-active-directory"></a>Ajouter un nouveau compte de locataire Azure Stack dans Azure Active Directory

Après le [déploiement du Kit de développement Azure Stack](../asdk/asdk-install.md), vous aurez besoin d’un compte utilisateur client afin de pouvoir explorer le portail client et tester vos offres et vos plans. Vous pouvez créer un compte client [à l’aide du portail Azure](#create-an-azure-stack-tenant-account-using-the-azure-portal) ou à l’aide de PowerShell.

## <a name="create-an-azure-stack-tenant-account-using-the-azure-portal"></a>Création d’un compte de locataire Azure Stack à l’aide du portail Azure

Pour utiliser le portail Azure, vous devez disposer d’un abonnement Azure.

1. Connectez-vous à [Azure](https://portal.azure.com).
2. Dans la barre de navigation de gauche, sélectionnez **Active Directory** et accédez au répertoire que vous souhaitez utiliser pour Azure Stack, ou créez-en un.
3. Sélectionnez **Azure Active Directory** > **Utilisateurs** > **Nouvel utilisateur**.

    ![Page Utilisateurs - Tous avec l’option Nouvel utilisateur mis en surbrillance](media/azure-stack-add-new-user-aad/new-user-all-users.png)

4. Dans la page **Utilisateur**, fournissez les informations requises.

    ![Ajouter un nouvel utilisateur, page Utilisateur avec les informations sur l’utilisateur](media/azure-stack-add-new-user-aad/new-user-user.png)

   - **Nom (obligatoire).** Prénom et nom du nouvel utilisateur. Par exemple, Mary Parker.
   - **Nom d’utilisateur (obligatoire).** Nom d’utilisateur du nouvel utilisateur. Par exemple : mary@contoso.com.
       Le domaine dans le nom d’utilisateur doit correspondre au nom de domaine par défaut initial, <_votrenomdedomaine_>.onmicrosoft.com, ou à un nom de domaine personnalisé, comme contoso.com. Pour plus d’informations sur la création d’un nom de domaine personnalisé, consultez [Guide pratique pour ajouter un nom de domaine personnalisé dans Azure Active Directory]((/azure/active-directory/fundamentals/add-custom-domain).
   - **Profil.** Si vous le souhaitez, ajoutez des informations supplémentaires sur l’utilisateur. Vous pouvez également ajouter ces informations ultérieurement. Pour plus d’informations sur l’ajout d’informations utilisateur, consultez [Guide pratique pour ajouter ou modifier des informations de profil utilisateur]((/azure/active-directory/fundamentals/active-directory-users-profile-azure-portal).
   - **Rôle de répertoire.**  Choisissez **Utilisateur**.

5. Cochez **Afficher le mot de passe** et copiez le mot de passe généré automatiquement dans le champ **Mot de passe**. Vous aurez besoin de ce mot de passe pour vous connecter la première fois.

6. Sélectionnez **Créer**.

    L’utilisateur est créé et ajouté à votre locataire Azure AD.

7. Connectez-vous au portail Microsoft Azure avec le nouveau compte. Modifiez le mot de passe lorsque vous y êtes invité.
8. Connectez-vous à `https://portal.local.azurestack.external` avec le nouveau compte pour afficher le portail client.

## <a name="create-an-azure-stack-user-account-using-powershell"></a>Création d’un compte d’utilisateur Azure Stack à l’aide de PowerShell

Si vous n’avez pas d’abonnement Azure, vous ne pouvez pas utiliser le portail Azure pour ajouter un compte utilisateur client. Dans ce cas, vous pouvez utiliser le module Azure Active Directory pour Windows PowerShell à la place.

> [!NOTE]
> Si vous utilisez un compte Microsoft (Live ID) pour déployer le Kit de développement Azure Stack, vous ne pouvez pas utiliser AAD PowerShell pour créer le compte client. 

1. Installez [l’Assistant de connexion Microsoft Online Services pour les professionnels de l’informatique RTW](https://www.microsoft.com/en-us/download/details.aspx?id=41950).
2. Installez le [module Azure Active Directory pour Windows PowerShell (version 64 bits)](https://go.microsoft.com/fwlink/p/?linkid=236297) et ouvrez-le.
3. Exécutez les applets de commande suivantes :

    ```powershell
    # Provide the AAD credential you use to deploy Azure Stack Development Kit

            $msolcred = get-credential

    # Add a tenant account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".

            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId

    ```

1. Connectez-vous à Microsoft Azure avec le nouveau compte. Modifiez le mot de passe lorsque vous y êtes invité.
2. Connectez-vous à `https://portal.local.azurestack.external` avec le nouveau compte pour afficher le portail client.

## <a name="next-steps"></a>Étapes suivantes

[Ajouter des utilisateurs Azure Stack dans AD FS](azure-stack-add-users-adfs.md)
