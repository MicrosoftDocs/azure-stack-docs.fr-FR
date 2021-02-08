---
title: Gérer les réseaux virtuels locataires
description: Cette rubrique fournit des instructions pas à pas sur l’utilisation de Windows Admin Center en vue de créer, mettre à jour et supprimer des réseaux virtuels de type Virtualisation de réseau Hyper-V après le déploiement du SDN (Software Defined Networking).
author: AnirbanPaul
ms.author: anpaul
ms.topic: how-to
ms.date: 02/01/2021
ms.openlocfilehash: 6af08615a25ed93f62f526d92ead7511699c8439
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248622"
---
# <a name="manage-tenant-virtual-networks"></a>Gérer les réseaux virtuels locataires

>S’applique à : Azure Stack HCI version 20H2, Windows Server 2019, Windows Server 2016

Cette rubrique fournit des instructions pas à pas sur l’utilisation de Windows Admin Center en vue de créer, mettre à jour et supprimer des réseaux virtuels de type Virtualisation de réseau Hyper-V après le déploiement du SDN (Software Defined Networking).

La virtualisation de réseau Hyper-V permet d’isoler les réseaux des locataires afin qu’ils constituent une entité distincte. Les entités ne peuvent pas être connectées entre elles, sauf si vous configurez des charges de travail d’accès public ou un peering de réseaux virtuels.

## <a name="create-a-virtual-network"></a>Créer un réseau virtuel
Pour créer un réseau virtuel, effectuez les étapes suivantes dans Windows Admin Center.

:::image type="content" source="./media/tenant-virtual-networks/create-virtual-network.png" alt-text="Capture d’écran de la page d’accueil de Windows Admin Center montrant le volet dans lequel créer le nom et le préfixe d’adresse d’un réseau virtuel." lightbox="./media/tenant-virtual-networks/create-virtual-network.png":::

1. Dans l’écran d’accueil de Windows Admin Center, sous **Toutes les connexions**, sélectionnez le cluster sur lequel vous voulez créer le réseau virtuel.
1. Sous **Outils**, faites défiler jusqu’à la zone **Réseau**, puis sélectionnez **Réseaux virtuels**.
1. Sous **Réseaux virtuels**, sélectionnez l’onglet **Inventaire**, puis sélectionnez **Nouveau**.
1. Dans le volet **Réseaux virtuels**, tapez un nom pour le réseau virtuel.
1. Sous **Préfixes d’adresse**, sélectionnez **Ajouter**, puis tapez un préfixe d’adresse en utilisant la notation CIDR (Classless Interdomain Routing). Si vous le souhaitez, vous pouvez ajouter des préfixes d’adresse supplémentaires.
1. Sous **Sous-réseaux**, sélectionnez **Ajouter**, tapez un nom pour le sous-réseau, puis spécifiez un préfixe d’adresse en utilisant la notation CIDR.

   >[!NOTE]
   > Le préfixe d’adresse du sous-réseau doit être compris dans la plage de préfixes que vous avez définie dans le champ **Préfixes d’adresse**, sous Réseau virtuel.

1. Sélectionnez **Envoyer** ou, si vous le souhaitez, ajoutez des sous-réseaux, puis sélectionnez **Envoyer**.
1. Dans la liste **Réseaux virtuels**, vérifiez que l’état du réseau virtuel est **Sain**.

## <a name="get-a-list-of-virtual-networks"></a>Obtenir la liste des réseaux virtuels
Vous pouvez facilement voir tous les réseaux virtuels de votre cluster.

:::image type="content" source="./media/tenant-virtual-networks/list-virtual-networks.png" alt-text="Capture d’écran de Windows Admin Center montrant la liste des réseaux virtuels." lightbox="./media/tenant-virtual-networks/list-virtual-networks.png":::

1. Dans l’écran d’accueil de Windows Admin Center, sous **Toutes les connexions**, sélectionnez le cluster dans lequel vous souhaitez afficher les réseaux virtuels.
1. Sous **Outils**, faites défiler jusqu’à la zone **Réseau**, puis sélectionnez **Réseaux virtuels**.
1. L’onglet **Inventaire** liste tous les réseaux virtuels qui sont disponibles sur le cluster, et fournit des commandes permettant de gérer les réseaux virtuels individuellement. Vous pouvez :
    - Voir la liste des réseaux virtuels.
    - Voir les paramètres des réseaux virtuels, l’état de chaque réseau virtuel, ainsi que le nombre de machines virtuelles connectées à chaque réseau virtuel.
    - Changer les paramètres d’un réseau virtuel.
    - Supprimez un réseau virtuel.

