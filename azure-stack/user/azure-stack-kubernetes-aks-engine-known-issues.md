---
title: Problèmes connus avec le moteur AKS sur Azure Stack Hub
description: Découvrez les problèmes connus de l’utilisation du moteur AKS sur Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 07/02/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 07/02/2020
ms.openlocfilehash: 02f4d8ec694ffc56966029f35dd12fd263a5cb8b
ms.sourcegitcommit: 0aa5f7f20690839661c8bb3bfdbe32f82bec0c64
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/21/2020
ms.locfileid: "86566139"
---
# <a name="known-issues-with-the-aks-engine-on-azure-stack-hub"></a>Problèmes connus avec le moteur AKS sur Azure Stack Hub

Cette rubrique décrit les problèmes connus pour le moteur AKS sur Azure Stack Hub.

## <a name="upgrade-issues-in-aks-engine-0510"></a>Problèmes de mise à niveau dans le moteur AKS 0.51.0

* Pendant la mise à niveau (aks-engine upgrade) d’un cluster Kubernetes de la version 1.15.x à 1.16.x, la mise à niveau des composants Kubernetes suivants nécessite des étapes manuelles supplémentaires : **kube-proxy**, **azure-cni-networkmonitor**, **csi-secrets-store**, **kubernetes-dashboard**. Voici une description de ce que vous pouvez observer et de comment contourner les problèmes.

  * Dans les environnements connectés, il n’est pas évident de remarquer ce problème, car il n’y a aucun signe dans le cluster indiquant que les composants concernés n’ont pas été mis à niveau. Tout semble fonctionner comme prévu.
  <!-- * In disconnected environments, you can see this problem when you run a query for the system pods status and see that the pods for the components mentioned below are not in "Ready" state: -->

    ```bash  
    kubectl get pods -n kube-system
    ```

  * Comme solution de contournement pour résoudre ce problème pour chacun de ces composants, exécutez la commande de la colonne Solution de contournement du tableau suivant.

    |Nom du composant |Solution de contournement |Scénarios affectés|
    |---------------|-----------|------------------|
    |**kube-proxy**     | `kubectl delete ds kube-proxy -n kube-system` |Connecté, déconnecté |
    |**azure-cni-networkmonitor**   | `kubectl delete ds azure-cni-networkmonitor -n kube-system`   | Connecté, déconnecté |
    |**csi-secrets-store**  |`sudo sed -i s/Always/IfNotPresent/g /etc/kubernetes/addons/secrets-store-csi-driver.yaml`<br>`kubectl delete ds csi-secrets-store -n kube-system` | Déconnecté |
    |**kubernetes-dashboard** |Exécutez la commande suivante sur chaque nœud maître :<br>`sudo sed -i s/Always/IfNotPresent/g /etc/kubernetes/addons/kubernetes-dashboard.yaml` |Déconnecté |

* Kubernetes 1.17 n’est pas pris en charge dans cette version. Bien qu’il existe des demandes de tirage (pull requests) GitHub référençant la version 1.17, celle-ci n’est pas prise en charge.

## <a name="basic-load-balancer-sku-limitations"></a>Limitations de la référence SKU de l’équilibreur de charge De base

* Limitation du pool d’agents unique. Actuellement, Azure Stack Hub prend en charge seulement la référence SKU d’équilibreur de charge De base. Cette référence SKU [limite](/azure/load-balancer/concepts#limitations) les points de terminaison du pool back-end aux machines virtuelles d’un même groupe à haute disponibilité (ou d’un même groupe de machines virtuelles identiques). Ceci implique que tous les réplicas d’un service LoadBalancer doivent être déployés sur le même pool d’agents ; cela implique aussi que chaque cluster individuel peut avoir un service LoadBalancer Linux ou un service LoadBalancer Windows.

  Vous pouvez forcer Kubernetes à créer des pods dans un pool d’agents spécifique en ajoutant [nodeSelector](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/) "agentpool: NOM_DE_MON_POOL" dans votre modèle de pod.

  ```json
  nodeSelector:

        agentpool: linuxpool
  ```
  
  Si un service LoadBalancer a déjà été créé dans votre cluster, vous pouvez déterminer quel pool d’agents a été sélectionné comme pool back-end de l’équilibreur de charge en examinant le volet des pools back-ends de l’équilibreur de charge dans le portail Azure Stack Hub. Une fois que vous disposez de ces informations, vous pouvez spécifier le pool d’agents cible en mettant à jour votre fichier YAML de déploiement/pod (comme expliqué dans le paragraphe précédent).

* Portée de la commande pour `get-versions`. La sortie de la commande `get-versions` concerne uniquement Azure et non pas les clouds Azure Stack Hub. Pour plus d’informations sur les différents chemins de mise à niveau, consultez [Étapes de mise à niveau vers une version plus récente de Kubernetes](azure-stack-kubernetes-aks-engine-upgrade.md#steps-to-upgrade-to-a-newer-kubernetes-version).

## <a name="next-steps"></a>Étapes suivantes

[Vue d’ensemble du moteur AKS sur Azure Stack Hub](azure-stack-kubernetes-aks-engine-overview.md)
