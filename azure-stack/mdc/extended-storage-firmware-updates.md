---
title: Azure Stack - Mise à jour du microprogramme pour le stockage étendu Modular Data Center
description: Cet article fournit une aide relative à l’exécution de mises à jour du microprogramme dans le stockage étendu pour le stockage Blob Modular Data Center.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2020
ms.author: justinha
ms.reviewer: karlt
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: 23430fd05254b1509677bf2d7ddedab75c7c2355
ms.sourcegitcommit: e4e2cc6a68f02c3e856f58ca5ee51b3313c7ff8f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92183393"
---
# <a name="updating-onefs-and-firmware-for-the-datacenter-extended-storage"></a>Mise à jour de OneFS et du microprogramme pour le stockage étendu du centre de données

Cet article fournit des conseils pour la mise à jour de OneFS et du microprogramme du matériel de stockage étendu du centre de données. 

Les étapes finales de l’intégration et du déploiement du matériel de stockage étendu sont les étapes permettant de valider le fait que le matériel est sain et prêt pour la configuration. Les étapes sont décrites dans [Contrôle d’intégrité du stockage étendu](extended-storage-health-checks.md).

## <a name="update-onefs"></a>Mettre à jour OneFS

Mettez à jour OneFS pour appliquer des correctifs récents du système d’exploitation avant de mettre à jour le microprogramme. 

1. Vérifiez que votre cluster dispose de suffisamment d’espace libre pour installer ce patch.
1. Ouvrez une connexion SSH sur n’importe quel nœud du cluster et connectez-vous à l’aide du compte racine.

   >[!NOTE]
   >Si le patch est en cours d’installation sur un cluster en mode de conformité, connectez-vous à l’aide du compte compadmin.

1. Copiez le fichier 8.2.2_GA-RUP_2020-07_PSP-40.tgz dans le répertoire /ifs/data/Isilon_Support sur le cluster.
1. Exécutez la commande suivante pour passer au répertoire /ifs/data/Isilon_Support : 

   ```
   /ifs/data/Isilon_Support
   pwd
   ```

1. Exécutez la commande suivante pour extraire le fichier correctif :

   ```
   -zxvf 8.2.2_GA-RUP_2020-07_PSP-40.tgz
   ```

1. Exécutez la commande suivante pour installer ce patch :
   >[!WARNING]
   >La commande suivante redémarrera tous les nœuds du cluster simultanément. Pour installer le patch, puis redémarrer chaque nœud successivement, omettez le paramètre `-simultaneous`. Pour plus d'informations, consultez la section consacrée aux impacts Installation/Suppression de LISEZMOI.

   ```
   isi upgrade patches install 8.2.2_GA-RUP_2020-07_PSP-40.pkg --simultaneous
   ```

1. Pour vérifier que ce patch est bien installé, exécutez la commande suivante :

   ```
   isi upgrade patches list
   ```

1. Confirmez que 8.2.2_GA-RUP_2020-07_PSP-40 apparaît dans la liste des packages installés et que l’état est défini sur Installé.

