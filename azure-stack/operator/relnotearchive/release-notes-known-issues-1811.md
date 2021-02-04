---
title: Mise à jour 1811 d’Azure Stack | Microsoft Docs
description: 'Découvrez la mise à jour 1811 pour les systèmes intégrés Azure Stack : nouveautés, problèmes connus et emplacement de téléchargement.'
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
ms.date: 04/09/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 02/28/2019
ms.openlocfilehash: 76a9909ac35197d8e51115e07d977a0ee39442b5
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248628"
---
# <a name="azure-stack-1811-update"></a>Mise à jour 1811 d’Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Cet article décrit le contenu de la mise à jour 1811. La mise à jour inclut des améliorations, des corrections de bogues et de nouvelles fonctionnalités pour cette version d’Azure Stack. Cet article décrit également les problèmes connus dans cette version, et contient un lien permettant de télécharger la mise à jour. Les problèmes connus sont divisés en problèmes directement liés au processus de mise à jour et problèmes propres à la build (après installation).

> [!IMPORTANT]  
> Cette mise à jour est destinée uniquement aux systèmes intégrés d’Azure Stack. N’appliquez pas cette mise à jour au Kit de développement Azure Stack.

## <a name="build-reference"></a>Référence de build

Le numéro de build de la mise à jour 1811 d’Azure Stack est **1.1811.0.101**.

## <a name="hotfixes"></a>Correctifs

