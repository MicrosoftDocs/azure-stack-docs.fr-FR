---
title: Architecture de l’ASDK
description: En savoir plus sur l’architecture du Kit de développement Azure Stack (ASDK).
author: justinha
ms.topic: article
ms.date: 06/28/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 06/28/2019
ms.openlocfilehash: 207b99b9a3cbb6c030a6e79137d036820b3b3f60
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "79295172"
---
# <a name="asdk-architecture"></a>Architecture de l’ASDK
Le Kit de développement Azure Stack (ASDK) est un déploiement à nœud unique d’Azure Stack qui s'exécute sur un seul ordinateur hôte. Des composants de routage Edge sont installés sur l’ordinateur hôte afin de fournir des fonctionnalités NAT et VPN pour Azure Stack. Les rôles d’infrastructure Azure Stack s’exécutent dans la couche Hyper-V de l’ordinateur hôte physique.


## <a name="virtual-machine-roles"></a>Rôles de machine virtuelle
L’ASDK propose des services à l’aide des machines virtuelles suivantes qui sont hébergées sur l’ordinateur hôte du Kit de développement :

| Name | Description |
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
[En savoir plus sur les tâches d’administration de base ASDK](asdk-admin-basics.md)
