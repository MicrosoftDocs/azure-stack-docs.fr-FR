---
title: Mise à jour 1902 d’Azure Stack | Microsoft Docs
description: 'Découvrez la mise à jour 1902 pour les systèmes intégrés Azure Stack : nouveautés, problèmes connus et emplacement de téléchargement.'
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 3a59470075e7903f354646be5c6ddb9fb0e5b301
ms.sourcegitcommit: 426380a3a27954cd609ba52d1066d9d69f5267fe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65532342"
---
# <a name="azure-stack-1902-update"></a>Mise à jour 1902 d’Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Cet article décrit le contenu de la mise à jour 1902. La mise à jour inclut des améliorations, des corrections de bogues et de nouvelles fonctionnalités pour cette version d’Azure Stack. Cet article décrit également les problèmes connus dans cette version, et contient un lien permettant de télécharger la mise à jour. Les problèmes connus sont divisés en problèmes directement liés au processus de mise à jour et problèmes propres à la build (après installation).

> [!IMPORTANT]  
> Cette mise à jour est destinée uniquement aux systèmes intégrés d’Azure Stack. N’appliquez pas cette mise à jour au Kit de développement Azure Stack.

## <a name="build-reference"></a>Référence de build

Le numéro de build de la mise à jour 1902 d’Azure Stack est **1.1902.0.69**.

## <a name="hotfixes"></a>Correctifs logiciels

