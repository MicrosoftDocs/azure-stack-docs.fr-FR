---
title: Inscrire le kit ASDK auprès d’Azure
description: Découvrez comment inscrire le kit de développement Azure Stack (ASDK) auprès d’Azure pour activer la syndication de la Place de marché et les rapports d’utilisation.
author: justinha
ms.topic: article
ms.date: 11/14/2020
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 11/14/2020
ms.openlocfilehash: 4fc89c86a2268c4648a7e90fa0f6e90cbc8b260e
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95517240"
---
# <a name="register-the-asdk-with-azure"></a>Inscrire le kit ASDK auprès d’Azure

Vous pouvez inscrire le Kit de développement Azure Stack auprès d’Azure pour télécharger des éléments de la Place de marché à partir d’Azure et renvoyer des rapports de données commerciales à Microsoft. L’inscription est obligatoire pour prendre en charge les fonctionnalités complètes d’Azure Stack, notamment la syndication de la Place de marché. L'inscription est requise pour vous permettre de tester les fonctionnalités importantes d'Azure Stack, comme la syndication de marketplace et les rapports d'utilisation. Après avoir inscrit Azure Stack, l’utilisation est signalée à Azure Commerce. Vous pouvez la consulter sous l’abonnement utilisé pour l’inscription. Toutefois, l’utilisation du kit de développement Azure Stack n’est pas facturée à ses utilisateurs.

Si vous n’inscrivez pas votre kit ASDK, il se peut qu’un message d’avertissement **Activation requise** s’affiche pour vous inviter à l’inscrire. Il s’agit du comportement attendu.

## <a name="prerequisites"></a>Prérequis

Avant de suivre ces instructions pour inscrire le kit ASDK auprès d’Azure, veillez à installer Azure Stack PowerShell et télécharger les outils Azure Stack, comme décrit dans l’article relatif à la [configuration post-déploiement](asdk-post-deploy.md).

Le mode de langage PowerShell doit avoir la valeur **FullLanguage** sur l’ordinateur utilisé pour inscrire le kit ASDK auprès d’Azure. Pour vérifier que le mode de langage actuel est défini sur full, ouvrez une fenêtre PowerShell avec élévation de privilèges et exécutez les commandes PowerShell suivantes :

```powershell  
$ExecutionContext.SessionState.LanguageMode
```

Vérifiez que la sortie retourne **FullLanguage**. Si tout autre mode de langage est retourné, vous devez exécuter l’inscription sur un autre ordinateur ou définir le mode de langage sur **FullLanguage** avant de continuer.

Le compte Azure AD utilisé pour l'inscription doit avoir accès à l'abonnement Azure et disposer des autorisations nécessaires pour créer des applications avec une identité et des principaux de service dans le répertoire associé à cet abonnement. Nous vous recommandons d’inscrire Azure Stack auprès d’Azure en [créant un compte de service à utiliser pour l’inscription](../operator/azure-stack-registration-role.md), au lieu d’utiliser les informations d’identification de l’administrateur général.

## <a name="register-the-asdk"></a>Inscrire l’ASDK

Pour inscrire le kit ASDK auprès d’Azure, procédez aux étapes suivantes.

> [!NOTE]
> Toutes ces étapes doivent être exécutées à partir d’un ordinateur qui a accès au point de terminaison privilégié. Pour le kit ASDK, il s’agit de l’ordinateur hôte ASDK.



### <a name="az-modules"></a>[Modules Az](#tab/az1)

1. Ouvrez une console PowerShell en tant qu’administrateur.  

