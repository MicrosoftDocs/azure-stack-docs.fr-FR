---
title: Comparer Azure Stack HCI à Azure Stack Hub
description: Cette rubrique vous aide à déterminer si Azure Stack HCI ou Azure Stack Hub convient à votre organisation.
ms.topic: conceptual
author: khdownie
ms.author: v-kedow
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 02/04/2021
ms.openlocfilehash: 7c501650d5c9a4d2d6df66269f689d80ffd3f8e0
ms.sourcegitcommit: 283b1308142e668749345bf24b63d40172559509
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99570682"
---
# <a name="compare-azure-stack-hci-to-azure-stack-hub"></a>Comparer Azure Stack HCI à Azure Stack Hub

> S’applique à : Azure Stack HCI, version 20H2, Azure Stack Hub

Alors que votre organisation poursuit déjà sa transformation numérique, vous trouvez que vous pouvez changer plus rapidement en utilisant les services de cloud public pour créer des architectures modernes et actualiser les applications héritées. Pourtant, un grand nombre de charges de travail doivent rester locales, à cause d’obstacles technologiques et réglementaires. Utilisez ce tableau pour déterminer quelle stratégie cloud hybride Microsoft fournit ce dont vous avez besoin là où vous en avez besoin, en mettant à votre disposition les innovations du cloud pour les charges de travail où qu’elles soient.

| Azure Stack HCI | Azure Stack Hub |
| --------------- | --------------- |
| Mêmes qualifications, processus familiers | Nouvelles qualifications, processus novateurs |
| Connecter votre centre de données aux services Azure | Services Azure dans votre centre de données |

## <a name="when-to-use-azure-stack-hci"></a>Quand utiliser Azure Stack HCI

Le tableau suivant compare les cas d’utilisation pour lesquels Azure Stack HCI est mieux adapté qu’Azure Stack Hub.

| Azure Stack HCI                                                                 | Azure Stack Hub                                                                         |
| ------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------- |
| Utilisez Azure Stack HCI pour obtenir l’empreinte mémoire minimale pour les bureaux et branches. Commencez avec seulement 2 serveurs et un réseau dos à dos sans commutateur à des fins de simplicité et d’abordabilité de la charge maximale. | Azure Stack Hub nécessite au moins 4 serveurs et leurs propres commutateurs réseau. |
| Utilisez Azure Stack HCI pour virtualiser des applications d’entreprise classiques comme Exchange, SharePoint et SQL Server, et pour virtualiser des rôles Windows Server comme Serveur de fichiers, DNS, DHCP, IIS et Active Directory. Fournit un accès illimité aux fonctionnalités Hyper-V.| Azure Stack Hub limite la configurabilité Hyper-V et l’ensemble des fonctionnalités à des fins de cohérence avec Azure. | 
| Utilisez Azure Stack HCI pour utiliser une infrastructure à définition logicielle au lieu de baies de stockage ou d’appliances réseau vieillissantes, sans reconcevoir toute l’architecture. Les fonctionnalités Hyper-V, Espaces de stockage direct et Software-Defined Networking (SDN) intégrées sont directement accessibles et gérables. | Azure Stack Hub n’expose pas ces technologies d’infrastructure. |

## <a name="when-to-use-azure-stack-hub"></a>Quand utiliser Azure Stack Hub

Le tableau suivant compare les cas d’utilisation pour lesquels Azure Stack Hub est mieux adapté qu’Azure Stack HCI.

| Azure Stack HCI                                                                 | Azure Stack Hub                                                                          |
| ------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| Azure Stack HCI ne permet pas d’appliquer ni de fournir une multilocation de manière native. | Utilisez Azure Stack Hub pour bénéficier d’une fonctionnalité Infrastructure-as-a-Service (IaaS), avec une isolation renforcée, un suivi précis de l’utilisation et une facturation interne pour plusieurs locataires colocalisés. Idéal pour les fournisseurs de services et les clouds privés d’entreprise. Modèles issus de la Place de marché Azure. | 
| Azure Stack HCI n’est pas conçu pour exécuter des services PaaS (Platform-as-a-Service) au niveau local, bien qu’il offre la possibilité d’héberger [Azure Kubernetes Service](../../aks-hci/overview.md). | Utilisez Azure Stack Hub pour développer et exécuter des applications qui s’appuient sur des services PaaS comme Web Apps, Functions ou Event Hubs en local. Ces services s’exécutent sur Azure Stack Hub exactement comme dans Azure, en fournissant un environnement de développement et d’exécution hybride cohérent. |
| Azure Stack HCI n’inclut aucun outil DevOps de manière native. | Utilisez Azure Stack Hub pour moderniser le déploiement et le fonctionnement d’applications avec des pratiques DevOps comme l’infrastructure en tant que code, l’intégration continue et le déploiement continu (CI/CD), ainsi que des fonctionnalités utiles comme les extensions de machine virtuelle cohérentes avec Azure. Idéal pour les équipes de développement et DevOps. |

## <a name="next-steps"></a>Étapes suivantes

- [Comparer Azure Stack HCI à Windows Server 2019](compare-windows-server.md)
