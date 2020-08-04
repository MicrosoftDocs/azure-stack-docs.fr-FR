---
title: Créer des volumes de clusters étendus et configurer la réplication
description: Comment créer des volumes et configurer la réplication pour les clusters étendus dans Azure Stack HCI à l’aide de Windows Admin Center et de PowerShell.
author: v-dasis
ms.author: v-dasis
ms.topic: how-to
ms.date: 07/24/2020
ms.openlocfilehash: 9d6ba44da5da188f60f031bddab2e49190dbfee3
ms.sourcegitcommit: b2337a9309c52aac9f5a1ffd89f1426d6c178ad5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87250705"
---
# <a name="create-stretched-cluster-volumes-and-set-up-replication"></a>Créer des volumes de clusters étendus et configurer la réplication

> S’applique à : Azure Stack HCI, version 20H2

Cet article explique comment créer des volumes et configurer la réplication pour les clusters étendus dans Azure Stack HCI à l’aide de Windows Admin Center et de PowerShell.

En guise d’exemple, vous allez créer des volumes sur 4 serveurs dans 2 sites, soit 2 serveurs par site. Toutefois, gardez à l’esprit que si vous souhaitez créer des volumes en miroir triple, vous devez disposer d’au moins 6 serveurs, soit 3 serveurs par site.

## <a name="stretched-volumes-and-replication-using-windows-admin-center"></a>Volumes étendus et réplication à l’aide de Windows Admin Center

Commençons !

1. Dans Windows Admin Center, sous **Outils**, sélectionnez **Volumes**.
1. Dans le volet droit, sélectionnez l’onglet **Inventaire**, puis sélectionnez **Créer**.
1. Dans le panneau **Créer un volume**, sélectionnez **Répliquer le volume entre les sites**.
1. Sélectionnez un sens de réplication entre les sites dans la zone de liste déroulante.
1. Sous **Mode de réplication**, sélectionnez **Asynchrone** ou **Synchrone**.
1. Entrez un nom de groupe de réplication source et un nom de groupe de réplication de destination.
1. Entrez la taille souhaitée pour le volume des journaux.
1. Sous **Avancé**, vous pouvez également effectuer les opérations suivantes :
     - Entrer ou modifier le **Nom du groupe de réplication source**.
     - Entrer ou modifier le **Nom du groupe de réplication de destination**.
     - Pour **utiliser des blocs déjà amorcés sur la cible**..., cochez cette case.
     - Pour **chiffrer le trafic de réplication**, cochez cette case.
     - Pour **activer les groupes de cohérence**, cochez cette case.
1. Lorsque vous avez terminé, cliquez sur **Créer**.
1. Dans le volet droit, vérifiez qu’un disque de données et un disque de journaux sont créés dans votre site principal (actif), et que les disques de réplica de données et de journaux correspondants sont créés dans le site secondaire (passif). Pour la réplication bidirectionnelle, vous devez voir deux jeux de disques de données et de disques volume.
1. Sous **Outils**, sélectionnez **Réplica de stockage**.
1. Dans le volet droit, sous **Partenariats**, vérifiez que le partenariat de réplication a été correctement créé.

Ensuite, vous devez vérifier que la réplication des données entre les sites s’est bien déroulée avant de déployer les machines virtuelles et d’autres charges de travail. Pour plus d’informations, consultez la section Vérification de la réplication dans [Valider le cluster](../deploy/validate.md).

## <a name="create-stretched-volumes-using-powershell"></a>Créer des volumes étendus à l’aide de PowerShell

La création de volumes pour les clusters standard à site unique est différente de celle des clusters étendus (à deux sites). Toutefois, dans les deux scénarios, vous utilisez l’applet de commande `New-Volume` pour créer un disque virtuel, le partitionner et le formater, et pour créer également un volume avec un nom correspondant avant de l’ajouter aux volumes partagés de cluster (CSV).

La création de volumes et de disques virtuels pour les clusters étendus est un peu plus complexe que pour les clusters à site unique. Les clusters étendus nécessitent au minimum quatre volumes : deux volumes de données et deux volumes de journaux, avec une paire de volumes données/journaux résidant sur chaque site. Ainsi, vous allez créer un groupe de réplication pour chaque site, et configurer la réplication entre eux. Vous devez déplacer des groupes de ressources d’un serveur à un autre. L’applet de commande `Move-ClusterGroup` est utilisée à cette fin.

