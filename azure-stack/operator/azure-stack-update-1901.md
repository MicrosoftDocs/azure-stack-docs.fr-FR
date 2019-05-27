---
title: Mise à jour 1901 d’Azure Stack | Microsoft Docs
description: 'Découvrez la mise à jour 1901 pour les systèmes intégrés Azure Stack : nouveautés, problèmes connus et emplacement de téléchargement.'
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
ms.lastreviewed: 03/27/2019
ms.openlocfilehash: 1fcdcdc4f592056ce3da5074b2371fde91b47c85
ms.sourcegitcommit: 426380a3a27954cd609ba52d1066d9d69f5267fe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65532380"
---
# <a name="azure-stack-1901-update"></a>Mise à jour 1901 d’Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Cet article décrit le contenu de la mise à jour 1901. La mise à jour inclut des améliorations, des corrections de bogues et de nouvelles fonctionnalités pour cette version d’Azure Stack. Cet article décrit également les problèmes connus dans cette version, et contient un lien permettant de télécharger la mise à jour. Les problèmes connus sont divisés en problèmes directement liés au processus de mise à jour et problèmes propres à la build (après installation).

> [!IMPORTANT]  
> Cette mise à jour est destinée uniquement aux systèmes intégrés d’Azure Stack. N’appliquez pas cette mise à jour au Kit de développement Azure Stack.

## <a name="build-reference"></a>Référence de build

La numéro de build de mise à jour d’Azure Stack 1901 est **1.1901.0.95** ou **1.1901.0.99** après le 26 février 2019. Voir la remarque suivante :

> [!IMPORTANT]  
> Microsoft a détecté un problème qui peut impacter la mise à jour des clients de 1811 (1.1811.0.101) à 1901 et a publié un package 1901 mis à jour pour résoudre ce problème : build 1.1901.0.99, mis à jour à partir de 1.1901.0.95. Les clients qui ont déjà mis à jour vers 1.1901.0.95 n’ont rien à faire.
>
> Les clients connectés qui utilisent 1811 verront automatiquement le nouveau package 1901 (1.1901.0.99) disponible dans le portail d’administration et doivent l’installer lorsqu’ils sont prêts. Les clients déconnectés peuvent télécharger et importer le nouveau package 1901 en utilisant le même processus que celui [décrit ici](azure-stack-apply-updates.md).
>
> Les clients avec des versions de 1901 ne seront pas impactés par l’installation du package intégral ou du correctif logiciel suivant.

## <a name="hotfixes"></a>Correctifs logiciels

