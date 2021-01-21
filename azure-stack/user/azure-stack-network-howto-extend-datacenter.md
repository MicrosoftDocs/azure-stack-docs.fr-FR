---
title: Étendre le centre de données sur Azure Stack Hub
description: Découvrez comment étendre le centre de données sur Azure Stack Hub.
author: mattbriggs
ms.topic: how-to
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: 02765dbc33fe1bbcb4100e1523be96526549f367
ms.sourcegitcommit: 649540e30e1018b409f4b1142bf2cb392c9e8b0d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98208095"
---
# <a name="extending-storage-to-azure-stack-hub"></a>Extension du stockage sur Azure Stack Hub

Cet article fournit des informations sur l’infrastructure de stockage Azure Stack Hub, ce qui vous aidera à déterminer comment intégrer Azure Stack Hub dans votre environnement réseau existant. Après avoir décrit de manière générale l’extension de votre centre de données, l’article présente deux scénarios différents. Vous pouvez vous connecter à un serveur de stockage de fichiers Windows. Vous pouvez également vous connecter à un serveur ISCSI Windows.

## <a name="overview-of-extending-storage-to-azure-stack-hub"></a>Présentation de l’extension du stockage sur Azure Stack Hub

Il existe des scénarios dans lesquels le fait de placer vos données dans le cloud public ne suffit pas. Imaginons que vous ayez une charge de travail de base de données virtualisée qui nécessite beaucoup de ressources système et qui soit sensible aux latences. Supposons que le temps d’aller-retour vers le cloud public affecte les performances de la charge de travail de base de données. Peut-être que des données locales, stockées sur un serveur de fichiers, un NAS ou une baie de stockage iSCSI, doivent être accessibles aux charges de travail locales et résider localement pour répondre aux objectifs de réglementation ou de conformité. Il s’agit simplement de deux des scénarios dans lesquels les données sont conservées localement, ce qui est important pour de nombreuses organisations.

Alors, pourquoi ne pas simplement héberger ces données dans des comptes de stockage sur Azure Stack Hub ou sur des serveurs de fichiers virtualisés s’exécutant sur le système Azure Stack Hub ? Contrairement à Azure, le stockage Azure Stack Hub est limité. La capacité dont vous disposez pour votre utilisation dépend entièrement de la capacité par nœud que vous avez choisi d’acheter, en plus du nombre de nœuds que vous avez. De plus, dans la mesure où Azure Stack Hub est une solution hyperconvergée, si vous souhaitez augmenter votre capacité de stockage pour répondre aux besoins d’utilisation, vous devez également augmenter l’empreinte de calcul en ajoutant des nœuds. Cela peut entraîner des coûts prohibitifs, en particulier si le besoin de capacité supplémentaire concerne le stockage d’archivage froid, qui peut être ajouté à faible coût en dehors du système Azure Stack Hub.

Ce qui vous amène au scénario que vous allez aborder ci-dessous. Comment connecter des systèmes Azure Stack Hub, des charges de travail virtualisées s’exécutant sur Azure Stack Hub, de manière simple et efficace, à des systèmes de stockage en dehors d’Azure Stack Hub, accessibles via le réseau.

### <a name="design-for-extending-storage"></a>Conception pour étendre le stockage

Le schéma illustre un scénario dans lequel une machine virtuelle unique, qui exécute une charge de travail, se connecte à, et utilise le stockage externe (vers la machine virtuelle et l’instance Azure Stack Hub elle-même), à des fins de lecture/écriture de données, etc. Pour cet article, vous allez vous concentrer sur une simple récupération de fichiers, mais vous pouvez développer cet exemple pour des scénarios plus complexes, tels que le stockage étendu des fichiers de base de données.

![Une machine virtuelle de charge de travail exécutée sur le système Azure Stack Hub accède au stockage externe. La machine virtuelle dispose de deux cartes réseau, chacune ayant une adresse IP publique et une adresse IP privée.](./media/azure-stack-network-howto-extend-datacenter/azure-stack-network-howto-extend-datacenter-image1.svg)

