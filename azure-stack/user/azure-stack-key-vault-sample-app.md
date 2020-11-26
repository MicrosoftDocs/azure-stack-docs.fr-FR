---
title: Autoriser des applications à accéder aux secrets d’un Key Vault Azure Stack Hub
description: Découvrez comment exécuter un exemple d’application qui récupère des clés et secrets d’un coffre de clés dans Azure Stack Hub.
author: sethmanheim
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: sethm
ms.lastreviewed: 11/20/2020
ms.openlocfilehash: b30a99182f1c8c1392ae73ec0e70bc06b35a343f
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95518294"
---
# <a name="allow-apps-to-access-azure-stack-hub-key-vault-secrets"></a>Autoriser des applications à accéder aux secrets d’un Key Vault Azure Stack Hub

Les étapes de cet article décrivent comment exécuter l’exemple d’application **HelloKeyVault** qui récupère des clés et secrets d’un coffre de clés dans Azure Stack Hub.

## <a name="prerequisites"></a>Prérequis

Vous pouvez installer les prérequis suivants à partir du [Kit de développement Azure Stack](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) ou à partir d’un client externe Windows si vous êtes [connecté via un VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn) :

* Installez les [modules Azure PowerShell compatibles avec Azure Stack Hub](../operator/powershell-install-az-module.md).
* Téléchargez les [outils nécessaires pour utiliser Azure Stack Hub](../operator/azure-stack-powershell-download.md).

## <a name="create-a-key-vault-and-register-an-app"></a>Créer un coffre de clés et inscrire une application

Pour préparer l’exemple d’application :

* Créez un coffre de clés dans Azure Stack Hub.
* Inscrivez une application dans Azure Active Directory (Azure AD).

Utilisez le portail Azure ou PowerShell pour préparer l’exemple d’application.

> [!NOTE]
> Par défaut, le script PowerShell crée une application dans Active Directory. Cependant, vous pouvez inscrire une de vos applications existantes.

Avant d’exécuter le script suivant, veillez à fournir des valeurs pour les variables `aadTenantName` et `applicationPassword`. Si vous ne spécifiez pas de valeur pour `applicationPassword`, ce script génère un mot de passe aléatoire.

### <a name="az-modules"></a>[Modules Az](#tab/az)

```powershell
$vaultName           = 'myVault'
$resourceGroupName   = 'myResourceGroup'
$applicationName     = 'myApp'
$location            = 'local'

# Password for the application. If not specified, this script generates a random password during app creation.
$applicationPassword = ''

# Function to generate a random password for the application.
Function GenerateSymmetricKey()
{
    $key = New-Object byte[](32)
    $rng = [System.Security.Cryptography.RNGCryptoServiceProvider]::Create()
    $rng.GetBytes($key)
    return [System.Convert]::ToBase64String($key)
}

Write-Host 'Please log into your Azure Stack Hub user environment' -foregroundcolor Green

$tenantARM = "https://management.local.azurestack.external"
$aadTenantName = "FILL THIS IN WITH YOUR AAD TENANT NAME. FOR EXAMPLE: myazurestack.onmicrosoft.com"

# Configure the Azure Stack Hub operator's PowerShell environment.
Add-AzEnvironment `
  -Name "AzureStackUser" `
  -ArmEndpoint $tenantARM

$TenantID = Get-AzsDirectoryTenantId `
  -AADTenantName $aadTenantName `
  -EnvironmentName AzureStackUser

# Sign in to the user portal.
Add-AzAccount `
  -EnvironmentName "AzureStackUser" `
  -TenantId $TenantID `

$now = [System.DateTime]::Now
$oneYearFromNow = $now.AddYears(1)

$applicationPassword = GenerateSymmetricKey

# Create a new Azure AD application.
$identifierUri = [string]::Format("http://localhost:8080/{0}",[Guid]::NewGuid().ToString("N"))
$homePage = "https://contoso.com"

Write-Host "Creating a new AAD Application"
$ADApp = New-AzADApplication `
  -DisplayName $applicationName `
  -HomePage $homePage `
  -IdentifierUris $identifierUri `
  -StartDate $now `
  -EndDate $oneYearFromNow `
  -Password $applicationPassword

Write-Host "Creating a new AAD service principal"
$servicePrincipal = New-AzADServicePrincipal `
  -ApplicationId $ADApp.ApplicationId

# Create a new resource group and a key vault in that resource group.
New-AzResourceGroup `
  -Name $resourceGroupName `
  -Location $location

Write-Host "Creating vault $vaultName"
$vault = New-AzKeyVault -VaultName $vaultName `
  -ResourceGroupName $resourceGroupName `
  -Sku standard `
  -Location $location

# Specify full privileges to the vault for the application.
Write-Host "Setting access policy"
Set-AzKeyVaultAccessPolicy -VaultName $vaultName `
  -ObjectId $servicePrincipal.Id `
  -PermissionsToKeys all `
  -PermissionsToSecrets all

Write-Host "Paste the following settings into the app.config file for the HelloKeyVault project:"
'<add key="VaultUrl" value="' + $vault.VaultUri + '"/>'
'<add key="AuthClientId" value="' + $servicePrincipal.ApplicationId + '"/>'
'<add key="AuthClientSecret" value="' + $applicationPassword + '"/>'
Write-Host
```
### <a name="azurerm-modules"></a>[Modules AzureRM](#tab/azurerm)

