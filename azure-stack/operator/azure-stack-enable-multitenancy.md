---
title: Configurer l’architecture multilocataire dans Azure Stack Hub
description: Découvrez comment activer et désactiver plusieurs locataires Azure Active Directory dans Azure Stack Hub.
author: BryanLa
ms.topic: how-to
ms.date: 06/18/2020
ms.author: bryanla
ms.reviewer: bryanr
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: 16b8ca5999507bd64d3416c3ee22fdd5c827c8b5
ms.sourcegitcommit: 874ad1cf8ce7e9b3615d6d69651419642d5012b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85107167"
---
# <a name="configure-multi-tenancy-in-azure-stack-hub"></a>Configurer l’architecture multilocataire dans Azure Stack Hub

Vous pouvez configurer Azure Stack Hub pour prendre en charge l’utilisation des services Azure Stack Hub par les utilisateurs de plusieurs locataires Azure Active Directory (Azure AD). Par exemple, considérez le scénario suivant :

- Vous êtes l’administrateur de services de contoso.onmicrosoft.com, où Azure Stack Hub est installé.
- Marie est l’administrateur de l’annuaire de fabrikam.onmicrosoft.com, où se trouvent les utilisateurs invités.
- La société de Marie reçoit des services IaaS et PaaS de la part de votre entreprise et doit autoriser les utilisateurs de l’annuaire invité (fabrikam.onmicrosoft.com) à se connecter et à utiliser des ressources Azure Stack Hub dans contoso.onmicrosoft.com.

Ce guide fournit les étapes nécessaires, dans le cadre de ce scénario, pour configurer une architecture multilocataire dans Azure Stack Hub. Dans ce scénario, Marie et vous-même devez effectuer des étapes pour permettre aux utilisateurs de Fabrikam de se connecter et d’utiliser les services du déploiement Azure Stack Hub dans Contoso.

## <a name="enable-multi-tenancy"></a>Activer l’architecture mutualisée

Il existe quelques prérequis à prendre en compte avant de configurer une architecture multilocataire Azure Stack Hub :
  
 - Marie et vous-même devez coordonner les étapes administratives dans l’annuaire où Azure Stack Hub est installé (Contoso) et dans l’annuaire invité (Fabrikam).
 - Vérifiez que vous avez [installé](azure-stack-powershell-install.md) et [configuré](azure-stack-powershell-configure-admin.md) PowerShell pour Azure Stack Hub.
 - [Téléchargez les outils Azure Stack Hub](azure-stack-powershell-download.md) et importez les modules de connexion et d’identité :

    ```powershell
    Import-Module .\Connect\AzureStack.Connect.psm1
    Import-Module .\Identity\AzureStack.Identity.psm1
    ```

### <a name="configure-azure-stack-hub-directory"></a>Configurer l’annuaire Azure Stack Hub

Dans cette section, vous allez configurer Azure Stack Hub pour autoriser les connexions à partir de locataires de l’annuaire Azure AD Fabrikam.

Intégrez le locataire de l’annuaire invité (Fabrikam) à Azure Stack Hub en configurant Azure Resource Manager de façon à accepter les utilisateurs et les principaux du service du locataire d’annuaire invité.

L’administrateur de services de contoso.onmicrosoft.com exécute les commandes suivantes :

```powershell  
## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint.
$adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

## Replace the value below with the Azure Stack Hub directory
$azureStackDirectoryTenant = "contoso.onmicrosoft.com"

## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantToBeOnboarded = "fabrikam.onmicrosoft.com"

## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
$ResourceGroupName = "system.local"

## Replace the value below with the region location of the resource group.
$location = "local"

# Subscription Name
$SubscriptionName = "Default Provider Subscription"

Register-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
 -DirectoryTenantName $azureStackDirectoryTenant `
 -GuestDirectoryTenantName $guestDirectoryTenantToBeOnboarded `
 -Location $location `
 -ResourceGroupName $ResourceGroupName `
 -SubscriptionName $SubscriptionName
```

### <a name="configure-guest-directory"></a>Configurer l’annuaire invité

Une fois que l’opérateur Azure Stack Hub a activé l’utilisation de l’annuaire Fabrikam avec Azure Stack Hub, Marie doit inscrire Azure Stack Hub auprès du locataire d’annuaire de Fabrikam.

#### <a name="register-azure-stack-hub-with-the-guest-directory"></a>Inscrire Azure Stack Hub auprès de l’annuaire invité

Marie (administratrice de l’annuaire de Fabrikam) exécute les commandes suivantes dans l’annuaire invité fabrikam.onmicrosoft.com :

```powershell
## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint.
$tenantARMEndpoint = "https://management.local.azurestack.external"
    
## Replace the value below with the guest tenant directory.
$guestDirectoryTenantName = "fabrikam.onmicrosoft.com"

Register-AzSWithMyDirectoryTenant `
 -TenantResourceManagerEndpoint $tenantARMEndpoint `
 -DirectoryTenantName $guestDirectoryTenantName `
 -Verbose
