---
title: Problèmes connus avec le moteur AKS sur Azure Stack Hub
description: Découvrez les problèmes connus de l’utilisation du moteur AKS sur Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 09/11/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/11/2020
ms.openlocfilehash: 685cf02aed8e6e485d596531c37653f496a4bc5f
ms.sourcegitcommit: a845ae0d3794b5d845b2ae712baa7e38f3011a7b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2020
ms.locfileid: "90045276"
---
# <a name="known-issues-with-the-aks-engine-on-azure-stack-hub"></a>Problèmes connus avec le moteur AKS sur Azure Stack Hub

Cette rubrique décrit les problèmes connus pour le moteur AKS sur Azure Stack Hub.

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
