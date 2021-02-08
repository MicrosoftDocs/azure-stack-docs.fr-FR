---
title: Gérer les réseaux logiques de locataire
description: Cette rubrique fournit des instructions pas à pas sur l’utilisation de Windows Admin Center en vue de créer, mettre à jour et supprimer des réseaux logiques après le déploiement du contrôleur de réseau.
author: AnirbanPaul
ms.author: anpaul
ms.topic: how-to
ms.date: 02/02/2021
ms.openlocfilehash: e45ca61a09bd989645998a2e8993f29176d1b9cf
ms.sourcegitcommit: 5f3d37994b8cb63c76e54136c0cc05bc4f475950
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99501067"
---
# <a name="manage-tenant-logical-networks"></a>Gérer les réseaux logiques de locataire

>S’applique à : Azure Stack HCI version 20H2, Windows Server 2019, Windows Server 2016

Cette rubrique fournit des instructions pas à pas sur l’utilisation de Windows Admin Center en vue de créer, mettre à jour et supprimer des réseaux logiques après le déploiement du contrôleur de réseau. Un réseau logique SDN est un réseau basé sur un réseau VLAN traditionnel.

En modélisant un réseau VLAN comme un réseau logique SDN, vous pouvez appliquer des stratégies réseau aux charges de travail qui sont attachées à ces réseaux. Par exemple, vous pouvez appliquer des listes de contrôle d’accès (ACL) de sécurité aux charges de travail qui sont attachées aux réseaux logiques SDN. L’application de listes de contrôle d’accès protège vos charges de travail VLAN des attaques externes et des attaques internes.

## <a name="create-a-logical-network"></a>Créer un réseau logique
Pour créer un réseau logique, effectuez les étapes suivantes dans Windows Admin Center.

:::image type="content" source="./media/tenant-logical-networks/create-logical-network.png" alt-text="Capture d’écran de la page d’accueil Windows Admin Center montrant le champ Nom du réseau logique." lightbox="./media/tenant-logical-networks/create-logical-network.png":::

1. Dans l’écran d’accueil de Windows Admin Center, sous **Toutes les connexions**, sélectionnez le cluster sur lequel vous voulez créer le réseau logique.
1. Sous **Outils**, faites défiler jusqu’à la zone **Réseau**, puis sélectionnez **Réseaux logiques**.
1. Sous **Réseaux logiques**, sélectionnez l’onglet **Inventaire**, puis sélectionnez **Nouveau**.
1. Dans le volet **Réseaux logiques**, tapez un nom pour le réseau logique.
1. Sous **Sous-réseau logique**, sélectionnez **Ajouter**.

    :::image type="content" source="./media/tenant-logical-networks/create-logical-network-subnet.png" alt-text="Capture d’écran de la page d’accueil Windows Admin Center montrant le volet Sous-réseau logique." lightbox="./media/tenant-logical-networks/create-logical-network-subnet.png":::

1. Dans le volet **Sous-réseau logique**, tapez un nom pour le sous-réseau, puis fournissez les informations suivantes :
    1. Un **ID VLAN** pour le réseau.
    1. Un **préfixe d’adresse** avec la notation CIDR (Classless Interdomain Routing).
    1. La **passerelle par défaut** du réseau.
    1. L’adresse du serveur **DNS**, si nécessaire.
    1. Cochez la case **Réseau logique public** si le réseau logique doit fournir une connectivité à des clients externes.
1. Sous **Pools d’adresses IP de sous-réseau logique**, sélectionnez **Ajouter**, puis fournissez les informations suivantes :
    1. Le **nom du pool d’adresses IP** logique.
    1. Une **adresse IP de début**.
    1. Une **adresse IP de fin**. Les adresses IP de début et de fin doivent être comprises dans le préfixe d’adresse fourni pour le sous-réseau.
    1. Sélectionnez **Ajouter**.
1. Dans la page **Sous-réseau logique**, sélectionnez **Ajouter**.
1. Dans la page **Réseaux logiques**, sélectionnez **Envoyer**.
1. Dans la liste **Réseaux logiques**, vérifiez que l’état du réseau logique est **Sain**.

## <a name="get-a-list-of-logical-networks"></a>Obtenir la liste des réseaux logiques
Vous pouvez facilement voir tous les réseaux logiques de votre cluster.

:::image type="content" source="./media/tenant-logical-networks/list-logical-networks.png" alt-text="Capture d’écran de la page d’accueil Windows Admin Center montrant le volet Inventaire sous Réseaux logiques." lightbox="./media/tenant-logical-networks/list-logical-networks.png":::

