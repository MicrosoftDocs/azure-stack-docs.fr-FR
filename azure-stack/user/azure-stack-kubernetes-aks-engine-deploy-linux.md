---
title: Installer le moteur AKS sur Linux dans Azure Stack Hub
description: Découvrez comment utiliser une machine Linux dans votre infrastructure Azure Stack Hub pour héberger le moteur AKS afin de déployer et de gérer un cluster Kubernetes.
author: mattbriggs
ms.topic: article
ms.date: 09/16/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/16/2020
ms.openlocfilehash: 49855cbe926dc29b20c3309e4fc3a084ee5a5e70
ms.sourcegitcommit: 719569bb9e3f9924494a9229b4f2d211ae3f4f74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90717959"
---
# <a name="install-the-aks-engine-on-linux-in-azure-stack-hub"></a>Installer le moteur AKS sur Linux dans Azure Stack Hub

Vous pouvez utiliser une machine Linux dans votre infrastructure Azure Stack Hub pour héberger le moteur AKS afin de déployer et de gérer un cluster Kubernetes. Dans cet article, nous nous intéressons à la préparation de la machine virtuelle cliente en vue de gérer votre cluster pour des instances Azure Stack Hub à la fois connectées et déconnectées, à la vérification de l’installation et à la configuration de la machine virtuelle cliente sur le Kit de développement Azure Stack (ASDK).

## <a name="prepare-the-client-vm"></a>Préparer la machine virtuelle cliente

Le moteur AKS est un outil en ligne de commande utilisé pour déployer et gérer votre cluster Kubernetes. Vous pouvez exécuter le moteur sur une machine de votre infrastructure Azure Stack Hub. À partir de cette machine, vous allez exécuter le moteur AKS pour déployer les ressources et logiciels IaaS nécessaires à l’exécution de votre cluster. Vous pouvez ensuite utiliser la machine qui exécute le moteur pour effectuer des tâches de gestion sur votre cluster.

Pour choisir votre machine cliente, tenez compte des points suivants :

1. La nécessité d’une reprise d’activité de la machine cliente en cas de sinistre.
2. La façon dont vous allez vous connecter à la machine cliente et dont la machine interagit avec votre cluster.

## <a name="install-in-a-connected-environment"></a>Installation dans un environnement connecté

Vous pouvez installer la machine virtuelle cliente pour gérer votre cluster Kubernetes sur une infrastructure Azure Stack Hub connectée à Internet.

