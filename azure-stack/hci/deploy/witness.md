---
title: Configurer un témoin de cluster
description: Découvrez comment configurer un témoin de cluster
author: v-dasis
ms.topic: how-to
ms.date: 07/21/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: e660d1cc10e5d4b4e3d1ffbebeee99e421cd6dbb
ms.sourcegitcommit: a15a0f955bac922cebb7bf90a72384fd84ddfe56
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86947467"
---
# <a name="set-up-a-cluster-witness"></a>Configurer un témoin de cluster

> S’applique à Azure Stack HCI, version 20H2 ; Windows Server 2019

La configuration d’une ressource témoin est obligatoire pour tous les clusters et doit être configurée juste après la création d’un cluster. Dans les clusters à deux nœuds, un témoin est nécessaire pour que l’un des deux nœuds demeure disponible en cas de mise hors connexion de l’autre serveur. Dans les clusters à trois nœuds et plus, un témoin est nécessaire pour pallier la défaillance ou la mise hors connexion de deux serveurs.  

Vous pouvez utiliser un partage de fichiers en tant que témoin ou bien utiliser un témoin de cloud Azure. Un témoin de cloud Azure est recommandé, à condition que tous les nœuds de serveur du cluster disposent d’une connexion Internet fiable. Pour plus d’informations, consultez [Déployer un témoin de cloud pour un cluster de basculement](/windows-server/failover-clustering/deploy-cloud-witness).

Pour les témoins de partage de fichiers, le serveur de fichiers doit remplir certaines conditions. Pour plus d’informations, consultez [Avant de déployer Azure Stack HCI](before-you-start.md).

## <a name="set-up-a-witness-using-windows-admin-center"></a>Configurer un témoin en utilisant Windows Admin Center

1. Dans Windows Admin Center, sélectionnez **Gestionnaire de cluster** à partir de la flèche déroulante du haut.
1. Sous **Connexions de cluster**, sélectionnez le cluster.
1. Sous **Outils**, sélectionnez **Paramètres**.
1. Dans le volet droit, sélectionnez **Témoin**.
1. Pour **Type de témoin**, sélectionnez une des valeurs suivantes :
      - **Témoin de cloud** - entrez le nom, la clé et le point de terminaison de votre compte de stockage Azure
      - **Témoin de partage de fichiers** - entrez le chemin du partage de fichiers (//serveur/partage)

> [!NOTE]
> La troisième option, **Témoin de disque**, ne convient pas pour une utilisation dans des clusters étendus.

## <a name="set-up-a-witness-using-windows-powershell"></a>Configurer un témoin en utilisant Windows PowerShell

Pour configurer un témoin de cluster avec PowerShell, exécutez une des applets de commande suivantes.

Utilisez l’applet de commande suivante pour créer un témoin de cloud Azure :

```powershell
Set-ClusterQuorum –Cluster Cluster1 -CloudWitness -AccountName <AzureStorageAccountName> -AccessKey <AzureStorageAccountAccessKey>
```

Utilisez l’applet de commande suivante pour créer un témoin de partage de fichiers :

```powershell
Set-ClusterQuorum –Cluster Cluster1 -FileShareWitness \\fileserver\fsw
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le quorum du cluster, consultez [Présentation du quorum de cluster et de pool dans Azure Stack HCI](../concepts/quorum.md).
