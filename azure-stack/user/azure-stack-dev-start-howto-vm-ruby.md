---
title: Déployer une application Ruby sur une machine virtuelle dans Azure Stack | Microsoft Docs
description: Déployez une application Ruby sur une machine virtuelle dans Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 7744d1adcdcb1dde53c6ef887498a9a3978f4513
ms.sourcegitcommit: 41927cb812e6a705d8e414c5f605654da1fc6952
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "64482295"
---
# <a name="how-to-deploy-a-ruby-web-app-to-a-vm-in-azure-stack"></a>Guide pratique pour déployer une application web Ruby sur une machine virtuelle dans Azure Stack

Vous pouvez créer une machine virtuelle pour héberger votre application web Ruby dans Azure Stack. Cet article explique étape par étape comment configurer le serveur, le paramétrer de façon à héberger une application web Ruby, puis déployer l’application.

Ruby est un langage homogène. Son créateur, Yukihiro « Matz » Matsumoto, a rassemblé certaines fonctionnalités de ses langages préférés (Perl, Smalltalk, Eiffel, Ada et Lisp) afin d’imaginer un nouveau langage qui mêlerait programmations impérative et fonctionnelle. Pour apprendre le langage de programmation Ruby et trouver des ressources supplémentaires sur Ruby, voir [Ruby-lang.org](https://www.ruby-lang.org).

Cet article utilise Ruby et un framework web Ruby on Rails.

## <a name="create-a-vm"></a>Créer une machine virtuelle

1. Configurez votre machine virtuelle dans Azure Stack. Suivez les étapes de [Déployer une machine virtuelle Linux pour héberger une application web dans Azure Stack](azure-stack-dev-start-howto-deploy-linux.md).

2. Dans le panneau du réseau machines virtuelles, vérifiez que les ports suivants sont accessibles :

    | Port | Protocole | Description |
    | --- | --- | --- |
    | 80 | HTTP | Le protocole HTTP (Hypertext Transfer Protocol) est un protocole d’application pour les systèmes d’information hypermédias, collaboratifs et distribués. Les clients se connectent à l’application web avec l’adresse IP publique ou le nom DNS de la machine virtuelle. |
    | 443 | HTTPS | Le protocole HTTPS (Hypertext Transfer Protocol Secure) est une extension du protocole HTTP (Hypertext Transfer Protocol). Il sert à sécuriser la communication sur un réseau d’ordinateurs. Les clients se connectent à l’application web avec l’adresse IP publique ou le nom DNS de la machine virtuelle. |
    | 22 | SSH | Le protocole SSH (Secure Shell) est un protocole réseau de chiffrement qui permet d’exploiter en toute sécurité des services réseau sur un réseau non sécurisé. Nous utiliserons cette connexion avec un client SSH pour configurer la machine virtuelle et déployer l’application. |
    | 3389 | RDP | facultatif. Le protocole RDP (Remote Desktop Protocol) permet d’utiliser une connexion Bureau à distance avec une interface graphique utilisateur.   |
    | 3000 | Personnalisée | Le port 3000 est utilisé par l’infrastructure web Ruby on Rails dans le développement. Pour un serveur de production, le trafic est acheminé par les ports 80 et 443. |

## <a name="install-ruby"></a>Installer Ruby

1. Connectez-vous à votre machine virtuelle en utilisant votre client SSH. Pour obtenir des instructions, voir [Se connecter par SSH avec PuTTy](azure-stack-dev-start-howto-ssh-public-key.md#connect-via-ssh-with-putty).
1. Installez le référentiel PPA. Dans l’invite Bash de votre machine virtuelle, tapez les commandes suivantes :

    ```bash  
    sudo apt -y install software-properties-common
    sudo apt-add-repository ppa:brightbox/ruby-ng

    sudo apt update
    ```

2. Installez Ruby et Ruby on Rails sur votre machine virtuelle. Tout en conservant la connexion à votre machine virtuelle dans votre session SSH, tapez les commandes suivantes :

    ```bash  
    sudo apt install ruby
    gem install rails -v 4.2.6
    ```

3. Installez les dépendances Ruby on Rails. Tout en conservant la connexion à votre machine virtuelle dans votre session SSH, tapez les commandes suivantes :

    ```bash  
    sudo apt-get install make
    sudo apt-get install gcc
    sudo apt-get install sqlite3
    sudo apt-get install nodejs
    sudo gem install sqlite
    sudo gem install bundler
    ```

    > [!Note]  
    > Plusieurs exécutions de `sudo gem install bundler` seront peut-être nécessaires lors de l’installation. Si l’installation de certaines dépendances échoue, consultez les journaux des erreurs et résolvez les problèmes.

4. Validez votre installation. Tout en conservant la connexion à votre machine virtuelle dans votre session SSH, tapez les commandes suivantes :

    ```bash  
        ruby -v
    ```

3. Installez Git. [Git](https://git-scm.com) est un système très courant de gestion de code source et de contrôle des révisions. Tout en conservant la connexion à votre machine virtuelle dans votre session SSH, tapez les commandes suivantes :

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="create-and-run-an-app"></a>Création et exécution d’une application

1. Tout en conservant la connexion à votre machine virtuelle dans votre session SSH, tapez les commandes suivantes :

    ```bash
        rails new myapp
        cd myapp
        rails server -b 0.0.0.0 -p 3000
    ```

2.  Accédez maintenant à votre nouveau serveur. Vous devriez voir votre application web en cours d’exécution.

    ```HTTP  
       http://yourhostname.cloudapp.net:3000
    ```

## <a name="next-steps"></a>Étapes suivantes

- Apprenez à [Développer dans Azure Stack](azure-stack-dev-start.md).
- Découvrez les [Déploiements courants pour Azure Stack en IaaS](azure-stack-dev-start-deploy-app.md).