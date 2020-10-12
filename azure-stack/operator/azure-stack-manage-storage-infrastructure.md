---
title: Gérer l’infrastructure de stockage pour Azure Stack Hub
titleSuffix: Azure Stack
description: Découvrez comment gérer l’infrastructure de stockage pour Azure Stack Hub. Découvrez comment superviser des volumes et des lecteurs. Consultez les conseils sur la résolution des problèmes relatifs à l’ajout de lecteurs à des pools.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 5/4/2020
ms.author: inhenkel
ms.lastreviewed: 5/4/2020
ms.reviewer: jiaha
ms.openlocfilehash: a267c0bf5429bdd97b5a722f45f2fd188b63efb2
ms.sourcegitcommit: a1e2003fb9c6dacdc76f97614ff5a26a5b197b49
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91623198"
---
# <a name="manage-storage-infrastructure-for-azure-stack-hub"></a>Gérer l’infrastructure de stockage pour Azure Stack Hub
Cet article décrit l’intégrité et l’état opérationnel des ressources de l’infrastructure de stockage Azure Stack Hub. Ces ressources incluent les lecteurs et les volumes de stockage. Les informations contenues dans cette rubrique peuvent vous aider à résoudre différents problèmes, par exemple l’impossibilité d’ajouter un lecteur à un pool.

## <a name="volume-states"></a>États de volume

Pour savoir dans quel état sont les volumes, utilisez les commandes PowerShell suivantes :

```powershell
$scaleunit_name = (Get-AzsScaleUnit)[0].name

$subsystem_name = (Get-AzsStorageSubSystem -ScaleUnit $scaleunit_name)[0].name

Get-AzsVolume -ScaleUnit $scaleunit_name -StorageSubSystem $subsystem_name | Select-Object VolumeLabel, HealthStatus, OperationalStatus, RepairStatus, Description, Action, TotalCapacityGB, RemainingCapacityGB
```

Voici un exemple de sortie montrant un volume détaché et un volume détérioré/incomplet :

| VolumeLabel | HealthStatus | OperationalStatus |
|-------------|--------------|------------------------|
| ObjStore_1 | Unknown | Détachée |
| ObjStore_2 | Avertissement | {Détérioré, incomplet} |

Les sections suivantes répertorient les états d’intégrité et opérationnels :

### <a name="volume-health-state-healthy"></a>État d’intégrité du volume : Healthy

| État opérationnel | Description |
|---|---|
| OK | Le volume est intègre. |
| Non optimal | Les données ne sont pas écrites uniformément sur les disques.<br> <br>**Action :** Contactez le support pour optimiser l’utilisation du lecteur dans le pool de stockage. Auparavant, démarrez le processus de collecte du fichier journal à l’aide des instructions disponibles à l’adresse https://aka.ms/azurestacklogfiles. Vous devrez peut-être restaurer à partir de la sauvegarde après la restauration de la connexion ayant échoué. |

### <a name="volume-health-state-warning"></a>État d’intégrité du volume : Avertissement

Lorsque le volume est dans un état d’intégrité Avertissement, cela signifie qu’une ou plusieurs copies de vos données ne sont pas disponibles, mais Azure Stack Hub peut toujours lire au moins une copie de vos données.

| État opérationnel | Description |
|---|---|
| En service | Azure Stack Hub répare le volume, par exemple après l’ajout ou le retrait d’un lecteur. Lorsque la réparation est terminée, le volume doit revenir à l’état d’intégrité OK.<br> <br>**Action :** Attendez qu’Azure Stack Hub termine la réparation du volume et vérifiez l’état ensuite. |
| Incomplet | La résilience du volume est réduite car un ou plusieurs lecteurs ont échoué ou sont manquants. Toutefois, les lecteurs manquants contiennent des copies à jour de vos données.<br> <br>**Action :** Reconnectez les lecteurs manquants, remplacez les lecteurs ayant échoué et mettez en ligne les serveurs qui sont hors connexion. |
| Détérioré | La résilience du volume est réduite, car un ou plusieurs lecteurs ont échoué ou sont manquants, et il existe des copies obsolètes des données sur les lecteurs.<br> <br>**Action :** Reconnectez les lecteurs manquants, remplacez les lecteurs ayant échoué et mettez en ligne les serveurs qui sont hors connexion. |

### <a name="volume-health-state-unhealthy"></a>État d’intégrité du volume : Unhealthy

Lorsqu’un volume est dans un état d’intégrité Défectueux, tout ou partie des données sur le volume est actuellement inaccessible.

| État opérationnel | Description |
|---|---|
| Aucune redondance | Le volume a perdu des données, car trop de lecteurs ont échoué.<br> <br>**Action :** Contactez le support technique. Auparavant, démarrez le processus de collecte du fichier journal à l’aide des instructions disponibles à l’adresse https://aka.ms/azurestacklogfiles. |

### <a name="volume-health-state-unknown"></a>État d’intégrité du volume : Unknown

Le volume peut également être dans l’état d’intégrité Inconnu si le disque virtuel est détaché.

