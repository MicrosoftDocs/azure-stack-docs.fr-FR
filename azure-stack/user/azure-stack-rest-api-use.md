---
title: Envoi de demandes API à Azure Stack Hub
description: Découvrez comment récupérer une authentification d’Azure pour effectuer des requêtes d’API auprès d’Azure Stack Hub.
author: sethmanheim
ms.topic: article
ms.date: 05/06/2020
ms.author: sethm
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2020
ms.openlocfilehash: d44158342b1bca1aec575d51fb7144a8c88e88d1
ms.sourcegitcommit: 9894804f31527234d43f4a93a9b7c106c8540435
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967741"
---
<!--  cblackuk and charliejllewellyn. This is a community contribution by cblackuk-->

# <a name="make-api-requests-to-azure-stack-hub"></a>Envoi de demandes API à Azure Stack Hub

Vous pouvez utiliser les API REST Azure Stack Hub pour automatiser des opérations comme l’ajout d’une machine virtuelle à votre cloud Azure Stack Hub.

Les API demandent à votre client de s’authentifier auprès du point de terminaison de connexion Microsoft Azure. Le point de terminaison retourne un jeton à utiliser dans l’en-tête de chaque requête envoyée aux API Azure Stack Hub. Microsoft Azure utilise Oauth 2.0.

Cet article fournit des exemples qui utilisent l’utilitaire **cURL** pour créer des requêtes Azure Stack Hub. cURL est un outil en ligne de commande disposant d’une bibliothèque pour le transfert de données. Ces exemples détaillent le processus de récupération d’un jeton pour accéder aux API Azure Stack Hub. La plupart des langages de programmation proposent des bibliothèques Oauth 2.0, qui prennent en charge de lourdes tâches de gestion des jetons et de manipulation des tâches, comme l’actualisation des jetons.

Examinez l’ensemble du processus d’utilisation des API REST Azure Stack Hub avec un client REST générique, comme **cURL**, pour mieux comprendre les requêtes sous-jacentes et voir ce que vous pouvez attendre du contenu reçu dans une réponse.

Cet article ne traite pas de l’ensemble des options disponibles pour la récupération des jetons, comme la connexion interactive ou la création d’ID d’application dédiés. Pour des informations sur ces sujets, consultez [Informations de référence sur les API REST Azure](/rest/api/).

## <a name="get-a-token-from-azure"></a>Récupérer un jeton d’Azure

Créez un corps de demande à l’aide du type de contenu `x-www-form-urlencoded` afin d’obtenir un jeton d’accès. Exécutez la commande POST afin de publier votre requête sur le point de terminaison de connexion et d’authentification Azure REST.

### <a name="uri"></a>URI

```bash  
POST https://login.microsoftonline.com/{tenant id}/oauth2/token
```

L’élément **Tenant ID** est soit :

- Votre domaine de locataire, par exemple `fabrikam.onmicrosoft.com`
- Votre ID de locataire, par exemple `8eaed023-2b34-4da1-9baa-8bc8c9d6a491`
- La valeur par défaut des clés indépendantes du locataire : `common`

### <a name="post-body"></a>Corps de publication

```bash  
grant_type=password
&client_id=1950a258-227b-4e31-a9cf-717495945fc2
&resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
&username=admin@fabrikam.onmicrosoft.com
&password=Password123
&scope=openid
```

Pour chaque valeur :

- **grant_type** :  
   Type de schéma d’authentification que vous utiliserez. Dans cet exemple, la valeur est `password`.

- **resource** :  
   La ressource à laquelle le jeton accède. Pour rechercher la ressource, interrogez le point de terminaison des métadonnées d’administration Azure Stack Hub. Examinez la section **audiences**.

- **Point de terminaison d’administration Azure Stack Hub** :

   ```bash
   https://management.{region}.{Azure Stack Hub domain}/metadata/endpoints?api-version=2015-01-01
   ```

  > [!NOTE]  
  > Si vous êtes administrateur et que vous essayez d’accéder à l’API locataire, veillez à utiliser le point de terminaison de locataire ; par exemple, `https://adminmanagement.{region}.{Azure Stack Hub domain}/metadata/endpoints?api-version=2015-01-011`.

  Par exemple, avec le Kit de développement Azure Stack comme point de terminaison :

   ```bash
   curl 'https://management.local.azurestack.external/metadata/endpoints?api-version=2015-01-01'
   ```

  Réponse :

  ```bash
  {
  "galleryEndpoint":"https://adminportal.local.azurestack.external:30015/",
  "graphEndpoint":"https://graph.windows.net/",
  "portalEndpoint":"https://adminportal.local.azurestack.external/",
  "authentication":{
     "loginEndpoint":"https://login.windows.net/",
     "audiences":["https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155"]
     }
  }
  ```

### <a name="example"></a>Exemple

  ```bash
  https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
  ```

