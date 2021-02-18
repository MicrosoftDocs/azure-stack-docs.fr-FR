---
title: Superviser l’intégrité matérielle d’Azure Stack Hub
description: Découvrez comment superviser l’intégrité des composants matériels d’Azure Stack Hub.
author: sethmanheim
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: sethm
ms.reviewer: thoroet
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: 0b1d107d33cb19223018f6d6ac26f11f65d6cc15
ms.sourcegitcommit: d542b68b299b73e045f30916afb6018e365e9db6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975909"
---
# <a name="monitor-azure-stack-hub-hardware-components"></a>Superviser les composants matériels d’Azure Stack Hub

Le système de contrôle d’intégrité et de supervision d’Azure Stack Hub supervise déjà l’état du sous-système de stockage et déclenche des alertes en fonction des besoins. Le système de contrôle d’intégrité et de supervision peut désormais déclencher des alertes pour les composants matériels suivants :

- Ventilateurs système
- Température système
- Alimentation
- UC
- Mémoire
- Lecteurs de démarrage

> [!NOTE]
> Avant d’activer cette fonctionnalité, vous devez vérifier auprès de votre partenaire fournisseur de matériel qu’ils sont prêts. Votre partenaire matériel fournira également les étapes détaillées permettant d’activer cette fonctionnalité dans le contrôleur de gestion de la carte de base (BMC). Le chiffrement de l’utilisateur dans le contrôleur de gestion de la carte de base doit être défini sur AES pour la build 2005 et les builds ultérieures.

## <a name="snmp-listener-scenario"></a>Scénario d’écouteur SNMP

Un écouteur SNMP v3 s’exécute sur les trois instances ERCS, sur le port TCP 162. Le contrôleur BMC doit être configuré pour envoyer des interruptions SNMP à l’écouteur Azure Stack Hub. Vous pouvez obtenir les trois adresses IP PEP à partir du portail d’administration en ouvrant l’affichage des propriétés de région.

L’envoi d’interruptions à l’écouteur requiert une authentification et doit utiliser les mêmes informations d’identification que l’accès au contrôleur BMC de base lui-même.

Lors de la réception d’une interruption SNMP sur l’une des trois instances ERCS, sur le port TCP 162, l’OID est mis en correspondance en interne et une alerte est déclenchée. Le système de contrôle d’intégrité et de surveillance Azure Stack Hub accepte uniquement les OID définis par le partenaire Matériel. Si un OID est inconnu pour Azure Stack Hub, il ne sera pas associé à une alerte.

Une fois le composant défectueux remplacé, un événement est envoyé du contrôleur BMC à l’écouteur SNMP pour indiquer la modification d’état. L’alerte se ferme ensuite automatiquement dans Azure Stack Hub.

> [!NOTE]
> Les alertes existantes ne se ferment pas automatiquement lorsque la totalité du nœud ou de la carte mère est remplacée. Il en va de même lorsque le contrôleur BMC perd sa configuration ; par exemple, suite à une réinitialisation aux paramètres d’usine.

## <a name="next-steps"></a>Étapes suivantes

[Intégration du pare-feu](azure-stack-firewall.md)
