---
title: Gérer des machines virtuelles avec Windows Admin Center
description: Découvrez comment créer et gérer des machines virtuelles dans un cluster sur Azure Stack HCI à l’aide de Windows Admin Center.
author: v-dasis
ms.topic: how-to
ms.date: 11/06/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 73d705bf5b36509b3aed31afb09105f2da91862f
ms.sourcegitcommit: 08ef9545316798c9a21c2f9bc1da8c15cb648982
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360269"
---
# <a name="manage-vms-with-windows-admin-center"></a>Gérer des machines virtuelles avec Windows Admin Center

> S’applique à Azure Stack HCI, version 20H2 ; Windows Server 2019

Windows Admin Center peut être utilisé pour créer et gérer vos machines virtuelles sur Azure Stack HCI.

## <a name="create-a-new-vm"></a>Créer une machine virtuelle

Vous pouvez facilement créer une machine virtuelle à l’aide de Windows Admin Center.

:::image type="content" source="media/manage-vm/new-vm.png" alt-text="Écran Nouvelle machine virtuelle" lightbox="media/manage-vm/new-vm.png":::

1. Dans l’écran d’accueil de Windows Admin Center, sous **Toutes les connexions**, sélectionnez le serveur ou le cluster sur lequel vous voulez créer la machine virtuelle.
1. Sous **Outils**, faites défiler la liste et sélectionnez **Machines virtuelles**.
1. Sous **Machines virtuelles**, sélectionnez l’onglet **Inventaire**, puis sélectionnez **Ajouter** et **Nouveau**.
1. Sous **Nouvelle machine virtuelle**, entrez un nom pour votre machine virtuelle.
1. Sélectionnez **Génération 2 (recommandé)** .
1. Sous **Hôte**, sélectionnez le serveur sur lequel vous souhaitez que la machine virtuelle se trouve.
1. Sous **Chemin d’accès**, sélectionnez un chemin de fichier prédéfini dans la liste déroulante ou cliquez sur **Parcourir** pour choisir le dossier dans lequel enregistrer la configuration de la machine virtuelle et les fichiers de disque dur virtuel (VHD). Vous pouvez accéder à n’importe quel partage SMB disponible sur le réseau en entrant le chemin *\\server\share*.

1. Sous **Processeurs virtuels**, sélectionnez le nombre de processeurs virtuels et indiquez si vous voulez activer la virtualisation imbriquée pour la machine virtuelle.
1. Sous **Mémoire**, sélectionnez la quantité de mémoire de démarrage (4 Go minimum sont recommandés) et une plage minimale et maximale de mémoire dynamique selon ce qu’il convient d’allouer à la machine virtuelle.
1. Sous **Réseau**, sélectionnez une carte réseau dans la liste déroulante.
1. Sous **Stockage**, cliquez sur **Ajouter** et indiquez si vous voulez créer un disque dur virtuel ou en utiliser un existant. Pour utiliser un disque dur virtuel existant, cliquez sur **Parcourir** pour sélectionner le chemin du fichier concerné.  
1. Sous **Système d’exploitation**, effectuez l’une des opérations suivantes :
   - Sélectionnez **Installer un système d’exploitation ultérieurement** si vous voulez installer un système d’exploitation pour la machine virtuelle après sa création.
   - Sélectionnez **Installer un système d’exploitation à partir d’un fichier image (*.iso)** , cliquez sur **Parcourir**, puis sélectionnez le fichier image .iso applicable à utiliser.
1. Lorsque vous avez terminé, cliquez sur **Créer** pour créer la machine virtuelle.
1. Pour démarrer la machine virtuelle, dans la liste **Machines virtuelles**, pointez sur la nouvelle machine virtuelle, cochez la case correspondante à gauche, puis sélectionnez **Démarrer**.
1. Sous **État**, vérifiez que l’état de la machine virtuelle est **En cours d’exécution**.

## <a name="get-a-list-of-vms"></a>Obtenir la liste des machines virtuelles

Vous pouvez facilement voir toutes les machines virtuelles sur un serveur ou dans votre cluster.

:::image type="content" source="media/manage-vm/vm-inventory.png" alt-text="Écran Machines virtuelles" lightbox="media/manage-vm/vm-inventory.png":::

