---
title: Inscrire Azure Stack Hub auprès d’Azure – Renforcé
description: Découvrez comment inscrire Azure Stack Hub auprès d’Azure afin de pouvoir télécharger des éléments de la Place de marché Azure et configurer les rapports de données.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 12/20/2019
ms.openlocfilehash: 3d24ba3a714c8ea8f17189a7c63a134dab0f8d12
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97909801"
---
# <a name="register-azure-stack-hub-with-azure---azure-stack-hub-ruggedized"></a>Inscrire Azure Stack Hub auprès d’Azure - Azure Stack Hub renforcé

Pour configurer la syndication de la Place de marché et utiliser les services PaaS, vous devez inscrire et activer votre solution MDC (Modular Data Center) ou Azure Stack Hub renforcé, basée sur Azure Stack Hub, une fois le déploiement effectué. Avec la syndication de Place de marché, un administrateur renseigne la Place de marché Azure Stack Hub local avec les images téléchargées depuis la Place de marché Azure.

Une inscription est requise pour les systèmes qui se connecteront au cloud Azure, ainsi que pour les systèmes qui seront déconnectés.

## <a name="overview"></a>Vue d’ensemble

Le processus d’inscription est constitué des étapes suivantes :

1. Exporter un jeton d’inscription à partir d’Azure Stack Hub.
2. Inscrire Azure Stack Hub auprès d'Azure.
3. Exporter une clé d’activation à partir d’Azure.
4. Active Azure Stack Hub.

Si le système est connecté au cloud Azure, les quatre étapes peuvent être exécutées à l’aide d’un seul script. Si le système est déconnecté, l’administrateur doit effectuer les étapes individuellement.

> [!NOTE]
> Pour MDC, chacun système Azure Stack Hub doit être inscrit séparément. Par conséquent, les instructions suivantes doivent être exécutées à trois reprises.

## <a name="prerequisites"></a>Prérequis

Avant de vous inscrire, veillez à respecter les prérequis suivants :

- Vérifiez vos informations d’identification.
- Définissez le mode de langage PowerShell.
- Installez PowerShell pour Azure Stack Hub.
- Installer le module des outils Azure Stack Hub
- Déterminez votre scénario d’inscription.

### <a name="verify-your-credentials"></a>Vérifier vos informations d’identification

Avant d’inscrire Azure Stack Hub auprès d’Azure, vous devez disposer des éléments suivants :

- L’ID d’abonnement d’un abonnement Azure.  

    > [!NOTE]  
    > Les abonnements Azure sont associés aux environnements cloud Azure (Azure Commercial, Azure Government, etc.) Cela détermine le cloud auquel vous vous connectez pour accéder au contenu de la Place de marché.

- Le nom d’utilisateur et le mot de passe d’un compte propriétaire de l’abonnement. 
- Le compte d’utilisateur doit avoir accès à l’abonnement Azure, et avoir les autorisations nécessaires pour créer des applications d’identité et des principaux de service dans le répertoire associé à cet abonnement. Nous vous recommandons d’inscrire Azure Stack Hub auprès d’Azure avec une administration selon le principe des privilèges minimum. Pour plus d’informations sur la façon de créer une définition de rôle personnalisé qui limite l’accès à votre abonnement dans le cadre d’une inscription, consultez [Créer un rôle d’inscription pour Azure Stack Hub](../../operator/azure-stack-registration-role.md).
- Inscrivez le fournisseur de ressources Azure Stack Hub (pour plus d’informations, consultez les sections suivantes).

Après l’inscription, l’autorisation d’administrateur général Azure Active Directory (Azure AD) n’est pas nécessaire. Toutefois, certaines opérations peuvent nécessiter les informations d’identification d’administrateur général (par exemple, un script d’installation d’un fournisseur de ressources ou une nouvelle fonctionnalité peut avoir besoin d’une autorisation spécifique). Vous pouvez temporairement réactiver les autorisations d’administrateur général du compte ou utiliser un compte d’administrateur général distinct qui est propriétaire de *l’abonnement de fournisseur par défaut*.

