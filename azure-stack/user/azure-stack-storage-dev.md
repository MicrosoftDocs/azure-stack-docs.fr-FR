---
title: Bien démarrer avec les outils de développement du stockage Azure Stack Hub
description: Guide de démarrage pour l’utilisation des outils de développement du stockage Azure Stack Hub
author: mattbriggs
ms.author: mabrigg
ms.date: 1/22/2020
ms.topic: conceptual
ms.reviewer: xiaofmao
ms.lastreviewed: 02/27/2019
ms.openlocfilehash: 939479350718ae2176f7d1531e64ad71301e0596
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77701307"
---
# <a name="get-started-with-azure-stack-hub-storage-development-tools"></a>Bien démarrer avec les outils de développement du stockage Azure Stack Hub

L’infrastructure Microsoft Azure Stack Hub fournit un ensemble de services de stockage incluant le stockage d’objets blob, de tables et de files d’attente.

Utilisez cet article comme un guide de démarrage concernant l’utilisation des outils de développement du stockage Azure Stack Hub. Des informations plus détaillées et des exemples de code sont disponibles dans les didacticiels correspondants du Stockage Azure.

> [!NOTE]  
> Le stockage Azure Stack Hub et le stockage Azure présentent quelques différences, notamment certaines exigences propres à chaque plateforme. Par exemple, Azure Stack Hub a des bibliothèques clientes spécifiques, de même que des exigences en matière de suffixe de point de terminaison. Pour plus d’informations, consultez [Stockage Azure Stack Hub : différences et considérations](azure-stack-acs-differences.md).

## <a name="azure-client-libraries"></a>Bibliothèques clientes Azure

Par conséquent, pour les bibliothèques clientes de stockage, vous devez connaître la version qui est compatible avec l’API REST. Vous devez également préciser le point de terminaison Azure Stack Hub dans votre code.

::: moniker range=">=azs-1811"
### <a name="1811-update-or-newer-versions"></a>Mise à jour 1811 ou plus récente

| Bibliothèque cliente | Version prise en charge par Azure Stack Hub | Lien | Spécification du point de terminaison |
|----------------|-------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET | 9.2.0 | Package NuGet :<br><https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0><br> <br>Version de GitHub :<br><https://github.com/Azure/azure-storage-net/releases/tag/v9.2.0> | Fichier app.config |
| Java | 7.0.0 | Package Maven :<br><https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/7.0.0><br> <br>Version de GitHub :<br><https://github.com/Azure/azure-storage-java/releases/tag/v7.0.0> | Configuration de la chaîne de connexion |
| Node.js | 2.8.3 | Lien NPM :<br><https://www.npmjs.com/package/azure-storage><br>(Exécuter : `npm install azure-storage@2.8.3`)<br> <br>Version de GitHub :<br><https://github.com/Azure/azure-storage-node/releases/tag/v2.8.3> | Déclaration d’instance de service |
| C++ | 5.2.0 | Package NuGet :<br><https://www.nuget.org/packages/Microsoft.Azure.Storage.CPP.v140/5.2.0><br> <br>Version de GitHub :<br><https://github.com/Azure/azure-storage-cpp/releases/tag/v5.2.0> | Configuration de la chaîne de connexion |
| PHP | 1.2.0 | Version de GitHub :<br>Courant : <https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-common><br>Objet blob : <https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-blob><br>File d’attente :<br><https://github.com/Azure/azure-storage-php/releases/tag/v1.1.1-queue><br>Table : <https://github.com/Azure/azure-storage-php/releases/tag/v1.1.0-table><br> <br>Installer via Composer (pour en savoir plus, [voir les détails ci-dessous](#install-php-client-via-composer---current).) | Configuration de la chaîne de connexion |
| Python | 1.1.0 | Version de GitHub :<br>Courant :<br><https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-common><br>Blob :<br><https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-blob><br>File d’attente :<br><https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-queue> | Déclaration d’instance de service |
| Ruby | 1.0.1 | Package RubyGems :<br>Courant :<br><https://rubygems.org/gems/azure-storage-common/versions/1.0.1><br>Objet blob : <https://rubygems.org/gems/azure-storage-blob/versions/1.0.1><br>File d’attente : <https://rubygems.org/gems/azure-storage-queue/versions/1.0.1><br>Table : <https://rubygems.org/gems/azure-storage-table/versions/1.0.1><br> <br>Version de GitHub :<br>Courant : <https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common><br>Objet blob : <https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob><br>File d’attente : <https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-queue><br>Table : <https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-table> | Configuration de la chaîne de connexion |

#### <a name="install-php-client-via-composer---current"></a>Installer le client PHP via Composer - actuel

