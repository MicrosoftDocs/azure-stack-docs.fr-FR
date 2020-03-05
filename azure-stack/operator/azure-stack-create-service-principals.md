---
title: Utiliser une identité d’application pour accéder aux ressources
description: Découvrez comment gérer un principal de service Azure Stack Hub. Vous pouvez utiliser la fonctionnalité RBAC (contrôle d’accès en fonction du rôle) sur un principal de service pour la connexion et l’accès aux ressources.
author: BryanLa
ms.author: bryanla
ms.topic: how-to
ms.date: 11/11/2019
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 1c96ee9520285e0bc2b9784fa5d310a1ec2ae60f
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77704809"
---
# <a name="use-an-app-identity-to-access-azure-stack-hub-resources"></a>Utiliser une identité d’application pour accéder aux ressources Azure Stack Hub

Une application qui a besoin pour déployer ou configurer des ressources via Azure Resource Manager doit être représentée par un principal de service. Tout comme un utilisateur est représenté par un principal d’utilisateur, un principal de service est un type de principal de sécurité qui représente une application. Le principal de service fournit une identité pour votre application, ce qui vous permet de ne déléguer à ce principal de service que les autorisations nécessaires.  

Par exemple, vous avez peut-être une application de gestion de la configuration qui utilise Azure Resource Manager pour inventorier les ressources Azure. Dans ce scénario, vous pouvez créer un principal de service, lui accorder le rôle de lecteur et limiter l’application de gestion de la configuration à un accès en lecture seule.

## <a name="overview"></a>Vue d’ensemble

À l’instar d’un principal d’utilisateur, un principal de service doit présenter des informations d'identification lors de l’authentification. Cette authentification se compose de deux éléments :

- Un **ID d’application**, parfois appelé ID client. Il s’agit d’un GUID qui identifie de façon unique l’inscription de l’application dans votre locataire Active Directory.
- Un **secret** associé à l’ID d’application. Vous pouvez générer une clé secrète client (similaire à un mot de passe) ou spécifier un certificat X509 (qui utilise sa clé publique).

Il est préférable d’exécuter une application sous l’identité d’un principal de service plutôt que sous un principal d’utilisateur pour la raison suivante :

 - Un principal de service peut utiliser un certificat X509 pour renforcer les **informations d’identification**.  
 - Vous pouvez affecter des **autorisations plus restrictives** à un principal de service. En règle générale, ces autorisations sont limitées à ce que l’application doit faire, conformément au *principe de privilège minimum*.
 - Les **informations d’identification et autorisations** du principal de service ne changent pas aussi fréquemment que les informations d’identification de l’utilisateur. Par exemple, lorsque les responsabilités d’un utilisateur changent, les exigences de mot de passe dictent un changement, ou que l’utilisateur quitte l’entreprise.

