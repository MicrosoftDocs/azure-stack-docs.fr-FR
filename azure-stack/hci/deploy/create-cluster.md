---
title: Créer un cluster Azure Stack HCI en utilisant Windows Admin Center
description: Découvrez comment créer une batterie de serveurs pour Azure Stack HCI en utilisant Windows Admin Center
author: v-dasis
ms.topic: how-to
ms.date: 10/17/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 927a57097eff9890dc8c546be9914e70dad5ec3c
ms.sourcegitcommit: e4e2cc6a68f02c3e856f58ca5ee51b3313c7ff8f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92179540"
---
# <a name="create-an-azure-stack-hci-cluster-using-windows-admin-center"></a>Créer un cluster Azure Stack HCI en utilisant Windows Admin Center

> S’applique à Azure Stack HCI, version v20H2

Dans cet article, découvrez comment utiliser Windows Admin Center pour créer un cluster hyperconvergé Azure Stack HCI qui utilise les espaces de stockage direct. L’Assistant Création d’un cluster dans Windows Admin Center effectue la plupart des tâches importantes pour vous. Si vous préférez vous en charger vous-même avec PowerShell, consultez [Créer un cluster Azure Stack HCI à l’aide de PowerShell](create-cluster-powershell.md). L’article PowerShell est également une bonne source d’informations pour ce qui se passe dans les coulisses de l’Assistant et à des fins de résolution des problèmes.

Vous avez le choix entre deux types de cluster à créer :

- Cluster standard avec au moins deux nœuds serveur, se trouvant dans un même site.
- Cluster étendu avec au moins quatre nœuds serveur qui s’étendent sur deux sites, avec au moins deux nœuds par site.

Pour plus d’informations sur les clusters étendus, consultez [Vue d’ensemble des clusters étendus](../concepts/stretched-clusters.md).

