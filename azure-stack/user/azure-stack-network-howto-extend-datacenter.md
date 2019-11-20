---
title: Guide pratique pour étendre le centre de données sur Azure Stack Hub | Microsoft Docs
description: Découvrez comment étendre le centre de données sur Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/07/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 11/07/2019
ms.openlocfilehash: 92e82f549cddf51b1cbd6764cc122acc3ca8fdfc
ms.sourcegitcommit: ed44d477b9fd11573d1e0d1ed3a3c0ef4512df53
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73846017"
---
# <a name="how-to-extend-the-data-center-on--azure-stack-hub"></a>Guide pratique pour étendre le centre de données sur Azure Stack Hub

*S’applique à : Systèmes intégrés Azure Stack Hub et kit SDK de développement Azure Stack Hub*

Cet article fournit des informations sur l’infrastructure de stockage Azure Stack Hub, ce qui vous aidera à déterminer comment intégrer Azure Stack dans votre environnement réseau existant. L’article offre un point de vue général sur l’extension de votre centre de données, puis présente deux scénarios différents. Vous pouvez vous connecter à un serveur de stockage de fichiers Windows. Vous pouvez également vous connecter à un serveur ISCSI Windows.

## <a name="overview-of-extending-storage-to-azure-stack-hub"></a>Présentation de l’extension du stockage sur Azure Stack Hub

Il existe des scénarios dans lesquels le fait de placer vos données dans le cloud public ne suffit pas. Imaginons que vous ayez une charge de travail de base de données virtualisée qui nécessite beaucoup de ressources système et qui soit sensible aux latences. Supposons que le temps d’aller-retour vers le cloud public affecte les performances de la charge de travail de base de données. Peut-être que des données locales, stockées sur un serveur de fichiers, un NAS ou une baie de stockage iSCSI, doivent être accessibles aux charges de travail locales et résider localement pour répondre aux objectifs de réglementation ou de conformité.  Il s’agit simplement de deux des scénarios dans lesquels les données sont conservées localement, ce qui est important pour de nombreuses organisations.

Alors, pourquoi ne pas simplement héberger ces données dans des comptes de stockage sur Azure Stack ou sur des serveurs de fichiers virtualisés s’exécutant sur le système Azure Stack ? Contrairement à Azure, le stockage Azure Stack est limité. La capacité dont vous disposez pour votre utilisation dépend entièrement de la capacité par nœud que vous avez choisi d’acheter, en plus du nombre de nœuds que vous avez. De plus, dans la mesure où Azure Stack est une solution hyperconvergée, si vous souhaitez augmenter votre capacité de stockage pour répondre aux besoins d’utilisation, vous devez également augmenter l’empreinte de calcul en ajoutant des nœuds.  Cela peut entraîner des coûts prohibitifs, en particulier si le besoin de capacité supplémentaire concerne le stockage d’archivage froid, qui peut être ajouté à faible coût en dehors du système Azure Stack.

Ce qui vous amène au scénario que vous allez aborder ci-dessous. Comment connecter des systèmes Azure Stack, des charges de travail virtualisées s’exécutant sur Azure Stack, de manière simple et efficace, à des systèmes de stockage en dehors d’Azure Stack, accessibles via le réseau.

## <a name="design-for-extending-storage"></a>Conception pour étendre le stockage

Le schéma illustre un scénario dans lequel une machine virtuelle unique, qui exécute une charge de travail, se connecte à, et utilise le stockage externe (vers la machine virtuelle et l’instance Azure Stack elle-même), à des fins de lecture/écriture de données, etc. Pour cet article, vous allez vous concentrer sur une simple récupération de fichiers, mais vous pouvez développer cet exemple pour des scénarios plus complexes, tels que le stockage étendu des fichiers de base de données.

![Étendre le stockage Azure Stack](./media/azure-stack-network-howto-extend-datacenter/image1.png)