| État opérationnel | Description |
|---|---|
| Détachée | Une défaillance de l’appareil de stockage s’est produite qui peut rendre le volume inaccessible. Certaines données peuvent être perdues.<br> <br>**Action :** <br>1. Vérifiez la connectivité physique et réseau de tous les appareils de stockage.<br>2. Si tous les appareils sont connectés correctement, contactez le support. Auparavant, démarrez le processus de collecte du fichier journal à l’aide des instructions disponibles à l’adresse https://aka.ms/azurestacklogfiles. Vous devrez peut-être restaurer à partir de la sauvegarde après la restauration de la connexion ayant échoué. |

## <a name="drive-states"></a>États du disque

Utilisez les commandes PowerShell suivantes pour surveiller l’état des lecteurs :

```powershell
$scaleunit_name = (Get-AzsScaleUnit)[0].name

$subsystem_name = (Get-AzsStorageSubSystem -ScaleUnit $scaleunit_name)[0].name

Get-AzsDrive -ScaleUnit $scaleunit_name -StorageSubSystem $subsystem_name | Select-Object StorageNode, PhysicalLocation, HealthStatus, OperationalStatus, Description, Action, Usage, CanPool, CannotPoolReason, SerialNumber, Model, MediaType, CapacityGB
```

Les sections suivantes décrivent les états d’intégrité d’un lecteur :

### <a name="drive-health-state-healthy"></a>État d’intégrité du lecteur : Healthy

| État opérationnel | Description |
|---|---|
| OK | Le volume est intègre. |
| En service | Le lecteur effectue certaines opérations de nettoyage interne. Une fois l’opération terminée, le lecteur doit revenir à l’état d’intégrité OK. |

### <a name="drive-health-state-healthy"></a>État d’intégrité du lecteur : Healthy

Un lecteur en état Avertissement peut lire et écrire des données, mais il a un problème.

| État opérationnel | Description |
|---|---|
| Perte de communication | Perte de la connectivité au lecteur.<br> <br>**Action :** Mettez tous les serveurs en ligne. Si cela ne résout pas le problème, reconnectez le lecteur. Si cet état persiste, remplacez le lecteur pour garantir la résilience complète. |
| Défaillance prédictive | Une défaillance du lecteur est censée se produire bientôt.<br> <br>**Action :** Remplacez le lecteur dès que possible pour garantir la résilience complète. |
| Erreur E/S | Une erreur temporaire d’accès au lecteur s’est produite.<br> <br>**Action :** Si cet état persiste, remplacez le lecteur pour garantir la résilience complète. |
| Erreur temporaire | Une erreur temporaire du lecteur s’est produite. Cette erreur signifie généralement que le lecteur n’a pas répondu, mais cela peut aussi signifier que la partition de protection des espaces de stockage direct a été supprimée de façon inappropriée du lecteur. <br> <br>**Action :** Si cet état persiste, remplacez le lecteur pour garantir la résilience complète. |
| Latence anormale | Parfois, le lecteur ne répond pas et présente des signes de défaillance.<br> <br>**Action :** Si cet état persiste, remplacez le lecteur pour garantir la résilience complète. |
| Suppression du pool | Azure Stack Hub procède à la suppression du lecteur de son pool de stockage.<br> <br>**Action :** Attendez qu’Azure Stack Hub termine la suppression du lecteur et vérifiez l’état ensuite.<br>Si l’état reste le même, contactez le support. Auparavant, démarrez le processus de collecte du fichier journal à l’aide des instructions disponibles à l’adresse https://aka.ms/azurestacklogfiles. |
| Démarrage du mode de maintenance | Azure Stack Hub est en train de placer le lecteur en mode de maintenance. Cet état est temporaire : le lecteur devrait bientôt revenir à l’état En mode de maintenance.<br> <br>**Action :** Attendez qu’Azure Stack Hub termine l’opération et vérifiez l’état ensuite. |
| En mode de maintenance | Le lecteur est en mode de maintenance, les lectures et les écritures sont arrêtées. Cet état signifie généralement que les tâches d’administration d’Azure Stack Hub, comme PNU ou FRU, dirigent le lecteur. Mais l’administrateur peut également placer le lecteur en mode de maintenance.<br> <br>**Action :** Attendez qu’Azure Stack Hub termine la tâche d’administration et vérifiez l’état ensuite.<br>Si l’état reste le même, contactez le support. Auparavant, démarrez le processus de collecte du fichier journal à l’aide des instructions disponibles à l’adresse https://aka.ms/azurestacklogfiles. |
| Arrêt du mode de maintenance | Azure Stack Hub est en train de remettre le lecteur en ligne. Cet état est temporaire : le lecteur devrait bientôt avoir un autre état (de préférence Intègre).<br> <br>**Action :** Attendez qu’Azure Stack Hub termine l’opération et vérifiez l’état ensuite. |

### <a name="drive-health-state-unhealthy"></a>État d’intégrité du lecteur : Unhealthy

Un lecteur dans un état Non intègre n’est actuellement pas accessible et il est impossible d’y écrire.

