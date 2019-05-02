---
title: Se connecter à Azure Stack avec l’interface CLI | Microsoft Docs
description: Découvrez comment utiliser l’interface de ligne de commande (CLI) multiplateforme pour gérer et déployer des ressources sur Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 02/28/2019
ms.openlocfilehash: a0f01a70be83a556dfa0f8839711c2de1e7c688e
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "64311363"
---
# <a name="use-api-version-profiles-with-azure-cli-in-azure-stack"></a>Utiliser des profils de version des API avec Azure CLI dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Vous pouvez suivre les étapes de cet article pour configurer l’interface de ligne de commande (CLI) Azure pour gérer les ressources du Kit de développement Azure Stack (ASDK) à partir des plateformes clientes Linux, Mac et Windows.

## <a name="prepare-for-azure-cli"></a>Préparation pour Azure CLI

Vous aurez besoin du certificat racine pour Azure Stack afin d’utiliser Azure CLI sur votre ordinateur de développement. Vous utilisez le certificat pour gérer des ressources via l’interface CLI.

 - **Le certificat racine de l’autorité de certification Azure Stack** est obligatoire si vous utilisez l’interface CLI sur une station de travail qui se trouve en dehors du Kit de développement Azure Stack.  

 - **Le point de terminaison des alias de machines virtuelles** fournit un alias, comme « UbuntuLTS » ou « Win2012Datacenter », qui référence un éditeur, une offre, une référence (SKU) et une version d’image sous la forme d’un seul paramètre lors du déploiement de machines virtuelles.  

Les sections suivantes expliquent comment obtenir ces valeurs.

### <a name="export-the-azure-stack-ca-root-certificate"></a>Exporter le certificat racine d’autorité de certification Azure Stack

Si vous utilisez un système intégré, vous n'avez pas besoin d'exporter le certificat racine de l'autorité de certification. Vous devrez exporter le certificat racine de l'autorité de certification sur un Kit de développement Azure Stack (ASDK).

Pour exporter le certificat racine ASDK au format PEM :

1. [Créez une machine virtuelle Windows sur Azure Stack](azure-stack-quick-windows-portal.md).

