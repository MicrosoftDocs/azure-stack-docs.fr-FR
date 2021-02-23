---
title: Configurer l’architecture multilocataire dans Azure Stack Hub
description: Découvrez comment configurer une mutualisation pour des locataires Azure Active Directory invités dans Azure Stack Hub.
author: BryanLa
ms.topic: how-to
ms.date: 01/26/2021
ms.author: bryanla
ms.reviewer: bryanr
ms.lastreviewed: 01/26/2021
ms.openlocfilehash: 3de6c5db42285f90e1d4ce6c1ebf6736d7ce4863
ms.sourcegitcommit: d542b68b299b73e045f30916afb6018e365e9db6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975943"
---
# <a name="configure-multi-tenancy-in-azure-stack-hub"></a>Configurer l’architecture multilocataire dans Azure Stack Hub

Vous pouvez configurer Azure Stack Hub afin de prendre en charge les connexions d’utilisateurs résidant dans d’autres annuaires Azure Active Directory (Azure AD), en les autorisant à utiliser les services dans Azure Stack Hub. Ces annuaires ayant une relation « invité » avec votre Azure Stack Hub, ils sont considérés comme des locataires Azure AD invités. Par exemple, considérez le scénario suivant :

- Vous êtes l’administrateur de service de contoso.onmicrosoft.com, le locataire Azure AD de base fournissant des services de gestion des identités et des accès à votre Azure Stack Hub.
- Marie est l’administratrice d’annuaire de fabrikam.onmicrosoft.com, le locataire Azure AD invité où se trouvent les utilisateurs invités.
- La société de Mary (Fabrikam) utilise les services IaaS et PaaS de votre entreprise. Fabrikam souhaite autoriser les utilisateurs de l’annuaire invité (fabrikam.onmicrosoft.com) à se connecter et à utiliser les ressources Azure Stack Hub sécurisées par contoso.onmicrosoft.com.

Ce guide décrit les étapes requises dans le cadre de ce scénario afin d’activer ou de désactiver la mutualisation dans Azure Stack Hub pour un locataire d’annuaire invité. Mary et vous accomplissez ce processus en inscrivant ou désinscrivant le locataire d’annuaire invité, ce qui a pour effet d’activer ou de désactiver les connexions Azure Stack Hub et la consommation du service par les utilisateurs de Fabrikam. 

Si vous êtes un fournisseur de solutions cloud (CSP), vous pouvez [configurer et gérer un Azure Stack Hub mutualisé](azure-stack-add-manage-billing-as-a-csp.md). 

## <a name="prerequisites"></a>Prérequis

Avant d’inscrire ou de désinscrire un annuaire invité, Mary et vous devez suivre des étapes d’administration pour vos locataires Azure AD respectifs : l’annuaire de base Azure Stack Hub (Contoso) et l’annuaire invité (Fabrikam) :

 - [Installez](powershell-install-az-module.md) et [configurez](azure-stack-powershell-configure-admin.md) PowerShell pour Azure Stack Hub.
 - [Téléchargez les outils Azure Stack Hub](azure-stack-powershell-download.md) et importez les modules de connexion et d’identité :

    ```powershell
    Import-Module .\Identity\AzureStack.Identity.psm1
    ```

## <a name="register-a-guest-directory"></a>Inscrire un annuaire invité

Pour inscrire un annuaire invité en vue d’une mutualisation, vous devez configurer tant l’annuaire Azure Stack Hub de base que l’annuaire invité.

#### <a name="configure-azure-stack-hub-directory"></a>Configurer l’annuaire Azure Stack Hub

En tant qu’administrateur de service de contoso.onmicrosoft.com, vous devez commencer par intégrer le locataire d’annuaire invité de Fabrikam à Azure Stack Hub. Le script suivant configure Azure Resource Manager pour accepter les connexions d’utilisateurs et de principaux de service dans le locataire fabrikam.onmicrosoft.com :

