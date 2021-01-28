---
title: Déployer une infrastructure SDN avec SDN Express
description: Découvrez comment déployer une infrastructure SDN à l’aide de SDN Express
author: v-dasis
ms.topic: how-to
ms.date: 01/22/2021
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: ec4c348242910eaf78831b59659bd5943f9cb854
ms.sourcegitcommit: e772df8ac78c86d834a68d1a8be83b7f738019b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98782008"
---
# <a name="deploy-an-sdn-infrastructure-using-sdn-express"></a>Déployer une infrastructure SDN avec SDN Express

> S’applique à Azure Stack HCI, version 20H2

Dans cette rubrique, vous déployez une infrastructure SDN (Software Defined Network) de bout en bout à l’aide de scripts PowerShell pour SDN Express. L’infrastructure comprend ces trois composants à haut niveau de disponibilité : un contrôleur de réseau (HA), un équilibreur de charge logiciel (SLB) et une passerelle.  

Les scripts prennent en charge un déploiement échelonné, où vous pouvez déployer uniquement le contrôleur de réseau pour bénéficier d’un ensemble de fonctionnalités de base avec une configuration réseau minimale. Vous pouvez également déployer le contrôleur de réseau à l’aide de l’Assistant Création d’un cluster dans Windows Admin Center. En revanche, pour déployer d’autres composants SDN tels que l’équilibreur SLB et la passerelle, vous devez utiliser les scripts SDN Express.

Vous pouvez aussi déployer une infrastructure SDN à l’aide de VMM (System Center Virtual Machine Manager). Pour plus d’informations, consultez [Gérer les ressources SDN dans la structure VMM](/system-center/vmm/network-sdn).

## <a name="before-you-begin"></a>Avant de commencer

Avant de commencer le déploiement de SDN, planifiez et configurez votre infrastructure réseau physique et hôte. Consultez les articles suivants :

- [Configuration requise des réseaux virtuels](../concepts/physical-network-requirements.md)
- [Configuration requise des réseaux hôtes](../concepts/host-network-requirements.md)
- [Créer un cluster à l’aide de Windows Admin Center](../deploy/create-cluster.md)
- [Créer un cluster à l’aide de Windows PowerShell](../deploy/create-cluster-powershell.md)
- [Planifier une infrastructure de réseau défini par logiciel](../concepts/plan-software-defined-networking-infrastructure.md)

Vous n’avez pas besoin de déployer tous les composants SDN. Consultez la section [Déploiement échelonné](../concepts/plan-software-defined-networking-infrastructure.md#phased-deployment) dans [Planifier une infrastructure de réseau défini par logiciel](../concepts/plan-software-defined-networking-infrastructure.md) pour déterminer les composants d’infrastructure dont vous avez besoin, puis exécutez les scripts appropriés.

Assurez-vous que le système d’exploitation Azure Stack HCI est installé sur tous les serveurs hôtes. Pour cela, consultez [Déployer le système d’exploitation Azure Stack HCI](../deploy/operating-system.md).

## <a name="run-the-sdn-express-scripts"></a>Exécuter les scripts SDN Express

1. Accédez au dépôt GitHub [SDN Express](https://github.com/microsoft/SDN).

1. Téléchargez les fichiers du dépôt sur votre ordinateur de déploiement désigné. Sélectionnez **Cloner** ou **Télécharger le fichier ZIP**.

    > [!NOTE]
    > L’ordinateur de déploiement désigné doit exécuter Windows Server 2016 ou une version ultérieure.

1. Extrayez le fichier ZIP et copiez le dossier `SDNExpress` dans le dossier `C:\` sur l’ordinateur de déploiement.

1. Accédez au dossier `C:\SDNExpress\scripts`.

1. Exécutez le fichier de script `SDNExpress.ps1`. Ce script utilise un fichier de déploiement (PSD) PowerShell comme entrée pour les différents paramètres de configuration. Consultez le fichier `README.md` pour obtenir des instructions sur l’exécution du script.  

1. Utilisez un VHDX contenant le système d’exploitation Azure Stack HCI comme source pour la création des machines virtuelles SDN. Si vous avez téléchargé et installé le système d’exploitation Azure Stack HCI à partir d’une image ISO, vous pouvez créer ce VHDX à l’aide de l’utilitaire `convert-windowsimage`, comme décrit dans la documentation.

1. Personnalisez le fichier `SDNExpress\scripts\MultiNodeSampleConfig.psd1` en changeant les valeurs spécifiques en fonction de votre infrastructure, notamment les noms d’hôte, les noms de domaine, les noms d’utilisateur et les mots de passe, ainsi que les informations réseau pour les réseaux listés, comme cela est indiqué dans la rubrique [Planifier une infrastructure de réseau défini par logiciel](../concepts/plan-software-defined-networking-infrastructure.md). Ce fichier fournit des informations spécifiques sur les valeurs à renseigner selon que vous déployez uniquement le composant contrôleur de réseau, ou également les composants équilibreur de charge logiciel et passerelle.

1. Exécutez le script suivant à partir d’un compte d’utilisateur en tant qu’administrateur sur les hôtes Hyper-V :

    ```powershell
    SDNExpress\scripts\SDNExpress.ps1 -ConfigurationDataFile MultiNodeSampleConfig.psd1 -Verbose
    ```

1. Une fois les machines virtuelles du contrôleur de réseau créées, configurez les mises à jour DNS dynamiques pour le nom du cluster du contrôleur de réseau sur le serveur DNS. Pour plus d’informations, consultez l’étape 3 dans [Configuration requise pour le déploiement du contrôleur de réseau](/windows-server/networking/sdn/plan/installation-and-preparation-requirements-for-deploying-network-controller#step-3-configure-dynamic-dns-registration-for-network-controller).

## <a name="next-steps"></a>Étapes suivantes

Gérez vos machines virtuelles. Pour plus d’informations, consultez [Gérer les machines virtuelles](../manage/vm.md).