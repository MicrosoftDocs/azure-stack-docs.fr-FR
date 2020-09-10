---
title: Utiliser des profils de version d’API avec Ruby dans Azure Stack Hub
description: Découvrez comment utiliser les profils de version d'API avec Ruby dans Azure Stack Hub.
author: sethmanheim
ms.topic: article
ms.date: 09/03/2020
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: 6813bf18656e034688255bcdb46b9b943359ce9c
ms.sourcegitcommit: 7c01ab4b2e2250a7acd67d1c5ba27d15c1e8bce0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89448620"
---
# <a name="use-api-version-profiles-with-ruby-in-azure-stack-hub"></a>Utiliser des profils de version d’API avec Ruby dans Azure Stack Hub

## <a name="ruby-and-api-version-profiles"></a>Ruby et les profils de version d’API

Le kit de développement logiciel (SDK) Ruby pour Azure Stack Hub Resource Manager fournit des outils pour vous aider à créer et à gérer votre infrastructure. Les fournisseurs de ressources du SDK incluent le calcul, les réseaux virtuels et le stockage avec le langage Ruby. Les profils d’API du kit de développement logiciel (SDK) Ruby permettent le développement du cloud hybride en vous offrant la possibilité de basculer entre les ressources Azure globales et les ressources sur Azure Stack Hub.

Un profil d’API est une combinaison de fournisseurs de ressources et de versions de service. Vous pouvez utiliser un profil d’API pour combiner différents types de ressources.

- Pour utiliser les versions les plus récentes de tous les services, utilisez le profil **Le plus récent** de la gemme cumulative du Kit de développement logiciel (SDK) Azure.
- Pour utiliser les services compatibles avec Azure Stack Hub, utilisez le profil **V2019_03_01_Hybrid** ou **V2018_03_01** de la gemme cumulative du kit de développement logiciel (SDK) Azure.
- Pour utiliser la dernière **version d’API** d’un service, utilisez le profil **Le plus récent** de la gemme spécifique. Par exemple, pour utiliser la dernière **version d’API** d’un service de calcul autonome, utilisez le profil **Le plus récent** de la gemme **Calcul**.
- Pour utiliser une **version d’API** spécifique d’un service, utilisez les versions d’API spécifiques définies dans la gemme.

> [!NOTE]
> Vous pouvez combiner toutes les options dans la même application.

## <a name="install-the-azure-ruby-sdk"></a>Installer le Kit de développement logiciel (SDK) Azure pour Ruby

