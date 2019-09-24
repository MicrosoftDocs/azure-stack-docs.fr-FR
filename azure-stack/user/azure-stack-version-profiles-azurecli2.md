---
title: Gérer Azure Stack avec l’interface CLI Azure | Microsoft Docs
description: Découvrez comment utiliser l’interface de ligne de commande (CLI) multiplateforme pour gérer et déployer des ressources sur Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/16/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/08/2019
ms.openlocfilehash: 18644d3d331a5c093d0a78da435d6f79e03cb531
ms.sourcegitcommit: 245a4054a52e54d5989d6148fbbe386e1b2aa49c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70974658"
---
# <a name="manage-and-deploy-resources-to-azure-stack-with-azure-cli"></a>Gérer et déployer des ressources sur Azure Stack avec l’interface CLI Azure

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Suivez les étapes de cet article pour configurer l’interface de ligne de commande (CLI) Azure pour gérer les ressources du Kit de développement Azure Stack (ASDK) à partir des plateformes clientes Linux, Mac et Windows.

## <a name="prepare-for-azure-cli"></a>Préparation pour Azure CLI

Si vous utilisez l’ASDK, vous avez besoin du certificat racine de l’autorité de certification pour Azure Stack afin d’utiliser Azure CLI sur votre ordinateur de développement. Vous utilisez le certificat pour gérer des ressources via l’interface CLI.

 - **Le certificat racine de l’autorité de certification Azure Stack** est obligatoire si vous utilisez l’interface CLI sur une station de travail qui se trouve en dehors du Kit de développement Azure Stack.  

 - **Le point de terminaison des alias de la machine virtuelle** fournit un alias, par exemple « UbuntuLTS » ou « Win2012Datacenter ». Cet alias fait référence à un éditeur d'images, une offre, une référence SKU et une version en tant que paramètre unique lors du déploiement de machines virtuelles.  

Les sections suivantes expliquent comment obtenir ces valeurs.

### <a name="export-the-azure-stack-ca-root-certificate"></a>Exporter le certificat racine d’autorité de certification Azure Stack

Si vous utilisez un système intégré, vous n'avez pas besoin d'exporter le certificat racine de l'autorité de certification. Si vous utilisez l'ASDK, exportez le certificat racine de l’autorité de certification sur un ASDK.

Pour exporter le certificat racine ASDK au format PEM :

1. Obtenez le nom de votre certificat racine Azure Stack :
    - Connectez-vous au portail administrateur ou utilisateur Azure Stack.
    - Cliquez sur **Secure** (Sécurisé) près de la barre d’adresse.
    - Dans la fenêtre indépendante, cliquez sur **Valid** (Valide).
    - Dans la fenêtre Certificat, cliquez sur l’onglet **Chemin d’accès de certification**.
    - Notez le nom de votre certificat racine Azure Stack.

    ![Certificat racine Azure Stack](media/azure-stack-version-profiles-azurecli2/root-cert-name.png)

2. [Créez une machine virtuelle Windows sur Azure Stack](azure-stack-quick-windows-portal.md).

3. Connectez-vous à la machine virtuelle, ouvrez une invite PowerShell avec élévation de privilèges et exécutez le script suivant :

    ```powershell  
      $label = "<the name of your azure stack root cert from Step 1>"
      Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
      $root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
      if (-not $root)
      {
          Write-Error "Certificate with subject CN=$label not found"
          return
      }

    Write-Host "Exporting certificate"
    Export-Certificate -Type CERT -FilePath root.cer -Cert $root

    Write-Host "Converting certificate to PEM format"
    certutil -encode root.cer root.pem
    ```

4. Copiez le certificat sur votre machine locale.


### <a name="set-up-the-virtual-machine-aliases-endpoint"></a>Configurer le point de terminaison des alias de machines virtuelles

Vous pouvez configurer un point de terminaison accessible publiquement qui héberge un fichier d’alias de machines virtuelles. Le fichier d’alias de machines virtuelles est un fichier JSON qui fournit un nom commun pour une image. Vous utiliserez ce nom lorsque vous déploierez une machine virtuelle en tant que paramètre Azure CLI.

