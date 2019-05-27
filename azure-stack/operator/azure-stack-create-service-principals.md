---
title: Gérer un principal de service pour Azure Stack | Microsoft Docs
description: Explique comment gérer un principal de service utilisable avec le contrôle d’accès en fonction du rôle dans Azure Resource Manager pour gérer l’accès aux ressources.
services: azure-resource-manager
documentationcenter: na
author: sethmanheim
manager: femila
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/17/2019
ms.author: sethm
ms.lastreviewed: 05/17/2019
ms.openlocfilehash: 1bb07c1725d5c8ed81ec4b8ccc546a4d41dc64e0
ms.sourcegitcommit: 8cb2b567e9914d4d07e754d95c0864aa55868579
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65855305"
---
# <a name="provide-applications-access-to-azure-stack"></a>Fournir l’accès des applications à Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Lorsqu’une application a besoin d’accès pour déployer ou configurer des ressources par le biais d’Azure Resource Manager dans Azure Stack, créez un principal de service qui constitue l’information d’identification de votre application. Vous pouvez ensuite lui déléguer uniquement les autorisations nécessaires.  

Par exemple, vous avez peut-être un outil de gestion de la configuration qui utilise Azure Resource Manager pour inventorier les ressources Azure. Dans ce scénario, vous pouvez créer un principal de service, lui accorder le rôle de lecteur et limiter l’outil de gestion de la configuration à un accès en lecture seule. 

De préférence, utilisez des principaux de service plutôt que d’exécuter l’application avec vos propres informations d’identification, car :

 - Vous pouvez attribuer au principal de service des autorisations différentes des vôtres. En règle générale, ces autorisations sont strictement limitées à ce que l’application doit faire.
 - Il est inutile de modifier les informations d’identification de l’application si vos responsabilités évoluent.
 - Vous pouvez utiliser un certificat pour automatiser l’authentification lors de l’exécution d’un script sans assistance.  

## <a name="getting-started"></a>Prise en main

Selon la façon dont vous avez déployé Azure Stack, commencez par créer un principal de service. Ce document décrit la création d’un principal de service pour :

- Azure Active Directory (Azure AD). Azure AD est un service cloud et multilocataire de gestion des répertoires et des identités. Vous pouvez utiliser Azure AD avec une instance Azure Stack connectée.
- Services de fédération Active Directory (AD FS). AD FS simplifie et sécurise la fédération des identités et l’authentification unique (SSO) sur le web. Vous pouvez utiliser AD FS avec des instances Azure Stack connectées et déconnectées.

Une fois que vous avez créé le principal de service, une série d’étapes communes à AD FS et à Azure Active Directory permet de déléguer des autorisations à ce rôle.

## <a name="manage-service-principal-for-azure-ad"></a>Gérer un principal de service pour Azure AD

