---
title: Sauvegarder vos comptes de stockage sur Azure Stack Hub
description: Découvrez comment sauvegarder vos comptes de stockage sur Azure Stack Hub.
author: mattbriggs
ms.topic: how-to
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/19/2019
ms.openlocfilehash: 001514995d13c63ecf697769e0fc71e2155b7382
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90574157"
---
# <a name="back-up-your-storage-accounts-on-azure-stack-hub"></a>Sauvegarder vos comptes de stockage sur Azure Stack Hub

Cet article présente la protection et la récupération des comptes de stockage dans un compte de stockage Azure sur Azure Stack Hub.

## <a name="elements-of-the-solution"></a>Éléments de la solution

Cette section présente la structure globale de la solution et les principales parties.

![Sauvegarde de stockage Azure Stack Hub](./media/azure-stack-network-howto-backup-storage/azure-stack-storage-backup.png)

### <a name="application-layer"></a>Couche Application

Les données peuvent être répliquées entre les comptes de stockage sur des unités d’échelle Azure Stack Hub distinctes en émettant plusieurs opérations [PUT Blob](/rest/api/storageservices/put-blob) ou [Put Block](/rest/api/storageservices/put-block) pour écrire des objets dans plusieurs emplacements. L’application peut également émettre l’opération [Copy Blob](/rest/api/storageservices/copy-blob) pour copier le blob dans un compte de stockage hébergé sur une unité d’échelle distincte une fois l’opération Put effectuée sur le compte principal.

### <a name="scheduled-copy-task"></a>Tâche de copie planifiée

AzCopy est un excellent outil qui peut être utilisé pour copier des données à partir de systèmes de fichiers locaux, du stockage cloud Azure, du stockage Azure Stack Hub et du niveau S3. Actuellement, AzCopy ne peut pas copier de données entre deux comptes de stockage Azure Stack Hub. La copie d’objets d’un compte de stockage Azure Stack Hub source vers un compte de stockage Azure Stack Hub cible nécessite un système de fichiers local intermédiaire.

