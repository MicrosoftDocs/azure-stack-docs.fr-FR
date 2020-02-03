---
title: Modèle pour l’implémentation d’un solution de relais hybride utilisant Azure et Azure Stack Hub.
description: Découvrez comment utiliser les services Azure et Azure Stack Hub pour vous connecter à des ressources ou des appareils de périphérie protégés par des pare-feu.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 7315013253c04ebbb231a0915dba610e459848e7
ms.sourcegitcommit: 959513ec9cbf9d41e757d6ab706939415bd10c38
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2020
ms.locfileid: "76890066"
---
# <a name="hybrid-relay-pattern"></a>Modèle Relais hybride

Découvrez comment vous connecter à des ressources ou des appareils à la périphérie, protégés par des pare-feu à l’aide de relais Azure Service Bus.

## <a name="context-and-problem"></a>Contexte et problème

Les appareils à la périphérie sont souvent derrière un pare-feu d’entreprise ou un périphérique NAT. Bien qu’ils soient sécurisés, ils peuvent ne pas être en mesure de communiquer avec le cloud public ou les appareils de périphérie d’autres réseaux d’entreprise. Il peut être nécessaire d’exposer certains ports et fonctionnalités aux utilisateurs dans le cloud public de manière sécurisée. 

## <a name="solution"></a>Solution

Le modèle de relais hybride utilise des relais Azure Service Bus pour établir un tunnel WebSocket, entre deux points de terminaison qui ne peuvent pas communiquer directement. Les appareils qui ne sont pas locaux, mais qui doivent se connecter à un point de terminaison local, se connectent à un point de terminaison dans le cloud public. Ce point de terminaison redirige le trafic, sur les itinéraires prédéfinis sur un canal sécurisé. Un point de terminaison à l’intérieur de l’environnement local reçoit le trafic et l’achemine vers la destination correcte. 

![Architecture de la solution de relais hybride](media/pattern-hybrid-relay/solution-architecture.png)

Le fonctionnement de la solution est le suivant : 

1. Un appareil se connecte à la machine virtuelle dans Azure, sur un port prédéfini.
2. Le trafic est transféré vers le relais Service Bus dans Azure.
3. La machine virtuelle sur Azure Stack Hub, qui a déjà établi une connexion de longue durée au relais Service Bus, reçoit le trafic et le transfère vers la destination.
4. Le point de terminaison ou le service local traite la demande. 

## <a name="components"></a>Components

Cette solution utilise les composants suivants :

| Couche | Composant | Description |
|----------|-----------|-------------|
| Azure | Azure VM | Une machine virtuelle Azure fournit un point de terminaison accessible publiquement pour la ressource locale. |
| | Azure Service Bus Relay | Un [relais Azure Service Bus](/azure/service-bus-relay/) fournit l’infrastructure pour maintenir le tunnel et la connexion entre la machine virtuelle Azure et la machine virtuelle Azure Stack Hub.|
| Azure Stack Hub | Calcul | Une machine virtuelle Azure Stack Hub fournit le côté serveur du tunnel de relais hybride. |
| | Stockage | Le cluster du moteur AKS déployé dans Azure Stack Hub fournit un moteur résilient et scalable pour exécuter le conteneur de l’API Visage.|

## <a name="issues-and-considerations"></a>Problèmes et considérations

Prenez en compte des points suivants lors du choix de l'implémentation de cette solution :

### <a name="scalability"></a>Extensibilité 

Ce modèle autorise uniquement les mappages de port 1:1 sur le client et le serveur. Par exemple, si le port 80 est tunnelé pour un service sur le point de terminaison Azure, il ne peut pas être utilisé pour un autre service. Les mappages de ports doivent être planifiés en conséquence. Le relais Service Bus et les machines virtuelles doivent être mis à l’échelle de manière appropriée pour gérer le trafic.

### <a name="availability"></a>Disponibilité

Ces tunnels et connexions ne sont pas redondants. Pour garantir une haute disponibilité, vous pouvez implémenter un code de vérification des erreurs. Une autre option consiste à avoir un pool de machines virtuelles connectées au relais Service Bus derrière un équilibreur de charge.

### <a name="manageability"></a>Simplicité de gestion

Cette solution peut couvrir de nombreux appareils et emplacements, et donc devenir difficile à gérer. Les services IoT d'Azure peuvent être utilisés pour mettre automatiquement en ligne de nouveaux emplacements et appareils et les maintenir à jour.

### <a name="security"></a>Sécurité

Ce modèle, tel que présenté, permet un accès non autorisé à un port sur un appareil interne à partir de la périphérie. Envisagez d’ajouter un mécanisme d’authentification au service sur l’appareil interne, ou devant le point de terminaison de relais hybride. 

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les sujets abordés dans cet article :
- Ce modèle utilise des relais Azure Service Bus. Pour plus d’informations, consultez la [documentation relative aux relais Azure Service Bus](/azure/service-bus-relay/).
- Consultez [Considérations relatives à la conception des applications hybrides](overview-app-design-considerations.md) pour en savoir plus sur les meilleures pratiques et obtenir des réponses à d'autres questions.
- Consultez [Famille de produits et de solutions Azure Stack](/azure-stack) pour en savoir plus sur l'ensemble du portefeuille de produits et de solutions.

Lorsque vous êtes prêt à tester l’exemple de solution, poursuivez avec le [Guide de déploiement de la solution de relais hybride](https://aka.ms/hybridrelaydeployment). Ce guide de déploiement fournit des instructions pas à pas sur le déploiement et sur le test de ses composants.