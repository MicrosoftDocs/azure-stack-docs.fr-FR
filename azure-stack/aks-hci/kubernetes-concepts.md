---
title: Concepts de base de Kubernetes pour Azure Kubernetes Service (AKS) sur Azure Stack HCI
description: Découvrez les composants de charge de travail et de cluster de base de Kubernetes et leur lien avec les fonctionnalités d’Azure Kubernetes Service sur Azure Stack HCI
author: daschott
ms.author: daschott
ms.topic: conceptual
ms.date: 09/14/2020
ms.openlocfilehash: 49ecf74033b2b6c68de1fa5fdfba84136c845397
ms.sourcegitcommit: dabbe44c3208fbf989b7615301833929f50390ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90949302"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-on-azure-stack-hci"></a>Concepts de base de Kubernetes pour Azure Kubernetes Service sur Azure Stack HCI
Azure Kubernetes Service sur Azure Stack HCI est une plateforme de conteneur Kubernetes d’entreprise alimentée par Azure Stack HCI. Il comprend le Kubernetes de base pris en charge par Microsoft, des modules complémentaires, un hôte de conteneur Windows spécialement conçu et un hôte de conteneur Linux pris en charge par Microsoft dans le but de bénéficier d’une  **expérience de déploiement et de gestion du cycle de vie simple**.

Cet article présente les principaux composants de l’infrastructure Kubernetes, tels que le plan de contrôle, les nœuds et les pools de nœuds. Les ressources de charge de travail telles que les pods, les déploiements et les ensembles sont également présentées, ainsi que le regroupement de ressources dans des espaces de noms.

## <a name="kubernetes-cluster-architecture"></a>Architecture d’un cluster Kubernetes
Kubernetes est le composant principal d’Azure Kubernetes Service sur Azure Stack HCI. Azure Kubernetes Service sur Azure Stack HCI utilise un ensemble de configurations prédéfinies pour déployer le ou les clusters Kubernetes de manière efficace et évolutive.
 
L’opération de déploiement créera plusieurs machines virtuelles Linux ou Windows et les regroupera pour créer un ou des clusters Kubernetes. 
 
Le système déployé est prêt à recevoir des charges de travail Kubernetes standard, mettre à l’échelle ces charges de travail, voire mettre à l’échelle le nombre de machines virtuelles, ainsi qu’augmenter et réduire le nombre de clusters en fonction des besoins.

Un cluster Azure Kubernetes Service est divisé en deux composants principaux sur Azure Stack HCI :

- Le cluster de *gestion* fournit le mécanisme d’orchestration et l’interface de base pour le déploiement et la gestion d’un ou plusieurs clusters cibles.
- Les clusters *cibles* (également appelés clusters de charge de travail) sont les emplacements où les charges de travail d’application sont exécutées et gérées par un cluster de gestion.

![Illustre l’architecture technique d’Azure Kubernetes Service sur Azure Stack HCI](.\media\concepts\architecture.png)

## <a name="management-cluster"></a>Cluster de gestion
Lorsque vous créez un cluster Azure Kubernetes Service sur Azure Stack HCI, un cluster de gestion est automatiquement créé et configuré. Ce cluster de gestion est chargé de l’approvisionnement et de la gestion des clusters cibles sur lesquels les charges de travail s’exécutent. Un cluster de gestion inclut les composants Kubernetes principaux suivants :
- *Serveur d’API* : le serveur d’API détermine comment les API Kubernetes sous-jacentes sont exposées. Ce composant fournit l’interaction des outils de gestion, tels que Windows Admin Center, les modules PowerShell ou `kubectl`.
- *Load Balancer* : l’équilibreur de charge est une machine virtuelle Linux dédiée unique avec une règle d’équilibrage de charge pour le serveur d’API du cluster de gestion.

### <a name="windows-admin-center"></a>Windows Admin Center
Windows Admin Center offre une interface utilisateur intuitive pour l’opérateur Kubernetes afin de gérer le cycle de vie des clusters Azure Kubernetes Service sur Azure Stack HCI.

### <a name="powershell-module"></a>Module PowerShell
Le module PowerShell est un moyen simple de télécharger, configurer et déployer Azure Kubernetes Service sur Azure Stack HCI. Le module PowerShell prend également en charge le déploiement et la configuration de clusters cibles supplémentaires, ainsi que la reconfiguration de clusters cibles existants.