Dans le schéma, vous voyez que la machine virtuelle sur le système Azure Stack Hub a été déployée avec plusieurs cartes réseau. En termes de redondance, mais également en tant que bonne pratique de stockage, il est important de disposer de plusieurs chemins entre la cible et la destination. Lorsque les choses deviennent plus complexes, les machines virtuelles Azure Stack Hub doivent avoir des adresses IP publiques et privées, comme dans Azure. Si le stockage externe doit atteindre la machine virtuelle, il peut le faire par le biais de l’adresse IP publique, car les adresses IP privées sont principalement utilisées dans les systèmes Azure Stack Hub, au sein de réseaux virtuels et de sous-réseaux. Le stockage externe ne serait pas en mesure de communiquer avec l’espace d’adressage IP privé de la machine virtuelle, sauf s’il passe à travers un réseau VPN de site à site pour entrer dans réseau virtuel lui-même. Ainsi, pour cet exemple, nous allons nous concentrer sur la communication via l’espace d’adressage IP public. Une chose à noter avec l’espace d’adressage IP public dans le schéma est qu’il existe 2 sous-réseaux de pool d’adresses IP publiques différents. Par défaut, Azure Stack Hub nécessite un seul pool pour les adresses IP publiques, mais il peut être nécessaire d’en ajouter un autre pour proposer un routage redondant. Il n’est cependant pas possible pour le moment de sélectionner une adresse IP à partir d’un pool spécifique : vous pouvez donc vous retrouver avec des machines virtuelles avec des adresses IP publiques du même pool sur plusieurs cartes réseau virtuelles.

Dans le cadre de cette présentation, nous allons supposer que le routage entre les appareils en périphérie et le stockage externe est pris en charge, et que le trafic peut traverser le réseau de manière appropriée. Pour cet exemple, peu importe si le segment principal est 1 GbE, 10 GbE, 25 GbE ou encore plus rapide ; il est cependant important de prendre en compte dans la planification de votre intégration les besoins en termes de performances de toutes les applications qui accèdent à ce stockage externe.

## <a name="connect-to-a-windows-server-iscsi-target"></a>Se connecter à une cible iSCSI Windows Server

Dans ce scénario, vous allez déployer et configurer une machine virtuelle Windows Server 2019 sur Azure Stack Hub et la préparer pour la connexion à une cible iSCSI externe, qui exécute également Windows Server 2019. Là où c’est approprié, nous allons activer des fonctionnalités clés comme MPIO, afin d’optimiser les performances et la connectivité entre la machine virtuelle et le stockage externe.

### <a name="deploy-the-windows-server-2019-vm-on-azure-stack-hub"></a>Déployer la machine virtuelle Windows Server 2019 sur Azure Stack Hub

