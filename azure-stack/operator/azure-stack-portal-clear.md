---
title: Effacez les données utilisateur du portail sur demande dans Azure Stack Hub. | Microsoft Docs
description: En tant qu’opérateur Azure Stack Hub, découvrez comment effacer les données utilisateur du portail lorsque les utilisateurs Azure Stack Hub en font la demande.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.date: 09/10/2019
ms.author: sethm
ms.reviewer: troettinger
ms.lastreviewed: 09/10/2019
monikerRange: azs-1802
ms.openlocfilehash: ac28a67f7b1409ebc5a786a88e8b9702df94c2ff
ms.sourcegitcommit: d62400454b583249ba5074a5fc375ace0999c412
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76022759"
---
# <a name="clear-portal-user-data-from-azure-stack-hub"></a>Effacer les données utilisateur du portail dans Azure Stack Hub

Les opérateurs Azure Stack Hub peuvent effacer les données utilisateur du portail à la demande des utilisateurs. Les utilisateurs Azure Stack Hub peuvent personnaliser le portail en épinglant des vignettes et en modifiant la disposition du tableau de bord. Ils peuvent également modifier le thème et la langue par défaut pour que celle-ci corresponde aux préférences personnelles. 

Les données utilisateur du portail sont notamment les favoris, et les ressources récemment consultées dans le portail de l’utilisateur Azure Stack Hub. Cet article explique comment effacer les données utilisateur du portail.

La suppression des paramètres utilisateur du portail doit être effectuée uniquement après la suppression de l’abonnement utilisateur.

> [!NOTE]
> Une fois que vous aurez suivi les instructions de cet article, certaines données utilisateur peuvent persister dans la section système des journaux des événements. Ces données peuvent persister pendant plusieurs jours, jusqu’à la substitution automatique des journaux.

## <a name="requirements"></a>Spécifications

- [Installez PowerShell pour Azure Stack Hub](azure-stack-powershell-install.md).
- [Téléchargez les derniers outils Azure Stack Hub](azure-stack-powershell-download.md) à partir de GitHub.
- Le compte d’utilisateur doit encore se trouver dans le répertoire.
- Informations d’identification de l’administrateur Azure Stack Hub permettant d’accéder au point de terminaison Resource Manager de l’administrateur.

