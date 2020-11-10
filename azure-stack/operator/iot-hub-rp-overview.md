---
title: Présentation d'IoT Hub sur Azure Stack Hub
description: Familiarisez-vous avec le fournisseur de ressources IoT Hub sur Azure Stack Hub et découvrez les différences avec la version hébergée Azure d'IoT Hub.
author: yiyiguo
ms.author: yiygu
ms.service: azure-stack
ms.topic: how-to
ms.date: 1/6/2020
ms.openlocfilehash: adf6635fbdcf62398218a8e6ad0ce061900e0059
ms.sourcegitcommit: 0e3296fb27b9dabbc2569bf85656c4c7b1d58ba9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050274"
---
# <a name="iot-hub-on-azure-stack-hub-overview"></a>Présentation d'IoT Hub sur Azure Stack Hub

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

IoT Hub sur Azure Stack Hub vous permet de créer des solutions IoT hybrides. IoT Hub est un service géré qui fait office de hub de messages centralisé pour la communication bidirectionnelle entre votre application IoT et les appareils qu'il gère. Vous pouvez utiliser IoT Hub sur Azure Stack Hub pour générer des solutions IoT avec des communications fiables et sécurisées entre des appareils IoT et une solution back-end locale. 

## <a name="differences-between-azure-iot-hub-and-iot-hub-on-azure-stack-hub"></a>Différences entre Azure IoT Hub et IoT Hub sur Azure Stack Hub

| Fonctionnalité | Azure IoT Hub | IoT Hub sur Azure Stack Hub |
|-|-|-|
| Scale | https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling | Les références SKU S2 et S3 d'IoT Hub sont prises en charge|
| Mise à jour du service | Automatique | Manuel |

## <a name="iot-hub-throttling"></a>Limitations d'IoT Hub

Les limitations et quotas relatifs aux références SKU S2 et S3 d'IoT Hub sur Azure Stack Hub sont les mêmes que pour IoT Hub sur Azure. Pour plus d'informations, reportez-vous à [Quotas et limitations d'Azure IoT Hub](/azure/iot-hub/iot-hub-devguide-quotas-throttling#quotas-and-throttling).

## <a name="next-steps"></a>Étapes suivantes

Pour préparer l'installation d'IoT Hub sur Azure Stack Hub, consultez le document [Prérequis](iot-hub-rp-prerequisites.md).

Pour en savoir plus sur la différence entre une instance connectée et une instance déconnectée d'Azure Stack Hub, consultez [Modèle de connexion Azure Stack](azure-stack-connection-models.md).
