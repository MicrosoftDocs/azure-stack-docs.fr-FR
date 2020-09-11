---
title: Utiliser des profils de version d’API avec Python dans Azure Stack Hub
description: Découvrez comment utiliser des profils de version d’API avec Python dans Azure Stack Hub.
author: sethmanheim
ms.topic: article
ms.date: 09/03/2020
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: 88c2ecd1448a210daddd1d7da34ffea03c67a6b1
ms.sourcegitcommit: 7c01ab4b2e2250a7acd67d1c5ba27d15c1e8bce0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89448637"
---
# <a name="use-api-version-profiles-with-python-in-azure-stack-hub"></a>Utiliser des profils de version d’API avec Python dans Azure Stack Hub

Le Kit de développement logiciel (SDK) Python prend en charge les profils de version d’API pour cibler différentes plateformes cloud telles qu’Azure Stack Hub et Azure global. Utilisez des profils d’API dans la création de solutions pour un cloud hybride.

Les instructions de cet article nécessitent un abonnement Microsoft Azure. Si vous n’en avez pas, vous pouvez obtenir un [compte d’essai gratuit](https://go.microsoft.com/fwlink/?LinkId=330212).

## <a name="python-and-api-version-profiles"></a>Python et les profils de version d’API

Le Kit de développement logiciel (SDK) Python prend en charge les profils d’API suivants :

- **le plus récent**  
    Ce profil cible les dernières versions d’API de tous les fournisseurs de services de la plateforme Azure.
- **2019-03-01-hybrid**  
    Ce profil cible les dernières versions d’API de tous les fournisseurs de ressources de la plateforme Azure Stack Hub pour les versions 1904 ou ultérieures.
- **2018-03-01-hybrid**  
    Ce profil cible les versions d’API les plus compatibles pour tous les fournisseurs de ressources de la plateforme Azure Stack Hub.
- **2017-03-09-profile**  
    Ce profil cible les versions d’API les plus compatibles des fournisseurs de ressources pris en charge par Azure Stack Hub.

   Pour plus d’informations sur les profils d’API et Azure Stack Hub, voir [Gérer les profils de version des API dans Azure Stack Hub](azure-stack-version-profiles.md).

## <a name="install-the-azure-python-sdk"></a>Installer le kit SDK Azure Python

1. [Installer Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
2. Pour des instructions sur l’installation du SDK Python, consultez [Azure pour développeurs Python](/python/azure/python-sdk-azure-install?view=azure-python).
3. Si vous n’en avez pas de disponible, créez un abonnement et enregistrez l’ID d’abonnement pour l’utiliser plus tard. Pour des instructions sur la création d’un abonnement, voir [Créer des abonnements pour des offres dans Azure Stack Hub](../operator/azure-stack-subscribe-plan-provision-vm.md).
4. Créez un principal de service et enregistrez son ID et son secret. Pour plus d’informations sur la création d’un principal de service pour Azure Stack Hub, voir [Fournir à des applications l’accès à Azure Stack Hub](../operator/azure-stack-create-service-principals.md).
5. Vérifiez que votre principal de service bénéficie du rôle contributeur/propriétaire sur votre abonnement. Pour plus d’informations sur l’attribution d’un rôle à votre principal de service, voir [Fournir à des applications l’accès à Azure Stack Hub](../operator/azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Prérequis

Pour pouvoir utiliser le Kit de développement logiciel (SDK) Azure pour Python avec Azure Stack Hub, vous devez fournir les valeurs suivantes, puis les définir avec des variables d’environnement. Pour définir les variables d’environnement, consultez les instructions fournies après le tableau et qui correspondent à votre système d’exploitation spécifique.

| Valeur | Variables d'environnement | Description |
|---------------------------|-----------------------|-------------------------------------------------------------------------------------------------------------------------|
| ID client | `AZURE_TENANT_ID` | Votre [ID de locataire](../operator/azure-stack-identity-overview.md) Azure Stack Hub. |
| ID client | `AZURE_CLIENT_ID` | L’ID d’application du principal de service enregistré lors de la création du principal de service dans la section précédente de cet article. |
| Identifiant d’abonnement | `AZURE_SUBSCRIPTION_ID` | Vous utilisez l’[ID d’abonnement](../operator/service-plan-offer-subscription-overview.md#subscriptions) pour accéder à des offres dans Azure Stack Hub. |
| Clé secrète client | `AZURE_CLIENT_SECRET` | Le secret d’application du principal de service enregistré lors de la création du principal de service. |
| Point de terminaison Resource Manager | `ARM_ENDPOINT` | Voir l’article [Point de terminaison Azure Stack Hub Resource Manager](azure-stack-version-profiles-ruby.md#the-azure-stack-hub-resource-manager-endpoint). |
| Emplacement de la ressource | `AZURE_RESOURCE_LOCATION` | Emplacement de la ressource de votre environnement Azure Stack Hub.

### <a name="trust-the-azure-stack-hub-ca-root-certificate"></a>Approuver le certificat d’autorité de certification racine d’Azure Stack Hub

Si vous utilisez le kit ASDK, vous devez approuver explicitement le certificat racine de l’autorité de certification sur votre machine distante. Vous n’avez pas besoin d’approuver le certificat d’autorité de certification racine avec des systèmes intégrés Azure Stack Hub.

#### <a name="windows"></a>Windows

1. Trouvez l’emplacement du magasin de certificats Python sur votre machine. Cet emplacement peut varier en fonction de l’endroit où vous avez installé Python. Ouvrez une invite de commandes ou une invite PowerShell avec élévation des privilèges, puis tapez la commande suivante :

    ```PowerShell  
      python -c "import certifi; print(certifi.where())"
    ```

    Notez l’emplacement du magasin de certificats ; par exemple, **~/lib/python3.5/site-packages/certifi/cacert.pem**. Votre chemin particulier dépend de votre système d’exploitation et de la version de Python que vous avez installée.

2. Pour approuver le certificat d’autorité de certification racine Azure Stack Hub, ajoutez-le au certificat Python existant :

    ```powershell
    $pemFile = "<Fully qualified path to the PEM certificate; for ex: C:\Users\user1\Downloads\root.pem>"

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
    Add-Content "${env:ProgramFiles(x86)}\Python35\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

    Write-Host "Python Cert store was updated to allow the Azure Stack Hub CA root certificate"
    ```

> [!NOTE]  
> Si vous utilisez **virtualenv** dans le cadre du développement avec le SDK Python comme indiqué dans la section [Exécuter l’exemple Python](#run-the-python-sample) suivante, vous devez ajouter le certificat précédent au magasin de certificats de votre environnement virtuel. Le chemin peut se présenter comme suit : `..\mytestenv\Lib\site-packages\certifi\cacert.pem`.

## <a name="python-samples-for-azure-stack-hub"></a>Exemples Python pour Azure Stack Hub

Parmi les exemples de code disponibles pour Azure Stack Hub utilisant le Kit de développement logiciel (SDK) Python, citons :

- [Gérer des ressources et des groupes de ressources](https://azure.microsoft.com/resources/samples/hybrid-resourcemanager-python-manage-resources/)
- [Gérer un compte de stockage](https://azure.microsoft.com/resources/samples/hybrid-storage-python-manage-storage-account/)
- [Gérer des machines virtuelles](https://azure.microsoft.com/resources/samples/hybrid-compute-python-manage-vm/) : cet exemple utilise le profil **2019-03-01-hybrid** qui cible les dernières versions d’API prises en charge par Azure Stack Hub.

## <a name="manage-virtual-machine-sample"></a>Exemple de gestion de machines virtuelles

Utilisez l’exemple de code Python suivant pour effectuer des tâches de gestion courantes pour des machines virtuelles de votre Azure Stack Hub. L’exemple de code vous montre comment :

- Créer des machines virtuelles :
  - Créer une machine virtuelle Linux
  - Créer une machine virtuelle Windows
- Mettre à jour une machine virtuelle :
  - Étendre un disque
  - Ajouter des balises à une machine virtuelle
  - Connecter des disques de données
  - Détacher des disques de données
- Opérer une machine virtuelle :
  - Démarrer une machine virtuelle
  - Arrêter une machine virtuelle
  - Redémarrer une machine virtuelle
- Énumérer les machines virtuelles
- Supprimer une machine virtuelle

Pour passer en revue le code qui effectue ces opérations, consultez la fonction **run_example()** du script Python **example.py** dans le dépôt GitHub [Hybrid-Compute-Python-Manage-VM](https://github.com/Azure-Samples/Hybrid-Compute-Python-Manage-VM).

Chaque opération est clairement commentée et présente une fonction d’impression. Les exemples ne sont pas forcément fournis dans l’ordre indiqué dans cette liste.

## <a name="run-the-python-sample"></a>Exécuter l’exemple Python

1. Si vous ne l’avez pas déjà fait, [installez Python](https://www.python.org/downloads/). Cet exemple, ainsi que le SDK, sont compatibles avec Python 2.7, 3.4, 3.5 et 3.6.

2. Il est généralement conseillé d’utiliser un environnement virtuel pour le développement Python. Pour plus d’informations, consultez la [documentation de Python](https://docs.python.org/3/tutorial/venv.html).

3. Installez et initialisez l’environnement virtuel avec le module [venv](https://pypi.python.org/pypi/virtualenv) sur Python 3 (vous devez installer **virtualenv** sur Python 2.7) :

    ```bash
    python -m venv mytestenv # Might be "python3" or "py -3.6" depending on your Python installation
    cd mytestenv
    source bin/activate      # Linux shell (Bash, ZSH, etc.) only
    ./scripts/activate       # PowerShell only
    ./scripts/activate.bat   # Windows CMD only
    ```

4. Clonez le référentiel :

    ```bash
    git clone https://github.com/Azure-Samples/Hybrid-Compute-Python-Manage-VM.git
    ```

5. Installez les dépendances à l’aide de **pip** :

    ```bash
    cd Hybrid-Compute-Python-Manage-VM
    pip install -r requirements.txt
    ```

6. Créez un [principal de service](../operator/azure-stack-create-service-principals.md) à utiliser avec Azure Stack Hub. Vérifiez que votre principal de service bénéficie du [rôle contributeur/propriétaire](../operator/azure-stack-create-service-principals.md#assign-a-role) sur votre abonnement.

7. Définissez les variables d’environnement suivantes, puis exportez-les dans votre environnement actuel :

    ```bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    export AZURE_RESOURCE_LOCATION={your AzureStack Resource location}
    ```

8. Pour exécuter cet exemple, les images Ubuntu 16.04-LTS et WindowsServer 2012-R2-DataCenter doivent être présentes sur la Place de marché Azure Stack Hub. Ces images peuvent être [téléchargées à partir d’Azure](../operator/azure-stack-download-azure-marketplace-item.md) ou ajoutées au [dépôt d’images de la plateforme](../operator/azure-stack-add-vm-image.md).

9. Exécutez l’exemple :

    ```python
    python example.py
    ```

## <a name="next-steps"></a>Étapes suivantes

- [Centre de développement Azure Python](https://azure.microsoft.com/develop/python/)
- [Documentation sur les machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/)
- [Parcours d’apprentissage des machines virtuelles](/learn/paths/deploy-a-website-with-azure-virtual-machines/)
