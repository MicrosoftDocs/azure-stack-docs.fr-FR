---
title: Ajouter un nouveau compte d’utilisateur Azure Stack Hub dans Azure Active Directory
description: Découvrez comment créer un compte d’utilisateur dans Azure Active Directory, afin de pouvoir explorer le portail utilisateur.
author: JustinHall
ms.topic: article
ms.date: 05/20/2019
ms.author: justinha
ms.reviewer: thoroet
ms.lastreviewed: 09/17/2019
ms.openlocfilehash: 435b2bfdd2de9a232379190a0fe3db0dca642def
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77700559"
---
# <a name="add-a-new-azure-stack-hub-user-account-in-azure-active-directory-azure-ad"></a>Ajouter un nouveau compte d’utilisateur Azure Stack Hub dans Azure Active Directory (Azure AD)

Pour pouvoir tester des offres et des plans, ainsi que créer des ressources, vous devez disposer d’un compte d’utilisateur. Vous créez un compte d’utilisateur dans votre locataire Azure AD à l’aide du portail Azure ou de PowerShell.

## <a name="create-user-account-using-the-azure-portal"></a>Créer un compte d’utilisateur à l’aide du portail Azure

Pour utiliser le portail Azure, vous devez disposer d’un abonnement Azure.

1. Connectez-vous à [Azure](https://portal.azure.com).
2. Dans la barre de navigation de gauche, sélectionnez **Active Directory**, puis accédez au répertoire que vous souhaitez utiliser pour Azure Stack Hub, ou créez-en un.
3. Sélectionnez **Azure Active Directory** > **Utilisateurs** > **Nouvel utilisateur**.

    ![Page Utilisateurs - Tous avec l’option Nouvel utilisateur mis en surbrillance](media/azure-stack-add-new-user-aad/new-user-all-users.png)

4. Dans la page **Utilisateur**, fournissez les informations requises.

    ![Ajouter un nouvel utilisateur, page Utilisateur avec les informations sur l’utilisateur](media/azure-stack-add-new-user-aad/new-user-user.png)

   - **Nom (obligatoire)**  : Prénom et nom du nouvel utilisateur. Par exemple, Mary Parker.
   - **Nom d’utilisateur (obligatoire)** : Nom d’utilisateur du nouvel utilisateur. Par exemple : mary@contoso.com.
       Le domaine dans le nom d’utilisateur doit correspondre au nom de domaine par défaut initial, <_votrenomdedomaine_>.onmicrosoft.com, ou à un nom de domaine personnalisé, comme contoso.com. Pour plus d’informations sur la création d’un nom de domaine personnalisé, consultez [Ajouter un nom de domaine personnalisé dans Azure AD](/azure/active-directory/fundamentals/add-custom-domain).
   - **Profil** : Si vous le souhaitez, ajoutez des informations supplémentaires sur l’utilisateur. Vous pouvez également ajouter ces informations ultérieurement. Pour plus d’informations sur l’ajout d’informations sur l’utilisateur, consultez [Ajouter ou modifier des informations de profil utilisateur](/azure/active-directory/fundamentals/active-directory-users-profile-azure-portal).
   - **Rôle d’annuaire** : choisissez **Utilisateur**.

5. Cochez **Afficher le mot de passe** et copiez le mot de passe généré automatiquement dans le champ **Mot de passe**. Vous aurez besoin de ce mot de passe pour vous connecter la première fois.

6. Sélectionnez **Create** (Créer).

    L’utilisateur est créé et ajouté à votre locataire Azure AD.

7. Connectez-vous au portail Azure avec le nouveau compte. Modifiez le mot de passe lorsque vous y êtes invité.
8. Connectez-vous à `https://portal.local.azurestack.external` avec le nouveau compte pour afficher le portail utilisateur.

## <a name="create-a-user-account-using-powershell"></a>Créer un compte d’utilisateur à l’aide de PowerShell

Si vous n’avez pas d’abonnement Azure, vous ne pouvez pas utiliser le portail Azure pour ajouter un compte utilisateur client. Dans ce cas, vous pouvez utiliser le module Azure AD pour Windows PowerShell à la place.

> [!NOTE]
> Si vous utilisez un compte Microsoft pour déployer ASDK, vous ne pouvez pas utiliser Azure AD PowerShell pour créer le compte locataire.

1. Installez la version **64 bits** de l’[Assistant de connexion Microsoft Online Services pour les professionnels de l’informatique RTW](https://go.microsoft.com/fwlink/p/?LinkId=286152).

2. Installez le module Microsoft Azure AD pour Windows PowerShell en effectuant les étapes suivantes :

    - Ouvrez une invite de commandes Windows PowerShell avec élévation de privilèges (exécutez Windows PowerShell en tant qu’administrateur).
    - Exécutez la commande **Install-Module MSOnline**.
    - Si vous êtes invité à installer le fournisseur NuGet, sélectionnez **O**, puis **Entrée**.
    - Si vous êtes invité à installer le module à partir de PSGallery, sélectionnez **O**, puis **Entrée**.

3. Exécutez les applets de commande suivantes :

    ```powershell
    # Provide the Azure AD credential you use to deploy the ASDK.

            $msolcred = get-credential

    # Add a user account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".

            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId

    ```

1. Connectez-vous à Azure avec le nouveau compte. Modifiez le mot de passe lorsque vous y êtes invité.
2. Connectez-vous à `https://portal.local.azurestack.external` avec le nouveau compte pour afficher le portail utilisateur.

## <a name="next-steps"></a>Étapes suivantes

[Ajouter des utilisateurs d’Azure Stack Hub dans AD FS](azure-stack-add-users-adfs.md)
