---
title: Déployer des modèles fondamentaux sur Azure Stack Hub
description: Découvrez comment déployer des modèles fondamentaux avec Azure Stack Hub.
author: mattbriggs
ms.topic: how-to
ms.date: 04/20/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 11/06/2019
ms.openlocfilehash: d3450c78c3dc0632d5230d65bcd176b5067be852
ms.sourcegitcommit: 2a144c367a17eb1f8db58dd7bf1c820520182015
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85835073"
---
# <a name="deploy-foundational-patterns-overview"></a>Vue d’ensemble du déploiement de modèles fondamentaux


Chacun de ces modèles contient des conseils, des modèles Azure Resource Manager et des tutoriels. Vous pouvez utiliser ces modèles avec des applications tierces pour créer des offres qui ne sont pas encore prises en charge par Azure Stack. Par exemple, les opérateurs gèrent souvent les complexités liées à la configuration d’un réseau privé virtuel (VPN) pour une seule instance Azure Stack Hub, et beaucoup moins à la création d’un VPN qui s’étend sur deux environnements ou plus. Les opérateurs peuvent rencontrer des problèmes lors de la tentative de création d’un équilibreur de charge devant une instance Azure Stack Hub pour gérer les charges de travail. Avec les instructions suivantes, vous pouvez accélérer le déploiement afin de libérer vos charges de travail prêtes pour la production.

## <a name="networking"></a>Mise en réseau

Utilisez les modèles de mise en réseau pour trouver des instructions sur la création d’un peering de réseaux virtuels avec Azure Stack Hub. Le peering de réseaux virtuels vous permet de connecter deux réseaux virtuels pour qu’ils apparaissent comme un seul réseau. La connectivité de site à site entre différents réseaux virtuels s’effectue par le biais du service Routage et accès distant (RRAS). Le service RRAS permet aux machines virtuelles Windows de fonctionner comme des routeurs. Avec ces scripts, vous pouvez déployer deux réseaux virtuels entre des groupes de ressources d’un groupe de ressources Azure Stack Hub, entre différents abonnements et entre deux instances Azure Stack Hub. Vous pouvez déployer les scripts sur Azure Stack Hub et sur Azure global. 

Chaque article traite des considérations courantes, par exemple : 
- Scale
- Bande passante
- Sécurité
- Continuité de l’activité

|  Peering de réseau virtuel  |  VPN  |  Équilibrage de charge  |
| --- | --- | --- |
| ![Peering de réseaux virtuels avec des machines virtuelles](media/deploy-foundational-patterns/icon-networking-61-virtual-networks.svg)<br>[Peering de réseaux virtuels avec des machines virtuelles](azure-stack-network-howto-vnet-peering.md) | ![Configurer un VPN sur local](media/deploy-foundational-patterns/icon-networking-63-virtual-network-gateways.svg)<br>[Configurer un VPN sur local](azure-stack-network-howto-vnet-to-onprem.md) | ![Équilibreur de charge F5](media/deploy-foundational-patterns/icon-networking-62-load-balancers.svg)<br>[Équilibreur de charge F5](network-howto-f5.md) |
| ![Peering de réseaux virtuels avec FortiGate](media/deploy-foundational-patterns/icon-networking-61-virtual-networks.svg)<br>[Peering de réseaux virtuels avec FortiGate](azure-stack-network-howto-vnet-to-vnet.md) | ![Réseau privé virtuel](media/deploy-foundational-patterns/icon-networking-63-virtual-network-gateways.svg)<br>[Connexion de réseau virtuel à réseau virtuel](azure-stack-network-howto-vnet-to-vnet-stacks.md) |  |
|  | ![Créer un tunnel VPN (GRE)](media/deploy-foundational-patterns/icon-networking-63-virtual-network-gateways.svg)<br>[Créer un tunnel VPN (GRE)](network-howto-vpn-tunnel-gre.md) | |
|  | ![Configurer un VPN de site à site multiple](media/deploy-foundational-patterns/icon-networking-63-virtual-network-gateways.svg)<br>[Configurer un VPN de site à site multiple](network-howto-vpn-tunnel.md) | |
|  | ![Créer un tunnel VPN (IPSEC)](media/deploy-foundational-patterns/icon-networking-63-virtual-network-gateways.svg)<br>[Créer un tunnel VPN (IPSEC)](network-howto-vpn-tunnel-ipsec.md)| |


## <a name="storage"></a>Stockage

Utilisez les modèles de stockage pour augmenter vos options de stockage avec Azure Stack Hub. Dans Azure Stack Hub, le stockage est limité. Connectez-vous aux ressources de votre centre de ressources existant. Pour obtenir des instructions sur la création d’une machine virtuelle Windows dans Azure Stack Hub, connectez-vous à une cible iSCSI externe. Vous pouvez apprendre à activer des fonctionnalités clés comme MPIO (Multipath I/O) afin d’optimiser les performances et la connectivité entre la machine virtuelle et le stockage externe.

| Stockage iSCSI | Étendre le stockage |
| --- | --- |
| ![Se connecter au stockage iSCSI](media/deploy-foundational-patterns/icon-storage-87-storage-accounts-classic.svg)<br>[Se connecter au stockage iSCSI](azure-stack-network-howto-iscsi-storage.md) | ![Étendre le centre de données](media/deploy-foundational-patterns/icon-storage-88-recovery-services-vaults.svg)<br>[Étendre le centre de données](azure-stack-network-howto-extend-datacenter.md) |

## <a name="backup"></a>Backup

Vous pouvez utiliser les modèles de sauvegarde et de reprise d’activité après sinistre pour copier toutes les ressources d’un abonnement vers Azure ou une autre instance Azure Stack Hub. Ces modèles examinent l’utilisation de Commvault live-sync pour répliquer les informations stockées sur l’intérieur des machines virtuelles vers un autre environnement. Vous pouvez trouver des scripts permettant de créer un compte de stockage et un compte de stockage de sauvegarde pour envoyer les données. Avec le réplicateur d’abonnements Azure du module, vous pouvez orchestrer la réplication des ressources, et vous pouvez personnaliser le processeur pour gérer diverses ressources. 



|  Sauvegarder  |  Copier  |
| --- | --- |
| ![Sauvegarder votre machine virtuelle sur Azure Stack Hub avec CommVault](media/deploy-foundational-patterns/icon-storage-100-import-export-jobs.svg)<br>[Sauvegarder votre machine virtuelle sur Azure Stack Hub avec CommVault](azure-stack-network-howto-backup-commvault.md) | ![Copier des ressources d’abonnement](media/deploy-foundational-patterns/icon-storage-94-data-box.svg)<br>[Copier des ressources d’abonnement](azure-stack-network-howto-backup-replicator.md) |
|  ![Sauvegarder vos comptes de stockage sur Azure Stack Hub](media/deploy-foundational-patterns/icon-storage-93-storage-sync-services.svg)<br>[Sauvegarder vos comptes de stockage sur Azure Stack Hub](azure-stack-network-howto-backup-storage.md)  | |

## <a name="github-samples"></a>Exemples GitHub

Vous trouverez les modèles dans le dépôt GitHub [Azure Intelligent Edge Patterns](https://github.com/Azure-Samples/azure-intelligent-edge-patterns).

## <a name="next-steps"></a>Étapes suivantes

[Documentation sur les modèles hybrides et les solutions Azure](/hybrid/app-solutions)
