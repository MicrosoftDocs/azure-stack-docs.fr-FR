---
title: Utiliser des profils de version des API avec .NET dans Azure Stack Hub
description: Apprenez à utiliser des profils de version des API avec le kit de développement logiciel (SDK) .NET dans Azure Stack Hub.
author: sethmanheim
ms.topic: article
ms.date: 12/2/2020
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: 0b790eebb276c23f5204d3f6c178c9e6ba54480b
ms.sourcegitcommit: 52c934f5eeb5fcd8e8f2ce3380f9f03443d1e445
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/07/2021
ms.locfileid: "97973994"
---
# <a name="use-api-version-profiles-with-net-in-azure-stack-hub"></a>Utiliser des profils de version des API avec .NET dans Azure Stack Hub

Le kit de développement logiciel (SDK) .NET pour Azure Stack Hub Resource Manager fournit des outils pour vous aider à créer et gérer votre infrastructure. Le kit de développement logiciel (SDK) comporte des fournisseurs de ressources de calcul, de réseau, de stockage, de services d’application et [KeyVault](/azure/key-vault/key-vault-whatis). Le SDK .NET inclut 14 packages NuGet. Vous devez télécharger ces packages dans votre solution chaque fois que vous compilez votre projet. Toutefois, vous pouvez télécharger spécifiquement le fournisseur de ressources que vous utiliserez pour les versions **2019-03-01-hybrid** ou **2018-03-01-hybrid** afin d’optimiser la mémoire pour votre application. Chaque package se compose d’un fournisseur de ressources, de la version d’API correspondante et du profil d’API auquel il appartient. Les profils d'API du kit de développement logiciel (SDK) .NET permettent le développement du cloud hybride en vous offrant la possibilité de basculer entre les ressources Azure globales et les ressources disponibles sur Azure Stack Hub.

## <a name="net-and-api-version-profiles"></a>.NET et les profils de version d’API

Un profil d’API est une combinaison de fournisseurs de ressources et de versions d’API. Utilisez un profil d’API pour obtenir la version la plus récente et la plus stable de chaque type de ressource dans un package de fournisseur de ressources.

- Pour utiliser les versions les plus récentes de tous les services, utilisez le profil **La plus récente** des packages. Ce profil fait partie du package NuGet **Microsoft.Azure.Management**.

- Pour utiliser les services compatibles avec Azure Stack Hub, utilisez l'un des packages suivants :
  - **Microsoft.Azure.Management.Profiles.hybrid\_2019\_03\_01.<*ResourceProvider*>.0.9.0-preview.nupkg**
  - **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01.<*ResourceProvider*>.0.9.0-preview.nupkg**

  Vérifiez que la partie **ResourceProvider** du package NuGet ci-dessus est remplacée par le fournisseur approprié.

- Pour utiliser la dernière version d’API d’un service, utilisez le profil **La plus récente** du package NuGet spécifique. Par exemple, si vous souhaitez utiliser la **dernière version d’API** du service de calcul autonome, utilisez le profil **La plus récente** du package **Compute**. Le profil **La plus récente** fait partie du package NuGet **Microsoft.Azure.Management**.

- Pour utiliser des versions d’API spécifiques pour un type de ressource dans un fournisseur de ressources donné, utilisez les versions d’API spécifiques définies dans le package.

Vous pouvez combiner toutes les options dans la même application.

## <a name="install-the-azure-net-sdk"></a>Installer le Kit de développement logiciel (SDK) Azure .NET

- Installez Git. Pour obtenir des instructions, consultez [Démarrage rapide - Installation de Git][].

- Pour installer les packages NuGet corrects, consultez la rubrique [Finding and installing a package][] (Recherche et installation d’un package).

- Les packages à installer dépendent de la version de profil que vous voulez utiliser. Les noms de package pour les versions de profil sont les suivants :

  - **Microsoft.Azure.Management.Profiles.hybrid\_2019\_03\_01.<*ResourceProvider*>.0.9.0-preview.nupkg**

  - **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01.<*ResourceProvider*>.0.9.0-preview.nupkg**

- Pour installer les packages NuGet corrects pour Visual Studio Code, consultez le lien suivant afin de télécharger les [instructions du gestionnaire de package NuGet][].

- Si vous n’en avez pas de disponible, créez un abonnement et enregistrez l’ID d’abonnement pour une utilisation ultérieure. Pour plus d'informations sur la création d'un abonnement, consultez [Créer des abonnements à des offres dans Azure Stack Hub][].

