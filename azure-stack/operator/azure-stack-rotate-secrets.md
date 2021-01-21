---
title: Faire pivoter les clés secrètes
titleSuffix: Azure Stack Hub
description: Apprenez à effectuer la rotation de vos secrets dans Azure Stack Hub.
author: BryanLa
ms.topic: how-to
ms.date: 01/19/2021
ms.reviewer: fiseraci
ms.author: bryanla
ms.lastreviewed: 01/19/2021
monikerRange: '>=azs-1803'
ms.openlocfilehash: d7c75bc9864e564736b03477a3c37140e752d850
ms.sourcegitcommit: 0983c1f90734b7ea5e23ae614eeaed38f9cb3c9a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98571346"
---
# <a name="rotate-secrets-in-azure-stack-hub"></a>Effectuer la rotation des secrets dans Azure Stack Hub

Cet article fournit des instructions pour effectuer la rotation des secrets, afin de maintenir une communication sécurisée avec les ressources et services de l’infrastructure Azure Stack Hub.

## <a name="overview"></a>Vue d'ensemble

Azure Stack Hub utilise des secrets pour maintenir une communication sécurisée avec les ressources et services d’infrastructure. Pour maintenir l’intégrité de l’infrastructure Azure Stack Hub, les opérateurs doivent pouvoir effectuer une rotation des secrets à une fréquence conforme aux exigences de sécurité de leur organisation.

Lorsque les secrets approchent de leur date d’expiration, les alertes suivantes sont générées dans le portail administrateur. La rotation des secrets permet de résoudre les alertes suivantes :

- Pending service account password expiration (Expiration imminente du mot de passe du compte de service)
- Pending internal certificate expiration (Expiration imminente du certificat interne)
- Pending external certificate expiration (Expiration imminente du certificat externe)

> [!WARNING]
> Il y a 2 phases d’alertes déclenchées dans le portail d’administration avant l’expiration :
> - 90 jours avant l’expiration, une alerte d’avertissement est générée.
> - 30 jours avant l’expiration, une alerte critique est générée. 
>
> **Il est *primordial* que vous terminiez la rotation des secrets si vous recevez ces notifications. Dans le cas contraire, vous risquez de provoquer la perte de charges de travail et le redéploiement d’Azure Stack Hub à vos propres frais.**

Pour plus d’informations sur la surveillance et la correction des alertes, reportez-vous à [Surveiller l’intégrité et les alertes dans Azure Stack Hub](azure-stack-monitor-health.md).

::: moniker range="<azs-1811"  
> [!NOTE]
> Des environnements Azure Stack Hub sur des versions antérieures à la version 1811 peuvent afficher des alertes relatives à des expirations imminentes de certificat ou de secret internes. Ces alertes sont incorrectes. Vous pouvez donc les ignorer sans exécuter la rotation des secrets internes. Les alertes d’expiration incorrectes des secrets internes sont un problème connu, résolu dans la version 1811. Les secrets internes n’expirent pas tant que l’environnement n’a pas été actif pendant deux ans.
::: moniker-end

## <a name="prerequisites"></a>Prérequis

1. Il est fortement recommandé de commencer par mettre à jour l’instance Azure Stack Hub vers la [dernière version](release-notes.md).

    ::: moniker range="<azs-1811"  
    >[!IMPORTANT]
    > Pour les versions antérieures à la version 1811 :
    > - Si une rotation des secrets a déjà été effectuée, vous devez effectuer une mise à jour vers la version 1811 ou une version ultérieure avant d’exécuter à nouveau une rotation des secrets. Une rotation des secrets doit être exécutée via le [point de terminaison privilégié](azure-stack-privileged-endpoint.md) et nécessite les informations d’identification de l’opérateur Azure Stack Hub. Si vous ignorez si une rotation des secrets a été exécutée sur votre environnement, effectuez une mise à jour vers la version 1811 avant d’effectuer une rotation des secrets.
    > - Vous n’avez pas besoin d’effectuer une rotation des secrets pour ajouter des certificats d’hôte d’extension. Suivez les instructions de l’article [Préparer un hôte d’extension pour Azure Stack Hub](azure-stack-extension-host-prepare.md) pour ajouter des certificats d’hôte d’extension.
    ::: moniker-end

