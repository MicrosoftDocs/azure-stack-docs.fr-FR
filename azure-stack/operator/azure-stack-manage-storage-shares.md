---
title: Gérer la capacité de stockage dans Azure Stack Hub
description: Découvrez comment surveiller et gérer la capacité de stockage et la disponibilité dans Azure Stack Hub.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: inhenkel
ms.reviewer: xiaofmao
ms.lastreviewed: 03/19/2019
ms.openlocfilehash: 212f68be85bfe4b129e84057cc63472e5259a41c
ms.sourcegitcommit: 20d10ace7844170ccf7570db52e30f0424f20164
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79295082"
---
# <a name="manage-storage-capacity-for-azure-stack-hub"></a>Gérer la capacité de stockage pour Azure Stack Hub

Cet article aident les opérateurs de cloud Azure Stack Hub à superviser et gérer la capacité de stockage de leur déploiement Azure Stack Hub. L’infrastructure de stockage Azure Stack Hub alloue une partie de la capacité totale de stockage du déploiement Azure Stack Hub sous forme de *services de stockage*. Les services de stockage stockent les données d’un locataire dans les partages des volumes qui correspondent aux nœuds du déploiement.

En tant qu’opérateur de cloud, vous disposez d’une quantité limitée de stockage à utiliser. La quantité de stockage est définie par la solution que vous implémentez. La solution est fournie par votre fournisseur OEM si vous utilisez une solution à plusieurs nœuds, ou elle est fournie par le matériel sur lequel vous installez le Kit de développement Azure Stack (ASDK).

