---
title: Configurer un témoin de cluster
description: Découvrez comment configurer un témoin de cluster
author: v-dasis
ms.topic: how-to
ms.date: 02/17/2021
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 32d0f717e987d757f5315cfe048c75300ae9c776
ms.sourcegitcommit: 4c97ed2caf054ebeefa94da1f07cfb6be5929aac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2021
ms.locfileid: "100647905"
---
# <a name="set-up-a-cluster-witness"></a>Configurer un témoin de cluster

> S’applique à Azure Stack HCI, version 20H2 ; Windows Server 2019

La configuration d’une ressource témoin est fortement recommandée pour tous les clusters. Elle devrait intervenir juste après la création d’un cluster. Dans les clusters à deux nœuds, un témoin est nécessaire pour que l’un des deux nœuds demeure disponible en cas de mise hors connexion de l’autre serveur. Dans les clusters à trois nœuds et plus, un témoin est nécessaire pour pallier la défaillance ou la mise hors connexion de deux serveurs.  

Vous pouvez utiliser un partage de fichiers SMB faisant office de témoin, ou un témoin cloud Azure. Un témoin de cloud Azure est recommandé, à condition que tous les nœuds de serveur du cluster disposent d’une connexion Internet fiable. Cet article traite de la création d’un témoin cloud.

## <a name="before-you-begin"></a>Avant de commencer

Pour créer un témoin cloud, vous devez disposer d’un compte et d’un abonnement Azure, et inscrire votre cluster Azure Stack HCI auprès d’Azure. Pour plus d’informations, consultez les articles suivants :