2. Exécutez les commandes PowerShell suivantes pour inscrire votre installation ASDK auprès d’Azure. Connectez-vous à votre ID d'abonnement de facturation Azure et à l’installation ASDK locale. Si vous n’avez toujours pas d’ID d’abonnement de facturation Azure, vous pouvez créer un [compte Azure gratuit ici](https://azure.microsoft.com/free/?b=17.06). L’inscription d’Azure Stack n’entraîne aucun frais sur votre abonnement Azure.<br><br>Définissez un nom unique pour l'inscription au moment de l'exécution de la cmdlet **Set-AzsRegistration**. Par défaut, le paramètre **RegistrationName** a la valeur **AzureStackRegistration**. Cependant, si vous utilisez le même nom dans plusieurs instances Azure Stack, le script échoue.

    ```powershell  
    # Add the Azure cloud subscription environment name. 
    # Supported environment names are AzureCloud, AzureChinaCloud, or AzureUSGovernment depending which Azure subscription you're using.
    Add-AzAccount -EnvironmentName "<environment name>"
    
    # Register the Azure Stack resource provider in your Azure subscription
    Register-AzResourceProvider -ProviderNamespace Microsoft.AzureStack
    
    # Import the registration module that was downloaded with the GitHub tools
    Import-Module C:\AzureStack-Tools-az\Registration\RegisterWithAzure.psm1
    
    # If you have multiple subscriptions, run the following command to select the one you want to use:
    # Get-AzSubscription -SubscriptionID "<subscription ID>" | Select-AzSubscription
    
    # Register Azure Stack
    $AzureContext = Get-AzContext
    $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
    $RegistrationName = "<unique-registration-name>"
    Set-AzsRegistration `
    -PrivilegedEndpointCredential $CloudAdminCred `
    -PrivilegedEndpoint AzS-ERCS01 `
    -BillingModel Development `
    -RegistrationName $RegistrationName `
    -UsageReportingEnabled:$true
    ```

3. Une fois le script terminé, le message **Votre environnement est à présent enregistré et activé à l’aide des paramètres fournis.** s’affiche.

### <a name="azurerm-modules"></a>[Modules AzureRM](#tab/azurerm1)

1. Ouvrez une console PowerShell en tant qu’administrateur.  

2. Exécutez les commandes PowerShell suivantes pour inscrire votre installation ASDK auprès d’Azure. Connectez-vous à votre ID d'abonnement de facturation Azure et à l’installation ASDK locale. Si vous n’avez toujours pas d’ID d’abonnement de facturation Azure, vous pouvez créer un [compte Azure gratuit ici](https://azure.microsoft.com/free/?b=17.06). L’inscription d’Azure Stack n’entraîne aucun frais sur votre abonnement Azure.<br><br>Définissez un nom unique pour l'inscription au moment de l'exécution de la cmdlet **Set-AzsRegistration**. Par défaut, le paramètre **RegistrationName** a la valeur **AzureStackRegistration**. Cependant, si vous utilisez le même nom dans plusieurs instances Azure Stack, le script échoue.

    ```powershell  
    # Add the AzureRMure cloud subscription environment name. 
    # Supported environment names are AzureRMureCloud, AzureRMureChinaCloud, or AzureRMureUSGovernment depending which AzureRMure subscription you're using.
    Add-AzureRMAccount -EnvironmentName "<environment name>"
    
    # Register the AzureRMure Stack resource provider in your AzureRMure subscription
    Register-AzureRMResourceProvider -ProviderNamespace Microsoft.AzureRMureStack
    
    # Import the registration module that was downloaded with the GitHub tools
    Import-Module C:\zureRMureStack-Tools-AzureRM-master\Registration\RegisterWithAzureRMure.psm1
    
    # If you have multiple subscriptions, run the following command to select the one you want to use:
    # Get-AzureRMSubscription -SubscriptionID "<subscription ID>" | Select-AzureRMSubscription
    
    # Register AzureRMure Stack
    $AzureRMureContext = Get-AzureRMContext
    $CloudAdminCred = Get-Credential -UserName AzureRMURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
    $RegistrationName = "<unique-registration-name>"
    Set-AzureRMsRegistration `
    -PrivilegedEndpointCredential $CloudAdminCred `
    -PrivilegedEndpoint AzS-ERCS01 `
    -BillingModel Development `
    -RegistrationName $RegistrationName `
    -UsageReportingEnabled:$true
    ```

3. Une fois le script terminé, le message **Votre environnement est à présent enregistré et activé à l’aide des paramètres fournis.** s’affiche.

---



![Votre environnement est maintenant inscrit](media/asdk-register/1.PNG)

## <a name="register-in-disconnected-environments"></a>Effectuer une inscription dans des environnements déconnectés

Si vous inscrivez Azure Stack dans un environnement déconnecté (sans connectivité Internet), vous devez obtenir un jeton d’inscription à partir de l’environnement Azure Stack, puis l’utiliser sur un ordinateur qui peut se connecter à Azure pour vous inscrire et créer une ressource d’activation pour votre environnement ASDK.

 > [!IMPORTANT]
 > Avant de suivre ces instructions pour inscrire Azure Stack, vérifiez que vous avez installé PowerShell pour Azure Stack et téléchargé les outils Azure Stack comme décrit dans l’article [Configuration post-déploiement](asdk-post-deploy.md) sur l’ordinateur hôte ASDK et sur l’ordinateur avec un accès Internet utilisé pour se connecter à Azure et effectuer l’inscription.

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Obtenir un jeton d’inscription à partir de l’environnement d’Azure Stack