1. Créez une machine virtuelle Linux dans votre infrastructure Azure Stack Hub. Pour obtenir des instructions, consultez [Démarrage rapide : Créez une machine virtuelle serveur Linux sur le portail Azure Stack Hub](./azure-stack-quick-linux-portal.md).
2. Connectez-vous à votre machine virtuelle.
3. Recherchez la version du moteur AKS dans le tableau [Versions de Kubernetes prises en charge](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-aks-engine-versions). L’image de base AKS doit être disponible dans votre Place de marché Azure Stack Hub. Lors de l’exécution de la commande, vous devez spécifier la version `--version v0.55.4`. Si vous ne spécifiez pas la version, la commande installe la version la plus récente, qui nécessite peut-être une image de disque dur virtuel non disponible dans votre place de marché.
4. Exécutez la commande suivante :

    ```bash  
        curl -o get-akse.sh https://raw.githubusercontent.com/Azure/aks-engine/master/scripts/get-akse.sh
        chmod 700 get-akse.sh
        ./get-akse.sh --version 0.55.4
    ```

    > [!NOTE]  
    > Si votre méthode d’installation échoue, vous pouvez essayer de suivre les étapes indiquées dans l’[environnement déconnecté](#install-in-a-disconnected-environment) ou [essayer GoFish](azure-stack-kubernetes-aks-engine-troubleshoot.md#try-gofish), un autre gestionnaire de package.

## <a name="install-in-a-disconnected-environment"></a>Installation dans un environnement déconnecté

Vous pouvez installer la machine virtuelle cliente pour gérer votre cluster Kubernetes sur une infrastructure Azure Stack Hub déconnectée d’Internet.

1.  À partir d’une machine disposant d’un accès à Internet, accédez à GitHub [Azure/aks-engine](https://github.com/Azure/aks-engine/releases/latest). Téléchargez une archive (*.tar.gz) pour une machine Linux, par exemple, `aks-engine-v0.xx.x-linux-amd64.tar.gz`.

2.  Créez un compte de stockage dans votre instance Azure Stack Hub pour charger le fichier d’archive (*.tar.gz) avec le binaire du moteur AKS. Pour obtenir des instructions sur l’utilisation d’Explorateur Stockage Azure, consultez [Explorateur Stockage Azure avec Azure Stack Hub](./azure-stack-storage-connect-se.md).

3. Créez une machine virtuelle Linux dans votre infrastructure Azure Stack Hub. Pour obtenir des instructions, consultez [Démarrage rapide : Créez une machine virtuelle serveur Linux sur le portail Azure Stack Hub](./azure-stack-quick-linux-portal.md).

3.  À partir de l’URL de l’objet blob du compte de stockage Azure Stack Hub où vous avez chargé le fichier d’archive (*.tar.gz), téléchargez le fichier sur votre machine virtuelle de gestion. Extrayez l’archive dans le répertoire `/usr/local/bin`.

4. Connectez-vous à votre machine virtuelle.

5.  Exécutez la commande suivante :

    ```bash  
    curl -o aks-engine-v0.xx.x-linux-amd64.tar.gz <httpurl/aks-engine-v0.xx.x-linux-amd64.tar.gz>
    tar xvzf aks-engine-v0.xx.x-linux-amd64.tar.gz -C /usr/local/bin
    ```

## <a name="verify-the-installation"></a>Vérifier l’installation

Une fois que votre machine virtuelle cliente est configurée, vérifiez que vous avez installé le moteur AKS.

1. Connectez-vous à votre machine virtuelle cliente.
2. Exécutez la commande suivante :

   ```bash  
   aks-engine version
   ```

3. Si le point de terminaison Azure Resource Manager utilise un certificat auto-signé, vous devez ajouter explicitement le certificat racine au magasin de certificats approuvé de la machine. Le certificat racine se trouve dans le répertoire suivant sur la machine virtuelle : /var/lib/waagent/Certificates.pem. Copiez le fichier de certificat à l’aide de la commande suivante : 

   ```bash
   sudo cp /var/lib/waagent/Certificates.pem /usr/local/share/ca-certificates/azurestackca.crt 
   sudo update-ca-certificates
   ```

Si vous ne parvenez pas à vérifier si vous avez installé le moteur AKS sur votre machine virtuelle cliente, consultez [Résoudre les problèmes d’installation du moteur AKS](azure-stack-kubernetes-aks-engine-troubleshoot.md).


## <a name="asdk-installation"></a>Installation du Kit de développement Azure Stack (ASDK)

Vous devrez ajouter un certificat lors de l’exécution de la machine virtuelle cliente pour le moteur AKS sur le Kit de développement Azure Stack (ASDK).

Quand vous utilisez un ASDK et que votre point de terminaison Azure Resource Manager utilise un certificat auto-signé, vous devez ajouter explicitement ce certificat au magasin de certificats approuvé de la machine. Vous pouvez trouver le certificat racine ASDK sur toutes les machines virtuelles que vous déployez sur l’ASDK. Par exemple, sur une machine virtuelle Ubuntu, vous le trouverez dans ce répertoire `/var/lib/waagent/Certificates.pem`. 

Copiez le fichier de certificat à l’aide de la commande suivante :

```bash
sudo cp /var/lib/waagent/Certificates.pem /usr/local/share/ca-certificates/azurestackca.crt

sudo update-ca-certificates
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Déployer un cluster Kubernetes avec le moteur AKS sur Azure Stack Hub](azure-stack-kubernetes-aks-engine-deploy-cluster.md)
