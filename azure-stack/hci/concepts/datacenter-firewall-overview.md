---
title: Vue d’ensemble du Pare-feu Datacenter dans Azure Stack HCI
description: Utilisez cette rubrique pour découvrir le Pare-feu Datacenter dans Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 11/17/2020
ms.openlocfilehash: 34efe18aee4a62481d81a5e9a810a0a71d97b063
ms.sourcegitcommit: 40d3f3f0ac088d1590d1fb64ca05ac1dabf4e00c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874955"
---
# <a name="what-is-datacenter-firewall"></a>Qu’est-ce que le Pare-feu Datacenter ?

> S’applique à : Azure Stack HCI, version 20H2 ; Windows Server 2019

Le Pare-feu Datacenter est un pare-feu de couche réseau à 5 tuples (protocole, numéro de port source et de destination, adresses IP source et de destination), avec état, multilocataire et SDN (Software Defined Networking). Le Pare-feu Datacenter protège les flux de trafic Est-Ouest et Nord-Sud sur la couche réseau des réseaux virtuels et des réseaux VLAN traditionnels.

## <a name="how-datacenter-firewall-works"></a>Comment fonctionne le Pare-feu Datacenter

Vous activez et configurez le Pare-feu Datacenter en créant des listes de contrôle d’accès (ACL) qui sont appliquées à un sous-réseau ou une interface réseau. Les stratégies du Pare-feu sont appliquées au port vSwitch de chaque machine virtuelle locataire. Les stratégies sont envoyées via le portail des locataires, tandis que le [contrôleur de réseau](network-controller-overview.md) les distribue à tous les hôtes applicables.

Les administrateurs de locataires peuvent installer et configurer des stratégies de pare-feu pour protéger leurs réseaux contre le trafic indésirable provenant des réseaux Internet et intranet.

:::image type="content" source="media/datacenter-firewall/multitenant-firewall-overview.png" alt-text="Pare-feu Datacenter dans la pile réseau" border="false":::

L’administrateur de fournisseur de services ou l’administrateur de locataire peut gérer les stratégies du Pare-feu Datacenter via le contrôleur de réseau et les API orientées Nord. Vous pouvez également configurer et gérer les stratégies du Pare-feu Datacenter avec Windows Admin Center.

## <a name="advantages-for-cloud-service-providers"></a>Avantages des fournisseurs de services cloud (CSP)

Le Pare-feu Datacenter offre les avantages suivants pour les CSP :

- Une solution de pare-feu logicielle hautement scalable, gérable et diagnosticable qui peut être offerte aux locataires

- La liberté de déplacer les machines virtuelles locataires vers différents hôtes de calcul sans rompre les stratégies de pare-feu de locataire

    - Déployé en tant que pare-feu de l’agent hôte du port vSwitch

    - Les machines virtuelles locataires obtiennent les stratégies affectées à leur pare-feu de l’agent hôte vSwitch

    - Les règles de pare-feu sont configurées dans chaque port vSwitch, indépendamment de l’hôte exécutant la machine virtuelle

- Offre une protection aux machines virtuelles locataires, indépendamment du système d’exploitation invité du locataire

## <a name="advantages-for-tenants"></a>Avantages pour les locataires

Le Pare-feu Datacenter offre les avantages suivants pour les locataires :

- Possibilité de définir des règles de pare-feu pour protéger les charges de travail orientées Internet et les charges de travail internes sur les réseaux

- Possibilité de définir des règles de pare-feu pour protéger le trafic entre les machines virtuelles sur le même sous-réseau de couche 2 (C2) et entre les machines virtuelles sur différents sous-réseaux C2

- Possibilité de définir des règles de pare-feu pour protéger et isoler le trafic réseau entre les réseaux locaux de locataire et leurs réseaux virtuels au niveau du fournisseur de services

- Possibilité d’appliquer des stratégies de pare-feu à des réseaux VLAN traditionnels et à des réseaux virtuels basés sur des superpositions

## <a name="next-steps"></a>Étapes suivantes

Pour consulter des informations connexes, reportez-vous également à :

- [Utiliser le Pare-feu Datacenter pour les réseaux à définition logicielle (SDN) dans Azure Stack HCI](../manage/use-datacenter-firewall.md)
- [SDN dans Azure Stack HCI](software-defined-networking.md)