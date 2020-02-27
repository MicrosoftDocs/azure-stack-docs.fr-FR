---
title: Intégrer l’identité AD FS avec votre centre de données Azure Stack Hub
description: Découvrez comment intégrer le fournisseur d’identité AD FS d’Azure Stack Hub avec votre centre de données AD FS.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 05/10/2019
ms.author: inhenkel
ms.reviewer: thoroet
ms.lastreviewed: 05/10/2019
ms.openlocfilehash: b4f48c8fe4138b74b735615777b16630c0fe7060
ms.sourcegitcommit: 97806b43314d306e0ddb15847c86be2c92ae001e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77509838"
---
# <a name="integrate-ad-fs-identity-with-your-azure-stack-hub-datacenter"></a>Intégrer l’identité AD FS avec votre centre de données Azure Stack Hub

Vous pouvez déployer Azure Stack Hub en utilisant Azure Active Directory (Azure AD) ou Active Directory Federation Services (AD FS) en tant que fournisseur d’identité. Vous devez faire le choix avant de déployer Azure Stack Hub. Dans un scénario connecté, vous pouvez choisir Azure AD ou AD FS. Pour un scénario déconnecté, seul AD FS est pris en charge. Cet article montre comment intégrer les services de fédération Active Directory (AD FS) d’Azure Stack Hub AD FS avec ceux de votre centre de données.

> [!IMPORTANT]
> Vous ne pouvez pas changer de fournisseur d’identité sans redéployer la solution Azure Stack Hub complète.

## <a name="active-directory-federation-services-and-graph"></a>Active Directory Federation Services et Graph

Un déploiement avec les services de fédération Active Directory (AD FS) permet aux identités dans une forêt Active Directory existante de s’authentifier auprès des ressources dans Azure Stack Hub. Cette forêt Active Directory existante nécessite un déploiement d’AD FS pour permettre la création d’une fédération AD FS de confiance.

L’authentification est un composant d’identité. Pour pouvoir gérer le contrôle d’accès en fonction du rôle (RBAC) dans Azure Stack Hub, le composant Graph doit être configuré. Lorsque l’accès à une ressource est délégué, le composant Graph recherche le compte d’utilisateur dans la forêt Active Directory existante à l’aide du protocole LDAP.

![Architecture des services de fédération Active Directory (AD FS) d’Azure Stack Hub](media/azure-stack-integrate-identity/Azure-Stack-ADFS-architecture.png)

Les services de fédération Active Directory (AD FS) existants sont le service d’émission de jeton de sécurité du compte qui envoie des revendications aux services de fédération Active Directory (AD FS) d’Azure Stack Hub (STS ressource). Dans Azure Stack Hub, une automation crée l’approbation de fournisseur de revendications avec le point de terminaison de métadonnées pour les services de fédération Active Directory (AD FS) existants.

Sans les services AD FS existants, une partie de confiance doit être configurée. Cette étape n’est pas effectuée par l’automatisation et elle doit être configurée par l’opérateur. Il est possible de créer le point de terminaison de l’adresse IP virtuelle Azure Stack Hub pour AD FS à l’aide du modèle `https://adfs.<Region>.<ExternalFQDN>/`.

La configuration de la partie de confiance nécessite également la configuration des règles de transformation de revendication fournies par Microsoft.

Pour la configuration de Graph, un compte de service doit être fourni, avec un accès en lecture à l’annuaire Active Directory existant. Ce compte est requis en tant qu’entrée pour permettre à l’automation de gérer les scénarios RBAC.

Pour la dernière étape, un nouveau propriétaire est configuré pour l’abonnement du fournisseur par défaut. Ce compte dispose d’un accès complet à toutes les ressources quand il est connecté au portail administrateur Azure Stack Hub.

Conditions requises :

|Composant|Condition requise|
|---------|---------|
|Graph|Microsoft Active Directory 2012/2012 R2/2016|
|AD FS|Windows Server 2012/2012 R2/2016|

## <a name="setting-up-graph-integration"></a>Configuration de l’intégration de Graph

Graph prend uniquement en charge l’intégration avec une seule forêt Active Directory. En présence de plusieurs forêts, seule la forêt spécifiée dans la configuration servira à extraire les utilisateurs et les groupes.