> [!NOTE]
> Si vous tentez de supprimer du portail des informations relatives à un utilisateur invité à partir d’un répertoire invité (multilocataire), vous devez disposer d’autorisations de lecture pour ce répertoire. Pour plus d’informations, consultez le [scénario CSP mentionné plus loin dans cet article](#clear-portal-user-data-in-guest-directory).

## <a name="clear-portal-user-data-using-a-user-principal-name"></a>Effacer des données utilisateur du portail à l’aide d’un nom d’utilisateur principal

Ce scénario suppose que l’abonnement du fournisseur par défaut et l’utilisateur font partie du même répertoire, ou que vous disposez d’un accès en lecture pour le répertoire où réside l’utilisateur.

Avant de continuer, veillez à [télécharger la dernière version des outils Azure Stack Hub](azure-stack-powershell-download.md) à partir de GitHub.

Pour cette procédure, utilisez un ordinateur qui peut communiquer avec le point de terminaison Resource Manager de l’administrateur dans Azure Stack Hub.

1. Ouvrez une session Windows PowerShell avec élévation de privilèges (en tant qu’administrateur), accédez au dossier racine dans le répertoire **AzureStack-Tools-master**, puis importez le module PowerShell nécessaire :

   ```powershell
   Import-Module .\DatacenterIntegration\Portal\PortalUserDataUtilities.psm1
   ```

2. Exécutez les commandes suivantes : Veillez à remplacer les espaces réservés par des valeurs qui correspondent à celles de votre environnement.

   ```powershell
   ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.

   $adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

   ## Replace the following value with the Azure Stack Hub directory tenant ID.
   $azureStackDirectoryTenantId = "f5025bf2-547f-4b49-9693-6420c1d5e4ca"

   ## Replace the following value with the user directory tenant ID.
   $userDirectoryTenantId = " 7ddf3648-9671-47fd-b63d-eecd82ed040e"

   ## Replace the following value with name of the user principal whose portal user data is to be cleared.
   $userPrincipalName = "myaccount@contoso.onmicrosoft.com"

   Clear-AzsUserDataWithUserPrincipalName -AzsAdminArmEndpoint $adminARMEndpoint `
    -AzsAdminDirectoryTenantId $azureStackDirectoryTenantId `
    -UserPrincipalName $userPrincipalName `
    -DirectoryTenantId $userDirectoryTenantId
   ```

   > [!NOTE]
   > `azureStackDirectoryTenantId` est facultatif. Si vous ne spécifiez pas cette valeur, le script va rechercher le nom d’utilisateur principal dans tous les répertoires de locataire inscrits dans Azure Stack Hub, puis il va effacer les données du portail pour tous les utilisateurs correspondants trouvés.

## <a name="clear-portal-user-data-in-guest-directory"></a>Effacer les données utilisateur du portail dans le répertoire invité

Dans ce scénario, l’opérateur Azure Stack Hub n’a pas accès au répertoire invité où réside l’utilisateur. Il s’agit d’un scénario courant pour un fournisseur de solutions cloud (CSP).

Pour qu’un opérateur Azure Stack Hub supprime les données utilisateur du portail, vous devez fournir au moins l’ID d’objet utilisateur.

L’utilisateur doit interroger l’ID d’objet et le fournir à l’opérateur Azure Stack Hub. L’opérateur n’a pas accès au répertoire où réside l’utilisateur.

### <a name="user-retrieves-the-user-object-id"></a>L’utilisateur récupère l’ID d’objet utilisateur

1. Ouvrez une session Windows PowerShell avec élévation de privilèges (en tant qu’administrateur), accédez au dossier racine dans le répertoire **AzureStack-Tools-master**, puis importez le module PowerShell nécessaire.

   ```powershell
   Import-Module .\DatacenterIntegration\Portal\PortalUserDataUtilities.psm1
   ```

2. Exécutez les commandes suivantes : Veillez à remplacer les espaces réservés par des valeurs qui correspondent à celles de votre environnement.

   ```powershell
   ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
   $userARMEndpoint = "https://management.local.azurestack.external"

   ## Replace the following value with the directory tenant ID, which contains the user account.
   $userDirectoryTenantId = "3160cbf5-c227-49dd-8654-86e924c0b72f"

   ## Replace the following value with the name of the user principal whose portal user data is to be cleared.
   $userPrincipleName = "myaccount@contoso.onmicrosoft.com"

   Get-UserObjectId -DirectoryTenantId $userDirectoryTenantId `
    -AzsArmEndpoint $userARMEndpoint `
    -UserPricinpalName $userPrincipleName
   ```

   > [!NOTE]
   > En tant qu’utilisateur, vous devez fournir l’ID d’objet utilisateur (qui correspond à la sortie du script précédent) à l’opérateur Azure Stack Hub.

## <a name="azure-stack-hub-operator-removes-the-portal-user-data"></a>L’opérateur Azure Stack Hub supprime les données utilisateur du portail

Après avoir reçu l’ID d’objet utilisateur en tant qu’opérateur Azure Stack Hub, exécutez les commandes suivantes pour supprimer les données utilisateur du portail :

1. Ouvrez une session Windows PowerShell avec élévation de privilèges (en tant qu’administrateur), accédez au dossier racine dans le répertoire **AzureStack-Tools-master**, puis importez le module PowerShell nécessaire.

   ```powershell
   Import-Module .\DatacenterIntegration\Portal\PortalUserDataUtilities.psm1
   ```

2. Exécutez les commandes suivantes, en veillant à ajuster le paramètre en fonction de votre environnement :

   ```powershell
   ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
   $AzsAdminARMEndpoint = "https://adminmanagement.local.azurestack.external"

   ## Replace the following value with the Azure Stack Hub directory tenant ID.
   $AzsAdminDirectoryTenantId = "f5025bf2-547f-4b49-9693-6420c1d5e4ca"
   
   ## Replace the following value with the directory tenant ID of the user to clear.
   $DirectoryTenantId = "3160cbf5-c227-49dd-8654-86e924c0b72f"

   ## Replace the following value with the name of the user principal whose portal user data is to be cleared.
   $userObjectID = "s-1-*******"
   Clear-AzsUserDataWithUserObject -AzsAdminArmEndpoint $AzsAdminARMEndpoint `
    -AzsAdminDirectoryTenantId $AzsAdminDirectoryTenantId `
    -DirectoryTenantID $DirectoryTenantId `
    -UserObjectID $userObjectID `
   ```

## <a name="next-steps"></a>Étapes suivantes

- [Inscrivez Azure Stack Hub auprès d’Azure](azure-stack-registration.md) et renseignez la [Place de marché Azure Stack Hub](azure-stack-marketplace.md) avec les éléments que vous proposez à vos utilisateurs.
