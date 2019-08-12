---
title: Utiliser des profils de version des API avec Go dans Azure Stack | Microsoft Docs
description: Découvrez comment utiliser des profils de version des API avec Go dans Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/26/2019
ms.openlocfilehash: dac2eeaf3499068812d9c9a66348b0c44ea07b7d
ms.sourcegitcommit: 637018771ac016b7d428174e88d4dcb131b54959
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68842711"
---
# <a name="use-api-version-profiles-with-go-in-azure-stack"></a>Utilisez des profils de version des API avec Go dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

## <a name="go-and-version-profiles"></a>Go et les profils de version

Un profil est une combinaison de différents types de ressources dans différentes versions provenant de différents services. L’utilisation d’un profil vous aide à combiner différents types de ressource. Les profils peuvent offrir les avantages suivants :

- La stabilité de votre app en verrouillant sur des versions d’API spécifiques.
- La compatibilité de votre app avec Azure Stack et les centres de données régionaux Azure.

Dans le SDK Go, les profils sont disponibles sous le chemin des profils. Leurs numéros de version sont étiquetés au format **AAAA-MM-JJ**. La dernière version du profil d’API Azure Stack est la version **2019-03-01** pour les versions portant le tampon 1904 ou ultérieures. Pour importer un service donné à partir d’un profil, importez son module correspondant à partir du profil. Par exemple, pour importer le service **Compute** à partir du profil **2019-03-01**, utilisez le code suivant :

```go
import "github.com/Azure/azure-sdk-for-go/profiles/2019-03-01/compute/mgmt/compute"
```

## <a name="install-azure-sdk-for-go"></a>Installer le Kit de développement logiciel Microsoft Azure SDK pour Go