```powershell  
## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint, formatted as adminmanagement.<region>.<FQDN>.
$adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

## Replace the value below with the Azure Stack Hub directory
$azureStackDirectoryTenant = "contoso.onmicrosoft.com"

## Replace the value below with the guest directory tenant. 
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

#### <a name="configure-guest-directory"></a>Configurer l’annuaire invité

Ensuite, Mary (l’administratrice d’annuaire de Fabrikam) doit inscrire Azure Stack Hub auprès de l’annuaire invité fabrikam.onmicrosoft.com en exécutant le script suivant :

```powershell
## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint, formatted as management.<region>.<FQDN>.
$tenantARMEndpoint = "https://management.local.azurestack.external"
    
## Replace the value below with the guest directory tenant.
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

Enfin, Mary peut inviter les utilisateurs Fabrikam disposant de comptes @fabrikam.onmicrosoft.com à se connecter en visitant le [portail utilisateur Azure Stack Hub](../user/azure-stack-use-portal.md). Pour les systèmes à plusieurs nœuds, l’URL du portail utilisateur est au format `https://management.<region>.<FQDN>`. Pour un déploiement d’ASDK, l’URL est `https://portal.local.azurestack.external`.

Marie doit également inviter les principaux étrangers (utilisateurs dans l’annuaire de Fabrikam sans le suffixe fabrikam.onmicrosoft.com) à se connecter à l’aide de `https://<user-portal-url>/fabrikam.onmicrosoft.com`. S’ils ne spécifient pas l’annuaire de locataire `/fabrikam.onmicrosoft.com` dans l’URL, ils sont dirigés vers l’annuaire par défaut et reçoivent une erreur indiquant que leur administrateur n’a pas donné son consentement.

## <a name="unregister-a-guest-directory"></a>Désinscrire un annuaire invité

Si vous ne souhaitez plus autoriser les connexions aux services Azure Stack Hub à partir d’un locataire d’annuaire invité, vous pouvez désinscrire l’annuaire. Une fois encore, tant l’annuaire Azure Stack Hub de base que l’annuaire invité doivent être configurés :

1. En tant qu’administrateur de l’annuaire invité (Mary dans ce scénario), exécutez `Unregister-AzsWithMyDirectoryTenant`. L’applet de commande désinstalle toutes les applications Azure Stack Hub du nouvel annuaire.

    ``` PowerShell
    ## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint, formatted as management.<region>.<FQDN>.
    $tenantARMEndpoint = "https://management.local.azurestack.external"
        
    ## Replace the value below with the guest directory tenant.
    $guestDirectoryTenantName = "fabrikam.onmicrosoft.com"
    
    Unregister-AzsWithMyDirectoryTenant `
     -TenantResourceManagerEndpoint $tenantARMEndpoint `
     -DirectoryTenantName $guestDirectoryTenantName `
     -Verbose 
    ```

2. En tant qu’administrateur de service d’Azure Stack Hub (vous dans ce scénario), exécutez l’applet de commande `Unregister-AzSGuestDirectoryTenant` :

    ``` PowerShell
    ## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint, formatted as adminmanagement.<region>.<FQDN>.
    $adminARMEndpoint = "https://adminmanagement.local.azurestack.external"
    
    ## Replace the value below with the Azure Stack Hub directory
    $azureStackDirectoryTenant = "contoso.onmicrosoft.com"
    
    ## Replace the value below with the guest directory tenant. 
    $guestDirectoryTenantToBeDecommissioned = "fabrikam.onmicrosoft.com"
    
    ## Replace the value below with the name of the resource group in which the directory tenant resource was created (resource group must already exist).
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

## <a name="update-azure-ad-tenant-permissions"></a>Mettre à jour les autorisations de locataire Azure AD

Cette action a pour effet de supprimer une alerte dans Azure Stack Hub, indiquant qu’un annuaire a besoin d’une mise à jour. Exécutez la commande suivante à partir du dossier **Azurestack-tools-master/identity** :

```powershell
Import-Module ..\Identity\AzureStack.Identity.psm1

$adminResourceManagerEndpoint = "https://adminmanagement.<region>.<domain>"

# This is the primary tenant Azure Stack Hub is registered to:
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
