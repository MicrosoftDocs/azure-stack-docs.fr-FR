---
title: Créer une machine virtuelle Linux à l’aide d’Azure CLI dans Azure Stack | Microsoft Docs
description: Créez une machine virtuelle Linux à l’aide de CLI dans Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 05/16/2019
ms.author: mabrigg
ms.custom: mvc
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 19c856bdf981775b0b3a8ee923046b51e6ef79ca
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782786"
---
# <a name="quickstart-create-a-linux-server-virtual-machine-using-azure-cli-in-azure-stack"></a>Démarrage rapide : Créer une machine virtuelle serveur Linux à l’aide d’Azure CLI dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Vous pouvez créer une machine virtuelle Ubuntu Server 16.04 LTS à l’aide d’Azure CLI. Suivez les étapes décrites dans cet article pour créer et utiliser une machine virtuelle. Cet article vous présente également les étapes pour :

* Se connecter à la machine virtuelle avec un client distant.
* Installer le serveur web NGINX et afficher la page d’accueil par défaut.
* Nettoyer les ressources inutiles.

## <a name="prerequisites"></a>Prérequis

* **Une image Linux dans la Place de Marché Azure Stack**

   La Place de Marché Azure Stack ne contient pas d’image Linux par défaut. Obtenez de l’opérateur d’Azure Stack qu’il vous fournisse l’image **Ubuntu Server 16.04 LTS** dont vous avez besoin. L’opérateur peut utiliser la procédure décrite dans l’article [Télécharger des éléments de la Place de Marché à partir d’Azure dans Azure Stack](../operator/azure-stack-download-azure-marketplace-item.md).

* Azure Stack nécessite une version spécifique d’Azure CLI pour créer et gérer les ressources. Si Azure CLI n’est pas configurée pour Azure Stack, connectez-vous au [kit de développement](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) (ou à un client externe basé sur Windows si vous êtes [connecté via VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn)) et suivez les étapes pour [installer et configurer Azure CLI](azure-stack-version-profiles-azurecli2.md).

* Une clé SSH publique nommée id_rsa.pub enregistrée dans le répertoire .ssh de votre profil utilisateur Windows. Pour plus d’informations sur la création de clés SSH, voir [Guide pratique pour utiliser une clé publique SSH](azure-stack-dev-start-howto-ssh-public-key.md).

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources est un conteneur logique dans lequel vous pouvez déployer et gérer des ressources Azure Stack. À partir de votre kit de développement ou du système intégré Azure Stack, exécutez la commande [az group create](/cli/azure/group#az-group-create) pour créer un groupe de ressources.

> [!NOTE]
> Des valeurs sont attribuées pour toutes les variables dans les exemples de code. Toutefois, vous pouvez attribuer de nouvelles valeurs si vous le souhaitez.

L’exemple suivant crée un groupe de ressources nommé myResourceGroup à l’emplacement local : 

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle

Création d’une machine virtuelle en utilisant la commande [az vm create](/cli/azure/vm#az-vm-create). L’exemple suivant crée une machine virtuelle nommée myVM. Cet exemple utilise Demouser comme nom d’utilisateur administrateur et Demouser@123 comme mot de passe administrateur. Remplacez ces valeurs par quelque chose d’approprié pour votre environnement.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "UbuntuLTS" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --location local
```

L’adresse IP publique est retournée dans le paramètre **PublicIpAddress**. Notez cette adresse, car vous en aurez besoin pour utiliser la machine virtuelle.

## <a name="open-port-80-for-web-traffic"></a>Ouvrez le port 80 pour le trafic web

Étant donné que cette machine virtuelle va exécuter le serveur web IIS, vous devez ouvrir le port 80 pour le trafic Internet. Utilisez la commande [az vm open-port](/cli/azure/vm) pour ouvrir le port souhaité : 

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="use-ssh-to-connect-to-the-virtual-machine"></a>Utiliser SSH pour se connecter à la machine virtuelle

À partir d’un ordinateur client disposant de SSH, connectez-vous à la machine virtuelle. Si vous travaillez sur un client Windows, utilisez [Putty](https://www.putty.org/) pour créer la connexion. Pour vous connecter à la machine virtuelle, utilisez la commande suivante :

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

Avec NGINX installé et le port 80 ouvert sur votre machine virtuelle, vous pouvez accéder au serveur web à l’aide de l’adresse IP publique de la machine virtuelle. Ouvrez un navigateur et accédez à ```http://<public IP address>```.

![Page d’accueil du serveur web NGINX](./media/azure-stack-quick-create-vm-linux-cli/nginx.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Nettoyez les ressources dont vous n’avez plus besoin. Vous pouvez utiliser la commande [az groupe delete](/cli/azure/group#az-group-delete) pour supprimer ces ressources. Pour supprimer le groupe de ressources et toutes ses ressources, exécutez la commande suivante :

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez déployé une machine virtuelle serveur de base sous Linux avec un serveur web. Pour en savoir plus sur les machines virtuelles Azure Stack, continuez avec [Considérations relatives aux machines virtuelles dans Azure Stack](azure-stack-vm-considerations.md).
