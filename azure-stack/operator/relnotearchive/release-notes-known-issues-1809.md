---
title: Mise à jour 1809 d’Azure Stack | Microsoft Docs
description: Découvrez les nouveautés de la mise à jour 1809 pour les systèmes intégrés Azure Stack, y compris les problèmes connus et l’emplacement de téléchargement de la mise à jour.
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
ms.date: 02/28/2019
ms.author: sethm
ms.reviewer: justini
ms.lastreviewed: 02/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f98f0f28037cfc1de648f36cce20dd1c5334f93b
ms.sourcegitcommit: f194f9ca4297864500e62d8658674a0625b29d1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102186940"
---
# <a name="azure-stack-1809-update"></a>Mise à jour 1809 d’Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Cet article décrit le contenu de la mise à jour 1809. La mise à jour inclut des améliorations, des corrections et des problèmes connus de cette version d’Azure Stack. Cet article inclut également un lien de téléchargement de la mise à jour. Les problèmes connus sont divisés en problèmes directement liés au processus de mise à jour et en problèmes propres à la build (après l’installation).

> [!IMPORTANT]  
> Cette mise à jour est destinée uniquement aux systèmes intégrés d’Azure Stack. N’appliquez pas cette mise à jour au Kit de développement Azure Stack.

## <a name="build-reference"></a>Référence de build

Le numéro de build de la mise à jour 1809 d’Azure Stack est **1.1809.0.90**.  

### <a name="new-features"></a>Nouvelles fonctionnalités

Cette mise à jour inclut les améliorations suivantes pour Azure Stack :

- Avec cette version, les systèmes intégrés Azure Stack prennent en charge des configurations de 4 à 16 nœuds. Vous pouvez utiliser [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) pour vous aider à planifier la capacité et la configuration d’Azure Stack.

- <!--  2712869   | IS  ASDK -->  **Client Syslog Azure Stack (disponibilité générale)**  Ce client permet de transférer des journaux d’activité d’audit, d’alertes et de sécurité relatifs à l’infrastructure Azure Stack vers un serveur Syslog ou un logiciel SIEM (Security Information and Event Management, gestion des informations et événements de sécurité) externe à Azure Stack. Le client Syslog prend désormais en charge la spécification du port sur lequel écoute le serveur Syslog.

   Avec cette publication, le client Syslog devient généralement disponible et peut être utilisé en environnement de production.

   Pour plus d’informations, consultez [Transfert de syslog dans Azure Stack](../azure-stack-integrate-security.md).

