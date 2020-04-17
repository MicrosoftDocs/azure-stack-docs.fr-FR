---
title: Sauvegarder votre machine virtuelle sur Azure Stack Hub avec CommVault
description: Découvrez comment sauvegarder votre machine virtuelle sur Azure Stack Hub avec CommVault.
author: mattbriggs
ms.topic: how-to
ms.date: 10/30/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/30/2019
ms.openlocfilehash: ec02ccd45773c97df71369b1e325120452ce7da1
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "77702463"
---
# <a name="back-up-your-vm-on-azure-stack-hub-with-commvault"></a>Sauvegarder votre machine virtuelle sur Azure Stack Hub avec CommVault

## <a name="overview-of-backing-up-a-vm-with-commvault"></a>Présentation de la sauvegarde d’une machine virtuelle avec CommVault

Cet article explique comment configurer CommVault Live Sync pour mettre à jour une machine virtuelle de récupération située sur une unité d’échelle Azure Stack Hub distincte. Cet article explique comment configurer une solution de partenaire commune pour protéger et récupérer les données et l’état système des machines virtuelles déployées sur Azure Stack Hub.

Le schéma suivant montre la solution globale lors de l’utilisation de CommVault pour sauvegarder vos machines virtuelles.

![](./media/azure-stack-network-howto-backup-commvault/bcdr-commvault-overall-arc.png)

Dans cet article, vous allez :

1. Créez une machine virtuelle exécutant le logiciel CommVault sur votre instance Azure Stack Hub source.

2. Créez un compte de stockage dans un emplacement secondaire. Cet article suppose que vous allez créer un conteneur de blobs dans un compte de stockage dans une instance Azure Stack Hub distincte (la cible) et que l’instance Azure Stack Hub cible est accessible à partir de l’instance Azure Stack Hub source.

3. Configurez CommVault sur votre instance Azure Stack Hub source et ajoutez des machines virtuelles de l’infrastructure Azure Stack Hub source au groupe de machines virtuelles.

4. Configurez la fonctionnalité LifeSync de CommVault.

Vous pouvez également télécharger et proposer des images de machines virtuelles partenaires compatibles pour protéger vos machines virtuelles Azure Stack Hub sur un cloud Azure ou sur une autre infrastructure Azure Stack Hub. Cet article illustre la protection des machines virtuelles avec CommVault Live Sync.

Le schéma suivant présente la topologie de cette approche :

![](./media/azure-stack-network-howto-backup-commvault/backup-vm-commvault-diagram.png)

## <a name="create-the-commvault-vm-form-the-commvault-marketplace-item"></a>Créer la machine virtuelle CommVault à partir de l’élément de Place de marché CommVault

1. Connectez-vous au portail utilisateur Azure Stack Hub.

2. Sélectionnez **Créer une ressource** > **Compute** > **CommVault**.

    > [!Note]  
    > Si CommVault n’est pas disponible, contactez votre opérateur cloud.

    ![](./media/azure-stack-network-howto-backup-commvault/commvault-create-vm-01.png)

3. Configurez les paramètres de base dans **Créer une machine virtuelle, 1 De base** :

    a. Saisissez un **Nom**.

    b. Sélectionnez **HHD Standard**.
    
    c. Entrez un **nom d’utilisateur**.
    
    d. Entrez un **mot de passe**.
    
    e. Confirmez votre mot de passe.
    
    f. Sélectionnez un **abonnement** pour la sauvegarde.
    
    g. Sélectionnez un **groupe de ressources**.
    
    h. Sélectionnez l’**emplacement** de l’infrastructure Azure Stack Hub. Si vous utilisez un ASDK, sélectionnez **local**.
    
    i. Sélectionnez **OK**.

    ![](./media/azure-stack-network-howto-backup-commvault/commvault-create-vm-02.png)

4. Choisissez la taille de la machine virtuelle CommVault. La taille de la machine virtuelle pour la sauvegarde doit être d’au moins 10 Go de RAM et de 100 Go de stockage.

    ![](./media/azure-stack-network-howto-backup-commvault/commvault-create-vm-03.png).

5. Choisissez les paramètres de la machine virtuelle CommVault.

    a. Définissez la disponibilité sur **Aucun**.
    
    b. Sélectionnez **Oui** pour Utiliser des disques managés.
    
    c. Sélectionnez le réseau virtuel par défaut comme **réseau virtuel**.
    
    d. Sélectionnez le **sous-réseau** par défaut.
    
    e. Sélectionnez l’**adresse IP publique** par défaut.
    
    f. Laissez la machine virtuelle dans le groupe de sécurité réseau **De base**.
    
    g. Ouvrez les ports HTTP (80), HTTPs (443), SSH (22) et RDP (3389).
    
    h. Sélectionnez **Aucune extension**.
    
    i. Sélectionnez **Activé** pour **Diagnostics de démarrage**.
    
    j. Laissez l’option **Diagnostics du système d’exploitation invité** définie sur **Désactivé**.
    
    k. Laissez la valeur par défaut **Compte de stockage de diagnostics**.
    
    l. Sélectionnez **OK**.

