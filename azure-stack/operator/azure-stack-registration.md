---
title: Inscrire Azure Stack Hub auprès d’Azure
titleSuffix: Azure Stack Hub
description: Découvrez comment inscrire les systèmes intégrés Azure Stack Hub auprès d’Azure afin de pouvoir télécharger des éléments de la Place de marché Azure et configurer les rapports de données.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 11/19/2020
ms.author: inhenkel
ms.reviewer: avishwan
ms.lastreviewed: 11/19/2020
ms.custom: contperf-fy20q4
zone_pivot_groups: state-connected-disconnected
ms.openlocfilehash: 28c809f70c479c4037061bd7c1504c0954a3584c
ms.sourcegitcommit: e13f27291bab236aac5d8b05401056961e9cc1e9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/19/2020
ms.locfileid: "97697652"
---
# <a name="register-azure-stack-hub-with-azure"></a>Inscrire Azure Stack Hub auprès d’Azure

Inscrivez Azure Stack Hub auprès d’Azure afin de pouvoir télécharger des éléments de la Place de marché Azure et configurer la génération de rapports de données commerciales destinés à Microsoft. Après avoir enregistré Azure Stack Hub, l’utilisation est signalée au commerce Azure et vous pouvez le voir dans l’ID d’abonnement de facturation Azure utilisé pour l’inscription.

Les informations contenues dans cet article décrivent l’inscription de systèmes intégrés Azure Stack Hub auprès d’Azure. Pour plus d’informations sur l’inscription du kit ASDK auprès d’Azure, consultez [Inscription d’Azure Stack Hub](../asdk/asdk-register.md) dans la documentation ASDK.