Si vous avez déployé Azure Stack avec Azure Active Directory (Azure AD) comme service de gestion des identités, vous pouvez créer des principaux de service comme vous le feriez pour Azure. Cette rubrique explique comment suivre ces étapes sur le portail. Vérifiez que vous disposez des [autorisations Azure AD exigées]((/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions) avant de commencer.

### <a name="create-service-principal"></a>Créer un principal du service

Dans cette section, vous créez une application (principal de service) dans Azure AD qui représente votre application.

1. Connectez-vous à votre compte Azure via le [portail Azure](https://portal.azure.com).
2. Sélectionnez **Azure Active Directory** > **Inscriptions d’applications** > **Nouvelle inscription**.
3. Fournissez un nom et une URL pour l’application. 
4. Sélectionnez les **Types de comptes pris en charge**.
5.  Ajoutez une URI pour l’application. Sélectionnez **Web** pour le type d’application que vous souhaitez créer. Après avoir défini les valeurs, sélectionnez **S’inscrire**.

Vous avez créé un principal de service pour votre application.

### <a name="get-credentials"></a>Récupérer les informations d’identification

Si vous vous connectez par programmation, utilisez l’ID de votre application et une clé d’authentification pour une application Web / API. Pour obtenir ces valeurs, procédez comme suit :

1. Sélectionnez **Azure Active Directory** > **Inscriptions des applications**. Sélectionnez votre application.

2. Copiez l’**ID d’application** et stockez-le dans votre code d’application. Les applications de la section Exemples d’applications font référence à cette valeur en tant qu’ID de client.

3. Pour générer une clé d’authentification pour une API/application web, sélectionnez **Certificats & secrets**. Sélectionnez **Nouveau secret client**.

4. Fournissez une description de la clé et la durée de la clé. Quand vous avez terminé, sélectionnez **Ajouter**.

Après avoir enregistré la clé, la valeur de la clé s’affiche. Copiez cette valeur dans Bloc-notes ou un autre emplacement temporaire, car vous ne pourrez pas récupérer la clé ultérieurement. Vous fournissez la valeur de la clé avec l’ID d’application pour vous connecter en tant qu’application. Stockez la valeur de la clé dans un emplacement où votre application peut la récupérer.

![Clé enregistrée](./media/azure-stack-create-service-principal/create-service-principal-in-azure-stack-secret.png)

Une fois terminé, vous pouvez attribuer un rôle à votre application.

## <a name="manage-service-principal-for-ad-fs"></a>Gérer le principal de service pour AD FS

Si vous avez déployé Azure Stack avec les services de fédération Active Directory (AD FS) comme service de gestion des identités, utilisez PowerShell pour créer un principal de service, attribuer un rôle pour l’accès et vous connecter avec cette identité.

Pour créer votre principal de service avec AD FS, deux méthodes s’offrent à vous. Vous pouvez :
 - [Créer un principal de service à l’aide d’un certificat](azure-stack-create-service-principals.md#create-a-service-principal-using-a-certificate)
 - [Créer un principal de service à l’aide d’un secret client](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret)

Tâches de gestion des principaux de service AD FS.

| Type | Action |
| --- | --- |
| Certificat AD FS | [Créer](azure-stack-create-service-principals.md#create-a-service-principal-using-a-certificate) |
| Certificat AD FS | [Mettre à jour](azure-stack-create-service-principals.md#update-certificate-for-service-principal-for-ad-fs) |
| Certificat AD FS | [Remove](azure-stack-create-service-principals.md#remove-a-service-principal-for-ad-fs) |
| Secret client AD FS | [Créer](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret) |
| Secret client AD FS | [Mettre à jour](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret) |
| Secret client AD FS | [Remove](azure-stack-create-service-principals.md#remove-a-service-principal-for-ad-fs) |

### <a name="create-a-service-principal-using-a-certificate"></a>Créer un principal de service à l’aide d’un certificat

Quand vous créez un principal de service en recourant à AD FS pour la gestion des identités, vous pouvez utiliser un certificat.

#### <a name="certificate"></a>Certificat

Un certificat est obligatoire.

**Spécifications du certificat**

 - Le fournisseur de services de chiffrement (CSP) doit être un fournisseur de clé hérité.
 - Le certificat doit se présenter sous la forme d’un fichier PFX, car la procédure requiert à la fois les clés publiques et privées. Les serveurs Windows utilisent des fichiers .pfx contenant le fichier de clé publique (fichier de certificat SSL) et le fichier de clé privée associé.
 - Pour la production, le certificat doit être émis par une autorité de certification interne ou une autorité de certification publique. Si vous utilisez une autorité de certification publique, vous devez l’inclure dans l’image du système d’exploitation de base dans le cadre du projet Microsoft Trusted Root Authority Program. La liste complète est disponible dans l’article [Programme de certification racine approuvé Microsoft : participants](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).
 - Votre infrastructure Azure Stack doit avoir accès au réseau de l’emplacement de la liste de révocation des certificats (CRL) de l’autorité de certification publiée dans le certificat. Cette CRL doit être un point de terminaison HTTP.

#### <a name="parameters"></a>parameters

Les informations suivantes sont nécessaires en entrée pour les paramètres Automation :

|Paramètre|Description|Exemples|
|---------|---------|---------|
|Nom|Nom du compte SPN|MyAPP|
|ClientCertificates|Tableau d’objets de certificat|Certificat X509|
|ClientRedirectUris<br>(facultatif)|URI de redirection de l’application|-|

#### <a name="use-powershell-to-create-a-service-principal"></a>Utiliser PowerShell pour créer un principal de service

1. Ouvrez une session Windows PowerShell avec élévation de privilèges et exécutez les applets de commande suivantes :

   ```powershell  
    # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
    $Creds = Get-Credential

    # Creating a PSSession to the ERCS PrivilegedEndpoint
    $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

    # If you have a managed certificate use the Get-Item command to retrieve your certificate from your certificate location.
    # If you don't want to use a managed certificate, you can produce a self signed cert for testing purposes: 
    # $Cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange
    $Cert = Get-Item "<YourCertificateLocation>"
    
    $ServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {New-GraphApplication -Name '<YourAppName>' -ClientCertificates $using:cert}
    $AzureStackInfo = Invoke-Command -Session $Session -ScriptBlock {Get-AzureStackStampInformation}
    $Session | Remove-PSSession

    # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. This is read from the AzureStackStampInformation output of the ERCS VM.
    $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager

    # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. This is read from the AzureStackStampInformation output of the ERCS VM.
    $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"

    # TenantID for the stamp. This is read from the AzureStackStampInformation output of the ERCS VM.
    $TenantID = $AzureStackInfo.AADTenantID

    # Register an AzureRM environment that targets your Azure Stack instance
    Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

    # Set the GraphEndpointResourceId value
    Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience `
    -EnableAdfsAuthentication:$true

    Add-AzureRmAccount -EnvironmentName "AzureStackUser" `
    -ServicePrincipal `
    -CertificateThumbprint $ServicePrincipal.Thumbprint `
    -ApplicationId $ServicePrincipal.ClientId `
    -TenantId $TenantID

    # Output the SPN details
    $ServicePrincipal

   ```
   > [!Note]  
   > À des fins de validation, un certificat auto-signé peut être créé à l’aide de l’exemple ci-dessous :

   ```powershell  
   $Cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<yourappname>" -KeySpec KeyExchange
   ```


2. Quand Automation a terminé, il affiche les informations nécessaires pour utiliser le SPN. Il est recommandé de stocker la sortie en vue d’une utilisation ultérieure.

   Par exemple : 

   ```shell
   ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
   ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
   Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
   ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
   PSComputerName        : azs-ercs01
   RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
   ```

### <a name="update-certificate-for-service-principal-for-ad-fs"></a>Mettre à jour le certificat du principal de service pour AD FS

Si vous avez déployé Azure Stack avec AD FS, vous pouvez utiliser PowerShell pour mettre à jour le secret d’un principal de service.

Le script est exécuté depuis le point de terminaison privilégié sur une machine virtuelle ERCS.

#### <a name="parameters"></a>parameters

Les informations suivantes sont nécessaires en entrée pour les paramètres Automation :

|Paramètre|Description|Exemples|
|---------|---------|---------|
|Nom|Nom du compte SPN|MyAPP|
|ApplicationIdentifier|Identificateur unique|S-1-5-21-1634563105-1224503876-2692824315-2119|
|ClientCertificate|Tableau d’objets de certificat|Certificat X509|

#### <a name="example-of-updating-service-principal-for-ad-fs"></a>Exemple de mise à jour du principal de service pour AD FS

L’exemple crée un certificat auto-signé. Quand vous exécutez les applets de commande dans un déploiement de production, utilisez [Get-Item](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Management/Get-Item) afin de récupérer l’objet de certificat pour le certificat que vous voulez utiliser.

1. Ouvrez une session Windows PowerShell avec élévation de privilèges et exécutez les applets de commande suivantes :

     ```powershell
          # Creating a PSSession to the ERCS PrivilegedEndpoint
          $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

          # This produces a self signed cert for testing purposes. It is preferred to use a managed certificate for this.
          $NewCert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange

          $RemoveServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier  S-1-5-21-1634563105-1224503876-2692824315-2120 -ClientCertificates $NewCert}

          $Session | Remove-PSSession
     ```

2. Au terme de l’automatisation, la valeur de l’empreinte numérique mise à jour nécessaire pour l’authentification SPN s’affiche.

     ```Shell  
          ClientId              : 
          Thumbprint            : AF22EE716909041055A01FE6C6F5C5CDE78948E9
          ApplicationName       : Azurestack-ThomasAPP-3e5dc4d2-d286-481c-89ba-57aa290a4818
          ClientSecret          : 
          RunspaceId            : a580f894-8f9b-40ee-aa10-77d4d142b4e5
     ```

### <a name="create-a-service-principal-using-a-client-secret"></a>Créer un principal de service à l’aide d’un secret client

Quand vous créez un principal de service en recourant à AD FS pour la gestion des identités, vous pouvez utiliser un certificat. Utilisez le point de terminaison privilégié pour exécuter les applets de commande.

Ces scripts sont exécutés à partir du point de terminaison privilégié sur une machine virtuelle ERCS. Pour plus d’informations sur le point de terminaison privilégié, consultez [Utilisation du point de terminaison privilégié dans Azure Stack](azure-stack-privileged-endpoint.md).

#### <a name="parameters"></a>parameters

Les informations suivantes sont nécessaires en entrée pour les paramètres Automation :

| Paramètre | Description | Exemples |
|----------------------|--------------------------|---------|
| Nom | Nom du compte SPN | MyAPP |
| GenerateClientSecret | Créer un secret |  |

#### <a name="use-the-ercs-privilegedendpoint-to-create-the-service-principal"></a>Utiliser le PrivilegedEndpoint ERCS pour créer le principal de service

1. Ouvrez une session Windows PowerShell avec élévation de privilèges et exécutez les applets de commande suivantes :

     ```powershell  
      # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
     $Creds = Get-Credential

     # Creating a PSSession to the ERCS PrivilegedEndpoint
     $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

     # Creating a SPN with a secre
     $ServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {New-GraphApplication -Name '<YourAppName>' -GenerateClientSecret}
     $AzureStackInfo = Invoke-Command -Session $Session -ScriptBlock {Get-AzureStackStampInformation}
     $Session | Remove-PSSession

     # Output the SPN details
     $ServicePrincipal
     ```

2. Au terme de l’exécution des applets de commande, l’interpréteur de commandes affiche les informations nécessaires pour utiliser le SPN. Veillez à stocker le secret client.

     ```powershell  
     ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2623
     ClientId              : 8e0ffd12-26c8-4178-a74b-f26bd28db601
     Thumbprint            : 
     ApplicationName       : Azurestack-YourApp-6967581b-497e-4f5a-87b5-0c8d01a9f146
     ClientSecret          : 6RUZLRoBw3EebMDgaWGiowCkoko5_j_ujIPjA8dS
     PSComputerName        : 192.168.200.224
     RunspaceId            : 286daaa1-c9a6-4176-a1a8-03f543f90998
     ```

#### <a name="update-client-secret-for-a-service-principal-for-ad-fs"></a>Mettre à jour le secret client d’un principal de service pour AD FS

Un nouveau secret client est généré automatiquement par l’applet de commande PowerShell.

Le script est exécuté depuis le point de terminaison privilégié sur une machine virtuelle ERCS.

##### <a name="parameters"></a>parameters

Les informations suivantes sont nécessaires en entrée pour les paramètres Automation :

| Paramètre | Description | Exemples |
|-----------------------|-----------------------------------------------------------------------------------------------------------|------------------------------------------------|
| ApplicationIdentifier | Identificateur unique. | S-1-5-21-1634563105-1224503876-2692824315-2119 |
| ChangeClientSecret | Change le secret client avec une période de substitution de 2 880 minutes pendant laquelle l’ancien secret est toujours valide. |  |
| ResetClientSecret | Change le secret client immédiatement. |  |

##### <a name="example-of-updating-a-client-secret-for-ad-fs"></a>Exemple de mise à jour d’un secret client pour AD FS

L’exemple utilise le paramètre **ResetClientSecret** qui change immédiatement le secret client.

1. Ouvrez une session Windows PowerShell avec élévation de privilèges et exécutez les applets de commande suivantes :

     ```powershell  
          # Creating a PSSession to the ERCS PrivilegedEndpoint
          $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

          # This produces a self signed cert for testing purposes. It is preferred to use a managed certificate for this.
          $NewCert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange

          $UpdateServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier  S-1-5-21-1634563105-1224503876-2692824315-2120 -ResetClientSecret}

          $Session | Remove-PSSession
     ```

2. Au terme de l’automatisation, le nouveau secret généré nécessaire pour l’authentification SPN s’affiche. Veillez à stocker le nouveau secret client.

     ```powershell  
          ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2120
          ClientId              :  
          Thumbprint            : 
          ApplicationName       : Azurestack-Yourapp-6967581b-497e-4f5a-87b5-0c8d01a9f146
          ClientSecret          : MKUNzeL6PwmlhWdHB59c25WDDZlJ1A6IWzwgv_Kn
          RunspaceId            : 6ed9f903-f1be-44e3-9fef-e7e0e3f48564
     ```

### <a name="remove-a-service-principal-for-ad-fs"></a>Supprimer un principal de service pour AD FS

Si vous avez déployé Azure Stack avec AD FS, vous pouvez utiliser PowerShell pour supprimer un principal de service.

Le script est exécuté depuis le point de terminaison privilégié sur une machine virtuelle ERCS.

#### <a name="parameters"></a>parameters

Les informations suivantes sont nécessaires en entrée pour les paramètres Automation :

|Paramètre|Description|Exemples|
|---------|---------|---------|
| Paramètre | Description | Exemples |
| ApplicationIdentifier | Identificateur unique | S-1-5-21-1634563105-1224503876-2692824315-2119 |

> [!Note]  
> Pour afficher la liste de tous les principaux de service existants et de leur identificateur d’application, vous pouvez utiliser la commande get-graphapplication.

#### <a name="example-of-removing-the-service-principal-for-ad-fs"></a>Exemple de suppression du principal de service pour AD FS

```powershell  
     Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
     $Creds = Get-Credential

     # Creating a PSSession to the ERCS PrivilegedEndpoint
     $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

     $UpdateServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {Remove-GraphApplication -ApplicationIdentifier S-1-5-21-1634563105-1224503876-2692824315-2119}

     $Session | Remove-PSSession
```

## <a name="assign-a-role"></a>Attribuer un rôle

Pour accéder aux ressources de votre abonnement, vous devez affecter un rôle à l’application. Vous devez décider du rôle qui doit représenter les autorisations appropriées pour l’application. Pour en savoir plus sur les rôles disponibles, consultez [RBAC : Rôles intégrés]((/azure/role-based-access-control/built-in-roles).

Vous pouvez définir l’étendue au niveau de l’abonnement, du groupe de ressources ou de la ressource. Les autorisations sont héritées des niveaux inférieurs de l’étendue (par exemple, l’ajout d’une application au rôle Lecteur pour un groupe de ressources signifie qu’elle peut lire le groupe de ressources et toutes les ressources qu’il contient).

1. Sur le portail Azure Stack, accédez au niveau d’étendue que vous souhaitez attribuer à l’application. Par exemple, pour affecter un rôle sur l’étendue de l’abonnement, sélectionnez **Abonnements**. Vous pouvez également sélectionner un groupe de ressources ou une ressource.

2. Sélectionnez l’abonnement (groupe de ressources ou ressource) auquel l’application doit être affectée.

     ![Sélectionner l’abonnement pour l’assignation](./media/azure-stack-create-service-principal/image16.png)

3. Sélectionnez **Contrôle d’accès (IAM)** .

     ![Sélectionner l’accès](./media/azure-stack-create-service-principal/image17.png)

4. Sélectionnez **Ajouter une attribution de rôle**.

5. Sélectionnez le rôle que vous souhaitez affecter à l’application.

6. Recherchez votre application et sélectionnez-la.

7. Sélectionnez **OK** pour finaliser l’affectation du rôle. Votre application apparaît dans la liste des utilisateurs affectés à un rôle pour cette étendue.

Maintenant que vous avez créé un principal de service et que vous lui avez attribué un rôle, vous pouvez commencer à l’utiliser au sein de votre application pour accéder aux ressources Azure Stack.  

## <a name="next-steps"></a>Étapes suivantes

[Ajouter des utilisateurs pour AD FS](azure-stack-add-users-adfs.md)  
[Gérer les autorisations utilisateur](azure-stack-manage-permissions.md)  
[Documentation Azure Active Directory](https://docs.microsoft.com/azure/active-directory)  
[Active Directory Federation Services](https://docs.microsoft.com/windows-server/identity/active-directory-federation-services)