1. Dans l’écran d’accueil de Windows Admin Center, sous **Toutes les connexions**, sélectionnez le cluster dans lequel vous souhaitez afficher les réseaux logiques.
1. Sous **Outils**, faites défiler jusqu’à la zone **Réseau**, puis sélectionnez **Réseaux logiques**.
1. L’onglet **Inventaire** liste tous les réseaux logiques qui sont disponibles sur le cluster, et fournit des commandes permettant de gérer les réseaux logiques individuellement. Vous pouvez :
    - Afficher la liste des réseaux logiques.
    - Afficher les paramètres des réseaux logiques, l’état de chaque réseau logique, et voir si la virtualisation réseau est activée pour chaque réseau logique. Si la virtualisation réseau est activée, vous pouvez également afficher le nombre de réseaux virtuels qui sont associés à chaque réseau logique.
    - Changer les paramètres d’un réseau logique.
    - Supprimer un réseau logique.

## <a name="view-logical-network-details"></a>Afficher les informations d’un réseau logique
Vous pouvez voir des informations détaillées sur chaque réseau logique dans la page qui lui est dédiée.

:::image type="content" source="./media/tenant-logical-networks/view-logical-network-details.png" alt-text="Capture d’écran de Windows Admin Center montrant les informations relatives à un réseau logique." lightbox="./media/tenant-logical-networks/view-logical-network-details.png":::

1. Sous **Outils**, faites défiler jusqu’à la zone **Réseau**, puis sélectionnez **Réseaux logiques**.
1. Sélectionnez l’onglet **Inventaire**, puis sélectionnez le réseau logique pour lequel vous souhaitez afficher des informations. Dans la page qui suit, vous pouvez :
    - Voir l’état de provisionnement du réseau logique (Réussite, Échec).
    - Voir si la virtualisation réseau est activée pour le réseau logique.
    - Voir les sous-réseaux du réseau logique.
    - Ajouter des sous-réseaux, supprimer des sous-réseaux et modifier les paramètres des sous-réseaux logiques.
    - Sélectionnez un sous-réseau pour accéder à la page **Sous-réseau** qui lui est consacrée, et dans laquelle vous pouvez ajouter, supprimer et modifier des pools d’IP de sous-réseau logique.
    - Voir les réseaux virtuels et les connexions qui sont associés au réseau logique.

## <a name="change-a-logical-networks-virtualization-setting"></a>Changer le paramètre de virtualisation d’un réseau logique
Vous pouvez changer le paramètre de virtualisation réseau d’un réseau logique.

:::image type="content" source="./media/tenant-logical-networks/change-logical-network-setting.png" alt-text="Capture d’écran de Windows Admin Center montrant la case à cocher Activer la virtualisation réseau d’un réseau logique." lightbox="./media/tenant-logical-networks/change-logical-network-setting.png":::

1. Sous **Outils**, faites défiler jusqu’à la zone **Réseau**, puis sélectionnez **Réseaux logiques**.
1. Sélectionnez l’onglet **Inventaire**, sélectionnez le réseau logique, puis sélectionnez **Paramètres**.
1. Si vous prévoyez de déployer des réseaux virtuels sur le réseau logique, sous le nom du réseau logique, cochez la case **Activer la virtualisation réseau**, puis sélectionnez **Envoyer**.

## <a name="delete-a-logical-network"></a>Supprimer un réseau logique
Vous pouvez supprimer les réseaux logiques lorsque vous n’en avez plus besoin.

:::image type="content" source="./media/tenant-logical-networks/delete-logical-network.png" alt-text="Capture d’écran de Windows Admin Center montrant le message vous demandant de confirmer la suppression du réseau logique." lightbox="./media/tenant-logical-networks/delete-logical-network.png":::

1. Sous **Outils**, faites défiler jusqu’à la zone **Réseau**, puis sélectionnez **Réseaux logiques**.
1. Sélectionnez l’onglet **Inventaire**, sélectionnez le réseau logique, puis sélectionnez **Supprimer**.
1. Ensuite, dans l’invite de confirmation **Supprimer**, sélectionnez **Oui**.
1. En regard de la zone de recherche **Réseaux logiques**, sélectionnez **Actualiser** pour vérifier que le réseau logique a bien été supprimé.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d'informations, consultez également :
- [Gérer les réseaux virtuels locataires](tenant-virtual-networks.md)
- [Mise en réseau SDN (Software Defined Networking) dans Azure Stack HCI](../concepts/software-defined-networking.md)
