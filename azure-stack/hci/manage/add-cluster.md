---
title: Ajouter ou supprimer des serveurs pour un cluster Azure Stack HCI
description: Découvrir comment ajouter ou supprimer des nœuds de serveur pour un cluster dans Azure Stack HCI
ms.topic: how-to
author: v-dasis
ms.author: v-dasis
ms.reviewer: jgerend
ms.date: 01/06/2021
ms.openlocfilehash: 8b27859b7afab0a6e279774e43d0269f6d58065a
ms.sourcegitcommit: 1465bca8b7f87ea6f24faf47e86c2ba497943b28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2021
ms.locfileid: "98103128"
---
# <a name="add-or-remove-servers-for-an-azure-stack-hci-cluster"></a>Ajouter ou supprimer des serveurs pour un cluster Azure Stack HCI

> S’applique à : Azure Stack HCI, version 20H2

Vous pouvez facilement ajouter ou supprimer des serveurs pour un cluster dans Azure Stack HCI. N’oubliez pas que chaque nouveau serveur physique doit correspondre étroitement aux autres serveurs du cluster en termes de type de processeur, mémoire, nombre de lecteurs et type et taille des lecteurs.

Chaque fois que vous ajoutez ou supprimez un serveur, vous devez aussi effectuer une validation du cluster par la suite pour vérifier que le cluster fonctionne normalement. Cela s’applique aux clusters étendus et non étendus.

## <a name="obtain-oem-hardware"></a>Obtenir du matériel OEM

La première étape consiste à acquérir du nouveau matériel HCI auprès de votre fabricant OEM d’origine. Reportez-vous toujours à la documentation fournie par le fabricant OEM pour ajouter du matériel de serveur en vue de l’utiliser dans votre cluster.

1. Placez le nouveau serveur physique dans le rack et câblez-le de manière appropriée.
1. Activez les ports du commutateur physique et ajustez les listes de contrôle d’accès (ACL) et les ID de VLAN le cas échéant.
1. Configurez l’adresse IP appropriée dans le contrôleur de gestion de la carte de base (BMC) et appliquez tous les paramètres du BIOS conformément aux instructions du fabricant OEM.
1. Appliquez la base de référence du microprogramme actuel à tous les composants à l’aide des outils fournis par votre fabricant OEM.
1. Exécutez des tests de validation OEM pour garantir l’homogénéité avec les serveurs de cluster existants.

## <a name="add-a-server-to-a-cluster"></a>Ajouter un serveur à un cluster

Une fois que votre serveur s’est correctement lancé, utilisez Windows Admin Center pour joindre le serveur à votre cluster.

:::image type="content" source="media/manage-cluster/add-server.png" alt-text="Écran d’ajout d’un serveur" lightbox="media/manage-cluster/add-server.png":::

1. Dans **Windows Admin Center**, sélectionnez **Gestionnaire de cluster** à partir de la flèche déroulante supérieure.
1. Sous **Connexions de cluster**, sélectionnez le cluster.
1. Sous **Outils**, sélectionnez **Serveurs**.
1. Sous **Serveurs**, sélectionnez l’onglet **Inventaire**.
1. Sous l’onglet **Inventaire**, sélectionnez **Ajouter**.
1. Dans **Nom du serveur**, entrez le nom de domaine complet du serveur à ajouter, cliquez sur **Ajouter**, puis de nouveau sur **Ajouter** en bas.
1. Vérifiez que le serveur s’est correctement ajouté à votre cluster.

## <a name="remove-a-server-from-a-cluster"></a>Supprimer un serveur d’un cluster

Les étapes de suppression d’un serveur de votre cluster sont similaires à celles de l’ajout d’un serveur à un cluster.

N’oubliez pas que lorsque vous supprimez un serveur, vous supprimez également la totalité des machines virtuelles, lecteurs et charges de travail associés à ce serveur.

:::image type="content" source="media/manage-cluster/remove-server.png" alt-text="Boîte de dialogue de suppression d’un serveur" lightbox="media/manage-cluster/remove-server.png":::