Dans le schéma, vous voyez que la machine virtuelle sur le système Azure Stack a été déployée avec plusieurs cartes réseau. En termes de redondance, mais également en tant que meilleure pratique de stockage, il est important de disposer de plusieurs chemins entre la cible et la destination. Lorsque les choses deviennent plus complexes, les machines virtuelles Azure Stack doivent avoir des adresses IP publiques et privées, comme dans Azure. Si le stockage externe doit atteindre la machine virtuelle, il peut le faire par le biais de l’adresse IP publique, car les adresses IP privées sont principalement utilisées dans les systèmes Azure Stack, au sein de réseaux virtuels et de sous-réseaux. Le stockage externe n’est pas en mesure de communiquer avec l’espace d’adresse IP privée de la machine virtuelle, sauf s’il traverse un réseau VPN de site à site pour se connecter au réseau virtuel lui-même. Ainsi, pour cet exemple, vous allez vous concentrer sur la communication via l’espace IP public. Notez que dans l’espace d’adresses IP publiques du graphique, il existe deux sous-réseaux de pools d’adresses IP publiques distincts. Par défaut, Azure Stack nécessite un seul pool pour les adresses IP publiques, mais il peut être nécessaire d’en ajouter un autre pour proposer un routage redondant. Toutefois, il n’est pas possible de sélectionner une adresse IP à partir d’un pool spécifique. Par conséquent, vous pouvez vous retrouver avec des machines virtuelles avec des adresses IP publiques du même pool sur plusieurs cartes réseau virtuelles.

Dans cet exemple, vous pouvez supposer que le routage entre les appareils frontière et le stockage externe est pris en charge, et que le trafic peut traverser le réseau de manière appropriée. Pour cet exemple, peu importe si le segment principal est 1 GbE, 10 GbE, 25 GbE ou encore plus rapide. Toutefois, il est important de prendre en compte la planification de votre intégration, pour répondre aux besoins de performances de toutes les applications qui accèdent à ce stockage externe.

## <a name="connect-to-a-windows-server-file-server-storage"></a>Se connecter à un stockage de serveur de fichiers Windows Server

Dans ce scénario, vous pouvez déployer et configurer une machine virtuelle Windows Server 2019 sur Azure Stack et la préparer pour la connexion à un serveur de fichiers externe, qui peut également exécuter Windows Server 2019. Le cas échéant, définissez des fonctionnalités clés telles que SMB Multichannel, afin d’optimiser les performances et la connectivité entre la machine virtuelle et le stockage externe.

### <a name="deploy-the-windows-server-2019-vm-on-azure-stack"></a>Déployer la machine virtuelle Windows Server 2019 sur Azure Stack

1.  À partir de votre **portail administrateur Azure Stack**, en supposant que ce système a été inscrit correctement et qu’il est connecté à la place de marché, sélectionnez **Gestion de la place de marché**, puis, en supposant que vous n’avez pas encore d’image Windows Server 2019, sélectionnez **Ajouter à partir d’Azure**, puis recherchez **Windows Server 2019**, en ajoutant l’image **Windows Server 2019 Datacenter**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image2.png)

    Le téléchargement d’une image Windows Server 2019 peut prendre un certain temps.

2.  Une fois que vous disposez d’une image Windows Server 2019 dans votre environnement Azure Stack, connectez-vous au portail utilisateur Azure Stack**.

