---
title: Utilisation de profils de version d’API avec Java dans Azure Stack | Microsoft Docs
description: Apprenez-en davantage sur l’utilisation des profils de version d’API avec Java dans Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: 90c4665b1519e078015f9a6096417c2d97425da9
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782734"
---
# <a name="use-api-version-profiles-with-java-in-azure-stack"></a>Utiliser des profils de version d’API avec Java dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Le Kit de développement logiciel (SDK) Java pour Azure Stack Resource Manager fournit des outils conçus pour vous aider à créer et gérer votre infrastructure. Le Kit de développement logiciel (SDK) comporte des fournisseurs de ressources de calcul, de mise en réseau, de stockage, de services d’application et [KeyVault](/azure/key-vault/key-vault-whatis). Le Kit de développement logiciel (SDK) Java incorpore des profils d’API en incluant des dépendances dans le fichier Pom.xml qui charge les modules adéquats dans le fichier .java. Toutefois, vous pouvez ajouter plusieurs profils en tant que dépendances, comme **2018-03-01-hybrid**, ou **latest**, en guise de profil Azure. L’utilisation de ces dépendances charge le module approprié ; ainsi, lorsque vous créez votre type de ressource, vous êtes en mesure de sélectionner la version d’API de ces profils que vous souhaitez utiliser. Cette approche vous permet d’utiliser les dernières versions dans Azure, tout en développant avec les dernières versions d’API pour Azure Stack. Le Kit de développement logiciel (SDK) Java favorise une véritable expérience de développement cloud hybride. Les profils d’API fournis dans le Kit de développement logiciel (SDK) Java autorisent un développement cloud hybride en vous permettant de basculer entre les ressources Azure globales et les ressources d’Azure Stack.

## <a name="java-and-api-version-profiles"></a>Java et les profils de version d’API

Un profil d’API est une combinaison de fournisseurs de ressources et de versions d’API. Vous pouvez utiliser un profil d’API pour obtenir la version la plus récente et la plus stable de chaque type de ressource dans un package de fournisseur de ressources.

- Pour recourir à la dernière version de chacun des services, utilisez le profil **latest** en tant que dépendance.

  - Pour l’utilisation du profil latest, la dépendance est **com.microsoft.azure**.

  - Pour utiliser les services compatibles avec Azure Stack, utilisez le profil **com.microsoft.azure.profile\_2018\_03\_01\_hybrid**.

    - Ce profil doit être spécifié dans le fichier Pom.xml en tant que dépendance, qui charge automatiquement les modules si vous choisissez la classe appropriée dans la liste déroulante tout comme vous le feriez avec .NET.

    - Le début de chaque module apparaît sous la forme suivante :      `Import com.microsoft.azure.management.resources.v2018_03_01.ResourceGroup`

  - Les dépendances apparaissent comme suit :

     ```xml
     <dependency>
     <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
     <artifactId>azure</artifactId>
     <version>1.0.0-beta</version>
     </dependency>
     ```

  - Pour utiliser des versions d’API spécifiques pour un type de ressource dans un fournisseur de ressources donné, utilisez les versions d’API spécifiques définies par le biais d’IntelliSense.

Notez que vous pouvez combiner toutes les options dans la même application.

## <a name="install-the-azure-java-sdk"></a>Installer le Kit de développement logiciel (SDK) Java Azure

Pour installer le Kit de développement logiciel (SDK) Java, procédez comme suit :

