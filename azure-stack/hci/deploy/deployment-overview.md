---
title: Vue d’ensemble du déploiement d’Azure Stack HCI
description: Vue d’ensemble du processus de déploiement d’Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/09/2020
ms.openlocfilehash: 9b851b59093a85f7ddd5f9df47359784f41fe24e
ms.sourcegitcommit: 4af79f4fa2598d57c81e994192c10f8c6be5a445
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89742156"
---
# <a name="what-is-the-deployment-process-for-azure-stack-hci"></a>Quel est le processus de déploiement pour Azure Stack HCI ?

> S’applique à : Azure Stack HCI, version 20H2

Cette rubrique offre une vue générale des étapes du processus de déploiement d’Azure Stack HCI, avec des liens vers des informations plus détaillées.

## <a name="plan"></a>Plan

Avant d’effectuer le déploiement, planifiez soigneusement votre stockage, votre réseau et vos besoins pour le clustering multisite (le cas échéant).

### <a name="plan-storage"></a>Planifier le stockage

Azure Stack HCI utilise des serveurs standard avec des lecteurs connectés localement pour créer un stockage à définition logicielle hautement disponible et hautement scalable. Pour répondre à vos besoins en matière de performances et de capacité, [choisissez les lecteurs](../concepts/choose-drives.md) et [planifier les volumes](../concepts/plan-volumes.md) avec soin.

### <a name="plan-networking"></a>Planifier la mise en réseau

Notez les noms de serveur, les noms de domaine, les protocoles et versions RDMA et l’ID du VLAN pour votre déploiement.

### <a name="plan-stretched-clusters"></a>Planifier des clusters étendus

Si votre déploiement d’Azure Stack HCI s’étend sur plusieurs sites, déterminez le nombre de serveurs nécessaires sur chaque site et la nature de la configuration du cluster (active/passive ou active/active). Pour plus d’informations, consultez [À propos des clusters étendus](../concepts/stretched-clusters.md).

## <a name="deploy"></a>Déployer

### <a name="1-before-you-begin"></a>1. Avant de commencer

Avant de commencer, [déterminez si votre matériel répond aux exigences de base et collectez les informations nécessaires](before-you-start.md) pour le déploiement d’Azure Stack HCI. Ensuite, [installez Windows Admin Center](/windows-server/manage/windows-admin-center/deploy/install) pour gérer votre cluster Azure Stack HCI.

### <a name="2-deploy-azure-stack-hci"></a>2. Déployer Azure Stack HCI

[Téléchargez Azure Stack HCI](https://azure.microsoft.com/en-us/products/azure-stack/hci/hci-download/) et déployez le [système d’exploitation](operating-system.md) Azure Stack HCI sur chacun des serveurs que vous souhaitez mettre en cluster.

### <a name="3-create-the-cluster"></a>3. Créer le cluster

Créez un cluster de basculement à l’aide de [Windows Admin Center](create-cluster.md) ou [PowerShell](create-cluster-powershell.md). Pour la reprise d’activité et la continuité de l’activité natives, vous pouvez déployer un [cluster étendu](../concepts/stretched-clusters.md) qui englobe deux sites géographiquement distincts.

### <a name="4-set-up-a-cluster-witness"></a>4. Configurer un témoin de cluster

La [configuration d’une ressource témoin](witness.md) est obligatoire pour tous les clusters. Dans les clusters à deux nœuds, un témoin est nécessaire pour que l’un des deux nœuds demeure disponible en cas de mise hors connexion de l’autre serveur. Dans les clusters à trois nœuds et davantage, un témoin est nécessaire pour pallier la défaillance ou la mise hors connexion de deux serveurs. 

### <a name="5-register-with-azure"></a>5. Inscription auprès d’Azure

Azure Stack HCI nécessite une connexion à Azure. Pour connecter votre cluster à Azure, consultez [Inscrire Azure Stack HCI auprès d’Azure](register-with-azure.md). Une fois inscrit, le cluster se connecte automatiquement en arrière-plan.

### <a name="6-validate-the-cluster"></a>6. Valider le cluster

Après avoir créé et inscrit le cluster, [exécutez les tests de validation du cluster](validate.md) pour détecter les problèmes de configuration ou matériels avant que le cluster ne passe en production.

### <a name="7-deploy-storage"></a>7. Déployer le stockage

[Créez des volumes](../manage/create-volumes.md) sur un cluster à site unique, ou [créez des volumes et configurez la réplication sur un cluster étendu](../manage/create-stretched-volumes.md).

### <a name="8-deploy-workloads"></a>8. Déployer les charges de travail

Vous êtes maintenant prêt à [créer des machines virtuelles](../manage/vm.md) et à déployer des charges de travail sur Azure Stack HCI à l’aide de Windows Admin Center.

## <a name="next-steps"></a>Étapes suivantes

Découvrez ce que vous devez faire avant de déployer Azure Stack HCI.

> [!div class="nextstepaction"]
> [Avant de commencer](before-you-start.md)
