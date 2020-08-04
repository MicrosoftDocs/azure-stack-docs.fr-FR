---
title: Créer un cluster Azure Stack HCI en utilisant Windows Admin Center
description: Découvrez comment créer une batterie de serveurs pour Azure Stack HCI en utilisant Windows Admin Center
author: v-dasis
ms.topic: how-to
ms.date: 07/21/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: faca39736eb1ed3410f2f3972765d4d7df6c9d5a
ms.sourcegitcommit: 0e52f460295255b799bac92b40122a22bf994e27
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/21/2020
ms.locfileid: "86867997"
---
# <a name="create-an-azure-stack-hci-cluster-using-windows-admin-center"></a>Créer un cluster Azure Stack HCI en utilisant Windows Admin Center

> S’applique à Azure Stack HCI, version v20H2

Dans cet article, découvrez comment utiliser Windows Admin Center pour créer un cluster hyperconvergé Azure Stack HCI qui utilise les espaces de stockage direct. L’Assistant Création d’un cluster dans Windows Admin Center effectue la plupart des tâches importantes pour vous. Si vous préférez vous en charger vous-même avec PowerShell, consultez [Créer un cluster Azure Stack HCI à l’aide de PowerShell](create-cluster-powershell.md).

Vous avez le choix entre deux types de cluster à créer :

- Cluster standard avec au moins deux nœuds serveur, se trouvant dans un même site.
- Cluster étendu avec au moins quatre nœuds serveur qui s’étendent sur deux sites, avec au moins deux nœuds par site.

Pour plus d’informations sur les clusters étendus, consultez [Vue d’ensemble des clusters étendus](../concepts/stretched-clusters.md).

## <a name="before-you-run-the-wizard"></a>Avant l’exécution de l’Assistant

Avant d’exécuter l’Assistant Création d’un cluster, veillez à effectuer les opérations suivantes :

- Vous avez lu la configuration matérielle requise et les autres exigences dans [Avant de déployer Azure Stack HCI](before-you-start.md).
- Vous avez installé le système d’exploitation Azure Stack HCI sur chaque serveur du cluster. Consultez [Déployer le système d’exploitation Azure Stack HCI](operating-system.md).
- Installez Windows Admin Center sur un ordinateur distant (de gestion). Consultez [Avant le déploiement de Azure Stack HCI](before-you-start.md). N’exécutez pas l’Assistant à partir d’un serveur du cluster.
- Vous avez un compte qui est membre du groupe Administrateurs local sur chaque serveur.

En outre, votre ordinateur de gestion doit être joint au même domaine Active Directory que celui dans lequel vous allez créer le cluster, ou un domaine entièrement fiable. Les serveurs que vous allez utiliser au sein d’une batterie n’ont pas besoin d’appartenir au domaine tout de suite. Ils pourront être ajoutés au domaine lors de la création du cluster.

Voici les principales étapes de l’Assistant Création d’un cluster :

1. **Get Started (Prise en main)**  : garantit que chaque serveur remplit les prérequis et les fonctionnalités nécessaires à la jonction de cluster.
1. **Networking (Mise en réseau)**  : attribue et configure des cartes réseau et crée les commutateurs virtuels pour chaque serveur.
1. **Clustering** : valide que le cluster est configuré correctement. Pour les clusters étendus, installe également les deux sites.
1. **Storage (Stockage)**  : configure les espaces de stockage direct.

Une fois que l’Assistant est terminé, vous configurez le témoin de cluster, vous vous inscrivez auprès d’Azure et vous créez des volumes (ce qui permet également de configurer la réplication entre les sites si vous créez un cluster étendu).

C’est parti !

