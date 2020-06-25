---
title: Protéger les machines virtuelles Azure Stack HCI à l'aide d'Azure Site Recovery
description: Utilisez le Centre d'administration Windows pour protéger vos machines virtuelles Azure Stack HCI avec Azure Site Recovery.
ms.topic: how-to
author: davannaw-msft
ms.author: dawhite
ms.date: 04/30/2020
ms.localizationpriority: low
ms.openlocfilehash: 0465666549e0ae8801c9bab0be6d8a3d6dad8891
ms.sourcegitcommit: 76af742a42e807c400474a337e29d088ede8a60d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2020
ms.locfileid: "85196458"
---
# <a name="protect-azure-stack-hci-vms-using-azure-site-recovery"></a>Protéger les machines virtuelles Azure Stack HCI à l'aide d'Azure Site Recovery

>S’applique à : Windows Server 2019

Le Centre d'administration Windows rationalise le processus de réplication des machines virtuelles sur vos serveurs ou clusters, ce qui vous permet de tirer parti d'Azure depuis votre propre centre de données. Pour automatiser la configuration, vous devez connecter le Centre d'administration Windows à Azure.

Cet article explique comment utiliser Azure Site Recovery pour configurer les paramètres de réplication et créer un plan de récupération sur le portail Azure. L'exécution de ces tâches permet au Centre d'administration Windows de lancer la réplication de vos machines virtuelles afin de les protéger. Azure Site Recovery protège également vos serveurs physiques et les nœuds de cluster.

Pour plus d'informations, consultez [Connexion de Windows Server aux services Azure hybrides](/windows-server/manage/windows-admin-center/azure/).

## <a name="how-azure-site-recovery-works-with-windows-admin-center"></a>Fonctionnement d'Azure Site Recovery avec le Centre d'administration Windows
*Azure Site Recovery* est un service Azure qui réplique les charges de travail exécutées sur les machines virtuelles afin de protéger l'infrastructure de votre entreprise en cas de sinistre. Pour plus d'informations, consultez [À propos de Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview).

Azure Site Recovery comprend un composant de *réplication* et d'un composant de *basculement*. En cas de sinistre, la partie réplication protège vos machines virtuelles en répliquant le disque dur virtuel de la machine virtuelle cible sur un compte de stockage Azure. Vous pouvez ensuite basculer les machines virtuelles et les exécuter dans Azure. Vous pouvez également effectuer un test de basculement sans impact sur vos machines virtuelles principales pour tester le processus de récupération dans Azure.

La configuration du composant de réplication suffit à lui seul à protéger vos machines virtuelles d'un sinistre. Toutefois, vous ne pouvez pas démarrer les machines virtuelles dans Azure tant que vous n'avez pas configuré la partie basculement du processus.

Vous pouvez configurer la partie basculement lorsque vous souhaitez basculer vers une machine virtuelle Azure ; ce n'est pas nécessaire lors de la configuration initiale. Si le serveur hôte tombe en panne, vous pouvez configurer le composant de basculement à ce moment-là pour accéder aux charges de travail de la machine virtuelle protégée. Nous vous recommandons toutefois de configurer les paramètres liés au basculement avant qu'un sinistre ne se produise.

## <a name="prerequisites-and-planning"></a>Conditions préalables et planification
Les conditions préalables ci-dessous doivent être réunies pour suivre les étapes décrites dans cet article.