- [Créer un compte Azure](https://docs.microsoft.com/dotnet/azure/create-azure-account)
- Le cas échéant, [créez un abonnement Azure supplémentaire](https://docs.microsoft.com/azure/cost-management-billing/manage/create-subscription).
- [Connecter Azure Stack HCI à Azure](../deploy/register-with-azure.md)

Pour les témoins partages de fichiers, le serveur de fichiers doit répondre à certaines exigences. Pour plus d'informations, consultez [Configuration requise](../concepts/system-requirements.md).

## <a name="create-an-azure-storage-account"></a>Créer un compte de stockage Azure

Cette section décrit comment créer un compte de stockage Azure. Ce compte est utilisé pour stocker un fichier blob Azure utilisé à des fins d’arbitrage pour un cluster spécifique. Vous pouvez utiliser le même compte de stockage Azure afin de configurer un témoin cloud pour plusieurs clusters.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Dans le menu Accueil du portail Azure, sous **Services Azure**, sélectionnez **Comptes de stockage**. Si cette icône est absente, sélectionnez **Créer une ressource** afin de commencer par créer une ressource de *Comptes de stockage*.

    :::image type="content" source="media/witness/cloud-witness-home.png" alt-text="Écran d’accueil du portail Azure" lightbox="media/witness/cloud-witness-home.png":::

1. Sur la page **Comptes de stockage**, sélectionnez **Nouveau**.

    :::image type="content" source="media/witness/cloud-witness-create.png" alt-text="Nouveau compte de stockage Azure" lightbox="media/witness/cloud-witness-create.png":::

1. Sur la page **Créer un compte de stockage**, procédez comme suit :
    1. Sélectionnez l'**abonnement** Azure auquel appliquer le compte de stockage.
    1. Sélectionnez le **Groupe de ressources** Azure auquel appliquer le compte de stockage.
    1. Entrez un **Nom du compte de stockage**.
    <br>Les noms des comptes de stockage doivent comporter entre 3 et 24 caractères, uniquement des lettres minuscules et des chiffres. Ce nom doit également être unique dans Azure.
    1. Sélectionnez un **emplacement** le plus proche de vous physiquement.
    1. Pour **Performances**, sélectionnez **Standard**.
    1. Pour **Type de compte**, sélectionnez **Stockage usage général**.
    1. Pour **Réplication**, sélectionnez **Stockage localement redondant (LRS)** .
    1. Quand vous avez terminé, cliquez sur **Vérifier + créer**.

    :::image type="content" source="media/witness/cloud-witness-create-storage-account.png" alt-text="Créer un compte de stockage Azure" lightbox="media/witness/cloud-witness-create-storage-account.png":::

1. Assurez-vous que le compte de stockage réussit la validation, puis examinez les paramètres du compte. Lorsque vous avez terminé, cliquez sur **Créer**.

    :::image type="content" source="media/witness/cloud-witness-validation.png" alt-text="Validation de compte de stockage Azure" lightbox="media/witness/cloud-witness-validation.png":::

1. Le déploiement de compte dans Azure peut prendre quelques secondes. Une fois le déploiement terminé, cliquez sur **Accéder à la ressource**.

    :::image type="content" source="media/witness/cloud-witness-deployment.png" alt-text="Déploiement de compte de stockage Azure" lightbox="media/witness/cloud-witness-deployment.png":::

## <a name="copy-the-access-key-and-endpoint-url"></a>Copier la clé d’accès et l’URL du point de terminaison

Lorsque vous créez un compte de stockage Azure, le processus génère automatiquement deux clés d’accès, une clé primaire (clé1) et une clé secondaire (clé2). Pour la première création d’un témoin cloud, la clé **clé1** est utilisée. L’URL du point de terminaison est également générée automatiquement.

Un témoin cloud Azure utilise un fichier blob pour le stockage, avec un point de terminaison généré sous la forme *storage_account_name.blob.core.windows.net* en guise de point de terminaison. 

> [!NOTE]  
> Un témoin cloud Azure utilise le protocole HTTPS (port par défaut 443) pour établir la communication avec le service BLOB Azure. Vérifiez que le port HTTPS est accessible.

### <a name="copy-the-account-name-and-access-key"></a>Copier le nom du compte et la clé d’accès

1. Dans le portail Azure, sous **Paramètres**, sélectionnez **Clés d’accès**.
1. Sélectionnez **Afficher les clés** pour afficher les informations de la clé.
1. Cliquez sur l’icône de copier-coller à droite des champs **Nom du compte de stockage** et **clé1**, puis collez chaque chaîne de texte dans le Bloc-notes ou un autre éditeur de texte.

    :::image type="content" source="media/witness/cloud-witness-access-keys.png" alt-text="Clé d’accès de compte de stockage Azure" lightbox="media/witness/cloud-witness-access-keys.png":::

### <a name="copy-the-endpoint-url-optional"></a>Copier l’URL du point de terminaison (facultatif)

L’URL du point de terminaison est facultative et n’est pas forcément nécessaire pour un témoin cloud.

1. Dans le portail Azure, sélectionnez **Propriétés**.
1. Sélectionnez **Afficher les clés** pour afficher les informations du point de terminaison.
1. Sous **Service BLOB**, cliquez sur l’icône copier-coller à droite du champ **Service BLOB**, puis collez la chaîne de texte dans le Bloc-notes ou un autre éditeur de texte.

    :::image type="content" source="media/witness/cloud-witness-blob-service.png" alt-text="Point de terminaison de blob Azure" lightbox="media/witness/cloud-witness-blob-service.png":::

## <a name="create-a-cloud-witness-using-windows-admin-center"></a>Configurer un témoin cloud à l’aide du Windows Admin Center

Vous êtes maintenant prêt à créer une instance témoin pour votre cluster à l’aide du Windows Admin Center.

1. Dans Windows Admin Center, sélectionnez **Gestionnaire de cluster** à partir de la flèche déroulante du haut.
1. Sous **Connexions de cluster**, sélectionnez le cluster.
1. Sous **Outils**, sélectionnez **Paramètres**.
1. Dans le volet droit, sélectionnez **Témoin**.
1. Pour **Type de témoin**, sélectionnez une des valeurs suivantes :
      - **Témoin cloud** : entrez le nom, la clé d’accès et l’URL du point de terminaison de votre compte de stockage Azure comme décrit précédemment.
      - **Témoin de partage de fichiers** : entrez le chemin d’accès de partage de fichiers « (//serveur/partage) »
1. Pour un témoin cloud, pour les champs suivants, collez les chaînes de texte que vous avez copiées précédemment pour :
    1. **Nom du compte de stockage Azure**
    1. **Clé d'accès de stockage Azure**
    1. **Point de terminaison du service Azure**

    :::image type="content" source="media/witness/cloud-witness-1.png" alt-text="Clés d’accès témoin cloud" lightbox="media/witness/cloud-witness-1.png":::

1. Lorsque vous avez terminé, cliquez sur **Enregistrer**. La propagation des informations vers Azure peut prendre un peu.

> [!NOTE]
> La troisième option, **Témoin de disque**, ne convient pas pour une utilisation dans des clusters étendus.

## <a name="create-a-cloud-witness-using-windows-powershell"></a>Créer un témoin cloud à l’aide de Windows PowerShell

Vous pouvez également créer une instance témoin pour votre cluster à l’aide de PowerShell.

Utilisez l’applet de commande suivante pour créer un témoin cloud Azure. Entrez le nom du compte de stockage Azure et les informations de la clé d’accès comme décrit précédemment :

```powershell
Set-ClusterQuorum –Cluster "Cluster1" -CloudWitness -AccountName "AzureStorageAccountName" -AccessKey "AzureStorageAccountAccessKey"
```

Utilisez l’applet de commande suivante pour créer un témoin partage de fichiers. Entrez le chemin d’accès au partage du serveur de fichiers :

```powershell
Set-ClusterQuorum -FileShareWitness "\\fileserver\share" -Credential (Get-Credential)
```

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur le quorum du cluster, consultez [Présentation du quorum de cluster et de pool dans Azure Stack HCI](../concepts/quorum.md).

- Pour plus d'informations sur la création et la gestion des comptes de stockage Azure, consultez [Créer un compte de stockage](https://docs.microsoft.com/azure/storage/common/storage-account-create).