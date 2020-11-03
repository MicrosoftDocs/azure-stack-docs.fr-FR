---
title: Mise hors connexion d’un serveur Azure Stack HCI pour maintenance
description: Cette rubrique fournit des instructions sur la façon de suspendre, de purger et de reprendre correctement des serveurs qui exécutent le système d’exploitation Azure Stack HCI à l’aide de Windows Admin Center et de PowerShell.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/23/2020
ms.openlocfilehash: 3b2c462ad8e6db96be5968074ce5478f1ca9c870
ms.sourcegitcommit: 6a51687a98c417a004cd4295ad06ae813e1978cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92641041"
---
# <a name="taking-an-azure-stack-hci-server-offline-for-maintenance"></a>Mise hors connexion d’un serveur Azure Stack HCI pour maintenance

> S’applique à : Azure Stack HCI, version 20H2 ; Windows Server 2019

Avec Azure Stack HCI, le fait de mettre un serveur hors connexion à des fins de maintenance nécessite de mettre hors connexion des parties du stockage qui sont partagées entre tous les serveurs du cluster. Cela nécessite de suspendre le serveur que vous souhaitez mettre hors connexion, de déplacer des rôles et des machines virtuelles vers d’autres serveurs du cluster, et de vérifier que toutes les données sont disponibles sur les autres serveurs du cluster. Ce processus garantit que les données restent sécurisées et accessibles pendant la période de maintenance.

Vous pouvez utiliser, au choix, Windows Admin Center ou PowerShell pour mettre un serveur hors connexion à des fins de maintenance. Cette rubrique décrit les deux méthodes.

   > [!IMPORTANT]
   > Cette rubrique part du principe que vous devez mettre hors tension un serveur physique pour effectuer une opération de maintenance ou le redémarrer pour une raison quelconque. Pour installer les mises à jour sur un cluster Azure Stack HCI, consultez la rubrique [Mettre à jour des clusters Azure Stack HCI](update-cluster.md), qui explique comment utiliser la Mise à jour adaptée aux clusters pour effectuer automatiquement toutes les étapes indiquées tout en mettant également à jour les serveurs et en les redémarrant si nécessaire.

## <a name="take-a-server-offline-using-windows-admin-center"></a>Mettre un serveur hors connexion à l’aide de Windows Admin Center

Le moyen le plus simple de préparer un serveur dans un cluster Azure Stack HCI hors connexion consiste à utiliser Windows Admin Center.

### <a name="verify-its-safe-to-take-the-server-offline"></a>Vérifier qu’il est possible de mettre le serveur hors connexion sans risque

1. À l’aide de Windows Admin Center, connectez-vous au serveur que vous voulez mettre hors connexion. Sélectionnez **Stockage > Disques** dans le menu **Outils** , puis vérifiez que la colonne **État** de chaque disque virtuel affiche **En ligne**.

2. Sélectionnez ensuite **Stockage > Volumes** , puis vérifiez que la colonne **Intégrité** de chaque volume affiche **Sain** et que la colonne **État** de chaque volume affiche **OK**.

### <a name="pause-and-drain-the-server"></a>Suspendre et purger le serveur

Avant d’arrêter ou de redémarrer un serveur, vous devez suspendre le serveur et purger (déplacer) tous les rôles en cluster, comme les machines virtuelles qui s’exécutent dessus, pour garantir que l’arrêt du serveur n’affecte pas l’état de l’application. Suspendez et purgez toujours les serveurs en cluster avant de les mettre hors connexion pour maintenance.

1. À l’aide de Windows Admin Center, connectez-vous au cluster, puis sélectionnez **Calcul > Nœuds** dans le menu **Outils** du Gestionnaire de cluster.

2. Cliquez sur le nom du serveur que vous souhaitez suspendre et purger, puis sélectionnez **Suspendre**. L’invite suivante doit s’afficher :

   *Si vous suspendez ce nœud, tous les rôles en cluster sont déplacés vers d’autres nœuds et aucun rôle ne peut lui être ajouté jusqu’à sa reprise. Voulez-vous vraiment suspendre le nœud de cluster ?*

