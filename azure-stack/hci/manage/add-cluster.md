---
title: Ajouter ou supprimer des serveurs pour un cluster Azure Stack HCI
description: Découvrir comment ajouter ou supprimer des nœuds de serveur pour un cluster dans Azure Stack HCI
ms.topic: how-to
author: v-dasis
ms.author: v-dasis
ms.reviewer: jgerend
ms.date: 05/20/2020
ms.openlocfilehash: 81b78e542fe14981e0b0ba501a326a537a2a54cb
ms.sourcegitcommit: 76af742a42e807c400474a337e29d088ede8a60d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2020
ms.locfileid: "85196815"
---
# <a name="add-or-remove-servers-for-an-azure-stack-hci-cluster"></a>Ajouter ou supprimer des serveurs pour un cluster Azure Stack HCI

> S’applique à : Windows Server 2019

Vous pouvez facilement ajouter ou supprimer des serveurs pour un cluster dans Azure Stack HCI. N’oubliez pas que chaque nouveau serveur physique doit correspondre étroitement aux autres serveurs du cluster en termes de type de processeur, mémoire, nombre de lecteurs et type et taille des lecteurs.

Chaque fois que vous ajoutez ou supprimez un serveur, vous devez aussi effectuer une validation du cluster par la suite pour vérifier que le cluster fonctionne normalement.

## <a name="obtain-server-hardware-from-your-oem"></a>Obtenir du matériel de serveur auprès de votre fabricant OEM

La première étape consiste à acquérir du nouveau matériel HCI auprès de votre fabricant OEM d’origine. Reportez-vous toujours à la documentation fournie par le fabricant OEM pour ajouter du matériel de serveur en vue de l’utiliser dans votre cluster.

1. Placez le nouveau serveur physique dans le rack et câblez-le de manière appropriée.
1. Activez les ports du commutateur physique et ajustez les listes de contrôle d’accès (ACL) et les ID de VLAN le cas échéant.
1. Configurez l’adresse IP appropriée dans le contrôleur de gestion de la carte de base (BMC) et appliquez tous les paramètres du BIOS conformément aux instructions du fabricant OEM.
1. Appliquez la base de référence du microprogramme actuel à tous les composants à l’aide des outils fournis par votre fabricant OEM.
1. Exécutez des tests de validation OEM pour garantir l’homogénéité avec les serveurs de cluster existants.

## <a name="add-the-server-to-the-cluster"></a>Ajouter le serveur au cluster

Une fois que votre serveur s’est correctement lancé, utilisez Windows Admin Center pour joindre le serveur à votre cluster.

:::image type="content" source="media/manage-cluster/add-server.png" alt-text="Écran d’ajout d’un serveur":::

1. Dans **Windows Admin Center**, sélectionnez **Gestionnaire de cluster** à partir de la flèche déroulante supérieure.
1. Sous **Connexions de cluster**, sélectionnez le cluster.
1. Sous **Outils**, sélectionnez **Serveurs**.
1. Sous **Serveurs**, sélectionnez l’onglet **Inventaire**.
1. Sous l’onglet **Inventaire**, sélectionnez **Ajouter**.
1. Dans **Nom du serveur**, entrez le nom de domaine complet du serveur à ajouter, cliquez sur **Ajouter**, puis de nouveau sur **Ajouter** en bas.
1. Vérifiez que le serveur s’est correctement ajouté à votre cluster.

## <a name="remove-a-server-from-the-cluster"></a>Supprimer un serveur du cluster

Les étapes de suppression d’un serveur de votre cluster sont similaires à celles de l’ajout d’un serveur à un cluster.

N’oubliez pas que lorsque vous supprimez un serveur, vous supprimez également la totalité des machines virtuelles, lecteurs et charges de travail associés à ce serveur.

:::image type="content" source="media/manage-cluster/remove-server.png" alt-text="Boîte de dialogue de suppression d’un serveur":::

1. Dans **Windows Admin Center**, sélectionnez **Gestionnaire de cluster** à partir de la flèche déroulante supérieure.
1. Sous **Connexions de cluster**, sélectionnez le cluster.
1. Sous **Outils**, sélectionnez **Serveurs**.
1. Sous **Serveurs**, sélectionnez l’onglet **Inventaire**.
1. Sous l’onglet **Inventaire**, sélectionnez le serveur à supprimer, puis **Supprimer**.
1. Pour supprimer également tous les lecteurs du serveur du pool de stockage, cochez cette case.
1. Vérifiez que le serveur a correctement été supprimé du cluster.

## <a name="validate-the-cluster"></a>Valider le cluster

Chaque fois que vous ajoutez ou supprimez un serveur dans un cluster, vous devez valider ce cluster. La validation du cluster doit se dérouler sans qu’aucune erreur ne soit prise en charge par Microsoft.

:::image type="content" source="media//manage-cluster/validate-cluster.png" alt-text="Boîte de dialogue de validation d’un cluster":::

1. Dans **Windows Admin Center**, sélectionnez **Gestionnaire de cluster** à partir de la flèche déroulante supérieure.
1. Sous **Outils**, sélectionnez **Serveurs**.
1. Sous **Serveurs**, sélectionnez l’onglet **Inventaire**, sélectionnez **Plus**, puis **Valider le cluster**.
1. Vérifiez que toutes les étapes de validation se sont déroulées correctement.

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la validation d’un cluster, consultez [Valider le matériel d’un cluster de basculement](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134244(v=ws.11)).