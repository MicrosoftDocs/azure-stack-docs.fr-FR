---
title: Présentation du modèle de facturation client dans Azure Stack - MDC | Microsoft Docs
description: Découvrez comment l’utilisation des ressources est facturée aux utilisateurs d’Azure Stack dans un Modular Data Center (MDC).
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 12/23/2019
ms.openlocfilehash: 15f894a668374be5380f322d368b76d88bb93cba
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910752"
---
# <a name="billing-model-overview---modular-data-center-mdc"></a>Présentation du modèle de facturation - Modular Data Center (MDC)

En tant qu'utilisateur de MDC ou d'Azure Stack Hub renforcé, vous êtes facturé par Microsoft en fonction de la durée d'utilisation de chaque appliance. Les frais sont calculés par période et comprennent le droit d’utiliser les services de base liés au calcul, au stockage et à la gestion de réseau. Vous êtes facturé en fonction de l’utilisation d’App Service, d’Event Hubs et des autres services PaaS ainsi que des machines virtuelles PAYG Windows Server qui s’exécutent sur Azure Stack Hub renforcé et MDC. Si vous êtes complètement déconnecté et si vous ne pouvez pas rapporter les données d’utilisation, vous devez obtenir des licences de capacité pour les services PaaS et apporter vos propres licences pour les machines virtuelles Windows.

La facturation débute 14 jours après l'expédition d'une commande d'appliance. Pour Azure Stack Hub en conteneur, la période de facturation initiale est d'un an. Au bout d'un an, la commande peut être prolongée d'une année supplémentaire ; une autre année d'utilisation est alors facturée. Si la commande n'est pas prolongée et que l'appareil n'est pas retourné, la facturation se poursuit sur une base mensuelle. Pour Azure Stack Hub renforcé, la facturation est toujours mensuelle.

Lorsque l'appliance est retournée à Microsoft, la facturation s'arrête dès réception de celle-ci par l'entrepôt Microsoft.

## <a name="next-steps"></a>Étapes suivantes

[Informations de référence sur l'API d'utilisation](analyze-usage-tzl.md)
