---
title: Préparation de l’hôte d’extension pour Azure Stack | Microsoft Docs
description: Apprenez à préparer l’hôte d’extension, qui est automatiquement activé avec un nouveau package de mise à jour Azure Stack.
services: azure-stack
keywords: ''
author: mattbriggs
ms.author: mabrigg
ms.date: 06/13/2019
ms.topic: article
ms.service: azure-stack
ms.reviewer: thoroet
manager: femila
ms.lastreviewed: 03/07/2019
ms.openlocfilehash: ab508956ddcc57baa04c74710ea485c07cc20416
ms.sourcegitcommit: b79a6ec12641d258b9f199da0a35365898ae55ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67131159"
---
# <a name="prepare-for-extension-host-for-azure-stack"></a>Préparer l’hôte d’extension pour Azure Stack

L’hôte d’extension sécurise Azure Stack en réduisant le nombre de ports TCP/IP requis. Cet article passe en revue la préparation d’Azure Stack pour l’hôte d’extension. Ce dernier est automatiquement activé via un package de mise à jour Azure Stack après la mise à jour 1808. Cet article s’applique aux mises à jour 1808, 1809 et 1811 d’Azure Stack.

## <a name="certificate-requirements"></a>Configuration requise des certificats

L’hôte d’extension implémente deux nouveaux espaces de noms de domaine pour garantir que les entrées d’hôte sont uniques pour chaque extension du portail. Les nouveaux espaces de noms de domaine requièrent deux certificats génériques supplémentaires pour garantir une communication sécurisée.

Le tableau présente les nouveaux espaces de noms et les certificats associés :

| Dossier de déploiement | Objet et autres noms de l’objet (SAN) du certificat requis | Étendue (par région) | Espace de noms de sous-domaine |
|-----------------------|------------------------------------------------------------------|-----------------------|------------------------------|
| Hôte d'extension d’administration | *.adminhosting.\<region>.\<fqdn> (Certificats SSL génériques) | Hôte d'extension d’administration | adminhosting.\<region>.\<fqdn> |
| Hôte d'extension public | *.hosting.\<region>.\<fqdn> (Certificats SSL génériques) | Hôte d'extension public | hosting.\<region>.\<fqdn> |

Vous trouverez les conditions requises détaillées dans l’article [Exigences de certificat pour infrastructure à clé publique Azure Stack](azure-stack-pki-certs.md).

## <a name="create-certificate-signing-request"></a>Créer une demande de signature de certificat

L’outil de vérification de la disponibilité Azure Stack offre la possibilité de créer une demande de signature de certificat pour les deux nouveaux certificats SSL qui sont obligatoires. Pour ce faire, suivez la procédure de l’article [Génération de CSR Azure Stack](azure-stack-get-pki-certs.md).

> [!Note]  
> Vous pouvez ignorer cette étape selon la méthode utilisée pour demander vos certificats SSL.

## <a name="validate-new-certificates"></a>Valider les nouveaux certificats

1. Ouvrez PowerShell avec des autorisations élevées sur l’hôte de cycle de vie du matériel ou sur la station de travail de gestion Azure Stack.
2. Exécutez le cmdlet suivant pour installer l’outil de vérification de la disponibilité Azure Stack.

    ```powershell  
    Install-Module -Name Microsoft.AzureStack.ReadinessChecker
    ```

3. Exécutez le script suivant pour créer la structure de dossiers suivante :

    ```powershell  
    New-Item C:\Certificates -ItemType Directory

    $directories = 'ACSBlob','ACSQueue','ACSTable','Admin Portal','ARM Admin','ARM Public','KeyVault','KeyVaultInternal','Public Portal', 'Admin extension host', 'Public extension host'

    $destination = 'c:\certificates'

    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ```

    > [!Note]  
    > Si vous effectuez votre déploiement à l’aide des services de fédération Active Directory (AD FS), les répertoires suivants doivent être ajoutés à **$directories** dans le script : `ADFS`, `Graph`.

4. Placez les certificats existants, que vous utilisez actuellement dans Azure Stack, dans les répertoires appropriés. Par exemple, placez le certificat **Admin ARM** dans le dossier `Arm Admin`. Puis, placez les certificats d’hébergement qui viennent d’être créés les répertoires `Admin extension host` et `Public extension host`.
5. Exécutez la cmdlet suivante pour démarrer la vérification du certificat :

    ```powershell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 

    Start-AzsReadinessChecker -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
    ```

