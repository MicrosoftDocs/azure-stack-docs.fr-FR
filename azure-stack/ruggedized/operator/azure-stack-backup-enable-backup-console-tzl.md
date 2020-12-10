---
title: Activer la sauvegarde Azure Stack à partir du portail d’administration | Microsoft Docs
description: Découvrez comment activer le service Infrastructure Backup sur le portail d’administration pour restaurer Azure Stack en cas de panne.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 12/16/2019
ms.openlocfilehash: 3864183ecda856500db1fcbfe38df84d70f3bae9
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96941082"
---
# <a name="configure-backup-for-azure-stack-hub-from-the-administrator-portal"></a>Configurer la sauvegarde d’Azure Stack Hub à partir du portail d’administration

*S’applique à : Modular Data Center, Azure Stack Hub renforcé*

Vous pouvez configurer le service Infrastructure Backup depuis le portail d’administration pour exporter les sauvegardes d’infrastructure vers un emplacement de stockage externe. Les sauvegardes d’infrastructure peuvent être utilisées par le support pour corriger les services dégradés.

## <a name="configure-backup"></a>Configurer une sauvegarde

1. Ouvrez le [portail d’administration Azure Stack](../../operator/azure-stack-manage-portals.md).

2. Sélectionnez **Tous les services** puis, sous la catégorie **Administration**, sélectionnez **Sauvegarde d’infrastructure**. Choisissez **Configuration** dans le panneau **Sauvegarde d’infrastructure**.

3. Saisissez le chemin d’accès à l’**emplacement de stockage de sauvegarde**. Utilisez une chaîne UNC (Universal Naming Convention) pour le chemin d’un partage de fichiers hébergé sur un appareil distinct. Une chaîne UNC spécifie l’emplacement de ressources telles que des appareils ou des fichiers partagés. Pour le service, vous pouvez utiliser une adresse IP. Pour garantir la disponibilité des données de sauvegarde après un sinistre, l’appareil doit se trouver dans un emplacement distinct.

    > [!NOTE]  
    > Si votre environnement prend en charge la résolution de noms entre le réseau d’infrastructure Azure Stack et votre environnement d’entreprise, vous pouvez utiliser un nom de domaine complet plutôt que l’adresse IP.

4. Saisissez le **Nom d’utilisateur** à l’aide du domaine et du nom d’utilisateur avec un accès suffisant pour lire et écrire des fichiers, par exemple, **Contoso\backupshareuser**.

5. Saisissez le **mot de passe** de l’utilisateur.

6. Saisissez une nouvelle fois le mot de passe pour le **confirmer**.

7. La **fréquence en heures** détermine la fréquence à laquelle les sauvegardes sont créées. La valeur par défaut est 12. Scheduler prend en charge un minimum de 4 jours et un maximum de 12 jours.

8. La **période de rétention en jours** détermine le nombre de jours pendant lesquels les sauvegardes sont conservées sur l’emplacement externe. La valeur par défaut est 7. Scheduler prend en charge un minimum de 2 jours et un maximum de 14 jours. Les sauvegardes antérieures à la période de rétention sont automatiquement supprimées de l’emplacement externe.

   > [!NOTE]
   > Si vous souhaitez archiver des sauvegardes antérieures à la période de conservation, veillez à sauvegarder les fichiers de sauvegarde avant que le planificateur ne les supprime. Si vous réduisez la période de rétention de sauvegarde (par exemple, de 7 à 5 jours), le planificateur supprime toutes les sauvegardes antérieures à la nouvelle période de rétention. Confirmez que vous acceptez la suppression des sauvegardes avant de mettre à jour cette valeur.

9. Dans **Paramètres de chiffrement**, l’empreinte de certificat public correspond au certificat fourni lors du déploiement. Il n’est pas nécessaire de mettre à jour le certificat existant.

10. Sélectionnez **OK** pour enregistrer vos paramètres de contrôleur de sauvegarde.

    ![Azure Stack - Paramètres du contrôleur de sauvegarde](media/azure-stack-backup-enable-backup-console-tzl/backup-controller-settings-certificate.png)

## <a name="enable-or-disable-automatic-backups"></a>Activer ou désactiver les sauvegardes automatiques

Les sauvegardes sont automatiquement activées lors du déploiement. Une fois l’emplacement de stockage configuré, les sauvegardes automatiques exportent les sauvegardes vers un emplacement de stockage externe selon le paramètre fréquence. Vous pouvez vérifier la prochaine sauvegarde planifiée dans le panneau **Éléments principaux**.

![Azure Stack - sauvegarde à la demande](media/azure-stack-backup-enable-backup-console-tzl/on-demand-backup.png)

Si vous devez désactiver les futures sauvegardes planifiées, sélectionnez **Désactiver les sauvegardes automatiques**. Lorsque vous désactivez des sauvegardes automatiques, les paramètres de sauvegarde configurés et la planification de sauvegarde sont conservés. Cette action indique au planificateur d’ignorer les futures sauvegardes.

![Azure Stack - Désactiver les sauvegardes planifiées](media/azure-stack-backup-enable-backup-console-tzl/disable-auto-backup.png)

Vérifiez que les futures sauvegardes planifiées ont été désactivées dans **Éléments principaux**:

![Azure Stack - confirmer que les sauvegardes ont été désactivées](media/azure-stack-backup-enable-backup-console-tzl/confirm-disable.png)

Sélectionnez **Activer les sauvegardes automatiques** pour indiquer au planificateur de démarrer les sauvegardes ultérieures à l’heure planifiée.

![Azure Stack - Activer les sauvegardes planifiées](media/azure-stack-backup-enable-backup-console-tzl/enable-auto-backup.png)

## <a name="update-backup-settings"></a>Mettre à jour les paramètres de sauvegarde

Pour mettre à jour le certificat servant à chiffrer les données de sauvegarde, chargez un nouveau fichier .CER comportant la partie clé publique, puis sélectionnez OK pour enregistrer les paramètres.

Les nouvelles sauvegardes commenceront à utiliser la clé publique dans le nouveau certificat, sans incidence sur toutes les sauvegardes déjà créées avec le certificat précédent. Veillez à conserver l’ancien certificat dans un emplacement sécurisé au cas où vous en auriez besoin pour une récupération cloud.

![Azure Stack – voir l’empreinte numérique du certificat](media/azure-stack-backup-enable-backup-console-tzl/encryption-settings-thumbprint.png)

## <a name="next-steps"></a>Étapes suivantes

Pour vérifier que votre sauvegarde a bien été exécutée, consultez [Vérifier que la sauvegarde est terminée sur le portail d'administration](../../operator/azure-stack-backup-back-up-azure-stack.md).