```powershell
$vaultName           = 'myVault'
$resourceGroupName   = 'myResourceGroup'
$applicationName     = 'myApp'
$location            = 'local'

# Password for the application. If not specified, this script generates a random password during app creation.
$applicationPassword = ''

# Function to generate a random password for the application.
Function GenerateSymmetricKey()
{
    $key = New-Object byte[](32)
    $rng = [System.Security.Cryptography.RNGCryptoServiceProvider]::Create()
    $rng.GetBytes($key)
    return [System.Convert]::ToBase64String($key)
}

Write-Host 'Please log into your Azure Stack Hub user environment' -foregroundcolor Green

$tenantARM = "https://management.local.azurestack.external"
$aadTenantName = "FILL THIS IN WITH YOUR AAD TENANT NAME. FOR EXAMPLE: myazurestack.onmicrosoft.com"

# Configure the Azure Stack Hub operator's PowerShell environment.
Add-AzureRMEnvironment `
  -Name "AzureStackUser" `
  -ArmEndpoint $tenantARM

$TenantID = Get-AzsDirectoryTenantId `
  -AADTenantName $aadTenantName `
  -EnvironmentName AzureStackUser

# Sign in to the user portal.
Add-AzureRMAccount `
  -EnvironmentName "AzureStackUser" `
  -TenantId $TenantID `

$now = [System.DateTime]::Now
$oneYearFromNow = $now.AddYears(1)

$applicationPassword = GenerateSymmetricKey

# Create a new Azure AD application.
$identifierUri = [string]::Format("http://localhost:8080/{0}",[Guid]::NewGuid().ToString("N"))
$homePage = "https://contoso.com"

Write-Host "Creating a new AAD Application"
$ADApp = New-AzureRMADApplication `
  -DisplayName $applicationName `
  -HomePage $homePage `
  -IdentifierUris $identifierUri `
  -StartDate $now `
  -EndDate $oneYearFromNow `
  -Password $applicationPassword

Write-Host "Creating a new AAD service principal"
$servicePrincipal = New-AzureRMADServicePrincipal `
  -ApplicationId $ADApp.ApplicationId

# Create a new resource group and a key vault in that resource group.
New-AzureRMResourceGroup `
  -Name $resourceGroupName `
  -Location $location

Write-Host "Creating vault $vaultName"
$vault = New-AzureRMKeyVault -VaultName $vaultName `
  -ResourceGroupName $resourceGroupName `
  -Sku standard `
  -Location $location

# Specify full privileges to the vault for the application.
Write-Host "Setting access policy"
Set-AzureRMKeyVaultAccessPolicy -VaultName $vaultName `
  -ObjectId $servicePrincipal.Id `
  -PermissionsToKeys all `
  -PermissionsToSecrets all

Write-Host "Paste the following settings into the app.config file for the HelloKeyVault project:"
'<add key="VaultUrl" value="' + $vault.VaultUri + '"/>'
'<add key="AuthClientId" value="' + $servicePrincipal.ApplicationId + '"/>'
'<add key="AuthClientSecret" value="' + $applicationPassword + '"/>'
Write-Host
```

---



L’image suivante montre la sortie du script utilisé pour créer le coffre de clés :

![Coffre de clés avec des clés d’accès](media/azure-stack-key-vault-sample-app/settingsoutput.png)

Prenez note des valeurs de **VaultUrl**, **AuthClientId** et **AuthClientSecret** renvoyées par le script précédent. Vous utiliserez ces valeurs pour exécuter l’application **HelloKeyVault**.

## <a name="download-and-configure-the-sample-application"></a>Télécharger et configurer l’exemple d’application

Téléchargez l’exemple de coffre de clés à partir de la page [Azure Key Vault client samples](https://www.microsoft.com/download/details.aspx?id=45343). Extrayez le contenu du fichier .zip sur votre station de travail de développement. Le dossier d’exemples contient deux applications : cet article utilise **HelloKeyVault**.

Pour charger l’exemple **HelloKeyVault** :

1. Accédez au dossier **Microsoft.Azure.KeyVault.Samples** > **samples** > **HelloKeyVault**.
2. Ouvrez l’application **HelloKeyVault** dans Visual Studio.

### <a name="configure-the-sample-application"></a>Configurer l’exemple d’application

Dans Visual Studio :

1. Ouvrez le fichier HelloKeyVault\App.config et accédez à l’élément `<appSettings>`.
2. Mettez à jour les clés **VaultUrl**, **AuthClientId** et **AuthCertThumbprint** avec les valeurs retournées lors de la création du coffre de clés. Par défaut, le fichier App.config a un espace réservé pour `AuthCertThumbprint`. Remplacez cet espace réservé par `AuthClientSecret`.

   ```xml
   <appSettings>
    <!-- Update these settings for your test environment -->
    <add key="VaultUrl" value="URL to your Vault" />
    <add key="AuthClientId" value="Client Id of your Service Principal" />
    <add key="AuthCertThumbprint" value="Thumbprint of the certificate used for authentication" />
    <add key="TracingEnabled" value="false" />
   </appSettings>
   ```

3. Régénérez la solution.

## <a name="run-the-app"></a>Exécuter l’application

Quand vous exécutez **HelloKeyVault**, l’application se connecte à Azure AD, puis utilise le jeton `AuthClientSecret` pour s’authentifier auprès du coffre de clés dans Azure Stack Hub.

Vous pouvez utiliser l’exemple **HelloKeyVault** pour :

* Effectuer des opérations de base comme créer, chiffrer, wrapper et supprimer des clés et des secrets.
* Transmettre des paramètres comme `encrypt` et `decrypt` à **HelloKeyVault**, et appliquer les modifications spécifiées à un coffre de clés.

## <a name="next-steps"></a>Étapes suivantes

* [Déployer une machine virtuelle avec un mot de passe Key Vault](azure-stack-key-vault-deploy-vm-with-secret.md)
* [Déployer une machine virtuelle avec un certificat Key Vault](azure-stack-key-vault-push-secret-into-vm.md)
