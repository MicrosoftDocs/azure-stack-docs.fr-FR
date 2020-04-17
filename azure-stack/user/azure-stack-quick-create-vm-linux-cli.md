---
title: Créer une machine virtuelle Linux à l’aide d’Azure CLI dans Azure Stack Hub
description: Créez une machine virtuelle Linux à l’aide d’Azure CLI dans Azure Stack Hub.
author: mattbriggs
ms.topic: quickstart
ms.date: 1/22/2020
ms.author: mabrigg
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 49b2a950b523ca76143f50784efb048880d1ac93
ms.sourcegitcommit: 4138a2a15f78e7db38b3a29acc963a71937146fd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "77701936"
---
# <a name="quickstart-create-a-linux-server-vm-by-using-the-azure-cli-in-azure-stack-hub"></a>Démarrage rapide : Créer une machine virtuelle serveur Linux à l’aide d’Azure CLI dans Azure Stack Hub

Vous pouvez créer une machine virtuelle Ubuntu Server 16.04 LTS à l’aide d’Azure CLI. Dans cet article, vous allez créer et utiliser une machine virtuelle. Cet article vous montre également comment :

* Se connecter à la machine virtuelle avec un client distant.
* Installer le serveur web NGINX et afficher la page d’accueil par défaut.
* Nettoyer les ressources inutiles.

## <a name="prerequisites"></a>Prérequis

* Une image Linux de la Place de marché Azure Stack Hub

   La Place de marché Azure Stack Hub ne contient pas d’image Linux par défaut. Demandez à l’opérateur Azure Stack Hub de vous fournir l’image Ubuntu Server 16.04 LTS dont vous avez besoin. L’opérateur peut utiliser les instructions fournies dans [Télécharger des éléments de la Place de marché à partir d’Azure dans Azure Stack Hub](../operator/azure-stack-download-azure-marketplace-item.md).

* Azure Stack Hub nécessite une version spécifique d’Azure CLI pour créer et gérer ses ressources. Si Azure CLI n’est pas configuré pour Azure Stack Hub, connectez-vous au [Kit de développement Azure Stack](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) (ou à un client externe basé sur Windows si vous êtes [connecté via VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn)) et suivez les instructions pour [installer et configurer Azure CLI](azure-stack-version-profiles-azurecli2.md).

* Une clé SSH publique nommée *id_rsa.pub* enregistrée dans le répertoire *.ssh* de votre profil utilisateur Windows. Pour plus d’informations sur la création de clés SSH, consultez [Utiliser une clé publique SSH](azure-stack-dev-start-howto-ssh-public-key.md).

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources est un conteneur logique dans lequel vous pouvez déployer et gérer des ressources Azure Stack Hub. À partir de votre kit de développement ou du système intégré Azure Stack Hub, exécutez la commande [az group create](/cli/azure/group#az-group-create) pour créer un groupe de ressources.

> [!NOTE]
> Nous avons attribué des valeurs à toutes les variables des exemples de code suivants. Toutefois, vous pouvez leur attribuer vos propres valeurs.

L’exemple suivant crée un groupe de ressources nommé myResourceGroup à l’emplacement local : 

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle

Création d’une machine virtuelle en utilisant la commande [az vm create](/cli/azure/vm#az-vm-create). L’exemple suivant crée une machine virtuelle nommée myVM. Cet exemple utilise *Demouser* comme nom d’utilisateur administrateur et *Demouser@123* comme mot de passe administrateur. Remplacez ces valeurs par des valeurs convenant à votre environnement.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "UbuntuLTS" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --location local
```

L’adresse IP publique est retournée dans le paramètre **PublicIpAddress**. Notez l’adresse, car nous l’utiliserons plus tard sur la machine virtuelle.

## <a name="open-port-80-for-web-traffic"></a>Ouvrez le port 80 pour le trafic web

Étant donné que cette machine virtuelle va exécuter le serveur web IIS, vous devez ouvrir le port 80 pour le trafic Internet. Pour ouvrir le port, utilisez la commande [az vm open-port](/cli/azure/vm) : 

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="use-ssh-to-connect-to-the-virtual-machine"></a>Utiliser SSH pour se connecter à la machine virtuelle

À partir d’un ordinateur client disposant de SSH, connectez-vous à la machine virtuelle. Si vous travaillez sur un client Windows, utilisez [PuTTY](https://www.putty.org/) pour créer la connexion. Pour vous connecter à la machine virtuelle, utilisez la commande suivante :

```bash
ssh <publicIpAddress>
```

## <a name="install-the-nginx-web-server"></a>Installer le serveur web NGINX

Pour mettre à jour les ressources du package et installer le dernier package NGINX, exécutez le script suivant :

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Afficher la page d’accueil NGINX

Avec le serveur web NGINX installé et le port 80 ouvert sur votre machine virtuelle, vous pouvez accéder au serveur web à l’aide de l’adresse IP publique de la machine virtuelle. Pour cela, ouvrez un navigateur et accédez à ```http://<public IP address>```.

![Page d’accueil du serveur web NGINX](./media/azure-stack-quick-create-vm-linux-cli/nginx.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Nettoyez les ressources dont vous n’avez plus besoin. Vous pouvez utiliser la commande [az group delete](/cli/azure/group#az-group-delete) pour les supprimer. Exécutez la commande suivante :

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez déployé une machine virtuelle serveur de base sous Linux avec un serveur web. Pour en savoir plus sur les machines virtuelles Azure Stack Hub, consultez [Considérations relatives aux machines virtuelles dans Azure Stack Hub](azure-stack-vm-considerations.md).