Pour installer via Composer : (prenez l’objet blob en guise d’exemple).

1. Créez un fichier nommé **composer.json** à la racine du projet avec le code suivant :

    ```json
    {
      "require": {
      "Microsoft/azure-storage-blob":"1.2.0"
      }
    }
    ```

2. Téléchargez [composer.phar](https://getcomposer.org/composer.phar) à la racine du projet.
3. Exécutez : `php composer.phar install`.
::: moniker-end

::: moniker range=">=azs-1802 <=azs-1809"
### <a name="previous-versions-1802-to-1809-update"></a>Versions précédentes (mises à jour 1802 à 1809)

| Bibliothèque cliente | Version prise en charge par Azure Stack Hub | Lien | Spécification du point de terminaison |
|----------------|-------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET | 8.7.0 | Package NuGet :<br><https://www.nuget.org/packages/WindowsAzure.Storage/8.7.0><br> <br>Version de GitHub :<br><https://github.com/Azure/azure-storage-net/releases/tag/v8.7.0> | Fichier app.config |
| Java | 6.1.0 | Package Maven :<br><https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0><br> <br>Version de GitHub :<br><https://github.com/Azure/azure-storage-java/releases/tag/v6.1.0> | Configuration de la chaîne de connexion |
| Node.js | 2.7.0 | Lien NPM :<br><https://www.npmjs.com/package/azure-storage><br>(Exécuter : `npm install azure-storage@2.7.0`)<br> <br>Version de GitHub :<br><https://github.com/Azure/azure-storage-node/releases/tag/v2.7.0> | Déclaration d’instance de service |
| C++ | 3.1.0 | Package NuGet :<br><https://www.nuget.org/packages/wastorage.v140/3.1.0><br> <br>Version de GitHub :<br><https://github.com/Azure/azure-storage-cpp/releases/tag/v3.1.0> | Configuration de la chaîne de connexion |
| PHP | 1.0.0 | Version de GitHub :<br>Courant : <https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-common><br>Objet blob : <https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-blob><br>File d’attente :<br><https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-queue><br>Table : <https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-table><br> <br>Installer via Composer (voir les détails ci-dessous.) | Configuration de la chaîne de connexion |
| Python | 1.0.0 | Version de GitHub :<br>Courant :<br><https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common><br>Blob :<br><https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-blob><br>File d’attente :<br><https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-queue> | Déclaration d’instance de service |
| Ruby | 1.0.1 | Package RubyGems :<br>Courant :<br><https://rubygems.org/gems/azure-storage-common/versions/1.0.1><br>Objet blob : <https://rubygems.org/gems/azure-storage-blob/versions/1.0.1><br>File d’attente : <https://rubygems.org/gems/azure-storage-queue/versions/1.0.1><br>Table : <https://rubygems.org/gems/azure-storage-table/versions/1.0.1><br> <br>Version de GitHub :<br>Courant : <https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common><br>Objet blob : <https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob><br>File d’attente : <https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-queue><br>Table : <https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-table> | Configuration de la chaîne de connexion |

#### <a name="install-php-client-via-composer---previous"></a>Installer le client PHP via Composer - précédent

Pour installer via Composer : (prenez blob pour exemple).

1. Créez un fichier nommé **composer.json** à la racine du projet avec le code suivant :

   ```json
    {
      "require": {
      "Microsoft/azure-storage-blob":"1.0.0"
      }
    }
   ```

2. Téléchargez [composer.phar](https://getcomposer.org/composer.phar) à la racine du projet.
3. Exécutez : `php composer.phar install`.
:::moniker-end

## <a name="endpoint-declaration"></a>Déclaration de point de terminaison

Un point de terminaison Azure Stack Hub comprend deux parties : le nom d’une région et le domaine Azure Stack Hub.
Dans le Kit de développement Azure Stack, le point de terminaison par défaut est **local.azurestack.external**.
Contactez votre administrateur cloud si vous ne connaissez pas votre point de terminaison.

## <a name="examples"></a>Exemples

### <a name="net"></a>.NET

Pour Azure Stack Hub, le suffixe de point de terminaison est spécifié dans le fichier app.config :

```xml
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=local.azurestack.external;" />
```

### <a name="java"></a>Java

Pour Azure Stack Hub, le suffixe de point de terminaison est spécifié dans la configuration de la chaîne de connexion :

```java
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=local.azurestack.external";
```

### <a name="nodejs"></a>Node.js

Pour Azure Stack Hub, le suffixe de point de terminaison est spécifié dans l’instance de déclaration :

```nodejs
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob.local.azurestack.external');
```

### <a name="c"></a>C++

Pour Azure Stack Hub, le suffixe de point de terminaison est spécifié dans la configuration de la chaîne de connexion :

```cpp
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=local.azurestack.external"));
```

### <a name="php"></a>PHP

Pour Azure Stack Hub, le suffixe de point de terminaison est spécifié dans la configuration de la chaîne de connexion :

```php
$connectionString = 'BlobEndpoint=https://<storage account name>.blob.local.azurestack.external/;
QueueEndpoint=https:// <storage account name>.queue.local.azurestack.external/;
TableEndpoint=https:// <storage account name>.table.local.azurestack.external/;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Pour Azure Stack Hub, le suffixe de point de terminaison est spécifié dans l’instance de déclaration :

```python
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix='local.azurestack.external')
```

### <a name="ruby"></a>Ruby

Pour Azure Stack Hub, le suffixe de point de terminaison est spécifié dans la configuration de la chaîne de connexion :

```ruby
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=local.azurestack.external
```

## <a name="blob-storage"></a>Stockage d'objets blob

Les didacticiels du stockage Blob Azure suivants sont applicables à Azure Stack Hub. Notez l’exigence particulière d’un suffixe de point de terminaison pour Azure Stack Hub, décrit dans la précédente section [Exemples](#examples).

* [Prise en main d’Azure Blob Storage à l’aide de .NET](/azure/storage/blobs/storage-dotnet-how-to-use-blobs)
* [Utilisation du stockage d'objets blob à partir de Java](/azure/storage/blobs/storage-java-how-to-use-blob-storage)
* [Utilisation du stockage d'objets blob à partir de Node.js](/azure/storage/blobs/storage-nodejs-how-to-use-blob-storage)
* [Utilisation du stockage d’objets blob à partir de C++](/azure/storage/blobs/storage-c-plus-plus-how-to-use-blobs)
* [Utilisation du stockage d'objets blob à partir de PHP](/azure/storage/blobs/storage-php-how-to-use-blobs)
* [Utilisation du stockage Blob Azure à partir de Python](/azure/storage/blobs/storage-python-how-to-use-blob-storage)
* [Utilisation du stockage d'objets blob à partir de Ruby](/azure/storage/blobs/storage-ruby-how-to-use-blob-storage)

## <a name="queue-storage"></a>Stockage de files d'attente

Les didacticiels du stockage File d’attente Azure suivants sont applicables à Azure Stack Hub. Notez l’exigence particulière d’un suffixe de point de terminaison pour Azure Stack Hub, décrit dans la précédente section [Exemples](#examples).

* [Prise en main du stockage de files d’attente Azure à l’aide de .NET](/azure/storage/queues/storage-dotnet-how-to-use-queues)
* [Utilisation du stockage de files d'attente à partir de Java](/azure/storage/queues/storage-java-how-to-use-queue-storage)
* [Utilisation du stockage de files d'attente à partir de Node.js](/azure/storage/queues/storage-nodejs-how-to-use-queues)
* [Utilisation du service de stockage de files d’attente à partir de C++](/azure/storage/queues/storage-c-plus-plus-how-to-use-queues)
* [Utilisation du stockage de files d'attente à partir de PHP](/azure/storage/queues/storage-php-how-to-use-queues)
* [Utilisation du stockage de files d'attente à partir de Python](/azure/storage/queues/storage-python-how-to-use-queue-storage)
* [Utilisation du stockage de files d'attente à partir de Ruby](/azure/storage/queues/storage-ruby-how-to-use-queue-storage)

## <a name="table-storage"></a>Stockage de tables

Les didacticiels du stockage Table Azure suivants sont applicables à Azure Stack Hub. Notez l’exigence particulière d’un suffixe de point de terminaison pour Azure Stack Hub, décrit dans la précédente section [Exemples](#examples).

* [Prise en main d’Azure Table Storage à l’aide de .NET](/azure/cosmos-db/table-storage-how-to-use-dotnet)
* [Utilisation du stockage de tables à partir de Java](/azure/cosmos-db/table-storage-how-to-use-java)
* [Utilisation du stockage Table Azure à partir de Node.js](/azure/cosmos-db/table-storage-how-to-use-nodejs)
* [Utilisation du stockage Table à partir de C++](/azure/cosmos-db/table-storage-how-to-use-c-plus)
* [Utilisation du stockage de tables à partir de PHP](/azure/cosmos-db/table-storage-how-to-use-php)
* [Utilisation du stockage Table dans Python](/azure/cosmos-db/table-storage-how-to-use-python)
* [Utilisation du stockage de tables à partir de Ruby](/azure/cosmos-db/table-storage-how-to-use-ruby)

## <a name="next-steps"></a>Étapes suivantes

* [Introduction au stockage Microsoft Azure](/azure/storage/common/storage-introduction)
