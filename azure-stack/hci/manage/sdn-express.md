---
title: Déployer une infrastructure SDN avec SDN Express
description: Découvrez comment déployer une infrastructure SDN à l’aide de SDN Express
author: v-dasis
ms.topic: how-to
ms.date: 02/01/2021
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: b8431b7e2cf2cad3d238386619839a760b722042
ms.sourcegitcommit: d74f6d8630783de49667956f39cac67e1968a89d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99473189"
---
# <a name="deploy-an-sdn-infrastructure-using-sdn-express"></a>Déployer une infrastructure SDN avec SDN Express

> S’applique à Azure Stack HCI, version 20H2

Dans cette rubrique, vous déployez une infrastructure SDN (Software Defined Network) de bout en bout à l’aide de scripts PowerShell pour SDN Express. L’infrastructure peut comprendre ces trois composants à haut niveau de disponibilité : un contrôleur de réseau (HA), un équilibreur de charge logiciel (SLB) et une passerelle.  Les scripts prennent en charge un déploiement échelonné, où vous pouvez déployer uniquement le contrôleur de réseau pour bénéficier d’un ensemble de fonctionnalités de base avec une configuration réseau minimale. 

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

## <a name="requirements"></a>Configuration requise

Pour un déploiement SDN, les conditions suivantes doivent être remplies :

- Hyper-V doit être activé sur tous les serveurs hôtes
- Tous les serveurs hôtes doivent être joints à Active Directory
- Un commutateur virtuel doit être créé
- Le réseau physique doit être configuré pour les sous-réseaux et les réseaux VLAN qui sont définis dans le fichier de configuration.
- Le fichier VHDX spécifié dans le fichier de configuration doit être accessible à partir de l’ordinateur de déploiement sur lequel est exécuté le script SDN Express.

## <a name="create-the-vdx-file"></a>Créer le fichier VDX

SDN utilise un fichier VHDX qui contient le système d’exploitation Azure Stack HCI utilisé comme source de création des machines virtuelles SDN. La version du système d’exploitation de votre fichier VHDX doit correspondre à celle utilisée par les hôtes Hyper-V.

Si vous avez téléchargé et installé le système d’exploitation Azure Stack HCI à partir d’une image ISO, vous pouvez créer ce fichier VHDX à l’aide de l’utilitaire `Convert-WindowsImage`, comme décrit dans [Centre de scripts](https://gallery.technet.microsoft.com/scriptcenter/Convert-WindowsImageps1-0fe23a8f).

L’exemple suivant utilise `Convert-WindowsImage` :

 ```powershell
$wimpath = "d:\sources\install.wim"
$vhdpath = "c:\temp\WindowsServerDatacenter.vhdx"
$Edition = 4   # 4 = Full Desktop, 3 = Server Core

import-module ".\convert-windowsimage.ps1"

Convert-WindowsImage -SourcePath $wimpath -Edition $Edition -VHDPath $vhdpath -SizeBytes 500GB -DiskLayout UEFI
```

## <a name="download-the-github-repository"></a>Télécharger le dépôt GitHub

Les fichiers de script SDN Express se trouvent dans GitHub. La première étape consiste à obtenir les fichiers et dossiers nécessaires sur votre ordinateur de déploiement.

1. Accédez au dépôt GitHub [SDN Express](https://github.com/microsoft/SDN).

1. Téléchargez les fichiers du dépôt sur votre ordinateur de déploiement désigné. Sélectionnez **Cloner** ou **Télécharger le fichier ZIP**.

    > [!NOTE]
    > L’ordinateur de déploiement désigné doit exécuter Windows Server 2016 ou une version ultérieure.

1. Extrayez le fichier ZIP et copiez le dossier `SDNExpress` dans le dossier `C:\` sur l’ordinateur de déploiement.

## <a name="edit-the-configuration-file"></a>Modifier le fichier de configuration

Le fichier de données de configuration `MultiNodeSampleConfig.psd1` de PowerShell contient tous les paramètres dont a besoin le script SDN Express comme entrée pour les divers paramètres de configuration. Ce fichier fournit des informations spécifiques sur les valeurs à renseigner selon que vous déployez uniquement le composant contrôleur de réseau, ou également les composants équilibreur de charge logiciel et passerelle.

Pour plus d’informations, consultez [Planifier une infrastructure de réseau défini par logiciel](../concepts/plan-software-defined-networking-infrastructure.md).

Commençons !

1. Accédez au dossier `C:\SDNExpress\scripts`.

1. Ouvrez le fichier `MultiNodeSampleConfig.psd1` dans l’éditeur de texte de votre choix.

1. Changez certaines valeurs de paramètre pour les adapter à votre infrastructure.

## <a name="run-the-deployment-script"></a>Exécuter le script de déploiement

Le script SDN Express déploie votre infrastructure SDN. Une fois l’exécution du script terminée, votre infrastructure est prête à être utilisée pour les déploiements de charge de travail.

1. Pour obtenir des informations de dernière minute sur la façon d’exécuter le script de déploiement, consultez le fichier `README.md`.  

1. Exécutez la commande suivante à partir d’un compte d’utilisateur avec des informations d’identification d’administration pour les serveurs hôtes du cluster :

    ```powershell
    SDNExpress\scripts\SDNExpress.ps1 -ConfigurationDataFile MultiNodeSampleConfig.psd1 -Verbose
    ```

1. Une fois les machines virtuelles du contrôleur de réseau créées, configurez les mises à jour DNS dynamiques pour le nom du cluster du contrôleur de réseau sur le serveur DNS. Pour plus d’informations, consultez l’étape 3 dans [Configuration requise pour le déploiement du contrôleur de réseau](/windows-server/networking/sdn/plan/installation-and-preparation-requirements-for-deploying-network-controller#step-3-configure-dynamic-dns-registration-for-network-controller).

## <a name="next-steps"></a>Étapes suivantes

Gérez vos machines virtuelles. Pour plus d’informations, consultez [Gérer les machines virtuelles](../manage/vm.md).