---
title: Problèmes connus avec le moteur AKS sur Azure Stack Hub
description: Découvrez les problèmes connus de l’utilisation du moteur AKS sur Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/11/2020
ms.openlocfilehash: b65fb410cd18e058f4e0ed7299eeff2d9daeb7f6
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99246875"
---
# <a name="known-issues-with-the-aks-engine-on-azure-stack-hub"></a>Problèmes connus avec le moteur AKS sur Azure Stack Hub

Cette rubrique décrit les problèmes connus pour le moteur AKS sur Azure Stack Hub.

## <a name="unable-to-resize-cluster-vms-with-the-compute-service"></a>Impossible de redimensionner des machines virtuelles de cluster avec le service de calcul

- **S’applique à** : Azure Stack Hub, moteur AKS (tout)
- **Description** : Le redimensionnement de machines virtuelles de cluster par le biais du service de calcul ne fonctionne pas avec le moteur AKS. Le moteur AKS conserve l’état du cluster dans le fichier JSON de modèle d’API. Pour garantir que la taille de machine virtuelle souhaitée est reflétée dans toute opération de création, de mise à jour ou de mise à l’échelle effectuée avec le moteur AKS, vous devez mettre à jour le modèle d’API avant d’exécuter l’une de ces opérations. Par exemple, si vous utilisez le service de calcul pour changer la taille d’une machine virtuelle sur un cluster déjà déployé, l’état est perdu quand la commande `aks-engine update` est exécutée.
- **Correction** : Pour que cela fonctionne, localisez le modèle d’API pour le cluster, changez la taille, puis exécutez `aks-engine update`.
- **Occurrence** : Lors d’une tentative de redimensionnement à l’aide du service de calcul.

## <a name="disk-detach-operation-fails-in-aks-engine-0550"></a>L’opération de détachement du disque échoue dans un moteur AKS 0.55.0

- **S’applique à** : Azure Stack Hub (mise à jour 2005), moteur AKS 0.55.0
- **Description** : Lorsque vous essayez de supprimer un déploiement contenant des volumes de persistance, l’opération de suppression déclenche une série d’erreurs d’attachement/détachement. Cela est dû à un bogue dans le fournisseur de cloud du moteur AKS v0.55.0. Le fournisseur de cloud appelle Azure Resource Manager à l’aide d’une version plus récente de l’API que celle qu’Azure Resource Manager prend actuellement en charge dans Azure Stack Hub (mise à jour 2005).
- **Correction** : Vous trouverez les détails et les étapes d’atténuation dans [le dépôt GitHub du moteur AKS (problème 3817)](https://github.com/Azure/aks-engine/issues/3817#issuecomment-691329443). Effectuez une mise à niveau dès qu’une nouvelle build du moteur AKS et de l’image correspondante sont disponibles.
- **Occurrence** : Lors de la suppression d’un déploiement contenant des volumes de persistance.



## <a name="upgrade-issues-in-aks-engine-0510"></a>Problèmes de mise à niveau dans le moteur AKS 0.51.0

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

## <a name="aks-engine-get-versions-command-limitations"></a>limitations de la commande aks-engine get-versions

Le résultat de la commande **aks-engine** `get-versions` ne se rapporte qu’à Azure global plutôt qu’à Azure Stack Hub. Pour plus d’informations sur les différents chemins d’accès aux mises à niveau, consultez [Étapes de mise à niveau vers une version plus récente de Kubernetes](azure-stack-kubernetes-aks-engine-upgrade.md#steps-to-upgrade-to-a-newer-kubernetes-version).

## <a name="next-steps"></a>Étapes suivantes

[Vue d’ensemble du moteur AKS sur Azure Stack Hub](azure-stack-kubernetes-aks-engine-overview.md)