Pour plus d’informations, consultez l’article AzCopy [Utiliser les outils de transfert de données dans le stockage Azure Stack Hub](./azure-stack-storage-transfer.md?view=azs-1908#azcopy).

### <a name="azure-stack-hub-source"></a>Azure Stack Hub (source)

Il s’agit de la source des données de compte de stockage que vous souhaitez sauvegarder.

Vous devez utiliser l’URL du compte de stockage source et le jeton SAP. Pour obtenir des instructions sur l’utilisation d’un compte de stockage, consultez [Bien démarrer avec les outils de développement du stockage Azure Stack Hub](azure-stack-storage-dev.md).

### <a name="azure-stack-hub-target"></a>Azure Stack Hub (cible)

Il s’agit de la cible qui stocke les données de compte que vous souhaitez sauvegarder. L’instance Azure Stack Hub cible doit être située dans un emplacement différent de votre Azure Stack Hub cible. Et la source doit pouvoir se connecter à la cible.

Vous devez utiliser l’URL du compte de stockage source et le jeton SAP. Pour obtenir des instructions sur l’utilisation d’un compte de stockage, consultez [Bien démarrer avec les outils de développement du stockage Azure Stack Hub](azure-stack-storage-dev.md).

### <a name="intermediary-local-filesystem"></a>Système de fichiers local intermédiaire

Vous avez besoin d’un emplacement pour exécuter AzCopy et stocker des données lors de la copie à partir de votre source, puis lors de l’écriture sur votre Azure Stack Hub cible. Il s’agit d’un serveur intermédiaire dans votre instance Azure Stack Hub source.

Vous pouvez créer un serveur Linux ou Windows Server en tant que serveur intermédiaire. Le serveur doit disposer de suffisamment d’espace pour stocker tous les objets dans les conteneurs du compte de stockage source.
- Pour des instructions sur la configuration d’un serveur Linux, consultez [Créer une machine virtuelle de serveur Linux sur le portail Azure Stack Hub](azure-stack-quick-linux-portal.md).  
- Pour des instructions sur la configuration d’un serveur Windows, consultez [Créer une machine virtuelle de serveur Windows sur le portail Azure Stack Hub](azure-stack-quick-windows-portal.md).  

Une fois que vous avez configuré votre serveur Windows, vous devez installer [Azure Stack Hub PowerShell](../operator/azure-stack-powershell-install.md?toc=https%3A%2F%2Fdocs.microsoft.com%2FFazure-stack%2Fuser%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2FFazure-stack%2Fbreadcrumb%2Ftoc.json) et les [outils Azure Stack Hub](../operator/azure-stack-powershell-download.md?toc=https%3A%2F%2Fdocs.microsoft.com%2FFazure-stack%2Fuser%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2FFazure-stack%2Fbreadcrumb%2Ftoc.json).

## <a name="set-up-backup-for-storage-accounts"></a>Configurer la sauvegarde pour les comptes de stockage

1. Récupérez le point de terminaison de blob pour les comptes de stockage source et cible.

    ![Sauvegarde de stockage Azure Stack Hub](./media/azure-stack-network-howto-backup-storage/back-up-step1.png)

2. Créez et enregistrez des jetons SAS pour les comptes de stockage source et cible.

    ![Sauvegarde de stockage Azure Stack Hub](./media/azure-stack-network-howto-backup-storage/back-up-step2.png)

3. Installez [AzCopy](https://github.com/Azure/azure-storage-azcopy) sur le serveur intermédiaire et définissez la version de l’API de façon à ce qu’elle prenne en compte les comptes de stockage Azure Stack Hub.

    - Pour un serveur Windows Server :

    ```PowerShell  
    set AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09 PowerShell use: $env:AZCOPY_DEFAULT_SERVICE_API_VERSION="2017-11-09"
    ```

    - Pour un serveur Linux (Ubuntu) :

    ```bash  
    export AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09
    ```

4. Sur votre serveur intermédiaire, créez un script. Mettez à jour cette commande avec votre **compte de stockage**, la **clé SAS** et le **chemin d’accès au répertoire local**. Vous exécutez le script pour copier des données de façon incrémentielle à partir du compte de stockage **source**.

    ```
    azcopy sync "https:/<storagaccount>/<container>?<SAS Key>" "C:\\myFolder" --recursive=true --delete-destination=true
    ```

5.  Entrez le **compte de stockage**,** la clé SAP** et le **chemin d’accès au répertoire local.  Vous allez les utiliser pour copier des données de façon incrémentielle vers le compte de stockage **cible**.
    
    ```
    azcopy sync "C:\\myFolder" "https:// <storagaccount>/<container>?<SAS Key>" --recursive=true --delete-destination=true
    ```

6.  Utilisez Cron ou le Planificateur de tâches Windows pour planifier la copie à partir du compte de stockage source Azure Stack Hub vers le stockage local sur le serveur intermédiaire. Effectuez ensuite une copie du stockage local dans le serveur intermédiaire vers le compte de stockage Azure Stack Hub cible.

    L’objectif RPO que vous pouvez atteindre avec cette solution est déterminé par la valeur du paramètre /MO et la bande passante réseau entre le compte source et le serveur intermédiaire, ainsi que le serveur intermédiaire et le compte cible.

    - Pour un serveur Linux (Ubuntu) :

    ```bash  
    schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\\&lt;script name>.bat
    ```

    | Paramètre | Remarque | 
    | ---- | ---- |
    | /SC | Utilisez une planification en minutes. |
    | /MO | Intervalle de *XX* minutes. |
    | /TN | Nom de la tâche. |
    | /TR | Chemin du fichier `script.bat`. |


    - Pour un serveur Windows Server :

    Pour plus d’informations sur l’utilisation de la Planification de tâches Windows, consultez [Planificateur de tâches pour les développeurs](/windows/win32/taskschd/task-scheduler-start-page)
    

## <a name="use-your-storage-account-in-a-disaster"></a>Utiliser votre compte de stockage en cas de sinistre

Chaque compte de stockage Azure Stack Hub possède un nom DNS unique dérivé du nom de la région Azure Stack Hub elle-même, par exemple `https://krsource.blob.east.asicdc.com/`. Les applications qui écrivent et lisent à partir de ce nom DNS doivent tenir compte du changement de nom DNS du compte de stockage lorsque le compte cible, par exemple `https://krtarget.blob.west.asicdc.com/`, doit être utilisé lors d’un incident.

Les chaînes de connexion d’application peuvent être modifiées après qu’un incident a été déclaré pour tenir compte du déplacement des objets ou, si un enregistrement CNAME est utilisé devant un équilibreur de charge frontal pour les comptes de stockage source et cible, l’équilibreur de charge peut être configuré avec un algorithme de basculement manuel qui permet à l’administrateur de déclarer la cible.

Si la signature d’accès partagé est utilisée par l’application plutôt que par AAD ou AD FS, la méthode ci-dessus ne fonctionne pas et les chaînes de connexion de l’application doivent être mises à jour avec l’URL du compte de stockage cible et la ou les clés SAS générées pour le compte de stockage cible.

## <a name="next-steps"></a>Étapes suivantes

[Bien démarrer avec les outils de développement du stockage Azure Stack Hub](azure-stack-storage-dev.md)
