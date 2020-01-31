---
title: Activer la sauvegarde d’Azure Stack Hub à partir du portail administrateur
description: Découvrez comment activer le service Infrastructure Backup du portail administrateur de façon à ce qu’il soit possible de restaurer Azure Stack Hub en cas de défaillance.
author: justinha
ms.topic: article
ms.date: 08/21/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 08/21/2019
ms.openlocfilehash: 0aa5fca80cfc408088f34923a607d4200ece15cb
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76878205"
---
# <a name="enable-backup-for-azure-stack-hub-from-the-administrator-portal"></a>Activer la sauvegarde d’Azure Stack Hub à partir du portail administrateur

Vous pouvez activer le service Infrastructure Backup à partir du portail administrateur pour permettre à Azure Stack Hub de générer des sauvegardes d’infrastructure. Le partenaire matériel peut utiliser ces sauvegardes pour restaurer votre environnement par récupération cloud en cas [d’erreur grave](./azure-stack-backup-recover-data.md). L’objectif d’une récupération cloud est de s’assurer que les opérateurs et utilisateurs peuvent se reconnecter au portail une fois la récupération terminée. Les abonnements des utilisateurs seront restaurés, notamment :

- Les rôles et les autorisations d’accès en fonction du rôle
- Les plans et les offres d’origine
- Les quotas de calcul, de stockage et de réseau précédemment définis
- Les secrets Key Vault

Toutefois, le service Infrastructure Backup ne permet pas de sauvegarder les machines virtuelles IaaS, les configurations réseau ou les ressources de stockage, telles que les comptes de stockage, les objets blob, les tables, etc. Les utilisateurs qui se connectent après une récupération cloud ne verront pas les ressources qui existaient avant la récupération. Les ressources et données Platform as a service (PaaS) ne sont également pas sauvegardées par le service.

Les administrateurs et les utilisateurs sont en charge de sauvegarder et de restaurer les ressources IaaS et PaaS indépendamment des processus de sauvegarde d’infrastructure. Pour plus d’informations sur la sauvegarde des ressources IaaS et PaaS, consultez les liens suivants :