6. Passez en revue le résumé de votre machine virtuelle CommVault une fois que cette dernière a réussi la validation. Sélectionnez **OK**.

## <a name="get-your-service-principal"></a>Obtenir votre principal de service

Vous devez savoir si votre gestionnaire d’identité est Azure AD ou AD DFS. Le tableau suivant contient les informations dont vous avez besoin pour configurer CommVault dans votre Azure Stack Hub.

| Élément | Description | Source |
|--------------------------|--------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| URL Azure Resource Manager | Point de terminaison Azure Stack Hub Resource Manager. | https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles-ruby?view=azs-1908#the-azure-stack-hub-resource-manager-endpoint |
| Nom de l'application |  |  |
| ID de l'application | L’ID d’application du principal de service enregistré lors de la création du principal de service dans la section précédente de cet article. | https://docs.microsoft.com/azure-stack/operator/azure-stack-create-service-principals?view=azs-1908 |
| Identifiant d’abonnement | Vous utilisez l’ID d’abonnement pour accéder à des offres dans Azure Stack Hub. | https://docs.microsoft.com/azure-stack/operator/service-plan-offer-subscription-overview?view=azs-1908#subscriptions |
| ID de locataire (ID de répertoire) | Votre ID de locataire Azure Stack Hub. | https://docs.microsoft.com/azure-stack/operator/azure-stack-identity-overview?view=azs-1908 |
| Mot de passe de l’application | Le secret d’application du principal de service enregistré lors de la création du principal de service. | https://docs.microsoft.com/azure-stack/operator/azure-stack-create-service-principals?view=azs-1908 |

## <a name="configure-backup-using-the-commvault-console"></a>Configurer la sauvegarde à l’aide de la console CommVault

1. Ouvrez votre client RDP et connectez-vous à la machine virtuelle Commavult dans votre Azure Stack Hub. Entrez vos informations d’identification.

2. Installez Azure Stack Hub PowerShell et les outils Azure Stack Hub sur la machine virtuelle CommVault.

    a. Pour obtenir des instructions sur l’installation d’Azure Stack Hub PowerShell, voir [Installer PowerShell pour Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fuser%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fbreadcrumb%2Ftoc.json).  
    b. Pour obtenir des instructions sur l’installation des outils Azure Stack Hub, voir [Télécharger les outils Azure Stack Hub à partir de GitHub](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-download?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fuser%2FTOC.json%3Fview%3Dazs-1908&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fbreadcrumb%2Ftoc.json%3Fview%3Dazs-1908&view=azs-1908).

3. Après l’installation de CommVault sur votre machine virtuelle CommVault, ouvrez la console CommCell. Dans Démarrer, sélectionnez **CommVault** > **Console CommVault CommCell**.

    ![](./media/azure-stack-network-howto-backup-commvault/commcell-console.png)

4. Configurez vos référentiels de sauvegarde pour qu’ils utilisent un stockage externe à l’infrastructure Azure Stack Hub dans la console CommVault CommCell. Dans le navigateur CommCell, sélectionnez Ressources de stockage > Pools de stockage. Cliquez avec le bouton droit et sélectionnez **Ajouter un pool de stockage**. Sélectionnez **Cloud**.

5. Ajoutez le nom du pool de stockage. Sélectionnez **Suivant**.

6. Sélectionnez **Créer** > **Stockage cloud**.

    ![](./media/azure-stack-network-howto-backup-commvault/commcell-storage-add-storage-device.png)

7. Sélectionnez votre fournisseur de services cloud. Dans cette procédure, vous allez utiliser une deuxième infrastructure Azure Stack Hub située dans un autre emplacement. Sélectionnez Stockage Microsoft Azure.

8. Sélectionnez votre machine virtuelle CommVault en tant que MediaAgent.

9. Entrez les informations d’accès à votre compte de stockage. Vous trouverez des instructions sur la configuration d’un compte de stockage Azure ici. Informations d’accès :

    -  **Hôte de service** : Récupérez le nom de l’URL à partir des propriétés du conteneur de blobs dans votre ressource. Par exemple, mon URL était https:\//backuptest.blob.westus.stackpoc.com/mybackups et j’ai utilisé blob.westus.stackpoc.com dans l’hôte Service.
    
    -   **Nom du compte** : utilisez le nom du compte de stockage. Vous pouvez le trouver dans le panneau Clés d’accès de la ressource de stockage.
    
    -   **Clé d’accès** : Obtenez la clé d’accès dans le panneau Clés d’accès de la ressource de stockage.
    
    -   **Conteneur** : nom du conteneur. Dans le cas présent, mybackups.
    
    -   **Classe de stockage** : conservez la classe de stockage par défaut du conteneur Utilisateur.

