---
title: Autoriser des applications à accéder aux secrets d’un Key Vault Azure Stack Hub
description: Découvrez comment exécuter un exemple d’application qui récupère des clés et secrets d’un coffre de clés dans Azure Stack Hub.
author: sethmanheim
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: sethm
ms.lastreviewed: 04/08/2019
ms.openlocfilehash: 11b26d5e079ce16c7450bea0424aa4902c0ff8b3
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76883573"
---
# <a name="allow-apps-to-access-azure-stack-hub-key-vault-secrets"></a>Autoriser des applications à accéder aux secrets d’un Key Vault Azure Stack Hub

Suivez les étapes de cet article pour exécuter l’exemple d’application **HelloKeyVault** qui récupère des clés et secrets d’un coffre de clés dans Azure Stack Hub.

## <a name="prerequisites"></a>Conditions préalables requises

Vous pouvez installer les prérequis suivants à partir du [Kit de développement Azure Stack](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) ou à partir d’un client externe Windows si vous êtes [connecté via un VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn) :

* Installez les [modules Azure PowerShell compatibles avec Azure Stack Hub](../operator/azure-stack-powershell-install.md).
* Téléchargez les [outils nécessaires pour utiliser Azure Stack Hub](../operator/azure-stack-powershell-download.md).

## <a name="create-a-key-vault-and-register-an-app"></a>Créer un coffre de clés et inscrire une application

Pour préparer l’exemple d’application :

* Créez un coffre de clés dans Azure Stack Hub.
* Inscrivez une application dans Azure Active Directory (Azure AD).

Utilisez le portail Azure ou PowerShell pour préparer l’exemple d’application.

> [!NOTE]
> Par défaut, le script PowerShell crée une application dans Active Directory. Cependant, vous pouvez inscrire une de vos applications existantes.

Avant d’exécuter le script suivant, veillez à fournir des valeurs pour les variables `aadTenantName` et `applicationPassword`. Si vous ne spécifiez pas de valeur pour `applicationPassword`, ce script génère un mot de passe aléatoire.

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
Add-AzureRmAccount `
  -EnvironmentName "AzureStackUser" `
  -TenantId $TenantID `

$now = [System.DateTime]::Now
$oneYearFromNow = $now.AddYears(1)

$applicationPassword = GenerateSymmetricKey

# Create a new Azure AD application.
$identifierUri = [string]::Format("http://localhost:8080/{0}",[Guid]::NewGuid().ToString("N"))
$homePage = "https://contoso.com"

Write-Host "Creating a new AAD Application"
$ADApp = New-AzureRmADApplication `
  -DisplayName $applicationName `
  -HomePage $homePage `
  -IdentifierUris $identifierUri `
  -StartDate $now `
  -EndDate $oneYearFromNow `
  -Password $applicationPassword

Write-Host "Creating a new AAD service principal"
$servicePrincipal = New-AzureRmADServicePrincipal `
  -ApplicationId $ADApp.ApplicationId

# Create a new resource group and a key vault in that resource group.
New-AzureRmResourceGroup `
  -Name $resourceGroupName `
  -Location $location

Write-Host "Creating vault $vaultName"
$vault = New-AzureRmKeyVault -VaultName $vaultName `
  -ResourceGroupName $resourceGroupName `
  -Sku standard `
  -Location $location

# Specify full privileges to the vault for the application.
Write-Host "Setting access policy"
Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName `
  -ObjectId $servicePrincipal.Id `
  -PermissionsToKeys all `
  -PermissionsToSecrets all

Write-Host "Paste the following settings into the app.config file for the HelloKeyVault project:"
'<add key="VaultUrl" value="' + $vault.VaultUri + '"/>'
'<add key="AuthClientId" value="' + $servicePrincipal.ApplicationId + '"/>'
'<add key="AuthClientSecret" value="' + $applicationPassword + '"/>'
Write-Host
```

L’image suivante montre la sortie du script utilisé pour créer le coffre de clés :

![Coffre de clés avec des clés d’accès](media/azure-stack-key-vault-sample-app/settingsoutput.png)

Prenez note des valeurs de **VaultUrl**, **AuthClientId** et **AuthClientSecret** renvoyées par le script précédent. Vous utiliserez ces valeurs pour exécuter l’application **HelloKeyVault**.

## <a name="download-and-configure-the-sample-application"></a>Télécharger et configurer l’exemple d’application

Téléchargez l’exemple de coffre de clés à partir de la page [Azure Key Vault client samples](https://www.microsoft.com/download/details.aspx?id=45343). Extrayez le contenu du fichier .zip sur votre station de travail de développement. Il existe deux applications dans le dossier d’exemples. Cet article utilise **HelloKeyVault**.

Pour charger l’exemple **HelloKeyVault** :

1. Accédez au dossier **Microsoft.Azure.KeyVault.Samples** > **samples** > **HelloKeyVault**.
2. Ouvrez l’application **HelloKeyVault** dans Visual Studio.

### <a name="configure-the-sample-application"></a>Configurer l’exemple d’application

Dans Visual Studio :

1. Ouvrez le fichier HelloKeyVault\App.config et accédez à l’élément `<appSettings>`.
2. Mettez à jour les clés **VaultUrl**, **AuthClientId** et **AuthClientSecret** avec les valeurs retournées lors de la création du coffre de clés. Par défaut, le fichier App.config a un espace réservé pour `AuthCertThumbprint`. Remplacez cet espace réservé par `AuthClientSecret`.

   ![Paramètres de l’application](media/azure-stack-key-vault-sample-app/appconfig.png)

3. Régénérez la solution.

## <a name="run-the-app"></a>Exécuter l’application

Quand vous exécutez **HelloKeyVault**, l’application se connecte à Azure AD, puis utilise le jeton `AuthClientSecret` pour s’authentifier auprès du coffre de clés dans Azure Stack Hub.

Vous pouvez utiliser l’exemple **HelloKeyVault** pour :

* Effectuer des opérations de base comme créer, chiffrer, wrapper et supprimer des clés et des secrets.
* Transmettre des paramètres comme `encrypt` et `decrypt` à **HelloKeyVault**, et appliquer les modifications spécifiées à un coffre de clés.

## <a name="next-steps"></a>Étapes suivantes

* [Déployer une machine virtuelle avec un mot de passe Key Vault](azure-stack-key-vault-deploy-vm-with-secret.md)
* [Déployer une machine virtuelle avec un certificat Key Vault](azure-stack-key-vault-push-secret-into-vm.md)
