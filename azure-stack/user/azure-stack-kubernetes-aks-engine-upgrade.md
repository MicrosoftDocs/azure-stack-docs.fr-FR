---
title: Mettre à niveau un cluster Kubernetes sur Azure Stack | Microsoft Docs
description: Découvrez comment mettre à niveau un cluster Kubernetes sur Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na (Kubernetes)
ms.devlang: nav
ms.topic: article
ms.date: 10/16/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 10/16/2019
ms.openlocfilehash: 3720781dc2545fefaff0b2cd703d7c3880c4b97b
ms.sourcegitcommit: 83cef2c4ec6e1b2fd3f997c91675c1058a850e2f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "72999898"
---
# <a name="upgrade-a-kubernetes-cluster-on-azure-stack"></a>Mettre à niveau un cluster Kubernetes sur Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

## <a name="upgrade-a-cluster"></a>Mettre à niveau un cluster

Le moteur AKS vous permet de mettre à niveau le cluster qui a été déployé à l’origine à l’aide de l’outil. Vous pouvez entretenir les clusters à l’aide du moteur AKS. Vos tâches de maintenance sont similaires à celles de tout système IaaS. Vous devez prendre connaissance de la disponibilité des nouvelles mises à jour et utiliser le moteur AKS pour les appliquer.

La commande de mise à niveau met à jour la version de Kubernetes et l’image du système d’exploitation de base. Chaque fois que vous exécutez la commande de mise à niveau, pour chaque nœud du cluster, le moteur AKS crée une machine virtuelle à l’aide de l’image de base AKS associée à la version **aks_engine** utilisée. Vous pouvez utiliser la commande `aks-engine upgrade` pour conserver la devise de chaque nœud principal et nœud d'agent dans votre cluster. 

Microsoft ne gère pas votre cluster. Cela étant, Microsoft fournit l’outil et l’image de machine virtuelle que vous pouvez utiliser pour gérer votre cluster. 

Les mises à niveau d’un cluster déployé regroupent :

-   kubernetes
-   Fournisseur Kubernetes Azure Stack
-   Système d’exploitation de base

Lors de la mise à niveau d’un cluster de production, tenez compte des points suivants :

-   Utilisez-vous la spécification de cluster (`apimodel.json`) et le groupe de ressources adaptés au cluster cible ?
-   Utilisez-vous une machine fiable pour permettre à la machine cliente d’exécuter le moteur AKS et à partir de laquelle effectuer des opérations de mise à niveau ?
-   Veillez à disposer d’un cluster de sauvegarde et qu’il est opérationnel.
-   Si possible, exécutez la commande à partir d’une machine virtuelle au sein de l’environnement Azure Stack pour réduire les tronçons réseau et les échecs de connectivité potentiels.
-   Vérifiez que votre abonnement dispose de suffisamment d’espace pour l’ensemble du processus. De nouvelles machines virtuelles sont allouées pendant le processus.
-   Aucune mise à jour système ni tâche planifiée ne sont prévues.
-   Configurez une mise à niveau intermédiaire sur un cluster configuré exactement comme un cluster de production et testez-y la mise à niveau avant de l’appliquer à votre cluster de production.

## <a name="steps-to-upgrade-to-a-newer-kubernetes-version"></a>Procédure de mise à niveau vers une version plus récente de Kubernetes

> [!Note]  
> L’image de base AKS sera également mise à niveau si vous utilisez une version plus récente de aks-engine et que l’image est disponible dans la Place de marché.

