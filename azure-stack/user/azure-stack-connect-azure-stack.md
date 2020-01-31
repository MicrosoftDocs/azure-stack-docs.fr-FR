---
title: Se connecter à Azure Stack Hub
description: Découvrez comment vous connecter à Azure Stack Hub.
author: mattbriggs
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 388c01ce8a4c5c6834ab88f8fe28013c1208e728
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76883905"
---
# <a name="connect-to-azure-stack-hub"></a>Se connecter à Azure Stack Hub

Pour pouvoir gérer des ressources, vous devez vous connecter au Kit de développement Azure Stack. Cet article explique en détail les étapes à suivre pour vous connecter à ce kit de développement. Vous avez le choix entre ces deux options de connexion :

* Bureau à distance : permet à un seul utilisateur à la fois de se connecter rapidement à partir du kit de développement.
* Réseau privé virtuel (VPN) : permet à plusieurs utilisateurs de se connecter simultanément à partir de clients extérieurs à l’infrastructure Azure Stack Hub (nécessite une configuration particulière).

## <a name="connect-to-azure-stack-hub-with-remote-desktop"></a>Se connecter à Azure Stack Hub avec l’option Bureau à distance
Avec une connexion Bureau à distance, un seul utilisateur à la fois peut se connecter au portail pour gérer des ressources.

1. Ouvrez une connexion Bureau à distance et connectez-vous au kit de développement. Entrez le nom d’utilisateur **AzureStack\AzureStackAdmin**, ainsi que le mot de passe d’administration que vous avez fourni au moment de l’installation d’Azure Stack Hub.  

2. À partir de l’ordinateur du kit de développement, ouvrez le Gestionnaire de serveur, cliquez sur **Serveur local**, désactivez la sécurité renforcée d’Internet Explorer, puis fermez le Gestionnaire de serveur.

3. Pour ouvrir le portail, accédez à (https://portal.local.azurestack.external/) et connectez-vous à l’aide des informations d’identification utilisateur.


## <a name="connect-to-azure-stack-hub-with-vpn"></a>Se connecter à Azure Stack Hub avec l'option VPN

Vous pouvez établir une connexion VPN avec tunneling fractionné à un Kit de développement Azure Stack. Par le biais de la connexion VPN, vous pouvez accéder au portail administrateur, au portail utilisateur et à des outils installés localement, tels que Visual Studio et PowerShell, pour gérer des ressources Azure Stack Hub. La connectivité VPN est prise en charge dans les déploiements Azure Active Directory (Azure AD) et Active Directory Federation Services (AD FS). Les connexions VPN permettent à plusieurs clients de se connecter à Azure Stack Hub en même temps. 

> [!NOTE] 
> Cette connexion VPN ne fournit pas de connectivité aux machines virtuelles de l’infrastructure Azure Stack Hub. 

### <a name="prerequisites"></a>Conditions préalables requises

* Installez [Azure PowerShell pour Azure Stack Hub](../operator/azure-stack-powershell-install.md) sur votre ordinateur local.  
* Téléchargez les [outils nécessaires pour utiliser Azure Stack Hub](../operator/azure-stack-powershell-download.md). 

### <a name="configure-vpn-connectivity"></a>Configurer la connectivité VPN

Pour créer une connexion VPN au kit de développement, ouvrez une session PowerShell avec des privilèges élevés à partir de votre ordinateur Windows local, puis exécutez le script suivant (après avoir changé l’adresse IP et le mot de passe de manière appropriée pour votre environnement) :

```powershell 
# Configure winrm if it's not already configured
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module
Import-Module .\Connect\AzureStack.Connect.psm1 

# Add the development kit computer's host IP address & certificate authority (CA) to the list of trusted hosts. Make sure to update the IP address and password values for your environment. 

$hostIP = "<Azure Stack Hub host IP address>"

$Password = ConvertTo-SecureString `
  "<Administrator password provided when deploying Azure Stack Hub>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

Si la configuration réussit, `azurestack` apparaît dans votre liste des connexions VPN.

![Connexions réseau](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack-hub"></a>Se connecter à Azure Stack Hub

Connectez-vous à l’instance Azure Stack Hub à l’aide d’une des deux méthodes suivantes :  

* Utilisez la commande `Connect-AzsVpn` : 
    
  ```powershell
  Connect-AzsVpn `
    -Password $Password
  ```

  Lorsque vous y êtes invité, approuvez l’hôte Azure Stack Hub et installez le certificat fourni par **AzureStackCertificateAuthority** dans le magasin de certificats de votre ordinateur local. L’invite s’affiche parfois derrière la fenêtre de session PowerShell. 

* Sur votre ordinateur local, accédez à **Paramètres réseau** > **VPN** > sélectionnez `azurestack` > **Se connecter**. À l’invite de connexion, entrez le nom d’utilisateur (AzureStack\AzureStackAdmin) et le mot de passe.

### <a name="test-the-vpn-connectivity"></a>Tester la connectivité VPN

Pour tester la connexion au portail, ouvrez un navigateur et accédez au portail de l’utilisateur (https://portal.local.azurestack.external/), connectez-vous et créez des ressources.  

## <a name="next-steps"></a>Étapes suivantes