## <a name="update-firmware-on-nodes"></a>Mettre à jour le microprogramme sur des nœuds

   1. Évaluez le microprogramme et les [mises en production du package](https://aka.ms/currentextstoragerelease) de support du lecteur actuel.
   1. Téléchargez les fichiers sur votre ordinateur local.  
   1. Chargez les packages du microprogramme du lecteur et du nœud dans le partage de fichiers Isilon, en plaçant les fichiers dans les répertoires respectifs suivants :
    
        * Drive_Support_*.tgz: /ifs/data/Isilon_Support/dsp/
        * IsiFw_Package_*.tar: /ifs/data 

## <a name="verify-disk-drive-firmware"></a>Vérifier le microprogramme du lecteur de disque

Effectuez d’abord la mise à jour/vérification du support du lecteur. Cela permet de vérifier si le microprogramme du lecteur de disque est à jour avec le package fourni. Si ce n’est pas le cas, le package peut être utilisé pour effectuer les mises à jour nécessaires :

   1. À partir d’une session SSH active, exécutez :

       ```
       cd /ifs/data/Isilon_Support/dsp/
       ```

   1. Assurez-vous que le Drive_Support_ *.tgz se trouve dans ce répertoire en exécutant la commande ls.
   1. Ouvrez le fichier .tgz en exécutant la commande suivante. Vérifiez que vous disposez du nom de fichier complet et remplacez * par le numéro de version réel du package :
       ```
       tar -zxvf Drive_Support_v1.*.tgz
       ```
       Par exemple, si la version du package est Drive_Support_v1.33.tgz, exécutez la commande suivante :
       ```
       tar -zxvf Drive_Support_v1.33.tgz
       ```
   1. Cela se développe en trois fichiers :
    
        * Drive_Support_v1.33.tar
        * Drive_Support_v1.33.tar.md5
        * Drive_Support_v1.33.tar.sha256
   1. Exécutez la commande suivante pour installer/vérifier le logiciel de support du lecteur :
       ```
       isi_dsp_install Drive_Support_v1.*.tar
       ```
       Par exemple, si la version du package est Drive_Support_v1.33.tgz, exécutez la commande suivante :
       ```
       isi_dsp_install Drive_Support_v1.33.tar
       ```
   1. Cette commande exécute les vérifications et demande une installation pour le package de support du lecteur. Si le package est nécessaire, il sera installé ; sinon, il indiquera qu’il est installé et ignorera les mises à jour supplémentaires pour le logiciel de support du lecteur. Exemple de sortie pour v1.33 comme suit :
    
        ```
        Running local CHECKS for patch Drive_Support_v1.33
        INSTALL operation has been requested for patch Drive_Support_v1.33
        Installed Drive_Support_v1.33
        DELETE operation has been requested for patch
        Drive_Support_v1.33
        Removed Drive_Support_v1.33 package files
        DSP Install Succeeded, 170.605088949 sec elapsed
        ```
        
   1. La confirmation finale peut être activée en exécutant la commande suivante. Toute autre chose que « - » dans la colonne « Souhaité » indique que le lecteur doit être mis à jour :
       ```
       isi devices drive firmware list --node-lnn all
       ```
   1. Si certains lecteurs doivent être mis à jour, la commande suivante doit être utilisée pour effectuer les mises à jour du microprogramme de disque :
       ```
       isi devices drive firmware update start all --node-lnn all
       ```
   1. Ce processus peut être analysé à l’aide de la commande suivante :
       ```
       isi devices drive firmware update list
       ```
   1. Une fois l’opération terminée, l’intégrité des lecteurs peut être contrôlée à l’aide de la commande suivante :
       ```
       isi devices drive list --node-lnn all
       ```
## <a name="install-node-firmware"></a>Installer le microprogramme du nœud

Une fois les mises à jour de support du lecteur terminées ou vérifiées, le microprogramme du nœud peut être installé en tirant parti de IsiFw_Package_ *.tar.

   1. À partir d’une session SSH active, exécutez :
        ```
        cd /ifs/data/
        ```
   1. Assurez-vous que IsiFw_Package_*.tar se trouve dans ce répertoire en exécutant la commande ls.
   1. Exécutez la commande suivante pour indexer et installer le microprogramme sur les nœuds. Vérifiez que vous disposez du nom de fichier complet et remplacez * par le numéro de version réel du package :
       ```
       isi upgrade patches install IsiFw_Package_v*.tar --rolling=false
       ```
       Par exemple, si le numéro de version est v10.3.3.tar, exécutez la commande suivante :
       ```
       isi upgrade patches install IsiFw_Package_v10.3.3.tar --rolling=false
       ```
   1. Une fois exécuté, le message suivant est envoyé en confirmation :
       ```
        Requested install of patch IsiFw_Package_v10.3.3.
       ```
   1. Vérifiez l’état de l’index/installation à l’aide de la commande suivante :
       ```
       isi upgrade patches list
       ```
   1. La colonne « État » est mise à jour à mesure que le processus se termine, l’état affiché est le suivant : En attente, Inconnu, En cours d’installation et Installé, comme illustré dans l’exemple de sortie suivant :
        
       ![État de la mise à jour du microprogramme](media/extended-storage-firmware-updates/firmware-update-status.png)
        
   1. Une fois IsiFw_Package *.tar indexé ou installé, le déploiement du microprogramme mis à jour peut être démarré sur les nœuds, à l’aide de la commande suivante :
        ```
        isi upgrade cluster firmware start --no-verify
        ```
   1. La confirmation suivante s’affiche, tapez « Oui » pour continuer :
       ```
       You are about to start a Rolling Firmware UPGRADE, are you sure? (yes/[no]):
       ```
        
       > [!NOTE]
       > La mise à jour du microprogramme du nœud peut prendre plusieurs heures et les nœuds sont mis hors connexion pendant le processus. Une fois l’exécution de toutes ces commandes effectuée à partir du Nœud 1, la connexion est déposée, car il s’agit du premier nœud à mettre à jour. Il en va de même pour le portail OneFS : attendez 5 à 10 minutes avant de tenter de vous reconnecter pour vérifier l’état (si nécessaire, la connexion via une autre interface de gestion qui n’est pas mise à jour est également possible).
   1. À partir d’une connexion ou d’une interface de gestion active, l’état en cours de cette mise à jour du microprogramme peut être suivi à l’aide de la commande suivante :
       ```
       isi upgrade cluster nodes firmware progress list
       ```
       Ou en utilisant le portail OneFS, sous Gestion du cluster - Correctifs et microprogramme :
        
       ![Gestion de cluster](media/extended-storage-firmware-updates/cluster-management.png)
        
       Une fois l’opération terminée, l’état passe de **Inconnu** à **Installé** .

## <a name="remove-firmware-packages"></a>Supprimer les packages de microprogrammes

Supprimez les packages de microprogrammes après leur installation complète sur les lecteurs et les nœuds à l’aide des commandes suivantes. Vérifiez que vous disposez du nom de fichier complet et remplacez v* par la version réelle du package :
   ```
   isi upgrade patches uninstall IsiFw_Package_ v*.tar --rolling=false
   ```
   Par exemple, si la version du package est IsiFw_Package_ v10.3.1.tar, exécutez la commande suivante :
   ```
   isi upgrade patches uninstall IsiFw_Package_ v10.3.1.tar --rolling=false
   ```
À partir d’une session SSH active, exécutez :
   ```
   cd /ifs/data/
   rm -f Drive_Support_v*.tgz
   rm -f Drive_Support_v*.tar
   rm -f Drive_Support_v*.tar.md5
   rm -f Drive_Support_v*.tar.sha256
   ```
   Par exemple :
   ```
   cd /ifs/data/
   rm -f Drive_Support_v10.3.1.tgz
   rm -f Drive_Support_v10.3.1.tar
   rm -f Drive_Support_v10.3.1.tar.md5
   rm -f Drive_Support_v10.3.1.tar.sha256
   ```


        
<!-- ## Contact Dell EMC for support -->

Contactez le Support Microsoft pour obtenir de l’aide, quel que soit le problème.

## <a name="next-steps"></a>Étapes suivantes

- [Contrôles d’intégrité du stockage étendu](extended-storage-health-checks.md)
