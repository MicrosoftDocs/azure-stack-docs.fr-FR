---
title: Comparer Azure Stack HCI à Windows Server
description: Cette rubrique vous aide à déterminer si Azure Stack HCI ou Windows Server convient à votre organisation.
ms.topic: conceptual
author: khdownie
ms.author: v-kedow
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 01/21/2021
ms.openlocfilehash: c691424251d096794315880106c131ecaaea1f57
ms.sourcegitcommit: 925351b77490364b3d52746f788c4c1b93343631
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98705189"
---
# <a name="compare-azure-stack-hci-to-windows-server-2019"></a>Comparer Azure Stack HCI à Windows Server 2019

> S’applique à : Azure Stack HCI, version 20H2 ; Windows Server 2019

Cette rubrique explique les principales différences entre Azure Stack HCI et Windows Server 2019, et fournit de l’aide sur l’opportunité de l’utilisation de chacun d’eux. Les deux produits sont activement pris en charge et gérés par Microsoft. De nombreuses organisations peuvent choisir de déployer les deux, car elles sont destinées à des fins différentes et complémentaires.

## <a name="when-to-use-azure-stack-hci"></a>Quand utiliser Azure Stack HCI

Azure Stack HCI est la meilleure plateforme d’infrastructure hyperconvergée de Microsoft pour l’exécution de machines virtuelles ou de bureaux virtuels en local avec des connexions aux services hybrides Azure. Il s’agit d’un moyen simple de moderniser et de sécuriser vos centres de données et filiales, d’obtenir des performances optimales offrant une latence faible et la souveraineté des données.


:::image type="content" source="media/compare-windows-server/hci-scenarios.png" alt-text="Quand utiliser Azure Stack HCI sur Windows Server 2019" border="false" lightbox="media/compare-windows-server/hci-scenarios.png":::


Utilisez Azure Stack HCI pour :

- le meilleur hôte de virtualisation afin de moderniser votre infrastructure, que ce soit pour des charges de travail existantes dans votre centre de données principal ou pour des exigences émergentes pour les filiales et les sites en périphérie ;
- une extensibilité simple dans le cloud, avec un flux régulier d’innovations de votre abonnement Azure et un ensemble cohérent d’outils et d’expériences ;
- tous les avantages de l’infrastructure hyperconvergée : une architecture de centre de données plus simple et plus consolidée avec un stockage et une mise en réseau à grande vitesse.

  >[!NOTE]
  >Azure Stack HCI étant destiné à être utilisé en tant qu’hôte de virtualisation Hyper-V pour une architecture moderne, hyperconvergée, il n’inclut pas de droits d’invité. C’est pourquoi Azure Stack HCI est concédé sous licence uniquement pour exécuter un petit nombre de rôles serveur directement ; tous les autres rôles doivent s’exécuter à l’intérieur des machines virtuelles.

## <a name="when-to-use-windows-server-2019"></a>Quand utiliser Windows Server 2019

Windows Server 2019 est un système d’exploitation hautement polyvalent, avec des dizaines de rôles et des centaines de fonctionnalités, y compris des droits d’invité. Les machines Windows Server peuvent être dans le cloud ou en local, voire virtualisés sur Azure Stack HCI.


:::image type="content" source="media/compare-windows-server/windows-server-scenarios.png" alt-text="Quand utiliser Windows Server 2019 sur Azure Stack HCI" border="false" lightbox="media/compare-windows-server/windows-server-scenarios.png":::


Utilisez Windows Server 2019 pour :

- un système d’exploitation invité à l’intérieur de machines virtuelles (VM) ou de conteneurs ;
- comme runtime pour une application Windows.
- pour utiliser au moins un rôle serveur intégré, tel qu’Active Directory, les services de fichiers, DNS, DHCP ou Internet Information Services (IIS) ;
- comme un serveur traditionnel, tel qu’un contrôleur de domaine nu ou une installation SQL Server ;
- pour les infrastructures traditionnelles, telles que les machines virtuelles connectées au stockage SAN Fibre Channel ;

## <a name="compare-product-positioning"></a>comparer le positionnement du produit.

Le tableau suivant présente le packaging du produit de haut niveau pour Azure Stack HCI et Windows Server 2019.