3.  Une fois que vous êtes connecté au portail utilisateur Azure Stack, assurez-vous de disposer d’un [abonnement à une offre](https://docs.microsoft.com/azure-stack/operator/azure-stack-subscribe-plan-provision-vm?view=azs-1908), ce qui vous permet de provisionner des ressources IaaS (calcul, stockage et réseau).

4.  Une fois que vous disposez d’un abonnement, revenez dans le **tableau de bord** du portail utilisateur Azure Stack, sélectionnez **Créer une ressource**, **Calcul**, puis l’**élément de galerie Windows Server 2019 Datacenter**.

5.  Sur le panneau **De base** :

    a.  **Nom** : VM001

    b.  **Nom d’utilisateur** : localadmin

    c.  **Nouveau mot de passe** et **Confirmer le mot de passe** : \<mot de passe de votre choix>

    d.  **Abonnement** : \<abonnement de votre choix, avec des ressources de calcul/stockage/réseau>

    e. **Groupe de ressources** : Créer : storagetesting

    f.  Sélectionnez ensuite **OK**.

6.  Dans le panneau **Choisir une taille**, sélectionnez **Standard_F8s_v2**.

7.  Dans le panneau **Paramètres**, sélectionnez le **réseau virtuel**, puis dans le panneau **Créer un réseau virtuel**, ajustez l’espace d’adressage pour qu’il soit défini sur **10.10.10.0/23** et mettez à jour la plage d’adresses de sous-réseau pour qu’elle soit définie sur **10.10.10.0/24**, puis sélectionnez **OK**.

8.  Sélectionnez l’**adresse IP publique**, puis dans le panneau **Créer une adresse IP publique**, sélectionnez **Statique**.

9.  Dans **Sélectionner des ports entrants publics**, sélectionnez **RDP (3389)** .

10. Conservez les autres valeurs par défaut et sélectionnez **Ok**.
    
    ![](./media/azure-stack-network-howto-extend-datacenter/image3.png)

11. Lisez le résumé, attendez la validation, puis sélectionnez **OK** pour commencer le déploiement. Le déploiement doit se terminer en environ 10 minutes.

12. Une fois le déploiement terminé, sous **Ressource** sélectionnez le nom de machine virtuelle **VM001** pour accéder au panneau *Présentation*.
    
    ![](./media/azure-stack-network-howto-extend-datacenter/image4.png)

13. Sous Nom DNS, sélectionnez **Configurer**, indiquez une étiquette de nom DNS, **vm001, sélectionnez **Enregistrer**, puis sélectionnez **VM001** dans la barre de navigation pour retourner au panneau *Présentation*.

14. Sur le côté droit du panneau Présentation, sélectionnez **storagetesting-vnet/default** sous le texte de réseau virtuel/sous-réseau.

15. Dans le panneau storagetesting-vnet, sélectionnez **Sous-réseaux** puis **+ Sous-réseau**, puis, dans le nouveau panneau Ajouter un sous-réseau, entrez les informations suivantes : 

    a.  **Nom** : subnet2

    b.  **Plage d’adresses (bloc CIDR)** : 10.10.11.0/24

    c. **Groupe de sécurité réseau** : Aucun

    d.  **Table de routage** : Aucun

    e. Sélectionnez **OK**.

16. Une fois l’enregistrement effectué, sélectionnez **VM001** dans le fil d’Ariane pour revenir au panneau Présentation.

17. Sélectionnez **Mise en réseau**.

18. Sélectionnez **Attacher une interface réseau**, puis sélectionnez **Créer une interface réseau**.

19. Dans le panneau **Créer une interface réseau** :

    a.  **Nom** : vm001nic2

    b.  **Sous-réseau** : Vérifiez que le sous-réseau est 10.10.11.0/24

    c. **Groupe de sécurité réseau** : VM001-nsg

    d.  **Groupe de ressources** : storagetesting

20. Une fois la création terminée, sélectionnez **VM001** dans le fil d’Ariane et sélectionnez **Arrêter** pour arrêter la machine virtuelle.

21. Une fois la machine virtuelle arrêtée (désallouée), sélectionnez **Réseau**, sélectionnez **Attacher l’interface réseau**, puis **vm001nic2**, puis cliquez sur **OK**. Vous allez ajouter une carte réseau supplémentaire à la machine virtuelle dans quelques instants.

22. Dans le panneau **Mise en réseau,** sélectionnez l’onglet **vm001nic2**, puis sélectionnez **Interface réseau : vm001nic2**.

23. Dans le panneau de l’interface vm001nic, sélectionnez **Configurations IP**, puis dans le centre du panneau, sélectionnez **ipconfig1**.

24. Dans le panneau Paramètres ipconfig1, sélectionnez **Activé** pour l’adresse IP publique et sélectionnez **Configurer les paramètres requis**, **Créer**, puis entrez vm001nic2pip comme nom, sélectionnez **Statique** et **OK**, puis **Enregistrer**.

25. Une fois l’enregistrement réussi, revenez au panneau Présentation de VM001, puis sélectionnez **Démarrer** pour démarrer la machine virtuelle Windows Server 2019 configurée.

### <a name="configure-the-windows-server-2019-file-server-storage"></a>Configurer le stockage du serveur de fichiers Windows Server 2019

Pour ce premier exemple, vous validez une configuration dans laquelle le serveur de fichiers Windows Server 2019 est une machine virtuelle s’exécutant sur Hyper-V. Cette machine virtuelle est configurée avec 8 processeurs virtuels, un fichier VHDX unique et, plus important encore, deux cartes réseau virtuelles. Dans un scénario idéal, ces cartes réseau ont des sous-réseaux routables différents, mais dans ce test, ils ont des cartes réseau situées sur le même sous-réseau.

![](./media/azure-stack-network-howto-extend-datacenter/image5.png)

Pour votre serveur de fichiers, il peut s’agir d’une machine Windows Server 2016 ou 2019, physique ou virtuel, s’exécutant sur Hyper-V, VMware ou sur la plateforme de votre choix. Le sujet clé ici, est la connectivité à l’intérieur et à l’extérieur du système Azure Stack. Il est toutefois préférable de disposer de plusieurs chemins entre la source et la destination, car cela fournit une redondance supplémentaire et permet d’utiliser des fonctionnalités plus avancées pour augmenter les performances, telles que SMB Multichannel.

Mettez à jour votre serveur de fichiers avec les dernières mises à jour cumulatives et les derniers correctifs, en effectuant un redémarrage avant de procéder à la configuration des partages de fichiers.

Une fois que vous avez effectué la emis à jour et le redémarrage, vous pouvez maintenant configurer ce serveur en tant que serveur de fichiers.

1) Sur votre serveur de fichiers, exécutez **ipconfig/all** à partir de **CMD** et notez le **serveur DNS** utilisé par votre serveur de fichiers.

