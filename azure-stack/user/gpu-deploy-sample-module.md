---
title: Déployer un module IoT compatible GPU (Graphic Processing Unit) sur Azure Stack Hub
description: Procédure de déploiement d’un module IoT compatible GPU (Graphic Processing Unit) sur Azure Stack Hub
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/13/2020
ms.reviewer: gara
ms.lastreviewed: 11/13/2020
ms.openlocfilehash: 7a5a29c48533874cf2c859780ab0332b8410811f
ms.sourcegitcommit: 8526f642ef859b0006c3991d966f93608a87288a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2021
ms.locfileid: "98243474"
---
# <a name="deploy-a-gpu-enabled-iot-module-on-azure-stack-hub"></a>Déployer un module IoT compatible GPU sur Azure Stack Hub

Azure Stack Hub compatible GPU vous permet de déployer des modules nécessitant d’importantes ressources processeur sur des appareils Linux s’exécutant sur IoT Edge. Les tailles de machine virtuelle au GPU optimisé sont des machines virtuelles spécialisées disponibles avec des GPU NVIDIA uniques ou multiples. Dans cet article, découvrez comment utiliser des machines virtuelles au GPU optimisé pour exécuter des charges de travail intensives en termes de calcul, de graphiques et de visualisation.

Avant de commencer, vous aurez besoin d’un abonnement Azure Active Directory (Azure AD) avec un accès à Azure global et à Azure Stack Hub, un Azure Container Registry (ACR) et un hub IoT.

Dans cet article, vous découvrirez comment :
  - Installer une machine virtuelle Linux compatible GPU et les pilotes appropriés.
  - Installer Docker et activer le GPU dans le runtime.
  - Connecter votre appareil IoT à votre instance IoT Hub et, à partir de la place de marché IoT, installer le modèle : `Getting started with GPUs`.
  - Installer et surveiller votre appareil depuis un ordinateur local à l’aide de l’explorateur Azure IoT.
  - Installer et surveiller votre appareil à l’aide de l’extension Azure IoT dans Visual Studio Code.

## <a name="prerequisites"></a>Prérequis

Vous devrez disposer des ressources suivantes dans votre instance Azure Stack Hub, Azure global et sur votre ordinateur de développement local.

### <a name="azure-stack-hub-and-azure"></a>Azure Stack Hub et Azure

  - Un abonnement en tant qu’utilisateur utilisant Azure Active Directory (Azure AD) dans un système intégré Azure Stack Hub avec un GPU NVIDA. Les puces suivantes fonctionnent avec IoT Hub :
    - NCv3
    - NCas_v4

    Pour plus d’informations sur les GPU sur Azure Stack Hub, consultez [Machine virtuelle de processeur graphique (GPU) sur Azure Stack Hub](gpu-vms-about.md).
  - Un abonnement Azure global. Si vous n’avez pas d’abonnement Azure global, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
- Un [ACR (Azure Container Registry)](/). Notez le serveur de connexion ACR, le nom d’utilisateur et le mot de passe.
-   Un [hub IoT](/azure/iot-hub/iot-hub-create-through-portal) de niveau gratuit ou standard dans Azure global.

### <a name="a-development-machine"></a>Un ordinateur de développement