6. Vérifiez que la sortie et tous les certificats réussissent tous les tests.


## <a name="import-extension-host-certificates"></a>Importer les certificats d’hôte d’extension

Utilisez un ordinateur capable de se connecter au point de terminaison Azure Stack privilégié pour les étapes suivantes. Assurez-vous d’avoir accès aux nouveaux fichiers de certificat à partir de cet ordinateur.

1. Utilisez un ordinateur capable de se connecter au point de terminaison Azure Stack privilégié pour les étapes suivantes. Assurez-vous d’avoir accès aux nouveaux fichiers de certificat à partir de cet ordinateur.
2. Ouvrez PowerShell ISE pour exécuter les blocs de script suivants.
3. Importez le certificat pour le point de terminaison d’hébergement d’administration.

    ```powershell  

    $CertPassword = read-host -AsSecureString -prompt "Certificate Password"

    $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."

    [Byte[]]$AdminHostingCertContent = [Byte[]](Get-Content c:\certificate\myadminhostingcertificate.pfx -Encoding Byte)

    Invoke-Command -ComputerName <PrivilegedEndpoint computer name> `
    -Credential $CloudAdminCred `
    -ConfigurationName "PrivilegedEndpoint" `
    -ArgumentList @($AdminHostingCertContent, $CertPassword) `
    -ScriptBlock {
            param($AdminHostingCertContent, $CertPassword)
            Import-AdminHostingServiceCert $AdminHostingCertContent $certPassword
    }
    ```
4. Importez le certificat pour le point de terminaison d’hébergement.
    ```powershell  
    $CertPassword = read-host -AsSecureString -prompt "Certificate Password"

    $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."

    [Byte[]]$HostingCertContent = [Byte[]](Get-Content c:\certificate\myhostingcertificate.pfx  -Encoding Byte)

    Invoke-Command -ComputerName <PrivilegedEndpoint computer name> `
    -Credential $CloudAdminCred `
    -ConfigurationName "PrivilegedEndpoint" `
    -ArgumentList @($HostingCertContent, $CertPassword) `
    -ScriptBlock {
            param($HostingCertContent, $CertPassword)
            Import-UserHostingServiceCert $HostingCertContent $certPassword
    }
    ```

### <a name="update-dns-configuration"></a>Mettre à jour la configuration DNS

> [!Note]  
> Cette étape n’est pas obligatoire si vous avez utilisé la délégation de Zone DNS pour l’intégration DNS.
Si les enregistrements d’un hôte A individuel ont été configurés de sorte à publier des points de terminaison Azure Stack, vous devez créer deux enregistrements d’hôte A supplémentaires :

| IP | Nom d’hôte | Type |
|----|------------------------------|------|
| \<IP> | *.Adminhosting.\<Region>.\<FQDN> | A |
| \<IP> | *.Hosting.\<Region>.\<FQDN> | A |

Les adresses IP allouées peuvent être récupérées à l’aide du point de terminaison privilégié en exécutant le cmdlet **Get-AzureStackStampInformation**.

### <a name="ports-and-protocols"></a>Ports et protocoles

L’article [Intégration au centre de données Azure Stack : publier des points de terminaison](azure-stack-integrate-endpoints.md) traite des ports et protocoles qui ont besoin d’une communication entrante pour publier Azure Stack avant le lancement de l’hôte d’extension.

### <a name="publish-new-endpoints"></a>Publier les nouveaux points de terminaison