1. Dans **Windows Admin Center**, sélectionnez **Gestionnaire de cluster** à partir de la flèche déroulante supérieure.
1. Sous **Connexions de cluster**, sélectionnez le cluster.
1. Sous **Outils**, sélectionnez **Serveurs**.
1. Sous **Serveurs**, sélectionnez l’onglet **Inventaire**.
1. Sous l’onglet **Inventaire**, sélectionnez le serveur à supprimer, puis **Supprimer**.
1. Pour supprimer également tous les lecteurs du serveur du pool de stockage, cochez cette case.
1. Vérifiez que le serveur a correctement été supprimé du cluster.

Chaque fois que vous ajoutez ou supprimez des serveurs dans un cluster, vous devez exécuter un test de validation du cluster.

## <a name="add-server-pairs-to-a-stretched-cluster"></a>Ajouter des paires de serveurs à un cluster étendu

Les clusters étendus nécessitent le même nombre de nœuds serveur et le même nombre de lecteurs dans chaque site. Lorsque vous ajoutez une paire de serveurs à un cluster étendu, leurs lecteurs sont immédiatement ajoutés au pool de stockage des deux sites dans le cluster étendu. Si le pool de stockage de chaque site n’a pas la même taille au moment de l’ajout, il est rejeté. Cela est dû au fait que la taille du pool de stockage doit être identique entre les sites.

Prenez quelques minutes pour visionner la vidéo sur l’ajout de nœuds de serveur à un cluster étendu :

