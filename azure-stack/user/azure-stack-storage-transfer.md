---
title: Utiliser des outils de transfert de données pour le stockage Azure Stack | Microsoft Docs
description: En savoir plus sur les outils de transfert de données du stockage Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 12/03/2018
ms.openlocfilehash: 92e0c1a3bfd055373ffba5091d79922f1203b20b
ms.sourcegitcommit: 71d7990a2b21576c44bb2aea13ae2026e9510c55
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70188030"
---
# <a name="use-data-transfer-tools-in-azure-stack-storage"></a>Utiliser des outils de transfert de données pour le stockage Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Azure Stack fournit un ensemble de services de stockage pour les disques, les blobs, les tables, les files d’attente, ainsi que des fonctions de gestion de compte. Certains outils de stockage Azure si vous voulez gérer des données ou les déplacer vers ou à partir du stockage Azure Stack. Cet article fournit une vue d’ensemble des outils disponibles.

Vos exigences déterminent lequel des outils suivants est le mieux pour vous :

* [AZCopy](#azcopy)

    Un utilitaire de ligne de commande propre au stockage que vous pouvez télécharger pour copier des données d’un objet vers un autre au sein ou entre vos comptes de stockage.

* [Azure PowerShell](#azure-powershell)

    Interpréteur de ligne de commande basé sur les tâches et langage de génération de scripts conçu spécialement pour l’administration de systèmes.

* [Interface de ligne de commande Azure](#azure-cli)

    Outil multiplateforme open source qui fournit un ensemble de commandes pour utiliser les plateformes Azure et Azure Stack.

* [Explorateur de stockage Microsoft](#microsoft-azure-storage-explorer)

    Application autonome, facile à utiliser, avec une interface utilisateur.

* [Blobfuse](#blobfuse)

    Un pilote de système de fichiers virtuel pour Stockage Blob Azure, qui vous permet d’accéder à vos données d’objet blob de blocs dans votre compte de stockage via le système de fichiers Linux.

En raison des différences des services de stockage Azure et Azure Stack, chaque outil décrit dans les sections suivantes peut avoir des exigences spécifiques. Pour une comparaison entre le stockage Azure Stack et le stockage Azure, consultez [Stockage Azure Stack : différences et considérations](azure-stack-acs-differences.md).

## <a name="azcopy"></a>AzCopy

AzCopy est un utilitaire de ligne de commande conçu pour copier des données à partir et vers le stockage Blob et Table Microsoft Azure à l’aide de commandes simples avec des performances optimales. Vous pouvez copier des données d’un objet vers un autre au sein ou entre vos comptes de stockage.

### <a name="download-and-install-azcopy"></a>Téléchargement et installation d’AzCopy

::: moniker range=">=azs-1811"
* Pour la mise à jour 1811 ou des versions plus récentes, [téléchargez AzCopy V10+](/azure/storage/common/storage-use-azcopy-v10#download-azcopy).
::: moniker-end

::: moniker range="<azs-1811"
* Pour des versions antérieures (mises à jour 1802 à 1809), [téléchargez AzCopy 7.1.0](https://aka.ms/azcopyforazurestack20170417).
::: moniker-end

### <a name="azcopy-101-configuration-and-limits"></a>Configuration et limites d’AzCopy 10.1

Vous pouvez désormais configurer AzCopy 10.1 pour utiliser des versions d’API antérieures. Cela permet une prise en charge (limitée) pour Azure Stack.
Pour configurer la version d’API pour AzCopy afin de prendre en charge Azure Stack, définissez la variable d’environnement `AZCOPY_DEFAULT_SERVICE_API_VERSION` sur `2017-11-09`.

| Système d’exploitation | Commande  |
|--------|-----------|
| **Windows** | Dans une invite de commandes, tapez : `set AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09`<br> Pour PowerShell, tapez : `$env:AZCOPY_DEFAULT_SERVICE_API_VERSION="2017-11-09"`|
| **Linux** | `export AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09` |
| **MacOS** | `export AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09` |

Dans AzCopy 10.1, les fonctionnalités suivantes sont prises en charge pour Azure Stack :

| Fonctionnalité | Actions prises en charge |
| --- | --- |
|Gérer un conteneur|Créez un conteneur.<br>Afficher le contenu de conteneurs
|Gérer un travail|Afficher des travaux<br>Reprise d’une tâche
|Supprimer un objet blob|Supprimer un seul objet blob<br>Supprimer un annuaire virtuel entier ou partiel
|Charger le fichier|Charger un fichier<br>Charger un annuaire<br>Charger le contenu d’un annuaire
|Télécharger un fichier|Téléchargement d’un fichier<br>Télécharger un annuaire<br>Téléharger le contenu d’un annuaire
|Synchroniser un fichier|Synchroniser un conteneur sur un système de fichiers local<br>Synchroniser un système de fichiers local sur un conteneur

   > [!NOTE]
   > * Azure Stack ne prend pas en charge la fourniture d’informations d’identification d’autorisation à AzCopy à l’aide d’Azure Active Directory (AD). Vous devez accéder à des objets de stockage sur Azure Stack en utilisant un jeton de signature d’accès partagé (SAP).
   > * Azure Stack ne prend pas en charge le transfert de données synchrone entre deux emplacements de blob Azure Stack ou entre Stockage Azure et Azure Stack. Vous ne pouvez pas utiliser la commande « azcopy cp » pour déplacer des données d’Azure Stack vers Stockage Azure (ou dans le sens contraire) directement avec AzCopy 10.1.

### <a name="azcopy-command-examples-for-data-transfer"></a>Exemples de commandes AzCopy pour le transfert de données

Les exemples suivants présentent des scénarios permettant de copier des données vers et à partir d’objets blob Azure Stack. Pour en savoir plus, voir [Bien démarrer avec AzCopy](/azure/storage/common/storage-use-azcopy-v10).

### <a name="download-all-blobs-to-a-local-disk"></a>Télécharger tous les objets blob sur un disque local

```
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "/path/to/dir" --recursive=true
```

### <a name="upload-single-file-to-virtual-directory"></a>Télécharger un fichier unique dans le répertoire virtuel

```
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

### <a name="azcopy-known-issues"></a>Problèmes connus d’AzCopy

 - Toute opération AzCopy sur un stockage de fichiers n’est pas disponible, car le stockage de fichiers n’est pas encore disponible dans Azure Stack.
 - Si vous souhaitez transférer des données entre deux emplacements blob Azure Stack, ou entre Azure Stack et Stockage Azure à l’aide d’AzCopy 10.1, vous devez d’abord télécharger les données vers un emplacement local, puis les charger à nouveau dans le répertoire cible sur Azure Stack ou Stockage Azure. Vous pouvez également utiliser AzCopy 7.1 en spécifiant le transfert avec l’option **/SyncCopy** pour copier les données.  
 - La version Linux d’AzCopy prend uniquement en charge la mise à jour 1802 ou les versions ultérieures et ne prend pas en charge le service de Table.
 
## <a name="azure-powershell"></a>Azure PowerShell

Le module Azure PowerShell fournit des applets de commande pour gérer des services sur Azure et Azure Stack. Il s’agit d’un interpréteur en ligne de commande basé sur les tâches et d’un langage de génération de scripts conçu spécialement pour l’administration de systèmes.

### <a name="install-and-configure-powershell-for-azure-stack"></a>Installer et configurer PowerShell pour Azure Stack

Des modules Azure PowerShell compatibles avec Azure Stack sont nécessaires pour utiliser Azure Stack. Pour plus d’informations, consultez [Installer PowerShell pour Azure Stack](../operator/azure-stack-powershell-install.md) et [Configurer l’environnement PowerShell de l’utilisateur Azure Stack](azure-stack-powershell-configure-user.md).

### <a name="powershell-sample-script-for-azure-stack"></a>Exemple de script PowerShell pour Azure Stack 

Cet exemple suppose que vous avez [installé PowerShell pour Azure Stack](../operator/azure-stack-powershell-install.md) avec succès. Ce script vous permet d’effectuer la configuration et de demander à votre locataire Azure Stack des informations d’identification pour ajouter votre compte à l’environnement PowerShell local. Ensuite, le script définit l’abonnement Azure par défaut, crée un nouveau compte de stockage dans Azure, crée un conteneur dans ce nouveau compte de stockage et charge un fichier image existant (blob) dans ce conteneur. Une fois que le script répertorie tous les objets blob de ce conteneur, il crée un répertoire de destination sur votre ordinateur local et télécharge le fichier image.

1. Installez les [modules Azure PowerShell compatibles avec Azure Stack](../operator/azure-stack-powershell-install.md).
2. Téléchargez les [outils nécessaires pour utiliser Azure Stack](../operator/azure-stack-powershell-download.md).
3. Ouvrez **Windows PowerShell ISE** et **Exécuter en tant qu’administrateur**, puis cliquez sur **Fichier** > **Nouveau** pour créer un nouveau fichier script.
4. Copiez le script ci-dessous et collez-le sur le nouveau fichier script.
5. Mettez à jour les variables du script en fonction de vos paramètres de configuration.
   > [!NOTE]
   > Ce script doit être exécuté dans le répertoire racine pour **AzureStack_Tools**.

```powershell  
# begin

$ARMEvnName = "AzureStackUser" # set AzureStackUser as your Azure Stack environment name
$ARMEndPoint = "https://management.local.azurestack.external" 
$GraphAudience = "https://graph.windows.net/" 
$AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com" 

$SubscriptionName = "basic" # Update with the name of your subscription.
$ResourceGroupName = "myTestRG" # Give a name to your new resource group.
$StorageAccountName = "azsblobcontainer" # Give a name to your new storage account. It must be lowercase.
$Location = "Local" # Choose "Local" as an example.
$ContainerName = "photo" # Give a name to your new container.
$ImageToUpload = "C:\temp\Hello.jpg" # Prepare an image file and a source directory in your local computer.
$DestinationFolder = "C:\temp\download" # A destination directory in your local computer.

# Import the Connect PowerShell module"
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser -Force
Import-Module .\Connect\AzureStack.Connect.psm1

# Configure the PowerShell environment
# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRmEnvironment -Name $ARMEvnName -ARMEndpoint $ARMEndPoint 

# Set the GraphEndpointResourceId value
Set-AzureRmEnvironment -Name $ARMEvnName -GraphEndpoint $GraphAudience

# Login
$TenantID = Get-AzsDirectoryTenantId -AADTenantName $AADTenantName -EnvironmentName $ARMEvnName
Add-AzureRmAccount -EnvironmentName $ARMEvnName -TenantId $TenantID 

# Set a default Azure subscription.
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Create a new Resource Group 
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

# Create a new storage account.
New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Location $Location -Type Standard_LRS

# Set a default storage account.
Set-AzureRmCurrentStorageAccount -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName 

# Create a new container.
New-AzureStorageContainer -Name $ContainerName -Permission Off

# Upload a blob into a container.
Set-AzureStorageBlobContent -Container $ContainerName -File $ImageToUpload

# List all blobs in a container.
Get-AzureStorageBlob -Container $ContainerName

# Download blobs from the container:
# Get a reference to a list of all blobs in a container.
$blobs = Get-AzureStorageBlob -Container $ContainerName

# Create the destination directory.
New-Item -Path $DestinationFolder -ItemType Directory -Force  

# Download blobs into the local destination directory.
$blobs | Get-AzureStorageBlobContent -Destination $DestinationFolder

# end
```

### <a name="powershell-known-issues"></a>Problèmes connus de PowerShell

La version actuelle du module Azure PowerShell compatible avec Azure Stack est 1.2.11 pour les opérations utilisateur. Elle est différente de la dernière version d’Azure PowerShell. Cette différence impacte l’opération de service de stockage de la manière suivante :

Le format de la valeur renvoyée de `Get-AzureRmStorageAccountKey` dans la version 1.2.11 comporte deux propriétés, `Key1` et `Key2`, alors que la version actuelle d’Azure renvoie un tableau contenant toutes les clés de compte.

```powershell
# This command gets a specific key for a storage account, 
# and works for Azure PowerShell version 1.4, and later versions.
(Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
-AccountName "MyStorageAccount").Value[0]

# This command gets a specific key for a storage account, 
# and works for Azure PowerShell version 1.3.2, and previous versions.
(Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
-AccountName "MyStorageAccount").Key1
```

Pour plus d’informations, consultez [Get-AzureRmStorageAccountKey](/powershell/module/azurerm.storage/Get-AzureRmStorageAccountKey).

## <a name="azure-cli"></a>D’Azure CLI

L’interface de ligne de commande Azure (Azure CLI) est l’expérience de ligne de commande d’Azure pour gérer les ressources Azure. Vous pouvez l’installer sur Windows, Linux et macOS, et l’exécuter à partir de la ligne de commande.

Azure CLI est optimisé pour la gestion et l’administration des ressources Azure à partir de la ligne de commande, et pour la création de scripts d’automatisation qui opèrent sur Azure Resource Manager. Elle offre de nombreuses fonctionnalités similaires à celles du portail Azure Stack, notamment l’accès étendu aux données.

Azure Stack nécessite la version 2.0 d’Azure CLI ou ultérieure. Pour plus d’informations sur l’installation et la configuration d’Azure CLI avec Azure Stack, consultez [Installer et configurer Azure Stack CLI](azure-stack-version-profiles-azurecli2.md). Pour plus d’informations sur l’utilisation d’Azure CLI pour effectuer plusieurs tâches fonctionnant avec des ressources dans votre compte de stockage Azure Stack, consultez [Utilisation d’Azure CLI avec le Stockage Azure](/azure/storage/storage-azure-cli).

### <a name="azure-cli-sample-script-for-azure-stack"></a>Exemple de script Azure CLI pour Azure Stack

Une fois que vous avez terminé l’installation et la configuration de l’interface CLI, vous pouvez essayer les étapes suivantes pour utiliser un petit exemple de script shell afin d’interagir avec les ressources de stockage Azure Stack. Le script complète les actions suivantes :

* Crée un nouveau conteneur dans votre compte de stockage.
* Télécharge un fichier existant (comme un objet blob) sur le conteneur.
* Dresse la liste de tous les objets blob du conteneur.
* Télécharge le fichier vers une destination sur votre ordinateur local que vous spécifiez.

Avant d’exécuter ce script, vérifiez que vous pouvez vous connecter et vous enregistrer sur Azure Stack cible avec succès.

1. Ouvrez votre éditeur de texte préféré, puis copiez et collez le script précédent dans l’éditeur.
2. Mettez à jour les variables du script pour refléter vos paramètres de configuration.
3. Une fois que vous avez mis à jour les variables nécessaires, enregistrez le script et quittez l’éditeur. Les étapes suivantes supposent que vous avez nommé votre script **my_storage_sample.sh**.
4. Rendez le script exécutable, si nécessaire : `chmod +x my_storage_sample.sh`
5. Exécutez le script. Par exemple, dans Bash : `./my_storage_sample.sh`

```azurecli
#!/bin/bash
# A simple Azure Stack storage example script

export AZURESTACK_RESOURCE_GROUP=<resource_group_name>
export AZURESTACK_RG_LOCATION="local"
export AZURESTACK_STORAGE_ACCOUNT_NAME=<storage_account_name>
export AZURESTACK_STORAGE_CONTAINER_NAME=<container_name>
export AZURESTACK_STORAGE_BLOB_NAME=<blob_name>
export FILE_TO_UPLOAD=<file_to_upload>
export DESTINATION_FILE=<destination_file>

echo "Creating the resource group..."
az group create --name $AZURESTACK_RESOURCE_GROUP --location $AZURESTACK_RG_LOCATION

echo "Creating the storage account..."
az storage account create --name $AZURESTACK_STORAGE_ACCOUNT_NAME --resource-group $AZURESTACK_RESOURCE_GROUP --account-type Standard_LRS

echo "Creating the blob container..."
az storage container create --name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME

echo "Uploading the file..."
az storage blob upload --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --file $FILE_TO_UPLOAD --name $AZURESTACK_STORAGE_BLOB_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME

echo "Listing the blobs..."
az storage blob list --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME --output table

echo "Downloading the file..."
az storage blob download --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME --name $AZURESTACK_STORAGE_BLOB_NAME --file $DESTINATION_FILE --output table

echo "Done"
```

## <a name="microsoft-azure-storage-explorer"></a>Explorateur Stockage Microsoft Azure

L’Explorateur de stockage Azure est une application autonome de Microsoft. Ceci vous permet d’utiliser facilement des données du stockage Azure et du stockage Azure Stack sur des ordinateurs Windows, macOS et Linux. Si vous voulez un moyen simple de gérer les données de votre stockage Azure Stack, utilisez l’Explorateur Stockage Microsoft Azure.

* Pour en savoir plus sur la configuration de l’Explorateur Stockage Azure afin d’utiliser Azure Stack, consultez [Connecter l’Explorateur Stockage à un abonnement Azure Stack](azure-stack-storage-connect-se.md).
* Pour en savoir sur l’Explorateur Stockage Microsoft Azure, consultez la rubrique [Prise en main de l’Explorateur de stockage](/azure/vs-azure-tools-storage-manage-with-storage-explorer)

## <a name="blobfuse"></a>Blobfuse 

[Blobfuse](https://github.com/Azure/azure-storage-fuse) est un pilote de système de fichiers virtuel pour le Stockage Blob Azure, qui vous permet d’accéder à vos données d’objet blob de blocs dans votre compte de stockage via le système de fichiers Linux. Le Stockage Blob Azure est un service de stockage d’objets qui n’a pas d’espace de noms hiérarchique. Blobfuse fournit cet espace de noms grâce au schéma de répertoire virtuel qui utilise la barre oblique (`/`) comme délimiteur. Blobfuse fonctionne à la fois sur Azure et sur Azure Stack. 

Pour plus d’informations sur le montage du stockage Blob en tant que système de fichiers avec Blobfuse sur Linux, consultez [Guide pratique pour monter le stockage Blob en tant que système de fichiers avec Blobfuse](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux). 

Pour Azure Stack, *blobEndpoint* doit être spécifié lors de la configuration de vos informations d’identification du compte de stockage avec accountName, accountKey/sasToken et containerName.

Dans le Kit de développement Azure Stack (ASDK), le *blobEndpoint* doit être `myaccount.blob.local.azurestack.external`. Dans le système intégré Azure Stack, contactez votre administrateur cloud si vous n’êtes pas sûr de votre point de terminaison.

Notez que vous ne pouvez pas configurer à la fois *accountKey* et *sasToken*. Lorsqu’une clé de compte de stockage est spécifiée, le fichier de configuration des informations d’identification est au format suivant :

```
accountName myaccount 
accountKey myaccesskey== 
containerName mycontainer 
blobEndpoint myaccount.blob.local.azurestack.external
```

Quand un jeton d’accès partagé est spécifié, le fichier de configuration des informations d’identification est au format suivant :

```  
accountName myaccount 
sasToken ?mysastoken 
containerName mycontainer 
blobEndpoint myaccount.blob.local.azurestack.external
```

## <a name="next-steps"></a>Étapes suivantes

* [Connecter l’Explorateur de stockage à un abonnement Azure Stack](azure-stack-storage-connect-se.md)
* [Prise en main de l’Explorateur de stockage](/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Stockage Azure : Différences et considérations](azure-stack-acs-differences.md)
* [Introduction au stockage Microsoft Azure](/azure/storage/common/storage-introduction)