Sur l’ordinateur hôte ASDK, démarrez PowerShell en tant qu’administrateur et accédez au dossier **Inscription** dans le répertoire **AzureStack-Tools-az** créé quand vous avez téléchargé les outils Azure Stack. Utilisez les commandes PowerShell suivantes pour importer le module **RegisterWithAzure.psm1**, puis utilisez l’applet de commande **Get-AzsRegistrationToken** pour obtenir le jeton d’inscription :  

### <a name="az-modules"></a>[Modules Az](#tab/az2)

   ```powershell  
   # Import the registration module that was downloaded with the GitHub tools
   Import-Module C:\AzureStack-Tools-az\Registration\RegisterWithAzure.psm1

   # Create registration token
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
   # File path to save the token. This example saves the file as C:\RegistrationToken.txt.
   $FilePathForRegistrationToken = "$env:SystemDrive\RegistrationToken.txt"
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $CloudAdminCred `
   -UsageReportingEnabled:$false `
   -PrivilegedEndpoint AzS-ERCS01 `
   -BillingModel Development `
   -MarketplaceSyndicationEnabled:$false `
   -TokenOutputFilePath $FilePathForRegistrationToken
   ```

### <a name="azurerm-modules"></a>[Modules AzureRM](#tab/azurerm2)

  ```powershell  
  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\zureRMureStack-Tools-AzureRM-master\Registration\RegisterWithAzureRMure.psm1

  # Create registration token
  $CloudAdminCred = Get-Credential -UserName AzureRMURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  # File path to save the token. This example saves the file as C:\RegistrationToken.txt.
  $FilePathForRegistrationToken = "$env:SystemDrive\RegistrationToken.txt"
  $RegistrationToken = Get-AzureRMsRegistrationToken -PrivilegedEndpointCredential $CloudAdminCred `
  -UsageReportingEnabled:$false `
  -PrivilegedEndpoint AzureRMS-ERCS01 `
  -BillingModel Development `
  -MarketplaceSyndicationEnabled:$false `
  -TokenOutputFilePath $FilePathForRegistrationToken
  ```

---

Enregistrez ce jeton d’inscription pour l’utiliser sur l’ordinateur connecté à Internet. Vous pouvez copier le fichier ou le texte à partir du fichier créé par le paramètre `$FilePathForRegistrationToken`.

### <a name="connect-to-azure-and-register"></a>Se connecter à Azure et s’inscrire

Sur l’ordinateur connecté à Internet, utilisez les commandes PowerShell suivantes pour importer le module **RegisterWithAzure.psm1**, puis utilisez l’applet de commande **Register-AzsEnvironment** pour vous inscrire auprès d’Azure à l’aide du jeton d’inscription que vous venez de créer et d’un nom d’inscription unique :  

### <a name="az-modules"></a>[Modules Az](#tab/az3)

  ```powershell  
  # Add the Azure cloud subscription environment name. 
  # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
  Add-AzAccount -EnvironmentName "<environment name>"

  # If you have multiple subscriptions, run the following command to select the one you want to use:
  # Get-AzSubscription -SubscriptionID "<subscription ID>" | Select-AzSubscription

  # Register the Azure Stack resource provider in your Azure subscription
  Register-AzResourceProvider -ProviderNamespace Microsoft.AzureStack

  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-az\Registration\RegisterWithAzure.psm1

  # Register with Azure
  # This example uses the C:\RegistrationToken.txt file.
  $registrationToken = Get-Content -Path "$env:SystemDrive\RegistrationToken.txt"
  $RegistrationName = "<unique-registration-name>"
  Register-AzsEnvironment -RegistrationToken $registrationToken `
  -RegistrationName $RegistrationName
  ```


### <a name="azurerm-modules"></a>[Modules AzureRM](#tab/azurerm3)

  ```powershell  
  # Add the AzureRMure cloud subscription environment name. 
  # Supported environment names are AzureRMureCloud, AzureRMureChinaCloud or AzureRMureUSGovernment depending which AzureRMure subscription you are using.
  Add-AzureRMAccount -EnvironmentName "<environment name>"

  # If you have multiple subscriptions, run the following command to select the one you want to use:
  # Get-AzureRMSubscription -SubscriptionID "<subscription ID>" | Select-AzureRMSubscription

  # Register the AzureRMure Stack resource provider in your AzureRMure subscription
  Register-AzureRMResourceProvider -ProviderNamespace Microsoft.AzureRMureStack

  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\zureRMureStack-Tools-AzureRM-master\Registration\RegisterWithAzureRMure.psm1

  # Register with AzureRMure
  # This example uses the C:\RegistrationToken.txt file.
  $registrationToken = Get-Content -Path "$env:SystemDrive\RegistrationToken.txt"
  $RegistrationName = "<unique-registration-name>"
  Register-AzureRMsEnvironment -RegistrationToken $registrationToken `
  -RegistrationName $RegistrationName
  ```