Si vous êtes intéressé par les tests Azure Stack HCI, mais que vous disposez d’un matériel de rechange limité ou inexistant, consultez le [Guide d’évaluation Azure Stack HCI](https://github.com/Azure/AzureStackHCI-EvalGuide/blob/main/README.md), dans lequel nous vous guidons tout au long de la rencontre avec Azure Stack HCI à l’aide de la virtualisation imbriquée, dans Azure ou sur un seul système physique local.

## <a name="before-you-run-the-wizard"></a>Avant l’exécution de l’Assistant

Avant d’exécuter l’Assistant Création d’un cluster, veillez à effectuer les opérations suivantes :

- Vous avez lu la configuration matérielle requise et les autres exigences dans [Avant de déployer Azure Stack HCI](before-you-start.md).
- Vous avez installé le système d’exploitation Azure Stack HCI sur chaque serveur du cluster. Consultez [Déployer le système d’exploitation Azure Stack HCI](operating-system.md).
- Vous avez un compte qui est membre du groupe Administrateurs local sur chaque serveur.
- Installer Windows Admin Center pour la gestion d’un PC ou d’un serveur. Consultez [Installer Windows Admin Center](/windows-server/manage/windows-admin-center/deploy/install).
- Pour les clusters étendus, configurez vos deux sites au préalable dans Active Directory. Mais ne vous inquiétez pas, l’Assistant peut également les configurer pour vous.

Si vous exécutez le Windows Admin Center sur un serveur (au lieu d’un ordinateur local), utilisez un compte qui est membre du groupe d’administrateurs de passerelles ou le groupe d’administrateurs local sur le serveur Windows Admin Center.

En outre, votre ordinateur de gestion Windows Admin Center doit être joint au même domaine Active Directory que celui dans lequel vous allez créer le cluster ou un domaine entièrement approuvé. Les serveurs que vous allez utiliser au sein d’une batterie n’ont pas besoin d’appartenir au domaine tout de suite. Ils pourront être ajoutés au domaine lors de la création du cluster.

Voici les principales étapes de l’Assistant Création d’un cluster :

1. **Get Started (Prise en main)**  : garantit que chaque serveur remplit les prérequis et les fonctionnalités nécessaires à la jonction de cluster.
1. **Networking (Mise en réseau)**  : attribue et configure des cartes réseau et crée les commutateurs virtuels pour chaque serveur.
1. **Clustering**  : valide que le cluster est configuré correctement. Pour les clusters étendus, installe également les deux sites.
1. **Storage (Stockage)**  : configure les espaces de stockage direct.

Une fois que l’Assistant est terminé, vous configurez le témoin de cluster, vous vous inscrivez auprès d’Azure et vous créez des volumes (ce qui permet également de configurer la réplication entre les sites si vous créez un cluster étendu).

Commençons !

1. Dans Windows Admin Center, sous **All connections (Toutes les connexions)** , cliquez sur **Add (Ajouter)** .
1. Dans le volet **Add resources (Ajouter des ressources)** , sous **Windows Server cluster (Cluster Windows Server)** , sélectionnez **Create new (Créer)** .
1. Sous **Choose cluster type (Choisir le type de cluster)** , sélectionnez **Azure Stack HCI** .

    :::image type="content" source="media/cluster/create-cluster-type.png" alt-text="Assistant Création d’un cluster - Option HCI" lightbox="media/cluster/create-cluster-type.png":::

1. Sous **Select server locations (Sélectionner les emplacements des serveurs)** , sélectionnez l’une des options suivantes :

    - **All servers in one site (Tous les serveurs d’un site)**
    - **Servers in two sites (Serveurs dans deux sites)** (pour un cluster étendu)

1. Lorsque vous avez terminé, cliquez sur **Créer** . L’Assistant Création d’un cluster s’affiche à présent, comme indiqué ci-dessous.

    :::image type="content" source="media/cluster/create-cluster-wizard.png" alt-text="Assistant Création d’un cluster - Option HCI" lightbox="media/cluster/create-cluster-wizard.png":::

## <a name="step-1-get-started"></a>Étape 1 : Bien démarrer

L’étape 1 de l’Assistant vous guide tout au long de la vérification de la configuration requise, l’ajout des nœuds serveur, l’installation des composants nécessaires, puis le redémarrage de chaque serveur si nécessaire.

1. Passez en revue les prérequis indiqués dans l’Assistant pour vous assurer que chaque nœud serveur est prêt pour les clusters. Une fois que vous avez terminé, cliquez sur **Suivant** .
1. Dans la page **Add servers to the cluster (Ajouter des serveurs au cluster)** , entrez le nom d’utilisateur et le mot de passe de votre compte, puis cliquez sur **Suivant** . Ce compte doit être membre du groupe Administrateurs local sur chaque serveur.
1. Entrez le nom du premier serveur que vous souhaitez ajouter, puis cliquez sur **Ajouter** .
1. Répétez l’étape 3 pour chaque serveur qui fera partie du cluster. Une fois que vous avez terminé, cliquez sur **Suivant** .
1. Si nécessaire, dans la page **Join the servers to a domain (Joindre les serveurs à un domaine)** , spécifiez le domaine et un compte pour joindre les serveurs au domaine. Ensuite, si vous le souhaitez, renommez les serveurs avec des noms plus conviviaux, puis cliquez sur **Suivant** .
1. Cliquez sur **Installer les composants** . Une fois que vous avez terminé, cliquez sur **Suivant** .

    L’Assistant installe les composants requis suivants pour vous :

    - BitLocker
    - Data Center Bridging (pour les cartes réseau RoCEv2)
    - Clustering de basculement
    - Serveur de fichiers
    - Module FS-Data-Deduplication
    - Hyper-V
    - Module RSAT-AD-PowerShell
    - Réplica de stockage (installé uniquement pour les clusters étendus)

1. Pour **Installer les mises à jour** , si nécessaire, cliquez sur **Installer les mises à jour** . Lorsque vous avez terminé, cliquez sur **Suivant** .
1. Pour **Solution updates (Mises à jour de solution)** , si nécessaire, cliquez sur **Installer l’extension** . Lorsque vous avez terminé, cliquez sur **Suivant** .
1. Cliquez sur **Redémarrer les serveurs** , si nécessaire. Vérifiez que chaque serveur a bien démarré.

## <a name="step-2-networking"></a>Étape 2 : Réseau

L’étape 2 de l’Assistant vous guide dans la configuration de commutateurs virtuels et d’autres éléments de mise en réseau pour votre cluster.

> [!NOTE]
> Si vous voyez des erreurs figurant dans les étapes de mise en réseau ou de commutateur virtuel, essayez de cliquer sur **Appliquer et tester** à nouveau.

1. Sélectionnez **Suivant : Mise en réseau** .
1. Sous **Verify the network adapters (Vérifier les cartes réseau)** , attendez que les cases à cocher vertes s’affichent en regard de chaque carte, puis sélectionnez **Suivant** .

1. Pour **Sélectionner les cartes de gestion** , sélectionnez une ou deux cartes de gestion à utiliser pour chaque serveur. Il est obligatoire de sélectionner au moins l’une des cartes à des fins de gestion, car l’Assistant a besoin d’au moins une carte réseau physique dédiée pour la gestion du cluster.  Une fois que la carte est désignée pour la gestion, elle est exclue du reste du workflow de l’Assistant.

    Les cartes de gestion ont deux options de configuration :

    - **Une carte réseau physique pour la gestion** . Pour cette option, les deux affectations d’adresses IP statiques et DHCP sont prises en charge.

    - **Deux cartes réseau physique associées pour la gestion** . Quand une paire d’adaptateurs est associée, seule l’attribution d’adresses IP statiques est prise en charge. Si les adaptateurs sélectionnés utilisent l’adressage DHCP (pour l’une ou les deux), l’adresse IP DHCP est converti en adresses IP statiques avant la création du commutateur virtuel.

    En utilisant des adaptateurs associés, vous disposez d’une seule connexion à plusieurs commutateurs physiques, mais vous n’utilisez qu’une seule adresse IP. L’équilibrage de charge devient disponible et la tolérance de panne est instantanée au lieu d’attendre la mise à jour des enregistrements DNS.

    À présent, pour chaque serveur, procédez comme suit :

    - Activez la case à cocher **Description** . Notez que toutes les cartes sont sélectionnées et que l’Assistant peut vous proposer une recommandation.
    - Désélectionnez les cases à cocher correspondant aux cartes que vous ne souhaitez pas utiliser pour la gestion du cluster.

    > [!NOTE]
    > Vous pouvez utiliser des cartes 1 Gb comme cartes de gestion, mais nous vous recommandons d’utiliser des cartes 10 Gb ou plus pour transporter le trafic lié au stockage et à la charge de travail (machine virtuelle).

1. Une fois les modifications effectuées, cliquez sur **Apply and test (Appliquer et tester)** .
1. Sous **Define networks (Définir des réseaux)** , vérifiez que chaque carte réseau de chaque serveur possède une adresse IP statique, un masque de sous-réseau et un ID de réseau local virtuel uniques. Pointez sur chaque élément de table, puis entrez ou modifiez les valeurs selon vos besoins. Lorsque vous avez terminé, cliquez sur **Apply and test (Appliquer et tester)** .

    > [!NOTE]
    > Pour prendre en charge la configuration de l’ID VLAN pour le cluster, toutes les cartes réseau de tous les serveurs doivent prendre en charge la propriété VLANID.

1. Attendez que la colonne **Status (État)** indique **Passed (Réussi)** pour chaque serveur, puis cliquez sur **Suivant** . Cette étape vérifie la connectivité réseau entre toutes les cartes ayant les mêmes sous-réseau et ID de réseau local virtuel. Les adresses IP fournies sont transférées de la carte physique vers les cartes virtuelles une fois que les commutateurs virtuels ont été créés (voir l’étape suivante). La réalisation de l’opération peut prendre plusieurs minutes en fonction du nombre de cartes configurées.

1. Sous **Commutateur virtuel** , sélectionnez l’une des options suivantes, le cas échéant. En fonction du nombre d’adaptateurs présents, il se peut que certaines options ne s’affichent pas :

    - **Ignorer la création du commutateur virtuel**
    - **Créer un commutateur virtuel unique pour le calcul et l’utilisation du stockage**
    - **Créer un commutateur virtuel pour le calcul uniquement**
    - **Créer deux commutateurs virtuels**

    > [!NOTE]
    > Si vous envisagez de déployer le contrôleur de réseau pour SDN (dans **étape 5 : SDN** de l’Assistant), vous aurez besoin d’un commutateur virtuel. Par conséquent, si vous désactivez la création d’un commutateur virtuel ici et que vous n’en créez aucun en dehors de l’Assistant, ce dernier ne déploiera pas le contrôleur de réseau.

    Le tableau suivant affiche les configurations de commutateur virtuel prises en charge et activées pour différentes configurations de cartes réseau :

    | Option | 1 à 2 adapteurs | plus de 3 adapteurs | adaptateurs associés |
    | :------------- | :--------- |:--------| :---------|
    | commutateur unique (calcul + stockage) | enabled | enabled  | non pris en charge |
    | commutateur unique (calcul uniquement) | non pris en charge| enabled | enabled |
    | deux commutateurs | non pris en charge | enabled | enabled |

1. Modifiez le nom d’un commutateur et d’autres paramètres de configuration si nécessaire, puis cliquez sur **Apply and test (Appliquer et tester)** . La colonne **Status (État)** doit maintenant indiquer **Passed (Réussi)** pour chaque serveur après la création des commutateurs virtuels.

## <a name="step-3-clustering"></a>Étape 3 : Clustering

L’étape 3 de l’Assistant vous permet de vérifier que tout a été correctement configuré jusqu’à maintenant, configure automatiquement deux sites dans le cas de déploiements de cluster étendus, puis crée réellement le cluster. Vous pouvez également configurer vos sites à l’avance dans Active Directory.

1. Sélectionnez **Suivant : Clustering** .
1. Sous **Validate the cluster (Valider le cluster)** , sélectionnez **Validate (Valider)** . La validation peut prendre plusieurs minutes.

    Si la fenêtre contextuelle **Credential Security Service Provider (CredSSP)** s’affiche, sélectionnez **Oui** pour activer temporairement CredSSP pour que l’Assistant continue. Une fois votre cluster créé et l’Assistant terminé, vous désactiverez CredSSP pour augmenter la sécurité. Si vous rencontrez des problèmes avec CredSSP, consultez [Résoudre les problèmes liés à CredSSP](../manage/troubleshoot-credssp.md) pour plus d’informations.

1. Passez en revue tous les états de validation, téléchargez le rapport pour obtenir des informations détaillées sur les échecs, apportez des modifications, puis cliquez sur **Valider à nouveau** si nécessaire. Répétez ces étapes si besoin jusqu’à ce que tous les contrôles de validation réussissent.
1. Sous **Create the cluster (Créer le cluster)** , entrez le nom de votre cluster.
1. Sous **Networks (Réseaux)** , sélectionnez la configuration préférée.
1. Sous **IP addresses (Adresses IP)** , sélectionnez les adresses IP dynamiques ou statiques à utiliser.
1. Lorsque vous avez terminé, cliquez sur **Create cluster (Créer le cluster)** .

1. Pour les clusters étendus, sous **Assign servers to sites (Attribuer des serveurs à des sites)** , nommez les deux sites qui seront utilisés.

1. Attribuez ensuite chaque serveur à un site. Vous allez configurer ultérieurement la réplication entre les sites. Lorsque vous avez terminé, cliquez sur **Appliquer** .

## <a name="step-4-storage"></a>Étape 4 : Stockage

L’étape 4 de l’Assistant vous guide dans la configuration des espaces de stockage direct pour votre cluster.

1. Sélectionnez **Suivant : Stockage** .
1. Sous **Verify drives (Vérifier les lecteurs)** , cliquez sur l’icône **>** en regard de chaque serveur pour vérifier que les disques fonctionnent et sont connectés, puis cliquez sur **Suivant** .
1. Sous **Clean drives (Nettoyer les lecteurs)** , cliquez sur **Clean drives (Nettoyer les lecteurs)** pour vider les lecteurs de données. Lorsque vous êtes prêt, cliquez sur **Suivant** .
1. Sous **Validate storage (Valider le stockage)** , cliquez sur **Suivant** .
1. Passez en revue les résultats de la validation. Si tout est bon, cliquez sur **Suivant** .
1. Sous **Activer les espaces de stockage direct** , cliquez sur **Suivant** .
1. Téléchargez le rapport et lisez-le. Si tout est bon, cliquez sur **Terminer** .

Félicitations, vous disposez maintenant d’un cluster.

Une fois le cluster créé, la réplication du nom du cluster sur votre domaine peut prendre du temps, en particulier si des serveurs de groupe de travail ont été ajoutés à Active Directory. Bien que le cluster puisse être affiché dans Windows Admin Center, il est possible qu’il ne soit pas encore disponible pour s’y connecter.

Si la résolution du cluster échoue au bout d’un certain temps, dans la plupart des cas, vous pouvez remplacer un nom du serveur dans le cluster plutôt que le nom du cluster.

## <a name="step-5-sdn-optional"></a>Étape 5 : SDN (facultatif)

Cette étape facultative vous guide tout au long de la configuration du composant de contrôleur du réseau de [SDN (Software Defined Networking)](../concepts/software-defined-networking.md). Une fois le contrôleur de réseau configuré, il peut être utilisé pour configurer d’autres composants de SDN, tels que l’équilibrage de charge Azure et la passerelle RAS.

> [!NOTE]
> SDN n’est pas pris en charge ou n’est pas disponible pour les clusters étendus.

:::image type="content" source="media/cluster/create-cluster-network-controller.png" alt-text="Assistant Création d’un cluster - Option HCI" lightbox="media/cluster/create-cluster-network-controller.png":::

1. Sélectionnez **Suivant : SDN** .
1. Sous l’ **hôte** , entrez un nom pour le contrôleur de réseau.
1. Spécifiez un chemin d’accès au fichier de disque dur virtuel Azure Stack HCI. Utilisez **Parcourir** pour le trouver plus rapidement.
1. Spécifiez le nombre de machines virtuelles à consacrer au contrôleur de réseau. Trois à cinq machines virtuelles sont recommandées pour la haute disponibilité.
1. Sous **Réseau** , entrez l’ID du VLAN.
1. Pour l’ **Adressage réseau de la machine virtuelle** , sélectionnez **DHCP** ou **Statique** .
1. Si vous avez sélectionné **DHCP** , entrez le nom et l’adresse IP des machines virtuelles du contrôleur de réseau.
1. Si vous avez sélectionné **Statique** , procédez comme suit :
    1. Spécifier un préfixe de sous-réseau.
    1. Spécifiez le passerelle par défaut.
    1. Spécifiez un ou plusieurs serveurs DNS. Cliquez sur **Ajouter** pour ajouter des serveurs DNS supplémentaires.
1. Sous **Informations de connexion** , entrez le nom d’utilisateur et le mot de passe utilisés pour joindre les machines virtuelles du contrôleur de réseau au domaine du cluster.
1. Entrez le mot de passe d’administrateur local pour ces machines virtuelles.
1. Sous **Avancé** , entrez le chemin d’accès aux machines virtuelles.
1. Entrez des valeurs pour le **Démarrage du pool d’adresses MAC** et la **Fin du pool d’adresses MAC** .
1. Une fois que vous avez terminé, cliquez sur **Suivant** .
1. Attendez que l’Assistant termine son travail. Restez sur cette page jusqu’à ce que toutes les tâches de progression soient terminées. Puis, cliquez sur **Terminer** .

Si le déploiement du contrôleur de réseau échoue, procédez comme suit avant de réessayer :

- Arrêtez et supprimez toutes les machines virtuelles de contrôleur de réseau que l’Assistant a créées.  

- Nettoyez les points de montage de disque dur virtuel que l’Assistant a créés.  

- Vérifiez que vous disposez d’au moins 50-100 Go d’espace libre sur vos ordinateurs hôtes Hyper-V.  

## <a name="after-you-complete-the-wizard"></a>Après avoir terminé l’Assistant

Une fois que vous avez terminé d’exécuter l’Assistant, il y a encore des tâches importantes à effectuer.

La première tâche consiste à désactiver le protocole CredSSP (Credential Security Support Provider) sur chaque serveur à des fins de sécurité. Gardez à l’esprit que CredSSP devait être activé pour l’Assistant. Si vous rencontrez des problèmes avec CredSSP, consultez [Résoudre les problèmes liés à CredSSP](../manage/troubleshoot-credssp.md) pour plus d’informations.

1. Dans Windows Admin Center, sous **All connections (Toutes les connexions)** , sélectionnez le cluster que vous venez de créer.
1. Sous **Outils** , sélectionnez **Serveurs** .
1. Dans le volet droit, sélectionnez le premier serveur du cluster.
1. Sous **Overview (Présentation)** , sélectionnez **Disable CredSSP (Désactiver CredSSP)** . La bannière rouge **CredSSP ENABLED** en haut disparaît.
1. Répétez les étapes 3 et 4 pour chaque serveur de votre cluster.

Voici d’autres tâches à effectuer :

- Configurer un témoin de cluster. Consultez [Configurer un témoin de cluster](witness.md).
- Créer vos volumes. Consultez [Créer des volumes](../manage/create-volumes.md).
- Pour les clusters étendus, créez des volumes et configurez la réplication en utilisant le réplica de stockage. Consultez [Créer des volumes et configurer la réplication pour des clusters étendus](../manage/create-stretched-volumes.md).

## <a name="next-steps"></a>Étapes suivantes

- Inscrire votre cluster auprès d’Azure. Consultez [Gérer une inscription Azure](../manage/manage-azure-registration.md).
- Effectuer une validation finale du cluster. Consultez [Valider un cluster Azure Stack HCI](validate.md).
- Provisionner vos machines virtuelles. Consultez [Gérer des machines virtuelles sur Azure Stack HCI avec Windows Admin Center](../manage/vm.md).
- Vous pouvez également déployer un cluster à l’aide de PowerShell. Consultez [Créer un cluster Azure Stack HCI à l’aide de PowerShell](create-cluster-powershell.md).
- Vous pouvez également déployer un contrôleur de réseau à l’aide de PowerShell. Consultez [Déployer un contrôleur de réseau à l’aide de PowerShell](network-controller-powershell.md).