Azure Stack publie des correctifs logiciels à intervalles réguliers. Avant d’installer la mise à jour 1901 d’Azure Stack, veillez à installer le [dernier correctif logiciel d’Azure Stack](#azure-stack-hotfixes) pour la build 1811.

Les correctifs logiciels Azure Stack sont uniquement applicables aux systèmes intégrés Azure Stack. N’essayez pas d’installer des correctifs logiciels sur l’ASDK.

> [!TIP]  
> Pour rester informé des publications des correctifs logiciels d’Azure Stack, abonnez-vous aux flux *RSS* ou *Atom* suivants :
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Correctifs logiciels d’Azure Stack

Si vous utilisez déjà la mise à jour 1901 et n’avez pas encore installé les correctifs logiciels, vous pouvez [installer directement la mise à jour 1902](azure-stack-update-1902.md), sans installer au préalable le correctif logiciel 1901.

- **1811** : Aucun correctif logiciel actuellement disponible.
- **1901** : [KB 4500636 – Correctif logiciel d’Azure Stack 1.1901.5.109](https://support.microsoft.com/help/4500636)

## <a name="prerequisites"></a>Prérequis

> [!IMPORTANT]
> Avant d’installer la mise à jour 1901, installez le [dernier correctif logiciel d’Azure Stack](#azure-stack-hotfixes) pour la build 1811. Si vous utilisez déjà la mise à jour 1901 et n’avez pas encore installé les correctifs logiciels, vous pouvez installer directement la mise à jour 1902, sans installer au préalable le correctif logiciel 1901.

- Avant de démarrer l’installation de cette mise à jour, exécutez [Test-AzureStack](azure-stack-diagnostic-test.md) avec les paramètres suivants pour valider l’état de votre Azure Stack et résoudre les éventuels problèmes opérationnels détectés, y compris tous les avertissements et les échecs. Examinez aussi les alertes actives et résolvez toutes celles qui nécessitent une action :

    ```powershell
    Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

- Lorsqu’Azure Stack est géré par System Center Operations Manager (SCOM), veillez à mettre à jour le Pack d’administration pour Microsoft Azure Stack vers la version 1.0.3.11 avant d’appliquer la version 1901.

## <a name="new-features"></a>Nouvelles fonctionnalités

Cette mise à jour inclut les nouvelles fonctionnalités et améliorations suivantes pour Azure Stack :

- Dans Azure Stack, les images managées vous permettent de créer un objet d’image managée sur une machine virtuelle généralisée (à la fois managée et non managée) qui ne pourra ensuite créer que des machines virtuelles à disque managé. Pour plus d’informations, consultez [Disques managés d’Azure Stack](../user/azure-stack-managed-disk-considerations.md#managed-images).

- **AzureRm 2.4.0**
   * **AzureRm.Profile**  
         Résolution de bogue - `Import-AzureRmContext` pour désérialiser correctement le jeton enregistré.  
   * **AzureRm.Resources**  
         Résolution de bogue - `Get-AzureRmResource` pour exécuter des requêtes sans tenir compte de la casse, par type de ressource.  
   * **Azure.Storage**  
         Le module de correctif cumulatif AzureRm inclut désormais la version 4.5.0 déjà publiée qui prend en charge **api-version 2017-07-29**.  
   * **AzureRm.Storage**  
         Le module de correctif cumulatif AzureRm inclut désormais la version 5.0.4 déjà publiée qui prend en charge **api-version 2017-10-01**.  
   * **AzureRm.Compute**  
         Ajout d’un jeu de paramètres simples dans `New-AzureRmVM` et `New-AzureRmVmss`, `-Image` pour prendre en charge la spécification des images utilisateur.  
   * **AzureRm.Insights**  
         Le module de correctif cumulatif AzureRm inclut désormais la version 5.1.5 déjà publiée qui prend en charge **api-version 2018-01-01** pour les métriques, les définitions de métriques et les types de ressources.

- **AzureStack 1.7.1** Il s’agit d’une modification critique. Pour plus d’informations sur les modifications critiques, reportez-vous à https://aka.ms/azspshmigration171.
   * **Module Azs.Backup.Admin**  
         Changement cassant : Modifications concernant les sauvegardes dans le mode de chiffrement basé sur le certificat. La prise en charge des clés symétriques est dépréciée.  
   * **Module Azs.Fabric.Admin**  
         `Get-AzsInfrastructureVolume` a été déprécié. Utilisez la nouvelle applet de commande `Get-AzsVolume`.  
         `Get-AzsStorageSystem` a été déprécié.  Utilisez la nouvelle applet de commande `Get-AzsStorageSubSystem`.  
         `Get-AzsStoragePool` a été déprécié. L’objet `StorageSubSystem` contient la propriété de capacité.  
   * **Module Azs.Compute.Admin**  
         Résolution de bogue - `Add-AzsPlatformImage`, `Get-AzsPlatformImage` : Appel de `ConvertTo-PlatformImageObject` uniquement dans le chemin de réussite.  
         Résolution de bogue - `Add-AzsVmExtension`, `Get-AzsVmExtension` : Appel de ConvertTo-VmExtensionObject uniquement dans le chemin de réussite.  
   * **Module Azs.Storage.Admin**  
         Résolution de bogue - Le nouveau quota de stockage utilise les valeurs par défaut si aucune n’est spécifiée.

Pour consulter la référence des modules mis à jour, lisez [Référence du module Azure Stack](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.6.0&viewFallbackFrom=azurestackps-1.7.0).

## <a name="fixed-issues"></a>Problèmes résolus

- Correction d’un problème avec lequel le portail affichait une option permettant de créer des passerelles VPN basées sur les stratégies, alors qu’elles ne sont pas prises en charge dans Azure Stack. Cette option a été supprimée du portail.

<!-- 16523695 - IS, ASDK -->
- Correction d’un problème où, après avoir modifié les paramètres DNS de votre réseau virtuel en remplaçant l’option **Utiliser le DNS Azure Stack** par l’option **DNS personnalisé**, les instances n’étaient pas mises à jour avec le nouveau paramètre.

- <!-- 3235634 - IS, ASDK -->
  Correction d’un problème où, pour déployer des machines virtuelles dont les tailles contenaient le suffixe **v2** (par exemple, **Standard_A2_v2**), vous deviez spécifier le suffixe avec un « v » minuscule (**Standard_A2_v2**). Comme dans l’ensemble des produits Azure, vous pouvez désormais utiliser la forme **Standard_A2_V2** (avec un « V » majuscule).

<!--  2795678 - IS, ASDK --> 
- Correction d’un problème qui affichait un avertissement quand vous utilisiez le portail pour créer des machines virtuelles de taille Premium (DS, Ds_v2, FS, FSv2). Celles-ci étaient créées dans un compte de stockage standard. Même si cela n’avait pas d’impact sur sa fonctionnalité, sur les IOPS ou sur la facturation, l’avertissement a été supprimé.

<!-- 1264761 - IS ASDK -->  
- Correction d’un problème lié au composant **Contrôleur d’intégrité** qui générait les alertes suivantes. Ces alertes pouvaient être ignorées sans que cela pose un problème :

    - Alerte 1 :
       - NOM :  Rôle d’infrastructure défectueux
       - Niveau de gravité : Avertissement
       - COMPOSANT : Contrôleur d’intégrité
       - DESCRIPTION : L’analyseur de pulsations du contrôleur d’intégrité n’est pas disponible. Cela peut affecter les rapports et les métriques d’intégrité.  

    - Alerte 2 :
       - NOM :  Rôle d’infrastructure défectueux
       - Niveau de gravité : Avertissement
       - COMPOSANT : Contrôleur d’intégrité
       - DESCRIPTION : L’analyseur d’erreur du contrôleur d’intégrité n’est pas disponible. Cela peut affecter les rapports et les métriques d’intégrité.


<!-- 3507629 - IS, ASDK --> 
- Correction d’un problème où, lorsque vous définissiez la valeur des quotas de disques managés sur 0 sous [Types de quotas de calcul](azure-stack-quota-types.md#compute-quota-types), la valeur par défaut de 2 048 Gio était utilisée. La valeur de zéro est désormais respectée.

<!-- 2724873 - IS --> 
- Correction d’un problème où, lorsque vous utilisiez les applets de commande PowerShell **Start-AzsScaleUnitNode** ou **Stop-AzsScaleUnitNode** pour gérer les unités d’échelle, la première tentative de démarrage ou d’arrêt de l’unité d’échelle échouait.

<!-- 2724961- IS ASDK --> 
- Correction d’un problème où, lorsque vous inscriviez le fournisseur de ressources **Microsoft.Insight** dans les paramètres d’abonnement, puis créiez une machine virtuelle Windows en ayant activé les diagnostics du système d’exploitation invité, le graphique Pourcentage UC dans la page de vue d’ensemble de la machine virtuelle n’affichait pas les données de métriques. Les données sont désormais affichées.

- Correction d’un problème où l’exécution de l’applet de commande **Get-AzureStackLog** échouait après l’exécution de **Test-AzureStack** dans la même session de point de terminaison privilégié. Vous pouvez désormais utiliser la session de point de terminaison privilégié dans laquelle vous avez exécuté **Test-AzureStack**.

<!-- bug 3615401, IS -->
- Correction d’un problème lié aux sauvegardes automatiques, où le service du planificateur passait à l’état désactivé de façon inattendue. 

<!--2850083, IS ASDK -->
- Suppression du bouton **Réinitialiser la passerelle** du portail Azure Stack, qui générait une erreur lorsque l’on cliquait dessus. Ce bouton n’a pas d’utilité, puisqu’Azure Stack comprend une passerelle multilocataire et non des instances de machines virtuelles dédiées pour chaque passerelle VPN de locataire. Il a donc été supprimé pour éviter toute confusion. 

<!-- 3209594, IS ASDK -->
- Suppression du lien **Règle de sécurité en vigueur** dans le panneau **Propriétés de réseau**, car cette fonctionnalité n’est pas prise en charge dans Azure Stack. La présence de lien donnait l’impression que cette fonctionnalité était prise en charge, mais qu’elle ne fonctionnait pas. Pour éviter toute confusion, nous avons supprimé ce lien.

<!-- 3139614 | IS -->
- Correction d’un problème où, lorsqu’une mise à jour était appliquée à Azure Stack par un fabricant OEM, la notification **Mise à jour disponible** ne s’affichait pas dans le portail d’administration Azure Stack.

## <a name="changes"></a>Changements

<!-- 3083238 IS -->
- Les améliorations en matière de sécurité introduites dans cette mise à jour entraînent une augmentation de la taille de sauvegarde du rôle service d’annuaire. Pour obtenir des conseils actualisés concernant le dimensionnement de l’emplacement de stockage externe, voir la [documentation sur la sauvegarde d’infrastructure](azure-stack-backup-reference.md#storage-location-sizing). Cette modification a pour effet d’allonger la durée de la sauvegarde en raison de l’accroissement du volume des données transférées. Cette modification a une incidence sur les systèmes intégrés. 

- À compter de janvier 2019, vous pourrez déployer des clusters Kubernetes sur les tampons connectés à Azure Stack et inscrits auprès d’Active Directory Federated Services (AD FS) (l’accès à Internet est nécessaire). Pour télécharger le nouvel élément Place de marché de Kubernetes, suivez [ces instructions](azure-stack-solution-template-kubernetes-cluster-add.md). Pour déployer un cluster Kubernetes, suivez [ces instructions](../user/azure-stack-solution-template-kubernetes-adfs.md). Notez les nouveaux paramètres indiquant si le système cible est inscrit auprès d’ADD ou d’AD FS. S’il est inscrit auprès d’AD FS, de nouveaux champs sont disponibles pour entrer les paramètres du coffre de clés où est stocké le certificat de déploiement.

   Notez que même avec la prise en charge AD FS, le déploiement des clusters Kubernetes nécessite un accès à Internet.

- Après l’installation de mises à jour ou de correctifs logiciels sur Azure Stack, de nouvelles fonctionnalités peuvent être ajoutées et nécessiter que de nouvelles autorisations soient accordées à une ou plusieurs applications d’identité. Pour accorder ces autorisations, vous avez besoin d’un accès administrateur au répertoire de base. Elles ne peuvent donc pas être accordées automatiquement. Par exemple : 

   ```powershell
   $adminResourceManagerEndpoint = "https://adminmanagement.<region>.<domain>"
   $homeDirectoryTenantName = "<homeDirectoryTenant>.onmicrosoft.com" # This is the primary tenant Azure Stack is registered to

   Update-AzsHomeDirectoryTenant -AdminResourceManagerEndpoint $adminResourceManagerEndpoint `
     -DirectoryTenantName $homeDirectoryTenantName -Verbose
   ```

- Un nouvel élément doit être pris en compte afin de planifier correctement la capacité Azure Stack. Avec la mise à jour 1901, le nombre total de machines virtuelles pouvant être créées est limité.  Cette limite est destinée à être temporaire et a pour but de garantir la stabilité de la solution. Nous recherchons actuellement la cause du problème de stabilité en présence d’un plus grand nombre de machines virtuelles, mais aucun échéancier n’a encore été déterminé pour y remédier. Avec la mise à jour 1901, il y a désormais une limite de 60 machines virtuelles par serveur, la limite totale pour la solution étant de 700.  Par exemple, la limite pour 8 serveurs Azure Stack serait 480 machines virtuelles (8 * 60).  Pour une solution Azure Stack de 12 à 16 serveurs, la limite serait 700. Cette limite a été définie en gardant à l’esprit toutes les considérations relatives à la capacité de calcul, telles que la réserve de résilience et le rapport virtuel/physique de l’UC qu’un opérateur souhaite conserver. Pour plus d’informations, consultez la nouvelle version de l’outil de planification de la capacité.  
Si la limite de mise à l'échelle de la machine virtuelle a été atteinte, les codes d’erreur suivants sont retournés comme résultat : VMsPerScaleUnitLimitExceeded, VMsPerScaleUnitNodeLimitExceeded. 
 

- La version de l’API Compute est passée à 2017-12-01.

- La sauvegarde de l’infrastructure nécessite désormais un certificat avec une clé publique uniquement (. CER) pour le chiffrement des données de sauvegarde. La prise en charge des clés de chiffrement symétrique est dépréciée à compter de la mise à jour 1901. Si la sauvegarde de l’infrastructure est configurée avant l’application de la mise à jour 1901, les clés de chiffrement resteront en place. Vous disposerez d’au moins deux mises à jour supplémentaires avec prise en charge de la compatibilité descendante pour mettre à jour les paramètres de sauvegarde. Pour plus d’informations, consultez [Meilleures pratiques concernant le service de sauvegarde de l’infrastructure](azure-stack-backup-best-practices.md).

## <a name="common-vulnerabilities-and-exposures"></a>Vulnérabilités et menaces courantes

Cette mise à jour installe les mises à jour de sécurité suivantes :  

- [CVE-2018-8477](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8477)
- [CVE-2018-8514](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8514)
- [CVE-2018-8580](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8580)
- [CVE-2018-8595](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8595)
- [CVE-2018-8596](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8596)
- [CVE-2018-8598](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8598)
- [CVE-2018-8621](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8621)
- [CVE-2018-8622](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8622)
- [CVE-2018-8627](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8627)
- [CVE-2018-8637](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8637)
- [CVE-2018-8638](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8638)
- [ADV190001](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190001)
- [CVE-2019-0536](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0536)
- [CVE-2019-0537](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0537)
- [CVE-2019-0545](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0545)
- [CVE-2019-0549](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0549)
- [CVE-2019-0553](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0553)
- [CVE-2019-0554](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0554)
- [CVE-2019-0559](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0559)
- [CVE-2019-0560](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0560)
- [CVE-2019-0561](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0561)
- [CVE-2019-0569](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0569)
- [CVE-2019-0585](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0585)
- [CVE-2019-0588](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0588)


Pour plus d’informations sur ces vulnérabilités, cliquez sur les liens précédents ou consultez l’article [4480977](https://support.microsoft.com/en-us/help/4480977) de la Base de connaissances Microsoft.

## <a name="known-issues-with-the-update-process"></a>Problèmes connus avec le processus de mise à jour

- Lorsque vous tentez d’installer une mise à jour Azure Stack, l’état de la mise à jour peut échouer et définir l’état sur **PreparationFailed**. Cela est dû au fait que le fournisseur de ressources de mise à jour est dans l’impossibilité de transférer correctement les fichiers du conteneur de stockage vers un partage d’infrastructure interne à des fins de traitement. À compter de la version 1901 (1.1901.0.95), vous pouvez contourner ce problème en cliquant sur **Mettre à jour maintenant** à nouveau (et pas sur **Reprendre**). Le fournisseur de ressources de mise à jour nettoie les fichiers de la tentative précédente, puis redémarre le téléchargement.

- Lors de l’exécution de la commande [Test-AzureStack](azure-stack-diagnostic-test.md), si le test **AzsInfraRoleSummary** ou **AzsPortalApiSummary** échoue, vous êtes invité à exécuter la commande **Test-AzureStack** avec l’indicateur `-Repair`.  Si vous exécutez cette commande, elle échoue et le message d’erreur suivant s’affiche : `Unexpected exception getting Azure Stack health status. Cannot bind argument to parameter 'TestResult' because it is null.`

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

<!-- ### Health and monitoring -->

### <a name="compute"></a>Calcul

- Lors de la création d'une machine virtuelle Windows, l’erreur suivante peut s’afficher :

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   Cette erreur se produit si vous activez les diagnostics de démarrage sur une machine virtuelle alors que vous avez supprimé votre compte de stockage des diagnostics de démarrage. Pour contourner ce problème, recréez le compte de stockage avec le même nom que celui utilisé précédemment.

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- Quand vous créez un groupe de machines virtuelles identiques, l’option CentOS 7.2 est proposée pour le déploiement. Étant donné que cette image n’est pas disponible sur Azure Stack, sélectionnez un autre système d’exploitation pour votre déploiement, ou choisissez un modèle Azure Resource Manager spécifiant une autre image CentOS qui a été téléchargée par l’opérateur avant le déploiement à partir de la Place de marché.  

<!-- TBD - IS ASDK --> 
- Après avoir appliqué la mise à jour 1901, vous rencontrerez peut-être les problèmes suivants lors du déploiement de machines virtuelles avec la fonctionnalité Disques managés :

   - Si l’abonnement a été créé avant la mise à jour 1808, le déploiement d’une machine virtuelle avec la fonctionnalité Disques managés peut échouer avec un message d’erreur interne. Pour résoudre cette erreur, effectuez les étapes suivantes pour chaque abonnement :
      1. Dans le portail locataire, accédez à **Abonnements** et recherchez l’abonnement. Sélectionnez **Fournisseurs de ressources**, **Microsoft.Compute**, puis **Réinscrire**.
      2. Sous le même abonnement, accédez à **Access Control (IAM)** et vérifiez qu’**AzureStack-DiskRP-Client** est répertorié.
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

Vous pouvez télécharger la mise à jour 1901 d’Azure Stack à partir de [cet emplacement](https://aka.ms/azurestackupdatedownload). 

Dans les scénarios connectés, et uniquement dans ceux-ci, les déploiements Azure Stack consultent régulièrement un point de terminaison sécurisé et vous avertissent automatiquement si une mise à jour est disponible pour votre cloud. Pour plus d’informations, voir la documentation sur la [gestion des mises à jour pour Azure Stack](azure-stack-updates.md#using-the-update-tile-to-manage-updates).

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble de la gestion des mises à jour dans Azure Stack, consultez [Gérer les mises à jour dans Azure Stack - Vue d’ensemble](azure-stack-updates.md).  
- Pour plus d’informations sur la façon d’appliquer des mises à jour avec Azure Stack, consultez [Effectuer des mises à jour dans Azure Stack](azure-stack-apply-updates.md).
- Pour passer en revue la stratégie de maintenance pour les systèmes intégrés Azure Stack et pour connaître la marche à suivre afin de conserver votre système dans un état de prise en charge, consultez [Stratégie de maintenance Azure Stack](azure-stack-servicing-policy.md).  
- Pour utiliser le Point de terminaison privilégié (PEP) afin de surveiller et de reprendre les mises à jour, consultez [Surveiller les mises à jour dans Azure Stack à l’aide du point de terminaison privilégié](azure-stack-monitor-update.md).  
