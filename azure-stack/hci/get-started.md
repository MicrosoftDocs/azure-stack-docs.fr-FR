---
title: Bien démarrer avec Azure Stack HCI et Windows Admin Center
description: Connectez-vous rapidement à un cluster Azure Stack HCI existant et utilisez Windows Admin Center pour superviser les performances du cluster et du stockage.
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 04/08/2020
ms.openlocfilehash: efd0922639f628bfea0f2c78755b10de0053bc1f
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "80979454"
---
# <a name="get-started-with-azure-stack-hci-and-windows-admin-center"></a>Bien démarrer avec Azure Stack HCI et Windows Admin Center

> S’applique à : Windows Server 2019

Cette rubrique indique comment installer Windows Admin Center, se connecter à un cluster Azure Stack HCI et superviser les performances du cluster et du stockage.

## <a name="install-windows-admin-center"></a>Installer Windows Admin Center

La façon la plus simple d’installer Windows Admin Center consiste à effectuer l’opération sur un PC Windows 10 local, mais vous devez pour cela être membre du groupe Administrateurs local.

1. Téléchargez [Windows Admin Center](https://www.microsoft.com/evalcenter/evaluate-windows-admin-center) à partir du Centre d’évaluation de Microsoft. Même si le Centre indique « Commencer l’évaluation », il s’agit de la version en disponibilité générale pour l’utilisation en production, incluse dans le cadre de votre licence Windows Server.
2. Exécutez le fichier WindowsAdminCenter.msi pour lancer l’installation.
3. Quand vous démarrez Windows Admin Center pour la première fois, une icône s’affiche dans la zone de notification de votre bureau. Cliquez avec le bouton droit sur cette icône et choisissez Ouvrir pour ouvrir l’outil dans votre navigateur par défaut. Quand vous êtes invité à sélectionner un certificat, veillez à sélectionner le certificat client Windows Admin Center.

## <a name="add-and-connect-to-an-azure-stack-hci-cluster"></a>Ajouter un cluster Azure Stack HCI et s’y connecter

Une fois que vous avez terminé l’installation de Windows Admin Center, vous pouvez ajouter un cluster à gérer à partir de la page de présentation principale.

1. Cliquez sur **+ Ajouter** sous **Toutes les connexions**.

    :::image type="content" source="media/get-started/addcluster.png" alt-text="Capture d’écran d’ajout de cluster":::

2. Choisissez d’ajouter un cluster Windows Server :
    
    :::image type="content" source="media/get-started/chooseconnectiontype.png" alt-text="Capture d’écran du choix d’un type de connexion":::

3. Tapez le nom du cluster à gérer, puis cliquez sur **Ajouter**. Le cluster est ajouté à votre liste de connexions dans la page de présentation.

4. Sous **Toutes les connexions**, cliquez sur le nom du cluster que vous venez d’ajouter. Windows Admin Center démarre le **gestionnaire de cluster** et ouvre directement le tableau de bord Windows Admin Center associé à ce cluster.

## <a name="monitor-cluster-performance-with-the-windows-admin-center-dashboard"></a>Superviser les performances du cluster avec le tableau de bord Windows Admin Center

Le tableau de bord Windows Admin Center fournit des alertes et des informations d’intégrité sur les serveurs, les lecteurs et les volumes ainsi que des détails sur l’utilisation du processeur, de la mémoire et du stockage. Le bas du tableau de bord affiche des informations sur les performances du cluster, telles que les IOPS et la latence par heure, jour, semaine, mois ou année.

:::image type="content" source="media/get-started/dashboard.png" alt-text="Capture d’écran du tableau de bord Windows Admin Center":::

## <a name="monitor-performance-of-individual-components"></a>Superviser les performances de composants individuels

Le menu **Outils** à gauche du tableau de bord vous permet d’accéder à n’importe quel composant du cluster pour afficher les résumés et les inventaires des machines virtuelles, des serveurs, des volumes et des lecteurs.

### <a name="virtual-machines"></a>Machines virtuelles

Pour voir une synthèse des machines virtuelles en cours d’exécution sur le cluster, cliquez sur **Machines virtuelles** dans le menu **Outils** à gauche.

:::image type="content" source="media/get-started/vms-summary.png" alt-text="Synthèse des machines virtuelles":::

Pour un inventaire complet des machines virtuelles en cours d’exécution sur le cluster comprenant leur état, serveur hôte, utilisation du processeur, sollicitation de la mémoire, demande de mémoire, mémoire affectée et temps d’activité, cliquez sur **Inventaire** en haut de la page.

:::image type="content" source="media/get-started/vms-inventory.png" alt-text="Inventaire des machines virtuelles":::

### <a name="servers"></a>Serveurs

Pour voir une synthèse des serveurs dans le cluster, cliquez sur **Serveurs** dans le menu **Outils** à gauche.

:::image type="content" source="media/get-started/servers-summary.png" alt-text="Synthèse des serveurs":::

Pour un inventaire complet des serveurs dans le cluster, notamment leur état, le temps d’activité, le fabricant, le modèle et le numéro de série, cliquez sur **Inventaire** en haut de la page.

:::image type="content" source="media/get-started/servers-inventory.png" alt-text="Inventaire des serveurs":::

### <a name="volumes"></a>Volumes

Pour voir une synthèse des volumes sur le cluster, cliquez sur **Volumes** dans le menu **Outils** à gauche.

:::image type="content" source="media/get-started/volumes-summary.png" alt-text="Synthèse des volumes":::

Pour un inventaire complet des volumes sur le cluster, notamment leur état, système de fichiers, résilience, taille, utilisation du stockage et IOPS, cliquez sur **Inventaire** en haut de la page.

:::image type="content" source="media/get-started/volumes-inventory.png" alt-text="Inventaire des volumes":::

### <a name="drives"></a>Lecteurs

Pour voir une synthèse des lecteurs dans le cluster, cliquez sur **Lecteurs** dans le menu **Outils** à gauche.

:::image type="content" source="media/get-started/drives-summary.png" alt-text="Synthèse des lecteurs":::

Pour un inventaire complet des lecteurs du cluster, comprenant leur numéro de série, état, modèle, taille, type, utilisation, emplacement, serveur et capacité, cliquez sur **Inventaire** en haut de la page.

:::image type="content" source="media/get-started/drives-inventory.png" alt-text="Inventaire des lecteurs":::

### <a name="virtual-switches"></a>Commutateurs virtuels

Pour voir les paramètres d’un commutateur virtuel dans le cluster, cliquez sur **Commutateurs virtuels** dans le menu **Outils** à gauche, puis cliquez sur le nom du commutateur virtuel concerné. Windows Admin Center affiche les cartes réseau associées au commutateur virtuel, notamment leurs adresses IP, état de la connexion, vitesse de liaison et adresse MAC.

:::image type="content" source="media/get-started/virtual-switch-settings.png" alt-text="Paramètres du commutateur virtuel":::

## <a name="add-counters-with-the-performance-monitor-tool"></a>Ajouter des compteurs avec l’outil Analyseur de performances

Utilisez l’outil Analyseur de performances afin d’afficher et comparer des compteurs de performances pour Windows, des applications ou des appareils en temps réel.

1. Sélectionnez **Analyseur de performances** dans le menu **Outils** à gauche.
2. Cliquez sur **Espace de travail vide** pour démarrer un nouvel espace de travail, ou sur **Restaurer précédent** pour restaurer un espace de travail précédent.
    :::image type="content" source="media/get-started/performance-monitor.png" alt-text="Capture d’écran de l’Analyseur de performances":::
3. Si vous créez un espace de travail, cliquez sur le bouton **Ajouter un compteur** et sélectionnez un ou plusieurs serveurs sources à superviser, ou sélectionnez l’ensemble du cluster.
4. Sélectionnez l’objet et l’instance que vous souhaitez superviser ainsi que le type de compteur et de graphique à utiliser pour l’affichage des informations de performances dynamiques.
    :::image type="content" source="media/get-started/example-counter.png" alt-text="Capture d’écran de l’exemple de compteur":::
5. Enregistrez l’espace de travail en choisissant **Enregistrer > Enregistrer sous** dans le menu supérieur.

## <a name="use-azure-monitor-for-monitoring-and-alerts"></a>Utiliser Azure Monitor pour la supervision et les alertes

Vous pouvez également utiliser [Azure Monitor](/windows-server/manage/windows-admin-center/azure/azure-monitor) (nécessite un abonnement Azure) pour collecter des événements et des compteurs de performances à des fins d’analyse et de création de rapports, agir quand une condition particulière est détectée et recevoir des notifications par e-mail. Cliquez sur **Azure Monitor** dans le menu **Outils** pour vous connecter directement à Azure à partir de Windows Admin Center.

## <a name="collect-diagnostics-information"></a>Collecter les informations de diagnostic

Sélectionnez **Diagnostics** dans le menu **Outils** pour collecter les informations sur la résolution des problèmes liés à votre cluster. Si vous appelez le Support Microsoft, ces informations peuvent vous être demandées.

## <a name="next-steps"></a>Étapes suivantes

Pour approfondir vos connaissances dans l’analyse des performances, consultez également :

- [Historique des performances pour les espaces de stockage direct](/windows-server/storage/storage-spaces/performance-history)
- [Superviser Azure Stack HCI avec Azure Monitor](manage/azure-monitor.md)