1. Dans Windows Admin Center, sous **All connections (Toutes les connexions)** , cliquez sur **Add (Ajouter)** .
1. Dans le volet **Add resources (Ajouter des ressources)** , sous **Windows Server cluster (Cluster Windows Server)** , sélectionnez **Create new (Créer)** .
1. Sous **Choose cluster type (Choisir le type de cluster)** , sélectionnez **Azure Stack HCI**.
1. Sous **Select server locations (Sélectionner les emplacements des serveurs)** , sélectionnez l’une des options suivantes :

    - **All servers in one site (Tous les serveurs d’un site)**
    - **Servers in two sites (Serveurs dans deux sites)** (pour un cluster étendu)

1. Lorsque vous avez terminé, cliquez sur **Créer**. L’Assistant Création d’un cluster s’affiche à présent, comme indiqué ci-dessous.

    :::image type="content" source="media/cluster/create-cluster-wizard.png" alt-text="Scénario de cluster étendu actif/actif" lightbox="media/cluster/create-cluster-wizard.png":::

## <a name="step-1-get-started"></a>Étape 1 : Bien démarrer

L’étape 1 de l’Assistant vous guide tout au long de la vérification de la configuration requise, l’ajout des nœuds serveur, l’installation des composants nécessaires, puis le redémarrage de chaque serveur si nécessaire.

1. Passez en revue les prérequis indiqués dans l’Assistant pour vous assurer que chaque nœud serveur est prêt pour les clusters. Une fois que vous avez terminé, cliquez sur **Suivant**.
1. Dans la page **Add servers to the cluster (Ajouter des serveurs au cluster)** , entrez le nom d’utilisateur et le mot de passe de votre compte, puis cliquez sur **Suivant**. Ce compte doit être membre du groupe Administrateurs local sur chaque serveur.
1. Entrez le nom du premier serveur que vous souhaitez ajouter, puis cliquez sur **Ajouter**.
1. Répétez l’étape 3 pour chaque serveur qui fera partie du cluster. Une fois que vous avez terminé, cliquez sur **Suivant**.
1. Si nécessaire, dans la page **Join the servers to a domain (Joindre les serveurs à un domaine)** , spécifiez le domaine et un compte pour joindre les serveurs au domaine. Ensuite, si vous le souhaitez, renommez les serveurs avec des noms plus conviviaux, puis cliquez sur **Suivant**.
1. Cliquez sur **Installer les composants**. Une fois que vous avez terminé, cliquez sur **Suivant**.

    L’Assistant installe les composants requis suivants pour vous :

    - BitLocker
    - Data Center Bridging (pour les cartes réseau RoCEv2)
    - Clustering de basculement
    - Serveur de fichiers (pour un témoin de partage de fichiers ou des partages de fichiers d’hébergement)
    - Module FS-Data-Deduplication
    - Hyper-V
    - Module RSAT-AD-PowerShell
    - Réplica de stockage (installé uniquement pour les clusters étendus)

1. Pour **Installer les mises à jour**, si nécessaire, cliquez sur **Installer les mises à jour**. Lorsque vous avez terminé, cliquez sur **Suivant**.
1. Pour **Solution updates (Mises à jour de solution)** , si nécessaire, cliquez sur **Installer l’extension**. Lorsque vous avez terminé, cliquez sur **Suivant**.
1. Cliquez sur **Redémarrer les serveurs**, si nécessaire. Vérifiez que chaque serveur a bien démarré.

## <a name="step-2-networking"></a>Étape 2 : Réseau

L’étape 2 de l’Assistant vous guide lors de la vérification des cartes d’interface réseau (NIC), de la sélection d’une carte de gestion, de l’attribution d’adresses IP, de masques de sous-réseau et d’ID de réseau local virtuel pour chaque serveur et de la création des commutateurs virtuels.

Il est obligatoire de sélectionner au moins l’une des cartes à des fins de gestion, car l’Assistant a besoin d’au moins une carte réseau physique dédiée pour la gestion du cluster.  Une fois que la carte est désignée pour la gestion, elle est exclue du reste du workflow de l’Assistant.

Les cartes de gestion ont deux options de configuration :

- Carte physique : si la carte sélectionnée utilise l’adressage DHCP, celui-ci est utilisée.

