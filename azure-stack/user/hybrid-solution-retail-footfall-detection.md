---
title: Solution hybride pour la mise en œuvre d'un modèle de détection des pas basé sur l'intelligence artificielle, utilisant Azure et Azure Stack
description: Découvrez comment utiliser les services Azure et Azure Stack pour mettre en œuvre un modèle de détection des pas basé sur l'intelligence artificielle afin d’analyser la fréquentation des magasins de détail.
services: azure-stack
documentationcenter: ''
author: BryanLa
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 09/23/2019
ms.openlocfilehash: 32533b063d4c42b740825923e9e2b5692ec03728
ms.sourcegitcommit: bbf3edbfc07603d2c23de44240933c07976ea550
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71714637"
---
# <a name="footfall-detection-solution"></a>Solution de détection des pas

Cet article offre une vue d’ensemble d'une solution de mise en œuvre d'un modèle de détection des pas basé sur l'intelligence artificielle. Ce modèle est utile pour analyser la fréquentation de magasins de détail. La solution fournit des informations à partir d'actions du monde réel, en utilisant Azure, Azure Stack, et le kit de développement d’intelligence artificielle personnalisé Vision AI.

## <a name="context-and-problem"></a>Contexte et problème

Les magasins Contoso aimeraient avoir un aperçu de la façon dont les clients accueillent leurs produits actuels, par rapport à l'agencement des magasins. Ils ne peuvent pas affecter du personnel dans chaque section, et demander à une équipe d'analystes d'examiner les séquences vidéo d'un magasin tout entier serait trop fastidieux. Par ailleurs, aucun des magasins ne dispose d'une bande passante suffisante pour diffuser la vidéo de toutes les caméras dans le cloud à des fins d'analyse. 

Contoso aimerait trouver une façon discrète et respectueuse de la vie privée d’identifier certaines caractéristiques de sa clientèle, notamment le nombre de clients, leur loyauté et leurs réactions face aux présentoirs et produits proposés en magasin.

## <a name="solution"></a>Solution

Cette solution d'analyse du commerce de détail utilise une approche à plusieurs niveaux pour l’inférence à la périphérie. En utilisant le kit de développement personnalisé Vision AI, seules les images affichant un visage humain sont envoyées pour analyse à une instance Azure Stack privée qui exécute Azure Cognitive Services. Les données anonymisées et agrégées sont envoyées à Azure pour être agrégées dans tous les magasins puis visualisées dans Power BI. En combinant l’inférence en périphérie et le cloud public, Contoso peut exploiter la technologie moderne de l'intelligence artificielle, tout en assurant la conformité avec les stratégies de l’entreprise et en respectant la vie privée de ses clients.

[![Modèle de détection des pas](media/hybrid-pattern-ai-footfall-detection/solution-architecture.png)](media/hybrid-pattern-ai-footfall-detection/solution-architecture.png)

Voici un résumé du fonctionnement de la solution : 

1. Le kit de développement personnalisé Vision AI reçoit une configuration de la part d’IoT Hub, qui installe le runtime IoT Edge et un modèle ML.
2. Si le modèle détecte une personne, il prend une photo et la télécharge sur le stockage Blob Azure Stack. 
3. Le service Blob déclenche une fonction Azure sur Azure Stack. 
4. La fonction Azure appelle un conteneur avec l'API Visage pour obtenir des données démographiques et émotionnelles de l'image.
5. Les données sont anonymisées et envoyées à un hub d’événements Azure.
6. Le hub d’événements transmet les données à Stream Analytics.
7. Stream Analytics agrège les données et les transmet à Power BI.

## <a name="components"></a>Composants

Cette solution utilise les composants suivants :