---

Vous pouvez également utiliser l’applet de commande **Get-Content** pour pointer vers un fichier contenant votre jeton d’inscription :

### <a name="az-modules"></a>[Modules Az](#tab/az4)

  ```powershell  
  # Add the Azure cloud subscription environment name. 
  # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
  Add-AzAccount -EnvironmentName "<environment name>"

  # If you have multiple subscriptions, run the following command to select the one you want to use:
  # Get-AzSubscription -SubscriptionID "<subscription ID>" | Select-AzSubscription

  # Register the Azure Stack resource provider in your Azure subscription
  Register-AzResourceProvider -ProviderNamespace Microsoft.AzureStack

  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-az\Registration\RegisterWithAzure.psm1

  # Register with Azure 
  # This example uses the C:\RegistrationToken.txt file.
  $registrationToken = Get-Content -Path "$env:SystemDrive\RegistrationToken.txt"
  Register-AzsEnvironment -RegistrationToken $registrationToken `
  -RegistrationName $RegistrationName
  ```

### <a name="azurerm-modules"></a>[Modules AzureRM](#tab/azurerm4)

  ```powershell  
  # Add the AzureRMure cloud subscription environment name. 
  # Supported environment names are AzureRMureCloud, AzureRMureChinaCloud or AzureRMureUSGovernment depending which AzureRMure subscription you are using.
  Add-AzureRMAccount -EnvironmentName "<environment name>"

  # If you have multiple subscriptions, run the following command to select the one you want to use:
  # Get-AzureRMSubscription -SubscriptionID "<subscription ID>" | Select-AzureRMSubscription

  # Register the AzureRMure Stack resource provider in your AzureRMure subscription
  Register-AzureRMResourceProvider -ProviderNamespace Microsoft.AzureRMureStack

  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\zureRMureStack-Tools-AzureRM-master\Registration\RegisterWithAzureRMure.psm1

  # Register with AzureRMure 
  # This example uses the C:\RegistrationToken.txt file.
  $registrationToken = Get-Content -Path "$env:SystemDrive\RegistrationToken.txt"
  Register-AzureRMsEnvironment -RegistrationToken $registrationToken `
  -RegistrationName $RegistrationName
  ```

---

Une fois l’inscription terminée, un message semblable à celui-ci doit s’afficher : **Votre environnement Azure Stack est désormais inscrit auprès d’Azure.**

> [!IMPORTANT]
> Ne fermez **pas** la fenêtre PowerShell.

Enregistrez le jeton d’inscription et le nom de la ressource d’inscription pour vous y référer ultérieurement.

### <a name="retrieve-an-activation-key-from-the-azure-registration-resource"></a>Récupérer une clé d’activation à partir de la ressource d’inscription Azure

Toujours sur l’ordinateur connecté à Internet, **et dans la même fenêtre de console PowerShell**, récupérez une clé d’activation à partir de la ressource d’inscription créée quand vous vous êtes inscrit auprès d’Azure.

Pour obtenir la clé d’activation, exécutez les commandes PowerShell suivantes. Utilisez la même valeur de nom d’inscription unique que celle fournie lors de l’inscription auprès d’Azure à l’étape précédente :  

