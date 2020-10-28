---
title: Valider un cluster Azure Stack HCI
description: Comprendre l’importance de la validation du cluster et le moment auquel l’exécuter sur un cluster Azure Stack HCI existant. Explorez les scénarios de résolution des problèmes d’un cluster de serveurs mis à jour.
author: JohnCobb1
ms.author: v-johcob
ms.topic: article
ms.date: 10/16/2020
ms.openlocfilehash: fe49df76ccb2a90849587acd5d4df7a41e329efb
ms.sourcegitcommit: 301e571626f8e85556d9eabee3f385d0b81fdef4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92157697"
---
# <a name="validate-an-azure-stack-hci-cluster"></a>Valider un cluster Azure Stack HCI

>S’applique à : Azure Stack HCI, version v20H2 ; Windows Server 2019

Cet article de procédure expose les raisons pour lesquelles la validation de cluster est si importante, et il explique à quel moment l’exécuter sur un cluster Azure Stack HCI existant. Nous vous recommandons d’effectuer une validation de cluster dans les principaux scénarios suivants :
- Après avoir déployé un cluster de serveurs, exécutez l’outil Validate-DCB pour tester le réseau.
- Après avoir mis à jour un cluster de serveurs. En fonction de votre scénario, exécutez les deux options de validation pour résoudre les problèmes de cluster.
- Après avoir configuré la réplication avec le réplica de stockage. Vérifiez que la réplication se poursuit normalement en vérifiant certains événements et en exécutant quelques commandes.
- Après avoir créé un cluster de serveurs, exécutez l’outil Validate-DCB avant de le placer en production.

    Pour en savoir plus sur le déploiement d’un cluster Azure Stack HCI, consultez la [Vue d’ensemble du déploiement](deployment-overview.md).

## <a name="what-is-cluster-validation"></a>Qu’est-ce que la validation de cluster ?
La validation de cluster est conçue pour intercepter les problèmes liés au matériel ou à la configuration avant la mise en production du cluster. La validation de cluster vous permet de vérifier que la solution Azure Stack HCI que vous êtes sur le point de déployer est réellement fiable. Vous pouvez également utiliser la validation de cluster sur des clusters de basculement qui sont configurés comme un outil de diagnostic.

## <a name="specific-validation-scenarios"></a>Scénarios de validation
Cette section aborde d’autres scénarios dans lesquels la validation est également nécessaire ou utile.

- **Validation avant la configuration du cluster :**
  - **Un ensemble de serveurs prêt à constituer un cluster de basculement :** Il s’agit du scénario de validation le plus simple. Les composants matériels (systèmes, réseaux et stockage) sont connectés, mais les systèmes ne fonctionnent pas encore en tant que cluster. Dans cette situation, l’exécution de tests n’a aucun impact sur la disponibilité.
 
  - **Machines virtuelles serveurs :** Pour les serveurs virtualisés situés dans un cluster, exécutez la validation de cluster comme vous le feriez pour n’importe quel autre nouveau cluster. Pour exécuter la fonctionnalité, les exigences sont les mêmes que vous disposiez :
    - D’un « cluster hôte », où le basculement se produit entre deux ordinateurs physiques.
    - D’un « cluster invité », où le basculement se produit entre les systèmes d’exploitation invités d’un même ordinateur physique.
 
- **Validation après la configuration et l’utilisation du cluster :**

  - **Avant l’ajout d’un serveur au cluster :** Lorsque vous ajoutez un serveur à un cluster, nous vous recommandons vivement de valider le cluster. Lorsque vous exécutez la validation de cluster, spécifiez les membres de cluster existants et le nouveau serveur.
  
  - **Lors de l’ajout de lecteurs :** Lorsque vous ajoutez des lecteurs au cluster (ce qui n’est pas la même chose que remplacer des lecteurs défaillants ou créer des disques virtuels ou des volumes qui reposent sur les lecteurs existants), exécutez la validation de cluster pour être sûr que le nouveau stockage fonctionnera correctement.

  - **Lors de l’apport de modifications qui affectent le microprogramme ou les pilotes**  : Si vous effectuez une mise à niveau ou si vous apportez des modifications au cluster qui affectent le microprogramme ou les pilotes, vous devez exécuter la validation de cluster afin de vérifier que la nouvelle combinaison de matériel, microprogramme, pilotes et logiciels prend en charge la fonctionnalité de cluster de basculement.

  - **Après la restauration d’un système à partir d’une sauvegarde**  : Après avoir restauré un système à partir d’une sauvegarde, exécutez la validation de cluster pour vérifier que le système fonctionne correctement au sein d’un cluster.