1. Tout d’abord, déplacez le groupe de ressources du pool de stockage `Available Storage` vers `Server1` dans `Site1` à l’aide de l’applet de commande `Move-ClusterGroup` :

    ```powershell
    Move-ClusterGroup -Cluster ClusterS1 -Name ‘Available Storage’ -Node Server1
    ```

1. Ensuite, créez le premier disque virtuel (`Disk1`) pour `Server1` dans `Site1` :

    ```powershell
    New-Volume -CimSession Server1 -FriendlyName Disk1 -FileSystem REFS -DriveLetter F -ResiliencySettingName Mirror -Size 10GB -StoragePoolFriendlyName "Storage Pool for Site 1"
    ```

1. Créez un deuxième disque virtuel (`Disk2`) pour `Server1` dans `Site1` :

    ```powershell
    New-Volume -CimSession Server1 -FriendlyName Disk2 -FileSystem REFS -DriveLetter G -ResiliencySettingName Mirror -Size 10GB -StoragePoolFriendlyName "Storage Pool for Site 1"
    ```

1. Mettez à présent le groupe `Available Storage` hors connexion :

    ```powershell
    Stop-ClusterGroup -Cluster ClusterS1 -Name 'Available Storage'
    ```

1. Ensuite, déplacez le groupe `Available Storage` vers `Server3` dans `Site2` :

    ```powershell
    Move-ClusterGroup -Name 'Available Storage' -Node Server3
    ```

1. Créez le premier disque virtuel (`Disk3`) sur `Server3` dans `Site2` :

    ```powershell
    New-Volume -CimSession Server3 -FriendlyName Disk3 -FileSystem REFS -DriveLetter H -ResiliencySettingName Mirror -Size 10GB -StoragePoolFriendlyName "Storage Pool for Site 2"
    ```

1. Ensuite, créez un deuxième disque virtuel (`Disk4`) sur `Server3` dans `Site2` :

    ```powershell
    New-Volume -CimSession Server3 -FriendlyName Disk4 -FileSystem REFS -DriveLetter I -ResiliencySettingName Mirror -Size 10GB -StoragePoolFriendlyName "Storage Pool for Site 2"
    ```

1. Mettez à présent le groupe `Available Storage` hors connexion, puis replacez-le sur l’un des serveurs dans `Site1` :

    ```powershell
    Stop-ClusterGroup -Cluster ClusterS1 -Name 'Available Storage'
    ```

    ```powershell
    Move-ClusterGroup -Cluster ClusterS1 -Name 'Available Storage' -Node Server1
    ```

1. À l’aide de l’applet de commande `Get-ClusterResource`, assurez-vous que quatre volumes de disque virtuel ont été créés, deux dans chaque pool de stockage :

    ```powershell
    Get-ClusterResource -Cluster ClusterS1
    ```

1. Ajoutez à présent `Disk1` aux volumes partagés de cluster :

    ```powershell
    Add-ClusterSharedVolume -Name 'Cluster Virtual Disk (Disk1)'
    ```

Vous avez terminé de créer des volumes et vous êtes prêt à configurer le réplica de stockage pour la réplication.

## <a name="set-up-replication-using-powershell"></a>Configurer la réplication à l’aide de PowerShell

Lorsque vous utilisez PowerShell pour configurer le réplica de stockage d’un cluster étendu, le disque qui est utilisé pour les données sources doit être ajouté en tant que volume partagé de cluster (CSV). Tous les autres disques doivent demeurer des lecteurs non CSV dans le groupe Available Storage. Ces disques seront ensuite ajoutés en tant que volumes partagés de cluster pendant le processus de création du réplica de stockage.

À l’étape précédente, les disques virtuels ont été ajoutés au moyen de lettres de lecteur, afin de faciliter leur identification. Le réplica de stockage est une réplication de un pour un, ce qui signifie qu’un seul disque peut être répliqué sur un autre disque unique.

### <a name="step-1-validate-the-topology-for-replication"></a>Étape 1 : Valider la topologie pour la réplication

Avant de commencer, vous devez exécuter l’applet de commande `Test-SRTopology` sur une période prolongée (par exemple, plusieurs heures). L’applet de commande `Test-SRTopology` valide un partenariat de réplication potentiel, puis valide l’hôte local sur le serveur de destination ou à distance entre les serveurs source et de destination.

Cette applet de commande vérifie les éléments suivants :