-   Vous pouvez utiliser votre propre ordinateur ou une machine virtuelle, selon vos préférences de développement. Votre ordinateur de développement doit prendre en charge la virtualisation imbriquée. Cette fonctionnalité est requise pour l’exécution de Docker, le moteur de conteneur utilisé dans cet article.

  - Votre ordinateur de développement devra disposer des ressources suivantes :
      - [Python 3.x](https://www.python.org/downloads/)
      - [Pip](https://pypi.org/project/pip/) pour l’installation des packages Python. Ces ressources sont installées lors de l’installation de Python. Si Pip est installé, vous souhaiterez peut-être procéder à une mise à niveau vers la dernière version. Pour ce faire, vous pouvez utiliser pip. Entrez : `pip install --upgrade pip`.
      - [Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest)
      - [Git](https://git-scm.com/downloads)
      - [Docker](https://docs.docker.com/get-docker/)
      - [Visual Studio Code](https://code.visualstudio.com/)
      - [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) pour Visual Studio Code.
      - [Pack d’extension Python](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python-extension-pack) pour Visual Studio Code

## <a name="register-an-iot-edge-device"></a>Enregistrer un appareil IoT Edge

Utilisez un autre appareil pour héberger votre appareil IoT Edge. L’utilisation d’un appareil distinct permet de séparer votre ordinateur de développement et l’appareil IoT Edge afin de refléter plus précisément un scénario de déploiement. 

Créer un appareil IoT Edge dans Azure avec une machine virtuelle Linux :

1.  [Créez une machine virtuelle serveur Linux de série N](./azure-stack-quick-linux-portal.md) sur Azure Stack Hub. Lorsque vous installez des composants pour votre serveur, vous interagissez avec le serveur via SSH. Pour plus d’informations, consultez [Utiliser une clé publique SSH](./azure-stack-dev-start-howto-ssh-public-key.md).

2.  [Créer et inscrire un appareil IoT Edge](/azure/iot-edge/how-to-register-device)

## <a name="prepare-a-gpu-enabled-vm"></a>Préparer une machine virtuelle compatible GPU

1. Installez les pilotes GPU NVIDA sur votre serveur Linux de série N en suivant les étapes décrites dans l’article [Installer les pilotes GPU NVIDIA sur les machines virtuelles de série N exécutant Linux](/azure/virtual-machines/linux/n-series-driver-setup).

    > [!NOTE]  
    > Vous utiliserez la ligne de commande bash pour installer votre logiciel. Notez les commandes car vous utiliserez les mêmes commandes pour installer les pilotes sur le conteneur s’exécutant dans Docker sur votre machine virtuelle compatible GPU.

2.  Installez le dernier runtime IoT Edge sur votre serveur Linux de série N dans Azure Stack Hub. Pour obtenir des instructions, consultez [Installer le runtime Azure IoT Edge sur des systèmes Linux Debian](/azure/iot-edge/how-to-install-iot-edge-linux#install-the-latest-runtime-version).

## <a name="install-docker"></a>Installer Docker

Installez Docker sur votre machine virtuelle compatible GPU. Vous exécuterez le module à partir de la Place de marché IoT Edge dans un conteneur sur la machine virtuelle.

Vous devez installer Docker 19.02 ou une version ultérieure. Le runtime Docker prend désormais en charge les GPU NVIDIA. Pour en savoir plus sur les GPU dans Docker, consultez l’article [Options de runtime avec mémoire, UC et GPU](https://docs.docker.com/config/containers/resource_constraints/#gpu) de la documentation Docker.

### <a name="install-docker"></a>Installer Docker

Les conteneurs Docker peuvent s’exécuter n’importe où, en local dans le centre de données client, dans un fournisseur de services externe ou dans le cloud, sur Azure. Les conteneurs d’images Docker peuvent s’exécuter en mode natif sur Linux et Windows. Toutefois, les images Windows peuvent s’exécuter uniquement sur des hôtes Windows et les images Linux peuvent s’exécuter sur des hôtes Linux et des hôtes Windows (à l’aide d’une machine virtuelle Linux Hyper-V, jusqu’à présent), où le terme « hôte » désigne un serveur ou une machine virtuelle. Pour plus d’informations, consultez [Qu’est-ce que Docker ?](/dotnet/architecture/microservices/container-docker-introduction/docker-defined).

1. Connectez-vous à votre serveur Linux de série N à l’aide de votre client SSH.

1.  Mettez à jour l’index et les listes apt.

    ```bash  
    sudo apt-get update
    ```

1.  Récupérez les nouvelles versions des packages existants sur la machine.

    ```bash  
    sudo apt-get upgrade
    ```

2.  Installez les dépendances requises pour ajouter le référentiel apt Docker.

    ```bash  
    sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
    ```

3.  Ajoutez la clé GPG Docker.

    ```bash  
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```

4.  Ajoutez le référentiel apt Docker.

    ```bash  
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    ```

5.  Mettez à jour l’index et les listes apt, puis installez Docker Community Edition.

    ```bash  
    sudo apt-get update 
    sudo apt-get install docker-ce
    ```

6.  Vérifiez l’installation en examinant la version Docker.

    ```bash  
    docker -v
    ```

7. Exposez les ressources GPU disponibles dans Docker.

    ```bash
    docker run -it --rm --gpus all ubuntu nvidia-smi
    ```

## <a name="get-the-item-from-the-marketplace"></a>Accéder à l'élément à partir de la Place de marché

Revenez au portail Azure et ajoutez le modèle à votre périphérique à l’aide de la Place de marché IoT. Sélectionnez **Module de la Place de marché** dans le menu. Recherchez `Getting started with GPUs` et suivez les instructions pour ajouter le module.

Pour obtenir des instructions, consultez [Sélectionner un appareil et ajouter des modules](/azure/iot-edge/how-to-deploy-modules-portal#select-device-and-add-modules).

## <a name="enable-monitoring"></a>Activer la supervision

1. Téléchargez l’[explorateur Azure IoT](/azure/iot-pnp/howto-use-iot-explorer) et connectez l’application à votre instance IoT Hub.

2. Sélectionnez votre appareil IoT et accédez à Télémétrie dans le menu de navigation.

3. Sélectionnez **Démarrer** pour surveiller la sortie de l’appareil IoT Edge.

![Installation valide](media/gpu-deploy-sample-module/user-azure-iot-explorer-gpu.png)

## <a name="monitor-the-module-optional"></a>Surveiller la sortie du module (facultatif)  

1. Dans la palette de commandes VS Code, exécutez **Azure IoT Hub: Select IoT Hub** (Azure IoT Hub : Sélectionner l’IoT Hub).

2. Choisissez l’abonnement et l’IoT Hub qui contiennent l’appareil IoT Edge que vous souhaitez configurer. Dans ce cas, sélectionnez l’abonnement utilisé pour déployer l’appareil Azure Stack Edge, puis sélectionnez l’appareil IoT Edge créé pour votre appareil Azure Stack Edge. Cela se produit lorsque vous avez configuré le calcul via le portail Azure dans les étapes précédentes.

3. Dans l’Explorateur VS Code, développez la section Appareils Azure IoT Hub. Sous **Appareils**, vous devez voir l’appareil IoT Edge correspondant à votre appareil Azure Stack Edge. 

    1. Sélectionnez cet appareil, cliquez dessus avec le bouton droit, puis sélectionnez **Démarrer la supervision du point de terminaison d’événement intégré**.

    2. Accédez à **Appareils > Modules**. Vous devriez voir votre **module GPU** en cours d’exécution.

    3. Le terminal VS Code doit également afficher les événements IoT Hub en tant que sortie de supervision pour votre appareil Azure Stack Edge.

    ![une installation valide est](media/gpu-deploy-sample-module/gpu-monitor-events-output.png)

    Vous pouvez voir que le temps nécessaire pour exécuter le même ensemble d’opérations (5 000 itérations de transformation de forme) par GPU est sensiblement inférieur à celui dont a besoin un processeur.

## <a name="next-steps"></a>Étapes suivantes

  - En savoir plus sur la [Machine virtuelle du processeur graphique (GPU) sur Azure Stack Hub](gpu-vms-about.md)

  - En savoir plus sur Azure Stack Hub, Data Box Edge et la périphérie intelligente, [L’avenir de l’informatique : cloud intelligent et périphérie intelligente](https://azure.microsoft.com/overview/future-of-cloud)

  - En savoir plus sur les applications de cloud hybride, voir [Solutions de cloud hybride](/hybrid/app-solutions/)