## <a name="target-cluster"></a>Cluster cible
Le cluster cible (charge de travail) est un déploiement à haute disponibilité de Kubernetes à l’aide de machines virtuelles Linux pour l’exécution des composants du plan de contrôle Kubernetes, ainsi que des nœuds Worker Linux. Les machines virtuelles Windows Server Core sont utilisées pour l’établissement des nœuds Worker Windows. Un ou plusieurs clusters cibles peuvent être gérés par un cluster de gestion.

### <a name="worker-nodes"></a>Nœuds de travail
Pour exécuter vos applications et les services de prise en charge, vous avez besoin d’un nœud Kubernetes. Un cluster cible Azure Kubernetes Service sur Azure Stack HCI comprend un ou plusieurs nœuds Worker, à savoir une machine virtuelle qui exécute les composants de nœud Kubernetes et héberge les pods et les services qui constituent la charge de travail de l’application.

### <a name="load-balancer"></a>Équilibrage de charge
L’équilibreur de charge est une machine virtuelle exécutant Linux et HAProxy + KeepAlive pour fournir des services à charge équilibrée pour les clusters cibles déployés par le cluster de gestion.

Pour chaque cluster cible, Azure Kubernetes Service sur Azure Stack HCI ajoute au moins une machine virtuelle d’équilibreur de charge. Un autre équilibreur de charge peut également être créé pour la haute disponibilité du serveur d’API sur le cluster cible. Tout service Kubernetes de type `LoadBalancer` créé sur le cluster cible finit par créer une règle d’équilibrage de charge dans la machine virtuelle d’équilibreur de charge.

### <a name="add-on-components"></a>Composants de modules complémentaires
Plusieurs composants de modules complémentaires facultatifs peuvent être déployés dans un cluster, notamment : Azure Arc, Prometheus, Grafana ou le tableau de bord Kubernetes.

## <a name="kubernetes-components"></a>Composants Kubernetes
Cette section présente les principaux composants de charge de travail Kubernetes qui peuvent être déployés sur Azure Kubernetes Service sur des clusters cibles Azure Stack HCI tels que des pods, déploiements et ensembles, ainsi que la méthode de regroupement des ressources dans des espaces de noms.

### <a name="pods"></a>pods

Kubernetes Utilise des *pods* pour exécuter une instance de votre application. Un pod représente une instance unique de votre application. les pods ont généralement un mappage 1 à 1 avec un conteneur, bien qu’il existe des scénarios avancés où un pod peut contenir plusieurs conteneurs. Ces pods multiconteneurs sont planifiés ensemble sur le même nœud et permettent aux conteneurs de partager des ressources connexes.

Quand vous créez un pod, vous pouvez définir des *demandes de ressources* afin de demander une certaine quantité de ressources de processeur ou de mémoire. Le planificateur de Kubernetes essaie de planifier les pods afin qu’ils s’exécutent sur un nœud dont les ressources permettent de répondre à la demande. Vous pouvez également spécifier des limites de ressources maximales qui empêchent un pod donné de consommer trop de ressources de calcul à partir du nœud sous-jacent. Une bonne pratique consiste à inclure des limites de ressources pour tous les pods afin d'aider le Scheduler Kubernetes à identifier les ressources nécessaires et autorisées.

Pour plus d’informations, consultez [Kubernetes pods][kubernetes-pods] (Pods Kubernetes) et [Kubernetes pod lifecycle][kubernetes-pod-lifecycle] (Cycle de vie des pods Kubernetes).

Un pod est une ressource logique, tandis que les conteneurs sont l’endroit où s’exécutent les charges de travail d’applications. les pods sont en général des ressources éphémères jetables, et ceux planifiés individuellement ne bénéficient pas de toutes les fonctionnalités de haute disponibilité et de redondance fournies par Kubernetes. En effet, les pods sont déployés et gérés par des *contrôleurs* Kubernetes, comme le contrôleur de déploiement.

### <a name="deployments-and-yaml-manifests"></a>Déploiements et manifestes YAML

Un *déploiement* représente un ou plusieurs pods identiques, gérés par le contrôleur de déploiement Kubernetes. Un déploiement définit le nombre de *réplicas* (pods) à créer, tandis que le planificateur de Kubernetes veille à ce que des pods supplémentaires soient planifiés sur des nœuds sains si les pods ou les nœuds rencontrent des problèmes.