1. Suivez les instructions officielles pour installer Git. Pour obtenir des instructions, consultez [Démarrage rapide - Installation de Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

2. Suivez les instructions pour installer le [Kit de développement logiciel (SDK) Java](https://zulu.org/download/) et [Maven](https://maven.apache.org/). La version correcte du Kit de développement Java est la version 8. La version adéquate d’Apache Maven est la version 3.0 ou une version ultérieure. Pour suivre le guide de démarrage rapide, vous devez définir la variable d’environnement JAVA_HOME sur l’emplacement d’installation du Kit de développement logiciel (SDK) Java. Pour plus d’informations, consultez l’article [Créer votre première fonction dans Azure avec Java et Maven](/azure/azure-functions/functions-create-first-java-maven).

3. Pour installer les packages de dépendances corrects, ouvrez le fichier Pom.xml dans votre application Java. Ajoutez une dépendance, comme indiqué dans le code suivant :

   ```xml  
   <dependency>
   <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <version>1.0.0-beta</version>
   </dependency>
   ```

4. L’ensemble de packages à installer dépend de la version de profil que vous souhaitez utiliser. Les noms de package pour les versions de profil sont les suivants :

   - **com.microsoft.azure.profile\_2018\_03\_01\_hybrid**
   - **com.microsoft.azure**
     - **le plus récent**

5. Si vous n’en avez pas de disponible, créez un abonnement et enregistrez l’ID d’abonnement pour une utilisation ultérieure. Pour plus d’informations sur la procédure de création d’un abonnement, consultez l’article [Créer des abonnements pour des offres dans Azure Stack](../operator/azure-stack-subscribe-plan-provision-vm.md).

6. Créez un principal de service et enregistrez l’ID client, ainsi que la clé secrète client. Pour plus d’informations sur la création d’un principal de service pour Azure Stack, consultez l’article [Fournir l’accès des applications à Azure Stack](../operator/azure-stack-create-service-principals.md). Notez que l’ID client est également connu en tant qu’ID d’application lors de la création d’un principal de service.

7. Vérifiez que votre principal de service présente le rôle contributeur/propriétaire sur votre abonnement. Pour plus d’informations sur l’assignation d’un rôle au principal de service, consultez l’article [Fournir l’accès des applications à Azure Stack](../operator/azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Prérequis

Pour utiliser le Kit de développement logiciel (SDK) Java Azure avec Azure Stack, vous devez fournir les valeurs ci-après, puis définir ces valeurs avec des variables d’environnement. Pour définir les variables d’environnement, consultez les instructions fournies sous le tableau qui correspondent à votre système d’exploitation.

| Valeur                     | Variables d’environnement | Description                                                                                                                                                                                                          |
| ------------------------- | --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ID client                 | AZURE_TENANT_ID            | La valeur de votre [ID de locataire](../operator/azure-stack-identity-overview.md) Azure Stack.                                                          |
| ID client                 | AZURE_CLIENT_ID             | L’ID d’application du principal de service enregistré lors de la création du principal de service dans la section précédente.                                                                                              |
| Identifiant d’abonnement           | AZURE_SUBSCRIPTION_ID      | [L’ID d’abonnement](../operator/azure-stack-plan-offer-quota-overview.md#subscriptions) correspond à la façon dont vous accédez à des offres dans Azure Stack.                |
| Clé secrète client             | AZURE_CLIENT_SECRET        | Le secret d’application du principal de service enregistré lors de la création du principal de service.                                                                                                                                   |
| Point de terminaison Resource Manager | ARM_ENDPOINT              | Consultez [Point de terminaison Azure Stack Resource Manager](../user/azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint). |
| Lieu                  | RESOURCE_LOCATION    | **Local** pour Azure Stack.                                                                                                                                                                                                |

Pour rechercher l’ID de locataire de votre environnement Azure Stack, suivez les instructions fournies [ici](../operator/azure-stack-csp-ref-operations.md). Pour définir vos variables d’environnement, procédez comme suit :

### <a name="microsoft-windows"></a>Microsoft Windows

Pour définir les variables d’environnement dans une invite de commandes Windows, utilisez le format suivant :

```shell
Set AZURE_TENANT_ID=<Your_Tenant_ID>
```

### <a name="macos-linux-and-unix-based-systems"></a>Systèmes macOS, Linux et Unix

Dans les systèmes Unix, utilisez la commande suivante :

```shell
Export AZURE_TENANT_ID=<Your_Tenant_ID>
```

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Approuver le certificat racine d’autorité de certification Azure Stack

Si vous utilisez l’ASDK, vous devez approuver le certificat racine de l’autorité de certification sur votre ordinateur distant. Cette opération n’est pas nécessaire sur les systèmes intégrés.

#### <a name="windows"></a> Windows

1. Exportez le certificat auto-signé Azure Stack vers votre bureau.

1. Dans une invite de commandes, remplacez le répertoire par %JAVA_HOME%\bin.

1. Exécutez la commande suivante :

   ```shell
   .\keytool.exe -importcert -noprompt -file <location of the exported certificate here> -alias root -keystore %JAVA_HOME%\lib\security\cacerts -trustcacerts -storepass changeit
   ```

### <a name="the-azure-stack-resource-manager-endpoint"></a>Point de terminaison Azure Stack Resource Manager

Microsoft Azure Resource Manager est une infrastructure de gestion qui permet aux administrateurs de déployer, gérer et superviser les ressources Azure. Azure Resource Manager peut gérer ces tâches en groupe, plutôt qu’individuellement, dans une seule opération.

Vous pouvez obtenir les informations de métadonnées du point de terminaison Resource Manager. Le point de terminaison renvoie un fichier JSON avec les informations requises pour exécuter votre code.

Tenez compte des points suivants :

- Le **ResourceManagerUrl** dans le Kit de développement Azure Stack (ASDK) est : https://management.local.azurestack.external/.

- **ResourceManagerUrl** dans les systèmes intégrés est : `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`.

Pour récupérer les métadonnées requises : `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`.

Exemple de fichier JSON :

```json
{
   "galleryEndpoint": "https://portal.local.azurestack.external:30015/",
   "graphEndpoint": "https://graph.windows.net/",
   "portal Endpoint": "https://portal.local.azurestack.external/",
   "authentication":
      {
      "loginEndpoint": "https://login.windows.net/",
      "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
      }
}
```

## <a name="existing-api-profiles"></a>Profils d’API existants

- **com.microsoft.azure.profile\_2018\_03\_01\_hybrid** : Dernier profil créé pour Azure Stack. Utilisez ce profil pour que les services soient davantage compatibles avec Azure Stack, à condition que vous utilisiez le tampon 1808 ou un tampon ultérieur.

- **com.microsoft.azure** : profil constitué des dernières versions de l’ensemble des services. Utilisez les dernières versions de tous les services.

Pour plus d’informations sur Azure Stack et les profils d’API, consultez la section [Résumé des profils d’API](../user/azure-stack-version-profiles.md#summary-of-api-profiles).

## <a name="azure-java-sdk-api-profile-usage"></a>Utilisation du profil d’API du Kit de développement logiciel (SDK) Java Azure

Le code ci-après authentifie le principal de service sur Azure Stack. Il crée un jeton spécifique à Azure Stack en utilisant l’ID de locataire et la base d’authentification :

```java
AzureTokenCredentials credentials = new ApplicationTokenCredentials(client, tenant, key, AZURE_STACK)
                    .withDefaultSubscriptionID(subscriptionID);
Azure azureStack = Azure.configure()
                    .withLogLevel(com.microsoft.rest.LogLevel.BASIC)
                    .authenticate(credentials, credentials.defaultSubscriptionID());
```

Ceci vous permet d’utiliser les dépendances de profil d’API pour déployer correctement votre application sur Azure Stack.

## <a name="define-azure-stack-environment-setting-functions"></a>Définir des fonctions de définition d’environnement Azure Stack

Pour inscrire le cloud Azure Stack avec les points de terminaison appropriés, utilisez le code suivant :

```java
AzureEnvironment AZURE_STACK = new AzureEnvironment(new HashMap<String, String>() {
                {
                    put("managementEndpointUrl", settings.get("audience"));
                    put("resourceManagerEndpointUrl", armEndpoint);
                    put("galleryEndpointUrl", settings.get("galleryEndpoint"));
                    put("activeDirectoryEndpointUrl", settings.get("login_endpoint"));
                    put("activeDirectoryResourceID", settings.get("audience"));
                    put("activeDirectoryGraphResourceID", settings.get("graphEndpoint"));
                    put("storageEndpointSuffix", armEndpoint.substring(armEndpoint.indexOf('.')));
                    put("keyVaultDnsSuffix", ".vault" + armEndpoint.substring(armEndpoint.indexOf('.')));
                }
            });
```

L’appel `getActiveDirectorySettings` dans le code ci-après récupère les points de terminaison à partir des points de terminaison de métadonnées. Il indique les variables d’environnement à partir de l’appel qui est effectué :

```java
public static HashMap<String, String>
getActiveDirectorySettings(String armEndpoint) {

HashMap<String, String> adSettings = new HashMap<String, String>();

try {

// create HTTP Client
HttpClient httpClient = HttpClientBuilder.create().build();

// Create new getRequest with below mentioned URL
HttpGet getRequest = new
HttpGet(String.format("%s/metadata/endpoints?api-version=1.0",
armEndpoint));

// Add additional header to getRequest which accepts application/xml data
getRequest.addHeader("accept", "application/xml");

// Execute request and catch response
HttpResponse response = httpClient.execute(getRequest);
```

## <a name="samples-using-api-profiles"></a>Exemples à l’aide de profils d’API

Vous pouvez utiliser les exemples GitHub ci-après en guise de références pour la création de solutions avec des profils d’API .NET et Azure Stack :

- [Gérer des groupes de ressources](https://github.com/Azure-Samples/Hybrid-resources-java-manage-resource-group)

- [Gérer des comptes de stockage](https://github.com/Azure-Samples/hybrid-storage-java-manage-storage-accounts)

- [Gérer une machine virtuelle](https://github.com/Azure-Samples/hybrid-compute-java-manage-vm)

### <a name="sample-unit-test-project"></a>Exemple de projet de test unitaire

1. Clonez le référentiel à l’aide de la commande suivante :

   `git clone https://github.com/Azure-Samples/Hybrid-resources-java-manage-resource-group.git`

2. Créez un principal de service Azure et assignez un rôle pour accéder à l’abonnement. Pour obtenir des instructions sur la création d’un principal de service, consultez [Fournir l’accès des applications à Azure Stack](../operator/azure-stack-create-service-principals.md).

3. Récupérez les valeurs de variable d’environnement requises suivantes :

   - AZURE_TENANT_ID
   - AZURE_CLIENT_ID
   - AZURE_CLIENT_SECRET
   - AZURE_SUBSCRIPTION_ID
   - ARM_ENDPOINT
   - RESOURCE_LOCATION

4. Définissez les variables d’environnement ci-après en utilisant les informations que vous avez récupérées à partir du principal de service que vous avez créé à l’aide de l’invite de commandes :

   - export AZURE_TENANT_ID={votre ID de locataire}
   - export AZURE_CLIENT_ID={votre ID de client}
   - export AZURE_CLIENT_SECRET={votre secret de client}
   - export AZURE_SUBSCRIPTION_ID={votre ID d’abonnement}
   - export ARM_ENDPOINT={votre URL Azure Stack Resource Manager}
   - export RESOURCE_LOCATION={emplacement d’Azure Stack}

   Dans Windows, utilisez **set** et non **export**.

5. Utilisez le code `getactivedirectorysettings` pour récupérer le point de terminaison de métadonnées ARM et utilisez le client HTTP pour définir les informations de point de terminaison.

   ```java
   public static HashMap<String, String> getActiveDirectorySettings(String armEndpoint) {
   HashMap<String, String> adSettings = new HashMap<String,> String>();

   try {

   // create HTTP Client
   HttpClient httpClient = HttpClientBuilder.create().build();

   // Create new getRequest with below mentioned URL
   HttpGet getRequest = new
   HttpGet(String.format("%s/metadata/endpoints?api-version=1.0", armEndpoint));

   // Add additional header to getRequest which accepts application/xml data
   getRequest.addHeader("accept", "application/xml");

   // Execute request and catch response
   HttpResponse response = httpClient.execute(getRequest);
   ```

6. Dans le fichier Pom.xml, ajoutez la dépendance suivante afin d’utiliser le profil **2018-03-01-hybrid** pour Azure Stack. Cette dépendance installe les modules associés à ce profil pour les fournisseurs de ressources Compute, Mise en réseau, Stockage, KeyVault et App Services :

   ```xml
   <dependency>
   <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <vers1s.0.0-beta</version>
   </dependency>
   ```

7. Dans l’invite de commandes qui était ouverte pour la définition des variables d’environnement, entrez la commande suivante :

   ```shell
   mvn clean compile exec:java
   ```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les profils d’API, consultez les articles suivants :

- [Profils de version dans Azure Stack](azure-stack-version-profiles.md)
- [Versions des API du fournisseur de ressources prises en charge par des profils dans Azure Stack](azure-stack-profiles-azure-resource-manager-versions.md)
