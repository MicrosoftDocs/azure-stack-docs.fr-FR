---
title: Se connecter à l’ASDK
description: Découvrez comment se connecter au Kit de développement Azure Stack (ASDK).
author: justinha
ms.topic: article
ms.date: 05/06/2019
ms.author: justinha
ms.reviewer: knithinc
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: df4b6c770ef1dd93cddbeb748c0845f391305173
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76874023"
---
# <a name="connect-to-the-asdk"></a>Se connecter à l’ASDK

Pour gérer des ressources, vous devez d’abord vous connecter au Kit de développement Azure Stack (ADSK). Dans cet article, nous décrivons les étapes à suivre pour se connecter à ce Kit de développement en utilisant les options de connexion suivantes :

* [Connexion Bureau à distance (RDP)](#connect-with-rdp) : lorsque vous vous connectez à l’aide de la connexion Bureau à distance, un seul utilisateur peut se connecter rapidement au Kit de développement Azure Stack (ASDK).
* [Réseau privé virtuel (VPN)](#connect-with-vpn) : Lorsque vous vous connectez à l’aide d’un VPN, plusieurs utilisateurs peuvent se connecter simultanément aux portails Azure Stack à partir de clients situés en dehors de l’infrastructure Azure Stack. Une connexion VPN nécessite une configuration.

<a name="connect-with-rdp"></a>
## <a name="connect-to-azure-stack-using-rdp"></a>Se connecter à Azure Stack avec RDP

Un seul utilisateur simultané peut gérer les ressources dans le portail d’administration Azure Stack ou le portail de l’utilisateur par le biais d’une connexion Bureau à distance, directement à partir de l’ordinateur hôte ASDK.

> [!TIP]
> Cette option vous permet également de réutiliser RDP en étant connecté à l’ordinateur hôte ASDK pour vous connecter aux machines virtuelles créées sur l’ordinateur hôte ASDK.

1. Ouvrez Connexion Bureau à distance (mstc.exe) et connectez-vous à l’adresse IP de l’ordinateur hôte ASDK. Veillez à utiliser un compte autorisé à se connecter à distance à l’ordinateur hôte ASDK. Par défaut, **AzureStack\AzureStackAdmin** dispose des autorisations de connexion à distance à l’ordinateur hôte ASDK.  

2. Sur l’ordinateur hôte ASDK, ouvrez le gestionnaire de serveur (ServerManager.exe). Sélectionnez **Serveur local**, désactiver **Configuration de sécurité renforcée d’Internet Explorer**, puis fermez le Gestionnaire de serveur.

3. Connectez-vous au portail d’administration en tant que **AzureStack\CloudAdmin** ou utilisez d’autres informations d’identification de l’opérateur d’Azure Stack. L’adresse du portail d’administration d’ASDK est [https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external).

4. Connectez-vous au portail utilisateur en tant que **AzureStack\CloudAdmin** ou utilisez d’autres informations d’identification de l’utilisateur d’Azure Stack. L’adresse du portail utilisateur d’ASDK est [https://portal.local.azurestack.external](https://portal.local.azurestack.external).

> [!NOTE]
> Pour plus d’informations sur quand utiliser quel compte, consultez [Informations de base sur l’administration du kit de développement Azure Stack](asdk-admin-basics.md#what-account-should-i-use).

<a name="connect-with-vpn"></a>
## <a name="connect-to-azure-stack-using-vpn"></a>Se connecter à Azure Stack avec un VPN

Vous pouvez établir une connexion VPN par tunnel fractionné à un ordinateur hôte ASDK pour accéder aux portails Azure Stack et aux outils installés localement tels que Visual Studio et PowerShell. Grâce aux connexions VPN, plusieurs utilisateurs peuvent se connecter en même temps aux ressources Azure Stack hébergées par l’ASDK.

La connectivité VPN est prise en charge dans les déploiements Azure AD et Active Directory Federation Services (AD FS).

> [!NOTE]
> Une connexion VPN ne fournit *pas* de connectivité aux machines virtuelles Azure Stack. Vous ne pourrez pas établir de connexion RDP aux machines virtuelles Azure Stack en étant connecté par le biais d’un VPN.

### <a name="prerequisites"></a>Conditions préalables requises
Avant de configurer une connexion VPN à l’ASDK, vérifiez que les conditions préalables suivantes sont réunies :

- Installez [Azure PowerShell pour Azure Stack](asdk-post-deploy.md#install-azure-stack-powershell) sur votre ordinateur local.  
- Téléchargez les [outils nécessaires pour utiliser Azure Stack](asdk-post-deploy.md#download-the-azure-stack-tools).

### <a name="set-up-vpn-connectivity"></a>Configurer une connectivité VPN

Pour créer une connexion VPN à l’ASDK, ouvrez Windows PowerShell en tant qu’administrateur sur votre ordinateur local Windows. Ensuite, exécutez le script suivant (mise à jour des valeurs de l’adresse IP et du mot de passe pour votre environnement) :

```powershell
# Change directories to the default Azure Stack tools directory
cd C:\AzureStack-Tools-master

# Configure Windows Remote Management (WinRM), if it's not already configured.
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module.
Import-Module .\Connect\AzureStack.Connect.psm1

# Add the ASDK host computer's IP address as the ASDK certificate authority (CA) to the list of trusted hosts. Make sure you update the IP address and password values for your environment.

$hostIP = "<Azure Stack host IP address>"

$Password = ConvertTo-SecureString `
  "<operator's password provided when deploying Azure Stack>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user.
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

Si la configuration réussit, **Azure Stack** apparaît dans votre liste des connexions VPN :

![Connexions réseau](media/asdk-connect/vpn.png)  

### <a name="connect-to-azure-stack"></a>Se connecter à Azure Stack

  Connectez-vous à l’instance Azure Stack à l’aide d’une des méthodes suivantes :  

  * Utiliser la commande `Connect-AzsVpn` :
      
    ```powershell
    Connect-AzsVpn `
      -Password $Password
    ```

  * Sur votre ordinateur local, sélectionnez **Paramètres réseau** > **VPN** > **Azure Stack** > **Se connecter**. À l’invite de connexion, entrez le nom d’utilisateur (**AzureStack\AzureStackAdmin**) et votre mot de passe.

La première fois que vous vous connectez, vous êtes invité à installer le certificat racine Azure Stack à partir de **AzureStackCertificateAuthority** dans le magasin de certificats de votre ordinateur local. Cette étape ajoute l’autorité de certification ASDK à la liste des hôtes approuvés. Cliquez sur **Oui** pour installer le certificat.

![Certificat racine](media/asdk-connect/cert.png)  
  
  > [!IMPORTANT]
  > L’invite peut être masquée par la fenêtre PowerShell ou d’autres applications.

### <a name="test-vpn-connectivity"></a>Connectivité VPN de test

Pour tester la connexion au portail, ouvrez un navigateur, puis accédez au portail utilisateur (https://portal.local.azurestack.external/) ou au portail administrateur (https://adminportal.local.azurestack.external/).

Connectez-vous avec les informations d’identification de l’abonnement approprié pour créer et gérer des ressources.  

## <a name="next-steps"></a>Étapes suivantes

[Dépannage](asdk-troubleshooting.md)