1.  À partir de votre **portail d’administration Azure Stack Hub**, en supposant que ce système a été inscrit correctement et qu’il est connecté à la Place de marché, sélectionnez **Gestion de la Place de marché** puis, en supposant que vous n’avez pas encore d’image Windows Server 2019, sélectionnez **Ajouter à partir d’Azure** puis recherchez **Windows Server 2019** et ajoutez l’image **Windows Server 2019 Datacenter**.

    ![La boîte de dialogue « Tableau de bord > Gestion de la Place de marché - Éléments de la Place de marché > Ajouter à partir d'Azure » affiche « Windows Server 2019 » dans la zone de recherche, ainsi qu'une liste d'éléments dont le nom contient cette chaîne.](./media/azure-stack-network-howto-extend-datacenter/image2.png)

    Le téléchargement d’une image Windows Server 2019 peut prendre un certain temps.

2.  Une fois que vous disposez d’une image Windows Server 2019 dans votre environnement Azure Stack Hub, **connectez-vous au portail utilisateur d’Azure Stack Hub**.

3.  Une fois que vous êtes connecté au portail utilisateur Azure Stack Hub, vérifiez que vous disposez d’un [abonnement à une offre](../operator/azure-stack-subscribe-plan-provision-vm.md), ce qui vous permet de provisionner des ressources IaaS (calcul, stockage et réseau).

4.  Une fois que vous disposez d’un abonnement, revenez dans le **tableau de bord** du portail utilisateur Azure Stack Hub, sélectionnez **Créer une ressource**, **Calcul**, puis l’**élément de galerie Windows Server 2019 Datacenter**.

5.  Dans le panneau **De base**, renseignez les informations comme suit :

    a.  **Name** : VM001

    b.  **Nom d’utilisateur** : localadmin

    c.  **Mot de passe** et **Confirmer le mot de passe** : \<password of your choice>

    d.  **Abonnement** : \<subscription of your choice, with compute/storage/network resources>.

    e.  **Groupe de ressources** : storagetesting (créer)

    f.  Sélectionnez **OK**.

6.  Dans le panneau **Choisir une taille**, sélectionnez **Standard_F8s_v2** puis **Sélectionner**.

7.  Dans le panneau **Paramètres**, sélectionnez le **réseau virtuel**, puis dans le panneau **Créer un réseau virtuel**, ajustez l’espace d’adressage pour qu’il soit défini sur **10.10.10.0/23** et mettez à jour la plage d’adresses de sous-réseau pour qu’elle soit définie sur **10.10.10.0/24**, puis sélectionnez **OK**.

8.  Sélectionnez l’**adresse IP publique** puis, dans le panneau **Créer une adresse IP publique**, sélectionnez la case d’option **Statique**.

9.  Dans la liste déroulante **Sélectionner des ports entrants publics**, sélectionnez **RDP (3389)** .

10. Conservez les autres valeurs par défaut et sélectionnez **Ok**.

    ![La boîte de dialogue « Tableau de bord > Nouveau > Créer une machine virtuelle > Résumé » indique « Validation réussie » et affiche des informations sur la machine virtuelle VM001.](./media/azure-stack-network-howto-extend-datacenter/image3.png)

11. Lisez le résumé, attendez la validation, puis sélectionnez **OK** pour commencer le déploiement. Le déploiement doit se terminer en environ 10 minutes.

12. Une fois le déploiement terminé, sous **Ressource**, sélectionnez le nom de la machine virtuelle, **VM001**, pour accéder ouvrir **Vue d’ensemble**.

    ![L'écran Vue d'ensemble affiche des informations sur la machine virtuelle VM001.](./media/azure-stack-network-howto-extend-datacenter/image4.png)

13. Sous Nom DNS, sélectionnez **Configurer**, spécifiez une étiquette de nom DNS, **vm001**, sélectionnez **Enregistrer**, puis sélectionnez **VM001**.

14. Sur le côté droit du panneau Présentation, sélectionnez **storagetesting-vnet/default** sous le texte de réseau virtuel/sous-réseau.

15. Dans le panneau storagetesting-vnet, sélectionnez **Sous-réseaux** puis **+ Sous-réseau** puis, dans le nouveau panneau Ajouter un sous-réseau, entrez les informations suivantes et sélectionnez **OK** :

    a.  **Nom** : subnet2

    b.  **Plage d’adresses (bloc CIDR)** : 10.10.11.0/24

    c.  **Groupe de sécurité réseau** : None

    d.  **Table de routage** : None

16. Une fois que c’est enregistré, sélectionnez **VM001**.

17. Dans le volet gauche du panneau Vue d’ensemble, sélectionnez **Réseau**.

18. Sélectionnez **Attacher une interface réseau**, puis sélectionnez **Créer une interface réseau**.

19. Dans le panneau **Créer l’interface réseau**, entrez les informations suivantes.

    a.  **Nom** : vm001nic2

    b.  **Sous-réseau** : Vérifiez que le sous-réseau est 10.10.11.0/24

    c.  **Groupe de sécurité réseau** : VM001-nsg

    d. **Groupe de ressources** : storagetesting

20. Une fois l’interface attachée, sélectionnez **VM001**, puis **Arrêter** pour arrêter la machine virtuelle.

21. Une fois la machine virtuelle arrêtée (désallouée), sur le côté gauche du panneau Vue d’ensemble, sélectionnez **Réseau**, **Attacher l’interface réseau**, **vm001nic2**, puis **OK**. La carte réseau supplémentaire sera ajoutée à la machine virtuelle dans quelques instants.

22. Toujours dans le panneau **Réseau,** sélectionnez l’onglet **vm001nic2**, puis sélectionnez **Interface réseau : vm001nic2**.

23. Dans le panneau de l’interface vm001nic, sélectionnez **Configurations IP**, puis dans le centre du panneau, sélectionnez **ipconfig1**.

24. Dans le panneau Paramètres ipconfig1, sélectionnez **Activé** pour l’adresse IP publique et sélectionnez **Configurer les paramètres requis**, **Créer**, puis entrez vm001nic2pip comme nom, sélectionnez **Statique** et **OK**, puis **Enregistrer**.

25. Une fois l’enregistrement réussi, revenez au panneau Présentation de VM001, puis sélectionnez **Démarrer** pour démarrer la machine virtuelle Windows Server 2019 configurée.

26. Une fois la machine démarrée, **établissez une session RDP** dans VM001.

27. Une fois connecté à l’intérieur de la machine virtuelle, ouvrez **CMD** (en tant qu’administrateur) et entrez **nom d’hôte** pour récupérer le nom d’ordinateur du système d’exploitation. **Il doit correspondre à VM001**. Prenez en note pour plus tard.

### <a name="configure-second-network-adapter-on-windows-server-2019-vm-on-azure-stack-hub"></a>Configurer une deuxième carte réseau sur la machine virtuelle Windows Server 2019 sur Azure Stack Hub

Par défaut, Azure Stack Hub affecte une passerelle par défaut à la première (principale) interface réseau attachée à la machine virtuelle. Azure Stack Hub n’affecte pas de passerelle par défaut aux interfaces réseau additionnelles (secondaires) attachées à une machine virtuelle. Par conséquent, vous ne pouvez pas communiquer avec les ressources hors du sous-réseau dans lequel se trouve, par défaut, une interface réseau secondaire. Toutefois, les interfaces réseau secondaires peuvent communiquer avec les ressources hors de leur sous-réseau. Ceci dit, les étapes à suivre pour permettre cette communication sont différentes d’un système d’exploitation à un autre.

1.  Si vous n’avez pas encore de connexion ouverte, établissez une connexion RDP dans **VM001**.

2.  Ouvrez **CMD** en tant qu’administrateur et exécutez **route print**, qui doit retourner les deux interfaces (cartes réseau Hyper-V) à l’intérieur de cette machine virtuelle.

    ![La sortie de la commande « route print » est une liste d'interfaces qui comprend deux cartes réseau Hyper-V : L'Interface 6 correspond à la carte réseau Hyper-V n°2 et l'Interface 7 à la carte n°3.](./media/azure-stack-network-howto-extend-datacenter/image5.png)

3.  Exécutez maintenant **ipconfig** pour voir quelle adresse IP est affectée à l’interface réseau secondaire. Dans cet exemple, l’adresse 10.10.11.4 est affectée à l’interface 6. Aucune adresse de passerelle par défaut n’est retournée pour l’interface réseau secondaire.

    ![La liste ipconfig partielle montre que l'adresse IPv4 de la carte Ethernet 2 est 10.10.11.4.](./media/azure-stack-network-howto-extend-datacenter/image6.png)

4.  Pour router le trafic destiné aux adresses en dehors du sous-réseau de l’interface réseau secondaire vers la passerelle du sous-réseau, exécutez la commande suivante à partir de **CMD :** .

    ```CMD  
    route add -p 0.0.0.0 MASK 0.0.0.0 <ipaddress> METRIC 5015 IF <interface>
    ```

    `<ipaddress>` est l’adresse .1 du sous-réseau actuel et `<interface>` est le numéro d’interface.

    ![La commande route add est émise avec la valeur ipaddress 10.10.11.1 et le numéro d'interface 6.](./media/azure-stack-network-howto-extend-datacenter/image7.png)

5.  Pour vérifier que la route ajoutée se trouve dans la table de routage, entrez la commande **route print**.

    ![L'itinéraire ajouté est présenté comme un itinéraire persistant avec l'adresse de passerelle 10.10.11.1 et la métrique 5015.](./media/azure-stack-network-howto-extend-datacenter/image8.png)

6.  Vous pouvez également vérifier la communication sortante en exécutant une commande ping :  
    `ping 8.8.8.8 -S 10.10.11.4`  
    L’indicateur `-S` vous permet de spécifier une adresse source : dans le cas présent, 10.10.11.4 est l’adresse IP de la carte réseau qui a maintenant une passerelle par défaut.

7.  Fermez **CMD**.

### <a name="configure-the-windows-server-2019-iscsi-target"></a>Configurer la cible iSCSI Windows Server 2019

Dans le cadre de ce scénario, vous allez vérifier une configuration où la cible iSCSI Windows Server 2019 est une machine virtuelle s’exécutant sur Hyper-V, en dehors de l’environnement Azure Stack Hub. Cette machine virtuelle est configurée avec 8 processeurs virtuels, un fichier VHDX unique et, plus important encore, deux cartes réseau virtuelles. Dans un scénario idéal, ces cartes réseau ont des sous-réseaux routables différents, mais dans cette vérification, les cartes réseau se trouvent sur le même sous-réseau.

![La sortie de la commande ipconfig partielle révèle la présence de deux cartes Ethernet sur le même sous-réseau ; les adresses IP sont 10.33.131.15 et 10.33.131.16.](./media/azure-stack-network-howto-extend-datacenter/image9.png)

Pour votre serveur cible iSCSI, il peut s’agir de Windows Server 2016 ou 2019, physique ou virtuel, s’exécutant sur Hyper-V, VMware ou sur une autre appliance de votre choix, comme un SAN iSCSI physique dédié. Le point important ici est la connectivité à l’intérieur et à l’extérieur du système Azure Stack Hub. Il est toutefois préférable de disposer de plusieurs chemins entre la source et la destination, car cela fournit une redondance supplémentaire et permet d’utiliser des fonctionnalités plus avancées pour augmenter les performances, comme MPIO.

Je vous encourage à mettre à jour votre cible iSCSI Windows Server 2019 avec les dernières mises à jour cumulatives et les derniers correctifs, en effectuant si nécessaire un redémarrage avant de procéder à la configuration des partages de fichiers.

Une fois que vous avez effectué la mise à jour et le redémarrage, vous pouvez configurer ce serveur comme cible iSCSI.

1.  Ouvrez **Gestionnaire de serveur** et sélectionnez **Gérer**, puis **Ajouter des rôles et des fonctionnalités**.

2.  Une fois l’opération effectuée, sélectionnez **Suivant**, sélectionnez **Installation basée sur un rôle ou une fonctionnalité**, puis effectuez les sélections jusqu’à atteindre la page **Sélectionner des rôles de serveurs**.

3.  Développez **Services de fichiers et de stockage**, développez **Services de fichiers et iSCSI**, cochez la case **Serveur cible iSCSI**, acceptez les invites pour ajouter de nouvelles fonctionnalités, puis continuez jusqu’à la fin.

    ![La page de confirmation de l'Assistant Ajouter des rôles et des fonctionnalités est intitulée « Confirmer les sélections d'installation ». Les services de fichiers et de stockage sont étendus pour afficher les services de fichiers et iSCSI, qui sont eux-mêmes étendus pour afficher le serveur cible iSCSI.](./media/azure-stack-network-howto-extend-datacenter/image10.png)

    Quand vous avez terminé, fermez le **Gestionnaire de serveur.**

4.  Ouvrez l’**Explorateur de fichiers**, accédez à C:\\, puis **créez un dossier** appelé **iSCSI**.

5.  Rouvrez le **Gestionnaire de serveur** et sélectionnez **Services de fichiers et de stockage** dans le menu de gauche.

6.  Sélectionnez **iSCSI**, puis le lien **Pour créer un disque virtuel iSCSI, démarrez l’Assistant Nouveau disque virtuel iSCSI** dans le volet droit. Sélectionnez-le. Un Assistant apparaît.

7.  Dans la page **Sélectionner l’emplacement du disque virtuel iSCSI**, sélectionnez **Tapez un chemin personnalisé**, puis accédez à votre **C:\\iSCSI** et sélectionnez **Suivant**.

8.  Donnez au disque virtuel iSCSI le nom **iSCSIdisk1** et éventuellement une description, puis sélectionnez **Suivant**.

9.  Définissez la taille du disque virtuel sur **10 Go**, sélectionnez **Taille fixe**, puis **Suivant**.

    ![La page Taille du disque virtuel iSCSI de l'Assistant Nouveau disque virtuel iSCSI spécifie une taille fixe de 10 Go, et la case « Effacer le disque virtuel au moment de l'allocation » est cochée.](./media/azure-stack-network-howto-extend-datacenter/image11.png)

10) Dans la mesure où il s’agit d’une nouvelle cible, sélectionnez **Nouvelle cible iSCSI**, puis sélectionnez **Suivant**.

11) Dans la page **Indiquer le nom de la cible**, entrez **CIBLE1**, puis sélectionnez **Suivant**.

12) Dans la page **Indiquer les serveurs d’accès**, sélectionnez **Ajouter**. Cela entraîne l’ouverture d’une boîte de dialogue qui permet d’entrer des **initiateurs** spécifiques autorisés à se connecter à la cible iSCSI.

13) Dans la fenêtre **Ajouter l’ID d’initiateur**, sélectionnez **Entrez une valeur pour le type sélectionné** puis, sous **Type**, vérifiez que IQN est sélectionné dans le menu déroulant. Entrez **iqn.1991-05.com.microsoft:\<computername>** où \<computername> est le **nom d’ordinateur** de **VM001**, puis sélectionnez **Suivant**.

    ![La fenêtre « Ajouter l'ID d'initiateur » contient les valeurs permettant de spécifier l'ID d'initiateur.](./media/azure-stack-network-howto-extend-datacenter/image12.png)