Comme Azure Stack Hub ne prend pas en charge l’extension de la capacité de stockage, il est important de [superviser](#monitor-shares) le stockage disponible pour assurer le maintien du bon fonctionnement.

Lorsque la capacité libre restante d’un partage se restreint, pensez à [gérer l’espace disponible](#manage-available-space) pour empêcher les partages de manquer de capacité.

Les options de gestion de la capacité sont les suivantes :
- Récupération de la capacité.
- Migration d’un conteneur.

Quand un partage est utilisé à 100 pour cent, le service de stockage ne fonctionne plus pour celui-ci. Pour obtenir de l’aide sur les opérations de restauration du partage, contactez le support Microsoft.

## <a name="understand-volumes-and-shares-containers-and-disks"></a>Présentation des volumes, des partages, des conteneurs et des disques
### <a name="volumes-and-shares"></a>Volumes et partages
Le *service de stockage* partitionne le stockage disponible en volumes distincts, égaux qui sont alloués pour stocker des données de locataire. Le nombre de volumes est égal au nombre de nœuds du déploiement Azure Stack Hub :

- Un déploiement à quatre nœuds comporte quatre volumes. Chaque volume possède un seul partage. Dans un déploiement de plusieurs nœuds, le nombre de partages n’est pas réduit si un nœud est supprimé ou ne fonctionne pas correctement.
- Si vous utilisez le Kit de développement Azure Stack, il existe un seul volume avec un seul partage.

Comme les partages de service de stockage sont destinés à l’utilisation exclusive des services de stockage, vous ne devez pas y modifier, ajouter ni supprimer directement des fichiers. Seuls les services de stockage doivent fonctionner sur les fichiers stockés dans ces volumes.

Partages des volumes contenant des données de locataire. Les données de locataire incluent des objets blob de pages, des objets blob de blocs, des objets blob d’ajouts, des tables, des files d’attente, des bases de données et des magasins de métadonnées associés. Comme les objets de stockage (objets blob, etc.) sont individuellement contenus dans un seul et même partage, la taille maximale de chaque objet ne peut pas dépasser la taille d’un partage. La taille maximale d’un nouvel objet dépend de la capacité restant dans un partage sous forme d’espace inutilisé lorsque le nouvel objet est créé.

Quand un partage manque d’espace libre et que les actions pour en [récupérer](#reclaim-capacity) n’aboutissent pas ou qu’elles ne sont pas disponibles, les opérateurs cloud Azure Stack Hub peuvent migrer les conteneurs d’objets blob d’un partage vers un autre.

Pour plus d’informations sur l’utilisation du stockage d’objets blob dans Azure Stack Hub par les utilisateurs locataires, voir la section [Services de stockage Azure Stack Hub](/azure-stack/user/azure-stack-storage-overview).

### <a name="containers"></a>Containers
Les utilisateurs locataires créent des conteneurs qui sont ensuite utilisés pour stocker des données d’objet blob. Même si les utilisateurs décident du conteneur dans lequel placer les objets blob, le service de stockage utilise un algorithme pour identifier le volume dans lequel placer le conteneur. En règle générale, l’algorithme choisit le volume contenant la plus grande quantité d’espace disponible.  

Une fois qu’un objet blob a été placé dans un conteneur, il peut se développer pour utiliser plus d’espace. À mesure que vous ajoutez de nouveaux objets blob et que les objets blob existants se développent, l’espace disponible dans le volume du conteneur se réduit.  

Les conteneurs ne sont pas limités à un seul partage. Lorsque les données d’objets blob combinées dans un conteneur augmentent et utilisent 80 pour cent ou plus de l’espace disponible, le conteneur passe en mode *dépassement de capacité*. Lorsqu’il est en mode dépassement de capacité, les nouveaux objets blob qui sont créés dans le conteneur sont alloués à un autre volume disposant d’un espace suffisant. Au fil du temps, un conteneur en mode dépassement de capacité peut présenter des objets blob répartis sur plusieurs volumes.

Lorsque 80 pour cent (puis 90 pour cent) de l’espace disponible d’un volume sont utilisés, le système déclenche des alertes dans le portail d’administration Azure Stack Hub. Les opérateurs de cloud doivent vérifier la capacité de stockage disponible et envisager de rééquilibrer le contenu. Le service de stockage cesse de fonctionner si un disque est utilisé à 100 pour cent et aucune alerte supplémentaire n’est déclenchée.

### <a name="disks"></a>Disques
Les disques de machine virtuelle sont ajoutés aux conteneurs par les locataires, et ils comportent un disque de système d’exploitation. Les machines virtuelles peuvent également posséder un ou plusieurs disques de données. Les deux types de disque sont stockés en tant qu’objets blob de pages. Il est conseillé aux locataires de placer chaque disque dans un conteneur distinct pour améliorer les performances de la machine virtuelle.

- Chaque conteneur qui comprend un disque, ou un objet blob de pages, d’une machine virtuelle est considéré comme un conteneur attaché à la machine virtuelle qui possède le disque.
- Un conteneur dépourvu de disques d’une machine virtuelle est considéré comme un conteneur libre.

Les options permettant de libérer de l’espace dans un conteneur attaché sont limitées. Pour plus d’informations, consultez [Déplacer des disques de machine virtuelle](#move-vm-disks).

>[!TIP]  
> Les opérateurs de cloud ne gèrent pas directement les disques qui sont attachés aux machines virtuelles que les locataires peuvent ajouter à un conteneur. Cependant, lorsque vous prévoyez de gérer l’espace des partages de stockage, il peut être utile de comprendre comment les disques sont liés aux conteneurs et aux partages.

## <a name="monitor-shares"></a>Surveiller les partages
Utilisez Azure PowerShell ou le portail d’administration pour superviser les partages, afin de pouvoir savoir à quel moment l’espace libre sera limité. Lorsque vous utilisez le portail, vous recevez des alertes sur les partages qui manquent d’espace.

### <a name="use-powershell"></a>Utiliser PowerShell
En tant qu’opérateur de cloud, vous pouvez superviser la capacité de stockage d’un partage à l’aide de l’applet de commande PowerShell `Get-AzsStorageShare`. L’applet de commande retourne l’espace libre, alloué et total en octets de chacun des partages.

![Exemple : retourner l’espace libre des partages](media/azure-stack-manage-storage-shares/free-space.png)

- **Capacité totale** : représente l’espace total, en octets, qui est disponible dans le partage. Cet espace est utilisé pour les données et métadonnées gérées par les services de stockage.
- **Capacité utilisée** : représente la quantité de données, en octets, utilisée par toutes les extensions des fichiers qui stockent les données de locataire et les métadonnées associées.

### <a name="use-the-administrator-portal"></a>Utiliser le portail d’administration
En tant qu’opérateur de cloud, vous pouvez utiliser le portail d’administration pour afficher la capacité de stockage de tous les partages.

1. Connectez-vous au [portail d’administration](https://adminportal.local.azurestack.external).
2. Sélectionnez **Tous les services** > **Stockage** > **Partages de fichiers** pour ouvrir la liste de partages de fichiers, où vous pouvez consulter les informations d’utilisation.

    ![Exemple : stocker des partages de fichiers dans le portail administrateur Azure Stack Hub](media/azure-stack-manage-storage-shares/storage-file-shares.png)

   - **Total** : représente l’espace total, en octets, qui est disponible dans le partage. Cet espace est utilisé pour les données et métadonnées gérées par les services de stockage.
   - **Utilisé** : représente la quantité de données, en octets, utilisée par toutes les extensions des fichiers qui stockent les données de locataire et les métadonnées associées.

### <a name="storage-space-alerts"></a>Alertes de l’espace de stockage
Lorsque vous utilisez le portail d’administration, vous recevez des alertes sur les partages qui manquent d’espace.

> [!IMPORTANT]
> En tant qu’opérateur de cloud, vous devez empêcher les partages d’être utilisés complètement. Quand un partage est utilisé à 100 pour cent, le service de stockage ne fonctionne plus pour celui-ci. Pour récupérer de l’espace libre et les opérations de restauration dans un partage utilisé à 100 pour cent, vous devez contacter le support Microsoft.

* **Avertissement** : quand un partage de fichiers est utilisé à plus de 80 pour cent, vous recevez une alerte de type *Avertissement* dans le portail d’administration :

  ![Exemple : alerte d’avertissement dans le portail d’administration Azure Stack Hub](media/azure-stack-manage-storage-shares/alert-warning.png)

* **Critique** : quand un partage de fichiers est utilisé à plus de 90 pour cent, vous recevez une alerte de type *Critique* dans le portail d’administration :

  ![Exemple : alerte critique dans le portail d’administration Azure Stack Hub](media/azure-stack-manage-storage-shares/alert-critical.png)

* **Afficher les détails** : dans le portail d’administration, vous pouvez afficher les détails d’une alerte pour voir vos options d’atténuation :

  ![Exemple : afficher les détails d’une alerte dans le portail d’administration Azure Stack Hub](media/azure-stack-manage-storage-shares/alert-details.png)

## <a name="manage-available-space"></a>Gérer l’espace disponible
Lorsqu’il est nécessaire de libérer de l’espace dans un partage, utilisez d’abord les méthodes les moins invasives. Par exemple, essayez de récupérer de l’espace avant de migrer un conteneur.  

### <a name="reclaim-capacity"></a>Récupérer de la capacité
*Cette option s’applique aux déploiements de plusieurs nœuds et au Kit de développement Azure Stack.*

Vous pouvez récupérer la capacité utilisée par les comptes locataires qui ont été supprimés. Cette capacité est automatiquement récupérée lorsque la [période de rétention](azure-stack-manage-storage-accounts.md#set-the-retention-period) des données est atteinte. Vous pouvez également choisir de la récupérer immédiatement.

Pour plus d’informations, consultez la section « Récupérer de la capacité » de l’article [Gérer des comptes de stockage Azure Stack Hub](azure-stack-manage-storage-accounts.md#reclaim).

### <a name="migrate-a-container-between-volumes"></a>Migrer un conteneur entre des volumes
*Cette option s’applique uniquement aux systèmes intégrés Azure Stack Hub.*

En raison des modèles d’utilisation de locataire, certains partages de locataire utilisent davantage d’espace que d’autres. En conséquence, certains partages peuvent manquer d’espace avant d’autres partages relativement peu utilisés.

Vous pouvez libérer de l’espace dans un partage surutilisé en migrant manuellement des conteneurs d’objets blob vers un autre partage. Vous pouvez migrer plusieurs conteneurs plus petits vers un partage unique dont la capacité est suffisante pour les contenir intégralement. Utilisez la migration pour déplacer des conteneurs *libres*. Les conteneurs libres sont des conteneurs dépourvus de disque pour une machine virtuelle.

La migration consolide tous les objets blob d’un conteneur du nouveau partage.

- Si un conteneur passe en mode dépassement de capacité et a placé des objets blob dans des volumes supplémentaires, le nouveau partage doit disposer d’une capacité suffisante pour contenir tous les objets blob du conteneur que vous migrez. Cela inclut les objets blob qui se trouvent dans les partages supplémentaires.

- L’applet de commande PowerShell `Get-AzsStorageContainer` identifie uniquement l’espace utilisé dans le volume initial d’un conteneur. L’applet de commande n’identifie pas l’espace utilisé par les objets blob qui sont placés dans des volumes supplémentaires. Par conséquent, la taille totale d’un conteneur peut ne peut pas être évidente. Il est possible que la consolidation d’un conteneur dans un nouveau partage envoie ce nouveau partage dans une condition de dépassement de capacité, où il place les données dans des partages supplémentaires. Vous devrez donc peut-être rééquilibrer les partages.

- Si vous ne disposez pas d’autorisations sur certains groupes de ressources et ne pouvez pas utiliser PowerShell pour interroger les volumes supplémentaires à propos des données de dépassement de capacité, contactez le propriétaire de ces conteneurs et groupes de ressources pour connaître le volume total des données à migrer avant de procéder à cette migration.  

> [!IMPORTANT]
> La migration des objets blob d’un conteneur est une opération hors connexion qui nécessite l’utilisation de PowerShell. Tant que la migration n’est pas terminée, tous les objets blob du conteneur que vous migrez restent hors connexion et ne peuvent pas être utilisés. Il est aussi conseillé d’éviter la mise à niveau d’Azure Stack Hub tant que toutes les migrations en cours ne sont pas terminées.

#### <a name="migrate-containers-by-using-powershell"></a>Migrer des conteneurs en utilisant PowerShell
1. Vérifiez qu’[Azure PowerShell est installé et configuré](https://azure.microsoft.com/documentation/articles/powershell-install-configure/). Pour plus d’informations, consultez [Gérer les ressources Azure à l’aide d’Azure PowerShell](https://go.microsoft.com/fwlink/?LinkId=394767).
2. Examinez le conteneur pour connaître les données du partage que vous envisagez de migrer. Afin d’identifier les meilleurs conteneurs candidats pour la migration d’un volume, utilisez l’applet de commande `Get-AzsStorageContainer` :

   ```powershell  
   $farm_name = (Get-AzsStorageFarm)[0].name
   $shares = Get-AzsStorageShare -FarmName $farm_name
   $containers = Get-AzsStorageContainer -ShareName $shares[0].ShareName -FarmName $farm_name
   ```
   Examinez ensuite $containers :

   ```powershell
   $containers
   ```

   ![Exemple : $containers](media/azure-stack-manage-storage-shares/containers.png)

3. Identifiez les meilleurs partages de destination pour contenir le conteneur que vous migrez :

   ```powershell
   $destinationshare = ($shares | Sort-Object FreeCapacity -Descending)[0]
   ```

   Examinez ensuite $destinationshares :

   ```powershell 
   $destinationshares
   ```

   ![Exemple : partages $destination](media/azure-stack-manage-storage-shares/examine-destinationshares.png)

4. Démarrez la migration d’un conteneur. Il s’agit d’un processus asynchrone. Si vous démarrez la migration des conteneurs supplémentaires avant la fin de la première migration, utilisez l’ID de travail pour suivre l’état de chaque migration.

   ```powershell
   $job_id = Start-AzsStorageContainerMigration -StorageAccountName $containers[0].Accountname -ContainerName $containers[0].Containername -ShareName $containers[0].Sharename -DestinationShareUncPath $destinationshares[0].UncPath -FarmName $farm_name
   ```

   Examinez ensuite $jobId. Dans l’exemple suivant, remplacez *d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0* par l’ID de travail que vous souhaitez examiner :

   ```powershell
   $jobId
   d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0
   ```

5. Utilisez l’ID de travail pour vérifier l’état du travail de migration. À la fin de la migration d’un conteneur, l’état **MigrationStatus** est défini sur *Terminé*.

   ```powershell 
   Get-AzsStorageContainerMigrationStatus -JobId $job_id -FarmName $farm_name
   ```

   ![Exemple : état de la migration](media/azure-stack-manage-storage-shares/migration-status1.png)

6. Vous pouvez annuler une tâche de migration en cours d’exécution. Les travaux de migration annulés sont traités de façon asynchrone. Vous pouvez suivre les annulations à l’aide de $jobid :

   ```powershell
   Stop-AzsStorageContainerMigration -JobId $job_id -FarmName $farm_name
   ```

   ![Exemple : état de la restauration](media/azure-stack-manage-storage-shares/rollback.png)

7. Vous pouvez réexécuter la commande de l’étape 6 jusqu’à ce que l’état de la migration soit *Annulé* :  

    ![Exemple : état Annulé](media/azure-stack-manage-storage-shares/cancelled.png)

### <a name="move-vm-disks"></a>Déplacer des disques de machine virtuelle
*Cette option s’applique uniquement aux systèmes intégrés Azure Stack Hub.*

La méthode la plus extrême pour gérer l’espace implique le déplacement des disques de machine virtuelle. Comme le déplacement d’un conteneur attaché (contenant un disque de machine virtuelle) est complexe, contactez le support Microsoft pour effectuer cette action.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur l’offre des machines virtuelles aux utilisateurs, consultez [Gérer la capacité de stockage pour Azure Stack Hub](azure-stack-tutorial-tenant-vm.md).
