---
title: Supprimer IoT Hub sur Azure Stack Hub
description: Instructions relatives à la désinstallation d’IoT Hub sur Azure Stack Hub
author: yiyiguo
ms.author: yiygu
ms.service: azure-stack
ms.topic: how-to
ms.date: 1/6/2020
ms.openlocfilehash: 4095336f6a18441512e8c1052051d6e1608d904a
ms.sourcegitcommit: 0e3296fb27b9dabbc2569bf85656c4c7b1d58ba9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050275"
---
# <a name="how-to-remove-iot-hub-on-azure-stack-hub"></a>Supprimer IoT Hub sur Azure Stack Hub

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

Cet article fournit des instructions relatives à la suppression du fournisseur de ressources IoT Hub sur Azure Stack Hub. Ce processus prend environ 37 minutes.

> [!WARNING]
> Une fois IoT Hub désinstallé, **_tous les appareils et données sont supprimés_**. Cette opération est **irréversible**.

## <a name="uninstalling-iot-hub"></a>Désinstaller IoT Hub

1) Accédez à **Gestion de la Place de marché**. IoT Hub est répertorié dans la liste et marqué comme installé. Cliquez sur **IoT Hub**.

    [![Liste des fournisseurs de ressources](../operator/media/iot-hub-rp-remove/uninstall1.png)](../operator/media/iot-hub-rp-remove/uninstall1.png#lightbox)

2) Cliquez sur **Désinstaller** sous **IoT Hub** , indiquez le nom du fournisseur de ressources **microsoft. iothub** , puis cliquez sur le bouton **Désinstaller** situé en dessous.

    [![Désinstaller IoT Hub et confirmer](../operator/media/iot-hub-rp-remove/uninstall2.png)](../operator/media/iot-hub-rp-remove/uninstall2.png#lightbox)

3) Attendez la fin de la désinstallation. Avant d’essayer de réinstaller IoT Hub, patientez au moins 10 minutes.

>[!IMPORTANT]
>Les dépendances (par exemple, Event Hub) ne seront **PAS** désinstallées. Si vous souhaitez désinstaller/supprimer des dépendances de la Place de marché, vous devez le faire séparément.

## <a name="next-steps"></a>Étapes suivantes

Pour une réinstallation, consultez [Installer le fournisseur de ressource sur une instance connectée d'Azure Stack](iot-hub-rp-install.md).
