---
title: Adapter les applications pour une utilisation dans les clusters Kubernetes de système d’exploitation mixte
description: Comment utiliser des sélecteurs de nœuds ou des teintes et des tolérances sur Azure Kubernetes Service pour garantir que les applications dans des clusters Kubernetes de système d’exploitation mixte s’exécutant sur Azure Stack HCI sont planifiées sur le système d’exploitation du nœud Worker approprié
author: abha
ms.topic: how-to
ms.date: 12/02/2020
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: 0d4fd0e62e10e4afc4a76c9cac2deaed97e23549
ms.sourcegitcommit: 61556b7b6e029e3a26a4b7ef97f0b13fbe7cd5a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96761556"
---
# <a name="adapt-apps-for-mixed-os-kubernetes-clusters-using-node-selectors-or-taints-and-tolerations"></a>Adapter des applications pour les clusters Kubernetes de système d’exploitation mixte à l’aide de sélecteurs de nœuds ou de teintes et de tolérances

> S’applique à : AKS sur Azure Stack HCI, AKS Runtime sur Windows Server 2019 Datacenter

Azure Kubernetes Service sur Azure Stack HCI vous permet d’exécuter des clusters Kubernetes avec des nœuds Linux et Windows, mais vous oblige à apporter de petites modifications à vos applications pour les utiliser dans ces clusters de système d’exploitation mixte. Dans ce guide pratique, vous apprendrez à vous assurer que votre application est planifiée sur le système d’exploitation hôte approprié à l’aide de sélecteurs de nœuds ou de teintes et de tolérances.

Ce guide pratique suppose une compréhension élémentaire des concepts liés à Kubernetes. Pour plus d’informations, consultez [Concepts de base d’Azure Kubernetes Service sur Azure Stack HCI](kubernetes-concepts.md).

## <a name="node-selector"></a>Sélecteur de nœud

*Sélecteur de nœud* est un champ simple dans le YAML de spécification de pod qui limite les pods pour qu’elles soient uniquement planifiées sur des nœuds sains correspondant au système d’exploitation. Dans votre YAML de spécification de pod, spécifiez un `nodeSelector` - Windows ou Linux, comme indiqué dans les exemples ci-dessous. 

```yaml
kubernetes.io/os = Windows
```
ou

```yaml
kubernetes.io/os = Linux
```

Pour plus d’informations sur nodeSelectors, consultez [Sélecteurs de nœud](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/). 

## <a name="taints-and-tolerations"></a>Teintes et tolérances

Les *teintes* et les *tolérances* fonctionnent ensemble pour s’assurer que les pods ne sont pas planifiés sur les nœuds de manière involontaire. Un nœud peut être « souillé » pour ne pas accepter les pods qui ne tolèrent pas explicitement sa teinte par le biais d’une « tolérance » dans le YAML de spécification de pod.

Les nœuds de système d’exploitation Windows dans Azure Kubernetes Service sur Azure Stack HCI peuvent être souillés avec la paire clé-valeur suivante. Les utilisateurs ne doivent pas en utiliser une autre.

```yaml
node.kubernetes.io/os=Windows:NoSchedule
```
Exécutez `kubectl get` et identifiez les nœuds Worker Windows que vous souhaitez souiller.

```
kubectl get nodes --all-namespaces -o=custom-columns=NAME:.metadata.name,OS:.status.nodeInfo.operatingSystem
```
Sortie :
```output
NAME                                     OS
my-aks-hci-cluster-control-plane-krx7j   linux
my-aks-hci-cluster-md-md-1-5h4bl         windows
my-aks-hci-cluster-md-md-1-5xlwz         windows
```

Souillez les nœuds Worker Windows Server à l’aide de `kubectl taint node`.

```
kubectl taint node my-aks-hci-cluster-md-md-1-5h4bl node.kubernetes.io/os=Windows:NoSchedule
kubectl taint node my-aks-hci-cluster-md-md-1-5xlwz node.kubernetes.io/os=Windows:NoSchedule
```

Vous spécifiez une tolérance pour un pod dans le YAML de spécification de pod. La tolérance suivante « correspond » à la teinte créée par la ligne de teinte kubectl ci-dessus, et un pod avec la tolérance peut donc être planifié sur my-aks-hci-cluster-md-md-1-5h4bl ou my-aks-hci-cluster-md-md-1-5xlwz :

```yaml
tolerations:
- key: node.kubernetes.io/os
  operator: Equal
  value: Windows
  effect: NoSchedule
```
Pour plus d’informations sur les teintes et les tolérances, consultez [Teintes et tolérances](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/). 

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide pratique, vous avez appris à ajouter des sélecteurs de nœuds ou des teintes et des tolérances à vos clusters Kubernetes à l’aide de kubectl. À présent, vous pouvez :
- [Déployer une application Linux sur un cluster Kubernetes](./deploy-linux-application.md).
- [Déployer une application Windows Server sur un cluster Kubernetes](./deploy-windows-application.md).