- Les serveurs cibles qui hébergent les machines virtuelles à protéger doivent disposer d'un accès à Internet pour la réplication vers Azure.
- Connexion entre le Centre d'administration Windows et Azure. Pour plus d'informations, consultez [Configurer l'intégration d'Azure](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/azure-integration).
- Passer en revue l'outil de planification de la capacité pour évaluer les conditions d'une réplication et d'un basculement réussis. Pour plus d'informations, consultez [À propos du Planificateur de déploiement Azure Site Recovery pour la récupération d'urgence de Hyper-V sur Azure](https://docs.microsoft.com/azure/site-recovery/hyper-v-site-walkthrough-capacity).

## <a name="step-1-set-up-vm-protection-on-your-target-host"></a>Étape 1 : Configurer la protection des machines virtuelles sur votre hôte cible
Procédez comme suit une fois par serveur hôte ou cluster contenant les machines virtuelles à protéger :
1. Dans le Centre d'administration Windows, accédez à la page **Toutes les connexions**, et connectez-vous au serveur ou au cluster qui héberge les machines virtuelles à protéger.
1. Sous **Outils**, sélectionnez **Machines virtuelles**, puis l'onglet **Inventaire**.
1. Sélectionnez une machine virtuelle (pas nécessairement celle que vous souhaitez protéger).
1. Développez le sous-menu **Plus**, puis sélectionnez **Configurer la protection des machines virtuelles**.

    :::image type="content" source="media/azure-site-recovery/set-up-vm-protection.png" alt-text="Option de sous-menu Configurer la protection des machines virtuelles dans le Centre d'administration Windows.":::

1. Connectez-vous à votre compte Azure.
1. Sur la page Configurer l'hôte avec Azure Site Recovery, entrez les informations requises, puis sélectionnez **Configurer** :

   - **Abonnement :** abonnement Azure que vous souhaitez utiliser pour la réplication des machines virtuelles sur cet hôte.
   - **Groupe de ressources :** nouveau nom du groupe de ressources.
   - **Coffre Recovery Services :** nom du coffre Azure Site Recovery des machines virtuelles protégées sur cet hôte.
   - **Emplacement :** région Azure où les ressources Azure Site Recovery doivent être créées.

    :::image type="content" source="media/azure-site-recovery/set-up-host-with-asr.png" alt-text="Page Configurer l'hôte avec Azure Site Recovery dans le Centre d'administration Windows.":::

1. Attendez que la notification suivante s'affiche : **Configuration de Site Recovery terminée**.

Ce processus peut prendre jusqu'à 10 minutes. Vous pouvez suivre la progression en accédant à **Notifications** (icône représentant une cloche en haut à droite du Centre d'administration Windows).

>[!NOTE]
> Ce processus installe automatiquement l'agent Azure Site Recovery sur le serveur ou sur les nœuds cibles (si vous configurez un cluster), et crée un **groupe de ressources** avec le **compte de stockage** et le **coffre** spécifiés, à l'**emplacement** spécifié. Il inscrit également l'hôte cible auprès du service Azure Site Recovery et configure une stratégie de réplication par défaut.

## <a name="step-2-select-vms-to-protect"></a>Étape 2 : Sélectionner les machines virtuelles à protéger
Pour protéger vos machines virtuelles, procédez comme suit :
1. Dans le Centre d'administration Windows, revenez au serveur ou au cluster que vous avez configuré au cours de la tâche précédente.
1. Sous **Outils**, sélectionnez **Machines virtuelles**, puis l'onglet **Inventaire**.
1. Sélectionnez la machine virtuelle que vous souhaitez protéger, développez le sous-menu **Plus**, puis sélectionnez **Protéger la machine virtuelle**.

    :::image type="content" source="media/azure-site-recovery/protect-vm-setting.png" alt-text="Option Protéger la machine virtuelle dans le Centre d'administration Windows.":::

