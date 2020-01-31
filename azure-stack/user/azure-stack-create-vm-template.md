---
title: Créer une machine virtuelle à l’aide d’un modèle fourni par la communauté
description: Découvrez comment utiliser le Kit de développement Azure Stack (ASDK) pour créer une machine virtuelle à l’aide d’un modèle prédéfini et d’un modèle GitHub personnalisé.
author: sethmanheim
ms.topic: tutorial
ms.date: 01/23/2020
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/13/2018
ms.openlocfilehash: ab2a177c9e2edc1b0362b03023148072fb885fb9
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76883885"
---
# <a name="tutorial-create-a-vm-using-a-community-template"></a>Tutoriel : Créer une machine virtuelle à l’aide d’un modèle fourni par la communauté

En tant qu’opérateur ou utilisateur Azure Stack Hub, vous pouvez créer une machine virtuelle à l’aide de [modèles de démarrage rapide GitHub personnalisés](https://github.com/Azure/AzureStack-QuickStart-Templates) plutôt que de déployer un modèle manuellement depuis la Place de marché Azure Stack Hub.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Utiliser des modèles de démarrage rapide Azure Stack Hub
> * Créer une machine virtuelle à l’aide d’un modèle GitHub personnalisé
> * Démarrer Minikube et installer une application

## <a name="azure-stack-hub-quickstart-templates"></a>Modèles de démarrage rapide Azure Stack Hub

Les modèles de démarrage rapide Azure Stack Hub sont stockés dans le [dépôt de modèles de démarrage rapide Azure Stack Hub global](https://github.com/Azure/AzureStack-QuickStart-Templates) sur GitHub. Ce dépôt contient des modèles de déploiement Azure Resource Manager qui ont été testés avec le kit de développement Microsoft Azure Stack (ASDK). Vous pouvez les utiliser pour simplifier l’évaluation d’Azure Stack Hub et l’utilisation de l’environnement du Kit ASDK.

Avec le temps, de nombreux utilisateurs de GitHub ont contribué au dépôt, offrant ainsi une collection de plus de 400 modèles de déploiement. Ce dépôt constitue un bon point de départ pour comprendre comment déployer différents types d’environnement dans Azure Stack Hub.

>[!IMPORTANT]
> Certains modèles présents sont créés par des membres de la communauté, et non par Microsoft. Chaque modèle est concédé sous licence sous un contrat de licence par son propriétaire, et non par Microsoft. Microsoft ne peut pas être tenu responsable de ces modèles ni ne vérifie leur sécurité, leur compatibilité ou leurs performances. Les modèles de la communauté ne sont pris en charge par aucun programme ou service de support Microsoft. Ils sont proposés « EN L’ÉTAT » sans garantie d’aucune sorte.

Si vous souhaitez proposer des modèles Azure Resource Manager sur GitHub, faites-le dans le dépôt [AzureStack-QuickStart-Templates](https://github.com/Azure/AzureStack-QuickStart-Templates). Pour en savoir plus sur ce dépôt et sur la façon d’y contribuer, consultez le [fichier Lisez-moi](https://aka.ms/aa6zktg).

## <a name="create-a-vm-using-a-custom-github-template"></a>Créer une machine virtuelle à l’aide d’un modèle GitHub personnalisé

Dans l’exemple de ce tutoriel, le modèle de démarrage rapide Azure Stack Hub [101-vm-linux-minikube](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-linux-minikube) est utilisé pour déployer une machine virtuelle Ubuntu 16.04 sur Azure Stack Hub qui exécute Minikube pour gérer un cluster Kubernetes.

Minikube est un outil qui simplifie l’exécution de Kubernetes en local. Minikube exécute un cluster Kubernetes à un seul nœud dans une machine virtuelle, qui vous permet d’essayer Kubernetes ou de développer avec au quotidien. Il prend en charge un simple cluster Kubernetes à un seul nœud qui s’exécute sur une machine virtuelle Linux. Minikube est le moyen le plus rapide et le plus direct pour exécuter un cluster Kubernetes entièrement opérationnel. Les développeurs peuvent ainsi développer et tester leurs déploiements d’applications basées sur Kubernetes sur leurs machines locales. Concernant l’architecture, une machine virtuelle Minikube exécute localement les composants du nœud principal et du nœud d’agent :

* Les composants du nœud principal, comme le serveur d’API, le planificateur et le [serveur etcd](https://coreos.com/etcd/) sont exécutés dans un même processus Linux appelé **LocalKube**.
* Les composants du nœud d’agent sont exécutés dans des conteneurs Docker, exactement comme s’ils étaient exécutés sur un nœud d’agent normal. Du point de vue du déploiement d’application, il n’y a aucune différence entre le déploiement d’une application sur un Minikube ou dans un cluster Kubernetes normal.

Ce modèle installe les composants suivants :

* Une machine virtuelle Ubuntu 16.04 LTS
* [Docker-CE](https://download.docker.com/linux/ubuntu)
* [Kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.8.0/bin/linux/amd64/kubectl)
* [Minikube](https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64)
* xFCE4
* xRDP

> [!IMPORTANT]
> L’image de machine virtuelle Ubuntu (Ubuntu Server 16.04 LTS dans cet exemple) doit avoir été ajoutée à la Place de marché Azure Stack Hub avant le démarrage de cette procédure.

1. Sélectionnez **+ Créer une ressource**, **Personnalisé**, puis **Déploiement de modèle**.

    ![Créer un modèle](media/azure-stack-create-vm-template/1.PNG)

2. Sélectionnez **Modifier un modèle**.

    ![Modifier un modèle](media/azure-stack-create-vm-template/2.PNG)

3. Sélectionnez **Modèle de démarrage rapide**.

    ![Modèle de démarrage rapide](media/azure-stack-create-vm-template/3.PNG)

4. Sélectionnez **101-vm-linux-minikube** dans les modèles disponibles dans la liste déroulante **Sélectionner un modèle**, puis cliquez sur **OK**.

    ![Sélectionner un modèle](media/azure-stack-create-vm-template/4.PNG)

5. Si vous le souhaitez, vous pouvez modifier le modèle JSON. Si vous n’avez pas de modifications à apporter ou si vous avez terminé, sélectionnez **Enregistrer** pour fermer la boîte de dialogue **Modifier le modèle**.

    ![Enregistrer un modèle](media/azure-stack-create-vm-template/5.PNG)

6. Sélectionnez **Paramètres**, renseignez ou modifiez les champs disponibles si nécessaire, puis cliquez sur **OK**.

    ![Paramètres](media/azure-stack-create-vm-template/6.PNG)

7. Sélectionnez l’abonnement à utiliser, puis créez ou choisissez un nom de groupe de ressources existant. Ensuite, sélectionnez **Créer** pour commencer le déploiement du modèle.

    ![Choisir un abonnement](media/azure-stack-create-vm-template/7.PNG)

8. Le déploiement du groupe de ressources met quelques minutes à créer la machine virtuelle basée sur le modèle personnalisé. Vous pouvez surveiller l’état de l’installation via des notifications et depuis les propriétés du groupe de ressources.

    ![Déployer](media/azure-stack-create-vm-template/8.PNG)

    >[!NOTE]
    > La machine virtuelle s’exécute lorsque le déploiement est terminé.

## <a name="start-minikube-and-install-an-application"></a>Démarrer Minikube et installer une application

Maintenant que la machine virtuelle Linux est créée, vous pouvez vous connecter pour démarrer Minikube et installer une application.

1. Une fois le déploiement terminé, sélectionnez **Connexion** pour afficher l’adresse IP publique qui sera utilisée pour se connecter à la machine virtuelle Linux.

    ![Se connecter](media/azure-stack-create-vm-template/9.PNG)

2. À partir d’une invite de commandes avec élévation de privilèges, exécutez la commande **mstsc.exe** pour ouvrir la connexion Bureau à distance et vous connecter à l’adresse IP publique de la machine virtuelle Linux obtenue à l’étape précédente. Lorsque vous êtes invité à vous connecter à xRDP, utilisez les informations d’identification que vous avez spécifiées lors de la création de la machine virtuelle.

    ![Remote](media/azure-stack-create-vm-template/10.PNG)

3. Ouvrez l’émulateur de terminal et entrez les commandes suivantes pour démarrer Minikube :

    ```shell
    sudo minikube start --vm-driver=none
    sudo minikube addons enable dashboard
    sudo minikube dashboard --url
    ```

    ![Commandes d’exécution](media/azure-stack-create-vm-template/11.PNG)

4. Ouvrez un navigateur et accédez à l’adresse du tableau de bord Kubernetes. Félicitations ! Vous disposez maintenant d’une installation Kubernetes entièrement opérationnelle utilisant Minikube.

    ![tableau de bord](media/azure-stack-create-vm-template/12.PNG)

5. Pour déployer un exemple d’application, accédez à la page de documentation officielle de Kubernetes. Comme vous avez déjà créé un cluster, ignorez la section « Create Minikube Cluster » (Créer un cluster Minikube). Passez à la section « Create your Node.js application » (Créer votre application Node.js) sur https://kubernetes.io/docs/tutorials/stateless-application/hello-minikube/.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * En savoir plus sur les modèles de démarrage rapide Azure Stack Hub
> * Créer une machine virtuelle à l’aide d’un modèle GitHub personnalisé
> * Démarrer minikube et installer une application