2)  Ouvrez **Gestionnaire de serveur** et sélectionnez **Gérer**, puis **Ajouter des rôles et des fonctionnalités**.

3)  Sélectionnez **Suivant**, sélectionnez **Installation basée sur un rôle ou une fonctionnalité**, puis passez en revue les sélections jusqu’à ce que vous atteigniez la page **Sélectionner des rôles de serveurs**.

4)  Développez **Services de fichiers et de stockage**, **Services de fichiers et iSCSI**, puis sélectionnez **Serveur de fichiers**. Quand vous avez terminé, fermez le **Gestionnaire de serveur**.

5)  Rouvrez le **Gestionnaire de serveur**, puis sélectionnez **Services de fichiers et de stockage**.

6)  Sélectionnez **Partages**.

7)  Dans la **zone Partages** , sélectionnez le lien **Pour créer un partage de fichiers, démarrez l’Assistant Nouveau partage**.

8)  Dans la zone **Assistant Nouveau partage**, sélectionnez **Partage SMB – Rapide**, puis sélectionnez **Suivant** et passez en revue l’Assistant, en sélectionnant le volume **C:\\\\** .

9)  Donnez au partage le nom **TestStorage**, puis sélectionnez **Suivant**.

10) De retour dans l’Assistant **Nouveau partage**, sélectionnez **Suivant**, puis **Créer** et **Fermer**.

Vous avez maintenant créé votre partage de fichiers sur votre serveur de fichiers.

### <a name="testing-file-storage-performance-and-connectivity"></a>Test des performances et de la connectivité du stockage de fichiers

Pour vérifier la communication et effectuer des tests rudimentaires, connectez-vous au portail utilisateur Azure Stack sur votre système **Azure Stack**, puis accédez au panneau **Présentation** pour **VM001**.

1)  Sélectionnez **Se connecter** pour établir une connexion RDP dans VM001.

