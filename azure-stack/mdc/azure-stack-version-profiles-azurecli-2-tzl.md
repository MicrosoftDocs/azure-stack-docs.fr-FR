---
title: Gérer Azure Stack Hub à l'aide de l'interface Azure CLI | Microsoft Docs
description: Apprenez à utiliser l'interface de ligne de commande (CLI) multiplateforme pour gérer et déployer des ressources sur Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/26/2020
ms.openlocfilehash: 55a9c4ec924cc12a52199f00e77857b2813207cc
ms.sourcegitcommit: 9ecf9c58fbcc4bc42c1fdc688f370c643c761a29
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93330030"
---
# <a name="manage-and-deploy-resources-to-azure-stack-hub-with-azure-cli"></a>Gérer et déployer des ressources sur Azure Stack Hub à l'aide de l'interface Azure CLI

*S’applique à : Systèmes intégrés Azure Stack Hub*

Suivez les étapes de cet article pour configurer l’interface de ligne de commande (CLI) Azure afin de gérer les ressources Azure Stack Hub à partir des plateformes clientes Linux, Mac et Windows.

## <a name="prepare-for-azure-cli"></a>Préparation pour Azure CLI

**Le point de terminaison des alias de la machine virtuelle** fournit un alias, par exemple « UbuntuLTS » ou « Win2012Datacenter ». Cet alias fait référence à un éditeur d'images, une offre, une référence SKU et une version en tant que paramètre unique lors du déploiement de machines virtuelles.  

La section suivante explique comment configurer le point de terminaison des alias de machines virtuelles.

### <a name="set-up-the-virtual-machine-aliases-endpoint"></a>Configurer le point de terminaison des alias de machines virtuelles

Vous pouvez configurer un point de terminaison accessible publiquement qui héberge un fichier d’alias de machines virtuelles. Le fichier d’alias de machines virtuelles est un fichier JSON qui fournit un nom commun pour une image. Vous utiliserez ce nom lorsque vous déploierez une machine virtuelle en tant que paramètre Azure CLI.

1. Si vous publiez une image personnalisée, prenez note des informations concernant l’éditeur, l’offre, la référence (SKU) et la version que vous avez spécifiées lors de la publication. S'il s'agit d'une image provenant de la Place de marché, vous pouvez afficher les informations à l’aide de la cmdlet ```Get-AzureVMImage```.  

2. Téléchargez l’[ exemple de fichier](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) à partir de GitHub.

3. Créez un compte de stockage dans Azure Stack Hub. Puis créez un conteneur d'objets blob. Définissez la stratégie d’accès sur « publique ».  

4. Chargez le fichier JSON dans le nouveau conteneur. Au terme de cette opération, vous pouvez afficher l'URL de l'objet blob. Sélectionnez le nom de l'objet blob, puis l'URL dans ses propriétés.

### <a name="install-or-upgrade-cli"></a>Installer ou mettre à niveau l’interface CLI

Connectez-vous à votre station de travail de développement et installez l’interface CLI. Azure Stack Hub nécessite la version 2.0 ou ultérieure d'Azure CLI. La dernière version des profils d’API nécessite une version actuelle de l’interface CLI. Vous l’installerez en utilisant la procédure décrite dans l’article [Installer Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

Pour vérifier que l’installation a réussi, ouvrez un terminal ou une fenêtre d’invite de commandes, puis exécutez la commande suivante :

```shell
az --version
```

Vous devriez voir la version d’Azure CLI et d’autres bibliothèques dépendantes installées sur votre ordinateur.
    