Les informations suivantes sont requises en tant qu’entrées pour les paramètres d’automation :

|Paramètre|Paramètre Feuille de calcul de déploiement|Description|Exemple|
|---------|---------|---------|---------|
|`CustomADGlobalCatalog`|Nom de domaine complet de la forêt AD FS|Nom de domaine complet de la forêt Active Directory à laquelle s’intégrer|Contoso.com|
|`CustomADAdminCredentials`| |Un utilisateur avec autorisation de lecture LDAP|YOURDOMAIN\graphservice|

### <a name="configure-active-directory-sites"></a>Configurer les sites Active Directory

Pour des déploiements d’Active Directory sur plusieurs sites, configurez le site Active Directory le plus proche de votre déploiement Azure Stack Hub. La configuration évite que le service Azure Stack Hub Graph résolve les requêtes en utilisant un serveur de catalogue global d’un site distant.

Ajoutez le sous-réseau [Réseau d’adresses IP virtuelles publiques](azure-stack-network.md#public-vip-network) d’Azure Stack Hub au site Active Directory le plus proche d’Azure Stack Hub. Supposons par exemple que votre annuaire Active Directory ait deux sites : Seattle et Redmond. Si Azure Stack Hub est déployé sur le site de Seattle, vous devez ajouter le sous-réseau Réseau d’adresses IP virtuelles publiques d’Azure Stack Hub au site Active Directory de Seattle.

Pour plus d’informations sur les sites Active Directory, consultez [Conception de la topologie du site](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology).

> [!Note]  
> Si vous n’avez qu’un seul site Active Directory, vous pouvez ignorer cette étape. Si vous avez configuré un sous-réseau fourre-tout, assurez-vous que le sous-réseau Réseau d’adresses IP virtuelles publiques d’Azure Stack Hub n’en fait pas partie.

### <a name="create-user-account-in-the-existing-active-directory-optional"></a>Créer un compte d’utilisateur dans l’Active Directory existant (facultatif)

Vous pouvez également créer un compte pour le service Graph dans l’Active Directory existant. Effectuez cette étape si vous n’avez pas déjà un compte que vous voulez utiliser.

1. Dans l’Active Directory existant, créez le compte d’utilisateur suivant (recommandé) :
   - **Nom d’utilisateur** : graphservice
   - **Mot de passe** : Utilisez un mot de passe fort et configurez le mot de passe pour qu’il n’expire jamais.

   Aucune autorisation ou appartenance spéciale n’est requise.

#### <a name="trigger-automation-to-configure-graph"></a>Déclencher l’automatisation pour configurer Graph

Pour cette procédure, utilisez un ordinateur de votre réseau de centre de données qui peut communiquer avec le point de terminaison privilégié dans Azure Stack Hub.

1. Ouvrez une session Windows PowerShell avec élévation de privilèges (exécuter en tant qu’administrateur) et connectez-vous à l’adresse IP du point de terminaison privilégié. Utilisez les informations d’identification de **CloudAdmin** à authentifier.

   ```powershell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Maintenant que vous êtes connecté au point de terminaison privilégié, exécutez la commande suivante : 

   ```powershell  
   Register-DirectoryService -CustomADGlobalCatalog contoso.com
   ```

   Lorsque vous y êtes invité, spécifiez les informations d’identification du compte d’utilisateur que vous souhaitez utiliser pour le service Graph (par exemple, graphservice). L’entrée de l’applet de commande Register-DirectoryService doit correspondre au nom de la forêt/racine du domaine dans la forêt plutôt que n’importe quel autre domaine dans la forêt.

   > [!IMPORTANT]
   > Attendez que les informations d’identification apparaissent (Get-Credential n’est pas pris en charge dans le point de terminaison privilégié) et entrez les informations d’identification du compte du service Graph.

3. L’applet de commande **Register-DirectoryService** a des paramètres facultatifs que vous pouvez utiliser dans certains scénarios où la validation de l’instance Active Directory existante échoue. Quand cette applet de commande est exécutée, elle valide le fait que le domaine fourni est le domaine racine, qu’un serveur de catalogue global peut être atteint et que le compte fourni a reçu un droit d’accès en lecture.

   |Paramètre|Description|
   |---------|---------|
   |`-SkipRootDomainValidation`|Spécifie qu’un domaine enfant doit être utilisé à la place du domaine racine recommandé.|
   |`-Force`|Ignore tous les contrôles de validation.|

#### <a name="graph-protocols-and-ports"></a>Ports et protocoles Graph

Le service Graph d’Azure Stack Hub utilise les protocoles et ports suivants pour communiquer avec un serveur de catalogue global (GC) accessible en écriture et un centre de distribution de clés (KDC) pouvant traiter les demandes de connexion dans la forêt Active Directory cible.

Le service Graph d’Azure Stack Hub utilise les protocoles et ports suivants pour communiquer avec l’Active Directory cible :

|Type|Port|Protocol|
|---------|---------|---------|
|LDAP|389|TCP et UDP|
|LDAP SSL|636|TCP|
|LDAP GC|3268|TCP|
|LDAP GC SSL|3269|TCP|

## <a name="setting-up-ad-fs-integration-by-downloading-federation-metadata"></a>Configuration de l’intégration AD FS en téléchargeant des métadonnées de fédération

Les informations suivantes sont nécessaires en entrée pour les paramètres Automation :

|Paramètre|Paramètre Feuille de calcul de déploiement|Description|Exemple|
|---------|---------|---------|---------|
|CustomAdfsName|Nom du fournisseur AD FS|Nom du fournisseur de revendications.<br>Il apparaît ainsi dans la page d’accueil AD FS.|Contoso|
|CustomAD<br>FSFederationMetadataEndpointUri|URI de métadonnées AD FS|Lien des métadonnées de fédération.| https:\//ad01.contoso.com/federationmetadata/2007-06/federationmetadata.xml |
|SigningCertificateRevocationCheck|N/D|Paramètre facultatif pour ignorer la vérification CRL.|None|


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack-hub"></a>Déclencher l’automation pour configurer une approbation de fournisseur de revendications dans Azure Stack Hub

Pour cette procédure, utilisez un ordinateur capable de communiquer avec le point de terminaison privilégié dans Azure Stack Hub. Le certificat utilisé par le compte **STS AD FS** doit être approuvé par Azure Stack Hub.

1. Ouvrez une session Windows PowerShell avec élévation de privilèges et connectez-vous au point de terminaison privilégié.

   ```powershell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Maintenant que vous êtes connecté au point de terminaison privilégié, exécutez la commande suivante en utilisant les paramètres correspondant à votre environnement :

   ```powershell  
   Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataEndpointUri https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml
   ```

3. Exécutez la commande suivante pour mettre à jour le propriétaire de l’abonnement du fournisseur par défaut, en utilisant les paramètres correspondant à votre environnement :

   ```powershell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

## <a name="setting-up-ad-fs-integration-by-providing-federation-metadata-file"></a>Configuration de l’intégration AD FS en fournissant un fichier de métadonnées de fédération

À compter de la version 1807, utilisez cette méthode si l’une des conditions suivantes est remplie :

- La chaîne de certificats est différente pour AD FS par rapport à tous les autres points de terminaison dans Azure Stack Hub.
- Il n’y a aucune connectivité réseau vers le serveur AD FS existant à partir l’instance AD FS d’Azure Stack Hub.

Les informations suivantes sont nécessaires en entrée pour les paramètres Automation :


|Paramètre|Description|Exemple|
|---------|---------|---------|
|CustomAdfsName|Nom du fournisseur de revendications. Il apparaît ainsi dans la page d’accueil AD FS.|Contoso|
|CustomADFSFederationMetadataFileContent|Contenu des métadonnées.|$using:federationMetadataFileContent|

### <a name="create-federation-metadata-file"></a>Créer un fichier de métadonnées de fédération

Pour la procédure suivante, vous devez utiliser un ordinateur qui dispose d’une connectivité réseau vers le déploiement d’AD FS existant, qui devient le compte STS. Les certificats nécessaires doivent aussi être installés.

1. Ouvrez une session Windows PowerShell avec élévation de privilèges, puis exécutez la commande suivante en utilisant les paramètres correspondant à votre environnement :

   ```powershell  
    $url = "https://win-SQOOJN70SGL.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml"
    $webclient = New-Object System.Net.WebClient
    $webclient.Encoding = [System.Text.Encoding]::UTF8
    $metadataAsString = $webclient.DownloadString($url)
    Set-Content -Path c:\metadata.xml -Encoding UTF8 -Value $metadataAsString
   ```

2. Copiez le fichier de métadonnées sur un ordinateur qui peut communiquer avec le point de terminaison privilégié.

### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack-hub"></a>Déclencher l’automation pour configurer une approbation de fournisseur de revendications dans Azure Stack Hub

Pour cette procédure, utilisez un ordinateur capable de communiquer avec le point de terminaison privilégié dans Azure Stack Hub et ayant accès au fichier de métadonnées que vous avez créé à l’étape précédente.

1. Ouvrez une session Windows PowerShell avec élévation de privilèges et connectez-vous au point de terminaison privilégié.

   ```powershell  
   $federationMetadataFileContent = get-content c:\metadata.xml
   $creds=Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Maintenant que vous êtes connecté au point de terminaison privilégié, exécutez la commande suivante en utilisant les paramètres correspondant à votre environnement :

    ```powershell
    Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataFileContent $using:federationMetadataFileContent
    ```

3. Exécutez la commande suivante pour mettre à jour le propriétaire de l’abonnement du fournisseur par défaut. Utilisez les paramètres appropriés pour votre environnement.

   ```powershell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

   > [!Note]  
   > Quand vous effectuez la rotation du certificat sur les services AD FS existants (compte STS), vous devez configurer à nouveau l’intégration AD FS. Vous devez configurer l’intégration même si le point de terminaison des métadonnées est accessible ou a été configuré à l’aide du fichier de métadonnées.

## <a name="configure-relying-party-on-existing-ad-fs-deployment-account-sts"></a>Configurer la partie de confiance sur le déploiement AD FS existant (compte STS)

Microsoft fournit un script qui configure la partie de confiance, y compris les règles de transformation de revendication. L’utilisation du script est facultative car vous pouvez exécuter les commandes manuellement.

Vous pouvez télécharger le script d’assistance à partir des [outils Azure Stack Hub](https://github.com/Azure/AzureStack-Tools/tree/vnext/DatacenterIntegration/Identity) sur GitHub.

Si vous décidez d’exécuter manuellement les commandes, procédez comme suit :

1. Copiez le contenu suivant dans un fichier .txt (par exemple, enregistré en tant que c:\ClaimRules.txt) sur l’instance AD FS de votre centre de données ou membre de la batterie de serveurs :

   ```text
   @RuleTemplate = "LdapClaims"
   @RuleName = "Name claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "UPN claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "ObjectID claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid"]
   => issue(Type = "http://schemas.microsoft.com/identity/claims/objectidentifier", Issuer = c.Issuer, OriginalIssuer = c.OriginalIssuer, Value = c.Value, ValueType = c.ValueType);

   @RuleName = "Family Name and Given claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname", "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname"), query = ";sn,givenName;{0}", param = c.Value);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all Group SID claims"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
   => issue(claim = c);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all windows account name claims"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
   => issue(claim = c);
   ```

2. Vérifiez que cette authentification basée sur Windows Forms pour extranet et intranet est activée. Vous pouvez vérifier si elle est déjà activée en exécutant l’applet de commande suivante :

   ```powershell  
   Get-AdfsAuthenticationProvider | where-object { $_.name -eq "FormsAuthentication" } | select Name, AllowedForPrimaryExtranet, AllowedForPrimaryIntranet
   ```

    > [!Note]  
    > Les chaînes d’agent utilisateur prises en charge par l’authentification Windows intégrée peuvent être obsolètes pour votre déploiement AD FS et nécessiter une mise à jour pour prendre en charge les clients les plus récents. Pour plus d’informations sur les chaînes d’agent utilisateur prises en charge par l’authentification Windows intégrée, consultez [Configuration de l’authentification basée sur des formulaires intranet pour les appareils qui ne prennent pas en charge l’authentification Windows intégrée](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-intranet-forms-based-authentication-for-devices-that-do-not-support-wia).<br><br>Pour les étapes d’activation de la stratégie d’authentification par formulaire, consultez [Configurer des stratégies d’authentification](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-authentication-policies).

3. Pour ajouter la partie de confiance, exécutez la commande Windows PowerShell suivante sur votre instance AD FS ou membre de la batterie de serveurs. Veillez à mettre à jour le point de terminaison AD FS et pointez vers le fichier créé à l’étape 1.

   **Pour AD FS 2016**

   ```powershell  
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -AccessControlPolicyName "Permit everyone" -TokenLifeTime 1440
   ```

   **Pour AD FS 2012/2012 R2**

   ```powershell  
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -TokenLifeTime 1440
   ```

   > [!IMPORTANT]  
   > Vous devez utiliser le composant logiciel enfichable MMC AD FS pour configurer les règles d’autorisation d’émission si vous utilisez AD FS sous Windows Server 2012 ou 2012 R2.

4. Si vous utilisez Internet Explorer ou Microsoft Edge pour accéder à Azure Stack Hub, vous devez ignorer les liaisons de jeton. Sinon, les tentatives de connexion échouent. Sur votre instance AD FS ou membre de la batterie de serveurs, exécutez la commande suivante :

   > [!note]  
   > Cette étape n’est pas applicable si vous exécutez Windows Server 2012 ou 2012 R2 AD FS. Dans ce cas, vous pouvez ignorer cette commande et poursuivre l’intégration.

   ```powershell  
   Set-AdfsProperties -IgnoreTokenBinding $true
   ```

## <a name="spn-creation"></a>Création du nom principal de service

Il existe plusieurs scénarios qui requièrent l’utilisation d’un nom principal de service (SPN) pour l’authentification. Voici quelques exemples :

- Utilisation de CLI avec un déploiement AD FS d’Azure Stack Hub.
- Pack d’administration System Center pour Azure Stack Hub lors d’un déploiement avec AD FS.
- Fournisseurs de ressources dans Azure Stack Hub lors d’un déploiement avec AD FS.
- Différentes applications.
- Vous exigez une connexion non interactive.

> [!Important]  
> AD FS prend en charge seulement les sessions de connexion interactives. Si vous avez besoin d’une connexion non interactive pour un scénario automatisé, vous devez utiliser un nom de principal du service (SPN).

Pour plus d’informations sur la création d’un SPN, consultez [Créer un principal de service pour AD FS](azure-stack-create-service-principals.md).


## <a name="troubleshooting"></a>Dépannage

### <a name="configuration-rollback"></a>Restauration de la configuration

Si une erreur se produit et laisse l’environnement dans un état vous empêchant de vous authentifier, une option de restauration est disponible.

1. Ouvrez une session Windows PowerShell avec élévation de privilèges et exécutez les commandes suivantes :

   ```powershell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Puis exécutez l’applet de commande suivante :

   ```powershell  
   Reset-DatacenterIntegrationConfiguration
   ```

   Après avoir exécuté l’action de restauration, toutes les modifications de configuration sont restaurées. Seule l’authentification avec l’utilisateur **CloudAdmin** intégré est possible.

   > [!IMPORTANT]
   > Vous devez configurer le propriétaire d’origine de l’abonnement du fournisseur par défaut.

   ```powershell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "azurestackadmin@[Internal Domain]"
   ```

### <a name="collecting-additional-logs"></a>Collecte de journaux d’activité supplémentaires

Si une des applets de commande échoue, vous pouvez collecter des journaux d’activité supplémentaires à l’aide de l’applet de commande `Get-Azurestacklogs`.

1. Ouvrez une session Windows PowerShell avec élévation de privilèges et exécutez les commandes suivantes :

   ```powershell  
   $creds = Get-Credential
   Enter-pssession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Puis exécutez l’applet de commande suivante :

   ```powershell  
   Get-AzureStackLog -OutputPath \\myworkstation\AzureStackLogs -FilterByRole ECE
   ```

## <a name="next-steps"></a>Étapes suivantes

[Intégrer des solutions de surveillance externes](azure-stack-integrate-monitor.md)