Azure Stack publie des correctifs logiciels à intervalles réguliers. Avant d’installer la mise à jour 1811 d’Azure Stack, veillez à installer le [dernier correctif logiciel d’Azure Stack](#azure-stack-hotfixes) pour la build 1809.

### <a name="azure-stack-hotfixes"></a>Correctifs logiciels d’Azure Stack

- **1809** : [KB 4481548 – Correctif logiciel d’Azure Stack 1.1809.12.114](https://support.microsoft.com/help/4481548/)
- **1811** : Aucun correctif logiciel actuellement disponible.

## <a name="prerequisites"></a>Prérequis

> [!IMPORTANT]
> Pendant l’installation de la mise à jour 1811, vous devez vérifier que toutes les instances du portail administrateur sont fermées. Le portail utilisateur peut rester ouvert, mais le portail administrateur doit être fermé.

- Préparez votre déploiement d’Azure Stack pour l’hôte d’extension d’Azure Stack. Préparez votre système en suivant les instructions suivantes : [Préparez l’hôte d’extension pour Azure Stack](../azure-stack-extension-host-prepare.md). 
 
- Avant d’installer la mise à jour 1811, installez le [dernier correctif logiciel d’Azure Stack](#azure-stack-hotfixes) pour la build 1809.

- Avant de démarrer l’installation de cette mise à jour, exécutez [Test-AzureStack](../azure-stack-diagnostic-test.md) avec les paramètres suivants pour valider l’état de votre Azure Stack et résoudre les éventuels problèmes opérationnels détectés, y compris tous les avertissements et les échecs. Examinez aussi les alertes actives et résolvez toutes celles qui nécessitent une intervention.  

    ```powershell
    Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

    Si la configuration requise pour l’hôte d’extension n’est pas satisfaite, la sortie de la commande `Test-AzureStack` affiche le message suivant : 
  
    `To proceed with installation of the 1811 update, you will need to import 
    the SSL certificates required for Extension Host, which simplifies network 
    integration and increases the security posture of Azure Stack. Refer to this 
    link to prepare for Extension Host:
    https://docs.microsoft.com/azure-stack/operator/azure-stack-extension-host-prepare`

- Pour pouvoir installer la mise à jour 1811 d’Azure Stack, vous devez avoir au préalable correctement importé les certificats d’hôte d’extension obligatoires dans votre environnement Azure Stack. Pour installer la mise à jour 1811, vous devez importer les certificats SSL requis pour l’hôte d’extension. Pour savoir comment importer les certificats, voir [cette section](../azure-stack-extension-host-prepare.md#import-extension-host-certificates).

    Si vous persistez à ignorer tous les avertissements préalables à l’installation de la mise à jour 1811, celle-ci échouera après environ une heure, et vous recevrez le message suivant :   
 
    `The required SSL certificates for the Extension Host have not been found.
    The Azure Stack update will halt. Refer to this link to prepare for 
    Extension Host: https://docs.microsoft.com/azure-stack/operator/azure-stack-extension-host-prepare,
    then resume the update.
    Exception: The Certificate path does not exist: [certificate path here]` 
 
    Après avoir correctement importé les certificats d’hôte d’extension obligatoires, vous pouvez relancer la mise à jour 1811 à partir du portail administrateur. Même si Microsoft conseille aux opérateurs Azure Stack de planifier une fenêtre de maintenance pendant le processus de mise à jour, une défaillance due à l’absence de certificats d’hôte d’extension ne devrait pas avoir d’incidence sur les charges de travail ou les services existants.  

    Lors de l’installation de cette mise à jour, le portail utilisateur d’Azure Stack est indisponible pendant la configuration de l’hôte d’extension. La configuration de l’hôte d’extension peut prendre jusqu’à 5 heures. Pendant ce temps, vous pouvez vérifier l’état d’une mise à jour ou reprendre l’installation d’une mise à jour ayant échoué à l’aide [d’Azure Stack Administrator PowerShell ou du point de terminaison privilégié](../azure-stack-monitor-update.md).

- Lorsqu’Azure Stack est géré par System Center Operations Manager (SCOM), veillez à mettre à jour le Pack d’administration pour Microsoft Azure Stack vers la version 1.0.3.11 avant d’appliquer la version 1811.

## <a name="new-features"></a>Nouvelles fonctionnalités

Cette mise à jour inclut les nouvelles fonctionnalités et améliorations suivantes pour Azure Stack :

- Avec cette publication, l’[hôte d’extension](../azure-stack-extension-host-prepare.md) est activé. L’hôte d’extension simplifie l’intégration du réseau et améliore la situation de sécurité d’Azure Stack.

- Ajout de la prise en charge de l’authentification des appareils avec les services de fédération Active Directory (AD FS), en particulier lors de l’utilisation d’Azure CLI. Pour plus d’informations, voir [Utiliser des profils de version des API avec Azure CLI dans Azure Stack](../../user/azure-stack-version-profiles-azurecli2.md).

- Ajout de la prise en charge des principaux de service utilisant une clé secrète client avec les services de fédération Active Directory (AD FS). Pour plus d’informations, voir [Créer un principal de service pour AD FS](../azure-stack-create-service-principals.md#manage-an-azure-ad-app-identity).

- Cette publication ajoute la prise en charge des versions d’API de service de stockage Azure suivantes : **2017-07-29**, **2017-11-09**. Est également ajoutée la prise en charge des versions d’API de fournisseur de ressources de stockage Azure suivantes : **2016-05-01**, **2016-12-01**, **2017-06-01** et **2017-10-01**. Pour plus d’informations, consultez [Stockage Azure Stack : différences et considérations](../../user/azure-stack-acs-differences.md).

- Ajout de nouvelles commandes de point de terminaison privilégié pour la mise à jour et la suppression de principaux de service pour ADFS. Pour plus d’informations, voir [Créer un principal de service pour AD FS](../azure-stack-create-service-principals.md#manage-an-azure-ad-app-identity).

- Ajout de nouvelles opérations de nœud d’unité d’échelle qui permettent à un opérateur Azure Stack de démarrer, d’arrêter et de fermer un nœud d’unité d’échelle. Pour plus d’informations, voir [Mettre à l’échelle des actions de nœud d’unité dans Azure Stack](../azure-stack-node-actions.md).

- Ajout d’un nouveau panneau de propriétés de région qui affiche les détails d’inscription de l’environnement. Vous pouvez afficher ces informations en cliquant sur la mosaïque de **gestion des régions** sur le tableau de bord par défaut dans le portail administrateur, puis en sélectionnant **Propriétés**.

- Ajout d’une nouvelle commande de point de terminaison privilégié pour mettre à jour les informations d’identification BMC avec un nom d’utilisateur et mot de passe utilisés pour communiquer avec les machines physiques. Pour plus d’informations, voir [Mettre à jour les informations d’identification du contrôleur de gestion de la carte de base \(BMC)](../azure-stack-rotate-secrets.md).

- Ajout de la possibilité d’accéder à la feuille de route Azure via l’icône d’aide et de support (point d’interrogation) dans l’angle supérieur droit des portails administrateur et utilisateur, comme dans le portail Microsoft Azure.

- Amélioration de l’expérience de gestion de la Place de marché pour les utilisateurs déconnectés. Le processus de chargement pour publier un élément sur la Place de marché dans un environnement déconnecté est réduit à une seule étape, au lieu de devoir charger séparément l’image et le package de la Place de marché. Le produit téléchargé est également visible dans le panneau de gestion de la Place de marché.

- Cette publication réduit la fenêtre de maintenance requise pour la rotation des secrets en ajoutant la possibilité de ne faire tourner que les certificats externes durant la [rotation des secrets d’Azure Stack](../azure-stack-rotate-secrets.md).

- [Azure Stack PowerShell](../azure-stack-powershell-install.md) a été mis à jour vers la version 1.6.0. La mise à jour prend en charge les nouvelles fonctionnalités de stockage dans Azure Stack. Pour plus d’informations, voir les notes de publication du [Module d’administration Azure Stack 1.6.0 dans PowerShell Gallery](https://www.powershellgallery.com/packages/AzureStack/1.6.0). Pour plus d’informations sur la mise à jour ou l’installation d’Azure Stack PowerShell, voir [Installer PowerShell pour Azure Stack](../azure-stack-powershell-install.md).

- La fonctionnalité Disques managés est désormais activée par défaut lors de la création de machines virtuelles via le portail Azure Stack. Pour connaître les étapes supplémentaires requises pour les disques managés afin d’éviter des échecs de création de machine virtuelle, voir la section [Problèmes connus](#known-issues-post-installation).

- Cette publication introduit des actions d’alerte de type **Réparer** pour l’opérateur Azure Stack. Certaines alertes dans 1811 incluent un bouton **Réparer** que vous pouvez actionner pour résoudre le problème. Pour plus d’informations, voir [Surveiller l’intégrité et les alertes dans Azure Stack](../azure-stack-monitor-health.md).

- Nouvelle expérience de mise à jour dans Azure Stack. Voici quelques-unes des améliorations de la mise à jour : 
  - Onglets scindant les mises à jour de l'historique des mises à jour pour mieux suivre les mises à jour en cours et terminées.
  - Visualisations améliorées des états dans la section des Essentials avec de nouvelles icônes et une nouvelle présentation pour les versions actuelles et OEM, ainsi que la date de la dernière mise à jour.
  - Le lien **Afficher** de la colonne Notes de publication permet à l'utilisateur d'accéder directement à la documentation spécifique à la mise à jour, et non à la page de mise à jour générique.
  - L'onglet **Historique des mises à jour** permet de déterminer le temps d'exécution de chaque mise à jour et propose des fonctionnalités de filtrage améliorées.  
  - Les unités d'échelle Azure Stack connectées reçoivent automatiquement la **Mise à jour disponible**.
  - Les unités d’échelle Azure Stack non connectées peuvent importer les mises à jour comme précédemment. 
  - Le processus de téléchargement des journaux d’activité JSON à partir du portail reste inchangé. Les opérateurs Azure Stack pourront suivre la progression des différentes étapes.

    Pour plus d’informations, consultez [Effectuer des mises à jour dans Azure Stack](../azure-stack-apply-updates.md).


## <a name="fixed-issues"></a>Problèmes résolus

<!-- TBD - IS ASDK --> 
- Résolution du problème des données d’utilisation des adresses IP publiques qui affichaient la même valeur **EventDateTime** pour chaque enregistrement au lieu de l’horodatage **TimeDate** qui s’affiche lors de la création de l’enregistrement. Vous pouvez désormais utiliser ces données pour calculer de manière précise l’utilisation des adresses IP publiques.

<!-- 3099544 – IS, ASDK --> 
- Résolution d’un problème qui se produisait lors de la création d’une machine virtuelle à l’aide du portail Azure Stack. La sélection de la taille de machine virtuelle avait pour effet que la colonne **USD/mois** affichait le message **Indisponible**. Cette colonne ne s’affiche plus, car l’affichage de la colonne des prix des machines virtuelles n’est pas pris en charge dans Azure Stack.

<!-- 2930718 - IS ASDK --> 
- Résolution d’un problème dans le portail administrateur où, quand vous accédiez aux détails d’un abonnement utilisateur, après que vous aviez fermé le panneau et cliqué sur **Récent**, le nom de l’abonnement utilisateur n’apparaissait pas. Désormais, le nom d’abonnement utilisateur apparaît.

<!-- 3060156 - IS ASDK --> 
- Résolution d’un problème dans les portails administrateur et utilisateur où, quand vous cliquiez sur les paramètres du portail et sélectionniez l’option **Supprimer tous les paramètres et tableaux de bord privés**, cette action ne produisait pas le résultat escompté et entraînait l’affichage d’une notification d’erreur. Cette option fonctionne désormais correctement.

<!-- 2930799 - IS ASDK --> 
- Résolution d’un problème dans les portails administrateur et utilisateur où, sous **Tous les services**, l’élément **Plans de protection DDoS** n’était pas correctement répertorié. Il n’est pas disponible dans Azure Stack. L’affichage de la liste a été supprimé.
 
<!--2760466 – IS  ASDK --> 
- Correction d’un problème qui avait pour effet que, quand vous installiez un nouvel environnement Azure Stack, l’alerte **Activation requise** ne s’affichait pas. Elle s’affiche désormais correctement.

<!--1236441 – IS  ASDK --> 
- Correction d’un problème qui empêchait l’application de stratégies de contrôle d’accès en fonction du rôle (RBAC) à un groupe d’utilisateurs lors de l’utilisation d’ADFS.

<!--3463840 - IS, ASDK --> 
- Correction d’un problème d’échec des sauvegardes d’infrastructure en raison d’un serveur de fichiers inaccessible à partir du réseau d’adresses IP virtuelles publiques. Ce correctif replace le service de sauvegarde d’infrastructure (Infrastructure Backup) dans le réseau d’infrastructure publique. Si vous avez appliqué le dernier [correctif logiciel Azure Stack pour 1809](#azure-stack-hotfixes) qui traite ce problème, la mise à jour 1811 n’apportera pas d’autre modification. 

<!-- 2967387 – IS, ASDK --> 
- Correction d’un problème qui avait pour effet que le compte que vous utilisiez pour vous connecter au portail administrateur ou utilisateur d’Azure Stack s’affichait en tant qu’**Utilisateur non identifié**. Ce message s’affichait quand le **prénom** ou le **nom** du compte n’étaient pas spécifiés.   

<!--  2873083 - IS ASDK --> 
- Correction d’un problème qui avait pour effet que, quand vous utilisiez le portail pour créer un groupe de machines virtuelles identiques (VMSS), la liste déroulante **Taille d’instance** ne se chargeait pas correctement dans Internet Explorer. Ce navigateur fonctionne désormais correctement.  

<!-- 3190553 - IS ASDK -->
- Correction d’un problème qui générait des alertes bruyantes indiquant qu’une instance de rôle d’infrastructure était indisponible ou qu’un nœud d’unité d’échelle était hors connexion.

<!-- 2724961 - IS ASDK -->
- Correction d'un problème qui empêchait la page de présentation des machines virtuelles d'afficher correctement le graphique des métriques des machines virtuelles. 

## <a name="changes"></a>Modifications

- La mise à jour 1811 introduit une nouvelle façon d’afficher et de modifier les quotas dans une offre. Pour plus d’informations, voir [Afficher un quota existant](../azure-stack-quota-types.md#view-an-existing-quota).

<!-- 3083238 IS -->
- Les améliorations en matière de sécurité introduites dans cette mise à jour entraînent une augmentation de la taille de sauvegarde du rôle service d’annuaire. Pour obtenir des conseils actualisés concernant le dimensionnement de l’emplacement de stockage externe, voir la [documentation sur la sauvegarde d’infrastructure](../azure-stack-backup-reference.md#storage-location-sizing). Cette modification a pour effet d’allonger la durée de la sauvegarde en raison de l’accroissement du volume des données transférées. Cette modification a une incidence sur les systèmes intégrés. 

- L’applet de commande PEP existante pour récupérer les clés de récupération BitLocker est renommée de Get-AzsCsvsRecoveryKeys en Get-AzsRecoveryKeys dans la mise à jour 1811. Pour plus d’informations sur la façon d’obtenir les clés de récupération BitLocker, voir les [instructions sur la façon d’obtenir les clés](../azure-stack-security-bitlocker.md).

## <a name="common-vulnerabilities-and-exposures"></a>Vulnérabilités et menaces courantes

Cette mise à jour installe les mises à jour de sécurité suivantes :  

- [CVE-2018-8256](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8256)
- [CVE-2018-8407](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8407)
- [CVE-2018-8408](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8408)
- [CVE-2018-8415](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8415)
- [CVE-2018-8417](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8417)
- [CVE-2018-8450](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8450)
- [CVE-2018-8471](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8471)
- [CVE-2018-8476](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8476)
- [CVE-2018-8485](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8485)
- [CVE-2018-8544](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8544)
- [CVE-2018-8547](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8547)
- [CVE-2018-8549](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8549)
- [CVE-2018-8550](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8550)
- [CVE-2018-8553](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8553)
- [CVE-2018-8561](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8561)
- [CVE-2018-8562](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8562)
- [CVE-2018-8565](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8565)
- [CVE-2018-8566](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8566)
- [CVE-2018-8584](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8584)

Pour plus d'informations sur ces vulnérabilités, cliquez sur les liens précédents ou consultez l'article [4478877](https://support.microsoft.com/help/4478877) de la Base de connaissances Microsoft.

## <a name="known-issues-with-the-update-process"></a>Problèmes connus avec le processus de mise à jour

- Si vous exécutez la cmdlet PowerShell **Get-AzureStackLog** après avoir exécuté **Test-AzureStack** dans la même session de point de terminaison privilégié, **Get-AzureStackLog** échoue. Pour contourner ce problème, fermez la session de point de terminaison privilégié dans laquelle vous avez exécuté **Test-AzureStack**, puis ouvrez une nouvelle session pour exécuter **Get-AzureStackLog**.

- Durant l’installation de la mise à jour 1811, assurez-vous que toutes les instances du portail administrateur sont fermées. Le portail utilisateur peut rester ouvert, mais le portail administrateur doit être fermé.

- Lors de l’exécution de la commande [Test-AzureStack](../azure-stack-diagnostic-test.md), si le test **AzsInfraRoleSummary** ou **AzsPortalApiSummary** échoue, vous êtes invité à exécuter la commande **Test-AzureStack** avec l’indicateur `-Repair`.  Si vous exécutez cette commande, elle échoue et le message d’erreur suivant s’affiche :  `Unexpected exception getting Azure Stack health status. Cannot bind argument to parameter 'TestResult' because it is null.` Ce problème sera résolu dans une publication ultérieure.

- Lors de l’installation de la mise à jour 1811, le portail d’utilisation Azure Stack est indisponible durant la configuration de l’hôte d’extension. La configuration de l’hôte d’extension peut prendre jusqu’à 5 heures. Pendant ce temps, vous pouvez vérifier l’état d’une mise à jour ou reprendre l’installation d’une mise à jour ayant échoué à l’aide [d’Azure Stack Administrator PowerShell ou du point de terminaison privilégié](../azure-stack-monitor-update.md). 

- Pendant l’installation de la mise à jour 1811, il se peut que le tableau de bord du portail utilisateur soit indisponible et que des personnalisations soient perdues. Vous pouvez restaurer le tableau de bord par défaut une fois la mise à jour terminée en accédant aux paramètres du portail, puis en sélectionnant **Restaurer les paramètres par défaut**.

- Quand vous exécutez la commande [Test-AzureStack](../azure-stack-diagnostic-test.md), un message d’avertissement du contrôleur de gestion de la carte de base (BMC) s’affiche. Vous pouvez ignorer cet avertissement sans problème.

- <!-- 2468613 - IS --> Pendant l’installation de cette mise à jour, il se peut que des alertes intitulées `Error – Template for FaultType UserAccounts.New is missing.` s’affichent. Vous pouvez les ignorer. Elles disparaîtront automatiquement une fois l’installation de cette mise à jour terminée.

- <!-- 3139614 | IS -->  Si vous avez appliqué à Azure Stack une mise à jour provenant de votre fabricant d’ordinateurs (OEM), il se peut que la notification **Mise à jour disponible** n’apparaisse pas sur le portail administrateur d’Azure Stack. Pour installer la mise à jour Microsoft, téléchargez et importez manuellement cette mise à jour en suivant les instructions indiquées ici [Effectuer des mises à jour dans Azure Stack](../azure-stack-apply-updates.md).

## <a name="post-update-steps"></a>Étapes après la mise à jour

- Après l’installation de cette mise à jour, installez les correctifs logiciels applicables. Pour plus d’informations, voir [Correctifs logiciels](#hotfixes) ainsi que notre [Stratégie de maintenance](../azure-stack-servicing-policy.md).  

- Récupérez les clés de chiffrement de données au repos et stockez-les en toute sécurité hors de votre déploiement Azure Stack. Suivez les [instructions sur la façon de récupérer les clés](../azure-stack-security-bitlocker.md).

## <a name="known-issues-post-installation"></a>Problèmes connus (après l’installation)

Les éléments suivants sont des problèmes connus qui apparaissent après l’installation de cette build.

### <a name="portal"></a>Portail

<!-- 2930820 - IS ASDK --> 
- Dans les portails d’administration et utilisateur, si vous recherchez « Docker », l’élément est incorrectement retourné dans les résultats. Il n’est pas disponible dans Azure Stack. Si vous essayez de le créer, un panneau indiquant une erreur s’affiche. 

<!-- 2931230 – IS  ASDK --> 
- Les plans ajoutés à un abonnement utilisateur comme plan d’extension ne peuvent pas être supprimés, même quand vous supprimez le plan de l’abonnement utilisateur. Le plan est conservé jusqu’à ce que les abonnements qui référencent le plan d’extension soient aussi supprimés. 

<!-- TBD - IS ASDK --> 
- Les deux types d’abonnements d’administration qui ont été introduits avec la version 1804 ne doivent pas être utilisés. Les types d’abonnements sont **Abonnement de contrôle** et **Abonnement de consommation**. Ces types d’abonnements sont visibles dans les nouveaux environnements Azure Stack depuis la version 1804, mais ils ne sont pas encore prêts à être utilisés. Vous devez continuer à utiliser le type d’abonnement **Fournisseur par défaut**.

<!-- TBD - IS ASDK --> 
- La suppression d’abonnements utilisateur aboutit à des ressources orphelines. Pour contourner ce problème, commencez pas supprimer des ressources d’utilisateurs ou la totalité du groupe de ressources, puis supprimez les abonnements utilisateur.

<!-- TBD - IS ASDK --> 
- Vous ne pouvez pas afficher les autorisations définies pour votre abonnement à l’aide des portails Azure Stack. Pour contourner ce problème, utilisez [PowerShell pour vérifier les autorisations](/powershell/module/azs.subscriptions.admin/get-azssubscriptionplan).

### <a name="health-and-monitoring"></a>Intégrité et surveillance

<!-- 1264761 - IS ASDK -->  
-   Vous risquez de recevoir des alertes pour le composant du **contrôleur d’intégrité** contenant les informations suivantes :  

  - Alerte 1 :
     - NOM : Rôle d’infrastructure défectueux
     - GRAVITÉ : Avertissement
     - COMPOSANT : Contrôleur d’intégrité
     - DESCRIPTION : L’analyseur de pulsations du contrôleur d’intégrité n’est pas disponible. Cela peut affecter les rapports et les métriques d’intégrité.  

  - Alerte 2 :
     - NOM : Rôle d’infrastructure défectueux
     - GRAVITÉ : Avertissement
     - COMPOSANT : Contrôleur d’intégrité
     - DESCRIPTION : L’analyseur d’erreur du contrôleur d’intégrité n’est pas disponible. Cela peut affecter les rapports et les métriques d’intégrité.

    Ces deux alertes peuvent être ignorées en toute sécurité. Elles se ferment automatiquement dans le temps.  

### <a name="compute"></a>Calcul

- Lors de la création d’une machine virtuelle Windows, vous devez sélectionner un port d’entrée public dans le panneau **Paramètres** pour pouvoir continuer. Dans la mise à jour 1811, ce paramètre est obligatoire, mais n’a aucun effet. En effet, cette fonctionnalité dépend du Pare-feu Azure qui n’est pas implémenté dans Azure Stack. Vous pouvez sélectionner **Aucun port d’entrée public** ou toute autre option pour poursuivre la création de machine virtuelle. Le paramètre n’a aucun effet.

- Lors de la création d'une machine virtuelle Windows, l’erreur suivante peut s’afficher :

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   Cette erreur se produit si vous activez les diagnostics de démarrage sur une machine virtuelle alors que vous avez supprimé votre compte de stockage des diagnostics de démarrage. Pour contourner ce problème, recréez le compte de stockage avec le même nom que celui utilisé précédemment.

- Lorsque vous créez une [machine virtuelle de série Dv2](../../user/azure-stack-vm-considerations.md#vm-sizes), les machines virtuelles D11-14v2 vous autorisent à créer 4, 8, 16 et 32 disques de données respectivement. Toutefois, le volet Créer une machine virtuelle affiche 8, 16, 32 et 64 disques de données.

- Les enregistrements d'utilisation sur Azure Stack peuvent contenir des majuscules inattendues ; par exemple :

   `{"Microsoft.Resources":{"resourceUri":"/subscriptions/<subid>/resourceGroups/ANDREWRG/providers/Microsoft.Compute/
   virtualMachines/andrewVM0002","location":"twm","tags":"null","additionalInfo":
   "{\"ServiceType\":\"Standard_DS3_v2\",\"ImageType\":\"Windows_Server\"}"}}`

   Dans cet exemple, le nom du groupe de ressources doit être **AndrewRG**. Vous pouvez ignorer cette incohérence.

<!-- 3235634 – IS, ASDK -->
- Pour déployer des machines virtuelles dont les tailles contiennent un suffixe **v2** (par exemple, **Standard_A2_v2**), spécifiez le suffixe sous la forme **Standard_A2_v2** (v minuscule). N’utilisez pas **Standard_A2_V2** (V majuscule). Cette méthode fonctionne dans Azure global et constitue une incohérence sur Azure Stack.

<!-- 2869209 – IS, ASDK --> 
- Quand vous utilisez [l’applet de commande **Add-AzsPlatformImage**](/powershell/module/azs.compute.admin/add-azsplatformimage), vous devez spécifier le paramètre **-OsUri** comme URI du compte de stockage où le disque est chargé. Si vous utilisez le chemin local du disque, l’applet de commande échoue avec l’erreur suivante : 

    `Long running operation failed with status 'Failed'`

<!--  2795678 – IS, ASDK --> 
- Quand vous utilisez le portail pour créer des machines virtuelles de taille Premium (DS, Ds_v2, FS, FSv2), celles-ci sont créées dans un compte de stockage standard. La création dans un compte de stockage standard n’a pas d’impact sur les fonctionnalités, l’IOP ou la facturation. Vous pouvez sans risque ignorer l’avertissement qui indique : 

    `You've chosen to use a standard disk on a size that supports premium disks. This could impact operating system performance and is not recommended. Consider using premium storage (SSD) instead.`

<!-- 2967447 - IS, ASDK --> 
- Quand vous créez un groupe de machines virtuelles identiques, l’option CentOS 7.2 est proposée pour le déploiement. Étant donné que cette image n’est pas disponible sur Azure Stack, sélectionnez un autre système d’exploitation pour votre déploiement, ou choisissez un modèle Azure Resource Manager spécifiant une autre image CentOS qui a été téléchargée par l’opérateur avant le déploiement à partir de la Place de marché.  

<!-- 2724873 - IS --> 
-  En utilisant les cmdlets PowerShell **Start-AzsScaleUnitNode** ou **Stop-AzsScaleunitNode** pour gérer les unités d’échelle, il est possible que la première tentative de démarrage ou d’arrêt de l’unité d’échelle échoue. Si la cmdlet échoue une première fois, exécutez-la une seconde fois. La deuxième exécution doit accomplir correctement l’opération. 

<!-- TBD - IS ASDK --> 
- Si l’approvisionnement d’une extension sur un déploiement de machine virtuelle prend trop de temps, laissez expirer le délai d’attente de l’approvisionnement au lieu d’essayer d’arrêter le processus pour désallouer ou supprimer la machine virtuelle.  

<!-- 1662991 IS ASDK --> 
- Les diagnostics de machine virtuelle Linux ne sont pas pris en charge dans Azure Stack. Lorsque vous déployez une machine virtuelle Linux en activant les diagnostics de machine virtuelle, le déploiement échoue. Le déploiement échoue également si vous activez les mesures de base de la machine virtuelle Linux dans les paramètres de diagnostic.  

<!-- 3507629 - IS, ASDK --> 
- La fonctionnalité Disques managés créent deux nouveaux [types de quotas de calcul](../azure-stack-quota-types.md#compute-quota-types) pour limiter la capacité maximale des disques managés qui peuvent être provisionnés. Par défaut, 2 048 Gio sont alloués pour chaque type de quota de disques managés. Toutefois, vous pouvez rencontrer les problèmes suivants :

   - Pour les quotas créés avant la mise à jour 1808, le quota de la fonctionnalité Disques managés affichera des valeurs 0 dans le portail de l’administrateur, bien que 2 048 Gio soient alloués. Vous pouvez augmenter ou diminuer la valeur en fonction de vos besoins réels, et la nouvelle valeur de quota remplace la valeur par défaut de 2 048 Gio.
   - Si vous mettez à jour la valeur de quota à 0, cela équivalut à la valeur par défaut de 2 048 Gio. En guise de solution de contournement, définissez la valeur de quota sur 1.

<!-- TBD - IS ASDK --> 
- Après avoir appliqué la mise à jour 1811, il se peut que vous rencontriez les problèmes suivants lors du déploiement de machines virtuelles avec la fonctionnalité Disques managés :

   - Si l’abonnement a été créé avant la mise à jour 1808, le déploiement d’une machine virtuelle avec la fonctionnalité Disques managés peut échouer avec un message d’erreur interne. Pour résoudre cette erreur, effectuez les étapes suivantes pour chaque abonnement :
      1. Dans le portail locataire, accédez à **Abonnements** et recherchez l’abonnement. Sélectionnez **Fournisseurs de ressources**, **Microsoft.Compute**, puis **Réinscrire**.
      2. Sous le même abonnement, accédez à **Contrôle d'accès (IAM)** et vérifiez que le rôle **AzureStack-DiskRP-Client** est répertorié.
   - Si vous avez configuré un environnement multilocataire, le déploiement de machines virtuelles dans un abonnement associé à un annuaire invité peut échouer avec un message d’erreur interne. Pour résoudre le problème, effectuez les étapes décrites dans [cet article](../azure-stack-enable-multitenancy.md#register-azure-stack-hub-with-the-guest-directory) afin de reconfigurer chacun de vos annuaires invités.

- Une machine virtuelle 18.04 Ubuntu créée avec une autorisation SSH activée ne vous permet pas d’utiliser les clés SSH pour vous connecter. Pour contourner ce problème, utilisez un accès à la machine virtuelle pour l’extension Linux afin d’implémenter des clés SSH après l’approvisionnement, ou utilisez une authentification par mot de passe.

### <a name="networking"></a>Mise en réseau  

<!-- 1766332 - IS ASDK --> 
-  Sous **Mise en réseau**, si vous cliquez sur **Créer une passerelle VPN** pour configurer une connexion VPN, l’option **Basé sur des stratégies** s’affiche dans la liste des types de VPN. Ne sélectionnez pas cette option. Seule l’option **Basé sur itinéraires** est prise en charge dans Azure Stack.

<!-- 1902460 - IS ASDK --> 
- Azure Stack prend en charge une seule *passerelle de réseau local* par adresse IP. Cela est vrai pour tous les abonnements de locataire. Après la création de la première connexion de passerelle de réseau local, les tentatives suivantes de création d’une ressource de passerelle de réseau local avec la même adresse IP sont rejetées.

<!-- 16309153 - IS ASDK --> 
- Sur un réseau virtuel créé avec un paramètre de serveur DNS défini sur **Automatique**, la modification d’un serveur DNS personnalisé échoue. Les paramètres mis à jour ne sont pas envoyés (par push) aux machines virtuelles dans ce réseau virtuel.

<!-- 2529607 - IS ASDK --> 
- Durant la *rotation des secrets* Azure Stack, une période de deux à cinq minutes s’écoule pendant laquelle les adresses IP publiques sont inaccessibles.

<!-- 2664148 - IS ASDK --> 
- Lorsque le locataire accède à des machines virtuelles à l’aide d’un tunnel VPN S2S, il peut être confronté à une situation où les tentatives de connexion échouent si le sous-réseau local a été ajouté à la passerelle de réseau local après la création de la passerelle. 

- Dans le portail Azure Stack, lorsque vous modifiez une adresse IP statique pour une configuration IP liée à une carte réseau connectée à une instance de machine virtuelle, le message d’avertissement suivant s’affiche : 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`. 

    Vous pouvez ignorer ce message. L’adresse IP changera même si l’instance de machine virtuelle ne redémarre pas.

- Sur le portail, le panneau **Propriétés de réseau** contient un lien **Obtenir les règles de sécurité effectives** pour chaque carte réseau. Si vous sélectionnez ce lien, un nouveau panneau s’ouvre qui affiche le message d’erreur `Not Found.`. Cette erreur se produit parce qu’Azure Stack ne prend pas encore en charge la fonction **Obtenir les règles de sécurité effectives**.

- Dans le portail, si vous ajoutez une règle de sécurité de trafic entrant et sélectionnez **Balise du service** en tant que source, plusieurs options s’affichent dans la liste **Balise source**, qui ne sont pas disponibles pour Azure Stack. Les seules options valides dans Azure Stack sont les suivantes :

  - **Internet**
  - **VirtualNetwork**
  - **AzureLoadBalancer**
  
    Les autres options ne sont pas pris en charge en tant que balises sources dans Azure Stack. De même, si vous ajoutez une règle de sécurité de trafic sortant et sélectionnez **Balise du service** en tant que destination, la même liste d’options s’affiche pour **Balise source**. Les seules options valides sont les mêmes que pour **Balise source**, comme décrit dans la liste précédente.

- L’applet de commande PowerShell **New-AzureRmIpSecPolicy** ne prend pas en charge la valeur **DHGroup24** pour le paramètre `DHGroup`.

- Les Groupes de sécurité réseau (NSG) ne fonctionnent pas de la même manière dans Azure Stack que dans le reste d'Azure. Dans Azure, vous pouvez définir plusieurs ports sur une règle NSG (en utilisant les modèles du portail, de PowerShell et de Resource Manager). Dans Azure Stack, via le portail, vous ne pouvez définir qu'un seul port sur une règle NSG. Pour contourner ce problème, utilisez un modèle Resource Manager qui vous permettra de définir ces autres règles.

### <a name="infrastructure-backup"></a>Infrastructure Backup

<!--scheduler config lost, bug 3615401, new issue in 1811,  hectorl-->
<!-- TSG: https://www.csssupportwiki.com/index.php/Azure_Stack/KI/Backup_scheduler_configuration_lost --> 
- Après activation des sauvegardes automatiques, le service du planificateur passe à l’état désactivé de façon inattendue. Le service du contrôleur de sauvegarde détecte que les sauvegardes automatiques sont désactivées et déclenche un avertissement dans le portail administrateur. Cet avertissement est attendu quand les sauvegardes automatiques sont désactivées. 
    - Cause : Ce problème est dû à un bogue dans le service qui entraîne la perte de la configuration du planificateur. Ce bogue ne modifie pas l’emplacement de stockage, le nom d’utilisateur, le mot de passe ou la clé de chiffrement.   
    - Correction : Pour résoudre ce problème, ouvrez le panneau des paramètres du contrôleur de sauvegarde dans le fournisseur de ressources Infrastructure Backup, puis sélectionnez **Activer les sauvegardes automatiques**. Veillez à définir la fréquence et la période de rétention souhaitées.
    - Occurrence : Faible 

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

Vous pouvez télécharger la mise à jour 1811 d’Azure Stack à partir de [cet emplacement](https://aka.ms/azurestackupdatedownload). 

Dans les scénarios connectés, et uniquement dans ceux-ci, les déploiements Azure Stack consultent régulièrement un point de terminaison sécurisé et vous avertissent automatiquement si une mise à jour est disponible pour votre cloud. Pour plus d’informations, voir la documentation sur la [gestion des mises à jour pour Azure Stack](../azure-stack-updates.md#how-to-know-an-update-is-available).

## <a name="next-steps"></a>Étapes suivantes

- Pour passer en revue la stratégie de maintenance pour les systèmes intégrés Azure Stack et pour connaître la marche à suivre afin de conserver votre système dans un état de prise en charge, consultez [Stratégie de maintenance Azure Stack](../azure-stack-servicing-policy.md).  
- Pour utiliser le Point de terminaison privilégié (PEP) afin de surveiller et de reprendre les mises à jour, consultez [Surveiller les mises à jour dans Azure Stack à l’aide du point de terminaison privilégié](../azure-stack-monitor-update.md).  
- Pour obtenir une vue d’ensemble de la gestion des mises à jour dans Azure Stack, consultez [Gérer les mises à jour dans Azure Stack - Vue d’ensemble](../azure-stack-updates.md).  
- Pour plus d’informations sur la façon d’appliquer des mises à jour avec Azure Stack, consultez [Effectuer des mises à jour dans Azure Stack](../azure-stack-apply-updates.md).  