2. Notifiez vos utilisateurs des opérations de maintenance planifiée. Planifiez les fenêtres de maintenance normale pendant les heures creuses, autant que possible. Les opérations de maintenance peuvent affecter les opérations du portail et les charges de travail des utilisateurs.

3. Pendant la rotation des secrets, les opérateurs remarqueront peut-être que des alertes s’ouvrent et se ferment automatiquement. Ce comportement est normal et les alertes peuvent être ignorées. Les opérateurs peuvent vérifier la validité de ces alertes à l’aide de la [cmdlet PowerShell Test-AzureStack](azure-stack-diagnostic-test.md). Pour les opérateurs utilisant System Center Operations Manager afin de superviser les systèmes Azure Stack Hub, le placement d’un système en mode maintenance empêche ces alertes d’atteindre leurs systèmes ITSM, mais continue d’alerter si le système Azure Stack Hub devient inaccessible.

::: moniker range=">=azs-1811"
## <a name="rotate-external-secrets"></a>Effectuer une rotation des secrets externes

> [!Important]
> Rotation des secrets externes :
> - **La rotation des secrets autres que des certificats, comme les clés et chaînes sécurisées** doit être effectuée manuellement par l’administrateur. Sont inclus les mots de passe des comptes d’utilisateur et d’administrateur, ainsi que [les mots de passe de commutateur réseau](azure-stack-customer-defined.md).
> - **La rotation des secrets de fournisseur de ressources à valeur ajoutée** est expliquée dans une aide distincte :
>    - [App Service sur Azure Stack Hub](app-service-rotate-certificates.md)
>    - [Event Hubs sur Azure Stack Hub](event-hubs-rp-rotate-secrets.md)
>    - [IoT Hub sur Azure Stack Hub](iot-hub-rp-rotate-secrets.md)
>    - [MySQL sur Azure Stack Hub](azure-stack-mysql-resource-provider-maintain.md#secrets-rotation)
>    - [SQL sur Azure Stack Hub](azure-stack-sql-resource-provider-maintain.md#secrets-rotation)
> - **La rotation des informations d’identification du contrôleur de gestion de la carte de base (BMC)** est également un processus manuel, [décrit plus loin dans cet article](#update-the-bmc-credential). 

Cette section traite de la rotation des certificats utilisés pour sécuriser des services externes. Ces certificats sont fournis par l’opérateur Azure Stack Hub, pour les services suivants :

- Portail administrateur
- Portail public
- Administrateur Azure Resource Manager
- Global Azure Resource Manager
- Administrateur Key Vault
- Key Vault
- Hôte d’extension d’administration
- ACS (y compris stockage objet blob, table et file d’attente)
- ADFS<sup>*</sup>
- Graph<sup>*</sup>

<sup>*</sup>Applicable dans le cadre de l’utilisation des services de fédération Active Directory (AD FS).

### <a name="preparation"></a>Préparation

Avant d’effectuer la rotation des secrets externes :

1. Exécutez l’applet de commande PowerShell **[`Test-AzureStack`](azure-stack-diagnostic-test.md)** en utilisant le paramètre `-group SecretRotationReadiness`, pour confirmer que toutes les sorties de test sont intègres avant d’effectuer la rotation des secrets.
2. Préparez un nouveau jeu de certificats externes de remplacement :
   - Le nouveau jeu doit répondre aux spécifications de certificat décrites dans la page [Exigences de certificat d’infrastructure de clés publiques Azure Stack Hub](azure-stack-pki-certs.md). 
   - Générez une demande de signature de certificat (CSR) à soumettre à votre autorité de certification. Suivez la procédure décrite dans [Générer les demandes de signature de certificat](azure-stack-get-pki-certs.md), puis préparez les demandes en vue de les utiliser dans votre environnement Azure Stack Hub en procédant de la manière décrite dans [Préparer des certificats d’infrastructure à clé publique](azure-stack-prepare-pki-certs.md). Azure Stack Hub prend en charge la rotation des secrets pour les certificats externes obtenus auprès d’une nouvelle autorité de certification dans les contextes suivants :

     |Rotation depuis l’autorité de certification|Rotation vers l’autorité de certification|Prise en charge des versions Azure Stack Hub|
     |-----|-----|-----|-----|
     |Auto-signé|Enterprise| 1903 et versions ultérieures|
     |Auto-signé|Auto-signé|Non pris en charge|
     |Auto-signé|Public<sup>*</sup>|1803 et versions ultérieures|
     |Entreprise|Entreprise|1803 et versions ultérieures, 1803-1903 si l’autorité de certification d’entreprise est la MÊME que celle utilisée lors du déploiement|
     |Enterprise|Auto-signé|Non pris en charge|
     |Enterprise|Public<sup>*</sup>|1803 et versions ultérieures|
     |Public<sup>*</sup>|Enterprise|1903 et versions ultérieures|
     |Public<sup>*</sup>|Auto-signé|Non pris en charge|
     |Public<sup>*</sup>|Public<sup>*</sup>|1803 et versions ultérieures|

     <sup>*</sup>Dans le cadre du [programme de certification racine approuvé Windows](/security/trusted-root/participants-list).

   - Veillez à valider les certificats que vous préparez en suivant la procédure décrite dans [Valider des certificats PKI](azure-stack-validate-pki-certs.md).
   - Vérifiez que le mot de passe ne contient pas de caractères spéciaux, tels que `*` ou `)`.
   - Vérifiez que le chiffrement PFX est **TripleDES-SHA1**. Si vous rencontrez un problème, voir [Corriger les problèmes courants liés aux certificats d’infrastructure de clés publiques Azure Stack Hub](azure-stack-remediate-certs.md#pfx-encryption).

3. Stockez une sauvegarde des certificats utilisés pour la rotation dans un emplacement de sauvegarde sécurisé. Si votre rotation s’exécute puis échoue, remplacez les certificats dans le partage de fichiers par les copies de sauvegarde avant d’exécuter à nouveau la rotation. Conservez des copies de sauvegarde dans l’emplacement de sauvegarde sécurisé.
4. Créez un partage de fichiers auquel vous pouvez accéder depuis les machines virtuelles ERCS. Le partage de fichiers doit être accessible en lecture et en écriture pour l’identité **CloudAdmin**.
5. Ouvrez une console PowerShell ISE à partir d’un ordinateur sur lequel vous avez accès au partage de fichiers. Accédez à votre partage de fichiers afin de créer des répertoires dans lesquels placer vos certificats externes.
6. Téléchargez **[CertDirectoryMaker.ps1](https://www.aka.ms/azssecretrotationhelper)** sur votre partage de fichiers réseau, puis exécutez le script. Le script crée une structure de dossiers conforme à **_.\Certificates\AAD_ *_ ou à _* _.\Certificates\ADFS_ *_, en fonction de votre fournisseur d’identité. Votre structure de dossiers doit commencer par un dossier _* \\Certificates**, suivi UNIQUEMENT d’un dossier **\\AAD** ou **\\ADFS**. Tous les sous-répertoires restants sont contenus dans la structure précédente. Par exemple :
    - Partage de fichiers = **\\\\\<IPAddress>\\\<ShareName>**
    - Dossier racine de certificat pour le fournisseur Azure AD = **\\Certificates\AAD**
    - FullPath = **\\\\\<IPAddress>\\\<ShareName>\Certificates\AAD**

    > [!IMPORTANT]
    > Lorsque vous exécutez `Start-SecretRotation` par la suite, il valide la structure de dossiers. Une structure de dossiers non conforme génère l’erreur suivante :
    >
    > ```powershell
    > Cannot bind argument to parameter 'Path' because it is null.
    > + CategoryInfo          : InvalidData: (:) [Test-Certificate], ParameterBindingValidationException
    > + FullyQualifiedErrorId : ParameterArgumentValidationErrorNullNotAllowed,Test-Certificate
    > + PSComputerName        : xxx.xxx.xxx.xxx
    > ```

7. Copiez le nouveau jeu de certificats externes de remplacement créé à l’étape 2 vers le répertoire **\Certificates\\\<IdentityProvider>** créé à l’étape 6. Veillez à suivre le format `cert.<regionName>.<externalFQDN>` pour \<CertName\>. 

    Voici une exemple de structure de dossiers pour le fournisseur d’identité Azure AD :
    ```powershell
        <ShareName>
            │
            └───Certificates
                  └───AAD
                      ├───ACSBlob
                      │       <CertName>.pfx
                      │
                      ├───ACSQueue
                      │       <CertName>.pfx
                      │
                      ├───ACSTable
                      │       <CertName>.pfx
                      │
                      ├───Admin Extension Host
                      │       <CertName>.pfx
                      │
                      ├───Admin Portal
                      │       <CertName>.pfx
                      │
                      ├───ARM Admin
                      │       <CertName>.pfx
                      │
                      ├───ARM Public
                      │       <CertName>.pfx
                      │
                      ├───KeyVault
                      │       <CertName>.pfx
                      │
                      ├───KeyVaultInternal
                      │       <CertName>.pfx
                      │
                      ├───Public Extension Host
                      │       <CertName>.pfx
                      │
                      └───Public Portal
                              <CertName>.pfx

    ```

### <a name="rotation"></a>Rotation

Pour effectuer une rotation des secrets externes, procédez comme suit :

1. Utilisez le script PowerShell suivant pour effectuer la rotation des secrets. Le script requiert un accès à une session de point de terminaison privilégié (PEP). Vous accédez au point de terminaison privilégié via une session PowerShell à distance sur la machine virtuelle qui l’héberge. Si vous utilisez un système intégré, il existe trois instances du point de terminaison privilégié, chacune s’exécutant à l’intérieur d’une machine virtuelle (Prefix-ERCS01, Prefix-ERCS02 ou Prefix-ERCS03) sur des hôtes différents. Si vous utilisez l’ASDK, cette machine virtuelle est nommée AzS-ERCS01. Mettez à jour les valeurs de `<placeholder>` avant d’exécuter :

    ```powershell
    # Create a PEP Session
    winrm s winrm/config/client '@{TrustedHosts= "<IP_address_of_ERCS>"}'
    $PEPCreds = Get-Credential
    $PEPSession = New-PSSession -ComputerName <IP_address_of_ERCS_Machine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

    # Run Secret Rotation
    $CertPassword = ConvertTo-SecureString "<Cert_Password>" -AsPlainText -Force
    $CertShareCreds = Get-Credential
    $CertSharePath = "<Network_Path_Of_CertShare>"
    Invoke-Command -Session $PEPSession -ScriptBlock {
        Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCreds -CertificatePassword $using:CertPassword
    }
    Remove-PSSession -Session $PEPSession
    ```

    Le script effectue les étapes suivantes :

    - Crée une session PowerShell avec le [point de terminaison privilégié](azure-stack-privileged-endpoint.md) en utilisant le compte **CloudAdmin**, et stocke la session en tant que variable. Cette variable est utilisée en tant que paramètre à l’étape suivante. 

    - Exécute la commande [Invoke-Command](/powershell/module/microsoft.powershell.core/Invoke-Command) en passant la variable de session PEP en tant que paramètre `-Session`.

    - Exécute `Start-SecretRotation` dans la session PEP, en utilisant les paramètres suivants :
        - `-PfxFilesPath`: chemin d’accès réseau de votre répertoire de certificats créé précédemment.  
        - `-PathAccessCredential`: objet PSCredential pour les informations d’identification pour l’accès au partage.
        - `-CertificatePassword`: Une chaîne sécurisée du mot de passe utilisée pour tous les fichiers de certificat pfx créés.

2. La rotation des secrets externes prend environ une heure. Une fois l’opération terminée, votre console affiche un message `ActionPlanInstanceID ... CurrentStatus: Completed`, suivi de `DONE`. Supprimez vos certificats du partage créé dans la section Préparation, puis stockez-les à leur emplacement de sauvegarde sécurisé.

    > [!Note]
    > En cas d’échec de la rotation des secrets, suivez les instructions figurant dans le message d’erreur, puis réexécutez `Start-SecretRotation` avec le paramètre `-ReRun`.
    >
    >```powershell
    >Start-SecretRotation -ReRun
    >```  
    >
    >Contactez le support si vous rencontrez des échecs répétés de rotation des secrets.
::: moniker-end

## <a name="rotate-internal-secrets"></a>Effectuer une rotation des secrets internes

Les secrets internes incluent les certificats, mots de passe, chaînes sécurisées et clés utilisés par l’infrastructure Azure Stack Hub sans intervention de l’opérateur Azure Stack Hub. Une rotation de secret interne n’est requise que si vous soupçonnez que quelqu’un a été compromis, ou si vous avez reçu une alerte d’expiration. 
::: moniker range="<azs-1811"  
Des déploiements antérieurs à la version 1811 peuvent afficher des alertes relatives à des expirations imminentes de secrets ou certificats internes. Ces alertes sont inexactes et doivent être ignorées. Il s’agit d’un problème connu résolu dans la version 1811.
::: moniker-end

Pour effectuer une rotation des secrets internes, effectuez les étapes suivantes :

1. Exécutez le script PowerShell suivant. Notez que pour la rotation interne des secrets, la section « Exécuter la rotation des secrets » utilise uniquement le paramètre `-Internal` de l’[applet de commande Start-SecretRotation](../reference/pep-2002/start-secretrotation.md) :

    ```powershell
    # Create a PEP Session
    winrm s winrm/config/client '@{TrustedHosts= "<IP_address_of_ERCS>"}'
    $PEPCreds = Get-Credential
    $PEPSession = New-PSSession -ComputerName <IP_address_of_ERCS_Machine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

    # Run Secret Rotation
    Invoke-Command -Session $PEPSession -ScriptBlock {
        Start-SecretRotation -Internal
    }
    Remove-PSSession -Session $PEPSession
    ```

    ::: moniker range="<azs-1811"
    > [!Note]
    > Les versions antérieures à la version 1811 ne nécessitent pas l’indicateur `-Internal`. 
    ::: moniker-end


2. Une fois l’opération terminée, votre console affiche un message `ActionPlanInstanceID ... CurrentStatus: Completed`, suivi de `DONE`.

    > [!Note]
    > En cas d’échec de la rotation des secrets, suivez les instructions dans le message d’erreur et réexécutez `Start-SecretRotation` avec les paramètres `-Internal` et `-ReRun`.  
    >
    >```powershell
    >Start-SecretRotation -Internal -ReRun
    >```
    >
    > Contactez le support si vous rencontrez des échecs répétés de rotation des secrets.

## <a name="update-the-bmc-credential"></a>Mettre à jour les informations d’identification du contrôleur de gestion de la carte de base (BMC)

Le contrôleur de gestion de la carte de base (BMC) analyse l’état physique de vos serveurs. Pour des instructions sur la mise à jour du nom et du mot de passe du compte d’utilisateur du contrôleur BMC, contactez le fabricant d’ordinateurs OEM.

>[!NOTE]
> Votre OEM peut fournir des applications de gestion supplémentaires. La mise à jour du nom d’utilisateur ou du mot de passe pour d’autres applications de gestion n’a aucun effet sur le nom d’utilisateur ou le mot de passe du contrôleur de gestion de la carte de base. 

::: moniker range="<azs-1910"
1. Mettez à jour le contrôleur BMC sur les serveurs physiques Azure Stack Hub en suivant les instructions de votre fabricant OEM. Tous les contrôleurs BMC de votre environnement doivent avoir les mêmes nom et mot de passe d’utilisateur. Les noms d’utilisateur BMC ne peuvent pas dépasser 16 caractères.
::: moniker-end

::: moniker range=">=azs-1910"
1. Il n’est plus nécessaire de commencer par mettre à jour les informations d’identification BMC sur les serveurs physiques Azure Stack Hub en suivant les instructions de votre fabricant OEM. Les nom d’utilisateur et mot de passe de chaque BMC dans votre environnement doivent être identiques, et ne peuvent pas dépasser 16 caractères. 
::: moniker-end

2. Ouvrez un point de terminaison privilégié dans des sessions Azure Stack Hub. Pour obtenir des instructions, voir [Utilisation du point de terminaison privilégié dans Azure Stack Hub](azure-stack-privileged-endpoint.md). 

3. Après avoir ouvert une session de point de terminaison privilégié, exécutez l’un des scripts PowerShell ci-dessous, qui utilisent Invoke-Command pour exécuter Set-BmcCredential. Si vous utilisez le paramètre facultatif -BypassBMCUpdate avec Set-BMCCredential, les informations d’identification dans le BMC ne sont pas mises à jour. Seul le magasin de données interne Azure Stack Hub est mis à jour. Transmettez votre variable de session de point de terminaison privilégié en tant que paramètre.

    Voici un exemple de script PowerShell qui vous invite à entrer le nom d’utilisateur et le mot de passe : 

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

    Vous pouvez également encoder le nom d’utilisateur et le mot de passe dans des variables qui peuvent être moins sécurisées :

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

## <a name="reference-start-secretrotation-cmdlet"></a>Référence : Cmdlet Start-SecretRotation

La [cmdlet Start-SecretRotation](../reference/pep-2002/start-secretrotation.md) effectue la rotation des secrets d’infrastructure d’un système Azure Stack Hub. Cette cmdlet ne peut être exécutée que sur le point de terminaison privilégié Azure Stack Hub, à l’aide d’un bloc de script  `Invoke-Command` passant la session PEP dans le paramètre `-Session`. Par défaut, elle effectue uniquement la rotation des certificats de tous les points de terminaison de l’infrastructure réseau externe.

| Paramètre | Type | Obligatoire | Position | Default | Description |
|--|--|--|--|--|--|
| `PfxFilesPath` | String  | False  | named  | None  | Le chemin d’accès au partage de fichiers pour le répertoire **\Certificates** contenant tous les certificats de points de terminaison réseau externe. Uniquement requis lors de la rotation de secrets externes. Le répertoire de fin doit être **\Certificates**. |
| `CertificatePassword` | SecureString | False  | named  | None  | Le mot de passe pour tous les certificats fournis dans le -PfXFilesPath. Valeur requise si PfxFilesPath est fourni lors de la rotation des secrets externes. |
| `Internal` | String | False | named | None | L’indicateur interne doit être utilisé chaque fois qu’un opérateur Azure Stack Hub souhaite effectuer la rotation des secrets d’infrastructure internes. |
| `PathAccessCredential` | PSCredential | False  | named  | None  | Les informations d’identification PowerShell du partage de fichiers pour le répertoire **\Certificates** contenant tous les certificats de points de terminaison réseau externe. Uniquement requis lors de la rotation de secrets externes.  |
| `ReRun` | SwitchParameter | False  | named  | None  | Doit être utilisé à chaque nouvelle tentative de rotation des secrets après un échec. |

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
winrm s winrm/config/client '@{TrustedHosts= "<IP_address_of_ERCS>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IP_address_of_ERCS> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

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

::: moniker range="<azs-1811"
#### <a name="rotate-internal-and-external-infrastructure-secrets-pre-1811-only"></a>Effectuer la rotation des secrets d’infrastructure internes et externes (**avant la mise à jour 1811** uniquement)

> [!IMPORTANT]
> Cette commande s’applique uniquement aux versions d’Azure Stack Hub **antérieures à la version 1811** car la rotation a été fractionnée pour les certificats internes et externes.
>
> **Depuis la mise à jour *1811+* , vous ne pouvez plus faire pivoter des certificats internes et externes.**

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IP_address_of_ERCS>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IP_address_of_ERCS> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

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

Cette commande effectue la rotation des secrets d’infrastructure exposés au réseau interne Azure Stack Hub, ainsi que des certificats TLS utilisés pour les points de terminaison de l’infrastructure réseau externe d’Azure Stack Hub. Start-SecretRotation effectue la rotation de tous les secrets générés par la pile, et comme des certificats sont fournis, la rotation des certificats de points de terminaison externes sera également effectuée.  
::: moniker-end

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur la sécurité dans Azure Stack Hub](azure-stack-security-foundations.md)
