---
title: Utiliser des profils de version d’API avec Java dans Azure Stack Hub
description: Découvrez comment utiliser des profils de version d’API avec Java dans Azure Stack Hub.
author: sethmanheim
ms.topic: article
ms.date: 09/03/2020
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: 2e37d8f977a89e9a0207ef094273e9838a54d23a
ms.sourcegitcommit: 7c01ab4b2e2250a7acd67d1c5ba27d15c1e8bce0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89448654"
---
# <a name="use-api-version-profiles-with-java-in-azure-stack-hub"></a>Utiliser des profils de version d’API avec Java dans Azure Stack Hub

Le Kit de développement logiciel (SDK) Java pour Azure Stack Hub Resource Manager fournit des outils conçus pour vous aider à créer et gérer votre infrastructure. Le kit de développement logiciel (SDK) comporte des fournisseurs de ressources de calcul, de réseau, de stockage, de services d’application et [KeyVault](/azure/key-vault/key-vault-whatis).

Le Kit de développement logiciel (SDK) Java incorpore des profils d’API en incluant des dépendances dans le fichier **Pom.xml** qui charge les modules adéquats dans le fichier **.java**. Toutefois, vous pouvez ajouter plusieurs profils en tant que dépendances, comme **2019-03-01-hybrid**, ou **latest**, en guise de profil Azure. L’utilisation de ces dépendances charge le module approprié ; ainsi, lorsque vous créez votre type de ressource, vous pouvez sélectionner la version d’API de ces profils que vous souhaitez utiliser. Cette approche vous permet d’utiliser les dernières versions dans Azure, tout en développant par rapport aux dernières versions d’API pour Azure Stack Hub.

Le Kit de développement logiciel (SDK) Java favorise une véritable expérience de développement cloud hybride. Les profils d’API disponibles dans le Kit de développement logiciel (SDK) Java permettent un développement cloud hybride en vous aidant à basculer entre des ressources Azure globales et des ressources dans Azure Stack Hub.

## <a name="java-and-api-version-profiles"></a>Java et les profils de version d’API

Un profil d’API est une combinaison de fournisseurs de ressources et de versions d’API. Utilisez un profil d’API pour obtenir la version la plus récente et la plus stable de chaque type de ressource dans un package de fournisseur de ressources.

- Pour recourir à la dernière version de chacun des services, utilisez le profil **latest** en tant que dépendance.

  - Pour l’utilisation du profil latest, la dépendance est **com.microsoft.azure**.

  - Pour utiliser les derniers services pris en charge disponibles dans Azure Stack Hub, utilisez le profil **com.microsoft.azure.profile\_2019\_03\_01\_hybrid**.

    - Le profil est spécifié dans le fichier **Pom.xml** en tant que dépendance, ce qui charge automatiquement les modules si vous choisissez la classe appropriée dans la liste déroulante (comme vous le feriez avec .NET).

  - Les dépendances apparaissent comme suit :

     ```xml
     <dependency>
     <groupId>com.microsoft.azure.profile_2019_03_01_hybrid</groupId>
     <artifactId>azure</artifactId>
     <version>1.0.0-beta-1</version>
     </dependency>
     ```

  - Pour utiliser des versions d’API spécifiques pour un type de ressource dans un fournisseur de ressources donné, utilisez les versions d’API spécifiques définies par le biais d’IntelliSense.

Vous pouvez combiner toutes les options dans la même application.

## <a name="install-the-azure-java-sdk"></a>Installer le Kit de développement logiciel (SDK) Java Azure

Pour installer le SDK Java, effectuez les étapes suivantes :