> [!IMPORTANT]  
> L’inscription est obligatoire pour prendre en charge les fonctionnalités complètes d’Azure Stack Hub, notamment pour proposer les éléments sur la Place de marché. Vous enfreignez les conditions du contrat de licence d’Azure Stack Hub si vous ne vous inscrivez pas lorsque vous utilisez le modèle de facturation à l’utilisation. Pour en savoir plus sur les modèles de licence d’Azure Stack Hub, consultez la [page Comment acheter](https://azure.microsoft.com/overview/azure-stack/how-to-buy/).

## <a name="prerequisites"></a>Prérequis

Avant de vous inscrire, veillez à respecter les prérequis suivants :

- Vérifiez vos informations d’identification.
- Définissez le mode de langage PowerShell.
- Installez PowerShell pour Azure Stack Hub.
- Téléchargez les outils Azure Stack Hub.
- Déterminez votre modèle de facturation.
- Déterminez votre nom d’inscription unique.

### <a name="verify-your-credentials"></a>Vérifier vos informations d’identification

Avant d’inscrire Azure Stack Hub auprès d’Azure, vous devez disposer des éléments suivants :

::: zone pivot="state-connected"
- L’ID d’abonnement d’un abonnement Azure. L’inscription est prise en charge uniquement pour les abonnements aux services partagés EA, CSP et CSP. Les CSP ont le choix d’[utiliser un abonnement CSP ou APSS](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription).<br><br>Pour obtenir l’ID, connectez-vous à Azure et cliquez sur **Tous les services**. Ensuite, sous la catégorie **GÉNÉRAL**, sélectionnez **Abonnements** et cliquez sur l’abonnement que vous voulez utiliser. Sous **Éléments principaux**, vous trouverez alors l’ID d’abonnement. Au titre de bonne pratique, utilisez des abonnements distincts pour les environnements de production et pour les environnements de développement ou de test. 
::: zone-end
::: zone pivot="state-disconnected"
- L’ID d’abonnement d’un abonnement Azure. Seuls les abonnements EA sont pris en charge pour l’inscription. 

    Pour obtenir l’ID, connectez-vous à Azure et cliquez sur **Tous les services**. Ensuite, sous la catégorie **GÉNÉRAL**, sélectionnez **Abonnements** et cliquez sur l’abonnement que vous voulez utiliser. Sous **Éléments principaux**, vous trouverez alors l’ID d’abonnement. Au titre de bonne pratique, utilisez des abonnements distincts pour les environnements de production et pour les environnements de développement ou de test. 
::: zone-end
   > [!Note]  
   > Les abonnements au cloud en Allemagne ne sont actuellement pas pris en charge.  

- Le nom d’utilisateur et le mot de passe d’un compte propriétaire de l’abonnement.

- Le compte d’utilisateur doit avoir accès à l’abonnement Azure, et avoir les autorisations nécessaires pour créer des applications d’identité et des principaux de service dans le répertoire associé à cet abonnement. Nous vous recommandons d’inscrire Azure Stack Hub auprès d’Azure avec une administration selon le principe des privilèges minimum. Pour plus d’informations sur la façon de créer une définition de rôle personnalisé qui limite l’accès à votre abonnement dans le cadre d’une inscription, consultez [Créer un rôle d’inscription pour Azure Stack Hub](azure-stack-registration-role.md).

- Le fournisseur de ressources Azure Stack Hub inscrit (pour plus d’informations, consultez la section Inscrire le fournisseur de ressources Azure Stack Hub ci-dessous).

Après l’inscription, l’autorisation d’administrateur général Azure Active Directory (Azure AD) n’est pas nécessaire. Toutefois, certaines opérations peuvent nécessiter les informations d’identification d’administrateur général (par exemple, un script d’installation d’un fournisseur de ressources ou une nouvelle fonctionnalité peut avoir besoin d’une autorisation spécifique). Vous pouvez temporairement réactiver les autorisations d’administrateur général du compte ou utiliser un compte d’administrateur général distinct qui est propriétaire de *l’abonnement de fournisseur par défaut*.

L’utilisateur qui inscrit Azure Stack Hub est le propriétaire du principal de service dans Azure AD. Seul l’utilisateur ayant inscrit Azure Stack Hub peut en modifier l’inscription. Si un utilisateur qui n’est ni administrateur ni propriétaire du principal de service d’inscription tente d’inscrire ou de réinscrire Azure Stack Hub, il peut recevoir une réponse 403. Une réponse 403 indique que l’utilisateur n’a pas les autorisations suffisantes pour effectuer l’opération.

Si vous n’avez pas d’abonnement Azure répondant à ces exigences, vous pouvez [créer un compte Azure gratuit ici](https://azure.microsoft.com/free/?b=17.06). L’inscription d’Azure Stack Hub n’entraîne aucun frais sur votre abonnement Azure.

> [!NOTE]
> Si vous avez plusieurs instances Azure Stack Hub, la bonne pratique est d’inscrire chaque instance Azure Stack Hub séparément dans son abonnement. Cela vous facilite le suivi de l’utilisation.

### <a name="set-the-powershell-language-mode"></a>Définir le mode de langage PowerShell

Pour inscrire correctement Azure Stack Hub, le mode de langage PowerShell doit être défini sur **FullLanguageMode**.  Pour vérifier que le mode de langage actuel est défini sur full, ouvrez une fenêtre PowerShell avec élévation de privilèges et exécutez les applets de commande PowerShell suivantes :

```powershell  
$ExecutionContext.SessionState.LanguageMode
```

Vérifiez que la sortie retourne **FullLanguageMode**. Si tout autre mode de langage est retourné, vous devez effectuer l’inscription sur une autre machine ou bien définir le mode de langage sur **FullLanguageMode** avant de continuer.

### <a name="install-powershell-for-azure-stack-hub"></a>Installer PowerShell pour Azure Stack Hub

Utilisez la dernière version de PowerShell pour Azure Stack Hub pour effectuer l’inscription auprès d’Azure.

Si la dernière version n’est pas déjà installée, consultez [Installer PowerShell pour Azure Stack Hub](powershell-install-az-module.md).

### <a name="download-the-azure-stack-hub-tools"></a>Télécharger les outils Azure Stack Hub

Le dépôt GitHub des outils Azure Stack Hub contient des modules PowerShell qui prennent en charge les fonctionnalités Azure Stack Hub, notamment les fonctionnalités d’inscription. Pendant le processus d’inscription, vous devez importer et utiliser le module PowerShell **RegisterWithAzure.psm1** (disponible dans le dépôt des outils Azure Stack Hub) pour inscrire votre instance Azure Stack Hub auprès d’Azure.

Pour garantir que vous utilisez la version la plus récente, supprimez toutes les versions existantes des outils Azure Stack Hub et [téléchargez la dernière version à partir de GitHub](azure-stack-powershell-download.md) avant d’effectuer l’inscription auprès d’Azure.

[!INCLUDE [Azure Stack Hub Operator Access Workstation](../includes/operator-note-owa.md)]

### <a name="determine-your-billing-model"></a>Déterminer votre modèle de facturation
::: zone pivot="state-connected"
 Un déploiement connecté permet à Azure Stack Hub de se connecter à Internet et à Azure. Vous pouvez également utiliser Azure AD ou les services ADFS (Active Directory Federation Services) comme magasin d’identités et choisir parmi deux modèles de facturation : paiement à l’utilisation ou selon la capacité. Vous allez spécifier le modèle de facturation ultérieurement, lors de l’exécution du script d’inscription.
::: zone-end

::: zone pivot="state-disconnected"
 Un déploiement déconnecté vous permet d’utiliser Azure Stack Hub sans connexion à Internet. Avec un déploiement déconnecté, vous êtes limité à un magasin d’identités ADFS et au modèle de facturation selon la capacité. Vous allez spécifier le modèle de facturation ultérieurement, lors de l’exécution du script d’inscription.
::: zone-end

### <a name="determine-your-unique-registration-name"></a>Déterminer votre nom d’inscription unique

Quand vous exécutez le script d’inscription, vous devez fournir un nom d’inscription unique. Un moyen simple d’associer votre abonnement Azure Stack Hub avec une inscription Azure est d’utiliser votre **ID cloud** Azure Stack Hub.

> [!NOTE]
> Pour les inscriptions Azure Stack Hub basées sur le modèle de facturation par capacité, vous devez changer le nom unique lors de la réinscription après l’expiration des abonnements annuels, sauf si vous [supprimez l’inscription expirée](#renew-or-change-registration) et que vous vous réinscrivez à Azure.

Pour déterminer l’ID de cloud de votre déploiement d’Azure Stack Hub, consultez [Rechercher votre ID Cloud](azure-stack-find-cloud-id.md).

::: zone pivot="state-connected"
## <a name="register-with-pay-as-you-use-billing"></a>Inscrire avec facturation de paiement à l’utilisation

Utilisez ces étapes pour inscrire Azure Stack Hub auprès d’Azure à l’aide du modèle de facturation de paiement à l’utilisation.

> [!Note]  
> Toutes ces étapes doivent être exécutées à partir d’un ordinateur qui a accès au point de terminaison privilégié (PEP). Pour plus de détails sur le PEP, consultez [Utilisation du point de terminaison privilégié dans Azure Stack Hub](azure-stack-privileged-endpoint.md).

Les environnements connectés peuvent accéder à Internet et à Azure. Pour ces environnements, vous devez inscrire le fournisseur de ressources Azure Stack Hub auprès d’Azure, puis configurer votre modèle de facturation.

### <a name="az-modules"></a>[Modules Az](#tab/az1)

1. Pour inscrire le fournisseur de ressources Azure Stack Hub auprès d’Azure, démarrez PowerShell ISE en tant qu’administrateur et utilisez les cmdlets PowerShell suivantes avec le paramètre **EnvironmentName** défini sur le type d’abonnement Azure approprié (voir les paramètres ci-dessous).

2. Ajoutez le compte Azure que vous avez utilisé pour inscrire Azure Stack Hub. Pour ajouter le compte, exécutez la cmdlet **Add-AzAccount**. Vous êtes invité à entrer vos informations d’identification de compte Azure et vous devrez peut-être utiliser l’authentification à deux facteurs en fonction de la configuration de votre compte.

   ```powershell
   Add-AzAccount -EnvironmentName "<environment name>"
   ```

   | Paramètre | Description |  
   |-----|-----|
   | EnvironmentName | Le nom de l’environnement d’abonnement cloud Azure. Les noms d’environnement pris en charge sont **AzureCloud**, **AzureUSGovernment**, ou, si vous utilisez un abonnement Azure en Chine, **AzureChinaCloud**.  |

   >[!Note]
   > Si votre session expire, si votre mot de passe a changé ou si vous souhaitez simplement changer de compte, exécutez la cmdlet suivante avant de vous connecter en utilisant Add-AzAccount : `Remove-AzAccount-Scope Process`

3. Si vous avez plusieurs abonnements, exécutez la commande suivante pour sélectionner celui que vous souhaitez utiliser :  

   ```powershell  
   Get-AzSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzSubscription
   ```

4. Exécutez la commande suivante pour inscrire le fournisseur de ressources Azure Stack Hub dans votre abonnement Azure :

   ```powershell  
   Register-AzResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Démarrez PowerShell ISE en tant qu’administrateur et accédez au dossier **Registration** du répertoire **AzureStack-Tools-az** créé lorsque vous avez téléchargé les outils Azure Stack Hub. Importez le module **RegisterWithAzure.psm1** à l’aide de PowerShell :

   ```powershell  
   Import-Module .\RegisterWithAzure.psm1
   ```

6. Ensuite, dans la même session PowerShell, vérifiez que vous êtes connecté au contexte Azure PowerShell approprié. Ce contexte correspond au compte Azure qui a été utilisé précédemment pour inscrire le fournisseur de ressources Azure Stack Hub. PowerShell pour :

   ```powershell  
   Connect-AzAccount -Environment "<environment name>"
   ```

   | Paramètre | Description |  
   |-----|-----|
   | EnvironmentName | Le nom de l’environnement d’abonnement cloud Azure. Les noms d’environnement pris en charge sont **AzureCloud**, **AzureUSGovernment**, ou, si vous utilisez un abonnement Azure en Chine, **AzureChinaCloud**.  |

7. Dans la même session PowerShell, exécutez l’applet de commande **Set-AzsRegistration**. PowerShell pour :  

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel PayAsYouUse `
      -RegistrationName $RegistrationName
   ```
   Pour plus d’informations sur l’applet de commande Set-AzsRegistration, voir [Référence de l’inscription](#registration-reference).

### <a name="azurerm-modules"></a>[Modules AzureRM](#tab/azurerm1)

1. Pour inscrire le fournisseur de ressources Azure Stack Hub auprès d’Azure, démarrez PowerShell ISE en tant qu’administrateur et utilisez les cmdlets PowerShell suivantes avec le paramètre **EnvironmentName** défini sur le type d’abonnement Azure approprié (voir les paramètres ci-dessous).

2. Ajoutez le compte Azure que vous avez utilisé pour inscrire Azure Stack Hub. Pour ajouter le compte, exécutez l’applet de commande **Add-AzureRMAccount**. Vous êtes invité à entrer vos informations d’identification de compte Azure et vous devrez peut-être utiliser l’authentification à deux facteurs en fonction de la configuration de votre compte.

   ```powershell
   Add-AzureRMAccount -EnvironmentName "<environment name>"
   ```

   | Paramètre | Description |  
   |-----|-----|
   | EnvironmentName | Le nom de l’environnement d’abonnement cloud Azure. Les noms d’environnement pris en charge sont **AzureCloud**, **AzureUSGovernment**, ou, si vous utilisez un abonnement Azure en Chine, **AzureChinaCloud**.  |

   >[!Note]
   > Si votre session expire, si votre mot de passe a changé ou si vous souhaitez simplement changer de compte, exécutez l’applet de commande suivante avant de vous connecter en utilisant Add-AzureRMAccount : `Remove-AzureRMAccount-Scope Process`

3. Si vous avez plusieurs abonnements, exécutez la commande suivante pour sélectionner celui que vous souhaitez utiliser :  

   ```powershell  
   Get-AzureRMSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRMSubscription
   ```

4. Exécutez la commande suivante pour inscrire le fournisseur de ressources Azure Stack Hub dans votre abonnement Azure :

   ```powershell  
   Register-AzureRMResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Démarrez PowerShell ISE en tant qu’administrateur et accédez au dossier **Registration** du répertoire **AzureStack-Tools-az** créé lorsque vous avez téléchargé les outils Azure Stack Hub. Importez le module **RegisterWithAzure.psm1** à l’aide de PowerShell :

   ```powershell  
   Import-Module .\RegisterWithAzure.psm1
   ```

6. Ensuite, dans la même session PowerShell, vérifiez que vous êtes connecté au contexte Azure PowerShell approprié. Ce contexte correspond au compte Azure qui a été utilisé précédemment pour inscrire le fournisseur de ressources Azure Stack Hub. PowerShell pour :

   ```powershell  
   Connect-AzureRMAccount -Environment "<environment name>"
   ```

   | Paramètre | Description |  
   |-----|-----|
   | EnvironmentName | Le nom de l’environnement d’abonnement cloud Azure. Les noms d’environnement pris en charge sont **AzureCloud**, **AzureUSGovernment**, ou, si vous utilisez un abonnement Azure en Chine, **AzureChinaCloud**.  |

7. Dans la même session PowerShell, exécutez l’applet de commande **Set-AzsRegistration**. PowerShell pour :  

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel PayAsYouUse `
      -RegistrationName $RegistrationName
   ```
   Pour plus d’informations sur l’applet de commande Set-AzsRegistration, voir [Référence de l’inscription](#registration-reference).

---

   Le processus prend entre 10 et 15 minutes. Quand la commande est terminée, le message **« Votre environnement est à présent inscrit et activé avec les paramètres fournis. »** s’affiche.

## <a name="register-with-capacity-billing"></a>Inscrire avec facturation selon la capacité

Utilisez ces étapes pour inscrire Azure Stack Hub auprès d’Azure à l’aide du modèle de facturation selon la capacité.

> [!Note]  
> Toutes ces étapes doivent être exécutées à partir d’un ordinateur qui a accès au point de terminaison privilégié (PEP). Pour plus de détails sur le PEP, consultez [Utilisation du point de terminaison privilégié dans Azure Stack Hub](azure-stack-privileged-endpoint.md).

Les environnements connectés peuvent accéder à Internet et à Azure. Pour ces environnements, vous devez inscrire le fournisseur de ressources Azure Stack Hub auprès d’Azure, puis configurer votre modèle de facturation.

### <a name="az-modules"></a>[Modules Az](#tab/az2)

1. Pour inscrire le fournisseur de ressources Azure Stack Hub auprès d’Azure, démarrez PowerShell ISE en tant qu’administrateur et utilisez les cmdlets PowerShell suivantes avec le paramètre **EnvironmentName** défini sur le type d’abonnement Azure approprié (voir les paramètres ci-dessous).

2. Ajoutez le compte Azure que vous avez utilisé pour inscrire Azure Stack Hub. Pour ajouter le compte, exécutez la cmdlet **Add-AzAccount**. Vous êtes invité à entrer vos informations d’identification de compte Azure et vous devrez peut-être utiliser l’authentification à deux facteurs en fonction de la configuration de votre compte.

   ```powershell  
   Connect-AzAccount -Environment "<environment name>"
   ```

   | Paramètre | Description |  
   |-----|-----|
   | EnvironmentName | Le nom de l’environnement d’abonnement cloud Azure. Les noms d’environnement pris en charge sont **AzureCloud**, **AzureUSGovernment**, ou, si vous utilisez un abonnement Azure en Chine, **AzureChinaCloud**.  |

3. Si vous avez plusieurs abonnements, exécutez la commande suivante pour sélectionner celui que vous souhaitez utiliser :  

   ```powershell  
   Get-AzSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzSubscription
   ```

4. Exécutez la commande suivante pour inscrire le fournisseur de ressources Azure Stack Hub dans votre abonnement Azure :

   ```powershell  
   Register-AzResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Démarrez PowerShell ISE en tant qu’administrateur et accédez au dossier **Registration** du répertoire **AzureStack-Tools-az** créé lorsque vous avez téléchargé les outils Azure Stack Hub. Importez le module **RegisterWithAzure.psm1** à l’aide de PowerShell :

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -AgreementNumber <EA agreement number> `
      -BillingModel Capacity `
      -RegistrationName $RegistrationName
   ```
   > [!Note]  
   > Vous pouvez désactiver la création de rapports avec le paramètre UsageReportingEnabled pour l’applet de commande **Set-AzsRegistration** en définissant le paramètre avec la valeur false. 
   
   Pour plus d’informations sur l’applet de commande Set-AzsRegistration, voir [Référence de l’inscription](#registration-reference).

### <a name="azurerm-modules"></a>[Modules AzureRM](#tab/azurerm2)

1. Pour inscrire le fournisseur de ressources Azure Stack Hub auprès d’Azure, démarrez PowerShell ISE en tant qu’administrateur et utilisez les cmdlets PowerShell suivantes avec le paramètre **EnvironmentName** défini sur le type d’abonnement Azure approprié (voir les paramètres ci-dessous).

2. Ajoutez le compte Azure que vous avez utilisé pour inscrire Azure Stack Hub. Pour ajouter le compte, exécutez l’applet de commande **Add-AzureRMAccount**. Vous êtes invité à entrer vos informations d’identification de compte Azure et vous devrez peut-être utiliser l’authentification à deux facteurs en fonction de la configuration de votre compte.

   ```powershell  
   Connect-AzureRMAccount -Environment "<environment name>"
   ```

   | Paramètre | Description |  
   |-----|-----|
   | EnvironmentName | Le nom de l’environnement d’abonnement cloud Azure. Les noms d’environnement pris en charge sont **AzureCloud**, **AzureUSGovernment**, ou, si vous utilisez un abonnement Azure en Chine, **AzureChinaCloud**.  |

3. Si vous avez plusieurs abonnements, exécutez la commande suivante pour sélectionner celui que vous souhaitez utiliser :  

   ```powershell  
   Get-AzureRMSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRMSubscription
   ```

4. Exécutez la commande suivante pour inscrire le fournisseur de ressources Azure Stack Hub dans votre abonnement Azure :

   ```powershell  
   Register-AzureRMResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Démarrez PowerShell ISE en tant qu’administrateur et accédez au dossier **Registration** du répertoire **AzureStack-Tools-master** créé lorsque vous avez téléchargé les outils Azure Stack Hub. Importez le module **RegisterWithAzure.psm1** à l’aide de PowerShell :

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -AgreementNumber <EA agreement number> `
      -BillingModel Capacity `
      -RegistrationName $RegistrationName
   ```
   > [!Note]  
   > Vous pouvez désactiver la création de rapports avec le paramètre UsageReportingEnabled pour l’applet de commande **Set-AzsRegistration** en définissant le paramètre avec la valeur false. 
   
   Pour plus d’informations sur l’applet de commande Set-AzsRegistration, voir [Référence de l’inscription](#registration-reference).

---

::: zone-end

::: zone pivot="state-disconnected"
## <a name="register-with-capacity-billing"></a>Inscrire avec facturation selon la capacité

Si vous inscrivez Azure Stack Hub dans un environnement déconnecté (sans connectivité Internet), vous devez obtenir un jeton d’inscription à partir de l’environnement Azure Stack Hub. Ensuite, utilisez ce jeton sur un ordinateur capable de se connecter à Azure et sur lequel PowerShell pour Azure Stack Hub est installé.  

### <a name="get-a-registration-token-from-the-azure-stack-hub-environment"></a>Obtenir un jeton d’inscription à partir de l’environnement d’Azure Stack Hub

1. Démarrez PowerShell ISE en tant qu’administrateur et accédez au dossier **Registration** du répertoire **AzureStack-Tools-az** créé lorsque vous avez téléchargé les outils Azure Stack Hub. Importez le module **RegisterWithAzure.psm1** :  

   ```powershell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. Pour obtenir le jeton d’inscription, exécutez les applets de commande PowerShell suivantes :  

   ```Powershell
   $FilePathForRegistrationToken = "$env:SystemDrive\RegistrationToken.txt"
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $YourCloudAdminCredential -UsageReportingEnabled:$False -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```
   Pour plus d’informations sur l’applet de commande Get-AzsRegistrationToken, voir [Référence de l’inscription](#registration-reference).

   > [!Tip]  
   > Le jeton d’inscription est enregistré dans le fichier spécifié pour *$FilePathForRegistrationToken*. Vous pouvez modifier à votre convenance le nom ou le chemin d’accès au fichier.

3. Enregistrez ce jeton d’inscription pour l’utiliser sur la machine connectée à Azure. Vous pouvez copier le fichier ou le texte à partir de *$FilePathForRegistrationToken*.

### <a name="connect-to-azure-and-register"></a>Se connecter à Azure et s’inscrire

Sur l’ordinateur qui est connecté à Internet, effectuez les mêmes étapes pour importer le module RegisterWithAzure.psm1 et vous connecter au bon contexte Azure PowerShell. Appelez ensuite Register-AzsEnvironment. Spécifiez le jeton d’inscription pour vous inscrire auprès d’Azure. Si vous inscrivez plusieurs instances Azure Stack Hub en utilisant le même ID d’abonnement Azure, spécifiez un nom d’inscription unique.

Vous avez besoin de votre jeton d’inscription et d’un nom de jeton unique.

1. Démarrez PowerShell ISE en tant qu’administrateur et accédez au dossier **Registration** du répertoire **AzureStack-Tools-az** créé lorsque vous avez téléchargé les outils Azure Stack Hub. Importez le module **RegisterWithAzure.psm1** :  

   ```powershell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. Exécutez ensuite les applets de commande PowerShell suivantes :  

    ```powershell  
    $RegistrationToken = "<Your Registration Token>"
    $RegistrationName = "<unique-registration-name>"
    Register-AzsEnvironment -RegistrationToken $RegistrationToken -RegistrationName $RegistrationName
    ```

Si vous le souhaitez, utilisez l’applet de commande Get-Content pour pointer vers un fichier contenant votre jeton d’inscription.

Vous avez besoin de votre jeton d’inscription et d’un nom de jeton unique.

1. Démarrez PowerShell ISE en tant qu’administrateur et accédez au dossier **Registration** du répertoire **AzureStack-Tools-az** créé lorsque vous avez téléchargé les outils Azure Stack Hub. Importez le module **RegisterWithAzure.psm1** :  

    ```powershell  
    Import-Module .\RegisterWithAzure.psm1
    ```

2. Exécutez ensuite les applets de commande PowerShell suivantes :  

    ```powershell  
    $RegistrationToken = Get-Content -Path '<Path>\<Registration Token File>'
    Register-AzsEnvironment -RegistrationToken $RegistrationToken -RegistrationName $RegistrationName
    ```

  > [!Note]  
  > Enregistrez le nom de la ressource d’inscription et le jeton d’inscription pour une référence ultérieure.

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>Récupérer une clé d’activation auprès d’Azure Registration Resource

Vous devez ensuite extraire une clé d’activation auprès de la ressource d’inscription créée dans Azure pendant Register-AzsEnvironment.

Pour obtenir la clé d’activation, exécutez les applets de commande PowerShell suivantes :  

  ```Powershell
  $RegistrationResourceName = "<unique-registration-name>"
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath
  ```

  > [!Tip]  
  > La clé d’activation est enregistrée dans le fichier spécifié pour *$KeyOutputFilePath*. Vous pouvez modifier à votre convenance le nom ou le chemin d’accès au fichier.

### <a name="create-an-activation-resource-in-azure-stack-hub"></a>Créer une ressource d’activation dans Azure Stack Hub

Retournez dans l’environnement Azure Stack Hub avec le fichier ou le texte obtenu de la clé d’activation créée à partir de Get-AzsActivationKey. Ensuite, créez une ressource d’activation dans Azure Stack Hub en utilisant cette clé d’activation. Pour créer une ressource d’activation, exécutez les applets de commande PowerShell suivantes :

  ```Powershell
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

Si vous le souhaitez, vous pouvez utiliser l’applet de commande Get-Content pour pointer vers un fichier contenant votre jeton d’inscription :

  ```Powershell
  $ActivationKey = Get-Content -Path '<Path>\<Activation Key File>'
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```
::: zone-end

## <a name="verify-azure-stack-hub-registration"></a>Vérifier l’inscription Azure Stack Hub

Vous pouvez utiliser la vignette **Gestion des régions** pour vérifier que l’inscription Azure Stack Hub a réussi. Cette vignette est disponible sur le tableau de bord par défaut du portail d’administration. L’état peut être inscrit ou non. S’il est inscrit, il montre également l’ID d’abonnement Azure que vous avez utilisé pour inscrire votre instance Azure Stack Hub ainsi que le groupe de ressources et le nom de l’inscription.

1. Connectez-vous au portail administrateur Azure Stack Hub `https://adminportal.local.azurestack.external`.

2. Dans le tableau de bord, sélectionnez **Gestion des régions**.

3. Sélectionner **Propriétés**. Ce panneau affiche l’état et les détails de votre environnement. L’état peut être **Inscrit**, **Non inscrit** ou **Expiré**.

    [![Vignette Gestion des régions dans le portail administrateur Azure Stack Hub](media/azure-stack-registration/admin1sm.png "Vignette Gestion des régions")](media/azure-stack-registration/admin1.png#lightbox)

    Si l’état est Inscrit, les propriétés sont les suivantes :
    
    - **ID d’abonnement de l’inscription** : ID d’abonnement Azure inscrit et associé à Azure Stack Hub.
    - **Groupe de ressources de l’inscription** : Groupe de ressources Azure dans l’abonnement associé contenant les ressources Azure Stack Hub.

4. Vous pouvez utiliser le portail Azure pour voir les ressources d'inscription Azure Stack Hub puis vérifier que l’inscription a réussi. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte associé à l’abonnement que vous avez utilisé pour inscrire Azure Stack Hub. Cochez **Toutes les ressources**, activez la case **Afficher les types masqués**, puis sélectionnez le nom d’inscription.

5. Si l’inscription a échoué, vous devez vous réinscrire en suivant [ces étapes](#change-the-subscription-you-use) pour résoudre le problème.  

Vous pouvez également vérifier si votre inscription a réussi à l’aide de la fonctionnalité Gestion de la Place de marché. Si vous voyez une liste d’éléments de la Place de marché dans le panneau Gestion de la Place de marché, votre inscription a réussi. Toutefois, dans des environnements déconnectés, les éléments de la Place de marché n’apparaissent pas dans Gestion de la Place de marché.

> [!NOTE]
> Une fois l’inscription terminée, l’avertissement relatif à la non-inscription n’apparaît plus. Avec les versions Azure Stack Hub antérieures à 1904, dans les scénarios déconnectés, vous recevez un message dans Gestion de la Place de marché vous invitant à inscrire et à activer votre instance Azure Stack Hub, même si l’inscription a déjà été effectuée. Ce message n’apparaît pas dans les versions 1904 et ultérieures.

## <a name="renew-or-change-registration"></a>Renouveler ou modifier l’inscription

::: zone pivot="state-connected"
Vous devez mettre à jour votre inscription dans les cas suivants :

- Après le renouvellement de votre abonnement annuel basé sur la capacité.
- Lorsque vous changez de modèle de facturation.
- Lorsque vous mettez à l’échelle des modifications (ajout/suppression de nœuds) pour la facturation basée sur la capacité.

### <a name="prerequisites"></a>Prérequis

Vous avez besoin d’obtenir les informations suivantes à partir du [portail administrateur](#verify-azure-stack-hub-registration) pour renouveler ou modifier une inscription :

| Portail administrateur | Paramètre d’applet de commande | Notes | 
|-----|-----|-----|
| ID D’ABONNEMENT DE L’INSCRIPTION | Abonnement | ID d’abonnement utilisé lors de l’inscription précédente |
| GROUPE DE RESSOURCES DE L’INSCRIPTION | ResourceGroupName | Groupe de ressources sous lequel la ressource de l’inscription précédente existe |
| NOM DE L’INSCRIPTION | RegistrationName | Nom de l’inscription utilisé lors de l’inscription précédente |

### <a name="change-the-subscription-you-use"></a>Modifier l’abonnement que vous utilisez

Pour modifier l’abonnement que vous utilisez, vous devez d’abord exécuter l’applet de commande **Remove-AzsRegistration**, puis vous assurer d’être connecté au contexte Azure PowerShell correct. Ensuite, exécutez **Set-AzsRegistration** avec les paramètres modifiés, y compris `<billing model>`. Lors de l’exécution de **Remove-AzsRegistration**, vous devez être connecté à l’abonnement utilisé pendant l’inscription et vous servir des valeurs des paramètres `RegistrationName` et `ResourceGroupName`, comme indiqué dans le [portail administrateur](#verify-azure-stack-hub-registration) :

### <a name="az-modules"></a>[Modules Az](#tab/az3)

```powershell  
# select the subscription used during the registration (shown in portal)
Select-AzSubscription -Subscription '<Registration subscription ID from portal>'
# unregister using the parameter values from portal
Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -RegistrationName '<Registration name from portal>' -ResourceGroupName '<Registration resource group from portal>'
# switch to new subscription id
Select-AzSubscription -Subscription '<New subscription ID>'
# register 
Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel '<Billing model>' -RegistrationName '<Registration name>' -ResourceGroupName '<Registration resource group name>'
```

### <a name="azurerm-modules"></a>[Modules AzureRM](#tab/azurerm3)

```powershell  
# select the subscription used during the registration (shown in portal)
Select-AzureRMSubscription -Subscription '<Registration subscription ID from portal>'
# unregister using the parameter values from portal
Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -RegistrationName '<Registration name from portal>' -ResourceGroupName '<Registration resource group from portal>'
# switch to new subscription id
Select-AzureRMSubscription -Subscription '<New subscription ID>'
# register 
Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel '<Billing model>' -RegistrationName '<Registration name>' -ResourceGroupName '<Registration resource group name>'
```

---

### <a name="change-billing-model-how-features-are-offered-or-re-register-your-instance"></a>Modifier le modèle de facturation, la manière dont les fonctionnalités sont proposées ou réinscrire votre instance

Cette section s’applique si vous voulez modifier le modèle de facturation, la manière dont les fonctionnalités sont proposées ou si vous voulez réinscrire votre instance. Pour tous ces cas, vous appelez la fonction d’inscription pour définir les nouvelles valeurs. Vous n’avez pas besoin de commencer par supprimer l’inscription actuelle. Connectez-vous à l’ID d’abonnement affiché dans le [portail administrateur](#verify-azure-stack-hub-registration), puis réexécutez l’inscription avec une nouvelle valeur `BillingModel` tout en conservant les valeurs des paramètres `RegistrationName` et `ResourceGroupName` comme indiqué dans le [portail administrateur](#verify-azure-stack-hub-registration) :

### <a name="az-modules"></a>[Modules Az](#tab/az4)

```powershell  
# select the subscription used during the registration
Select-AzSubscription -Subscription '<Registration subscription ID from portal>'
# rerun registration with new BillingModel (or same billing model in case of re-registration) but using other parameters values from portal
Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel '<New billing model>' -RegistrationName '<Registration name from portal>' -ResourceGroupName '<Registration resource group from portal>'
```

### <a name="azurerm-modules"></a>[Modules AzureRM](#tab/azurerm4)

```powershell  
# select the subscription used during the registration
Select-AzureRMSubscription -Subscription '<Registration subscription ID from portal>'
# rerun registration with new BillingModel (or same billing model in case of re-registration) but using other parameters values from portal
Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel '<New billing model>' -RegistrationName '<Registration name from portal>' -ResourceGroupName '<Registration resource group from portal>'
```

---

::: zone-end

::: zone pivot="state-disconnected"
Vous devez mettre à jour ou renouveler votre inscription dans les cas suivants :

- Après le renouvellement de votre abonnement annuel basé sur la capacité.
- Lorsque vous changez de modèle de facturation.
- Lorsque vous mettez à l’échelle des modifications (ajout/suppression de nœuds) pour la facturation basée sur la capacité.

### <a name="remove-the-activation-resource-from-azure-stack-hub"></a>Supprimer la ressource d’activation d’Azure Stack Hub

Vous devez d’abord supprimer la ressource d’activation d’Azure Stack Hub, puis la ressource d’inscription dans Azure.  

Pour supprimer la ressource d’activation d’Azure Stack Hub, exécutez les cmdlets PowerShell suivantes dans votre environnement Azure Stack Hub :  

  ```powershell
  Remove-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  ```

Ensuite, pour supprimer la ressource d’inscription dans Azure, vérifiez que vous êtes sur un ordinateur connecté à Azure, connectez-vous au contexte Azure PowerShell correct et exécutez les applets de commande PowerShell appropriées, comme décrit ci-dessous.

Vous pouvez utiliser le jeton d’inscription utilisé pour créer la ressource :  

  ```powershell
  $RegistrationToken = "<registration token>"
  Unregister-AzsEnvironment -RegistrationToken $RegistrationToken
  ```

Vous pouvez utiliser le nom de l’inscription et celui du groupe de ressources de l’inscription obtenus à partir du [portail administrateur](#verify-azure-stack-hub-registration) :

  ```powershell
  Unregister-AzsEnvironment -RegistrationName '<Registration name from portal>' -ResourceGroupName '<Registration resource group from portal>'
  ```

### <a name="re-register-using-connected-steps"></a>Effectuer une réinscription à l’aide des étapes connectées

Si vous remplacez votre modèle de facturation selon la capacité dans un état déconnecté par le modèle de facturation de la consommation dans un état connecté, vous allez effectuer une réinscription en suivant les [étapes de modèle connectées](azure-stack-registration.md?pivots=state-connected#change-billing-model-how-features-are-offered-or-re-register-your-instance). 

>[!Note] 
>Cela ne modifie pas votre modèle d’identité, uniquement le mécanisme de facturation, et vous continuerez à utiliser AD FS comme source d’identité.

### <a name="re-register-using-disconnected-steps"></a>Effectuer une réinscription à l’aide des étapes déconnectées

Vous avez maintenant complètement annulé l’inscription dans un scénario déconnecté et vous devez répéter les étapes d’inscription d’un environnement Azure Stack Hub dans un scénario déconnecté.
::: zone-end

### <a name="disable-or-enable-usage-reporting"></a>Désactiver ou activer les rapports d’utilisation

Pour les environnements Azure Stack Hub qui utilisent un modèle de facturation selon la capacité, désactivez la création de rapports d’utilisation avec le paramètre **UsageReportingEnabled** en utilisant la cmdlet **Set-AzsRegistration** ou  **Get-AzsRegistrationToken**. Azure Stack Hub crée par défaut des rapports sur les métriques d’utilisation. Les opérateurs qui se basent sur la capacité ou qui gèrent un environnement déconnecté doivent désactiver la génération de rapports d’utilisation.

::: zone pivot="state-connected"
Exécutez les applets de commande PowerShell suivantes :

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel Capacity
      -RegistrationName $RegistrationName
      -UsageReportingEnabled:$false
   ```
::: zone-end
::: zone pivot="state-disconnected"
1. Pour changer le jeton d’inscription, exécutez les applets de commande PowerShell suivantes :  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential -UsageReportingEnabled:$False
   $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```

   > [!Tip]  
   > Le jeton d’inscription est enregistré dans le fichier spécifié pour *$FilePathForRegistrationToken*. Vous pouvez modifier à votre convenance le nom ou le chemin d’accès au fichier.

2. Enregistrez ce jeton d’inscription pour l’utiliser sur la machine connectée à Azure. Vous pouvez copier le fichier ou le texte à partir de *$FilePathForRegistrationToken*.
::: zone-end

## <a name="move-a-registration-resource"></a>Déplacer une ressource d’inscription

Le déplacement d’une ressource d’inscription entre des groupes de ressources sous le même abonnement **est** pris en charge pour tous les environnements. Toutefois, le déplacement d’une ressource d’inscription entre abonnements est uniquement pris en charge pour les fournisseurs de services cloud quand les deux abonnements correspondent au même ID partenaire. Pour plus d’informations sur le déplacement de ressources vers un nouveau groupe de ressources, voir [Déplacer des ressources vers un nouveau groupe de ressource ou un nouvel abonnement](/azure/azure-resource-manager/resource-group-move-resources).

> [!IMPORTANT]
> Pour éviter la suppression accidentelle de ressources d'inscription sur le portail, le script d'inscription ajoute automatiquement un verrou à la ressource. Vous devez retirer ce verrou avant de le déplacer ou de le supprimer. Il est recommandé d’ajouter un verrou à votre ressource d’inscription pour éviter toute suppression accidentelle.

## <a name="registration-reference"></a>Référence de l’inscription

### <a name="set-azsregistration"></a>Set-AzsRegistration

Vous pouvez utiliser **Set-AzsRegistration** pour inscrire Azure Stack Hub auprès d’Azure et activer ou désactiver l’offre d’éléments sur la Place de marché et la création de rapports d’utilisation.

Pour exécuter l’applet de commande, vous avez besoin des éléments suivants :

- Un abonnement Azure global de n’importe quel type.
- Une connexion à Azure PowerShell avec un compte propriétaire ou collaborateur de cet abonnement.

```powershell
Set-AzsRegistration [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String> [[-AzureContext]
    <PSObject>] [[-ResourceGroupName] <String>] [[-ResourceGroupLocation] <String>] [[-BillingModel] <String>]
    [-MarketplaceSyndicationEnabled] [-UsageReportingEnabled] [[-AgreementNumber] <String>] [[-RegistrationName]
    <String>] [<CommonParameters>]
```

| Paramètre | Type | Description |
|-------------------------------|--------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PrivilegedEndpointCredential | PSCredential | Les informations d’identification qui sont utilisées pour [accéder au point de terminaison privilégié](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Le nom d’utilisateur est au format **AzureStackDomain\CloudAdmin**. |
| PrivilegedEndpoint | String | Une console PowerShell distante préconfigurée qui vous fournit des fonctionnalités telles que la collecte de journaux et d’autres tâches de post-déploiement. Pour en savoir plus, reportez-vous à l’article relatif à l’[utilisation du point de terminaison privilégié](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). |
| AzureContext | PSObject |  |
| ResourceGroupName | String |  |
| ResourceGroupLocation | String |  |
| BillingModel | String | Le modèle de facturation utilisé par votre abonnement. Les valeurs valides pour ce paramètre sont : Capacity, PayAsYouUse et Development. |
| MarketplaceSyndicationEnabled | True/False | Détermine si la fonctionnalité de gestion de la Place de marché est disponible dans le portail. Définissez la valeur true en cas d’inscription avec une connectivité Internet. Définissez la valeur false en cas d’inscription dans des environnements déconnectés. Pour les inscriptions déconnectées, l’[outil de syndication hors connexion](azure-stack-download-azure-marketplace-item.md?pivots=state-disconnected) peut être utilisé pour le téléchargement d’éléments de la Place de Marché. |
| UsageReportingEnabled | True/False | Azure Stack Hub crée par défaut des rapports sur les métriques d’utilisation. Les opérateurs qui se basent sur la capacité ou qui gèrent un environnement déconnecté doivent désactiver la génération de rapports d’utilisation. Les valeurs valides pour ce paramètre sont : True, False. |
| AgreementNumber | String | Numéro du Contrat Entreprise sous lequel la référence SKU de capacité d’Azure Stack a été commandée. |
| RegistrationName | String | Définissez un nom unique pour l’inscription si vous exécutez le script d’inscription dans plusieurs instances Azure Stack Hub en utilisant le même ID d’abonnement Azure. Par défaut, le paramètre a la valeur **AzureStackRegistration**. Cependant, si vous utilisez le même nom sur plusieurs instances d’Azure Stack Hub, le script échoue. |

### <a name="get-azsregistrationtoken"></a>Get-AzsRegistrationToken

Get-AzsRegistrationToken génère un jeton d’inscription à partir des paramètres d’entrée.

```powershell  
Get-AzsRegistrationToken [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String>
    [-BillingModel] <String> [[-TokenOutputFilePath] <String>] [-UsageReportingEnabled] [[-AgreementNumber] <String>]
    [<CommonParameters>]
```
| Paramètre | Type | Description |
|-------------------------------|--------------|-------------|
| PrivilegedEndpointCredential | PSCredential | Les informations d’identification qui sont utilisées pour [accéder au point de terminaison privilégié](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Le nom d’utilisateur est au format **AzureStackDomain\CloudAdmin**. |
| PrivilegedEndpoint | String |  Une console PowerShell distante préconfigurée qui vous fournit des fonctionnalités telles que la collecte de journaux et d’autres tâches de post-déploiement. Pour en savoir plus, reportez-vous à l’article relatif à l’[utilisation du point de terminaison privilégié](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). |
| AzureContext | PSObject |  |
| ResourceGroupName | String |  |
| ResourceGroupLocation | String |  |
| BillingModel | String | Le modèle de facturation utilisé par votre abonnement. Les valeurs valides pour ce paramètre sont : Capacity, PayAsYouUse et Development. |
| MarketplaceSyndicationEnabled | True/False |  |
| UsageReportingEnabled | True/False | Azure Stack Hub crée par défaut des rapports sur les métriques d’utilisation. Les opérateurs qui se basent sur la capacité ou qui gèrent un environnement déconnecté doivent désactiver la génération de rapports d’utilisation. Les valeurs valides pour ce paramètre sont : True, False. |
| AgreementNumber | String |  |

## <a name="registration-failures"></a>Échecs d’enregistrement

Vous pouvez recevoir l’une des erreurs ci-dessous quand vous tentez d’inscrire votre instance Azure Stack Hub :

- Impossible d’obtenir les informations requises sur le matériel pour `$hostName`. Vérifiez la connectivité et l’hôte physique, puis réessayez l’inscription.

- Impossible de se connecter à `$hostName` pour accéder aux informations sur le matériel. Vérifiez la connectivité et l’hôte physique, puis réessayez l’inscription.

   Cause : cette erreur se produit généralement quand vous essayez d’obtenir les informations sur le matériel, comme l’UUID, le BIOS et l’UC, auprès des hôtes lors d’une tentative d’activation et que cette opération échoue en raison d’une connexion impossible à l’hôte physique.

- L’identificateur de cloud [`GUID`] est déjà inscrit. La réutilisation des identificateurs de cloud n’est pas autorisée.

   Cause : cela se produit lorsque votre environnement Azure Stack est déjà inscrit. Si vous souhaitez réinscrire votre environnement avec un autre abonnement ou modèle de facturation, suivez les étapes dans [Renouveler ou modifier l’inscription](#renew-or-change-registration).

- Une erreur se produit quand vous tentez d’accéder à la Gestion de la Place de marché et de syndiquer des produits.

   Cause : cette erreur se produit généralement quand Azure Stack Hub ne réussit pas à accéder à la ressource d’inscription. Une des causes courantes de cette erreur est le changement du locataire d’annuaire d’un abonnement Azure, qui entraîne la réinitialisation de l’inscription. Vous ne pouvez pas accéder aux rapports d’utilisation ni à la Place de marché Azure Stack Hub après avoir changé le locataire d’annuaire de l’abonnement. Vous devez refaire l’inscription pour résoudre ce problème.
::: zone pivot="state-disconnected"
- Vous recevez un message de la Gestion de la Place de marché vous invitant à inscrire et à activer votre instance Azure Stack Hub alors que vous avez déjà inscrit votre tampon à l’aide du processus déconnecté.

   Cause : il s’agit d’un problème connu pour les environnements déconnectés qui vous oblige à [vérifier l’état de votre inscription](#verify-azure-stack-hub-registration). Pour utiliser la gestion de la Place de marché, utilisez l’[outil hors connexion](azure-stack-download-azure-marketplace-item.md?pivots=state-disconnected).
::: zone-end

## <a name="next-steps"></a>Étapes suivantes

[Télécharger des éléments de la Place de marché à partir d’Azure](azure-stack-download-azure-marketplace-item.md)
