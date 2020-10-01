---
title: Mettre à jour les clusters Azure Stack HCI
description: Comment appliquer les mises à jour de système d’exploitation et de microprogramme sur Azure Stack HCI à l’aide de Windows Admin Center et de PowerShell.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 08/31/2020
ms.openlocfilehash: 06a5a1ccf59b5d5c34ef1d2e36feeb1000b49776
ms.sourcegitcommit: 69cfff119ab425d0fbb71e38d1480d051fc91216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91572634"
---
# <a name="update-azure-stack-hci-clusters"></a>Mettre à jour les clusters Azure Stack HCI

> S’applique à : Azure Stack HCI, version 20H2 ; Windows Server 2019

Lors d’une mise à jour de clusters Azure Stack HCI, l’objectif poursuivi est de maintenir la disponibilité en ne mettant à jour qu’un seul serveur du cluster à la fois. De nombreuses mises à jour de système d’exploitation nécessitent la mise hors connexion du serveur, par exemple pour effectuer un redémarrage ou mettre à jour un logiciel tel que la pile réseau. Nous vous conseillons d’utiliser la [Mise à jour adaptée aux clusters (CAU)](/windows-server/failover-clustering/cluster-aware-updating), une fonctionnalité qui facilite l’installation des mises à jour Windows sur chaque serveur de votre cluster tout en conservant l’exécution de vos applications par l’automatisation du processus de mise à jour logicielle. La mise à jour adaptée aux clusters peut être utilisée sur toutes les éditions de Windows Server, y compris les installations Server Core ; elle peut être lancée via Windows Admin Center ou au moyen de PowerShell.

## <a name="update-a-cluster-using-windows-admin-center"></a>Mettre à jour un cluster à l’aide de Windows Admin Center

Windows Admin Center facilite la mise à jour d’un cluster, et l’application des mises à jour du système d’exploitation et de la solution à l’aide d’une interface utilisateur simple. Si vous avez acheté un système intégré auprès d’un partenaire matériel Microsoft, il est facile d’obtenir les derniers pilotes, microprogrammes et autres mises à jour, directement à partir de Windows Admin Center, en installant les extensions de mise à jour de partenaire appropriées. Si votre matériel n’a pas été acheté comme système intégré, des mises à jour de microprogramme et de pilote devront éventuellement être effectuées séparément, conformément aux recommandations du fabricant du matériel.

Windows Admin Center vérifiera si le cluster est correctement configuré pour exécuter la Mise à jour adaptée aux clusters et, si nécessaire, vous demandera si vous souhaitez que Windows Admin Center configure cette mise à jour pour vous, avec notamment l'installation du rôle de cluster Mise à jour adaptée aux clusters et l'activation des règles de pare-feu requises.

1. Quand vous vous connectez à un cluster, le tableau de bord de Windows Admin Center vous avertit si un ou plusieurs serveurs disposent de mises à jour prêtes à être installées, et vous fournit un lien pour procéder à la mise à jour immédiatement. Vous pouvez également sélectionner **Mises à jour** dans le menu **Outils** sur la gauche.
1. Pour utiliser l’outil de mise à jour adaptée aux clusters dans Windows Admin Center, vous devez activer CredSSP (Credential Security Service Provider) et fournir des informations d’identification explicites. Quand vous êtes invité à activer CredSSP, cliquez sur **Oui**.
1. Indiquez votre nom d’utilisateur et votre mot de passe, puis cliquez sur **Continuer**.
1. Toutes les mises à jour disponibles seront affichées ; cliquez sur **Vérifier les mises à jour disponibles** pour actualiser la liste.
1. Sélectionnez les mises à jour que vous souhaitez installer, puis cliquez sur **Appliquer toutes les mises à jour**. Cette opération installe les mises à jour sur chaque serveur du cluster. Si un redémarrage est nécessaire, les rôles de cluster, tels que les machines virtuelles, seront d’abord déplacés sur un autre serveur pour éviter toute interruption.
1. Pour améliorer la sécurité, désactivez CredSSP dès que vous avez terminé l’installation des mises à jour :
    - Dans Windows Admin Center, sous **Toutes les connexions**, sélectionnez le premier serveur de votre cluster, puis sélectionnez **Se connecter**.
    - Sur la page **Vue d’ensemble**, sélectionnez **Désactiver CredSSP**, puis, dans la fenêtre contextuelle de **désactivation de CredSSP**, sélectionnez **Oui**.

