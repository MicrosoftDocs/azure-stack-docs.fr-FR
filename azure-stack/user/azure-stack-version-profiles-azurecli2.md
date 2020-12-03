---
title: Gérer Azure Stack Hub avec Azure CLI
description: Apprenez à utiliser l'interface de ligne de commande (CLI) multiplateforme pour gérer et déployer des ressources sur Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: 5cd1c1b7dac9e05925488b3543461f3fbd8dd9e5
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96525878"
---
# <a name="install-azure-cli-on-azure-stack-hub"></a>Installer Azure CLI sur Azure Stack Hub

Vous pouvez installer Azure CLI pour gérer Azure Stack Hub avec un ordinateur Windows ou Linux. Cet article vous guide tout au long de la procédure d’installation et de configuration d’Azure CLI.

## <a name="install-azure-cli"></a>Installation de l’interface de ligne de commande Azure

1. Connectez-vous à votre station de travail de développement et installez l’interface CLI. Azure Stack Hub nécessite la version 2.0 ou ultérieure d'Azure CLI. 

2. Vous pouvez l’installer en utilisant la procédure décrite dans l’article [Installer Azure CLI](/cli/azure/install-azure-cli). 

3. Pour vérifier que l’installation a réussi, ouvrez un terminal ou une fenêtre d’invite de commandes, puis exécutez la commande suivante :

    ```shell
    az --version
    ```

    Vous devriez voir la version d’Azure CLI et d’autres bibliothèques dépendantes installées sur votre ordinateur.

    ![Azure CLI sur l'emplacement Python Azure Stack Hub](media/azure-stack-version-profiles-azurecli2/cli-python-location.png)

2. Prenez note de l’emplacement Python de l’interface CLI. Si vous exécutez ASDK, vous avez besoin de cet emplacement pour ajouter votre certificat. Pour obtenir des instructions sur la configuration des certificats afin d’installer l’interface CLI sur le kit ASDK, consultez [Configuration de certificats pour Azure CLI sur le Kit de développement Azure Stack](../asdk/asdk-cli.md).

## <a name="set-up-azure-cli"></a>Configuration de l’interface de ligne de commande Azure CLI

### <a name="azure-ad-on-windows"></a>[Azure AD sur Windows](#tab/ad-win)

Cette section vous guide dans la configuration de l’interface CLI si vous utilisez Azure AD comme service de gestion des identités et si vous utilisez l’interface CLI sur un ordinateur Windows.

#### <a name="connect-to-azure-stack-hub"></a>Se connecter à Azure Stack Hub

1. Si vous utilisez le kit ASDK, approuvez le certificat racine de l’autorité de certification Azure Stack Hub. Pour obtenir des instructions, consultez [Approuver le certificat](../asdk/asdk-cli.md#trust-the-certificate).

2. Inscrivez votre environnement Azure Stack Hub en exécutant la commande `az cloud register`.

3. Inscrivez votre environnement. Utilisez les paramètres suivants lors de l’exécution de `az cloud register` :

    | Valeur | Exemple | Description |
    | --- | --- | --- |
    | Nom de l’environnement | AzureStackUser | Pour l’environnement utilisateur, utilisez `AzureStackUser`. Si vous êtes un opérateur, spécifiez `AzureStackAdmin`. |
    | Point de terminaison Resource Manager | `https://management.local.azurestack.external` | La propriété **ResourceManagerUrl** dans l’ASDK est : `https://management.local.azurestack.external/`**ResourceManagerUrl** dans les systèmes intégrés est : `https://management.<region>.<fqdn>/` Si vous avez une question sur le point de terminaison du système intégré, contactez votre opérateur cloud. |
    | Point de terminaison de stockage | local.azurestack.external | `local.azurestack.external` concerne l’ASDK. Pour un système intégré, utilisez un point de terminaison pour votre système.  |
    | Suffixe du coffre de clés | .vault.local.azurestack.external | `.vault.local.azurestack.external` concerne l’ASDK. Pour un système intégré, utilisez un point de terminaison pour votre système.  |
    | Point de terminaison du document d’alias d’image de machine virtuelle- | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | URI du document, qui contient les alias d’images de machine virtuelle. Pour plus d’informations, consultez [Configurer le point de terminaison d’alias de machines virtuelles](../asdk/asdk-cli.md#set-up-the-virtual-machine-alias-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

4. Définissez l’environnement actif avec les commandes suivantes.

      ```azurecli
      az cloud set -n <environmentname>
      ```

5. Mettez à jour la configuration de votre environnement pour utiliser le profil de version des API propre à Azure Stack Hub. Pour mettre à jour la configuration, exécutez la commande suivante :

    ```azurecli
    az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Si vous exécutez une version d'Azure Stack Hub antérieure à la build 1808, vous devez utiliser le profil de version d'API **2017-03-09-profile** plutôt que le profil de version d'API **2019-03-01-hybrid**. Vous devez également utiliser une version récente de l’interface CLI.
 
6. Connectez-vous à votre environnement Azure Stack Hub à l'aide de la commande `az login`. Connectez-vous à l'environnement Azure Stack Hub en tant qu'utilisateur ou que [principal de service](/azure/active-directory/develop/app-objects-and-service-principals). 

   - Connectez-vous en tant qu’*utilisateur* : 

     Vous pouvez spécifier directement le nom d’utilisateur et le mot de passe dans la commande `az login`, ou vous authentifier avec un navigateur. Vous devez choisir cette dernière solution si l’authentification multifacteur est activée sur votre compte :

     ```azurecli
     az login -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > Si l'authentification multifacteur est activée sur votre compte d'utilisateur, utilisez la commande `az login` sans fournir le paramètre `-u`. L’exécution de cette commande vous donne une URL et un code à utiliser pour vous authentifier.

   - Connectez-vous en tant que *principal de service* : 
    
     avant de vous connecter, [créez un principal de service avec le portail Azure](../operator/azure-stack-create-service-principals.md?view=azs-2002) ou l’interface CLI, et attribuez-lui un rôle. Ensuite, connectez-vous avec la commande suivante :

     ```azurecli  
     az login --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> --service-principal -u <Application Id of the Service Principal> -p <Key generated for the Service Principal>
     ```

#### <a name="test-the-connectivity"></a>Tester la connectivité

Lorsque tout est configuré, utilisez l'interface CLI pour créer des ressources dans Azure Stack Hub. Par exemple, vous pouvez créer un groupe de ressources pour une app et ajouter une machine virtuelle. Utilisez la commande suivante pour créer le groupe de ressources nommé « myResourceGroup » :

```azurecli
az group create -n MyResourceGroup -l local
```

Si la création du groupe de ressources réussit, la commande précédente affiche les propriétés suivantes pour la ressource créée :

![Sortie de la création du groupe de ressources](media/azure-stack-connect-cli/image1.png)

### <a name="ad-fs-on-windows"></a>[AD FS sur Windows](#tab/adfs-win)

Cette section vous guide dans la configuration de l’interface CLI si vous utilisez Active Directory Federated Services (AD FS) comme service de gestion des identités et si vous utilisez l’interface CLI sur un ordinateur Windows.

#### <a name="connect-to-azure-stack-hub"></a>Se connecter à Azure Stack Hub


1. Si vous utilisez le kit ASDK, approuvez le certificat racine de l’autorité de certification Azure Stack Hub. Pour obtenir des instructions, consultez [Approuver le certificat](../asdk/asdk-cli.md#trust-the-certificate).

2. Inscrivez votre environnement Azure Stack Hub en exécutant la commande `az cloud register`.

3. Inscrivez votre environnement. Utilisez les paramètres suivants lors de l’exécution de `az cloud register` :

    | Valeur | Exemple | Description |
    | --- | --- | --- |
    | Nom de l’environnement | AzureStackUser | Pour l’environnement utilisateur, utilisez `AzureStackUser`. Si vous êtes un opérateur, spécifiez `AzureStackAdmin`. |
    | Point de terminaison Resource Manager | `https://management.local.azurestack.external` | La propriété **ResourceManagerUrl** dans l’ASDK est : `https://management.local.azurestack.external/`**ResourceManagerUrl** dans les systèmes intégrés est : `https://management.<region>.<fqdn>/` Si vous avez une question sur le point de terminaison du système intégré, contactez votre opérateur cloud. |
    | Point de terminaison de stockage | local.azurestack.external | `local.azurestack.external` concerne l’ASDK. Pour un système intégré, utilisez un point de terminaison pour votre système.  |
    | Suffixe du coffre de clés | .vault.local.azurestack.external | `.vault.local.azurestack.external` concerne l’ASDK. Pour un système intégré, utilisez un point de terminaison pour votre système.  |
    | Point de terminaison du document d’alias d’image de machine virtuelle- | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | URI du document, qui contient les alias d’images de machine virtuelle. Pour plus d’informations, consultez [Configurer le point de terminaison d’alias de machines virtuelles](../asdk/asdk-cli.md#set-up-the-virtual-machine-alias-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

4. Définissez l’environnement actif avec les commandes suivantes.

      ```azurecli
      az cloud set -n <environmentname>
      ```

5. Mettez à jour la configuration de votre environnement pour utiliser le profil de version des API propre à Azure Stack Hub. Pour mettre à jour la configuration, exécutez la commande suivante :

    ```azurecli
    az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Si vous exécutez une version d'Azure Stack Hub antérieure à la build 1808, vous devez utiliser le profil de version d'API **2017-03-09-profile** plutôt que le profil de version d'API **2019-03-01-hybrid**. Vous devez également utiliser une version récente de l’interface CLI.

6. Connectez-vous à votre environnement Azure Stack Hub à l'aide de la commande `az login`. Vous pouvez vous connecter à l'environnement Azure Stack Hub en tant qu'utilisateur ou que [principal de service](/azure/active-directory/develop/app-objects-and-service-principals). 

   - Connectez-vous en tant qu’*utilisateur* :

     Vous pouvez spécifier directement le nom d’utilisateur et le mot de passe dans la commande `az login`, ou vous authentifier avec un navigateur. Vous devez choisir cette dernière solution si l’authentification multifacteur est activée sur votre compte :

     ```azurecli
     az cloud register  -n <environmentname>   --endpoint-resource-manager "https://management.local.azurestack.external"  --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>   --profile "2019-03-01-hybrid"
     ```

     > [!NOTE]
     > Si l'authentification multifacteur est activée sur votre compte d'utilisateur, utilisez la commande `az login` sans fournir le paramètre `-u`. L’exécution de cette commande vous donne une URL et un code à utiliser pour vous authentifier.

   - Connectez-vous en tant que *principal de service* : 
    
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

#### <a name="test-the-connectivity"></a>Tester la connectivité

Lorsque tout est configuré, utilisez l'interface CLI pour créer des ressources dans Azure Stack Hub. Par exemple, vous pouvez créer un groupe de ressources pour une app et ajouter une machine virtuelle. Utilisez la commande suivante pour créer le groupe de ressources nommé « myResourceGroup » :

```azurecli
az group create -n MyResourceGroup -l local
```

Si la création du groupe de ressources réussit, la commande précédente affiche les propriétés suivantes pour la ressource créée :

![Sortie de la création du groupe de ressources](media/azure-stack-connect-cli/image1.png)

### <a name="azure-ad-on-linux"></a>[Azure AD sur Linux](#tab/ad-lin)

Cette section vous guide dans la configuration de l’interface CLI si vous utilisez Azure AD comme service de gestion des identités et si vous utilisez l’interface CLI sur un ordinateur Linux.

#### <a name="connect-to-azure-stack-hub"></a>Se connecter à Azure Stack Hub

Suivez les étapes ci-dessous pour vous connecter à Azure Stack Hub :


1. Si vous utilisez le kit ASDK, approuvez le certificat racine de l’autorité de certification Azure Stack Hub. Pour obtenir des instructions, consultez [Approuver le certificat](../asdk/asdk-cli.md#trust-the-certificate).

2. Inscrivez votre environnement Azure Stack Hub en exécutant la commande `az cloud register`.

3. Inscrivez votre environnement. Utilisez les paramètres suivants lors de l’exécution de `az cloud register` :

    | Valeur | Exemple | Description |
    | --- | --- | --- |
    | Nom de l’environnement | AzureStackUser | Pour l’environnement utilisateur, utilisez `AzureStackUser`. Si vous êtes un opérateur, spécifiez `AzureStackAdmin`. |
    | Point de terminaison Resource Manager | `https://management.local.azurestack.external` | La propriété **ResourceManagerUrl** dans l’ASDK est : `https://management.local.azurestack.external/`**ResourceManagerUrl** dans les systèmes intégrés est : `https://management.<region>.<fqdn>/` Si vous avez une question sur le point de terminaison du système intégré, contactez votre opérateur cloud. |
    | Point de terminaison de stockage | local.azurestack.external | `local.azurestack.external` concerne l’ASDK. Pour un système intégré, utilisez un point de terminaison pour votre système.  |
    | Suffixe du coffre de clés | .vault.local.azurestack.external | `.vault.local.azurestack.external` concerne l’ASDK. Pour un système intégré, utilisez un point de terminaison pour votre système.  |
    | Point de terminaison du document d’alias d’image de machine virtuelle- | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | URI du document, qui contient les alias d’images de machine virtuelle. Pour plus d’informations, consultez [Configurer le point de terminaison d’alias de machines virtuelles](../asdk/asdk-cli.md#set-up-the-virtual-machine-alias-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

4. Définissez l’environnement actif. 

      ```azurecli
        az cloud set -n <environmentname>
      ```

5. Mettez à jour la configuration de votre environnement pour utiliser le profil de version des API propre à Azure Stack Hub. Pour mettre à jour la configuration, exécutez la commande suivante :

    ```azurecli
      az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Si vous exécutez une version d'Azure Stack Hub antérieure à la build 1808, vous devez utiliser le profil de version d'API **2017-03-09-profile** plutôt que le profil de version d'API **2019-03-01-hybrid**. Vous devez également utiliser une version récente de l’interface CLI.

6. Connectez-vous à votre environnement Azure Stack Hub à l'aide de la commande `az login`. Vous pouvez vous connecter à l'environnement Azure Stack Hub en tant qu'utilisateur ou que [principal de service](/azure/active-directory/develop/app-objects-and-service-principals). 

   * Connectez-vous en tant qu’*utilisateur* :

     Vous pouvez spécifier directement le nom d’utilisateur et le mot de passe dans la commande `az login`, ou vous authentifier avec un navigateur. Vous devez choisir cette dernière solution si l’authentification multifacteur est activée sur votre compte :

     ```azurecli
     az login \
       -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
       --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > Si l'authentification multifacteur est activée sur votre compte d'utilisateur, vous pouvez utiliser la commande `az login` sans fournir le paramètre `-u`. L’exécution de cette commande vous donne une URL et un code à utiliser pour vous authentifier.
   
   * Connectez-vous en tant que *principal de service*
    
     avant de vous connecter, [créez un principal de service avec le portail Azure](../operator/azure-stack-create-service-principals.md?view=azs-2002) ou l’interface CLI, et attribuez-lui un rôle. Ensuite, connectez-vous avec la commande suivante :

     ```azurecli  
     az login \
       --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
       --service-principal \
       -u <Application Id of the Service Principal> \
       -p <Key generated for the Service Principal>
     ```

#### <a name="test-the-connectivity"></a>Tester la connectivité

Lorsque tout est configuré, utilisez l'interface CLI pour créer des ressources dans Azure Stack Hub. Par exemple, vous pouvez créer un groupe de ressources pour une app et ajouter une machine virtuelle. Utilisez la commande suivante pour créer le groupe de ressources nommé « myResourceGroup » :

```azurecli
    az group create -n MyResourceGroup -l local
```

Si la création du groupe de ressources réussit, la commande précédente affiche les propriétés suivantes pour la ressource créée :

![Sortie de la création du groupe de ressources](media/azure-stack-connect-cli/image1.png)

### <a name="ad-fs-linux"></a>[AD FS sur Linux](#tab/adfs-lin)

Cette section vous guide dans la configuration de l’interface CLI si vous utilisez Active Directory Federated Services (AD FS) comme service de gestion et si vous utilisez l’interface CLI sur un ordinateur Linux.

#### <a name="connect-to-azure-stack-hub"></a>Se connecter à Azure Stack Hub

Suivez les étapes ci-dessous pour vous connecter à Azure Stack Hub :

1. Si vous utilisez le kit ASDK, approuvez le certificat racine de l’autorité de certification Azure Stack Hub. Pour obtenir des instructions, consultez [Approuver le certificat](../asdk/asdk-cli.md#trust-the-certificate).

2. Inscrivez votre environnement Azure Stack Hub en exécutant la commande `az cloud register`.

3. Inscrivez votre environnement. Utilisez les paramètres suivants lors de l’exécution de `az cloud register`.

    | Valeur | Exemple | Description |
    | --- | --- | --- |
    | Nom de l’environnement | AzureStackUser | Pour l’environnement utilisateur, utilisez `AzureStackUser`. Si vous êtes un opérateur, spécifiez `AzureStackAdmin`. |
    | Point de terminaison Resource Manager | `https://management.local.azurestack.external` | La propriété **ResourceManagerUrl** dans l’ASDK est : `https://management.local.azurestack.external/`**ResourceManagerUrl** dans les systèmes intégrés est : `https://management.<region>.<fqdn>/` Si vous avez une question sur le point de terminaison du système intégré, contactez votre opérateur cloud. |
    | Point de terminaison de stockage | local.azurestack.external | `local.azurestack.external` concerne l’ASDK. Pour un système intégré, utilisez un point de terminaison pour votre système.  |
    | Suffixe du coffre de clés | .vault.local.azurestack.external | `.vault.local.azurestack.external` concerne l’ASDK. Pour un système intégré, utilisez un point de terminaison pour votre système.  |
    | Point de terminaison du document d’alias d’image de machine virtuelle- | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | URI du document, qui contient les alias d’images de machine virtuelle. Pour plus d’informations, consultez [Configurer le point de terminaison d’alias de machines virtuelles](../asdk/asdk-cli.md#set-up-the-virtual-machine-alias-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

4. Définissez l’environnement actif. 

      ```azurecli
        az cloud set -n <environmentname>
      ```

5. Mettez à jour la configuration de votre environnement pour utiliser le profil de version des API propre à Azure Stack Hub. Pour mettre à jour la configuration, exécutez la commande suivante :

    ```azurecli
      az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Si vous exécutez une version d'Azure Stack Hub antérieure à la build 1808, vous devez utiliser le profil de version d'API **2017-03-09-profile** plutôt que le profil de version d'API **2019-03-01-hybrid**. Vous devez également utiliser une version récente de l’interface CLI.

6. Connectez-vous à votre environnement Azure Stack Hub à l'aide de la commande `az login`. Vous pouvez vous connecter à l'environnement Azure Stack Hub en tant qu'utilisateur ou que [principal de service](/azure/active-directory/develop/app-objects-and-service-principals). 

7. Connexion : 

   *  En tant **qu’utilisateur** à l’aide d’un navigateur web avec un code d’appareil :  

   ```azurecli  
    az login --use-device-code
   ```

   > [!NOTE]  
   >Elle vous donne une URL et un code qui vous permettent de vous authentifier.

   * En tant que principal de service :
        
     Préparez le fichier .pem à utiliser pour la connexion du principal de service.

      * Sur l’ordinateur client où le principal a été créé, exportez le certificat du principal de service au format pfx avec la clé privée située sous `cert:\CurrentUser\My`. Le certificat porte le même nom que le principal.
  
      * Convertissez le fichier pfx au format pem (avec l’utilitaire OpenSSL).

     Connectez-vous à l’interface CLI :

      ```azurecli  
      az login --service-principal \
        -u <Client ID from the Service Principal details> \
        -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
        --tenant <Tenant ID> \
        --debug 
      ```

#### <a name="test-the-connectivity"></a>Tester la connectivité

Lorsque tout est configuré, utilisez l'interface CLI pour créer des ressources dans Azure Stack Hub. Par exemple, vous pouvez créer un groupe de ressources pour une app et ajouter une machine virtuelle. Utilisez la commande suivante pour créer le groupe de ressources nommé « myResourceGroup » :

```azurecli
  az group create -n MyResourceGroup -l local
```

Si la création du groupe de ressources réussit, la commande précédente affiche les propriétés suivantes pour la ressource créée :

![Sortie de la création du groupe de ressources](media/azure-stack-connect-cli/image1.png)

### <a name="known-issues"></a>Problèmes connus

Différents problèmes liés à l'utilisation de l'interface CLI dans Azure Stack Hub ont été identifiés :

 - Le mode interactif de l’interface CLI. Par exemple, la commande `az interactive` n'est pas encore prise en charge dans Azure Stack Hub.
 - Pour obtenir la liste des images de machines virtuelles disponibles dans Azure Stack Hub, utilisez la commande `az vm image list --all` au lieu de la commande `az vm image list`. En spécifiant l'option `--all`, vous êtes assuré que la réponse renverra uniquement les images disponibles dans votre environnement Azure Stack Hub.
 - Les alias d'images de machines virtuelles disponibles dans Azure ne s'appliquent pas forcément à Azure Stack Hub. Si vous utilisez des images de machines virtuelles, vous devez utiliser la totalité du paramètre URN (Canonical:UbuntuServer:14.04.3-LTS:1.0.0) plutôt que l’alias de l’image. Cet URN doit par ailleurs correspondre aux spécifications des images dérivées de la commande `az vm images list`.

---

## <a name="next-steps"></a>Étapes suivantes

- [Déployer des modèles avec l’interface de ligne de commande Azure](azure-stack-deploy-template-command-line.md)
- [Activer Azure CLI pour les utilisateurs d'Azure Stack Hub (opérateur)](../operator/azure-stack-cli-admin.md)
- [Gérer les autorisations utilisateur](azure-stack-manage-permissions.md) 