3. Sélectionnez **Oui** pour suspendre le serveur et lancer le processus de purge. L’état du nœud de cluster indique **Drainage** , et les rôles comme Hyper-V et Machines virtuelles commencent immédiatement à migrer dynamiquement vers un ou plusieurs autres serveurs se trouvant dans le cluster. ce qui peut prendre quelques minutes.

   > [!NOTE]
   > Quand vous suspendez et purgez le serveur correctement, Azure Stack HCI effectue une vérification automatique de la sécurité pour garantir qu’il n’y a aucun risque à continuer. Si des volumes ne sont pas intègres, le processus s’arrête et vous êtes averti qu’il est déconseillé de continuer.

### <a name="shut-down-the-server"></a>Arrêter le serveur

Une fois la purge du serveur terminée, son état indique **Suspendu** dans Windows Admin Center. Vous pouvez maintenant arrêter le serveur pour maintenance ou le redémarrer de manière sécurisée.

### <a name="resume-the-server"></a>Reprendre l’exécution du serveur

Quand vous êtes prêt pour que le serveur commence à réhéberger les rôles en cluster et les machines virtuelles, il vous suffit mettre le serveur sous tension, d’attendre qu’il démarre et de reprendre son exécution en effectuant les étapes suivantes.

1. Dans le Gestionnaire de cluster, sélectionnez **Calcul > Nœuds** dans le menu **Outils** situé à gauche.

2. Sélectionnez le nom du serveur dont vous souhaitez reprendre l’exécution, puis cliquez sur **Reprendre**. L’invite suivante doit s’afficher :

   *Voulez-vous vraiment relancer le nœud de cluster ?*

3. Dans la plupart des cas, vous devez cocher la case qui indique de *retransférer les rôles en cluster vers ce nœud*. Sélectionnez **Oui** pour reprendre l’exécution du serveur.

Si vous avez coché la case à l’étape 3 ci-dessus, les rôles en cluster et les machines virtuelles commencent immédiatement à remigrer dynamiquement vers le serveur. ce qui peut prendre quelques minutes.

### <a name="wait-for-storage-to-resync"></a>Attendre la resynchronisation du stockage

Quand l’exécution du serveur reprend, toute nouvelle écriture qui s’est produite alors qu’il n’était pas disponible doit être resynchronisée. Cela se produit automatiquement à l’aide du suivi intelligent des changements. Il n’est pas nécessaire que *toutes* les données soient analysées ou synchronisées, mais uniquement les changements. Ce processus est limité pour atténuer l’impact sur les charges de travail de production. En fonction de la durée pendant laquelle le serveur a été mis en pause et de a quantité de nouvelles données qui ont été écrites, cela peut prendre plusieurs minutes.

   > [!IMPORTANT]
   > Vous devez attendre la fin de la resynchronisation pour pouvoir mettre hors connexion d’autres serveurs du cluster.

Pour vérifier si la resynchronisation est terminée, connectez-vous au serveur à l’aide de Windows Admin Center, puis sélectionnez **Stockage > Volumes** dans le menu **Outils** situé à gauche, puis sélectionnez **Volumes** en haut de la page. Si la colonne **Intégrité** de chaque volume affiche **Sain** et que la colonne **État** de chaque volume affiche **OK** , la resynchronisation est terminée et il est maintenant possible de mettre d’autres serveurs du cluster hors connexion sans risque.

## <a name="take-a-server-offline-using-powershell"></a>Mettre un serveur hors connexion à l’aide de PowerShell

Utilisez les procédures suivantes pour correctement suspendre un serveur, le purger et reprendre son exécution dans un cluster Azure Stack HCI à l’aide de PowerShell.

### <a name="verify-its-safe-to-take-the-server-offline"></a>Vérifier qu’il est possible de mettre le serveur hors connexion sans risque

Pour vérifier que tous vos volumes sont intègres, exécutez l’applet de commande suivante en tant qu’administrateur :

```PowerShell
Get-VirtualDisk
```

Voici un exemple de ce à quoi la sortie peut ressembler :

```
FriendlyName              ResiliencySettingName FaultDomainRedundancy OperationalStatus HealthStatus    Size FootprintOnPool StorageEfficiency
------------              --------------------- --------------------- ----------------- ------------    ---- --------------- -----------------
Mirror II                 Mirror                1                     OK                Healthy         4 TB         8.01 TB            49.99%
Mirror-accelerated parity                                             OK                Healthy      1002 GB         1.96 TB            49.98%
Mirror                    Mirror                1                     OK                Healthy         1 TB            2 TB            49.98%
ClusterPerformanceHistory Mirror                1                     OK                Healthy        24 GB           49 GB            48.98%
```

Vérifiez que la propriété **HealthStatus** de chaque volume a la valeur **Sain** et qu’ **OperationalStatus** indique OK.

