---
title: Déployer une application web Python sur une machine virtuelle dans Azure Stack | Microsoft Docs
description: Déployez une application web Python sur une machine virtuelle dans Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: f37e963ad73a361f9d4cd5a6e68ec4213d5f32fb
ms.sourcegitcommit: 05a16552569fae342896b6300514c656c1df3c4e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65838324"
---
# <a name="deploy-a-python-web-app-to-a-vm-in-azure-stack"></a>Déployer une application web Python sur une machine virtuelle dans Azure Stack

Vous pouvez créer une machine virtuelle pour héberger votre application web Python dans Azure Stack. Dans cet article, vous installez un serveur et vous le configurez pour qu’il héberge votre application web Python, puis vous déployez l’application sur Azure Stack.

Cet article utilise Python 3.x avec Flask dans un environnement virtuel sur un serveur Nginx.

## <a name="create-a-vm"></a>Créer une machine virtuelle

1. Configurez votre machine virtuelle dans Azure Stack en suivant les instructions fournies dans [Déployer une machine virtuelle Linux pour héberger une application web dans Azure Stack](azure-stack-dev-start-howto-deploy-linux.md).

2. Dans le volet du réseau de machines virtuelles, vérifiez que les ports suivants sont accessibles :

    | Port | Protocole | Description |
    | --- | --- | --- |
    | 80 | HTTP | HTTP (Hypertext Transfer Protocol) est le protocole utilisé pour fournir des pages web à partir des serveurs. Les clients se connectent via HTTP avec une adresse IP ou un nom DNS. |
    | 443 | HTTPS | HTTPS (Hypertext Transfer Protocol Secure) est une version sécurisée du protocole HTTP qui nécessite un certificat de sécurité pour la transmission chiffrée des informations. |
    | 22 | SSH | SSH (Secure Shell) est un protocole réseau chiffré pour les communications sécurisées. Vous utilisez cette connexion avec un client SSH pour configurer la machine virtuelle et déployer l’application. |
    | 3389 | RDP | facultatif. Le protocole RDP (Remote Desktop Protocol) permet d’utiliser une connexion Bureau à distance avec une interface graphique utilisateur sur votre machine.   |
    | 5000 et 8000 | Personnalisée | Ports utilisés par le framework web Flask dans le développement. Pour un serveur de production, vous routez le trafic par les ports 80 et 443. |

## <a name="install-python"></a>Installation de Python

1. Connectez-vous à votre machine virtuelle en utilisant votre client SSH. Pour obtenir des instructions, voir [Se connecter par SSH avec PuTTy](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).
2. À l’invite Bash sur votre machine virtuelle, entrez la commande suivante :

    ```bash  
    sudo apt-get -y install python3 python3-venv python3-dev
    ```

3. Validez votre installation. Tout en restant connecté à votre machine virtuelle dans votre session SSH, entrez la commande suivante :

    ```bash  
        python -version
    ```

3. [Installez Nginx](https://www.nginx.com/resources/wiki/), un serveur web léger. Tout en restant connecté à votre machine virtuelle dans votre session SSH, entrez la commande suivante :

    ```bash  
       sudo apt-get -y install nginx git
    ```

4. [Installez Git](https://git-scm.com), système couramment utilisé pour la gestion du code source et des versions. Tout en restant connecté à votre machine virtuelle dans votre session SSH, entrez la commande suivante :

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="deploy-and-run-the-app"></a>Déployer et exécuter l’application

1. Configurez votre référentiel Git sur la machine virtuelle. Tout en restant connecté à votre machine virtuelle dans votre session SSH, entrez les commandes suivantes :

    ```bash  
       git clone https://github.com/mattbriggs/flask-hello-world.git
    
       cd flask-hello-world
    ```

2. Créez un environnement virtuel et ajoutez-y toutes les dépendances de package. Tout en restant connecté à votre machine virtuelle dans votre session SSH, entrez les commandes suivantes :

    ```bash  
    python3 -m venv venv
    source venv/bin/activate
    pip install -r requirements.txt
    
    export FLASK_APP=application.py
    # export FLASK_DEBUG=1 
    flask run -h 0.0.0.0
    ```

3. Accédez au nouveau serveur. Vous devriez voir votre application web en cours d’exécution.

    ```HTTP  
       http://yourhostname.cloudapp.net:5000
    ```

## <a name="update-your-server"></a>Mettre à jour le serveur

1. Connectez-vous à votre machine virtuelle dans votre session SSH. Arrêtez le serveur en tapant Ctrl+C.

2. Entrez les commandes suivantes :

    ```bash  
    deactivate
    open the git repo
    git pull
    ```

3. Activez l’environnement virtuel et démarrez l’application :

    ```bash  
    source venv/bin/activate
    export FLASK_APP=application.py
    flask run -h 0.0.0.0
    ```

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [développer pour Azure Stack](azure-stack-dev-start.md).
- Découvrez les [Déploiements courants pour Azure Stack en IaaS](azure-stack-dev-start-deploy-app.md).
- Pour apprendre le langage de programmation Python et trouver des ressources supplémentaires sur Python, voir [Python.org](https://www.python.org).
