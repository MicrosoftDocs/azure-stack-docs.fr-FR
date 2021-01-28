---
title: Configurer un témoin de cluster
description: Découvrez comment configurer un témoin de cluster
author: v-dasis
ms.topic: how-to
ms.date: 01/21/2021
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: cb964bafae7dd9252b386c30a12251e89fc8ead5
ms.sourcegitcommit: e772df8ac78c86d834a68d1a8be83b7f738019b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98782010"
---
# <a name="set-up-a-cluster-witness"></a>Configurer un témoin de cluster

> S’applique à Azure Stack HCI, version 20H2 ; Windows Server 2019

La configuration d’une ressource témoin est obligatoire pour tous les clusters et doit être configurée juste après la création d’un cluster. Dans les clusters à deux nœuds, un témoin est nécessaire pour que l’un des deux nœuds demeure disponible en cas de mise hors connexion de l’autre serveur. Dans les clusters à trois nœuds et plus, un témoin est nécessaire pour pallier la défaillance ou la mise hors connexion de deux serveurs.  

Vous pouvez utiliser un partage de fichiers SMB en tant que témoin ou bien utiliser un témoin cloud Azure. Un témoin de cloud Azure est recommandé, à condition que tous les nœuds de serveur du cluster disposent d’une connexion Internet fiable. Pour plus d’informations, consultez [Déployer un témoin de cloud pour un cluster de basculement](/windows-server/failover-clustering/deploy-cloud-witness).

Pour les témoins de partage de fichiers, le serveur de fichiers doit remplir certaines conditions. Pour plus d'informations, consultez [Configuration requise](../concepts/system-requirements.md).

## <a name="set-up-a-witness-using-windows-admin-center"></a>Configurer un témoin en utilisant Windows Admin Center

