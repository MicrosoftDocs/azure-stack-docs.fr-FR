---
title: Vous configurez des certificats pour Azure CLI sur le Kit de développement Azure Stack (ASDK) ? | Microsoft Docs
description: Découvrez comment configurer des certificats pour Azure CLI sur le Kit de développement Azure Stack.
author: mattbriggs
ms.topic: how-to
ms.date: 10/26/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/26/2020
ms.openlocfilehash: 8b55fea8ed9692da34234e2633bdb43e92de46eb
ms.sourcegitcommit: b960df16e84ec9fbccfce772102b91f0b7ae7060
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93294423"
---
# <a name="setting-up-certificates-for-azure-cli-on-azure-stack-development-kit"></a>Configurer des certificats pour Azure CLI sur le Kit de développement Azure Stack

Suivez les étapes de cet article pour configurer l’interface de ligne de commande (CLI) Azure pour gérer les ressources du Kit de développement Azure Stack (ASDK) à partir des plateformes clientes Linux, Mac et Windows.

Cet article traite de l’obtention et de l’approbation de vos certificats sur votre ordinateur de gestion distant. Pour installer Azure CLI et vous connecter à votre environnement, consultez [Installer Azure CLI sur Azure Stack Hub](/azure-stack/user/azure-stack-version-profiles-azurecli2).

## <a name="prepare-for-azure-cli"></a>Préparation pour Azure CLI

Pour le kit ASDK, vous devez disposer du certificat d'autorité de certification racine Azure Stack Hub afin d’utiliser Azure CLI sur votre ordinateur de développement. Vous utilisez le certificat pour gérer des ressources via l’interface CLI.

 - **Le certificat d'autorité de certification racine Azure Stack Hub** est obligatoire si vous utilisez l'interface CLI sur une station de travail qui se trouve en dehors du kit ASDK.  

 - **Le point de terminaison des alias de la machine virtuelle** fournit un alias, par exemple « UbuntuLTS » ou « Win2012Datacenter ». Cet alias fait référence à un éditeur d'images, une offre, une référence SKU et une version en tant que paramètre unique lors du déploiement de machines virtuelles.  

Les sections suivantes expliquent comment obtenir ces valeurs.

## <a name="export-the-azure-stack-hub-ca-root-certificate"></a>Exporter le certificat de l'autorité de certification racine Azure Stack Hub

Pour utiliser Azure CLI avec le kit ASDK, exportez le certificat racine de l’autorité de certification.

Pour exporter le certificat racine ASDK au format PEM :

1. Obtenez le nom de votre certificat racine Azure Stack Hub :
    1. Connectez-vous au portail administrateur ou utilisateur Azure Stack Hub.
    2. Sélectionnez **Sécuriser** près de la barre d’adresse.
    3. Dans la fenêtre contextuelle qui s’affiche, sélectionnez **Valide**.
    4. Dans la fenêtre Certificat, sélectionnez **Chemin d’accès de certification**.
    5. Notez le nom de votre certificat racine Azure Stack Hub.

    ![Certificat racine Azure Stack Hub](../user/media/azure-stack-version-profiles-azurecli2/root-cert-name.png)

2. [Créez une machine virtuelle Windows sur Azure Stack Hub](../user/azure-stack-quick-windows-portal.md).

3. Connectez-vous à la machine virtuelle, ouvrez une invite PowerShell avec élévation de privilèges et exécutez le script suivant :

    ```powershell  
      $label = "<the name of your Azure Stack Hub root cert from Step 1>"
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

## <a name="set-up-the-virtual-machine-alias-endpoint"></a>Configurer le point de terminaison d’alias de machines virtuelles

Vous pouvez configurer un point de terminaison accessible publiquement qui héberge un fichier d’alias de machines virtuelles. Le fichier d’alias de machines virtuelles est un fichier JSON qui fournit un nom commun pour une image. Vous utiliserez ce nom lorsque vous déploierez une machine virtuelle en tant que paramètre Azure CLI.

1. Si vous publiez une image personnalisée, prenez note des informations concernant l’éditeur, l’offre, la référence (SKU) et la version que vous avez spécifiées lors de la publication. S'il s'agit d'une image provenant de la Place de marché, vous pouvez afficher les informations en utilisant la cmdlet ```Get-AzureVMImage```.  

2. Téléchargez l’[ exemple de fichier](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) à partir de GitHub.

3. Créez un compte de stockage dans Azure Stack Hub. Puis créez un conteneur d'objets blob. Définissez la stratégie d’accès sur « publique ».  

4. Chargez le fichier JSON dans le nouveau conteneur. Au terme de cette opération, vous pouvez afficher l'URL de l'objet blob. Sélectionnez le nom de l'objet blob, puis l'URL dans ses propriétés.


## <a name="trust-the-certificate"></a>Approuver le certificat

Pour utiliser Azure CLI avec le kit ASDK, vous devez approuver le certificat racine de l’autorité de certification sur votre ordinateur distant.

### <a name="windows"></a>[Windows](#tab/win)

1. Trouvez l’emplacement du certificat sur votre machine. L’emplacement peut varier en fonction de l’endroit où vous avez installé Python. Ouvrez une invite de commande ou une invite de PowerShell avec élévation de privilèges et tapez la commande suivante :

    ```powershell  
      python -c "import certifi; print(certifi.where())"
    ```

    Notez l’emplacement du certificat. Par exemple, `~/lib/python3.5/site-packages/certifi/cacert.pem`. Votre chemin d’accès particulier dépend de votre système d’exploitation et de la version de Python que vous avez installée.

2. Pour approuver le certificat d'autorité de certification racine Azure Stack Hub, ajoutez-le au certificat Python existant.

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

    Write-Host "Python Cert store was updated to allow the Azure Stack Hub CA root certificate"
    ```

Pour obtenir des instructions sur l’installation et la connexion à Azure CLI, consultez [Installer Azure CLI sur Azure Stack Hub](/azure-stack/user/azure-stack-version-profiles-azurecli2).

### <a name="linux"></a>[Linux](#tab/lin)

Lorsque vous configurer l’interface CLI, approuvez le certificat d'autorité de certification racine Azure Stack Hub en l’ajoutant au certificat Python existant.

1. Trouvez l’emplacement du certificat sur votre machine. L’emplacement peut varier en fonction de l’endroit où vous avez installé Python. Vous devez avoir installé pip et le module certifi. Utilisez la commande Python suivante depuis l’invite de commandes bash :

    ```bash  
    az --version
    ```

    Notez l’emplacement du certificat. Par exemple, `~/lib/python3.5/site-packages/certifi/cacert.pem`. Votre chemin d’accès spécifique dépend de votre système d’exploitation et de la version de Python que vous avez installée.

2. Exécutez la commande bash suivante avec le chemin d’accès à votre certificat.

   - Pour un ordinateur Linux distant :

     ```bash  
     sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
     ```

   - Pour un ordinateur Linux situé dans l'environnement Azure Stack Hub :

     ```bash  
     sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
     ```

Pour obtenir des instructions sur l’installation et la connexion à Azure CLI, consultez [Installer Azure CLI sur Azure Stack Hub](/azure-stack/user/azure-stack-version-profiles-azurecli2).

---

## <a name="next-steps"></a>Étapes suivantes

[Installer Azure CLI sur Azure Stack Hub](../user/azure-stack-version-profiles-azurecli2.md)