## <a name="validate-networking"></a>Vérifier le réseau
L’outil Validate-DCB de Microsoft est conçu pour valider la configuration DCB (Data Center Bridging) sur le cluster. Pour cela, l’outil utilise comme entrée une configuration prévue, puis teste chaque serveur du cluster. Cette section explique comment installer et exécuter l’outil Validate-DCB, comment passer en revue les résultats et comment résoudre les erreurs de réseau détectées par l’outil.

Sur le réseau, l’accès direct à la mémoire à distance (RDMA) sur l’Ethernet convergé (RoCE) nécessite des technologies DCB pour que la structure fabric réseau ne connaisse pas de pertes. Avec iWARP, DCB est facultatif. Toutefois, la configuration de DCB peut être complexe, en exigeant notamment une configuration exacte sur :
- Chaque serveur du cluster
- Chaque port réseau que le trafic RDMA traverse dans la structure fabric

### <a name="prerequisites"></a>Prérequis
- Les informations de configuration réseau du cluster de serveurs que vous souhaitez valider, notamment :
    - Le nom du cluster hôte ou du cluster de serveurs
    - Le nom du commutateur virtuel
    - Les noms de la carte réseau
    - Les paramètres PFC (Priority Flow Control) et les paramètres ETS (Enhanced Transmission Selection)
- Une connexion Internet pour télécharger le module de l’outil dans Windows PowerShell à partir de Microsoft.

### <a name="install-and-run-the-validate-dcb-tool"></a>Installer et exécuter l’outil Validate-DCB
Pour installer et exécuter l’outil Validate-DCB :
1. Sur votre PC de gestion, ouvrez une session Windows PowerShell en tant qu’administrateur, puis utilisez la commande suivante pour installer l’outil.

    ```powershell
    Install-Module Validate-DCB
    ```

1. Acceptez les demandes d’utilisation du fournisseur NuGet, puis accédez au dépôt pour installer l’outil.
1. Une fois que PowerShell est connecté au réseau Microsoft pour télécharger l’outil, tapez `Validate-DCB`, puis appuyez sur **Entrée** pour démarrer l’Assistant de l’outil.

    > [!NOTE]
    > Si vous ne pouvez pas exécuter le script de l’outil Validate-DCB, vous devrez peut-être ajuster vos stratégies d’exécution PowerShell. Utilisez l’applet de commande Get-ExecutionPolicy pour afficher vos paramètres de stratégie d’exécution de script actuels. Pour plus d’informations sur la configuration des stratégies d’exécution dans PowerShell, consultez [À propos des stratégies d’exécution](/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-7).

1. Dans la page Welcome to the Validate-DCB configuration wizard (Bienvenue dans l’Assistant Configuration de Validate-DCB), sélectionnez **Suivant** .
1. Dans la page Clusters and Nodes (Clusters et nœuds), tapez le nom du cluster de serveurs que vous souhaitez valider, sélectionnez **Resolve** (Résoudre) pour le lister dans la page, puis sélectionnez **Next** (Suivant).

    :::image type="content" source="../media/validate/clusters-and-nodes.png" alt-text="Page Clusters and Nodes de l’Assistant Configuration de Validate-DCB":::

1. Dans la page Adapters (Cartes réseau) :
   1. Cochez la case **vSwitch attached** (Commutateur virtuel attaché), puis tapez le nom du commutateur virtuel.
   1. Sous **Adapter Name** (Nom de la carte réseau), tapez le nom de chaque carte réseau physique. Ensuite, sous **Host vNIC Name** (Nom de la carte réseau virtuelle hôte), tapez le nom de chaque carte réseau virtuelle. Enfin, sous **VLAN** , tapez l’ID de réseau local virtuel (VLAN) qui est utilisé pour chaque carte réseau.
   1. Développez la zone de liste déroulante **RDMA Type** , puis sélectionnez le protocole approprié : **RoCE** ou **iWARP** . Configurez également **Jumbo Frames** (Trames Jumbo) sur la valeur qui convient à votre réseau, puis sélectionnez **Next** .

    :::image type="content" source="../media/validate/adapters.png" alt-text="Page Clusters and Nodes de l’Assistant Configuration de Validate-DCB" lightbox="../media/validate/adapters.png":::

    > [!NOTE]
    > - Pour en savoir plus sur la façon dont SR-IOV améliore les performances du réseau, consultez [Vue d’ensemble de la virtualisation d’E/S d’une racine unique (SR-IOV)](/windows-hardware/drivers/network/overview-of-single-root-i-o-virtualization--sr-iov-).

