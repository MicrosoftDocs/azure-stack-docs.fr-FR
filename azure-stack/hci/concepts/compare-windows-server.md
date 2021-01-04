---
title: Comparer Azure Stack HCI à Azure Stack Hub et Windows Server
description: Cette rubrique vous aide à déterminer si Azure Stack HCI, Azure Stack Hub ou Windows Server convient à votre organisation.
ms.topic: conceptual
author: khdownie
ms.author: v-kedow
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 12/11/2020
ms.openlocfilehash: bd1798ddfcf0897eeaedaeaae0cfb02eac1aaa1e
ms.sourcegitcommit: 79e8df69b139bfa21eb83aceb824b97e7f418c03
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2020
ms.locfileid: "97365157"
---
# <a name="compare-azure-stack-hci-to-azure-stack-hub-and-windows-server"></a>Comparer Azure Stack HCI à Azure Stack Hub et Windows Server

> S’applique à : Azure Stack HCI, version 20H2 ; Windows Server 2019 ; Azure Stack Hub

De nombreux clients se demandent si c’est Windows Server, Azure Stack HCI ou Azure Stack Hub qui est le plus adapté à leurs besoins. Cette rubrique va vous aider à identifier le produit qui convient pour votre organisation. 

## <a name="compare-azure-stack-hci-to-windows-server"></a>Comparer Azure Stack HCI à Windows Server

Windows Server et Azure Stack HCI offrent la même expérience utilisateur de haute qualité avec une feuille de route des nouvelles versions.

| Windows Server | Azure Stack HCI |
| --------------- | --------------- |
| Meilleur serveur traditionnel et invité | Meilleur hôte de virtualisation pour un centre de données à définition logicielle, y compris les espaces de stockage direct |
| S’exécute en tout lieu, à l’aide d’un modèle de gestion de licences logicielles traditionnel | S’exécute sur le matériel du fournisseur de votre choix, mais est fourni en tant que service Azure et est facturé sur votre compte Azure |
| Deux options d’installation : Serveur avec expérience utilisateur ou Server Core | Basé sur un noyau de serveur peu personnalisé |

### <a name="when-to-use-windows-server"></a>Quand utiliser Windows Server

| Windows Server | Azure Stack HCI |
| --------------- | --------------- |
| Windows Server est un système d’exploitation hautement polyvalent, avec des dizaines de rôles et des centaines de fonctionnalités, y compris des droits d’invité. | Azure Stack HCI n’inclut pas de droits d’invité et est destiné à être utilisé dans le cadre d’une architecture hyperconvergée moderne. |
| Utilisez Windows Server pour exécuter des machines virtuelles ou pour des installations complètes englobant tous les rôles serveur traditionnels, y compris Active Directory, les services de fichiers, DNS, DHCP, Internet Information Services (IIS), hôte de conteneur/invité, SQL Server, Exchange Server, service Guardian hôte et bien plus encore. | Conçu en tant qu’hôte de virtualisation Hyper-V, Azure Stack HCI est concédé sous licence uniquement pour exécuter un petit nombre de rôles serveur directement ; tous les autres rôles doivent s’exécuter à l’intérieur des machines virtuelles. |

### <a name="when-to-use-azure-stack-hci"></a>Quand utiliser Azure Stack HCI

| Windows Server | Azure Stack HCI |
| --------------- | --------------- |
| Windows Server peut s’exécuter localement ou dans le cloud, mais il ne s’agit pas d’une offre hyperconvergé complète.| Utilisez Azure Stack HCI pour exécuter des machines virtuelles localement, éventuellement étendues sur deux sites et avec des connexions aux services hybrides Azure. Il s’agit d’un moyen simple de moderniser et de sécuriser vos centres de données et succursales, d’obtenir des performances optimales pour les bases de données SQL Server et d’exécuter des bureaux virtuels localement afin de bénéficier d’une latence faible et de préserver la souveraineté des données.|
| Windows Server est un formidable couteau suisse polyvalent pour tous les rôles Windows Server, virtualisés ou non. | Utilisez Azure Stack HCI pour virtualiser des applications d’entreprise classiques comme Exchange, SharePoint et SQL Server, et pour virtualiser des rôles Windows Server comme Serveur de fichiers, DNS, DHCP, IIS et Active Directory. Comprend un accès illimité à toutes les fonctionnalités Hyper-V, comme les machines virtuelles dotées d’une protection maximale.|
| De nombreux déploiements Windows Server s’exécutent sur du matériel vieillissant. | Utilisez Azure Stack HCI pour utiliser une infrastructure à définition logicielle au lieu de baies de stockage ou d’appliances réseau vieillissantes, sans reconcevoir toute l’architecture. Les fonctionnalités Hyper-V, Espaces de stockage direct et Software-Defined Networking (SDN) intégrées sont directement accessibles et gérables. Exécutez les applications dans des machines virtuelles Windows ou Linux.|