- Cartes associées (pour la création d’un commutateur virtuel) : si les cartes sélectionnées utilisent l’adressage DHCP (pour l’une ou les deux), l’adresse IP DHCP est attribuée en tant qu’adresses IP statiques pour ces cartes.

En utilisant des cartes associées, vous disposez d’une seule connexion à plusieurs commutateurs physiques, mais vous n’utilisez qu’une seule adresse IP. L’équilibrage de charge devient disponible et la tolérance de panne est instantanée au lieu d’attendre la mise à jour des enregistrements DNS.

Commençons !

1. Sélectionnez **Suivant : Mise en réseau**.
1. Sous **Verify the network adapters (Vérifier les cartes réseau)** , attendez que les cases à cocher vertes s’affichent en regard de chaque carte, puis sélectionnez **Suivant**.

1. Pour **Select management adapters (Sélectionner les cartes de gestion)** , sélectionnez une ou deux cartes de gestion à utiliser pour chaque serveur, puis procédez comme suit pour chaque serveur :

    - Activez la case à cocher **Description**. Notez que toutes les cartes sont sélectionnées et que l’Assistant peut vous proposer une recommandation.
    - Désélectionnez les cases à cocher correspondant aux cartes que vous ne souhaitez pas utiliser pour la gestion du cluster.

     Vous pouvez utiliser des cartes 1 Gb comme cartes de gestion, mais nous vous recommandons d’utiliser des cartes 10 Gb ou plus pour transporter le trafic lié au stockage et à la charge de travail (machine virtuelle).

1. Une fois les modifications effectuées, cliquez sur **Apply and test (Appliquer et tester)** .
1. Sous **Define networks (Définir des réseaux)** , vérifiez que chaque carte réseau de chaque serveur possède une adresse IP statique, un masque de sous-réseau et un ID de réseau local virtuel uniques. Pointez sur chaque élément de table, puis entrez ou modifiez les valeurs selon vos besoins. Lorsque vous avez terminé, cliquez sur **Apply and test (Appliquer et tester)** .

    > [!NOTE]
    > Les cartes réseau qui ne prennent pas en charge la propriété avancée `VLANID` n’acceptent pas les ID de réseau local virtuel.

1. Attendez que la colonne **Status (État)** indique **Passed (Réussi)** pour chaque serveur, puis cliquez sur **Suivant**. Cette étape vérifie la connectivité réseau entre toutes les cartes ayant les mêmes sous-réseau et ID de réseau local virtuel. Les adresses IP fournies sont transférées de la carte physique vers les cartes virtuelles une fois que les commutateurs virtuels ont été créés (voir l’étape suivante). La réalisation de l’opération peut prendre plusieurs minutes en fonction du nombre de cartes configurées.

1. Sous **Commutateur virtuel**, sélectionnez l’une des options suivantes, le cas échéant. En fonction du nombre de cartes présentes, il se peut que certaines options ne s’affichent pas.

    - Create one virtual switch for both Hyper-V and storage use (Créer un commutateur virtuel unique pour Hyper-V et l’utilisation du stockage)
    - Create one virtual switch for Hyper-V use only (Créer un commutateur virtuel pour l’utilisation de Hyper-V uniquement)
    - Create two virtual switches, one for Hyper-V and one for storage use (Créer deux commutateurs virtuels, un pour Hyper-V et un pour l’utilisation du stockage)
    - Don't create a virtual switch (Ne pas créer de commutateur virtuel)

1. Modifiez le nom d’un commutateur et d’autres paramètres de configuration si nécessaire, puis cliquez sur **Apply and test (Appliquer et tester)** . La colonne **Status (État)** doit maintenant indiquer **Passed (Réussi)** pour chaque serveur après la création des commutateurs virtuels.

## <a name="step-3-clustering"></a>Étape 3 : Clustering

L’étape 3 de l’Assistant vous permet de vérifier que tout a été correctement configuré jusqu’à maintenant, attribue des sites dans le cas de déploiements de cluster étendus, puis crée le cluster.