1. Dans la page Data Center Bridging, modifiez les valeurs pour qu’elles correspondent aux paramètres de votre organisation concernant la priorité ( **Priority** ), le nom de la stratégie ( **Policy Name** ) et la réservation de bande passante ( **Bandwidth Reservation** ), puis sélectionnez **Next** .

    :::image type="content" source="../media/validate/data-center-bridging.png" alt-text="Page Clusters and Nodes de l’Assistant Configuration de Validate-DCB" lightbox="../media/validate/data-center-bridging.png":::

    > [!NOTE]
    > Si vous avez sélectionné RDMA sur RoCE dans la page précédente de l’Assistant, vous aurez besoin de DCB pour garantir la fiabilité du réseau sur l’ensemble des cartes réseau et des ports de commutateur.

1. Dans la page Save and Deploy (Enregistrer et déployer), dans la zone **Configuration File Path** (Chemin du fichier de configuration), enregistrez le fichier de configuration au format .ps1, à un emplacement où vous pourrez l’utiliser ultérieurement si nécessaire. Ensuite, sélectionnez **Export** pour démarrer l’exécution de l’outil Validate-DCB.

   - Si vous le souhaitez, vous pouvez déployer votre fichier de configuration en renseignant la section **Deploy Configuration to Nodes** (Déployer la configuration sur les nœuds) de la page, ce qui vous permet d’utiliser un compte Azure Automation pour déployer la configuration, puis pour la valider. Pour bien démarrer avec Azure Automation, consultez [Créer un compte Azure Automation](/azure/automation/automation-quickstart-create-account).

    :::image type="content" source="../media/validate/save-and-deploy.png" alt-text="Page Clusters and Nodes de l’Assistant Configuration de Validate-DCB":::

### <a name="review-results-and-fix-errors"></a>Examiner les résultats et corriger les erreurs
L’outil Validate-DCB génère des résultats dans deux unités :
1. Les résultats [Global Unit] listent les prérequis et la configuration requise pour exécuter les tests modaux.
1. Les résultats [Modal Unit] fournissent des commentaires sur chaque configuration d’hôte de cluster et sur les bonnes pratiques.

Cet exemple montre les résultats de l’analyse réussie d’un serveur unique pour l’ensemble des preréquis et des tests unitaires modaux en indiquant un nombre d’échecs égal à 0.

:::image type="content" source="../media/validate/global-unit-and-modal-unit-results.png" alt-text="Page Clusters and Nodes de l’Assistant Configuration de Validate-DCB":::

Les étapes suivantes montrent comment identifier une erreur de paquet Jumbo à partir d’une carte réseau virtuelle SMB02, et comment corriger cette erreur :
1. Les résultats des analyses de l’outil Validate-DCB affichent une erreur avec un nombre d’échecs égal à 1.

    :::image type="content" source="../media/validate/failed-count-error-1.png" alt-text="Page Clusters and Nodes de l’Assistant Configuration de Validate-DCB":::

1. Si vous faites défiler les résultats, vous verrez une erreur en rouge qui indique que le paquet Jumbo pour la carte réseau virtuelle SMB02 sur l’ordinateur hôte S046036 est défini sur la taille par défaut de 1514, alors qu’il doit être défini sur une taille de 9014.

    :::image type="content" source="../media/validate/jumbo-packet-setting-error.png" alt-text="Page Clusters and Nodes de l’Assistant Configuration de Validate-DCB":::

1. Quand vous consultez les propriétés avancées ( **Advanced** ) de la carte réseau virtuelle SMB02 sur l’ordinateur hôte S046036, vous voyez que le paquet Jumbo est défini sur la valeur par défaut **Disabled** (Désactivé).

    :::image type="content" source="../media/validate/hyper-v-advanced-properties-jumbo-packet-setting.png" alt-text="Page Clusters and Nodes de l’Assistant Configuration de Validate-DCB":::

1. Pour résoudre l’erreur, vous devez activer la fonctionnalité Jumbo Packet (Paquet Jumbo) et configurer sa taille sur 9 014 octets. Le fait de réexécuter l’analyse sur l’hôte S046036 confirme que la modification a bien été effectuée en retournant un nombre d’échecs égal à 0.

    :::image type="content" source="../media/validate/jumbo-packet-error-fix-confirmation.png" alt-text="Page Clusters and Nodes de l’Assistant Configuration de Validate-DCB":::

