---
title: Déployer un cluster Kubernetes sur un réseau virtuel personnalisé sur Azure Stack Hub
description: Découvrez comment déployer un cluster Kubernetes sur un réseau virtuel personnalisé sur Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 9/2/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 9/2/2020
ms.openlocfilehash: 588adfc39dc6ff3eec26e67283a7f51a28655fd2
ms.sourcegitcommit: 1621f2748b2059fd47ccacd48595a597c44ee63f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2020
ms.locfileid: "91853208"
---
# <a name="deploy-a-kubernetes-cluster-to-a-custom-virtual-network-on-azure-stack-hub"></a>Déployer un cluster Kubernetes sur un réseau virtuel personnalisé sur Azure Stack Hub 

Vous pouvez déployer un cluster Kubernetes à l’aide du moteur Azure Kubernetes Service (AKS) sur un réseau virtuel personnalisé. Cet article s’intéresse aux informations dont vous avez besoin dans votre réseau virtuel. Vous allez découvrir les étapes à suivre pour calculer les adresses IP utilisées par votre cluster, définir les valeurs dans le modèle d’API et définir la table de routage et le groupe de sécurité réseau.

Dans Azure Stack Hub, le cluster Kubernetes qui utilise le moteur AKS utilise le plug-in réseau kubenet. Pour plus d’informations sur la mise en réseau du plug-in réseau kubenet dans Azure, consultez [Utiliser la mise en réseau kubenet avec vos propres plages d’adresses IP dans Azure Kubernetes Service (AKS)](/azure/aks/configure-kubenet).

## <a name="constraints-when-creating-a-custom-virtual-network"></a>Contraintes lors de la création d’un réseau virtuel personnalisé

