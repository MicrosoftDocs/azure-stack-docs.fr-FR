---
title: 'Didacticiel : déployer une application Windows dans AKS sur Azure Stack HCI'
description: Dans le cadre de ce didacticiel, vous allez déployer une application Windows dans votre cluster à l’aide d’une image personnalisée stockée dans Azure Container Registry.
author: abha
ms.topic: tutorial
ms.date: 09/22/2020
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: f6bc0132dd7ce3ee9972b0aaff6d0718cab86843
ms.sourcegitcommit: dabbe44c3208fbf989b7615301833929f50390ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90948730"
---
# <a name="tutorial-deploy-windows-applications-in-azure-kubernetes-service-on-azure-stack-hci"></a>Tutoriel : Déployer des applications Windows dans Azure Kubernetes Service sur Azure Stack HCI

Dans ce didacticiel, vous déployez un exemple d’application ASP.NET dans un conteneur Windows Server vers le cluster Kubernetes. Vous voyez ensuite comment tester et mettre à l’échelle votre application. Ce didacticiel suppose que vous disposez de connaissances de base sur les concepts de Kubernetes. Pour plus d’informations, consultez [Concepts de base d’Azure Kubernetes Service sur Azure Stack HCI](kubernetes-concepts.md).

## <a name="before-you-begin"></a>Avant de commencer

Vérifiez que les exigences suivantes sont remplies :

* Un cluster Azure Kubernetes Service sur Azure Stack HCI avec au moins un nœud Worker Windows en cours d’exécution. 
* Un fichier kubeconfig pour accéder au cluster.
* Module PowerShell Azure Kubernetes Service sur Azure Stack HCI installé.
* Exécutez les commandes dans ce document dans une fenêtre d’administration PowerShell.
* Vérifiez que les charges de travail spécifiques au système d’exploitation se trouvent sur l’hôte de conteneur approprié. Si vous disposez d’un cluster Kubernetes mixte de nœuds worker Linux et Windows, vous pouvez utiliser soit des sélecteurs de nœuds, soit des teintes et des tolérances. Pour plus d’informations, consultez les [sélecteurs de nœud et les teintes et tolérances](adapt-apps-mixed-os-clusters.md).

## <a name="deploy-the-application"></a>Déployer l’application

Un fichier manifeste Kubernetes définit un état souhaité pour le cluster, notamment les images conteneur à exécuter. Dans cet article, un manifeste est utilisé pour créer tous les objets nécessaires pour exécuter l’exemple d’application ASP.NET dans un conteneur Windows Server. Ce manifeste comprend un déploiement Kubernetes pour l’exemple d’application ASP.NET et un service Kubernetes externe pour accéder à l’application à partir d’Internet.

L’exemple d’application ASP.NET est fourni dans le cadre des Exemples .NET Framework et s’exécute dans un conteneur Windows Server. Azure Kubernetes Service sur Azure Stack HCI exige que les conteneurs Windows Server soient basés sur des images de *Windows Server 2019*. 

Le fichier manifeste Kubernetes doit également définir un sélecteur de nœud pour indiquer à votre cluster AKS d’exécuter le pod de votre exemple d’application ASP.NET sur un nœud qui peut exécuter des conteneurs Windows Server.

Créez un fichier nommé `sample.yaml`, et copiez-y la définition YAML suivante. 

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
  labels:
    app: sample
spec:
  replicas: 1
  template:
    metadata:
      name: sample
      labels:
        app: sample
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": windows
      containers:
      - name: sample
        image: mcr.microsoft.com/dotnet/framework/samples:aspnetapp
        resources:
          limits:
            cpu: 1
            memory: 800M
          requests:
            cpu: .1
            memory: 300M
        ports:
          - containerPort: 80
  selector:
    matchLabels:
      app: sample
---
apiVersion: v1
kind: Service
metadata:
  name: sample
spec:
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 80
  selector:
    app: sample
```

Déployez l’application à l’aide de la commande `kubectl apply` et spécifiez le nom de votre manifeste YAML :

```console
kubectl apply -f sample.yaml
```

L’exemple de sortie suivant montre que le déploiement et le service ont été créés correctement :

```output
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Test de l’application

Quand l’application s’exécute, un service Kubernetes expose le front-end de l’application sur Internet. L’exécution de ce processus peut prendre plusieurs minutes. Parfois, le provisionnement du service peut prendre plus de quelques minutes. Autorisez jusqu’à 10 minutes dans ces cas de figure.

Pour surveiller la progression, utilisez la commande `kubectl get service` avec l’argument `--watch`.

```PowerShell
kubectl get service sample --watch
```

Dans un premier temps, la valeur *EXTERNAL-IP* pour *l’exemple* de service apparaît comme étant *en attente*.

```output
NAME    TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample  LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Quand l’adresse *EXTERNAL-IP* passe de l’état *pending* à une adresse IP publique réelle, utilisez `CTRL-C` pour arrêter le processus de surveillance `kubectl`. L’exemple de sortie suivant montre une adresse IP publique valide affectée au service :

```output
NAME    TYPE           CLUSTER-IP   EXTERNAL-IP     PORT(S)        AGE
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Pour voir l’exemple d’application en action, ouvrez un navigateur web en utilisant l’adresse IP externe de votre service.

![Image de la navigation vers l’exemple d’application ASP.NET](media/deploy-windows-application/asp-net-sample-app.png)

Si vous dépassez le délai d’attente de la connexion en tentant de charger la page, vérifiez que l’exemple d’application est prêt avec la commande `kubectl get pods --watch`. Parfois, l’adresse IP externe est disponible avant le démarrage du conteneur Windows.

## <a name="scale-application-pods"></a>Mettre à l’échelle les pods d’application

Nous avons créé un réplica unique du serveur frontal de l’application. Pour voir le nombre et l’état des pods de votre cluster, utilisez la commande `kubectl get` comme suit :

```console
kubectl get pods -n default
```

Pour changer le nombre de pods dans le déploiement *sample*, utilisez la commande `kubectl scale`. L’exemple suivant augmente le nombre de pods de serveur frontal à *3* :

```console
kubectl scale --replicas=3 deployment/sample
```

Réexécutez `kubectl get pods` pour vérifier que des modules supplémentaires ont été créés. Au bout d’une minute environ, les pods supplémentaires sont disponibles dans votre cluster :

```console
kubectl get pods -n default
```

## <a name="next-steps"></a>Étapes suivantes

* [Utilisez Azure Monitor pour surveiller votre cluster et votre application](/azure/azure-monitor/insights/container-insights-enable-arc-enabled-clusters).
* [Utilisez le stockage persistant et configurez la prise en charge de gMSA dans un conteneur Windows](persistent-storage-windows-nodes.md).