Les instructions suivantes utilisent les étapes minimales pour effectuer la mise à niveau. Pour plus d'informations, consultez l'article [Mise à niveau des clusters Kubernetes](https://github.com/Azure/aks-engine/blob/master/docs/topics/upgrade.md).

1. Vous devez d’abord déterminer les versions que vous pouvez cibler pour la mise à niveau. Ces versions dépendent de celle dont vous disposez actuellement. Utilisez alors la valeur de version adéquate pour effectuer la mise à niveau.

    Exécutez les commandes suivantes :

    ```bash  
    $ aks-engine get-versions
    Version Upgrades
    1.15.0
    1.14.3  1.15.0
    1.14.1  1.14.3, 1.15.0
    1.13.7  1.14.1, 1.14.3
    1.13.5  1.13.7, 1.14.1, 1.14.3
    1.12.8  1.13.5, 1.13.7
    1.12.7  1.12.8, 1.13.5, 1.13.7
    1.11.10 1.12.7, 1.12.8
    1.11.9  1.11.10, 1.12.7, 1.12.8
    1.10.13 1.11.9, 1.11.10
    1.10.12 1.10.13, 1.11.9, 1.11.10
    1.9.11  1.10.12, 1.10.13
    1.9.10  1.9.11, 1.10.12, 1.10.13
    1.6.9   1.9.10, 1.9.11
    ```

    Par exemple, selon la sortie de la commande `get-versions`, si la version actuelle de Kubernetes est « 1.13.5 », vous pouvez effectuer la mise à niveau vers « 1.13.7, 1.14.1, 1.14.3 ».

2. Collectez les informations nécessaires à l’exécution de la commande `upgrade`. La mise à niveau utilise les paramètres suivants :

    | Paramètre | Exemples | Description |
    | --- | --- | --- |
    | azure-env | AzureStackCloud | Pour indiquer au moteur AKS que votre plateforme cible est Azure Stack, utilisez `AzureStackCloud`. |
    | location | local | Nom de la région de votre Azure Stack. Pour le kit de développement Azure Stack (ASDK), la région est définie sur `local`. |
    | resource-group | kube-rg | Entrez le nom d’un nouveau groupe de ressources ou sélectionnez un groupe de ressources existant. Le nom de la ressource doit être alphanumérique et en minuscules. |
    | subscription-id | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Entrez votre ID d’abonnement. Pour plus d’informations, consultez [S’abonner à une offre](https://docs.microsoft.com/azure-stack/user/azure-stack-subscribe-services#subscribe-to-an-offer). |
    | api-model | ./kubernetes-azurestack.json | Chemin du fichier de configuration du cluster ou modèle d’API. |
    | client-id | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Entrez le GUID du principal du service. ID de client identifié comme ID d’application quand votre administrateur Azure Stack a créé le principal du service. |
    | client-secret | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Entrez le secret du principal du service. Il s’agit de la clé secrète client que vous définissez lors de la création de votre service. |
    | identity-system | adfs | facultatif. Spécifiez votre solution de gestion des identités si vous utilisez Active Directory Federated Services (AD FS). |

3. Une fois vos valeurs en place, exécutez la commande suivante :

    ```bash  
    aks-engine upgrade \
    --azure-env AzureStackCloud   
    --location <for an ASDK is local> \
    --resource-group kube-rg \
    --subscription-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --api-model kube-rg/apimodel.json \
    --upgrade-version 1.13.5 \
    --client-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --client-secret xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --identity-system adfs # required if using AD FS
    ```

4.  Si, pour une raison ou une autre, l’opération de mise à niveau échoue, vous pouvez réexécuter la commande de mise à niveau après avoir résolu le problème. Le moteur AKS reprend l’opération là où elle a échoué la fois précédente.

## <a name="steps-to-only-upgrade-the-os-image"></a>Procédure de mise à niveau de l’image du système d’exploitation uniquement

1. Examinez [la table supported-kubernetes-versions](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions) afin de déterminer si vous disposez de la version aks-engine et de l'image de base AKS que vous envisagez pour votre mise à niveau. Pour afficher la version aks-engine, exécutez : `aks-engine version`.
2. Mettez à niveau votre moteur AKS en conséquence sur la machine où vous avez installé aks-engine, exécutez : `./get-akse.sh --version vx.xx.x` en remplaçant **x.xx.x** par votre version ciblée.
3. Demandez à votre opérateur Azure Stack d’ajouter la version de l’image de base AKS dont vous avez besoin dans la Place de marché Azure Stack que vous envisagez d’utiliser.
4. Exécutez la commande `aks-engine upgrade` en utilisant la même version de Kubernetes qu'actuellement, mais ajoutez `--force`. Vous pouvez consulter un exemple dans [Forcer une mise à niveau](#forcing-an-upgrade).


## <a name="forcing-an-upgrade"></a>Forcer une mise à niveau

Dans certaines circonstances, vous pouvez avoir besoin de forcer une mise à niveau de votre cluster. Par exemple, le premier jour, vous déployez un cluster dans un environnement déconnecté à l’aide de la dernière version de Kubernetes. Le lendemain, Ubuntu publie un correctif pour traiter une vulnérabilité pour laquelle Microsoft génère une nouvelle **image de base AKS**. Vous pouvez appliquer la nouvelle image en forçant une mise à niveau à l’aide de la même version de Kubernetes que celle que vous avez déjà déployée.

```bash  
aks-engine upgrade \
--azure-env AzureStackCloud   
--location <for an ASDK is local> \
--resource-group kube-rg \
--subscription-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
--api-model kube-rg/apimodel.json \
--upgrade-version 1.13.5 \
--client-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
--client-secret xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
--force
```

Pour obtenir des instructions, consultez [Forcer la mise à niveau](https://github.com/Azure/aks-engine/blob/master/docs/topics/upgrade.md#force-upgrade).

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le [moteur AKS sur Azure Stack](azure-stack-kubernetes-aks-engine-overview.md)
- [Mettre à l’échelle un cluster Kubernetes sur Azure Stack](azure-stack-kubernetes-aks-engine-scale.md)