-  Le réseau virtuel personnalisé doit se trouver dans le même abonnement que tous les autres composants du cluster Kubernetes.
-  Le pool de nœuds principaux et le pool de nœuds d’agent doivent se trouver dans le même réseau virtuel. Vous pouvez déployer vos nœuds dans différents sous-réseaux au sein du même réseau virtuel.
-  Le sous-réseau de cluster Kubernetes doit utiliser une plage d’adresses IP dans l’espace de la plage d’adresses IP du réseau virtuel personnalisé, consultez [Obtenir le bloc d’adresses IP](#get-the-ip-address-block).

## <a name="create-custom-virtual-network"></a>Créer un réseau virtuel personnalisé

Vous devez avoir un réseau virtuel personnalisé dans votre instance Azure Stack Hub. Pour plus d’informations, consultez [Démarrage rapide : Créer un réseau virtuel avec le portail Azure](/azure/virtual-network/quick-create-portal).

Créez un sous-réseau dans votre réseau virtuel. Vous devez obtenir l’ID de ressource de sous-réseau et la plage d’adresses IP. Vous allez utiliser l’ID de ressource et la plage dans votre modèle d’API quand vous déploierez votre cluster.

1. Ouvrez le portail utilisateur Azure Stack Hub dans votre instance Azure Stack Hub.
2. Sélectionnez **Toutes les ressources**.
3. Entrez le nom de votre réseau virtuel dans la zone de recherche.
4. Sélectionnez **Sous-réseaux** >  **+ Sous-réseaux** pour ajouter un sous-réseau.
5. Ajoutez un **nom** et une **plage d’adresses** en utilisant la notation CIDR. Sélectionnez **OK**.
4. Sélectionnez **Propriétés** dans le panneau **Réseaux virtuels**. Copiez l’**ID de ressource**, puis ajoutez `/subnets/<nameofyoursubnect>`. Vous allez utiliser cette valeur pour la clé `vnetSubnetId` dans le modèle d’API de votre cluster. L’ID de ressource du sous-réseau utilise le format suivant :<br>`/subscriptions/SUB_ID/resourceGroups/RG_NAME/providers/Microsoft.Network/virtualNetworks/VNET_NAME/subnets/SUBNET_NAME`

    ![ID de ressource de réseau virtuel](media/kubernetes-aks-engine-custom-vnet/virtual-network-id.png)

5. Sélectionnez **Sous-réseaux** dans le panneau **Réseaux virtuels**. Sélectionnez le nom du sous-réseau, par exemple « par défaut ».
    
    ![Bloc CIDR du réseau virtuel](media/kubernetes-aks-engine-custom-vnet/virtual-network-cidr-block.png)
    
6. Dans le panneau du sous-réseau, prenez note de la plage d’adresses et du bloc CIDR du réseau virtuel, par exemple : `10.1.0.0 - 10.1.0.255 (256 addresses)` et `10.1.0.0/24`.

## <a name="considerations-for-selecting-an-address-space"></a>Considérations relatives à la sélection d’un espace d’adressage

Lorsque vous créez un réseau virtuel personnalisé, vous spécifiez l’espace d’adressage IP de votre réseau et une plage d’adresses IP pour chaque sous-réseau. Tenez compte des facteurs suivants lorsque vous choisissez les espaces d’adressage et les plages d’adresses à utiliser dans votre cluster Kubernetes :
-  Le chevauchement des espaces d’adressage peut entraîner des erreurs de communication ou des conflits d’adresses IP. Pour réduire le risque de chevauchement des adresses IP, choisissez un espace d’adressage unique pour votre nouveau réseau virtuel.
-  Les espaces d’adressage dans les plages `10.` et `172.` sont souvent utilisés pour les réseaux privés et peuvent être utilisés par votre infrastructure de centre de données existante. Si vos applications Kubernetes utilisent des ressources dans votre centre de données, réduisez les risques de conflit en choisissant un espace d’adressage pour votre réseau virtuel personnalisé, qui soit différent de l’espace d’adressage de votre centre de données.
-  Nous vous recommandons d’utiliser un sous-réseau dédié pour votre cluster Kubernetes.

## <a name="get-the-ip-address-block"></a>Obtenir le bloc d’adresses IP

Le moteur AKS prend en charge le déploiement sur un réseau virtuel existant. Quand vous déployez sur un sous-réseau existant, votre cluster utilise un bloc d’adresses IP consécutives pour les agents et un autre pour les maîtres.

Vous devez alors définir deux valeurs. Vous devez connaître le nombre d’adresses IP à réserver pour votre cluster et la première adresse IP statique consécutive au sein de l’espace IP du sous-réseau.

Le moteur AKS nécessite une plage allant jusqu’à 16 adresses IP inutilisées quand vous utilisez plusieurs nœuds maîtres. Le cluster va utiliser une seule adresse IP pour chaque maître jusqu’à cinq maîtres. Le moteur AKS a également besoin de réserver une marge constituée des 10 adresses IP qui viennent après le dernier maître. Enfin, une adresse IP supplémentaire est utilisée par l’équilibreur de charge après les maîtres et la marge, ce qui donne un total de 16 adresses IP.

Quand vous placez votre bloc d’adresses IP, le sous-réseau exige les allocations suivantes des adresses IP existantes :
 - Les quatre premières adresses IP et la dernière adresse IP sont réservées et ne peuvent pas être utilisées dans un sous-réseau Azure.
 - Un tampon de 16 adresses IP doit être laissé ouvert.
 - La valeur de la première adresse IP du cluster doit se situer à la fin de l’espace d’adressage pour éviter les conflits d’adresses IP. Si possible, affectez à la propriété `firstConsecutiveStaticIP` une adresse IP proche de la *fin* de l’espace d’adressage IP disponible dans le sous-réseau.

Dans l’exemple suivant, vous pouvez voir comment ces différentes considérations s’appliquent à la plage d’adresses IP d’un sous-réseau. Cet exemple comprend trois maîtres. Si vous utilisez un sous-réseau avec 256 adresses, par exemple 10.1.0.0/24, vous devez définir votre première adresse IP statique consécutive sur 207. Le tableau suivant indique les adresses et considérations :

| Plage pour le sous-réseau /24 | Number | Remarque |
| --- | --- | --- |
| 10.1.0.0  - 10.1.03 | 4 | Réservé dans le sous-réseau Azure. |
| **10.1.0.224** - 10.1.0.238 | 14 | Nombre d’adresses IP pour un cluster défini par le moteur AKS.<br><br> 3 adresses IP pour 3 maîtres<br>10 adresses IP pour la marge<br>1 adresse IP pour l’équilibreur de charge |
| 10.1.0.239 - 10.1.0.255 | 16 | Tampon de 16 adresses IP. |
| 10.1.0.256 | 1 | Réservé dans le sous-réseau Azure. |

Dans cet exemple, la propriété `firstConsecutiveStaticIP` a la valeur `10.1.0.224`.

Pour les sous-réseaux plus larges, par exemple /16 avec plus de 60 000 adresses, il peut s’avérer difficile de définir vos affectations d’adresses IP statiques à la fin de l’espace réseau. Définissez la plage d’adresses IP statiques de votre cluster en dehors des 24 premières adresses de votre espace IP afin que le cluster soit résilient lors de la revendication d’adresses.

## <a name="update-the-api-model"></a>Mettre à jour le modèle d’API

Mettez à jour le modèle d’API utilisé pour déployer le cluster à partir du moteur AKS sur votre réseau virtuel personnalisé.

Dans **masterProfile**, définissez les valeurs suivantes :

| Champ | Exemple | Description |
| --- | --- | --- |
| vnetSubnetId | `/subscriptions/77e28b6a-582f-42b0-94d2-93b9eca60845/resourceGroups/MDBN-K8S/providers/Microsoft.Network/virtualNetworks/MDBN-K8S/subnets/default` | Spécifiez l’ID de ressource du sous-réseau.  |
| firstConsecutiveStaticIP | 10.1.0.224 | Affectez à la propriété de configuration `firstConsecutiveStaticIP` une adresse IP proche de la *fin* de l’espace d’adressage IP disponible dans le sous-réseau souhaité. `firstConsecutiveStaticIP` s’applique uniquement au pool principal. |

Dans **agentPoolProfiles**, définissez les valeurs suivantes :

| Champ | Exemple | Description |
| --- | --- | --- |
| vnetSubnetId | `/subscriptions/77e28b6a-582f-42b0-94d2-93b9eca60845/resourceGroups/MDBN-K8S/providers/Microsoft.Network/virtualNetworks/MDBN-K8S/subnets/default` | Spécifiez l’ID de chemin Azure Resource Manager du sous-réseau.  |

Dans **orchestratorProfile**, recherchez **kubernetesConfig** et définissez la valeur suivante :

| Champ | Exemple | Description |
| --- | --- | --- |
| clusterSubnet | `172.16.244.0/24` | La plage d’adresses IP du sous-réseau de clusters doit utiliser une plage d’adresses IP dans l’espace de la plage d’adresses IP du réseau virtuel personnalisé que vous avez défini. |

Par exemple :

```json
"masterProfile": {
  ...
  "vnetSubnetId": "/subscriptions/77e28b6a-582f-42b0-94d2-93b9eca60845/resourceGroups/MDBN-K8S/providers/Microsoft.Network/virtualNetworks/MDBN-K8S/subnets/default",
  "firstConsecutiveStaticIP": "10.1.0.224",
  ...
},
...
"agentPoolProfiles": [
  {
    ...
    "vnetSubnetId": "/subscriptions/77e28b6a-582f-42b0-94d2-93b9eca60845/resourceGroups/MDBN-K8S/providers/Microsoft.Network/virtualNetworks/MDBN-K8S/subnets/default",
    ...
  },
    ...
"kubernetesConfig": [
  {
    ...
    "clusterSubnet": "172.16.244.0/24",
    ...
  },

```

## <a name="deploy-your-cluster"></a>Déployer votre cluster

Après avoir ajouté les valeurs à votre modèle d’API, vous pouvez déployer votre cluster à partir de votre ordinateur client à l’aide de la commande `deploy` en utilisant le moteur AKS. Pour obtenir des instructions, consultez [Déployer un cluster Kubernetes](azure-stack-kubernetes-aks-engine-deploy-cluster.md#deploy-a-kubernetes-cluster).

## <a name="set-the-route-table"></a>Définir la table de route

Après avoir déployé votre cluster, revenez à votre réseau virtuel dans le portail utilisateur Azure Stack. Définissez la table de routage et le groupe de sécurité réseau (NSG) dans le panneau du sous-réseau. Si vous n’utilisez pas Azure CNI, par exemple, `networkPlugin`: `kubenet` dans l’objet de configuration du modèle d’API `kubernetesConfig`. Une fois que vous avez correctement déployé un cluster sur votre réseau virtuel personnalisé, obtenez l’ID de la ressource Table de routage à partir du panneau **Réseau** dans le groupe de ressources de votre cluster.

1. Ouvrez le portail utilisateur Azure Stack Hub dans votre instance Azure Stack Hub.
2. Sélectionnez **Toutes les ressources**.
3. Entrez le nom de votre réseau virtuel dans la zone de recherche.
4. Sélectionnez **Sous-réseaux**, puis le nom du sous-réseau qui contient votre cluster.
    
    ![Table de routage et groupe de sécurité réseau](media/kubernetes-aks-engine-custom-vnet/virtual-network-route-table.png)
    
5. Sélectionnez **Table de routage**, puis la table de routage de votre cluster.

> [!NOTE]  
> Il existe un [problème connu](https://github.com/Azure/aks-engine/issues/371) dans le réseau virtuel personnalisé d’un cluster Windows Kubernetes.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [le moteur AKS sur Azure Stack Hub](azure-stack-kubernetes-aks-engine-overview.md)  
- Consultez l’article [Vue d’ensemble d’Azure Monitor pour conteneurs](/azure/azure-monitor/insights/container-insights-overview).
