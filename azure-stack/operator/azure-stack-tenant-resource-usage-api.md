---
title: Informations de référence sur les API d'utilisation des ressources de locataire
titleSuffix: Azure Stack
description: Informations de référence sur les API d'utilisation des ressources, lesquelles récupèrent des informations relatives à l'utilisation d'Azure Stack Hub.
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
ms.date: 09/17/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 5e6fd1042edcf59955a6e766d2ffb215c49c2949
ms.sourcegitcommit: c4368652f0dd68c432aa1dabddbabf161a4a6399
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/13/2020
ms.locfileid: "75914716"
---
# <a name="tenant-resource-usage-api-reference"></a>Informations de référence sur l'API d'utilisation des ressources de locataire

Un locataire peut utiliser ses API pour voir ses propres données d’utilisation des ressources. Ces API sont cohérentes avec les API d’utilisation d’Azure.

Vous pouvez utiliser le cmdlet Windows PowerShell [Get-UsageAggregates](/powershell/module/azurerm.usageaggregates/get-usageaggregates) pour obtenir des données d'utilisation, exactement comme dans Azure.

## <a name="api-call"></a>Appel d’API

### <a name="request"></a>Requête

La requête obtient les détails de la consommation pour les abonnements demandés et pour la période demandée. Il n’existe aucun corps de requête.

| **Méthode** | **URI de la requête** |
| --- | --- |
| GET |https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce/usageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&api-version=2015-06-01-preview&continuationToken={token-value} |

### <a name="parameters"></a>Paramètres

| **Paramètre** | **Description** |
| --- | --- |
| ArmEndpoint |Point de terminaison Azure Resource Manager de votre environnement Azure Stack Hub. Par convention, dans l'infrastructure Azure Stack Hub, le nom du point de terminaison Azure Resource Manager est au format `https://management.{domain-name}`. Par exemple, pour le kit de développement, si le nom du domaine est local.azurestack.external, le point de terminaison Resource Manager est `https://management.local.azurestack.external`. |
| subId |ID d’abonnement de l’utilisateur qui effectue l’appel. Vous pouvez utiliser cette API uniquement pour lancer une requête sur l’utilisation d’un abonnement unique. Les fournisseurs peuvent utiliser l’API d’utilisation des ressources pour interroger l’utilisation de tous les locataires. |
| reportedStartTime |Heure de début de la requête. La valeur de *DateTime* doit être au format UTC et indiquer le début de l’heure ; par exemple, 13:00. Pour l’agrégation quotidienne, définissez cette valeur sur minuit au format UTC. Le format est l’ISO 8601 échappé, par exemple **2015-06-16T18%3a53%3a11%2b00%3a00Z**, où les deux-points sont échappés avec %3a, et le signe plus est échappé avec %2b afin qu’il soit adapté aux URI. |
| reportedEndTime |Heure de fin de la requête. Les contraintes qui s’appliquent à **reportedStartTime** s’appliquent également à ce paramètre. La valeur de **reportedEndTime** ne peut pas être ultérieure à la date actuelle. |
| aggregationGranularity |Paramètre facultatif qui peut prendre deux valeurs : **daily** et **hourly**. Comme le suggèrent les valeurs, l’une retourne les données avec une granularité journalière, et l’autre est une résolution horaire. L’option **daily** est la valeur par défaut. |
| api-version |Version du protocole utilisé pour effectuer cette requête. Vous devez utiliser **2015-06-01-preview**. |
| continuationToken |Jeton récupéré à partir du dernier appel au fournisseur d’API d’utilisation. Ce jeton est nécessaire quand une réponse compte plus de 1 000 lignes. Il sert de signet pour la progression. En son absence, les données sont récupérées à partir du début de la journée ou de l’heure, en fonction de la granularité transmise. |

### <a name="response"></a>response

```html
GET
/subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00&reportedEndTime=2015-06-01T00%3a00%3a00%2b00%3a00&aggregationGranularity=Daily&api-version=1.0
```

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

...
```

### <a name="response-details"></a>Détails de la réponse

| **Paramètre** | **Description** |
| --- | --- |
| id |ID unique de l’agrégat d’utilisation. |
| name |Nom de l’agrégat d’utilisation. |
| type |Définition de la ressource. |
| subscriptionId |Identificateur d’abonnement de l’utilisateur Azure. |
| usageStartTime |Heure de début, au format UTC, du compartiment d’utilisation auquel appartient cet agrégat d’utilisation. |
| usageEndTime |Heure de fin, au format UTC, du compartiment d’utilisation auquel appartient cet agrégat d’utilisation. |
| instanceData |Paires clé-valeur des détails de l’instance (dans un nouveau format) :<br>  *resourceUri* : ID de ressource complet, comprenant les groupes de ressources et le nom de l’instance. <br>  *location* : région dans laquelle ce service a été exécuté. <br>  *tags* : Étiquettes de ressources spécifiées par l’utilisateur. <br>  *additionalInfo* : détails supplémentaires sur la ressource utilisée. Par exemple, version du système d'exploitation ou type d'image. |
| quantité |Quantité de ressources consommées au cours de cette période. |
| meterId |ID unique de la ressource consommée (également appelé **ResourceID**). |

## <a name="next-steps"></a>Étapes suivantes

- [API Utilisation des ressources de fournisseur](azure-stack-provider-resource-api.md)
- [Questions fréquentes (FAQ) relatives à l’utilisation](azure-stack-usage-related-faq.md)