### <a name="az-modules"></a>[Modules Az](#tab/az5)

  ```Powershell
  $RegistrationResourceName = "<unique-registration-name>"
  # File path to save the activation key. This example saves the file as C:\ActivationKey.txt.
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName `
  -KeyOutputFilePath $KeyOutputFilePath
  ```



### <a name="azurerm-modules"></a>[Modules AzureRM](#tab/azurerm5)

  ```Powershell
  $RegistrationResourceName = "<unique-registration-name>"
  # File path to save the activation key. This example saves the file as C:\ActivationKey.txt.
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzureRMsActivationKey -RegistrationName $RegistrationResourceName `
  -KeyOutputFilePath $KeyOutputFilePath
  ```

---


### <a name="create-an-activation-resource-in-azure-stack"></a>Créer une ressource d’activation dans Azure Stack

Retournez dans l’environnement Azure Stack avec le fichier ou le texte obtenu de la clé d’activation créée à partir de **Get-AzsActivationKey**. Exécutez les commandes PowerShell suivantes pour créer une ressource d’activation dans Azure Stack à l’aide de cette clé d’activation :   

### <a name="az-modules"></a>[Modules Az](#tab/az6)

  ```Powershell
  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-az\Registration\RegisterWithAzure.psm1
  
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzS-ERCS01 `
  -ActivationKey $ActivationKey
  ```



### <a name="azurerm-modules"></a>[Modules AzureRM](#tab/azurerm6)

  ```Powershell
  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureRMureStack-Tools-Master\Registration\RegisterWithAzureRMure.psm1
  
  $CloudAdminCred = Get-Credential -UserName AzureRMURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  $ActivationKey = "<activation key>"
  New-AzureRMsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzureRMS-ERCS01 `
  -ActivationKey $ActivationKey
  ```

---


Vous pouvez également utiliser l’applet de commande **Get-Content** pour pointer vers un fichier contenant votre jeton d’inscription :

### <a name="az-modules"></a>[Modules Az](#tab/az7)

  ```Powershell
  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-az\Registration\RegisterWithAzure.psm1

  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  # This example uses the C:\ActivationKey.txt file.
  $ActivationKey = Get-Content -Path "$env:SystemDrive\Activationkey.txt"
  New-AzsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzS-ERCS01 `
  -ActivationKey $ActivationKey
  ```



### <a name="azurerm-modules"></a>[Modules AzureRM](#tab/azurerm7)

  ```Powershell
  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\zureRMureStack-Tools-AzureRM-master\Registration\RegisterWithAzureRMure.psm1

  $CloudAdminCred = Get-Credential -UserName AzureRMURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  # This example uses the C:\ActivationKey.txt file.
  $ActivationKey = Get-Content -Path "$env:SystemDrive\Activationkey.txt"
  New-AzureRMsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzureRMS-ERCS01 `
  -ActivationKey $ActivationKey
  ```

---


Une fois l’activation terminée, un message similaire à celui-ci doit s’afficher : **Votre environnement a terminé le processus d’inscription et d’activation.**

## <a name="verify-the-registration-was-successful"></a>Vérifier que l’inscription a abouti

Vous pouvez utiliser la vignette **Gestion des régions** pour vérifier que l’inscription Azure Stack a réussi. Cette vignette est disponible sur le tableau de bord par défaut du portail d’administration.

1. Connectez-vous au portail d'administration Azure Stack `https://adminportal.local.azurestack.external`.

2. Dans le tableau de bord, sélectionnez **Gestion des régions**.

    [![Vignette Gestion des régions dans le portail administrateur Azure Stack](media/asdk-register/admin1sm.png "Vignette Gestion des régions")](media/asdk-register/admin1.png#lightbox)

3. Sélectionner **Propriétés**. Ce panneau affiche l’état et les détails de votre environnement. L’état peut être **Inscrit** ou **Non inscrit**. S’il est inscrit, il montre également l’ID d’abonnement Azure que vous avez utilisé pour inscrire votre instance Azure Stack ainsi que le groupe de ressources et le nom de l’inscription.

## <a name="move-a-registration-resource"></a>Déplacer une ressource d’inscription
Le déplacement d’une ressource d’inscription entre des groupes de ressources sous le même abonnement **est** pris en charge. Pour plus d’informations sur le déplacement de ressources vers un nouveau groupe de ressources, voir [Déplacer des ressources vers un nouveau groupe de ressource ou un nouvel abonnement](/azure/azure-resource-manager/resource-group-move-resources).


## <a name="next-steps"></a>Étapes suivantes

- [Ajouter un élément de la Place de marché Azure Stack Hub](../operator/azure-stack-marketplace.md)
