---
title: Notes de publication Azure Stack | Microsoft Docs
description: 'Découvrez les mises à jour des systèmes intégrés Azure Stack : nouveautés et emplacement de téléchargement des mises à jour.'
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
ms.date: 12/18/2019
ms.author: sethm
ms.reviewer: prchint
ms.lastreviewed: 11/22/2019
ms.openlocfilehash: 8c124f44ed938108a4b23cea44642b7e275eb04d
ms.sourcegitcommit: c0fe1ac8c38d0475621ad8022db96983f9e5ae79
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2019
ms.locfileid: "75188389"
---
# <a name="azure-stack-updates-release-notes"></a>Mises à jour Azure Stack : notes de publication

*S’applique à : systèmes intégrés Azure Stack*

Cet article décrit le contenu des packages de mise à jour d’Azure Stack. La mise à jour inclut des améliorations, nouveautés et correctifs pour cette version d’Azure Stack.

Pour accéder aux notes de publication d'une autre version, utilisez le menu déroulant de sélection de la version, situé au-dessus de la table des matières à gauche.

::: moniker range=">=azs-1906"
> [!IMPORTANT]  
> Cette mise à jour est destinée uniquement aux systèmes intégrés d’Azure Stack. N’appliquez pas cette mise à jour au Kit de développement Azure Stack.
::: moniker-end
::: moniker range="<azs-1906"
> [!IMPORTANT]  
> Si votre instance Azure Stack est en retard de plus de deux mises à jour, elle est considérée comme non conforme. Pour bénéficier de la prise en charge, vous devez [mettre à jour avec au moins la version minimale prise en charge](azure-stack-servicing-policy.md#keep-your-system-under-support).
::: moniker-end

## <a name="update-planning"></a>Planification des mises à jour

Avant d’appliquer la mise à jour, veillez à consulter les informations suivantes :

- [Problèmes connus](known-issues.md)
- [Mises à jour de sécurité](release-notes-security-updates.md)
- [Liste de vérification des activités avant et après l’application de la mise à jour](release-notes-checklist.md)

Pour obtenir de l'aide sur le dépannage des mises à jour et le processus de mise à jour, voir [Résolution des problèmes liés aux correctifs et aux mises à jour pour Azure Stack](azure-stack-updates-troubleshoot.md).

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->
::: moniker range="azs-1910"
## <a name="1910-build-reference"></a>Référence de la build 1910

Le numéro de build de la mise à jour 1910 d’Azure Stack est **1.1910.0.58**.

### <a name="update-type"></a>Type de mise à jour

Depuis la version 1908, le système d’exploitation sous-jacent sur lequel Azure Stack s’exécute a été mis à jour vers Windows Server 2019. Cela apporte des améliorations fondamentales, ainsi que la possibilité d’ajouter des fonctionnalités supplémentaires à Azure Stack dans un avenir proche.

Le type de build de la mise à jour 1910 d’Azure Stack est **Express**.

La taille du package de mise à jour 1910 est supérieure à celle des mises à jour précédentes. Cette augmentation de taille allonge les temps de téléchargement. La mise à jour reste à l’état de **préparation** pendant une longue période, et les opérateurs peuvent s’attendre à ce que ce processus prenne plus de temps qu’avec les mises à jour précédentes. Le temps nécessaire pour effectuer la mise à jour 1910 est estimé à environ 10 heures, quel que soit le nombre de nœuds physiques dans votre environnement Azure Stack. La durée d’exécution exacte de la mise à jour dépend généralement de la capacité utilisée sur votre système par les charges de travail de locataire, de la connectivité réseau de votre système (s’il est connecté à Internet) et des caractéristiques de vos composants matériels système. Les durées d’exécution plus longues que les valeurs attendues ne sont pas rares et ne nécessitent aucune action de la part des opérateurs Azure Stack, sauf si la mise à jour échoue. Cette approximation d’exécution est propre à la mise à jour 1910 et n’est pas comparable aux autres mises à jour d’Azure Stack.

Pour plus d’informations sur les types de build de mise à jour, voir [Gérer les mises à jour dans Azure Stack](azure-stack-updates.md).

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Nouveautés

<!-- What's new, also net new experiences and features. -->

- Le portail administrateur montre désormais les adresses IP des points de terminaison privilégiés dans le menu des propriétés de région pour faciliter leur découverte. Il montre aussi le serveur de temps et les redirecteurs DNS actuellement configurés.

- Le système de contrôle d’intégrité et de supervision d’Azure Stack peut désormais déclencher des alertes pour différents composants matériels en cas d’erreur. Cela demande une configuration supplémentaire. Pour plus d’informations, consultez [Superviser les composants matériels d’Azure Stack](azure-stack-hardware-monitoring.md).

- [Prise en charge de cloud-init pour Azure Stack](/azure/virtual-machines/linux/using-cloud-init) : Cloud-init est une approche courante permettant de personnaliser une machine virtuelle Linux lors de son premier démarrage. Vous pouvez utiliser cloud-init pour installer des packages et écrire des fichiers, ou encore pour configurer des utilisateurs ou des paramètres de sécurité. cloud-init étant appelé pendant le processus de démarrage initial, aucune autre étape ni aucun agent ne sont nécessaires pour appliquer votre configuration. Les images Ubuntu de la Place de marché ont été mises à jour pour prendre en charge cloud-init pour le provisionnement.

- Azure Stack prend à présent en charge toutes les versions des agents Windows Azure Linux, comme Azure.

- Une nouvelle version des modules PowerShell d’administrateur Azure Stack est disponible. <!-- For more information, see -->

- Ajout de l’applet de commande **Set-AzSDefenderManualUpdate** dans le point de terminaison privilégié (PEP) pour configurer la mise à jour manuelle des définitions Windows Defender dans l’infrastructure Azure Stack. Pour plus d’informations, consultez [Mettre à jour l’antivirus Windows Defender sur Azure Stack](azure-stack-security-av.md).

- Ajout de l’applet de commande **Get-AzSDefenderManualUpdate** dans le point de terminaison privilégié (PEP) pour récupérer la configuration de la mise à jour manuelle des définitions Windows Defender dans l’infrastructure Azure Stack. Pour plus d’informations, consultez [Mettre à jour l’antivirus Windows Defender sur Azure Stack](azure-stack-security-av.md).

- Ajout de l’applet de commande **Set-AzSDnsForwarder** dans le point de terminaison privilégié (PEP) pour changer les paramètres du redirecteur des serveurs DNS dans Azure Stack. Pour plus d’informations sur la configuration DNS, consultez [Intégration des services DNS Azure Stack au DNS du centre de données](azure-stack-integrate-dns.md).

- Ajout de l’applet de commande **Get-AzSDnsForwarder** dans le point de terminaison privilégié (PEP) pour récupérer les paramètres du redirecteur des serveurs DNS dans Azure Stack. Pour plus d’informations sur la configuration DNS, consultez [Intégration des services DNS Azure Stack au DNS du centre de données](azure-stack-integrate-dns.md).

- Ajout de la prise en charge de la gestion des **clusters Kubernetes** à l’aide du [moteur AKS](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview). À partir de cette mise à jour, les clients peuvent déployer des clusters Kubernetes de production. Le moteur AKS permet aux utilisateurs d’effectuer les opérations suivantes :
   - Gérer le cycle de vie de leurs clusters Kubernetes. Ils peuvent créer, mettre à jour et mettre à l’échelle des clusters.
   - Gérer leurs clusters à l’aide d’images managées produites par AKS et les équipes Azure Stack.
   - Tirer parti d’un fournisseur de cloud Kubernetes intégré à Azure Resource Manager qui crée des clusters à l’aide des ressources natives Azure.
   - Déployer et gérer leurs clusters dans des tampons Azure Stack connectés ou déconnectés.
   - Utiliser les fonctionnalités hybrides d’Azure :
      * Intégration avec Azure Arc (préversion privée bientôt disponible)
      * Intégration avec Azure Monitor pour conteneurs (préversion publique)
   - Utiliser des conteneurs Windows avec le moteur AKS (préversion privée).
   - Recevoir le support CSS et PG pour leurs déploiements.

### <a name="improvements"></a>Améliorations

<!-- Changes and product improvements with tangible customer-facing value. -->

- Azure Stack a amélioré sa capacité à corriger automatiquement certains problèmes liés aux correctifs et aux mises à jour, qui entraînaient précédemment des échecs de mise à jour ou empêchaient les opérateurs de lancer une mise à jour Azure Stack. Par conséquent, le groupe **Test-AzureStack -UpdateReadiness** comprend moins de tests. Pour plus d’informations, consultez [Valider l’état du système Azure Stack](azure-stack-diagnostic-test.md#groups). Les trois tests suivants font encore partie du groupe **UpdateReadiness** :

  - **AzSInfraFileValidation**
  - **AzSActionPlanStatus**
  - **AzsStampBMCSummary**

- Ajout d’une règle d’audit pour signaler qu’un appareil externe (par exemple, une clé USB) est monté sur un nœud de l’infrastructure Azure Stack. Le journal d’audit est émis via syslog et s’affiche sous la forme **Microsoft-Windows-Security-Auditing : 6416|Événements Plug-and-Play**. Pour plus d’informations sur la façon de configurer le client syslog, consultez [Transfert Syslog](azure-stack-integrate-security.md).

- Azure Stack passe aux clés RSA 4 096 bits pour les certificats internes. La rotation des secrets internes remplace les anciens certificats 2 048 bits par des certificats d’une longueur de 4 096 bits. Pour plus d’informations sur la rotation des secrets dans Azure Stack, consultez [Faire pivoter les clés secrètes dans Azure Stack](azure-stack-rotate-secrets.md).

- Mises à niveau vers la complexité des algorithmes de chiffrement et la robustesse des clés pour plusieurs composants internes afin de se conformer à la stratégie CNSSP-15 (Committee on National Security Systems - Policy 15), qui fournit les bonnes pratiques d’utilisation des normes publiques pour assurer la sécurité du partage des informations. Parmi les améliorations, citons AES256 pour l’authentification Kerberos et SHA384 pour le chiffrement VPN. Pour plus d’informations sur la stratégie CNSSP-15, reportez-vous à la [page Policies du Committee on National Security Systems](http://www.cnss.gov/CNSS/issuances/Policies.cfm).

- Suite à la mise à niveau ci-dessus, Azure Stack a maintenant de nouvelles valeurs par défaut pour les configurations IPsec/IKEv2. Les nouvelles valeurs par défaut utilisées côté Azure Stack sont les suivantes :

   **Paramètres IKE Phase 1 (Mode principal)**

   | Propriété              | Valeur|
   |-|-|
   | Version IKE           | IKEv2 |
   |Groupe Diffie-Hellman   | ECP384 |
   | Méthode d’authentification | Clé prépartagée |
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
   |Détection d’homologue mort | Pris en charge|

   Ces changements sont également reflétés dans la documentation sur la [proposition IPsec/IKE par défaut](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters).

- Le service de sauvegarde d’infrastructure améliore la logique qui calcule l’espace disponible souhaité pour les sauvegardes au lieu de s’appuyer sur un seuil fixe. Le service utilise la taille d’une sauvegarde, une stratégie de conservation, une réserve et l’utilisation actuelle de l’emplacement de stockage externe pour déterminer si un avertissement doit être déclenché pour l’opérateur. 

### <a name="changes"></a>Changements

- Lors du téléchargement d’éléments de la Place de marché à partir d’Azure dans Azure Stack, il existe une nouvelle interface utilisateur qui vous permet de spécifier une version d’un élément quand plusieurs versions existent. La nouvelle interface utilisateur est disponible dans les deux scénarios, connecté et déconnecté. Pour plus d’informations, consultez [Télécharger des éléments de la Place de marché à partir d’Azure dans Azure Stack](azure-stack-download-azure-marketplace-item.md).  

- Depuis la version 1910, le système Azure Stack **nécessite** un espace IP interne privé /20 supplémentaire. Ce réseau est privé pour le système Azure Stack et peut être réutilisé sur plusieurs systèmes Azure Stack dans votre centre de données. Ce réseau est privé pour Azure Stack, mais il ne doit pas empiéter sur un réseau dans votre centre de données. L’espace IP privé /20 est divisé en plusieurs réseaux qui permettent d’exécuter l’infrastructure Azure Stack sur des conteneurs (comme mentionné précédemment dans les [notes de publication de la version 1905](release-notes.md?view=azs-1905)). L’objectif de l’exécution de l’infrastructure Azure Stack dans des conteneurs est d’optimiser l’utilisation et d’améliorer les performances. L’espace IP privé /20 est également utilisé pour favoriser les efforts en cours qui réduiront l’espace IP routable nécessaire avant le déploiement.

  - Notez que l’entrée /20 fait office de condition préalable à la mise à jour suivante d’Azure Stack, après 1910. Lorsque la mise à jour suivante d’Azure Stack, après 1910, est publiée et qu’une tentative d’installation est effectuée, la mise à jour échoue si vous n’avez pas réalisé l’entrée /20, comme décrit ci-dessous dans les étapes de correction. Une alerte sera présente dans le portail d’administration jusqu’au terme des étapes de correction ci-dessus. Consultez l’article sur l’[intégration réseau du centre de données](azure-stack-network.md#private-network) pour comprendre comment ce nouvel espace privé sera consommé. 

  - Étapes de correction : pour corriger, suivez les instructions permettant d’[ouvrir une session PEP](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Préparez une [plage d’adresses IP internes privées](azure-stack-network.md#logical-networks) de taille /20 et exécutez l’applet de commande suivante (disponible uniquement à partir de la version 1910) dans la session du point de terminaison privilégié, en utilisant l’exemple suivant : `Set-AzsPrivateNetwork -UserSubnet 100.87.0.0/20`. Si l’opération réussit, vous recevez le message **Azs Internal Network range added to the config** (La plage du réseau interne AZS a été ajoutée à la configuration). En cas de réussite, l’alerte se ferme dans le portail administrateur. Le système Azure Stack peut maintenant être mis à jour avec la version suivante.
  
- Le service de sauvegarde d’infrastructure supprime partiellement les données de sauvegarde chargées si la capacité de l’emplacement de stockage externe devient insuffisante pendant la procédure de chargement.  

- Le service de sauvegarde d’infrastructure ajoute le service d’identité à la charge utile de sauvegarde pour les déploiements AAD.  

- Le module PowerShell Azure Stack a été mis à jour vers la version 1.8.0 pour la version 1910.<br>Les changements sont notamment :
   - **Nouveau module d’administration DRP** : Le fournisseur de ressources de déploiement (DRP) permet des déploiements orchestrés de fournisseurs de ressources dans Azure Stack. Ces commandes interagissent avec la couche Azure Resource Manager pour interagir avec le fournisseur DRP.
   - **BRP** : <br />
           - Prise en charge de la restauration de rôle unique pour la sauvegarde d’infrastructure Azure Stack. <br />
           - Ajout du paramètre `RoleName` à l’applet de commande `Restore-AzsBackup`.
   - **FRP** : Changements cassants pour les ressources de **lecteur** et de **volume** avec la version d’API `2019-05-01`. Les fonctionnalités sont prises en charge par Azure Stack versions 1910 et ultérieures : <br />
            – Les valeurs de `ID`, `Name`, `HealthStatus` et `OperationalStatus` ont été changées. <br />
            – Nouvelles propriétés prises en charge `FirmwareVersion`, `IsIndicationEnabled`, `Manufacturer`et `StoragePool` pour les ressources de **lecteur**. <br />
            – Les propriétés `CanPool` et `CannotPoolReason` des ressources de **lecteur** sont désormais déconseillées. Utilisez `OperationalStatus` à la place.

### <a name="fixes"></a>Correctifs

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Correction d’un problème qui empêchait l’application de la stratégie TLS 1.2 sur les environnements déployés avant la version 1904 d’Azure Stack.
- Correction d’un problème où une machine virtuelle Ubuntu 18.04 créée avec une autorisation SSH activée ne vous permettait pas d’utiliser les clés SSH pour vous connecter. 
- Suppression de la **réinitialisation du mot de passe** de l’interface utilisateur du groupe de machines virtuelles identiques.
- Correction d’un problème où la suppression de l’équilibreur de charge à partir du portail n’aboutissait pas à la suppression de l’objet dans la couche d’infrastructure.
- Correction d’un problème qui affichait un pourcentage inexact de l’alerte d’utilisation du pool de passerelle sur le portail d’administration.
- Correction d’un problème où l’ajout de plusieurs adresses IP publiques sur la même carte réseau d’une machine virtuelle provoquait des problèmes de connectivité Internet. À présent, une carte réseau avec deux adresses IP publiques doit fonctionner comme prévu.

## <a name="security-updates"></a>Mises à jour de sécurité

Pour plus d’informations sur les mises à jour de sécurité dans cette mise à jour d’Azure Stack, consultez [Mises à jour de sécurité Azure Stack](release-notes-security-updates.md).

## <a name="update-planning"></a>Planification des mises à jour

Avant d’appliquer la mise à jour, veillez à consulter les informations suivantes :

- [Problèmes connus](known-issues.md)
- [Mises à jour de sécurité](release-notes-security-updates.md)
- [Liste de vérification des activités avant et après l’application de la mise à jour](release-notes-checklist.md)

## <a name="download-the-update"></a>Télécharger la mise à jour

Vous pouvez télécharger le package de mise à jour 1910 d’Azure Stack sur la [page de téléchargement d’Azure Stack](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes"></a>Correctifs logiciels

Azure Stack publie des correctifs logiciels à intervalles réguliers. Veillez à installer le dernier correctif logiciel Azure Stack pour la version 1908 avant de mettre à jour Azure Stack vers la version 1910.

Les correctifs logiciels Azure Stack sont uniquement applicables aux systèmes intégrés Azure Stack. N’essayez pas d’installer des correctifs logiciels sur l’ASDK.

### <a name="prerequisites-before-applying-the-1910-update"></a>Configuration requise : Avant d’appliquer la mise à jour 1910

La version 1910 d’Azure Stack doit être appliquée sur la version 1908 avec les correctifs logiciels suivants :

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Correctif logiciel Azure Stack 1.1908.11.47](https://support.microsoft.com/help/4535000)

### <a name="after-successfully-applying-the-1910-update"></a>Après l’application de la mise à jour 1910

Après l’installation de cette mise à jour, installez les correctifs logiciels applicables. Pour plus d’informations, consultez notre [stratégie de maintenance](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Correctif logiciel Azure Stack 1.1910.8.76](https://support.microsoft.com/help/4534999)
::: moniker-end

::: moniker range="azs-1908"
## <a name="1908-build-reference"></a>Référence de build 1908

Le numéro de build de la mise à jour 1908 d’Azure Stack est **1.1908.4.33**.

### <a name="update-type"></a>Type de mise à jour

Pour la version 1908, le système d’exploitation sous-jacent sur lequel Azure Stack s’exécute a été mis à jour vers Windows Server 2019. Cela apporte des améliorations fondamentales, ainsi que la possibilité d’ajouter des fonctionnalités supplémentaires à Azure Stack dans un avenir proche.

Le type de build de la mise à jour 1908 d’Azure Stack est **Complète**. Par conséquent, la mise à jour 1908 prend plus de temps que les mises à jour Express telles que 1906 et 1907. Les runtimes exacts des mises à jour complètes dépendent généralement du nombre de nœuds que votre instance Azure Stack contient, de la capacité utilisée sur votre système par les charges de travail clientes, de la connectivité réseau de votre système (s’il est connecté à Internet) et de la configuration de votre matériel système. La mise à jour 1908 a présenté les durées d’exécution attendues suivantes dans nos tests internes : 4 nœuds - 42 heures, 8 nœuds - 50 heures, 12 nœuds - 60 heures, 16 nœuds - 70 heures. Il n’est pas rare de constater des durées d’exécution des mises à jour plus longues que ces durées prévues, mais cela ne nécessite aucune action de la part des opérateurs Azure Stack, sauf si la mise à jour échoue.

Pour plus d’informations sur les types de build de mise à jour, voir [Gérer les mises à jour dans Azure Stack](azure-stack-updates.md).

- La durée d’exécution exacte des mises à jour dépend généralement de la capacité utilisée sur votre système par les charges de travail client, de la connectivité réseau de votre système (s’il est connecté à Internet) et de la configuration de votre matériel système.
- Les durées d’exécution plus longues que prévu ne sont pas rares et ne nécessitent aucune action de la part des opérateurs Azure Stack, sauf si la mise à jour échoue.
- Cette approximation d’exécution est propre à la mise à jour 1908 et n’est pas comparable aux autres mises à jour d’Azure Stack.

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Nouveautés

<!-- What's new, also net new experiences and features. -->

- Pour la version 1908, le système d’exploitation sous-jacent sur lequel Azure Stack s’exécute a été mis à jour vers Windows Server 2019. Cela apporte des améliorations fondamentales, ainsi que la possibilité d’ajouter des fonctionnalités supplémentaires à Azure Stack dans un avenir proche.
- Tous les composants de l’infrastructure Azure Stack fonctionnent désormais en mode FIPS 140-2.
- Les opérateurs Azure Stack peuvent désormais supprimer les données utilisateur du portail. Pour plus d’informations, consultez [Effacer les données utilisateur du portail dans Azure Stack](azure-stack-portal-clear.md).

### <a name="improvements"></a>Améliorations

<!-- Changes and product improvements with tangible customer-facing value. -->
- Le chiffrement des données au repos dans Azure Stack a été amélioré afin de rendre les secrets persistants dans le module matériel TPM des nœuds physiques.

### <a name="changes"></a>Changements

- Les fournisseurs de matériel publieront le package d’extension OEM version 2.1 ou ultérieure en même temps qu’Azure Stack version 1908. Le package d’extension OEM version 2.1 ou ultérieure est requis pour la version 1908 d’Azure Stack. Pour plus d’informations sur le téléchargement du package d’extension OEM version 2.1 ou ultérieure, contactez le fournisseur de matériel pour votre système, et consultez l’article sur les [mises à jour OEM](azure-stack-update-oem.md#oem-contact-information).  

### <a name="fixes"></a>Correctifs

- Résolution d’un problème de compatibilité avec les futures mises à jour OEM d’Azure Stack et d’un problème de déploiement de machine virtuelle à l’aide d’images utilisateur client. Ce problème a été détecté dans la version 1907 et résolu avec le correctif logiciel [KB4517473](https://support.microsoft.com/en-us/help/4517473/azure-stack-hotfix-1-1907-12-44)  
- Résolution d’un problème avec la mise à jour des microprogrammes OEM et correction du diagnostic incorrect dans Test-AzureStack pour Fabric Ring Health. Ce problème a été détecté dans la version 1907 et résolu avec le correctif logiciel [KB4515310](https://support.microsoft.com/en-us/help/4515310/azure-stack-hotfix-1-1907-7-35)
- Correction d’un problème avec le processus de mise à jour des microprogrammes OEM. Ce problème a été détecté dans la version 1907 et résolu avec le correctif logiciel [KB4515650](https://support.microsoft.com/en-us/help/4515650/azure-stack-hotfix-1-1907-8-37)

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

## <a name="security-updates"></a>Mises à jour de sécurité

Pour plus d’informations sur les mises à jour de sécurité dans cette mise à jour d’Azure Stack, consultez [Mises à jour de sécurité Azure Stack](release-notes-security-updates.md).

## <a name="download-the-update-1908"></a>Télécharger la mise à jour

Vous pouvez télécharger la mise à jour 1908 d’Azure Stack sur la [page de téléchargement d’Azure Stack](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes"></a>Correctifs logiciels

Azure Stack publie des correctifs logiciels à intervalles réguliers. Avant d’installer la mise à jour 1908 d’Azure Stack, veillez à installer le dernier correctif logiciel d’Azure Stack pour la build 1907.

Les correctifs logiciels Azure Stack sont uniquement applicables aux systèmes intégrés Azure Stack. N’essayez pas d’installer des correctifs logiciels sur l’ASDK.

### <a name="prerequisites-before-applying-the-1908-update"></a>Configuration requise : Avant d’appliquer la mise à jour 1908

La version 1908 d’Azure Stack doit être appliquée sur la version 1907 avec les correctifs logiciels suivants :

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Correctif logiciel Azure Stack 1.1907.18.56](https://support.microsoft.com/help/4528552)

La mise à jour 1908 d’Azure Stack nécessite la **version 2.1 ou ultérieure d’OEM pour Azure Stack** du fournisseur de matériel de votre système. Les mises à jour OEM appliquent les mises à jour des pilotes et des microprogrammes aux composants matériels de votre système Azure Stack. Pour plus d’informations sur l’application des mises à jour OEM, consultez [Appliquer des mises à jour de fabricants d’ordinateurs à Azure Stack](azure-stack-update-oem.md)

### <a name="after-successfully-applying-the-1908-update"></a>Après l’application de la mise à jour 1908

Après l’installation de cette mise à jour, installez les correctifs logiciels applicables. Pour plus d’informations, consultez notre [stratégie de maintenance](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Correctif logiciel Azure Stack 1.1908.11.47](https://support.microsoft.com/help/4535000)
::: moniker-end

::: moniker range="azs-1907"
## <a name="1907-build-reference"></a>Référence de build 1907

Le numéro de build de la mise à jour 1907 d’Azure Stack est **1.1907.0.20**.

### <a name="update-type"></a>Type de mise à jour

Le type de build de la mise à jour 1907 d’Azure Stack est **Express**. Pour plus d’informations sur les types de build de mise à jour, consultez l’article [Gérer les mises à jour dans Azure Stack](azure-stack-updates.md). D'après des tests internes, la mise à jour 1907 prend environ 13 heures.

- La durée d’exécution exacte des mises à jour dépend généralement de la capacité utilisée sur votre système par les charges de travail client, de la connectivité réseau de votre système (s’il est connecté à Internet) et de la configuration de votre matériel système.
- Les durées d’exécution plus longues que prévu ne sont pas rares et ne nécessitent aucune action de la part des opérateurs Azure Stack, sauf si la mise à jour échoue.
- Cette approximation d’exécution est propre à la mise à jour 1907 et n’est pas comparable aux autres mises à jour d’Azure Stack.

## <a name="whats-in-this-update"></a>Éléments de cette mise à jour

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Nouveautés

<!-- What's new, also net new experiences and features. -->

- Mise à la disposition générale du service de collecte des journaux de diagnostic Azure Stack pour faciliter et améliorer la collecte des journaux de diagnostic. Le service de collecte des journaux de diagnostic Azure Stack propose un moyen simplifié de collecter et de partager des journaux de diagnostic avec les services de support technique Microsoft. Ce service de collecte des journaux de diagnostic offre une nouvelle expérience utilisateur dans le portail d’administration Azure Stack, qui permet aux opérateurs de configurer le chargement automatique des journaux de diagnostic sur un objet blob de stockage lorsque certaines alertes critiques sont générées, ou d’effectuer la même opération à la demande. Pour plus d’informations, consultez l'article [Collecte des journaux de diagnostic](azure-stack-diagnostic-log-collection-overview.md).

- Mise à la disposition générale de la validation de l’infrastructure réseau Azure Stack dans le cadre de l'outil de validation Azure Stack **Test-AzureStack**. L'infrastructure réseau Azure Stack fera partie de **Test-AzureStack** pour identifier toute défaillance sur l'infrastructure. Le test vérifie la connectivité de l’infrastructure réseau en contournant le réseau à définition logicielle Azure Stack. Il démontre la connectivité d’une adresse IP virtuelle publique aux redirecteurs DNS, serveurs NTP et points de terminaison d'identité configurés. En outre, il vérifie la connectivité à Azure quand Azure AD est utilisé en tant que fournisseur d’identité ou au serveur fédéré quand ADFS est utilisé. Pour plus d’informations, consultez l'article [Outil de validation Azure Stack](azure-stack-diagnostic-test.md).

- Ajout d’une procédure de rotation des secrets internes pour faire tourner les certificats TLS SQL internes en fonction des besoins pendant une mise à jour du système.

### <a name="improvements"></a>Améliorations

<!-- Changes and product improvements with tangible customer-facing value. -->

- Le panneau de mise à jour Azure Stack affiche désormais une heure **Dernière étape terminée** pour les mises à jour actives. Pour la consulter, accédez au panneau de mise à jour et cliquez sur une mise à jour en cours d’exécution. **Dernière étape terminée** est ensuite disponible dans la section **Détails de l'exécution de la mise à jour**.

- Améliorations apportées aux actions de l’opérateur **Start-AzureStack** et **Stop-AzureStack**. Le temps de démarrage d'Azure Stack a été réduit d'environ 50 %. Le temps d’arrêt d'Azure Stack a été réduit d'environ 30 %. Les temps de démarrage et d’arrêt moyens sont les mêmes lorsque le nombre de nœuds augmente dans une unité d’échelle.

- Amélioration de la gestion des erreurs pour l’outil Place de marché déconnecté. Si un téléchargement échoue ou réussit partiellement lors de l'utilisation de **Export-AzSOfflineMarketplaceItem**, un message s'affiche avec plus de détails sur l'erreur et la procédure d’atténuation, le cas échéant.

- Amélioration des performances de création de disques managés à partir d’un objet blob de pages/instantané de grande taille. Auparavant, cela déclenchait un délai d’expiration lors de la création d’un disque de grande taille.  

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/127669774/home -->
- Amélioration de la vérification d'intégrité du disque virtuel avant l’arrêt d’un nœud pour éviter le détachement inattendu du disque.

- Amélioration du stockage des journaux internes pour les opérations de l’administrateur. Cela permet d’améliorer les performances et la fiabilité lors des opérations de l’administrateur en limitant la consommation de mémoire et de stockage des processus de journalisation interne. Vous remarquerez peut-être une amélioration des délais de chargement des pages du panneau de mise à jour dans le portail administrateur. Dans le cadre de cette amélioration, les journaux des mises à jour datant de plus de 6 mois ne seront plus disponibles dans le système. Si vous avez besoin de ces journaux, veillez à [Télécharger le résumé](azure-stack-apply-updates.md) de toutes les exécutions de mises à jour datant de plus de 6 mois avant d’effectuer la mise à jour 1907.

### <a name="changes"></a>Changements

- Azure Stack version 1907 contient une alerte d’avertissement qui indique aux opérateurs de bien mettre à jour le package OEM de leur système vers la version 2.1 ou ultérieure avant d’effectuer la mise à jour vers la version 1908. Pour plus d’informations sur l’application de mises à jour OEM dans Azure Stack, consultez [Appliquer des mises à jour de fabricants d’ordinateurs à Azure Stack](azure-stack-update-oem.md).

- Ajout d’une nouvelle règle de trafic sortant (HTTPS) afin d'activer la communication pour le service de collecte des journaux de diagnostic Azure Stack. Pour plus d’informations, consultez [Intégration au centre de données Azure Stack - Publier des points de terminaison](azure-stack-integrate-endpoints.md#ports-and-urls-outbound).

- Désormais, le service de sauvegarde d’infrastructure supprime partiellement les sauvegardes téléchargées si la capacité de l’emplacement de stockage externe est insuffisante.

- Les sauvegardes d’infrastructure n’incluent plus la sauvegarde des données des services de domaine. Cela s'applique uniquement aux systèmes utilisant Azure Active Directory en tant que fournisseur d'identité.

- Nous vérifions désormais que l'ingestion d'une image dans le panneau **Calcul -> Images de machine virtuelle** est de type objet blob de pages.

- La commande de point de terminaison privilégié **Set-BmcCredential** met à jour les informations d’identification dans le contrôleur BMC (Baseboard Management Controller).

### <a name="fixes"></a>Correctifs

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->
- Résolution d’un problème lié au fait que le serveur de publication, l'offre et la référence SKU d'un modèle Resource Manager étaient considérés comme sensibles à la casse : l’image n'était pas extraite pour le déploiement, sauf si la casse de ses paramètres était identique à celle du serveur de publication, de l'offre et de la référence SKU.

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/129536438/home -->
- Résolution d'un problème lié à l'échec des sauvegardes avec un message d'erreur **PartialSucceeded**, en raison des délais d'expiration lors de la sauvegarde des métadonnées du service de stockage.  

- Résolution d'un problème lié au fait que la suppression d’abonnements utilisateur aboutissait à des ressources orphelines.

- Résolution d’un problème lié au fait que le champ de description n'était pas enregistré lors de la création d’une offre.

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

Pour plus d’informations sur les mises à jour de sécurité dans cette mise à jour d’Azure Stack, consultez [Mises à jour de sécurité Azure Stack](release-notes-security-updates.md).

## <a name="update-planning"></a>Planification des mises à jour

Avant d’appliquer la mise à jour, veillez à consulter les informations suivantes :

- [Problèmes connus](known-issues.md)
- [Mises à jour de sécurité](release-notes-security-updates.md)
- [Liste de vérification des activités avant et après l’application de la mise à jour](release-notes-checklist.md)

## <a name="download-the-update"></a>Télécharger la mise à jour

Vous pouvez télécharger la mise à jour 1907 d’Azure Stack sur la [page de téléchargement d’Azure Stack](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes"></a>Correctifs logiciels

Azure Stack publie des correctifs logiciels à intervalles réguliers. Avant d’installer la mise à jour 1907 d’Azure Stack, veillez à installer le dernier correctif logiciel d’Azure Stack pour la build 1906.

Les correctifs logiciels Azure Stack sont uniquement applicables aux systèmes intégrés Azure Stack. N’essayez pas d’installer des correctifs logiciels sur l’ASDK.

### <a name="before-applying-the-1907-update"></a>Avant d’appliquer la mise à jour 1907

La version 1907 d’Azure Stack doit être appliquée à la version 1906 avec les correctifs logiciels suivants :

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Correctif logiciel Azure Stack 1.1906.15.60](https://support.microsoft.com/help/4524559)

### <a name="after-successfully-applying-the-1907-update"></a>Une fois la mise à jour 1907 correctement appliquée

Après l’installation de cette mise à jour, installez les correctifs logiciels applicables. Pour plus d’informations, consultez notre [stratégie de maintenance](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Correctif logiciel Azure Stack 1.1907.18.56](https://support.microsoft.com/help/4528552)
::: moniker-end

::: moniker range="azs-1906"
## <a name="1906-build-reference"></a>Référence de build 1906

Le numéro de build de la mise à jour 1906 d’Azure Stack est **1.1906.0.30**.

### <a name="update-type"></a>Type de mise à jour

Le type de build de la mise à jour 1906 d’Azure Stack est **Express**. Pour plus d’informations sur les types de build de mise à jour, consultez l’article [Gérer les mises à jour dans Azure Stack](azure-stack-updates.md). La durée nécessaire pour effectuer la mise à jour 1906 terminer est estimée à environ 10 heures, quel que soit le nombre de nœuds physiques dans votre environnement Azure Stack. La durée d’exécution exacte des mises à jour dépend généralement de la capacité utilisée sur votre système par les charges de travail client, de la connectivité réseau de votre système (s’il est connecté à Internet) et les caractéristiques de votre matériel système. Les durées d’exécution plus longues que les valeurs attendues ne sont pas rares et ne nécessitent aucune action de la part des opérateurs Azure Stack, sauf si la mise à jour échoue. Cette approximation d’exécution est propre à la mise à jour 1906 et n’est pas comparable aux autres mises à jour d’Azure Stack.

## <a name="whats-in-this-update"></a>Éléments de cette mise à jour

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

- Ajouter un cmdlet **Set-TLSPolicy** dans le point de terminaison privilégié (PEP) pour forcer le TLS 1.2 sur tous les points de terminaison. Pour plus d’informations, consultez [Contrôles de sécurité Azure Stack](azure-stack-security-configuration.md).

- Ajout d’un cmdlet **Get-TLSPolicy** dans le point de terminaison privilégié (PEP) pour récupérer la stratégie TLS appliquée. Pour plus d’informations, consultez [Contrôles de sécurité Azure Stack](azure-stack-security-configuration.md).

- Ajout d’une procédure de rotation des secrets internes pour faire tourner les certificats TLS internes en fonction des besoins pendant une mise à jour du système.

- Ajout d’un dispositif de protection pour empêcher l’expiration des secrets internes en forçant la rotation des secrets internes au cas où une alerte critique sur les secrets arrivant à expiration est ignorée. Ceci ne doit pas être considéré comme une procédure de fonctionnement normale. La rotation des secrets doit être planifiée pour se produire pendant une fenêtre de maintenance. Pour plus d’informations, consultez [Rotation des secrets Azure Stack](azure-stack-rotate-secrets.md).

- Visual Studio Code est désormais pris en charge avec le déploiement Azure Stack avec AD FS.

### <a name="improvements"></a>Améliorations

<!-- Changes and product improvements with tangible customer-facing value. -->

- Le cmdlet **Get-GraphApplication** dans le point de terminaison privilégié affiche désormais l’empreinte du certificat actuellement utilisé. Ceci améliore la gestion des certificats pour les principaux de service quand Azure Stack est déployé avec AD FS.

- De nouvelles règles de surveillance d’intégrité ont été ajoutées pour valider la disponibilité d’AD Graph et AD FS, y compris la possibilité de déclencher les alertes.

- Améliorations de la fiabilité du fournisseur de ressources de sauvegarde lorsque le service de sauvegarde d’infrastructure est déplacé vers une autre instance.

- Optimisation des performances de la procédure de rotation des secrets externes pour fournir une durée d’exécution uniforme afin de faciliter la planification de la fenêtre de maintenance.

- Le cmdlet **Test-AzureStack** signale désormais les secrets internes qui sont sur le point d’expirer (alertes critiques).

- Un nouveau paramètre est disponible pour le cmdlet **Register-CustomAdfs** dans le point de terminaison privilégié qui permet d’ignorer la vérification de la liste de révocation des certificats lors de la configuration de l’approbation de fédération pour AD FS.

- La version 1906 bénéficie d’une meilleure visibilité sur la progression en cours de mise à jour : vous êtes donc certain que les mises à jour ne sont pas suspendues. Cela entraîne une augmentation du nombre total d’étapes de mise à jour présentées aux opérateurs dans le panneau **Mise à jour**. Vous pourrez également remarquer plus d’étapes de mise à jour en parallèle que dans les mises à jour précédentes.

#### <a name="networking-updates"></a>Mises à jour en réseau

- Mise à jour de la durée de bail définie dans le répondeur DHCP pour être cohérent avec Azure.

- Amélioration des taux de nouvelles tentatives auprès du fournisseur de ressources en cas d’échec du déploiement de ressources.

- Suppression de l’option de référence SKU **Standard** de l’équilibreur de charge et de l’adresse IP publique, car ce n’est actuellement pas pris en charge.

### <a name="changes"></a>Changements

- La création des comptes de stockage est désormais cohérente avec celle d’Azure.

- Modification des déclencheurs d’alertes à l’expiration des secrets internes :
  - Les alertes d’avertissement sont maintenant déclenchées 90 jours avant l’expiration des secrets.
  - Les alertes critiques sont maintenant déclenchées 30 jours avant l’expiration des secrets.

- Mise à jour des chaînes dans le fournisseur de ressources de sauvegarde d’infrastructure pour rendre la terminologie plus cohérente.

### <a name="fixes"></a>Correctifs

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Correction d’un problème où le redimensionnement d’une machine virtuelle avec disque managé a échoué avec une **erreur interne de l’opération**.

- Correction d’un problème où la création d’une image utilisateur ayant échoué provoque un état incorrect du service, ce qui bloque la création de nouvelles images et la suppression de l’image a échoué. Ceci est également corrigé dans le correctif logiciel 1905.

- Les alertes actives sur les secrets internes arrivant à expiration sont désormais automatiquement fermées après l’exécution réussie de la rotation interne des secrets.

- Correction d’un problème dans lequel la durée de mise à jour dans l’onglet de l’historique des mises à jour coupait le premier chiffre si la mise à jour était exécutée pendant plus de 99 heures.

- Le panneau **Mise à jour** comprend une option de **reprise** pour les mises à jour ayant échoué.

- Dans les portails d’administrateur et d’utilisateur, résolution du problème de la place de marché dans lequel l’extension Docker a été correctement retournée par recherche, mais aucune action supplémentaire n’a pu être prise en compte, car elle n’est pas disponible dans Azure Stack.

- Résolution d’un problème dans l’interface utilisateur de déploiement du modèle qui ne remplit pas les paramètres si le nom du modèle commence par un trait de soulignement « _ ».

- Résolution d’un problème qui survient quand vous créez un groupe identique de machines virtuelles et que l’option CentOS 7.2 est proposée pour le déploiement. CentOS 7.2 n’est pas disponible dans Azure Stack. Nous proposons maintenant CentOS 7.5 comme option de déploiement

- Vous pouvez désormais supprimer un groupe identique à partir du panneau **Groupes de machines virtuelles identiques**.

## <a name="security-updates"></a>Mises à jour de sécurité

Pour plus d’informations sur les mises à jour de sécurité dans cette mise à jour d’Azure Stack, consultez [Mises à jour de sécurité Azure Stack](release-notes-security-updates.md).

## <a name="update-planning"></a>Planification des mises à jour

Avant d’appliquer la mise à jour, veillez à consulter les informations suivantes :

- [Problèmes connus](known-issues.md)
- [Mises à jour de sécurité](release-notes-security-updates.md)
- [Liste de vérification des activités avant et après l’application de la mise à jour](release-notes-checklist.md)

## <a name="download-the-update"></a>Télécharger la mise à jour

Vous pouvez télécharger la mise à jour 1906 d’Azure Stack sur la [page de téléchargement d’Azure Stack](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes"></a>Correctifs logiciels

Azure Stack publie des correctifs logiciels à intervalles réguliers. Avant d’installer la mise à jour 1906 d’Azure Stack, veillez à installer le dernier correctif logiciel d’Azure Stack pour la build 1905. Après la mise à jour, installez tous les [correctifs logiciels disponibles pour 1906](#after-successfully-applying-the-1906-update).

Les correctifs logiciels Azure Stack sont uniquement applicables aux systèmes intégrés Azure Stack. N’essayez pas d’installer des correctifs logiciels sur l’ASDK.

### <a name="before-applying-the-1906-update"></a>Avant d’appliquer la mise à jour 1906

La version 1906 d’Azure Stack doit être appliquée à la version 1905 avec les correctifs logiciels suivants :

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack hotfix 1.1905.3.48](https://support.microsoft.com/help/4510078)

### <a name="after-successfully-applying-the-1906-update"></a>Une fois la mise à jour 1906 correctement appliquée

Après l’installation de cette mise à jour, installez les correctifs logiciels applicables. Pour plus d’informations, consultez notre [stratégie de maintenance](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Correctif logiciel Azure Stack 1.1906.15.60](https://support.microsoft.com/help/4524559)
::: moniker-end

::: moniker range=">=azs-1906"
## <a name="automatic-update-notifications"></a>Notifications de mise à jour automatique

Les systèmes qui peuvent accéder à Internet à partir du réseau d’infrastructure voient le message **Mise à jour disponible** dans le portail opérateur. Les systèmes sans accès à Internet peuvent télécharger et importer le fichier .zip avec le fichier .xml correspondant.

> [!TIP]  
> Pour rester informé des publications des correctifs logiciels d’Azure Stack, abonnez-vous aux flux *RSS* ou *Atom* suivants :
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="archive"></a>Archivage

Pour accéder aux notes de publication archivées d'une ancienne version, utilisez le menu déroulant de sélection de la version, situé au-dessus de la table des matières à gauche, puis sélectionnez la version que vous voulez afficher.

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble de la gestion des mises à jour dans Azure Stack, consultez [Gérer les mises à jour dans Azure Stack - Vue d’ensemble](azure-stack-updates.md).  
- Pour plus d’informations sur la façon d’appliquer des mises à jour avec Azure Stack, consultez [Effectuer des mises à jour dans Azure Stack](azure-stack-apply-updates.md).
- Pour passer en revue la stratégie de maintenance pour les systèmes intégrés Azure Stack et pour connaître la marche à suivre afin de conserver votre système dans un état de prise en charge, consultez [Stratégie de maintenance Azure Stack](azure-stack-servicing-policy.md).  
- Pour utiliser le Point de terminaison privilégié (PEP) afin de surveiller et de reprendre les mises à jour, consultez [Surveiller les mises à jour dans Azure Stack à l’aide du point de terminaison privilégié](azure-stack-monitor-update.md).
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
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

::: moniker range="<azs-1906"
Vous pouvez accéder aux [versions antérieures des notes de publication d’Azure Stack dans la galerie TechNet](https://aka.ms/azsarchivedrelnotes). Ces documents archivés sont fournis uniquement pour référence et n’impliquent aucune prise en charge de ces versions. Pour plus d’informations sur le support d’Azure Stack, consultez [Stratégie de maintenance Azure Stack](azure-stack-servicing-policy.md). Pour obtenir de l’aide, contactez les services de support technique Microsoft.
::: moniker-end