10. Créez un client Microsoft Azure Stack Hub en suivant les instructions fournies dans [Création d’un client Microsoft Azure Stack Hub](https://documentation.commvault.com/commvault/v11_sp13/article?p=86495.htm)

    ![](./media/azure-stack-network-howto-backup-commvault/commcell-ceate-client.png)

11. Sélectionnez les machines virtuelles ou les groupes de ressources à protéger et à attacher à une stratégie de sauvegarde.

12. Configurez votre planification de sauvegarde en fonction de vos besoins RPO pour la récupération.

13. Effectuez la première sauvegarde complète.

## <a name="configure-commvault-live-sync"></a>Configurer CommVault Live Sync 

Deux options sont disponibles. Vous pouvez choisir de répliquer les modifications à partir de la copie principale des sauvegardes ou de répliquer les modifications à partir d’une copie secondaire sur la machine virtuelle de récupération. La réplication à partir d’un jeu de sauvegarde élimine l’impact des E/S de lecture sur la machine source.

1. Pendant la configuration de Live Sync, vous devez fournir l’infrastructure Azure Stack Hub source (agent de serveur virtuel) et les détails de l’infrastructure Azure Stack Hub cible.

2. Pour plus d’instructions sur la configuration de CommVault Live Sync, voir [Réplication Live Sync pour Microsoft Azure Stack Hub](https://documentation.commvault.com/commvault/v11_sp13/article?p=94386.htm).

    ![](./media/azure-stack-network-howto-backup-commvault/live-sync-1.png)
 
3. Pendant la configuration de Live Sync, vous devez fournir les détails de l’infrastructure Azure Stack Hub cible et de l’agent de serveur virtuel.

    ![](./media/azure-stack-network-howto-backup-commvault/live-sync-2.png)

4. Poursuivez la configuration et ajoutez le compte de stockage cible dans lequel les disques de réplica seront hébergés, le ou les groupes de ressources où les machines virtuelles de réplication seront placées et le nom que vous souhaitez associer aux machines virtuelles de réplication.

    ![](./media/azure-stack-network-howto-backup-commvault/live-sync-3.png)

5. Vous pouvez également modifier la taille de la machine virtuelle et configurer les paramètres réseau en sélectionnant **Configurer** en regard de chaque machine virtuelle.

6. Définir la fréquence de réplication sur l’infrastructure Azure Stack Hub cible

    ![](./media/azure-stack-network-howto-backup-commvault/live-sync-5.png)

7. Passez en revue vos paramètres pour enregistrer la configuration. L’environnement de récupération sera ensuite créé et la réplication commencera à l’intervalle choisi.


## <a name="set-up-failover-behavior-using-live-sync"></a>Configurer le comportement de basculement à l’aide de Live Sync

CommVault Live Sync vous permet de basculer des machines d’une infrastructure Azure Stack Hub vers une autre, et d’effectuer une restauration automatique pour reprendre les opérations sur l’infrastructure Azure Stack Hub d’origine. Le workflow est automatisé et journalisé.

![](./media/azure-stack-network-howto-backup-commvault/back-up-live-sync-panel.png)

Sélectionnez les machines virtuelles que vous souhaitez basculer vers votre infrastructure Azure Stack Hub de récupération et choisissez un basculement planifié ou non planifié. Un basculement planifié est approprié lorsqu’il est temps d’arrêter l’environnement de production de manière appropriée avant de reprendre les opérations dans le site de récupération. Le basculement planifié arrête les machines virtuelles de production, réplique les modifications finales sur le site de récupération, puis met les machines virtuelles de récupération en ligne avec les données les plus récentes et applique la taille de machine virtuelle et la configuration réseau spécifiées lors de la configuration de Live Sync. Un basculement non planifié tente d’arrêter les machines virtuelles de production, mais se poursuit si l’environnement de production n’est pas disponible et met simplement les machines virtuelles de récupération en ligne avec le dernier jeu de données de réplication reçu appliqué à la machine virtuelle, ainsi que la taille et le réseau choisis précédemment. Les images ci-dessous illustrent un basculement non planifié dans lequel les machines virtuelles de récupération ont été mises en ligne par CommVault Live Sync.

![](./media/azure-stack-network-howto-backup-commvault/unplanned-failover.png)

![](./media/azure-stack-network-howto-backup-commvault/fail-over-2.png)

![](./media/azure-stack-network-howto-backup-commvault/fail-over-3.png)

## <a name="next-steps"></a>Étapes suivantes

[Différences et considérations relatives aux réseaux Azure Stack Hub](azure-stack-network-differences.md)  