Vous pouvez mettre à jour les déploiements pour changer la configuration des pods, l’image de conteneur utilisée ou le stockage attaché. Le contrôleur de déploiement draine et met fin à un certain nombre de réplicas, crée des réplicas à partir de la nouvelle définition de déploiement et poursuit le processus jusqu’à ce que tous les réplicas dans le déploiement soient mis à jour.

La plupart des applications sans état doivent utiliser le modèle de déploiement plutôt que la planification de pods individuels. Kubernetes peut superviser l’intégrité et l’état des déploiements pour s’assurer que le nombre requis de réplicas s’exécutent dans le cluster. Quand vous planifiez uniquement des pods individuels, ces derniers ne sont pas redémarrés s’ils rencontrent un problème et ne sont pas replanifiés sur des nœuds sains si leur nœud actuel rencontre un problème.

Si une application requiert qu’un quorum d’instances soit toujours disponible pour les prises de décisions de gestion, il convient qu’aucun processus de mise à jour ne rompe ce dispositif. vous pouvez utiliser des *budgets d’interruption de pods* pour définir le nombre de réplicas dans un déploiement pouvant être retirés pendant une mise à niveau d’un nœud ou une mise à jour. Par exemple, si votre déploiement comprend *cinq (5)*  réplicas, vous pouvez définir une interruption de pods de *4* pour limiter la suppression ou la replanification autorisée à un seul réplica à la fois. Comme dans le cas des limites de ressources des pods, une bonne pratique consiste à définir des budgets d’interruption de pods sur les applications qui nécessitent la présence systématique d’un nombre minimal de réplicas.

