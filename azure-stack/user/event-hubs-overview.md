---
title: Vue d’ensemble d’Event Hubs sur Azure Stack Hub
description: Découvrez Event Hubs sur Azure Stack Hub. Découvrez comment générer des solutions hybrides. Comparez les fonctionnalités d’Azure Event Hubs et d’Event Hubs sur Azure Stack Hub.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/27/2019
ms.reviewer: jfggdl
ms.lastreviewed: 11/27/2019
ms.openlocfilehash: adb00ebbe3f5ea3cf233ebaa485913d48ea63e23
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94546954"
---
# <a name="overview-of-event-hubs-on-azure-stack-hub"></a>Vue d’ensemble d’Event Hubs sur Azure Stack Hub

[!INCLUDE [preview-banner](../includes/event-hubs-preview.md)]

Event Hubs sur Azure Stack Hub vous permet de réaliser des scénarios de cloud hybride. Le streaming et les solutions basées sur les événements sont pris en charge pour le traitement par le cloud Azure et local. Que votre scénario soit hybride (connecté) ou déconnecté, votre solution peut prendre en charge le traitement des événements/flux à grande échelle. Votre scénario est limité uniquement par la taille du cluster Event Hubs, que vous pouvez provisionner en fonction de vos besoins. 

## <a name="run-event-processing-tasks-and-build-event-driven-applications-on-site"></a>Exécuter des tâches de traitement des événements et générer des applications pilotées par les événements sur le site

Avec Event Hubs sur Azure Stack Hub, vous pouvez implémenter des scénarios métier tels que :

- Charges de travail d’intelligence artificielle et de machine learning où Event Hubs est le moteur de streaming d’événements.
- Implémentation d’architectures pilotées par les événements dans vos propres sites en dehors des centres de données Azure.
- Analytique des parcours de visite pour vos applications web déployées localement.
- Analyse de la télémétrie des appareils.
- Traitement de flux avec des frameworks open source qui utilisent Apache Kafka comme Apache Spark, Flink, Storm et Samza.
- [Consommation de métriques et d’événements du système d’exploitation invité Compute](azure-stack-metrics-monitor.md).

## <a name="build-hybrid-solutions"></a>Générer des solutions hybrides

Générez des solutions hybrides qui ingèrent et traitent les données Edge localement sur votre instance Azure Stack Hub. Envoyez des données agrégées à Azure en vue d’un traitement, d’une visualisation et d’un stockage plus poussés. Le cas échéant, tirez parti de l’informatique Serverless sur Azure.

[![Diagramme de solutions hybrides](media/event-hubs-overview/hybrid-architecture-ehoash.png)](media/event-hubs-overview/hybrid-architecture-ehoash.png#lightbox)

## <a name="features"></a>Fonctionnalités 

Les éditions Event Hubs (sur Azure Stack Hub et sur Azure) offrent un degré élevé de parité des fonctionnalités. Cette parité signifie que les SDK, les exemples, PowerShell, l’interface CLI et les portails offrent une expérience similaire, avec quelques différences. Le tableau suivant récapitule les principales différences en matière de disponibilité des fonctionnalités entre les éditions.  

| Fonctionnalité | Event Hubs sur Azure Stack Hub | Azure Event Hubs |
|-|-|-|-|
| Expérience de l’administrateur d’opérateur | ✔ | ✘ |
| Prise en charge de Kafka | ✔ | ✔ |
| Même ensemble de SDK | ✔ | ✔ |
| Autoriser l’accès à Event Hubs à l’aide d’Azure Active Directory | ✘ | ✔ |
| Fonctionnalité Capture | ✘ | ✔ |
| Géorécupération d’urgence | ✘ | ✔ |
| Azure Monitor | ✔ | ✔ |
| Fonctionnalité d’augmentation automatique | ✘ | ✔ |

Les opérations de gestion des ressources Azure peuvent aussi être effectuées avec des modèles Azure Resource Manager, [PowerShell](/powershell/module/Az.eventhub/) et [Azure CLI](/cli/azure/eventhubs/eventhub/). Actuellement, les opérations d’administration d’opérateur dans PowerShell et Azure CLI ne sont pas prises en charge.

## <a name="feature-documentation"></a>Documentation sur les fonctionnalités

La [documentation Azure Event Hubs](/azure/event-hubs/) s’applique aux deux éditions d’Event Hubs : Event Hubs sur Azure Stack Hub et Azure Event Hubs. Cette documentation couvre des sujets sur l’utilisation d’Event Hubs et des activités comme :

- Détails sur les [concepts Event Hubs](/azure/event-hubs/event-hubs-features)
- Guide pratique pour [créer un cluster et un espace de noms Event Hubs](event-hubs-quickstart-cluster-portal.md)
- Guide pratique pour créer un [hub d’événements](/azure/event-hubs/event-hubs-create#create-an-event-hub)
- Guide pratique pour diffuser en streaming [avec le protocole Kafka](/azure/event-hubs/event-hubs-quickstart-kafka-enabled-event-hubs)

### <a name="operator-documentation"></a>Documentation pour les opérateurs 
 
Pour en savoir plus sur l’expérience de l’opérateur Event Hubs sur Azure Stack Hub, reportez-vous à la [documentation pour les opérateurs Event Hubs](../operator/event-hubs-rp-overview.md). Cette documentation fournit des informations sur les activités comme :

- Installation d’Event Hubs.
- Mise d’Event Hubs à la disposition des utilisateurs.
- Obtention d’informations sur l’intégrité du service.
- Collecte des journaux.


## <a name="next-steps"></a>Étapes suivantes

Si Event Hubs n’est pas disponible dans votre abonnement, demandez à votre administrateur d’[installer le fournisseur de ressources Event Hubs sur Azure Stack Hub](../operator/event-hubs-rp-overview.md).

Si Event Hubs est installé et que vous êtes prêt à l’utiliser, consultez la [documentation Event Hubs](/azure/event-hubs/event-hubs-about) pour plus d’informations sur le service.