1. Suivez les instructions officielles pour installer Git. Consultez [Bien démarrer - Installation de Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

2. Suivez les instructions pour installer le [Kit de développement logiciel (SDK) Java](https://zulu.org/download/) et [Maven](https://maven.apache.org/). La version correcte du Kit de développement Java est la version 8. La version correcte d’Apache Maven est la version 3.0 ou ultérieure. Pour suivre le guide de démarrage rapide, la variable d’environnement `JAVA_HOME` doit être définie sur l’emplacement d’installation du Kit de développement Java. Pour plus d’informations, consultez l’article [Créer votre première fonction dans Azure avec Java et Maven](/azure/azure-functions/functions-create-first-java-maven).

3. Pour installer les packages de dépendances corrects, ouvrez le fichier **Pom.xml** dans votre application Java. Ajoutez une dépendance, comme indiqué dans le code suivant :

   ```xml  
   <dependency>
   <groupId>com.microsoft.azure.profile_2019_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <version>1.0.0-beta-1</version>
   </dependency>
   ```

4. L’ensemble de packages à installer dépend de la version de profil que vous souhaitez utiliser. Les noms de package pour les versions de profil sont les suivants :

   - **com.microsoft.azure.profile\_2019\_03\_01\_hybrid**
   - **com.microsoft.azure**
     - **le plus récent**

5. Si vous n’en avez pas de disponible, créez un abonnement et enregistrez l’ID d’abonnement pour une utilisation ultérieure. Pour plus d’informations sur la procédure de création d’un abonnement, voir [Créer des abonnements pour des offres dans Azure Stack Hub](../operator/azure-stack-subscribe-plan-provision-vm.md).

6. Créez un principal de service et enregistrez l’ID client, ainsi que la clé secrète client. Pour plus d’informations sur la création d’un principal de service pour Azure Stack Hub, voir [Fournir à des applications l’accès à Azure Stack Hub](../operator/azure-stack-create-service-principals.md). L’ID client est également connu en tant qu’ID d’application lors de la création d’un principal de service.

7. Vérifiez que votre principal de service bénéficie du rôle contributeur/propriétaire sur votre abonnement. Pour plus d’informations sur l’attribution d’un rôle à un principal de service, voir [Fournir à des applications l’accès à Azure Stack Hub](../operator/azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Prérequis

Pour utiliser le Kit de développement logiciel (SDK) Java Azure avec Azure Stack Hub, vous devez fournir les valeurs ci-après, puis les définir avec des variables d’environnement. Pour définir les variables d’environnement, consultez les instructions fournies sous le tableau qui correspondent à votre système d’exploitation.

| Valeur                     | Variables d'environnement | Description                                                                                                                                                                                                          |
| ------------------------- | --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ID client                 | `AZURE_TENANT_ID`            | Votre [ID de locataire](../operator/azure-stack-identity-overview.md) Azure Stack Hub.                                                          |
| ID client                 | `AZURE_CLIENT_ID`             | L’ID d’application du principal de service enregistré lors de la création du principal de service dans la section précédente.                                                                                              |
| Identifiant d’abonnement           | `AZURE_SUBSCRIPTION_ID`      | Vous utilisez l’[ID d’abonnement](../operator/service-plan-offer-subscription-overview.md#subscriptions) pour accéder à des offres dans Azure Stack Hub.                |
| Clé secrète client             | `AZURE_CLIENT_SECRET`        | Le secret d’application du principal de service enregistré lors de la création du principal de service.                                                                                                                                   |
| Point de terminaison Resource Manager | `ARM_ENDPOINT`              | Voir l’article [Point de terminaison Azure Stack Hub Resource Manager](../user/azure-stack-version-profiles-ruby.md#the-azure-stack-hub-resource-manager-endpoint). |
| Emplacement                  | `RESOURCE_LOCATION`    | **Local** pour Azure Stack Hub.                                                                                                                                                                                                |

Pour rechercher l’ID de locataire de votre Azure Stack Hub, suivez les instructions fournies [ici](../operator/azure-stack-csp-ref-operations.md). Pour définir vos variables d’environnement, suivez les procédures décrites dans les sections suivantes :

### <a name="microsoft-windows"></a>Microsoft Windows

Pour définir les variables d’environnement dans une invite de commandes Windows, utilisez le format suivant :

```shell
Set AZURE_TENANT_ID=<Your_Tenant_ID>
```

### <a name="macos-linux-and-unix-based-systems"></a>Systèmes macOS, Linux et Unix

Dans les systèmes Unix, utilisez la commande suivante :

```shell
Export AZURE_TENANT_ID=<Your_Tenant_ID>
```

### <a name="trust-the-azure-stack-hub-ca-root-certificate"></a>Approuver le certificat d'autorité de certification racine Azure Stack Hub

Si vous utilisez le Kit de développement Azure Stack (ASDK), vous devez approuver le certificat racine de l’autorité de certification sur votre machine distante. Vous n’avez pas besoin d’approuver le certificat d’autorité de certification racine avec des systèmes intégrés Azure Stack Hub.

#### <a name="windows"></a>Windows

1. Exportez le certificat auto-signé Azure Stack Hub vers votre bureau.

1. Dans une invite de commandes, remplacez le répertoire par `%JAVA_HOME%\bin`.

1. Exécutez la commande suivante :

   ```shell
   .\keytool.exe -importcert -noprompt -file <location of the exported certificate here> -alias root -keystore %JAVA_HOME%\lib\security\cacerts -trustcacerts -storepass changeit
   ```

### <a name="the-azure-stack-hub-resource-manager-endpoint"></a>Point de terminaison Azure Stack Hub Resource Manager

Azure Resource Manager est une infrastructure de gestion qui permet aux administrateurs de déployer, de gérer et de superviser les ressources Azure. Azure Resource Manager peut gérer ces tâches en groupe, plutôt qu’individuellement, dans une seule opération.

Vous pouvez obtenir les informations de métadonnées du point de terminaison du Gestionnaire des ressources. Le point de terminaison retourne un fichier JSON avec les informations requises pour exécuter votre code.

Tenez compte des points suivants :

- La propriété **ResourceManagerUrl** dans l’ASDK est : `https://management.local.azurestack.external/`.

- Le **ResourceManagerUrl** dans les systèmes intégrés est le suivant : `https://management.region.<fqdn>/`, où `<fqdn>` est votre nom de domaine complet.

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

- **com.microsoft.azure.profile\_2019\_03\_01\_hybrid** : Dernier profil créé pour Azure Stack Hub. Utilisez ce profil pour que les services soient les plus compatibles possibles avec Azure Stack Hub, à condition de disposer de la version 1904 ou ultérieure.

- **com.microsoft.azure.profile\_2018\_03\_01\_hybrid** : Profil créé pour Azure Stack Hub. Utilisez ce profil pour que les services soient compatibles avec les versions 1808 ou ultérieures d’Azure Stack Hub.

- **com.microsoft.azure** : profil constitué des dernières versions de l’ensemble des services. Utilisez les dernières versions de tous les services.

Pour plus d’informations sur Azure Stack Hub et les profils d’API, voir [Résumé des profils d’API](../user/azure-stack-version-profiles.md#summary-of-api-profiles).

## <a name="azure-java-sdk-api-profile-usage"></a>Utilisation du profil d’API du Kit de développement logiciel (SDK) Java Azure

Le code ci-après authentifie le principal de service sur Azure Stack Hub. Il crée un jeton spécifique d’Azure Stack Hub en utilisant l’ID de locataire et la base d’authentification :

```java
AzureTokenCredentials credentials = new ApplicationTokenCredentials(client, tenant, key, AZURE_STACK)
                    .withDefaultSubscriptionID(subscriptionID);
Azure azureStack = Azure.configure()
                    .withLogLevel(com.microsoft.rest.LogLevel.BASIC)
                    .authenticate(credentials, credentials.defaultSubscriptionID());
```

Ce code vous permet d’utiliser les dépendances du profil d’API pour déployer correctement votre application sur Azure Stack Hub.

## <a name="define-azure-stack-hub-environment-setting-functions"></a>Définir des fonctions de définition d’environnement Azure Stack Hub

Pour inscrire le cloud Azure Stack Hub auprès des points de terminaison appropriés, utilisez le code suivant :

```java
// Get Azure Stack Hub cloud endpoints
final HashMap<String, String> settings = getActiveDirectorySettings(armEndpoint);

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

L’appel de `getActiveDirectorySettings` dans le code précédent récupère les points de terminaison à partir des points de terminaison de métadonnées. Il indique les variables d’environnement à partir de l’appel qui est effectué :

```java
public static HashMap<String, String> getActiveDirectorySettings(String armEndpoint) {

    HashMap<String, String> adSettings = new HashMap<String, String>();
    try {

        // create HTTP Client
        HttpClient httpClient = HttpClientBuilder.create().build();

        // Create new getRequest with below mentioned URL
        HttpGet getRequest = new HttpGet(String.format("%s/metadata/endpoints?api-version=1.0",
                             armEndpoint));

        // Add additional header to getRequest which accepts application/xml data
        getRequest.addHeader("accept", "application/xml");

        // Execute request and catch response
        HttpResponse response = httpClient.execute(getRequest);

        // Check for HTTP response code: 200 = success
        if (response.getStatusLine().getStatusCode() != 200) {
            throw new RuntimeException("Failed : HTTP error code : " + response.getStatusLine().getStatusCode());
        }

        String responseStr = EntityUtils.toString(response.getEntity());
        JSONObject responseJson = new JSONObject(responseStr);
        adSettings.put("galleryEndpoint", responseJson.getString("galleryEndpoint"));
        JSONObject authentication = (JSONObject) responseJson.get("authentication");
        String audience = authentication.get("audiences").toString().split("\"")[1];
        adSettings.put("login_endpoint", authentication.getString("loginEndpoint"));
        adSettings.put("audience", audience);
        adSettings.put("graphEndpoint", responseJson.getString("graphEndpoint"));

    } catch (ClientProtocolException cpe) {
        cpe.printStackTrace();
        throw new RuntimeException(cpe);
    } catch (IOException ioe) {
        ioe.printStackTrace();
        throw new RuntimeException(ioe);
    }
    return adSettings;
}
```

## <a name="samples-using-api-profiles"></a>Exemples à l’aide de profils d’API

Utilisez les exemples GitHub ci-après en guise de références pour la création de solutions avec des profils d’API .NET et Azure Stack Hub :

- [Gérer des groupes de ressources](https://github.com/Azure-Samples/Hybrid-resources-java-manage-resource-group)

- [Gestion des comptes de stockage](https://github.com/Azure-Samples/hybrid-storage-java-manage-storage-accounts)

- [Gérer une machine virtuelle](https://github.com/Azure-Samples/hybrid-compute-java-manage-vm) (mise à jour avec le profil 2019-03-01-hybrid)

### <a name="sample-unit-test-project"></a>Exemple de projet de test unitaire

1. Clonez le référentiel à l’aide de la commande suivante :

   ```shell
   git clone https://github.com/Azure-Samples/Hybrid-resources-java-manage-resource-group.git`
   ```

2. Créez un principal de service Azure et assignez un rôle pour accéder à l’abonnement. Pour obtenir des instructions sur la création d’un principal de service, consultez [Fournir l’accès des applications à Azure Stack](../operator/azure-stack-create-service-principals.md).

3. Récupérez les variables d’environnement nécessaires suivantes :

   - `AZURE_TENANT_ID`
   - `AZURE_CLIENT_ID`
   - `AZURE_CLIENT_SECRET`
   - `AZURE_SUBSCRIPTION_ID`
   - `ARM_ENDPOINT`
   - `RESOURCE_LOCATION`

4. Définissez les variables d’environnement ci-après en utilisant les informations que vous avez récupérées à partir du principal de service que vous avez créé à l’aide de l’invite de commandes :

   - `export AZURE_TENANT_ID={your tenant ID}`
   - `export AZURE_CLIENT_ID={your client ID}`
   - `export AZURE_CLIENT_SECRET={your client secret}`
   - `export AZURE_SUBSCRIPTION_ID={your subscription ID}`
   - `export ARM_ENDPOINT={your Azure Stack Hub Resource Manager URL}`
   - `export RESOURCE_LOCATION={location of Azure Stack Hub}`

   Sous Windows, utilisez **set** et non **export**.

5. Utilisez la fonction `getActiveDirectorySettings` pour récupérer les points de terminaison de métadonnées Azure Resource Manager.

    ```java
    // Get Azure Stack Hub cloud endpoints
    final HashMap<String, String> settings = getActiveDirectorySettings(armEndpoint);
    ```

6. Dans le fichier **Pom.xml**, ajoutez la dépendance suivante afin d’utiliser le profil **2019-03-01-hybrid** pour Azure Stack Hub. Cette dépendance installe les modules associés à ce profil pour les fournisseurs de ressources Compute, Mise en réseau, Stockage, KeyVault et App Services :

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.profile_2019_03_01_hybrid</groupId>
      <artifactId>azure</artifactId>
      <version>1.0.0-beta-1</version>
    </dependency>
    ```

7. Dans l’invite de commandes qui était ouverte pour la définition des variables d’environnement, entrez la commande suivante :

   ```shell
   mvn clean compile exec:java
   ```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les profils d’API, consultez les articles suivants :

- [Profils de version dans Azure Stack Hub](azure-stack-version-profiles.md)
- [Versions des API du fournisseur de ressources prises en charge par des profils dans Azure Stack](azure-stack-profiles-azure-resource-manager-versions.md)