## <a name="compare-azure-stack-hci-to-azure-stack-hub"></a>Comparer Azure Stack HCI à Azure Stack Hub

Alors que votre organisation poursuit déjà sa transformation numérique, vous trouvez que vous pouvez changer plus rapidement en utilisant les services de cloud public pour créer des architectures modernes et actualiser les applications héritées. Pourtant, un grand nombre de charges de travail doivent rester locales, à cause d’obstacles technologiques et réglementaires. Utilisez ce tableau pour déterminer quelle stratégie cloud hybride Microsoft fournit ce dont vous avez besoin là où vous en avez besoin, en mettant à votre disposition les innovations du cloud pour les charges de travail où qu’elles soient.

| Azure Stack Hub | Azure Stack HCI |
| --------------- | --------------- |
| Nouvelles qualifications, processus novateurs | Mêmes qualifications, processus familiers |
| Services Azure dans votre centre de données | Connecter votre centre de données aux services Azure |

### <a name="when-to-use-azure-stack-hub"></a>Quand utiliser Azure Stack Hub

| Azure Stack Hub | Azure Stack HCI |
| --------------- | --------------- |
| Utilisez Azure Stack Hub pour bénéficier d’une fonctionnalité Infrastructure-as-a-Service (IaaS), avec une isolation renforcée, un suivi précis de l’utilisation et une facturation interne pour plusieurs locataires colocalisés. Idéal pour les fournisseurs de services et les clouds privés d’entreprise. Modèles issus de la Place de marché Azure. | Azure Stack HCI ne permet pas d’appliquer ni de fournir une multilocation de manière native. |
| Utilisez Azure Stack Hub pour développer et exécuter des applications qui s’appuient sur des services Platform-as-a-Service (PaaS) comme Web Apps, Functions ou Event Hubs en local. Ces services s’exécutent sur Azure Stack Hub exactement comme dans Azure, en fournissant un environnement de développement et d’exécution hybride cohérent. | Azure Stack HCI n’exécute pas de services PaaS en local. |
| Utilisez Azure Stack Hub pour moderniser le déploiement et le fonctionnement d’applications avec des pratiques DevOps comme l’infrastructure en tant que code, l’intégration continue et le déploiement continu (CI/CD), ainsi que des fonctionnalités utiles comme les extensions de machine virtuelle cohérentes avec Azure. Idéal pour les équipes de développement et DevOps. | Azure Stack HCI n’inclut aucun outil DevOps de manière native. |

### <a name="when-to-use-azure-stack-hci"></a>Quand utiliser Azure Stack HCI

| Azure Stack Hub | Azure Stack HCI |
| --------------- | --------------- |
| Azure Stack Hub nécessite au moins 4 nœuds et ses propres commutateurs réseau. | Utilisez Azure Stack HCI pour obtenir l’empreinte mémoire minimale pour les bureaux et branches. Commencez avec seulement 2 nœuds serveur et un réseau dos à dos sans commutateur à des fins de simplicité et d’abordabilité de la charge maximale. Les offres de matériel commencent à 4 lecteurs, 64 Go de mémoire, bien en dessous de 10 000 $/nœud. |
| Azure Stack Hub limite la configurabilité Hyper V et l’ensemble des fonctionnalités à des fins de cohérence avec Azure. | Utilisez Azure Stack HCI pour une virtualisation Hyper-V simple pour des applications d’entreprise classiques comme Exchange, SharePoint et SQL Server, et pour virtualiser des rôles Windows Server comme Serveur de fichiers, DNS, DHCP, IIS et Active Directory. Accès illimité à toutes les fonctionnalités Hyper-V, comme les machines virtuelles dotées d’une protection maximale.|
| Azure Stack Hub n’expose pas ces technologies d’infrastructure. | Utilisez Azure Stack HCI pour utiliser une infrastructure à définition logicielle au lieu de baies de stockage ou d’appliances réseau vieillissantes, sans reconcevoir toute l’architecture. Les fonctionnalités Hyper-V, Espaces de stockage direct et Software-Defined Networking (SDN) intégrées sont directement accessibles et gérables. |

## <a name="next-steps"></a>Étapes suivantes

- [Qu’est-ce qu’Azure Stack HCI ?](../overview.md)