Pour savoir comment résoudre les erreurs détectées par l’outil Validate-DCB, consultez la vidéo suivante.

> [!VIDEO https://www.youtube.com/embed/cC1uACvhPBs]

## <a name="validate-the-cluster"></a>Valider le cluster
Effectuez les étapes suivantes pour valider les serveurs présents dans un cluster existant dans Windows Admin Center.

1. Dans Windows Admin Center, sous **All connections** (Toutes les connexions), sélectionnez le cluster Azure Stack HCI que vous souhaitez valider, puis sélectionnez **Connect** .

    Le tableau de bord du Gestionnaire de clusters ( **Cluster Manager Dashboard** ) affiche des informations générales sur le cluster.

1. Dans **Cluster Manager Dashboard** , sous **Tools** (Outils), sélectionnez **Servers** (Serveurs).
1. Dans la page **Inventory** (Inventaire), sélectionnez les serveurs du cluster, développez le sous-menu **More** , puis sélectionnez **Validate cluster** (Valider le cluster).
1. Dans la fenêtre contextuelle **Validate Cluster** (Valider le cluster), sélectionnez **Yes** .

    :::image type="content" source="../media/validate/validate-cluster-pop-up.png" alt-text="Page Clusters and Nodes de l’Assistant Configuration de Validate-DCB":::

1. Dans la fenêtre contextuelle **Credential Security Service Provider (CredSSP)** , sélectionnez **Yes** .
1. Indiquez vos informations d’identification pour activer **CredSSP** , puis sélectionnez **Continue** .<br> La validation de cluster s’exécute en arrière-plan, et une notification s’affiche lorsque celle-ci est terminée. Vous pouvez alors voir le rapport de validation, comme décrit dans la section suivante.

> [!NOTE]
> Une fois que vos serveurs de cluster ont été validés, vous devez désactiver CredSSP pour des raisons de sécurité.

### <a name="disable-credssp"></a>Désactiver CredSSP
Une fois votre cluster de serveurs correctement validé, vous devez désactiver le protocole CredSSP sur chaque serveur pour des raisons de sécurité. Pour plus d’informations, consultez [CVE-2018-0886](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-0886).

1. Dans Windows Admin Center, sous **All connections** (Toutes les connexions), sélectionnez le premier serveur de votre cluster, puis sélectionnez **Connect** .
1. Dans la page **Overview** (Vue d’ensemble), sélectionnez **Disable CredSSP** (Désactiver CredSSP), puis, dans la fenêtre contextuelle **Disable CredSSP** , sélectionnez **Yes** .

    Le résultat de l’étape 2 supprime la bannière rouge **CredSSP ENABLED** située en haut de la page **Overview** du serveur, et désactive CredSSP sur les autres serveurs.

### <a name="view-validation-reports"></a>Afficher le rapport de validation
Vous êtes maintenant prêt à afficher le rapport de validation de votre cluster.

Il existe plusieurs façons d’accéder aux rapports de validation :
- Dans la page **Inventory** (Inventaire), développez le sous-menu **More** , puis sélectionnez **View validation reports** (Afficher les rapports de validation).


- En haut à droite de **Windows Admin Center** , sélectionnez l’icône des **Notifications** représentant une cloche afin d’afficher le volet **Notifications** .
Sélectionnez la notification **Successfully validated cluster** (Cluster validé), puis sélectionnez **Go to Failover Cluster validation report** (Accéder au rapport de validation du cluster de basculement).

> [!NOTE]
> Le processus de validation du cluster de serveurs peut prendre un certain temps. Ne passez pas à un autre outil dans Windows Admin Center pendant l’exécution du processus. Dans le volet **Notifications** , une barre d’état située sous la notification **Validate cluster** (valider le cluster) indique quand le processus est terminé.

## <a name="validate-the-cluster-using-powershell"></a>Valider le cluster à l’aide de PowerShell
Vous pouvez également utiliser Windows PowerShell pour exécuter des tests de validation sur votre cluster de serveurs et afficher les résultats. Vous pouvez exécuter des tests avant et après avoir configuré un cluster.

Pour exécuter un test de validation sur un cluster de serveurs, exécutez les applets de commande PowerShell **Get-Cluster** et **Test-Cluster** <server clustername> à partir de votre PC de gestion, ou exécutez uniquement l’applet de commande **Test-Cluster** directement sur le cluster :

```PowerShell
$Cluster = Get-Cluster -Name 'server-cluster1'
Test-Cluster -InputObject $Cluster -Verbose
```

Pour obtenir des exemples et plus d’informations, consultez la documentation de référence sur [Test-Cluster](/powershell/module/failoverclusters/test-cluster?view=win10-ps).

## <a name="validate-replication-for-storage-replica"></a>Valider la réplication pour un réplica de stockage
Si vous utilisez un réplica de stockage pour répliquer des volumes dans un cluster étendu ou d’un cluster à un autre, plusieurs événements et applets de commande peuvent vous permettre d’obtenir l’état de réplication. 

Dans le scénario suivant, nous avons configuré le réplica de stockage en créant des groupes de réplication (RG) pour deux sites, puis nous avons spécifié les volumes de données et les volumes de journaux pour les nœuds de serveur source de Site1 (Server1, Server2) et les nœuds de serveur de destination (répliqués) de Site2 (Server3, Server4).

Pour déterminer la progression de la réplication pour Server1 dans Site1, exécutez la commande WinEvent et examinez les événements 5015, 5002, 5004, 1237, 5001 et 2200 :

```powershell
Get-WinEvent -ComputerName Server1 -ProviderName Microsoft-Windows-StorageReplica -max 20
```

Pour Server3 dans Site2, exécutez la commande `Get-WinEvent` suivante pour afficher les événements du réplica de stockage qui indiquent la création du partenariat. Cet événement indique le nombre d’octets copiés, ainsi que la durée de l’opération de copie. Par exemple :

```powershell
Get-WinEvent -ComputerName Server3 -ProviderName Microsoft-Windows-StorageReplica | Where-Object {$_.ID -eq "1215"} | FL
```

Pour Server3 dans Site2, exécutez la commande `Get-WinEvent`, puis examinez les événements 5009, 1237, 5001, 5015, 5005 et 2200 pour voir la progression du traitement. Aucun avertissement ni aucune erreur ne doivent s’afficher durant cette séquence. En revanche, il y aura de nombreux événements 1237 , car ils indiquent la progression.

```powershell
Get-WinEvent -ComputerName Server3 -ProviderName Microsoft-Windows-StorageReplica | FL
```

Sinon, vous pouvez interroger à tout moment le groupe de serveurs de destination du réplica pour connaître le nombre d’octets restants à copier, en utilisant la commande PowerShell `Get-SRGroup`. Par exemple :

```powershell
(Get-SRGroup).Replicas | Select-Object numofbytesremaining
```

Pour le nœud Server3 dans Site2, exécutez la commande suivante, puis examinez les événements 5009, 1237, 5001, 5015, 5005 et 2200 pour voir la progression de la réplication. Aucun avertissement ni aucune erreur ne doivent s’afficher. Toutefois, il y aura de nombreux événements « 1237 », car ceux-ci indiquent la progression.

```powershell
Get-WinEvent -ComputerName Server3 -ProviderName Microsoft-Windows-StorageReplica | FL
```

Script de progression qui ne se termine pas :

```powershell
while($true) {
$v = (Get-SRGroup -Name "Replication2").replicas | Select-Object numofbytesremaining
[System.Console]::Write("Number of bytes remaining: {0}`r", $v.numofbytesremaining)
Start-Sleep -s 5
}
```

Pour accéder à l’état de réplication au sein du cluster étendu, utilisez `Get-SRGroup` et `Get-SRPartnership` :

```powershell
Get-SRGroup -Cluster ClusterS1
```

```powershell
Get-SRPartnership -Cluster ClusterS1
```

```powershell
(Get-SRGroup).replicas -Cluster ClusterS1
```

Une fois que la réplication des données entre les sites a été confirmée, vous pouvez créer vos machines virtuelles, ainsi que d’autres charges de travail.

## <a name="see-also"></a>Voir aussi
- Test de performances effectués sur les charges de travail synthétiques dans un espace de stockage nouvellement créé à l’aide de DiskSpd.exe. Pour plus d’informations, consultez [Test Storage Spaces Performance Using Synthetic Workloads in Windows Server](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn894707(v=ws.11)).
- L’évaluation de Windows Server est un service Premier qui est mis à disposition des clients souhaitant que Microsoft révise leurs installations de Windows Server 2019. Pour plus d’informations, contactez le Support Premier. Pour plus d’informations, consultez [Prise en main de l’évaluation à la demande de Windows Server (Serveur, Sécurité, Hyper-V, cluster de basculement, IIS)](/services-hub/health/getting-started-windows-server).