1. Sélectionnez **Suivant : Clustering**.
1. Sous **Validate the cluster (Valider le cluster)** , sélectionnez **Validate (Valider)** . La validation peut prendre plusieurs minutes.

    Si la fenêtre contextuelle **Credential Security Service Provider (CredSSP)** s’affiche, sélectionnez **Oui** pour activer temporairement CredSSP pour que l’Assistant continue. Une fois votre cluster créé et l’Assistant terminé, vous désactiverez CredSSP pour augmenter la sécurité.

1. Passez en revue tous les états de validation, téléchargez le rapport pour obtenir des informations détaillées sur les échecs, apportez des modifications, puis cliquez sur **Valider à nouveau** si nécessaire. Répétez ces étapes si besoin jusqu’à ce que tous les contrôles de validation réussissent.
1. Sous **Create the cluster (Créer le cluster)** , entrez le nom de votre cluster.
1. Sous **Networks (Réseaux)** , sélectionnez la configuration préférée.
1. Sous **IP addresses (Adresses IP)** , sélectionnez les adresses IP dynamiques ou statiques à utiliser.
1. Lorsque vous avez terminé, cliquez sur **Create cluster (Créer le cluster)** .

1. Pour les clusters étendus, sous **Assign servers to sites (Attribuer des serveurs à des sites)** , nommez les deux sites qui seront utilisés.

1. Attribuez ensuite chaque serveur à un site. Vous allez configurer ultérieurement la réplication entre les sites. Lorsque vous avez terminé, cliquez sur **Appliquer**.

## <a name="step-4-storage"></a>Étape 4 : Stockage

L’étape 4 de l’Assistant vous guide dans la configuration des espaces de stockage direct pour votre cluster.


1. Sélectionnez **Suivant : Stockage**.
1. Sous **Verify drives (Vérifier les lecteurs)** , cliquez sur l’icône **>** en regard de chaque serveur pour vérifier que les disques fonctionnent et sont connectés, puis cliquez sur **Suivant**.
1. Sous **Clean drives (Nettoyer les lecteurs)** , cliquez sur **Clean drives (Nettoyer les lecteurs)** pour vider les lecteurs de données. Lorsque vous êtes prêt, cliquez sur **Suivant**.
1. Sous **Validate storage (Valider le stockage)** , cliquez sur **Suivant**.
1. Passez en revue les résultats de la validation. Si tout est bon, cliquez sur **Suivant**.
1. Sous **Activer les espaces de stockage direct**, cliquez sur **Suivant**.
1. Téléchargez le rapport et lisez-le. Si tout est bon, cliquez sur **Terminer**.

Félicitations, vous disposez maintenant d’un cluster.

Une fois le cluster créé, la réplication du nom du cluster sur votre domaine peut prendre du temps. Si la résolution du cluster échoue au bout d’un certain temps, dans la plupart des cas, vous pouvez remplacer le nom du cluster par le nom d’ordinateur d’un nœud serveur du cluster.

## <a name="after-you-run-the-wizard"></a>Après l’exécution de l’Assistant

Une fois que vous avez terminé d’exécuter l’Assistant, il y a encore des tâches importantes à effectuer pour disposer d’un cluster entièrement fonctionnel.

La première tâche consiste à désactiver le protocole CredSSP (Credential Security Support Provider) sur chaque serveur à des fins de sécurité. Gardez à l’esprit que CredSSP devait être activé pour l’Assistant. Pour plus d’informations, consultez [CVE-2018-0886](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2018-0886).

1. Dans Windows Admin Center, sous **All connections (Toutes les connexions)** , sélectionnez le cluster que vous venez de créer.
1. Sous **Outils**, sélectionnez **Serveurs**.
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
- Vous pouvez également créer un cluster à l’aide de PowerShell. Consultez [Créer un cluster Azure Stack HCI à l’aide de PowerShell](create-cluster-powershell.md).
