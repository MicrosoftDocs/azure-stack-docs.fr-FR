---
title: Déployer Kubernetes sur Azure Stack à l’aide d’Active Directory Federation Services (AD FS) | Microsoft Docs
description: Découvrez comment déployer Kubernetes sur Azure Stack à l’aide d’Active Directory Federation Services (AD FS).
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 02/11/2019
ms.openlocfilehash: ca0dd74a08ce1abe454cb497a2569aae0b958d7c
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "64311501"
---
# <a name="deploy-kubernetes-to-azure-stack-using-active-directory-federated-services"></a>Déployer Kubernetes sur Azure Stack à l’aide d’Active Directory Federation Services

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

> [!Note]  
> Kubernetes sur Azure Stack est en préversion. Le scénario Azure Stack déconnecté n’est actuellement pas pris en charge par la préversion.

Vous pouvez suivre les étapes de cet article pour déployer et configurer les ressources pour Kubernetes. Utilisez ces étapes quand Active Directory Federation Services (AD FS) est votre service de gestion des identités.

## <a name="prerequisites"></a>Prérequis 

Pour commencer, assurez-vous que vous disposez des autorisations appropriées et que votre Azure Stack est prêt.

1. Générez une paire de clés publique et privée SSH pour vous connecter à la machine virtuelle Linux sur Azure Stack. Vous avec besoin de la clé publique lors de la création du cluster.

    Pour obtenir des instructions sur la génération d’une clé, voir [Génération d’une clé SSH](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation).

1. Vérifiez que vous avez un abonnement valide dans votre portail de client Azure Stack et que vous disposez d’un nombre suffisant d’adresses IP publiques pour ajouter de nouvelles applications.

    Vous ne pouvez pas déployer le cluster sur un abonnement **Administrateur** Azure Stack. Vous devez utiliser un abonnement **Utilisateur**. 

1. Vous aurez besoin du service Key Vault dans votre abonnement Azure Stack.

1. Vous aurez besoin du cluster Kubernetes dans votre marketplace. 

Si vous ne disposez pas du service Key Vault et de l'élément de marketplace de cluster Kubernetes, contactez votre administrateur Azure Stack.

## <a name="create-a-service-principal"></a>Créer un principal du service

Vous devez travailler avec votre administrateur Azure Stack pour configurer votre principal du service si vous utilisez AD FS comme solution de gestion des identités. Le principal du service permet à votre application d’accéder aux ressources Azure Stack.

1. Votre administrateur Azure Stack vous fournit un certificat ainsi que les informations relatives au principal du service.

   - Les informations du principal de service doivent se présenter comme suit :

     ```Text  
       ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
       ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
       Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
       ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
       PSComputerName        : azs-ercs01
       RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
     ```

   - Votre certificat se présentera sous forme de fichier portant l'extension `.pfx`. Vous stockerez votre certificat en tant que secret dans un coffre de clés.

2. Attribuez à votre nouveau principal de service un rôle de contributeur à votre abonnement. Pour obtenir des instructions, consultez [Attribuer un rôle](../operator/azure-stack-create-service-principals.md).