- Créez un principal de service et enregistrez l’ID client, ainsi que la clé secrète client. Pour plus d'informations sur la création d'un principal de service pour Azure Stack Hub, consultez [Fournir aux applications l'accès à Azure Stack Hub][]. L’ID client est également connu en tant qu’ID d’application lors de la création d’un principal de service.

- Vérifiez que votre principal de service bénéficie du rôle contributeur/propriétaire sur votre abonnement. Pour plus d'informations sur l'attribution d'un rôle au principal de service, consultez [Fournir aux applications l'accès à Azure Stack Hub][].

## <a name="prerequisites"></a>Prérequis

Pour utiliser le kit de développement logiciel (SDK) .NET Azure avec Azure Stack Hub, vous devez fournir les valeurs ci-après, puis définir ces valeurs avec des variables d'environnement. Pour définir les variables d’environnement, consultez les instructions fournies sous le tableau qui correspondent à votre système d’exploitation spécifique.

| Valeur                     | Variables d'environnement   | Description                                                                                                             |
|---------------------------|-------------------------|-------------------------------------------------------------------------------------------------------------------------|
| ID client                 | `AZURE_TENANT_ID `      | La valeur de votre [*ID de locataire*][] Azure Stack Hub.                                                                          |
| ID client                 | `AZURE_CLIENT_ID `      | L’ID d’application du principal de service enregistré lors de la création du principal de service dans la section précédente de cet article. |
| Identifiant d’abonnement           | `AZURE_SUBSCRIPTION_ID` | L'[*ID d'abonnement*][] correspond à la façon dont vous accédez aux offres dans Azure Stack Hub.                                                      |
| Clé secrète client             | `AZURE_CLIENT_SECRET`   | Le secret d’application du principal de service enregistré lors de la création du principal de service.                                      |
| Point de terminaison Resource Manager | `ARM_ENDPOINT`          | Voir [*Point de terminaison Azure Stack Hub Resource Manager*][].                                                                    |
| Emplacement                  | `RESOURCE_LOCATION`     | Emplacement d'Azure Stack Hub.

Pour rechercher l'ID de locataire de votre environnement Azure Stack Hub, suivez les instructions fournies [dans cet article](../operator/azure-stack-csp-ref-operations.md). Pour définir vos variables d’environnement, procédez comme suit :

### <a name="windows"></a>Windows

Pour définir les variables d’environnement dans une invite de commandes Windows, utilisez le format suivant :

```shell
set Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="macos-linux-and-unix-based-systems"></a>Systèmes macOS, Linux et Unix

Dans les systèmes Unix, utilisez la commande suivante :

```shell
export Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="the-azure-stack-hub-resource-manager-endpoint"></a>Point de terminaison Azure Stack Hub Resource Manager

Azure Resource Manager est un framework de gestion qui permet aux administrateurs de déployer, gérer et superviser les ressources Azure. Azure Resource Manager peut gérer ces tâches en groupe, plutôt qu’individuellement, dans une seule opération.

Vous pouvez obtenir les informations de métadonnées du point de terminaison du Gestionnaire des ressources. Le point de terminaison retourne un fichier JSON avec les informations requises pour exécuter votre code.

Tenez compte des points suivants :