### <a name="pause-and-drain-the-server"></a>Suspendre et purger le serveur

Exécutez l’applet de commande suivante en tant qu’administrateur pour suspendre et purger le serveur :

```PowerShell
Suspend-ClusterNode -Drain
```

### <a name="shut-down-the-server"></a>Arrêter le serveur

Une fois la purge du serveur terminée, il indique **Suspendu** dans PowerShell.

Vous pouvez maintenant arrêter le serveur ou le redémarrer de manière sécurisée à l’aide des applets de commande PowerShell `Stop-Computer` ou `Restart-Computer`.

   > [!NOTE]
   > Lors de l’exécution d’une commande `Get-VirtualDisk` sur des serveurs qui éteignent ou qui démarrent/arrêtent le service de cluster, l’état Opérationnel du serveur peut être signalé comme étant incomplet ou détérioré, et la colonne État d’intégrité peut afficher un avertissement. Ce comportement est normal et ne doit pas susciter de préoccupations. Tous vos volumes restent en ligne et accessibles.

### <a name="resume-the-server"></a>Reprendre l’exécution du serveur

Exécutez l’applet de commande suivante en tant qu’administrateur pour reprendre l’exécution du serveur dans le cluster. Pour retourner les rôles en cluster et les machines virtuelles qui étaient préalablement en cours d’exécution sur le serveur, utilisez l’indicateur facultatif **-Failback**  :

```PowerShell
Resume-ClusterNode –Failback Immediate
```

Une fois que l’exécution du serveur a repris, ce dernier s’affiche comme **Actif** dans PowerShell.

### <a name="wait-for-storage-to-resync"></a>Attendre la resynchronisation du stockage

Quand l’exécution du serveur reprend, vous devez attendre la fin de la resynchronisation pour pouvoir mettre hors connexion d’autres serveurs du cluster.

Exécutez l’applet de commande suivante en tant qu’administrateur pour superviser la progression :

```PowerShell
Get-StorageJob
```

Si la resynchronisation est déjà terminée, vous n’obtenez aucune sortie.

Voici un exemple de sortie montrant des travaux de resynchronisation (réparation) toujours en cours d’exécution :

```
Name   IsBackgroundTask ElapsedTime JobState  PercentComplete BytesProcessed BytesTotal
----   ---------------- ----------- --------  --------------- -------------- ----------
Repair True             00:06:23    Running   65              11477975040    17448304640
Repair True             00:06:40    Running   66              15987900416    23890755584
Repair True             00:06:52    Running   68              20104802841    22104819713
```

La colonne **BytesTotal** indique la quantité de stockage à resynchroniser. La colonne **PercentComplete** affiche la progression.

   > [!WARNING]
   > Mettre un autre serveur hors connexion est une action risquée tant que ces travaux de réparation ne sont pas terminés.

Pendant ce temps, sous **HealthStatus** , vos volumes continuent à s’afficher comme **Avertissement** , ce qui est normal.

Par exemple, si vous utilisez l’applet de commande `Get-VirtualDisk` pendant la resynchronisation du stockage, la sortie suivante peut s’afficher :

```
FriendlyName ResiliencySettingName OperationalStatus HealthStatus IsManualAttach Size
------------ --------------------- ----------------- ------------ -------------- ----
MyVolume1    Mirror                InService         Warning      True           1 TB
MyVolume2    Mirror                InService         Warning      True           1 TB
MyVolume3    Mirror                InService         Warning      True           1 TB
```

Une fois les travaux terminés, vérifiez que les volumes s’affichent comme **Sains** en utilisant à nouveau l’applet de commande `Get-VirtualDisk`. Voici un exemple de sortie :

```
FriendlyName ResiliencySettingName OperationalStatus HealthStatus IsManualAttach Size
------------ --------------------- ----------------- ------------ -------------- ----
MyVolume1    Mirror                OK                Healthy      True           1 TB
MyVolume2    Mirror                OK                Healthy      True           1 TB
MyVolume3    Mirror                OK                Healthy      True           1 TB
```

Vous pouvez maintenant suspendre et redémarrer d’autres serveurs du cluster sans risque.

## <a name="next-steps"></a>Étapes suivantes

Pour consulter des informations connexes, reportez-vous également à :

- [Vue d’ensemble des espaces de stockage direct](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Mettre à jour des clusters Azure Stack HCI](update-cluster.md)