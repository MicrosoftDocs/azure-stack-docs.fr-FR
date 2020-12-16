---
title: Utiliser un volume persistant dans un cluster AKS sur Azure Stack HCI
description: Utiliser le stockage persistant dans un conteneur Windows et préparer des nœuds Windows pour des comptes de service administrés de groupe
author: v-susbo
ms.topic: how-to
ms.date: 12/02/2020
ms.author: v-susbo
ms.reviewer: ''
ms.openlocfilehash: 4baaf83c16b3fc290d9dc1339aaf432dc05d89b7
ms.sourcegitcommit: 0efffe1d04a54062a26d5c6ce31a417f511b9dbf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2020
ms.locfileid: "96612605"
---
# <a name="use-persistent-volume-in-an-aks-on-azure-stack-hci-cluster"></a>Utiliser un volume persistant dans un cluster AKS sur Azure Stack HCI

> S’applique à : AKS sur Azure Stack HCI, AKS Runtime sur Windows Server 2019 Datacenter

Un volume persistant représente un élément de stockage provisionné pour une utilisation dans des pods Kubernetes. Un volume persistant peut être utilisé par un ou plusieurs pods et est destiné au stockage à long terme. Il est également indépendant du cycle de vie du pod ou du nœud. Bien que vous puissiez provisionner un PVC pour les nœuds Windows et Linux, dans cette section, vous allez voir comment créer un volume persistant et comment l’utiliser dans votre application Windows. Pour plus d’informations, consultez [Volumes persistants dans Kubernetes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/).

## <a name="before-you-begin"></a>Avant de commencer

Voici ce dont vous avez besoin pour commencer :

* Un cluster Kubernetes avec au moins un nœud Worker Windows.
* Un fichier kubeconfig pour accéder au cluster Kubernetes.

## <a name="create-a-persistent-volume-claim"></a>Créer une revendication de volume persistant

Une revendication de volume persistant est utilisée pour approvisionner automatiquement le stockage basé sur une classe de stockage.  Pour créer une revendication de volume, créez tout d’abord un fichier nommé `pvc-akshci-csi.yaml` et copiez-y la définition YAML suivante. La revendication demande un disque d’une taille de 10 Go avec un accès  *ReadWriteOnce* . La classe de stockage  *par défaut*  est spécifiée en tant que classe de stockage (vhdx).  

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
 name: pvc-akshci-csi
spec:
 accessModes:
 - ReadWriteOnce
 resources:
  requests:
   storage: 10Gi
```
Créez le volume en exécutant les commandes suivantes dans une session PowerShell d’administration sur l’un des serveurs du cluster Azure Stack HCI (à l’aide d’une méthode telle que [Enter-PSSession](/powershell/module/microsoft.powershell.core/enter-pssession) ou Bureau à distance pour la connexion au serveur) : 

```
kubectl create -f pvc-akshci-csi.yaml 
```
La sortie suivante indique que votre revendication de volume persistant a été créée avec succès :

**Output:**
```
persistentvolumeclaim/pvc-akshci-csi created
```

## <a name="use-persistent-volume"></a>Utiliser le volume persistant

Pour utiliser un volume persistant, créez un fichier nomméwinwebserver.yaml et copiez-y la définition YAML suivante.  Vous allez ensuite créer un pod avec un accès à la revendication de volume persistant et à vhdx. 

Dans la définition YAML ci-dessous, *mountPath* est le chemin d’accès pour monter un volume à l’intérieur d’un conteneur. Après la création de pod, vous verrez le sous-répertoire *mnt* créé dans *C:\\* et le sous-répertoire *akshciscsi* créé dans *mnt*.


```yaml
apiVersion: apps/v1 
kind: Deployment 
metadata: 
  labels: 
    app: win-webserver 
  name: win-webserver 
spec: 
  replicas: 1 
  selector: 
    matchLabels: 
      app: win-webserver 
  template: 
    metadata: 
      labels: 
        app: win-webserver 
      name: win-webserver 
    spec: 
     containers: 
      - name: windowswebserver 
        image: mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2019 
        ports:  
          - containerPort: 80    
        volumeMounts: 
            - name: akshciscsi 
              mountPath: "/mnt/akshciscsi" 
     volumes: 
        - name: akshciscsi 
          persistentVolumeClaim: 
            claimName:  pvc-akshci-csi 
     nodeSelector: 
      kubernetes.io/os: windows 
```

Pour créer un pod avec la définition YAML ci-dessus, exécutez :
```
kubectl create -f winwebserver.yaml 
```

Pour vous assurer que le pod est en cours d’exécution, exécutez la commande suivante. Patientez quelques minutes jusqu’à ce que le pod soit à l’état d’exécution en cours, dans la mesure où l’extraction de l’image prend du temps. 
```
kubectl get pods -o wide 
```
Lorsque votre pod est en cours d’exécution, affichez l’état du pod en exécutant la commande suivante : 
```
kubectl.exe describe pod %podName% 
```

Pour vérifier que votre volume a été monté dans le pod, exécutez la commande suivante :
```
kubectl exec -it %podname% cmd.exe 
```

## <a name="delete-a-persistent-volume-claim"></a>Supprimer une revendication de volume persistant

Avant de supprimer une revendication de volume persistant, vous devez supprimer le déploiement de l’application en exécutant :
```
kubectl delete deployments win-webserver
```

Vous pouvez ensuite supprimer une revendication de volume persistant en exécutant :
```
kubectl delete PersistentVolumeClaim pvc-akshci-csi
```

## <a name="next-steps"></a>Étapes suivantes
- [Déployer une application Windows sur votre cluster Kubernetes](./deploy-windows-application.md).
- [Connecter votre cluster Kubernetes à Azure Arc pour Kubernetes](./connect-to-arc.md).
