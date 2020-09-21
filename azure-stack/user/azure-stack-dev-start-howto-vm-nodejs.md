---
title: Déployer une application Node.js sur une machine virtuelle dans Azure Stack Hub
description: Déployez une application Node.js dans Azure Stack Hub.
author: mattbriggs
ms.topic: overview
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/02/20
ms.openlocfilehash: 72bd63e7f7c007a7c39c6150d0e2feac29b5d91d
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90573987"
---
# <a name="deploy-a-nodejs-web-app-to-a-vm-in-azure-stack-hub"></a>Déployer une application web Node.js sur une machine virtuelle dans Azure Stack Hub

Vous pouvez créer une machine virtuelle pour héberger une application web Node.js dans Azure Stack Hub. Dans cet article, vous installez un serveur et vous le configurez pour qu’il héberge votre application web Node.js, puis vous déployez l’application sur Azure Stack Hub.

## <a name="create-a-vm"></a>Créer une machine virtuelle

1. Configurez votre machine virtuelle dans Azure Stack Hub en suivant les instructions fournies dans [Déployer une machine virtuelle Linux pour héberger une application web dans Azure Stack Hub](azure-stack-dev-start-howto-deploy-linux.md).

2. Dans le volet du réseau de machines virtuelles, vérifiez que les ports suivants sont accessibles :

    | Port | Protocol | Description |
    | --- | --- | --- |
    | 80 | HTTP | HTTP (Hypertext Transfer Protocol) est le protocole utilisé pour fournir des pages web à partir des serveurs. Les clients se connectent via HTTP avec une adresse IP ou un nom DNS. |
    | 443 | HTTPS | HTTPS (Hypertext Transfer Protocol Secure) est une version sécurisée du protocole HTTP qui nécessite un certificat de sécurité pour la transmission chiffrée des informations. |
    | 22 | SSH | SSH (Secure Shell) est un protocole réseau chiffré pour les communications sécurisées. Vous utilisez cette connexion avec un client SSH pour configurer la machine virtuelle et déployer l’application. |
    | 3389 | RDP | facultatif. Le protocole RDP (Remote Desktop Protocol) permet d’utiliser une connexion Bureau à distance avec une interface graphique utilisateur sur votre machine.   |
    | 1337 | Custom | Port utilisé par Node.js. Pour un serveur de production, vous routez le trafic par les ports 80 et 443. |

## <a name="install-node"></a>Installer Node

1. Connectez-vous à votre machine virtuelle en utilisant votre client SSH. Pour obtenir des instructions, consultez [Se connecter par SSH avec PuTTY](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

1. À l’invite Bash sur votre machine virtuelle, entrez la commande suivante :

    ```bash  
      sudo apt install nodejs-legacy
    ```

2. [Installez NPM](https://www.npmjs.com/), un gestionnaire de packages (ou de modules) Node.js. Tout en restant connecté à votre machine virtuelle dans votre session SSH, entrez la commande suivante :

    ```bash  
       node --version
    ```

3. [Installez Git](https://git-scm.com), un système couramment utilisé pour la gestion de code source et de versions. Tout en restant connecté à votre machine virtuelle dans votre session SSH, entrez la commande suivante :

    ```bash  
       sudo apt-get -y install git
    ```

3. Validez votre installation. Tout en restant connecté à votre machine virtuelle dans votre session SSH, entrez la commande suivante :

    ```bash  
       node -v
    ```

## <a name="deploy-and-run-the-app"></a>Déployer et exécuter l’application

1. Configurez votre référentiel Git sur la machine virtuelle. Tout en restant connecté à votre machine virtuelle dans votre session SSH, entrez les commandes suivantes :

    ```bash  
       git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
    
       cd nodejs-docs-hello-world
        npm start
    ```

2. Démarrez l’application. Tout en restant connecté à votre machine virtuelle dans votre session SSH, entrez la commande suivante :

    ```bash  
       sudo node app.js
    ```

3. Accédez au nouveau serveur. Vous devriez voir votre application web en cours d’exécution.

    ```HTTP  
       http://yourhostname.cloudapp.net:1337
    ```

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [développer pour Azure Stack Hub](azure-stack-dev-start.md).
- Découvrez les [déploiements courants pour Azure Stack Hub en IaaS](azure-stack-dev-start-deploy-app.md).
- Pour apprendre le langage de programmation Node et trouver des ressources supplémentaires sur Node, voir [Nodejs.org](https://nodejs.org).
