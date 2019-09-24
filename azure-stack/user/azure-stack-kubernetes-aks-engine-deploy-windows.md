---
title: Déployer le moteur AKS sur Windows dans Azure Stack | Microsoft Docs
description: Découvrez comment utiliser une machine Windows dans votre infrastructure Azure Stack pour héberger le moteur AKS afin de déployer et de gérer un cluster Kubernetes.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na (Kubernetes)
ms.devlang: nav
ms.topic: article
ms.date: 09/14/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/14/2019
ms.openlocfilehash: b7057acd34625ffdea1de9ed533bf608de3059d6
ms.sourcegitcommit: 09d14eb77a43fd585e7e6be93c32fa427770adb6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/16/2019
ms.locfileid: "71019407"
---
# <a name="install-the-aks-engine-on-windows-in-azure-stack"></a>Installer le moteur AKS sur Windows dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Vous pouvez utiliser une machine Windows dans votre infrastructure Azure Stack pour héberger le moteur AKS afin de déployer et de gérer un cluster Kubernetes. Dans cet article, nous nous intéressons à la préparation de la machine virtuelle cliente en vue de gérer votre cluster pour des instances Azure Stack à la fois connectées et déconnectées, à la vérification de l’installation et à la configuration de la machine virtuelle cliente sur le Kit de développement Azure Stack (ASDK).

## <a name="prepare-the-client-vm"></a>Préparer la machine virtuelle cliente

Le moteur AKS est un outil en ligne de commande utilisé pour déployer et gérer votre cluster Kubernetes. Vous pouvez exécuter le moteur sur une machine de votre infrastructure Azure Stack. À partir de cette machine, vous allez exécuter le moteur AKS pour déployer les ressources et logiciels IaaS nécessaires à l’exécution de votre cluster. Vous pouvez ensuite utiliser la machine qui exécute le moteur pour effectuer des tâches de gestion sur votre cluster.

Pour choisir votre machine cliente, tenez compte des points suivants :

1. La nécessité d’une reprise d’activité de la machine cliente en cas de sinistre.
3. La façon dont vous allez vous connecter à la machine cliente et dont la machine interagit avec votre cluster.

## <a name="install-in-a-connected-environment"></a>Installation dans un environnement connecté

Vous pouvez installer la machine virtuelle cliente pour gérer votre cluster Kubernetes sur une infrastructure Azure Stack connectée à Internet.