- Le protocole SMB est accessible sur le réseau, ce qui signifie que le port TCP 445 et le port 5445 sont ouverts de manière bidirectionnelle.
- WS-MAN est accessible via HTTP sur le réseau, ce qui signifie que les ports TCP 5985 et 5986 sont ouverts.
- Un fournisseur SR WMIv2 est accessible et accepte les requêtes.
- Les volumes de données sources et de destination existent et sont accessibles en écriture.
- Les volumes de journaux sources et de destination existent avec le format NTFS ou le format ReFS, de même qu’un espace libre suffisant.
- Le stockage est initialisé au format GPT, et non MBR, avec des tailles de secteur correspondantes.
- La mémoire physique est suffisante pour exécuter la réplication.

Par ailleurs, l’applet de commande `Test-SRTopology` mesurera également :

- La latence des boucles d’ICMP pour en indiquer la moyenne.
- Les compteurs de performances des entrées/sorties en écriture pour indiquer la moyenne observée sur ce volume.
- La durée estimée de la synchronisation initiale.

À l’issue de l’exécution de Test-SRTopology, un fichier .html (TestSrTopologyReport avec date et heure) sera créé dans votre dossier temporaire Windows. Les avertissements ou les échecs doivent être examinés, car ils peuvent entraîner l’impossibilité de créer convenablement le réplica de stockage.

Voici un exemple de commande pouvant s’exécuter pendant 5 heures :

```powershell
Test-SRTopology -SourceComputerName Server1 -SourceVolumeName W: -SourceLogVolumeName X: -DestinationComputerName Server3 -DestinationVolumeName Y: -DestinationLogVolumeName Z: -DurationInMinutes 300 -ResultPath c:\temp
```

### <a name="step-2-create-the-replication-partnership"></a>Étape 2 : Créer le partenariat de réplication

Maintenant que vous avez terminé les tests `Test-SRTopology`, vous êtes prêt à configurer le réplica de stockage et à créer le partenariat de réplication. En résumé, nous allons configurer le réplica de stockage en créant des groupes de réplication (RG) pour chaque site, et en spécifiant les volumes de données et les volumes de journaux pour les nœuds de serveur source dans Site1 (Server1, Server2) et les nœuds de serveur de destination (répliqués) dans Site2 (Server3, Server4).

Commençons !

1. Ajoutez le disque de données Site1 en tant que volume partagé de cluster (CSV) :

   ```powershell
   Add-ClusterSharedVolume -Name "Cluster Virtual Disk (Site1)"
   ```

1. Le groupe Available Storage doit être « détenu » par le nœud sur lequel il se trouve actuellement. Le groupe peut être déplacé vers Server1 en utilisant :

   ```powershell
   Move-ClusterGroup -Name “Available Storage” -Node Server1
   ```

1. Pour créer le partenariat de réplication, utilisez l’applet de commande `New-SRPartnership`. Cette applet de commande vous sert également à spécifier les noms des volumes sources de données et de journaux :

   ```powershell
   New-SRPartnership -SourceComputerName "Server1" -SourceRGName "Replication1" -SourceVolumeName "C:\ClusterStorage\Disk1\" -SourceLogVolumeName "G:" -DestinationComputerName "Server3" -DestinationRGName "Replication2" -DestinationVolumeName "H:" -DestinationLogVolumeName "I:"
   ```

L’applet de commande `New-SRPartnership` crée un partenariat de réplication entre les deux groupes de réplication pour les deux sites. Dans cet exemple, `Replication1` est le groupe de réplication du nœud principal, Server1 dans Site1, et `Replication2` est le groupe de réplication du nœud de destination, Server3 dans Site2.

Le réplica de stockage va maintenant s’occuper de tout. Si des données doivent être répliquées, cette opération se déroulera ici. Selon la quantité de données à répliquer, cette opération peut prendre un certain temps. Il est recommandé de ne pas déplacer les groupes tant que ce processus n’est pas terminé.

## <a name="next-steps"></a>Étapes suivantes

Pour consulter des rubriques connexes et d’autres tâches de gestion du stockage, référez-vous également à :

- [Vue d’ensemble du cluster étendu](../concepts/stretched-clusters.md)
- [Planifier des volumes](../concepts/plan-volumes.md)
- [Étendre les volumes](extend-volumes.md)
- [Supprimer des volumes](delete-volumes.md)
