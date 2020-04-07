---
title: Vue d’ensemble d’Event Hubs sur Azure Stack Hub
description: Découvrez le fournisseur de ressources Event Hubs sur Azure Stack Hub.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/27/2019
ms.reviewer: jfggdl
ms.lastreviewed: 11/27/2019
ms.openlocfilehash: b0a4e18afdc8ce68ffa212bcc7bc78506420c235
ms.sourcegitcommit: dd53af1b0fc2390de162d41e3d59545d1baad1a7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80424739"
---
# <a name="event-hubs-on-azure-stack-hub-overview"></a>Vue d’ensemble d’Event Hubs sur Azure Stack Hub

[!INCLUDE [preview-banner](../includes/event-hubs-preview.md)]

Event Hubs sur Azure Stack Hub vous permet de réaliser des scénarios de cloud hybride. Le streaming et les solutions basées sur les événements sont pris en charge pour le traitement par le cloud Azure et local. Que votre scénario soit hybride (connecté) ou déconnecté, votre solution peut prendre en charge le traitement des événements/flux à grande échelle. Votre scénario est limité uniquement par la taille du cluster Event Hubs, que vous pouvez provisionner en fonction de vos besoins. 

## <a name="features"></a>Fonctionnalités 

Les éditions Event Hubs (sur Azure Stack Hub et sur Azure) offrent un degré élevé de parité des fonctionnalités. Cette parité signifie que les SDK, les exemples, PowerShell, l’interface CLI et les portails offrent une expérience similaire, avec quelques différences. Le tableau suivant récapitule les principales différences en matière de disponibilité des fonctionnalités entre les éditions.  

| Fonctionnalité | Event Hubs sur Azure Stack Hub | Hubs d'événements Azure |
|-|-|-|-|
| Expérience de l’administrateur d’opérateur | ✔ | ✘ |
| Prise en charge de Kafka | ✔ | ✔ |
| Même ensemble de SDK | ✔ | ✔ |
| Autoriser l’accès à Event Hubs à l’aide d’Azure Active Directory | ✘ | ✔ |
| Fonctionnalité Capture | ✘ | ✔ |
| Géorécupération d’urgence | ✘ | ✔ |
| Azure Monitor | ✔ | ✔ |
| Fonctionnalité d’augmentation automatique | ✘ | ✔ |

De plus, les opérations de gestion des ressources Azure peuvent être effectuées à l’aide de modèles Azure Resource Manager, de [PowerShell](/powershell/module/azurerm.eventhub/) et d’[Azure CLI](/cli/azure/eventhubs/eventhub/). Actuellement, les opérations d’administration d’opérateur dans PowerShell et Azure CLI ne sont pas prises en charge.

## <a name="feature-documentation"></a>Documentation sur les fonctionnalités

La [documentation Azure Event Hubs](/azure/event-hubs/) s’applique aux deux éditions d’Event Hubs : Event Hubs sur Azure Stack Hub et Azure Event Hubs. Cette documentation contient des rubriques sur l’utilisation d’Event Hubs et des activités comme :

- Détails sur les [concepts Event Hubs](/azure/event-hubs/event-hubs-features)
- Guide pratique pour [créer un cluster et un espace de noms Event Hubs](event-hubs-quickstart-cluster-portal.md)
- Guide pratique pour créer un [hub d’événements](/azure/event-hubs/event-hubs-create#create-an-event-hub)
- Guide pratique pour diffuser en streaming [avec le protocole Kafka](/azure/event-hubs/event-hubs-quickstart-kafka-enabled-event-hubs)

### <a name="operator-documentation"></a>Documentation pour les opérateurs 
 
Pour en savoir plus sur l’expérience de l’opérateur Event Hubs sur Azure Stack Hub, reportez-vous à la [documentation pour les opérateurs Event Hubs](/azure-stack/operator/event-hubs-rp-overview). Cette documentation fournit des informations sur les activités comme :

- Installation d’Event Hubs.
- Mise d’Event Hubs à la disposition des utilisateurs.
- Obtention d’informations sur l’intégrité du service.
- Collecte des journaux.


## <a name="next-steps"></a>Étapes suivantes

Si Event Hubs n’est pas disponible dans votre abonnement, demandez à votre administrateur d’[installer le fournisseur de ressources Event Hubs sur Azure Stack Hub](../operator/event-hubs-rp-overview.md).

Si Event Hubs est installé et que vous êtes prêt à l’utiliser, consultez la [documentation Event Hubs](/azure/event-hubs/event-hubs-about) pour plus d’informations sur le service.