1. Créez une machine virtuelle Windows dans votre infrastructure Azure Stack. Pour obtenir des instructions, consultez [Démarrage rapide : Créer une machine virtuelle serveur Windows sur le portail Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-windows-portal).
2. Connectez-vous à votre machine virtuelle.
3. [Installez Chocolatey en suivant les instructions PowerShell](https://chocolatey.org/install#install-with-powershellexe). 

    Selon le site web de Chocolatey : Chocolatey est un gestionnaire de package pour Windows, comme apt-get ou yum mais pour Windows. Il a été conçu pour servir de framework décentralisé afin d’installer rapidement les applications et outils dont vous avez besoin. Il s’appuie sur l’infrastructure NuGet qui utilise actuellement PowerShell en priorité pour remettre les packages des distributions à votre porte, erreur, ordinateur.
4. Recherchez la version du moteur AKS dans le tableau [Versions de Kubernetes prises en charge](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions). Le moteur de base AKS doit être disponible dans votre Place de marché Azure Stack. Lors de l’exécution de la commande, vous devez spécifier la version, par exemple, `--version v0.39.0`. Si vous ne spécifiez pas la version, la commande installe la version la plus récente, qui n’est peut-être pas disponible dans votre place de marché.
5. Exécutez la commande suivante à partir d’une invite avec élévation de privilèges et incluez le numéro de version :

    ```PowerShell  
        choco install aks-engine --version v0.0.0 -y
    ```

> [!Note]  
> Si cette méthode d’installation échoue, vous pouvez essayer de suivre les étapes indiquées dans l’[environnement déconnecté](#install-in-a-disconnected-environment) ou [essayer GoFish](azure-stack-kubernetes-aks-engine-troubleshoot.md#try-gofish), un autre gestionnaire de package.

## <a name="install-in-a-disconnected-environment"></a>Installation dans un environnement déconnecté

Vous pouvez installer la machine virtuelle cliente pour gérer votre cluster Kubernetes sur une infrastructure Azure Stack déconnectée d’Internet.

1.  À partir d’une machine disposant d’un accès à Internet, accédez à GitHub [Azure/aks-engine](https://github.com/Azure/aks-engine/releases/latest). Téléchargez une archive (*.tar.gz) pour une machine Windows, par exemple, `aks-engine-v0.38.8-windows-amd64.tar.gz`.

2.  Créez un compte de stockage dans votre instance Azure Stack pour charger le fichier d’archive (*.tar.gz) avec le binaire du moteur AKS. Pour obtenir des instructions sur l’utilisation d’Explorateur Stockage Azure, consultez [Explorateur Stockage Azure avec Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-connect-se).

3. Créez une machine virtuelle Windows dans votre infrastructure Azure Stack. Pour obtenir des instructions, consultez [Démarrage rapide : Créer une machine virtuelle serveur Windows sur le portail Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-windows-portal)

4.  À partir de l’URL de l’objet blob du compte de stockage Azure Stack où vous avez chargé le fichier d’archive (*.tar.gz), téléchargez le fichier sur votre machine virtuelle de gestion. Extrayez l’archive dans un répertoire auquel vous avez accès à partir de votre invite de commandes.

5. Connectez-vous à votre machine virtuelle.

6. [Installez Chocolatey en suivant les instructions PowerShell](https://chocolatey.org/install#install-with-powershellexe). 

7.  Exécutez la commande suivante à partir d’une invite de commandes avec élévation de privilèges. Incluez le numéro de version approprié :

    ```PowerShell  
        choco install aks-engine --version v0.0.0 -y
    ```

## <a name="verify-the-installation"></a>Vérifier l’installation

Une fois que votre machine virtuelle cliente est configurée, vérifiez que vous avez installé le moteur AKS.

1. Connectez-vous à votre machine virtuelle cliente.
2. Exécutez la commande suivante :

    ```PowerShell  
    aks-engine version
    ```

Si vous ne parvenez pas à vérifier si vous avez installé le moteur AKS sur votre machine virtuelle cliente, consultez [Résoudre les problèmes d’installation du moteur AKS](azure-stack-kubernetes-aks-engine-troubleshoot.md).


## <a name="asdk-installation"></a>Installation du Kit de développement Azure Stack (ASDK)

Vous devrez ajouter un certificat, lors de l’exécution de la machine virtuelle cliente pour le moteur AKS sur le Kit de développement Azure Stack (ASDK), sur une machine en dehors de l’ASDK. Si vous utilisez une machine virtuelle Windows au sein de l’environnement ASDK proprement dit, celle-ci approuve déjà le certificat ASDK. Si votre machine cliente se trouve en dehors de l’ASDK, vous devez extraire le certificat de l’ASDK, puis l’ajouter à votre machine Windows.

Quand vous utilisez un ASDK et que votre point de terminaison Azure Resource Manager utilise un certificat auto-signé, vous devez ajouter explicitement ce certificat au magasin de certificats approuvé de la machine. Vous pouvez trouver le certificat racine ASDK sur toutes les machines virtuelles que vous déployez sur l’ASDK.

1. Exportez le certificat racine de l’autorité de certification. Pour obtenir des instructions, consultez [Exporter le certificat racine de l’autorité de certification Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles-azurecli2#export-the-azure-stack-ca-root-certificate).
2. Approuvez le certificat racine de l’autorité de certification Azure Stack. Pour obtenir des instructions, consultez [Approuver le certificat racine de l’autorité de certification Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles-azurecli2#trust-the-azure-stack-ca-root-certificate).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Déployer un cluster Kubernetes avec le moteur AKS sur Azure Stack](azure-stack-kubernetes-aks-engine-deploy-cluster.md)