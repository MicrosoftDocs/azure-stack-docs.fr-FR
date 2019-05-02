---
title: Créer une machine virtuelle Windows sur Azure Stack avec Azure CLI | Microsoft Docs
description: Découvrez comment créer une machine virtuelle Windows sur Azure Stack à l’aide d’Azure CLI
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
ms.date: 01/14/2019
ms.author: mabrigg
ms.custom: mvc
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 67e0ccfa883e79d66eb9ca38a6cf15f00154c487
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "64312938"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-by-using-azure-cli-in-azure-stack"></a>Démarrage rapide : créer une machine virtuelle Windows Server à l’aide d’Azure CLI dans Azure Stack

‎*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Vous pouvez créer une machine virtuelle Windows Server 2016 par le biais d’Azure CLI. Suivez les étapes décrites dans cet article pour créer et utiliser une machine virtuelle. Cet article vous présente également les étapes suivantes :

* Se connecter à la machine virtuelle avec un client distant.
* Installer le serveur web IIS et afficher la page d’accueil par défaut.
* Nettoyer des ressources.

## <a name="prerequisites"></a>Prérequis

* Vérifiez que votre opérateur Azure Stack a ajouté l’image **Windows Server 2016**  à la Place de Marché Azure Stack.

* Azure Stack nécessite une version spécifique d’Azure CLI pour créer et gérer les ressources. Si Azure CLI n’est pas configuré pour Azure Stack, suivez les étapes permettant [d’installer et de configurer Azure CLI](azure-stack-version-profiles-azurecli2.md).

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources est un conteneur logique dans lequel vous pouvez déployer et gérer des ressources Azure Stack. À partir de votre environnement Azure Stack, exécutez la commande [az group create](/cli/azure/group#az-group-create) pour créer un groupe de ressources.

> [!NOTE]
>  Des valeurs sont attribuées pour toutes les variables dans les exemples de code. Toutefois, vous pouvez attribuer de nouvelles valeurs si vous le souhaitez.

L’exemple suivant crée un groupe de ressources nommé myResourceGroup à l’emplacement local.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle

Créez une machine virtuelle en utilisant la commande [az vm create](/cli/azure/vm#az-vm-create). L’exemple suivant crée une machine virtuelle nommée myVM. Cet exemple utilise Demouser comme nom d’utilisateur administratif et Demouser@123 comme mot de passe utilisateur. Remplacez ces valeurs par quelque chose d’approprié pour votre environnement.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "Win2016Datacenter" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --location local
```

Lorsque la machine virtuelle est créée, le paramètre **PublicIPAddress** dans la sortie contient l’adresse IP publique de la machine virtuelle. Notez cette adresse, car vous en aurez besoin pour accéder à la machine virtuelle.

## <a name="open-port-80-for-web-traffic"></a>Ouvrez le port 80 pour le trafic web

Étant donné que cette machine virtuelle va exécuter le serveur web IIS, vous devez ouvrir le port 80 pour le trafic Internet.

Utilisez la commande [az vm open-port](/cli/azure/vm) pour ouvrir le port 80.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-the-virtual-machine"></a>Connectez-vous à la machine virtuelle.

Utilisez la commande suivante pour créer une connexion Bureau à distance vers votre machine virtuelle. Remplacez « Public IP Address » par l’adresse IP de votre machine virtuelle. Quand vous y êtes invité, entrez le nom d’utilisateur et le mot de passe que vous avez utilisés pour la machine virtuelle.

```
mstsc /v <Public IP Address>
```

## <a name="install-iis-using-powershell"></a>Installation de IIS à l’aide de PowerShell

Maintenant que vous êtes connecté à la machine virtuelle, vous pouvez utiliser PowerShell pour installer IIS. Lancez PowerShell sur la machine virtuelle et exécutez la commande suivante :

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Afficher la page d’accueil IIS

Vous pouvez utiliser un navigateur web de votre choix pour visualiser la page d’accueil IIS par défaut. Veillez à utiliser l’adresse IP publique indiquée dans la section précédente pour vous rendre sur la page par défaut.

![Site IIS par défaut](./media/azure-stack-quick-create-vm-windows-cli/default-iis-website.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Nettoyez les ressources dont vous n’avez plus besoin. Vous pouvez utiliser la commande [az group delete](/cli/azure/group#az-group-delete) pour supprimer le groupe de ressources, la machine virtuelle, ainsi que toutes les ressources associées.

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez déployé une machine virtuelle Windows Server de base. Pour en savoir plus sur les machines virtuelles Azure Stack, continuez avec [Considérations relatives aux machines virtuelles dans Azure Stack](azure-stack-vm-considerations.md).