1. Dans Windows Admin Center, sous **Outils**, faites défiler la liste pour sélectionner **Machines virtuelles**.
1. L’onglet **Inventaire** situé à droite liste toutes les machines virtuelles disponibles sur le serveur ou cluster actuel et propose des commandes pour gérer des machines virtuelles individuelles. Vous pouvez :
    - Voir la liste des machines virtuelles en cours d’exécution sur le serveur ou cluster actuel.
    - Voir l’état et le serveur hôte de la machine virtuelle si vous affichez les machines virtuelles d’un cluster. Voir l’utilisation des processeurs et de la mémoire du point de vue de l’hôte, notamment la sollicitation de la mémoire, la demande de mémoire et la mémoire allouée, ainsi que la durée de bon fonctionnement, l’état des pulsations et l’état de protection de la machine virtuelle (avec Azure Site Recovery).
    - Créez une machine virtuelle.
    - Supprimer, démarrer, désactiver, arrêter, suspendre, reprendre, réinitialiser ou renommer une machine virtuelle. Enregistrer la machine virtuelle, supprimer un état enregistré ou créer un point de contrôle.
    - Modifier les paramètres d’une machine virtuelle.
    - Vous connecter à une console de machine virtuelle par le biais de l’hôte Hyper-V.
    - Répliquer une machine virtuelle à l’aide d’Azure Site Recovery.
    - Pour les opérations pouvant être exécutées sur plusieurs machines virtuelles, comme Démarrer, Arrêter, Enregistrer, Suspendre, Supprimer ou Réinitialiser, vous pouvez sélectionner plusieurs machines virtuelles et exécuter l’opération une seule fois.

## <a name="view-vm-details"></a>Voir les détails de la machine virtuelle

Vous pouvez voir les informations détaillées et les graphiques de performances d’une machine virtuelle particulière à partir de sa page dédiée.

:::image type="content" source="media/manage-vm/vm-details.png" alt-text="Écran des informations détaillées sur les machines virtuelles" lightbox="media/manage-vm/vm-details.png":::

1. Sous **Outils**, faites défiler la liste et sélectionnez **Machines virtuelles**.
1. Cliquez sur l’onglet **Inventaire** situé à droite, puis sélectionnez la machine virtuelle. Dans la page qui suit, vous pouvez effectuer les tâches suivantes :

   - Voir les graphiques en courbes des données historiques et en temps réel sur les processeurs, la mémoire, le réseau, les IOPS et le débit d’E/S (les données historiques sont uniquement disponibles pour les clusters hyperconvergés).
   - Afficher, créer, appliquer, renommer et supprimer des points de contrôle.
   - Afficher les détails des fichiers de disque dur virtuel (.vhd), des cartes réseau et du serveur hôte.
   - Afficher l’état de la machine virtuelle.
   - Enregistrer la machine virtuelle, supprimer un état enregistré, exporter ou cloner la machine virtuelle.
   - Modifier les paramètres de la machine virtuelle.
   - Vous connecter à la console de la machine virtuelle à l’aide de VMConnect par le biais de l’hôte Hyper-V.
   - Répliquer la machine virtuelle à l’aide d’Azure Site Recovery.

## <a name="view-aggregate-vm-metrics"></a>Afficher les métriques de machines virtuelles agrégées

Vous pouvez afficher les métriques d’utilisation des ressources et de performances de toutes les machines virtuelles incluses dans votre cluster.

:::image type="content" source="media/manage-vm/host-metrics.png" alt-text="Écran des métriques d’hôte" lightbox="media/manage-vm/host-metrics.png":::

1. Sous **Outils**, faites défiler la liste et sélectionnez **Machines virtuelles**.
1. L’onglet **Résumé** situé à droite fournit un aperçu complet des ressources et des performances des hôtes Hyper-V d’un serveur ou cluster sélectionné, y compris les informations suivantes :
    - Nombre de machines virtuelles en cours d’exécution, arrêtées, suspendues et enregistrées
    - Alertes d’intégrité récentes ou événements du journal des événements Hyper-V pour les clusters
    - Utilisation des processeurs et de la mémoire avec répartition entre hôte et invité
    - Graphiques en courbes des données historiques et en temps réel sur les IOPS et le débit d’E/S des clusters

## <a name="change-vm-settings"></a>Modifier les paramètres de machine virtuelle

Vous pouvez modifier un large éventail de paramètres pour une machine virtuelle.

> [!NOTE]
> Certains paramètres ne sont pas modifiables quand une machine virtuelle est en cours d’exécution ; vous devez donc d’abord l’arrêter.

