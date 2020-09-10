---
title: Utiliser des profils de version d’API avec GO dans Azure Stack Hub
description: Découvrez comment utiliser des profils de version d’API avec Go dans Azure Stack Hub.
author: sethmanheim
ms.topic: article
ms.date: 09/02/2020
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/26/2019
ms.openlocfilehash: 3b8df9de2975c7ba0e6eefdb10a2731cd5d47ca6
ms.sourcegitcommit: 7c01ab4b2e2250a7acd67d1c5ba27d15c1e8bce0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89448671"
---
# <a name="use-api-version-profiles-with-go-in-azure-stack-hub"></a>Utiliser des profils de version d’API avec Go dans Azure Stack Hub

## <a name="go-and-version-profiles"></a>Go et les profils de version

Un profil est une combinaison de différents types de ressources dans différentes versions provenant de différents services. L’utilisation d’un profil vous aide à combiner différents types de ressource. Les profils peuvent offrir les avantages suivants :

- La stabilité de votre app en verrouillant sur des versions d’API spécifiques.
- La compatibilité de votre app avec Azure Stack Hub et les centres de données régionaux Azure.

Dans le SDK Go, les profils sont disponibles sous le chemin des profils. Les numéros de version de profil sont étiquetés au format **AAAA-MM-JJ**. La dernière version du profil d’API Azure Stack Hub est la version **2019-03-01** pour Azure Stack Hub versions 1904 ou ultérieures. Pour importer un service donné à partir d’un profil, importez son module correspondant à partir du profil. Par exemple, pour importer le service **Compute** à partir du profil **2019-03-01**, utilisez le code suivant :

```go
import "github.com/Azure/azure-sdk-for-go/profiles/2019-03-01/compute/mgmt/compute"
```

## <a name="install-the-azure-sdk-for-go"></a>Installer le kit de développement logiciel Microsoft Azure SDK pour Go

