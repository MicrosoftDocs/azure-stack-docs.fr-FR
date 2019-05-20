---
title: Guide pratique pour déployer une application web Python sur une machine virtuelle dans Azure Stack | Microsoft Docs
description: Déployez une application web Python sur une machine virtuelle dans Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: b1a588dd084962c095a534f6569333b34e694bc6
ms.sourcegitcommit: 41927cb812e6a705d8e414c5f605654da1fc6952
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "64482253"
---
# <a name="how-to-deploy-a-python-web-app-to-a-vm-in-azure-stack"></a>Guide pratique pour déployer une application web Python sur une machine virtuelle dans Azure Stack

Vous pouvez créer une machine virtuelle pour héberger votre application web Python dans Azure Stack. Cet article explique étape par étape comment configurer le serveur, le paramétrer de façon à héberger une application web Python, puis déployer l’application.

Python est un langage de programmation interprété, de haut niveau et à usage général. Créé par Guido van Rossum et diffusé pour la première fois en 1991, Python a une philosophie de conception qui met l’accent sur la lisibilité du code, notamment en rendant l’espace blanc significatif. Il fournit des constructions qui permettent de produire du code clair à petite comme à grande échelle. Pour apprendre le langage de programmation Python et trouver des ressources supplémentaires sur Python, voir [Python.org](https://www.python.org).

Cet article utilise Python 3.x avec Flask dans un environnement virtuel sur un serveur Ngnix.

## <a name="create-a-vm"></a>Créer une machine virtuelle

1. Configurez votre machine virtuelle dans Azure Stack. Suivez les étapes de [Déployer une machine virtuelle Linux pour héberger une application web dans Azure Stack](azure-stack-dev-start-howto-deploy-linux.md).

2. Dans le panneau du réseau machines virtuelles, vérifiez que les ports suivants sont accessibles :

    | Port | Protocole | Description |
    | --- | --- | --- |
    | 80 | HTTP | Le protocole HTTP (Hypertext Transfer Protocol) est un protocole d’application pour les systèmes d’information hypermédias, collaboratifs et distribués. Les clients se connectent à l’application web avec l’adresse IP publique ou le nom DNS de la machine virtuelle. |
    | 443 | HTTPS | Le protocole HTTPS (Hypertext Transfer Protocol Secure) est une extension du protocole HTTP (Hypertext Transfer Protocol). Il sert à sécuriser la communication sur un réseau d’ordinateurs. Les clients se connectent à l’application web avec l’adresse IP publique ou le nom DNS de la machine virtuelle. |
    | 22 | SSH | Le protocole SSH (Secure Shell) est un protocole réseau de chiffrement qui permet d’exploiter en toute sécurité des services réseau sur un réseau non sécurisé. Nous utiliserons cette connexion avec un client SSH pour configurer la machine virtuelle et déployer l’application. |
    | 3389 | RDP | facultatif. Le protocole RDP (Remote Desktop Protocol) permet d’utiliser une connexion Bureau à distance avec une interface graphique utilisateur.   |
    | 5000 et 8000 | Personnalisée | Les ports 5000 et 8000 sont utilisés par l’infrastructure web Flask dans le développement. Pour un serveur de production, le trafic est acheminé par les ports 80 et 443. |

## <a name="install-python"></a>Installation de Python

1. Connectez-vous à votre machine virtuelle en utilisant votre client SSH. Pour obtenir des instructions, voir [Se connecter par SSH avec PuTTy](azure-stack-dev-start-howto-ssh-public-key.md#connect-via-ssh-with-putty).
2. Dans l’invite Bash de votre machine virtuelle, tapez les commandes suivantes :

    ```bash  
    sudo apt-get -y install python3 python3-venv python3-dev
    ```

3. Validez votre installation. Tout en conservant la connexion à votre machine virtuelle dans votre session SSH, tapez les commandes suivantes :

    ```bash  
        python -version
    ```


3. Installez Nginx. [Nginx](https://www.nginx.com/resources/wiki/) est un serveur web qui peut également servir de proxy inverse, d’équilibreur de charge, de proxy de messagerie et de cache HTTP. Tout en conservant la connexion à votre machine virtuelle dans votre session SSH, tapez les commandes suivantes :

    ```bash  
       sudo apt-get -y install nginx git
    ```

4. Installez Git. [Git](https://git-scm.com) est un système très courant de gestion de code source et de contrôle des révisions. Tout en conservant la connexion à votre machine virtuelle dans votre session SSH, tapez les commandes suivantes :

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="deploy-and-run-the-app"></a>Déployer et exécuter l’application

1. Configurez votre référentiel Git sur la machine virtuelle. Tout en conservant la connexion à votre machine virtuelle dans votre session SSH, tapez les commandes suivantes :

    ```bash  
       git clone https://github.com/mattbriggs/flask-hello-world.git
    
       cd flask-hello-world
    ```

2. Créez un environnement virtuel et ajoutez-y toutes les dépendances de package.  Tout en conservant la connexion à votre machine virtuelle dans votre session SSH, tapez les commandes suivantes :

    ```bash  
    python3 -m venv venv
    source venv/bin/activate
    pip install -r requirements.txt
    
    export FLASK_APP=application.py
    # export FLASK_DEBUG=1 
    flask run -h 0.0.0.0
    ```

3.  Accédez maintenant à votre nouveau serveur. Vous devriez voir votre application web en cours d’exécution.

    ```HTTP  
       http://yourhostname.cloudapp.net:5000
    ```

## <a name="update-your-server"></a>Mettre à jour le serveur

1. Connectez-vous à votre machine virtuelle dans votre session SSH. Arrêtez le serveur en tapant `CTRL+C`.
2. Tapez les commandes suivantes :

    ```bash  
    deactivate
    open the git repo
    git pull
    ```

3. Activez l’environnement virtuel et lancez l’application :

    ```bash  
    source venv/bin/activate
    export FLASK_APP=application.py
    flask run -h 0.0.0.0
    ```

## <a name="next-steps"></a>Étapes suivantes

- Apprenez à [Développer dans Azure Stack](azure-stack-dev-start.md).
- Découvrez les [Déploiements courants pour Azure Stack en IaaS](azure-stack-dev-start-deploy-app.md).
