---
title: Mise à jour 1808 d’Azure Stack | Microsoft Docs
description: Découvrez les nouveautés de la mise à jour 1808 pour les systèmes intégrés Azure Stack, y compris les problèmes connus et l’emplacement de téléchargement de la mise à jour.
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
ms.date: 10/02/2018
ms.author: sethm
ms.reviewer: justini
ROBOTS: NOINDEX
ms.openlocfilehash: 3574c55568db86c96d22d43fdf1bb08ac79c061e
ms.sourcegitcommit: f9be5640dd445b3d926c9ce3e2165e96c72ece89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2021
ms.locfileid: "100009194"
---
# <a name="azure-stack-1808-update"></a>Mise à jour 1808 d’Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Cet article décrit le contenu de la mise à jour 1808. La mise à jour inclut des améliorations, des corrections et des problèmes connus de cette version d’Azure Stack. Cet article inclut également un lien de téléchargement de la mise à jour. Les problèmes connus sont divisés en problèmes directement liés au processus de mise à jour et en problèmes propres à la build (après l’installation).

> [!IMPORTANT]  
> Cette mise à jour est destinée uniquement aux systèmes intégrés d’Azure Stack. N’appliquez pas cette mise à jour au Kit de développement Azure Stack.

## <a name="build-reference"></a>Référence de build

Le numéro de build de la mise à jour 1808 d’Azure Stack est **1.1808.0.97**.  

### <a name="new-features"></a>Nouvelles fonctionnalités

Cette mise à jour inclut les améliorations suivantes pour Azure Stack.

<!--  2682594   | IS  --> 
- **Tous les environnements Azure Stack utilisent maintenant le format du fuseau horaire UTC (Temps Universel Coordonné).**  Toutes les données de journal et informations connexes sont donc affichées au format UTC. Si vous effectuez une mise à jour d’une version antérieure qui n’avait pas été installée avec le format UTC, votre environnement est mis à jour pour utiliser ce format. 

<!-- 2437250  | IS  ASDK --> 
- **Les disques managés sont pris en charge.** Vous pouvez maintenant utiliser des disques managés dans les machines virtuelles et les groupes de machines virtuelles identiques Azure Stack. Pour plus d’informations, consultez [Fonctionnalité Disques managés d’Azure Stack : différences et considérations](../../user/azure-stack-managed-disk-considerations.md).

<!-- 2563799  | IS  ASDK --> 
- **Azure Monitor**. Tout comme Azure Monitor sur Azure, Azure Monitor sur Azure Stack fournit des métriques et journaux d’activité de base à propos de l’infrastructure pour une majorité de services. Pour plus d’informations, consultez [Azure Monitor sur Azure Stack](../../user/azure-stack-metrics-azure-data.md).

<!-- 2487932| IS --> 
- **Préparer l’hôte d’extension**. Vous pouvez utiliser l’hôte d’extension pour sécuriser Azure Stack en réduisant le nombre de ports TCP/IP requis. Avec la mise à jour 1808, vous pouvez préparer l’hôte d’extension afin de l’utiliser dans votre système Azure Stack. Pour plus d’informations, consultez [Préparer l’hôte d’extension pour Azure Stack](../azure-stack-extension-host-prepare.md).

<!-- IS --> 
- **Les éléments de la galerie pour les groupes de machines virtuelles identiques sont maintenant intégrés**. L’élément de galerie Groupe de machines virtuelles identiques est désormais accessible dans les portails administrateur et utilisateur sans avoir à le télécharger.  Si vous effectuez une mise à niveau vers la version 1808, cet élément est disponible à la fin de la mise à niveau.  