2)  Pour pouvoir communiquer via le nom d’hôte, vous allez modifier le fichier **Hosts**. Toutefois, dans un environnement de production, DNS est configuré de façon optimale de telle sorte que les noms se résolvent automatiquement. Vous pouvez également utiliser des adresses IP. Pour notre exemple, toutefois, vous allez modifiez le fichier **Hosts**.

3)  Ouvrez le **Bloc-notes**, en vous assurant que vous l’**exécutez en tant qu’administrateur**.

4)  Une fois que vous avez ouvert le Bloc-notes, sélectionnez **Fichier**, **Ouvrir**, puis accédez à C:\\Windows\system32\Drivers\etc\, et sélectionnez **Tous les fichiers** dans le coin inférieur droit de la boîte de dialogue Ouvrir. Sélectionnez le fichier **hosts** et sélectionnez **Ouvrir**.

5)  Modifiez votre fichier hosts en ajoutant une adresse IP et un nom DNS pour votre serveur de fichiers.

    ![](./media/azure-stack-network-howto-extend-datacenter/image6.png)

6)  Enregistrez le fichier et fermez le Bloc-notes.

7)  Ouvrez **CMD** et exécutez une commande ping sur le nom du serveur de fichiers que vous avez entré dans le fichier hosts. Cela doit renvoyer l’adresse IP de l’une des cartes réseau sur le serveur de fichiers. Par conséquent, testez manuellement la communication avec l’autre carte, en exécutant une commande ping sur l’adresse IP.

## <a name="connect-to-a-windows-server-iscsi-target-server"></a>Se connecter à un serveur cible iSCSI Windows Server

Dans ce scénario, vous allez déployer et configurer une machine virtuelle Windows Server 2019 sur Azure Stack et la préparer pour la connexion à un serveur cible iSCSI externe, qui exécute également Windows Server 2019.

> [!Note]  
> Si vous avez déjà effectué le scénario 1, passez directement à la personnalisation de vos machines.

### <a name="deploy-the-windows-server-2019-vm-on-azure-stack"></a>Déployer la machine virtuelle Windows Server 2019 sur Azure Stack

Si vous n’avez pas encore déployé la machine virtuelle Windows Server 2019 sur Azure Stack, suivez les étapes décrites dans [Déployer la machine virtuelle Windows Server 2019 sur Azure Stack](#deploy-the-windows-server-2019-vm-on-azure-stack). Vous pouvez ensuite configurer la cible iSCSI de Windows Server 2019.

### <a name="configure-the-windows-server-2019-iscsi-target"></a>Configurer la cible iSCSI de Windows Server 2019

Dans le cadre de ce deuxième scénario, validez une configuration où la cible iSCSI Windows Server 2019 est une machine virtuelle s’exécutant sur Hyper-V. Cette machine virtuelle est configurée avec 8 processeurs virtuels, un fichier VHDX unique et, plus important encore, deux cartes réseau virtuelles. Dans un scénario idéal, ces cartes réseau ont des sous-réseaux routables différents, mais dans ce test, vous avez des cartes réseau qui sont situées sur le même sous-réseau.

![](./media/azure-stack-network-howto-extend-datacenter/image5.png)

Pour votre cible iSCSI, il peut s’agir de Windows Server 2016 ou 2019, physique ou virtuel, s’exécutant sur Hyper-V, VMware ou sur une plateforme de votre choix. Le sujet clé ici, est la connectivité à l’intérieur et à l’extérieur du système Azure Stack. Il est toutefois préférable de disposer de plusieurs chemins entre la source et la destination, car cela fournit une redondance et un débit supplémentaires.

Mettez à jour votre serveur de fichiers avec les dernières mises à jour cumulatives et les derniers correctifs, en effectuant un redémarrage avant de procéder à la configuration du serveur cible iSCSI.

Une fois que vous avez effectué la mise à jour et le redémarrage, vous pouvez configurer ce serveur en tant que serveur cible iSCSI.

1. Ouvrez **Gestionnaire de serveur** > **Gérer** > **Ajouter des rôles et des fonctionnalités**.

2. Une fois l’opération effectuée, sélectionnez **Suivant**, sélectionnez **Installation basée sur un rôle ou une fonctionnalité**, puis passez en revue les sélections jusqu’à ce que vous atteigniez la page **Sélectionner des rôles de serveurs**.

3. Développez **Services de fichiers et de stockage**, **Services de fichiers et iSCSI**, sélectionnez le **Serveur cible iSCSI**, acceptez les invites pour ajouter de nouvelles fonctionnalités, puis continuez jusqu’à la fin.

    ![](./media/azure-stack-network-howto-extend-datacenter/image8.png)
    
    Une fois que vous avez fini, fermez le **Gestionnaire de serveur**.

4. Ouvrez l’**Explorateur de fichiers**, accédez à `C:\\`, puis **créez un dossier** appelé `iSCSI`.

5. Rouvrez **Gestionnaire de serveur** > **Services de fichiers et de stockage** dans le menu de gauche.

6. Sélectionnez **iSCSI**, puis **Pour créer un disque virtuel iSCSI, démarrez l’Assistant Nouveau disque virtuel iSCSI**.

7. Dans la page **Sélectionner l’emplacement du disque virtuel iSCSI**, sélectionnez **Tapez un chemin personnalisé**, puis accédez à votre `C:\\iSCSI`. Sélectionnez **Suivant**.

8. Donnez au disque virtuel iSCSI le nom `iSCSIdisk1` et éventuellement une description, puis sélectionnez **Suivant**.

9. Définissez la taille du disque virtuel à `10GB`, sélectionnez **Taille fixe**, puis **Suivant**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image9.png)

