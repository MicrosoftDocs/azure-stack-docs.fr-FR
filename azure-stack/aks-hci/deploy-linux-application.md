---
title: 'Didacticiel : déployer une application Linux dans AKS sur Azure Stack HCI'
description: Dans le cadre de ce didacticiel, vous allez déployer une application Linux multiconteneur dans votre cluster à l’aide d’une image personnalisée stockée dans Azure Container Registry.
author: abha
ms.topic: tutorial
ms.date: 09/22/2020
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: 6fd907a44cdaad5f5dfe7ccb3a29f5fc6a0152b6
ms.sourcegitcommit: dabbe44c3208fbf989b7615301833929f50390ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90948731"
---
# <a name="tutorial-deploy-linux-applications-in-azure-kubernetes-service-on-azure-stack-hci"></a>Tutoriel : Déployer des applications Linux dans Azure Kubernetes Service sur Azure Stack HCI

Dans ce didacticiel, vous déployez une application multiconteneur comprenant un serveur web frontal et une instance de base de données ReDim dans votre cluster Azure Kubernetes Service sur Azure Stack HCI. Vous voyez ensuite comment tester et mettre à l’échelle votre application. 

Ce didacticiel suppose que vous disposez de connaissances de base sur les concepts de Kubernetes. Pour plus d’informations, consultez [Concepts de base d’Azure Kubernetes Service sur Azure Stack HCI](kubernetes-concepts.md).

## <a name="before-you-begin"></a>Avant de commencer

Vérifiez que les exigences suivantes sont remplies :

* Un cluster Azure Kubernetes Service sur Azure Stack HCI avec au moins un nœud Worker Linux en cours d’exécution. 
* Un fichier kubeconfig pour accéder au cluster.
* Module PowerShell Azure Kubernetes Service sur Azure Stack HCI installé.
* Exécutez les commandes dans ce document dans une fenêtre d’administration PowerShell.
* Vérifiez que les charges de travail spécifiques au système d’exploitation se trouvent sur l’hôte de conteneur approprié. Si vous disposez d’un cluster Kubernetes mixte de nœuds worker Linux et Windows, vous pouvez utiliser soit des sélecteurs de nœuds, soit des teintes et des tolérances. Pour plus d’informations, consultez les [sélecteurs de nœud et les teintes et tolérances](adapt-apps-mixed-os-clusters.md).

## <a name="deploy-the-application"></a>Déployer l’application

Un fichier manifeste Kubernetes définit un état souhaité pour le cluster, notamment les images conteneur à exécuter. Dans ce guide de démarrage rapide, un manifeste est utilisé afin de créer tous les objets nécessaires pour l’exécution de l’[application de vote Azure](https://github.com/Azure-Samples/azure-voting-app-redis). Ce manifeste inclut deux déploiements Kubernetes : un pour les exemples d’applications Azure Vote Python et l’autre pour une instance Redis. Deux services Kubernetes sont également créés : un service interne pour l’instance Redis et un service externe pour accéder à l’application Azure Vote à partir d’Internet.

Créez un fichier nommé `azure-vote.yaml`, et copiez-y la définition YAML suivante.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: redis
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Déployez l’application à l’aide de la commande `kubectl apply` et spécifiez le nom de votre manifeste YAML :

```PowerShell
kubectl apply -f azure-vote.yaml
```

L’exemple de sortie suivant montre que les déploiements et les ressources ont été créés correctement :

```output
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Test de l’application

Quand l’application s’exécute, un service Kubernetes expose le front-end de l’application sur Internet. L’exécution de ce processus peut prendre plusieurs minutes.

Pour surveiller la progression, utilisez la commande `kubectl get service` avec l’argument `--watch`.

```PowerShell
kubectl get service azure-vote-front --watch
```

Dans un premier temps, la valeur *EXTERNAL-IP* du service *azure-vote-front* apparaît comme étant en attente (*pending*).

```output
NAME               TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27      <pending>     80:30572/TCP   22m
```

Quand l’adresse *EXTERNAL-IP* passe de l’état *pending* à une adresse IP publique réelle, utilisez `CTRL-C` pour arrêter le processus de surveillance `kubectl`. L’exemple de sortie suivant montre une adresse IP publique valide affectée au service :

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP     PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   24m
```

Pour voir l’application Azure Vote en action, ouvrez un navigateur web en utilisant l’adresse IP externe de votre service.

![Image du cluster Kubernetes sur Azure](media/deploy-linux-application/azure-vote.png)

## <a name="scale-application-pods"></a>Mettre à l’échelle les pods d’application

Nous avons créé un réplica unique de l’instance frontale Azure Vote et de l’instance de ReDim. Pour voir le nombre et l’état des pods de votre cluster, utilisez la commande `kubectl get` comme suit :

```console
kubectl get pods -n default
```

L’exemple de sortie suivant montre un pod de serveur frontal et un pod de serveur principal :

```
NAME                                READY     STATUS    RESTARTS   AGE
azure-vote-back-6bdcb87f89-g2pqg    1/1       Running   0          25m
azure-vote-front-84c8bf64fc-cdq86   1/1       Running   0          25m
```

Pour changer le nombre de pods dans le déploiement *azure-vote-front*, utilisez la commande `kubectl scale`. L’exemple suivant augmente le nombre de pods de serveur frontal à *5* :

```console
kubectl scale --replicas=5 deployment/azure-vote-front
```

Réexécutez `kubectl get pods` pour vérifier que des modules supplémentaires ont été créés. Au bout d’une minute environ, les pods supplémentaires sont disponibles dans votre cluster :

```console
kubectl get pods -n default

Name                                READY   STATUS    RESTARTS   AGE
azure-vote-back-6bdcb87f89-g2pqg    1/1     Running   0          31m
azure-vote-front-84c8bf64fc-cdq86   1/1     Running   0          31m
azure-vote-front-84c8bf64fc-56h64   1/1     Running   0          80s
azure-vote-front-84c8bf64fc-djkp8   1/1     Running   0          80s
azure-vote-front-84c8bf64fc-jmmvs   1/1     Running   0          80s
azure-vote-front-84c8bf64fc-znc6z   1/1     Running   0          80s
```

## <a name="next-steps"></a>Étapes suivantes

* [Utilisez Azure Monitor pour surveiller votre cluster et votre application](/azure/azure-monitor/insights/container-insights-enable-arc-enabled-clusters).
