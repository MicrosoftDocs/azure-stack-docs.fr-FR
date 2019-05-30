---
title: API d’utilisation des ressources de locataire | Microsoft Docs
description: Informations de référence sur l’utilisation de ressources API, lesquelles récupèrent des informations relatives à l’utilisation d’Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 6aaeff9ef708bcb89147d89205aa6526ebdb874f
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66268223"
---
# <a name="tenant-resource-usage-api"></a>API d’utilisation des ressources de locataire

Un locataire peut utiliser l’API locataire pour afficher ses propres données d’utilisation des ressources. Cette API est cohérente avec l’API d’utilisation Azure (actuellement en préversion limitée).

Vous pouvez utiliser l’applet de commande Windows PowerShell **Get-UsageAggregates** pour obtenir des données d’utilisation comme dans Azure.

## <a name="api-call"></a>Appel d’API
### <a name="request"></a>Requête
La requête obtient les détails de la consommation pour les abonnements demandés et pour la période demandée. Il n’existe aucun corps de demande.

| **Méthode** | **URI de la requête** |
| --- | --- |
| GET |https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce/usageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&api-version=2015-06-01-preview&continuationToken={token-value} |

### <a name="arguments"></a>Arguments
| **Argument** | **Description** |
| --- | --- |
| *Armendpoint* |Point de terminaison Azure Resource Manager de votre environnement Azure Stack. La convention Azure Stack est que le nom du point de terminaison Azure Resource Manager soit au format `https://management.{domain-name}`. Par exemple, pour le kit de développement, si le nom du domaine est local.azurestack.external, le point de terminaison Resource Manager est `https://management.local.azurestack.external`. |
| *subId* |ID d’abonnement de l’utilisateur qui effectue l’appel. Vous pouvez utiliser cette API uniquement pour lancer une requête sur l’utilisation d’un abonnement unique. Les fournisseurs peuvent utiliser l’API d’utilisation des ressources de fournisseur pour effectuer une requête sur l’utilisation pour tous les locataires. |
| *reportedStartTime* |Heure de début de la requête. La valeur de *DateTime* doit être au format UTC et indiquer le début de l’heure ; par exemple, 13:00. Pour l’agrégation quotidienne, définissez cette valeur sur minuit au format UTC. Le format est l’ISO 8601 *échappé*, par exemple 2015-06-16T18%3a53%3a11%2b00%3a00Z, où les deux-points sont échappés avec %3a et le signe plus est échappé avec %2b afin qu’il soit adapté aux URI. |
| *reportedEndTime* |Heure de fin de la requête. Les contraintes qui s’appliquent à *reportedStartTime* s’appliquent également à cet argument. La valeur de *reportedEndTime* ne peut pas être ultérieure à la date actuelle. |
| *aggregationGranularity* |Paramètre facultatif qui a deux valeurs potentielles discrètes : quotidienne et horaire. Comme le suggèrent les valeurs, l’une retourne les données avec une granularité journalière, et l’autre est une résolution horaire. L’option quotidienne est la valeur par défaut. |
| *api-version* |Version du protocole utilisé pour effectuer cette requête. Vous devez utiliser 2015-06-01-preview. |
| *continuationToken* |Jeton récupéré à partir du dernier appel au fournisseur d’API d’utilisation. Ce jeton est nécessaire quand une réponse comprend plus de 1 000 lignes. Il joue alors le rôle de signet pour la progression. En son absence, les données sont récupérées à partir du début de la journée ou de l’heure, en fonction de la granularité transmise. |

### <a name="response"></a>response
GET /subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00&reportedEndTime=2015-06-01T00%3a00%3a00%2b00%3a00&aggregationGranularity=Daily&api-version=1.0

```json
{
"value": [
{

"id":
"/subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregate/sub1-meterID1",
"name": "sub1-meterID1",
"type": "Microsoft.Commerce/UsageAggregate",

"properties": {
"subscriptionId":"sub1",
"usageStartTime": "2015-03-03T00:00:00+00:00",
"usageEndTime": "2015-03-04T00:00:00+00:00",
"instanceData":"{\"Microsoft.Resources\":{\"resourceUri\":\"resourceUri1\",\"location\":\"Alaska\",\"tags\":null,\"additionalInfo\":null}}",
"quantity":2.4000000000,
"meterId":"meterID1"

}
},

. . .
```

### <a name="response-details"></a>Détails de la réponse
| **Argument** | **Description** |
| --- | --- |
| *id* |ID unique de l’agrégat d’utilisation |
| *name* |Nom de l’agrégat d’utilisation |
| *type* |Définition de la ressource |
| *subscriptionId* |Identificateur d’abonnement de l’utilisateur Azure |
| *usageStartTime* |Heure de début, au format UTC, du compartiment d’utilisation auquel appartient cet agrégat d’utilisation |
| *usageEndTime* |Heure de fin, au format UTC, du compartiment d’utilisation auquel appartient cet agrégat d’utilisation |
| *instanceData* |Paires clé-valeur des détails de l’instance (dans un nouveau format) :<br>  *resourceUri* : ID de ressource complet, notamment les groupes de ressources et le nom de l’instance <br>  *location* : région dans laquelle ce service a été exécuté <br>  *tags* : balises de ressources spécifiées par l’utilisateur <br>  *additionalInfo* : informations supplémentaires sur la ressource consommée, par exemple, la version du système d’exploitation ou le type d’image |
| *quantity* |Quantité de ressources consommées au cours de cette période |
| *meterId* |ID unique de la ressource consommée (également appelé *ResourceID*) |


## <a name="next-steps"></a>Étapes suivantes
[API Utilisation des ressources de fournisseur](azure-stack-provider-resource-api.md)

[Questions fréquentes (FAQ) relatives à l’utilisation](azure-stack-usage-related-faq.md)