| Couche | Composant | Description |
|----------|-----------|-------------|
| Matériel en magasin | [Kit de développement d’intelligence artificielle personnalisé Vision AI](https://azure.github.io/Vision-AI-DevKit-Pages/) | Fournit un filtrage en magasin à l'aide d'un modèle ML local qui capture des images de personnes uniquement à des fins d'analyse. Configuré et mis à jour de façon sécurisée via l’IoT Hub.<br><br>|
| Azure Stack | [App Service](../operator/azure-stack-app-service-overview.md) | Le fournisseur de ressources App Service (RP) sert de base pour les composants en périphérie. Il inclut des options d'hébergement et de gestion pour les applications web/API et les fonctions. |
| | Cluster du moteur Azure Kubernetes Service [(AKS)](https://github.com/Azure/aks-engine) | Le fournisseur de ressources AKS avec le cluster du moteur AKS déployé dans Azure Stack fournit un moteur résilient et évolutif pour exécuter le conteneur de l’API Visage. |
| | Azure Cognitive Services [Conteneurs de l’API Visage](/azure/cognitive-services/face/face-how-to-install-containers)| Le fournisseur de ressources Azure Cognitive Services avec les conteneurs de l’API Visage offre une détection démographique, émotionnelle et unique des visiteurs sur le réseau privé de Contoso. |
| | Stockage Blob | Les images capturées à partir du kit de développement d’intelligence artificielle sont téléchargées sur le stockage Blob Azure Stack. |
| | Azure Functions | Une fonction Azure s'exécutant sur Azure Stack reçoit les données du stockage Blob et gère les interactions avec l'API Visage. Elle transmet des données anonymisées vers un hub d’événements situé dans Azure.<br><br>|
| Azure |  |  |
|  | [Azure Event Hubs](/azure/event-hubs/) | Azure Event Hubs fournit une plate-forme évolutive pour l'acquisition de données anonymisées qui s'intègre parfaitement à Azure Stream Analytics. |
|  | [Azure Stream Analytics](/azure/stream-analytics/) | Une tâche Azure Stream Analytics agrège les données anonymisées et les regroupe dans des fenêtres de 15 secondes pour les visualiser. |
|  | [Microsoft Power BI](https://powerbi.microsoft.com/) | Power BI fournit une interface de tableau de bord conviviale pour visualiser les résultats issus d’Azure Stream Analytics. |

## <a name="issues-and-considerations"></a>Problèmes et considérations

Prenez en compte les points suivants lorsque vous choisissez comment implémenter ce modèle :

### <a name="scalability"></a>Extensibilité 

Pour permettre à cette solution de s'adapter à plusieurs caméras et emplacements, vous devez vous assurer que tous les composants peuvent gérer cette surcharge. Vous devrez peut-être prendre des mesures comme :

- Augmenter le nombre d'unités de diffusion en continu Stream Analytics
- Étendre le déploiement de l'API Visage
- Augmenter le débit des hubs d'événements
- Dans les cas extrêmes, il peut être nécessaire de migrer des fonctions Azure vers une machine virtuelle.

### <a name="availability"></a>Disponibilité

Comme cette solution comporte plusieurs niveaux, il est important de réfléchir à la façon de faire face aux pannes de réseau ou d'électricité. Selon les besoins de l'entreprise, il peut être approprié de mettre en place un mécanisme de mise en cache des images localement, puis de les transférer vers Azure Stack lorsque la connectivité est rétablie. Si l'emplacement est suffisamment grand, le déploiement d'un appareil Data Box Edge avec le conteneur API Visage sur cet emplacement peut être préférable.

### <a name="manageability"></a>Facilité de gestion

Cette solution peut couvrir de nombreux appareils et emplacements, et donc devenir difficile à gérer. Les [services IoT d'Azure](/azure/iot-fundamentals/) peuvent être utilisés pour mettre automatiquement en ligne de nouveaux emplacements et appareils et les maintenir à jour. 

### <a name="security"></a>Sécurité

Cette solution capturant des images des clients, la sécurité revêt ici une importance critique. Assurez-vous que tous les comptes de stockage sont sécurisés à l'aide des stratégies d'accès appropriées et que les clés sont régulièrement modifiées. Veillez à ce que les comptes de stockage et Event Hubs disposent de stratégies de rétention conformes aux règlements sur la protection des informations personnelles définies par l'entreprise et le gouvernement. Veillez également à ce que les niveaux d'accès soient hiérarchisés afin que chaque personne ayant besoin d'accéder au système n'ait accès qu'aux données requises dans le cadre de son rôle.

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur les modèles connexes utilisés par cette solution, consultez le [modèle de données à plusieurs niveaux](azure-stack-solution-staged-data.md). 
- Pour en savoir plus sur l'utilisation de la vision personnalisée, voir le [Kit de développement d’intelligence artificielle personnalisé Vision AI](https://azure.github.io/Vision-AI-DevKit-Pages/). 
- Téléchargez et déployez un [exemple d’implémentation de la solution des pas](https://github.com/Azure-Samples/azure-intelligent-edge-patterns/tree/master/footfall-analysis). Vous apprenez à générer des insights à partir d’actions réelles, en utilisant Azure, Azure Stack et le kit de développement d’IA Custom Vision.   
