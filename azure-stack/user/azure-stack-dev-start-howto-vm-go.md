---
title: Déployer une application web Go sur une machine virtuelle dans Azure Stack Hub
description: Guide pratique pour déployer une application web Go sur une machine virtuelle dans Azure Stack Hub
author: mattbriggs
ms.topic: overview
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: c81ef85db48cbb45e104666ef1d9974bd56aa3ff
ms.sourcegitcommit: db3c9179916a36be78b43a8a47e1fd414aed3c2e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84146901"
---
# <a name="deploy-a-go-web-app-to-a-vm-in-azure-stack-hub"></a>Déployer une application web Go sur une machine virtuelle dans Azure Stack Hub

Vous pouvez créer une machine virtuelle pour héberger une application web Go dans Azure Stack Hub. Dans cet article, vous installez un serveur et vous le configurez pour qu’il héberge votre application web Go, puis vous déployez l’application sur Azure Stack Hub.

## <a name="create-a-vm"></a>Créer une machine virtuelle

1. Configurez votre machine virtuelle dans Azure Stack Hub en suivant les instructions fournies dans [Déployer une machine virtuelle Linux pour héberger une application web dans Azure Stack Hub](azure-stack-dev-start-howto-deploy-linux.md).

2. Dans le volet du réseau de machines virtuelles, vérifiez que les ports suivants sont accessibles :

    | Port | Protocol | Description |
    | --- | --- | --- |
    | 80 | HTTP | HTTP (Hypertext Transfer Protocol) est le protocole utilisé pour fournir des pages web à partir des serveurs. Les clients se connectent via HTTP avec une adresse IP ou un nom DNS. |
    | 443 | HTTPS | HTTPS (Hypertext Transfer Protocol Secure) est une version sécurisée du protocole HTTP qui nécessite un certificat de sécurité pour la transmission chiffrée des informations. |
    | 22 | SSH | SSH (Secure Shell) est un protocole réseau chiffré pour les communications sécurisées. Vous utilisez cette connexion avec un client SSH pour configurer la machine virtuelle et déployer l’application. |
    | 3389 | RDP | facultatif. Le protocole RDP (Remote Desktop Protocol) permet d’utiliser une connexion Bureau à distance avec une interface graphique utilisateur sur votre machine.   |
    | 3000 | Custom | Port utilisé par le framework web Go dans le développement. Pour un serveur de production, vous routez le trafic par les ports 80 et 443. |

## <a name="install-go"></a>Installer Go

1. Connectez-vous à votre machine virtuelle en utilisant votre client SSH. Pour obtenir des instructions, consultez [Se connecter par SSH avec PuTTy](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

1. À l’invite Bash sur votre machine virtuelle, entrez les commandes suivantes :

    ```bash  
    wget https://dl.google.com/go/go1.10.linux-amd64.tar.gz
    sudo tar -xvf go1.10.linux-amd64.tar.gz
    sudo mv go /usr/local
    ```

2. Configurez l’environnement Go sur votre machine virtuelle. Tout en restant connecté à votre machine virtuelle dans votre session SSH, entrez les commandes suivantes :

    ```bash  
    export GOROOT=/usr/local/go
    export GOPATH=$HOME/Projects/ADMFactory/Golang
    export PATH=$GOPATH/bin:$GOROOT/bin:$PATH

    vi ~/.profile
    ```

3. Validez votre installation. Tout en restant connecté à votre machine virtuelle dans votre session SSH, entrez la commande suivante :

    ```bash  
        go version
    ```

3. [Installez Git](https://git-scm.com), système couramment utilisé pour la gestion du code source et des versions. Tout en restant connecté à votre machine virtuelle dans votre session SSH, entrez la commande suivante :

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="deploy-and-run-the-app"></a>Déployer et exécuter l’application

1. Configurez votre référentiel Git sur la machine virtuelle. Tout en restant connecté à votre machine virtuelle dans votre session SSH, entrez les commandes suivantes :

    ```bash  
       git clone https://github.com/appleboy/go-hello
    
       cd go-hello
       go get -d
    ```

2. Démarrez l’application. Tout en restant connecté à votre machine virtuelle dans votre session SSH, entrez la commande suivante :

    ```bash  
       go run hello-world.go
    ```

3. Accédez au nouveau serveur. Vous devriez voir votre application web en cours d’exécution.

    ```HTTP  
       http://yourhostname.cloudapp.net:3000
    ```

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [développer pour Azure Stack Hub](azure-stack-dev-start.md).
- Découvrez les [déploiements courants pour Azure Stack Hub en IaaS](azure-stack-dev-start-deploy-app.md).
- Pour apprendre le langage de programmation Go et trouver des ressources supplémentaires sur Go, consultez [Golang.org](https://golang.org).
