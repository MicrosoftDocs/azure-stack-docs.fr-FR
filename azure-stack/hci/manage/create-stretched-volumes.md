---
title: Créer des volumes et configurer la réplication pour les clusters étendus dans Azure Stack HCI
description: Comment créer des volumes et configurer la réplication pour les clusters étendus dans Azure Stack HCI à l’aide de Windows Admin Center et de PowerShell.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 07/21/2020
ms.openlocfilehash: 647fbbc1fc0ae070955f796aee7aa102290bbc4f
ms.sourcegitcommit: 0e52f460295255b799bac92b40122a22bf994e27
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/21/2020
ms.locfileid: "86867987"
---
# <a name="create-volumes-and-set-up-replication-for-stretched-clusters"></a>Créer des volumes et configurer la réplication pour les clusters étendus

> S’applique à : Azure Stack HCI, version 20H2

Cette rubrique explique comment créer des volumes et configurer la réplication pour les clusters étendus dans Azure Stack HCI à l’aide de Windows Admin Center et de PowerShell. Pour apprendre à créer des volumes sur les clusters à site unique et utiliser des fichiers sur ces volumes, et savoir également comment activer la déduplication et la compression des données sur des volumes, consultez [Créer des volumes](create-volumes.md).

## <a name="create-volumes-and-set-up-replication-for-stretched-clusters-using-windows-admin-center"></a>Créer des volumes et configurer la réplication pour les clusters étendus à l’aide de Windows Admin Center

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

## <a name="create-volumes-for-stretched-clusters-using-powershell"></a>Créer des volumes pour les clusters étendus à l’aide de PowerShell

La création de volumes pour les clusters standard à site unique est différente de celle des clusters étendus (à deux sites). Toutefois, dans les deux scénarios, vous utilisez l’applet de commande `New-Volume` pour créer un disque virtuel, le partitionner et le formater, et pour créer également un volume avec un nom correspondant avant de l’ajouter aux volumes partagés de cluster (CSV).

La création de volumes et de disques virtuels pour les clusters étendus est un peu plus complexe que pour les clusters à site unique. Les clusters étendus nécessitent au minimum quatre volumes : deux volumes de données et deux volumes de journaux, avec une paire de volumes données/journaux résidant sur chaque site. Ainsi, vous allez créer un groupe de réplication pour chaque site, et configurer la réplication entre eux.

Vous devez, avant toute chose, déplacer des groupes de ressources d’un nœud à l’autre. L’applet de commande `Move-ClusterGroup` est utilisée à cette fin.

Tout d’abord, nous déplaçons le groupe de ressources du pool de stockage « Available Storage (Stockage disponible) » vers le nœud Server1 dans Site1 à l’aide de l’applet de commande `Move-ClusterGroup` :

```powershell
Move-ClusterGroup -Cluster ClusterS1 -Name ‘Available Storage’ -Node Server1
```

Ensuite, créez le premier disque virtuel (Disk1) pour le nœud Server1 sur le site Site1 :

```powershell
New-Volume -CimSession Server1 -FriendlyName Disk1 -FileSystem REFS -DriveLetter F -ResiliencySettingName Mirror -Size 10GB -StoragePoolFriendlyName "Storage Pool for Site 1"
```

Créez un deuxième disque virtuel (Disk2) pour le nœud Server1 :

```powershell
New-Volume -CimSession Server1 -FriendlyName Disk2 -FileSystem REFS -DriveLetter G -ResiliencySettingName Mirror -Size 10GB -StoragePoolFriendlyName "Storage Pool for Site 1"
```

À présent, mettez le groupe « Available Storage » en mode hors connexion :

```powershell
Stop-ClusterGroup -Cluster ClusterS1 -Name 'Available Storage'
```

Après cela, déplacez le groupe « Available Storage » sur le nœud Server3 dans Site2 :

```powershell
Move-ClusterGroup -Name 'Available Storage' -Node Server3
```

Créez le premier disque virtuel (Disk3) sur le nœud Server3 dans Site2 :

```powershell
New-Volume -CimSession Server3 -FriendlyName Disk3 -FileSystem REFS -DriveLetter H -ResiliencySettingName Mirror -Size 10GB -StoragePoolFriendlyName "Storage Pool for Site 2"
```

Ensuite, créez un deuxième disque virtuel (Disk4) sur le nœud Server3 :

```powershell
New-Volume -CimSession Server3 -FriendlyName Disk4 -FileSystem REFS -DriveLetter I -ResiliencySettingName Mirror -Size 10GB -StoragePoolFriendlyName "Storage Pool for Site 2"
```

Mettez à présent le groupe `Available Storage` hors connexion, et replacez-le sur l’un des nœuds dans Site1 :

```powershell
Stop-ClusterGroup -Cluster ClusterS1 -Name 'Available Storage'
```

```powershell
Move-ClusterGroup -Cluster ClusterS1 -Name 'Available Storage' -Node Server1
```

À l’aide de l’applet de commande `Get-ClusterResource`, assurez-vous que quatre volumes de disque virtuel ont été créés, deux dans chaque pool de stockage :

```powershell
Get-ClusterResource -Cluster ClusterS1
```

Maintenant, ajoutez Disk1 aux volumes partagés de cluster :

```powershell
Add-ClusterSharedVolume -Name 'Cluster Virtual Disk (Disk1)'
```

## <a name="setup-replication-for-stretched-clusters-using-powershell"></a>Configurer la réplication pour les clusters étendus à l’aide de PowerShell

Lorsque vous utilisez PowerShell pour configurer le réplica de stockage d’un cluster étendu, le disque qui est utilisé pour les données sources doit être ajouté en tant que volume partagé de cluster (CSV). Tous les autres disques doivent demeurer des lecteurs non CSV dans le groupe Available Storage. Ces disques seront ensuite ajoutés en tant que volumes partagés de cluster pendant le processus de création du réplica de stockage.

À l’étape précédente, les disques virtuels ont été ajoutés au moyen de lettres de lecteur, afin de faciliter leur identification. Le réplica de stockage est une réplication de un pour un, ce qui signifie qu’un seul disque peut être répliqué sur un autre disque unique.

### <a name="validate-the-topology-for-replication"></a>Valider la topologie pour la réplication

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

### <a name="create-the-replication-partnership"></a>Créer le partenariat de réplication

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