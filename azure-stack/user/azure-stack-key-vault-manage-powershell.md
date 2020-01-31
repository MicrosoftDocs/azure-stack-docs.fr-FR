---
title: Gérer Key Vault dans Azure Stack Hub à l’aide de PowerShell
description: Découvrez comment gérer Key Vault dans Azure Stack Hub à l’aide de PowerShell.
author: sethmanheim
ms.topic: article
ms.date: 01/07/2020
ms.author: sethm
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: c9d0f5eda2e4107d0686b8869c005e812268acaa
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76883628"
---
# <a name="manage-key-vault-in-azure-stack-hub-using-powershell"></a>Gérer Key Vault dans Azure Stack Hub à l’aide de PowerShell

Cet article explique comment créer et gérer un coffre de clés dans Azure Stack Hub à l’aide de PowerShell. Vous apprendrez à utiliser les applets de commande PowerShell Key Vault pour :

* Création d’un coffre de clés
* Stocker et gérer des clés de chiffrement et des secrets.
* Autoriser des utilisateurs ou des apps à appeler des opérations dans le coffre.

>[!NOTE]
>Les applets de commande PowerShell Key Vault décrites dans cet article sont fournies dans le SDK Azure PowerShell.

## <a name="prerequisites"></a>Conditions préalables requises

* Vous devez vous abonner à une offre qui inclut le service Azure Key Vault.
* [Installez PowerShell pour Azure Stack Hub](../operator/azure-stack-powershell-install.md).
* [Configurez l’environnement Azure Stack Hub PowerShell](azure-stack-powershell-configure-user.md).

## <a name="enable-your-tenant-subscription-for-key-vault-operations"></a>Activer votre abonnement de locataire pour les opérations Key Vault

Avant de pouvoir exécuter des opérations sur un coffre de clés, vous devez vérifier que votre abonnement de locataire est activé pour les opérations de coffre. Pour vérifier que les opérations de coffre de clés sont activées, exécutez la commande suivante :

```powershell  
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault | ft -Autosize
```

Si votre abonnement est activé pour les opérations de coffre, la sortie indique que **RegistrationState** est **Registered** pour tous les types de ressources d’un coffre de clés.

![État d’inscription du coffre de clés dans PowerShell](media/azure-stack-key-vault-manage-powershell/image1.png)

Si les opérations de coffre ne sont pas activées, exécutez la commande suivante pour inscrire le service Key Vault dans votre abonnement :

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault
```

Si l’inscription réussit, la sortie suivante est retournée :

![Inscription du coffre de clés dans PowerShell réussie](media/azure-stack-key-vault-manage-powershell/image2.png)

Quand vous appelez les commandes Key Vault, vous pouvez obtenir une erreur similaire à « L’abonnement n’est pas inscrit pour utiliser l’espace de noms Microsoft.KeyVault ». Si vous recevez une erreur, assurez-vous d’avoir activé le fournisseur de ressources Key Vault en suivant les instructions fournies précédemment.

## <a name="create-a-key-vault"></a>Création d’un coffre de clés

Avant de créer un coffre de clés, créez un groupe de ressources pour que toutes les ressources associées au coffre de clés fassent partie d’un groupe de ressources. Utilisez la commande suivante pour créer un groupe de ressources :

```powershell
New-AzureRmResourceGroup -Name "VaultRG" -Location local -verbose -Force
```

![Nouveau groupe de ressources généré dans PowerShell](media/azure-stack-key-vault-manage-powershell/image3.png)

Maintenant, utilisez la cmdlet **New-AzureRMKeyVault** pour créer un coffre de clés dans le groupe de ressources que vous avez créé plus tôt. Cette commande lit trois paramètres obligatoires : le nom du groupe de ressources, le nom du coffre de clés et l’emplacement géographique.

Exécutez la commande suivante pour créer un coffre de clés :

```powershell
New-AzureRmKeyVault -VaultName "Vault01" -ResourceGroupName "VaultRG" -Location local -verbose
```

![Nouveau coffre de clés généré dans PowerShell](media/azure-stack-key-vault-manage-powershell/image4.png)

La sortie de cette commande affiche les propriétés du coffre de clés que vous avez créé. Lorsqu’une app accède à ce coffre, elle doit utiliser la propriété **URI du coffre**, `https://vault01.vault.local.azurestack.external` dans cet exemple.

### <a name="active-directory-federation-services-ad-fs-deployment"></a>Déploiement Active Directory Federation Services (AD FS)