14) Dans la page **Activer l’authentification**, laissez les zones vides, puis sélectionnez **Suivant**.

15) Confirmez vos choix, sélectionnez **Créer**, puis fermez la fenêtre. Vous devez voir votre disque virtuel iSCSI créé dans le Gestionnaire de serveur.

    ![La page Résultats de l'Assistant Nouveau disque virtuel iSCSI indique que le disque virtuel ISCSI a bien été créé.](./media/azure-stack-network-howto-extend-datacenter/image13.png)

### <a name="configure-the-windows-server-2019-iscsi-initiator-and-mpio"></a>Configurer l’initiateur iSCSI Windows Server 2019 et MPIO

Pour configurer l’initiateur iSCSI, reconnectez-vous au **portail utilisateur Azure Stack Hub** sur votre système **Azure Stack Hub**, puis accédez au panneau **Vue d’ensemble** de **VM001**.

1.  Établissez une connexion RDP à VM001. Une fois la connexion effectuée, ouvrez le **Gestionnaire de serveur**.

2.  Sélectionnez **Ajouter des rôles et des fonctionnalités**, puis acceptez les valeurs par défaut jusqu’à ce que vous atteigniez la page **Fonctionnalités**.

3.  Dans la page **Fonctionnalités**, ajoutez **MPIO (Multipath I/O)** , puis sélectionnez **Suivant**.

    ![La page Fonctionnalités de l'Assistant Ajouter des rôles et des fonctionnalités montre qu'une fonctionnalité est sélectionnée, MPIO (Multipath I/O).](./media/azure-stack-network-howto-extend-datacenter/image14.png)

4.  Cochez la case **Redémarrer automatiquement le serveur de destination si nécessaire**, sélectionnez **Installer**, puis **Fermer**. Un redémarrage sera probablement nécessaire. Une fois l’opération effectuée, reconnectez-vous à VM001.

5.  De retour dans **Gestionnaire de serveur**, attendez la fin de l’installation du **MPIO**, sélectionnez **fermer**, **Outils**, puis **MPIO**.

6.  Sélectionnez l’onglet **Découvrir plusieurs chemins**, cochez la case pour **Ajouter la prise en charge des périphériques iSCSI**, sélectionnez **Ajouter**, puis sélectionnez **Oui** pour **redémarrer** VM001. Si vous ne voyez pas de fenêtre, sélectionnez **OK**, puis redémarrez manuellement.

    ![La page Découvrir plusieurs chemins de la boîte de dialogue MPIO montre que la case « Ajouter la prise en charge des périphériques iSCSI » est cochée. Un bouton Ajouter est disponible.](./media/azure-stack-network-howto-extend-datacenter/image15.png)

7.  Une fois le redémarrage effectué, établissez une **nouvelle connexion RDP à VM001**.

8.  Une fois la connexion effectuée, ouvrez **Gestionnaire de serveur**, sélectionnez **Outils**, puis **Initiateur iSCSI**.

9.  Quand une fenêtre iSCSI Microsoft apparaît, sélectionnez **Oui** pour autoriser le service iSCSI à s’exécuter par défaut.

    ![La boîte de dialogue Microsoft iSCSI signale que le service iSCSI n'est pas en cours d'exécution ; un bouton Oui est disponible pour démarrer le service.](./media/azure-stack-network-howto-extend-datacenter/image16.png)

10. Dans la fenêtre Propriétés de l’initiateur iSCSI, sélectionnez l’onglet **Découverte**.

11. Vous allez maintenant ajouter 2 cibles, donc sélectionnez d’abord le bouton **Découvrir un portail**.

12. Entrez la première adresse IP de votre serveur cible iSCSI, puis sélectionnez **Avancé**.

    ![La fenêtre Détecter un portail cible affiche 10.33.131.15 dans la zone de texte « Adresse IP ou nom DNS : » et 3260 (valeur par défaut) dans la zone de texte Port. Un bouton Avancé est disponible.](./media/azure-stack-network-howto-extend-datacenter/image17.png)

13. Dans la fenêtre **Paramètres avancés**, sélectionnez les options suivantes, puis sélectionnez **OK**.

    a.  **Adaptateur local** : Initiateur Microsoft iSCSI.

    b.  **Adresse IP de l’initiateur :** 10.10.10.4.

14. De retour dans la fenêtre **Découvrir le portail cible**, sélectionnez **OK**.

15. Répétez le processus avec ce qui suit :

    a. **Adresse IP** : L’adresse IP de votre deuxième cible iSCSI.

    b.  **Adaptateur local** : Initiateur Microsoft iSCSI.

    c.  **Adresse IP de l’initiateur :** 10.10.11.4.

16. Les portails cibles doivent ressembler à ce qui suit, avec vos propres adresses IP cibles iSCSI sous la colonne **Adresse**.

    ![La boîte de dialogue « Portails cibles » affiche les deux portails qui viennent d'être créés. Les adresses IP sont 10.33.131.15 et 10.33.131.16.](./media/azure-stack-network-howto-extend-datacenter/image18.png)

17. De retour sous l’onglet **Cibles**, sélectionnez votre cible iSCSI au milieu de la fenêtre, puis sélectionnez **Se connecter**.

18. Dans la fenêtre **Se connecter à la cible**, cochez la case **Activer la prise en charge de plusieurs chemins d’accès**, puis sélectionnez **Avancé**.

    ![La boîte de dialogue « Se connecter à la cible » affiche les valeurs spécifiées. Un bouton Avancé et un bouton OK sont disponibles.](./media/azure-stack-network-howto-extend-datacenter/image19.png)

19. Entrez les informations suivantes et sélectionnez **OK** puis, dans la fenêtre **Se connecter à la cible**, sélectionnez **OK**.

    a.  **Adaptateur local** : Initiateur Microsoft iSCSI.

    b.  **Adresse IP de l’initiateur :** 10.10.10.4.

    c.  **Adresse IP du portail cible** : \<your first iSCSI Target IP / 3260>.

    ![La boîte de dialogue « Se connecter via » affiche les informations spécifiées pour le portail cible 10.33.131.15/3260.](./media/azure-stack-network-howto-extend-datacenter/image20.png)

1.  Répétez le processus pour la deuxième combinaison initiateur/cible.

    a. **Adaptateur local** : Initiateur Microsoft iSCSI.

    b.  **Adresse IP de l’initiateur :** 10.10.11.4.

    c.  **Adresse IP du portail cible** : \<your second iSCSI Target IP / 3260>.

    ![La boîte de dialogue « Se connecter via » affiche les informations spécifiées pour le portail cible 10.33.131.16/3260.](./media/azure-stack-network-howto-extend-datacenter/image21.png)

2.  Sélectionnez l’onglet **Volumes et périphériques**, puis **Configuration automatique**. Un volume MPIO doit vous être présenté :

    ![La fenêtre Liste des volumes affiche le nom du volume, le point de montage et le périphérique pour un seul volume.](./media/azure-stack-network-howto-extend-datacenter/image22.png)

3.  De retour sous l’onglet **Cibles**, sélectionnez **Périphériques**. Vous devez voir 2 connexions au disque dur virtuel iSCSI que vous avez créé précédemment.

    ![La boîte de dialogue Périphériques affiche Disque 2 sur deux lignes. La cible est 0 sur la première ligne, 1 sur la seconde.](./media/azure-stack-network-howto-extend-datacenter/image23.png)

4.  Sélectionnez le bouton **MPIO** pour voir des informations supplémentaires sur les chemins et la stratégie d’équilibrage de charge.

    ![La page MPIO de la boîte de dialogue Détails des périphériques indique Tourniquet (round robin) pour la stratégie d'équilibrage de charge, et répertorie deux périphériques.](./media/azure-stack-network-howto-extend-datacenter/image24.png)

5.  Sélectionnez **OK** trois fois pour quitter les fenêtres et l’initiateur iSCSI.

6.  Ouvrez Gestion des disques (diskmgmt.msc) : une fenêtre **​​Initialiser le disque** doit apparaître.

    ![La boîte de dialogue Initialiser le disque montre que la case Disque 2 est cochée et que le style de partition Enregistrement de démarrage principal est sélectionné . Un bouton OK est disponible.](./media/azure-stack-network-howto-extend-datacenter/image25.png)

7.  Sélectionnez **OK** pour accepter les valeurs par défaut, faites défiler l’affichage jusqu’au nouveau disque, cliquez avec le bouton droit sur celui-ci, puis sélectionnez **Nouveau volume simple**

8.  Suivez les étapes de l’Assistant en acceptant les valeurs par défaut. Remplacez l’étiquette de volume par **iSCSIdisk1**, puis sélectionnez **Terminer**.

    ![La boîte de dialogue Assistant Création d'un volume simple indique que le volume doit être NTFS, avec la taille d'unité d'allocation par défaut et l'étiquette de volume « iSCSIdisk1 ». Le formatage rapide est sélectionné. Un bouton Suivant est disponible.](./media/azure-stack-network-howto-extend-datacenter/image26.png)

9.  Le lecteur est ensuite formaté et présenté avec une lettre de lecteur.

10. Ouvrez l’**Explorateur de fichiers**, puis sélectionnez **Ce PC** pour voir votre nouveau lecteur attaché à VM001.

### <a name="testing-external-storage-connectivity"></a>Test de la connectivité du stockage externe

Pour vérifier la communication et effectuer un test rudimentaire de copie de fichier, connectez-vous d’abord au **portail utilisateur Azure Stack Hub** sur votre système **Azure Stack Hub**, puis accédez au panneau **Vue d’ensemble** pour **VM001**

1.  Sélectionnez **Se connecter** pour établir une connexion RDP à **VM001**.

2.  Ouvrez **Gestionnaire des tâches**, sélectionnez l’onglet **Performances**, puis alignez la fenêtre sur le côté droit de la session RDP.

3.  Ouvrez **Windows PowerShell ISE** en tant qu’administrateur, et ancrez-le à gauche de la session RDP. À droite d’ISE, fermez le volet **Commandes**, puis sélectionnez le bouton **Script** pour développer le volet de script blanc en haut de la fenêtre d’ISE.

4.  Dans cette machine virtuelle, il n’existe aucun module PowerShell natif pour créer un disque dur virtuel à utiliser comme gros fichier pour tester le transfert de fichiers vers la cible iSCSI. Dans ce cas, nous allons exécuter DiskPart pour créer un fichier VHD. Dans ISE, exécutez ce qui suit :

    1. `Start-Process Diskpart`

    2. Une nouvelle fenêtre CMD s’ouvre. Entrez ceci :  
        `**Create vdisk file="c:\\test.vhd" type=fixed maximum=5120**`
    
    ![La fenêtre CMD montre que la commande spécifiée a été envoyée à DiskPart qui l'a exécutée avec succès, créant ainsi le fichier de disque virtuel.](./media/azure-stack-network-howto-extend-datacenter/image27.png)
    
    3.  L’exécution de cette opération va prendre quelques instants. Une fois l’opération effectuée, pour vérifier la création, ouvrez l’**Explorateur de fichiers**, puis accédez à C:\\ : vous devez normalement voir le nouveau fichier test.vhd d’une taille de 5 Go.

    ![Le fichier test.vhd apparaît dans C:\,, comme prévu, avec la taille spécifiée.](./media/azure-stack-network-howto-extend-datacenter/image28.png)

    4. Fermez la fenêtre CMD et revenez à l’environnement ISE, puis entrez la commande suivante dans la fenêtre de script. Remplacez F:\\ par la lettre de lecteur cible iSCSI qui a été appliquée.

    5. `Copy-Item "C:\\test.vhd" -Destination "F:\\"`

    6. Sélectionnez la ligne dans la fenêtre de script, puis appuyez sur F8 pour l’exécuter.

    7. Pendant que la commande s’exécute, examinez les deux cartes réseau et observez le transfert de données qui a lieu entre celles-ci dans VM001. Notez également que chaque carte réseau doit partager la charge de manière égale.

    ![Les deux cartes affichent une charge de 2,6 Mbits/s.](./media/azure-stack-network-howto-extend-datacenter/image29.png)

Ce scénario a été conçu pour mettre en évidence la connectivité entre une charge de travail exécutée sur Azure Stack Hub et une baie de stockage externe, dans le cas présent, une cible iSCSI Windows Server. Il n’a pas été conçu pour être un test de performances ni pour refléter les étapes à suivre si vous utilisez une autre appliance basée sur iSCSI. Toutefois, il met en évidence certaines des considérations principales à prendre en compte durant le déploiement de charges de travail sur Azure Stack Hub ainsi que durant la connexion aux systèmes de stockage externes à l’environnement Azure Stack Hub.

## <a name="next-steps"></a>Étapes suivantes

[Différences et considérations relatives aux réseaux Azure Stack Hub](azure-stack-network-differences.md)