Vous commencez par créer une inscription d’application dans votre annuaire, ce qui a pour effet de créer un [objet principal de service](/azure/active-directory/develop/developer-glossary#service-principal-object) associé pour représenter l’identité de l’application dans l’annuaire. Ce document décrit le processus de création et de gestion d’un principal de service, en fonction de l’annuaire que vous avez choisi pour votre instance d’Azure Stack Hub :

- Azure Active Directory (Azure AD). Azure AD est un service cloud et multilocataire de gestion des répertoires et des identités. Vous pouvez utiliser Azure AD avec une instance d’Azure Stack Hub connectée.
- Services de fédération Active Directory (AD FS). AD FS simplifie et sécurise la fédération des identités et l’authentification unique (SSO) sur le Web. Vous pouvez utiliser AD FS avec des instances d’Azure Stack Hub connectées et déconnectées.

Vous allez apprendre à gérer un principal de service, puis à affecter le principal de service à un rôle en limitant l’accès de celui-ci aux ressources.

## <a name="manage-an-azure-ad-service-principal"></a>Gérer un principal de service Azure AD

Si vous avez déployé Azure Stack Hub avec Azure AD en tant que service de gestion des identités, vous pouvez créer des principaux de service, comme vous le faites pour Azure. Cette rubrique explique comment procéder via le portail Azure. Vérifiez que vous disposez des [autorisations Azure AD requises](/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions) avant de commencer.

### <a name="create-a-service-principal-that-uses-a-client-secret-credential"></a>Créer un principal de service utilisant une clé secrète client

Dans cette section, vous inscrivez votre application en utilisant le portail Azure qui crée l’objet principal de service dans votre locataire Azure AD. Dans cet exemple, le principal de service est créé avec une clé secrète client, mais le portail prend également en charge des informations d’identification basées sur un certificat X509.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec votre compte Azure.
2. Sélectionnez **Azure Active Directory** > **Inscriptions d’applications** > **Nouvelle inscription**.
3. Donnez un **nom** à l’application.
4. Sélectionnez les **Types de comptes pris en charge** appropriés.
5. Sous **URI de redirection**, sélectionnez **Web** comme type d’application, et spécifiez (éventuellement) un URI de redirection si votre application l’exige.
6. Après avoir défini les valeurs, sélectionnez **S’inscrire**. L’inscription d’application est créée et la page **Vue d’ensemble** s’affiche.
7. Copiez l’**ID d’application** pour l’utiliser dans le code de votre application. Cette valeur est également appelée ID client.
8. Pour générer une clé secrète client, sélectionnez la page **Certificats et secrets**. Sélectionnez **Nouveau secret client**.
9. Fournissez une **description** et un délai d’**expiration** pour le secret.
10. Quand vous avez terminé, sélectionnez **Ajouter**.
11. La valeur du secret s’affiche. Copiez et enregistrez cette valeur dans un autre emplacement, car vous ne pourrez pas la récupérer ultérieurement. Vous fournirez le secret avec l’ID d’application dans votre application cliente pendant la connexion au principal de service.

    ![Clé enregistrée dans les secrets du client](./media/azure-stack-create-service-principal/create-service-principal-in-azure-stack-secret.png)

## <a name="manage-an-ad-fs-service-principal"></a>Gérer un principal de service AD FS

Si vous avez déployé Azure Stack Hub avec AD FS en tant que service de gestion des identités, vous devez utiliser PowerShell pour gérer le principal de service. Des exemples sont fournis ci-dessous pour la gestion des informations d’identification du principal de service, qui montrent un certificat X509 et une clé secrète client.

Vous devez exécuter les scripts dans une console PowerShell avec élévation de privilèges (« Exécuter en tant qu’administrateur ») afin d’ouvrir une autre session sur une machine virtuelle qui héberge un point de terminaison privilégié pour votre instance d’Azure Stack Hub. Une fois la session de point de terminaison privilégié établie, des cmdlets supplémentaires exécutent et gèrent le principal de service. Pour plus d’informations sur le point de terminaison privilégié, consultez [Utilisation du point de terminaison privilégié dans Azure Stack Hub](azure-stack-privileged-endpoint.md).

### <a name="create-a-service-principal-that-uses-a-certificate-credential"></a>Créer un principal de service utilisant des informations d’identification de certificat

Lors de la création d’un certificat pour des informations de principal de service, les conditions suivantes doivent être remplies :

 - Pour la production, le certificat doit être émis par une autorité de certification interne ou une autorité de certification publique. Si vous utilisez une autorité de certification publique, vous devez l’inclure dans l’image du système d’exploitation de base dans le cadre du projet Microsoft Trusted Root Authority Program. La liste complète est disponible dans l’article [Programme de certification racine approuvé Microsoft : participants](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca). Un exemple de création de certificat de test « auto-signé » s’affichera plus tard pendant la [mise à jour des informations d’identification du certificat du principal de service](#update-a-service-principals-certificate-credential). 
 - Le fournisseur de services de chiffrement doit être spécifié en tant que fournisseur de clés du fournisseur de services de chiffrement (CSP) hérité Microsoft.
 - Le certificat doit se présenter sous la forme d’un fichier PFX, car la procédure requiert à la fois les clés publiques et privées. Les serveurs Windows utilisent des fichiers .pfx contenant le fichier de clé publique (fichier de certificat SSL) et le fichier de clé privée associé.
 - Votre infrastructure Azure Stack Hub doit avoir accès au réseau de l’emplacement de la liste de révocation de certificats de l’autorité de certification publiée dans le certificat. Cette CRL doit être un point de terminaison HTTP.

Une fois que vous avez un certificat, utilisez le script PowerShell ci-dessous pour inscrire votre application et créer un principal de service. Vous utilisez également le principal de service pour vous connecter à Azure. Substituez vos propres valeurs aux espaces réservés suivants :

| Espace réservé | Description | Exemple |
| ----------- | ----------- | ------- |
| \<PepVM\> | Nom de la machine virtuelle de point de terminaison privilégié sur votre instance d’Azure Stack Hub. | « AzS-ERCS01 » |
| \<YourCertificateLocation\> | Emplacement de votre certificat X509 dans le magasin de certificats local. | « Cert:\CurrentUser\My\AB5A8A3533CC7AA2025BF05120117E06DE407B34 » |
| \<YourAppName\> | Nom descriptif pour la nouvelle inscription d’application. | « Mon outil de gestion » |

1. Ouvrez une session Windows PowerShell privilégiée et exécutez le script suivants :

   ```powershell  
    # Sign in to PowerShell interactively, using credentials that have access to the VM running the Privileged Endpoint (typically <domain>\cloudadmin)
    $Creds = Get-Credential

    # Create a PSSession to the Privileged Endpoint VM
    $Session = New-PSSession -ComputerName "<PepVm>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

    # Use the Get-Item cmdlet to retrieve your certificate.
    # If you don't want to use a managed certificate, you can produce a self signed cert for testing purposes: 
    # $Cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange
    $Cert = Get-Item "<YourCertificateLocation>"
    
    # Use the privileged endpoint to create the new app registration (and service principal object)
    $SpObject = Invoke-Command -Session $Session -ScriptBlock {New-GraphApplication -Name "<YourAppName>" -ClientCertificates $using:cert}
    $AzureStackInfo = Invoke-Command -Session $Session -ScriptBlock {Get-AzureStackStampInformation}
    $Session | Remove-PSSession

    # Using the stamp info for your Azure Stack Hub instance, populate the following variables:
    # - AzureRM endpoint used for Azure Resource Manager operations 
    # - Audience for acquiring an OAuth token used to access Graph API 
    # - GUID of the directory tenant
    $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager
    $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"
    $TenantID = $AzureStackInfo.AADTenantID

    # Register and set an AzureRM environment that targets your Azure Stack Hub instance
    Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint $ArmEndpoint

    # Sign in using the new service principal identity
    $SpSignin = Connect-AzureRmAccount -Environment "AzureStackUser" `
    -ServicePrincipal `
    -CertificateThumbprint $SpObject.Thumbprint `
    -ApplicationId $SpObject.ClientId `
    -TenantId $TenantID

    # Output the service principal details
    $SpObject

   ```
   
2. Une fois l’exécution du script terminée, les informations sur l’inscription de l’application s’affichent, incluant les informations d’identification du principal de service. Comme indiqué, les propriétés `ClientID` et `Thumbprint` sont utilisées pour se connecter sous l’identité du principal de service. Une fois la connexion établie, l’identité du principal de service est utilisée pour l’autorisation subséquente et l’accès d’Azure Resource Manager aux ressources managées.

   ```shell
   ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
   ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
   Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
   ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
   ClientSecret          :
   PSComputerName        : azs-ercs01
   RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
   ```

Gardez votre session de console PowerShell ouverte, car vous aller l’utiliser avec la valeur `ApplicationIdentifier` dans la section suivante.

### <a name="update-a-service-principals-certificate-credential"></a>Mettre à jour les informations d’identification du certificat du principal de service

À présent que vous avez créé un principal de service, cette section vous montre comment :

1. Créer un certificat X509 auto-signé à des fins de test.
2. Mettre à jour les informations d’identification du principal de service, en mettant à jour sa propriété **Thumbprint** pour la faire correspondre au nouveau certificat.

Mettre à jour les informations d’identification du certificat à l’aide de PowerShell, en substituant vos propres valeurs aux espaces réservés suivants :

| Espace réservé | Description | Exemple |
| ----------- | ----------- | ------- |
| \<PepVM\> | Nom de la machine virtuelle de point de terminaison privilégié sur votre instance d’Azure Stack Hub. | « AzS-ERCS01 » |
| \<YourAppName\> | Nom descriptif pour la nouvelle inscription d’application. | « Mon outil de gestion » |
| \<YourCertificateLocation\> | Emplacement de votre certificat X509 dans le magasin de certificats local. | « Cert:\CurrentUser\My\AB5A8A3533CC7AA2025BF05120117E06DE407B34 » |
| \<AppIdentifier\> | Identificateur affecté à l’inscription de l’application. | « S-1-5-21-1512385356-3796245103-1243299919-1356 » |

1. Ouvrez une session Windows PowerShell avec élévation de privilèges, puis exécutez les cmdlets suivantes :

     ```powershell
     # Create a PSSession to the PrivilegedEndpoint VM
     $Session = New-PSSession -ComputerName "<PepVM>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

     # Create a self-signed certificate for testing purposes. 
     $NewCert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange
     # In production, use Get-Item and a managed certificate instead.
     # $Cert = Get-Item "<YourCertificateLocation>"

     # Use the privileged endpoint to update the certificate thumbprint, used by the service principal associated with <AppIdentifier>
     $SpObject = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier "<AppIdentifier>" -ClientCertificates $using:NewCert}
     $Session | Remove-PSSession

     # Output the updated service principal details
     $SpObject
     ```

2. Une fois l’exécution du script terminée, les informations mises à jour sur l’inscription de l’application s’affichent, incluant la valeur Thumbprint du nouveau certificat auto-signé.

     ```Shell  
     ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
     ClientId              : 
     Thumbprint            : AF22EE716909041055A01FE6C6F5C5CDE78948E9
     ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
     ClientSecret          : 
     PSComputerName        : azs-ercs01
     RunspaceId            : a580f894-8f9b-40ee-aa10-77d4d142b4e5
     ```

### <a name="create-a-service-principal-that-uses-client-secret-credentials"></a>Créer un principal de service utilisant les informations d’identification de la clé secrète client

> [!IMPORTANT]
> L’utilisation d’une clé secrète client est moins sécurisée que l’utilisation d’informations d’identification de certificat X509. En plus d’être moins sécurisé, le mécanisme d’authentification nécessite généralement l’incorporation de la clé secrète dans le code source de l’application cliente. Par conséquent, pour des applications de production, il est vivement recommandé d’utiliser des informations d’identification de certificat.

À présent, vous créez une autre inscription d’application, mais spécifiez des informations d’identification de clé secrète client. Contrairement aux informations d’identification de certificat, l’annuaire est capable de générer des informations d’identification de clé secrète client. Au lieu de spécifier la clé secrète client, vous utilisez le commutateur `-GenerateClientSecret` pour demander qu’elle soit générée. Substituez vos propres valeurs aux espaces réservés suivants :

| Espace réservé | Description | Exemple |
| ----------- | ----------- | ------- |
| \<PepVM\> | Nom de la machine virtuelle de point de terminaison privilégié sur votre instance d’Azure Stack Hub. | « AzS-ERCS01 » |
| \<YourAppName\> | Nom descriptif pour la nouvelle inscription d’application. | « Mon outil de gestion » |

1. Ouvrez une session Windows PowerShell avec élévation de privilèges et exécutez les applets de commande suivantes :

     ```powershell  
     # Sign in to PowerShell interactively, using credentials that have access to the VM running the Privileged Endpoint (typically <domain>\cloudadmin)
     $Creds = Get-Credential

     # Create a PSSession to the Privileged Endpoint VM
     $Session = New-PSSession -ComputerName "<PepVM>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

     # Use the privileged endpoint to create the new app registration (and service principal object)
     $SpObject = Invoke-Command -Session $Session -ScriptBlock {New-GraphApplication -Name "<YourAppName>" -GenerateClientSecret}
     $AzureStackInfo = Invoke-Command -Session $Session -ScriptBlock {Get-AzureStackStampInformation}
     $Session | Remove-PSSession

     # Using the stamp info for your Azure Stack Hub instance, populate the following variables:
     # - AzureRM endpoint used for Azure Resource Manager operations 
     # - Audience for acquiring an OAuth token used to access Graph API 
     # - GUID of the directory tenant
     $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager
     $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"
     $TenantID = $AzureStackInfo.AADTenantID

     # Register and set an AzureRM environment that targets your Azure Stack Hub instance
     Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint $ArmEndpoint

     # Sign in using the new service principal identity
     $securePassword = $SpObject.ClientSecret | ConvertTo-SecureString -AsPlainText -Force
     $credential = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $SpObject.ClientId, $securePassword
     $SpSignin = Connect-AzureRmAccount -Environment "AzureStackUser" -ServicePrincipal -Credential $credential -TenantId $TenantID

     # Output the service principal details
     $SpObject
     ```

2. Une fois l’exécution du script terminée, les informations sur l’inscription de l’application s’affichent, incluant les informations d’identification du principal de service. Comme indiqué, la propriété `ClientID` et la propriété `ClientSecret` générée sont utilisées pour se connecter sous l’identité du principal de service. Une fois la connexion établie, l’identité du principal de service est utilisée pour l’autorisation subséquente et l’accès d’Azure Resource Manager aux ressources managées.

     ```shell  
     ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2623
     ClientId              : 8e0ffd12-26c8-4178-a74b-f26bd28db601
     Thumbprint            : 
     ApplicationName       : Azurestack-YourApp-6967581b-497e-4f5a-87b5-0c8d01a9f146
     ClientSecret          : 6RUWLRoBw3EebBLgaWGiowCkoko5_j_ujIPjA8dS
     PSComputerName        : azs-ercs01
     RunspaceId            : 286daaa1-c9a6-4176-a1a8-03f543f90998
     ```

Gardez votre session de console PowerShell ouverte, car vous aller l’utiliser avec la valeur `ApplicationIdentifier` dans la section suivante.

### <a name="update-a-service-principals-client-secret"></a>Mettre à jour une clé secrète client de principal de service

Mettez à jour les informations d’identification de la clé secrète client dans PowerShell à l’aide du paramètre **ResetClientSecret**, ce qui a pour effet de modifier immédiatement la clé secrète client. Substituez vos propres valeurs aux espaces réservés suivants :

| Espace réservé | Description | Exemple |
| ----------- | ----------- | ------- |
| \<PepVM\> | Nom de la machine virtuelle de point de terminaison privilégié sur votre instance d’Azure Stack Hub. | « AzS-ERCS01 » |
| \<AppIdentifier\> | Identificateur affecté à l’inscription de l’application. | « S-1-5-21-1634563105-1224503876-2692824315-2623 » |

1. Ouvrez une session Windows PowerShell avec élévation de privilèges, puis exécutez les cmdlets suivantes :

     ```powershell
     # Create a PSSession to the PrivilegedEndpoint VM
     $Session = New-PSSession -ComputerName "<PepVM>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

     # Use the privileged endpoint to update the client secret, used by the service principal associated with <AppIdentifier>
     $SpObject = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier "<AppIdentifier>" -ResetClientSecret}
     $Session | Remove-PSSession

     # Output the updated service principal details
     $SpObject
     ```

2. Une fois l’exécution du script terminée, les informations mises à jour sur l’inscription de l’application s’affichent, incluant la clé secrète client nouvellement générée.

     ```shell  
     ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2623
     ClientId              : 8e0ffd12-26c8-4178-a74b-f26bd28db601
     Thumbprint            : 
     ApplicationName       : Azurestack-YourApp-6967581b-497e-4f5a-87b5-0c8d01a9f146
     ClientSecret          : MKUNzeL6PwmlhWdHB59c25WDDZlJ1A6IWzwgv_Kn
     PSComputerName        : azs-ercs01
     RunspaceId            : 6ed9f903-f1be-44e3-9fef-e7e0e3f48564
     ```

### <a name="remove-a-service-principal"></a>Supprimer un principal de service

Nous allons maintenant voir comment retirer/supprimer une inscription d’application de votre annuaire et de son objet principal de service associé, en utilisant PowerShell. 

Substituez vos propres valeurs aux espaces réservés suivants :

| Espace réservé | Description | Exemple |
| ----------- | ----------- | ------- |
| \<PepVM\> | Nom de la machine virtuelle de point de terminaison privilégié sur votre instance d’Azure Stack Hub. | « AzS-ERCS01 » |
| \<AppIdentifier\> | Identificateur affecté à l’inscription de l’application. | « S-1-5-21-1634563105-1224503876-2692824315-2623 » |

```powershell  
# Sign in to PowerShell interactively, using credentials that have access to the VM running the Privileged Endpoint (typically <domain>\cloudadmin)
$Creds = Get-Credential

# Create a PSSession to the PrivilegedEndpoint VM
$Session = New-PSSession -ComputerName "<PepVM>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

# OPTIONAL: Use the privileged endpoint to get a list of applications registered in AD FS
$AppList = Invoke-Command -Session $Session -ScriptBlock {Get-GraphApplication}

# Use the privileged endpoint to remove the application and associated service principal object for <AppIdentifier>
Invoke-Command -Session $Session -ScriptBlock {Remove-GraphApplication -ApplicationIdentifier "<AppIdentifier>"}
```

L’appel de la cmdlet Remove-GraphApplication sur le point de terminaison privilégié ne retourne pas de sortie, mais une sortie textuelle de confirmation apparaît sur la console pendant l’exécution de la cmdlet :

```shell
VERBOSE: Deleting graph application with identifier S-1-5-21-1634563105-1224503876-2692824315-2623.
VERBOSE: Remove-GraphApplication : BEGIN on AZS-ADFS01 on ADFSGraphEndpoint
VERBOSE: Application with identifier S-1-5-21-1634563105-1224503876-2692824315-2623 was deleted.
VERBOSE: Remove-GraphApplication : END on AZS-ADFS01 under ADFSGraphEndpoint configuration
```

## <a name="assign-a-role"></a>Attribuer un rôle

Les utilisateurs et applications sont autorisés à accéder aux ressources Azure au travers d’un contrôle d’accès en fonction du rôle (RBAC). Pour autoriser une application à accéder aux ressources de votre abonnement en utilisant son principal du service, vous devez *attribuer* à ce dernier un *rôle* pour une *ressource* spécifique. Commencez par décider du rôle représentant les *autorisations* appropriées pour l’application. Pour en savoir plus sur les rôles disponibles, voir [Rôles intégrés pour les ressources Azure](/azure/role-based-access-control/built-in-roles).

Le type de ressource que vous choisissez établit également l’*étendue d’accès* pour le principal de service. Vous pouvez définir l’étendue d’accès au niveau de l’abonnement, du groupe de ressources ou de la ressource. Les autorisations sont héritées des niveaux inférieurs de l’étendue (par exemple, l’ajout d’une application au rôle « Lecteur » pour un groupe de ressources signifie qu’elle peut lire le groupe de ressources et toutes les ressources qu’il contient).

1. Connectez-vous au portail approprié, en fonction de l’annuaire que vous avez spécifié durant l’installation d’Azure Stack Hub (par exemple le portail Azure pour Azure AD ou le portail utilisateur Azure Stack Hub pour AD FS). Dans cet exemple, nous montrons un utilisateur connecté au portail utilisateur Azure Stack Hub.

   > [!NOTE]
   > Pour pouvoir ajouter des attributions de rôle pour une ressource donnée, votre compte d’utilisateur doit appartenir à un rôle qui déclare l’autorisation `Microsoft.Authorization/roleAssignments/write`. Par exemple, l’un des rôles intégrés [Propriétaire](/azure/role-based-access-control/built-in-roles#owner) ou [Administrateur de l’accès utilisateur](/azure/role-based-access-control/built-in-roles#user-access-administrator).  
2. Accédez à la ressource à laquelle vous voulez que le principal de service soit autorisé à accéder. Dans cet exemple, affectez le principal de service à un rôle dans l’étendue de l’abonnement en sélectionnant **Abonnements**, puis un abonnement spécifique. Vous pouvez également sélectionner un groupe de ressources ou une ressource spécifique telle qu’une machine virtuelle.

     ![Sélectionner l’abonnement pour l’assignation](./media/azure-stack-create-service-principal/select-subscription.png)

3. Sélectionnez la page **Contrôle d’accès (IAM)** qui est universelle pour toutes les ressources qui prennent en charge le contrôle d’accès en fonction du rôle.
4. Sélectionnez **+ Ajouter**
5. Sous **Rôle**, sélectionnez le rôle que vous souhaitez attribuer à l’application.
6. Sous **Sélectionner**, recherchez votre application en utilisant un nom d’application partiel ou complet. Pendant l’inscription, le nom de l’application est généré en tant que *Azurestack-\<YourAppName\>-\<ClientId\>* . Par exemple, si vous avez utilisé le nom d’application *App2* et que le ClientId *2bbe67d8-3fdb-4b62-87cf-cc41dd4344ff* a été attribué lors de la création, le nom complet est  *Azurestack-App2-2bbe67d8-3fdb-4b62-87cf-cc41dd4344ff*. Vous pouvez rechercher la chaîne exacte ou une portion, par exemple *Azurestack* ou *Azurestack-App2*.
7. Après avoir trouvé l’application, sélectionnez-la pour la faire apparaître sous **Membres sélectionnés**.
8. Sélectionnez **Enregistrer** pour finaliser l’attribution du rôle.

     [![Attribuer un rôle](media/azure-stack-create-service-principal/assign-role.png)](media/azure-stack-create-service-principal/assign-role.png#lightbox)

9. Lorsque vous avez terminé, l’application apparaît dans la liste des principaux affectés pour l’étendue actuelle pour le rôle donné.

     [![Rôle attribué](media/azure-stack-create-service-principal/assigned-role.png)](media/azure-stack-create-service-principal/assigned-role.png#lightbox)

Une fois que vous avez créé un principal de service et que vous lui avez attribué un rôle, vous pouvez commencer à l’utiliser dans votre application pour accéder aux ressources Azure Stack Hub.  

## <a name="next-steps"></a>Étapes suivantes

[Ajouter des utilisateurs pour AD FS](azure-stack-add-users-adfs.md)  
[Gérer les autorisations utilisateur](azure-stack-manage-permissions.md)  
[Documentation Azure Active Directory](https://docs.microsoft.com/azure/active-directory)  
[Active Directory Federation Services](https://docs.microsoft.com/windows-server/identity/active-directory-federation-services)