Dans un déploiement AD FS, vous risquez d’obtenir cet avertissement : « La stratégie d’accès n’est pas définie. Aucun utilisateur ou application n’est autorisé à utiliser ce coffre ». Pour résoudre ce problème, définissez une stratégie d’accès au coffre à l’aide de la commande [**Set-AzureRmKeyVaultAccessPolicy**](#authorize-an-app-to-use-a-key-or-secret) :

```powershell
# Obtain the security identifier(SID) of the active directory user
$adUser = Get-ADUser -Filter "Name -eq '{Active directory user name}'"
$objectSID = $adUser.SID.Value

# Set the key vault access policy
Set-AzureRmKeyVaultAccessPolicy -VaultName "{key vault name}" -ResourceGroupName "{resource group name}" -ObjectId "{object SID}" -PermissionsToKeys {permissionsToKeys} -PermissionsToSecrets {permissionsToSecrets} -BypassObjectIdValidation
```

## <a name="manage-keys-and-secrets"></a>Gérer les clés et les secrets

Une fois que vous avez créé un coffre, effectuez les étapes suivantes pour créer et gérer les clés et les secrets dans le coffre.

### <a name="create-a-key"></a>Créer une clé

Utilisez l’applet de commande **Add-AzureKeyVaultKey** pour créer ou importer une clé protégée par logiciel dans un coffre de clés :

```powershell
Add-AzureKeyVaultKey -VaultName "Vault01" -Name "Key01" -verbose -Destination Software
```

Le paramètre `-Destination` permet de spécifier que la clé est protégée par un logiciel. Une fois l’opération terminée, la commande affiche les détails de la clé créée.

![Nouvelle clé de coffre de clés générée dans PowerShell](media/azure-stack-key-vault-manage-powershell/image5.png)

Vous pouvez maintenant référencer la clé créée à l’aide de son URI. Si vous créez ou importez une clé qui a le même nom qu’une clé existante, la clé d’origine est mise à jour avec les valeurs spécifiées dans la nouvelle clé. Vous pouvez accéder à la version précédente à l’aide de l’URI propre à la version de la clé. Par exemple :

* Utilisez `https://vault10.vault.local.azurestack.external:443/keys/key01` pour toujours obtenir la version actuelle.
* Utilisez `https://vault010.vault.local.azurestack.external:443/keys/key01/d0b36ee2e3d14e9f967b8b6b1d38938a` pour obtenir cette version spécifique.

### <a name="get-a-key"></a>Obtenir une clé

Utilisez l’applet de commande **Get-AzureKeyVaultKey** pour lire une clé et ses détails :

```powershell
Get-AzureKeyVaultKey -VaultName "Vault01" -Name "Key01"
```

### <a name="create-a-secret"></a>Créer un secret

Utilisez la cmdlet **Set-AzureKeyVaultSecret** pour créer ou mettre à jour un secret dans un coffre. Un secret est créé s’il n’existe pas encore. Une nouvelle version du secret est créée s’il existe déjà :

```powershell
$secretvalue = ConvertTo-SecureString "User@123" -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName "Vault01" -Name "Secret01" -SecretValue $secretvalue
```

![Créer un secret dans PowerShell](media/azure-stack-key-vault-manage-powershell/image6.png)

### <a name="get-a-secret"></a>Obtenir un secret

Utilisez la cmdlet **Get-AzureKeyVaultSecret** pour lire un secret dans un coffre de clés. Cette commande peut retourner toutes les versions ou des versions spécifiques d’un secret :

```powershell
Get-AzureKeyVaultSecret -VaultName "Vault01" -Name "Secret01"
```

Après avoir créé les clés et les secrets, vous pouvez autoriser des apps externes à les utiliser.

## <a name="authorize-an-app-to-use-a-key-or-secret"></a>Autoriser une app à utiliser une clé ou un secret

Utilisez l’applet de commande **Set-AzureRmKeyVaultAccessPolicy** pour autoriser une application à accéder à une clé ou un secret dans le coffre de clés.

Dans l’exemple suivant, le nom du coffre est **ContosoKeyVault** et l’ID client de l’application que vous souhaitez autoriser est **8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed**. Pour autoriser l’app, exécutez la commande suivante. Vous pouvez également spécifier le paramètre **PermissionsToKeys** pour définir des autorisations pour un utilisateur, une app ou un groupe de sécurité.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign
```

Si vous souhaitez autoriser cette même app à lire les secrets de votre coffre, exécutez l’applet de commande suivante :

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300 -PermissionsToKeys Get
```

## <a name="next-steps"></a>Étapes suivantes

* [Déployer une machine virtuelle avec un mot de passe stocké dans Key Vault](azure-stack-key-vault-deploy-vm-with-secret.md)
* [Déployer une machine virtuelle avec un certificat stocké dans Key Vault](azure-stack-key-vault-push-secret-into-vm.md)
