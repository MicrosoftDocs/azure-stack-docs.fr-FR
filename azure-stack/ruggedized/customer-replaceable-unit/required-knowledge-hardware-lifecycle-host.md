---
title: Connaissances requises pour utiliser l’hôte de cycle de vie du matériel
description: Apprenez-en davantage sur les connaissances requises pour utiliser l’hôte de cycle de vie du matériel
author: PatAltimore
ms.topic: how-to
ms.date: 02/05/2021
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 3d3e650e9b6b1b6c37e2f265aa5c049246600c2f
ms.sourcegitcommit: 5ea0e915f24c8bcddbcaf8268e3c963aa8877c9d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100487780"
---
# <a name="required-knowledge-for-working-with-the-hardware-lifecycle-host"></a>Connaissances requises pour utiliser l’hôte de cycle de vie du matériel

Pour terminer les procédures FRU, vous devez connaître et avoir accès aux concepts et guides suivants.

## <a name="hardware-lifecycle-host"></a>Hardware Lifecycle Host (HLH)

L’hôte de cycle de vie du matériel (HLH) est un serveur d’administration physique situé en haut du rack Azure Stack Hub. Pour accéder à l’hôte, vous pouvez vous y connecter à l’aide de l’une des trois méthodes suivantes :

* Direct (panier d’incident)
* iDRAC (port de service)
* iDRAC (accès IP)

Si vous vous trouvez à l’intérieur du centre de données, vous pouvez vous connecter directement au HLH à l’aide des ports VGA et USB, par exemple en vous connectant à un panier d’incident.

Si vous vous trouvez à l’intérieur du centre de données, connectez votre ordinateur portable au port de service iDRAC 9 à l’aide d’un câble micro USB. Pour plus d’informations, consultez Accès à l’interface iDRAC par le biais d’une connexion USB directe.

Collaborez avec le client pour vous connecter au HLH à partir de son réseau de gestion et de sa station de travail de gestion à l’adresse IP de l’iDRAC.

> [!NOTE]
> Seuls les réseaux qui ont été précédemment ajoutés aux listes ACL du commutateur peuvent se connecter directement à l’iDRAC du HLH.

## <a name="credentials"></a>Informations d’identification

Collaborez avec le client pour obtenir les informations d’identification pour :

* HLH
* administrator
* Compte iDRAC (facultatif)

Un compte Windows avec des droits d’administrateur complets.

Si vous ne vous connectez pas directement au serveur à l’aide d’un panier d’incident, vous aurez besoin des informations d’identification du compte iDRAC pour accéder au KVM virtuel.