<!-- IS, ASDK --> 
- **Mise à l’échelle de Virtual Machine Scale Sets**. Vous pouvez utiliser le portail pour [mettre à l’échelle un groupe de machines virtuelles identiques](../azure-stack-compute-add-scalesets.md#scale-a-virtual-machine-scale-set).    

<!-- 2489570 | IS ASDK--> 
- **Prise en charge des configurations de stratégie IPSec/IKE personnalisées** pour les [passerelles VPN dans Azure Stack](../../user/azure-stack-vpn-gateway-about-vpn-gateways.md).

<!-- | IS ASDK--> 
- **Élément Kubernetes de la Place de marché**. Vous pouvez désormais déployer des clusters Kubernetes avec [l’élément Kubernetes de la Place de marché](../azure-stack-solution-template-kubernetes-cluster-add.md). Pour déployer un cluster Kubernetes sur Azure Stack, les utilisateurs n’ont qu’à sélectionner l’élément Kubernetes et renseigner quelques paramètres. L’objectif des modèles est de permettre aux utilisateurs de créer des déploiements Kubernetes de développement et de test en quelques étapes seulement.

<!-- | IS ASDK--> 
- **Modèles Blockchain**. Vous pouvez maintenant exécuter des [déploiements de consortium Ethereum](../../user/azure-stack-ethereum.md) sur Azure Stack. Les [modèles de démarrage rapide Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates) proposent trois nouveaux modèles. Avec ces nouveaux modèles, les utilisateurs peuvent déployer et configurer un réseau Ethereum pour former un consortium de membres, sans avoir besoin de connaissances avancées sur Azure et Ethereum. L’objectif des modèles est de permettre aux utilisateurs de créer des déploiements Blockchain de développement et de test facilement, en quelques étapes seulement.

<!-- | IS ASDK--> 
- **Le profil de version d’API 2017-03-09-profile a été mis à jour vers 2018-03-01-hybrid**. Les profils d’API précisent le fournisseur de ressources Azure ainsi que la version d’API des points de terminaison Azure REST. Pour plus d’informations sur les profils, consultez [Gérer les profils de version des API dans Azure Stack](../../user/azure-stack-version-profiles.md).

 ### <a name="fixed-issues"></a>Problèmes résolus
<!-- IS ASDK--> 
- Nous avons résolu le problème de la création d’un groupe à haute disponibilité dans le portail qui aboutissait à un groupe avec un domaine d’erreur et un domaine de mise à jour 1. 

<!-- IS ASDK --> 
- Les paramètres de mise à l’échelle des groupe de machines virtuelles identiques sont maintenant disponibles dans le portail.  

<!-- 2494144- IS, ASDK --> 
- Le problème qui empêchait l’affichage de certaines tailles de machine virtuelle de série F quand vous sélectionniez une taille de machine virtuelle pour le déploiement est maintenant résolu. 

<!-- IS, ASDK --> 
- Améliorations des performances lors de la création de machines virtuelles, et utilisation optimisée du stockage sous-jacent.

- **Divers correctifs** pour les performances, la stabilité, la sécurité et le système d’exploitation utilisé par Azure Stack.


### <a name="changes"></a>Modifications
<!-- 1697698  | IS, ASDK --> 
- Les *tutoriels de démarrage rapide* dans le tableau de bord du portail utilisateur renvoient désormais vers des articles pertinents de la documentation Azure Stack en ligne.

<!-- 2515955   | IS ,ASDK--> 
- L’option *Tous les services* remplace l’option *Autres services* dans les portails d’administration et utilisateur Azure Stack. Vous pouvez maintenant utiliser *Tous les services* comme alternative pour naviguer dans les portails Azure Stack de la même façon que dans les portails Azure.

<!-- TBD | IS, ASDK --> 
- L’option *+ Créer une ressource* remplace l’option *+ Nouveau* dans les portails d’administration et utilisateur Azure Stack.  Vous pouvez maintenant utiliser *+ Créer une ressource* comme alternative pour naviguer dans les portails Azure Stack de la même façon que dans les portails Azure.  

<!--  TBD | IS, ASDK --> 
- Les tailles de machine virtuelle *De base A* ne sont plus disponibles pour la [création de groupes de machines virtuelles identiques](../azure-stack-compute-add-scalesets.md) (VMSS) dans le portail. Pour créer un groupe de machines virtuelles identiques avec cette taille, utilisez PowerShell ou un modèle.  

### <a name="common-vulnerabilities-and-exposures"></a>Failles et menaces courantes

Cette mise à jour installe les mises à jour suivantes :  

- [CVE-2018-0952](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-0952)
- [CVE-2018-8200](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8200)
- [CVE-2018-8204](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8204)
- [CVE-2018-8253](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8253)
- [CVE-2018-8339](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8339)
- [CVE-2018-8340](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8340)
- [CVE-2018-8341](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8341)
- [CVE-2018-8343](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8343)
- [CVE-2018-8344](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8344)
- [CVE-2018-8345](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8345)
- [CVE-2018-8347](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8347)
- [CVE-2018-8348](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8348)
- [CVE-2018-8349](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8349)
- [CVE-2018-8394](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8394)
- [CVE-2018-8398](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8398)
- [CVE-2018-8401](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8401)
- [CVE-2018-8404](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8404)
- [CVE-2018-8405](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8405)
- [CVE-2018-8406](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8406)  

Pour plus d’informations sur ces vulnérabilités, cliquez sur les liens précédents ou consultez l’article de la Base de connaissances Microsoft [4343887](https://support.microsoft.com/help/4343887).

Cette mise à jour contient également l’atténuation de la vulnérabilité du canal auxiliaire d’exécution spéculative appelée L1TF (L1 Terminal Fault) et décrite dans [l’avis de sécurité Microsoft ADV180018](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180018).  

### <a name="prerequisites"></a>Prérequis

- Installez la mise à jour 1807 d'Azure Stack avant d'appliquer la mise à jour 1808. 
- Avant de démarrer l’installation de cette mise à jour, exécutez [Test-AzureStack](../azure-stack-diagnostic-test.md) avec les paramètres suivants pour valider l’état de votre Azure Stack et résoudre les éventuels problèmes opérationnels détectés, y compris tous les avertissements et les échecs. Examinez aussi les alertes actives et résolvez toutes celles qui nécessitent une intervention.  

  ```PowerShell
  Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
  ```   

### <a name="known-issues-with-the-update-process"></a>Problèmes connus avec le processus de mise à jour

- Quand vous exécutez [Test-AzureStack](../azure-stack-diagnostic-test.md) après la mise à jour 1808, un message d’avertissement du contrôleur BMC s’affiche. Vous pouvez ignorer cet avertissement sans problème.

<!-- 2468613 - IS --> 
- Pendant l’installation de cette mise à jour, des alertes avec le titre *Erreur - Le modèle de FaultType UserAccounts.New est manquant* peuvent s’afficher.  Vous pouvez ignorer ces alertes de manière sécurisée. Elles se fermeront automatiquement une fois l’installation de la mise à jour terminée.

<!-- 2489559 - IS --> 
- Ne tentez pas de créer des machines virtuelles pendant l’installation de cette mise à jour. Pour plus d’informations sur la gestion des mises à jour, consultez [Manage updates in Azure Stack overview](../azure-stack-updates.md) (Vue d’ensemble de la gestion des mises à jour dans Azure Stack).

<!-- 2830461 - IS --> 
- Dans certaines circonstances quand une mise à jour nécessite une attention particulière, l’alerte correspondante peut ne pas être générée. L’état précis apparaît quand même dans le portail et n’est pas affecté.

### <a name="post-update-steps"></a>Étapes après la mise à jour
Après l’installation de cette mise à jour, installez les correctifs logiciels applicables. Pour plus d’informations, consultez les articles suivants de la base de connaissances, ainsi que notre [stratégie de maintenance](../azure-stack-servicing-policy.md). 
- [KB 4465859 - Correctif Azure Stack 1.1808.2.104](https://support.microsoft.com/help/4465859/)

## <a name="known-issues-post-installation"></a>Problèmes connus (après l’installation)

Les éléments suivants sont des problèmes connus qui apparaissent après l’installation de cette build.

### <a name="portal"></a>Portail

- La documentation technique Azure Stack s’applique à la dernière version. En raison des changements apportés au portail entre les versions, ce que vous voyez dans les portails Azure Stack peut différer du contenu de la documentation. 

<!-- TBD - IS ASDK --> 
- Il se peut qu’un tableau de bord vide s’affiche sur le portail. Pour récupérer le tableau de bord, cliquez sur **Modifier le tableau de bord**, puis cliquez avec le bouton droit sur **Rétablir l’état par défaut**.

<!-- 2930718 - IS ASDK --> 
- Dans le portail d’administration, quand vous affichez les détails d’un abonnement utilisateur, après avoir fermé le panneau et cliqué sur **Récent**, le nom de l’abonnement utilisateur n’apparaît pas.

<!-- 3060156 - IS ASDK --> 
-  Dans les portails administrateur et utilisateur, quand vous cliquez sur les paramètres du portail et sélectionnez **Supprimer tous les paramètres et tableaux de bord privés**, le résultat n’est pas le comportement prévu. Une notification d’erreur s’affiche. 

<!-- 2930799 - IS ASDK --> 
- Dans les portails d’administration et utilisateur, sous **Tous les services**, l’élément **Plans de protection DDoS** est incorrectement répertorié. En effet, il n’est pas disponible dans Azure Stack. Si vous essayez de le créer, une erreur s’affiche indiquant que le portail ne peut pas créer cet élément de la Place de marché. 

<!-- 2930820 - IS ASDK --> 
- Dans les portails d’administration et utilisateur, si vous recherchez « Docker », l’élément est incorrectement retourné dans les résultats. En effet, il n’est pas disponible dans Azure Stack. Si vous essayez de le créer, un panneau indiquant une erreur s’affiche. 

<!-- 2967387 IS, ASDK --> 
- Le compte que vous utilisez pour vous connecter au portail d’administration ou utilisateur Azure Stack s’affiche comme **Utilisateur non identifié**. Cela se produit quand le *prénom* ou le *nom* du compte n’a pas été spécifié. Pour contourner ce problème, modifiez le compte d’utilisateur afin d’ajouter le nom ou le prénom. Vous devez ensuite vous déconnecter et vous reconnecter au portail. 

<!--  2873083 - IS ASDK --> 
-  Quand vous utilisez le portail pour créer un groupe de machines virtuelles identiques, la liste déroulante *Taille d’instance* ne se charge pas correctement dans Internet Explorer. Pour contourner ce problème, utilisez un autre navigateur quand vous créez un groupe de machines virtuelles identiques à l’aide du portail.  

<!-- 2931230 IS  ASDK --> 
- Les plans ajoutés à un abonnement utilisateur comme plan d’extension ne peuvent pas être supprimés, même quand vous supprimez le plan de l’abonnement utilisateur. Le plan est conservé jusqu’à ce que les abonnements qui référencent le plan d’extension soient aussi supprimés. 

<!--2760466 IS  ASDK --> 
-  Quand vous installez un nouvel environnement Azure Stack qui exécute cette version, l’alerte qui indique *Activation requise* peut ne pas s’afficher. L’[activation](../azure-stack-registration.md) est nécessaire avant que vous puissiez utiliser la syndication de Place de marché.  

<!-- TBD - IS ASDK --> 
- Les deux types d’abonnements d’administration qui ont été introduits avec la version 1804 ne doivent pas être utilisés. Les types d’abonnements sont **Abonnement de contrôle** et **Abonnement de consommation**. Ces types d’abonnements sont visibles dans les nouveaux environnements Azure Stack depuis la version 1804, mais ils ne sont pas encore prêts à être utilisés. Vous devez continuer à utiliser le type d’abonnement **Fournisseur par défaut**.

<!-- TBD - IS ASDK --> 
- La suppression d’abonnements utilisateur aboutit à des ressources orphelines. Pour contourner ce problème, commencez pas supprimer des ressources d’utilisateurs ou la totalité du groupe de ressources, puis supprimez les abonnements utilisateur.

<!-- TBD - IS ASDK --> 
- Vous ne pouvez pas afficher les autorisations définies pour votre abonnement à l’aide des portails Azure Stack. Pour résoudre ce problème, utilisez PowerShell pour vérifier les autorisations.


### <a name="health-and-monitoring"></a>Intégrité et surveillance
<!-- 1264761 - IS ASDK --> 
-   Vous risquez de recevoir des alertes pour le composant du **contrôleur d’intégrité** contenant les informations suivantes :  

   Alerte 1 :
   - NOM : Rôle d’infrastructure défectueux
   - GRAVITÉ : Avertissement
   - COMPOSANT : Contrôleur d’intégrité
   - DESCRIPTION : L’analyseur de pulsations du contrôleur d’intégrité n’est pas disponible. Cela peut affecter les rapports et les métriques d’intégrité.  

  Alerte 2 :
   - NOM : Rôle d’infrastructure défectueux
   - GRAVITÉ : Avertissement
   - COMPOSANT : Contrôleur d’intégrité
   - DESCRIPTION : L’analyseur d’erreur du contrôleur d’intégrité n’est pas disponible. Cela peut affecter les rapports et les métriques d’intégrité.

  Les deux alertes peuvent être ignorées en toute sécurité. Elles se ferment automatiquement au bout d’un moment.  


<!-- 2812138 | IS --> 
- Vous pouvez voir une alerte pour le composant **Stockage** avec les informations suivantes :

   - NOM : Erreur de communication interne du service de stockage  
   - GRAVITÉ : Critique  
   - COMPOSANT : Stockage  
   - DESCRIPTION : Une erreur de communication interne du service de stockage s’est produite lors de l’envoi des requêtes aux nœuds suivants.  

    L’alerte peut être ignorée sans risque, mais vous devez la fermer manuellement.

<!-- 2368581 - IS. ASDK --> 
- En tant qu’opérateur d’Azure Stack, si vous recevez une alerte de mémoire insuffisante et que les machines virtuelles de locataire ne parviennent pas à se déployer à cause d’une **erreur de création de machine virtuelle de structure**, il est possible que l’horodatage d’Azure Stack soit à court de mémoire. Utilisez le [Capacity Planner Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) pour mieux comprendre la capacité disponible pour vos charges de travail.


### <a name="compute"></a>Calcul

<!-- 3099544 IS, ASDK --> 
-  Quand vous créez une machine virtuelle à l’aide du portail Azure Stack et sélectionnez la taille de machine virtuelle, la colonne EUR/mois s’affiche avec le message **Indisponible**. Cette colonne ne devrait pas s’afficher, car l’affichage de la colonne des prix des machines virtuelles n’est pas pris en charge dans Azure Stack.

<!-- 3090289 IS, ASDK --> 
- Après avoir appliqué la mise à jour 1808, il se peut que vous rencontriez les problèmes suivants lors du déploiement de machines virtuelles avec la fonctionnalité Disques managés :

   1. Si l’abonnement a été créé avant la mise à jour 1808, le déploiement de machines virtuelles avec la fonctionnalité Disques managés peut échouer avec un message d’erreur interne. Pour résoudre cette erreur, effectuez les étapes suivantes pour chaque abonnement :
      1. Dans le portail locataire, accédez à **Abonnements** et recherchez l’abonnement. Cliquez sur **Fournisseurs de ressources**, sur **Microsoft.Compute**, puis sur **Réinscrire**.
      2. Sous le même abonnement, accédez à **Contrôle d’accès (IAM)** et vérifiez que **Disques managés d’Azure Stack** figure dans la liste.
   2. Si vous avez configuré un environnement multilocataire, le déploiement de machines virtuelles dans un abonnement associé à un annuaire invité peut échouer avec un message d’erreur interne. Pour résoudre cette erreur, effectuez les étapes suivantes :
      1. Appliquez le [correctif 1808 d’Azure Stack](https://support.microsoft.com/help/4465859).
      2. Effectuez les étapes décrites dans [cet article](../azure-stack-enable-multitenancy.md#register-a-guest-directory) pour reconfigurer chacun de vos annuaires invités.

<!-- 2869209 IS, ASDK --> 
- Quand vous utilisez [l’applet de commande **Add-AzsPlatformImage**](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0&preserve-view=true), vous devez spécifier le paramètre **-OsUri** comme URI du compte de stockage où le disque est chargé. Si vous utilisez le chemin local du disque, l’applet de commande échoue avec l’erreur suivante : *Échec de l’opération de longue durée avec l’état « Failed »* . 

<!--  2966665 IS, ASDK --> 
- L’attachement de disques de données SSD à des machines virtuelles de disques managés de taille Premium (DS, DSv2, Fs, Fs_V2) échoue avec une erreur :  *Échec de mise à jour de la machine virtuelle « nom_machine_virtuelle ». Erreur : Impossible d’effectuer l’opération demandée car le type de compte de stockage « Premium_LRS » n’est pas pris en charge pour la taille de machine virtuelle « Standard_DS/Ds_V2/FS/Fs_v2) »* .

   Pour contourner ce problème, utilisez des disques de données *Standard_LRS* à la place de *disques Premium_LRS*. L’utilisation de disques de données *Standard_LRS* n’a pas d’impact sur l’IOP ou le coût de facturation. 

<!--  2795678 IS, ASDK --> 
- Quand vous utilisez le portail pour créer des machines virtuelles de taille Premium (DS, Ds_v2, FS, FSv2), celles-ci sont créées dans un compte de stockage standard. La création dans un compte de stockage standard n’a pas d’impact sur les fonctionnalités, l’IOP ou la facturation. 

   Vous pouvez sans risque ignorer l’avertissement qui indique : *Vous avez choisi d’utiliser un disque standard sur une taille qui prend en charge les disques Premium. Cela n’est pas recommandé, car cela peut impacter les performances du système d’exploitation. Utilisez le stockage Premium (SSD) à la place.*

<!-- 2967447 - IS, ASDK --> 
- Quand vous créez un groupe de machines virtuelles identiques, l’option CentOS 7.2 est proposée pour le déploiement. Étant donné que cette image n’est pas disponible sur Azure Stack, sélectionnez un autre système d’exploitation pour votre déploiement ou choisissez un modèle Azure Resource Manager spécifiant une autre image CentOS qui a été téléchargée par l’opérateur avant le déploiement à partir de la Place de marché.  

<!-- 2724873 - IS --> 
-  En utilisant les cmdlets PowerShell **Start-AzsScaleUnitNode** ou **Stop-AzsScaleunitNode** pour gérer les unités d’échelle, il est possible que la première tentative de démarrage ou d’arrêt de l’unité d’échelle échoue. Si la cmdlet échoue une première fois, exécutez-la une seconde fois. La deuxième exécution doit permettre de terminer l’opération. 

<!-- TBD - IS ASDK --> 
- Lorsque vous créez des machines virtuelles sur le portail utilisateur Azure Stack, le portail affiche un nombre incorrect de disques de données que vous pouvez attacher à une machine virtuelle de la série DS. Les machines virtuelles de série DS peuvent prendre en charge autant de disques de données que la configuration Azure.

<!-- TBD - IS ASDK --> 
- Si l’approvisionnement d’une extension sur un déploiement de machine virtuelle prend trop de temps, les utilisateurs doivent laisser expirer le délai d’attente plutôt que d’essayer d’arrêter le processus pour désallouer ou supprimer la machine virtuelle.  

<!-- 1662991 IS ASDK --> 
- Les diagnostics de machine virtuelle Linux ne sont pas pris en charge dans Azure Stack. Lorsque vous déployez une machine virtuelle Linux en activant les diagnostics de machine virtuelle, le déploiement échoue. Le déploiement échoue également si vous activez les mesures de base de la machine virtuelle Linux dans les paramètres de diagnostic.  

<!-- 2724961- IS ASDK --> 
-  Quand vous inscrivez le fournisseur de ressources **Microsoft.Insight** dans les paramètres d’abonnement et que vous créez une machine virtuelle Windows avec diagnostics du système d’exploitation invité activés, le graphique de pourcentage d’UC dans la page de vue d’ensemble de machine virtuelle ne peut pas afficher les données de métriques.

   Pour trouver le graphique de pourcentage d’UC de la machine virtuelle, accédez au panneau **Métriques** et affichez toutes les métriques de machines virtuelles Windows invitées prises en charge.



### <a name="networking"></a>Mise en réseau  

<!-- 1766332 - IS ASDK --> 
-  Sous **Mise en réseau**, si vous cliquez sur **Créer une passerelle VPN** pour configurer une connexion VPN, l’option **Basé sur des stratégies** s’affiche dans la liste des types de VPN. Ne sélectionnez pas cette option. Seule l’option **Basé sur itinéraires** est prise en charge dans Azure Stack.

<!-- 1902460 - IS ASDK --> 
- Azure Stack prend en charge une seule *passerelle de réseau local* par adresse IP. Cela est vrai pour tous les abonnements de locataire. Après la création de la première connexion de passerelle de réseau local, les tentatives suivantes de création d’une ressource de passerelle de réseau local avec la même adresse IP sont bloquées.

<!-- 16309153 - IS ASDK --> 
- Sur un réseau virtuel a été créé avec un paramètre de serveur DNS défini sur *Automatique*, vous ne pouvez pas choisir un serveur DNS personnalisé. Les paramètres mis à jour ne sont pas envoyés (par push) aux machines virtuelles dans ce réseau virtuel.

<!-- 2702741 -  IS ASDK --> 
- La conservation des adresses IP publiques déployées à l’aide de la méthode d’allocation dynamique n’est pas garantie après l’émission d’une commande Arrêter/Libérer.

<!-- 2529607 - IS ASDK --> 
- Durant la *rotation des secrets* Azure Stack, une période de deux à cinq minutes s’écoule pendant laquelle les adresses IP publiques sont inaccessibles.

<!-- 2664148 - IS ASDK --> 
- Dans les cas où le locataire accède à ses machines virtuelles à l’aide d’un tunnel VPN S2S, il peut rencontrer un scénario où les tentatives de connexion échouent si le sous-réseau local a été ajouté à la passerelle de réseau local après la création de la passerelle. 


<!-- ### SQL and MySQL-->


### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- Les utilisateurs doivent inscrire le fournisseur de ressources de stockage avant de créer leur première fonction Azure dans l’abonnement.

<!-- 2489178 - IS ASDK --> 
- Pour monter en puissance l’infrastructure (Workers, gestion, rôles frontaux), vous devez utiliser PowerShell comme décrit dans les notes de publication pour Compute.



### <a name="usage"></a>Utilisation  
<!-- TBD - IS ASDK --> 
-  Les données d’utilisation des adresses IP publiques indiquent une même valeur *EventDateTime* pour chaque enregistrement au lieu de l’horodatage *TimeDate* qui s’affiche lors de la création de chaque enregistrement. Pour le moment, vous ne pouvez pas utiliser ces données pour calculer de manière précise l’utilisation des adresses IP publiques.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Télécharger la mise à jour
Vous pouvez télécharger la mise à jour 1808 d’Azure Stack à partir de [cet emplacement](https://aka.ms/azurestackupdatedownload).
  

## <a name="next-steps"></a>Étapes suivantes
- Pour passer en revue la stratégie de maintenance pour les systèmes intégrés Azure Stack et pour connaître la marche à suivre afin de conserver votre système dans un état de prise en charge, consultez [Stratégie de maintenance Azure Stack](../azure-stack-servicing-policy.md).  
- Pour utiliser le Point de terminaison privilégié (PEP) afin de surveiller et de reprendre les mises à jour, consultez [Surveiller les mises à jour dans Azure Stack à l’aide du point de terminaison privilégié](../azure-stack-monitor-update.md).  
- Pour obtenir une vue d’ensemble de la gestion des mises à jour dans Azure Stack, consultez [Gérer les mises à jour dans Azure Stack - Vue d’ensemble](../azure-stack-updates.md).  
- Pour plus d’informations sur la façon d’appliquer des mises à jour avec Azure Stack, consultez [Effectuer des mises à jour dans Azure Stack](../azure-stack-apply-updates.md).  