1. Si vous publiez une image personnalisée, prenez note des informations concernant l’éditeur, l’offre, la référence (SKU) et la version que vous avez spécifiées lors de la publication. S'il s'agit d'une image provenant de la Place de marché, vous pouvez afficher les informations en utilisant la cmdlet ```Get-AzureVMImage```.  

2. Téléchargez l’[ exemple de fichier](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) à partir de GitHub.

3. Créez un compte de stockage dans Azure Stack. Puis créez un conteneur d'objets blob. Définissez la stratégie d’accès sur « publique ».  

4. Chargez le fichier JSON dans le nouveau conteneur. Au terme de cette opération, vous pouvez afficher l'URL de l'objet blob. Sélectionnez le nom de l'objet blob, puis l'URL dans ses propriétés.

### <a name="install-or-upgrade-cli"></a>Installer ou mettre à niveau l’interface CLI

Connectez-vous à votre station de travail de développement et installez l’interface CLI. Azure Stack nécessite la version 2.0 ou ultérieure d’Azure CLI. La dernière version des profils d’API nécessite une version actuelle de l’interface CLI. Vous l’installerez en utilisant la procédure décrite dans l’article [Installer Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

1. Pour vérifier que l’installation a réussi, ouvrez un terminal ou une fenêtre d’invite de commandes, puis exécutez la commande suivante :

    ```shell
    az --version
    ```

    Vous devriez voir la version d’Azure CLI et d’autres bibliothèques dépendantes installées sur votre ordinateur.

    ![Azure CLI sur l’emplacement Python Azure Stack](media/azure-stack-version-profiles-azurecli2/cli-python-location.png)

2. Prenez note de l’emplacement Python de l’interface CLI. Si vous exécutez ASDK, vous avez besoin de cet emplacement pour ajouter votre certificat.


## <a name="windows-azure-ad"></a>Windows (Azure AD)

Cette section vous guide dans la configuration de l’interface CLI si vous utilisez Azure AD comme service de gestion des identités et si vous utilisez l’interface CLI sur un ordinateur Windows.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Approuver le certificat racine d’autorité de certification Azure Stack

Si vous utilisez l’ASDK, vous devez approuver le certificat racine de l’autorité de certification sur votre ordinateur distant. Cette étape n'est pas nécessaire avec les systèmes intégrés.

Pour approuver le certificat racine d’autorité de certification Azure Stack, ajoutez-le au magasin de certificats Python existant pour la version de Python installée avec Azure CLI. Il se peut que vous exécutiez votre propre instance de Python. Azure CLI inclut sa propre version de Python.

1. Trouvez l’emplacement du magasin de certificats sur votre machine.  Vous pouvez trouver l’emplacement en exécutant la commande `az --version`.

2. Accédez au dossier qui contient votre app Python CLI. Vous devez exécuter cette version de Python. Si vous avez configuré Python dans votre chemin d’accès système, l’exécution de Python a pour effet d’exécuter votre propre version de Python. À la place, vous devez exécuter la version utilisée par l’interface CLI et ajouter votre certificat à cette version. Par exemple, votre instance Python CLI peut se trouver à l’emplacement suivant : `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\`.

    Utilisez les commandes suivantes :

    ```powershell  
    cd "c:\pathtoyourcliversionofpython"
    .\python -c "import certifi; print(certifi.where())"
    ```

    Notez l’emplacement du certificat. Par exemple : `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\certifi\cacert.pem`. Votre chemin d’accès particulier dépend de votre système d’exploitation et de votre installation de l’interface CLI.

2. Pour approuver le certificat racine d’autorité de certification Azure Stack, ajoutez-le au certificat Python existant.

    ```powershell
    $pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

    $root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
    $root.Import($pemFile)

    Write-Host "Extracting required information from the cert file"
    $md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
    $sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
    $sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

    $issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
    $subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
    $labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
    $serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
    $md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
    $sha1Entry    = [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
    $sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
    $certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")

    $rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
    $serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

    Write-Host "Adding the certificate content to Python Cert store"
    Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

    Write-Host "Python Cert store was updated to allow the Azure Stack CA root certificate"
    ```

### <a name="connect-to-azure-stack"></a>Se connecter à Azure Stack

1. Inscrivez votre environnement Azure Stack en exécutant la commande `az cloud register`.

    Dans certains scénarios, la connectivité Internet sortante directe est acheminée par l'intermédiaire d'un proxy ou d'un pare-feu, qui assure l'interception SSL. La commande `az cloud register` peut alors échouer avec une erreur de type « Impossible d'obtenir les points de terminaison du cloud ». Pour contourner cette erreur, définissez les variables d'environnement suivantes :

    ```shell  
    set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
    set ADAL_PYTHON_SSL_NO_VERIFY=1
    ```

2. Inscrivez votre environnement. Utilisez les paramètres suivants lors de l’exécution de `az cloud register` :

    | Valeur | Exemples | Description |
    | --- | --- | --- |
    | Nom de l’environnement | AzureStackUser | Pour l’environnement utilisateur, utilisez `AzureStackUser`. Si vous êtes un opérateur, spécifiez `AzureStackAdmin`. |
    | Point de terminaison Resource Manager | https://management.local.azurestack.external | La propriété **ResourceManagerUrl** dans l’ASDK est : `https://management.local.azurestack.external/` **ResourceManagerUrl** dans les systèmes intégrés est : `https://management.<region>.<fqdn>/` Pour récupérer les métadonnées requises : `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` Si vous avez une question sur le point de terminaison du système intégré, contactez votre opérateur cloud. |
    | Point de terminaison de stockage | local.azurestack.external | `local.azurestack.external` concerne l’ASDK. Pour un système intégré, utilisez un point de terminaison pour votre système.  |
    | Suffixe du coffre de clés | .vault.local.azurestack.external | `.vault.local.azurestack.external` concerne l’ASDK. Pour un système intégré, utilisez un point de terminaison pour votre système.  |
    | Point de terminaison du document d’alias d’image de machine virtuelle- | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | URI du document qui contient les alias d’images de machine virtuelle. Pour plus d’informations, consultez [Configurer le point de terminaison des alias de machines virtuelles](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

1. Définissez l’environnement actif avec les commandes suivantes.

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. Mettez à jour la configuration de votre environnement pour utiliser le profil de version des API propre à Azure Stack. Pour mettre à jour la configuration, exécutez la commande suivante :

    ```azurecli
    az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Si vous exécutez une version d’Azure Stack antérieure à la build 1808, vous devez utiliser le profil de version d’API **2017-03-09-profile** plutôt que le profil de version d’API **2019-03-01-hybrid**. Vous devez également utiliser une version récente de l’interface CLI.
 
1. Connectez-vous à votre environnement Azure Stack avec la commande `az login`. Connectez-vous à l’environnement Azure Stack en tant qu’utilisateur ou que [principal de service](/azure/active-directory/develop/app-objects-and-service-principals). 

   - Connectez-vous en tant qu’*utilisateur* : 

     Vous pouvez spécifier directement le nom d’utilisateur et le mot de passe dans la commande `az login`, ou vous authentifier avec un navigateur. Vous devez choisir cette dernière solution si l’authentification multifacteur est activée sur votre compte :

     ```azurecli
     az login -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > Si l'authentification multifacteur est activée sur votre compte d'utilisateur, utilisez la commande `az login` sans fournir le paramètre `-u`. L’exécution de cette commande vous donne une URL et un code à utiliser pour vous authentifier.

   - Connectez-vous en tant que *principal de service* : 
    
     avant de vous connecter, [créez un principal de service avec le portail Azure](azure-stack-create-service-principals.md) ou l’interface CLI, et attribuez-lui un rôle. Ensuite, connectez-vous avec la commande suivante :

     ```azurecli  
     az login --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> --service-principal -u <Application Id of the Service Principal> -p <Key generated for the Service Principal>
     ```

### <a name="test-the-connectivity"></a>Tester la connectivité

Quand tout est configuré, utilisez CLI pour créer des ressources dans Azure Stack. Par exemple, vous pouvez créer un groupe de ressources pour une app et ajouter une machine virtuelle. Utilisez la commande suivante pour créer le groupe de ressources nommé « myResourceGroup » :

```azurecli
az group create -n MyResourceGroup -l local
```

Si la création du groupe de ressources réussit, la commande précédente affiche les propriétés suivantes pour la ressource créée :

![Sortie de la création du groupe de ressources](media/azure-stack-connect-cli/image1.png)

## <a name="windows-ad-fs"></a>Windows (AD FS)

Cette section vous guide dans la configuration de l’interface CLI si vous utilisez Active Directory Federated Services (AD FS) comme service de gestion des identités et si vous utilisez l’interface CLI sur un ordinateur Windows.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Approuver le certificat racine d’autorité de certification Azure Stack

Si vous utilisez l’ASDK, vous devez approuver le certificat racine de l’autorité de certification sur votre ordinateur distant. Cette étape n'est pas nécessaire avec les systèmes intégrés.

1. Trouvez l’emplacement du certificat sur votre machine. L’emplacement peut varier en fonction de l’endroit où vous avez installé Python. Ouvrez une invite de commande ou une invite de PowerShell avec élévation de privilèges et tapez la commande suivante :

    ```powershell  
      python -c "import certifi; print(certifi.where())"
    ```

    Notez l’emplacement du certificat. Par exemple : `~/lib/python3.5/site-packages/certifi/cacert.pem`. Votre chemin d’accès particulier dépend de votre système d’exploitation et de la version de Python que vous avez installée.

2. Pour approuver le certificat racine d’autorité de certification Azure Stack, ajoutez-le au certificat Python existant.

    ```powershell
    $pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

    $root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
    $root.Import($pemFile)

    Write-Host "Extracting required information from the cert file"
    $md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
    $sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
    $sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

    $issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
    $subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
    $labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
    $serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
    $md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
    $sha1Entry    = [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
    $sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
    $certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")

    $rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
    $serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

    Write-Host "Adding the certificate content to Python Cert store"
    Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

    Write-Host "Python Cert store was updated to allow the Azure Stack CA root certificate"
    ```

### <a name="connect-to-azure-stack"></a>Se connecter à Azure Stack

1. Inscrivez votre environnement Azure Stack en exécutant la commande `az cloud register`.

    Dans certains scénarios, la connectivité Internet sortante directe est acheminée par l'intermédiaire d'un proxy ou d'un pare-feu, qui assure l'interception SSL. La commande `az cloud register` peut alors échouer avec une erreur de type « Impossible d'obtenir les points de terminaison du cloud ». Pour contourner cette erreur, définissez les variables d'environnement suivantes :

    ```shell  
    set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
    set ADAL_PYTHON_SSL_NO_VERIFY=1
    ```

2. Inscrivez votre environnement. Utilisez les paramètres suivants lors de l’exécution de `az cloud register` :

    | Valeur | Exemples | Description |
    | --- | --- | --- |
    | Nom de l’environnement | AzureStackUser | Pour l’environnement utilisateur, utilisez `AzureStackUser`. Si vous êtes un opérateur, spécifiez `AzureStackAdmin`. |
    | Point de terminaison Resource Manager | https://management.local.azurestack.external | La propriété **ResourceManagerUrl** dans l’ASDK est : `https://management.local.azurestack.external/` **ResourceManagerUrl** dans les systèmes intégrés est : `https://management.<region>.<fqdn>/` Pour récupérer les métadonnées requises : `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` Si vous avez une question sur le point de terminaison du système intégré, contactez votre opérateur cloud. |
    | Point de terminaison de stockage | local.azurestack.external | `local.azurestack.external` concerne l’ASDK. Pour un système intégré, utilisez un point de terminaison pour votre système.  |
    | Suffixe du coffre de clés | .vault.local.azurestack.external | `.vault.local.azurestack.external` concerne l’ASDK. Pour un système intégré, utilisez un point de terminaison pour votre système.  |
    | Point de terminaison du document d’alias d’image de machine virtuelle- | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | URI du document qui contient les alias d’images de machine virtuelle. Pour plus d’informations, consultez [Configurer le point de terminaison des alias de machines virtuelles](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

1. Définissez l’environnement actif avec les commandes suivantes.

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. Mettez à jour la configuration de votre environnement pour utiliser le profil de version des API propre à Azure Stack. Pour mettre à jour la configuration, exécutez la commande suivante :

    ```azurecli
    az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Si vous exécutez une version d’Azure Stack antérieure à la build 1808, vous devez utiliser le profil de version d’API **2017-03-09-profile** plutôt que le profil de version d’API **2019-03-01-hybrid**. Vous devez également utiliser une version récente de l’interface CLI.

1. Connectez-vous à votre environnement Azure Stack avec la commande `az login`. Vous pouvez vous connecter à l’environnement Azure Stack en tant qu’utilisateur ou que [principal de service](/azure/active-directory/develop/app-objects-and-service-principals). 

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

### <a name="test-the-connectivity"></a>Tester la connectivité

Quand tout est configuré, utilisez CLI pour créer des ressources dans Azure Stack. Par exemple, vous pouvez créer un groupe de ressources pour une app et ajouter une machine virtuelle. Utilisez la commande suivante pour créer le groupe de ressources nommé « myResourceGroup » :

```azurecli
az group create -n MyResourceGroup -l local
```

Si la création du groupe de ressources réussit, la commande précédente affiche les propriétés suivantes pour la ressource créée :

![Sortie de la création du groupe de ressources](media/azure-stack-connect-cli/image1.png)


## <a name="linux-azure-ad"></a>Linux (Azure AD)

Cette section vous guide dans la configuration de l’interface CLI si vous utilisez Azure AD comme service de gestion des identités et si vous utilisez l’interface CLI sur un ordinateur Linux.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Approuver le certificat racine d’autorité de certification Azure Stack

Si vous utilisez l’ASDK, vous devez approuver le certificat racine de l’autorité de certification sur votre ordinateur distant. Cette étape n'est pas nécessaire avec les systèmes intégrés.

Pour approuver le certificat racine d’autorité de certification Azure Stack, ajoutez-le au certificat Python existant.

1. Trouvez l’emplacement du certificat sur votre machine. L’emplacement peut varier en fonction de l’endroit où vous avez installé Python. Vous devez avoir installé pip et le module certifi. Utilisez la commande Python suivante depuis l’invite de commandes bash :

    ```bash  
    python3 -c "import certifi; print(certifi.where())"
    ```

    Notez l’emplacement du certificat. Par exemple : `~/lib/python3.5/site-packages/certifi/cacert.pem`. Votre chemin d’accès spécifique dépend de votre système d’exploitation et de la version de Python que vous avez installée.

2. Exécutez la commande bash suivante avec le chemin d’accès à votre certificat.

   - Pour un ordinateur Linux distant :

     ```bash  
     sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
     ```

   - Pour un ordinateur Linux dans l’environnement Azure Stack :

     ```bash  
     sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
     ```

### <a name="connect-to-azure-stack"></a>Se connecter à Azure Stack

Suivez les étapes ci-dessous pour vous connecter à Azure Stack :

1. Inscrivez votre environnement Azure Stack en exécutant la commande `az cloud register`. Dans certains scénarios, la connectivité Internet sortante directe est acheminée par l'intermédiaire d'un proxy ou d'un pare-feu, qui assure l'interception SSL. La commande `az cloud register` peut alors échouer avec une erreur de type « Impossible d'obtenir les points de terminaison du cloud ». Pour contourner cette erreur, définissez les variables d'environnement suivantes :

   ```shell
   export AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1
   export ADAL_PYTHON_SSL_NO_VERIFY=1
   ```

2. Inscrivez votre environnement. Utilisez les paramètres suivants lors de l’exécution de `az cloud register` :

    | Valeur | Exemples | Description |
    | --- | --- | --- |
    | Nom de l’environnement | AzureStackUser | Pour l’environnement utilisateur, utilisez `AzureStackUser`. Si vous êtes un opérateur, spécifiez `AzureStackAdmin`. |
    | Point de terminaison Resource Manager | https://management.local.azurestack.external | La propriété **ResourceManagerUrl** dans l’ASDK est : `https://management.local.azurestack.external/` **ResourceManagerUrl** dans les systèmes intégrés est : `https://management.<region>.<fqdn>/` Pour récupérer les métadonnées requises : `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` Si vous avez une question sur le point de terminaison du système intégré, contactez votre opérateur cloud. |
    | Point de terminaison de stockage | local.azurestack.external | `local.azurestack.external` concerne l’ASDK. Pour un système intégré, utilisez un point de terminaison pour votre système.  |
    | Suffixe du coffre de clés | .vault.local.azurestack.external | `.vault.local.azurestack.external` concerne l’ASDK. Pour un système intégré, utilisez un point de terminaison pour votre système.  |
    | Point de terminaison du document d’alias d’image de machine virtuelle- | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | URI du document qui contient les alias d’images de machine virtuelle. Pour plus d’informations, consultez [Configurer le point de terminaison des alias de machines virtuelles](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

3. Définissez l’environnement actif. 

      ```azurecli
        az cloud set -n <environmentname>
      ```

4. Mettez à jour la configuration de votre environnement pour utiliser le profil de version des API propre à Azure Stack. Pour mettre à jour la configuration, exécutez la commande suivante :

    ```azurecli
      az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Si vous exécutez une version d’Azure Stack antérieure à la build 1808, vous devez utiliser le profil de version d’API **2017-03-09-profile** plutôt que le profil de version d’API **2019-03-01-hybrid**. Vous devez également utiliser une version récente de l’interface CLI.

5. Connectez-vous à votre environnement Azure Stack avec la commande `az login`. Vous pouvez vous connecter à l’environnement Azure Stack en tant qu’utilisateur ou que [principal de service](/azure/active-directory/develop/app-objects-and-service-principals). 

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
    
     avant de vous connecter, [créez un principal de service avec le portail Azure](azure-stack-create-service-principals.md) ou l’interface CLI, et attribuez-lui un rôle. Ensuite, connectez-vous avec la commande suivante :

     ```azurecli  
     az login \
       --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
       --service-principal \
       -u <Application Id of the Service Principal> \
       -p <Key generated for the Service Principal>
     ```

### <a name="test-the-connectivity"></a>Tester la connectivité

Quand tout est configuré, utilisez CLI pour créer des ressources dans Azure Stack. Par exemple, vous pouvez créer un groupe de ressources pour une app et ajouter une machine virtuelle. Utilisez la commande suivante pour créer le groupe de ressources nommé « myResourceGroup » :

```azurecli
    az group create -n MyResourceGroup -l local
```

Si la création du groupe de ressources réussit, la commande précédente affiche les propriétés suivantes pour la ressource créée :

![Sortie de la création du groupe de ressources](media/azure-stack-connect-cli/image1.png)

## <a name="linux-ad-fs"></a>Linux (AD FS)

Cette section vous guide dans la configuration de l’interface CLI si vous utilisez Active Directory Federated Services (AD FS) comme service de gestion et si vous utilisez l’interface CLI sur un ordinateur Linux.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Approuver le certificat racine d’autorité de certification Azure Stack

Si vous utilisez l’ASDK, vous devez approuver le certificat racine de l’autorité de certification sur votre ordinateur distant. Cette étape n'est pas nécessaire avec les systèmes intégrés.

Pour approuver le certificat racine d’autorité de certification Azure Stack, ajoutez-le au certificat Python existant.

1. Trouvez l’emplacement du certificat sur votre machine. L’emplacement peut varier en fonction de l’endroit où vous avez installé Python. Vous devez avoir installé pip et le module certifi. Utilisez la commande Python suivante depuis l’invite de commandes bash :

    ```bash  
    python3 -c "import certifi; print(certifi.where())"
    ```

    Notez l’emplacement du certificat. Par exemple : `~/lib/python3.5/site-packages/certifi/cacert.pem`. Votre chemin d’accès spécifique dépend de votre système d’exploitation et de la version de Python que vous avez installée.

2. Exécutez la commande bash suivante avec le chemin d’accès à votre certificat.

   - Pour un ordinateur Linux distant :

     ```bash  
     sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
     ```

   - Pour un ordinateur Linux dans l’environnement Azure Stack :

     ```bash  
     sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
     ```

### <a name="connect-to-azure-stack"></a>Se connecter à Azure Stack

Suivez les étapes ci-dessous pour vous connecter à Azure Stack :

1. Inscrivez votre environnement Azure Stack en exécutant la commande `az cloud register`. Dans certains scénarios, la connectivité Internet sortante directe est acheminée par l'intermédiaire d'un proxy ou d'un pare-feu, qui assure l'interception SSL. La commande `az cloud register` peut alors échouer avec une erreur de type « Impossible d'obtenir les points de terminaison du cloud ». Pour contourner cette erreur, définissez les variables d'environnement suivantes :

   ```shell
   export AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1
   export ADAL_PYTHON_SSL_NO_VERIFY=1
   ```

2. Inscrivez votre environnement. Utilisez les paramètres suivants lors de l’exécution de `az cloud register`.

    | Valeur | Exemples | Description |
    | --- | --- | --- |
    | Nom de l’environnement | AzureStackUser | Pour l’environnement utilisateur, utilisez `AzureStackUser`. Si vous êtes un opérateur, spécifiez `AzureStackAdmin`. |
    | Point de terminaison Resource Manager | https://management.local.azurestack.external | La propriété **ResourceManagerUrl** dans l’ASDK est : `https://management.local.azurestack.external/` **ResourceManagerUrl** dans les systèmes intégrés est : `https://management.<region>.<fqdn>/` Pour récupérer les métadonnées requises : `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` Si vous avez une question sur le point de terminaison du système intégré, contactez votre opérateur cloud. |
    | Point de terminaison de stockage | local.azurestack.external | `local.azurestack.external` concerne l’ASDK. Pour un système intégré, utilisez un point de terminaison pour votre système.  |
    | Suffixe du coffre de clés | .vault.local.azurestack.external | `.vault.local.azurestack.external` concerne l’ASDK. Pour un système intégré, utilisez un point de terminaison pour votre système.  |
    | Point de terminaison du document d’alias d’image de machine virtuelle- | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | URI du document qui contient les alias d’images de machine virtuelle. Pour plus d’informations, consultez [Configurer le point de terminaison des alias de machines virtuelles](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

3. Définissez l’environnement actif. 

      ```azurecli
        az cloud set -n <environmentname>
      ```

4. Mettez à jour la configuration de votre environnement pour utiliser le profil de version des API propre à Azure Stack. Pour mettre à jour la configuration, exécutez la commande suivante :

    ```azurecli
      az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Si vous exécutez une version d’Azure Stack antérieure à la build 1808, vous devez utiliser le profil de version d’API **2017-03-09-profile** plutôt que le profil de version d’API **2019-03-01-hybrid**. Vous devez également utiliser une version récente de l’interface CLI.

5. Connectez-vous à votre environnement Azure Stack avec la commande `az login`. Vous pouvez vous connecter à l’environnement Azure Stack en tant qu’utilisateur ou que [principal de service](/azure/active-directory/develop/app-objects-and-service-principals). 

6. Connexion : 

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

### <a name="test-the-connectivity"></a>Tester la connectivité

Quand tout est configuré, utilisez CLI pour créer des ressources dans Azure Stack. Par exemple, vous pouvez créer un groupe de ressources pour une app et ajouter une machine virtuelle. Utilisez la commande suivante pour créer le groupe de ressources nommé « myResourceGroup » :

```azurecli
  az group create -n MyResourceGroup -l local
```

Si la création du groupe de ressources réussit, la commande précédente affiche les propriétés suivantes pour la ressource créée :

![Sortie de la création du groupe de ressources](media/azure-stack-connect-cli/image1.png)

## <a name="known-issues"></a>Problèmes connus

Il existe des problèmes connus liés à l’utilisation de CLI dans Azure Stack :

 - Le mode interactif de l’interface CLI. Par exemple, la commande `az interactive` n’est pas encore prise en charge dans Azure Stack.
 - Pour obtenir la liste des images de machines virtuelles disponibles dans Azure Stack, utilisez la commande `az vm image list --all` au lieu de la commande `az vm image list`. La spécification de l’option `--all` garantit que la réponse retourne uniquement les images disponibles dans votre environnement Azure Stack.
 - Les alias d’images de machines virtuelles disponibles dans Azure ne s’appliquent pas forcément à Azure Stack. Si vous utilisez des images de machines virtuelles, vous devez utiliser la totalité du paramètre URN (Canonical:UbuntuServer:14.04.3-LTS:1.0.0) plutôt que l’alias de l’image. Cet URN doit par ailleurs correspondre aux spécifications des images dérivées de la commande `az vm images list`.

## <a name="next-steps"></a>Étapes suivantes

- [Déployer des modèles avec l’interface de ligne de commande Azure](azure-stack-deploy-template-command-line.md)
- [Activer Azure CLI pour les utilisateurs d’Azure Stack (opérateur)](../operator/azure-stack-cli-admin.md)
- [Gérer les autorisations utilisateur](azure-stack-manage-permissions.md) 