- [Protéger des machines virtuelles déployées sur Azure Stack Hub](../user/azure-stack-manage-vm-protect.md)
- [Sauvegarde de votre application dans Azure](https://docs.microsoft.com/azure/app-service/manage-backup)
- [Qu’est-ce que SQL Server sur machines virtuelles Azure ? (Windows)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview)


## <a name="enable-or-reconfigure-backup"></a>Activer ou reconfigurer une sauvegarde

1. Ouvrez le [portail administrateur Azure Stack Hub](azure-stack-manage-portals.md).
2. Sélectionnez **Tous les services** puis, sous la catégorie **ADMINISTRATION**, sélectionnez **Sauvegarde d’infrastructure**. Choisissez **Configuration** dans le panneau **Sauvegarde d’infrastructure**.
3. Saisissez le chemin d’accès à l’**emplacement de stockage de sauvegarde**. Utilisez une chaîne UNC (Universal Naming Convention) pour le chemin d’un partage de fichiers hébergé sur un appareil distinct. Une chaîne UNC spécifie l’emplacement de ressources telles que des appareils ou des fichiers partagés. Pour le service, vous pouvez utiliser une adresse IP. Pour garantir la disponibilité des données de sauvegarde après un sinistre, l’appareil doit se trouver dans un emplacement distinct.

    > [!Note]  
    > Si votre environnement prend en charge la résolution de noms entre le réseau d’infrastructure Azure Stack Hub et votre environnement d’entreprise, vous pouvez utiliser un nom de domaine complet (FQDN) plutôt que l’adresse IP.

4. Saisissez le **Nom d’utilisateur** à l’aide du domaine et du nom d’utilisateur avec un accès suffisant pour lire et écrire des fichiers. Par exemple : `Contoso\backupshareuser`.
5. Saisissez le **mot de passe** de l’utilisateur.
6. Saisissez une nouvelle fois le mot de passe pour le **confirmer**.
7. La **fréquence en heures** détermine la fréquence à laquelle les sauvegardes sont créées. La valeur par défaut est 12. Scheduler prend en charge un maximum de 12 heures et un minimum de 4 heures. 
8. La **période de rétention en jours** détermine le nombre de jours pendant lesquels les sauvegardes sont conservées sur l’emplacement externe. La valeur par défaut est 7. Scheduler prend en charge un maximum de 14 jours et un minimum de 2 jours. Les sauvegardes antérieures à la période de rétention sont automatiquement supprimées de l’emplacement externe.

    > [!Note]  
    > Si vous souhaitez archiver des sauvegardes antérieures à la période de conservation, veillez à sauvegarder les fichiers de sauvegarde avant que le planificateur ne les supprime. Si vous réduisez la période de rétention de sauvegarde (par exemple, de 7 à 5 jours), le planificateur supprime toutes les sauvegardes antérieures à la nouvelle période de rétention. Confirmez que vous acceptez la suppression des sauvegardes avant de mettre à jour cette valeur.

9. Dans les paramètres de chiffrement, indiquez un certificat dans la zone de fichier .cer Certificat. Les fichiers de sauvegarde sont chiffrés avec cette clé publique dans le certificat. Le certificat spécifié lors de la configuration des paramètres de sauvegarde ne doit comporter que la partie clé publique. Une fois ce certificat défini pour la première fois ou modifié par la suite, seule son empreinte numérique est visible. Il n’est pas possible de télécharger ou d’afficher le fichier de certificat chargé. Pour créer le fichier de certificat, exécutez la commande PowerShell suivante, qui permet de créer un certificat auto-signé avec les clés publique et privée et d’exporter un certificat ne comportant que la partie clé publique. Vous pouvez enregistrer le certificat partout où il est accessible à partir du portail administrateur.

    ```powershell

        $cert = New-SelfSignedCertificate `
            -DnsName "www.contoso.com" `
            -CertStoreLocation "cert:\LocalMachine\My"

        New-Item -Path "C:\" -Name "Certs" -ItemType "Directory" 
        Export-Certificate `
            -Cert $cert `
            -FilePath c:\certs\AzSIBCCert.cer 
    ```

   > [!Note]
   > **Version 1901 et versions ultérieures** : Azure Stack Hub accepte un certificat pour chiffrer les données de sauvegarde de l’infrastructure. Veillez à stocker le certificat comportant la clé publique et la clé privée dans un emplacement sécurisé. Pour des raisons de sécurité, il est déconseillé de l’utiliser pour configurer les paramètres de sauvegarde. Pour savoir comment gérer le cycle de vie de ce certificat, consultez [Meilleures pratiques relatives au service Infrastructure Backup](azure-stack-backup-best-practices.md).
   > 
   > **1811 ou versions antérieures** : Azure Stack Hub accepte une clé symétrique pour chiffrer les données de sauvegarde de l’infrastructure. Utilisez la [cmdlet New-AzsEncryptionKey64 pour créer une clé](https://docs.microsoft.com/powershell/module/azs.backup.admin/new-azsencryptionkeybase64). Après la mise à niveau de 1811 à 1901, les paramètres de sauvegarde conservent la clé de chiffrement. Il est recommandé de mettre à jour les paramètres de sauvegarde pour utiliser un certificat. La prise en charge de la clé de chiffrement est désormais déconseillée. Vous disposez d’au moins trois versions pour mettre à jour les paramètres afin d’utiliser un certificat.

10. Sélectionnez **OK** pour enregistrer vos paramètres de contrôleur de sauvegarde.

![Azure Stack Hub – Paramètres du contrôleur de sauvegarde](media/azure-stack-backup/backup-controller-settings-certificate.png)


## <a name="start-backup"></a>Démarrer la sauvegarde
Pour démarrer une sauvegarde, cliquez sur **Sauvegarder maintenant** afin de démarrer une sauvegarde à la demande. Le fait d’effectuer une sauvegarde à la demande ne modifie pas la date et l’heure de la prochaine sauvegarde planifiée. Une fois la tâche terminée, vous pouvez confirmer les paramètres dans **Éléments principaux** :

![Azure Stack Hub – Sauvegarde à la demande](media/azure-stack-backup/scheduled-backup.png)

Vous pouvez également exécuter l’applet de commande PowerShell **Start-AzsBackup** sur votre ordinateur d’administration Azure Stack Hub. Pour plus d’informations, voir [Sauvegarder Azure Stack Hub](azure-stack-backup-back-up-azure-stack.md).

## <a name="enable-or-disable-automatic-backups"></a>Activer ou désactiver les sauvegardes automatiques
Les sauvegardes sont automatiquement planifiées lorsque vous activez la sauvegarde. Vous pouvez vérifier la prochaine sauvegarde planifiée dans **Éléments principaux**. 

![Azure Stack Hub – Sauvegarde à la demande](media/azure-stack-backup/on-demand-backup.png)

Si vous devez désactiver les futures sauvegardes planifiées, cliquez sur **Désactiver les sauvegardes automatiques**. Lorsque vous désactivez des sauvegardes automatiques, les paramètres de sauvegarde configurés et la planification de sauvegarde sont conservés. Cette action indique simplement au planificateur d’ignorer les futures sauvegardes.

![Azure Stack Hub – Désactiver les sauvegardes planifiées](media/azure-stack-backup/disable-auto-backup.png)

Vérifiez que les futures sauvegardes planifiées ont été désactivées dans **Éléments principaux**:

![Azure Stack Hub – Confirmer que les sauvegardes ont été désactivées](media/azure-stack-backup/confirm-disable.png)

Cliquez sur **Activer les sauvegardes automatiques** pour informer le planificateur de démarrer les sauvegardes ultérieures à l’heure planifiée. 

![Azure Stack Hub – Activer les sauvegardes planifiées](media/azure-stack-backup/enable-auto-backup.png)


> [!Note]  
> Si vous avez configuré la sauvegarde de l’infrastructure avant la mise à jour vers 1807, les sauvegardes automatiques seront désactivées. Ainsi, les sauvegardes démarrées par Azure Stack Hub n’entrent pas en conflit avec celles démarrées par un moteur de planification de tâches externe. Après avoir désactivé un planificateur de tâche externe, cliquez sur **Activer les sauvegardes automatiques**.

## <a name="update-backup-settings"></a>Mettre à jour les paramètres de sauvegarde
À partir de la version 1901, la prise en charge de la clé de chiffrement est déconseillée. Si vous configurez une sauvegarde pour la première fois dans la version 1901, vous devez utiliser un certificat. Azure Stack Hub ne prend en charge la clé de chiffrement que si celle-ci est configurée avant la mise à jour vers la version 1901. Le mode de compatibilité descendante se poursuivra pour trois versions, après quoi les clés de chiffrement ne seront plus gérées.

### <a name="default-mode"></a>Mode par défaut
Dans les paramètres de chiffrement, si vous configurez la sauvegarde de l’infrastructure pour la première fois après l’installation ou le passage à la version 1901, vous devez utiliser un certificat. Les clés de chiffrement ne sont plus prises en charge.

Pour mettre à jour le certificat servant à chiffrer les données de sauvegarde, chargez un nouveau fichier .CER comportant la partie clé publique, puis sélectionnez OK pour enregistrer les paramètres.

Les nouvelles sauvegardes commenceront à utiliser la clé publique dans le nouveau certificat, sans incidence sur toutes les sauvegardes déjà créées avec le certificat précédent. Veillez à conserver l’ancien certificat dans un emplacement sécurisé au cas où il soit nécessaire pour une récupération cloud.

![Azure Stack Hub – Afficher l’empreinte numérique du certificat](media/azure-stack-backup/encryption-settings-thumbprint.png)

### <a name="backwards-compatibility-mode"></a>Mode de compatibilité descendante
Si vous avez configuré la sauvegarde avant le passage à la version 1901, les paramètres sont transférés sans aucun changement de comportement. Dans ce cas, la clé de chiffrement est prise en charge par compatibilité descendante. Vous pouvez mettre à jour la clé de chiffrement ou le commutateur pour utiliser un certificat. Vous avez au moins trois versions pour poursuivre la mise à jour de la clé de chiffrement. Profitez de ce laps de temps pour passer à un certificat. Pour créer une clé de chiffrement, utilisez [New-AzsEncryptionKeyBase64](https://docs.microsoft.com/powershell/module/azs.backup.admin/new-azsencryptionkeybase64).

![Azure Stack Hub – Utiliser une clé de chiffrement en mode de compatibilité descendante](media/azure-stack-backup/encryption-settings-backcompat-encryption-key.png)

> [!Note]  
> La mise à jour de la clé de chiffrement au certificat est une opération unidirectionnelle. Une fois cette modification effectuée, il n’est pas possible de revenir à la clé de chiffrement. Toutes les sauvegardes existantes resteront chiffrées avec l’ancienne clé de chiffrement.

![Azure Stack Hub – Utiliser un certificat de chiffrement en mode de compatibilité descendante](media/azure-stack-backup/encryption-settings-backcompat-certificate.png)

## <a name="next-steps"></a>Étapes suivantes

Apprenez à exécuter une sauvegarde. Voir [Sauvegarder Azure Stack Hub](azure-stack-backup-back-up-azure-stack.md).

Apprenez à vérifier que votre sauvegarde a été exécutée. Voir [Confirmer l’exécution de la sauvegarde sur le portail d’administration](azure-stack-backup-back-up-azure-stack.md).