![Azure CLI sur l'emplacement Python Azure Stack Hub](media/azure-stack-version-profiles-azurecli2/cli-python-location.png)


## <a name="windowslinux-azure-ad"></a>Windows/Linux (Azure AD)

Cette section vous guide dans la configuration de l’interface CLI si vous utilisez Azure AD comme service de gestion des identités et si vous utilisez l’interface CLI sur un ordinateur Windows/Linux.

### <a name="connect-to-azure-stack-hub"></a>Se connecter à Azure Stack Hub

1. Inscrivez votre environnement Azure Stack Hub en exécutant la commande `az cloud register`.

2. Inscrivez votre environnement. Utilisez les paramètres suivants lors de l’exécution de `az cloud register` :

    | Valeur | Exemple | Description |
    | --- | --- | --- |
    | Nom de l’environnement | AzureStackUser | Pour l’environnement utilisateur, utilisez `AzureStackUser`. Si vous êtes un opérateur, spécifiez `AzureStackAdmin`. |
    | Point de terminaison Resource Manager | `https://management.local.azurestack.external` | <!-- TZLASDKFIX The **ResourceManagerUrl** in the ASDK is: `https://management.local.azurestack.external/`--> Le **ResourceManagerUrl** dans les systèmes intégrés est : `https://management.<region>.<fqdn>/` Si vous avez une question sur le point de terminaison du système intégré, contactez votre opérateur cloud. |
    | Point de terminaison de stockage | local.azurestack.external | <!-- TZLASDKFIX `local.azurestack.external` is for the ASDK.--> Pour un système intégré, utilisez un point de terminaison pour votre système.  |
    | Suffixe du coffre de clés | .vault.local.azurestack.external | <!-- TZLASDKFIX `.vault.local.azurestack.external` is for the ASDK.--> Pour un système intégré, utilisez un point de terminaison pour votre système.  |
    | Point de terminaison du document d’alias d’image de machine virtuelle- | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | URI du document, qui contient les alias d’images de machine virtuelle. Pour plus d’informations, consultez [Configurer le point de terminaison des alias de machines virtuelles](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

1. Définissez l’environnement actif avec les commandes suivantes.

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. Mettez à jour la configuration de votre environnement pour utiliser le profil de version des API propre à Azure Stack Hub. Pour mettre à jour la configuration, exécutez la commande suivante :

    ```azurecli
    az cloud update --profile 2019-03-01-hybrid
   ```
 
1. Connectez-vous à votre environnement Azure Stack Hub à l'aide de la commande `az login`. Connectez-vous à l'environnement Azure Stack Hub en tant qu'utilisateur ou que principal de service. 

   - Connectez-vous en tant qu’ *utilisateur*  : 

     Vous pouvez spécifier directement le nom d’utilisateur et le mot de passe dans la commande `az login`, ou vous authentifier avec un navigateur. Vous devez choisir cette dernière solution si l’authentification multifacteur est activée sur votre compte :

     ```azurecli
     az login -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > Si l'authentification multifacteur est activée sur votre compte d'utilisateur, utilisez la commande `az login` sans fournir le paramètre `-u`. L’exécution de cette commande vous donne une URL et un code à utiliser pour vous authentifier.

   - Connectez-vous en tant que *principal de service*  : 
    
     avant de vous connecter, [créez un principal de service avec le portail Azure](../operator/azure-stack-create-service-principals.md) ou l’interface CLI, et attribuez-lui un rôle. Ensuite, connectez-vous avec la commande suivante :

     ```azurecli  
     az login --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> --service-principal -u <Application Id of the Service Principal> -p <Key generated for the Service Principal>
     ```

### <a name="test-the-connectivity"></a>Tester la connectivité

Lorsque tout est configuré, utilisez l'interface CLI pour créer des ressources dans Azure Stack Hub. Par exemple, vous pouvez créer un groupe de ressources pour une app et ajouter une machine virtuelle. Utilisez la commande suivante pour créer le groupe de ressources nommé « myResourceGroup » :

```azurecli
az group create -n MyResourceGroup -l local
```

Si la création du groupe de ressources réussit, la commande précédente affiche les propriétés suivantes pour la ressource créée :

![Sortie de la création du groupe de ressources](media/azure-stack-connect-cli/output.png)

## <a name="windowslinux-ad-fs"></a>Windows/Linux (AD FS)

Cette section vous guide dans la configuration de l’interface CLI si vous utilisez Active Directory Federated Services (AD FS) comme service de gestion des identités et si vous utilisez l’interface CLI sur un ordinateur Windows/Linux.


### <a name="connect-to-azure-stack-hub"></a>Se connecter à Azure Stack Hub

1. Inscrivez votre environnement Azure Stack Hub en exécutant la commande `az cloud register`.

2. Inscrivez votre environnement. Utilisez les paramètres suivants lors de l’exécution de `az cloud register` :

    | Valeur | Exemple | Description |
    | --- | --- | --- |
    | Nom de l’environnement | AzureStackUser | Pour l’environnement utilisateur, utilisez `AzureStackUser`. Si vous êtes un opérateur, spécifiez `AzureStackAdmin`. |
    | Point de terminaison Resource Manager | `https://management.local.azurestack.external` | <!-- TZLASDKFIX The **ResourceManagerUrl** in the ASDK is: `https://management.local.azurestack.external/`--> Le **ResourceManagerUrl** dans les systèmes intégrés est : `https://management.<region>.<fqdn>/` Si vous avez une question sur le point de terminaison du système intégré, contactez votre opérateur cloud. |
    | Point de terminaison de stockage | local.azurestack.external | <!-- TZLASDKFIX `local.azurestack.external` is for the ASDK.--> Pour un système intégré, utilisez un point de terminaison pour votre système.  |
    | Suffixe du coffre de clés | .vault.local.azurestack.external | <!-- TZLASDKFIX `.vault.local.azurestack.external` is for the ASDK.--> Pour un système intégré, utilisez un point de terminaison pour votre système.  |
    | Point de terminaison du document d’alias d’image de machine virtuelle- | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | URI du document, qui contient les alias d’images de machine virtuelle. Pour plus d’informations, consultez [Configurer le point de terminaison des alias de machines virtuelles](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

1. Définissez l’environnement actif avec les commandes suivantes.

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. Mettez à jour la configuration de votre environnement pour utiliser le profil de version des API propre à Azure Stack Hub. Pour mettre à jour la configuration, exécutez la commande suivante :

    ```azurecli
    az cloud update --profile 2019-03-01-hybrid
   ```

1. Connectez-vous à votre environnement Azure Stack Hub à l'aide de la commande `az login`. Vous pouvez vous connecter à l'environnement Azure Stack Hub en tant qu'utilisateur ou que principal de service. 

   - Connectez-vous en tant qu’ *utilisateur*  :

     Vous pouvez spécifier directement le nom d’utilisateur et le mot de passe dans la commande `az login`, ou vous authentifier avec un navigateur. Vous devez choisir cette dernière solution si l’authentification multifacteur est activée sur votre compte :

     ```azurecli
     az cloud register  -n <environmentname>   --endpoint-resource-manager "https://management.local.azurestack.external"  --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>   --profile "2019-03-01-hybrid"
     ```

     > [!NOTE]
     > Si l'authentification multifacteur est activée sur votre compte d'utilisateur, utilisez la commande `az login` sans fournir le paramètre `-u`. L’exécution de cette commande vous donne une URL et un code à utiliser pour vous authentifier.

   - Connectez-vous en tant que *principal de service*  : 
    
     Préparez le fichier .pem à utiliser pour la connexion du principal de service.

     Sur l’ordinateur client où le principal a été créé, exportez le certificat du principal de service au format pfx avec la clé privée située sous `cert:\CurrentUser\My`. Le certificat porte le même nom que le principal.

     Convertissez le fichier pfx au format pem (avec l’utilitaire OpenSSL).

     Connectez-vous à l’interface CLI :
  
     ```azurecli  
     az login --service-principal \
      -u <Client ID from the Service Principal details> \
      -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
      --tenant <Tenant ID> \
      --debug 
     ```

### <a name="test-the-connectivity"></a>Tester la connectivité

Lorsque tout est configuré, utilisez l'interface CLI pour créer des ressources dans Azure Stack Hub. Par exemple, vous pouvez créer un groupe de ressources pour une app et ajouter une machine virtuelle. Utilisez la commande suivante pour créer le groupe de ressources nommé « myResourceGroup » :

```azurecli
az group create -n MyResourceGroup -l local
```

Si la création du groupe de ressources réussit, la commande précédente affiche les propriétés suivantes pour la ressource créée :

![Sortie de la création du groupe de ressources](media/azure-stack-connect-cli/output.png)


## <a name="known-issues"></a>Problèmes connus

Différents problèmes liés à l'utilisation de l'interface CLI dans Azure Stack Hub ont été identifiés :

 - Le mode interactif de l’interface CLI. Par exemple, la commande `az interactive` n'est pas encore prise en charge dans Azure Stack Hub.
 - Pour obtenir la liste des images de machines virtuelles disponibles dans Azure Stack Hub, utilisez la commande `az vm image list --all` au lieu de la commande `az vm image list`. En spécifiant l'option `--all`, vous êtes assuré que la réponse renverra uniquement les images disponibles dans votre environnement Azure Stack Hub.
 - Les alias d'images de machines virtuelles disponibles dans Azure ne s'appliquent pas forcément à Azure Stack Hub. Si vous utilisez des images de machines virtuelles, vous devez utiliser la totalité du paramètre URN (Canonical:UbuntuServer:14.04.3-LTS:1.0.0) plutôt que l’alias de l’image. Cet URN doit par ailleurs correspondre aux spécifications des images dérivées de la commande `az vm images list`.

## <a name="next-steps"></a>Étapes suivantes

- [Déployer des modèles avec l’interface de ligne de commande Azure](../user/azure-stack-deploy-template-command-line.md)
- [Activer Azure CLI pour les utilisateurs d'Azure Stack Hub (opérateur)](../operator/azure-stack-cli-admin.md)
- [Gérer les autorisations utilisateur](../operator/azure-stack-manage-permissions.md) 