```

> [!IMPORTANT]
> Si votre administrateur Azure Stack Hub installe des mises à jour ou de nouveaux services à l’avenir, vous devrez peut-être réexécuter ce script.
>
> Faites-le à tout moment pour vérifier l’état des applications Azure Stack Hub dans votre annuaire.
>
> Pour résoudre les problèmes avec la création de machines virtuelles dans la fonctionnalité Disques managés (une nouvelle fonctionnalité disponible dans la mise à jour 1808), un nouveau **fournisseur de ressources de disque** a été ajouté nécessitant la réexécution de ce script.

### <a name="direct-users-to-sign-in"></a>Inviter les utilisateurs à se connecter

Maintenant que Marie et vous-même avez effectué les étapes d’intégration de l’annuaire de Marie, celle-ci peut inviter les utilisateurs de Fabrikam à se connecter. Les utilisateurs de Fabrikam (ceux avec le suffixe fabrikam.onmicrosoft.com) se connectent en accédant à https\://portal.local.azurestack.external.

Marie invitera les [principaux étrangers](/azure/role-based-access-control/rbac-and-directory-admin-roles) dans l’annuaire Fabrikam (les utilisateurs dans l’annuaire Fabrikam sans le suffixe fabrikam.onmicrosoft.com) à se connecter à l’aide de https\://portal.local.azurestack.external/fabrikam.onmicrosoft.com. S’ils n’utilisent pas cette URL, ils sont dirigés vers leur annuaire par défaut (Fabrikam) et reçoivent une erreur indiquant que leur administrateur n’a pas donné son consentement.

## <a name="disable-multi-tenancy"></a>Désactiver la mutualisation

Si vous ne souhaitez plus bénéficier de la mutualisation dans Azure Stack Hub, vous pouvez la désactiver en suivant les étapes suivantes dans l’ordre :

1. En tant qu’administrateur de l’annuaire invité (Mary dans ce scénario), exécutez *Unregister-AzsWithMyDirectoryTenant*. L’applet de commande désinstalle toutes les applications Azure Stack Hub du nouvel annuaire.

    ``` PowerShell
    ## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint.
    $tenantARMEndpoint = "https://management.local.azurestack.external"
        
    ## Replace the value below with the guest tenant directory.
    $guestDirectoryTenantName = "fabrikam.onmicrosoft.com"
    
    Unregister-AzsWithMyDirectoryTenant `
     -TenantResourceManagerEndpoint $tenantARMEndpoint `
     -DirectoryTenantName $guestDirectoryTenantName `
     -Verbose 
    ```

2. En tant qu’administrateur de service d’Azure Stack Hub (vous dans ce scénario), exécutez *Unregister-AzSGuestDirectoryTenant*.

    ``` PowerShell
    ## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint.
    $adminARMEndpoint = "https://adminmanagement.local.azurestack.external"
    
    ## Replace the value below with the Azure Stack Hub directory
    $azureStackDirectoryTenant = "contoso.onmicrosoft.com"
    
    ## Replace the value below with the guest tenant directory. 
    $guestDirectoryTenantToBeDecommissioned = "fabrikam.onmicrosoft.com"
    
    ## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
    $ResourceGroupName = "system.local"
    
    Unregister-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
     -DirectoryTenantName $azureStackDirectoryTenant `
     -GuestDirectoryTenantName $guestDirectoryTenantToBeDecommissioned `
     -ResourceGroupName $ResourceGroupName
    ```

    > [!WARNING]
    > Les étapes de désactivation d’une architecture mutualisée doivent être effectuées dans l’ordre. L’étape 1 échoue si l’étape 2 est terminée en premier.

## <a name="retrieve-azure-stack-hub-identity-health-report"></a>Récupérer le rapport d’intégrité des identités Azure Stack Hub 

Remplacez les espaces réservés `<region>`, `<domain>` et `<homeDirectoryTenant>`, puis exécutez l’applet de commande suivante en tant qu’administrateur Azure Stack Hub.

```powershell

$AdminResourceManagerEndpoint = "https://adminmanagement.<region>.<domain>"
$DirectoryName = "<homeDirectoryTenant>.onmicrosoft.com"
$healthReport = Get-AzsHealthReport -AdminResourceManagerEndpoint $AdminResourceManagerEndpoint -DirectoryTenantName $DirectoryName
Write-Host "Healthy directories: "
$healthReport.directoryTenants | Where status -EQ 'Healthy' | Select -Property tenantName,tenantId,status | ft


Write-Host "Unhealthy directories: "
$healthReport.directoryTenants | Where status -NE 'Healthy' | Select -Property tenantName,tenantId,status | ft
```

### <a name="update-azure-ad-tenant-permissions"></a>Mettre à jour les autorisations de locataire Azure AD

Cette action désactivera l’alerte dans Azure Stack Hub, indiquant qu’un annuaire a besoin d’une mise à jour. Exécutez les commandes suivantes à partir du dossier **Azurestack-tools-master/identity** :

```powershell
Import-Module ..\Connect\AzureStack.Connect.psm1
Import-Module ..\Identity\AzureStack.Identity.psm1

$adminResourceManagerEndpoint = "https://adminmanagement.<region>.<domain>"

# This is the primary tenant Azure Stack is registered to:
$homeDirectoryTenantName = "<homeDirectoryTenant>.onmicrosoft.com"

Update-AzsHomeDirectoryTenant -AdminResourceManagerEndpoint $adminResourceManagerEndpoint `
   -DirectoryTenantName $homeDirectoryTenantName -Verbose
```

Le script vous invite à entrer des informations d’identification d’administration sur le locataire Azure AD, et prend plusieurs minutes pour s’exécuter. L’alerte devrait disparaître après l’exécution de l’applet de commande.

## <a name="next-steps"></a>Étapes suivantes

- [Gérer les fournisseurs délégués](azure-stack-delegated-provider.md)
- [Concepts clés d’Azure Stack Hub](azure-stack-overview.md)
- [Gérer l’utilisation et la facturation pour Azure Stack Hub comme fournisseur de solutions cloud](azure-stack-add-manage-billing-as-a-csp.md)
- [Ajouter un locataire pour l’utilisation et la facturation sur Azure Stack Hub](azure-stack-csp-howto-register-tenants.md)
