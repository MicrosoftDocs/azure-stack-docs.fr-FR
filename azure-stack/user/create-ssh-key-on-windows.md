---
title: Déployer un cluster Kubernetes sur un réseau virtuel personnalisé sur Azure Stack Hub
description: Découvrez comment déployer un cluster Kubernetes sur un réseau virtuel personnalisé sur Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 2/28/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 2/28/2020
ms.openlocfilehash: e82ddb48b3858acdf25163976854f538400da54b
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "80069201"
---
# <a name="create-an-ssh-key-for-linux-on-azure-stack-hub"></a>Créer une clé SSH pour Linux sur Azure Stack Hub

Vous pouvez créer une clé SSH pour votre machine Linux sur une machine Windows. Utilisez la clé publique générée par les étapes décrites dans cet article pour l’authentification SSH auprès de machines virtuelles. Si vous utilisez une machine Windows, installez Ubuntu sur Windows pour obtenir un terminal avec des utilitaires comme bash, ssh, git. Exécutez **ssh-keygen** pour créer votre clé.

## <a name="open-bash-on-windows"></a>Ouvrir bash sur Windows

1. Si le Sous-système Windows pour Linux n’est pas installé sur votre machine, installez [Ubuntu sur Windows](https://www.microsoft.com/en-us/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab).  
    Pour plus d’informations sur l’utilisation du Sous-système Windows pour Linux, consultez la [documentation du Sous-système Windows pour Linux](https://docs.microsoft.com/windows/wsl/about).

2. Tapez **Ubuntu** dans votre barre d’outils et sélectionnez **Ouvrir**.

## <a name="create-a-key-with-ssh-keygen"></a>Créer une clé avec ssh-keygen

1. Tapez la commande suivante à partir de votre invite bash :

    ```bash  
    ssh-keygen -t rsa
    ```

    Bash affiche l’invite suivante :

    ```bash
    Generating public/private rsa key pair.
    Enter file in which to save the key (/home/username/.ssh/id_rsa):
    ```

2. Tapez le nom de fichier et la phrase secrète. Retapez la phrase secrète.

    Bash affiche ceci :

    ```bash
    Generating public/private rsa key pair.
    Enter file in which to save the key (/home/user/.ssh/id_rsa): key.txt
    Enter passphrase (empty for no passphrase):
    Enter same passphrase again:
    Your identification has been saved in key.txt.
    Your public key has been saved in key.txt.pub.
    The key fingerprint is:
    SHA256:xanotrealoN6z1/KChqeah0CYVeyhL50/0rq37qgy6Ik username@machine
    The key's randomart image is:
    +---[RSA 2048]----+
    |   o.     .      |
    |  . o.   +       |
    | + o .+ o o      |
    |o o .  O +       |
    | . o .o S .      |
    |  o +. .         |
    |.  o +..o. .     |
    |= . ooB +o+ .    |
    |E=..*X=*.. +.    |
    +----[SHA256]-----+
    ```

3. Pour afficher la clé SSH publique :

    ```bash
    cat /home/<username>/<filename>
    ```

    Bash affiche quelque chose comme ceci :

    ```bash
    ssh-rsa AAAAB3NzaC1ycTHISISANEXAMPLEDITqEJRNrf6tXy9c0vKnMhiol1BFzHFV3
    +suXk6NDeFcA9uI58VdD/CuvG826R+3OPnXutDdl2MLyH3DGG1fJAHObUWQxmDWluhSGb
    JMHiw2L9Wnf9klG6+qWLuZgjB3TQdus8sZI8YdB4EOIuftpMQ1zkAJRAilY0p4QxHhKbU
    IkvWqBNR+rd5FcQx33apIrB4LMkjd+RpDKOTuSL2qIM2+szhdL5Vp5Y6Z1Ut1EpOrkbg1
    cVw7oW0eP3ROPdyNqnbi9m1UVzB99aoNXaepmYviwJGMzXsTkiMmi8Qq+F8/qy7i4Jxl0
    aignia880qOtQrvNEvyhgZOM5oDhgE3IJ username@machine
    ```

4. Copiez le texte `ssh-rsa [...]` jusqu’à `username@machinename`. Veillez à ce que le texte n’inclut pas de retours chariot. Vous pouvez utiliser ce texte quand vous créez votre machine virtuelle ou cluster Kubernetes à l’aide du moteur AKS.

5. Si vous utilisez une machine Windows, vous pouvez accéder à vos fichiers Linux à l’aide de **\\\\wsl$** .

    1. Tapez `\\wsl$` dans votre barre d’outils. La fenêtre par défaut de votre distribution s’ouvre.

    2. Accédez à `\\wsl$\Ubuntu\home\<username>` et recherchez la clé publique et privée, puis enregistrez-la à un emplacement sécurisé.

## <a name="next-steps"></a>Étapes suivantes

- [Déployer un cluster Kubernetes avec le moteur AKS sur Azure Stack Hub](azure-stack-kubernetes-aks-engine-deploy-cluster.md)
- [Démarrage rapide : Créer une machine virtuelle serveur Linux sur le portail Azure Stack Hub](azure-stack-quick-linux-portal.md)