1. Sous **Outils**, faites défiler la liste et sélectionnez **Machines virtuelles**.
1. Cliquez sur l’onglet **Inventaire** situé à droite, sélectionnez la machine virtuelle, puis cliquez sur **Paramètres**.

1. Pour modifier les actions de démarrage/arrêt de la machine virtuelle et les paramètres généraux, sélectionnez **Général** et effectuez les étapes suivantes :
    - Pour modifier le nom de la machine virtuelle, entrez-le dans le champ **Nom**.
    - Pour modifier les actions de démarrage/arrêt de la machine virtuelle par défaut, sélectionnez les paramètres appropriés dans les zones de liste déroulante.
    - Pour modifier les intervalles de temps pour la suspension ou le démarrage d’une machine virtuelle, entrez les valeurs appropriées dans les champs indiqués.

        :::image type="content" source="media/manage-vm/vm-settings-general.png" alt-text="Écran des paramètres généraux d’une machine virtuelle" lightbox="media/manage-vm/vm-settings-general.png":::

1. Sélectionnez **Mémoire** pour changer la mémoire de démarrage d’une machine virtuelle, la plage de mémoire dynamique, le pourcentage de mémoire tampon et le poids de la mémoire.

    :::image type="content" source="media/manage-vm/vm-settings-memory.png" alt-text="Écran de modification des paramètres de mémoire de la machine virtuelle" lightbox="media/manage-vm/vm-settings-memory.png":::

1. Sélectionnez **Processeurs** pour changer le nombre de processeurs virtuels, activer la virtualisation imbriquée ou activer le multithreading simultané.

    :::image type="content" source="media/manage-vm/vm-settings-processor.png" alt-text="Écran de modification des paramètres des processeurs de la machine virtuelle" lightbox="media/manage-vm/vm-settings-processor.png":::

1. Pour modifier la taille d’un disque existant, modifiez la valeur dans **Taille (Go)** . Pour ajouter un nouveau disque virtuel, sélectionnez **Disques**, puis indiquez si vous voulez créer un disque virtuel vide ou utiliser un disque virtuel existant ou un fichier image ISO (.iso). Cliquez sur **Parcourir** et sélectionnez le chemin du disque virtuel ou du fichier image.

    :::image type="content" source="media/manage-vm/vm-settings-disk.png" alt-text="Écran de modification des paramètres des disques de la machine virtuelle" lightbox="media/manage-vm/vm-settings-disk.png":::

1. Pour ajouter, supprimer ou modifier des paramètres de carte réseau, sélectionnez **Réseaux** et effectuez les étapes suivantes :
    - Spécifiez le commutateur virtuel à utiliser et indiquez si vous voulez activer l’identification du réseau local virtuel (vous devez également spécifier l’identificateur VLAN).
    - Pour modifier des paramètres supplémentaires pour une carte réseau, cliquez sur **Avancé** pour pouvoir :
        - Sélectionner un type d’adresse MAC dynamique ou statique.
        - Activer l’usurpation des adresses MAC.
        - Activer la gestion de la bande passante et spécifier la plage max/min.

        :::image type="content" source="media/manage-vm/vm-settings-network.png" alt-text="Écran de modification des paramètres réseau des machines virtuelles" lightbox="media/manage-vm/vm-settings-network.png":::

1. Sélectionnez **Ordre de démarrage** pour ajouter des périphériques d’amorçage ou modifier la séquence de démarrage des machines virtuelles.

    :::image type="content" source="media/manage-vm/vm-settings-boot.png" alt-text="Écran de modification de l’ordre de démarrage des machines virtuelles" lightbox="media/manage-vm/vm-settings-boot.png":::

1. Sélectionnez **Points de contrôle** pour activer des points de contrôle de machine virtuelle, sélectionner un type de point de contrôle et sélectionner un emplacement de fichier de point de contrôle.

    > [!NOTE]
    > Le paramètre de point de contrôle **Production** est recommandé. Il utilise la technologie de sauvegarde du système d’exploitation invité pour créer des points de contrôle de cohérence des données. Le paramètre **Standard** utilise des instantanés de VHD pour créer des points de contrôle avec l’état de l’application et du service.

     :::image type="content" source="media/manage-vm/vm-settings-checkpoint.png" alt-text="Écran de modification des points de contrôle de machine virtuelle" lightbox="media/manage-vm/vm-settings-checkpoint.png":::

