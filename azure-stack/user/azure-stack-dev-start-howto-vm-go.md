---
title: Guide pratique pour déployer une application web Go sur une machine virtuelle dans Azure Stack | Microsoft Docs
description: Guide pratique pour déployer une application web Go sur une machine virtuelle dans Azure Stack
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: c0cef076522e77a6d0fdafbd8848d5e9bb8a90a8
ms.sourcegitcommit: 41927cb812e6a705d8e414c5f605654da1fc6952
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "64482283"
---
# <a name="how-to-deploy-a-go-web-app-to-a-vm-in-azure-stack"></a>Guide pratique pour déployer une application web Go sur une machine virtuelle dans Azure Stack

Vous pouvez créer une machine virtuelle pour héberger votre application web Go dans Azure Stack. Cet article explique étape par étape comment configurer le serveur, le paramétrer de façon à héberger une application web Go, puis déployer l’application.

Go est expressif, concis, épuré et efficace. Ses mécanismes de concurrence facilitent l’écriture de programmes qui tirent le meilleur parti des ordinateurs multicœurs et en réseau, tandis que son système de type permet la construction de programmes flexibles et modulaires. Pour apprendre le langage de programmation Go et trouver des ressources supplémentaires sur Go, voir [Golang.org](https://golang.org).

## <a name="create-a-vm"></a>Créer une machine virtuelle

1. Configurez votre machine virtuelle dans Azure Stack. Suivez les étapes de [Déployer une machine virtuelle Linux pour héberger une application web dans Azure Stack](azure-stack-dev-start-howto-deploy-linux.md).

2. Dans le panneau du réseau machines virtuelles, vérifiez que les ports suivants sont accessibles :

    | Port | Protocole | Description |
    | --- | --- | --- |
    | 80 | HTTP | Le protocole HTTP (Hypertext Transfer Protocol) est un protocole d’application pour les systèmes d’information hypermédias, collaboratifs et distribués. Les clients se connectent à l’application web avec l’adresse IP publique ou le nom DNS de la machine virtuelle. |
    | 443 | HTTPS | Le protocole HTTPS (Hypertext Transfer Protocol Secure) est une extension du protocole HTTP (Hypertext Transfer Protocol). Il sert à sécuriser la communication sur un réseau d’ordinateurs. Les clients se connectent à l’application web avec l’adresse IP publique ou le nom DNS de la machine virtuelle. |
    | 22 | SSH | Le protocole SSH (Secure Shell) est un protocole réseau de chiffrement qui permet d’exploiter en toute sécurité des services réseau sur un réseau non sécurisé. Nous utiliserons cette connexion avec un client SSH pour configurer la machine virtuelle et déployer l’application. |
    | 3389 | RDP | facultatif. Le protocole RDP (Remote Desktop Protocol) permet d’utiliser une connexion Bureau à distance avec une interface graphique utilisateur.   |
    | 3000 | Personnalisée | Le port 3000 est utilisé par l’infrastructure web Go dans le développement. Pour un serveur de production, le trafic est acheminé par les ports 80 et 443. |

## <a name="install-go"></a>Installer Go

1. Connectez-vous à votre machine virtuelle en utilisant votre client SSH. Pour obtenir des instructions, voir [Se connecter par SSH avec PuTTy](azure-stack-dev-start-howto-ssh-public-key.md#connect-via-ssh-with-putty).
1. Dans l’invite Bash de votre machine virtuelle, tapez les commandes suivantes :

    ```bash  
    wget https://dl.google.com/go/go1.10.linux-amd64.tar.gz
    sudo tar -xvf go1.10.linux-amd64.tar.gz
    sudo mv go /usr/local
    ```

2. Configurez l’environnement Go sur votre machine virtuelle. Tout en conservant la connexion à votre machine virtuelle dans votre session SSH, tapez les commandes suivantes :

    ```bash  
    export GOROOT=/usr/local/go
    export GOPATH=$HOME/Projects/ADMFactory/Golang
    export PATH=$GOPATH/bin:$GOROOT/bin:$PATH

    vi ~/.profile
    ```

3. Validez votre installation. Tout en conservant la connexion à votre machine virtuelle dans votre session SSH, tapez les commandes suivantes :

    ```bash  
        go version
    ```

3. Installez Git. [Git](https://git-scm.com) est un système très courant de gestion de code source et de contrôle des révisions. Tout en conservant la connexion à votre machine virtuelle dans votre session SSH, tapez les commandes suivantes :

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="deploy-and-run-the-app"></a>Déployer et exécuter l’application

1. Configurez votre référentiel Git sur la machine virtuelle. Tout en conservant la connexion à votre machine virtuelle dans votre session SSH, tapez les commandes suivantes :

    ```bash  
       git clone https://github.com/appleboy/go-hello
    
       cd go-hello
       go get -d
    ```

2. Démarrez l’application. Tout en conservant la connexion à votre machine virtuelle dans votre session SSH, tapez la commande suivante :

    ```bash  
       go run hello-world.go
    ```

3.  Accédez maintenant à votre nouveau serveur. Vous devriez voir votre application web en cours d’exécution.

    ```HTTP  
       http://yourhostname.cloudapp.net:3000
    ```

## <a name="next-steps"></a>Étapes suivantes

- Apprenez à [Développer dans Azure Stack](azure-stack-dev-start.md).
- Découvrez les [Déploiements courants pour Azure Stack en IaaS](azure-stack-dev-start-deploy-app.md).