---
title: Créer un cluster Azure Stack HCI en utilisant Windows Admin Center
description: Découvrez comment créer une batterie de serveurs pour Azure Stack HCI en utilisant Windows Admin Center
author: v-dasis
ms.topic: how-to
ms.date: 12/11/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: e33096b2667ad9d620e942b66934f341982e619b
ms.sourcegitcommit: 79e8df69b139bfa21eb83aceb824b97e7f418c03
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2020
ms.locfileid: "97364215"
---
# <a name="create-an-azure-stack-hci-cluster-using-windows-admin-center"></a>Créer un cluster Azure Stack HCI en utilisant Windows Admin Center

> S’applique à Azure Stack HCI, version v20H2

Dans cet article, vous allez découvrir comment utiliser Windows Admin Center pour créer un cluster Azure Stack HCI qui utilise les espaces de stockage direct. L’Assistant Création d’un cluster dans Windows Admin Center effectue la plupart des tâches importantes pour vous. Si vous préférez vous en charger vous-même avec PowerShell, consultez [Créer un cluster Azure Stack HCI à l’aide de PowerShell](create-cluster-powershell.md). L’article PowerShell est également une bonne source d’informations pour ce qui se passe dans les coulisses de l’Assistant et à des fins de résolution des problèmes.

Vous avez le choix entre deux types de cluster à créer :

- Cluster standard avec au moins deux nœuds serveur, se trouvant dans un même site.
- Cluster étendu avec au moins quatre nœuds serveur qui s’étendent sur deux sites, avec au moins deux nœuds par site.

Pour plus d’informations sur les clusters étendus, consultez [Vue d’ensemble des clusters étendus](../concepts/stretched-clusters.md).