1. Installez Git. Pour obtenir des instructions, consultez [Démarrage rapide - Installation de Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
2. Installez le [langage de programmation Go](https://golang.org/dl). Les profils d’API pour Azure nécessitent Go version 1.9 ou une version ultérieure.
3. Installez le Kit de développement logiciel (SDK) Azure et ses dépendances en exécutant la commande Bash suivante :

   ```bash
   go get -u -d github.com/Azure/azure-sdk-for-go/...
   ```

### <a name="the-go-sdk"></a>Kit SDK Go

Pour plus d’informations sur le kit SDK Azure GO, cliquez sur les liens suivants :

- Le Kit de développement logiciel (SDK) sur la page [Installation du Kit de développement logiciel Microsoft Azure SDK pour Go](/go/azure/azure-sdk-go-install).
- Le kit SDK Azure Go public est disponible sur GitHub dans le dépôt [azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go).

### <a name="go-autorest-dependencies"></a>Dépendances de Go-AutoRest

Le kit SDK Go dépend des modules Azure **Go-AutoRest** pour l’envoi des requêtes REST aux points de terminaison Azure Resource Manager. Vous devez importer les dépendances du module Azure **Go-AutoRest** à partir de [Azure Go-AutoRest on GitHub](https://github.com/Azure/go-autorest). Vous trouverez les commandes Bash d’installation dans la section **Install**.

## <a name="how-to-use-go-sdk-profiles-on-azure-stack"></a>Comment utiliser les profils du kit SDK Go sur Azure Stack

Pour exécuter un exemple de code Go sur Azure Stack, effectuez les étapes suivantes :

1. Installez le Kit de développement logiciel (SDK) Azure pour Go et ses dépendances. Pour obtenir des instructions, consultez la section précédente, [Installer le Kit de développement logiciel Microsoft Azure SDK pour Go](#install-azure-sdk-for-go).
2. Obtenir les informations de métadonnées du point de terminaison du Gestionnaire des ressources. Le point de terminaison retourne un fichier JSON avec les informations requises pour exécuter votre code Go.

   > [!NOTE]  
   > Le **ResourceManagerUrl** dans le Kit de développement Azure Stack (ASDK) est : `https://management.local.azurestack.external/`  
   > Le **ResourceManagerUrl** dans les systèmes intégrés est : `https://management.<region>.<fqdn>/`  
   > Pour extraire les métadonnées requises : `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`
  
   Exemple de fichier JSON :

   ```json
   { "galleryEndpoint": "https://portal.local.azurestack.external:30015/",  
     "graphEndpoint": "https://graph.windows.net/",  
     "portal Endpoint": "https://portal.local.azurestack.external/",
     "authentication": {
       "loginEndpoint": "https://login.windows.net/",
       "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
     }
   }
   ```

3. Si vous n’en avez pas de disponible, créez un abonnement et enregistrez l’ID d’abonnement pour une utilisation ultérieure. Pour plus d’informations sur la création d’un abonnement, consultez [Créer des abonnements pour des offres dans Azure Stack](../operator/azure-stack-subscribe-plan-provision-vm.md).

4. Créez un principal de service utilisant une clé secrète client, avec l’étendue **Abonnement** et le rôle **Propriétaire**. Enregistrez l’ID et le secret du principal de service. Pour plus d’informations sur la création d’un principal de service pour Azure Stack, voir [Utiliser une identité d’application pour accéder aux ressources](../operator/azure-stack-create-service-principals.md). <br>Votre environnement Azure Stack est désormais configuré.

5. Importez un module de service à partir du profil de kit SDK Go dans votre code. La version actuelle du profil Azure Stack est **2019-03-01**. Par exemple, pour importer le module réseau à partir du type de profil **2019-03-01**, utilisez le code suivant :

   ```go
   package main
    import "github.com/Azure/azure-sdk-for-go/profiles/2019-03-01/network/mgmt/network"
   ```

6. Dans votre fonction, créez et authentifiez un client avec l’appel de fonction **Nouveau** client. Vous pouvez utiliser le code suivant pour créer un réseau virtuel client :  

   ```go
   package main

   import "github.com/Azure/azure-sdk-for-go/profiles/2019-03-01/network/mgmt/network"

   func main() {
      vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
      vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
   ```

   Affectez à `<baseURI>` la valeur de **ResourceManagerUrl** utilisée à l’étape 2. Affectez à `<subscriptionID>` la valeur de **SubscriptionID** enregistrée à l’étape 3.

   Pour créer le jeton, consultez la section suivante.  

7. Appelez les méthodes de l’API à l’aide du client que vous avez créé à l’étape précédente. Par exemple, pour créer un réseau virtuel à l’aide du client de l’étape précédente, consultez l’exemple suivant :

   ```go
   package main

   import "github.com/Azure/azure-sdk-for-go/profiles/2019-03-01/network/mgmt/network"
   func main() {
   vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
   vnetClient .Authorizer = autorest.NewBearerAuthorizer(token)

   vnetClient .CreateOrUpdate( )
   ```

Pour obtenir un exemple complet de création d’un réseau virtuel sur Azure Stack à l’aide du profil de kit SDK Go, consultez l’[exemple](#example).

## <a name="authentication"></a>Authentication

Pour obtenir la propriété **Authorizer** d’Azure Active Directory à l’aide du kit SDK Go, installez les modules **Go-AutoRest**. Ces modules doivent déjà avoir été installés avec l'installation du « SDK Go ». Si ce n'est pas le cas, installez le [package d'authentification de GitHub](https://github.com/Azure/go-autorest/tree/master/autorest/adal).

L’agent d’autorisation doit être défini comme l’agent d’autorisation pour le client de la ressource. Il existe différentes manières d’obtenir des jetons d’autorisation sur Azure Stack à l’aide des informations d’identification du client :

1. Si un principal de service disposant du rôle de propriétaire sur l’abonnement est disponible, ignorez cette étape. Sinon, voir [Utiliser une identité d’application pour accéder aux ressources](../operator/azure-stack-create-service-principals.md) afin d’obtenir des instructions sur la création d’un principal de service utilisant une clé secrète client, et une aide sur l’attribution à celui-ci d’un rôle « propriétaire » étendu à votre abonnement. Veillez à capturer l’ID d’application et le secret du principal de service.

2. Importez le package **adal** à partir de Go-AutoRest dans votre code.

   ```go
   package main
   import "github.com/Azure/go-autorest/autorest/adal"
   ```

3. Créez un **oauthConfig** à l’aide de la méthode NewOAuthConfig du module **adal**.

   ```go
   package main

   import "github.com/Azure/go-autorest/autorest/ada1"

   func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
   }
   ```

   Affectez à `<activeDirectoryEndpoint>` la valeur de la propriété `loginEndpoint` provenant des métadonnées `ResourceManagerUrl` récupérées dans la section précédente de ce document. Affectez à `<tenantID>` la valeur de votre ID de locataire Azure Stack.

4. Enfin, créez un jeton de principal de service à l’aide de la méthode `NewServicePrincipalToken` du module **adal** :

   ```go
   package main

   import "github.com/Azure/go-autorest/autorest/adal"

   func CreateToken() (adal.OAuthTokenProvider, error) {
       var token adal.OAuthTokenProvider
       oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
       token, err = adal.NewServicePrincipalToken(
           *oauthConfig,
           clientID,
           clientSecret,
           activeDirectoryResourceID)
       return token, err
   ```

    Affectez à `<activeDirectoryResourceID>` l’une des valeurs de la liste « audience » provenant des métadonnées **ResourceManagerUrl** récupérées dans la section précédente de cet article.
    Définissez `<clientID>` en utilisant l’ID d’application du principal de service enregistré lors de la création du principal de service dans la section précédente de cet article.
    Affectez à `<clientSecret>` le secret d’application du principal de service enregistré durant la création du principal de service dans la section précédente de cet article.

## <a name="example"></a>Exemples

Cet exemple montre un exemple de code Go créant un réseau virtuel sur Azure Stack. Pour obtenir des exemples complets du kit SDK Go, consultez le [dépôt d’exemples du kit SDK Azure Go](https://github.com/Azure-Samples/azure-sdk-for-go-samples). Les exemples Azure Stack sont disponibles sous le chemin hybride dans les dossiers de service du dépôt.

> [!NOTE]  
> Pour exécuter le code de cet exemple, vérifiez que le fournisseur de ressources **Réseau** de l’abonnement utilisé est listé comme étant **Inscrit**. Pour vérifier, recherchez l’abonnement sur le portail Azure Stack, puis sélectionnez **Fournisseurs de ressources.**

1. Importez les packages nécessaires dans votre code. Utilisez le dernier profil disponible sur Azure Stack pour importer le module réseau :

   ```go
   package main

   import (
       "context"
       "fmt"
       "github.com/Azure/azure-sdk-for-go/profiles/2019-03-01/network/mgmt/network"
       "github.com/Azure/go-autorest/autorest"
       "github.com/Azure/go-autorest/autorest/adal"
       "github.com/Azure/go-autorest/autorest/to"
   )
   ```

2. Définissez les variables d’environnement. Pour créer un réseau virtuel, vous devez avoir un groupe de ressources.

   ```go
   var (
       activeDirectoryEndpoint = "yourLoginEndpointFromResourceManagerUrlMetadata"
       tenantID = "yourAzureStackTenantID"
       clientID = "yourServicePrincipalApplicationID"
       clientSecret = "yourServicePrincipalSecret"
       activeDirectoryResourceID = "yourAudienceFromResourceManagerUrlMetadata"
       subscriptionID = "yourSubscriptionID"
       baseURI = "yourResourceManagerURL"
       resourceGroupName = "existingResourceGroupName"
   )
   ```

3. Une fois que vous avez défini vos variables d’environnement, ajoutez une méthode pour créer un jeton d’authentification à l’aide du package **adal**. Pour plus d’informations sur l’authentification, consultez la section précédente.

   ```go
   //CreateToken creates a service principal token
   func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
      token, err = adal.NewServicePrincipalToken(
          *oauthConfig,
          clientID,
          clientSecret,
          activeDirectoryResourceID)
      return token, err
   }
   ```

4. Ajoutez la méthode `main`. La méthode `main` obtient d’abord un jeton à l’aide de la méthode définie à l’étape précédente. Elle crée ensuite un client à l’aide d’un module réseau du profil. Enfin, elle crée un réseau virtuel.

   ```go
   package main

   import (
      "context"
      "fmt"
      "github.com/Azure/azure-sdk-for-go/profiles/2019-03-01/network/mgmt/network"
      "github.com/Azure/go-autorest/autorest"
      "github.com/Azure/go-autorest/autorest/adal"
      "github.com/Azure/go-autorest/autorest/to"
   )

   var (
      activeDirectoryEndpoint = "yourLoginEndpointFromResourceManagerUrlMetadata"
      tenantID = "yourAzureStackTenantID"
      clientID = "yourServicePrincipalApplicationID"
      clientSecret = "yourServicePrincipalSecret"
      activeDirectoryResourceID = "yourAudienceFromResourceManagerUrlMetadata"
     subscriptionID = "yourSubscriptionID"
     baseURI = "yourResourceManagerURL"
     resourceGroupName = "existingResourceGroupName"
   )

   //CreateToken creates a service principal token
   func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
      token, err = adal.NewServicePrincipalToken(
          *oauthConfig,
          clientID,
          clientSecret,
          activeDirectoryResourceID)
      return token, err
   }

   func main() {
      token, _ := CreateToken()
      vnetClient := network.NewVirtualNetworksClientWithBaseURI(baseURI, subscriptionID)
      vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
      future, _ := vnetClient.CreateOrUpdate(
          context.Background(),
          resourceGroupName,
          "sampleVnetName",
          network.VirtualNetwork{
              Location: to.StringPtr("local"),
              VirtualNetworkPropertiesFormat: &network.VirtualNetworkPropertiesFormat{
                  AddressSpace: &network.AddressSpace{
                      AddressPrefixes: &[]string{"10.0.0.0/8"},
                  },
                  Subnets: &[]network.Subnet{
                      {
                          Name: to.StringPtr("subnetName"),
                          SubnetPropertiesFormat: &network.SubnetPropertiesFormat{
                              AddressPrefix: to.StringPtr("10.0.0.0/16"),
                          },
                      },
                  },
              },
          })
      err := future.WaitForCompletionRef(context.Background(), vnetClient.Client)
      if err != nil {
          fmt.Printf(err.Error())
          return
      }
   }
   ```
Parmi les exemples de code disponibles pour Azure Stack à l’aide du kit SDK Go, citons :
- [Créer une machine virtuelle](https://github.com/Azure-Samples/Hybrid-Compute-Go-Create-VM)
- [Plan de données du stockage](https://github.com/Azure-Samples/Hybrid-Storage-Go-Dataplane)
- [Utiliser des disques managés](https://github.com/Azure-Samples/Hybrid-Compute-Go-ManagedDisks) (exemple utilisant le profil 2019-03-01 qui cible les dernières versions d’API prises en charge par Azure Stack)
## <a name="next-steps"></a>Étapes suivantes

- [Installer PowerShell pour Azure Stack](../operator/azure-stack-powershell-install.md)
- [Configurez l’environnement PowerShell de l’utilisateur Azure Stack.](azure-stack-powershell-configure-user.md)  
