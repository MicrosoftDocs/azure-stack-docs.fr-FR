---
title: Inscrire des locataires pour un suivi de l’utilisation dans Azure Stack | Microsoft Docs
description: Découvrez comment inscrire des locataires et comment l’utilisation des locataires est suivie dans Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 10/14/2019
ms.openlocfilehash: 72310e813d0dd0a64575f1b2452bf4a5191638ef
ms.sourcegitcommit: 97d41b3ebed07aa85a50087b6076671fd37e08c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72350179"
---
# <a name="register-tenants-for-usage-tracking-in-azure-stack"></a>Inscrire des locataires pour un suivi de l’utilisation dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Cet article contient des détails sur les opérations d’inscription. Vous pouvez réaliser ces opérations pour :

- Gérer les inscriptions des locataires
- Gérer le suivi de l’utilisation des locataires

## <a name="add-tenant-to-registration"></a>Ajouter un locataire à l’inscription

Vous pouvez utiliser cette opération quand vous voulez ajouter un nouveau locataire à votre inscription. L’utilisation des locataires est signalée sous un abonnement Azure connecté avec le locataire Azure Active Directory (Azure AD).

Vous pouvez aussi utiliser cette opération pour changer l’abonnement associé à un locataire. Appelez PUT ou **New-AzureRMResource** pour remplacer le mappage précédent.

Vous ne pouvez associer qu’un seul abonnement Azure à un locataire. Si vous essayez d’ajouter un deuxième abonnement à un locataire existant, le premier abonnement est remplacé.

### <a name="use-api-profiles"></a>Utiliser des profils d’API

Les applets de commande d’inscription suivantes nécessitent la spécification d’un profil d’API lors de l’exécution de PowerShell. Les profils d’API représentent un ensemble de fournisseurs de ressources Azure et leurs versions d’API. Ils vous aident à utiliser la version appropriée de l’API lors de l’interaction avec plusieurs clouds Azure, Par exemple, si vous travaillez avec plusieurs clouds quand vous utilisez des infrastructures Azure et Azure Stack globales, les profils d’API spécifient un nom qui correspond à leur date de publication. Vous utilisez le profil **2017-09-03**.

Pour plus d’informations sur Azure Stack et les profils d’API, consultez [Gérer les profils de version des API dans Azure Stack](../user/azure-stack-version-profiles.md).

### <a name="parameters"></a>parameters

| Paramètre                  | Description |
|---                         | --- |
| registrationSubscriptionID | L’abonnement Azure qui était utilisé au moment de l’inscription. |
| customerSubscriptionID     | L’abonnement Azure (pas Azure Stack) appartenant au client à inscrire. Il doit être créé dans l’offre du fournisseur de solution cloud via l’Espace partenaires. Si un client a plusieurs locataires, créez un abonnement pour le locataire pour vous connecter à Azure Stack. |
| resourceGroup              | Le groupe de ressources Azure dans lequel est stockée votre inscription. |
| registrationName           | Le nom de l’inscription de votre compte Azure Stack. Il s’agit d’un objet stocké dans Azure. Le nom prend en général la forme **azurestack-CloudID**, où **CloudID** est l’ID du cloud de votre déploiement Azure Stack. |

> [!NOTE]  
> Les locataires doivent être inscrits auprès de chaque déploiement Azure Stack qu’ils utilisent. Si un locataire utilise plusieurs comptes Azure Stack, mettez à jour les inscriptions initiales de chaque déploiement avec l’abonnement du locataire.

### <a name="powershell"></a>PowerShell

Utilisez l’applet de commande **New-AzureRmResource** pour ajouter un locataire. [Connectez-vous à Azure Stack](azure-stack-powershell-configure-admin.md), puis à partir d'une invite de commandes avec élévation de privilèges , exécutez l’applet de commande suivante :

```powershell  
New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Appel d’API

**Opération** : PUT  
**RequestURI** : `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Réponse**: 201 Créé  
**Corps de réponse** : Vide  

## <a name="list-all-registered-tenants"></a>Répertorier tous les locataires inscrits

Obtenez une liste de tous les locataires qui ont été ajoutés à une inscription.

 > [!NOTE]  
 > Si aucun locataire n’a été inscrit, vous ne recevez aucune réponse.

### <a name="parameters"></a>parameters

| Paramètre                  | Description          |
|---                         | ---                  |
| registrationSubscriptionId | L’abonnement Azure qui était utilisé au moment de l’inscription.   |
| resourceGroup              | Le groupe de ressources Azure dans lequel est stockée votre inscription.    |
| registrationName           | Le nom de l’inscription de votre déploiement Azure Stack. Il s’agit d’un objet stocké dans Azure. Le nom prend en général la forme **azurestack-CloudID**, où **CloudID** est l’ID du cloud de votre déploiement Azure Stack.   |

### <a name="powershell"></a>PowerShell

Utilisez l’applet de commande **Get-AzureRmResource** pour lister tous les locataires inscrits. [Connectez-vous à Azure Stack](azure-stack-powershell-configure-admin.md), puis à partir d’une invite de commandes avec élévation de privilèges, exécutez l’applet de commande suivante :

```powershell
Get-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Appel d’API

Vous pouvez obtenir une liste de tous les mappages de locataires à l’aide de l’opération GET.

**Opération** : GET  
**RequestURI** : `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions?api-version=2017-06-01 HTTP/1.1`  
**Réponse**: 200  
**Corps de réponse** :

```json
{
    "value": [{
            "id": " subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{ cspSubscriptionId 1}",
            "name": " cspSubscriptionId 1",
            "type": "Microsoft.AzureStack\customerSubscriptions",
            "properties": { "tenantId": "tId1" }
        },
        {
            "id": " subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{ cspSubscriptionId 2}",
            "name": " cspSubscriptionId2 ",
            "type": "Microsoft.AzureStack\customerSubscriptions",
            "properties": { "tenantId": "tId2" }
        }
    ],
    "nextLink": "{originalRequestUrl}?$skipToken={opaqueString}"
}
```

## <a name="remove-a-tenant-mapping"></a>Supprimer un mappage de locataire

Vous pouvez supprimer un locataire qui a été ajouté à une inscription. Si ce locataire utilise toujours les ressources sur Azure Stack, son utilisation est facturée sur l’abonnement utilisé lors de l’inscription Azure Stack initiale.

### <a name="parameters"></a>parameters

| Paramètre                  | Description          |
|---                         | ---                  |
| registrationSubscriptionId | L’ID d’abonnement pour l’inscription.   |
| resourceGroup              | Le groupe de ressources pour l’inscription.   |
| registrationName           | Le nom de l’inscription.  |
| customerSubscriptionId     | L’ID de l’abonnement client.  |

### <a name="powershell"></a>PowerShell

Utilisez l’applet de commande **Remove-AzureRmResource** pour supprimer un abonné. [Connectez-vous à Azure Stack](azure-stack-powershell-configure-admin.md), puis à partir d’une invite de commandes avec élévation de privilèges, exécutez l’applet de commande suivante :

```powershell
Remove-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Appel d’API

Vous pouvez supprimer des mappages de locataires à l’aide de l’opération DELETE.

**Opération** : SUPPRIMER  
**RequestURI** : `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Réponse**: 204 Pas de contenu  
**Corps de réponse** : Vide

## <a name="next-steps"></a>Étapes suivantes

- [Guide pratique pour récupérer des informations sur l’utilisation des ressources à partir d’Azure Stack](azure-stack-billing-and-chargeback.md)
