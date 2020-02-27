---
title: Faire pivoter les clés secrètes
titleSuffix: Azure Stack Hub
description: Apprenez à effectuer la rotation de vos secrets dans Azure Stack Hub.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 12/13/2019
ms.reviewer: ppacent
ms.author: inhenkel
ms.lastreviewed: 12/13/2019
monikerRange: '>=azs-1802'
ms.openlocfilehash: 22be9075f6c1d8b25c6ce241ad24ed8e10630261
ms.sourcegitcommit: 97806b43314d306e0ddb15847c86be2c92ae001e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77509566"
---
# <a name="rotate-secrets-in-azure-stack-hub"></a>Effectuer la rotation des secrets dans Azure Stack Hub

*Ces instructions s’appliquent uniquement à des systèmes intégrés Azure Stack Hub versions 1803 et ultérieures. N’essayez pas d’effectuer la rotation des secrets sur des versions d’Azure Stack Hub antérieures à la version 1802*

Les secrets vous aident à maintenir une communication sécurisée entre les ressources et les services de l’infrastructure Azure Stack Hub.

## <a name="rotate-secrets-overview"></a>Présentation de la rotation des secrets

1. Préparez les certificats qui seront utilisés pour la rotation des secrets.
2. Passez en revue les [exigences de certificat d’infrastructure de clés publiques](https://docs.microsoft.com/azure-stack/operator/azure-stack-pki-certs) d’Azure Stack Hub.
3. [Utilisez le point de terminaison privilégié](azure-stack-privileged-endpoint.md) et exécutez **Test-azurestack** pour vérifier que tout fonctionne bien.  
4. Passez en revue les [étapes préliminaires à la rotation des secrets](#pre-steps-for-secret-rotation).
5. [Validez les certificats d’infrastructure de clés publiques Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-validate-pki-certs). Vérifiez que le mot de passe ne contient pas de caractères spéciaux, tels que `*` ou `)`.
6. Vérifiez que le chiffrement PFX est **TripleDES-SHA1**. Si vous rencontrez un problème, voir [Corriger les problèmes courants liés aux certificats d’infrastructure de clés publiques Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-remediate-certs#pfx-encryption).
7. Préparez la structure de dossiers.  Vous trouverez un exemple dans la section [Rotation des secrets externes](https://docs.microsoft.com/azure-stack/operator/azure-stack-rotate-secrets#rotating-external-secrets).
8. [Démarrez la rotation des secrets](#use-powershell-to-rotate-secrets).

## <a name="rotate-secrets"></a>Faire pivoter les clés secrètes

Azure Stack Hub utilise différents secrets pour assurer une communication sécurisée entre les ressources et services d’infrastructure Azure Stack Hub.

- **Secrets internes**

    Tous les certificats, mots de passe, chaînes sécurisées et clés utilisés par l’infrastructure Azure Stack Hub sans intervention de l’Opérateur Azure Stack Hub.

- **Secrets externes**

    Certificats de service d’infrastructure pour les services accessibles de l’extérieur fournis par l’opérateur Azure Stack Hub. Les secrets externes inclut les certificats des services suivants :

    - Portail administrateur
    - Portail public
    - Administrateur Azure Resource Manager
    - Global Azure Resource Manager
    - Administrateur Key Vault
    - Key Vault
    - Hôte d’extension d’administration
    - ACS (y compris stockage objet blob, table et file d’attente)
    - ADFS*
    - Graph*
    
    \* Applicable uniquement si le fournisseur d’identité de l’environnement est AD FS (services de fédération Active Directory).

> [!Note]
> Toutes les autres clés et chaînes sécurisées, y compris les mots de passe BMC et switch ainsi que les mots de passe des comptes utilisateur et administrateur, sont toujours mises à jour manuellement par l’administrateur.

> [!Important]
> Depuis la version 1811 d’Azure Stack Hub, la rotation des secrets est séparée pour les certificats internes et externes.

Pour maintenir l’intégrité de l’infrastructure Azure Stack Hub, les opérateurs doivent pouvoir effectuer régulièrement la rotation des secrets de leur infrastructure, à une fréquence conforme aux exigences de sécurité de leur organisation.

### <a name="rotating-secrets-with-external-certificates-from-a-new-certificate-authority"></a>Rotation des secrets avec des certificats externes obtenus auprès d’une nouvelle autorité de certification

Azure Stack Hub prend en charge la rotation des secrets avec des certificats externes obtenus auprès d’une nouvelle autorité de certification dans les contextes suivants :

|Autorité de certification de certificats installée|Autorité de certification vers laquelle effectuer la rotation|Prise en charge|Versions d’Azure Stack Hub prises en charge|
|-----|-----|-----|-----|
|De : Autorité de certification de certificats autosignés|Vers : Autorité de certification d’entreprise|Prise en charge|1903 et version ultérieure|
|De : Autorité de certification de certificats autosignés|Vers : Autorité de certification de certificats autosignés|Non pris en charge||
|De : Autorité de certification de certificats autosignés|Vers : Autorité de certification publique<sup>*</sup>|Prise en charge|1803 et ultérieure|
|De : Autorité de certification d’entreprise|Vers : Autorité de certification d’entreprise|Pris en charge. Versions 1803 à 1903 : prise en charge à condition que les clients utilisent la MÊME autorité de certification d’entreprise que celle utilisée au moment du déploiement|1803 et ultérieure|
|De : Autorité de certification d’entreprise|Vers : Autorité de certification de certificats autosignés|Non pris en charge||
|De : Autorité de certification d’entreprise|Vers : Autorité de certification publique<sup>*</sup>|Prise en charge|1803 et ultérieure|
|De : Autorité de certification publique<sup>*</sup>|Vers : Autorité de certification d’entreprise|Prise en charge|1903 et version ultérieure|
|De : Autorité de certification publique<sup>*</sup>|Vers : Autorité de certification de certificats autosignés|Non pris en charge||
|De : Autorité de certification publique<sup>*</sup>|Vers : Autorité de certification publique<sup>*</sup>|Prise en charge|1803 et ultérieure|

<sup>*</sup>Indique que les autorités de certification publiques mentionnées ici sont celles qui font partie du programme de certification racine approuvé Windows. La liste complète est disponible dans l’article [Programme de certification racine approuvé Microsoft : Participants (à compter du 27 juin 2017)](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).

## <a name="fixing-alerts"></a>Correction des alertes

Lorsque les secrets arrivent à moins de 30 jours de leur expiration, les alertes suivantes sont générées dans le portail administrateur :

- Pending service account password expiration (Expiration imminente du mot de passe du compte de service)
- Pending internal certificate expiration (Expiration imminente du certificat interne)
- Pending external certificate expiration (Expiration imminente du certificat externe)

Pour corriger ces alertes, exécutez la rotation des secrets en suivant les instructions ci-dessous.

> [!Note]
> Des environnements Azure Stack Hub sur des versions antérieures à la version 1811 peuvent afficher des alertes relatives à des expirations imminentes de certificat ou de secret internes. Ces alertes sont incorrectes. Vous pouvez donc les ignorer sans exécuter la rotation des secrets internes. Les alertes d’expiration incorrectes des secrets internes sont un problème connu, résolu dans la version 1811. Les secrets internes n’expirent pas tant que l’environnement n’a pas été actif pendant deux ans.

## <a name="pre-steps-for-secret-rotation"></a>Étapes préliminaires à la rotation des secrets

   > [!IMPORTANT]
   > Si une rotation des secrets a déjà été effectuée sur votre environnement Azure Stack Hub, vous devez mettre à jour le système vers la version 1811 ou un version ultérieure avant de réexécuter une rotation des secrets. Une rotation des secrets doit être exécutée via le [point de terminaison privilégié](azure-stack-privileged-endpoint.md) et nécessite les informations d’identification de l’opérateur Azure Stack Hub. Si le ou les opérateurs Azure Stack Hub de votre environnement ne savent pas si une rotation des secrets a été exécutée sur votre environnement, effectuez une mise à jour vers la version 1811 avant de réexécuter une rotation des secrets.

1. Nous vous recommandons vivement de mettre à jour votre instance Azure Stack Hub vers la version 1811.

    > [!Note]
    > Pour les versions antérieures à 1811, vous n’avez pas besoin de procéder à la rotation des secrets pour ajouter des certificats d’hôte d’extension. Suivez les instructions de l’article [Préparer un hôte d’extension pour Azure Stack Hub](azure-stack-extension-host-prepare.md) pour ajouter des certificats d’hôte d’extension.

2. Les opérateurs remarqueront peut-être que des alertes s’ouvrent et se ferment automatiquement pendant la rotation des secrets Azure Stack Hub.  Ce comportement est normal et les alertes peuvent être ignorées.  Les opérateurs peuvent vérifier la validité de ces alertes en exécutant **Test-AzureStack**.  Pour les opérateurs utilisant System Center Operations Manager pour superviser les systèmes Azure Stack Hub, le placement d’un système en mode maintenance empêche ces alertes d’atteindre leurs systèmes ITSM, mais continue d’alerter si le système Azure Stack Hub devient inaccessible.

3. Notifiez vos utilisateurs de toute opération de maintenance. Planifiez les fenêtres de maintenance normale pendant les heures creuses, autant que possible. Les opérations de maintenance peuvent affecter les opérations du portail et les charges de travail des utilisateurs.

    > [!Note]
    > Les étapes suivantes s’appliquent uniquement lorsque vous effectuez la rotation de secrets externes à Azure Stack Hub.

4. Exécutez **[Test-AzureStack](azure-stack-diagnostic-test.md)** et vérifiez que toutes les sorties de test sont saines avant de procéder à la rotation des secrets.
5. Préparez un nouveau jeu de certificats externes de remplacement. Le nouveau jeu répond aux spécifications de certificat décrites dans la page [Exigences de certificat d’infrastructure de clés publiques Azure Stack Hub](azure-stack-pki-certs.md). Vous pouvez générer une demande de signature de certificat (CSR) pour l’achat ou la création de certificats en suivant la procédure décrite dans [Générer des certificats d’infrastructure de clés publiques](azure-stack-get-pki-certs.md), puis les préparer en vue d’une utilisation dans votre environnement Azure Stack Hub en suivant la procédure décrite dans [Préparer des certificats d’infrastructure de clés publiques Azure Stack Hub](azure-stack-prepare-pki-certs.md). Veillez à valider les certificats que vous préparez en suivant la procédure décrite dans [Valider des certificats PKI](azure-stack-validate-pki-certs.md).
6. Stockez une sauvegarde des certificats utilisés pour la rotation dans un emplacement de sauvegarde sécurisé. Si votre rotation s’exécute puis échoue, remplacez les certificats dans le partage de fichiers par les copies de sauvegarde avant d’exécuter à nouveau la rotation. Conservez des copies de sauvegarde dans l’emplacement de sauvegarde sécurisé.
7. Créez un partage de fichiers auquel vous pouvez accéder depuis les machines virtuelles ERCS. Le partage de fichiers doit être accessible en lecture et en écriture pour l’identité **CloudAdmin**.
8. Ouvrez une console PowerShell ISE à partir d’un ordinateur sur lequel vous avez accès au partage de fichiers. Accédez à votre partage de fichiers.
9. Exécutez **[CertDirectoryMaker.ps1](https://www.aka.ms/azssecretrotationhelper)** pour créer les répertoires requis pour vos certificats externes.

> [!IMPORTANT]
> Le script CertDirectoryMaker crée une structure de dossiers qui adhèrent à :
>
> **.\Certificates\AAD** ou ***.\Certificates\ADFS*** en fonction de votre fournisseur d’identité utilisé pour Azure Stack Hub.
>
> Il est d’une importance capitale que votre structure de dossiers se termine par les dossiers **AAD** ou **ADFS** et que tous les sous-répertoires soient au sein de cette structure. Autrement, **Start-SecretRotation** affichera :
>
> ```powershell
> Cannot bind argument to parameter 'Path' because it is null.
> + CategoryInfo          : InvalidData: (:) [Test-Certificate], ParameterBindingValidationException
> + FullyQualifiedErrorId : ParameterArgumentValidationErrorNullNotAllowed,Test-Certificate
> + PSComputerName        : xxx.xxx.xxx.xxx
> ```
>
> Le message d’erreur indique l’existence d’un problème d’accès à votre partage de fichiers, alors qu’il s’agit en réalité de la structure de dossiers appliquée ici. Plus d’informations sont disponibles dans Microsoft.AzureStack.ReadinessChecker – [module PublicCertHelper](https://www.powershellgallery.com/packages/Microsoft.AzureStack.ReadinessChecker/1.1811.1101.1/Content/CertificateValidation%5CPublicCertHelper.psm1).
>
> Il est également important que votre structure de dossiers de partage de fichiers commence par le dossier **Certificates**, sans quoi la validation échouerait également.
> Le montage du partage de fichiers doit se présenter sous la forme **\\\\\<IPAddress>\\\<ShareName>\\** et contenir le dossier  **Certificates\AAD** ou **Certificates\ADFS**.
>
> Par exemple :
> - Fileshare = **\\\\\<IPAddress>\\\<ShareName>\\**
> - CertFolder = **Certificates\AAD**
> - FullPath = **\\\\\<IPAddress>\\\<ShareName>\Certificates\AAD**

## <a name="rotating-external-secrets"></a>Rotation des secrets externes

Pour effectuer une rotation des secrets externes :

1. Dans le répertoire **\Certificates\\\<IdentityProvider>** créé lors des étapes préliminaires, placez le nouveau jeu de certificats externes de remplacement dans la structure de répertoires en respectant le format décrit dans la section **Certificats obligatoires** de la page [Exigences de certificat d’infrastructure de clés publiques Azure Stack Hub](azure-stack-pki-certs.md#mandatory-certificates).

    Exemple de structure de dossiers pour le fournisseur d’identité Azure AD :
    ```powershell
        <ShareName>
        │   │
        │   └───Certificates
        │         └───AAD
        │             ├───ACSBlob
        │             │       <CertName>.pfx
        │             │
        │             ├───ACSQueue
        │             │       <CertName>.pfx
        │             │
        │             ├───ACSTable
        │             │       <CertName>.pfx
        │             │
        │             ├───Admin Extension Host
        │             │       <CertName>.pfx
        │             │
        │             ├───Admin Portal
        │             │       <CertName>.pfx
        │             │
        │             ├───ARM Admin
        │             │       <CertName>.pfx
        │             │
        │             ├───ARM Public
        │             │       <CertName>.pfx
        │             │
        │             ├───KeyVault
        │             │       <CertName>.pfx
        │             │
        │             ├───KeyVaultInternal
        │             │       <CertName>.pfx
        │             │
        │             ├───Public Extension Host
        │             │       <CertName>.pfx
        │             │
        │             └───Public Portal
        │                     <CertName>.pfx

    ```

2. Créez une session PowerShell avec le [point de terminaison privilégié](azure-stack-privileged-endpoint.md) à l’aide du compte **CloudAdmin** et stockez les sessions en tant que variable. Vous allez utiliser cette variable comme paramètre dans l’étape suivante.

    > [!IMPORTANT]  
    > N’entrez pas dans la session. Stockez la session en tant que variable.

3. Exécutez **[invoke-command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/Invoke-Command?view=powershell-5.1)** . Passez la variable de session PowerShell de votre point de terminaison privilégié en tant que paramètre **Session**.

4. Exécutez **Start-SecretRotation** avec les paramètres suivants :
    - **PfxFilesPath**  
    Spécifiez le chemin d’accès réseau vers votre répertoire de certificats créé précédemment.  
    - **PathAccessCredential**  
    Un objet PSCredential pour les informations d’identification pour accéder au partage.
    - **CertificatePassword**  
    Une chaîne sécurisée du mot de passe utilisée pour tous les fichiers de certificat pfx créés.

5. Patientez pendant la rotation de vos secrets. La rotation des secrets externes prend environ une heure.

    Quand la rotation des secrets a réussi, la console affiche **Overall action status: Success (état global de l’action : réussite).**

    > [!Note]
    > En cas d’échec de la rotation des secrets, suivez les instructions figurant dans le message d’erreur, puis réexécutez **Start-SecretRotation** avec le paramètre **-ReRun**.

    ```powershell
    Start-SecretRotation -ReRun
    ```

    Contactez le support si vous rencontrez des échecs répétés de rotation des secrets.

6. Une fois la rotation des secrets réussie, supprimez vos certificats du partage créé lors de l’étape préliminaire et stockez-les dans leur emplacement de sauvegarde sécurisé.

## <a name="use-powershell-to-rotate-secrets"></a>Utiliser PowerShell pour effectuer la rotation des secrets

L’exemple PowerShell suivant montre les cmdlet et paramètres à exécuter afin d’effectuer la rotation des secrets.

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Run Secret Rotation
$CertPassword = ConvertTo-SecureString "<CertPasswordHere>" -AsPlainText -Force
$CertShareCreds = Get-Credential
$CertSharePath = "<NetworkPathOfCertShare>"
Invoke-Command -Session $PEPSession -ScriptBlock {
    Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCreds -CertificatePassword $using:CertPassword
}
Remove-PSSession -Session $PEPSession
```

## <a name="rotating-only-internal-secrets"></a>Rotation des secrets internes uniquement

> [!Note]
> La rotation des secrets internes doit uniquement être effectuée si vous suspectez qu’un secret interne a été compromis par une entité malveillante ou si vous avez reçu une alerte (à compter de la build 1811) indiquant que des certificats internes s’apprêtent à expirer. Des environnements Azure Stack Hub sur des versions antérieures à la version 1811 peuvent afficher des alertes relatives à des expirations imminentes de certificat ou de secret internes. Ces alertes sont incorrectes. Vous pouvez donc les ignorer sans exécuter la rotation des secrets internes. Les alertes d’expiration incorrectes des secrets internes sont un problème connu, résolu dans la version 1811. Les secrets internes n’expirent pas tant que l’environnement n’a pas été actif pendant deux ans.

1. Créez une session PowerShell avec le [point de terminaison privilégié](azure-stack-privileged-endpoint.md).
2. Dans la session de point de terminaison privilégié, exécutez **Start-SecretRotation -Internal**.

    > [!Note]
    > Les environnements Azure Stack Hub sur les versions antérieures à la version 1811 ne nécessitent pas l’indicateur **-Internal**. **Start-SecretRotation** effectue uniquement la rotation des secrets internes.

3. Patientez pendant la rotation de vos secrets.

   Quand la rotation des secrets a réussi, la console affiche **Overall action status: Success (état global de l’action : réussite).**
    > [!Note]
    > En cas d’échec de la rotation des secrets, suivez les instructions dans le message d’erreur et réexécutez **Start-SecretRotation** avec les paramètres **-Internal** et **-Rerun**.  

```powershell
Start-SecretRotation -Internal -ReRun
```

Contactez le support si vous rencontrez des échecs répétés de rotation des secrets.

## <a name="start-secretrotation-reference"></a>Référence Start-SecretRotation

Effectue la rotation des secrets d’un système Azure Stack Hub. Exécutée uniquement sur le point de terminaison privilégié Azure Stack Hub.

### <a name="syntax"></a>Syntaxe

#### <a name="for-external-secret-rotation"></a>Rotation des secrets externes

```powershell
Start-SecretRotation [-PfxFilesPath <string>] [-PathAccessCredential <PSCredential>] [-CertificatePassword <SecureString>]  
```

#### <a name="for-internal-secret-rotation"></a>Rotation des secrets internes

```powershell
Start-SecretRotation [-Internal]  
```

#### <a name="for-external-secret-rotation-rerun"></a>Réexécution de la rotation des secrets externes

```powershell
Start-SecretRotation [-ReRun]
```

#### <a name="for-internal-secret-rotation-rerun"></a>Réexécution de la rotation des secrets internes

```powershell
Start-SecretRotation [-ReRun] [-Internal]
```

### <a name="description"></a>Description

La cmdlet **Start-SecretRotation** effectue la rotation des secrets d’infrastructure d’un système Azure Stack Hub. Par défaut, elle effectue uniquement la rotation des certificats de tous les points de terminaison de l’infrastructure réseau externe. Si l’indicateur -Internal est utilisé, les secrets d’infrastructure internes font l’objet d’une rotation. Durant la rotation des points de terminaison de l’infrastructure réseau externe, la cmdlet **Start-SecretRotation** doit être exécutée avec un bloc de script **Invoke-Command**, et la session du point de terminaison privilégié de l’environnement Azure Stack Hub passée comme paramètre **Session**.

### <a name="parameters"></a>Paramètres

| Paramètre | Type | Obligatoire | Position | Default | Description |
| -- | -- | -- | -- | -- | -- |
| `PfxFilesPath` | String  | False  | named  | None  | Le chemin d’accès au partage de fichiers pour le répertoire **\Certificates** contenant tous les certificats de points de terminaison réseau externe. Uniquement requis lors de la rotation de secrets externes. Le répertoire de fin doit être **\Certificates**. |
| `CertificatePassword` | SecureString | False  | named  | None  | Le mot de passe pour tous les certificats fournis dans le -PfXFilesPath. Valeur requise si PfxFilesPath est fourni lors de la rotation des secrets externes. |
| `Internal` | String | False | named | None | L’indicateur interne doit être utilisé chaque fois qu’un opérateur Azure Stack Hub souhaite effectuer la rotation des secrets d’infrastructure internes. |
| `PathAccessCredential` | PSCredential | False  | named  | None  | Les informations d’identification PowerShell du partage de fichiers pour le répertoire **\Certificates** contenant tous les certificats de points de terminaison réseau externe. Uniquement requis lors de la rotation de secrets externes.  |
| `ReRun` | SwitchParameter | False  | named  | None  | ReRun doit être utilisé à chaque nouvelle tentative de rotation des secrets après un échec. |

### <a name="examples"></a>Exemples

#### <a name="rotate-only-internal-infrastructure-secrets"></a>Effectuer la rotation des secrets d’infrastructure internes uniquement

Cette commande doit être exécutée via le [point de terminaison privilégié de votre environnement](azure-stack-privileged-endpoint.md) Azure Stack Hub.

```powershell
PS C:\> Start-SecretRotation -Internal
```

Cette commande effectue la rotation de tous les secrets de l’infrastructure exposés au réseau interne Azure Stack Hub.

#### <a name="rotate-only-external-infrastructure-secrets"></a>Effectuer la rotation des secrets d’infrastructure externes uniquement  

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Create Credentials for the fileshare
$CertPassword = ConvertTo-SecureString "<CertPasswordHere>" -AsPlainText -Force
$CertShareCreds = Get-Credential
$CertSharePath = "<NetworkPathOfCertShare>"
# Run Secret Rotation
Invoke-Command -Session $PEPSession -ScriptBlock {  
    Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCreds -CertificatePassword $using:CertPassword
}
Remove-PSSession -Session $PEPSession
```

Cette commande effectue la rotation des certificats TLS utilisés pour les points de terminaison de l’infrastructure réseau externe d’Azure Stack Hub.

#### <a name="rotate-internal-and-external-infrastructure-secrets-pre-1811-only"></a>Effectuer la rotation des secrets d’infrastructure internes et externes (**avant la mise à jour 1811** uniquement)

> [!IMPORTANT]
> Cette commande s’applique uniquement aux versions d’Azure Stack Hub **antérieures à la version 1811** car la rotation a été fractionnée pour les certificats internes et externes.
>
> **Depuis la mise à jour *1811+* , vous ne pouvez plus faire pivoter des certificats internes et externes.**

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Create Credentials for the fileshare
$CertPassword = ConvertTo-SecureString "<CertPasswordHere>" -AsPlainText -Force
$CertShareCreds = Get-Credential
$CertSharePath = "<NetworkPathOfCertShare>"
# Run Secret Rotation
Invoke-Command -Session $PEPSession -ScriptBlock {
    Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCreds -CertificatePassword $using:CertPassword
}
Remove-PSSession -Session $PEPSession
```

Cette commande effectue la rotation de tous les secrets d’infrastructure exposés au réseau interne Azure Stack Hub, ainsi que des certificats TLS utilisés pour les points de terminaison de l’infrastructure réseau externe d’Azure Stack Hub. Start-SecretRotation effectue la rotation de tous les secrets générés par la pile, et comme des certificats sont fournis, la rotation des certificats de points de terminaison externes sera également effectuée.  

## <a name="update-the-baseboard-management-controller-bmc-credential"></a>Mettre à jour les informations d’identification du contrôleur BMC (Baseboard Management Controller)

Le contrôleur BMC (Baseboard Management Controller) analyse l’état physique de vos serveurs. Pour des instructions sur la mise à jour du nom et du mot de passe du compte d’utilisateur du contrôleur BMC, contactez le fabricant d’ordinateurs OEM.

>[!NOTE]
> Votre OEM peut fournir des applications de gestion supplémentaires. La mise à jour du nom d’utilisateur ou du mot de passe pour d’autres applications de gestion n’a aucun effet sur le nom d’utilisateur ou le mot de passe du contrôleur BMC.

1. **Versions antérieures à 1910** : Mettez à jour le contrôleur BMC sur les serveurs physiques Azure Stack Hub en suivant les instructions de votre fabricant OEM. Tous les contrôleurs BMC de votre environnement doivent avoir les mêmes nom et mot de passe d’utilisateur. Les noms d’utilisateur BMC ne peuvent pas dépasser 16 caractères.

   **Versions 1910 et ultérieures** : Il n’est plus nécessaire de commencer par mettre à jour les informations d’identification BMC sur les serveurs physiques Azure Stack Hub en suivant les instructions de votre fabricant OEM. Tous les contrôleurs BMC de votre environnement doivent avoir les mêmes nom et mot de passe d’utilisateur. Les noms d’utilisateur BMC ne peuvent pas dépasser 16 caractères.

    | Paramètre | Description | State |
    | --- | --- | --- |
    | BypassBMCUpdate | Quand vous utilisez le paramètre, les informations d’identification dans le BMC ne sont pas mises à jour. Seul le magasin de données interne d’Azure Stack Hub est mis à jour. | Facultatif |

2. Ouvrez un point de terminaison privilégié dans des sessions Azure Stack Hub. Pour obtenir des instructions, voir [Utilisation du point de terminaison privilégié dans Azure Stack Hub](azure-stack-privileged-endpoint.md).

3. Une fois que votre invite PowerShell est passé à **[adresse IP ou nom de machine virtuelle ERCS]: PS>** ou à **[azs-ercs01]: PS>** , en fonction de l’environnement, exécutez `Set-BmcCredential` en exécutant `Invoke-Command`. Passez la variable de session de votre point de terminaison privilégié en tant que paramètre. Par exemple :

    ```powershell
    # Interactive Version
    $PEPIp = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEPCreds = Get-Credential "<Domain>\CloudAdmin" -Message "PEP Credentials"
    $NewBmcPwd = Read-Host -Prompt "Enter New BMC password" -AsSecureString
    $NewBmcUser = Read-Host -Prompt "Enter New BMC user name"

    $PEPSession = New-PSSession -ComputerName $PEPIp -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

    Invoke-Command -Session $PEPSession -ScriptBlock {
        # Parameter BmcPassword is mandatory, while the BmcUser parameter is optional.
        Set-BmcCredential -BmcPassword $using:NewBmcPwd -BmcUser $using:NewBmcUser
    }
    Remove-PSSession -Session $PEPSession
    ```

    Vous pouvez également utiliser la version statique de PowerShell avec les mots de passe sous forme de lignes de code :

    ```powershell
    # Static Version
    $PEPIp = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEPUser = "<Privileged Endpoint user for example Domain\CloudAdmin>"
    $PEPPwd = ConvertTo-SecureString "<Privileged Endpoint Password>" -AsPlainText -Force
    $PEPCreds = New-Object System.Management.Automation.PSCredential ($PEPUser, $PEPPwd)
    $NewBmcPwd = ConvertTo-SecureString "<New BMC Password>" -AsPlainText -Force
    $NewBmcUser = "<New BMC User name>"

    $PEPSession = New-PSSession -ComputerName $PEPIp -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

    Invoke-Command -Session $PEPSession -ScriptBlock {
        # Parameter BmcPassword is mandatory, while the BmcUser parameter is optional.
        Set-BmcCredential -BmcPassword $using:NewBmcPwd -BmcUser $using:NewBmcUser
    }
    Remove-PSSession -Session $PEPSession
    ```

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur la sécurité dans Azure Stack Hub](azure-stack-security-foundations.md)