10. Dans la mesure où il s’agit d’une nouvelle cible, sélectionnez **Nouvelle cible iSCSI**, puis sélectionnez **Suivant**.

11. Dans la page **Indiquer le nom de la cible**, entrez **CIBLE1**, puis sélectionnez **Suivant**.

12. Dans la page **Indiquer les serveurs d’accès**, sélectionnez **Ajouter**. Cela entraîne l’ouverture d’une boîte de dialogue qui permet d’entrer des **initiateurs** spécifiques autorisés à se connecter à la cible iSCSI.

13. Sélectionnez **Entrez une valeur pour le type sélectionné** dans la fenêtre **Ajouter l’ID d’initiateur**, puis sous **Type**, vérifiez que IQN est sélectionné dans le menu déroulant. Entrez `iqn.1991-05.com.microsoft:<computername>`, où `<computername>` représente le **nom d’ordinateur** de **VM001**. Sélectionnez **Suivant**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image10.png)

14. Dans la page **Activer l’authentification**, laissez les zones vides, puis sélectionnez **Suivant**.

15. Confirmez vos choix, sélectionnez **Créer**, puis fermez la fenêtre.

    ![](./media/azure-stack-network-howto-extend-datacenter/image11.png)

Vous devez voir votre disque virtuel iSCSI créé dans le Gestionnaire de serveur.

### <a name="configure-the-windows-server-2019-iscsi-initiator-and-mpio"></a>Configurer l’initiateur iSCSI Windows Server 2019 et MPIO

Pour configurer l’initiateur iSCSI, connectez-vous au **portail utilisateur** de votre système **Azure Stack**, puis accédez au panneau **Présentation** de **VM001.** .

1.  Établissez une connexion RDP à VM001. Une fois la connexion effectuée, ouvrez le **Gestionnaire de serveur**.

2.  Sélectionnez **Ajouter des rôles et des fonctionnalités**, puis acceptez les valeurs par défaut jusqu’à ce que vous atteigniez la page **Fonctionnalités**.

3.  Dans la page **Fonctionnalités**, ajoutez **MPIO (Multipath I/O)** , puis sélectionnez **Suivant**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image12.png)

4.  Cochez la case **Redémarrer automatiquement le serveur de destination, si nécessaire**, sélectionnez **Installer**, puis **Fermer**. Un redémarrage sera probablement nécessaire. Une fois l’opération effectuée, reconnectez-vous à VM001.