| État opérationnel | Description |
|---|---|
| Split | Le lecteur est séparé du pool.<br> <br>**Action :** Remplacez le lecteur par un nouveau disque. Si vous devez utiliser ce disque, retirez-le du système, vérifiez qu’il ne comporte aucune donnée utile, effacez-le et réinsérez-le. |
| Inutilisable | Le disque physique est mis en quarantaine, car il n’est pas pris en charge par votre fournisseur de solutions. Seuls les disques qui sont approuvés pour la solution et qui ont le microprogramme de disque approprié sont pris en charge.<br> <br>**Action :** Remplacez le lecteur par un disque avec un fabricant et un numéro de modèle approuvés pour la solution. |
| Métadonnées obsolètes | Le disque de remplacement a été utilisé précédemment et peut contenir des données d’un système de stockage inconnu. Le disque est mis en quarantaine. <br> <br>**Action :** Remplacez le lecteur par un nouveau disque. Si vous devez utiliser ce disque, retirez-le du système, vérifiez qu’il ne comporte aucune donnée utile, effacez-le et réinsérez-le. |
| Métadonnées non reconnues | Métadonnées non reconnues trouvées sur le lecteur, ce qui signifie généralement que le lecteur contient des métadonnées d’un autre pool.<br> <br>**Action :** Remplacez le lecteur par un nouveau disque. Si vous devez utiliser ce disque, retirez-le du système, vérifiez qu’il ne comporte aucune donnée utile, effacez-le et réinsérez-le. |
| Support défaillant | Le lecteur a échoué et ne plus être utilisé par des espaces de stockage.<br> <br>**Action :** Remplacez le lecteur dès que possible pour garantir la résilience complète. |
| Panne matérielle d’appareil | Une panne matérielle s’est produite sur ce lecteur. <br> <br>**Action :** Remplacez le lecteur dès que possible pour garantir la résilience complète. |
| Mise à jour du microprogramme | Azure Stack Hub met à jour le microprogramme sur le lecteur. Cet état est temporaire et dure généralement moins d’une minute. Pendant ce temps, les autres lecteurs du pool gèrent toutes les lectures et écritures.<br> <br>**Action :** Attendez qu’Azure Stack Hub termine la mise à jour et vérifiez l’état ensuite. |
| Démarrage en cours | Le lecteur se prépare pour l’opération. Cet état doit être temporaire. Le lecteur doit ensuite passer à un état opérationnel différent.<br> <br>**Action :** Attendez qu’Azure Stack Hub termine l’opération et vérifiez l’état ensuite. |

## <a name="reasons-a-drive-cant-be-pooled"></a>Raisons pour lesquelles un lecteur ne peut pas être mis en pool

Certains lecteurs ne sont pas prêts à être placés dans le pool de stockage Azure Stack Hub. Vous pouvez déterminer pourquoi un lecteur n’est pas éligible pour le pooling en examinant la propriété `CannotPoolReason` d’un lecteur. Le tableau suivant donne un peu plus de détails sur chacune des raisons.

| Motif | Description |
|---|---|
| Matériel non conforme | Le lecteur n’est pas dans la liste des modèles de stockage approuvés spécifiés à l’aide du Service de contrôle d’intégrité.<br> <br>**Action :** Remplacez le lecteur par un nouveau disque. |
| Microprogramme non conforme | Le microprogramme sur le lecteur physique n’est pas dans la liste des versions de microprogramme approuvées à l’aide du Service de contrôle d’intégrité.<br> <br>**Action :** Remplacez le lecteur par un nouveau disque. |
| En cours d’utilisation par le cluster | Le lecteur est actuellement utilisé par un Cluster de basculement.<br> <br>**Action :** Remplacez le lecteur par un nouveau disque. |
| Médias amovibles | Le lecteur est classé comme un lecteur amovible. <br> <br>**Action :** Remplacez le lecteur par un nouveau disque. |
| Défectueux | Le lecteur n’est pas dans un état sain et devra peut-être être remplacé.<br> <br>**Action :** Remplacez le lecteur par un nouveau disque. |
| Capacité insuffisante | Des partitions occupent l’espace libre sur le lecteur.<br> <br>**Action :** Remplacez le lecteur par un nouveau disque. Si vous devez utiliser ce disque, retirez-le du système, vérifiez qu’il ne comporte aucune donnée utile, effacez-le et réinsérez-le. |
| Vérification en cours | Le Service de contrôle d’intégrité vérifie si l’utilisation du lecteur ou du microprogramme sur le lecteur est approuvée.<br> <br>**Action :** Attendez qu’Azure Stack Hub termine l’opération et vérifiez l’état ensuite. |
| Échec de la vérification | Le Service de contrôle d’intégrité n’a pas pu vérifier si l’utilisation du lecteur ou du microprogramme sur le lecteur est approuvée.<br> <br>**Action :** Contactez le support technique. Auparavant, démarrez le processus de collecte du fichier journal à l’aide des instructions disponibles à l’adresse https://aka.ms/azurestacklogfiles. |
| Hors connexion | Le lecteur est en mode hors connexion. <br> <br>**Action :** Contactez le support technique. Auparavant, démarrez le processus de collecte du fichier journal à l’aide des instructions disponibles à l’adresse https://aka.ms/azurestacklogfiles. |