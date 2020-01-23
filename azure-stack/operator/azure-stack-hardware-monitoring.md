---
title: Superviser l’intégrité matérielle d’Azure Stack Hub | Microsoft Docs
description: Découvrez comment superviser l’intégrité des composants matériels d’Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: sethm
ms.reviewer: thoroet
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: 6db83102d9252a2027dfacd0054fddba38175ec9
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75818195"
---
# <a name="monitor-azure-stack-hub-hardware-components"></a>Superviser les composants matériels d’Azure Stack Hub

Le système de contrôle d’intégrité et de supervision d’Azure Stack Hub supervise déjà l’état du sous-système de stockage et déclenche des alertes en fonction des besoins. Avec la version 1910 d’Azure Stack Hub, le système de contrôle d’intégrité et de supervision peut désormais déclencher des alertes pour les composants matériels suivants :

- Ventilateurs système
- Température système
- Alimentation
- UC
- Mémoire
- Lecteurs de démarrage

> [!NOTE]
> Avant d’activer cette fonctionnalité, vous devez vérifier auprès de votre partenaire Matériel qu’il est prêt. Votre partenaire Matériel fournira également les étapes détaillées permettant d’activer cette fonctionnalité dans le contrôleur BMC.

## <a name="snmp-listener-scenario"></a>Scénario d’écouteur SNMP

Un écouteur SNMP v3 s’exécute sur les trois instances ERCS, sur le port TCP 162. Le contrôleur BMC (Baseboard Management Controller) doit être configuré pour envoyer des interruptions SNMP à l’écouteur Azure Stack Hub. Vous pouvez obtenir les trois adresses IP PEP à partir du portail d’administration en ouvrant la vue des propriétés de région.

L’envoi d’interruptions à l’écouteur requiert une authentification et doit utiliser les mêmes informations d’identification que l’accès au contrôleur BMC de base lui-même.

Lors de la réception d’une interruption SNMP sur l’une des trois instances ERCS, sur le port TCP 162, l’OID est mis en correspondance en interne et une alerte est déclenchée. Le système de contrôle d’intégrité et de surveillance Azure Stack Hub accepte uniquement les OID définis par le partenaire Matériel. Si un OID est inconnu pour Azure Stack Hub, il ne sera pas associé à une alerte.

Une fois qu’un composant défectueux est remplacé, un événement est envoyé du contrôleur BMC vers l’écouteur SNMP pour indiquer la modification d’état, et l’alerte se ferme automatiquement dans Azure Stack Hub.

> [!NOTE]
> Les alertes existantes ne se ferment pas automatiquement lorsque la totalité du nœud ou de la carte mère est remplacée. Il en va de même lorsque le contrôleur BMC perd sa configuration ; par exemple, suite à une réinitialisation aux paramètres d’usine.

## <a name="next-steps"></a>Étapes suivantes

[Intégration du pare-feu](azure-stack-firewall.md)
