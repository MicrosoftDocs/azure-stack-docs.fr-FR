---
title: Utilisation des profils de version d’API avec Node.js dans Azure Stack | Microsoft Docs
description: Apprenez-en davantage sur l’utilisation des profils de version d’API avec Node.js dans Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 07/30/2019
ms.openlocfilehash: 65ea0b4f6f7f7cb3769e83bf9052ac2953668e48
ms.sourcegitcommit: 20d1c0ab3892e9c4c71d5b039457f1e15b1c84c7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73618223"
---
# <a name="use-api-version-profiles-with-nodejs-software-development-kit-sdk-in-azure-stack"></a>Utiliser des profils de version d'API avec le kit de développement logiciel (SDK) Node.js dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

## <a name="nodejs-and-api-version-profiles"></a>Node.js et les profils de version d’API

Vous pouvez utiliser le kit de développement logiciel (SDK) Node.js pour créer et gérer facilement l'infrastructure de vos applications. Les profils d’API du kit de développement logiciel (SDK) Node.js simplifient vos solutions cloud hybrides en vous permettant de basculer entre les ressources Azure globales et les ressources Azure Stack. Vous pouvez coder une fois, puis cibler Azure global et Azure Stack. 

Dans cet article, vous pouvez utiliser [Visual Studio Code](https://code.visualstudio.com/) comme outil de développement. Visual Studio Code peut déboguer le kit de développement logiciel (SDK) Node.js et vous permettre d’exécuter et d'envoyer l’application à votre instance Azure Stack. Vous pouvez déboguer depuis Visual Studio Code ou par le biais d’une fenêtre de terminal exécutant la commande `node <nodefile.js>`.

## <a name="the-nodejs-sdk"></a>Kit de développement logiciel (SDK) Node.js

Le kit de développement logiciel (SDK) Node.js fournit les outils Azure Stack Resource Manager. Le kit de développement logiciel (SDK) comporte des fournisseurs de ressources de calcul, de réseau, de stockage, de services d’application et KeyVault. Il existe 10 bibliothèques clientes de fournisseur de ressources que vous pouvez installer dans votre application node.js. Vous pouvez également télécharger spécifiquement le fournisseur de ressources que vous utiliserez pour **2018-03-01-hybrid** ou **2019-03-01-profile** afin d’optimiser la mémoire pour votre application. Chaque module se compose d’un fournisseur de ressources, de la version d’API correspondante et du profil d’API. 

Un profil d’API est une combinaison de fournisseurs de ressources et de versions d’API. Vous pouvez utiliser un profil d’API pour obtenir la version la plus récente et la plus stable de chaque type de ressource dans un package de fournisseur de ressources.

  -   Pour utiliser les versions les plus récentes de tous les services, utilisez le profil **La plus récente** des packages.

  -   Pour utiliser les services compatibles avec Azure Stack, utilisez **\@azure/arm-resources-profile-hybrid-2019-03-01** ou **\@azure/arm-storage-profile-2019-03-01-hybrid**

### <a name="packages-in-npm"></a>Packages dans npm

Chaque fournisseur de ressources dispose de son propre package. Vous pouvez obtenir le package à partir du [registre npm](https://www.npmjs.com/package/@azure/arm-storage-profile-2019-03-01-hybrid).

Les packages suivants sont disponibles :

| Fournisseur de ressources | Package |
| --- | --- |
| [App Service](https://www.npmjs.com/package/@azure/arm-appservice-profile-2019-03-01-hybrid) | @azure/arm-appservice-profile-2019-03-01-hybrid |
| [Abonnements Azure Resource Manager](https://www.npmjs.com/package/@azure/arm-subscriptions-profile-hybrid-2019-03-01) | @azure/arm-subscriptions-profile-hybrid-2019-03-01  |
| [Stratégie Azure Resource Manager](https://www.npmjs.com/package/@azure/arm-policy-profile-hybrid-2019-03-01) | @azure/arm-policy-profile-hybrid-2019-03-01
| [DNS Azure Resource Manager](https://www.npmjs.com/package/@azure/arm-dns-profile-2019-03-01-hybrid) | @azure/arm-dns-profile-2019-03-01-hybrid  |
| [Autorisation](https://www.npmjs.com/package/@azure/arm-authorization-profile-2019-03-01-hybrid) | @azure/arm-authorization-profile-2019-03-01-hybrid  |
| [Calcul](https://www.npmjs.com/package/@azure/arm-compute-profile-2019-03-01-hybrid) | @azure/arm-compute-profile-2019-03-01-hybrid |
| [Stockage](https://www.npmjs.com/package/@azure/arm-storage-profile-2019-03-01-hybrid) | @azure/arm-storage-profile-2019-03-01-hybrid |
| [Réseau](https://www.npmjs.com/package/@azure/arm-network-profile-2019-03-01-hybrid) | @azure/arm-network-profile-2019-03-01-hybrid |
| [Ressources](https://www.npmjs.com/package/@azure/arm-resources-profile-hybrid-2019-03-01) | @azure/arm-resources-profile-hybrid-2019-03-01 |
 | [Keyvault](https://www.npmjs.com/package/@azure/arm-keyvault-profile-2019-03-01-hybrid) | @azure/arm-keyvault-profile-2019-03-01-hybrid |

Pour utiliser la dernière version d’API d’un service, utilisez le profil **Le plus récent** de la bibliothèque de client spécifique. Par exemple, si vous souhaitez utiliser la dernière version d’API d’un service de ressources autonome, utilisez le profil `azure-arm-resource` de la **bibliothèque de client Gestion des ressources.** package.

Utilisez les versions d’API spécifiques définies dans le package des versions d'API spécifiques à un fournisseur de ressources.

  > [!Note]  
  > Vous pouvez combiner toutes les options dans la même application.

## <a name="install-the-nodejs-sdk"></a>Installer le kit de développement logiciel (SDK) Node.js

1. Installez Git. Pour obtenir des instructions, consultez [Démarrage rapide - Installation de Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

2. Installez ou mettez à niveau la version actuelle de [Node.js](https://nodejs.org/en/download/). Node.js comprend également le gestionnaire de package Javascript [npm](https://www.npmjs.com/).

3. Installez ou mettez à niveau [Visual Studio Code](https://code.visualstudio.com/) et installez l'[extension Node.js](https://code.visualstudio.com/docs/nodejs/nodejs-debugging) pour Visual Studio Code.

2.  Installez les packages du client pour Azure Stack Resource Manager. Pour plus d’informations, consultez [Installer les bibliothèques de client](https://www.npmjs.com/package/@azure/arm-keyvault-profile-2019-03-01-hybrid).

3.  Les packages à installer dépendent de la version de profil que vous souhaitez utiliser. La liste des fournisseurs de ressources est disponible dans la section [Packages dans npm](#packages-in-npm).

4. Installez la bibliothèque de client du fournisseur de ressources à l'aide de npm. À partir de la ligne de commande, exécutez : `npm install <package-name>`. Par exemple, vous pouvez exécuter `npm install @azure/arm-authorization-profile-2019-03-01-hybrid` pour installer la bibliothèque du fournisseur de ressources d’autorisation.

5.  Créez un abonnement et notez l'ID d'abonnement lorsque vous utilisez le kit de développement logiciel (SDK). Pour obtenir des instructions, consultez [Créer des abonnements pour des offres dans Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm).

6.  Créez un principal de service et enregistrez l’ID client, ainsi que la clé secrète client. L’ID client est également connu en tant qu’ID d’application lors de la création d’un principal de service. Pour obtenir des instructions, consultez [Fournir l’accès des applications à Azure Stack](../operator/azure-stack-create-service-principals.md).

7.  Vérifiez que votre principal de service a le rôle de contributeur/propriétaire sur votre abonnement. Pour plus d’informations sur l’assignation d’un rôle au principal de service, consultez l’article [Fournir l’accès des applications à Azure Stack](../operator/azure-stack-create-service-principals.md).

### <a name="nodejs-prerequisites"></a>Conditions préalables de Node.js 

Pour utiliser le kit de développement logiciel (SDK) Node.js Azure avec Azure Stack, vous devez fournir les valeurs ci-après, puis définir ces valeurs avec des variables d’environnement. Pour définir les variables d’environnement, consultez les instructions fournies sous le tableau qui correspondent à votre système d’exploitation.

| Valeur | Variables d’environnement | Description |
| --- | --- | --- |
| ID client | TENANT\_ID | La valeur de votre [ID de locataire](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-overview) Azure Stack. |
| ID client | CLIENT\_ID | L’ID d’application du principal du service enregistré lors de la création du principal de service dans la section précédente de ce document.  |
| Identifiant d’abonnement | AZURE\_SUBSCRIPTION\_ID   L'[ID d'abonnement](/azure-stack/operator/service-plan-offer-subscription-overview#subscriptions) correspond à la façon dont vous accédez à des offres dans Azure Stack.  |
| Clé secrète client | APPLICATION\_SECRET | Le secret d’application du principal de service enregistré lors de la création du principal de service. |
| Point de terminaison Resource Manager | ARM\_ENDPOINT | Consultez [Point de terminaison Azure Stack Resource Manager](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-version-profiles-ruby#the-azure-stack-resource-manager-endpoint). |

#### <a name="set-your-environmental-variables-for-nodejs"></a>Définir vos variables d’environnement pour Node.js

Pour définir vos variables d’environnement :

  - **Microsoft Windows**  

    Pour définir les variables d’environnement dans une invite de commandes Windows, utilisez le format suivant :
      
      `set Tenant_ID=<Your_Tenant_ID>`

  - **Systèmes Unix, Linux et macOS**  

    Pour définir les variables d’environnement, à partir d'une invite Bash, utilisez le format suivant :

    `export Azure_Tenant_ID=<Your_Tenant_ID>`

**Point de terminaison Azure Stack Resource Manager**

Microsoft Azure Resource Manager est une infrastructure de gestion qui permet aux administrateurs de déployer, gérer et superviser les ressources Azure. Azure Resource Manager peut gérer ces tâches en groupe, plutôt qu’individuellement, dans une seule opération.

Vous pouvez obtenir les informations de métadonnées du point de terminaison Resource Manager. Le point de terminaison renvoie un fichier JSON avec les informations requises pour exécuter votre code.

> [!Note]  
> Le **ResourceManagerUrl** dans le Kit de développement Azure Stack (ASDK) est : `https://management.local.azurestack.external` **ResourceManagerUrl** dans les systèmes intégrés est : `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com` Pour récupérer les métadonnées requises : `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

Exemple de fichier JSON :

```JSON  
{
    "galleryEndpoint": "https://portal.local.azurestack.external/",

    "graphEndpoint": "https://graph.windowsNode.js/",

    "portal Endpoint": "https://portal.local.azurestack.external/",

    "authentication": {

        "loginEndpoint": "https://login.windowsNode.js/",

        "audiences": ["https://management.<yourtenant>.onmicrosoft.com/"]

    }

}
```

### <a name="existing-api-profiles"></a>Profils d’API existants

-  **\@azure/arm-resourceprovider-profile-2019-03-01-hybrid**

    Dernier profil créé pour Azure Stack. Utilisez ce profil pour que les services soient davantage compatibles avec Azure Stack, à condition que vous utilisiez le tampon 1808 ou un tampon ultérieur.

-  **\@azure-arm-resource**

    Profil composé des versions les plus récentes de tous les services. Utilisez les dernières versions de tous les services dans Azure.

Pour plus d’informations sur les profils d’API et Azure Stack, consultez la section [Résumé des profils d’API](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-version-profiles#summary-of-api-profiles).

### <a name="azure-nodejs-sdk-api-profile-usage"></a>Utilisation du profil d’API du kit de développement logiciel (SDK) Azure Node.js

Les lignes suivantes doivent être utilisées pour instancier un client de profil. Ce paramètre est uniquement requis pour Azure Stack ou d’autres clouds privés. Azure global a déjà ces paramètres par défaut avec @azure-arm-resource ou @azure-arm-storage.

```Node.js  
var ResourceManagementClient = require('@azure/arm-resources-profile-hybrid-2019-03-01').ResourceManagementClient;

var StorageManagementClient = require('@azure/arm-storage-profile-2019-03-01-hybrid').StorageManagementClient;
````

Le code ci-après est requis pour authentifier le principal de service sur Azure Stack. Il crée un jeton spécifique à Azure Stack à partir de l’ID de locataire et de la base d’authentification.

```Node.js  
var clientId = process.env['AZURE_CLIENT_ID'];
var tenantId = process.env['AZURE_TENANT_ID']; //"adfs"
var secret = process.env['AZURE_CLIENT_SECRET'];
var subscriptionId = process.env['AZURE_SUBSCRIPTION_ID'];
var base_url = process.env['ARM_ENDPOINT'];
var resourceClient, storageClient;
```

Ceci vous permet d’utiliser la bibliothèque de client de profils d’API pour déployer correctement votre application sur Azure Stack.

L’extrait de code ci-dessous utilise le point de terminaison Azure Resource Manager que vous spécifiez pour votre instance Azure Stack et collecte les données indiquées ci-dessus, telles que le point de terminaison de la galerie, le point de terminaison Graph, les audiences et le point de terminaison du portail.

```Node.js  
var map = {};
const fetchUrl = base_url + 'metadata/endpoints?api-version=1.0'
```

## <a name="environment-settings"></a>Paramètres d'environnement

Pour authentifier le principal de service auprès de l’environnement Azure Stack, utilisez le code suivant : Utiliser ce code et définir vos variables d’environnement dans l’invite de commandes génère automatiquement ce mappage pour le développeur.

```Node.js  
function main() {
  var initializePromise = initialize();
  initializePromise.then(function (result) {
    var userDetails = result;
    console.log("Initialized user details");
    // Use user details from here
    console.log(userDetails)
    map["name"] = "AzureStack"
    map["portalUrl"] = userDetails.portalEndpoint 
    map["resourceManagerEndpointUrl"] = base_url 
    map["galleryEndpointUrl"] = userDetails.galleryEndpoint 
    map["activeDirectoryEndpointUrl"] = userDetails.authentication.loginEndpoint.slice(0, userDetails.authentication.loginEndpoint.lastIndexOf("/") + 1) 
    map["activeDirectoryResourceId"] = userDetails.authentication.audiences[0] 
    map["activeDirectoryGraphResourceId"] = userDetails.graphEndpoint 
    map["storageEndpointSuffix"] = "." + base_url.substring(base_url.indexOf('.'))  
    map["keyVaultDnsSuffix"] = ".vault" + base_url.substring(base_url.indexOf('.')) 
    map["managementEndpointUrl"] = userDetails.authentication.audiences[0] 
    map["validateAuthority"] = "false"
    Environment.Environment.add(map);
```

## <a name="samples-using-api-profiles"></a>Exemples à l’aide de profils d’API

Vous pouvez utiliser les exemples suivants en guise de références pour la création de solutions avec des profils d’API Node.js et Azure Stack. Vous pouvez récupérer les exemples à partir de GitHub dans les référentiels suivants :

- [Prise en main du fournisseur de ressources de nœud de stockage](https://github.com/sijuman/storage-node-resource-provider-getting-started)
- [Gérer les nœuds de calcul](https://github.com/sijuman/compute-node-manage-vm)
- [Ressources et groupes de nœuds du gestionnaire de ressources](https://github.com/sijuman/resource-manager-node-resources-and-groups)

### <a name="sample-create-storage-account"></a>Exemple de création de compte de stockage 

1.  Clonez le référentiel.

    ```bash  
    git clone https://github.com/sijuman/storage-node-resource-provider-getting-started.git
    ```

2.  Créez un principal de service Azure et assignez un rôle pour accéder à l’abonnement. Pour obtenir des instructions, consultez [Utiliser Azure PowerShell pour créer un principal du service avec un certificat](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals).

3.  Récupérez les valeurs requises suivantes :
    - ID client
    - ID client
    - Clé secrète client
    - ID d’abonnement Azure
    - Point de terminaison Azure Stack Resource Manager

4.  Définissez les variables d’environnement ci-après en utilisant les informations que vous avez récupérées à partir du principal de service que vous avez créé à l’aide de l’invite de commandes :

    ```bash  
    export TENANT_ID=<your tenant id>
    export CLIENT_ID=<your client id>
    export APPLICATION_SECRET=<your client secret>K
    export AZURE_SUBSCRIPTION_ID=<your subscription id>
    export ARM_ENDPOINT=<your Azure Stack Resource manager URL>
    ```

    > [!Note]  
    > Sous Windows, utilisez **set** plutôt que **export**.

5.  Ouvrez le fichier `index.js` d’exemple d’application.

6.  Définissez la variable d’emplacement sur votre emplacement Azure Stack. Par exemple : `LOCAL = "local"`.

7.  Définissez les informations d’identification qui vous permettront de vous authentifier auprès d’Azure Stack. Cette partie du code est incluse dans cet exemple dans le fichier index.js.

    ```Node.js  
    var clientId = process.env['CLIENT_ID'];
    var tenantId = process.env['TENANT_ID']; //"adfs"
    var secret = process.env['APPLICATION_SECRET'];
    var subscriptionId = process.env['AZURE_SUBSCRIPTION_ID'];
    var base_url = process.env['ARM_ENDPOINT'];
    var resourceClient, storageClient;
    ```

8.  Vérifiez que vous avez défini les bibliothèques de client qui conviennent, à l’aide d’une combinaison des bibliothèques de client stipulées ci-dessus. Dans cet exemple, nous avons utilisé les éléments suivants :

    ```Node.js
    var ResourceManagementClient = require('@azure/arm-resources-profile-hybrid-2019-03-01').ResourceManagementClient;
    var StorageManagementClient = require('@azure/arm-storage-profile-2019-03-01-hybrid').StorageManagementClient;
    ```

9.  À l’aide de [npm modules search](https://www.npmjs.com/package/@azure/arm-keyvault-profile-2019-03-01-hybrid), recherchez le profil **2019-03-01-hybrid** et installez les packages associés à ce profil pour les fournisseurs de ressources de calcul, de mise en réseau, de stockage, KeyVault et App Services.

    Pour ce faire, vous pouvez ouvrir l’invite de commandes, la rediriger vers le dossier racine du référentiel et exécuter `npm install @azure/arm-keyvault-profile-2019-03-01-hybrid` pour chaque fournisseur de ressources utilisé.

10.  À l’invite de commandes, exécutez la commande `npm install` pour installer tous les modules node.js.

11.  Exécutez l’exemple.

        ```Node.js  
        node index.js
        ```

12.  Ensuite, pour nettoyer l'index.js, exécutez le script de nettoyage.

        ```Node.js  
        Node cleanup.js <resourceGroupName> <storageAccountName>
        ```

13.  L’exemple a été correctement suivi. Pour plus d’informations, voir ci-dessous.

### <a name="what-does-indexjs-do"></a>Que fait index.js ?

L’exemple crée un compte de stockage, répertorie les comptes de stockage de l’abonnement ou le groupe de ressources, répertorie les clés de compte de stockage, régénère les clés de compte de stockage, obtient les propriétés de compte de stockage, met à jour la référence SKU du compte de stockage et vérifie la disponibilité du nom du compte de stockage.

L’exemple commence par une connexion à votre principal de service et la création des objets **ResourceManagementClient** et **StorageManagementClient** à l’aide de vos informations d’identification et ID d’abonnement.

L’exemple configure alors un groupe de ressources dans lequel créer le compte de stockage.

```Node.js  
function createResourceGroup(callback) {
  var groupParameters = { location: location, tags: { sampletag: 'sampleValue' } };
  console.log('\nCreating resource group: ' + resourceGroupName);
  return resourceClient.resourceGroups.createOrUpdate(resourceGroupName, groupParameters, callback);
}
```

### <a name="create-a-new-storage-account"></a>Création d’un nouveau compte de stockage

L’exemple crée ensuite un compte de stockage qui est associé au groupe de ressources créé à l’étape précédente.

Dans ce cas, le nom du compte de stockage est généré de manière aléatoire pour garantir son unicité. Toutefois, l’appel visant à créer un compte de stockage échoue en présence d'un compte portant le même nom dans l’abonnement.

```Node.js  
var createParameters = {
    location: location,
    sku: {
        name: accType,
    },
    kind: 'Storage',
    tags: {
        tag1: 'val1',
        tag2: 'val2'
    }
};


console.log('\\n--&gt;Creating storage account: ' + storageAccountName + ' with parameters:\\n' + util.inspect(createParameters));

return storageClient.storageAccounts.create(resourceGroupName, storageAccountName, createParameters, callback);
```

### <a name="list-storage-accounts-in-the-subscription-or-resource-group"></a>Répertorier les comptes de stockage de l’abonnement ou du groupe de ressources

L’exemple répertorie tous les comptes de stockage d’un abonnement donné :

```Node.js  
console.log('\\n--&gt;Listing storage accounts in the current subscription.');

return storageClient.storageAccounts.list(callback);

It also lists storage accounts in the resource group:

    console.log('\\n--&gt;Listing storage accounts in the resourceGroup : ' + resourceGroupName);

return storageClient.storageAccounts.listByResourceGroup(resourceGroupName, callback);
```

### <a name="read-and-regenerate-storage-account-keys"></a>Lire et régénérer les clés du compte de stockage

L’exemple répertorie les clés de compte de stockage du nouveau compte de stockage ou groupe de ressources :

```Node.js  
console.log('\\n--&gt;Listing storage account keys for account: ' + storageAccountName);

return storageClient.storageAccounts.listKeys(resourceGroupName, storageAccountName, callback);
```

Il régénère également les clés d’accès au compte :

```Node.js  
console.log('\\n--&gt;Regenerating storage account keys for account: ' + storageAccountName);

return storageClient.storageAccounts.regenerateKey(resourceGroupName, storageAccountName, 'key1', callback);
```

### <a name="get-storage-account-properties"></a>Obtenir les propriétés du compte de stockage

L’exemple lit les propriétés du compte de stockage :

```Node.js  
console.log('\\n--&gt;Getting info of storage account: ' + storageAccountName);

return storageClient.storageAccounts.getProperties(resourceGroupName, storageAccountName, callback);
```

### <a name="check-storage-account-name-availability"></a>Vérifier la disponibilité d'un nom de compte de stockage

L’exemple vérifie si un nom de compte de stockage donné est disponible dans Azure :

```Node.js  
console.log('\\n--&gt;Checking if the storage account name : ' + storageAccountName + ' is available.');

return storageClient.storageAccounts.checkNameAvailability(storageAccountName, callback);
```

### <a name="delete-the-storage-account-and-resource-group"></a>Supprimer le compte de stockage et le groupe de ressources

L’exécution de cleanup.js supprime le compte de stockage créé par l'exemple :

```Node.js  
console.log('\\nDeleting storage account : ' + storageAccountName);
return storageClient.storageAccounts.deleteMethod(resourceGroupName, storageAccountName, callback);
```

Elle supprime également le groupe de ressources créé par l'exemple :

```Node.js  
console.log('\\nDeleting resource group: ' + resourceGroupName);

return resourceClient.resourceGroups.deleteMethod(resourceGroupName, callback);
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les profils d’API, consultez les articles suivants :

- [Gérer les profils de version des API dans Azure Stack](azure-stack-version-profiles.md)
- [Versions des API du fournisseur de ressources prises en charge par des profils dans Azure Stack](azure-stack-profiles-azure-resource-manager-versions.md)
