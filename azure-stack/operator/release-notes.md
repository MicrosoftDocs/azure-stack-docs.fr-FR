---
title: Notes de publication d’Azure Stack Hub
description: Notes de publication des systèmes intégrés Azure Stack Hub, y compris les mises à jour et les correctifs de bogues.
author: sethmanheim
ms.topic: article
ms.date: 01/25/2021
ms.author: sethm
ms.reviewer: sranthar
ms.lastreviewed: 09/09/2020
ms.openlocfilehash: 196313ed569a701dae9cc558ff1438d9d9df1241
ms.sourcegitcommit: 82f7d16bc5f4224678edbbda0ba3441c8e07ca0b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98763156"
---
# <a name="azure-stack-hub-release-notes"></a>Notes de publication d’Azure Stack Hub

Cet article décrit le contenu des mises à jour d’Azure Stack Hub. La mise à jour inclut les améliorations et les correctifs logiciels de la dernière version d’Azure Stack Hub.

Pour accéder aux notes de publication d'une autre version, utilisez le menu déroulant de sélection de la version, situé au-dessus de la table des matières à gauche.

::: moniker range=">=azs-2002"
> [!IMPORTANT]  
> Cette mise à jour est destinée uniquement aux systèmes intégrés d’Azure Stack Hub. N’appliquez pas cette mise à jour au Kit de développement Azure Stack (ASDK).
::: moniker-end
::: moniker range="<azs-2002"
> [!IMPORTANT]  
> Si votre instance d’Azure Stack Hub a plus de deux mises à jour de retard, elle est considérée comme non conforme. Pour bénéficier de la prise en charge, vous devez [mettre à jour avec au moins la version minimale prise en charge](azure-stack-servicing-policy.md#keep-your-system-under-support).
::: moniker-end

## <a name="update-planning"></a>Planification des mises à jour

Avant d’appliquer la mise à jour, veillez à consulter les informations suivantes :

- [Liste de vérification des activités avant et après l’application de la mise à jour](release-notes-checklist.md)
- [Problèmes connus](known-issues.md)
- [Correctifs logiciels](#hotfixes)
- [Mises à jour de sécurité](release-notes-security-updates.md)

Pour obtenir de l’aide sur la résolution des problèmes liés aux mises à jour et au processus de mise à jour, consultez [Résoudre les problèmes liés aux correctifs logiciels et aux mises à jour pour Azure Stack Hub](azure-stack-troubleshooting.md).

## <a name="download-the-update"></a>Télécharger la mise à jour

Vous pouvez télécharger le package de mise à jour d’Azure Stack Hub à l’aide de [l’outil téléchargeur des mises à jour d’Azure Stack Hub](https://aka.ms/azurestackupdatedownload).

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->
::: moniker range="azs-2008"
## <a name="2008-build-reference"></a>Référence de la build 2008

Le numéro de build de la mise à jour 2008 d’Azure Stack Hub est **1.2008.13.88**.

### <a name="update-type"></a>Type de mise à jour

Le type de build de la mise à jour 2008 d’Azure Stack Hub est **Complète**.

La taille du package de mise à jour 2008 est supérieure à celle des mises à jour précédentes. Cette augmentation de taille allonge les temps de téléchargement. La mise à jour reste à l’état de **préparation** pendant une longue période, et les opérateurs peuvent s’attendre à ce que ce processus prenne plus de temps qu’avec les mises à jour précédentes. La mise à jour 2008 a présenté les durées d’exécution attendues suivantes dans les nœuds au cours de nos 4 tests internes : de 13 à 20 heures, 8 nœuds : de 16 à 26 heures, 12 nœuds : de 19 à 32 heures, 16 nœuds : de 22-38 heures. La durée d’exécution exacte de la mise à jour dépend généralement de la capacité utilisée sur votre système par les charges de travail de locataire, de la connectivité réseau de votre système (s’il est connecté à Internet) et des caractéristiques de vos composants matériels système. Les durées d’exécution plus courtes ou plus longues que la valeur attendue ne sont pas rares et ne nécessitent aucune action de la part des opérateurs Azure Stack Hub, sauf en cas d’échec de la mise à jour. Cette durée d’exécution approximative est propre à la mise à jour 2008. Elle ne doit pas être comparée aux autres mises à jour d’Azure Stack Hub.

Pour plus d’informations sur les types de build de mise à jour, consultez [Gérer les mises à jour dans Azure Stack Hub](azure-stack-updates.md).

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Nouveautés

<!-- What's new, also net new experiences and features. -->
- Azure Stack Hub prend à présent en charge la fonctionnalité VNET Peering, ce qui permet de connecter des réseaux virtuels sans appliance virtuelle réseau. Pour plus d’informations, consultez la [documentation sur la nouvelle fonctionnalité VNET Peering](../user/virtual-network-peering.md).
- Le stockage d’objets blob Azure Stack Hub permet à présent aux utilisateurs d’utiliser un objet blob immuable. En définissant des stratégies immuables sur un conteneur, vous pouvez stocker les objets de données vitaux pour l’entreprise dans un état WORM (Write Once, Read Many). Dans cette version, les stratégies immuables ne peuvent être définies qu’à l’aide de l’API REST ou des SDK clients. Les écritures d’objets blob d’ajout ne sont pas non plus possibles dans cette version. Pour plus d’informations sur les objets blob immuables, consultez [Stocker des données blob critiques pour l’entreprise avec un stockage immuable](/azure/storage/blobs/storage-blob-immutable-storage).
- Le stockage Azure Stack Hub prend désormais en charge les API des services de stockage Azure version **2019-07-07**. Pour les bibliothèques clientes Azure, compatibles avec la nouvelle version de l’API REST, consultez [Outils de développement de stockage Azure Stack Hub](../user/azure-stack-storage-dev.md#azure-client-libraries). Pour les API de gestion des services de stockage Azure, la version **2018-02-01** a été ajoutée à la prise en charge, avec un sous-ensemble de fonctionnalités totales disponibles.
- Le service de calcul Azure Stack Hub prend désormais en charge les API Azure Compute version **2020-06-01**, avec un sous-ensemble des fonctionnalités totales disponibles.
- Les disques managés Azure Stack Hub prennent désormais en charge les API Azure Disk version **2019-03-01**, avec un sous-ensemble des fonctionnalités disponibles.
- Préversion de Windows Admin Center qui peut maintenant se connecter à Azure Stack Hub pour fournir des insights approfondis sur l’infrastructure pendant les opérations de support (arrêt requis).
- Possibilité d’ajouter une bannière de connexion au point de terminaison privilégié au moment du déploiement.
- Publication de nouvelles bannières **Opérations exclusives**, qui améliorent la visibilité des opérations qui se produisent actuellement sur le système et qui permettent aux utilisateurs de lancer (et de faire échouer par la suite) toute autre opération exclusive.
- Introduction de deux nouvelles bannières dans chaque page produit de l’élément de la Place de marché Azure Stack Hub. En cas de défaillance du téléchargement de la Place de marché, les opérateurs peuvent afficher les détails de l’erreur et tenter d’effectuer les étapes recommandées pour résoudre le problème.
- Publication d’un outil de notation permettant aux clients de fournir des commentaires. Cela permet à l’équipe Azure Stack Hub de mesurer et d’optimiser l’expérience utilisateur.
- Cette version d’Azure Stack Hub comprend une préversion privée d’Azure Kubernetes service (AKS) et d’Azure Container Registry (ACR). L’objectif de la préversion privée consiste à recueillir des commentaires en termes de qualité, de fonctionnalités et d’expérience utilisateur de AKS et ACR sur Azure Stack Hub.
- Cette version comprend une préversion publique des conteneurs Azure CNI et Windows utilisant le [moteur AKS v0.55.4](../user/kubernetes-aks-engine-release-notes.md). Pour un exemple sur la manière de les utiliser dans votre modèle d’API, [consultez cet exemple sur GitHub](https://raw.githubusercontent.com/Azure/aks-engine/master/examples/azure-stack/kubernetes-windows.json).
- Il existe désormais une prise en charge du [déploiement Istio 1.3](https://github.com/Azure/aks-engine/tree/master/examples/service-mesh) sur les clusters déployés par le [moteur AKS v0.55.4](../user/kubernetes-aks-engine-release-notes.md). Pour plus d’informations, [consultez ces instructions](../user/kubernetes-aks-engine-service-account.md).
- Il existe désormais une prise en charge du déploiement des [clusters privés](https://github.com/Azure/aks-engine/blob/master/docs/topics/features.md#private-cluster) utilisant le [moteur AKS v0.55.4](../user/kubernetes-aks-engine-release-notes.md).
- Cette version prend en charge l’approvisionnement des [secrets de configuration Kubernetes](https://github.com/Azure/aks-engine/blob/master/docs/topics/keyvault-secrets.md#use-key-vault-as-the-source-of-cluster-configuration-secrets) depuis Azure et Key Vault Azure Stack Hub Key.

### <a name="improvements"></a>Améliorations

<!-- Changes and product improvements with tangible customer-facing value. -->
- Mise en œuvre de la surveillance interne pour le contrôleur de réseau et les agents hôtes SLB. Les services sont donc corrigés automatiquement s’ils entrent dans un état arrêté.
- Les services de fédération Active Directory (AD FS) récupèrent maintenant le nouveau certificat de signature de jetons après que le client a effectué sa rotation sur son propre serveur AD FS. Pour tirer parti de cette nouvelle fonctionnalité pour les systèmes déjà configurés, l’intégration de AD FS doit être reconfigurée. Pour plus d’informations, consultez [Intégrer l’identité AD FS avec votre centre de données Azure Stack Hub](azure-stack-integrate-identity.md).
- Modifications apportées au processus de démarrage et d’arrêt sur les instances de rôle d’infrastructure et leurs dépendances sur les nœuds d’unité d’échelle. Cela augmente la fiabilité du démarrage et de l’arrêt d’Azure Stack Hub.
- La suite **AzSScenarios** de l’outil de validation **Test-AzureStack** a été mise à jour pour permettre aux fournisseurs de services cloud d’exécuter correctement cette suite avec l’authentification multifacteur appliquée à tous les comptes clients.
- Amélioration de la fiabilité des alertes en ajoutant une logique de suppression pour 29 alertes côté client pendant les opérations de cycle de vie.
- Vous pouvez maintenant afficher un rapport HTML de collecte de journaux qui fournit des détails sur les rôles, la durée et l’état de la collecte de journaux. Ce rapport a pour objectif d’aider les utilisateurs à fournir un résumé des journaux collectés. Les services de support technique Microsoft peuvent ensuite rapidement examiner le rapport pour évaluer les données de journal et faciliter l’atténuation et la résolution des problèmes système.
- La couverture de détection des défaillances d’infrastructure a été améliorée moyennant l’ajout de 7 nouvelles analyses dans des scénarios utilisateur, tels que l’utilisation du processeur et la consommation de mémoire, pour plus de fiabilité en matière de détection des défaillances.

### <a name="changes"></a>Modifications

- La propriété de type de ressource de compte de stockage **supportHttpsTrafficOnly** dans la version de l’API SRP **2016-01-01** et **2016-05-01** a été activée, mais cette propriété n’est pas prise en charge dans Azure Stack Hub.
- Augmentation du seuil d’alerte d’utilisation de la capacité du volume de 80 % (avertissement) et 90 % (critique) à 90 % (avertissement) et 95 % (critique). Pour plus d’informations, consultez la page [Alertes de l’espace de stockage](azure-stack-manage-storage-shares.md#storage-space-alerts).
- Les étapes de configuration d’AD Graph changent avec cette version. Pour plus d’informations, consultez [Intégrer l’identité AD FS avec votre centre de données Azure Stack Hub](azure-stack-integrate-identity.md).
- Pour s’aligner sur les meilleures pratiques actuelles définies pour Windows Server 2019, Azure Stack Hub change de façon à utiliser une classe de trafic ou une priorité supplémentaires afin de séparer les communications de serveur à serveur dans la prise en charge de la communication de contrôle du clustering de basculement. Le résultat de ces modifications offre une meilleure résilience pour la communication des clusters de basculement. Cette configuration de réservation de bande passante et de classe de trafic est opérée par une modification des commutateurs ToR (top-of-rack) de la solution Azure Stack Hub, ainsi que sur l’ordinateur hôte ou les serveurs d’Azure Stack Hub.

  Notez que ces modifications sont ajoutées au niveau de l’hôte d’un système Azure Stack Hub. Contactez votre fabricant OEM pour lui demander d’apporter les modifications nécessaires aux commutateurs réseau ToR (top-of-rack). Cette modification des commutateurs ToR peut être effectuée tant avant qu’après la mise à jour vers la version 2008. Pour plus d’informations, consultez la [documentation relative à l’intégration réseau](azure-stack-network.md).

- Les tailles de machines virtuelles compatibles GPU **NCas_v4 (NVIDIA T4)** ont été remplacées dans cette version par les tailles de machines virtuelles **NCasT4_v3**, à des fins de mise en cohérence avec Azure. Notez que celles-ci ne sont pas encore visibles dans le portail et peuvent être utilisées uniquement par le biais de modèles Azure Resource Manager.

### <a name="fixes"></a>Correctifs

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->
- Correction d’un problème où la suppression d’un groupe de sécurité réseau d’une carte réseau qui n’est pas attachée à une machine virtuelle en cours d’exécution échouait.
- Correction d’un problème où la modification de la valeur **IdleTimeoutInMinutes** pour une adresse IP publique associée à un équilibreur de charge plaçait l’adresse IP publique dans un état d’échec.
- Correction de l’applet de commande **Get-AzsDisk** pour qu’elle retourne l’état **Attaché** approprié, au lieu de **OnlineMigration**, pour les disques managés attachés.

## <a name="security-updates"></a>Mises à jour de sécurité

Pour plus d’informations sur les mises à jour de sécurité dans cette mise à jour d’Azure Stack Hub, consultez [Mises à jour de sécurité Azure Stack Hub](release-notes-security-updates.md).

## <a name="hotfixes"></a>Correctifs logiciels

Azure Stack Hub publie régulièrement des correctifs logiciels. À partir de la version 2005, lorsque vous mettez à jour vers une nouvelle version principale (par exemple, 1.2005.x vers 1.2008.x), les derniers correctifs (le cas échéant) de la nouvelle version principale sont installés automatiquement. À partir de là, si un correctif est mis en production pour votre build, vous devez l’installer.

> [!NOTE]
> Les versions des correctifs logiciels Azure Stack Hub sont cumulatives. Il vous suffit d’installer le dernier correctif logiciel afin d’obtenir l’ensemble des correctifs logiciels inclus dans les versions précédentes de correctifs logiciels pour cette version.

Pour plus d’informations, consultez notre [stratégie de maintenance](azure-stack-servicing-policy.md).

Les correctifs logiciels Azure Stack Hub s’appliquent uniquement aux systèmes intégrés Azure Stack Hub. N’essayez pas d’installer des correctifs logiciels sur l’ASDK.

### <a name="after-successfully-applying-the-2008-update"></a>Après l’application réussie de la mise à jour 2008

Étant donné que les correctifs Azure Stack Hub sont cumulatifs, il est recommandé d’installer tous les correctifs publiés pour votre build afin de garantir la meilleure expérience de mise à jour possible entre les versions majeures. Lorsque vous mettez à jour vers une nouvelle version principale (par exemple, 1.2005.x vers 1.2008.x), les derniers correctifs (le cas échéant) de la nouvelle version principale sont installés automatiquement.

Après l’installation de 2008, si des correctifs 2008 sont mis en production par la suite, vous devez les installer :

- [Correctif logiciel Azure Stack Hub 1.2008.23.108](https://support.microsoft.com/topic/c0d203fd-7585-4c8d-8ea5-ae13897e352e)
::: moniker-end

::: moniker range="azs-2005"
## <a name="2005-build-reference"></a>Référence de la build 2005

Le numéro de build de la mise à jour 2005 d’Azure Stack Hub est **1.2005.6.53**.

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
- Le paramètre de chiffrement des utilisateurs requis pour la surveillance du matériel est passé de DES à AES afin de renforcer la sécurité. Contactez votre fournisseur de matériel pour savoir comment modifier le paramètre dans le contrôleur de gestion de la carte de base (BMC). Une fois la modification apportée dans le BMC, vous devrez peut-être réexécuter la commande **Set-BmcCredential** à l’aide du point de terminaison privilégié. Pour plus de détails, consultez [Effectuer la rotation des secrets dans Azure Stack Hub](azure-stack-rotate-secrets.md).

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

- [Correctif logiciel Azure Stack Hub 1.2002.65.171](https://support.microsoft.com/topic/d743db84-df31-496b-b37c-6e5618b4cc8f)

### <a name="after-successfully-applying-the-2005-update"></a>Après l’application réussie de la mise à jour 2005

À partir de la version 2005, lorsque vous mettez à jour vers une nouvelle version principale (par exemple, 1.2002.x vers 1.2005.x), les derniers correctifs (le cas échéant) de la nouvelle version principale sont installés automatiquement.

Après l’installation de 2005, si des correctifs 2005 sont mis en production par la suite, vous devez les installer :

- [Correctif logiciel Azure Stack Hub 1.2005.28.98](https://support.microsoft.com/topic/ecf727b1-3dc1-4070-ace8-1291cc437389)
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
- Le [SDK Java](https://azure.microsoft.com/develop/java/) a publié de nouveaux packages en raison d’un changement cassant dans la version 2002 lié aux restrictions TLS. Vous devez installer la nouvelle dépendance du SDK Java. Vous trouverez les instructions à la section [Java et les profils de version d’API](../user/azure-stack-version-profiles-java.md?view=azs-2002&preserve-view=true#java-and-api-version-profiles).
- Une nouvelle version (1.0.5.10) du pack d’administration de System Center Operations Manager - Azure Stack Hub est disponible et est nécessaire pour tous les systèmes exécutant 2002 en raison des la rupture des changements cassants apportées aux API. Les changements apportés aux API impactent les tableaux de bord des performances de sauvegarde et de stockage, et nous vous recommandons de commencer par mettre à jour tous les systèmes vers la version 2002 avant de mettre à jour le pack d’administration.

### <a name="improvements"></a>Améliorations

<!-- Changes and product improvements with tangible customer-facing value. -->

- Cette mise à jour contient des modifications du processus de mise à jour qui améliorent considérablement les performances des futures mises à jour complètes. Ces modifications prennent effet avec la prochaine mise à jour complète après la version 2002, et visent spécifiquement à améliorer les performances de la phase d’une mise à jour complète dans laquelle les systèmes d’exploitation hôtes sont mis à jour. L’amélioration des performances des mises à jour des systèmes d’exploitation hôtes réduit considérablement la durée pendant laquelle les charges de travail des locataires sont impactées pendant les mises à jour complètes.
- L’outil de vérification de la disponibilité d’Azure Stack Hub valide désormais l’intégration d’AD Graph à l’aide de tous les ports TCP IP alloués à AD Graph.
- L’outil de syndication hors connexion a été mis à jour avec des améliorations de la fiabilité. L’outil n’est plus disponible sur GitHub et a été [déplacé vers PowerShell Gallery](https://www.powershellgallery.com/packages/Azs.Syndication.Admin/). Pour plus d’informations, consultez [Télécharger des éléments de la Place de marché vers Azure Stack Hub](azure-stack-download-azure-marketplace-item.md).
- Une nouvelle fonctionnalité de supervision est sur le point d’être ajoutée. L’alerte concernant un espace disque insuffisant pour les hôtes physiques et les machines virtuelles d’infrastructure sera corrigée automatiquement par la plateforme. Si cette action échoue, l’alerte s’affichera dans le portail d’administration Azure Stack Hub pour que l’opérateur corrige le problème.
- Améliorations apportées à la [collecte des journaux de diagnostic](./diagnostic-log-collection.md?preserve-view=true&view=azs-2002). La nouvelle expérience rationalise et simplifie la collecte des journaux de diagnostic en éliminant la nécessité de configurer un compte de stockage d’objets blob à l’avance. L’environnement de stockage est préconfiguré afin que vous puissiez envoyer des journaux avant d’ouvrir un cas de support et consacrer moins de temps à un appel de support.
- Le temps nécessaire à la [collecte proactive des journaux et à la collecte des journaux à la demande](./diagnostic-log-collection.md?preserve-view=true&view=azs-2002) a été réduit de 80 %. La collecte des journaux peut prendre plus de temps que cette valeur attendue, mais elle ne nécessite aucune action de la part des opérateurs Azure Stack Hub, sauf si la collecte des journaux échoue.
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
- [Correctif logiciel Azure Stack Hub 1.1910.84.230](https://support.microsoft.com/help/4592243)

### <a name="after-successfully-applying-the-2002-update"></a>Après l’application de la mise à jour 2002

Après l’installation de cette mise à jour, installez les correctifs logiciels applicables.

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Correctif logiciel Azure Stack Hub 1.2002.65.171](https://support.microsoft.com/topic/d743db84-df31-496b-b37c-6e5618b4cc8f)
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
::: moniker range="azs-1910"
## <a name="1910-archived-release-notes"></a>Notes de publication archivées 1910
::: moniker-end
::: moniker range="azs-1908"
## <a name="1908-archived-release-notes"></a>Notes de publication archivées 1908
::: moniker-end
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

::: moniker range="<azs-2002"
Vous pouvez accéder aux [versions antérieures des notes de publication d’Azure Stack Hub dans la galerie TechNet](https://aka.ms/azsarchivedrelnotes). Ces documents archivés sont fournis uniquement pour référence et n’impliquent aucune prise en charge de ces versions. Pour plus d’informations sur le support d’Azure Stack Hub, consultez [Stratégie de maintenance Azure Stack Hub](azure-stack-servicing-policy.md). Pour obtenir de l’aide, contactez les services de support technique Microsoft.
::: moniker-end