## <a name="update-a-cluster-using-powershell"></a>Mettre à jour un cluster à l’aide de PowerShell

Avant de pouvoir mettre à jour un cluster avec la mise à jour adaptée aux clusters, vous devez d’abord installer les **Outils de clustering avec basculement** qui font partie des **Outils d’administration de serveur distant (RSAT)** et incluent le logiciel de mise à jour adaptée aux clusters. Si vous mettez à jour un cluster existant, ces outils sont peut-être déjà installés.

Pour déterminer si un cluster de basculement est correctement configuré pour appliquer des mises à jour logicielles au moyen de la mise à jour adaptée aux clusters, exécutez l’applet de commande PowerShell **Test-CauSetup** ; elle effectue une analyse Best Practices Analyzer (BPA) du cluster de basculement et de l’environnement réseau, et vous alerte en cas d’avertissement ou d’erreur :

```PowerShell
Test-CauSetup -ClusterName Cluster1
```

Si vous devez installer des fonctionnalités, des outils ou des rôles, consultez les sections suivantes. Sinon, passez directement à [Rechercher des mises à jour avec PowerShell](#check-for-updates-with-powershell).

### <a name="install-failover-clustering-and-failover-clustering-tools-using-powershell"></a>Installer le clustering de basculement et les outils de clustering avec basculement au moyen de PowerShell

Pour vérifier si la fonctionnalité de clustering de basculement et les outils de clustering avec basculement sont déjà installés sur un cluster ou un serveur, exécutez l’applet de commande PowerShell **`Get-WindowsFeature`** à partir de votre PC de gestion (ou lancez-la directement sur le cluster ou le serveur, en omettant le paramètre -ComputerName) :

```PowerShell
Get-WindowsFeature -Name Failover*, RSAT-Clustering* -ComputerName Server1
```

Vérifiez que « Install State » (État de l’installation) indique Installed (Installé) et qu’une croix (X) apparaît en regard de Failover Clustering (Clustering de basculement) et de Failover Cluster Module for Windows PowerShell (Module de cluster de basculement pour Windows PowerShell) :

```
Display Name                                            Name                       Install State
------------                                            ----                       -------------
[X] Failover Clustering                                 Failover-Clustering            Installed
        [X] Failover Clustering Tools                   RSAT-Clustering                Installed
            [X] Failover Cluster Module for Windows ... RSAT-Clustering-Powe...        Installed
            [ ] Failover Cluster Automation Server      RSAT-Clustering-Auto...        Available
            [ ] Failover Cluster Command Interface      RSAT-Clustering-CmdI...        Available
```

Si la fonctionnalité de clustering de basculement n’est pas installée, installez-la sur chaque serveur du cluster avec l’applet de commande **`Install-WindowsFeature`** et en utilisant les paramètres -IncludeAllSubFeature et-IncludeManagementTools :

```PowerShell
Install-WindowsFeature –Name Failover-Clustering -IncludeAllSubFeature –IncludeManagementTools -ComputerName Server1
```

Cette commande installe également le module de cluster de basculement pour PowerShell ; il comprend des applets de commande PowerShell pour la gestion des clusters de basculement, et le module de mise à jour adaptée aux clusters pour PowerShell, qui permet l’installation des mises à jour logicielles sur les clusters de basculement.

Si la fonctionnalité de clustering de basculement est déjà installée, mais que le module de cluster de basculement pour Windows PowerShell ne l’est pas, il vous suffit de l’installer sur chaque serveur du cluster avec l’applet de commande **Install-WindowsFeature** :

```PowerShell
Install-WindowsFeature –Name RSAT-Clustering-PowerShell -ComputerName Server1
```

### <a name="choose-an-updating-mode"></a>Choisir un mode de mise à jour

L’outil de mise à jour adaptée aux clusters peut coordonner l’intégralité de l’opération de mise à jour des clusters selon deux modes distincts :  
  
-   **Mode de mise à jour automatique** Pour ce mode, le rôle en cluster Mise à jour adaptée aux clusters est configuré sous la forme d’une charge de travail sur le cluster de basculement qui doit être mis à jour, et une planification de mise à jour associée est définie. Le cluster effectue ses propres mises à jour aux moments planifiés, en utilisant un profil d’exécution de mise à jour par défaut ou personnalisé. Lors de l’exécution de mise à jour, le coordinateur de la mise à jour adaptée aux clusters commence par mettre à jour le nœud qui détient à ce moment-là le rôle en cluster Mise à jour adaptée aux clusters, puis il met à jour les nœuds suivants du cluster, à tour de rôle. Pour mettre à jour le nœud de cluster en cours, le rôle en cluster Mise à jour adaptée aux clusters bascule sur un autre nœud de cluster, et un nouveau coordinateur de mise à jour sur ce nœud prend le contrôle de l’exécution de mise à jour. En mode de mise à jour automatique, la mise à jour adaptée aux clusters peut mettre à jour le cluster de basculement à l’aide d’un processus de mise à jour de bout en bout entièrement automatisé. Un administrateur peut également déclencher des mises à jour à la demande dans ce mode, ou simplement utiliser l’approche de mise à jour à distance, si vous le souhaitez.
  
-   **Mode de mise à jour à distance** Pour ce mode, un ordinateur de gestion à distance (généralement un PC Windows 10) disposant d’une connectivité réseau au cluster de basculement, mais qui n’est pas membre du cluster de basculement, est configuré avec les Outils de clustering avec basculement. À partir de l’ordinateur de gestion à distance, appelé « coordinateur de mise à jour », l’administrateur déclenche une exécution de mise à jour à la demande, en utilisant un profil d’exécution de mise à jour par défaut ou personnalisé. Le mode de mise à jour à distance est utile pour superviser en temps réel la progression lors de l’exécution de mise à jour, et pour les clusters qui s’exécutent sur des installations Server Core.  

   > [!NOTE]
   > À compter de la mise à jour d’octobre 2018 de Windows 10, la fonctionnalité Outils d’administration de serveur distant est incluse en tant qu’ensemble de « Fonctionnalités à la demande » directement dans Windows 10. Accédez simplement à **Paramètres > Applications > Applications et fonctionnalités > Fonctionnalités facultatives > Ajouter une fonctionnalité > Outils d’administration de serveur distant : Outils de clustering avec basculement**, puis sélectionnez **Installer**. Pour voir la progression de l’installation, cliquez sur le bouton Précédent pour en afficher l’état dans la page « Gérer les fonctionnalités facultatives ». La fonctionnalité installée est conservée à l’issue des mises à niveau des versions de Windows 10. Si vous voulez installer les Outils d’administration de serveur distant pour Windows 10 avant la mise à jour d’octobre 2018, [téléchargez un package Outils d’administration de serveur distant](https://www.microsoft.com/download/details.aspx?id=45520).

### <a name="add-cau-cluster-role-to-the-cluster"></a>Ajouter au cluster le rôle de cluster Mise à jour adaptée aux clusters

Le rôle de cluster Mise à jour adaptée aux clusters est obligatoire pour le mode de mise à jour automatique. Si vous utilisez Windows Admin Center pour effectuer les mises à jour, le rôle de cluster est automatiquement ajouté.

L’applet de commande **`Get-CauClusterRole`** affiche les propriétés de configuration du rôle de cluster Mise à jour adaptée aux clusters sur le cluster spécifié.

```PowerShell
Get-CauClusterRole -ClusterName Cluster1
```

Si le rôle n’est pas encore configuré sur le cluster, le message d’erreur suivant s’affiche :

```Get-CauClusterRole : The current cluster is not configured with a Cluster-Aware Updating clustered role.```

Si vous voulez ajouter le rôle de cluster Mise à jour adaptée aux clusters pour le mode de mise à jour automatique à l’aide de PowerShell, utilisez l’applet de commande **`Add-CauClusterRole`** et fournissez les [paramètres](/powershell/module/clusterawareupdating/add-cauclusterrole#parameters) appropriés, comme dans l’exemple suivant :

```PowerShell
Add-CauClusterRole -ClusterName Cluster1 -MaxFailedNodes 0 -RequireAllNodesOnline -EnableFirewallRules -VirtualComputerObjectName Cluster1-CAU -Force -CauPluginName Microsoft.WindowsUpdatePlugin -MaxRetriesPerNode 3 -CauPluginArguments @{ 'IncludeRecommendedUpdates' = 'False' } -StartDate "3/2/2020 3:00:00 AM" -DaysOfWeek 4 -WeeksOfMonth @(3) -verbose
```

   > [!NOTE]
   > La commande ci-dessus doit être exécutée à partir d’un PC de gestion ou d’un contrôleur de domaine.

### <a name="enable-firewall-rules-to-allow-remote-restarts"></a>Activer les règles de pare-feu pour autoriser les redémarrages à distance

Vous devez autoriser les serveurs à redémarrer à distance pendant le processus de mise à jour. Si vous utilisez Windows Admin Center pour effectuer les mises à jour, les règles de Pare-feu Windows seront automatiquement mises à jour sur chaque serveur pour autoriser les redémarrages à distance. Si vous effectuez une mise à jour avec PowerShell, activez le groupe de règles de pare-feu d’arrêt à distance dans le Pare-feu Windows, ou passez le paramètre -EnableFirewallRules à l’applet de commande, comme dans l’exemple ci-dessus.

## <a name="check-for-updates-with-powershell"></a>Rechercher des mises à jour avec PowerShell

Vous pouvez utiliser l’applet de commande **`Invoke-CAUScan`** pour rechercher les mises à jour applicables sur les serveurs, et obtenir une liste de l’ensemble initial de mises à jour appliquées à chaque serveur dans un cluster spécifié :

```PowerShell
Invoke-CauScan -ClusterName Cluster1 -CauPluginName Microsoft.WindowsUpdatePlugin -Verbose
```

La génération de cette liste peut prendre plusieurs minutes. L’aperçu de la liste comporte uniquement l’ensemble initial des mises à jour ; il n’inclut pas les mises à jour pouvant devenir applicables après l’installation des premières mises à jour.

## <a name="install-updates-with-powershell"></a>Installer les mises à jour avec PowerShell

Pour rechercher les mises à jour applicables sur les serveurs et procéder à une exécution de mise à jour complète sur le cluster spécifié, utilisez l’applet de commande **`Invoke-CAURun`**  :

```PowerShell
Invoke-CauRun -ClusterName Cluster1 -CauPluginName Microsoft.WindowsUpdatePlugin -MaxFailedNodes 1 -MaxRetriesPerNode 3 -RequireAllNodesOnline -EnableFirewallRules -Force
```

Cette commande effectue une analyse et une exécution de mise à jour complète sur le cluster nommé Cluster1. Cette applet de commande utilise le plug-in Microsoft.WindowsUpdatePlugin et nécessite que tous les nœuds de cluster soient en ligne avant son exécution. Par ailleurs, l’applet de commande n’autorise pas plus de trois tentatives par nœud avant de marquer le nœud en état d’échec, et elle n’autorise qu’un seul nœud à échouer avant de marquer la totalité de l’exécution de mise à jour comme ayant échoué. Elle permet également aux règles de pare-feu d’autoriser les serveurs à redémarrer à distance. Étant donné que la commande spécifie le paramètre Force, l’applet de commande s’exécute sans afficher d’invites de confirmation.

Le processus d’exécution de mise à jour comprend les opérations suivantes : 
- Recherche et téléchargement des mises à jour applicables sur chaque serveur du cluster
- Déplacement hors de chaque serveur des rôles en cluster en cours d’exécution
- Installation des mises à jour sur chaque serveur
- Redémarrage du serveur si les mises à jour installées le nécessitent
- Réintégration des rôles en cluster sur le serveur d’origine

Le processus d’exécution de mise à jour comprend également l’assurance que le quorum est géré, par la recherche de mises à jour supplémentaires ne pouvant être installées qu’après l’installation initiale des mises à jour, et par l’enregistrement d’un rapport des actions effectuées.

## <a name="check-on-the-status-of-an-updating-run"></a>Vérifier l’état d’une exécution de mise à jour

Un administrateur peut obtenir des informations récapitulatives sur une exécution de mise à jour en cours en exécutant l’applet de commande **`Get-CauRun`**  :

```PowerShell
Get-CauRun -ClusterName Cluster1
```

Voici un exemple de sortie :

```
RunId                   : 834dd11e-584b-41f2-8d22-4c9c0471dbad 
RunStartTime            : 10/13/2019 1:35:39 PM 
CurrentOrchestrator     : NODE1 
NodeStatusNotifications : { 
Node      : NODE1 
Status    : Waiting 
Timestamp : 10/13/2019 1:35:49 PM 
} 
NodeResults             : { 
Node                     : NODE2 
Status                   : Succeeded 
ErrorRecordData          : 
NumberOfSucceededUpdates : 0 
NumberOfFailedUpdates    : 0 
InstallResults           : Microsoft.ClusterAwareUpdating.UpdateInstallResult[] 
}
```

## <a name="next-steps"></a>Étapes suivantes

Pour consulter des informations connexes, reportez-vous également à :

- [Mise à jour du microprogramme de lecteur dans les espaces de stockage direct](/windows-server/storage/update-firmware)
- [Valider un cluster Azure Stack HCI](../deploy/validate.md)
