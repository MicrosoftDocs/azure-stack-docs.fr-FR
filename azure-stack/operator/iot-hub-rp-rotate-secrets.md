---
title: Rotation des secrets pour le fournisseur de ressources IoT Hub sur Azure Stack Hub
description: Apprenez à effectuer une rotation des secrets pour le fournisseur de ressources IoT Hub sur Azure Stack Hub
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/15/2020
ms.reviewer: bryanla
ms.lastreviewed: 11/15/2020
ms.openlocfilehash: d693b73794b71ec32816f2e79370e2f836b00e7b
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95517551"
---
# <a name="how-to-rotate-secrets-for-iot-hub-on-azure-stack-hub"></a>Rotation des secrets pour IoT Hub sur Azure Stack Hub

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

Cet article explique comment effectuer une rotation des secrets utilisés par le fournisseur de ressources IoT Hub.

## <a name="overview-and-prerequisites"></a>Vue d’ensemble et conditions préalables

[!INCLUDE [prereqs](../includes/resource-provider-va-rotate-secrets-prereqs.md)]

## <a name="prepare-a-new-tls-certificate"></a>Préparer un nouveau certificat TLS

[!INCLUDE [prepare](../includes/resource-provider-va-rotate-secrets-prepare.md)]

## <a name="rotate-secrets"></a>Faire pivoter les clés secrètes

[!INCLUDE [rotate](../includes/resource-provider-va-rotate-secrets-rotate.md)]

## <a name="troubleshooting"></a>Résolution des problèmes

La rotation du secret doit s'effectuer correctement et sans erreur. Si vous rencontrez l'un des problèmes suivants sur le portail administrateur, [ouvrez une demande de support](azure-stack-manage-basics.md#where-to-get-support) pour obtenir de l'aide :

   - Problèmes d'authentification, comme les problèmes de connexion au fournisseur de ressources IoT Hub.
   - Impossible de mettre à niveau le fournisseur de ressources ou de modifier les paramètres de configuration.
   - Les métriques d'utilisation ne sont pas affichées.
   - Les factures ne sont pas générées.
   - Les sauvegardes n'ont pas lieu.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations sur la rotation des secrets dans l'infrastructure Azure Stack Hub, consultez [Effectuer la rotation des secrets dans Azure Stack Hub](azure-stack-rotate-secrets.md).