> [!VIDEO https://www.youtube.com/embed/AVHPkRmsZ5Y]

Vous ajoutez ou vous supprimez des serveurs dans un cluster étendu à l’aide de Windows PowerShell. À l’aide des applets de commande [ClusterFaultDomainXML](https://docs.microsoft.com/powershell/module/failoverclusters/get-clusterfaultdomainxml) et [Set-ClusterFaultDomainXML](https://docs.microsoft.com/powershell/module/failoverclusters/set-clusterfaultdomainxml), vous devez d’abord modifier les informations du site (domaine d’erreur) avant d’ajouter les serveurs.

Ensuite, vous pouvez ajouter la paire de serveurs à chaque site en même temps à l’aide de l’applet de commande [Add-ClusterNode](https://docs.microsoft.com/powershell/module/failoverclusters/add-clusternode), ce qui permet également d’ajouter les lecteurs de chaque nouveau serveur.

En règle générale, vous gérez les clusters à partir d’un ordinateur distant plutôt que sur un serveur dans un cluster. Cet ordinateur distant est appelé ordinateur de gestion.

> [!NOTE]
> Quand vous exécutez des commandes PowerShell à partir d’un ordinateur de gestion, incluez le paramètre `-Cluster` dans le nom du cluster que vous gérez.

Commençons !

1. Utilisez les applets de commande PowerShell suivantes pour déterminer l’état du cluster :

    Retourne la liste des serveurs actifs dans le cluster :

     ```powershell
    Get-ClusterNode
    ```

    Retourne les statistiques du pool de stockage de cluster :

    ```powershell
    Get-StoragePool pool*
    ```

    Répertorie les serveurs qui se trouvent sur le site (domaine d’erreur) :

    ```powershell
    Get-ClusterFaultDomain
    ```

1. Ouvrez le fichier `Sites.xml` dans le Bloc-notes ou dans un autre éditeur de texte :

    ```powershell
    Get-ClusterFaultDomainXML | out-file sites.xml
    ```
 
    ```powershell
    notepad
    ```

1. Accédez à l’emplacement où se trouve le fichier `Sites.xml` localement sur votre PC de gestion et ouvrez le fichier. Le fichier `Sites.xml` se présente comme suit :

    ```
    <Topology>
        <Site Name="Site1" Description="" Location="">
            <Node Name="Server1" Description="" Location="">
            <Node Name="Server2" Description="" Location="">
        </Site>
        <Site Name="Site2" Description="" Location="">
            <Node Name="Server3" Description="" Location="">
            <Node Name="Server4" Description="" Location="">
        </Site>
    <Topology>
    ```

1. À l’aide de cet exemple, vous devez ajouter un serveur à chaque site (`Server5`, `Server6`) comme suit :

    ```
    <Topology>
        <Site Name="Site1" Description="" Location="">
            <Node Name="Server1" Description="" Location="">
            <Node Name="Server2" Description="" Location="">
            <Node Name="Server5" Description="" Location="">
        </Site>
        <Site Name="Site2" Description="" Location="">
            <Node Name="Server3" Description="" Location="">
            <Node Name="Server4" Description="" Location="">
            <Node Name="Server6" Description="" Location="">
        </Site>
    <Topology>
    ```

1. Modifiez les informations du site actuel (domaine d’erreur).  La première commande définit une variable pour obtenir le contenu du fichier `Sites.xml` et le générer. La deuxième commande définit la modification en fonction de la variable `$XML`.

    ```
    $XML = Get-Content .\sites.xml | out-string
    Set-ClusterFaultDomainXML -xml $XML
    ```

1. Vérifiez que les modifications que vous avez apportées sont correctes :

    ```
    Get-ClusterFaultDomain
    ```

1. Ajoutez la paire de serveurs à votre cluster à l’aide de l’applet de commande `Add-ClusterNode` :

    ```
    Add-ClusterNode -Name Server5,Server6
    ```

Une fois les serveurs ajoutés, les lecteurs associés sont automatiquement ajoutés aux pools de stockage de chaque site. Enfin, le Service de contrôle d’intégrité crée un travail de stockage pour inclure les nouveaux lecteurs.

## <a name="remove-server-pairs-from-a-stretched-cluster"></a>Supprimer des paires de serveurs d’un cluster étendu

La suppression d’une paire de serveurs d’un cluster étendu est un processus similaire à l’ajout d’une paire de serveurs, mais en utilisant l’applet de commande [Remove-ClusterNode](https://docs.microsoft.com/powershell/module/failoverclusters/remove-clusternode) à la place.

1. Utilisez les applets de commande PowerShell suivantes pour déterminer l’état du cluster :

    Retourne la liste des serveurs actifs dans le cluster :

     ```powershell
    Get-ClusterNode
    ```

    Retourne les statistiques du pool de stockage de cluster :

    ```powershell
    Get-StoragePool pool*
    ```

    Répertorie les serveurs qui se trouvent sur le site (domaine d’erreur) :

    ```powershell
    Get-ClusterFaultDomain
    ```

1. Ouvrez le fichier `Sites.xml` dans le Bloc-notes ou dans un autre éditeur de texte :

    ```powershell
    Get-ClusterFaultDomainXML | out-file sites.xml
    ```
 
    ```powershell
    notepad
    ```

1. À l’aide de l’exemple précédent, dans le fichier `Sites.xml`, supprimez les entrées XML `<Node Name="Server5" Description="" Location="">` et `<Node Name="Server6" Description="" Location="">` pour chaque site.
1. Modifiez les informations du site actuel (domaine d’erreur) à l’aide des deux applets de commande suivantes :

    ```
    $XML = Get-Content .\sites.xml | out-string
    Set-ClusterFaultDomainXML -xml $XML
    ```

1. Vérifiez que les modifications que vous avez apportées sont correctes :

    ```
    Get-ClusterFaultDomain
    ```
1. Supprimez les paires de serveurs du cluster à l’aide de l’applet de commande `Remove-ClusterNode` :

    ```
    Remove-ClusterNode -Name Server5,Server6
    ```

Une fois les serveurs correctement supprimés, les lecteurs associés sont automatiquement supprimés des pools de sites. Enfin, le Service de contrôle d’intégrité crée un travail de stockage pour supprimer ces lecteurs.

## <a name="next-steps"></a>Étapes suivantes

- Vous devez valider le cluster après l’ajout ou la suppression d’un serveur. Pour plus d’informations, consultez [Valider le cluster](../deploy/validate.md).