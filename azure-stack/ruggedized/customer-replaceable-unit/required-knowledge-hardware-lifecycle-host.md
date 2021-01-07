---
title: Connaissances requises pour utiliser l’hôte de cycle de vie du matériel
description: Apprenez-en davantage sur les connaissances requises pour utiliser l’hôte de cycle de vie du matériel
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: d6b13e26a1df94958f2b91d7a587e6d7f7a9f4bf
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97867585"
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

## <a name="dell-emc-poweredge-r640-installation-and-service-manual"></a>Dell EMC PowerEdge R640 - Manuel d’installation et de maintenance

Pour plus d’informations sur le remplacement physique du matériel approprié, consultez les procédures de remplacement de FRU PowerEdge R640 dans [Dell](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4)
[EMC PowerEdge R640 Manuel d’installation et de maintenance](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4).
Accédez à la section [Installation et retrait des composants du système](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/installing-and-removing-system-components?guid=guid-5a5943c4-fe26-4faa-a10c-2afa4c1993ff&lang=en-us).

## <a name="microsoft-azure-stack-hub-tactical-cloud-appliance-service-manual"></a>Manuel de service d’appliance cloud tactique Microsoft Azure Stack Hub

Le Manuel de service d’appliance cloud tactique Microsoft Azure Stack Hub contient des instructions pour le retrait de l’hôte de cycle de vie du matériel des pods tactiques Tracewell.

## <a name="dell-emc-poweredge-idrac"></a>iDRAC Dell EMC PowerEdge

Vous devez comprendre comment parcourir et utiliser l’interface web de l’iDRAC Dell EMC PowerEdge.

