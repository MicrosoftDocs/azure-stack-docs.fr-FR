---
title: Notes de publication d’Azure Stack Hub 1910
description: Notes de publication des systèmes intégrés Azure Stack Hub 1910, y compris les mises à jour et les correctifs de bogues.
author: sethmanheim
ms.topic: article
ms.date: 01/25/2021
ms.author: sethm
ms.reviewer: sranthar
ms.lastreviewed: 09/09/2020
ROBOTS: NOINDEX
ms.openlocfilehash: ced055a2983fb4aabb1c31f314ccb4587f3801cc
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248638"
---
# <a name="azure-stack-hub-1910-release-notes"></a>Notes de publication d’Azure Stack Hub 1910

Cet article décrit le contenu des mises à jour d’Azure Stack Hub. La mise à jour inclut les améliorations et les correctifs logiciels de la dernière version d’Azure Stack Hub.

> [!IMPORTANT]  
> Cette mise à jour est destinée uniquement aux systèmes intégrés d’Azure Stack Hub. N’appliquez pas cette mise à jour au Kit de développement Azure Stack (ASDK).

> [!IMPORTANT]  
> Si votre instance d’Azure Stack Hub a plus de deux mises à jour de retard, elle est considérée comme non conforme. Pour bénéficier de la prise en charge, vous devez [mettre à jour avec au moins la version minimale prise en charge](../azure-stack-servicing-policy.md#keep-your-system-under-support).

## <a name="update-planning"></a>Planification des mises à jour

Avant d’appliquer la mise à jour, veillez à consulter les informations suivantes :

- [Liste de vérification des activités avant et après l’application de la mise à jour](../release-notes-checklist.md)
- [Problèmes connus](../known-issues.md)
- [Correctifs logiciels](#hotfixes)
- [Mises à jour de sécurité](../release-notes-security-updates.md)

Pour obtenir de l’aide sur la résolution des problèmes liés aux mises à jour et au processus de mise à jour, consultez [Résoudre les problèmes liés aux correctifs logiciels et aux mises à jour pour Azure Stack Hub](../azure-stack-troubleshooting.md).

## <a name="download-the-update"></a>Télécharger la mise à jour

Vous pouvez télécharger le package de mise à jour d’Azure Stack Hub à l’aide de [l’outil téléchargeur des mises à jour d’Azure Stack Hub](https://aka.ms/azurestackupdatedownload).

## <a name="1910-build-reference"></a>Référence de la build 1910

Le numéro de build de la mise à jour 1910 d’Azure Stack Hub est **1.1910.0.58**.

### <a name="update-type"></a>Type de mise à jour

Depuis la version 1908, le système d’exploitation sous-jacent sur lequel Azure Stack Hub s’exécute a été mis à jour vers Windows Server 2019. Cette mise à jour apporte des améliorations fondamentales et permet l’ajout de fonctionnalités supplémentaires dans Azure Stack Hub.

Le type de build de la mise à jour 1910 d’Azure Stack Hub est **Express**.

Le package de la mise à jour 1910 étant d’une taille supérieure à celle des mises à jour précédentes, les temps de téléchargement sont plus longs. La mise à jour reste à l’état de **préparation** pendant une longue période, et les opérateurs peuvent s’attendre à ce que ce processus prenne plus de temps que les mises à jour précédentes. Le temps prévu pour la mise à jour 1910 est d’environ 10 heures, quel que soit le nombre de nœuds physiques dans votre environnement Azure Stack Hub. La durée d’exécution exacte de la mise à jour dépend généralement de la capacité utilisée sur votre système par les charges de travail de locataire, de la connectivité réseau de votre système (s’il est connecté à Internet) et des caractéristiques de vos composants matériels système. Il n’est pas rare d’observer des durées d’exécution plus longues que la durée prévue, mais cela ne nécessite aucune action de la part des opérateurs Azure Stack Hub, sauf en cas d’échec de la mise à jour. Cette durée d’exécution approximative est propre à la mise à jour 1910. Elle ne doit pas être comparée aux autres mises à jour d’Azure Stack Hub.

Pour plus d’informations sur les types de build de mise à jour, consultez [Gérer les mises à jour dans Azure Stack Hub](../azure-stack-updates.md).

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Nouveautés

<!-- What's new, also net new experiences and features. -->

- Le portail administrateur montre désormais les adresses IP des points de terminaison privilégiés dans le menu des propriétés de région pour faciliter leur découverte. Il montre aussi le serveur de temps et les redirecteurs DNS actuellement configurés. Pour plus d’informations, consultez [Utiliser le point de terminaison privilégié dans Azure Stack Hub](../azure-stack-privileged-endpoint.md).

- Le système de contrôle d’intégrité et de supervision d’Azure Stack Hub peut désormais déclencher des alertes pour divers composants matériels en cas d’erreur. Ces alertes nécessitent une configuration supplémentaire. Pour plus d’informations, consultez [Superviser les composants matériels d’Azure Stack Hub](../azure-stack-hardware-monitoring.md).

- [Prise en charge de cloud-init pour Azure Stack Hub](/azure/virtual-machines/linux/using-cloud-init) : Cloud-init est une approche courante permettant de personnaliser une machine virtuelle Linux lors de son premier démarrage. Vous pouvez utiliser cloud-init pour installer des packages et écrire des fichiers, ou encore pour configurer des utilisateurs ou des paramètres de sécurité. cloud-init étant appelé pendant le processus de démarrage initial, aucune autre étape ni aucun agent ne sont nécessaires pour appliquer votre configuration. Les images Ubuntu de la Place de marché ont été mises à jour pour prendre en charge cloud-init pour le provisionnement.

- Azure Stack Hub prend désormais en charge toutes les versions de l’agent Windows Azure Linux en tant qu’Azure.

- Une nouvelle version des modules PowerShell d’administration d’Azure Stack Hub est disponible. <!-- For more information, see -->

- Les nouveaux modules d’abonnés Azure PowerShell ont été mis en production pour Azure Stack Hub le 15 avril 2020. Les modules Azure RM utilisés continuent de fonctionner, mais ne seront plus mis à jour après la build 2002.

- Ajout de l’applet de commande **Set-AzSDefenderManualUpdate** dans le point de terminaison privilégié (PEP) pour configurer la mise à jour manuelle des définitions Windows Defender dans l’infrastructure Azure Stack Hub. Pour plus d’informations, consultez [Mettre à jour l’antivirus Windows Defender sur Azure Stack Hub](../azure-stack-security-av.md).

- Ajout de l’applet de commande **Set-AzSDnsForwarder** dans le point de terminaison privilégié (PEP) pour changer les paramètres de redirecteur des serveurs DNS dans Azure Stack Hub. Pour plus d’informations sur la configuration DNS, consultez [Intégration des services DNS au centre de données Azure Stack Hub](../azure-stack-integrate-dns.md).

- Ajout de la prise en charge de la gestion des **clusters Kubernetes** à l’aide du [moteur AKS](../../user/azure-stack-kubernetes-aks-engine-overview.md). À partir de cette mise à jour, les clients peuvent déployer des clusters Kubernetes de production. Le moteur AKS permet aux utilisateurs d’effectuer les opérations suivantes :
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

- Azure Stack Hub a amélioré sa capacité à corriger automatiquement certains problèmes liés aux correctifs logiciels et aux mises à jour, qui entraînaient des échecs de mise à jour ou empêchaient les opérateurs de lancer une mise à jour d’Azure Stack Hub. Par conséquent, le groupe **Test-AzureStack -UpdateReadiness** comprend moins de tests. Pour plus d’informations, consultez [Valider l’état du système Azure Stack Hub](../azure-stack-diagnostic-test.md#groups). Les trois tests suivants font encore partie du groupe **UpdateReadiness** :

  - **AzSInfraFileValidation**
  - **AzSActionPlanStatus**
  - **AzsStampBMCSummary**

- Ajout d’une règle d’audit pour signaler le moment où un périphérique externe (par exemple une clé USB) est monté sur un nœud de l’infrastructure Azure Stack Hub. Le journal d’audit est émis via syslog et s’affiche sous la forme **Microsoft-Windows-Security-Auditing : 6416|Événements Plug-and-Play**. Pour plus d’informations sur la façon de configurer le client syslog, consultez [Transfert Syslog](../azure-stack-integrate-security.md).

- Azure Stack Hub utilise maintenant des clés RSA 4096 bits pour les certificats internes. La rotation des secrets internes remplace les anciens certificats 2048 bits par des certificats d’une longueur de 4096 bits. Pour plus d’informations sur la rotation des secrets dans Azure Stack Hub, consultez [Effectuer une rotation des secrets dans Azure Stack Hub](../azure-stack-rotate-secrets.md).

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

   Ces changements sont également reflétés dans la documentation sur la [proposition IPsec/IKE par défaut](../../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters).

- Le service Infrastructure Backup améliore la logique qui calcule l’espace disponible souhaité pour les sauvegardes au lieu de s’appuyer sur un seuil fixe. Le service se base sur la taille d’une sauvegarde, une stratégie de conservation, une réserve et l’utilisation actuelle de l’emplacement de stockage externe pour déterminer si un avertissement doit être adressé à l’opérateur.

### <a name="changes"></a>Modifications

- Quand vous téléchargez des éléments de la Place de marché d’Azure vers Azure Stack Hub, une nouvelle interface utilisateur vous permet de spécifier une version de l’élément, au cas où il en existerait plusieurs. La nouvelle interface utilisateur est disponible dans les deux scénarios, connecté et déconnecté. Pour plus d’informations, consultez [Télécharger des éléments de la Place de marché d’Azure vers Azure Stack Hub](../azure-stack-download-azure-marketplace-item.md).  

- Depuis la version 1910, le système Azure Stack Hub **nécessite** un espace IP interne privé /20 supplémentaire. Pour plus d’informations, consultez [Planification de l’intégration réseau pour Azure Stack](../azure-stack-network.md).
  
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

Pour plus d’informations sur les mises à jour de sécurité dans cette mise à jour d’Azure Stack Hub, consultez [Mises à jour de sécurité Azure Stack Hub](../release-notes-security-updates.md).

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

Après l’installation de cette mise à jour, installez les correctifs logiciels applicables. Pour plus d’informations, consultez notre [stratégie de maintenance](../azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Correctif logiciel Azure Stack Hub 1.1910.63.186](https://support.microsoft.com/help/4574735)
