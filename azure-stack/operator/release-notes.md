---
title: Notes de publication d’Azure Stack Hub
description: Notes de publication des systèmes intégrés Azure Stack Hub, y compris les mises à jour et les correctifs de bogues.
author: sethmanheim
ms.topic: article
ms.date: 03/20/2020
ms.author: sethm
ms.reviewer: prchint
ms.lastreviewed: 03/18/2020
ms.openlocfilehash: cd8569e5ea0f3537aa915207b7d52141d9444afa
ms.sourcegitcommit: fec2b21e80c8049a823baeaf2b642802ccdcfb67
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80229551"
---
# <a name="azure-stack-hub-release-notes"></a>Notes de publication d’Azure Stack Hub

Cet article décrit le contenu des mises à jour d’Azure Stack Hub. La mise à jour inclut les améliorations et les correctifs logiciels de la dernière version d’Azure Stack Hub.

Pour accéder aux notes de publication d'une autre version, utilisez le menu déroulant de sélection de la version, situé au-dessus de la table des matières à gauche.

::: moniker range=">=azs-1907"
> [!IMPORTANT]  
> Cette mise à jour est destinée uniquement aux systèmes intégrés d’Azure Stack Hub. N’appliquez pas cette mise à jour au Kit de développement Azure Stack (ASDK).
::: moniker-end
::: moniker range="<azs-1907"
> [!IMPORTANT]  
> Si votre instance d’Azure Stack Hub a plus de deux mises à jour de retard, elle est considérée comme non conforme. Pour bénéficier de la prise en charge, vous devez [mettre à jour avec au moins la version minimale prise en charge](azure-stack-servicing-policy.md#keep-your-system-under-support).
::: moniker-end

## <a name="update-planning"></a>Planification des mises à jour

Avant d’appliquer la mise à jour, veillez à consulter les informations suivantes :

- [Problèmes connus](known-issues.md)
- [Mises à jour de sécurité](release-notes-security-updates.md)
- [Liste de vérification des activités avant et après l’application de la mise à jour](release-notes-checklist.md)

Pour obtenir de l’aide sur la résolution des problèmes liés aux mises à jour et au processus de mise à jour, consultez [Résoudre les problèmes liés aux correctifs logiciels et aux mises à jour pour Azure Stack Hub](azure-stack-updates-troubleshoot.md).

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->
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
- Améliorations apportées à la [collecte des journaux de diagnostic](azure-stack-diagnostic-log-collection-overview-tzl.md). La nouvelle expérience rationalise et simplifie la collecte des journaux de diagnostic en éliminant la nécessité de configurer un compte de stockage d’objets blob à l’avance. L’environnement de stockage est préconfiguré afin que vous puissiez envoyer des journaux avant d’ouvrir un cas de support et consacrer moins de temps à un appel de support.
- Le temps nécessaire à la [collecte proactive des journaux et à la collecte des journaux à la demande](azure-stack-diagnostic-log-collection-overview-tzl.md) a été réduit de 80 %. La collecte des journaux peut prendre plus de temps que cette valeur attendue, mais elle ne nécessite aucune action de la part des opérateurs Azure Stack Hub, sauf si la collecte des journaux échoue.
- La progression du téléchargement d’un package de mise à jour Azure Stack Hub est désormais visible dans le panneau de mise à jour après le lancement d’une mise à jour. Seuls sont concernés les systèmes connectés Azure Stack Hub qui choisissent de [préparer les packages de mise à jour par le biais du téléchargement automatique](azure-stack-update-prepare-package.md#automatic-download-and-preparation-for-update-packages).
- Améliorations de la fiabilité de l’agent hôte du contrôleur de réseau.
- Introduction d’un nouveau micro-service nommé DNS Orchestrator qui améliore la logique de résilience pour les services DNS internes au cours des mises à jour et des correctifs.
- Ajout d’une nouvelle validation de demande visant à faire échouer les URI d’objet blob non valides pour le paramètre de compte de stockage de diagnostics de démarrage lors de la création de machines virtuelles.
- Améliorations de la correction automatique et de la journalisation pour Rdagent et l’agent Host, deux services sur l’hôte qui facilitent les opérations CRUD de machine virtuelle.
- Ajout d’une nouvelle fonctionnalité à la gestion de la marketplace qui permet d’empêcher les administrateurs de télécharger des produits de la marketplace qui ne sont pas compatibles avec leurs environnements Azure Stack, en raison de différents attributs, tels que la version Azure Stack ou le modèle de facturation.

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
  
### <a name="fixes"></a>Correctifs

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Correction d’un problème où l’ajout de plusieurs adresses IP publiques sur la même carte réseau d’une machine virtuelle provoquait des problèmes de connectivité Internet. À présent, une carte réseau avec deux adresses IP publiques fonctionne comme prévu.
- Résolution d’un problème qui provoquait la génération d’une alerte par le système, indiquant que le répertoire de démarrage d’Azure AD devait être configuré.
- Résolution d’un problème qui provoquait l’échec de la fermeture automatique d’une alerte. L’alerte indiquait que le répertoire de démarrage d’Azure AD devait être configuré, mais ne se fermait pas même après l’atténuation du problème.
- Résolution d’un problème qui provoquait l’échec des mises à jour pendant la phase de leur préparation à la suite de défaillances internes du fournisseur de ressources de mise à jour.
- Résolution d’un problème provoquant l’échec des opérations du fournisseur de ressources de module complémentaire après l’exécution de la rotation des secrets Azure Stack Hub.
- Résolution d’un problème qui était une cause courante des échecs de mise à jour d’Azure Stack Hub en raison de la sollicitation de la mémoire sur le rôle ERCS.
- Correction d’un bogue dans le panneau de mise à jour lié au fait que l’état de la mise à jour indiquait **Installation** au lieu de **Préparation** pendant la phase de préparation d’une mise à jour d’Azure Stack Hub.
- Résolution d’un problème lié au fait que la fonctionnalité RSC sur les commutateurs physiques créait des incohérences et abandonnait le trafic circulant via un équilibreur de charge. La fonctionnalité RSC est désormais désactivée par défaut.
- Résolution d’un problème lié au fait que l’ajout d’une adresse IP secondaire à la machine virtuelle provoquait des problèmes RDP.
- Résolution d’un problème lié au fait que l’adresse MAC d’une carte réseau était mise en cache et que l’affectation de cette adresse à une autre ressource provoquait des échecs de déploiement de machine virtuelle.
- Résolution d’un problème lié au fait que la licence des images de machine virtuelle Windows du canal de vente au détail n’a pas pu être activée par AVMA.
- Résolution d’un problème qui entraînait l’échec de la création de machines virtuelles si le nombre de cœurs virtuels demandés par la machine virtuelle était égal au nombre de cœurs physiques du nœud. Nous autorisons maintenant les machines virtuelles à avoir un nombre de cœurs virtuels égal ou inférieur au nombre de cœurs physiques du nœud.
- Résolution d’un problème lié au fait que nous n’autorisons pas la définition du type de licence sur « null » pour basculer les images avec paiement à l’utilisation vers BYOL.
- Résolution d’un problème empêchant l’ajout d’extensions à un groupe de machines virtuelles identiques.

## <a name="security-updates"></a>Mises à jour de sécurité

Pour plus d’informations sur les mises à jour de sécurité dans cette mise à jour d’Azure Stack Hub, consultez [Mises à jour de sécurité Azure Stack Hub](release-notes-security-updates.md).

## <a name="update-planning"></a>Planification des mises à jour

Avant d’appliquer la mise à jour, veillez à consulter les informations suivantes :

- [Problèmes connus](known-issues.md)
- [Mises à jour de sécurité](release-notes-security-updates.md)
- [Liste de vérification des activités avant et après l’application de la mise à jour](release-notes-checklist.md)

## <a name="download-the-update"></a>Télécharger la mise à jour

Vous pouvez télécharger la mise à jour 2002 d’Azure Stack Hub à partir de la [page de téléchargement d’Azure Stack Hub](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes"></a>Correctifs logiciels

Azure Stack Hub publie régulièrement des correctifs logiciels. Veillez à installer le dernier correctif logiciel Azure Stack Hub pour la version 1910 avant de mettre à jour Azure Stack Hub vers la version 2002.

> [!NOTE]
> Les versions des correctifs logiciels Azure Stack Hub sont cumulatives. Il vous suffit d’installer le dernier correctif logiciel afin d’obtenir l’ensemble des correctifs logiciels inclus dans les versions précédentes de correctifs logiciels pour cette version.

Les correctifs logiciels Azure Stack Hub s’appliquent uniquement aux systèmes intégrés Azure Stack Hub. N’essayez pas d’installer des correctifs logiciels sur l’ASDK.

### <a name="prerequisites-before-applying-the-2002-update"></a>Configuration requise : Avant d’appliquer la mise à jour 2002

La version 2002 d’Azure Stack Hub doit être appliquée sur la version 1910 avec les correctifs logiciels suivants :

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Correctif logiciel Azure Stack Hub 1.1910.24.108](https://support.microsoft.com/help/4541350)

### <a name="after-successfully-applying-the-2002-update"></a>Après l’application de la mise à jour 2002

Après l’installation de cette mise à jour, installez les correctifs logiciels applicables. Pour plus d’informations, consultez notre [stratégie de maintenance](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- Aucun correctif Azure Stack Hub n’est disponible pour 2002.
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
  - Bénéficier du support CSS et d’ingénierie pour leurs déploiements.

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

- Depuis la version 1910, le système Azure Stack Hub **nécessite** un espace IP interne privé /20 supplémentaire. Ce réseau est privé pour le système Azure Stack Hub et peut être réutilisé sur plusieurs systèmes Azure Stack Hub dans votre centre de données. Bien que ce réseau soit privé pour Azure Stack Hub, il ne doit pas chevaucher un réseau de votre centre de données. L’espace IP privé /20 est divisé en plusieurs réseaux qui permettent d’exécuter l’infrastructure Azure Stack Hub sur des conteneurs (comme indiqué dans les [notes de publication de la version 1905](release-notes.md?view=azs-1905)). L’objectif de l’exécution de l’infrastructure Azure Stack Hub dans des conteneurs est d’optimiser l’utilisation et d’améliorer le niveau de performance. L’espace IP privé /20 est également utilisé pour favoriser les efforts en cours qui réduiront l’espace IP routable nécessaire avant le déploiement.

  - Notez que la présence de l’entrée /20 sera un prérequis pour installer la prochaine mise à jour d’Azure Stack Hub après la version 1910. Vous ne pourrez pas installer cette mise à jour si vous n’avez pas préparé l’entrée /20 comme indiqué ci-après dans les étapes de correction. Une alerte restera affichée dans le portail d’administration tant que les étapes de correction n’auront pas été effectuées. Consultez l’article sur l’[intégration au réseau du centre de données](azure-stack-network.md#private-network) pour comprendre comment ce nouvel espace privé est consommé.

  - Étapes de correction : pour corriger, suivez les instructions permettant d’[ouvrir une session PEP](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Préparez une [plage d’adresses IP internes privées](azure-stack-network.md#logical-networks) de taille /20 et exécutez l’applet de commande suivante (disponible uniquement à partir de la version 1910) dans la session du point de terminaison privilégié, en utilisant l’exemple suivant : `Set-AzsPrivateNetwork -UserSubnet 100.87.0.0/20`. Si l’opération réussit, vous recevez le message **Azs Internal Network range added to the config** (La plage du réseau interne AZS a été ajoutée à la configuration). L’alerte disparaît alors du portail d’administration. Le système Azure Stack Hub peut maintenant être mis à jour vers la prochaine version.
  
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

## <a name="update-planning"></a>Planification des mises à jour

Avant d’appliquer la mise à jour, veillez à consulter les informations suivantes :

- [Problèmes connus](known-issues.md)
- [Mises à jour de sécurité](release-notes-security-updates.md)
- [Liste de vérification des activités avant et après l’application de la mise à jour](release-notes-checklist.md)

## <a name="download-the-update"></a>Télécharger la mise à jour

Vous pouvez télécharger la mise à jour 1910 d’Azure Stack Hub à partir de la [page de téléchargement d’Azure Stack Hub](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes"></a>Correctifs logiciels

Azure Stack Hub publie régulièrement des correctifs logiciels. Veillez à installer le dernier correctif logiciel Azure Stack Hub pour la version 1908 avant de mettre à jour Azure Stack Hub vers la version 1910.

> [!NOTE]
> Les versions des correctifs logiciels Azure Stack Hub sont cumulatives. Il vous suffit d’installer le dernier correctif logiciel afin d’obtenir l’ensemble des correctifs logiciels inclus dans les versions précédentes de correctifs logiciels pour cette version.

Les correctifs logiciels Azure Stack Hub s’appliquent uniquement aux systèmes intégrés Azure Stack Hub. N’essayez pas d’installer des correctifs logiciels sur l’ASDK.

### <a name="prerequisites-before-applying-the-1910-update"></a>Configuration requise : Avant d’appliquer la mise à jour 1910

La version 1910 d’Azure Stack Hub doit être appliquée sur la version 1908 avec les correctifs logiciels suivants :

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Correctif logiciel Azure Stack Hub 1.1908.19.62](https://support.microsoft.com/help/4541349)

### <a name="after-successfully-applying-the-1910-update"></a>Après l’application de la mise à jour 1910

Après l’installation de cette mise à jour, installez les correctifs logiciels applicables. Pour plus d’informations, consultez notre [stratégie de maintenance](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Correctif logiciel Azure Stack Hub 1.1910.24.108](https://support.microsoft.com/help/4541350)
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

## <a name="download-the-update"></a><a name="download-the-update-1908"></a>Télécharger la mise à jour

Vous pouvez télécharger la mise à jour 1908 d’Azure Stack Hub à partir de la [page de téléchargement d’Azure Stack Hub](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes"></a>Correctifs logiciels

Azure Stack Hub publie régulièrement des correctifs logiciels. Veillez à installer le dernier correctif logiciel Azure Stack Hub pour la version 1907 avant de mettre à jour Azure Stack Hub vers la version 1908.

Les correctifs logiciels d’Azure Stack Hub s’appliquent uniquement aux systèmes intégrés Azure Stack Hub. N’essayez pas de les installer sur l’ASDK.

### <a name="prerequisites-before-applying-the-1908-update"></a>Configuration requise : Avant d’appliquer la mise à jour 1908

La version 1908 d’Azure Stack Hub doit être appliquée sur la version 1907 avec les correctifs logiciels suivants :

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Correctif logiciel Azure Stack Hub 1.1907.26.70](https://support.microsoft.com/help/4541348)

La mise à jour 1908 d’Azure Stack Hub nécessite la **version 2.1 ou ultérieure d’OEM pour Azure Stack Hub** du fournisseur de matériel de votre système. Les mises à jour OEM appliquent les mises à jour des pilotes et des microprogrammes aux composants matériels de votre système Azure Stack Hub. Pour plus d’informations sur l’application des mises à jour OEM, consultez [Appliquer des mises à jour de fabricants d’ordinateurs à Azure Stack Hub](azure-stack-update-oem.md)

### <a name="after-successfully-applying-the-1908-update"></a>Après l’application de la mise à jour 1908

Après l’installation de cette mise à jour, installez les correctifs logiciels applicables. Pour plus d’informations, consultez notre [stratégie de maintenance](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Correctif logiciel Azure Stack Hub 1.1908.19.62](https://support.microsoft.com/help/4541349)
::: moniker-end

::: moniker range="azs-1907"
## <a name="1907-build-reference"></a>Référence de build 1907

Le numéro de build de la mise à jour 1907 d’Azure Stack Hub est **1.1907.0.20**.

### <a name="update-type"></a>Type de mise à jour

Le type de build de la mise à jour 1907 d’Azure Stack Hub est **Express**. Pour plus d’informations sur les types de build de mise à jour, consultez l’article [Gérer les mises à jour dans Azure Stack Hub](azure-stack-updates.md). D'après des tests internes, la mise à jour 1907 prend environ 13 heures.

- La durée d’exécution exacte des mises à jour dépend généralement de la capacité utilisée sur votre système par les charges de travail client, de la connectivité réseau de votre système (s’il est connecté à Internet) et de la configuration de votre matériel système.
- Il n’est pas rare d’observer des durées d’exécution plus longues que la durée prévue, mais cela ne nécessite aucune action de la part des opérateurs Azure Stack Hub (sauf si la mise à jour échoue).
- Cette durée d’exécution approximative est propre à la mise à jour 1907. Elle ne doit pas être comparée aux autres mises à jour d’Azure Stack Hub.

## <a name="whats-in-this-update"></a>Éléments de cette mise à jour

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Nouveautés

<!-- What's new, also net new experiences and features. -->

- Mise à la disposition générale du service de collecte des journaux de diagnostic Azure Stack Hub pour faciliter et améliorer la collecte des journaux de diagnostic. Le service de collecte des journaux de diagnostic Azure Stack Hub propose un moyen simplifié de collecter et de partager des journaux de diagnostic avec les services de support technique Microsoft. Ce service de collecte des journaux de diagnostic offre une nouvelle expérience utilisateur dans le portail d’administration Azure Stack Hub. Il permet aux opérateurs de configurer le chargement automatique des journaux de diagnostic dans un objet blob de stockage lorsque certaines alertes critiques sont générées. Ce service peut également être utilisé pour effectuer la même opération à la demande. Pour plus d’informations, consultez l'article [Collecte des journaux de diagnostic](azure-stack-diagnostic-log-collection-overview.md).

- Mise à la disposition générale de la validation de l’infrastructure réseau Azure Stack Hub dans le cadre de l’outil de validation Azure Stack Hub **Test-AzureStack**. L’infrastructure réseau Azure Stack Hub fera partie de **Test-AzureStack** pour identifier toute défaillance sur l’infrastructure d’Azure Stack Hub. Le test vérifie la connectivité de l’infrastructure réseau en contournant le réseau à définition logicielle Azure Stack Hub. Il démontre la connectivité d’une adresse IP virtuelle publique aux redirecteurs DNS, serveurs NTP et points de terminaison d'identité configurés. Il vérifie aussi la connectivité à Azure quand Azure AD est utilisé en tant que fournisseur d’identité, ou au serveur fédéré quand ADFS est utilisé. Pour plus d’informations, consultez l’article [Outil de validation Azure Stack Hub](azure-stack-diagnostic-test.md).

- Ajout d’une procédure de rotation des secrets internes pour faire tourner les certificats TLS SQL internes en fonction des besoins pendant une mise à jour du système.

### <a name="improvements"></a>Améliorations

<!-- Changes and product improvements with tangible customer-facing value. -->

- Le panneau de mise à jour Azure Stack Hub affiche à présent une heure **Dernière étape terminée** pour les mises à jour actives. Pour consulter cette nouvelle information, accédez au panneau de mise à jour et cliquez sur une mise à jour en cours d’exécution. **Dernière étape terminée** est ensuite disponible dans la section **Détails de l'exécution de la mise à jour**.

- Améliorations apportées aux actions de l’opérateur **Start-AzureStack** et **Stop-AzureStack**. Le temps de démarrage d’Azure Stack Hub a été réduit d’environ 50 %. Le temps d’arrêt d’Azure Stack Hub a été réduit d’environ 30 %. Les temps de démarrage et d’arrêt moyens sont les mêmes lorsque le nombre de nœuds augmente dans une unité d’échelle.

- Amélioration de la gestion des erreurs pour l’outil Place de marché déconnecté. Si un téléchargement échoue ou réussit partiellement lors de l'utilisation de **Export-AzSOfflineMarketplaceItem**, un message s’affiche avec plus de détails sur l’erreur et sur les étapes d’atténuation possibles.

- Amélioration des performances de création de disques managés à partir d’un objet blob de pages/instantané de grande taille. Auparavant, cela déclenchait un délai d’expiration lors de la création d’un disque de grande taille.  

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/127669774/home -->
- Amélioration de la vérification d'intégrité du disque virtuel avant l’arrêt d’un nœud pour éviter le détachement inattendu du disque.

- Amélioration du stockage des journaux internes pour les opérations de l’administrateur. Cet ajout améliore les performances et la fiabilité lors des opérations de l’administrateur en réduisant la consommation de mémoire et de stockage des processus de journalisation interne. Vous remarquerez peut-être une amélioration des délais de chargement des pages du panneau de mise à jour dans le portail administrateur. Dans le cadre de cette amélioration, les journaux des mises à jour datant de plus de six mois ne seront plus disponibles dans le système. Si vous avez besoin de ces journaux, veillez à [télécharger le récapitulatif](azure-stack-apply-updates.md) de toutes les exécutions de mises à jour remontant à plus de six mois avant d’effectuer la mise à jour 1907.

### <a name="changes"></a>Modifications

- Azure Stack Hub version 1907 contient une alerte d’avertissement qui indique aux opérateurs de bien mettre à jour le package OEM de leur système vers la version 2.1 ou ultérieure avant d’effectuer la mise à jour vers la version 1908. Pour plus d’informations sur l’application de mises à jour OEM dans Azure Stack Hub, consultez [Appliquer des mises à jour de fabricants d’ordinateurs à Azure Stack Hub](azure-stack-update-oem.md).

- Ajout d’une nouvelle règle de trafic sortant (HTTPS) afin d’activer la communication pour le service de collecte des journaux de diagnostic Azure Stack Hub. Pour plus d’informations, consultez [Intégration au centre de données Azure Stack Hub - Publier des points de terminaison](azure-stack-integrate-endpoints.md#ports-and-urls-outbound).

- Désormais, le service de sauvegarde d’infrastructure supprime partiellement les sauvegardes téléchargées si la capacité de l’emplacement de stockage externe est insuffisante.

- Les sauvegardes d’infrastructure n’incluent plus la sauvegarde des données des services de domaine. Ce changement s’applique uniquement aux systèmes qui utilisent Azure Active Directory en tant que fournisseur d’identité.

- Nous vérifions désormais que l'ingestion d'une image dans le panneau **Calcul -> Images de machine virtuelle** est de type objet blob de pages.

- La commande de point de terminaison privilégié **Set-BmcCredential** met à jour les informations d’identification dans le contrôleur BMC (Baseboard Management Controller).

### <a name="fixes"></a>Correctifs

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there's an SR/ICM associated to it. -->
- Résolution d’un problème lié au fait que le serveur de publication, l’offre et la référence SKU d’un modèle Resource Manager étaient considérés comme sensibles à la casse : l’image n’était pas extraite pour le déploiement, sauf si la casse de ses paramètres était identique à celle du serveur de publication, de l’offre et de la référence SKU.

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/129536438/home -->
- Résolution d'un problème lié à l'échec des sauvegardes avec un message d'erreur **PartialSucceeded**, en raison des délais d'expiration lors de la sauvegarde des métadonnées du service de stockage.  

- Résolution d'un problème lié au fait que la suppression d’abonnements utilisateur aboutissait à des ressources orphelines.

- Résolution d’un problème lié au fait que le champ de description n’était pas enregistré au moment de la création d’une offre.

- Correction d'un problème dans lequel un utilisateur avec des autorisations **Lecture seule** pouvait créer, modifier et supprimer des ressources. Désormais, l'utilisateur ne peut créer des ressources que lorsque l'autorisation **Contributeur** lui est attribuée. 

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/127772311/home -->
- Résolution d’un problème lié à l'échec de la mise à jour en raison d'un fichier DLL verrouillé par l’hôte du fournisseur WMI.

- Résolution d’un problème lié au service de mise à jour qui empêchait l’affichage des mises à jour disponibles dans la vignette de mise à jour ou le fournisseur de ressources. Ce problème a été détecté dans la version 1906 et résolu avec le correctif logiciel [KB4511282](https://support.microsoft.com/help/4511282/).

- Résolution d’un problème lié à l'échec des mises à jour en raison de la non-intégrité du plan de gestion suite à une mauvaise configuration. Ce problème a été détecté dans la version 1906 et résolu avec le correctif logiciel [KB4512794](https://support.microsoft.com/help/4512794/).

- Résolution d’un problème qui empêchait les utilisateurs de mener à bien le déploiement d’images tierces à partir de la Place de marché. Ce problème a été détecté dans la version 1906 et résolu avec le correctif logiciel [KB4511259](https://support.microsoft.com/help/4511259/).

- Résolution d’un problème lié à l'impossibilité de créer une machine virtuelle à partir d'images managées en raison du blocage du service de gestion des images utilisateur. Ce problème a été détecté dans la version 1906 et résolu avec le correctif logiciel [KB4512794](https://support.microsoft.com/help/4512794/).

- Résolution d’un problème lié à l'échec des opérations CRUD de machine virtuelle en raison de la non-actualisation comme prévu du cache de la passerelle d'application. Ce problème a été détecté dans la version 1906 et résolu avec le correctif logiciel [KB4513119](https://support.microsoft.com/en-us/help/4513119/).

- Résolution d’un problème lié au fournisseur de ressources d'intégrité qui affectait la disponibilité des panneaux de région et d'alerte dans le portail administrateur. Ce problème a été détecté dans la version 1906 et résolu avec le correctif logiciel [KB4512794](https://support.microsoft.com/help/4512794).

## <a name="security-updates"></a>Mises à jour de sécurité

Pour plus d’informations sur les mises à jour de sécurité dans cette mise à jour d’Azure Stack Hub, consultez [Mises à jour de sécurité Azure Stack Hub](release-notes-security-updates.md).

## <a name="update-planning"></a>Planification des mises à jour

Avant d’appliquer la mise à jour, veillez à consulter les informations suivantes :

- [Problèmes connus](known-issues.md)
- [Mises à jour de sécurité](release-notes-security-updates.md)
- [Liste de vérification des activités avant et après l’application de la mise à jour](release-notes-checklist.md)

## <a name="download-the-update"></a>Télécharger la mise à jour

Vous pouvez télécharger la mise à jour 1907 d’Azure Stack Hub à partir de la [page de téléchargement d’Azure Stack Hub](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes"></a>Correctifs logiciels

Azure Stack Hub publie régulièrement des correctifs logiciels. Veillez à installer le dernier correctif logiciel Azure Stack Hub pour la version 1906 avant de mettre à jour Azure Stack Hub vers la version 1907.

Les correctifs logiciels d’Azure Stack Hub s’appliquent uniquement aux systèmes intégrés Azure Stack Hub. N’essayez pas de les installer sur l’ASDK.

### <a name="before-applying-the-1907-update"></a>Avant d’appliquer la mise à jour 1907

La version 1907 d’Azure Stack Hub doit être appliquée sur la version 1906 avec les correctifs logiciels suivants :

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Correctif logiciel Azure Stack Hub 1.1906.15.60](https://support.microsoft.com/help/4524559)

### <a name="after-successfully-applying-the-1907-update"></a>Une fois la mise à jour 1907 correctement appliquée

Après l’installation de cette mise à jour, installez les correctifs logiciels applicables. Pour plus d’informations, consultez notre [stratégie de maintenance](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Correctif logiciel Azure Stack Hub 1.1907.26.70](https://support.microsoft.com/help/4541348)
::: moniker-end

::: moniker range=">=azs-1907"
## <a name="automatic-update-notifications"></a>Notifications de mise à jour automatique

Les systèmes qui peuvent accéder à Internet à partir du réseau d’infrastructure voient le message **Mise à jour disponible** dans le portail opérateur. Les systèmes sans accès à Internet peuvent télécharger et importer le fichier .zip avec le fichier .xml correspondant.

> [!TIP]  
> Pour rester informé des publications des correctifs logiciels d’Azure Stack Hub, abonnez-vous aux flux *RSS* ou *Atom* suivants :
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="archive"></a>Archivage

Pour accéder aux notes de publication archivées d’une ancienne version, utilisez le menu déroulant de sélection de la version situé au-dessus de la table des matières à gauche, puis sélectionnez la version qui vous intéresse.

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble de la gestion des mises à jour dans Azure Stack Hub, consultez [Gérer les mises à jour dans Azure Stack Hub - Vue d’ensemble](azure-stack-updates.md).  
- Pour plus d’informations sur la façon d’appliquer des mises à jour avec Azure Stack Hub, consultez [Effectuer des mises à jour dans Azure Stack Hub](azure-stack-apply-updates.md).
- Pour passer en revue la stratégie de maintenance d’Azure Stack Hub et pour connaître la marche à suivre afin de conserver votre système dans un état de prise en charge, consultez [Stratégie de maintenance Azure Stack Hub](azure-stack-servicing-policy.md).  
- Pour utiliser le point de terminaison privilégié (PEP) afin de superviser et de reprendre les mises à jour, consultez [Superviser les mises à jour dans Azure Stack Hub à l’aide du point de terminaison privilégié](azure-stack-monitor-update.md).
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
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

::: moniker range="<azs-1907"
Vous pouvez accéder aux [versions antérieures des notes de publication d’Azure Stack Hub dans la galerie TechNet](https://aka.ms/azsarchivedrelnotes). Ces documents archivés sont fournis uniquement pour référence et n’impliquent aucune prise en charge de ces versions. Pour plus d’informations sur le support d’Azure Stack Hub, consultez [Stratégie de maintenance Azure Stack Hub](azure-stack-servicing-policy.md). Pour obtenir de l’aide, contactez les services de support technique Microsoft.
::: moniker-end