5.  De retour dans **Gestionnaire de serveur**, attendez la fin de l’installation du **MPIO**, sélectionnez **fermer**, **Outils**, puis **MPIO**.

6.  Sélectionnez l’onglet **Découvrir plusieurs chemins**, cochez la case pour **Ajouter la prise en charge des périphériques iSCSI** > **Ajouter**, puis sélectionnez **Oui** afin de **redémarrer** VM001. Sélectionnez **OK**, puis redémarrez manuellement.

    ![](./media/azure-stack-network-howto-extend-datacenter/image13.png)

1.  Une fois le redémarrage effectué, établissez une **nouvelle connexion RDP à VM001**.

2.  Une fois la connexion effectuée, ouvrez **Gestionnaire de serveur**, sélectionnez **Outils** > **Initiateur iSCSI**.

3.  Sélectionnez **Oui** à l’ouverture de Microsoft iSCSI pour permettre au service iSCSI de s’exécuter par défaut.

    ![](./media/azure-stack-network-howto-extend-datacenter/image17.png)

4.  Sélectionnez l’onglet **Découverte**.

5.  Sélectionnez le bouton **Découvrir un portail**. Vous allez maintenant ajouter deux cibles.

6.  Entrez la première adresse IP de votre serveur cible iSCSI, puis sélectionnez **Avancé**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image15.png)

7. Sélectionnez les éléments suivants pour **Paramètres avancés** :

    - Adaptateur local : Initiateur Microsoft iSCSI

    - Initiateur IP : 10.10.10.4

    - Une fois l’opération effectuée, sélectionnez **OK**.

8.  Sélectionnez **OK** dans **Détecter un portail cible**.

9.  Répétez le processus avec ce qui suit :

    - Adresse IP : Votre deuxième adresse IP cible iSCSI

    - Adaptateur local : Initiateur Microsoft iSCSI

    - Initiateur IP : 10.10.11.4

10. Les portails cibles doivent ressembler à ce qui suit, avec vos propres adresses IP cibles iSCSI sous la colonne **Adresse**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image16.png)

11. Sélectionnez l’onglet **Cibles**, puis sélectionnez votre cible iSCSI. Sélectionnez **Connecter**.

12. Sélectionnez **Activer la prise en charge de plusieurs chemins d’accès** dans **Se connecter à la cible**, puis sélectionnez **Avancé**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image17.png)

13. Pour **Se connecter à la cible**, il est nécessaire d’entrer les informations suivantes :

    - Adaptateur local : Initiateur Microsoft iSCSI

    - Initiateur IP : 10.10.10.4

    - IP du portail cible : \<votre première adresse IP cible iSCSI/3260>

    - Sélectionnez **OK**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image18.png)

1.  Répétez le processus pour la deuxième combinaison initiateur/cible.

    - Adaptateur local : Initiateur Microsoft iSCSI

    - Initiateur IP : 10.10.11.4

    - IP du portail cible : \<votre deuxième adresse IP cible iSCSI/3260>

    ![](./media/azure-stack-network-howto-extend-datacenter/image19.png)

1.  Sélectionnez l’onglet **Volumes et périphériques**, puis **Configuration automatique**. Un volume MPIO doit vous être présenté :

    ![](./media/azure-stack-network-howto-extend-datacenter/image20.png)

2.  De retour sous l’onglet **Cibles**, sélectionnez **Périphériques**. Vous devez voir deux connexions au VHD (disque dur virtuel) iSCSI que vous avez créé.

    ![](./media/azure-stack-network-howto-extend-datacenter/image20.png)

3.  Sélectionnez le bouton **MPIO** pour voir des informations supplémentaires sur les chemins et la stratégie d’équilibrage de charge.

    ![](./media/azure-stack-network-howto-extend-datacenter/image21.png)

4.  Sélectionnez **OK** trois fois pour quitter les fenêtres et l’initiateur iSCSI.

