---
title: Architecture du Kit de développement Azure Stack | Microsoft Docs
description: Décrit l’architecture du Kit de développement Azure Stack (ASDK).
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 06/28/2019
ms.openlocfilehash: 4cd20ed4c76b062ea2fcfdf3d314ef2bcdecaff0
ms.sourcegitcommit: eccbd0098ef652919f357ef6dba62b68abde1090
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67492346"
---
# <a name="microsoft-azure-stack-development-kit-architecture"></a>Architecture du Kit de développement Microsoft Azure Stack
Le Kit de développement Azure Stack (ASDK) est un déploiement à nœud unique d’Azure Stack qui s'exécute sur un seul ordinateur hôte. Des composants de routage Edge sont installés sur l’ordinateur hôte afin de fournir des fonctionnalités NAT et VPN pour Azure Stack. Les rôles d’infrastructure Azure Stack s’exécutent dans la couche Hyper-V de l’ordinateur hôte physique.


## <a name="virtual-machine-roles"></a>Rôles de machine virtuelle
L’ASDK propose des services à l’aide des machines virtuelles suivantes qui sont hébergées sur l’ordinateur hôte du Kit de développement :

| Nom | Description |
| ----- | ----- |
| **AzS-ACS01** | Services de stockage Azure Stack.|
| **AzS-ADFS01** | Services de fédération Active Directory (ADFS).  |
| **AzS-CA01** | Services d’autorité de certification pour les services de rôle Azure Stack.|
| **AzS-DC01** | Services Active Directory, DNS et DHCP pour Microsoft Azure Stack.|
| **AzS-ERCS01** | Machine virtuelle la console de récupération d’urgence. |
| **AzS-GWY01** | Services de passerelle Edge, comme les connexions de site à site VPN pour les réseaux locataires.|
| **AzS-NC01** | Contrôleur réseau qui gère les services réseau Azure Stack.  |
| **AzS-SLB01** | Services multiplexeur d’équilibrage de charge dans Azure Stack pour les clients et les services d’infrastructure Azure Stack.  |
| **AzS-SQL01** | Magasin de données interne pour les rôles d’infrastructure Azure Stack.  |
| **AzS-WAS01** | Portail d’administration Azure Stack et services Azure Resource Manager.|
| **AzS-WASP01**| Portail utilisateur (locataire) Azure Stack et services Azure Resource Manager.|
| **AzS-XRP01** | Contrôleur de gestion d’infrastructure pour Microsoft Azure Stack, notamment les fournisseurs de ressources Calcul, Réseau et Stockage.|
| **AzS-SRNG01** | Machine virtuelle de prise en charge qui héberge le service de collecte des journaux pour Azure Stack. |

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus sur les tâches d’administration de base de l’ASDK](asdk-admin-basics.md)