- Vous pouvez à présent [déplacer la ressource d’inscription](../azure-stack-registration.md#move-a-registration-resource) sur Azure entre des groupes de ressources sans avoir à vous réinscrire. Les fournisseurs de solutions cloud (CSP) peuvent également déplacer la ressource d’inscription entre des abonnements, tant que les anciens et nouveaux abonnements sont mappés vers le même ID de partenaire CSP. Cela n’affecte pas les mappages du locataire client existants. 

- Ajout de la prise en charge de l’attribution de plusieurs adresses IP par interface réseau.  Pour plus d’informations, consultez [Affecter plusieurs adresses IP à des machines virtuelles avec PowerShell](/azure/virtual-network/virtual-network-multiple-ip-addresses-powershell).

### <a name="fixed-issues"></a>Problèmes résolus

<!-- TBD - IS ASDK -->
- Dans le portail, le graphique indiquant la mémoire libre et la capacité utilisée est désormais plus précis. Vous pouvez maintenant prévoir de manière plus fiable le nombre de machines virtuelles que vous êtes en mesure de créer.

<!-- TBD - IS ASDK --> 
- Le problème a été résolu, dans lequel le portail affichait un nombre incorrect de disques de données que vous pouvez attacher à une machine virtuelle de la série DS lorsque vous créez des machines virtuelles sur le portail utilisateur Azure Stack. Les machines virtuelles de série DS peuvent prendre en charge autant de disques de données que la configuration Azure.

- Les problèmes de disque managé suivants ont été corrigés dans la mise à jour 1809 ainsi que dans la mise à jour 1808 [Correctif Azure Stack 1.1808.9.117](https://support.microsoft.com/help/4481066/) : 

   <!--  2966665 IS, ASDK --> 
  - Résolution du problème qui faisait que l’attachement de disques de données SSD à des machines virtuelles de disques managés de taille Premium (DS, DSv2, Fs, Fs_V2) échouait avec une erreur :  *Échec de mise à jour de la machine virtuelle « nom_machine_virtuelle ». Erreur : Impossible d’effectuer l’opération demandée car le type de compte de stockage « Premium_LRS » n’est pas pris en charge pour la taille de machine virtuelle « Standard_DS/Ds_V2/FS/Fs_v2) »* . 
   
  - Création d’une machine virtuelle avec disque managé à l’aide de **createOption** : **Attach** échoue avec l’erreur suivante : *Échec de l’opération de longue durée avec l’état « Failed ». Informations supplémentaires : 'Une erreur d'exécution interne s'est produite.'*
    ErrorCode: InternalExecutionError ErrorMessage : une erreur d’exécution interne s’est produite.
   
    Ce problème est à présent résolu.

- <!-- 2702741 -  IS, ASDK --> Problème résolu de la conservation des adresses IP publiques déployées à l’aide de la méthode d’allocation dynamique qui n’était pas garantie après l’émission d’une commande d’arrêt/libération. Elles sont désormais conservées.

- <!-- 3078022 - IS, ASDK --> Si une machine virtuelle était arrêtée/libérée avant la mise à jour 1808, il n’était pas possible de la réallouer après cette mise à jour.  Ce problème a été résolu dans la mise à jour 1809. Grâce à ce correctif introduit dans la mise à jour 1809, les instances qui se trouvaient dans cet état empêchant leur démarrage peuvent désormais être démarrées. Le correctif empêche également que ce problème se reproduise.

### <a name="changes"></a>Modifications

<!-- 2635202 - IS, ASDK -->
- Le service de sauvegarde de l’infrastructure se passe du [réseau d’infrastructure publique](/azure/azure-stack/azure-stack-network) au [réseau d’adresse IP virtuelle publique](/azure/azure-stack/azure-stack-network#public-vip-network). Les clients doivent vérifier que le service a accès à l’emplacement de stockage de sauvegarde à partir du réseau d’adresse IP virtuelle publique.  

> [!IMPORTANT]  
> Si vous avez un pare-feu qui n’autorise pas les connexions du réseau d’adresse IP virtuelle publique au serveur de fichiers, ce changement entraîne l’échec des sauvegardes d’infrastructure avec l’« Erreur 53 Le chemin réseau n’a pas été trouvé ». Il s’agit d’un changement cassant pour lequel il n’existe aucune solution de contournement raisonnable. Sur la base des remarques des clients, Microsoft établira ce changement dans un correctif. Pour plus d’informations sur les correctifs logiciels disponibles pour 1809, passez en revue la [section relative aux étapes post-mise à jour](#post-update-steps). Une fois le correctif logiciel disponible, veillez à l’appliquer après avoir effectué la mise à jour vers 1809 uniquement si vos stratégies réseau n’autorisent pas le réseau d’adresse IP virtuelle publique à accéder aux ressources d’infrastructure. Dans 1811, cette modification sera appliquée à tous les systèmes. Si vous avez appliqué le correctif logiciel dans 1809, aucune action supplémentaire n’est nécessaire.  

### <a name="common-vulnerabilities-and-exposures"></a>Vulnérabilités et menaces courantes

Cette mise à jour installe les mises à jour de sécurité suivantes :  

- [ADV180022](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180022)
- [CVE-2018-0965](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-0965)
- [CVE-2018-8271](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8271)
- [CVE-2018-8320](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8320)
- [CVE-2018-8330](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8330)
- [CVE-2018-8332](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8332)
- [CVE-2018-8333](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8333)
- [CVE-2018-8335](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8335)
- [CVE-2018-8392](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8392)
- [CVE-2018-8393](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8393)
- [CVE-2018-8410](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8410)
- [CVE-2018-8411](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8411)
- [CVE-2018-8413](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8413)
- [CVE-2018-8419](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8419)
- [CVE-2018-8420](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8420)
- [CVE-2018-8423](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8423)
- [CVE-2018-8424](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8424)
- [CVE-2018-8433](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8433)
- [CVE-2018-8434](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8434)
- [CVE-2018-8435](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8435)
- [CVE-2018-8438](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8438)
- [CVE-2018-8439](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8439)
- [CVE-2018-8440](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8440)
- [CVE-2018-8442](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8442)
- [CVE-2018-8443](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8443)
- [CVE-2018-8446](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8446)
- [CVE-2018-8449](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8449)
- [CVE-2018-8453](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8453)
- [CVE-2018-8455](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8455)
- [CVE-2018-8462](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8462)
- [CVE-2018-8468](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8468)
- [CVE-2018-8472](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8472)
- [CVE-2018-8475](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8475)
- [CVE-2018-8481](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8481)
- [CVE-2018-8482](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8482)
- [CVE-2018-8484](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8484)
- [CVE-2018-8486](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8486)
- [CVE-2018-8489](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8489)
- [CVE-2018-8490](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8490)
- [CVE-2018-8492](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8492)
- [CVE-2018-8493](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8493)
- [CVE-2018-8494](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8494)
- [CVE-2018-8495](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8495)
- [CVE-2018-8497](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8497)

Pour plus d’informations sur ces vulnérabilités, cliquez sur les liens précédents ou consultez les articles de la Base de connaissances Microsoft [4457131](https://support.microsoft.com/help/4457131) et [4462917](https://support.microsoft.com/help/4462917).

### <a name="prerequisites"></a>Prérequis

- Installez le dernier correctif Azure Stack pour 1808 avant d’appliquer 1809. Pour plus d’informations, consultez [KB 4481066 - Correctif Azure Stack 1.1808.9.117](https://support.microsoft.com/help/4481066/). Bien que Microsoft recommande le dernier correctif logiciel disponible, la version minimale requise pour installer 1809 est 1.1808.5.110.

  > [!TIP]  
  > Abonnez-vous aux flux *RRS* ou *Atom* suivants pour vous tenir informés des correctifs logiciels Azure Stack :
  > - RRS : https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss 
  > - Atom : https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom


- Avant de démarrer l’installation de cette mise à jour, exécutez [Test-AzureStack](../azure-stack-diagnostic-test.md) avec les paramètres suivants pour valider l’état de votre Azure Stack et résoudre les éventuels problèmes opérationnels détectés, y compris tous les avertissements et les échecs. Examinez aussi les alertes actives et résolvez toutes celles qui nécessitent une intervention.  

  ```powershell
  Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
  ```

- Lorsqu’Azure Stack est géré par System Center Operations Manager (SCOM), veillez à mettre à jour le Pack d’administration pour Microsoft Azure Stack vers la version 1.0.3.11 avant d’appliquer la version 1809.

### <a name="known-issues-with-the-update-process"></a>Problèmes connus avec le processus de mise à jour

- Quand vous exécutez [Test-AzureStack](../azure-stack-diagnostic-test.md) après la mise à jour 1809, un message d’avertissement du contrôleur BMC s’affiche. Vous pouvez ignorer cet avertissement sans problème.

- <!-- 2468613 - IS --> Pendant l’installation de cette mise à jour, des alertes avec le titre *Erreur - Le modèle de FaultType UserAccounts.New est manquant* peuvent s’afficher.  Vous pouvez ignorer ces alertes de manière sécurisée. Elles se fermeront automatiquement une fois l’installation de la mise à jour terminée.

- <!-- 2489559 - IS --> Ne tentez pas de créer des machines virtuelles pendant l’installation de cette mise à jour. Pour plus d’informations sur la gestion des mises à jour, consultez [Manage updates in Azure Stack overview](../azure-stack-updates.md#plan-for-updates) (Vue d’ensemble de la gestion des mises à jour dans Azure Stack).

- <!-- 3139614 | IS --> Si vous avez appliqué une mise à jour à Azure Stack à partir de votre matériel OEM, la notification **Mise à jour disponible** peut ne pas apparaître dans le portail d’administration Azure Stack. Pour installer la mise à jour Microsoft, téléchargez et importez manuellement cette mise à jour en suivant les instructions indiquées ici [Effectuer des mises à jour dans Azure Stack](../azure-stack-apply-updates.md).

### <a name="post-update-steps"></a>Étapes après la mise à jour

> [!Important]  
> Préparer votre déploiement Azure Stack pour l’hôte d’extension qui est activé par le prochain package de mise à jour. Préparez votre système en utilisant les instructions suivantes, [Préparer l’hôte d’extension pour Azure Stack](../azure-stack-extension-host-prepare.md).

Après l’installation de cette mise à jour, installez les correctifs logiciels applicables. Pour plus d’informations, consultez les articles suivants de la base de connaissances, ainsi que notre [stratégie de maintenance](../azure-stack-servicing-policy.md).  
- [KB 4481548 - Correctif Azure Stack 1.1809.12.114](https://support.microsoft.com/help/4481548/)  

## <a name="known-issues-post-installation"></a>Problèmes connus (après l’installation)

Les éléments suivants sont des problèmes connus qui apparaissent après l’installation de cette build.

### <a name="portal"></a>Portail

- La documentation technique Azure Stack s’applique à la dernière version. En raison des changements apportés au portail entre les versions, ce que vous voyez dans les portails Azure Stack peut différer du contenu de la documentation.

<!-- 2930718 - IS ASDK --> 
- Dans le portail d’administration, quand vous affichez les détails d’un abonnement utilisateur, après avoir fermé le panneau et cliqué sur **Récent**, le nom de l’abonnement utilisateur n’apparaît pas.

<!-- 3060156 - IS ASDK --> 
-  Dans les portails administrateur et utilisateur, quand vous cliquez sur les paramètres du portail et sélectionnez **Supprimer tous les paramètres et tableaux de bord privés**, le résultat n’est pas le comportement prévu. Une notification d’erreur s’affiche. 

<!-- 2930799 - IS ASDK --> 
- Dans les portails d’administration et utilisateur, sous **Tous les services**, l’élément **Plans de protection DDoS** est incorrectement répertorié. Il n’est pas disponible dans Azure Stack. Si vous essayez de le créer, une erreur s’affiche indiquant que le portail ne peut pas créer cet élément de la Place de marché. 

<!-- 2930820 - IS ASDK --> 
- Dans les portails d’administration et utilisateur, si vous recherchez « Docker », l’élément est incorrectement retourné dans les résultats. Il n’est pas disponible dans Azure Stack. Si vous essayez de le créer, un panneau indiquant une erreur s’affiche. 

<!-- 2967387 - IS, ASDK --> 
- Le compte que vous utilisez pour vous connecter au portail d’administration ou utilisateur Azure Stack s’affiche comme **Utilisateur non identifié**. Ce message s’affiche quand le *prénom* ou le *nom* du compte n’a pas été spécifié. Pour contourner ce problème, modifiez le compte d’utilisateur afin d’ajouter le nom ou le prénom. Vous devez ensuite vous déconnecter et vous reconnecter au portail.  

<!--  2873083 - IS ASDK --> 
-  Quand vous utilisez le portail pour créer un groupe de machines virtuelles identiques, la liste déroulante *Taille d’instance* ne se charge pas correctement dans Internet Explorer. Pour contourner ce problème, utilisez un autre navigateur quand vous créez un groupe de machines virtuelles identiques à l’aide du portail.  

<!-- 2931230 - IS  ASDK --> 
- Les plans ajoutés à un abonnement utilisateur comme plan d’extension ne peuvent pas être supprimés, même quand vous supprimez le plan de l’abonnement utilisateur. Le plan est conservé jusqu’à ce que les abonnements qui référencent le plan d’extension soient aussi supprimés. 

<!--2760466 - IS  ASDK --> 
-  Quand vous installez un nouvel environnement Azure Stack qui exécute cette version, l’alerte qui indique *Activation requise* peut ne pas s’afficher. L’[activation](../azure-stack-registration.md) est nécessaire avant que vous puissiez utiliser la syndication de Place de marché.  

<!-- TBD - IS ASDK --> 
- Les deux types d’abonnements d’administration qui ont été introduits avec la version 1804 ne doivent pas être utilisés. Les types d’abonnements sont **Abonnement de contrôle** et **Abonnement de consommation**. Ces types d’abonnements sont visibles dans les nouveaux environnements Azure Stack depuis la version 1804, mais ils ne sont pas encore prêts à être utilisés. Vous devez continuer à utiliser le type d’abonnement **Fournisseur par défaut**.

<!-- TBD - IS ASDK --> 
- La suppression d’abonnements utilisateur aboutit à des ressources orphelines. Pour contourner ce problème, commencez pas supprimer des ressources d’utilisateurs ou la totalité du groupe de ressources, puis supprimez les abonnements utilisateur.

<!-- TBD - IS ASDK --> 
- Vous ne pouvez pas afficher les autorisations définies pour votre abonnement à l’aide des portails Azure Stack. Pour résoudre ce problème, utilisez PowerShell pour vérifier les autorisations.


### <a name="health-and-monitoring"></a>Intégrité et surveillance

<!-- TBD - IS -->
- Vous risquez de recevoir les alertes suivantes à plusieurs reprises avant qu’elles ne disparaissent sur votre système Azure Stack :
   - *Instance de rôle d’infrastructure non disponible*
   - *Nœud d’unité d’échelle hors ligne*
   
  Exécutez l’applet de commande [Test-AzureStack](../azure-stack-diagnostic-test.md) pour vérifier l’intégrité des instances de rôle d’infrastructure et des nœuds d’unité d’échelle. Si aucun problème n’est détecté par [Test-AzureStack](../azure-stack-diagnostic-test.md), vous pouvez ignorer ces alertes. Si un problème est détecté, vous pouvez tenter de démarrer l’instance de rôle d’infrastructure ou le nœud à l’aide du portail d’administration ou de PowerShell.

  Ce problème est résolu dans la dernière [version de correctif logiciel 1809](https://support.microsoft.com/help/4481548/), par conséquent, veillez à installer ce correctif logiciel si vous rencontrez le problème. 

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

<!-- 2368581 - IS, ASDK --> 
- En tant qu’opérateur d’Azure Stack, si vous recevez une alerte de mémoire insuffisante et que les machines virtuelles de locataire ne parviennent pas à se déployer à cause d’une **erreur de création de machine virtuelle de structure**, il est possible que l’horodatage d’Azure Stack soit à court de mémoire. Utilisez le [Capacity Planner Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) pour mieux comprendre la capacité disponible pour vos charges de travail.

### <a name="compute"></a>Calcul

- Lorsque vous créez une [machine virtuelle de série Dv2](../../user/azure-stack-vm-considerations.md#vm-sizes), les machines virtuelles D11-14v2 vous autorisent à créer 4, 8, 16 et 32 disques de données respectivement. Toutefois, le volet Créer une machine virtuelle affiche 8, 16, 32 et 64 disques de données.

<!-- 3235634 - IS, ASDK -->
- Pour déployer des machines virtuelles avec des tailles contenant un suffixe **v2** ; par exemple, **Standard_A2_v2**, spécifiez le suffixe sous la forme **Standard_A2_v2** (v minuscule). N’utilisez pas **Standard_A2_V2** (V majuscule). Cette méthode fonctionne dans Azure global et constitue une incohérence sur Azure Stack.

<!-- 3099544 - IS, ASDK --> 
-  Quand vous créez une machine virtuelle à l’aide du portail Azure Stack et sélectionnez la taille de machine virtuelle, la colonne EUR/mois s’affiche avec le message **Indisponible**. Cette colonne ne devrait pas s’afficher, car l’affichage de la colonne des prix des machines virtuelles n’est pas pris en charge dans Azure Stack.

<!-- 2869209 - IS, ASDK --> 
- Quand vous utilisez [l’applet de commande **Add-AzsPlatformImage**](/powershell/module/azs.compute.admin/add-azsplatformimage?preserve-view=true&view=azurestackps-1.4.0), vous devez spécifier le paramètre **-OsUri** comme URI du compte de stockage où le disque est chargé. Si vous utilisez le chemin local du disque, l’applet de commande échoue avec l’erreur suivante : *Échec de l’opération de longue durée avec l’état « Failed »* . 

<!--  2795678 - IS, ASDK --> 
- Quand vous utilisez le portail pour créer des machines virtuelles de taille Premium (DS, Ds_v2, FS, FSv2), celles-ci sont créées dans un compte de stockage standard. La création dans un compte de stockage standard n’a pas d’impact sur les fonctionnalités, l’IOP ou la facturation. 

   Vous pouvez sans risque ignorer l’avertissement qui indique : *Vous avez choisi d’utiliser un disque standard sur une taille qui prend en charge les disques Premium. Cela n’est pas recommandé, car cela peut impacter les performances du système d’exploitation. Utilisez le stockage Premium (SSD) à la place.*

<!-- 2967447 - IS, ASDK --> 
- Quand vous créez un groupe de machines virtuelles identiques, l’option CentOS 7.2 est proposée pour le déploiement. Étant donné que cette image n’est pas disponible sur Azure Stack, sélectionnez un autre système d’exploitation pour votre déploiement ou choisissez un modèle Azure Resource Manager spécifiant une autre image CentOS qui a été téléchargée par l’opérateur avant le déploiement à partir de la Place de marché.  

<!-- 2724873 - IS --> 
-  En utilisant les cmdlets PowerShell **Start-AzsScaleUnitNode** ou **Stop-AzsScaleunitNode** pour gérer les unités d’échelle, il est possible que la première tentative de démarrage ou d’arrêt de l’unité d’échelle échoue. Si la cmdlet échoue une première fois, exécutez-la une seconde fois. La deuxième exécution doit permettre de terminer l’opération. 

<!-- TBD - IS ASDK --> 
- Si l’approvisionnement d’une extension sur un déploiement de machine virtuelle prend trop de temps, les utilisateurs doivent laisser expirer le délai d’attente plutôt que d’essayer d’arrêter le processus pour désallouer ou supprimer la machine virtuelle.  

<!-- 1662991 IS ASDK --> 
- Les diagnostics de machine virtuelle Linux ne sont pas pris en charge dans Azure Stack. Lorsque vous déployez une machine virtuelle Linux en activant les diagnostics de machine virtuelle, le déploiement échoue. Le déploiement échoue également si vous activez les mesures de base de la machine virtuelle Linux dans les paramètres de diagnostic.  

<!-- 2724961- IS ASDK --> 
- Quand vous inscrivez le fournisseur de ressources **Microsoft.Insight** dans les paramètres d’abonnement, puis créez une machine virtuelle Windows en ayant activé les diagnostics du système d’exploitation invité, le graphique Pourcentage UC dans la page de vue d’ensemble de la machine virtuelle n’affiche pas les données de métriques.

   Pour trouver les données de métriques, comme le graphique de pourcentage d’UC, accédez à la fenêtre Métriques et affichez toutes les métriques d’invité de machine virtuelle Windows prises en charge.

<!-- 3507629 - IS, ASDK --> 
- La fonctionnalité Disques managés créent deux nouveaux [types de quotas de calcul](../azure-stack-quota-types.md#compute-quota-types) pour limiter la capacité maximale des disques managés qui peuvent être provisionnés. Par défaut, 2 048 Gio sont alloués pour chaque type de quota de disques managés. Toutefois, vous pouvez rencontrer les problèmes suivants :

   - Pour les quotas créés avant la mise à jour 1808, le quota de la fonctionnalité Disques managés affichera des valeurs 0 dans le portail de l’administrateur, bien que 2 048 Gio soient alloués. Vous pouvez augmenter ou diminuer la valeur en fonction de vos besoins réels, et la nouvelle valeur de quota remplace la valeur par défaut de 2 048 Gio.
   - Si vous mettez à jour la valeur de quota à 0, cela équivalut à la valeur par défaut de 2 048 Gio. En guise de solution de contournement, définissez la valeur de quota sur 1.

<!-- TBD - IS ASDK --> 
- Après avoir appliqué la mise à jour 1809, vous rencontrerez peut-être les problèmes suivants lors du déploiement de machines virtuelles avec la fonctionnalité Disques managés :

  - Si l’abonnement a été créé avant la mise à jour 1808, le déploiement d’une machine virtuelle avec la fonctionnalité Disques managés peut échouer avec un message d’erreur interne. Pour résoudre cette erreur, effectuez les étapes suivantes pour chaque abonnement :
     1. Dans le portail locataire, accédez à **Abonnements** et recherchez l’abonnement. Cliquez sur **Fournisseurs de ressources**, sur **Microsoft.Compute**, puis sur **Réinscrire**.
     2. Sous le même abonnement, accédez à **Contrôle d'accès (IAM)** et vérifiez que le rôle **AzureStack-DiskRP-Client** est répertorié.
  - Si vous avez configuré un environnement multilocataire, le déploiement de machines virtuelles dans un abonnement associé à un annuaire invité peut échouer avec un message d’erreur interne. Pour résoudre le problème, effectuez les étapes décrites dans [cet article](../azure-stack-enable-multitenancy.md#register-a-guest-directory) afin de reconfigurer chacun de vos annuaires invités.

- Une machine virtuelle 18.04 Ubuntu créée avec une autorisation SSH activée ne vous permet pas d’utiliser les clés SSH pour vous connecter. Pour contourner ce problème, utilisez un accès à la machine virtuelle pour l’extension Linux afin d’implémenter des clés SSH après l’approvisionnement, ou utilisez une authentification par mot de passe.

### <a name="networking"></a>Mise en réseau  

<!-- 1766332 - IS ASDK --> 
-  Sous **Mise en réseau**, si vous cliquez sur **Créer une passerelle VPN** pour configurer une connexion VPN, l’option **Basé sur des stratégies** s’affiche dans la liste des types de VPN. Ne sélectionnez pas cette option. Seule l’option **Basé sur itinéraires** est prise en charge dans Azure Stack.

<!-- 1902460 - IS ASDK --> 
- Azure Stack prend en charge une seule *passerelle de réseau local* par adresse IP. Cela est vrai pour tous les abonnements de locataire. Après la création de la première connexion de passerelle de réseau local, les tentatives suivantes de création d’une ressource de passerelle de réseau local avec la même adresse IP sont bloquées.

<!-- 16309153 - IS ASDK --> 
- Sur un réseau virtuel a été créé avec un paramètre de serveur DNS défini sur *Automatique*, vous ne pouvez pas choisir un serveur DNS personnalisé. Les paramètres mis à jour ne sont pas envoyés (par push) aux machines virtuelles dans ce réseau virtuel.

<!-- 2529607 - IS ASDK --> 
- Durant la *rotation des secrets* Azure Stack, une période de deux à cinq minutes s’écoule pendant laquelle les adresses IP publiques sont inaccessibles.

<!-- 2664148 - IS ASDK --> 
-   Dans les cas où le locataire accède à ses machines virtuelles à l’aide d’un tunnel VPN S2S, il peut rencontrer un scénario où les tentatives de connexion échouent si le sous-réseau local a été ajouté à la passerelle de réseau local après la création de la passerelle. 


<!-- ### SQL and MySQL-->


### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- Les utilisateurs doivent inscrire le fournisseur de ressources de stockage avant de créer leur première fonction Azure dans l’abonnement.


### <a name="usage"></a>Utilisation  

<!-- TBD - IS ASDK --> 
- Les données d’utilisation des adresses IP publiques affichent la même valeur *EventDateTime* pour chaque enregistrement au lieu de l’horodatage *TimeDate* qui s’affiche lors de la création de chaque enregistrement. Pour le moment, vous ne pouvez pas utiliser ces données pour calculer de manière précise l’utilisation des adresses IP publiques.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Télécharger la mise à jour

Vous pouvez télécharger la mise à jour 1809 d’Azure Stack à partir de [cet emplacement](https://aka.ms/azurestackupdatedownload).
  

## <a name="next-steps"></a>Étapes suivantes

- Pour passer en revue la stratégie de maintenance pour les systèmes intégrés Azure Stack et pour connaître la marche à suivre afin de conserver votre système dans un état de prise en charge, consultez [Stratégie de maintenance Azure Stack](../azure-stack-servicing-policy.md).  
- Pour utiliser le Point de terminaison privilégié (PEP) afin de surveiller et de reprendre les mises à jour, consultez [Surveiller les mises à jour dans Azure Stack à l’aide du point de terminaison privilégié](../azure-stack-monitor-update.md).  
- Pour obtenir une vue d’ensemble de la gestion des mises à jour dans Azure Stack, consultez [Gérer les mises à jour dans Azure Stack - Vue d’ensemble](../azure-stack-updates.md).  
- Pour plus d’informations sur la façon d’appliquer des mises à jour avec Azure Stack, consultez [Effectuer des mises à jour dans Azure Stack](../azure-stack-apply-updates.md).