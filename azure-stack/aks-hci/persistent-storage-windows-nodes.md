---
title: Utiliser « Utiliser le stockage persistant » dans un conteneur Windows
description: Utiliser le stockage persistant dans un conteneur Windows et préparer des nœuds Windows pour des comptes de service administrés de groupe
author: abhilashaagarwala
ms.topic: how-to
ms.date: 09/21/2020
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: 19b934e4bdec9e0ab6f4e7808dfea6e6fb648245
ms.sourcegitcommit: 2562b86f47db20e2652d4636227afb9cfd0e03ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94784850"
---
# <a name="use-persistent-storage-in-a-windows-container-and-prepare-windows-nodes-for-group-managed-service-accounts"></a>Utiliser le stockage persistant dans un conteneur Windows et préparer des nœuds Windows pour des comptes de service administrés de groupe

Un volume persistant représente un élément de stockage provisionné pour une utilisation dans des pods Kubernetes. Un volume persistant peut être utilisé par un ou plusieurs pods et est destiné au stockage à long terme. Il est également indépendant du cycle de vie du pod ou du nœud.    Dans cette section, vous allez apprendre à créer un volume persistant et à utiliser ce volume dans votre application Windows.

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


```PowerShell
kubectl create -f pvc-akshci-csi.yaml 
```
La sortie suivante indique que votre revendication de volume persistant a été créée avec succès :

**Output:**
```PowerShell
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

```PowerShell
Kubectl create -f winwebserver.yaml 
```

Pour vous assurer que le pod est en cours d’exécution, exécutez la commande suivante. Patientez quelques minutes jusqu’à ce que le pod soit à l’état d’exécution en cours, dans la mesure où l’extraction de l’image prend du temps.

```PowerShell
kubectl get pods -o wide 
```
Lorsque votre pod est en cours d’exécution, affichez l’état du pod en exécutant la commande suivante : 

```PowerShell
kubectl.exe describe pod %podName% 
```

Pour vérifier que votre volume a été monté dans le pod, exécutez la commande suivante :

```PowerShell
kubectl exec -it %podname% cmd.exe 
```

## <a name="delete-a-persistent-volume-claim"></a>Supprimer une revendication de volume persistant

Avant de supprimer une revendication de volume persistant, vous devez supprimer le déploiement de l’application en exécutant :

```PowerShell
kubectl.exe delete deployments win-webserver
```

Vous pouvez ensuite supprimer une revendication de volume persistant en exécutant :

```PowerShell
kubectl.exe delete PersistentVolumeClaim pvc-akshci-csi
```

## <a name="prepare-windows-nodes-for-group-managed-service-account-support-on-windows-nodes"></a>Préparer des nœuds Windows pour la prise en charge de compte de service administré de groupe sur des nœuds Windows

Les comptes de service administrés de groupe sont un type spécifique de compte Active Directory qui fournit la gestion automatique des mots de passe, la gestion simplifiée du nom de principal du service (SPN) et la possibilité de déléguer la gestion à d’autres administrateurs sur plusieurs serveurs. Pour configurer des comptes de service administrés de groupe (gMSA) pour les pods et les conteneurs qui s’exécuteront sur vos nœuds Windows, vous devez tout d’abord joindre vos nœuds Windows à un domaine Active Directory.

Pour activer la prise en charge de compte de service administré de groupe, le nom de votre cluster Kubernetes doit comporter moins de 4 caractères. Cela est dû au fait que la longueur maximale prise en charge pour un nom de serveur joint à un domaine est de 15 caractères et que la convention d’affectation de noms de cluster Kubernetes AKS sur Azure Stack HCI pour un nœud Worker ajoute quelques caractères prédéfinis à un nom de nœud.

Pour joindre vos nœuds Worker Windows à un domaine, connectez-vous à un nœud Worker Windows en exécutant `kubectl get` et en notant la valeur `EXTERNAL-IP`.

```PowerShell
kubectl get nodes -o wide
``` 

Vous pouvez ensuite utiliser SSH dans le nœud à l’aide de `ssh Administrator@ip`. 

Une fois que vous êtes connecté à votre nœud Worker Windows, exécutez la commande PowerShell suivante pour joindre le nœud à un domaine. Vous serez invité à entrer vos informations d’identification de **compte d’administrateur de domaine**. Vous pouvez également utiliser des informations d’identification de l’utilisateur avec élévation de privilèges qui ont reçu des droits pour joindre des ordinateurs au domaine donné. Vous devez ensuite redémarrer votre nœud Worker Windows.

```PowerShell
add-computer --domainame "YourDomainName" -restart
```

Une fois que tous les nœuds Worker Windows ont été joints à un domaine, suivez les étapes détaillées dans la [configuration de gMSA](https://kubernetes.io/docs/tasks/configure-pod-container/configure-gmsa) pour appliquer les définitions de ressource personnalisée Kubernetes gMSA et les webhooks sur votre cluster Kubernetes.

Pour plus d’informations sur le conteneur Windows avec gMSA, consultez [Conteneurs Windows et gMSA](/virtualization/windowscontainers/manage-containers/manage-serviceaccounts). 

## <a name="next-steps"></a>Étapes suivantes
- [Déployer une application Windows sur votre cluster Kubernetes](./deploy-windows-application.md).
- [Connecter votre cluster Kubernetes à Azure Arc pour Kubernetes](./connect-to-arc.md).