L’utilisateur qui inscrit Azure Stack Hub est le propriétaire du principal de service dans Azure AD. Seul l’utilisateur ayant inscrit Azure Stack Hub peut en modifier l’inscription. Si un utilisateur qui n’est ni administrateur ni propriétaire du principal de service d’inscription tente d’inscrire ou de réinscrire Azure Stack Hub, une réponse 403 peut s’afficher. Une réponse 403 indique que l’utilisateur ne dispose pas des autorisations suffisantes pour effectuer l’opération.

L’inscription d’Azure Stack Hub n’entraîne aucun frais sur votre abonnement Azure.

### <a name="powershell-language-mode"></a>Mode de langage PowerShell

Pour inscrire correctement Azure Stack Hub, le mode de langage PowerShell doit être défini sur **FullLanguageMode**. Pour vérifier que le mode de langage actuel est défini sur full, ouvrez une fenêtre PowerShell avec élévation de privilèges et exécutez les applets de commande PowerShell suivantes :

```powershell  
$ExecutionContext.SessionState.LanguageMode
```

Vérifiez que la sortie retourne **FullLanguageMode**. Si tout autre mode de langage est retourné, vous devez effectuer l’inscription sur une autre machine ou définir le mode de langage sur **FullLanguageMode** avant de continuer.

### <a name="install-powershell-for-azure-stack-hub"></a>Installer PowerShell pour Azure Stack Hub

Utilisez la dernière version de PowerShell pour Azure Stack Hub pour effectuer l’inscription auprès d’Azure.

Si la dernière version n’est pas déjà installée, consultez [Installer PowerShell pour Azure Stack Hub](../../operator/azure-stack-powershell-install.md).

### <a name="install-the-azure-stack-hub-tools-module"></a>Installer le module des outils Azure Stack Hub

