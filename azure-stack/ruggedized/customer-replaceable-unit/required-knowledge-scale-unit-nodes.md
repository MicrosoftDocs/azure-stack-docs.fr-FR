---
title: Connaissances requises dans l’utilisation des nœuds d’unité d’échelle dans une appliance cloud tactique
description: Apprenez-en davantage sur les connaissances requises pour l’utilisation des nœuds d’unité d’échelle dans une appliance cloud tactique.
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: f108559f191abd7a4d936f9f187384d150df8d62
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97392659"
---
# <a name="required-knowledge-for-working-with-scale-unit-nodes-in-a-tactical-cloud-appliance"></a>Connaissances requises dans l’utilisation des nœuds d’unité d’échelle dans une appliance cloud tactique

Pour terminer les procédures FRU, vous devez connaître et avoir accès aux concepts, guides et sites web suivants.

## <a name="privileged-access-workstation-and-the-privileged-endpoint"></a>Station de travail à accès privilégié et point de terminaison privilégié

La station de travail à accès privilégié est une station de travail dédiée, protégée d’Internet et des vecteurs de menaces externes. Elle peut résider sur l’hôte de cycle de vie du matériel (HLH) ou en externe sur le réseau du client avec un accès routable aux nœuds d’unité d’échelle Azure Stack Hub.

Pour accéder à la station de travail à accès privilégié, vous devez vous connecter à l’aide de Bureau à distance. Demandez au client de vous fournir les informations d’identification et l’adresse IP.

À partir de cet ordinateur, vous pouvez également accéder au point de terminaison privilégié.
Pour plus d’informations sur la station de travail à accès privilégié et le point de terminaison privilégié, consultez Accès à la station de travail à accès privilégié et aux points de terminaison privilégiés.

## <a name="azure-stack-hub-administrator-portal"></a>Portail administrateur Azure Stack Hub

Demandez au client de vous fournir les informations d’identification et l’URL du portail administrateur.
Pour plus d’informations, consultez [Utiliser le portail administrateur](https://docs.microsoft.com/azure-stack/operator/azure-stack-manage-portals)
[dans Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-manage-portals).

## <a name="dell-emc-poweredge-r640-installation-and-service-manual"></a>Dell EMC PowerEdge R640 - Manuel d’installation et de maintenance

Pour plus d’informations sur le remplacement physique du matériel approprié, consultez les procédures d’installation et de remplacement de composants système PowerEdge R640 dans [Dell EMC PowerEdge R640 Manuel d’installation et de maintenance](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4).
Accédez à la section [Installation et retrait](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/installing-and-removing-system-components?guid=guid-5a5943c4-fe26-4faa-a10c-2afa4c1993ff&lang=en-us)
[des composants du système](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/installing-and-removing-system-components?guid=guid-5a5943c4-fe26-4faa-a10c-2afa4c1993ff&lang=en-us).

## <a name="microsoft-azure-stack-hub-tactical-cloud-appliance-service-manual"></a>Manuel de service d’appliance cloud tactique Microsoft Azure Stack Hub

Le Manuel de service d’appliance cloud tactique Microsoft Azure Stack Hub contient des instructions pour le retrait des serveurs de nœud d’unité d’échelle des pods tactiques Tracewell.

## <a name="dell-emc-poweredge-idrac"></a>iDRAC Dell EMC PowerEdge

Vous devez comprendre comment parcourir et utiliser l’interface web de l’iDRAC Dell EMC PowerEdge. Pour plus d’informations sur l’utilisation de l’iDRAC, consultez le [Guide de l’utilisateur Integrated Dell Remote Access Controller 9](https://www.dell.com/support/manuals/us/en/04/poweredge-r840/idrac9_4.00.00.00_ug_new/overview-of-idrac?guid=guid-a03c2558-4f39-40c8-88b8-38835d0e9003).