Les déploiements sont généralement créés et gérés avec `kubectl create` ou `kubectl apply`. Pour créer un déploiement, vous définissez un fichier manifeste dans le format YAML (YAML Ain't Markup Language). L’exemple suivant crée un déploiement de base du serveur web NGINX. Le déploiement spécifie la création de *trois (3)*  réplicas ; le port *80* doit être ouvert sur le conteneur. Des demandes et limites de ressources sont également définies pour l’UC et la mémoire.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.15.2
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 250m
            memory: 64Mi
          limits:
            cpu: 500m
            memory: 256Mi
```

Vous pouvez également créer des applications plus complexes en incluant des services tels que des équilibreurs de charge dans le manifeste YAML.

Pour plus d’informations, consultez [Déploiements Kubernetes][kubernetes-deployments]

##### <a name="mixed-os-deployments"></a>Déploiements de système d’exploitation mixte

Si un cluster cible Azure Kubernetes Service sur Azure Stack HCI donné est constitué de nœuds Worker Linux et Windows, les charges de travail doivent être planifiées sur un système d’exploitation capable de prendre en charge l’approvisionnement de la charge de travail. Kubernetes offre deux mécanismes pour garantir le fonctionnement des charges de travail sur les nœuds avec un système d’exploitation cible :

- *Sélecteur de nœud* est un champ simple dans la spécification de pod qui limite les pods pour qu’ils soient uniquement planifiés sur des nœuds sains correspondant au système d’exploitation. 
- Les *teintes et tolérances* fonctionnent ensemble pour s’assurer que les pods ne sont pas planifiés sur les nœuds de manière involontaire. Un nœud peut être « souillé » dans le but de ne pas accepter les pods qui ne tolèrent pas explicitement sa teinte par le biais d’une « tolérance » dans la spécification de pod.

Pour plus d’informations, consultez les [sélecteurs de nœud][node-selectors] et les [teintes et tolérances][taints-tolerations].

### <a name="statefulsets-and-daemonsets"></a>Ressources StatefulSet et ressources DaemonSet

Le contrôleur de déploiement utilise le planificateur de Kubernetes pour exécuter un certain nombre de réplicas sur n’importe quel nœud disponible ayant des ressources. Cette approche de l’utilisation des déploiements peut être suffisante pour les applications sans état, mais pas pour les applications qui nécessitent un stockage ou une convention de nommage persistant. Pour les applications qui nécessitent qu’un réplica existe sur chaque nœud, ou certains nœuds, au sein d’un cluster, le contrôleur de déploiement ne considère pas la façon dont les réplicas sont répartis entre les nœuds.

Il existe deux ressources Kubernetes qui vous permettent de gérer ces types d’applications :

- *Ressources StatefulSet* : maintiennent l’état des applications au-delà du cycle de vie d’un pod individuel, tel que le stockage.
- *Ressources DaemonSet* : garantissent une instance en cours d’exécution sur chaque nœud, tôt dans le processus de démarrage de Kubernetes.

### <a name="statefulsets"></a>Ressources StatefulSet

Le développement d’applications modernes s’adresse souvent aux applications sans état, mais vous pouvez utiliser des *ressources StatefulSet* pour les applications avec état, telles que les applications qui incluent des composants de base de données. Une ressource StatefulSet est similaire à un déploiement, car un ou plusieurs pods identiques sont créés et gérés. Les réplicas d’une ressource StatefulSet suivent une approche séquentielle et sans perte de données du déploiement, de la mise à l’échelle, des mises à niveau et des arrêts. Avec une ressource StatefulSet (quand les réplicas sont replanifiés), la convention d’affectation de noms, les noms de réseau et le stockage persistent.

Vous définissez l’application au format YAML en utilisant `kind: StatefulSet`, puis le contrôleur StatefulSet gère le déploiement et la gestion des réplicas requis. Les données sont écrites dans un stockage persistant, le stockage sous-jacent étant conservé même après la suppression de la ressource StatefulSet.

Pour plus d’informations, consultez la section [Kubernetes StatefulSets][kubernetes-statefulsets].

Les réplicas dans une ressource StatefulSet sont planifiés et exécutés sur n’importe quel nœud disponible dans un cluster Azure Kubernetes Service sur Azure Stack HCI. Si vous devez vous assurer qu’au moins un pod dans votre jeu s’exécute sur un nœud, vous pouvez utiliser à la place une ressource DaemonSet.

### <a name="daemonsets"></a>Ressources DaemonSet

Dans le cadre d’une supervision ou d’une collecte de journaux spécifique, vous pouvez être amené à exécuter un pod donné sur la totalité ou une partie des nœuds. Une ressource *DaemonSet* permet de déployer un ou plusieurs pods identiques, mais le contrôleur DaemonSet garantit que chaque nœud spécifié exécute une instance du pod.

Le contrôleur DaemonSet peut planifier des pods sur des nœuds tôt dans le processus de démarrage du cluster, avant que ne démarre le planificateur Kubernetes par défaut. Ainsi, les pods dans une ressource DaemonSet sont démarrés avant que ne soient planifiés les pods traditionnels dans un déploiement ou une ressource StatefulSet.

À l’image des ressources StatefulSet, une ressource DaemonSet est définie dans le cadre d’une définition YAML à l’aide de `kind: DaemonSet`.

Pour plus d’informations, consultez la section [Kubernetes DaemonSets][kubernetes-daemonset].

### <a name="namespaces"></a>Espaces de noms

Les ressources Kubernetes, telles que les pods et les déploiements, sont regroupées logiquement dans un *espace de noms*. Ces regroupements permettent de scinder un cluster cible Azure Kubernetes Service sur Azure Stack HCI logiquement et de restreindre l’accès pour la création, l’affichage ou la gestion des ressources. Vous pouvez créer des espaces de noms pour séparer les groupes métier, par exemple. Les utilisateurs ne peuvent interagir qu’avec les ressources appartenant aux espaces de noms qui leur sont attribués.

Lorsque vous créez un cluster Azure Kubernetes Service sur Azure Stack HCI, les espaces de noms suivants sont disponibles :

- *default* : espace de noms dans lequel sont créés par défaut les pods et les déploiements quand aucun espace de noms n’est fourni. Dans les environnements plus petits, vous pouvez déployer les applications directement dans l’espace de noms par défaut sans provoquer la création de séparations logiques supplémentaires. Quand vous interagissez avec l’API Kubernetes, comme avec `kubectl get pods`, l’espace de noms par défaut est utilisé si aucun n’est spécifié.
- *kube-system* : espace de noms où se trouvent les principales ressources, telles que les fonctionnalités réseau, comme le DNS et le proxy, ou bien le tableau de bord Kubernetes. En règle générale, vous ne déployez pas vos propres applications dans cet espace de noms.
- *kube-public* : cet espace de noms n'est généralement pas utilisé, mais vous pouvez y recourir pour rendre les ressources visibles dans l'ensemble du cluster et consultables par tous les utilisateurs.

Pour plus d’informations, consultez la section [Espace de noms Kubernetes][kubernetes-namespaces].

[kubernetes-pods]: https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/
[kubernetes-pod-lifecycle]: https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/
[kubernetes-deployments]: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
[kubernetes-statefulsets]: https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/
[kubernetes-daemonset]: https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/
[kubernetes-namespaces]: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
[node-selectors]: https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/
[taints-tolerations]: https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/