- **client_id**

  Cette valeur est codée en dur sur une valeur par défaut :

  ```bash
  1950a258-227b-4e31-a9cf-717495945fc2
  ```

  D’autres options sont disponibles pour des scénarios spécifiques :

  | Application | ApplicationID |
  | --------------------------------------- |:-------------------------------------------------------------:|
  | LegacyPowerShell | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417 |
  | PowerShell | 1950a258-227b-4e31-a9cf-717495945fc2 |
  | WindowsAzureActiveDirectory | 00000002-0000-0000-c000-000000000000 |
  | VisualStudio | 872cd9fa-d31f-45e0-9eab-6e460a02d1f1 |
  | AzureCLI | 04b07795-8ddb-461a-bbee-02f9e1bf7b46 |

- **username**

  Par exemple, le compte Azure Stack Hub Azure AD :

  ```bash
  azurestackadmin@fabrikam.onmicrosoft.com
  ```

- **mot de passe**

  Le mot de passe administrateur Azure Stack Hub Azure AD.

### <a name="example"></a>Exemple

Demande :

```bash
curl -X "POST" "https://login.windows.net/fabrikam.onmicrosoft.com/oauth2/token" \
-H "Content-Type: application/x-www-form-urlencoded" \
--data-urlencode "client_id=1950a258-227b-4e31-a9cf-717495945fc2" \
--data-urlencode "grant_type=password" \
--data-urlencode "username=admin@fabrikam.onmicrosoft.com" \
--data-urlencode 'password=Password12345' \
--data-urlencode "resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155"
```

Réponse :

```bash
{
  "token_type": "Bearer",
  "scope": "user_impersonation",
  "expires_in": "3599",
  "ext_expires_in": "0",
  "expires_on": "1512574780",
  "not_before": "1512570880",
  "resource": "https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155",
  "access_token": "eyJ0eXAiOi...truncated for readability..."
}
```

## <a name="api-queries"></a>Requêtes d’API

Une fois que vous avez obtenu le jeton d’accès, ajoutez-le comme en-tête à chacune de vos demandes d’API. Pour l’ajouter comme en-tête, créez un en-tête **authorization** avec la valeur `Bearer <access token>`. Par exemple :

Demande :

```bash  
curl -H "Authorization: Bearer eyJ0eXAiOi...truncated for readability..." 'https://adminmanagement.local.azurestack.external/subscriptions?api-version=2016-05-01'
```

Réponse :

```bash  
offerId : /delegatedProviders/default/offers/92F30E5D-F163-4C58-8F02-F31CFE66C21B
id : /subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8
subscriptionId : 800c4168-3eb1-406b-a4ca-919fe7ee42e8
tenantId : 9fea4606-7c07-4518-9f3f-8de9c52ab628
displayName : Default Provider Subscription
state : Enabled
subscriptionPolicies : @{locationPlacementId=AzureStack}
```

### <a name="url-structure-and-query-syntax"></a>Structure d’URL et syntaxe de requête

URI de requête générique, qui comprend : `{URI-scheme} :// {URI-host} / {resource-path} ? {query-string}`

- **Schéma d’URI** :  
L’URI indique le protocole utilisé pour envoyer la requête. Par exemple, `http` ou `https`.
- **Hôte de l’URI** :  
L’hôte spécifie le nom de domaine ou l’adresse IP du serveur hébergeant le point de terminaison du service REST, comme `graph.microsoft.com` ou `adminmanagement.local.azurestack.external`.
- **Chemin d’accès de la ressource** :  
Le chemin d’accès spécifie la ressource ou la collection de ressources, et peut ainsi inclure plusieurs segments utilisés par le service pour sélectionner ces ressources. Par exemple : `beta/applications/00003f25-7e1f-4278-9488-efc7bac53c4a/owners` peut être utilisé pour interroger la liste de propriétaires d’applications spécifiques dans la collection d’applications.
- **Chaîne de requête** :  
La chaîne fournit des paramètres simples supplémentaires, comme la version d’API ou les critères de sélection des ressources.

## <a name="azure-stack-hub-request-uri-construct"></a>Construction de l’URI de requête Azure Stack Hub

```bash
{URI-scheme} :// {URI-host} / {subscription id} / {resource group} / {provider} / {resource-path} ? {OPTIONAL: filter-expression} {MANDATORY: api-version}
```

### <a name="uri-syntax"></a>Syntaxe d’URI

```bash
https://adminmanagement.local.azurestack.external/{subscription id}/resourcegroups/{resource group}/providers/{provider}/{resource-path}?{api-version}
```

### <a name="query-uri-example"></a>Exemple d’URI de requête

```bash
https://adminmanagement.local.azurestack.external/subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8/resourcegroups/system.local/providers/microsoft.infrastructureinsights.admin/regionhealths/local/Alerts?$filter=(Properties/State eq 'Active') and (Properties/Severity eq 'Critical')&$orderby=Properties/CreatedTimestamp desc&api-version=2016-05-01"
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation des points de terminaison Azure REST, consultez [Informations de référence sur les API REST Azure](/rest/api/).
