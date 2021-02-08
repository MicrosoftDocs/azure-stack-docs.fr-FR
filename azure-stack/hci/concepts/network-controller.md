---
title: Planifier le déploiement d’un contrôleur de réseau
description: Cette rubrique explique comment planifier le déploiement d’un contrôleur de réseau par le biais de Windows Admin Center sur un ensemble de machines virtuelles.
author: AnirbanPaul
ms.author: anpaul
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: bfea9216cefdc64c7749f8b49d5ecc3a422e5130
ms.sourcegitcommit: 0e58c5cefaa81541d9280c0e8a87034989358647
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99510786"
---
# <a name="plan-to-deploy-network-controller"></a>Planifier le déploiement d’un contrôleur de réseau

>S’applique à : Azure Stack HCI version 20H2, Windows Server 2019, Windows Server 2016

La planification du déploiement d’un contrôleur de réseau via Windows Admin Center nécessite un ensemble de machines virtuelles qui exécutent le système d’exploitation Azure Stack HCI ou Windows Server. Un contrôleur de réseau est un rôle serveur hautement disponible et scalable qui requiert un minimum de trois machines virtuelles pour fournir une haute disponibilité sur votre réseau.

   >[!NOTE]
   > Nous vous recommandons de déployer un contrôleur de réseau sur ses propres machines virtuelles dédiées.

## <a name="network-controller-requirements"></a>Configuration requise pour le contrôleur de réseau
Les éléments suivants sont requis pour déployer un contrôleur de réseau :
- Un disque dur virtuel pour le système d’exploitation Azure Stack HCI afin de créer les machines virtuelles du contrôleur de réseau.
- Un nom de domaine et des informations d’identification pour joindre les machines virtuelles du contrôleur de réseau à un domaine.
- Au moins un commutateur virtuel que vous configurez à l’aide de l’Assistant Création de cluster dans Windows Admin Center.
- Une configuration de réseau physique qui correspond à l’une des options de topologie de cette section.

    Windows Admin Center crée la configuration au sein de l’hôte Hyper-V. Toutefois, le réseau de gestion doit se connecter aux cartes physiques d’hôte selon l’une des trois options suivantes :

    **Option 1** : Le réseau de gestion est physiquement séparé des réseaux de charges de travail. Cette option utilise un seul commutateur virtuel pour le calcul et le stockage :

    :::image type="content" source="./media/network-controller/topology-option-1.png" alt-text="Option 1 pour créer un réseau physique pour le contrôleur de réseau." lightbox="./media/network-controller/topology-option-1.png":::

    **Option 2** : Le réseau de gestion est physiquement séparé des réseaux de charges de travail. Cette option utilise un seul commutateur virtuel pour le calcul uniquement :

    :::image type="content" source="./media/network-controller/topology-option-2.png" alt-text="Option 2 pour créer un réseau physique pour le contrôleur de réseau." lightbox="./media/network-controller/topology-option-2.png":::

    **Option 3** : Le réseau de gestion est physiquement séparé des réseaux de charges de travail. Cette option utilise deux commutateurs virtuels, un pour le calcul et un pour le stockage :

    :::image type="content" source="./media/network-controller/topology-option-3.png" alt-text="Option 3 pour créer un réseau physique pour le contrôleur de réseau." lightbox="./media/network-controller/topology-option-3.png":::

- Vous pouvez également associer les cartes physiques de gestion afin d’utiliser le même commutateur de gestion. Dans ce cas, nous vous recommandons encore d’utiliser l’une des options de cette section.
- Informations sur le réseau de gestion que le contrôleur de réseau utilise pour communiquer avec Windows Admin Center et les hôtes Hyper-V.
- Adressage basé sur DHCP ou sur un réseau statique pour les machines virtuelles du contrôleur de réseau.
- Nom de domaine complet (FQDN) REST (Representational State Transfer) du contrôleur de réseau utilisé par les clients de gestion pour communiquer avec le contrôleur de réseau.

   >[!NOTE]
   > Actuellement, Windows Admin Center ne prend pas en charge l’authentification du contrôleur de réseau pour la communication avec les clients REST ou la communication entre les machines virtuelles du contrôleur de réseau. Vous pouvez utiliser l’authentification Kerberos si vous utilisez PowerShell pour la déployer et la gérer.

## <a name="configuration-requirements"></a>Exigences de configuration
Vous pouvez déployer les nœuds de cluster du contrôleur de réseau sur le même sous-réseau ou sur des sous-réseaux différents. Si vous envisagez de déployer les nœuds de cluster du contrôleur de réseau sur des sous-réseaux différents, vous devez fournir le nom DNS REST du contrôleur de réseau au cours du processus de déploiement.

Pour plus d’informations, consultez [Configurer l’inscription DNS dynamique pour le contrôleur de réseau](/windows-server/networking/sdn/plan/installation-and-preparation-requirements-for-deploying-network-controller#step-3-configure-dynamic-dns-registration-for-network-controller).

## <a name="next-steps"></a>Étapes suivantes
Vous êtes maintenant prêt à déployer le contrôleur de réseau sur les machines virtuelles.

## <a name="see-also"></a>Voir aussi
- [Créer un cluster Azure Stack HCI](../deploy/create-cluster.md)
- [Déployer une infrastructure SDN avec SDN Express](../manage/sdn-express.md)
- [Vue d’ensemble du contrôleur de réseau](network-controller-overview.md)
- [Haute disponibilité du contrôleur de réseau](/windows-server/networking/sdn/technologies/network-controller/network-controller-high-availability)