Si vous êtes intéressé par les tests Azure Stack HCI, mais que vous disposez d’un matériel de rechange limité ou inexistant, consultez le [Guide d’évaluation Azure Stack HCI](https://github.com/Azure/AzureStackHCI-EvalGuide/blob/main/README.md), dans lequel nous vous guidons tout au long de la rencontre avec Azure Stack HCI à l’aide de la virtualisation imbriquée, dans Azure ou sur un seul système physique local.

## <a name="before-you-run-the-wizard"></a>Avant l’exécution de l’Assistant

Avant d’exécuter l’Assistant Création d’un cluster, veillez à effectuer les opérations suivantes :

- Vous avez consulté la configuration matérielle requise et les exigences connexes dans [Configuration requise](../concepts/system-requirements.md).
- Vous avez consulté les [Exigences liées aux réseaux physiques](../concepts/physical-network-requirements.md) et les [Exigences liées aux réseaux d’hôtes](../concepts/host-network-requirements.md) pour Azure Stack HCI.
- Vous avez installé le système d’exploitation Azure Stack HCI sur chaque serveur du cluster. Consultez [Déployer le système d’exploitation Azure Stack HCI](operating-system.md).
- Vous avez un compte qui est membre du groupe Administrateurs local sur chaque serveur.
- Installer Windows Admin Center pour la gestion d’un PC ou d’un serveur. Consultez [Installer Windows Admin Center](/windows-server/manage/windows-admin-center/deploy/install).
- Pour les clusters étendus, configurez vos deux sites au préalable dans Active Directory. Mais ne vous inquiétez pas, l’Assistant peut également les configurer pour vous.

Si vous exécutez le Windows Admin Center sur un serveur (au lieu d’un ordinateur local), utilisez un compte qui est membre du groupe d’administrateurs de passerelles ou le groupe d’administrateurs local sur le serveur Windows Admin Center.

En outre, votre ordinateur de gestion Windows Admin Center doit être joint au même domaine Active Directory que celui dans lequel vous allez créer le cluster ou un domaine entièrement approuvé. Les serveurs que vous allez utiliser au sein d’une batterie n’ont pas besoin d’appartenir au domaine tout de suite. Ils pourront être ajoutés au domaine lors de la création du cluster.

Voici les principales étapes de l’Assistant Création d’un cluster :

1. **Get Started (Prise en main)**  : garantit que chaque serveur remplit les prérequis et les fonctionnalités nécessaires à la jonction de cluster.
1. **Networking (Mise en réseau)**  : attribue et configure des cartes réseau et crée les commutateurs virtuels pour chaque serveur.
1. **Clustering** : valide que le cluster est configuré correctement. Pour les clusters étendus, installe également les deux sites.
1. **Storage (Stockage)**  : configure les espaces de stockage direct.

Une fois que l’Assistant est terminé, vous configurez le témoin de cluster, vous vous inscrivez auprès d’Azure et vous créez des volumes (ce qui permet également de configurer la réplication entre les sites si vous créez un cluster étendu).

Avant de démarrer l’Assistant, vérifiez que les extensions Windows Admin Center les plus récentes sont installées, en particulier l’extension de création de cluster. Pour ce faire :

1. Ouvrez Windows Admin Center, puis cliquez sur Paramètres (icône d’engrenage) en haut à droite.
1. Sous **Paramètres**, sélectionnez **Extensions**.
1. Sélectionnez **Cluster Creation**, puis cliquez sur **Install**.
1. Sélectionnez **Cluster Manager**, puis cliquez également sur **Install** pendant que vous y êtes.

Maintenant que vous êtes prêt, commençons :

1. Dans Windows Admin Center, sous **All connections (Toutes les connexions)** , cliquez sur **Add (Ajouter)** .
1. Dans le panneau **Add or create resources** (Ajouter ou créer des ressources), sous **Server clusters** (Clusters de serveurs), sélectionnez **Create new** (Créer nouveau).
1. Sous **1. Choose cluster type** (Choisir le type de cluster), sélectionnez **Azure Stack HCI**.

    :::image type="content" source="media/cluster/create-cluster-type.png" alt-text="Assistant Création d’un cluster - Option HCI" lightbox="media/cluster/create-cluster-type.png":::

1. Sous **Select server locations (Sélectionner les emplacements des serveurs)** , sélectionnez l’une des options suivantes :

    - **All servers in one site (Tous les serveurs d’un site)**
    - **Servers in two sites (Serveurs dans deux sites)** (pour un cluster étendu)

1. Lorsque vous avez terminé, cliquez sur **Créer**. L’Assistant Création d’un cluster s’affiche à présent, comme indiqué ci-dessous.

    :::image type="content" source="media/cluster/create-cluster-wizard.png" alt-text="Assistant Création d’un cluster - Prise en main" lightbox="media/cluster/create-cluster-wizard.png":::

## <a name="step-1-get-started"></a>Étape 1 : Bien démarrer

L’étape 1 de l’Assistant vous guide tout au long de la vérification de la configuration requise, l’ajout des nœuds serveur, l’installation des composants nécessaires, puis le redémarrage de chaque serveur si nécessaire.

1. Examinez **1.1 Check the prerequisites** (Vérifier les prérequis) dans l’Assistant pour vérifier que chaque nœud de serveur est prêt pour les clusters. Une fois que vous avez terminé, cliquez sur **Suivant**.
1. Dans **1.2 Add servers** (Ajouter des serveurs), entrez le nom d’utilisateur et le mot de passe de votre compte, puis cliquez sur **Next** (Suivant). Ce compte doit être membre du groupe Administrateurs local sur chaque serveur.
1. Entrez le nom du premier serveur que vous souhaitez ajouter, puis cliquez sur **Ajouter**.
1. Répétez l’étape 3 pour chaque serveur qui fera partie du cluster. Une fois que vous avez terminé, cliquez sur **Suivant**.
1. Si nécessaire, dans **1.3 Join a domain** (Joindre un domaine), spécifiez le domaine auquel joindre les serveurs et le compte à utiliser. Vous pouvez éventuellement renommer les serveurs si vous le souhaitez. Cliquez ensuite sur **Suivant**.
1. Dans **1.4 Install features** (Installer des fonctionnalités), passez en revue les fonctionnalités et ajoutez celles dont vous avez besoin. Une fois que vous avez terminé, cliquez sur **Suivant**.

    L’Assistant installe les composants requis suivants pour vous :

    - BitLocker
    - Data Center Bridging (pour les cartes réseau RoCEv2)
    - Clustering de basculement
    - Serveur de fichiers
    - Module FS-Data-Deduplication
    - Hyper-V
    - Module RSAT-AD-PowerShell
    - Réplica de stockage (installé pour les clusters étendus)

1. Dans **1.5 Install updates** (Installer les mises à jour), cliquez si nécessaire sur **Install updates** (Installer les mises à jour) pour installer les éventuelles mises à jour du système d’exploitation. Lorsque vous avez terminé, cliquez sur **Suivant**.
1. Dans **1.6 Install hardware updates** (Installer les mises à jour matérielles), cliquez si nécessaire sur **Get updates** (Obtenir les mises à jour) pour obtenir les mises à jour matérielles mises à disposition par le fabricant.
1. Suivez les étapes propres au fabricant pour installer les mises à jour sur votre matériel. Parmi ces étapes figurent l’exécution de vérifications de la symétrie et de la conformité sur votre matériel pour garantir la réussite de la mise à jour. Vous serez peut-être amené à réexécuter certaines étapes.
1. Dans **1.7 Restart servers** (Redémarrer les serveurs), cliquez sur **Restart servers** (Redémarrer les serveurs) si nécessaire. Vérifiez que chaque serveur a bien démarré.

## <a name="step-2-networking"></a>Étape 2 : Réseau

L’étape 2 de l’Assistant vous guide tout au long de la configuration des commutateurs virtuels, des cartes réseau et des autres éléments réseau de votre cluster. Les cartes réseau RDMA (à la fois iWARP et RoCE) sont prises en charge.

Pour plus d’informations sur la mise en réseau d’hôtes RDMA et Hyper-V pour Azure Stack HCI, consultez [Exigences liées aux réseaux d’hôtes](../concepts/host-network-requirements.md).

> [!NOTE]
> Si vous voyez des erreurs durant les étapes de configuration du réseau ou du commutateur virtuel, sélectionnez à nouveau **Apply and test** (Appliquer et tester).

1. Sélectionnez **Suivant : Réseau**.
1. Dans **2.1 Check network adapters** (Vérifier les cartes réseau), attendez que les cases à cocher vertes s’affichent en regard de chaque carte, puis sélectionnez **Next** (Suivant).

1. Dans **2.2 Select management adapters** (Sélectionner des cartes de gestion), sélectionnez une ou deux cartes de gestion à utiliser pour chaque serveur. Il est obligatoire de sélectionner au moins l’une des cartes à des fins de gestion, car l’Assistant a besoin d’au moins une carte réseau physique dédiée pour la gestion du cluster.  Une fois que la carte est désignée pour la gestion, elle est exclue du reste du workflow de l’Assistant.

    :::image type="content" source="media/cluster/create-cluster-management-adapters.png" alt-text="Assistant Création d’un cluster – Sélectionner des cartes de gestion" lightbox="media/cluster/create-cluster-management-adapters.png":::

    Les cartes de gestion ont deux options de configuration :

    - **Une carte réseau physique pour la gestion**. Pour cette option, les deux affectations d’adresses IP statiques et DHCP sont prises en charge.

    - **Deux cartes réseau physique associées pour la gestion**. Quand une paire d’adaptateurs est associée, seule l’attribution d’adresses IP statiques est prise en charge. Si les cartes sélectionnées utilisent l’adressage DHCP (pour une ou les deux), les adresses IP DHCP sont converties en adresses IP statiques avant la création du commutateur virtuel.

    En utilisant des adaptateurs associés, vous disposez d’une seule connexion à plusieurs commutateurs physiques, mais vous n’utilisez qu’une seule adresse IP. L’équilibrage de charge devient disponible et la tolérance de panne est instantanée au lieu d’attendre la mise à jour des enregistrements DNS.

    À présent, pour chaque serveur, procédez comme suit :

    - Activez la case à cocher **Description**. Notez que toutes les cartes sont sélectionnées et que l’Assistant peut vous proposer une recommandation.
    - Décochez les cases correspondant aux cartes qui ne doivent pas être utilisées pour la gestion du cluster.

    > [!NOTE]
    > Vous pouvez utiliser des cartes 1 Gb comme cartes de gestion, mais nous vous recommandons d’utiliser des cartes 10 Gb ou plus pour transporter le trafic lié au stockage et à la charge de travail (machine virtuelle).

1. Une fois les modifications effectuées, cliquez sur **Apply and test (Appliquer et tester)** .
1. Sous **Define networks (Définir des réseaux)** , vérifiez que chaque carte réseau de chaque serveur possède une adresse IP statique, un masque de sous-réseau et un ID de réseau local virtuel uniques. Pointez sur chaque élément de table, puis entrez ou modifiez les valeurs selon vos besoins. Lorsque vous avez terminé, cliquez sur **Apply and test (Appliquer et tester)** .

    > [!NOTE]
    > Pour prendre en charge la configuration d’ID de VLAN pour le cluster, toutes les cartes réseau de tous les serveurs doivent prendre en charge la propriété VLANID.

1. Attendez que la colonne **Status (État)** indique **Passed (Réussi)** pour chaque serveur, puis cliquez sur **Suivant**. Cette étape vérifie la connectivité réseau entre toutes les cartes ayant les mêmes sous-réseau et ID de réseau local virtuel. Les adresses IP fournies sont transférées de la carte physique vers les cartes virtuelles une fois que les commutateurs virtuels ont été créés (voir l’étape suivante). La réalisation de l’opération peut prendre plusieurs minutes en fonction du nombre de cartes configurées.

1. Sous **2.3 Virtual switch** (Commutateur virtuel), sélectionnez l’une des options suivantes, selon le cas. Selon le nombre de cartes réseau présentes, il se peut que certaines options ne soient pas disponibles :

    - **Skip virtual switch creation** (Passer la création de commutateur virtuel) – Choisissez cette option si vous souhaitez configurer des commutateurs virtuels plus tard.
    - **Create one virtual switch for compute and storage together** (Créer un commutateur virtuel pour le calcul et le stockage) – Choisissez cette option si vous souhaitez utiliser le même commutateur virtuel pour vos machines virtuelles et les espaces de stockage direct. Il s’agit de l’option « converged » (convergé).
    - **Create one virtual switch for compute only** (Créer un commutateur virtuel pour le calcul uniquement) – Choisissez cette option si vous souhaitez utiliser un commutateur virtuel pour vos machines virtuelles uniquement.
    - **Create two virtual switches** (Créer deux commutateurs virtuels) – Choisissez cette option si vous souhaitez un commutateur virtuel dédié pour les machines virtuelles et un autre dédié pour les espaces de stockage direct.

        :::image type="content" source="media/cluster/create-cluster-virtual-switches.png" alt-text="Assistant Création d’un cluster – Commutateurs virtuels" lightbox="media/cluster/create-cluster-virtual-switches.png":::

    > [!NOTE]
    > Si vous envisagez de déployer le contrôleur de réseau pour SDN (dans **étape 5 : SDN** de l’Assistant), vous aurez besoin d’un commutateur virtuel. Par conséquent, si vous désactivez la création d’un commutateur virtuel ici et que vous n’en créez aucun en dehors de l’Assistant, ce dernier ne déploiera pas le contrôleur de réseau.

    Le tableau suivant affiche les configurations de commutateur virtuel prises en charge et activées pour différentes configurations de cartes réseau :

    | Option | 1 à 2 adapteurs | plus de 3 adapteurs | adaptateurs associés |
    | :------------- | :--------- |:--------| :---------|
    | commutateur unique (calcul + stockage) | enabled | enabled  | non pris en charge |
    | commutateur unique (calcul uniquement) | non pris en charge| enabled | enabled |
    | deux commutateurs | non pris en charge | enabled | enabled |

1. Modifiez le nom d’un commutateur et d’autres paramètres de configuration si nécessaire, puis cliquez sur **Apply and test (Appliquer et tester)** . La colonne **Status (État)** doit maintenant indiquer **Passed (Réussi)** pour chaque serveur après la création des commutateurs virtuels.

1. L’étape **2.4 RDMA** est facultative. Si vous utilisez RDMA, cochez la case **Configure RDMA (Recommended)** [Configurer RDMA (recommandé)], puis cliquez sur **Next** (Suivant).

    :::image type="content" source="media/cluster/create-cluster-rdma.png" alt-text="Assistant Création d’un cluster – Configurer RDMA" lightbox="media/cluster/create-cluster-rdma.png":::

    Pour obtenir des informations sur l’affectation de réservations de bande passante, consultez la section [Allocation de bande passante au trafic](../concepts/host-network-requirements.md#traffic-bandwidth-allocation) dans [Exigences liées aux réseaux d’hôtes](../concepts/host-network-requirements.md).

1. Sélectionnez **Advanced** (Avancé), puis cochez la case **Data Center Bridging (DCB)** .

1. Sous **Cluster heartbeat** (Pulsation du cluster), attribuez un niveau de priorité et un pourcentage de réservation de bande passante.

1. Sous **Storage** (Stockage), attribuez un niveau de priorité et un pourcentage de réservation de bande passante.

1. Quand vous avez terminé, sélectionnez **Apply changes** (Appliquer les modifications), puis cliquez sur **Next** (Suivant).

1. Dans **2.5 Define networks** (Définir des réseaux), examinez et modifiez les champs Name (Nom), IP address (Adresse IP), Subnet mask (Masque de sous-réseau), VLAN ID (ID de VLAN) et Default gateway (Passerelle par défaut) pour chaque carte listée.

    :::image type="content" source="media/cluster/create-cluster-define-networks.png" alt-text="Assistant Création d’un cluster – Définir des réseaux" lightbox="media/cluster/create-cluster-define-networks.png":::

1. Lorsque vous avez terminé, cliquez sur **Apply and test (Appliquer et tester)** . Vous devrez peut-être sélectionner à nouveau **Retry connectivity test** (Réessayer le test de connectivité) si l’état n’est pas OK pour un adaptateur.

## <a name="step-3-clustering"></a>Étape 3 : Clustering

L’étape 3 de l’Assistant vous permet de vérifier que tout a été correctement configuré jusqu’à maintenant, configure automatiquement deux sites dans le cas de déploiements de cluster étendus, puis crée réellement le cluster. Vous pouvez également configurer vos sites à l’avance dans Active Directory.

1. Sélectionnez **Suivant : Clustering**.
1. Dans **3.1 Validate the cluster** (Valider le cluster), sélectionnez **Validate** (Valider). La validation peut prendre plusieurs minutes.

    Si la fenêtre contextuelle **Credential Security Service Provider (CredSSP)** s’affiche, sélectionnez **Oui** pour activer temporairement CredSSP pour que l’Assistant continue. Une fois votre cluster créé et l’Assistant terminé, vous désactiverez CredSSP pour augmenter la sécurité. Si vous rencontrez des problèmes avec CredSSP, consultez [Résoudre les problèmes liés à CredSSP](../manage/troubleshoot-credssp.md) pour plus d’informations.

1. Passez en revue tous les états de validation, téléchargez le rapport pour obtenir des informations détaillées sur les échecs, apportez des modifications, puis cliquez sur **Valider à nouveau** si nécessaire. Vous pouvez également télécharger un rapport (**Download report**). Répétez ces étapes si besoin jusqu’à ce que tous les contrôles de validation réussissent. Quand tout est OK, cliquez sur **Next** (Suivant).
1. Dans **3.2 Create cluster** (Créer le cluster), entrez un nom pour votre cluster.

    :::image type="content" source="media/cluster/create-cluster.png" alt-text="Assistant Création d’un cluster – Créer le cluster" lightbox="media/cluster/create-cluster.png":::

1. Sous **IP addresses** (Adresses IP), sélectionnez les adresses IP statiques ou dynamiques à utiliser.
1. Sélectionnez **Avancé**. Deux options vous sont ici proposées :

    - **Register the cluster with DNS and Active Directory** (Inscrire le cluster auprès du service DNS et d’Active Directory)
    - **Add eligible storage to the cluster (recommended)** [Ajouter un stockage éligible au cluster (recommandé)]

1. Sous **Networks** (Réseaux), sélectionnez **Use all networks (recommended)** [Utiliser tous les réseaux (recommandé)] ou **Specify one or more networks not to use** (Spécifier un ou plusieurs réseaux à ne pas utiliser).

1. Lorsque vous avez terminé, cliquez sur **Create cluster (Créer le cluster)** .

1. Pour les clusters étendus, sous **3.3 Assign servers to sites** (Attribuer des serveurs à des sites), nommez les deux sites qui seront utilisés.

1. Attribuez ensuite chaque serveur à un site. Vous allez configurer ultérieurement la réplication entre les sites. Quand vous avez terminé, cliquez sur **Apply changes** (Appliquer les modifications).

## <a name="step-4-storage"></a>Étape 4 : Stockage

L’étape 4 de l’Assistant vous guide dans la configuration des espaces de stockage direct pour votre cluster.

1. Sélectionnez **Suivant : Stockage**.
1. Dans **4.1 Clean drives** (Nettoyer les lecteurs), vous pouvez éventuellement sélectionner **Erase drives** (Effacer les lecteurs) si cela a du sens dans le cas de votre déploiement.
1. Dans **4.2 Check drives** (Vérifier les lecteurs), cliquez sur l’icône **>** en regard de chaque serveur pour vérifier que les disques fonctionnent et sont connectés. Si tout est OK, cliquez sur **Next** (Suivant).
1. Dans **4.3 Validate storage** (Valider le stockage), cliquez sur **Next** (Suivant).
1. Téléchargez et examinez le rapport de validation. Si tout est bon, cliquez sur **Suivant**. Dans le cas contraire, exécutez **Validate again** (Valider à nouveau).
1. Dans **4.4 Enable Storage Spaces Direct** (Activer les espaces de stockage direct), cliquez sur **Next** (Suivant).
1. Téléchargez et examinez le rapport. Si tout est bon, cliquez sur **Terminer**. 
1. Sélectionnez **Go to connections list** (Accéder à la liste des connexions).
1. Au bout de quelques minutes, vous devriez voir votre cluster dans la liste. Sélectionnez-le pour afficher la page de vue d’ensemble du cluster.

La réplication du nom du cluster dans votre domaine peut prendre un certain temps, en particulier si des serveurs de groupe de travail ont été récemment ajoutés à Active Directory. Bien que le cluster puisse être affiché dans Windows Admin Center, il est possible qu’il ne soit pas encore disponible pour s’y connecter.

Si la résolution du cluster échoue au bout d’un certain temps, dans la plupart des cas, vous pouvez remplacer un nom de serveur plutôt que le nom du cluster.

## <a name="step-5-sdn-optional"></a>Étape 5 : SDN (facultatif)

Cette étape facultative vous guide tout au long de la configuration du composant de contrôleur du réseau de [SDN (Software Defined Networking)](../concepts/software-defined-networking.md). Une fois le contrôleur de réseau paramétré, vous pouvez configurer d’autres composants de SDN, notamment l’équilibreur de charge logiciel et la passerelle RAS en fonction de vos besoins.

> [!NOTE]
> L’Assistant ne configure pas le SLB et la passerelle RAS pour SDN. Vous pouvez utiliser des scripts SDNExpress pour configurer ces composants. Pour savoir comment procéder, consultez le [dépôt GitHub SDNExpress](https://github.com/microsoft/SDN/tree/master/SDNExpress/scripts).

> [!NOTE]
> SDN n’est pas pris en charge pour les clusters étendus.

1. Sélectionnez **Suivant : SDN**.

    :::image type="content" source="media/cluster/create-cluster-network-controller.png" alt-text="Assistant Création d’un cluster : contrôleur de réseau SDN" lightbox="media/cluster/create-cluster-network-controller.png":::

1. Dans **5.1 Define the Network Controller cluster** (Définir le cluster du contrôleur de réseau), sous **Host** (Hôte), entrez un nom pour le contrôleur de réseau. Il s’agit du nom DNS utilisé par les clients de gestion (par exemple Windows Admin Center) pour communiquer avec le contrôleur de réseau.
1. Spécifiez un chemin d’accès au fichier de disque dur virtuel Azure Stack HCI. Utilisez **Parcourir** pour le trouver plus rapidement.
1. Spécifiez le nombre de machines virtuelles à consacrer au contrôleur de réseau. Pour profiter d’une haute disponibilité, nous vous recommandons au minimum trois machines virtuelles.
1. Sous **Réseau**, entrez l’ID VLAN du réseau de gestion. Le contrôleur de réseau a besoin d’une connectivité au même réseau de gestion que les hôtes pour communiquer avec eux et les configurer.

    > [!NOTE]
    > Les machines virtuelles du contrôleur de réseau utilisent le commutateur virtuel utilisé pour la gestion de cluster s’il est disponible. À défaut, elles utilisent le commutateur virtuel « Compute » (Calcul) comme le reste des machines virtuelles du cluster. Pour plus d’informations, consultez la section [Configuration requise pour le contrôleur de réseau](../concepts/network-controller.md#network-controller-requirements) dans [Planifier le déploiement d’un contrôleur de réseau](../concepts/network-controller.md).

1. Pour l’**Adressage réseau de la machine virtuelle**, sélectionnez **DHCP** ou **Statique**.
1. Si vous avez sélectionné **DHCP**, entrez le nom des machines virtuelles du contrôleur de réseau.
1. Si vous avez sélectionné **Static** (Statique), spécifiez ce qui suit :
    1. Adresse IP.
    1. Préfixe de sous-réseau.
    1. Passerelle par défaut.
    1. Un ou plusieurs serveurs DNS. Cliquez sur **Ajouter** pour ajouter des serveurs DNS supplémentaires.
1. Sous **Informations de connexion**, entrez le nom d’utilisateur et le mot de passe utilisés pour joindre les machines virtuelles du contrôleur de réseau au domaine du cluster.
1. Entrez le mot de passe d’administrateur local pour ces machines virtuelles.
1. Sous **Avancé**, entrez le chemin d’accès aux machines virtuelles.
1. Entrez des valeurs pour le **Démarrage du pool d’adresses MAC** et la **Fin du pool d’adresses MAC**.
1. Une fois que vous avez terminé, cliquez sur **Suivant**.
1. Dans **Deploy the Network Controller** (Déployer le contrôleur de réseau), attendez que l’Assistant termine sa tâche. Restez sur cette page jusqu’à ce que toutes les tâches de progression soient terminées. Puis, cliquez sur **Terminer**.

1. Une fois les machines virtuelles du contrôleur de réseau créées, configurez les mises à jour DNS dynamiques pour le nom du cluster du contrôleur de réseau sur le serveur DNS. Pour plus d’informations sur la façon de procéder, consultez [Configuration de l’inscription DNS dynamique pour le contrôleur de réseau](/windows-server/networking/sdn/plan/installation-and-preparation-requirements-for-deploying-network-controller#step-3-configure-dynamic-dns-registration-for-network-controller).

1. Si le déploiement du contrôleur de réseau échoue, procédez comme suit avant de réessayer :

- Arrêtez et supprimez toutes les machines virtuelles de contrôleur de réseau que l’Assistant a créées.  

- Nettoyez les points de montage de disque dur virtuel que l’Assistant a créés.  

- Vérifiez que vous disposez d’au moins 50-100 Go d’espace libre sur vos hôtes Hyper-V.  

## <a name="after-you-complete-the-wizard"></a>Après avoir terminé l’Assistant

Une fois que vous avez terminé d’exécuter l’Assistant, il y a encore des tâches importantes à effectuer.

La première tâche consiste à désactiver le protocole CredSSP (Credential Security Support Provider) sur chaque serveur à des fins de sécurité. Gardez à l’esprit que CredSSP devait être activé pour l’Assistant. Si vous rencontrez des problèmes avec CredSSP, consultez [Résoudre les problèmes liés à CredSSP](../manage/troubleshoot-credssp.md) pour plus d’informations.

1. Dans Windows Admin Center, sous **All connections (Toutes les connexions)** , sélectionnez le cluster que vous venez de créer.
1. Sous **Outils**, sélectionnez **Serveurs**.
1. Dans le volet droit, sélectionnez le premier serveur du cluster.
1. Sous **Overview (Présentation)** , sélectionnez **Disable CredSSP (Désactiver CredSSP)** . La bannière rouge **CredSSP ENABLED** en haut disparaît.
1. Répétez les étapes 3 et 4 pour chaque serveur de votre cluster.

Voici d’autres tâches à effectuer :

- Configurer un témoin de cluster. Consultez [Configurer un témoin de cluster](witness.md).
- Créer vos volumes. Consultez [Créer des volumes](../manage/create-volumes.md).
- Pour les clusters étendus, créez vos volumes et configurez la réplication. Consultez [Créer des volumes de clusters étendus et configurer la réplication](../manage/create-stretched-volumes.md).

## <a name="next-steps"></a>Étapes suivantes

- Inscrire votre cluster auprès d’Azure. Consultez [Gérer une inscription Azure](../manage/manage-azure-registration.md).
- Effectuer une validation finale du cluster. Consultez [Valider un cluster Azure Stack HCI](validate.md).
- Provisionner vos machines virtuelles. Consultez [Gérer des machines virtuelles sur Azure Stack HCI avec Windows Admin Center](../manage/vm.md).
- Vous pouvez également déployer un cluster à l’aide de PowerShell. Consultez [Créer un cluster Azure Stack HCI à l’aide de PowerShell](create-cluster-powershell.md).
- Vous pouvez également déployer un contrôleur de réseau à l’aide de PowerShell. Consultez [Déployer un contrôleur de réseau à l’aide de PowerShell](network-controller-powershell.md).