Azure Stack publie des correctifs logiciels à intervalles réguliers. Avant d’installer la mise à jour 1902 d’Azure Stack, veillez à installer le [dernier correctif logiciel d’Azure Stack](#azure-stack-hotfixes) pour la build 1901.

Les correctifs logiciels Azure Stack sont uniquement applicables aux systèmes intégrés Azure Stack. N’essayez pas d’installer des correctifs logiciels sur l’ASDK.

> [!TIP]  
> Pour rester informé des publications des correctifs logiciels d’Azure Stack, abonnez-vous aux flux *RSS* ou *Atom* suivants :
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Correctifs logiciels d’Azure Stack

- **1901** : [KB 4500636 – Correctif logiciel d’Azure Stack 1.1901.5.109](https://support.microsoft.com/help/4500636)
- **1902** : [KB 4500637 – Correctif logiciel d’Azure Stack 1.1902.3.75](https://support.microsoft.com/help/4500637)

## <a name="prerequisites"></a>Prérequis

> [!IMPORTANT]
> Vous pouvez installer la mise à jour 1902 directement à partir de la version [1.1901.0.95 ou 1.1901.0.99](azure-stack-update-1901.md#build-reference), sans installer au préalable un correctif logiciel 1901. Mais si vous avez installé l’ancien correctif logiciel **1901.2.103**, vous devez installer le nouveau [correctif logiciel 1901.3.105](https://support.microsoft.com/help/4495662) avant de passer à la mise à jour 1902.

- Avant de démarrer l’installation de cette mise à jour, exécutez [Test-AzureStack](azure-stack-diagnostic-test.md) avec les paramètres suivants pour valider l’état de votre Azure Stack et résoudre les éventuels problèmes opérationnels détectés, y compris tous les avertissements et les échecs. Examinez aussi les alertes actives et résolvez toutes celles qui nécessitent une action :

    ```powershell
    Test-AzureStack -Include AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

  Si le paramètre `AzsControlPlane` est inclus lors de l’exécution de **Test-AzureStack**, l’erreur suivante apparaît dans la sortie **Test-AzureStack** : **FAIL Azure Stack Control Plane Websites Summary**. Vous pouvez ignorer sans problème cette erreur spécifique.

- Lorsqu’Azure Stack est géré par System Center Operations Manager (SCOM), veillez à mettre à jour le [Pack d’administration pour Microsoft Azure Stack](https://www.microsoft.com/download/details.aspx?id=55184) vers la version 1.0.3.11 avant d’appliquer la version 1902.

- Le format de package pour la mise à jour d’Azure Stack est passé de **.bin/.exe/.xml** à **.zip/.xml** depuis la version 1902. Les clients avec des unités d’échelle Azure Stack connectées verront le message **Mise à jour disponible** dans le portail. Les clients qui ne sont pas connectés peuvent désormais simplement télécharger et importer le fichier .zip qui contient le fichier .xml correspondant.

<!-- ## New features -->

<!-- ## Fixed issues -->

## <a name="improvements"></a>Améliorations

- La build 1902 introduit une nouvelle interface utilisateur sur le portail Administrateur Azure Stack pour la création de plans, d’offres, de quotas et de plans complémentaires. Pour plus d’informations, y compris des captures d’écran, consultez [Créer des plans, des offres et des quotas](azure-stack-create-plan.md).

<!-- 1460884    Hotfix: Adding StorageController service permission to talk to ClusterOrchestrator  Add node -->
- Améliorations de la fiabilité de l’extension de capacité pendant une opération d’ajout de nœuds lors du passage de l’état de l’unité d’échelle de « Développement du stockage » à « Exécution ».

<!--
1426197 3852583: Increase Global VM script mutex wait time to accommodate enclosed operation timeout    PNU
1399240 3322580: [PNU] Optimize the DSC resource execution on the Host  PNU
1398846 Bug 3751038: ECEClient.psm1 should provide cmdlet to resume action plan instance    PNU
1398818 3685138, 3734779: ECE exception logging, VirtualMachine ConfigurePending should take node name from execution context   PNU
1381018 [1902] 3610787 - Infra VM creation should fail if the ClusterGroup already exists   PNU
-->
- Pour améliorer l’intégrité et la sécurité du package et faciliter la gestion pour l’ingestion de données hors connexion, Microsoft a modifié le format du package de mise à jour en transformant les fichiers .exe et .bin en un fichier .zip. Le nouveau augmente encore la fiabilité du processus de décompression qui, dans certains cas, peut bloquer la préparation de la mise à jour. Le même format de package s’applique également aux packages de mise à jour de votre OEM.
- Pour améliorer l’expérience des opérateurs Azure Stack lors de l’exécution de la commande Test-AzureStack, les opérateurs peuvent maintenant simplement utiliser « Test-AzureStack -Group UpdateReadiness » au lieu de passer dix paramètres supplémentaires après une instruction Include.

  ```powershell
    Test-AzureStack -Group UpdateReadiness  
  ```  
  
- Afin d’améliorer la fiabilité et la disponibilité globales des principaux services d’infrastructure pendant le processus de mise à jour, le fournisseur de ressources de mise à jour natif, en tant que partie prenante du plan d’action de mise à jour, détecte et appelle des corrections globales automatiques si nécessaire. Le flux de travail de correction globale « Réparer » inclut les opérations suivantes :

  - Rechercher les machines virtuelles de l’infrastructure dont l’état n’est pas optimal et tenter de les réparer si nécessaire.
  - Rechercher les problèmes de service SQL dans le cadre du plan de contrôle et tenter d’y remédier si nécessaire.
  - Vérifier l’état du service d’équilibrage de charge logicielle (SLB) dans le contrôleur de réseau (NC) et tenter de le réparer si nécessaire.
  - Vérifier l’état du service de contrôleur de réseau (NC) et tenter de le réparer si nécessaire
  - Vérifier l’état des nœuds de la structure du service de console de récupération d’urgence (ERCS) et les réparer si nécessaire.
  - Vérifier l’état du rôle d’infrastructure et le réparer si nécessaire.
  - Vérifier l’état des nœuds de la structure du service de stockage cohérent Azure (ACS) et les réparer si nécessaire.

<!-- 
1426690 [SOLNET] 3895478-Get-AzureStackLog_Output got terminated in the middle of network log   Diagnostics
1396607 3796092: Move Blob services log from Storage role to ACSBlob role to reduce the log size of Storage Diagnostics
1404529 3835749: Enable Group Policy Diagnostic Logs    Diagnostics
1436561 Bug 3949187: [Bug Fix] Remove AzsStorageSvcsSummary test from SecretRotationReadiness Test-AzureStack flag  Diagnostics
1404512 3849946: Get-AzureStackLog should collect all child folders from c:\Windows\Debug   Diagnostics 
-->
- Améliorations des outils de diagnostic de la pile Azure pour augmenter la fiabilité et les performances de la collection de journaux. Une journalisation supplémentaire pour les services de mise en réseau et d’identité. 

<!-- 1384958    Adding a Test-AzureStack group for Secret Rotation  Diagnostics -->
- Améliorations de la fiabilité de Test-AzureStack pour le test de préparation à la rotation des secrets.

<!-- 1404751    3617292: Graph: Remove dependency on ADWS.  Identity -->
- Améliorations visant à accroître la fiabilité d’AD Graph lors de la communication avec l’environnement Active Directory du client

<!-- 1391444    [ISE] Telemetry for Hardware Inventory - Fill gap for hardware inventory info   System info -->
- Améliorations de la collection d’inventaire matériel dans Get-AzureStackStampInformation.

- Pour améliorer la fiabilité des opérations en cours d’exécution sur l’infrastructure ERCS, la mémoire pour chaque instance ERCS passe de 8 Go à 12 Go. Sur une installation de systèmes intégrés Azure Stack, cela entraîne une augmentation globale de 12 Go.

<!-- 110303935 IcM Reported by HKEX -->
- La version 1902 résout un problème rencontré dans le service Network Controllers VSwitch, où toutes les machines virtuelles d’un nœud spécifique basculaient hors connexion.  À cause de ce problème, le service était bloqué dans un état de perte du composant principal, où le composant principal ne pouvait pas être contacté, mais où le rôle n’avait pas été basculé vers une autre instance saine et où la seule solution était de contacter les services de support Microsoft.

> [!IMPORTANT]
> Pour vous assurer que le processus de correctif et de mise à jour entraîne le moins de temps d’arrêt possible du client, vérifiez que votre tampon Azure Stack a plus de 12 Go d’espace disponible dans le panneau **Capacité**. Vous pouvez voir cette augmentation de mémoire reflétée dans le panneau **Capacité** panneau après une installation réussie de la mise à jour.

## <a name="common-vulnerabilities-and-exposures"></a>Vulnérabilités et menaces courantes

Cette mise à jour installe les mises à jour de sécurité suivantes :  
- [ADV190005](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190006)
- [CVE-2019-0595](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0595)
- [CVE-2019-0596](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0596)
- [CVE-2019-0597](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0597)
- [CVE-2019-0598](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0598)
- [CVE-2019-0599](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0599)
- [CVE-2019-0600](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0600)
- [CVE-2019-0601](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0601)
- [CVE-2019-0602](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0602)
- [CVE-2019-0615](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0615)
- [CVE-2019-0616](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0616)
- [CVE-2019-0618](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0618)
- [CVE-2019-0619](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0619)
- [CVE-2019-0621](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0621)
- [CVE-2019-0623](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0623)
- [CVE-2019-0625](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0625)
- [CVE-2019-0626](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0626)
- [CVE-2019-0627](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0627)
- [CVE-2019-0628](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0628)
- [CVE-2019-0630](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0630)
- [CVE-2019-0631](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0631)
- [CVE-2019-0632](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0632)
- [CVE-2019-0633](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0633)
- [CVE-2019-0635](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0635)
- [CVE-2019-0636](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0636)
- [CVE-2019-0656](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0656)
- [CVE-2019-0659](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0659)
- [CVE-2019-0660](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0660)
- [CVE-2019-0662](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0662)
- [CVE-2019-0663](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0663)

Pour plus d’informations sur ces vulnérabilités, cliquez sur les liens précédents ou consultez l’article [4487006](https://support.microsoft.com/en-us/help/4487006) de la Base de connaissances Microsoft.

## <a name="known-issues-with-the-update-process"></a>Problèmes connus avec le processus de mise à jour

- Lorsque vous tentez d’installer une mise à jour Azure Stack, l’état de la mise à jour peut échouer et définir l’état sur **PreparationFailed**. Cela est dû au fait que le fournisseur de ressources de mise à jour est dans l’impossibilité de transférer correctement les fichiers du conteneur de stockage vers un partage d’infrastructure interne à des fins de traitement. À compter de la version 1901 (1.1901.0.95), vous pouvez contourner ce problème en cliquant sur **Mettre à jour maintenant** à nouveau (et pas sur **Reprendre**). Le fournisseur de ressources de mise à jour nettoie les fichiers de la tentative précédente, puis redémarre le téléchargement.

- Quand vous exécutez la commande [Test-AzureStack](azure-stack-diagnostic-test.md), un message d’avertissement du contrôleur de gestion de la carte de base (BMC) s’affiche. Vous pouvez ignorer cet avertissement sans problème.

- <!-- 2468613 - IS --> Pendant l’installation de cette mise à jour, il se peut que des alertes intitulées `Error - Template for FaultType UserAccounts.New is missing.` s’affichent. Vous pouvez les ignorer. Elles disparaîtront automatiquement une fois l’installation de cette mise à jour terminée.

## <a name="post-update-steps"></a>Étapes après la mise à jour

- Après l’installation de cette mise à jour, installez les correctifs logiciels applicables. Pour plus d’informations, voir [Correctifs logiciels](#hotfixes) ainsi que notre [Stratégie de maintenance](azure-stack-servicing-policy.md).  

- Récupérez les clés de chiffrement de données au repos et stockez-les en toute sécurité hors de votre déploiement Azure Stack. Suivez les [instructions sur la façon de récupérer les clés](azure-stack-security-bitlocker.md).

## <a name="known-issues-post-installation"></a>Problèmes connus (après l’installation)

Les éléments suivants sont des problèmes connus qui apparaissent après l’installation de cette build.

### <a name="portal"></a>Portail

<!-- 2930820 - IS ASDK --> 
- Dans les portails d’administration et utilisateur, si vous recherchez « Docker », l’élément est incorrectement retourné dans les résultats. Il n’est pas disponible dans Azure Stack. Si vous essayez de le créer, un panneau indiquant une erreur s’affiche. 

<!-- 2931230 - IS  ASDK --> 
- Les plans ajoutés à un abonnement utilisateur comme plan d’extension ne peuvent pas être supprimés, même quand vous supprimez le plan de l’abonnement utilisateur. Le plan est conservé jusqu’à ce que les abonnements qui référencent le plan d’extension soient aussi supprimés. 

<!-- TBD - IS ASDK --> 
- Les deux types d’abonnements d’administration qui ont été introduits avec la version 1804 ne doivent pas être utilisés. Les types d’abonnements sont **Abonnement de contrôle** et **Abonnement de consommation**. Ces types d’abonnements sont visibles dans les nouveaux environnements Azure Stack depuis la version 1804, mais ils ne sont pas encore prêts à être utilisés. Vous devez continuer à utiliser le type d’abonnement **Fournisseur par défaut**.

<!-- 3557860 - IS ASDK --> 
- La suppression d’abonnements utilisateur aboutit à des ressources orphelines. Pour contourner ce problème, commencez pas supprimer des ressources d’utilisateurs ou la totalité du groupe de ressources, puis supprimez les abonnements utilisateur.

<!-- 1663805 - IS ASDK --> 
- Vous ne pouvez pas afficher les autorisations définies pour votre abonnement à l’aide des portails Azure Stack. Pour contourner ce problème, utilisez [PowerShell pour vérifier les autorisations](/powershell/module/azs.subscriptions.admin/get-azssubscriptionplan).

<!-- Daniel 3/28 -->
- Dans le portail de l’utilisateur, lorsque vous accédez à un objet blob au sein d’un compte de stockage et essayez d’ouvrir la rubrique **Stratégie d'accès** dans l’arborescence de navigation, la fenêtre suivante ne s’ouvre pas. Pour contourner ce problème, les applets de commande PowerShell suivantes permettent de créer, récupérer, définir et supprimer des stratégies d’accès, respectivement :

  - [New-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/new-azurestoragecontainerstoredaccesspolicy)
  - [Get-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/get-azurestoragecontainerstoredaccesspolicy)
  - [Set-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/set-azurestoragecontainerstoredaccesspolicy)
  - [Remove-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/remove-azurestoragecontainerstoredaccesspolicy)

<!-- ### Health and monitoring -->

### <a name="compute"></a>Calcul

- Lors de la création d'une machine virtuelle Windows, l’erreur suivante peut s’afficher :

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   Cette erreur se produit si vous activez les diagnostics de démarrage sur une machine virtuelle alors que vous avez supprimé votre compte de stockage des diagnostics de démarrage. Pour contourner ce problème, recréez le compte de stockage avec le même nom que celui utilisé précédemment.

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- Quand vous créez un groupe de machines virtuelles identiques, l’option CentOS 7.2 est proposée pour le déploiement. Étant donné que cette image n’est pas disponible sur Azure Stack, sélectionnez un autre système d’exploitation pour votre déploiement, ou choisissez un modèle Azure Resource Manager spécifiant une autre image CentOS qui a été téléchargée par l’opérateur avant le déploiement à partir de la Place de marché.  

<!-- TBD - IS ASDK --> 
- Après avoir appliqué la mise à jour 1902, vous rencontrerez peut-être les problèmes suivants lors du déploiement de machines virtuelles avec la fonctionnalité Disques managés :

   - Si l’abonnement a été créé avant la mise à jour 1808, le déploiement d’une machine virtuelle avec la fonctionnalité Disques managés peut échouer avec un message d’erreur interne. Pour résoudre cette erreur, effectuez les étapes suivantes pour chaque abonnement :
      1. Dans le portail locataire, accédez à **Abonnements** et recherchez l’abonnement. Sélectionnez **Fournisseurs de ressources**, **Microsoft.Compute**, puis **Réinscrire**.
      2. Sous le même abonnement, accédez à **Contrôle d’accès (IAM)** et vérifiez que l’élément **Azure Stack – Managed Disks** est répertorié.
   - Si vous avez configuré un environnement multilocataire, le déploiement de machines virtuelles dans un abonnement associé à un annuaire invité peut échouer avec un message d’erreur interne. Pour résoudre le problème, effectuez les étapes décrites dans [cet article](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) afin de reconfigurer chacun de vos annuaires invités.

- Une machine virtuelle 18.04 Ubuntu créée avec une autorisation SSH activée ne vous permet pas d’utiliser les clés SSH pour vous connecter. Pour contourner ce problème, utilisez un accès à la machine virtuelle pour l’extension Linux afin d’implémenter des clés SSH après l’approvisionnement, ou utilisez une authentification par mot de passe.

- Vous ne pouvez pas supprimer un groupe identique à partir du panneau **Virtual Machine Scale Sets**. Pour résoudre ce problème, sélectionnez le groupe identique que vous souhaitez supprimer, puis cliquez sur le bouton **Supprimer** dans le volet **Vue d’ensemble**.

### <a name="networking"></a>Mise en réseau  

<!-- 3239127 - IS, ASDK -->
- Dans le portail Azure Stack, lorsque vous modifiez une adresse IP statique pour une configuration IP liée à une carte réseau connectée à une instance de machine virtuelle, le message d’avertissement suivant s’affiche : 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`.

    Vous pouvez ignorer ce message. L’adresse IP changera même si l’instance de machine virtuelle ne redémarre pas.

<!-- 3632798 - IS, ASDK -->
- Dans le portail, si vous ajoutez une règle de sécurité de trafic entrant et sélectionnez **Balise du service** en tant que source, plusieurs options s’affichent dans la liste **Balise source**, qui ne sont pas disponibles pour Azure Stack. Les seules options valides dans Azure Stack sont les suivantes :

  - **Internet**
  - **VirtualNetwork**
  - **AzureLoadBalancer**
  
  Les autres options ne sont pas pris en charge en tant que balises sources dans Azure Stack. De même, si vous ajoutez une règle de sécurité de trafic sortant et sélectionnez **Balise du service** en tant que destination, la même liste d’options s’affiche pour **Balise source**. Les seules options valides sont les mêmes que pour **Balise source**, comme décrit dans la liste précédente.

- Les Groupes de sécurité réseau (NSG) ne fonctionnent pas de la même manière dans Azure Stack que dans le reste d'Azure. Dans Azure, vous pouvez définir plusieurs ports sur une règle NSG (en utilisant les modèles du portail, de PowerShell et de Resource Manager). Dans Azure Stack, cependant, vous ne pouvez définir qu’un seul port sur une règle NSG via la portail. Pour contourner ce problème, utilisez un modèle Resource Manager ou PowerShell qui vous permettra de définir ces autres règles.

<!-- 3203799 - IS, ASDK -->
- Azure Stack ne permet pas d’attacher plus de quatre interfaces réseau à une instance de machine virtuelle, quelle que soit la taille de cette instance.

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- Avant de créer votre première fonction Azure dans l’abonnement, vous devez inscrire le fournisseur de ressources de stockage.


<!-- ### Usage -->

 
<!-- #### Identity -->
<!-- #### Marketplace -->

### <a name="syslog"></a>syslog 

- La configuration syslog n’est pas conservée lors d’un cycle de mise à jour et, par conséquent, le client perd sa configuration et les messages syslog ne sont plus transférés. Ce problème s’applique à toutes les versions d’Azure Stack depuis la disponibilité générale du client syslog (1809). Pour contourner ce problème, reconfigurez le client syslog après avoir appliqué une mise à jour Azure Stack.

## <a name="download-the-update"></a>Télécharger la mise à jour

Vous pouvez télécharger la mise à jour 1902 d’Azure Stack à partir de [cet emplacement](https://aka.ms/azurestackupdatedownload). 

Dans les scénarios connectés, et uniquement dans ceux-ci, les déploiements Azure Stack consultent régulièrement un point de terminaison sécurisé et vous avertissent automatiquement si une mise à jour est disponible pour votre cloud. Pour plus d’informations, voir la documentation sur la [gestion des mises à jour pour Azure Stack](azure-stack-updates.md#using-the-update-tile-to-manage-updates).

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble de la gestion des mises à jour dans Azure Stack, consultez [Gérer les mises à jour dans Azure Stack - Vue d’ensemble](azure-stack-updates.md).  
- Pour plus d’informations sur la façon d’appliquer des mises à jour avec Azure Stack, consultez [Effectuer des mises à jour dans Azure Stack](azure-stack-apply-updates.md).
- Pour passer en revue la stratégie de maintenance pour les systèmes intégrés Azure Stack et pour connaître la marche à suivre afin de conserver votre système dans un état de prise en charge, consultez [Stratégie de maintenance Azure Stack](azure-stack-servicing-policy.md).  
- Pour utiliser le Point de terminaison privilégié (PEP) afin de surveiller et de reprendre les mises à jour, consultez [Surveiller les mises à jour dans Azure Stack à l’aide du point de terminaison privilégié](azure-stack-monitor-update.md).  