1. Dans Windows Admin Center, sélectionnez **Gestionnaire de cluster** à partir de la flèche déroulante du haut.
1. Sous **Connexions de cluster**, sélectionnez le cluster.
1. Sous **Outils**, sélectionnez **Paramètres**.
1. Dans le volet droit, sélectionnez **Témoin**.
1. Pour **Type de témoin**, sélectionnez une des valeurs suivantes :
      - **Témoin cloud** : entrez le nom, la clé d’accès et l’URL du point de terminaison de votre compte de stockage Azure
      - **Témoin de partage de fichiers** : entrez le chemin d’accès de partage de fichiers « (//serveur/partage) »

> [!NOTE]
> La troisième option, **Témoin de disque**, ne convient pas pour une utilisation dans des clusters étendus.

## <a name="create-an-azure-storage-account-to-use-as-a-cloud-witness"></a>Créer un compte de stockage Azure à utiliser comme témoin cloud

Cette section décrit comment créer un compte de stockage et afficher et copier des URL du point de terminaison et des clés d’accès pour ce compte.

Pour configurer un témoin cloud, vous devez disposer d’un compte de stockage Azure valide qui peut être utilisé pour stocker le fichier blob (utilisé pour l’arbitrage). Le témoin cloud crée un conteneur bien connu **msft-cloud-witness** sous le compte de stockage Microsoft. Le témoin cloud écrit un seul fichier blob avec l’ID unique du cluster correspondant utilisé comme nom de fichier du fichier blob sous ce conteneur **msft-cloud-witness**. Cela signifie que vous pouvez utiliser le même compte de stockage Microsoft Azure pour configurer un témoin cloud pour plusieurs clusters différents.

Lorsque vous utilisez le même compte de stockage Azure pour configurer le témoin cloud pour plusieurs clusters différents, un seul conteneur **msft-cloud-witness** est créé automatiquement. Ce conteneur contient un fichier à un blob par cluster.

> [!NOTE]  
> Le témoin cloud utilise le protocole HTTPS (port 443 par défaut) pour établir la communication avec le service blob Azure. Assurez-vous que le port HTTPS est accessible via le proxy.

### <a name="to-create-an-azure-storage-account"></a>Pour créer un compte de stockage Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Dans le menu hub, sélectionnez Nouveau -> Données + stockage -> Compte de stockage.
1. Dans la page Créer un compte de stockage, procédez comme suit :
    1. Entrez un nom pour votre compte de stockage.
    <br>Les noms de compte de stockage doivent avoir entre 3 et 24 caractères, uniquement des lettres minuscules et des chiffres. Le nom du compte de stockage doit être unique dans Azure.
    1. Pour **Type de compte**, sélectionnez **Usage général**.
    <br>Vous ne pouvez pas utiliser un compte de stockage blob pour un témoin cloud.
    1. Pour **Performances**, sélectionnez **Standard**.
    <br>Vous ne pouvez pas utiliser le Stockage Premium Azure pour un témoin cloud.
    1. Pour **Réplication**, sélectionnez **Stockage localement redondant (LRS)** .
    <br>Le clustering de basculement utilise le fichier blob comme point d’arbitrage, ce qui nécessite des garanties de cohérence lors de la lecture des données. Cependant, vous devez sélectionner Pour **Stockage localement redondant (LRS)** pour le type **Réplication**.

### <a name="view-and-copy-storage-access-keys-for-your-azure-storage-account"></a>Afficher et copier les clés d’accès de stockage pour votre compte de stockage Azure

Lorsque vous créez un compte de stockage Microsoft Azure, il est associé à deux clés d’accès générées automatiquement : clé d’accès principale et clé d’accès secondaire. Pour une première création de témoin cloud, utilisez la **Clé d’accès principale**. Il n’existe aucune restriction quant à la clé à utiliser pour le témoin cloud.  

#### <a name="to-view-and-copy-storage-access-keys"></a>Afficher et copier les clés d’accès de stockage

Dans le Portail Azure, accédez à votre compte de stockage, cliquez sur **Tous les paramètres**, puis cliquez sur **Clés d’accès** pour afficher, copier et régénérer les clés d’accès de votre compte. Le panneau Clés d’accès inclut également des chaînes de connexion préconfigurées utilisant vos clés principales et secondaires, que vous pouvez copier pour utilisation dans vos applications.

:::image type="content" source="media/witness/cloud-witness-1.png" alt-text="Clés d’accès témoin cloud" lightbox="media/witness/cloud-witness-1.png":::

### <a name="view-and-copy-endpoint-url-links"></a>Afficher et copier des liens URL de point de terminaison

Lorsque vous créez un compte de stockage, les URL suivantes sont générées à l’aide du format : `https://<Storage Account Name>.<Storage Type>.<Endpoint>`  

Le témoin cloud utilise toujours **Blob** comme type de stockage. Azure utilise **.core.windows.net** comme Point de terminaison. Lors de la configuration d’un témoin cloud, il est possible de le configurer avec un autre point de terminaison selon votre scénario (par exemple, le centre de données Microsoft Azure en Chine a un point de terminaison différent).  

> [!NOTE]  
> L’URL du point de terminaison est générée automatiquement par la ressource du témoin cloud et aucune étape supplémentaire de configuration n’est nécessaire pour l’URL.  

#### <a name="to-view-and-copy-endpoint-url-links"></a>Pour afficher et copier des liens URL de point de terminaison

Dans le Portail Azure, accédez à votre compte de stockage, cliquez sur **Tous les paramètres**, puis sur **Propriétés** pour afficher et copier les URL de votre point de terminaison.  

:::image type="content" source="media/witness/cloud-witness-2.png" alt-text="URL du point de terminaison témoin cloud" lightbox="media/witness/cloud-witness-2.png":::  

## <a name="set-up-a-witness-using-windows-powershell"></a>Configurer un témoin en utilisant Windows PowerShell

Pour configurer un témoin de cluster avec PowerShell, exécutez une des applets de commande suivantes.

Utilisez l’applet de commande suivante pour créer un témoin de cloud Azure :

```powershell
Set-ClusterQuorum –Cluster "Cluster1" -CloudWitness -AccountName "AzureStorageAccountName" -AccessKey "AzureStorageAccountAccessKey"
```

Utilisez l’applet de commande suivante pour créer un témoin de partage de fichiers :

```powershell
Set-ClusterQuorum -FileShareWitness "\\fileserver\share" -Credential (Get-Credential)
```

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur le quorum du cluster, consultez [Présentation du quorum de cluster et de pool dans Azure Stack HCI](../concepts/quorum.md).

- Pour plus d'informations sur la création et la gestion des comptes de stockage, consultez [À propos des comptes de stockage Azure](/azure/storage/common/storage-account-create).