---
title: Rotation des secrets pour le fournisseur de ressources Event Hubs sur Azure Stack Hub
description: Découvrez comment effectuer la rotation des secrets pour le fournisseur de ressources Event Hubs sur Azure Stack Hub.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 10/15/2020
ms.reviewer: jfggdl
ms.lastreviewed: 10/15/2020
ms.openlocfilehash: fe238576814d1aae5d7469e9438f8921f912f0ea
ms.sourcegitcommit: f56a5b287c90b2081ae111385c8b7833931d4059
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2020
ms.locfileid: "97349464"
---
# <a name="how-to-rotate-secrets-for-event-hubs-on-azure-stack-hub"></a>Rotation des secrets pour Event Hubs sur Azure Stack Hub

Cet article explique comment effectuer une rotation des secrets utilisés par le fournisseur de ressources Event Hubs.

## <a name="overview-and-prerequisites"></a>Vue d’ensemble et conditions préalables

[!INCLUDE [prereqs](../includes/resource-provider-va-rotate-secrets-prereqs.md)]

## <a name="prepare-a-new-tls-certificate"></a>Préparer un nouveau certificat TLS

[!INCLUDE [prepare](../includes/resource-provider-va-rotate-secrets-prepare.md)]

## <a name="rotate-secrets"></a>Faire pivoter les clés secrètes

[!INCLUDE [rotate](../includes/resource-provider-va-rotate-secrets-rotate.md)]

## <a name="troubleshooting"></a>Résolution des problèmes

La rotation du secret doit s'effectuer correctement et sans erreur. Si vous rencontrez l'un des problèmes suivants sur le portail administrateur, [ouvrez une demande de support](azure-stack-manage-basics.md#where-to-get-support) pour obtenir de l'aide :

   - Problèmes d’authentification, comme les problèmes de connexion au fournisseur de ressources Event Hubs.
   - Impossible de mettre à niveau le fournisseur de ressources ou de modifier les paramètres de configuration.
   - Les métriques d'utilisation ne sont pas affichées.
   - Les factures ne sont pas générées.
   - Les sauvegardes n'ont pas lieu.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations sur la rotation des secrets dans l'infrastructure Azure Stack Hub, consultez [Effectuer la rotation des secrets dans Azure Stack Hub](azure-stack-rotate-secrets.md).