1. Passez en revue la capacité requise pour protéger la machine virtuelle. Pour plus d'informations, consultez [Planifier la capacité nécessaire à la récupération d'urgence des machines virtuelles Hyper-V](https://docs.microsoft.com/azure/site-recovery/site-recovery-capacity-planner).

    Si vous souhaitez utiliser un compte de stockage Premium, créez-en un sur le portail Azure. Pour plus d'informations, consultez la section **SSD Premium** de [Quels sont les types de disque disponibles dans Azure ?](https://docs.microsoft.com/azure/storage/common/storage-premium-storage) L'option **Créer nouveau** du Centre d'administration Windows crée un compte de stockage standard.

1. Entrez le nom du **compte de stockage** à utiliser pour la réplication de cette machine virtuelle, puis sélectionnez **Protéger la machine virtuelle** pour activer la réplication de celle-ci.

    :::image type="content" source="media/azure-site-recovery/protect-vm-setting-asr.png" alt-text="Définition du compte de stockage à utiliser pour permettre à Azure Site Recovery de protéger une machine virtuelle dans le Centre d'administration Windows.":::

    Azure Site Recovery lance le processus de réplication. La machine virtuelle est protégée lorsque la valeur figurant dans la colonne **Protégée** de la grille **Inventaire des machines virtuelles** est remplacée par **Oui**. Ce processus peut prendre quelques minutes.

## <a name="step-3-configure-and-run-a-test-failover-in-the-azure-portal"></a>Étape 3 : Configurer et exécuter un test de basculement sur le portail Azure
Cette étape n'est pas obligatoire avant de lancer la réplication de la machine virtuelle. La réplication suffit à elle seule à protéger la machine virtuelle. Néanmoins, nous vous recommandons de configurer les paramètres de basculement au moment de la configuration d'Azure Site Recovery.

Procédez comme suit pour préparer le basculement vers une machine virtuelle Azure :
1. Configurez un réseau Azure que la machine virtuelle basculée joindra à ce réseau virtuel. Pour plus d'informations, consultez [Configurer la récupération d'urgence des machines virtuelles Hyper-V locales dans Azure](https://docs.microsoft.com/azure/site-recovery/hyper-v-site-walkthrough-prepare-azure).

    >[!NOTE]
    > Le Centre d'administration Windows accomplit automatiquement les étapes de cette ressource. Il vous suffit de configurer le réseau Azure.

1. Exécutez un test de basculement. Pour plus d'informations, consultez [Exécuter une simulation de récupération d'urgence dans Azure](https://docs.microsoft.com/azure/site-recovery/hyper-v-site-walkthrough-test-failover).

## <a name="step-4-create-recovery-plans"></a>Étape 4 : Créer des plans de récupération
Les plans de récupération Azure Site Recovery vous permettent de basculer et de récupérer l'ensemble des machines virtuelles d'une application. Il est possible de récupérer des machines virtuelles protégées individuellement. Mais il est préférable d'ajouter les machines virtuelles d'une application à votre plan de récupération. Vous pourrez alors basculer l'ensemble de l'application via le plan de récupération. Vous pouvez également utiliser la fonctionnalité de test de basculement d'un plan de récupération pour tester la récupération de l'application.

Les plans de récupération vous permettent de regrouper des machines virtuelles, de déterminer l'ordre dans lequel elles doivent démarrer lors d'un basculement et d'automatiser les autres étapes de récupération. Après avoir protégé vos machines virtuelles, vous pouvez accéder au coffre Azure Site Recovery du portail Azure afin de créer des plans de récupération pour celles-ci. Pour plus d'informations, consultez [Créer et personnaliser des plans de récupération](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans).

## <a name="step-5-monitor-replicated-vms-in-azure"></a>Étape 5 : Superviser les machines virtuelles répliquées dans Azure
Pour vérifier l'absence de défaillances dans le processus d'inscription du serveur, accédez au **portail Azure**, puis sélectionnez **Toutes les ressources**, **Coffre Recovery Services**, **Travaux** et **Travaux Site Recovery**. Le nom du **coffre Recovery Services** est celui que vous avez spécifié à l'étape 6 de la première tâche de cet article.

Pour superviser la réplication des machines virtuelles, accédez à **Coffre Recovery Services** et à **Éléments répliqués**.

Pour afficher tous les serveurs inscrits auprès du coffre, accédez à **Coffre Recovery Services**, **Infrastructure Site Recovery** et **Hôtes Hyper-V** (dans la section Sites Hyper-V).

## <a name="known-issue"></a>Problème connu ##
Lorsque vous inscrivez Azure Site Recovery auprès d'un cluster, si un nœud ne parvient pas à installer Azure Site Recovery ou à inscrire le service Azure Site Recovery, vos machines virtuelles peuvent ne pas être protégées. Pour vérifier que tous les nœuds du cluster sont inscrits sur le portail Azure, accédez à **Coffre Recovery Services**, **Travaux** et **Travaux Site Recovery**.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d'informations sur Azure Site Recovery, consultez également :

- [Questions générales sur Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-faq)