- Suivez les instructions officielles pour installer [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
- Suivez les instructions officielles pour installer [Ruby](https://www.ruby-lang.org/en/documentation/installation/).
  - Lors de l’installation, choisissez **Ajouter Ruby à la variable PATH**.
  - Quand vous y êtes invité pendant l’installation de Ruby, installez le kit de développement.
  - Ensuite, installez le programme d’installation à l’aide de la commande suivante : 

       ```ruby
       Gem install bundler
       ```

- Si vous n’en avez pas de disponible, créez un abonnement et enregistrez l’ID d’abonnement pour une utilisation ultérieure. Des instructions sur la création d’un abonnement sont fournies dans l’article [Créer des abonnements pour des offres dans Azure Stack Hub](../operator/azure-stack-subscribe-plan-provision-vm.md).
- Créez un principal de service et enregistrez son ID et son secret. Des instructions sur la création d’un principal de service pour Azure Stack Hub sont fournies dans l’article [Utiliser une identité d’application pour accéder aux ressources](../operator/azure-stack-create-service-principals.md).
- Vérifiez que votre principal de service bénéficie du rôle contributeur/propriétaire attribué sur votre abonnement. Des instructions sur l'attribution d'un rôle à un principal de service sont fournies dans l'article [Utiliser une identité d'application pour accéder aux ressources](../operator/azure-stack-create-service-principals.md).

## <a name="install-the-rubygem-packages"></a>Installer les packages Rubygem

Vous pouvez installer les packages Rubygem Azure directement.

```ruby  
gem install azure_mgmt_compute
gem install azure_mgmt_storage
gem install azure_mgmt_resources
gem install azure_mgmt_network
```

Sinon, utilisez-les dans votre fichier Gemfile.

```ruby
gem 'azure_mgmt_storage'
gem 'azure_mgmt_compute'
gem 'azure_mgmt_resources'
gem 'azure_mgmt_network'
```

Le Kit de développement logiciel (SDK) Azure Resource Manager pour Ruby est en version préliminaire et son interface sera donc probablement très différente dans les prochaines versions. Un nombre plus élevé dans la version mineure peut indiquer ces modifications importantes.

## <a name="use-the-azure_sdk-gem"></a>Utiliser la gemme azure_sdk

La gemme **azure_sdk** est un cumul de toutes les gemmes prises en charge dans le Kit de développement logiciel (SDK) Ruby. Cette gemme se compose du profil  **Le plus récent** , qui prend en charge la version la plus récente de tous les services. Elle inclut les profils avec version  **V2017_03_09** et **V2019_03_01_Hybrid**, qui sont générés pour Azure Stack Hub.

Vous pouvez installer la gemme cumulative azure_sdk avec la commande suivante :  

```ruby  
gem install 'azure_sdk'
```

## <a name="prerequisites"></a>Prérequis

Pour utiliser le kit de développement logiciel (SDK) Azure pour Ruby avec Azure Stack Hub, vous devez fournir les valeurs ci-après, puis définir ces valeurs avec des variables d'environnement. Pour définir les variables d’environnement, consultez les instructions fournies sous le tableau qui correspondent à votre système d’exploitation spécifique.

| Valeur | Variables d'environnement | Description |
| --- | --- | --- |
| ID client | `AZURE_TENANT_ID` | Votre [ID de locataire](../operator/azure-stack-identity-overview.md) Azure Stack Hub. |
| ID client | `AZURE_CLIENT_ID` | L’ID d’application du principal de service enregistré lors de la création du principal de service dans la section précédente de cet article.  |
| Identifiant d’abonnement | `AZURE_SUBSCRIPTION_ID` | Vous utilisez l’[ID d’abonnement](../operator/service-plan-offer-subscription-overview.md#subscriptions) pour accéder à des offres dans Azure Stack Hub. |
| Clé secrète client | `AZURE_CLIENT_SECRET` | Le secret d’application du principal de service enregistré lors de la création du principal de service. |
| Point de terminaison Resource Manager | `ARM_ENDPOINT` | Voir [Point de terminaison Azure Stack Hub Resource Manager](#the-azure-stack-hub-resource-manager-endpoint).  |

### <a name="the-azure-stack-hub-resource-manager-endpoint"></a>Point de terminaison Azure Stack Hub Resource Manager

Microsoft Azure Resource Manager est un framework de gestion qui permet aux administrateurs de déployer, gérer et superviser les ressources Azure. Azure Resource Manager peut gérer ces tâches en groupe, plutôt qu’individuellement, dans une seule opération.

Vous pouvez obtenir les informations de métadonnées du point de terminaison du Gestionnaire des ressources. Le point de terminaison retourne un fichier JSON avec les informations requises pour exécuter votre code.

 > [!NOTE]  
 > Le **ResourceManagerUrl** dans le Kit de développement Azure Stack (ASDK) est : `https://management.local.azurestack.external/` Le **ResourceManagerUrl** dans les systèmes intégrés est : `https://management.region.<fqdn>/`, où `<fqdn>` est votre nom de domaine complet.  
 > Pour extraire les métadonnées requises : `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`
  
 Exemple de fichier JSON :

 ```json
 {
   "galleryEndpoint": "https://portal.local.azurestack.external:30015/",  
   "graphEndpoint": "https://graph.windows.net/",  
   "portal Endpoint": "https://portal.local.azurestack.external/",
   "authentication": {
     "loginEndpoint": "https://login.windows.net/",
     "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
   }
 }
```

### <a name="set-environment-variables"></a>Définir des variables d’environnement

#### <a name="microsoft-windows"></a>Microsoft Windows

Pour définir les variables d’environnement, utilisez le format suivant dans une invite de commandes Windows :

```console
set AZURE_TENANT_ID=<YOUR_TENANT_ID>
```

#### <a name="macos-linux-and-unix-based-systems"></a>Systèmes macOS, Linux et Unix

Dans les systèmes Unix, utilisez la commande suivante :

```bash
export AZURE_TENANT_ID=<YOUR_TENANT_ID>
```

## <a name="existing-api-profiles"></a>Profils d’API existants

La gemme cumulative **Azure_sdk** comprend les 3 profils suivants :

- **V2019_03_01_Hybrid** : Profil créé pour Azure Stack Hub. Utilisez ce profil pour toutes les dernières versions des services disponibles dans Azure Stack Hub version 1904 ou ultérieure.
- **V2017_03_09** : Profil créé pour Azure Stack Hub. Utilisez ce profil pour que les services offrent une compatibilité optimale avec Azure Stack Hub version 1808 ou antérieure.
- **Les dernières** : Profil composé des versions les plus récentes de tous les services. Utilisez les dernières versions de tous les services.

Pour plus d’informations sur les profils d’API et Azure Stack Hub, consultez la section [Résumé des profils d’API](azure-stack-version-profiles.md#summary-of-api-profiles).

## <a name="azure-ruby-sdk-api-profile-usage"></a>Utilisation du profil d’API du Kit de développement logiciel (SDK) Azure pour Ruby

Utilisez le code suivant pour instancier un client de profil. Ce paramètre est uniquement requis pour Azure Stack Hub ou d’autres clouds privés. Azure global a déjà ces paramètres par défaut.

```ruby  
active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])

provider = MsRestAzure::ApplicationTokenProvider.new(
  ENV['AZURE_TENANT_ID'],
  ENV['AZURE_CLIENT_ID'],
  ENV['AZURE_CLIENT_SECRET'],
  active_directory_settings
)
credentials = MsRest::TokenCredentials.new(provider)
options = {
  credentials: credentials,
  subscription_id: subscription_id,
  active_directory_settings: active_directory_settings,
  base_url: ENV['ARM_ENDPOINT']
}

# Target profile built for Azure Stack Hub
client = Azure::Resources::Profiles::V2019_03_01_Hybrid::Mgmt::Client.new(options)
```

Le client de profil peut être utilisé pour accéder aux fournisseurs de ressources individuels, tels que le calcul, le stockage et le réseau :

```ruby  
# To access the operations associated with Compute
profile_client.compute.virtual_machines.get 'RESOURCE_GROUP_NAME', 'VIRTUAL_MACHINE_NAME'

# Option 1: To access the models associated with Compute
purchase_plan_obj = profile_client.compute.model_classes.purchase_plan.new

# Option 2: To access the models associated with Compute
# Notice Namespace: Azure::Profiles::<Profile Name>::<Service Name>::Mgmt::Models::<Model Name>
purchase_plan_obj = Azure::Profiles::V2019_03_01_Hybrid::Compute::Mgmt::Models::PurchasePlan.new
```

## <a name="define-azure-stack-hub-environment-setting-functions"></a>Définir des fonctions de définition d’environnement Azure Stack Hub

Pour authentifier le principal de service à l’environnement Azure Stack Hub, définissez les points de terminaison à l’aide de `get_active_directory_settings()`. Cette méthode utilise la variable d’environnement **ARM_Endpoint** que vous avez définie précédemment :

```ruby  
# Get Authentication endpoints using Arm Metadata Endpoints
def get_active_directory_settings(armEndpoint)
  settings = MsRestAzure::ActiveDirectoryServiceSettings.new
  response = Net::HTTP.get_response(URI("#{armEndpoint}/metadata/endpoints?api-version=1.0"))
  status_code = response.code
  response_content = response.body
  unless status_code == "200"
    error_model = JSON.load(response_content)
    fail MsRestAzure::AzureOperationError.new("Getting Azure Stack Hub Metadata Endpoints", response, error_model)
  end
  result = JSON.load(response_content)
  settings.authentication_endpoint = result['authentication']['loginEndpoint'] unless result['authentication']['loginEndpoint'].nil?
  settings.token_audience = result['authentication']['audiences'][0] unless result['authentication']['audiences'][0].nil?
  settings
end
```

## <a name="samples-using-api-profiles"></a>Exemples à l’aide de profils d’API

Utilisez les exemples suivants sur GitHub en guise de références pour la création de solutions avec des profils d’API Ruby et Azure Stack Hub :

- [Manage Azure resources and resource groups with Ruby (Gérer des ressources et des groupes de ressources Azure avec Ruby)](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups).
- [Manage virtual machines using Ruby](https://github.com/Azure-Samples/Hybrid-Compute-Ruby-Manage-VM)(Gérer des machines virtuelles à l’aide de Ruby) (Exemple qui utilise le profil 2019-03-01-hybrid pour cibler les dernières versions d’API prises en charge par Azure Stack Hub).
- [Deploy an SSH Enabled VM with a Template in Ruby (Déployer une machine virtuelle compatible SSH à l’aide d’un modèle dans Ruby)](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Template-Deployment).

### <a name="sample-resource-manager-and-groups"></a>Exemple Resource Manager et groupes

Pour exécuter l’exemple, assurez-vous que vous avez installé Ruby. Si vous utilisez Visual Studio Code, téléchargez l'extention du Kit de développement logiciel (SDK) Ruby également.

> [!NOTE]  
> Le dépôt de l’exemple est [Hybrid-Resource-Manager-Ruby-Resources-And-Groups](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups).

1. Clonez le référentiel :

   ```console
   git clone https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups.git
   ```

2. Installez les dépendances à l’aide de l'offre groupée :

   ```console
   cd Hybrid-Resource-Manager-Ruby-Resources-And-Groups
   bundle install
   ```

3. Créez un principal de service Azure à l’aide de PowerShell et récupérez les valeurs requises.

   Pour obtenir des instructions sur la création d’un principal de service, consultez [Fournir l’accès des applications à Azure Stack](../operator/azure-stack-create-service-principals.md).

   Les valeurs requises sont :

   - ID client
   - ID client
   - Clé secrète client
   - Identifiant d’abonnement
   - Point de terminaison Resource Manager

   Définissez les variables d’environnement suivantes en utilisant les informations récupérées à partir du principal de service que vous avez créé :

   - `export AZURE_TENANT_ID={your tenant ID}`
   - `export AZURE_CLIENT_ID={your client ID}`
   - `export AZURE_CLIENT_SECRET={your client secret}`
   - `export AZURE_SUBSCRIPTION_ID={your subscription ID}`
   - `export ARM_ENDPOINT={your Azure Stack Hub Resource Manager URL}`

   > [!NOTE]  
   > Sur Windows, utilisez `set` au lieu de `export`.

4. Assurez-vous que la variable d’emplacement est définie sur votre emplacement Azure Stack Hub ; par exemple, `LOCAL="local"`.

5. Pour cibler les points de terminaison Active Directory appropriés, ajoutez la ligne de code suivante si vous utilisez Azure Stack Hub ou d’autres clouds privés :

   ```ruby  
   active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])
   ```

6. Dans la variable `options`, ajoutez les paramètres Active Directory et l’URL de base pour utiliser Azure Stack Hub :

   ```ruby  
   options = {
   credentials: credentials,
   subscription_id: subscription_id,
   active_directory_settings: active_directory_settings,
   base_url: ENV['ARM_ENDPOINT']
   }
   ```

7. Créez un client de profil qui cible le profil Azure Stack Hub :

   ```ruby  
   client = Azure::Resources::Profiles::V2019_03_01_Hybrid::Mgmt::Client.new(options)
   ```

8. Pour authentifier le principal de service avec Azure Stack Hub, les points de terminaison doivent être définis à l’aide de **get_active_directory_settings()** . Cette méthode utilise la variable d’environnement **ARM_Endpoint** que vous avez définie précédemment :

   ```ruby  
   def get_active_directory_settings(armEndpoint)
     settings = MsRestAzure::ActiveDirectoryServiceSettings.new
     response = Net::HTTP.get_response(URI("#{armEndpoint}/metadata/endpoints?api-version=1.0"))
     status_code = response.code
     response_content = response.body
     unless status_code == "200"
       error_model = JSON.load(response_content)
       fail MsRestAzure::AzureOperationError.new("Getting Azure Stack Hub Metadata Endpoints", response, error_model)
     end
     result = JSON.load(response_content)
     settings.authentication_endpoint = result['authentication']['loginEndpoint'] unless result['authentication']['loginEndpoint'].nil?
     settings.token_audience = result['authentication']['audiences'][0] unless result['authentication']['audiences'][0].nil?
     settings
   end
   ```

9. Exécutez l’exemple.

   ```ruby
   bundle exec ruby example.rb
   ```

## <a name="next-steps"></a>Étapes suivantes

- [Installer PowerShell pour Azure Stack Hub](../operator/azure-stack-powershell-install.md)
- [Configurer l’environnement PowerShell de l’utilisateur Azure Stack Hub](azure-stack-powershell-configure-user.md)  