3. Créez un coffre de clés destiné à stocker votre certificat pour le déploiement. Utilisez les scripts PowerShell suivants plutôt que le portail.

   - Vous avez besoin des informations suivantes :

       | Valeur | Description |
       | ---   | ---         |
       | Point de terminaison Azure Resource Manager | Microsoft Azure Resource Manager est une infrastructure de gestion qui permet aux administrateurs de déployer, gérer et superviser les ressources Azure. Azure Resource Manager peut gérer ces tâches en groupe, plutôt qu’individuellement, dans une seule opération.<br>Le point de terminaison dans le Kit de développement Azure Stack (ASDK) est : `https://management.local.azurestack.external/`.<br>Le point de terminaison dans les systèmes intégrés est : `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` |
       | Votre ID d’abonnement | [L’ID d’abonnement](../operator/azure-stack-plan-offer-quota-overview.md#subscriptions) correspond à la façon dont vous accédez à des offres dans Azure Stack. |
       | Votre nom d’utilisateur | Utilisez votre nom d’utilisateur plutôt que votre nom de domaine et le nom d’utilisateur, comme `username` plutôt que `azurestack\username`. |
       | Nom du groupe de ressources  | Entrez le nom d’un nouveau groupe de ressources ou sélectionnez un groupe de ressources existant. Le nom de la ressource doit être alphanumérique et en minuscules. |
       | Nom du coffre de clés | Nom du coffre.<br> Modèle d’expression régulière : `^[a-zA-Z0-9-]{3,24}$` |
       | Emplacement du groupe de ressources | Emplacement du groupe de ressources. Il s’agit de la région que vous avez choisie pour votre installation Azure Stack. |

   - Ouvrez PowerShell avec une invite de commandes avec élévation de privilèges et [connectez-vous à Azure Stack](azure-stack-powershell-configure-user.md#connect-with-ad-fs). Exécutez le script suivant, en utilisant vos valeurs pour les paramètres :

   ```powershell  
       $armEndpoint="<Azure Resource Manager Endpoint>"
       $subscriptionId="<Your Subscription ID>"
       $username="<your user name >"
       $resource_group_name = "<the resource group name >"
       $key_vault_name = "<keyvault name>"
       $resource_group_location="<resource group location>"
        
       # Login Azure Stack Environment
       Add-AzureRmEnvironment -ARMEndpoint $armEndpoint -Name t
       $mycreds = Get-Credential
       Login-AzureRmAccount -Credential $mycreds -Environment t -Subscription $subscriptionId
        
       # Create new Resource group and key vault
       New-AzureRmResourceGroup -Name $resource_group_name -Location $resource_group_location -Force
        
       # Note, Do not omit -EnabledForTemplateDeployment flag
       New-AzureRmKeyVault -VaultName $key_vault_name -ResourceGroupName $resource_group_name -Location $resource_group_location -EnabledForTemplateDeployment
        
       # Obtain the security identifier(SID) of the active directory user
       $adUser = Get-ADUser -Filter "Name -eq '$username'" -Credential $mycreds
       $objectSID = $adUser.SID.Value
       # Set the key vault access policy
       Set-AzureRmKeyVaultAccessPolicy -VaultName $key_vault_name -ResourceGroupName $resource_group_name -ObjectId $objectSID -BypassObjectIdValidation -PermissionsToKeys all -PermissionsToSecrets all
     ```

4. Chargez votre certificat dans le coffre de clés.

   - Vous avez besoin des informations suivantes :

       | Valeur | Description |
       | ---   | ---         |
       | Chemin du certificat | FQDN ou chemin du fichier vers le certificat. |
       | Mot de passe du certificat | Mot de passe du certificat. |
       | Nom du secret | Le nom secret utilisé pour référencer le certificat stocké dans le coffre. |
       | Nom du coffre de clés | Le nom du coffre de clés créé à l’étape précédente. |
       | Point de terminaison Azure Resource Manager | Le point de terminaison dans le Kit de développement Azure Stack (ASDK) est : `https://management.local.azurestack.external/`.<br>Le point de terminaison dans les systèmes intégrés est : `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` |
       | Votre ID d’abonnement | [L’ID d’abonnement](../operator/azure-stack-plan-offer-quota-overview.md#subscriptions) correspond à la façon dont vous accédez à des offres dans Azure Stack. |

   - Ouvrez PowerShell avec une invite de commandes avec élévation de privilèges et [connectez-vous à Azure Stack](azure-stack-powershell-configure-user.md#connect-with-ad-fs). Exécutez le script suivant, en utilisant vos valeurs pour les paramètres :

    ```powershell
        
     # upload the pfx to key vault
     $tempPFXFilePath = "<certificate path>"
     $password = "<certificate password>"
     $keyVaultSecretName = "<secret name>"
     $keyVaultName = "<key vault name>"
     $armEndpoint="<Azure Resource Manager Endpoint>"
     $subscriptionId="<Your Subscription ID>"
     # Login Azure Stack Environment
     Add-AzureRmEnvironment -ARMEndpoint $armEndpoint -Name t
     $mycreds = Get-Credential
     Login-AzureRmAccount -Credential $mycreds -Environment t -Subscription $subscriptionId
    
     $certContentInBytes = [io.file]::ReadAllBytes($tempPFXFilePath)
     $pfxAsBase64EncodedString = [System.Convert]::ToBase64String($certContentInBytes)
     $jsonObject = @"
     {
     "data": "$pfxAsBase64EncodedString",
     "dataType" :"pfx",
     "password": "$password"
     }
     "@
     $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
     $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
     $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
     $keyVaultSecret = Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName -SecretValue $secret 
     ```

## <a name="deploy-kubernetes"></a>Déployer Kubernetes

1. Ouvrez le [portail Azure Stack](https://portal.local.azurestack.external).

1. Sélectionnez **Créer une ressource** > **Calcul** > **Cluster Kubernetes**. Cliquez sur **Créer**.

    ![Déployer un modèle de solution](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1. Concepts de base

1. Dans le volet Créer un cluster Kubernetes, sélectionnez **Bases**.

    ![Déployer un modèle de solution](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. Sélectionnez votre ID **d’abonnement**.

1. Entrez le nom d’un nouveau groupe de ressources ou sélectionnez un groupe de ressources existant. Le nom de la ressource doit être alphanumérique et en minuscules.

1. Sélectionnez **l’emplacement** du groupe de ressources. Il s’agit de la région que vous avez choisie pour votre installation Azure Stack.

### <a name="2-kubernetes-cluster-settings"></a>2. Paramètres de cluster Kubernetes

1. Dans le volet Créer un cluster Kubernetes, sélectionnez **Kubernetes Cluster Settings** (Paramètres de cluster Kubernetes).

    ![Déployer un modèle de solution](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings-adfs.png)

1. Entrez le **nom de l’utilisateur administrateur de la machine virtuelle Linux**. Nom d’utilisateur pour les machines virtuelles Linux qui font partie du cluster Kubernetes et de DVM.

1. Entrez la **Clé publique SSH** utilisée pour l’autorisation sur toutes les machines Linux, créée en même temps que le cluster Kubernetes et DVM.

1. Entrez le **Préfixe DNS du profil principal** qui est unique pour la région. Il doit s’agir d’un nom unique pour la région, tel que `k8s-12345`. Nous vous recommandons d’essayer de choisir le même préfixe que celui du nom du groupe de ressources.

    > [!Note]  
    > Pour chaque cluster, utilisez un préfixe DNS de profil principal nouveau et unique.

1. Sélectionnez la valeur du champ **Kubernetes Master Pool Profile Count** (Nombre de profils de pool maître Kubernetes). Il s’agit du nombre de nœuds dans le pool maître. Elle peut être comprise entre 1 et 7. Cette valeur doit être un nombre impair.

1. Sélectionnez la valeur du champ **The VMSize of the Kubernetes master VMs** (Taille des machines virtuelles maîtres Kubernetes).

1. Sélectionnez la valeur du champ **Kubernetes Node Pool Profile Count** (Nombre de profils de pool de nœuds Kubernetes). Il s’agit du nombre d’agents dans le cluster. 

1. Sélectionnez la valeur du champ **Storage Profile** (Profil de stockage). Vous pouvez choisir **Blob Disk** (Disque de blob) ou **Managed Disk** (Disque managé). Cette opération spécifie la taille des machines virtuelles de nœud Kubernetes. 

1. Sélectionnez **ADFS** comme **système d’identité Azure Stack** pour votre installation Azure Stack.

1. Entrez **l’ID client du principal de service**. Celui-ci est utilisé par le fournisseur cloud d’Azure Kubernetes. ID de client identifié comme ID d’application quand votre administrateur Azure Stack a créé le principal du service.

1. Entrez le **groupe de ressources Key Vault** d'où provient le coffre de clés contenant votre certificat.

1. Entrez le **nom du coffre de clés** contenant votre certificat comme secret. 

1. Entrez le **secret du coffre de clés**. Le nom du secret fait référence à votre certificat.

1. Entrez la **version du fournisseur cloud d’Azure Kubernetes**. Il s’agit de la version du fournisseur d’Azure Kubernetes. Azure Stack publie une build Kubernetes personnalisée pour chaque version d’Azure Stack.

### <a name="3-summary"></a>3. Résumé

1. Sélectionnez Résumé. Le panneau affiche un message de validation concernant vos paramètres de configuration de cluster Kubernetes.

    ![Déployer un modèle de solution](media/azure-stack-solution-template-kubernetes-deploy/04_preview.png)

2. Passez vos paramètres en revue.

3. Sélectionnez **OK** pour déployer votre cluster.

> [!TIP]  
>  Si vous avez des questions concernant votre déploiement, vous pouvez poster votre question ou lire la réponse si quelqu’un y a déjà répondu sur le [Forum Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 

## <a name="next-steps"></a>Étapes suivantes

[Se connecter à votre cluster](azure-stack-solution-template-kubernetes-deploy.md#connect-to-your-cluster)

[Activer le tableau de bord Kubernetes](azure-stack-solution-template-kubernetes-dashboard.md)