---
title: Déployer une application Node.js sur une machine virtuelle dans Azure Stack | Microsoft Docs
description: Déployez une application Node.js dans Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 4fcf76b8f4950fa7ca919d57281c5662b31e96f6
ms.sourcegitcommit: 05a16552569fae342896b6300514c656c1df3c4e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65838303"
---
# <a name="deploy-a-nodejs-web-app-to-a-vm-in-azure-stack"></a>Déployer une application web Node.js sur une machine virtuelle dans Azure Stack

Vous pouvez créer une machine virtuelle pour héberger une application web Node.js dans Azure Stack. Dans cet article, vous installez un serveur et vous le configurez pour qu’il héberge votre application web Node.js, puis vous déployez l’application sur Azure Stack.

## <a name="create-a-vm"></a>Créer une machine virtuelle

1. Configurez votre machine virtuelle dans Azure Stack en suivant les instructions fournies dans [Déployer une machine virtuelle Linux pour héberger une application web dans Azure Stack](azure-stack-dev-start-howto-deploy-linux.md).

2. Dans le volet du réseau de machines virtuelles, vérifiez que les ports suivants sont accessibles :

    | Port | Protocole | Description |
    | --- | --- | --- |
    | 80 | HTTP | HTTP (Hypertext Transfer Protocol) est le protocole utilisé pour fournir des pages web à partir des serveurs. Les clients se connectent via HTTP avec une adresse IP ou un nom DNS. |
    | 443 | HTTPS | HTTPS (Hypertext Transfer Protocol Secure) est une version sécurisée du protocole HTTP qui nécessite un certificat de sécurité pour la transmission chiffrée des informations. |
    | 22 | SSH | SSH (Secure Shell) est un protocole réseau chiffré pour les communications sécurisées. Vous utilisez cette connexion avec un client SSH pour configurer la machine virtuelle et déployer l’application. |
    | 3389 | RDP | facultatif. Le protocole RDP (Remote Desktop Protocol) permet d’utiliser une connexion Bureau à distance avec une interface graphique utilisateur sur votre machine.   |
    | 1337 | Personnalisée | Port utilisé par Node.js. Pour un serveur de production, vous routez le trafic par les ports 80 et 443. |

## <a name="install-node"></a>Installer Node

1. Connectez-vous à votre machine virtuelle en utilisant votre client SSH. Pour obtenir des instructions, consultez [Se connecter par SSH avec PuTTY](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

1. À l’invite Bash sur votre machine virtuelle, entrez la commande suivante :

    ```bash  
      sudo apt install nodejs-legacy
    ```

2. [Installez NPM](https://www.npmjs.com/), un gestionnaire de packages (ou de modules) Node.js. Tout en restant connecté à votre machine virtuelle dans votre session SSH, entrez la commande suivante :

    ```bash  
       go version
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

- Découvrez comment [développer pour Azure Stack](azure-stack-dev-start.md).
- Découvrez les [Déploiements courants pour Azure Stack en IaaS](azure-stack-dev-start-deploy-app.md).
- Pour apprendre le langage de programmation Node et trouver des ressources supplémentaires sur Node, voir [Nodejs.org](https://nodejs.org).
