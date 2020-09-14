---
title: Activer le chiffrement, la déduplication et la compression des volumes dans Azure Stack HCI
description: Cette rubrique explique comment utiliser le chiffrement, la déduplication et la compression de volume dans Azure Stack HCI à l’aide de Windows Admin Center.
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 09/03/2020
ms.openlocfilehash: af5eb68af12e831f310e96ba27a348e8c615d4fe
ms.sourcegitcommit: 7c01ab4b2e2250a7acd67d1c5ba27d15c1e8bce0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89449009"
---
# <a name="enable-volume-encryption-deduplication-and-compression-in-azure-stack-hci"></a>Activer le chiffrement, la déduplication et la compression des volumes dans Azure Stack HCI

> S’applique à : Azure Stack HCI, version 20H2 ; Windows Server 2019

Cette rubrique explique comment activer le chiffrement avec BitLocker sur des volumes dans Azure Stack HCI à l’aide de Windows Admin Center. Elle explique également comment activer la déduplication et la compression sur les volumes. Pour savoir comment créer des volumes, consultez [Créer des volumes](create-volumes.md).

## <a name="turn-on-bitlocker-to-protect-volumes"></a>Activer BitLocker pour protéger les volumes
Pour activer BitLocker dans Windows Admin Center :

1. Connectez-vous à un cluster Espaces de stockage direct puis, dans le volet **Outils**, sélectionnez **Volumes**.
1. Dans la page **Volumes**, sélectionnez l’onglet **Inventaire** puis, sous **Fonctionnalités facultatives**, activez le bouton bascule **Chiffrement (BitLocker)** .

    :::image type="content" source="media/volume-encryption-deduplication/bitlocker-toggle-switch.png" alt-text="Bouton bascule pour activer BitLocker":::

1. Dans la fenêtre contextuelle **Chiffrement (BitLocker)** , sélectionnez **Démarrer** puis, dans la page **Activer le chiffrement**, indiquez vos informations d’identification pour terminer le flux de travail.

>[!NOTE]
   > Si le message contextuel **Installer tout d’abord BitLocker** s’affiche, suivez les instructions pour installer la fonctionnalité sur chaque serveur du cluster, puis redémarrez vos serveurs.

## <a name="turn-on-deduplication-and-compression"></a>Activer la déduplication et la compression
La déduplication et la compression sont gérées par volume. La déduplication et la compression utilisent un modèle post-traitement, ce qui signifie que vous ne voyez aucun gain avant leur exécution. Une fois l’exécution effectuée, tous les fichiers sont affectés, même ceux déjà présents.

Pour activer la déduplication et la compression sur un volume dans Windows Admin Center :

1. Connectez-vous à un cluster Espaces de stockage direct puis, dans le volet **Outils**, sélectionnez **Volumes**.
1. Dans la page **Volumes**, sélectionnez l’onglet **Inventaire**.
1. Dans la liste des volumes, sélectionnez le nom du volume à gérer.
1. Dans la page de détails du volume, activez le bouton bascule intitulé **Déduplication et compression**.
1. Dans le volet **Activer la déduplication**, sélectionnez le mode de déduplication.

    Au lieu de vous imposer des paramètres compliqués, Windows Admin Center vous permet de faire un choix parmi divers profils prêts à l’emploi pour différentes charges de travail. Si vous avez des doutes, utilisez le paramètre par défaut.

1. Sélectionnez **Activer la déduplication**.

Regardez une courte vidéo sur l’activation de la déduplication et de la compression. La vidéo ne montre pas le chiffrement.

> [!VIDEO https://www.youtube-nocookie.com/embed/PRibTacyKko]

L’activation du chiffrement de volume a un impact minime sur les performances du volume, généralement sous les 10 %, mais l’impact varie en fonction de votre matériel et de vos charges de travail. La déduplication et la compression des données ont également un impact sur les performances. Pour plus d’informations, consultez [Identifier les charges de travail candidates à la déduplication des données](/windows-server/storage/data-deduplication/install-enable#enable-dedup-candidate-workloads).

<!---Add info on greyed out ReFS option? --->

Vous avez terminé ! Répétez cette opération si nécessaire pour protéger les données de vos volumes.

## <a name="next-steps"></a>Étapes suivantes
Pour consulter des rubriques connexes et d’autres tâches de gestion du stockage, référez-vous également à :

- [Vue d’ensemble des espaces de stockage direct](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Planifier des volumes](../concepts/plan-volumes.md)
- [Étendre les volumes](extend-volumes.md)
- [Supprimer des volumes](delete-volumes.md)