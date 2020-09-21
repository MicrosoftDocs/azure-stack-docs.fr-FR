---
title: Notes de publication d’Azure Stack Hub
description: Notes de publication des systèmes intégrés Azure Stack Hub, y compris les mises à jour et les correctifs de bogues.
author: sethmanheim
ms.topic: article
ms.date: 09/15/2020
ms.author: sethm
ms.reviewer: sranthar
ms.lastreviewed: 08/11/2020
ms.openlocfilehash: e90564a9dceefda2b9712b3dedf69974512bdcd1
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90571896"
---
# <a name="azure-stack-hub-release-notes"></a>Notes de publication d’Azure Stack Hub

Cet article décrit le contenu des mises à jour d’Azure Stack Hub. La mise à jour inclut les améliorations et les correctifs logiciels de la dernière version d’Azure Stack Hub.

Pour accéder aux notes de publication d'une autre version, utilisez le menu déroulant de sélection de la version, situé au-dessus de la table des matières à gauche.

::: moniker range=">=azs-1908"
> [!IMPORTANT]  
> Cette mise à jour est destinée uniquement aux systèmes intégrés d’Azure Stack Hub. N’appliquez pas cette mise à jour au Kit de développement Azure Stack (ASDK).
::: moniker-end
::: moniker range="<azs-1908"
> [!IMPORTANT]  
> Si votre instance d’Azure Stack Hub a plus de deux mises à jour de retard, elle est considérée comme non conforme. Pour bénéficier de la prise en charge, vous devez [mettre à jour avec au moins la version minimale prise en charge](azure-stack-servicing-policy.md#keep-your-system-under-support).
::: moniker-end

## <a name="update-planning"></a>Planification des mises à jour

Avant d’appliquer la mise à jour, veillez à consulter les informations suivantes :

- [Problèmes connus](known-issues.md)
- [Mises à jour de sécurité](release-notes-security-updates.md)
- [Liste de vérification des activités avant et après l’application de la mise à jour](release-notes-checklist.md)

Pour obtenir de l’aide sur la résolution des problèmes liés aux mises à jour et au processus de mise à jour, consultez [Résoudre les problèmes liés aux correctifs logiciels et aux mises à jour pour Azure Stack Hub](azure-stack-troubleshooting.md).

## <a name="download-the-update"></a>Télécharger la mise à jour

Vous pouvez télécharger le package de mise à jour d’Azure Stack Hub à l’aide de [l’outil téléchargeur des mises à jour d’Azure Stack Hub](https://aka.ms/azurestackupdatedownload).

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->
::: moniker range="azs-2005"
## <a name="2005-build-reference"></a>Référence de la build 2005

Le numéro de build de la mise à jour 2005 d’Azure Stack Hub est **1.2005.6.53**.

> [!IMPORTANT]  
> Avec [la version 2002](release-notes.md?view=azs-2002) d’Azure Stack Hub et pour la prise en charge de nos clients dans le monde entier qui réagissent à la COVID-19 et qui peuvent prendre des décisions importantes quant à leurs systèmes Azure Stack Hub, Microsoft a temporairement étendu sa stratégie de support pour inclure trois versions de mise à jour précédentes (N-3). Avec la version 2005, nous continuons cette extension pendant 45 jours supplémentaires (jusqu’au 25 septembre 2020). Ainsi, la nouvelle mise à jour 2005 et une des trois versions de mise à jour précédentes (par exemple, 2002, 1910 et 1908 ou N-3) sont prises en charge. Après ces 45 jours (après le 25 septembre 2020), nous revenons à notre politique de support standard, ce qui signifie que les versions prises en charge seront 2005, 2002 et 1910 ou N-2.

### <a name="update-type"></a>Type de mise à jour

Le type de build de la mise à jour 2005 d’Azure Stack Hub est **Complète**.

La taille du package de mise à jour 2005 est supérieure à celle des mises à jour précédentes. Cette augmentation de taille allonge les temps de téléchargement. La mise à jour reste à l’état de **préparation** pendant une longue période, et les opérateurs peuvent s’attendre à ce que ce processus prenne plus de temps qu’avec les mises à jour précédentes. La mise à jour 2005 a présenté les durées d’exécution attendues suivantes dans les nœuds au cours de nos 4 tests internes : de 13 à 20 heures, 8 nœuds : de 16 à 26 heures, 12 nœuds : de 19 à 32 heures, 16 nœuds : de 22-38 heures. La durée d’exécution exacte de la mise à jour dépend généralement de la capacité utilisée sur votre système par les charges de travail de locataire, de la connectivité réseau de votre système (s’il est connecté à Internet) et des caractéristiques de vos composants matériels système. Les durées d’exécution plus courtes ou plus longues que la valeur attendue ne sont pas rares et ne nécessitent aucune action de la part des opérateurs Azure Stack Hub, sauf en cas d’échec de la mise à jour. Cette durée d’exécution approximative est propre à la mise à jour 2005. Elle ne doit pas être comparée aux autres mises à jour d’Azure Stack Hub.

Pour plus d’informations sur les types de build de mise à jour, consultez [Gérer les mises à jour dans Azure Stack Hub](azure-stack-updates.md).

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Nouveautés

<!-- What's new, also net new experiences and features. -->
- Cette build offre un support de 3 nouveaux types de machines virtuelles GPU : Tailles de machines virtuelles NCv3 (Nvidia V100), NVv4 (AMD MI25) et NCas_v4 (NVIDIA T4). Les déploiements de machines virtuelles seront réussis pour ceux qui disposent du matériel approprié et sont intégrés au programme de préversion du GPU Azure Stack Hub. Si vous êtes intéressé, inscrivez-vous au programme de préversion du GPU sur https://aka.ms/azurestackhubgpupreview. Pour plus d’informations, [consultez](../user/gpu-vms-about.md).
- Cette version fournit une nouvelle fonctionnalité qui permet une capacité de réparation autonome, qui détecte les défaillances, évalue l’impact et atténue en toute sécurité les problèmes système. Avec cette fonctionnalité, nous travaillons à l’augmentation de la disponibilité du système sans intervention manuelle. Avec la version 2005 et les versions ultérieures, les clients subissent une réduction du nombre d’alertes. L’intervention des opérateurs d’Azure Stack Hub n’est pas nécessaire pour chaque défaillance de ce pipeline, sauf notification.
- Il existe une nouvelle option dans le portail d’administration Azure Stack Hub pour les clients disposant d’une connexion orientée vers l’air et déconnectée Azure Stack Hub, afin d’enregistrer les journaux localement. Vous pouvez stocker les journaux dans un partage SMB local lorsque Azure Stack Hub est déconnecté d’Azure.
- Le portail d’administration Azure Stack Hub bloque désormais certaines opérations si une opération système est déjà en cours. Par exemple, si une mise à jour est en cours, il n’est pas possible d’ajouter un nouveau nœud d’unité d’échelle.
- Cette version offre une plus grande cohérence des structures avec Azure sur les machines virtuelles créées avant la version 1910. Pour la version 1910, Microsoft a annoncé que toutes les machines virtuelles nouvellement créées utiliseront le protocole wireserver, ce qui permettra aux clients d’utiliser le même agent WALA et l’agent invité Windows comme Azure, facilitant ainsi l’utilisation des images Azure sur Azure Stack Hub. Avec cette version, toutes les machines virtuelles créées avant le 1910 sont automatiquement migrées pour utiliser le protocole wireserver. Cela permet également d’améliorer la fiabilité de la création de machines virtuelles, le déploiement d’extension de machines virtuelles et l’amélioration du temps de bon fonctionnement.
- Le stockage Azure Stack Hub prend désormais en charge les API des services de stockage Azure version 2019-02-02. Pour les bibliothèques clientes Azure, compatibles avec la nouvelle version de l’API REST. Pour plus d’informations, consultez [outils de développement du stockage Azure Stack Hub](../user/azure-stack-storage-dev.md#azure-client-libraries).
- Azure Stack Hub prend maintenant en charge la dernière version de [CreateUiDefinition (version 2)](/azure/azure-resource-manager/managed-applications/create-uidefinition-overview).
- Nouvelle aide pour les déploiements de machines virtuelles par lot. Pour plus d’informations, [consultez cet article](../operator/azure-stack-capacity-planning-compute.md).
- L’élément Conteneur de système d’exploitation principal Linux de la Place de marché Azure Stack Hub [approche de sa fin de vie](https://azure.microsoft.com/updates/flatcar-in-azure/). Pour plus d’informations, consultez [Migration à partir du conteneur de système d’exploitation principal Linux](https://docs.flatcar-linux.org/os/migrate-from-container-linux/).

### <a name="improvements"></a>Améliorations

<!-- Changes and product improvements with tangible customer-facing value. -->

- Améliorations apportées aux journaux et aux événements du service de cluster d’infrastructure de stockage. Les journaux et les événements du service de cluster de l’infrastructure de stockage sont conservés pendant jusqu’à 14 jours, afin d’améliorer les diagnostics et la résolution des problèmes.
- Améliorations qui augmentent la fiabilité du démarrage et de l’arrêt d’Azure Stack Hub.
- Améliorations qui réduisent le runtime des mises à jour à l’aide de la décentralisation et de la suppression des dépendances. Comparée à la mise à jour 2002, le temps de mise à jour de l’empreinte de 4 nœuds est réduit de 15 à 42 heures à 13 à 20 heures. 8 nœuds sont réduits de 20 à 50 heures à 16 à 26 heures. 12 nœuds sont réduits de 20 à 60 heures à 19 à 32 heures. 16 nœuds sont réduits de 25 à 70 heures à 22 à 38 heures. La durée d’exécution exacte de la mise à jour dépend généralement de la capacité utilisée sur votre système par les charges de travail de locataire, de la connectivité réseau de votre système (s’il est connecté à Internet) et des caractéristiques de vos composants matériels système.
- La mise à jour échoue à l’heure actuelle en cas d’erreurs irrécupérables.
- Résilience améliorée du package de mise à jour lors du téléchargement à partir d’Internet.
- Résilience améliorée de l’arrêt de la désallocation d’une machine virtuelle.
- Résilience améliorée de l’agent hôte du contrôleur de réseau.
- Ajout de champs supplémentaires à la charge utile CEF des messages syslog pour signaler l’adresse IP source et le compte utilisé pour se connecter au point de terminaison privilégié et au point de terminaison de récupération. Consultez [Intégrer Azure Stack Hub à des solutions de supervision avec le transfert Syslog](azure-stack-integrate-security.md) pour plus d’informations.
- Ajout des événements Windows Defender (ID d’événement 5001, 5010, 5012) à la liste des événements émis par le biais du client syslog.
- Ajout d’alertes dans le portail d’administration Azure Stack pour les événements liés à Windows Defender, pour signaler les incohérences de version de la plateforme et des signatures Defender et ne pas prendre des mesures sur les programmes malveillants détectés.
- Ajout de la prise en charge de 4 appareils de bordure lors de l’intégration d’Azure Stack Hub dans votre centre de données.

### <a name="changes"></a>Modifications

- Suppression des actions pour arrêter, interrompre et redémarrer une instance de rôle d’infrastructure à partir du portail d’administration. Les API correspondantes ont également été supprimées dans le fournisseur de ressources d’infrastructure. Les cmdlets PowerShell suivantes dans le module RM administrateur et préversion AZ pour Azure Stack Hub ne fonctionnent plus : **Stop-AzsInfrastructureRoleInstance**, **Disable-InfrastructureRoleInstance** et **Restart-InfrastructureRoleInstance**. Ces cmdlets seront supprimées de la prochaine version du module AZ administrateur pour Azure Stack Hub.
- Azure Stack Hub 2005 prend désormais en charge uniquement [App Service sur Azure Stack Hub 2020 (versions 87. x)](app-service-release-notes-2020-Q2.md).

### <a name="fixes"></a>Correctifs

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Correction d’un problème qui pouvait entraîner l’échec d’un nœud d’unité d’échelle de réparation, car il n’a pas pu trouver le chemin d’accès à l’image du système d’exploitation de base.
- Résolution d’un problème avec un scale-out et un scale-in pour le rôle d’infrastructure de support qui a un effet en cascade sur la réparation des nœuds d’unité d’échelle.
- Correction d’un problème dans lequel l’extension .VHD (au lieu de .vhd) n’était pas autorisée lorsque les opérateurs ajoutaient leurs propres images au portail d’administration Azure Stack Hub sur **Tous les services > Capacité de calcul > Images de machine virtuelle > Ajouter**.
- Correction d’un problème dans lequel une précédente opération de redémarrage de la machine virtuelle provoquait un redémarrage inattendu suivant après une autre opération de mise à jour de machines virtuelles (ajout de disques, de balises, etc.).
- Correction d’un problème entraînant le blocage du portail lors de la création d’une zone DNS dupliquée. Il doit maintenant afficher une erreur appropriée.
- Correction d’un problème dans lequel **Get-AzureStackLogs** n’a pas collecté les journaux requis pour résoudre les problèmes de mise en réseau. 
- Correction d’un problème dans lequel le portail permettait l’attachement de moins d’adaptateurs réseau que ce qu’il autorise réellement. 
- Correction de la stratégie d’intégrité du code pour ne pas émettre d’événements de violation pour certains logiciels internes. Cela réduit le bruit dans les événements de violation d’intégrité du code émis par le client syslog.
- Correction de la cmdlet **Set-TLSPolicy** pour appliquer une nouvelle stratégie sans redémarrage du service https ni de l’hôte.
- Correction d’un problème dans lequel l’utilisation d’un serveur NTP Linux génère de façon erronée des alertes dans le portail d’administration.  
- Correction d’un problème où le basculement de l’instance de service de contrôleur de sauvegarde entraînait la désactivation des sauvegardes automatiques.
- Correction d’un problème où la rotation du secret interne échoue lorsque les services d’infrastructure n’ont pas de connectivité Internet.
- Correction d’un problème empêchant les utilisateurs d’afficher les autorisations d’abonnement à l’aide des portails Azure Stack Hub.

## <a name="security-updates"></a>Mises à jour de sécurité

Pour plus d’informations sur les mises à jour de sécurité dans cette mise à jour d’Azure Stack Hub, consultez [Mises à jour de sécurité Azure Stack Hub](release-notes-security-updates.md).

## <a name="hotfixes"></a>Correctifs logiciels

Azure Stack Hub publie régulièrement des correctifs logiciels. À partir de la version 2005, lorsque vous mettez à jour vers une nouvelle version principale (par exemple, 1.2002.x vers 1.2005.x), les derniers correctifs (le cas échéant) de la nouvelle version principale sont installés automatiquement. À partir de là, si un correctif est mis en production pour votre build, vous devez l’installer.

> [!NOTE]
> Les versions des correctifs logiciels Azure Stack Hub sont cumulatives. Il vous suffit d’installer le dernier correctif logiciel afin d’obtenir l’ensemble des correctifs logiciels inclus dans les versions précédentes de correctifs logiciels pour cette version.

Pour plus d’informations, consultez notre [stratégie de maintenance](azure-stack-servicing-policy.md).

Les correctifs logiciels Azure Stack Hub s’appliquent uniquement aux systèmes intégrés Azure Stack Hub. N’essayez pas d’installer des correctifs logiciels sur l’ASDK.

### <a name="prerequisites-before-applying-the-2005-update"></a>Configuration requise : Avant d’appliquer la mise à jour 2005

La version 2005 d’Azure Stack Hub doit être appliquée sur la version 2002 avec les correctifs logiciels suivants :

- [Correctif logiciel Azure Stack Hub 1.2002.53.144](https://support.microsoft.com/help/4574736)

### <a name="after-successfully-applying-the-2005-update"></a>Après l’application réussie de la mise à jour 2005

À partir de la version 2005, lorsque vous mettez à jour vers une nouvelle version principale (par exemple, 1.2002.x vers 1.2005.x), les derniers correctifs (le cas échéant) de la nouvelle version principale sont installés automatiquement.

Après l’installation de 2005, si des correctifs 2005 sont mis en production par la suite, vous devez les installer :

- [Correctif logiciel Azure Stack Hub 1.2005.12.66](https://support.microsoft.com/help/4580968)
::: moniker-end

::: moniker range="azs-2002"
## <a name="2002-build-reference"></a>Référence de la build 2002

Le numéro de build de la mise à jour 2002 d’Azure Stack Hub est **1.2002.0.35**.

> [!IMPORTANT]  
> Avec la mise à jour Azure Stack Hub 2002, Microsoft prolonge provisoirement nos [conditions de politique de support d’Azure Stack Hub](azure-stack-servicing-policy.md).  Nous travaillons avec des clients du monde entier qui sont confrontés au virus COVID-19 et qui peuvent prendre des décisions importantes sur leurs systèmes Azure Stack Hub et sur la façon dont ils sont mis à jour et gérés, afin que les opérations commerciales de leur centre de données continuent de fonctionner normalement. Pour aider nos clients, Microsoft propose de reporter provisoirement le changement de politique de support pour inclure trois versions de mise à jour précédentes.  Ainsi, la nouvelle mise à jour 2002 et les trois versions de mise à jour précédentes (par exemple, 1910, 1908 et 1907) sont prises en charge.

### <a name="update-type"></a>Type de mise à jour

Le type de build de la mise à jour 2002 d’Azure Stack Hub est **Complète**.

La taille du package de mise à jour 2002 est supérieure à celle des mises à jour précédentes. Cette augmentation de taille allonge les temps de téléchargement. La mise à jour reste à l’état de **préparation** pendant une longue période, et les opérateurs peuvent s’attendre à ce que ce processus prenne plus de temps qu’avec les mises à jour précédentes. La mise à jour 2002 a présenté les durées d’exécution attendues suivantes dans les nœuds au cours de nos 4 tests internes : 15 à 42 heures, 8 nœuds : 20 50 heures, 12 nœuds : 20 à 60 heures, 16 nœuds : 25 à 70 heures. La durée d’exécution exacte de la mise à jour dépend généralement de la capacité utilisée sur votre système par les charges de travail de locataire, de la connectivité réseau de votre système (s’il est connecté à Internet) et des caractéristiques de vos composants matériels système. Les durées d’exécution plus courtes ou plus longues que la valeur attendue ne sont pas rares et ne nécessitent aucune action de la part des opérateurs Azure Stack Hub, sauf en cas d’échec de la mise à jour. Cette durée d’exécution approximative est propre à la mise à jour 2002. Elle ne doit pas être comparée aux autres mises à jour d’Azure Stack Hub.

Pour plus d’informations sur les types de build de mise à jour, consultez [Gérer les mises à jour dans Azure Stack Hub](azure-stack-updates.md).

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Nouveautés

<!-- What's new, also net new experiences and features. -->

- Une nouvelle version (1.8.1) des modules PowerShell d’administration d’Azure Stack Hub basés sur AzureRM est disponible.
- Une nouvelle version de l’API REST d’administration d’Azure Stack Hub est disponible. Vous trouverez plus d’informations sur les points de terminaison et les modifications cassantes dans les [Informations de référence sur l’API](/rest/api/azure-stack/).
- Les nouveaux modules de locataire Azure PowerShell seront publiés pour Azure Stack Hub le 15 avril 2020. Les modules Azure RM utilisés continuent de fonctionner, mais ne seront plus mis à jour après la build 2002.
- Ajout d’une nouvelle alerte d’avertissement sur le portail administrateur Azure Stack Hub pour signaler les problèmes de connectivité avec le serveur syslog configuré. Le titre de l’alerte indique que **le client Syslog a rencontré un problème de réseau lors de l’envoi d’un message Syslog**.
- Ajout d’une nouvelle alerte d’avertissement sur le portail administrateur Azure Stack Hub pour signaler les problèmes de connectivité avec le serveur NTP (Network Time Protocol). Le titre de l’alerte indique que **la source de temps n’est pas valide sur [nom du nœud]** .
- Le [SDK Java](https://azure.microsoft.com/develop/java/) a publié de nouveaux packages en raison d’un changement cassant dans la version 2002 lié aux restrictions TLS. Vous devez installer la nouvelle dépendance du SDK Java. Vous trouverez les instructions à la section [Java et les profils de version d’API](../user/azure-stack-version-profiles-java.md?view=azs-2002#java-and-api-version-profiles).
- Une nouvelle version (1.0.5.10) du pack d’administration de System Center Operations Manager - Azure Stack Hub est disponible et est nécessaire pour tous les systèmes exécutant 2002 en raison des la rupture des changements cassants apportées aux API. Les changements apportés aux API impactent les tableaux de bord des performances de sauvegarde et de stockage, et nous vous recommandons de commencer par mettre à jour tous les systèmes vers la version 2002 avant de mettre à jour le pack d’administration.

### <a name="improvements"></a>Améliorations

<!-- Changes and product improvements with tangible customer-facing value. -->

- Cette mise à jour contient des modifications du processus de mise à jour qui améliorent considérablement les performances des futures mises à jour complètes. Ces modifications prennent effet avec la prochaine mise à jour complète après la version 2002, et visent spécifiquement à améliorer les performances de la phase d’une mise à jour complète dans laquelle les systèmes d’exploitation hôtes sont mis à jour. L’amélioration des performances des mises à jour des systèmes d’exploitation hôtes réduit considérablement la durée pendant laquelle les charges de travail des locataires sont impactées pendant les mises à jour complètes.
- L’outil de vérification de la disponibilité d’Azure Stack Hub valide désormais l’intégration d’AD Graph à l’aide de tous les ports TCP IP alloués à AD Graph.
- L’outil de syndication hors connexion a été mis à jour avec des améliorations de la fiabilité. L’outil n’est plus disponible sur GitHub et a été [déplacé vers PowerShell Gallery](https://www.powershellgallery.com/packages/Azs.Syndication.Admin/). Pour plus d’informations, consultez [Télécharger des éléments de la Place de marché vers Azure Stack Hub](azure-stack-download-azure-marketplace-item.md).
- Une nouvelle fonctionnalité de supervision est sur le point d’être ajoutée. L’alerte concernant un espace disque insuffisant pour les hôtes physiques et les machines virtuelles d’infrastructure sera corrigée automatiquement par la plateforme. Si cette action échoue, l’alerte s’affichera dans le portail d’administration Azure Stack Hub pour que l’opérateur corrige le problème.
- Améliorations apportées à la [collecte des journaux de diagnostic](./azure-stack-diagnostic-log-collection-overview.md?view=azs-2002). La nouvelle expérience rationalise et simplifie la collecte des journaux de diagnostic en éliminant la nécessité de configurer un compte de stockage d’objets blob à l’avance. L’environnement de stockage est préconfiguré afin que vous puissiez envoyer des journaux avant d’ouvrir un cas de support et consacrer moins de temps à un appel de support.
- Le temps nécessaire à la [collecte proactive des journaux et à la collecte des journaux à la demande](./azure-stack-diagnostic-log-collection-overview.md?view=azs-2002) a été réduit de 80 %. La collecte des journaux peut prendre plus de temps que cette valeur attendue, mais elle ne nécessite aucune action de la part des opérateurs Azure Stack Hub, sauf si la collecte des journaux échoue.
- La progression du téléchargement d’un package de mise à jour Azure Stack Hub est désormais visible dans le panneau de mise à jour après le lancement d’une mise à jour. Seuls sont concernés les systèmes connectés Azure Stack Hub qui choisissent de [préparer les packages de mise à jour par le biais du téléchargement automatique](azure-stack-update-prepare-package.md#automatic-download-and-preparation-for-update-packages).
- Améliorations de la fiabilité de l’agent hôte du contrôleur de réseau.
- Introduction d’un nouveau micro-service nommé DNS Orchestrator qui améliore la logique de résilience pour les services DNS internes au cours des mises à jour et des correctifs.
- Ajout d’une nouvelle validation de demande visant à faire échouer les URI d’objet blob non valides pour le paramètre de compte de stockage de diagnostics de démarrage lors de la création de machines virtuelles.
- Améliorations de la correction automatique et de la journalisation pour Rdagent et l’agent Host, deux services sur l’hôte qui facilitent les opérations CRUD de machine virtuelle.
- Ajout d’une nouvelle fonctionnalité à la gestion de la Place de marché qui permet à Microsoft d’ajouter des attributs qui empêchent les administrateurs de télécharger des produits de la Place de marché qui ne sont pas compatibles avec leurs environnements Azure Stack, en raison de différents propriétés, comme la version Azure Stack ou le modèle de facturation. Seul Microsoft peut ajouter ces attributs. Pour plus d’informations, consultez [Utiliser le portail pour télécharger les éléments de la Place de marché](azure-stack-download-azure-marketplace-item.md#use-the-portal-to-download-marketplace-items).

### <a name="changes"></a>Modifications

- Le portail administrateur indique à présent si une opération est en cours, avec une icône en regard de la région Azure Stack. Quand vous pointez sur l’icône, le nom de l’opération s’affiche. Cela vous permet d’identifier les opérations système en cours d’exécution en arrière-plan, telles qu’un travail de sauvegarde ou une extension de stockage pouvant s’exécuter pendant plusieurs heures.

- Les API d’administration suivantes ont été dépréciées :

  | Fournisseur de ressources       | Ressource              | Version            |
  |-------------------------|-----------------------|--------------------|
  | Microsoft.Storage.Admin | exploitations agricoles                 | 2015-12-01-preview |
  | Microsoft.Storage.Admin | farms/acquisitions    | 2015-12-01-preview |
  | Microsoft.Storage.Admin | farms/shares          | 2015-12-01-preview |
  | Microsoft.Storage.Admin | farms/storageaccounts | 2015-12-01-preview |

- Les API d’administration suivantes ont été remplacées par une version plus récente (2018-09-01) :

  | Fournisseur de ressources      | Ressource              | Version    |
  |------------------------|-----------------------|------------|
  | Microsoft.Backup.Admin | backupLocation         | 2016-05-01 |
  | Microsoft.Backup.Admin | backups                | 2016-05-01 |
  | Microsoft.Backup.Admin | opérations             | 2016-05-01 |

- Quand vous créez une machine virtuelle Windows à l’aide de PowerShell, veillez à ajouter l’indicateur `provisionvmagent` si vous souhaitez que la machine virtuelle déploie des extensions. Sans cet indicateur, la machine virtuelle est créée sans l’agent invité, ce qui supprime la possibilité de déployer les extensions de machine virtuelle :

   ```powershell
   $VirtualMachine = Set-AzureRmVMOperatingSystem `
     -VM $VirtualMachine `
     -Windows `
     -ComputerName "MainComputer" `
     -Credential $Credential -ProvisionVMAgent
  ```

### <a name="fixes"></a>Correctifs

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Correction d’un problème où l’ajout de plusieurs adresses IP publiques sur la même carte réseau d’une machine virtuelle provoquait des problèmes de connectivité Internet. À présent, une carte réseau avec deux adresses IP publiques fonctionne comme prévu.
- Résolution d’un problème qui provoquait la génération d’une alerte par le système, indiquant que le répertoire de démarrage d’Azure AD devait être configuré.
- Résolution d’un problème qui provoquait l’échec de la fermeture automatique d’une alerte. L’alerte indiquait que le répertoire de démarrage d’Azure AD devait être configuré, mais ne se fermait pas même après l’atténuation du problème.
- Résolution d’un problème qui provoquait l’échec des mises à jour pendant la phase de leur préparation à la suite de défaillances internes du fournisseur de ressources de mise à jour.
- Résolution d’un problème provoquant l’échec des opérations du fournisseur de ressources de module complémentaire après l’exécution de la rotation des secrets Azure Stack Hub.
- Résolution d’un problème qui était une cause courante des échecs de mise à jour d’Azure Stack Hub en raison de la sollicitation de la mémoire sur le rôle ERCS.
- Correction d’un bogue dans le panneau de mise à jour lié au fait que l’état de la mise à jour indiquait **Installation** au lieu de **Préparation** pendant la phase de préparation d’une mise à jour d’Azure Stack Hub.
- Résolution d’un problème lié au fait que la fonctionnalité RSC sur les commutateurs virtuels créait des incohérences et abandonnait le trafic circulant via un équilibreur de charge. La fonctionnalité RSC est désormais désactivée par défaut.
- Résolution d’un problème lié au fait que plusieurs configurations IP sur une carte réseau entraînaient un routage incorrect et empêchaient la connectivité sortante. 
- Résolution d’un problème lié au fait que l’adresse MAC d’une carte réseau était mise en cache et que l’affectation de cette adresse à une autre ressource provoquait des échecs de déploiement de machine virtuelle.
- Résolution d’un problème lié au fait que la licence des images de machine virtuelle Windows du canal de vente au détail n’a pas pu être activée par AVMA.
- Résolution d’un problème qui entraînait l’échec de la création de machines virtuelles si le nombre de cœurs virtuels demandés par la machine virtuelle était égal au nombre de cœurs physiques du nœud. Nous autorisons maintenant les machines virtuelles à avoir un nombre de cœurs virtuels égal ou inférieur au nombre de cœurs physiques du nœud.
- Résolution d’un problème lié au fait que nous n’autorisons pas la définition du type de licence sur « null » pour basculer les images avec paiement à l’utilisation vers BYOL.
- Résolution d’un problème empêchant l’ajout d’extensions à un groupe de machines virtuelles identiques.

## <a name="security-updates"></a>Mises à jour de sécurité

Pour plus d’informations sur les mises à jour de sécurité dans cette mise à jour d’Azure Stack Hub, consultez [Mises à jour de sécurité Azure Stack Hub](release-notes-security-updates.md).

## <a name="hotfixes"></a>Correctifs logiciels

Azure Stack Hub publie régulièrement des correctifs logiciels. Veillez à installer le dernier correctif logiciel Azure Stack Hub pour la version 1910 avant de mettre à jour Azure Stack Hub vers la version 2002.

> [!NOTE]
> Les versions des correctifs logiciels Azure Stack Hub sont cumulatives. Il vous suffit d’installer le dernier correctif logiciel afin d’obtenir l’ensemble des correctifs logiciels inclus dans les versions précédentes de correctifs logiciels pour cette version.

Les correctifs logiciels Azure Stack Hub s’appliquent uniquement aux systèmes intégrés Azure Stack Hub. N’essayez pas d’installer des correctifs logiciels sur l’ASDK.

Pour plus d’informations sur les correctifs, consultez la [stratégie de maintenance Azure Stack Hub](azure-stack-servicing-policy.md#hotfixes).

### <a name="prerequisites-before-applying-the-2002-update"></a>Configuration requise : Avant d’appliquer la mise à jour 2002

La version 2002 d’Azure Stack Hub doit être appliquée sur la version 1910 avec les correctifs logiciels suivants :

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Correctif logiciel Azure Stack Hub 1.1910.63.186](https://support.microsoft.com/help/4574735)

### <a name="after-successfully-applying-the-2002-update"></a>Après l’application de la mise à jour 2002

Après l’installation de cette mise à jour, installez les correctifs logiciels applicables.

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Correctif logiciel Azure Stack Hub 1.2002.53.144](https://support.microsoft.com/help/4574736)
::: moniker-end

::: moniker range="azs-1910"
## <a name="1910-build-reference"></a>Référence de la build 1910

Le numéro de build de la mise à jour 1910 d’Azure Stack Hub est **1.1910.0.58**.

### <a name="update-type"></a>Type de mise à jour

Depuis la version 1908, le système d’exploitation sous-jacent sur lequel Azure Stack Hub s’exécute a été mis à jour vers Windows Server 2019. Cette mise à jour apporte des améliorations fondamentales et permet l’ajout de fonctionnalités supplémentaires dans Azure Stack Hub.

Le type de build de la mise à jour 1910 d’Azure Stack Hub est **Express**.

Le package de la mise à jour 1910 étant d’une taille supérieure à celle des mises à jour précédentes, les temps de téléchargement sont plus longs. La mise à jour reste à l’état de **préparation** pendant une longue période, et les opérateurs peuvent s’attendre à ce que ce processus prenne plus de temps que les mises à jour précédentes. Le temps prévu pour la mise à jour 1910 est d’environ 10 heures, quel que soit le nombre de nœuds physiques dans votre environnement Azure Stack Hub. La durée d’exécution exacte de la mise à jour dépend généralement de la capacité utilisée sur votre système par les charges de travail de locataire, de la connectivité réseau de votre système (s’il est connecté à Internet) et des caractéristiques de vos composants matériels système. Il n’est pas rare d’observer des durées d’exécution plus longues que la durée prévue, mais cela ne nécessite aucune action de la part des opérateurs Azure Stack Hub, sauf en cas d’échec de la mise à jour. Cette durée d’exécution approximative est propre à la mise à jour 1910. Elle ne doit pas être comparée aux autres mises à jour d’Azure Stack Hub.

Pour plus d’informations sur les types de build de mise à jour, consultez [Gérer les mises à jour dans Azure Stack Hub](azure-stack-updates.md).

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Nouveautés

<!-- What's new, also net new experiences and features. -->

- Le portail administrateur montre désormais les adresses IP des points de terminaison privilégiés dans le menu des propriétés de région pour faciliter leur découverte. Il montre aussi le serveur de temps et les redirecteurs DNS actuellement configurés. Pour plus d’informations, consultez [Utiliser le point de terminaison privilégié dans Azure Stack Hub](azure-stack-privileged-endpoint.md).

- Le système de contrôle d’intégrité et de supervision d’Azure Stack Hub peut désormais déclencher des alertes pour divers composants matériels en cas d’erreur. Ces alertes nécessitent une configuration supplémentaire. Pour plus d’informations, consultez [Superviser les composants matériels d’Azure Stack Hub](azure-stack-hardware-monitoring.md).

- [Prise en charge de cloud-init pour Azure Stack Hub](/azure/virtual-machines/linux/using-cloud-init) : Cloud-init est une approche courante permettant de personnaliser une machine virtuelle Linux lors de son premier démarrage. Vous pouvez utiliser cloud-init pour installer des packages et écrire des fichiers, ou encore pour configurer des utilisateurs ou des paramètres de sécurité. cloud-init étant appelé pendant le processus de démarrage initial, aucune autre étape ni aucun agent ne sont nécessaires pour appliquer votre configuration. Les images Ubuntu de la Place de marché ont été mises à jour pour prendre en charge cloud-init pour le provisionnement.

- Azure Stack Hub prend désormais en charge toutes les versions de l’agent Windows Azure Linux en tant qu’Azure.

- Une nouvelle version des modules PowerShell d’administration d’Azure Stack Hub est disponible. <!-- For more information, see -->

- Les nouveaux modules d’abonnés Azure PowerShell ont été mis en production pour Azure Stack Hub le 15 avril 2020. Les modules Azure RM utilisés continuent de fonctionner, mais ne seront plus mis à jour après la build 2002.

- Ajout de l’applet de commande **Set-AzSDefenderManualUpdate** dans le point de terminaison privilégié (PEP) pour configurer la mise à jour manuelle des définitions Windows Defender dans l’infrastructure Azure Stack Hub. Pour plus d’informations, consultez [Mettre à jour l’antivirus Windows Defender sur Azure Stack Hub](azure-stack-security-av.md).

- Ajout de l’applet de commande **Get-AzSDefenderManualUpdate** dans le point de terminaison privilégié (PEP) pour récupérer la configuration de la mise à jour manuelle des définitions Windows Defender dans l’infrastructure Azure Stack Hub. Pour plus d’informations, consultez [Mettre à jour l’antivirus Windows Defender sur Azure Stack Hub](azure-stack-security-av.md).

- Ajout de l’applet de commande **Set-AzSDnsForwarder** dans le point de terminaison privilégié (PEP) pour changer les paramètres de redirecteur des serveurs DNS dans Azure Stack Hub. Pour plus d’informations sur la configuration DNS, consultez [Intégration des services DNS au centre de données Azure Stack Hub](azure-stack-integrate-dns.md).

- Ajout de l’applet de commande **Get-AzSDnsForwarder** dans le point de terminaison privilégié (PEP) pour récupérer les paramètres de redirecteur des serveurs DNS dans Azure Stack Hub. Pour plus d’informations sur la configuration DNS, consultez [Intégration des services DNS au centre de données Azure Stack Hub](azure-stack-integrate-dns.md).

- Ajout de la prise en charge de la gestion des **clusters Kubernetes** à l’aide du [moteur AKS](../user/azure-stack-kubernetes-aks-engine-overview.md). À partir de cette mise à jour, les clients peuvent déployer des clusters Kubernetes de production. Le moteur AKS permet aux utilisateurs d’effectuer les opérations suivantes :
  - Gérer le cycle de vie de leurs clusters Kubernetes. Ils peuvent créer, mettre à jour et mettre à l’échelle des clusters.
  - Gérer leurs clusters à l’aide d’images managées produites par AKS et les équipes Azure Stack Hub.
  - Tirer parti d’un fournisseur de cloud Kubernetes intégré à Azure Resource Manager, qui crée des clusters à l’aide de ressources Azure natives.
  - Déployer et gérer leurs clusters dans des empreintes Azure Stack Hub connectées ou déconnectées.
  - Utiliser les fonctionnalités hybrides Azure :
    - Intégration avec Azure Arc.
    - Intégration avec Azure Monitor pour conteneurs.
  - Utiliser des conteneurs Windows avec le moteur AKS.
  - Bénéficier du support Microsoft et d’ingénierie pour leurs déploiements.

### <a name="improvements"></a>Améliorations

<!-- Changes and product improvements with tangible customer-facing value. -->

- Azure Stack Hub a amélioré sa capacité à corriger automatiquement certains problèmes liés aux correctifs logiciels et aux mises à jour, qui entraînaient des échecs de mise à jour ou empêchaient les opérateurs de lancer une mise à jour d’Azure Stack Hub. Par conséquent, le groupe **Test-AzureStack -UpdateReadiness** comprend moins de tests. Pour plus d’informations, consultez [Valider l’état du système Azure Stack Hub](azure-stack-diagnostic-test.md#groups). Les trois tests suivants font encore partie du groupe **UpdateReadiness** :

  - **AzSInfraFileValidation**
  - **AzSActionPlanStatus**
  - **AzsStampBMCSummary**

- Ajout d’une règle d’audit pour signaler le moment où un périphérique externe (par exemple une clé USB) est monté sur un nœud de l’infrastructure Azure Stack Hub. Le journal d’audit est émis via syslog et s’affiche sous la forme **Microsoft-Windows-Security-Auditing : 6416|Événements Plug-and-Play**. Pour plus d’informations sur la façon de configurer le client syslog, consultez [Transfert Syslog](azure-stack-integrate-security.md).

- Azure Stack Hub utilise maintenant des clés RSA 4096 bits pour les certificats internes. La rotation des secrets internes remplace les anciens certificats 2048 bits par des certificats d’une longueur de 4096 bits. Pour plus d’informations sur la rotation des secrets dans Azure Stack Hub, consultez [Effectuer une rotation des secrets dans Azure Stack Hub](azure-stack-rotate-secrets.md).

- Mises à niveau vers la complexité des algorithmes de chiffrement et la robustesse des clés pour plusieurs composants internes afin de se conformer à la stratégie CNSSP-15 (Committee on National Security Systems - Policy 15), qui fournit les bonnes pratiques d’utilisation des normes publiques pour assurer la sécurité du partage des informations. Parmi les améliorations, citons l’AES256 pour l’authentification Kerberos et le SHA384 pour le chiffrement VPN. Pour plus d’informations sur la stratégie CNSSP-15, reportez-vous à la [page Policies du Committee on National Security Systems](http://www.cnss.gov/CNSS/issuances/Policies.cfm).

- En raison de la mise à niveau ci-dessus, Azure Stack Hub utilise de nouvelles valeurs par défaut pour les configurations IPsec/IKEv2. Les nouvelles valeurs par défaut utilisées du côté d’Azure Stack Hub sont les suivantes :

   **Paramètres IKE Phase 1 (Mode principal)**

   | Propriété              | Valeur|
   |-|-|
   | Version IKE           | IKEv2 |
   |Groupe Diffie-Hellman   | ECP384 |
   | Méthode d'authentification | Clé prépartagée |
   |Chiffrement et algorithmes de hachage | AES256, SHA384 |
   |Durée de vie de l’AS (durée)     | 28 800 secondes|

   **Paramètres IKE Phase 2 (Mode rapide)**

   | Propriété| Valeur|
   |-|-|
   |Version IKE |IKEv2 |
   |Chiffrement et algorithmes de hachage (Chiffrement)     | GCMAES256|
   |Chiffrement et algorithmes de hachage (Authentification) | GCMAES256|
   |Durée de vie de l’AS (durée)  | 27 000 secondes  |
   |Durée de vie de l’AS (kilo-octets) | 33 553 408     |
   |PFS (Perfect Forward Secrecy) | ECP384 |
   |Détection d’homologue mort | Prise en charge|

   Ces changements sont également reflétés dans la documentation sur la [proposition IPsec/IKE par défaut](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters).

- Le service Infrastructure Backup améliore la logique qui calcule l’espace disponible souhaité pour les sauvegardes au lieu de s’appuyer sur un seuil fixe. Le service se base sur la taille d’une sauvegarde, une stratégie de conservation, une réserve et l’utilisation actuelle de l’emplacement de stockage externe pour déterminer si un avertissement doit être adressé à l’opérateur.

### <a name="changes"></a>Modifications

- Quand vous téléchargez des éléments de la Place de marché d’Azure vers Azure Stack Hub, une nouvelle interface utilisateur vous permet de spécifier une version de l’élément, au cas où il en existerait plusieurs. La nouvelle interface utilisateur est disponible dans les deux scénarios, connecté et déconnecté. Pour plus d’informations, consultez [Télécharger des éléments de la Place de marché d’Azure vers Azure Stack Hub](azure-stack-download-azure-marketplace-item.md).  

- Depuis la version 1910, le système Azure Stack Hub **nécessite** un espace IP interne privé /20 supplémentaire. Pour plus d’informations, consultez [Planification de l’intégration réseau pour Azure Stack](azure-stack-network.md).
  
- Le service Infrastructure Backup supprime partiellement les données de sauvegarde chargées si l’emplacement de stockage externe manque d’espace disponible pendant la procédure de chargement.  

- Le service Infrastructure Backup ajoute le service d’identité à la charge utile de sauvegarde pour les déploiements AAD.  

- Le module PowerShell Azure Stack Hub a été mis à jour vers la version 1.8.0 pour la version 1910.<br>Les changements sont notamment :
   - **Nouveau module d’administration DRP** : Le DRP (fournisseur de ressources de déploiement) permet des déploiements orchestrés de fournisseurs de ressources sur Azure Stack Hub. Ces commandes interagissent avec la couche Azure Resource Manager pour interagir avec le fournisseur DRP.
   - **BRP** : <br />
           - Prise en charge de la restauration de rôle unique pour la sauvegarde d’infrastructure Azure Stack. <br />
           - Ajout du paramètre `RoleName` à l’applet de commande `Restore-AzsBackup`.
   - **FRP** : Changements cassants pour les ressources de **lecteur** et de **volume** avec la version d’API `2019-05-01`. Les fonctionnalités sont prises en charge par la version 1910 d’Azure Stack Hub et les versions ultérieures : <br />
            – Les valeurs de `ID`, `Name`, `HealthStatus` et `OperationalStatus` ont été changées. <br />
            – Nouvelles propriétés prises en charge `FirmwareVersion`, `IsIndicationEnabled`, `Manufacturer`et `StoragePool` pour les ressources de **lecteur**. <br />
            – Les propriétés `CanPool` et `CannotPoolReason` des ressources de **lecteur** sont désormais déconseillées. Utilisez `OperationalStatus` à la place.

### <a name="fixes"></a>Correctifs

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there's an SR/ICM associated to it. -->

- Correction d’un problème qui empêchait d’appliquer la stratégie TLS 1.2 dans les environnements déployés avant la version 1904 d’Azure Stack Hub.
- Correction d’un problème où une machine virtuelle Ubuntu 18.04 créée avec une autorisation SSH activée ne vous permettait pas d’utiliser les clés SSH pour vous connecter.
- Suppression de la **réinitialisation du mot de passe** de l’interface utilisateur du groupe de machines virtuelles identiques.
- Correction d’un problème où la suppression de l’équilibreur de charge à partir du portail n’aboutissait pas à la suppression de l’objet dans la couche d’infrastructure.
- Correction d’un problème qui entraînait l’affichage d’un pourcentage inexact de l’alerte d’utilisation du pool de passerelle sur le portail d’administration.
<!-- Fixed an issue where adding more than one public IP on the same NIC on a Virtual Machine resulted in internet connectivity issues. Now, a NIC with two public IPs should work as expected.[This fix actually didn't go in 1910 due to build issues, commenting out until next build (2002) ] -->

## <a name="security-updates"></a>Mises à jour de sécurité

Pour plus d’informations sur les mises à jour de sécurité dans cette mise à jour d’Azure Stack Hub, consultez [Mises à jour de sécurité Azure Stack Hub](release-notes-security-updates.md).

Le rapport de vulnérabilité Qualys pour cette version peut être téléchargé à partir du [site web Qualys](https://www.qualys.com/azure-stack/).

## <a name="hotfixes"></a>Correctifs logiciels

Azure Stack Hub publie régulièrement des correctifs logiciels. Veillez à installer le dernier correctif logiciel Azure Stack Hub pour la version 1908 avant de mettre à jour Azure Stack Hub vers la version 1910.

> [!NOTE]
> Les versions des correctifs logiciels Azure Stack Hub sont cumulatives. Il vous suffit d’installer le dernier correctif logiciel afin d’obtenir l’ensemble des correctifs logiciels inclus dans les versions précédentes de correctifs logiciels pour cette version.

Les correctifs logiciels Azure Stack Hub s’appliquent uniquement aux systèmes intégrés Azure Stack Hub. N’essayez pas d’installer des correctifs logiciels sur l’ASDK.

### <a name="prerequisites-before-applying-the-1910-update"></a>Configuration requise : Avant d’appliquer la mise à jour 1910

La version 1910 d’Azure Stack Hub doit être appliquée sur la version 1908 avec les correctifs logiciels suivants :

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Correctif logiciel Azure Stack Hub 1.1908.51.133](https://support.microsoft.com/help/4574734)

### <a name="after-successfully-applying-the-1910-update"></a>Après l’application de la mise à jour 1910

Après l’installation de cette mise à jour, installez les correctifs logiciels applicables. Pour plus d’informations, consultez notre [stratégie de maintenance](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Correctif logiciel Azure Stack Hub 1.1910.63.186](https://support.microsoft.com/help/4574735)
::: moniker-end

::: moniker range="azs-1908"
## <a name="1908-build-reference"></a>Référence de build 1908

Le numéro de build de la mise à jour 1908 d’Azure Stack Hub est **1.1908.4.33**.

### <a name="update-type"></a>Type de mise à jour

Pour la version 1908, le système d’exploitation sous-jacent sur lequel Azure Stack Hub s’exécute a été mis à jour vers Windows Server 2019. Cette mise à jour apporte des améliorations fondamentales et permet l’ajout de fonctionnalités supplémentaires dans Azure Stack Hub.

Le type de build de la mise à jour 1908 d’Azure Stack Hub est **Complète**. Par conséquent, la mise à jour 1908 prend plus de temps que les mises à jour Express telles que 1906 et 1907. Les runtimes exacts des mises à jour complètes dépendent généralement du nombre de nœuds que votre instance Azure Stack Hub contient, de la capacité utilisée sur votre système par les charges de travail clientes, de la connectivité réseau de votre système (s’il est connecté à Internet) et de la configuration de votre matériel système. La mise à jour 1908 a présenté les durées d’exécution attendues suivantes dans nos tests internes : 4 nœuds - 42 heures, 8 nœuds - 50 heures, 12 nœuds - 60 heures, 16 nœuds - 70 heures. Il n’est pas rare de constater des durées d’exécution des mises à jour plus longues que ces durées prévues, mais cela ne nécessite aucune action de la part des opérateurs Azure Stack Hub (sauf si la mise à jour échoue).

Pour plus d’informations sur les types de build de mise à jour, consultez [Gérer les mises à jour dans Azure Stack Hub](azure-stack-updates.md).

- La durée d’exécution exacte des mises à jour dépend généralement de la capacité utilisée sur votre système par les charges de travail client, de la connectivité réseau de votre système (s’il est connecté à Internet) et de la configuration de votre matériel système.
- Il n’est pas rare d’observer des durées d’exécution plus longues que la durée prévue, mais cela ne nécessite aucune action de la part des opérateurs Azure Stack Hub (sauf si la mise à jour échoue).
- Cette durée d’exécution approximative est propre à la mise à jour 1908. Elle ne doit pas être comparée aux autres mises à jour d’Azure Stack Hub.

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Nouveautés

<!-- What's new, also net new experiences and features. -->

- Pour la version 1908, le système d’exploitation sous-jacent sur lequel Azure Stack Hub s’exécute a été mis à jour vers Windows Server 2019. Cette mise à jour apporte des améliorations fondamentales et permet l’ajout de fonctionnalités supplémentaires dans Azure Stack Hub.
- Tous les composants de l’infrastructure Azure Stack Hub fonctionnent à présent en mode FIPS 140-2.
- Les opérateurs Azure Stack Hub peuvent à présent supprimer les données utilisateur du portail. Pour plus d’informations, consultez [Effacer les données utilisateur du portail dans Azure Stack Hub](azure-stack-portal-clear.md).

### <a name="improvements"></a>Améliorations

<!-- Changes and product improvements with tangible customer-facing value. -->
- Le chiffrement des données au repos dans Azure Stack Hub a été amélioré afin de rendre les secrets persistants dans le module matériel TPM des nœuds physiques.

### <a name="changes"></a>Modifications

- Les fournisseurs de matériel publieront le package d’extension OEM version 2.1 ou ultérieure en même temps qu’Azure Stack Hub version 1908. Le package d’extension OEM version 2.1 ou ultérieure est requis pour la version 1908 d’Azure Stack Hub. Pour plus d’informations sur le téléchargement du package d’extension OEM version 2.1 ou ultérieure, contactez le fournisseur de matériel pour votre système, et consultez l’article sur les [mises à jour OEM](azure-stack-update-oem.md#oem-contact-information).  

### <a name="fixes"></a>Correctifs

- Résolution d’un problème de compatibilité avec les futures mises à jour OEM d’Azure Stack Hub et d’un problème de déploiement de machine virtuelle à l’aide d’images utilisateur client. Ce problème a été détecté dans la version 1907 et résolu avec le correctif logiciel [KB4517473](https://support.microsoft.com/en-us/help/4517473/azure-stack-hotfix-1-1907-12-44)  
- Résolution d’un problème avec la mise à jour des microprogrammes OEM et correction du diagnostic incorrect dans Test-AzureStack pour Fabric Ring Health. Ce problème a été détecté dans la version 1907 et résolu avec le correctif logiciel [KB4515310](https://support.microsoft.com/en-us/help/4515310/azure-stack-hotfix-1-1907-7-35)
- Correction d’un problème avec le processus de mise à jour des microprogrammes OEM. Ce problème a été détecté dans la version 1907 et résolu avec le correctif logiciel [KB4515650](https://support.microsoft.com/en-us/help/4515650/azure-stack-hotfix-1-1907-8-37)

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there's an SR/ICM associated to it. -->

## <a name="security-updates"></a>Mises à jour de sécurité

Pour plus d’informations sur les mises à jour de sécurité dans cette mise à jour d’Azure Stack Hub, consultez [Mises à jour de sécurité Azure Stack Hub](release-notes-security-updates.md).

Le rapport de vulnérabilité Qualys pour cette version peut être téléchargé à partir du [site web Qualys](https://www.qualys.com/azure-stack/).

## <a name="download-the-update"></a>Télécharger la mise à jour

Vous pouvez télécharger la mise à jour 1908 d’Azure Stack Hub à partir de la [page de téléchargement d’Azure Stack Hub](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes"></a>Correctifs logiciels

Azure Stack Hub publie régulièrement des correctifs logiciels. Veillez à installer le dernier correctif logiciel Azure Stack Hub pour la version 1907 avant de mettre à jour Azure Stack Hub vers la version 1908.

Les correctifs logiciels d’Azure Stack Hub s’appliquent uniquement aux systèmes intégrés Azure Stack Hub. N’essayez pas de les installer sur l’ASDK.

### <a name="prerequisites-before-applying-the-1908-update"></a>Configuration requise : Avant d’appliquer la mise à jour 1908

La version 1908 d’Azure Stack Hub doit être appliquée sur la version 1907 avec les correctifs logiciels suivants :

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Correctif logiciel Azure Stack Hub 1.1907.29.80](https://support.microsoft.com/help/4555650)

La mise à jour 1908 d’Azure Stack Hub nécessite la **version 2.1 ou ultérieure d’OEM pour Azure Stack Hub** du fournisseur de matériel de votre système. Les mises à jour OEM appliquent les mises à jour des pilotes et des microprogrammes aux composants matériels de votre système Azure Stack Hub. Pour plus d’informations sur l’application des mises à jour OEM, consultez [Appliquer des mises à jour de fabricants d’ordinateurs à Azure Stack Hub](azure-stack-update-oem.md)

### <a name="after-successfully-applying-the-1908-update"></a>Après l’application de la mise à jour 1908

Après l’installation de cette mise à jour, installez les correctifs logiciels applicables. Pour plus d’informations, consultez notre [stratégie de maintenance](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Correctif logiciel Azure Stack Hub 1.1908.51.133](https://support.microsoft.com/help/4574734)
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
::: moniker range="azs-1907"
## <a name="1907-archived-release-notes"></a>Notes de publication archivées 1907
::: moniker-end
::: moniker range="azs-1906"
## <a name="1906-archived-release-notes"></a>Notes de publication archivées 1906
::: moniker-end
::: moniker range="azs-1905"
## <a name="1905-archived-release-notes"></a>Notes de publication archivées 1905
::: moniker-end
::: moniker range="azs-1904"
## <a name="1904-archived-release-notes"></a>Notes de publication archivées 1904
::: moniker-end
::: moniker range="azs-1903"
## <a name="1903-archived-release-notes"></a>Notes de publication archivées 1903
::: moniker-end
::: moniker range="azs-1902"
## <a name="1902-archived-release-notes"></a>Notes de publication archivées 1902
::: moniker-end
::: moniker range="azs-1901"
## <a name="1901-archived-release-notes"></a>Notes de publication archivées 1901
::: moniker-end
::: moniker range="azs-1811"
## <a name="1811-archived-release-notes"></a>Notes de publication archivées 1811
::: moniker-end
::: moniker range="azs-1809"
## <a name="1809-archived-release-notes"></a>Notes de publication archivées 1809
::: moniker-end
::: moniker range="azs-1808"
## <a name="1808-archived-release-notes"></a>Notes de publication archivées 1808
::: moniker-end
::: moniker range="azs-1807"
## <a name="1807-archived-release-notes"></a>Notes de publication archivées 1807
::: moniker-end
::: moniker range="azs-1805"
## <a name="1805-archived-release-notes"></a>Notes de publication archivées 1805
::: moniker-end
::: moniker range="azs-1804"
## <a name="1804-archived-release-notes"></a>Notes de publication archivées 1804
::: moniker-end
::: moniker range="azs-1803"
## <a name="1803-archived-release-notes"></a>Notes de publication archivées 1803
::: moniker-end
::: moniker range="azs-1802"
## <a name="1802-archived-release-notes"></a>Notes de publication archivées 1802
::: moniker-end

::: moniker range="<azs-1908"
Vous pouvez accéder aux [versions antérieures des notes de publication d’Azure Stack Hub dans la galerie TechNet](https://aka.ms/azsarchivedrelnotes). Ces documents archivés sont fournis uniquement pour référence et n’impliquent aucune prise en charge de ces versions. Pour plus d’informations sur le support d’Azure Stack Hub, consultez [Stratégie de maintenance Azure Stack Hub](azure-stack-servicing-policy.md). Pour obtenir de l’aide, contactez les services de support technique Microsoft.
::: moniker-end
