---
title: Déployer le système d’exploitation Azure Stack HCI
description: Découvrez comment déployer le système d’exploitation Azure Stack HCI, puis utiliser Windows Admin Center pour vous connecter à vos serveurs. Découvrez comment créer un cluster de serveurs, et comment obtenir les mises à jour Windows et les microprogrammes les plus récents pour vos serveurs.
author: JohnCobb1
ms.author: v-johcob
ms.topic: tutorial
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/24/2020
ms.openlocfilehash: cf34506f5fbeec1c6e0531eda231219ae2949b59
ms.sourcegitcommit: 69cfff119ab425d0fbb71e38d1480d051fc91216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91572600"
---
# <a name="deploy-the-azure-stack-hci-operating-system"></a>Déployer le système d’exploitation Azure Stack HCI

> S’applique à : Azure Stack HCI, version 20H2

Après avoir effectué les étapes indiquées dans [Avant de déployer Azure Stack HCI](before-you-start.md#install-windows-admin-center), la première étape du déploiement d’Azure Stack HCI consiste à [télécharger Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) et à installer le système d’exploitation sur chaque serveur que vous souhaitez mettre en cluster. Cet article décrit les différentes façons de déployer le système d’exploitation et l’utilisation de Windows Admin Center pour se connecter aux serveurs.

Une fois le système d’exploitation déployé, vous pouvez utiliser les conseils associés à la création d’un cluster de serveurs et à l’obtention des dernières mises à jour Windows et de microprogramme pour vos serveurs, comme décrit dans [Créer un cluster Azure Stack HCI](create-cluster.md).

## <a name="prerequisites"></a>Prérequis

- Windows Admin Center configuré sur un système qui peut accéder aux serveurs que vous souhaitez mettre en cluster, comme décrit dans [Avant de déployer Azure Stack HCI](before-you-start.md#install-windows-admin-center).
- Une solution Azure Stack HCI qui fournit le matériel validé par Microsoft de votre fournisseur de matériel préféré, le système d’exploitation Azure Stack HCI et les services Azure, comme décrit dans [Solutions Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/).

## <a name="deployment-preparation"></a>Préparation du déploiement

Une fois que vous avez acquis le matériel serveur pour votre solution Azure Stack HCI, il est temps de le monter en rack et de le câbler. Pour préparer le matériel serveur au déploiement du système d’exploitation, effectuez les étapes suivantes.

1. Montez en rack tous les nœuds serveur que vous souhaitez utiliser dans votre cluster de serveurs.
1. Connectez les nœuds serveur à vos commutateurs réseau.
1. Configurez le BIOS ou l’interface UEFI (Unified Extensible Firmware Interface) de vos serveurs comme recommandé par votre fournisseur de matériel Azure Stack HCI pour optimiser les performances et la fiabilité.

## <a name="operating-system-deployment-options"></a>Options de déploiement du système d’exploitation

Vous pouvez déployer le système d’exploitation Azure Stack HCI de la même façon que d’autres systèmes d’exploitation Microsoft :

- Préinstallation par le fabricant du serveur.
- Déploiement non interactif à l’aide d’un fichier de réponses.
- System Center Virtual Machine Manager (VMM).
- Déploiement réseau.
- Déploiement manuel en connectant un clavier et un moniteur directement au matériel serveur dans votre centre de données ou en connectant un périphérique matériel KVM au serveur.

### <a name="server-manufacturer-preinstallation"></a>Préinstallation par le fabricant du serveur

Pour le déploiement en entreprise du système d’exploitation Azure Stack HCI, nous vous recommandons la solution matérielle avec système Azure Stack HCI intégré du partenaire fournisseur de matériel de votre choix. La solution matérielle est fournie avec le système d’exploitation préinstallé et prend en charge l’utilisation de Windows Admin Center pour déployer et mettre à jour les pilotes et le microprogramme du fabricant du matériel.

La solution matérielle comprend entre 2 et 16 nœuds et est testée et validée par Microsoft et les fournisseurs partenaires. Pour trouver la solution matérielle Azure Stack HCI auprès du partenaire fournisseur de matériel de votre choix, consultez le [Catalogue Azure Stack HCI](https://www.microsoft.com/cloud-platform/azure-stack-hci-catalog).

### <a name="headless-deployment"></a>Déploiement non interactif

Vous pouvez utiliser un fichier de réponses pour effectuer un déploiement non interactif du système d’exploitation. Le fichier de réponses utilise un format XML pour définir les paramètres et les valeurs de configuration pendant une installation sans assistance du système d’exploitation.

Pour cette option de déploiement, vous pouvez utiliser l’Assistant Gestion d’installation pour créer un fichier de réponses unattend.xml afin de déployer le système d’exploitation sur vos serveurs. L’Assistant Gestion d’installation crée votre fichier de réponses d’installation sans assistance par le biais d’un outil graphique avec des sections de composant permettant de définir les « réponses » aux questions de configuration, puis de garantir le format et la syntaxe corrects dans le fichier.
L’Assistant Gestion d’installation est disponible dans le Kit de déploiement et d’évaluation Windows (Windows ADK). Pour commencer : [Téléchargez et installez Windows ADK](/windows-hardware/get-started/adk-install).

### <a name="system-center-virtual-machine-manager-vmm-deployment"></a>Déploiement avec System Center Virtual Machine Manager (VMM)

System Center Virtual Machine Manager (VMM) fait partie de la suite System Center. Vous pouvez utiliser VMM pour déployer le système d’exploitation Azure Stack HCI sur du matériel nu ainsi que pour mettre en cluster les serveurs. Pour découvrir VMM, consultez [Configuration système requise pour System Center Virtual Machine Manager](/system-center/vmm/system-requirements).

Pour plus d’informations sur l’utilisation de VMM afin d’effectuer un déploiement complet du système d’exploitation, consultez [Configurer un hôte ou un cluster Hyper-V à partir de systèmes nus](/system-center/vmm/hyper-v-bare-metal).

### <a name="network-deployment"></a>Déploiement réseau

Une autre option consiste à installer le système d’exploitation Azure Stack HCI via le réseau à l’aide de [Services de déploiement Windows](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831764(v=ws.11)).

### <a name="manual-deployment"></a>Déploiement manuel

Pour déployer manuellement le système d’exploitation Azure Stack HCI sur le lecteur système de chaque serveur à mettre en cluster, installez le système d’exploitation par le biais de la méthode de votre choix, telle que le démarrage à partir d’un lecteur DVD ou USB. Effectuez le processus d’installation à l’aide de l’outil de configuration du serveur (Sconfig) pour préparer les serveurs au clustering. Pour en savoir plus sur cet outil, consultez [Configurer une installation minimale avec Sconfig](/windows-server/get-started/sconfig-on-ws2016).

Pour installer manuellement le système d’exploitation Azure Stack HCI :
1. Démarrez l’Assistant Installation d’Azure Stack HCI sur le lecteur système du serveur sur lequel vous souhaitez installer le système d’exploitation.
1. Choisissez la langue à installer ou acceptez les paramètres de langue par défaut, sélectionnez **Suivant** puis, dans la page suivante de l’Assistant, sélectionnez **Installer maintenant**.

    :::image type="content" source="../media/operating-system/azure-stack-hci-install-language.png" alt-text="Page de définition de la langue dans l’Assistant Installation d’Azure Stack HCI":::

1. Dans la page Mentions et termes du contrat de licence applicables, passez en revue les termes du contrat de licence, cochez la case **J’accepte les termes du contrat de licence**, puis sélectionnez **Suivant**.
1. Dans la page « Quel type d’installation voulez-vous effectuer ? », sélectionnez **Personnalisé : installer la version la plus récente d’Azure Stack HCI uniquement (avancé)** .

    > [!NOTE]
    > Les installations de mise à niveau ne sont pas prises en charge dans cette version du système d’exploitation.

    :::image type="content" source="../media/operating-system/azure-stack-hci-install-which-type.png" alt-text="Page de définition de la langue dans l’Assistant Installation d’Azure Stack HCI":::

1. Dans la page « Où voulez-vous installer Azure Stack HCI ? », confirmez l’emplacement du lecteur sur lequel vous souhaitez installer le système d’exploitation ou mettez-le à jour, puis sélectionnez **Suivant**.

    :::image type="content" source="../media/operating-system/azure-stack-hci-install-where.png" alt-text="Page de définition de la langue dans l’Assistant Installation d’Azure Stack HCI":::

1. La page Installation d’Azure Stack HCI s’affiche pour montrer la progression du processus.

    :::image type="content" source="../media/operating-system/azure-stack-hci-installing.png" alt-text="Page de définition de la langue dans l’Assistant Installation d’Azure Stack HCI":::

    > [!NOTE]
    > Le processus d’installation redémarre deux fois le système d’exploitation pour se terminer, puis affiche des notifications sur le démarrage des services avant d’ouvrir une invite de commandes d’administrateur.

1. À l’invite de commandes de l’administrateur, sélectionnez **OK** pour changer le mot de passe de l’utilisateur avant de vous connecter au système d’exploitation, puis appuyez sur Entrée.

    :::image type="content" source="../media/operating-system/azure-stack-hci-change-admin-password.png" alt-text="Page de définition de la langue dans l’Assistant Installation d’Azure Stack HCI":::

1. À l’invite Entrez les nouvelles informations d’identification de l’administrateur, entrez un nouveau mot de passe, entrez-le de nouveau pour le confirmer, puis appuyez sur Entrée.
1. À l’invite confirmant le changement de votre mot de passe, appuyez sur Entrée.

    :::image type="content" source="../media/operating-system/azure-stack-hci-admin-password-changed.png" alt-text="Page de définition de la langue dans l’Assistant Installation d’Azure Stack HCI":::

Vous êtes maintenant prêt à utiliser l’outil de configuration du serveur (Sconfig) pour effectuer des tâches importantes. Pour utiliser Sconfig, connectez-vous au serveur exécutant le système d’exploitation Azure Stack HCI. Vous pouvez procéder via un clavier et un moniteur, ou bien utiliser un contrôleur de gestion à distance (non interactif ou BMC) ou le Bureau à distance. L’outil Sconfig s’ouvre automatiquement quand vous ouvrez une session sur le serveur.

:::image type="content" source="../media/operating-system/azure-stack-hci-sconfig-screen.png" alt-text="Page de définition de la langue dans l’Assistant Installation d’Azure Stack HCI" lightbox="../media/operating-system/azure-stack-hci-sconfig-screen.png":::

Dans la page principale de l’outil Sconfig, vous pouvez effectuer les tâches de configuration initiales suivantes :
- Configurez le réseau ou vérifiez que le réseau a été configuré automatiquement à l’aide du protocole DHCP (Dynamic Host Configuration Protocol).
- Renommez le serveur si le nom de serveur généré automatiquement par défaut ne vous convient pas.
- Joignez le serveur à un domaine Active Directory.
- Ajoutez votre compte d’utilisateur de domaine ou votre groupe de domaine désigné aux administrateurs locaux.
- Activez l’accès à Windows Remote Management (WinRM) si vous envisagez de gérer le serveur en dehors du sous-réseau local et que vous avez décidé de ne pas encore joindre le domaine. (Les règles de pare-feu par défaut autorisent la gestion à la fois depuis le sous-réseau local et depuis n’importe quel sous-réseau au sein de vos services de domaine Active Directory.)

Après avoir configuré le système d’exploitation en fonction des besoins avec Sconfig sur chaque serveur, vous êtes prêt à utiliser l’Assistant Création de cluster dans Windows Admin Center pour organiser les serveurs en cluster.

## <a name="next-steps"></a>Étapes suivantes

Pour effectuer la tâche de gestion suivante associée à cet article, consultez :
> [!div class="nextstepaction"]
> [Créer un cluster Azure Stack HCI](../deploy/create-cluster.md)
