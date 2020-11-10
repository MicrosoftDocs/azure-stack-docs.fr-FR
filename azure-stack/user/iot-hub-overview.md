---
title: Présentation d'IoT Hub sur Azure Stack Hub
description: Familiarisez-vous avec le fournisseur de ressources IoT Hub sur Azure Stack Hub.
author: yiyiguo
ms.author: yiygu
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/12/2019
ms.openlocfilehash: 098168947a28214a3955a1961c80a2c06825126b
ms.sourcegitcommit: 296c95cad20ed62bdad0d27f1f5246bfc1c81d5e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93064716"
---
# <a name="overview-of-iot-hub-on-azure-stack-hub"></a>Présentation d'IoT Hub sur Azure Stack Hub

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

IoT Hub sur Azure Stack Hub vous permet de créer des solutions IoT hybrides. IoT Hub est un service géré qui fait office de hub de messages centralisé pour la communication bidirectionnelle entre votre application IoT et les appareils qu'il gère. Vous pouvez utiliser IoT Hub sur Azure Stack Hub pour générer des solutions IoT avec des communications fiables et sécurisées entre des appareils IoT et une solution back-end locale.

## <a name="features"></a>Fonctionnalités

| Fonctionnalité | IoT Hub sur Azure | Préversion d'IoT Hub on Azure Stack Hub (S2/S3) |
|-|-|-|
|Télémétrie appareil-à-cloud| ✔ | ✔ |
|Messages de cloud-à-appareil| ✔ | ✔ |
|Identité par appareil| ✔ | ✔ |
|Routage des messages <sub>1</sub><sub>, 4</sub>| ✔ | ✔ |
|Protocoles HTTP, AMQP, MQTT| ✔ | ✔ |
|Multilocation| ✔ | ✔ |
|Surveillance et diagnostics| ✔ | ✔ |
|Messages de cloud-à-appareil| ✔ | ✔ |
|Gestion des appareils, jumeau d'appareil, jumeau de module| ✔ | ✔ |
|Notifications des jumeaux, événements liés au cycle de vie des appareils| ✔ | ✔ |
|Déploiement en couches de périphérie| ✔ | à venir |
|Portail administrateur <sub>2</sub>| ✘ | ✔ |
|Rotation des secrets <sub>2</sub>| ✘ | ✔ |
|Gestion de la capacité <sub>2</sub>| ✘ | ✔ |
|Sauvegarde et restauration <sub>3</sub>| ✘ | ✘ |
|DeviceConnected, DeviceDisconnected, ASC <sub>4</sub>| ✔ | ✘ |
|Configuration de module d'appareil| ✔ | à venir |
|Flux d'appareils, IoT Plug-and-Play, travaux, chargement de fichiers <sub>5</sub>| ✔ | ✘ |
|Surveiller l'état de connexion des appareils à l'aide d'Event Grid <sub>4</sub>| ✔ | ✘ |
|Basculement <sub>6</sub>| ✔ | ✘ |

<sub>1</sub> Limité aux points de terminaison intégrés, à Event Hubs et au Stockage. Service Bus n'est pas disponible sur Azure Stack Hub.  
<sub>2</sub> Pour permettre aux opérateurs de gérer IoT Hub sur ASH.  
<sub>3</sub> La sauvegarde est disponible dans la préversion. La restauration sera prise en charge dans la disponibilité générale.  
<sub>4</sub> Dépend d'autres services qui ne sont pas disponibles sur Azure Stack Hub.  
<sub>5</sub> Dans la feuille de route à apporter à Azure Stack Hub.  
<sub>6</sub> Non applicable sur Azure Stack Hub.  

## <a name="api-available-for-iot-hub-on-azure-stack-hub"></a>API disponible pour IoT Hub sur Azure Stack Hub

|API|IoT Hub sur Azure Stack Hub|
|-|-|
|Appliquer la configuration sur l'appareil| ✔ |
| Configuration - Créer | ✔ |
| Configuration - Supprimer | ✔ |
| Configuration - Lire | ✔ |
|Configuration - Lire - Plusieurs| ✔ |
|Configuration - Service - Appliquer|  ✔ |
|Configuration - Mettre à jour|  ✔ |
|Méthode d'appel direct d'un appareil|  ✔ |
|GetDeviceAndModuleInScope|  ✔ |
|GetDevicesAndModulesInScope| ✔ |
|Désinscrire un appareil| ✔ |
|Obtenir des appareils| ✔ |
|Mettre à jour un jumeau de module| ✔ |
|D2C - Obtenir un jumeau| ✔ |
|Importer des appareils| ✔ |
|Obtenir un jumeau| ✔ |
|Désinscrire un module| ✔ |
|Mise à jour d’un appareil| ✔ |
|Mettre à jour un module| ✔ |
|Interroger des appareils| ✔ |
|Exporter des appareils| ✔ |
|Sauvegarder et restaurer - ADM| ✔ |
|Remplacer un jumeau| ✔ |
|Sauvegarder et restaurer - DSS| ✔ |
|D2C - Notifier les propriétés souhaitées| ✔ |
|D2C - Corriger les propriétés signalées| ✔ |
|Obtenir un jumeau de module| ✔ |
|Module - D2C - Obtenir un jumeau| ✔ |
|Obtenir un module| ✔ |
|Module - D2C - Notifier les propriétés souhaitées| ✔ |
|Module - D2C - Corriger les propriétés signalées| ✔ |
|Méthode d'appel direct d'un module| ✔ |
|Mettre à jour un jumeau| ✔ |
|Opérations sur des appareils en bloc| ✔ |
|Télémétrie appareil-à-cloud| ✔ |
|Inscrire un appareil| ✔ |
|Inscrire un module| ✔ |
|Remplacer un jumeau de module| ✔ |
|Authentification générique| ✔ |
|Obtenir un appareil| ✔ |
|Déplacement de partition/Changement de rôle| ✔ |

## <a name="differences-between-iot-hub-on-azure-cloud-and-iot-hub-on-azure-stack"></a>Différences entre IoT Hub sur Azure Cloud et IoT Hub sur Azure Stack

| Aspect | IoT Hub sur Azure Cloud | IoT Hub sur Azure Stack |
|-|-|-|
| Consommation de messages | https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-read-builtin |Par défaut, les messages sont acheminés vers le point de terminaison côté service intégré (messages/events) compatible avec Event Hubs. Sur Azure Cloud, vous pouvez accéder aux messages à partir du point de terminaison en fournissant la chaîne de connexion IoT Hub ou la chaîne de connexion Event Hub. En revanche, sur Azure Stack Hub, seule la chaîne de connexion Event Hub est prise en charge. |

## <a name="next-steps"></a>Étapes suivantes

Si IoT Hub n'est pas disponible dans le cadre de votre abonnement, demandez à votre administrateur d'[installer le fournisseur de ressources IoT Hub sur Azure Stack Hub](../operator/iot-hub-rp-overview.md).

Pour en savoir plus sur l'utilisation d'IoT Hub, consultez la [documentation d'Azure IoT Hub](/azure/iot-hub/).