1. Installez Git. Pour obtenir des instructions, consultez [Démarrage rapide - Installation de Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
2. Installez le [langage de programmation Go](https://golang.org/dl). Les profils d’API pour Azure nécessitent Go version 1.9 ou une version ultérieure.
3. Installez le SDK Azure Go et ses dépendances en exécutant la commande Bash suivante :

   ```bash
   go get -u -d github.com/Azure/azure-sdk-for-go/...
   ```

### <a name="the-go-sdk"></a>Kit SDK Go

Pour plus d’informations sur le kit SDK Azure Go, cliquez sur les liens suivants :

- Le Kit de développement logiciel (SDK) sur la page [Installation du Kit de développement logiciel Microsoft Azure SDK pour Go](/go/azure/azure-sdk-go-install).
- Le SDK Azure Go est disponible publiquement sur GitHub dans le dépôt [azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go).

### <a name="go-autorest-dependencies"></a>Dépendances de Go-AutoRest

Le kit SDK Go dépend des modules Azure **Go-AutoRest** pour l’envoi des requêtes REST aux points de terminaison Azure Resource Manager. Vous devez importer les dépendances du module Azure **Go-AutoRest** à partir de [Azure Go-AutoRest on GitHub](https://github.com/Azure/go-autorest). Vous trouverez les commandes Bash d’installation dans la section **Install**.

## <a name="how-to-use-go-sdk-profiles-on-azure-stack-hub"></a>Comment utiliser les profils du Kit de développement logiciel (SDK) Go sur Azure Stack Hub

Pour exécuter un exemple de code Go sur Azure Stack Hub, procédez comme suit :

1. Installez Azure SDK pour Go et ses dépendances. Pour obtenir des instructions, consultez la section précédente, [Installer le Kit de développement logiciel Microsoft Azure SDK pour Go](#install-the-azure-sdk-for-go).
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

3. Si vous n’en avez pas de disponible, créez un abonnement et enregistrez l’ID d’abonnement pour une utilisation ultérieure. Pour plus d’informations sur la création d’un abonnement, voir [Créer des abonnements pour des offres dans Azure Stack Hub](../operator/azure-stack-subscribe-plan-provision-vm.md).

4. Créez un principal de service utilisant une clé secrète client, avec l’étendue **Abonnement** et le rôle **Propriétaire**. Enregistrez l’ID et le secret du principal de service. Pour plus d’informations sur la création d’un principal de service pour Azure Stack Hub, voir [Utiliser une identité d’application pour accéder aux ressources](../operator/azure-stack-create-service-principals.md). Votre environnement Azure Stack Hub est désormais configuré.

5. Importez un module de service à partir du profil de kit SDK Go dans votre code. La version actuelle du profil Azure Stack Hub est **2019-03-01**. Par exemple, pour importer un module réseau à partir du type de profil **2019-03-01**, utilisez le code suivant :

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

   Pour créer le jeton, consultez la prochaine section.  

7. Appelez les méthodes de l’API à l’aide du client que vous avez créé à l’étape précédente. Par exemple, pour créer un réseau virtuel à l’aide du client de l’étape précédente, consultez l’exemple suivant :

   ```go
   package main

   import "github.com/Azure/azure-sdk-for-go/profiles/2019-03-01/network/mgmt/network"
   func main() {
   vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
   vnetClient .Authorizer = autorest.NewBearerAuthorizer(token)

   vnetClient .CreateOrUpdate( )
   ```

Pour obtenir un exemple complet de création d’un réseau virtuel sur Azure Stack Hub à l’aide du profil du Kit de développement logiciel (SDK) Go, voir l’[exemple](#example).

## <a name="authentication"></a>Authentication

Pour obtenir la propriété **Authorizer** d’Azure Active Directory à l’aide du kit SDK Go, installez les modules **Go-AutoRest**. Ces modules doivent déjà avoir été installés avec l'installation du « SDK Go ». Si ce n'est pas le cas, installez le [package d'authentification de GitHub](https://github.com/Azure/go-autorest/tree/master/autorest/adal).

L’agent d’autorisation doit être défini comme l’agent d’autorisation pour le client de la ressource. Il existe différentes manières d’obtenir des jetons d’autorisation sur Azure Stack Hub à l’aide des informations d’identification du client :

1. Si un principal de service disposant du rôle de propriétaire sur l’abonnement est disponible, ignorez cette étape. Sinon, voir [Utiliser une identité d’application pour accéder aux ressources](../operator/azure-stack-create-service-principals.md) afin d’obtenir des instructions sur la création d’un principal de service utilisant une clé secrète client, et une aide sur l’attribution à celui-ci d’un rôle « propriétaire » étendu à votre abonnement. Veillez à capturer l’ID d’application et le secret du principal de service.

2. Importez le package **adal** à partir de **Go-AutoRest** dans votre code.

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

   Affectez à `<activeDirectoryEndpoint>` la valeur de la propriété `loginEndpoint` provenant des métadonnées `ResourceManagerUrl` récupérées dans la section précédente de ce document. Définissez la valeur `<tenantID>` sur votre ID de locataire Azure Stack Hub.

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

## <a name="example"></a>Exemple

Cet exemple présente un échantillon de code Go créant un réseau virtuel sur Azure Stack Hub. Pour obtenir des exemples complets du kit SDK Go, consultez le [dépôt d’exemples du kit SDK Azure Go](https://github.com/Azure-Samples/azure-sdk-for-go-samples). Des exemples Azure Stack Hub sont disponibles dans le chemin hybride des dossiers de service du dépôt.

> [!NOTE]  
> Pour exécuter le code de cet exemple, vérifiez que le fournisseur de ressources **Réseau** de l’abonnement utilisé est listé comme étant **Inscrit**. Pour vérifier, recherchez l’abonnement sur le portail Azure Stack Hub, puis sélectionnez **Fournisseurs de ressources**.

1. Importez les packages nécessaires dans votre code. Utilisez le dernier profil disponible sur Azure Stack Hub pour importer le module réseau :

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

Parmi les exemples de code disponibles pour Azure Stack Hub utilisant le Kit de développement logiciel (SDK) Go, citons :

- [Créer une machine virtuelle](https://github.com/Azure-Samples/Hybrid-Compute-Go-Create-VM)
- [Plan de données du stockage](https://github.com/Azure-Samples/Hybrid-Storage-Go-Dataplane)
- [Utiliser des disques managés](https://github.com/Azure-Samples/Hybrid-Compute-Go-ManagedDisks) (exemple utilisant le profil 2019-03-01 qui cible les dernières versions d’API prises en charge par Azure Stack Hub)

## <a name="next-steps"></a>Étapes suivantes

- [Installer PowerShell pour Azure Stack Hub](../operator/azure-stack-powershell-install.md)
- [Configurez l’environnement PowerShell de l’utilisateur Azure Stack Hub.](azure-stack-powershell-configure-user.md)