Le [modules d’outils Azure Stack Hub](https://www.powershellgallery.com/packages/azs.tools.admin/0.1.0) contient des modules PowerShell qui prennent en charge les fonctionnalités Azure Stack Hub, notamment les fonctionnalités d’inscription. Lors du processus d’inscription, vous devez importer et utiliser le sous-module **RegisterWithAzure.psm1** pour inscrire votre instance Azure Stack Hub auprès d’Azure.

Pour installer l’outil Azure Stack Hub le plus récent :

1. Ouvrez une invite PowerShell avec élévation de privilèges.
2. Exécutez l’applet de commande suivante :

   ```powershell  
   Install-Module -Name Azs.Tools.Admin
   ```

### <a name="determine-your-registration-scenario"></a>Déterminer votre scénario d’inscription

Votre déploiement Azure Stack Hub peut être *connecté* ou *déconnecté*.

- **Connecté** : Connecté signifie que vous avez déployé Azure Stack Hub afin qu’il puisse se connecter à Internet et à Azure. Vous pouvez disposer d’Azure AD ou d’Active Directory Federation Services (AD FS) pour votre magasin d’identités.

- **Déconnecté** : Avec l’option de déploiement déconnecté d’Azure, vous pouvez déployer et utiliser Azure Stack Hub sans connexion à Internet. Dans la mesure où les systèmes déconnectés ne peuvent pas rapporter l’utilisation des services PaaS à Azure, ils doivent être inscrits en tant que modèle de facturation **Capacité** pour permettre l’utilisation des services PaaS.

### <a name="determine-a-unique-registration-name-to-use"></a>Déterminer le nom d’inscription unique à utiliser

Quand vous inscrivez Azure Stack Hub sur Azure, vous devez fournir un nom d’inscription unique. Un moyen simple d’associer votre abonnement Azure Stack Hub avec une inscription Azure est d’utiliser votre **ID cloud** Azure Stack Hub.

Pour déterminer l’ID de cloud associé à votre déploiement Azure Stack Hub, ouvrez PowerShell en tant qu’administrateur sur un ordinateur qui a accès au point de terminaison privilégié, exécutez les commandes suivantes, puis notez la valeur de **CloudID** :

```PowerShell
Enter-PSSession -ComputerName <privileged endpoint computer name> -ConfigurationName PrivilegedEndpoint
Get-AzureStackStampInformation
```

## <a name="registration-and-activation-for-systems-connected-to-the-azure-cloud"></a>Inscription et activation des systèmes connectés au cloud Azure

Pour inscrire un système Azure Stack Hub doté d’une connectivité à Azure, suivez la procédure ci-dessous.

> [!NOTE]  
> Toutes ces étapes doivent être exécutées à partir d’un ordinateur qui a accès au point de terminaison privilégié (PEP). Pour plus de détails sur le PEP, consultez [Utilisation du point de terminaison privilégié dans Azure Stack Hub](../../operator/azure-stack-privileged-endpoint.md).

Les environnements connectés peuvent accéder à Internet et à Azure. Pour ces environnements, inscrivez le fournisseur de ressources Azure Stack Hub auprès d’Azure, puis configurer votre modèle de facturation.

1. Pour inscrire le fournisseur de ressources Azure Stack Hub auprès d’Azure, démarrez PowerShell ISE en tant qu’administrateur et utilisez les cmdlets PowerShell suivantes avec le paramètre **EnvironmentName** défini sur le type d’abonnement Azure qui convient (voir les paramètres ci-dessous).

2. Dans la même session PowerShell, vérifiez que vous êtes connecté au contexte Azure PowerShell approprié. Ce contexte correspond au compte Azure utilisé précédemment pour inscrire le fournisseur de ressources Azure Stack Hub :

   ```powershell
   Connect-AzureRmAccount -Environment "<environment name>"
   ```

   Pour **AzureUSSec**, vous devez d’abord initialiser l’environnement `CustomCloud`, puis appeler **Connect-AzureRmAccount** :

   ```powershell
   Initialize-AzureRmEnvironment -Name 'CustomCloud' -CloudManifestFilePath $CloudManifestFilePath
   Connect-AzureRmAccount -Environment 'CustomCloud'
   ```

   | Paramètre | Description |  
   |-----|-----|
   | EnvironmentName | Le nom de l’environnement d’abonnement cloud Azure. Les noms d’environnements pris en charge sont **AzureCloud**, **AzureUSGovernment** ou **AzureUSSec**.   |

   > [!NOTE]
   > Si votre session expire, si votre mot de passe a changé ou si vous souhaitez changer de compte, exécutez la cmdlet suivante avant de vous connecter en utilisant **Add-AzureRmAccount** : **Processus Remove-AzureRmAccount-Scope**.

3. Si vous avez plusieurs abonnements, exécutez la commande suivante pour sélectionner celui que vous souhaitez utiliser :

   ```powershell
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Exécutez la commande suivante pour inscrire le fournisseur de ressources Azure Stack Hub auprès de votre abonnement Azure :

   ```powershell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

   Vous pouvez également inscrire le fournisseur de ressources Azure Stack Hub dans votre abonnement Azure à l’aide du portail Azure. Connectez-vous au portail Azure à l’aide du compte associé à l’abonnement Azure. Accédez à **Catégories** > **Général** > **Abonnements**, puis sélectionnez l’ID d’abonnement auprès duquel vous devez inscrire le fournisseur de ressources Azure Stack Hub. Dans le volet gauche, accédez à **Paramètres** > **Fournisseurs de ressources**. Sélectionnez le fournisseur de ressources **Microsoft.AzureStack**, puis **Inscrire**.

   ![Inscrire le fournisseur de ressources Azure Stack Hub](./media/registration-tzl/register-azure-resource-provider-portal.png)

5. Dans la même session PowerShell, exécutez la cmdlet **Set-AzsRegistration** :

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $msAssetTag = "Enter the value printed on the product"
   $RegistrationName = "<unique-registration-name>"

   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel Ruggedized `
      -RegistrationName $RegistrationName `
      -msAssetTag $msAssetTagName `
      -UsageReportingEnabled: $false
   ```

   L’étiquette d’inventaire MS (`msAssetTag`) est obligatoire pour l’inscription du modèle de facturation Renforcé et est imprimée sur le produit.

   Le processus prend entre 10 et 15 minutes. Une fois la commande exécutée, un message s’affiche. **Votre environnement est à présent enregistré et activé à l’aide des paramètres fournis.** s’affiche.

## <a name="registration-and-activation-for-systems-not-connected-to-the-azure-cloud"></a>Inscription et activation des systèmes non connectés au cloud Azure 

Si vous inscrivez Azure Stack Hub dans un environnement déconnecté (sans connectivité Internet), procédez comme suit :

1. Obtenez un jeton d’inscription à partir de l’environnement d’Azure Stack Hub.

2. Connectez-vous à Azure et inscrivez-vous à l’aide du jeton d’inscription de l’étape 1. Utilisez un ordinateur capable de se connecter à Azure et sur lequel PowerShell pour Azure Stack Hub est installé.

3. Récupérer une clé d’activation à partir de l’inscription Azure.

4. Activez Azure Stack Hub à l’aide de la clé d’activation d’Azure.

### <a name="get-a-registration-token-from-the-azure-stack-hub-environment"></a>Obtenir un jeton d’inscription à partir de l’environnement d’Azure Stack Hub

Obtenez un jeton d’inscription à partir de l’environnement d’Azure Stack Hub. Utilisez ensuite ce jeton sur un ordinateur capable de se connecter à Azure et sur lequel PowerShell pour Azure Stack Hub et les outils Azure Stack Hub sont installés.

1. Pour obtenir le jeton d’inscription, exécutez les cmdlets PowerShell suivantes à partir d’une invite avec élévation de privilèges :

   ```PowerShell
    $FilePathForRegistrationToken = "$env:SystemDrive\RegistrationToken.txt" 
    
    $RegistrationToken = Get-AzsRegistrationToken `
      -PrivilegedEndpointCredential $YourCloudAdminCredential `
      -UsageReportingEnabled:$False `
      -PrivilegedEndpoint $YourPrivilegedEndpoint `
      -BillingModel Capacity -AgreementNumber '<EA agreement number>' -msAssetTag '<MS Asset tag>' `
      -TokenOutputFilePath $FilePathForRegistrationToken 
   ```

   > [!TIP]  
   > Le jeton d’inscription est enregistré dans le fichier spécifié par `$FilePathForRegistrationToken`. Vous pouvez modifier le chemin d’accès ou le nom du fichier à votre guise.

2. Enregistrez ce jeton d’inscription pour l’utiliser sur la machine connectée à Azure. Vous pouvez copier le fichier ou le texte à partir de `$FilePathForRegistrationToken`.

### <a name="connect-to-azure-and-register"></a>Se connecter à Azure et s’inscrire

Sur l’ordinateur connecté à Internet, connectez-vous au contexte Azure PowerShell qui convient. Appelez ensuite **Register-AzsEnvironment**. Spécifiez le jeton d’inscription pour vous inscrire auprès d’Azure. Si vous inscrivez plusieurs instances Azure Stack Hub en utilisant le même ID d’abonnement Azure, spécifiez un nom d’inscription unique.

Vous avez besoin de votre jeton d’inscription et d’un nom de jeton unique.

1. À partir d’une invite PowerShell avec élévation de privilèges, exécutez les cmdlets suivantes pour vérifier que vous utilisez l’abonnement qui convient.

    ```powershell  
    Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
    ```

2. Exécutez ensuite les applets de commande PowerShell suivantes :

    ```powershell  
    $RegistrationToken = "<Your Registration Token>"
    $RegistrationName = "<unique-registration-name>"
    Register-AzsEnvironment -RegistrationToken $RegistrationToken -RegistrationName $RegistrationName
    ```

3. Vous pouvez également utiliser la cmdlet **Get-Content** pour pointer vers un fichier contenant votre jeton d’inscription.

   Vous avez besoin de votre jeton d’inscription et d’un nom de jeton unique.

   1. Démarrez PowerShell ISE en tant qu’administrateur et accédez au dossier **Registration** du répertoire **AzureStack-Tools-master** créé lorsque vous avez téléchargé les outils Azure Stack Hub. Importez le module **RegisterWithAzure.psm1** :

      ```powershell  
      Import-Module .\RegisterWithAzure.psm1
      ```

   2. Exécutez ensuite les applets de commande PowerShell suivantes :

      ```powershell  
      $RegistrationToken = Get-Content -Path '<Path>\<Registration Token File>'
      Register-AzsEnvironment -RegistrationToken $RegistrationToken -RegistrationName $RegistrationName
      ```

> [!NOTE]  
> Enregistrez le nom de la ressource d’inscription et le jeton d’inscription pour une référence ultérieure.

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>Récupérer une clé d’activation à partir de la ressource d’inscription Azure

Récupérez ensuite une clé d’activation auprès de la ressource d’inscription créée dans Azure pendant **Register-AzsEnvironment**.

Pour obtenir la clé d’activation, exécutez les applets de commande PowerShell suivantes :

```PowerShell
$RegistrationResourceName = "<unique-registration-name>"
$KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
$ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath
```

> [!TIP]  
> La clé d’activation est enregistrée dans le fichier spécifié par `$KeyOutputFilePath`. Vous pouvez modifier le chemin d’accès ou le nom du fichier à votre guise.

### <a name="create-an-activation-resource-in-azure-stack-hub"></a>Créer une ressource d’activation dans Azure Stack Hub

Retournez dans l’environnement Azure Stack Hub avec le fichier ou le texte obtenu de la clé d’activation créée par **Get-AzsActivationKey**. Créez ensuite une ressource d’activation dans Azure Stack Hub à l’aide de cette clé d’activation. Pour créer une ressource d’activation, exécutez les applets de commande PowerShell suivantes :

```PowerShell
$ActivationKey = "<activation key>"
New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
```

Vous pouvez également utiliser la cmdlet **Get-Content** pour pointer vers un fichier contenant votre jeton d’inscription.

```PowerShell
$ActivationKey = Get-Content -Path '<Path>\<Activation Key File>'
New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
```

## <a name="verify-azure-stack-hub-registration"></a>Vérifier l’inscription Azure Stack Hub

Vous pouvez utiliser la vignette **Gestion des régions** pour vérifier que l’inscription Azure Stack Hub a réussi. Cette vignette est disponible sur le tableau de bord par défaut du portail d’administration. L’état peut être inscrit ou non. S’il est inscrit, il montre également l’ID d’abonnement Azure que vous avez utilisé pour inscrire votre instance Azure Stack Hub ainsi que le groupe de ressources et le nom de l’inscription.

1. Connectez-vous au portail d’administration Azure Stack Hub. L’URL varie en fonction de la région et du nom de domaine externe de votre opérateur. Son format est `https://adminportal.<region>.<FQDN>`.

2. Dans le tableau de bord, sélectionnez **Gestion des régions**.

3. Sélectionner **Propriétés**. Ce panneau affiche l’état et les détails de votre environnement. L’état peut être **Inscrit**, **Non inscrit** ou **Expiré**.

   [![Vignette Gestion des régions dans le portail administrateur Azure Stack Hub](media/registration-tzl/admin1sm.png "Vignette Gestion des régions")](media/registration-tzl/admin1.png#lightbox)

   Si l’état est Inscrit, les propriétés sont les suivantes :

    - **ID d’abonnement de l’inscription** : ID d’abonnement Azure inscrit et associé à Azure Stack Hub.
    - **Groupe de ressources de l’inscription** : Groupe de ressources Azure dans l’abonnement associé contenant les ressources Azure Stack Hub.

4. Vous pouvez utiliser le portail Azure pour voir les ressources d'inscription Azure Stack Hub puis vérifier que l’inscription a réussi. Connectez-vous au [portail Azure](https://portal.azure.com/) avec un compte associé à l’abonnement que vous avez utilisé pour inscrire Azure Stack Hub. Cochez **Toutes les ressources**, activez la case **Afficher les types masqués**, puis sélectionnez le nom d’inscription.

5. Si l'inscription a échoué, vous devez vous réinscrire en suivant [Modifier l’abonnement que vous utilisez](https://docs.microsoft.com/azure-stack/operator/azure-stack-registration#change-the-subscription-you-use) pour résoudre le problème.

Vous pouvez également vérifier si votre inscription a réussi à l’aide de la fonctionnalité Gestion de la Place de marché. Si vous voyez une liste d’éléments de la Place de marché dans le panneau **Gestion de la Place de marché**, votre inscription a réussi. Toutefois, dans des environnements déconnectés, les éléments de la Place de marché n’apparaissent pas dans Gestion de la Place de marché.

> [!NOTE]
> Une fois l’inscription terminée, l’avertissement relatif à la non-inscription n’apparaît plus.

## <a name="next-steps"></a>Étapes suivantes

[Principes de bases de l’administration d’Azure Stack Hub](../../operator/azure-stack-manage-basics.md)  