5.  Ouvrez Gestion des disques (diskmgmt.msc). Une fenêtre contextuelle indiquant **​​Initialiser le disque** doit s’afficher.

    ![](./media/azure-stack-network-howto-extend-datacenter/image22.png)

6.  Sélectionnez **OK** pour accepter les valeurs par défaut, faites défiler l’affichage jusqu’au nouveau disque, cliquez avec le bouton droit sur celui-ci, puis sélectionnez **Nouveau volume simple**.

7.  Suivez les étapes de l’Assistant en acceptant les valeurs par défaut. Remplacez l’étiquette de volume par **iSCSIdisk1**, puis sélectionnez **Terminer**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image23.png)

8.  Le lecteur est ensuite formaté et présenté avec une lettre de lecteur.

9.  Ouvrez l’**Explorateur de fichiers** > **Ce PC** pour voir votre nouveau lecteur attaché à VM001.

### <a name="test-external-storage-connectivity"></a>Tester la connectivité du stockage externe

Pour valider la communication et exécuter un test de copie de fichier rudimentaire, connectez-vous au **portail utilisateur** de votre système **Azure Stack**, puis accédez au panneau **Présentation** pour **VM001**.

1. Sélectionnez **Se connecter** pour établir une connexion RDP à **VM001**.

2. Ouvrez le **Gestionnaire des tâches**, puis sélectionnez l’onglet **Performances**. Ancrez la fenêtre à droite de la session RDP.

3. Ouvrez **Windows PowerShell ISE** en tant qu’administrateur, et ancrez-le à gauche de la session RDP. À droite d’ISE, fermez le volet **Commandes**, puis sélectionnez le bouton **Script** pour développer le volet de script blanc en haut de la fenêtre d’ISE.

4. Dans cette machine virtuelle, il n’existe aucun module PowerShell natif pour créer un VHD (disque dur virtuel) à utiliser en tant que fichier volumineux pour tester le transfert de fichiers vers la cible iSCSI. Vous allez donc exécuter DiskPart pour créer un fichier VHD. Dans ISE, exécutez ce qui suit :

    1. `Start-Process Diskpart`

    2. Une nouvelle invite de commandes s’ouvre. Entrez la commande suivante :<br>`Create vdisk file="c:\\test.vhd" type=fixed maximum=5120`

    ![](./media/azure-stack-network-howto-extend-datacenter/image24.png)

    L’exécution de cette opération va prendre quelques instants. Une fois l’opération effectuée, pour valider la création, ouvrez l’**Explorateur de fichiers**, puis accédez à C:\\. Le nouveau fichier test.vhd d’une taille de 5 Go doit être présent.

    ![](./media/azure-stack-network-howto-extend-datacenter/image25.png)

    Fermez l’invite de commandes. Retournez dans ISE pour y entrer la commande suivante. Remplacez F:\\ par la lettre de lecteur cible iSCSI qui a été appliquée.

    1. `Copy-Item "C:\\test.vhd" -Destination "F:\\"`

    2. Sélectionnez la ligne dans ISE. Appuyez sur la touche **F8** de votre clavier.

    3. Pendant que la commande s’exécute, examinez les deux cartes réseau, et observez le transfert de données qui a lieu entre celles-ci dans VM001. Notez également que chaque carte réseau doit partager la charge de manière égale.

        ![](./media/azure-stack-network-howto-extend-datacenter/image26.png)

Ce scénario a été conçu pour mettre en évidence la connectivité entre une charge de travail exécutée sur Azure Stack et une baie de stockage externe, dans le cas présent, une cible iSCSI Windows Server. Il n’a pas été conçu pour être un test de performances ni pour refléter les étapes à suivre si vous utilisez une autre appliance basée sur iSCSI. Toutefois, il met en évidence certaines des considérations principales à prendre en compte durant le déploiement de charges de travail sur Azure Stack ainsi que durant la connexion aux systèmes de stockage externes à l’environnement Azure Stack.

## <a name="next-steps"></a>Étapes suivantes

[Différences et considérations relatives aux réseaux Azure Stack](azure-stack-network-differences.md)  