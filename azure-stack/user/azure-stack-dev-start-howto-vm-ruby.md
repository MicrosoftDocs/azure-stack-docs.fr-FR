---
title: Déployer une application Ruby sur une machine virtuelle dans Azure Stack Hub | Microsoft Docs
description: Déployez une application Ruby sur une machine virtuelle dans Azure Stack Hub.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: 1490a571078556d5fd958ecf9717a21f5607ab79
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75820540"
---
# <a name="deploy-a-ruby-web-app-to-a-vm-in-azure-stack-hub"></a>Déployer une application web Ruby sur une machine virtuelle dans Azure Stack Hub

Vous pouvez créer une machine virtuelle pour héberger votre application web Ruby dans Azure Stack Hub. Dans cet article, vous installez un serveur et vous le configurez pour qu’il héberge votre application web Ruby, puis vous déployez l’application sur Azure Stack Hub.

Cet article utilise Ruby et un framework web Ruby on Rails.

## <a name="create-a-vm"></a>Créer une machine virtuelle

1. Configurez votre machine virtuelle dans Azure Stack Hub. Pour obtenir des instructions, consultez [Déployer une machine virtuelle Linux pour héberger une application web dans Azure Stack Hub](azure-stack-dev-start-howto-deploy-linux.md).

2. Dans le volet du réseau de machines virtuelles, vérifiez que les ports suivants sont accessibles :

    | Port | Protocol | Description |
    | --- | --- | --- |
    | 80 | HTTP | HTTP (Hypertext Transfer Protocol) est le protocole utilisé pour fournir des pages web à partir des serveurs. Les clients se connectent via HTTP avec une adresse IP ou un nom DNS. |
    | 443 | HTTPS | HTTPS (Hypertext Transfer Protocol Secure) est une version sécurisée du protocole HTTP qui nécessite un certificat de sécurité pour la transmission chiffrée des informations. |
    | 22 | SSH | SSH (Secure Shell) est un protocole réseau chiffré pour les communications sécurisées. Vous utilisez cette connexion avec un client SSH pour configurer la machine virtuelle et déployer l’application. |
    | 3389 | RDP | facultatif. Le protocole RDP (Remote Desktop Protocol) permet d’utiliser une connexion Bureau à distance avec une interface graphique utilisateur sur votre machine.   |
    | 3000 | Custom | Port utilisé par le framework web Ruby on Rails dans le développement. Pour un serveur de production, vous routez le trafic par les ports 80 et 443. |

## <a name="install-ruby"></a>Installer Ruby

1. Connectez-vous à votre machine virtuelle en utilisant votre client SSH. Pour obtenir des instructions, voir [Se connecter par SSH avec PuTTy](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

1. Installez le référentiel PPA. À l’invite Bash sur votre machine virtuelle, entrez les commandes suivantes :

    ```bash  
    sudo apt -y install software-properties-common
    sudo apt-add-repository ppa:brightbox/ruby-ng

    sudo apt update
    ```

2. Installez Ruby et Ruby on Rails sur votre machine virtuelle. Tout en restant connecté à votre machine virtuelle dans votre session SSH, entrez les commandes suivantes :

    ```bash  
    sudo apt install ruby
    gem install rails -v 4.2.6
    ```

3. Installez les dépendances Ruby on Rails. Tout en restant connecté à votre machine virtuelle dans votre session SSH, entrez les commandes suivantes :

    ```bash  
    sudo apt-get install make
    sudo apt-get install gcc
    sudo apt-get install sqlite3
    sudo apt-get install nodejs
    sudo gem install sqlite
    sudo gem install bundler
    ```

    > [!Note]  
    > Durant l’installation des dépendances Ruby on Rails, vous devrez peut-être exécuter `sudo gem install bundler` à plusieurs reprises. Si l’installation échoue, passez en revue les journaux d’erreurs et résolvez les problèmes.

4. Validez votre installation. Tout en restant connecté à votre machine virtuelle dans votre session SSH, entrez la commande suivante :

    ```bash  
        ruby -v
    ```

3. [Installez Git](https://git-scm.com), système couramment utilisé pour la gestion du code source et des versions. Tout en restant connecté à votre machine virtuelle dans votre session SSH, entrez la commande suivante :

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="create-and-run-an-app"></a>Création et exécution d’une application

1. Tout en restant connecté à votre machine virtuelle dans votre session SSH, entrez les commandes suivantes :

    ```bash
        rails new myapp
        cd myapp
        rails server -b 0.0.0.0 -p 3000
    ```

2. Accédez au nouveau serveur. Vous devriez voir votre application web en cours d’exécution.

    ```HTTP  
       http://yourhostname.cloudapp.net:3000
    ```

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [développer pour Azure Stack Hub](azure-stack-dev-start.md).
- Découvrez les [déploiements courants pour Azure Stack Hub en IaaS](azure-stack-dev-start-deploy-app.md).
- Pour apprendre le langage de programmation Ruby et trouver des ressources supplémentaires sur Ruby, consultez [Ruby-lang.org](https://www.ruby-lang.org).