- Le **ResourceManagerUrl** dans le Kit de développement Azure Stack (ASDK) est : `https://management.local.azurestack.external/`.

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
         "audiences": ["https://management.yourtenant.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
      }
}
```

## <a name="existing-api-profiles"></a>Profils d’API existants

- **Microsoft.Azure.Management.Profiles.hybrid\_2019\_03\_01.<*ResourceProvider*>.0.9.0-preview.nupkg** : Dernier profil créé pour Azure Stack Hub. Utilisez ce profil pour que les services soient le plus compatible possible avec Azure Stack Hub, dès lors que vous utilisez la version 1904 ou une version ultérieure.

- **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01.<*ResourceProvider*>.0.9.0-preview.nupkg** : Utilisez ce profil afin que les services soient compatibles avec Azure Stack Hub pour les versions 1808 ou ultérieures.

- **Les dernières** : profil constitué des dernières versions de l’ensemble des services. Utilisez les dernières versions de tous les services. Ce profil fait partie du package NuGet **Microsoft.Azure.Management**.

Pour plus d'informations sur Azure Stack Hub et les profils d'API, consultez [Résumé des profils d’API][].

## <a name="azure-net-sdk-api-profile-usage"></a>Utilisation du profil d’API du Kit de développement logiciel (SDK) Azure .NET

Utilisez le code suivant pour instancier un client de gestion des ressources. Un code similaire peut être utilisé pour instancier d’autres clients fournisseurs de ressources (par exemple, pour le calcul, le réseau et le stockage).

```csharp
var client = new ResourceManagementClient(armEndpoint, credentials)
{
    SubscriptionId = subscriptionId;
};
```

Le paramètre `credentials` figurant dans ce code est obligatoire pour instancier un client. Le code suivant génère un jeton d’authentification en fonction de l’ID de client et du principal de service :

```csharp
var azureStackSettings = getActiveDirectoryServiceSettings(armEndpoint);
var credentials = ApplicationTokenProvider.LoginSilentAsync(tenantId, servicePrincipalId, servicePrincipalSecret, azureStackSettings).GetAwaiter().GetResult();
```

L'appel `getActiveDirectoryServiceSettings` figurant dans le code récupère les points de terminaison Azure Stack Hub à partir des points de terminaison de métadonnées. Il indique les variables d’environnement à partir de l’appel qui est effectué :

```csharp
public static ActiveDirectoryServiceSettings getActiveDirectoryServiceSettings(string armEndpoint)
{
    var settings = new ActiveDirectoryServiceSettings();
    try
    {
        var request = (HttpWebRequest)HttpWebRequest.Create(string.Format("{0}/metadata/endpoints?api-version=1.0", armEndpoint));
        request.Method = "GET";
        request.UserAgent = ComponentName;
        request.Accept = "application/xml";
        using (HttpWebResponse response = (HttpWebResponse)request.GetResponse())
        {
            using (StreamReader sr = new StreamReader(response.GetResponseStream()))
            {
                var rawResponse = sr.ReadToEnd();
                var deserialized = JObject.Parse(rawResponse);
                var authenticationObj = deserialized.GetValue("authentication").Value<JObject>();
                var loginEndpoint = authenticationObj.GetValue("loginEndpoint").Value<string>();
                var audiencesObj = authenticationObj.GetValue("audiences").Value<JArray>();
                settings.AuthenticationEndpoint = new Uri(loginEndpoint);
                settings.TokenAudience = new Uri(audiencesObj[0].Value<string>());
                settings.ValidateAuthority = loginEndpoint.TrimEnd('/').EndsWith("/adfs", StringComparison.OrdinalIgnoreCase) ? false : true;
            }
        }
    }
    catch (Exception ex)
    {
        Console.WriteLine(String.Format("Could not get AD service settings. Exception: {0}", ex.Message));
    }
    return settings;
}
```

Ces étapes vous permettent d'utiliser les packages NuGet de profils d'API pour déployer correctement votre application sur Azure Stack Hub.

## <a name="samples-using-api-profiles"></a>Exemples à l’aide de profils d’API

Vous pouvez utiliser les exemples suivants en guise de références pour la création de solutions avec des profils d'API .NET et Azure Stack Hub :

- [Gérer des groupes de ressources](https://github.com/Azure-Samples/hybrid-resources-dotnet-manage-resource-group)
- [Gérer des comptes de stockage](https://github.com/Azure-Samples/hybird-storage-dotnet-manage-storage-accounts)
- [Gérer une machine virtuelle](https://github.com/Azure-Samples/hybrid-compute-dotnet-manage-vm) : Cet exemple utilise le profil **2019-03-01-hybrid** pris en charge par Azure Stack Hub.

## <a name="next-steps"></a>Étapes suivantes

Découvrez-en plus sur les profils d’API :

- [Gérer les profils de version des API dans Azure Stack Hub](azure-stack-version-profiles.md)
- [Versions des API du fournisseur de ressources prises en charge par des profils dans Azure Stack](azure-stack-profiles-azure-resource-manager-versions.md)

  [Démarrage rapide - Installation de Git]: https://git-scm.com/book/en/v2/Getting-Started-Installing-Git
  [Finding and installing a package]: /nuget/tools/package-manager-ui (Recherche et installation d’un package)
  [Instructions du gestionnaire de package NuGet]: https://marketplace.visualstudio.com/items?itemName=jmrog.vscode-nuget-package-manager
  [Créer des abonnements à des offres dans Azure Stack Hub]: ../operator/azure-stack-subscribe-plan-provision-vm.md
  [Fournir aux applications l'accès à Azure Stack Hub]: ../operator/azure-stack-create-service-principals.md
  [*ID de locataire*]: ../operator/azure-stack-identity-overview.md
  [*ID d'abonnement*]: ../operator/service-plan-offer-subscription-overview.md#subscriptions
  [*Point de terminaison Azure Stack Hub Resource Manager*]: ../user/azure-stack-version-profiles-ruby.md#the-azure-stack-hub-resource-manager-endpoint
  [Résumé des profils d’API]: ../user/azure-stack-version-profiles.md#summary-of-api-profiles
  [Test Project to Virtual Machine, vNet, resource groups, and storage account]: https://github.com/seyadava/azure-sdk-for-net-samples/tree/master/TestProject
  [Use Azure PowerShell to create a service principal with a certificate]: ../operator/azure-stack-create-service-principals.md
  [Run unit tests with Test Explorer.]: /visualstudio/test/run-unit-tests-with-test-explorer?view=vs-2017&preserve-view=true