| **Attribut**    | **Azure Stack HCI** | **Windows Server 2019** |
| ---------------- | ------------------- | ----------------------- |
| Type de produit     | Service cloud qui comprend un système d’exploitation et bien plus encore | Système d’exploitation |
| Informations juridiques            | Couvert dans le cadre de votre contrat client Microsoft ou de votre contrat d’abonnement en ligne | Contrat de Licence Utilisateur Final de l'application |
| Licences        | Facturé dans votre abonnement Azure | Possède sa propre licence payante |
| Support          | Couvert par le support Azure | Peut être couvert par différents contrats de support, notamment le Support Premier Microsoft |
| Comment les obtenir  | À télécharger à partir de [Azure.com/HCI](https://azure.com/hci)ou préinstallé sur les systèmes intégrés | Centre de gestion des licences en volume Microsoft ou centre d’évaluation |
| S’exécute sur les machines virtuelles      | À des fins d’évaluation uniquement ; conçu comme système d’exploitation hôte | Oui, dans le cloud ou en local |
| Matériel         | S’exécute sur plus de 200 solutions prévalidées du [catalogue Azure Stack HCI](https://hcicatalog.azurewebsites.net) | S’exécute sur tout matériel présentant le logo « Certifié pour Windows Server 2019 » |
| Stratégie de cycle de vie | Toujours à jour avec les dernières fonctionnalités | Choisissez entre les [canaux de maintenance de Windows Server](/windows-server/get-started-19/servicing-channels-19) : Canal de maintenance à long terme (LTSC) et canal semi-annuel (SAC) |

## <a name="compare-technical-features"></a>Comparer les fonctionnalités techniques

Le tableau suivant compare les fonctionnalités techniques d’Azure Stack HCI et Windows Server 2019.

| **Attribut** | **Azure Stack HCI** | **Windows Server 2019** |
| ------------- | ------------------- | ----------------------- |
| Core Hyper-V | Oui | Oui |
| Principaux espaces de stockage direct | Oui | Oui |
| SDN de base | Oui | Oui |
| Clustering Stretch pour la récupération d'urgence | Oui | - |
| Réparations d’espaces de stockage 4 à 5 fois plus rapides | Oui | - |
| Mises à jour intégrées du pilote et du microprogramme | Oui (systèmes intégrés uniquement) | - |
| Déploiement guidé | Oui | - |

## <a name="compare-management-options"></a>Comparer les options de gestion

Le tableau suivant compare les options de gestion d’Azure Stack HCI et Windows Server 2019. Les deux produits sont conçus pour la gestion à distance et peuvent être gérés avec de nombreux outils identiques.

| **Attribut** | **Azure Stack HCI** | **Windows Server 2019** |
| ------------- | ------------------- | ----------------------- |
| Expérience utilisateur | - | Oui |
| Windows Admin Center | Oui | Oui |
| Microsoft System Center | Oui (vendu séparément) | Oui (vendu séparément) |
| Portail Azure | Oui (en mode natif) | Requiert l’agent Arc |
| Outils tiers | Oui | Oui |

## <a name="compare-product-pricing"></a>Comparer les tarifs des produits

Le tableau ci-dessous compare les tarifs des produits pour Azure Stack HCI et Windows Server 2019. Pour plus d’informations, consultez [Prix appliqués à Azure Stack HCI](https://azure.microsoft.com/pricing/details/azure-stack/hci/).

| **Attribut** | **Azure Stack HCI** | **Windows Server 2019** |
| ------------- | ------------------- | ----------------------- |
| Type de prix | Service d’abonnement | Variable : le plus souvent, licence à usage unique |
| Structure tarifaire | Par cœur, par mois | Variable : généralement, par cœur |
| Price | 10 USD par cœur, par mois | Consultez [Tarification et licences pour Windows Server 2019](https://www.microsoft.com/windows-server/pricing) |
| Période d’évaluation/période d’essai | Essai gratuit de 30 jours une fois inscrit | Copie d’évaluation de 180 jours |
| Canaux | Contrat entreprise, fournisseur de services cloud ou direct | Contrat entreprise/licence en volume, OEM, contrat de licence de fournisseur de services (SPLA) |

## <a name="next-steps"></a>Étapes suivantes

- [Comparer Azure Stack HCI à Azure Stack Hub](compare-azure-stack-hub.md)