1. Sélectionnez **Règles d’affinité** pour créer une règle d’affinité pour une machine virtuelle. Pour plus d’informations sur la création de règles d’affinité, consultez [Créer des règles d’affinité entre serveur et site pour les machines virtuelles](vm-affinity.md).

    :::image type="content" source="media/manage-vm/vm-affinity.png" alt-text="Écran Règle d’affinité des machines virtuelles" lightbox="media/manage-vm/vm-affinity.png":::

1. Pour modifier les paramètres de sécurité d’une machine virtuelle, sélectionnez **Sécurité** et effectuez les étapes suivantes :
    - Sélectionnez **Activer le démarrage sécurisé** pour empêcher l’exécution de code non autorisé au moment du démarrage (recommandé). Sélectionnez également un modèle Microsoft ou open source dans la zone de liste déroulante.
    - Pour **Modèle**, sélectionnez un modèle de sécurité à utiliser.

    - Sous **Prise en charge du chiffrement**, vous pouvez :

        - Sélectionner **Activer le module de plateforme sécurisée** pour pouvoir utiliser un module de service de chiffrement matériel.

        - Activer le chiffrement de l’état et du trafic de migration de la machine virtuelle.

        > [!NOTE]
        > La prise en charge du chiffrement nécessite un protecteur de clé pour la machine virtuelle. S’il n’existe pas déjà, la sélection de l’une de ces options génère un protecteur de clé qui autorise l’exécution de la machine virtuelle sur cet hôte.

    - Sous **Stratégie de sécurité**, sélectionnez **Activer la protection** pour obtenir des options de protection supplémentaires pour la machine virtuelle.

        :::image type="content" source="media/manage-vm/vm-settings-security.png" alt-text="Modifier les paramètres de sécurité d’une machine virtuelle" lightbox="media/manage-vm/vm-settings-security.png":::

## <a name="move-a-vm-to-another-server-or-cluster"></a>Déplacer une machine virtuelle vers un autre serveur ou cluster

Vous pouvez facilement déplacer une machine virtuelle vers un autre serveur ou cluster comme suit :

1. Sous **Outils**, faites défiler la liste et sélectionnez **Machines virtuelles**.
1. Sous l’onglet **Inventaire**, sélectionnez une machine virtuelle dans la liste et sélectionnez **Gérer > Déplacer**.
1. Choisissez un serveur dans la liste et sélectionnez **Déplacer**.
1. Si vous souhaitez déplacer la machine virtuelle et son stockage, indiquez si vous souhaitez la déplacer vers un autre cluster ou vers un autre serveur du même cluster.

    :::image type="content" source="media/manage-vm/vm-more-move.png" alt-text="Écran Déplacer la machine virtuelle" lightbox="media/manage-vm/vm-more-move.png":::

1. Si vous souhaitez déplacer uniquement le stockage de la machine virtuelle, indiquez si vous souhaitez le déplacer vers le même chemin d’accès ou sélectionnez des chemins d’accès différents pour la configuration, le point de contrôle ou la pagination intelligente.

    :::image type="content" source="media/manage-vm/vm-move-storage.png" alt-text="Écran Déplacer le stockage de la machine virtuelle" lightbox="media/manage-vm/vm-move-storage.png":::

## <a name="join-a-vm-to-a-domain"></a>Joindre une machine virtuelle à un domaine

Vous pouvez facilement joindre une machine virtuelle à un domaine comme suit :

:::image type="content" source="media/manage-vm/vm-domain-join.png" alt-text="Écran Déplacer une jonction de domaine de machine virtuelle" lightbox="media/manage-vm/vm-domain-join.png":::

1. Sous **Outils**, faites défiler la liste et sélectionnez **Machines virtuelles**.
1. Sous l’onglet **Inventaire**, sélectionnez une machine virtuelle dans la liste et sélectionnez **Gérer > Jonction de domaine**.
1. Entrez le nom du domaine à joindre, ainsi que le nom d’utilisateur et le mot de passe du domaine.
1. Saisissez le nom d’utilisateur et le mot de passe de la machine virtuelle.
1. Lorsque vous avez terminé, cliquez sur **Joindre**.

## <a name="clone-a-vm"></a>Cloner une machine virtuelle

Vous pouvez facilement cloner une machine virtuelle en procédant comme suit :