2. Connectez-vous à la machine, ouvrez une invite PowerShell avec élévation de privilèges et exécutez le script suivant :

    ```powershell  
      $label = "AzureStackSelfSignedRootCert"
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

3. Copiez le certificat sur votre machine locale.


### <a name="set-up-the-virtual-machine-aliases-endpoint"></a>Configurer le point de terminaison des alias de machines virtuelles

Vous pouvez configurer un point de terminaison accessible publiquement qui héberge un fichier d’alias de machines virtuelles. Le fichier d’alias de machines virtuelles est un fichier JSON qui fournit un nom commun pour une image. Vous utiliserez ce nom lorsque vous déploierez une machine virtuelle en tant que paramètre Azure CLI.

1. Si vous publiez une image personnalisée, prenez note des informations concernant l’éditeur, l’offre, la référence (SKU) et la version que vous avez spécifiées lors de la publication. S'il s'agit d'une image provenant de la Place de marché, vous pouvez afficher les informations en utilisant la cmdlet ```Get-AzureVMImage```.  

2. Téléchargez l’[ exemple de fichier](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) à partir de GitHub.

3. Créez un compte de stockage dans Azure Stack. Puis créez un conteneur d'objets blob. Définissez la stratégie d’accès sur « publique ».  

4. Chargez le fichier JSON dans le nouveau conteneur. Au terme de cette opération, vous pouvez afficher l'URL de l'objet blob. Sélectionnez le nom de l'objet blob, puis l'URL dans ses propriétés.

### <a name="install-or-upgrade-cli"></a>Installer ou mettre à niveau l’interface CLI

Connectez-vous à votre station de travail de développement et installez l’interface CLI. Azure Stack nécessite la version 2.0 ou ultérieure d’Azure CLI. La dernière version des profils d’API nécessite une version actuelle de l’interface CLI.  Vous pouvez l’installer en utilisant la procédure décrite dans l’article [Installer Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). Pour vérifier que l’installation a réussi, ouvrez un terminal ou une fenêtre d’invite de commandes, puis exécutez la commande suivante :

```shell
az --version
```

Vous devriez voir la version d’Azure CLI et d’autres bibliothèques dépendantes installées sur votre ordinateur.

### <a name="install-python-on-windows"></a>Installer Python sur Windows

1. Installez [Python 3 sur votre système](https://www.python.org/downloads/).

2. Mettez à niveau PIP. PIP est un gestionnaire de packages pour Python. Ouvrez une invite de commande ou une invite de PowerShell avec élévation de privilèges et tapez la commande suivante :

    ```powershell  
    python -m pip install --upgrade pip
    ```

3. Installez le module **certifi**. [Certifi](https://pypi.org/project/certifi/) regroupe un module et une collection de certificats racines pour valider la fiabilité des certificats SSL lors de la vérification de l’identité des hôtes TLS. Ouvrez une invite de commande ou une invite de PowerShell avec élévation de privilèges et tapez la commande suivante :

    ```powershell
    pip install certifi
    ```

### <a name="install-python-on-linux"></a>Installer Python sur Linux

1. L’image Ubuntu 16.04 est fournie avec Python 2.7 et Python 3.5 est installé par défaut. Vous pouvez vérifier votre version de Python 3 en exécutant la commande suivante :

    ```bash  
    python3 --version
    ```

2. Mettez à niveau PIP. PIP est un gestionnaire de packages pour Python. Ouvrez une invite de commande ou une invite de PowerShell avec élévation de privilèges et tapez la commande suivante :

    ```bash  
    sudo -H pip3 install --upgrade pip
    ```

3. Installez le module **certifi**. [Certifi](https://pypi.org/project/certifi/) est une collection de certificats racines pour valider la fiabilité des certificats SSL lors de la vérification de l’identité des hôtes TLS. Ouvrez une invite de commande ou une invite de PowerShell avec élévation de privilèges et tapez la commande suivante :

    ```bash
    pip3 install certifi
    ```

### <a name="install-python-on-macos"></a>Installer Python sur macOS

1. Installez [Python 3 sur votre système](https://www.python.org/downloads/). Pour les versions Python 3.7, Python.org fournit deux fichiers d’installation binaires disponibles au téléchargement. La variante par défaut est 64 bits uniquement et fonctionne sur macOS 10.9 (Mavericks) et les systèmes ultérieurs. Vérifiez votre version Python en ouvrant le terminal et en tapant la commande suivante :

    ```bash  
    python3 --version
    ```

2. Mettez à niveau PIP. PIP est un gestionnaire de packages pour Python. Ouvrez une invite de commande ou une invite de PowerShell avec élévation de privilèges et tapez la commande suivante :

    ```bash  
    sudo -H pip3 install --upgrade pip
    ```

3. Installez le module **certifi**. [Certifi](https://pypi.org/project/certifi/) regroupe un module et une collection de certificats racines pour valider la fiabilité des certificats SSL lors de la vérification de l’identité des hôtes TLS. Ouvrez une invite de commande ou une invite de PowerShell avec élévation de privilèges et tapez la commande suivante :

    ```bash
    pip3 install certifi
    ```

## <a name="windows-azure-ad"></a>Windows (Azure AD)

Cette section vous guidera dans la configuration de l’interface CLI si vous utilisez Azure AD comme service de gestion des identités et si vous utilisez l’interface CLI sur un ordinateur Windows.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Approuver le certificat racine d’autorité de certification Azure Stack

Si vous utilisez l’ASDK, vous devez approuver le certificat racine de l’autorité de certification sur votre ordinateur distant. Cette opération n’est pas nécessaire sur les systèmes intégrés.

Pour approuver le certificat racine d’autorité de certification Azure Stack, ajoutez-le au certificat Python existant.

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

    Dans certains scénarios, la connectivité Internet sortante directe est acheminée par l'intermédiaire d'un proxy ou d'un pare-feu, qui assure l'interception SSL. La commande `az cloud register` peut alors échouer avec une erreur de type « Impossible d'obtenir les points de terminaison du cloud ». Pour contourner cette erreur, vous pouvez définir les variables d'environnement suivantes :

    ```shell  
    set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
    set ADAL_PYTHON_SSL_NO_VERIFY=1
    ```

2. Inscrivez votre environnement. Utilisez les paramètres suivants lors de l’exécution de `az cloud register`.

    | Valeur | Exemples | Description |
    | --- | --- | --- |
    | Nom de l’environnement | AzureStackUser | Pour l’environnement utilisateur, utilisez `AzureStackUser`. Si vous êtes un opérateur, spécifiez `AzureStackAdmin`. |
    | Point de terminaison Resource Manager | https://management.local.azurestack.external | Le **ResourceManagerUrl** dans le Kit de développement Azure Stack (ASDK) est : `https://management.local.azurestack.external/` **ResourceManagerUrl** dans les systèmes intégrés est : `https://management.<region>.<fqdn>/` Pour récupérer les métadonnées requises : `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` Si vous avez une question sur le point de terminaison du système intégré, contactez votre opérateur cloud. |
    | Point de terminaison de stockage | local.azurestack.external | `local.azurestack.external` concerne l’ASDK. Pour un système intégré, vous devez utiliser un point de terminaison pour votre système.  |
    | Suffixe de Keyvalut | .vault.local.azurestack.external | `.vault.local.azurestack.external` concerne l’ASDK. Pour un système intégré, vous devez utiliser un point de terminaison pour votre système.  |
    | Point de terminaison du document d’alias d’image de machine virtuelle- | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | URI du document qui contient les alias d’images de machine virtuelle. Pour plus d’informations, consultez [### Configurer le point de terminaison des alias de machine virtuelle](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

1. Définissez l’environnement actif avec les commandes suivantes.

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. Mettez à jour la configuration de votre environnement pour utiliser le profil de version des API propre à Azure Stack. Pour mettre à jour la configuration, exécutez la commande suivante :

    ```azurecli
    az cloud update --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >Si vous exécutez une version d’Azure Stack antérieure à la build 1808, vous devez utiliser le profil de version API **2017-03-09-profile** plutôt que le profil de version API **2018-03-01-hybrid**. Vous devez utiliser une version récente de l’interface CLI.
 
1. Connectez-vous à votre environnement Azure Stack avec la commande `az login`. Vous pouvez vous connecter à l’environnement Azure Stack en tant qu’utilisateur ou que [principal de service](/azure/active-directory/develop/app-objects-and-service-principals). 

   - Connectez-vous en tant qu’*utilisateur* : 

     Vous pouvez spécifier directement le nom d’utilisateur et le mot de passe dans la commande `az login`, ou vous authentifier avec un navigateur. Vous devez choisir cette dernière solution si l’authentification multifacteur est activée sur votre compte :

     ```azurecli
     az login -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > Si l'authentification multifacteur est activée sur votre compte d'utilisateur, vous pouvez utiliser la commande `az login` sans fournir le paramètre `-u`. L’exécution de cette commande vous donne une URL et un code à utiliser pour vous authentifier.

   - Connectez-vous en tant que *principal de service* : 
    
     avant de vous connecter, [créez un principal de service avec le portail Azure](azure-stack-create-service-principals.md) ou l’interface CLI, et attribuez-lui un rôle. Ensuite, connectez-vous avec la commande suivante :

     ```azurecli  
     az login --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> --service-principal -u <Application Id of the Service Principal> -p <Key generated for the Service Principal>
     ```

### <a name="test-the-connectivity"></a>Tester la connectivité

Quand tout est configuré, utilisez CLI pour créer des ressources dans Azure Stack. Par exemple, vous pouvez créer un groupe de ressources pour une application et ajouter une machine virtuelle. Utilisez la commande suivante pour créer le groupe de ressources nommé « myResourceGroup » :

```azurecli
az group create -n MyResourceGroup -l local
```

Si la création du groupe de ressources réussit, la commande précédente affiche les propriétés suivantes pour la ressource créée :

![Sortie de la création du groupe de ressources](media/azure-stack-connect-cli/image1.png)

## <a name="windows-ad-fs"></a>Windows (AD FS)

Cette section vous guidera dans la configuration de l’interface CLI si vous utilisez Active Directory Federated Services (AD FS) comme service de gestion des identités et si vous utilisez l’interface CLI sur un ordinateur Windows.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Approuver le certificat racine d’autorité de certification Azure Stack

Si vous utilisez l’ASDK, vous devez approuver le certificat racine de l’autorité de certification sur votre ordinateur distant. Cette opération n’est pas nécessaire sur les systèmes intégrés.

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

    Dans certains scénarios, la connectivité Internet sortante directe est acheminée par l'intermédiaire d'un proxy ou d'un pare-feu, qui assure l'interception SSL. La commande `az cloud register` peut alors échouer avec une erreur de type « Impossible d'obtenir les points de terminaison du cloud ». Pour contourner cette erreur, vous pouvez définir les variables d'environnement suivantes :

    ```shell  
    set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
    set ADAL_PYTHON_SSL_NO_VERIFY=1
    ```

2. Inscrivez votre environnement. Utilisez les paramètres suivants lors de l’exécution de `az cloud register`.

    | Valeur | Exemples | Description |
    | --- | --- | --- |
    | Nom de l’environnement | AzureStackUser | Pour l’environnement utilisateur, utilisez `AzureStackUser`. Si vous êtes un opérateur, spécifiez `AzureStackAdmin`. |
    | Point de terminaison Resource Manager | https://management.local.azurestack.external | Le **ResourceManagerUrl** dans le Kit de développement Azure Stack (ASDK) est : `https://management.local.azurestack.external/` **ResourceManagerUrl** dans les systèmes intégrés est : `https://management.<region>.<fqdn>/` Pour récupérer les métadonnées requises : `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` Si vous avez une question sur le point de terminaison du système intégré, contactez votre opérateur cloud. |
    | Point de terminaison de stockage | local.azurestack.external | `local.azurestack.external` concerne l’ASDK. Pour un système intégré, vous devez utiliser un point de terminaison pour votre système.  |
    | Suffixe de Keyvalut | .vault.local.azurestack.external | `.vault.local.azurestack.external` concerne l’ASDK. Pour un système intégré, vous devez utiliser un point de terminaison pour votre système.  |
    | Point de terminaison du document d’alias d’image de machine virtuelle- | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | URI du document qui contient les alias d’images de machine virtuelle. Pour plus d’informations, consultez [### Configurer le point de terminaison des alias de machine virtuelle](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

1. Définissez l’environnement actif avec les commandes suivantes.

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. Mettez à jour la configuration de votre environnement pour utiliser le profil de version des API propre à Azure Stack. Pour mettre à jour la configuration, exécutez la commande suivante :

    ```azurecli
    az cloud update --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >Si vous exécutez une version d’Azure Stack antérieure à la build 1808, vous devez utiliser le profil de version API **2017-03-09-profile** plutôt que le profil de version API **2018-03-01-hybrid**. Vous devez utiliser une version récente de l’interface CLI.

1. Connectez-vous à votre environnement Azure Stack avec la commande `az login`. Vous pouvez vous connecter à l’environnement Azure Stack en tant qu’utilisateur ou que [principal de service](/azure/active-directory/develop/app-objects-and-service-principals). 

   - Connectez-vous en tant qu’*utilisateur* :

     Vous pouvez spécifier directement le nom d’utilisateur et le mot de passe dans la commande `az login`, ou vous authentifier avec un navigateur. Vous devez choisir cette dernière solution si l’authentification multifacteur est activée sur votre compte :

     ```azurecli
     az cloud register  -n <environmentname>   --endpoint-resource-manager "https://management.local.azurestack.external"  --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-active-directory-resource-id "https://management.adfs.azurestack.local/<tenantID>" --endpoint-active-directory-graph-resource-id "https://graph.local.azurestack.external/" --endpoint-active-directory "https://adfs.local.azurestack.external/adfs/" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>   --profile "2018-03-01-hybrid"
     ```

     > [!NOTE]
     > Si l'authentification multifacteur est activée sur votre compte d'utilisateur, vous pouvez utiliser la commande `az login` sans fournir le paramètre `-u`. L’exécution de cette commande vous donne une URL et un code à utiliser pour vous authentifier.

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

Quand tout est configuré, utilisez CLI pour créer des ressources dans Azure Stack. Par exemple, vous pouvez créer un groupe de ressources pour une application et ajouter une machine virtuelle. Utilisez la commande suivante pour créer le groupe de ressources nommé « myResourceGroup » :

```azurecli
az group create -n MyResourceGroup -l local
```

Si la création du groupe de ressources réussit, la commande précédente affiche les propriétés suivantes pour la ressource créée :

![Sortie de la création du groupe de ressources](media/azure-stack-connect-cli/image1.png)


## <a name="linux-azure-ad"></a>Linux (Azure AD)

Cette section vous guidera dans la configuration de l’interface CLI si vous utilisez Azure AD comme service de gestion des identités et si vous utilisez l’interface CLI sur un ordinateur Linux.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Approuver le certificat racine d’autorité de certification Azure Stack

Si vous utilisez l’ASDK, vous devez approuver le certificat racine de l’autorité de certification sur votre ordinateur distant. Cette opération n’est pas nécessaire sur les systèmes intégrés.

Pour approuver le certificat racine d’autorité de certification Azure Stack, ajoutez-le au certificat Python existant.

1. Trouvez l’emplacement du certificat sur votre machine. L’emplacement peut varier en fonction de l’endroit où vous avez installé Python. pip et le module [certifi doivent être installés](#install-python-on-linux). Vous pouvez utiliser la commande Python suivante depuis l’invite de commandes bash :

    ```bash  
    python3 -c "import certifi; print(certifi.where())"
    ```

    Notez l’emplacement du certificat, par exemple, `~/lib/python3.5/site-packages/certifi/cacert.pem`. Votre chemin d’accès spécifique dépend de votre système d’exploitation et de la version de Python que vous avez installée.

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

1. Inscrivez votre environnement Azure Stack en exécutant la commande `az cloud register`. Dans certains scénarios, la connectivité Internet sortante directe est acheminée par l'intermédiaire d'un proxy ou d'un pare-feu, qui assure l'interception SSL. La commande `az cloud register` peut alors échouer avec une erreur de type « Impossible d'obtenir les points de terminaison du cloud ». Pour contourner cette erreur, vous pouvez définir les variables d'environnement suivantes :

   ```shell
   set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
   set ADAL_PYTHON_SSL_NO_VERIFY=1
   ```

2. Inscrivez votre environnement. Utilisez les paramètres suivants lors de l’exécution de `az cloud register`.

    | Valeur | Exemples | Description |
    | --- | --- | --- |
    | Nom de l’environnement | AzureStackUser | Pour l’environnement utilisateur, utilisez `AzureStackUser`. Si vous êtes un opérateur, spécifiez `AzureStackAdmin`. |
    | Point de terminaison Resource Manager | https://management.local.azurestack.external | Le **ResourceManagerUrl** dans le Kit de développement Azure Stack (ASDK) est : `https://management.local.azurestack.external/` **ResourceManagerUrl** dans les systèmes intégrés est : `https://management.<region>.<fqdn>/` Pour récupérer les métadonnées requises : `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` Si vous avez une question sur le point de terminaison du système intégré, contactez votre opérateur cloud. |
    | Point de terminaison de stockage | local.azurestack.external | `local.azurestack.external` concerne l’ASDK. Pour un système intégré, vous devez utiliser un point de terminaison pour votre système.  |
    | Suffixe de Keyvalut | .vault.local.azurestack.external | `.vault.local.azurestack.external` concerne l’ASDK. Pour un système intégré, vous devez utiliser un point de terminaison pour votre système.  |
    | Point de terminaison du document d’alias d’image de machine virtuelle- | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | URI du document qui contient les alias d’images de machine virtuelle. Pour plus d’informations, consultez [### Configurer le point de terminaison des alias de machine virtuelle](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

3. Définissez l’environnement actif. 

      ```azurecli
        az cloud set -n <environmentname>
      ```

4. Mettez à jour la configuration de votre environnement pour utiliser le profil de version des API propre à Azure Stack. Pour mettre à jour la configuration, exécutez la commande suivante :

    ```azurecli
      az cloud update --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >Si vous exécutez une version d’Azure Stack antérieure à la build 1808, vous devez utiliser le profil de version API **2017-03-09-profile** plutôt que le profil de version API **2018-03-01-hybrid**. Vous devez utiliser une version récente de l’interface CLI.

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

Quand tout est configuré, utilisez CLI pour créer des ressources dans Azure Stack. Par exemple, vous pouvez créer un groupe de ressources pour une application et ajouter une machine virtuelle. Utilisez la commande suivante pour créer le groupe de ressources nommé « myResourceGroup » :

```azurecli
    az group create -n MyResourceGroup -l local
```

Si la création du groupe de ressources réussit, la commande précédente affiche les propriétés suivantes pour la ressource créée :

![Sortie de la création du groupe de ressources](media/azure-stack-connect-cli/image1.png)

## <a name="linux-ad-fs"></a>Linux (AD FS)

Cette section vous guidera dans la configuration de l’interface CLI si vous utilisez Active Directory Federated Services (AD FS) comme service de gestion et si vous utilisez l’interface CLI sur un ordinateur Linux.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Approuver le certificat racine d’autorité de certification Azure Stack

Si vous utilisez l’ASDK, vous devez approuver le certificat racine de l’autorité de certification sur votre ordinateur distant. Cette opération n’est pas nécessaire sur les systèmes intégrés.

Pour approuver le certificat racine d’autorité de certification Azure Stack, ajoutez-le au certificat Python existant.

1. Trouvez l’emplacement du certificat sur votre machine. L’emplacement peut varier en fonction de l’endroit où vous avez installé Python. pip et le module [certifi doivent être installés](#install-python-on-linux). Vous pouvez utiliser la commande Python suivante depuis l’invite de commandes bash :

    ```bash  
    python3 -c "import certifi; print(certifi.where())"
    ```

    Notez l’emplacement du certificat, par exemple, `~/lib/python3.5/site-packages/certifi/cacert.pem`. Votre chemin d’accès spécifique dépend de votre système d’exploitation et de la version de Python que vous avez installée.

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

1. Inscrivez votre environnement Azure Stack en exécutant la commande `az cloud register`. Dans certains scénarios, la connectivité Internet sortante directe est acheminée par l'intermédiaire d'un proxy ou d'un pare-feu, qui assure l'interception SSL. La commande `az cloud register` peut alors échouer avec une erreur de type « Impossible d'obtenir les points de terminaison du cloud ». Pour contourner cette erreur, vous pouvez définir les variables d'environnement suivantes :

   ```shell
   set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
   set ADAL_PYTHON_SSL_NO_VERIFY=1
   ```

2. Inscrivez votre environnement. Utilisez les paramètres suivants lors de l’exécution de `az cloud register`.

    | Valeur | Exemples | Description |
    | --- | --- | --- |
    | Nom de l’environnement | AzureStackUser | Pour l’environnement utilisateur, utilisez `AzureStackUser`. Si vous êtes un opérateur, spécifiez `AzureStackAdmin`. |
    | Point de terminaison Resource Manager | https://management.local.azurestack.external | Le **ResourceManagerUrl** dans le Kit de développement Azure Stack (ASDK) est : `https://management.local.azurestack.external/` **ResourceManagerUrl** dans les systèmes intégrés est : `https://management.<region>.<fqdn>/` Pour récupérer les métadonnées requises : `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` Si vous avez une question sur le point de terminaison du système intégré, contactez votre opérateur cloud. |
    | Point de terminaison de stockage | local.azurestack.external | `local.azurestack.external` concerne l’ASDK. Pour un système intégré, vous devez utiliser un point de terminaison pour votre système.  |
    | Suffixe de Keyvalut | .vault.local.azurestack.external | `.vault.local.azurestack.external` concerne l’ASDK. Pour un système intégré, vous devez utiliser un point de terminaison pour votre système.  |
    | Point de terminaison du document d’alias d’image de machine virtuelle- | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | URI du document qui contient les alias d’images de machine virtuelle. Pour plus d’informations, consultez [### Configurer le point de terminaison des alias de machine virtuelle](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

3. Définissez l’environnement actif. 

      ```azurecli
        az cloud set -n <environmentname>
      ```

4. Mettez à jour la configuration de votre environnement pour utiliser le profil de version des API propre à Azure Stack. Pour mettre à jour la configuration, exécutez la commande suivante :

    ```azurecli
      az cloud update --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >Si vous exécutez une version d’Azure Stack antérieure à la build 1808, vous devez utiliser le profil de version API **2017-03-09-profile** plutôt que le profil de version API **2018-03-01-hybrid**. Vous devez utiliser une version récente de l’interface CLI.

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

Quand tout est configuré, utilisez CLI pour créer des ressources dans Azure Stack. Par exemple, vous pouvez créer un groupe de ressources pour une application et ajouter une machine virtuelle. Utilisez la commande suivante pour créer le groupe de ressources nommé « myResourceGroup » :

```azurecli
  az group create -n MyResourceGroup -l local
```

Si la création du groupe de ressources réussit, la commande précédente affiche les propriétés suivantes pour la ressource créée :

![Sortie de la création du groupe de ressources](media/azure-stack-connect-cli/image1.png)

## <a name="known-issues"></a>Problèmes connus

Il existe des problèmes connus liés à l’utilisation de CLI dans Azure Stack :

 - Le mode interactif CLI ; par exemple, la commande `az interactive` n’est pas encore prise en charge dans Azure Stack.
 - Pour obtenir la liste des images de machines virtuelles disponibles dans Azure Stack, utilisez la commande `az vm image list --all` au lieu de la commande `az vm image list`. La spécification de l’option `--all` garantit que la réponse retourne uniquement les images disponibles dans votre environnement Azure Stack.
 - Les alias d’images de machines virtuelles disponibles dans Azure ne s’appliquent pas forcément à Azure Stack. Si vous utilisez des images de machines virtuelles, vous devez utiliser la totalité du paramètre URN (Canonical:UbuntuServer:14.04.3-LTS:1.0.0) plutôt que l’alias de l’image. Cet URN doit par ailleurs correspondre aux spécifications des images dérivées de la commande `az vm images list`.

## <a name="next-steps"></a>Étapes suivantes

- [Déployer des modèles avec l’interface de ligne de commande Azure](azure-stack-deploy-template-command-line.md)
- [Activer Azure CLI pour les utilisateurs d’Azure Stack (opérateur)](../operator/azure-stack-cli-admin.md)
- [Gérer les autorisations utilisateur](azure-stack-manage-permissions.md) 