## <a name="view-virtual-network-details"></a>Voir les informations concernant un réseau virtuel
Vous pouvez voir des informations détaillées sur chaque réseau virtuel dans la page qui lui est dédiée.

:::image type="content" source="./media/tenant-virtual-networks/view-virtual-network-details.png" alt-text="Capture d’écran de Windows Admin Center montrant les informations relatives à un réseau virtuel." lightbox="./media/tenant-virtual-networks/view-virtual-network-details.png":::

1. Sous **Outils**, faites défiler jusqu’à la zone **Réseau**, puis sélectionnez **Réseaux virtuels**.
1. Sélectionnez l’onglet **Inventaire**, puis sélectionnez le réseau virtuel pour lequel vous souhaitez afficher des informations. Dans la page qui suit, vous pouvez :
    - Voir l’**état de provisionnement** du réseau virtuel (Réussite, Échec).
    - Voir l’**état de configuration** du réseau virtuel (Sain, Erreur, Avertissement, Inconnu).
    - Voir le **réseau logique** sous-jacent du réseau virtuel.
    - Voir l’**espace d’adressage** du réseau virtuel.
    - Ajouter des sous-réseaux, supprimer des sous-réseaux et modifier les paramètres des sous-réseaux virtuels.
    - Voir les **connexions de machines virtuelles** au réseau virtuel.

## <a name="change-virtual-network-settings"></a>Modifier les paramètres de réseau virtuel
Vous pouvez mettre à jour les préfixes d’adresse des réseaux virtuels, gérer le peering des réseaux virtuels et configurer un routeur BGP, ainsi que des pairs pour le réseau virtuel.

:::image type="content" source="./media/tenant-virtual-networks/change-virtual-network-settings.png" alt-text="Capture d’écran de Windows Admin Center montrant la vue Paramètres d’un réseau virtuel." lightbox="./media/tenant-virtual-networks/change-virtual-network-settings.png":::

1. Sous **Outils**, faites défiler jusqu’à la zone **Réseau**, puis sélectionnez **Réseaux virtuels**.
1. Sélectionnez l’onglet **Inventaire**, sélectionnez le réseau virtuel, puis sélectionnez **Paramètres**.
1. Sous l’onglet **Général**, vous pouvez :
    - Supprimer les préfixes d’adresse existants ou en ajouter de nouveaux.
    - Configurer le peering avec un autre réseau virtuel.
    - Ajouter un routeur BGP au réseau virtuel. Pour ce faire, vous devez fournir le nom du routeur BGP et le numéro de système autonome (ASN).
    - Ajouter un ou plusieurs pairs BGP pour le routeur BGP. Pour ce faire, vous devez fournir le nom et le numéro ASN de chaque pair BGP.

## <a name="delete-a-virtual-network"></a>Supprimer un réseau virtuel
Vous pouvez supprimer les réseaux virtuels lorsque vous n’en avez plus besoin.

:::image type="content" source="./media/tenant-virtual-networks/delete-virtual-network.png" alt-text="Capture d’écran de Windows Admin Center montrant le message vous demandant de confirmer la suppression du réseau virtuel." lightbox="./media/tenant-virtual-networks/delete-virtual-network.png":::

1. Sous **Outils**, faites défiler jusqu’à la zone **Réseau**, puis sélectionnez **Réseaux virtuels**.
1. Sélectionnez l’onglet **Inventaire**, sélectionnez le réseau virtuel, puis sélectionnez **Supprimer**.
1. Dans l’invite de confirmation **Supprimer le réseau virtuel**, sélectionnez **Supprimer**.
1. En regard de la zone de recherche Réseaux virtuels, sélectionnez **Actualiser** pour vérifier que le réseau virtuel a bien été supprimé.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d'informations, consultez également :
- [Mise en réseau SDN (Software Defined Networking) dans Azure Stack HCI](../concepts/software-defined-networking.md)