1. Sous **Outils**, faites défiler la liste et sélectionnez **Machines virtuelles**.
1. Sélectionnez l’onglet **Inventaire** à droite. Choisissez une machine virtuelle dans la liste et sélectionnez **Gérer > Cloner**.
1. Spécifiez le nom et le chemin d’accès à la machine virtuelle clonée.
1. Si vous ne l’avez pas déjà fait, exécutez Sysprep sur votre machine virtuelle.

:::image type="content" source="media/manage-vm/vm-clone.png" alt-text="Écran Cloner la machine virtuelle" lightbox="media/manage-vm/vm-clone.png":::

## <a name="import-or-export-a-vm"></a>Importer ou exporter une machine virtuelle

Vous pouvez facilement importer ou exporter une machine virtuelle. La procédure suivante décrit le processus d’importation.

:::image type="content" source="media/manage-vm/vm-more-import.png" alt-text="Écran Importer une machine virtuelle" lightbox="media/manage-vm/vm-more-import.png":::

1. Sous **Outils**, faites défiler la liste et sélectionnez **Machines virtuelles**.
1. Sous l’onglet **Inventaire**, sélectionnez **Ajouter > Importer**.
1. Entrez le nom du dossier qui contient la machine virtuelle ou cliquez sur **Parcourir** pour sélectionner un dossier.
1. Sélectionnez la machine virtuelle à importer.
1. Créez un ID unique pour la machine virtuelle, si nécessaire.
1. Lorsque vous avez terminé, sélectionnez **Importer**.

Pour exporter une machine virtuelle, le processus est similaire :

1. Sous **Outils**, faites défiler la liste et sélectionnez **Machines virtuelles**.
1. Sous l’onglet **Inventaire**, sélectionnez la machine virtuelle à exporter dans la liste.
1. Sélectionnez **Gérer > Exporter**.
1. Entrez le chemin d’accès vers lequel exporter la machine virtuelle.

:::image type="content" source="media/manage-vm/vm-export.png" alt-text="Écran Exporter une machine virtuelle" lightbox="media/manage-vm/vm-export.png":::

## <a name="view-vm-event-logs"></a>Afficher les journaux des événements des machines virtuelles

Vous pouvez consulter les journaux des événements des machines virtuelles comme suit :

1. Sous **Outils**, faites défiler la liste et sélectionnez **Machines virtuelles**.
1. Sous l’onglet **Résumé** à droite, sélectionnez **Afficher tous les événements**.
1. Sélectionnez une catégorie d’événement et développez la vue.

## <a name="connect-to-a-vm-by-using-remote-desktop"></a>Se connecter à une machine virtuelle à l’aide de Bureau à distance

Au lieu d’utiliser Windows Admin Center, vous pouvez également gérer vos machines virtuelles par le biais d’un hôte Hyper-V à l’aide d’une connexion RDP (Remote Desktop Protocol).

1. Sous **Outils**, faites défiler la liste et sélectionnez **Machines virtuelles**.
1. Sous l’onglet **Inventaire**, sélectionnez Choisir une machine virtuelle dans la liste et sélectionnez l’option **Se connecter > Connecter** ou **Se connecter > Télécharger le fichier RDP**. Les deux options utilisent l’outil VMConnect pour se connecter à la machine virtuelle invitée par le biais de l’hôte Hyper-V et vous invitent à entrer vos informations d’identification (nom d’utilisateur et mot de passe administrateur) pour l’hôte Hyper-V.

    - L’option **Connecter** se connecte à la machine virtuelle à l’aide de Bureau à distance dans votre navigateur web.

    - L’option **Télécharger le fichier RDP** télécharge un fichier .rdp que vous pouvez ouvrir pour vous connecter à l’application Connexion Bureau à distance (mstsc.exe).

## <a name="protect-vms-with-azure-site-recovery"></a>Protéger des machines virtuelles avec Azure Site Recovery

Vous pouvez utiliser Windows Admin Center pour configurer Azure Site Recovery et répliquer vos machines virtuelles locales sur Azure. Ce service à valeur ajoutée est facultatif. Pour commencer, consultez [Protéger des machines virtuelles à l’aide d’Azure Site Recovery](azure-site-recovery.md).

:::image type="content" source="media/manage-vm/vm-more-azure.png" alt-text="Écran de configuration d’Azure Site Recovery" lightbox="media/manage-vm/vm-more-azure.png":::

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez également créer et gérer des machines virtuelles à l’aide de Windows PowerShell. Pour plus d’informations, consultez [Gérer des machines virtuelles sur Azure Stack HCI avec Windows PowerShell](vm-powershell.md).