Il existe deux nouveaux points de terminaison requis pour être publiés via votre pare-feu. Les adresses IP allouées à partir du pool d’adresses IP virtuelles peuvent être récupérées à l'aide du code suivant qui doit être exécuté à partir du [point de terminaison privilégié de votre environnement](azure-stack-privileged-endpoint.md) Azure Stack.

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Obtain DNS Servers and Extension Host information from Azure Stack Stamp Information and find the IPs for the Host Extension Endpoints
$StampInformation = Invoke-Command $PEPSession {Get-AzureStackStampInformation} | Select-Object -Property ExternalDNSIPAddress01, ExternalDNSIPAddress02, @{n="TenantHosting";e={($_.TenantExternalEndpoints.TenantHosting) -replace "https://*.","testdnsentry"-replace "/"}},  @{n="AdminHosting";e={($_.AdminExternalEndpoints.AdminHosting)-replace "https://*.","testdnsentry"-replace "/"}},@{n="TenantHostingDNS";e={($_.TenantExternalEndpoints.TenantHosting) -replace "https://",""-replace "/"}},  @{n="AdminHostingDNS";e={($_.AdminExternalEndpoints.AdminHosting)-replace "https://",""-replace "/"}}
If (Resolve-DnsName -Server $StampInformation.ExternalDNSIPAddress01 -Name $StampInformation.TenantHosting -ErrorAction SilentlyContinue) {
    Write-Host "Can access AZS DNS" -ForegroundColor Green
    $AdminIP = (Resolve-DnsName -Server $StampInformation.ExternalDNSIPAddress02 -Name $StampInformation.AdminHosting).IPAddress
    Write-Host "The IP for the Admin Extension Host is: $($StampInformation.AdminHostingDNS) - is: $($AdminIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.AdminHostingDNS), Value: $($AdminIP)" -ForegroundColor Green
    $TenantIP = (Resolve-DnsName -Server $StampInformation.ExternalDNSIPAddress01 -Name $StampInformation.TenantHosting).IPAddress
    Write-Host "The IP address for the Tenant Extension Host is $($StampInformation.TenantHostingDNS) - is: $($TenantIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.TenantHostingDNS), Value: $($TenantIP)" -ForegroundColor Green
}
Else {
    Write-Host "Cannot access AZS DNS" -ForegroundColor Yellow
    $AdminIP = (Resolve-DnsName -Name $StampInformation.AdminHosting).IPAddress
    Write-Host "The IP for the Admin Extension Host is: $($StampInformation.AdminHostingDNS) - is: $($AdminIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.AdminHostingDNS), Value: $($AdminIP)" -ForegroundColor Green
    $TenantIP = (Resolve-DnsName -Name $StampInformation.TenantHosting).IPAddress
    Write-Host "The IP address for the Tenant Extension Host is $($StampInformation.TenantHostingDNS) - is: $($TenantIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.TenantHostingDNS), Value: $($TenantIP)" -ForegroundColor Green
}
Remove-PSSession -Session $PEPSession
```

#### <a name="sample-output"></a>Exemple de sortie

```powershell
Can access AZS DNS
The IP for the Admin Extension Host is: *.adminhosting.\<region>.\<fqdn> - is: xxx.xxx.xxx.xxx
The Record to be added in the DNS zone: Type A, Name: *.adminhosting.\<region>.\<fqdn>, Value: xxx.xxx.xxx.xxx
The IP address for the Tenant Extension Host is *.hosting.\<region>.\<fqdn> - is: xxx.xxx.xxx.xxx
The Record to be added in the DNS zone: Type A, Name: *.hosting.\<region>.\<fqdn>, Value: xxx.xxx.xxx.xxx
```

> [!Note]  
> Effectuez cette modification avant d’activer l’hôte d’extension. Ainsi, les portails Azure Stack sont accessibles en continu.

| Point de terminaison (VIP) | Protocole | Ports |
|----------------|----------|-------|
| Hébergement Admin | HTTPS | 443 |
| Hébergement | HTTPS | 443 |

### <a name="update-existing-publishing-rules-post-enablement-of-extension-host"></a>Mettre à jour les règles de publication existantes (activation postérieure à l’hôte d’extension)

> [!Note]  
> Le package de mise à jour 1808 Azure Stack **n’active pas** l’hôte d’extension. Il permet seulement de préparer l’hôte d’extension en important les certificats requis. Ne fermez aucun port avant que l’hôte d’extension ne soit activé automatiquement via un package de mise à jour Azure Stack après la mise à jour 1808.

Les ports de points de terminaison existants suivants doivent être fermés dans vos règles de pare-feu existantes.

> [!Note]  
> Il est recommandé de fermer ces ports après une validation réussie.

| Point de terminaison (VIP) | Protocole | Ports |
|----------------------------------------|----------|-------------------------------------------------------------------------------------------------------------------------------------|
| Portail (administrateur) | HTTPS | 12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13012<br>13020<br>13021<br>13026<br>30015 |
| Portail (utilisateur) | HTTPS | 12495<br>12649<br>13001<br>13010<br>13011<br>13012<br>13020<br>13021<br>30015<br>13003 |
| Azure Resource Manager (administrateur) | HTTPS | 30024 |
| Azure Resource Manager (utilisateur) | HTTPS | 30024 |

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [Intégration du pare-feu](azure-stack-firewall.md).
- En savoir plus sur la [Génération de CSR Azure Stack](azure-stack-get-pki-certs.md).
