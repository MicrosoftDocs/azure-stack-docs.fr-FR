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
ms.date: 09/17/2019
ms.author: sethm
ms.reviewer: prchint
ms.lastreviewed: 08/30/2019
ms.openlocfilehash: 0091571dca5c966cb58fc7ce1533a17850558205
ms.sourcegitcommit: c46d913ebfa4cb6c775c5117ac5c9e87d032a271
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71106532"
---
# <a name="azure-stack-updates"></a>Mises à jour d’Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Cet article décrit le contenu des packages de mise à jour d’Azure Stack. La mise à jour inclut des améliorations, nouveautés et correctifs pour cette version d’Azure Stack. **Pour accéder aux notes de publication d’une autre version, utilisez le sélecteur de version en haut à gauche.**

::: moniker range=">=azs-1905"
> [!IMPORTANT]  
> Cette mise à jour est destinée uniquement aux systèmes intégrés d’Azure Stack. N’appliquez pas cette mise à jour au Kit de développement Azure Stack.
::: moniker-end
::: moniker range="<azs-1905"
> [!IMPORTANT]  
> Si votre instance Azure Stack est en retard de plus de deux mises à jour, elle est considérée comme non conforme. Pour bénéficier de la prise en charge, vous devez [mettre à jour avec au moins la version minimale prise en charge](azure-stack-servicing-policy.md#keep-your-system-under-support). 
::: moniker-end

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->
::: moniker range="azs-1908"
## <a name="1908-build-reference"></a>Référence de build 1908

Le numéro de build de la mise à jour 1908 d’Azure Stack est **1.1908.0.20**.

### <a name="update-type-1908"></a>Type de mise à jour

Pour la version 1908, le système d’exploitation sous-jacent sur lequel Azure Stack s’exécute a été mis à jour vers Windows Server 2019. Cela apporte des améliorations fondamentales, ainsi que la possibilité d’ajouter des fonctionnalités supplémentaires à Azure Stack dans un avenir proche.

Le type de build de la mise à jour 1908 d’Azure Stack est **Complète**. Par conséquent, la mise à jour 1908 prend plus de temps que les mises à jour Express telles que 1906 et 1907. Les runtimes exacts des mises à jour complètes dépendent généralement du nombre de nœuds que votre instance Azure Stack contient, de la capacité utilisée sur votre système par les charges de travail clientes, de la connectivité réseau de votre système (s’il est connecté à Internet) et de la configuration de votre matériel système. La mise à jour 1908 a présenté les durées d’exécution attendues suivantes dans nos tests internes : 4 nœuds - 42 heures, 8 nœuds - 50 heures, 12 nœuds - 60 heures, 16 nœuds - 70 heures. Il n’est pas rare de constater des durées d’exécution des mises à jour plus longues que ces durées prévues, mais cela ne nécessite aucune action de la part des opérateurs Azure Stack, sauf si la mise à jour échoue.

Pour plus d’informations sur les types de build de mise à jour, voir [Gérer les mises à jour dans Azure Stack](azure-stack-updates.md).

- La durée d’exécution exacte des mises à jour dépend généralement de la capacité utilisée sur votre système par les charges de travail client, de la connectivité réseau de votre système (s’il est connecté à Internet) et de la configuration de votre matériel système.
- Les durées d’exécution plus longues que prévu ne sont pas rares et ne nécessitent aucune action de la part des opérateurs Azure Stack, sauf si la mise à jour échoue.
- Cette approximation d’exécution est propre à la mise à jour 1908 et n’est pas comparable aux autres mises à jour d’Azure Stack.

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new-1908"></a>Nouveautés

<!-- What's new, also net new experiences and features. -->

- Pour la version 1908, le système d’exploitation sous-jacent sur lequel Azure Stack s’exécute a été mis à jour vers Windows Server 2019. Cela apporte des améliorations fondamentales, ainsi que la possibilité d’ajouter des fonctionnalités supplémentaires à Azure Stack dans un avenir proche.
- Tous les composants de l’infrastructure Azure Stack fonctionnent désormais en mode FIPS 140-2.
- Les opérateurs Azure Stack peuvent désormais supprimer les données utilisateur du portail. Pour plus d’informations, consultez [Effacer les données utilisateur du portail dans Azure Stack](azure-stack-portal-clear.md).

### <a name="improvements-1908"></a>Améliorations

<!-- Changes and product improvements with tangible customer-facing value. -->
- Le chiffrement des données au repos dans Azure Stack a été amélioré afin de rendre les secrets persistants dans le module matériel TPM des nœuds physiques.

### <a name="changes-1908"></a>Modifications

- Les fournisseurs de matériel publieront le package d’extension OEM version 2.1 ou ultérieure en même temps qu’Azure Stack version 1908. Le package d’extension OEM version 2.1 ou ultérieure est requis pour la version 1908 d’Azure Stack. Pour plus d’informations sur le téléchargement du package d’extension OEM version 2.1 ou ultérieure, contactez le fournisseur de matériel pour votre système, et consultez l’article sur les [mises à jour OEM](azure-stack-update-oem.md#oem-contact-information).  

### <a name="fixes-1908"></a>Correctifs

- Résolution d’un problème de compatibilité avec les futures mises à jour OEM d’Azure Stack et d’un problème de déploiement de machine virtuelle à l’aide d’images utilisateur client. Ce problème a été détecté dans la version 1907 et résolu avec le correctif logiciel [KB4517473](https://support.microsoft.com/en-us/help/4517473/azure-stack-hotfix-1-1907-12-44)  
- Résolution d’un problème avec la mise à jour des microprogrammes OEM et correction du diagnostic incorrect dans Test-AzureStack pour Fabric Ring Health. Ce problème a été détecté dans la version 1907 et résolu avec le correctif logiciel [KB4515310](https://support.microsoft.com/en-us/help/4515310/azure-stack-hotfix-1-1907-7-35)
- Correction d’un problème avec le processus de mise à jour des microprogrammes OEM. Ce problème a été détecté dans la version 1907 et résolu avec le correctif logiciel [KB4515650](https://support.microsoft.com/en-us/help/4515650/azure-stack-hotfix-1-1907-8-37)


<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

## <a name="security-updates-1908"></a>Mises à jour de sécurité

Pour plus d’informations sur les mises à jour de sécurité dans cette mise à jour d’Azure Stack, consultez [Mises à jour de sécurité Azure Stack](release-notes-security-updates.md).

## <a name="update-planning-1908"></a>Planification des mises à jour

Avant d’appliquer la mise à jour, veillez à consulter les informations suivantes :

- [Problèmes connus](known-issues.md)
- [Mises à jour de sécurité](release-notes-security-updates.md)
- [Liste de vérification des activités avant et après l’application de la mise à jour](release-notes-checklist.md)

## <a name="download-the-update-1908"></a>Télécharger la mise à jour

Vous pouvez télécharger la mise à jour 1908 d’Azure Stack sur la [page de téléchargement d’Azure Stack](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes-1908"></a>Correctifs logiciels

Azure Stack publie des correctifs logiciels à intervalles réguliers. Avant d’installer la mise à jour 1908 d’Azure Stack, veillez à installer le dernier correctif logiciel d’Azure Stack pour la build 1907.

Les correctifs logiciels Azure Stack sont uniquement applicables aux systèmes intégrés Azure Stack. N’essayez pas d’installer des correctifs logiciels sur l’ASDK.

### <a name="prerequisites-before-applying-the-1908-update"></a>Configuration requise : Avant d’appliquer la mise à jour 1908

La version 1908 d’Azure Stack doit être appliquée sur la version 1907 avec les correctifs logiciels suivants :

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Correctif logiciel Azure Stack 1.1907.12.44](https://support.microsoft.com/help/4517473)

La mise à jour 1908 d’Azure Stack nécessite la **version 2.1 ou ultérieure d’OEM pour Azure Stack** du fournisseur de matériel de votre système. Les mises à jour OEM appliquent les mises à jour des pilotes et des microprogrammes aux composants matériels de votre système Azure Stack. Pour plus d’informations sur l’application des mises à jour OEM, consultez [Appliquer des mises à jour de fabricants d’ordinateurs à Azure Stack](azure-stack-update-oem.md)

### <a name="after-successfully-applying-the-1908-update"></a>Après l’application de la mise à jour 1908

Après l’installation de cette mise à jour, installez les correctifs logiciels applicables. Pour plus d’informations, consultez notre [stratégie de maintenance](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- Aucun correctif logiciel n’est disponible pour la version 1908.
::: moniker-end

::: moniker range="azs-1907"
## <a name="1907-build-reference"></a>Référence de build 1907

Le numéro de build de la mise à jour 1907 d’Azure Stack est **1.1907.0.20**.

### <a name="update-type-1907"></a>Type de mise à jour

Le type de build de la mise à jour 1907 d’Azure Stack est **Express**. Pour plus d’informations sur les types de build de mise à jour, consultez l’article [Gérer les mises à jour dans Azure Stack](azure-stack-updates.md). D'après des tests internes, la mise à jour 1907 prend environ 13 heures.

- La durée d’exécution exacte des mises à jour dépend généralement de la capacité utilisée sur votre système par les charges de travail client, de la connectivité réseau de votre système (s’il est connecté à Internet) et de la configuration de votre matériel système.
- Les durées d’exécution plus longues que prévu ne sont pas rares et ne nécessitent aucune action de la part des opérateurs Azure Stack, sauf si la mise à jour échoue.
- Cette approximation d’exécution est propre à la mise à jour 1907 et n’est pas comparable aux autres mises à jour d’Azure Stack.

## <a name="whats-in-this-update-1907"></a>Éléments de cette mise à jour

<!-- The current theme (if any) of this release. -->

### <a name="whats-new-1907"></a>Nouveautés

<!-- What's new, also net new experiences and features. -->

- Mise à la disposition générale du service de collecte des journaux de diagnostic Azure Stack pour faciliter et améliorer la collecte des journaux de diagnostic. Le service de collecte des journaux de diagnostic Azure Stack propose un moyen simplifié de collecter et de partager des journaux de diagnostic avec les services de support technique Microsoft. Ce service de collecte des journaux de diagnostic offre une nouvelle expérience utilisateur dans le portail d’administration Azure Stack, qui permet aux opérateurs de configurer le chargement automatique des journaux de diagnostic sur un objet blob de stockage lorsque certaines alertes critiques sont générées, ou d’effectuer la même opération à la demande. Pour plus d’informations, consultez l'article [Collecte des journaux de diagnostic](azure-stack-diagnostic-log-collection-overview.md).

- Mise à la disposition générale de la validation de l’infrastructure réseau Azure Stack dans le cadre de l'outil de validation Azure Stack **Test-AzureStack**. L'infrastructure réseau Azure Stack fera partie de **Test-AzureStack** pour identifier toute défaillance sur l'infrastructure. Le test vérifie la connectivité de l’infrastructure réseau en contournant le réseau à définition logicielle Azure Stack. Il démontre la connectivité d’une adresse IP virtuelle publique aux redirecteurs DNS, serveurs NTP et points de terminaison d'identité configurés. En outre, il vérifie la connectivité à Azure quand Azure AD est utilisé en tant que fournisseur d’identité ou au serveur fédéré quand ADFS est utilisé. Pour plus d’informations, consultez l'article [Outil de validation Azure Stack](azure-stack-diagnostic-test.md).

- Ajout d’une procédure de rotation des secrets internes pour faire tourner les certificats TLS SQL internes en fonction des besoins pendant une mise à jour du système.

### <a name="improvements-1907"></a>Améliorations

<!-- Changes and product improvements with tangible customer-facing value. -->

- Le panneau de mise à jour Azure Stack affiche désormais une heure **Dernière étape terminée** pour les mises à jour actives. Pour la consulter, accédez au panneau de mise à jour et cliquez sur une mise à jour en cours d’exécution. **Dernière étape terminée** est ensuite disponible dans la section **Détails de l'exécution de la mise à jour**.

- Améliorations apportées aux actions de l’opérateur **Start-AzureStack** et **Stop-AzureStack**. Le temps de démarrage d'Azure Stack a été réduit d'environ 50 %. Le temps d’arrêt d'Azure Stack a été réduit d'environ 30 %. Les temps de démarrage et d’arrêt moyens sont les mêmes lorsque le nombre de nœuds augmente dans une unité d’échelle.

- Amélioration de la gestion des erreurs pour l’outil Place de marché déconnecté. Si un téléchargement échoue ou réussit partiellement lors de l'utilisation de **Export-AzSOfflineMarketplaceItem**, un message s'affiche avec plus de détails sur l'erreur et la procédure d’atténuation, le cas échéant.

- Amélioration des performances de création de disques managés à partir d’un objet blob de pages/instantané de grande taille. Auparavant, cela déclenchait un délai d’expiration lors de la création d’un disque de grande taille.  

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/127669774/home -->
- Amélioration de la vérification d'intégrité du disque virtuel avant l’arrêt d’un nœud pour éviter le détachement inattendu du disque.

- Amélioration du stockage des journaux internes pour les opérations de l’administrateur. Cela permet d’améliorer les performances et la fiabilité lors des opérations de l’administrateur en limitant la consommation de mémoire et de stockage des processus de journalisation interne. Vous remarquerez peut-être une amélioration des délais de chargement des pages du panneau de mise à jour dans le portail administrateur. Dans le cadre de cette amélioration, les journaux des mises à jour datant de plus de 6 mois ne seront plus disponibles dans le système. Si vous avez besoin de ces journaux, veillez à [Télécharger le résumé](azure-stack-apply-updates.md) de toutes les exécutions de mises à jour datant de plus de 6 mois avant d’effectuer la mise à jour 1907.

### <a name="changes-1907"></a>Modifications

- Azure Stack version 1907 contient une alerte d’avertissement qui indique aux opérateurs de bien mettre à jour le package OEM de leur système vers la version 2.1 ou ultérieure avant d’effectuer la mise à jour vers la version 1908. Pour plus d’informations sur l’application de mises à jour OEM dans Azure Stack, consultez [Appliquer des mises à jour de fabricants d’ordinateurs à Azure Stack](azure-stack-update-oem.md).

- Ajout d’une nouvelle règle de trafic sortant (HTTPS) afin d'activer la communication pour le service de collecte des journaux de diagnostic Azure Stack. Pour plus d’informations, consultez [Intégration au centre de données Azure Stack - Publier des points de terminaison](azure-stack-integrate-endpoints.md#ports-and-urls-outbound).

- Désormais, le service de sauvegarde d’infrastructure supprime partiellement les sauvegardes téléchargées si la capacité de l’emplacement de stockage externe est insuffisante.

- Les sauvegardes d’infrastructure n’incluent plus la sauvegarde des données des services de domaine. Cela s'applique uniquement aux systèmes utilisant Azure Active Directory en tant que fournisseur d'identité.

- Nous vérifions désormais que l'ingestion d'une image dans le panneau **Calcul -> Images de machine virtuelle** est de type objet blob de pages.

### <a name="fixes-1907"></a>Correctifs

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

## <a name="security-updates-1907"></a>Mises à jour de sécurité

Pour plus d’informations sur les mises à jour de sécurité dans cette mise à jour d’Azure Stack, consultez [Mises à jour de sécurité Azure Stack](release-notes-security-updates.md).

## <a name="update-planning-1907"></a>Planification des mises à jour

Avant d’appliquer la mise à jour, veillez à consulter les informations suivantes :

- [Problèmes connus](known-issues.md)
- [Mises à jour de sécurité](release-notes-security-updates.md)
- [Liste de vérification des activités avant et après l’application de la mise à jour](release-notes-checklist.md)

## <a name="download-the-update-1907"></a>Télécharger la mise à jour

Vous pouvez télécharger la mise à jour 1907 d’Azure Stack sur la [page de téléchargement d’Azure Stack](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes-1907"></a>Correctifs logiciels

Azure Stack publie des correctifs logiciels à intervalles réguliers. Avant d’installer la mise à jour 1907 d’Azure Stack, veillez à installer le dernier correctif logiciel d’Azure Stack pour la build 1906.

Les correctifs logiciels Azure Stack sont uniquement applicables aux systèmes intégrés Azure Stack. N’essayez pas d’installer des correctifs logiciels sur l’ASDK.

### <a name="before-applying-the-1907-update"></a>Avant d’appliquer la mise à jour 1907

La version 1907 d’Azure Stack doit être appliquée à la version 1906 avec les correctifs logiciels suivants :

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Correctif logiciel Azure Stack 1.1906.13.56](https://support.microsoft.com/help/4520375)

### <a name="after-successfully-applying-the-1907-update"></a>Une fois la mise à jour 1907 correctement appliquée

Après l’installation de cette mise à jour, installez les correctifs logiciels applicables. Pour plus d’informations, consultez notre [stratégie de maintenance](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Correctif logiciel Azure Stack 1.1907.15.50](https://support.microsoft.com/help/4520315)
::: moniker-end

::: moniker range="azs-1906"
## <a name="1906-build-reference"></a>Référence de build 1906

Le numéro de build de la mise à jour 1906 d’Azure Stack est **1.1906.0.30**.

### <a name="update-type-1906"></a>Type de mise à jour

Le type de build de la mise à jour 1906 d’Azure Stack est **Express**. Pour plus d’informations sur les types de build de mise à jour, consultez l’article [Gérer les mises à jour dans Azure Stack](azure-stack-updates.md). La durée nécessaire pour effectuer la mise à jour 1906 terminer est estimée à environ 10 heures, quel que soit le nombre de nœuds physiques dans votre environnement Azure Stack. La durée d’exécution exacte des mises à jour dépend généralement de la capacité utilisée sur votre système par les charges de travail client, de la connectivité réseau de votre système (s’il est connecté à Internet) et les caractéristiques de votre matériel système. Les durées d’exécution plus longues que les valeurs attendues ne sont pas rares et ne nécessitent aucune action de la part des opérateurs Azure Stack, sauf si la mise à jour échoue. Cette approximation d’exécution est propre à la mise à jour 1906 et n’est pas comparable aux autres mises à jour d’Azure Stack.

## <a name="whats-in-this-update-1906"></a>Éléments de cette mise à jour

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

- Ajouter un cmdlet **Set-TLSPolicy** dans le point de terminaison privilégié (PEP) pour forcer le TLS 1.2 sur tous les points de terminaison. Pour plus d’informations, consultez [Contrôles de sécurité Azure Stack](azure-stack-security-configuration.md).

- Ajout d’un cmdlet **Get-TLSPolicy** dans le point de terminaison privilégié (PEP) pour récupérer la stratégie TLS appliquée. Pour plus d’informations, consultez [Contrôles de sécurité Azure Stack](azure-stack-security-configuration.md).

- Ajout d’une procédure de rotation des secrets internes pour faire tourner les certificats TLS internes en fonction des besoins pendant une mise à jour du système.

- Ajout d’un dispositif de protection pour empêcher l’expiration des secrets internes en forçant la rotation des secrets internes au cas où une alerte critique sur les secrets arrivant à expiration est ignorée. Ceci ne doit pas être considéré comme une procédure de fonctionnement normale. La rotation des secrets doit être planifiée pour se produire pendant une fenêtre de maintenance. Pour plus d’informations, consultez [Rotation des secrets Azure Stack](azure-stack-rotate-secrets.md).

- Visual Studio Code est désormais pris en charge avec le déploiement Azure Stack avec AD FS.

### <a name="improvements-1906"></a>Améliorations

<!-- Changes and product improvements with tangible customer-facing value. -->

- Le cmdlet **Get-GraphApplication** dans le point de terminaison privilégié affiche désormais l’empreinte du certificat actuellement utilisé. Ceci améliore la gestion des certificats pour les principaux de service quand Azure Stack est déployé avec AD FS.

- De nouvelles règles de surveillance d’intégrité ont été ajoutées pour valider la disponibilité d’AD Graph et AD FS, y compris la possibilité de déclencher les alertes.

- Améliorations de la fiabilité du fournisseur de ressources de sauvegarde lorsque le service de sauvegarde d’infrastructure est déplacé vers une autre instance.

- Optimisation des performances de la procédure de rotation des secrets externes pour fournir une durée d’exécution uniforme afin de faciliter la planification de la fenêtre de maintenance.

- Le cmdlet **Test-AzureStack** signale désormais les secrets internes qui sont sur le point d’expirer (alertes critiques).

- Un nouveau paramètre est disponible pour le cmdlet **Register-CustomAdfs** dans le point de terminaison privilégié qui permet d’ignorer la vérification de la liste de révocation des certificats lors de la configuration de l’approbation de fédération pour AD FS.

- La version 1906 bénéficie d’une meilleure visibilité sur la progression en cours de mise à jour : vous êtes donc certain que les mises à jour ne sont pas suspendues. Cela entraîne une augmentation du nombre total d’étapes de mise à jour présentées aux opérateurs dans le panneau **Mise à jour**. Vous pourrez également remarquer plus d’étapes de mise à jour en parallèle que dans les mises à jour précédentes.

#### <a name="networking-updates-1906"></a>Mises à jour en réseau

- Mise à jour de la durée de bail définie dans le répondeur DHCP pour être cohérent avec Azure.

- Amélioration des taux de nouvelles tentatives auprès du fournisseur de ressources en cas d’échec du déploiement de ressources.

- Suppression de l’option de référence SKU **Standard** de l’équilibreur de charge et de l’adresse IP publique, car ce n’est actuellement pas pris en charge.

### <a name="changes-1906"></a>Modifications

- La création des comptes de stockage est désormais cohérente avec celle d’Azure.

- Modification des déclencheurs d’alertes à l’expiration des secrets internes :
  - Les alertes d’avertissement sont maintenant déclenchées 90 jours avant l’expiration des secrets.
  - Les alertes critiques sont maintenant déclenchées 30 jours avant l’expiration des secrets.

- Mise à jour des chaînes dans le fournisseur de ressources de sauvegarde d’infrastructure pour rendre la terminologie plus cohérente.

### <a name="fixes-1906"></a>Correctifs

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

## <a name="security-updates-1906"></a>Mises à jour de sécurité

Pour plus d’informations sur les mises à jour de sécurité dans cette mise à jour d’Azure Stack, consultez [Mises à jour de sécurité Azure Stack](release-notes-security-updates.md).

## <a name="update-planning-1906"></a>Planification des mises à jour

Avant d’appliquer la mise à jour, veillez à consulter les informations suivantes :

- [Problèmes connus](known-issues.md)
- [Mises à jour de sécurité](release-notes-security-updates.md)
- [Liste de vérification des activités avant et après l’application de la mise à jour](release-notes-checklist.md)

## <a name="download-the-update-1906"></a>Télécharger la mise à jour

Vous pouvez télécharger la mise à jour 1906 d’Azure Stack sur la [page de téléchargement d’Azure Stack](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes-1906"></a>Correctifs logiciels

Azure Stack publie des correctifs logiciels à intervalles réguliers. Avant d’installer la mise à jour 1906 d’Azure Stack, veillez à installer le dernier correctif logiciel d’Azure Stack pour la build 1905. Après la mise à jour, installez tous les [correctifs logiciels disponibles pour 1906](#after-successfully-applying-the-1906-update).

Les correctifs logiciels Azure Stack sont uniquement applicables aux systèmes intégrés Azure Stack. N’essayez pas d’installer des correctifs logiciels sur l’ASDK.

### <a name="before-applying-the-1906-update"></a>Avant d’appliquer la mise à jour 1906

La version 1906 d’Azure Stack doit être appliquée à la version 1905 avec les correctifs logiciels suivants :

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack hotfix 1.1905.3.48](https://support.microsoft.com/help/4510078)

### <a name="after-successfully-applying-the-1906-update"></a>Une fois la mise à jour 1906 correctement appliquée

Après l’installation de cette mise à jour, installez les correctifs logiciels applicables. Pour plus d’informations, consultez notre [stratégie de maintenance](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Correctif logiciel Azure Stack 1.1906.13.56](https://support.microsoft.com/help/4520375)
::: moniker-end

::: moniker range="azs-1905"
## <a name="1905-build-reference"></a>Référence de build 1905

Le numéro de build de la mise à jour 1905 d’Azure Stack est **1.1905.0.40**.

### <a name="update-type-1905"></a>Type de mise à jour

Le type de build de la mise à jour 1905 d’Azure Stack est **Complet**. Par conséquent, la mise à jour 1905 a un runtime plus long que les mises à jour rapides, telles que 1903 et 1904. Les runtimes exacts des mises à jour complètes dépendent généralement du nombre de nœuds que votre instance Azure Stack contient, de la capacité utilisée sur votre système par les charges de travail clientes, de la connectivité réseau de votre système (s’il est connecté à Internet) et de la configuration de votre matériel système. La mise à jour 1905 présentait les runtimes attendus suivants dans nos tests internes : 4 nœuds - 35 heures, 8 nœuds - 45 heures, 12 nœuds - 55 heures, 16 nœuds - 70 heures. Les runtimes de la mise à jour 1905 plus longs que ces valeurs attendues ne sont pas rares et ne nécessitent aucune action de la part des opérateurs Azure Stack, sauf si la mise à jour échoue. Pour plus d’informations sur les types de build de mise à jour, voir [Gérer les mises à jour dans Azure Stack](azure-stack-updates.md).

## <a name="whats-in-this-update-1905"></a>Éléments de cette mise à jour

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

- Avec cette mise à jour, le moteur de mise à jour dans Azure Stack peut mettre à jour le microprogramme des nœuds d’unité d’échelle. Cela nécessite un package de mise à jour compatible des partenaires fournisseurs de matériel. Contactez votre fournisseur de matériel pour en savoir plus sur la disponibilité.

- Windows Server 2019 est désormais pris en charge et disponible pour la syndication par le biais de la Place de marché Azure Stack.
Avec cette mise à jour, Windows Server 2019 peut être activé correctement sur un hôte 2016.

- Avec la nouvelle [extension Azure Account Visual Studio Code](../user/azure-stack-dev-start-vscode-azure.md), les développeurs peuvent cibler Azure Stack en se connectant et accédant aux abonnements, ainsi que divers autres services. L’extension Azure fonctionne aussi bien sur les environnements Azure Active Directory (Azure AD) et AD FS, et ne nécessite qu’une petite modification des paramètres utilisateur de Visual Studio Code. Visual Studio Code exige que le principal de service reçoive une autorisation à s’exécuter sur cet environnement. Pour ce faire, importez le script d’identité et exécutez les cmdlets spécifiées dans [Architecture mutualisée dans Azure Stack](../operator/azure-stack-enable-multitenancy.md). Ceci nécessite une mise à jour du répertoire de base et l’inscription du répertoire du locataire invité pour chaque annuaire. Une alerte s’affiche après la mise à jour vers la version 1905 ou une version ultérieure, pour mettre à jour le locataire du répertoire de base pour lequel le principal de service Visual Studio Code est inclus. 

### <a name="improvements-1905"></a>Améliorations

<!-- Changes and product improvements with tangible customer-facing value. -->
- Dans le cadre de l’application de TLS 1.2 sur Azure Stack, les extensions suivantes ont été mises à jour avec ces versions :

  - microsoft.customscriptextension-arm-1.9.3
  - microsoft.iaasdiagnostics-1.12.2.2
  - microsoft.antimalware-windows-arm-1.5.5.9
  - microsoft.dsc-arm-2.77.0.0
  - microsoft.vmaccessforlinux-1.5.2

  Téléchargez ces nouvelles versions d’extensions immédiatement afin d’éviter l’échec des nouveaux déploiements de l’extension quand TLS 1.2 sera appliqué dans une version ultérieure. Définissez toujours **autoUpgradeMinorVersion=true** pour que les mises à jour de version mineure des extensions (par exemple, version 1.8 à 1.9) soient effectuées automatiquement.

- La nouvelle fonctionnalité **Aide et support - Vue d’ensemble** du portail Azure Stack permet aux opérateurs de se renseigner sur leurs options de support, de bénéficier d’une aide spécialisée et d’en savoir plus sur Azure Stack plus facilement. Sur les systèmes intégrés, la création d’une demande de support présélectionne le service Azure Stack. Nous recommandons fortement aux clients d’utiliser ce moyen pour envoyer des tickets de support, plutôt que d’utiliser le portail Azure général. Pour plus d’informations, consultez [Aide et support de Microsoft Azure Stack](azure-stack-help-and-support-overview.md).

- Quand plusieurs annuaires Azure Active Directory sont intégrés (par le biais de [ce processus](azure-stack-enable-multitenancy.md)), il est possible de ne pas réexécuter le script lors de certaines mises à jour, ou lorsque des droits sont insuffisants en raison des modifications apportées à l’autorisation du principal de Service Azure AD. Ceci peut entraîner divers problèmes, d’un blocage de l’accès à certaines fonctionnalités à des erreurs plus discrètes qui sont difficiles à tracer jusqu’au problème d’origine. Pour éviter ce problème, la version 1905 introduit une nouvelle fonctionnalité qui vérifie les autorisations et déclenche une alerte en présence de certains problèmes de configuration. Cette validation s’exécute toutes les heures et affiche les actions de correction requises pour corriger le problème. L’alerte est fermée quand tous les locataires présentent un état sain.

- Plus grande fiabilité des opérations de sauvegarde de l’infrastructure pendant le basculement du service.

- Une nouvelle version du [plug-in Nagios pour Azure Stack](azure-stack-integrate-monitor.md#integrate-with-nagios) est disponible. Elle utilise les [bibliothèques d’authentification Azure Active Directory](/azure/active-directory/develop/active-directory-authentication-libraries) (ADAL) pour l’authentification. Désormais, le plug-in prend également en charge les déploiements Azure AD et Active Directory Federation Services (AD FS) d’Azure Stack. Pour plus d’informations, consultez le site [Nagios Exchange pour les plug-ins](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details).

- Un nouveau profil hybride **2019-03-01-Hybrid** a été publié. Il prend en charge toutes les dernières fonctionnalités disponibles dans Azure Stack. Azure PowerShell et Azure CLI prennent tous les deux en charge le profil **2019-03-01-Hybrid**. Les SDK .NET, Ruby, Node.js, Go et Python ont publié des packages qui prennent en charge le profil **2019-03-01-Hybrid**. La documentation respective et certains exemples ont été mis à jour pour refléter les modifications.

- Le [SDK Node.js](https://www.npmjs.com/search?q=2019-03-01-hybrid) prend désormais en charge les profils d’API. Les packages qui prennent en charge le profil **2019-03-01-Hybrid** sont publiés.

- La mise à jour Azure Stack 1905 ajoute deux nouveaux rôles d’infrastructure pour améliorer la fiabilité et la capacité de prise en charge de la plateforme :

  - **Anneau d’infrastructure** : À l’avenir, l’anneau d’infrastructure hébergera des versions en conteneur des rôles d’infrastructure existants (par exemple, xrp), qui nécessitent actuellement leurs propres machines virtuelles d’infrastructure désignées. Cela va améliorer la fiabilité de la plateforme et réduire le nombre de machines virtuelles d’infrastructure nécessaires à Azure Stack. Cela réduira ensuite la future consommation globale des ressources des rôles d’infrastructure Azure Stack.
  - **Anneau de support** À l’avenir, l’anneau de support servira à gérer les scénarios de prise en charge améliorée pour les clients.  

  En outre, nous avons ajouté une instance supplémentaire de la machine virtuelle du contrôleur de domaine pour améliorer la disponibilité de ce rôle.

  Ces modifications vont augmenter la consommation des ressources de l’infrastructure Azure Stack de la manière suivante :
  
    | Référence SKU Azure Stack | Augmentation de la consommation des capacités de calcul | Augmentation de la consommation de mémoire |
    | -- | -- | -- |
    |4 nœuds|22 processeurs virtuels|28 Go|
    |8 nœuds|38 processeurs virtuels|44 Go|
    |12 nœuds|54 processeurs virtuels|60 Go|
    |16 nœuds|70 processeurs virtuels|76 Go|
  
### <a name="changes-1905"></a>Modifications

- Pour améliorer la fiabilité et la disponibilité durant les scénarios de maintenance planifiée et non planifiée, Azure Stack fournit une instance de rôle d’infrastructure supplémentaire pour les services de domaine.

- Avec cette mise à jour, pendant les opérations de réparation et d’ajout de nœuds, le matériel est validé pour garantir l’homogénéité des nœuds d’unité d’échelle au sein d’une unité d’échelle.

- Si des sauvegardes planifiées échouent et que la période de rétention définie est dépassée, le contrôleur de sauvegarde de l’infrastructure s’assure qu’au moins une sauvegarde réussie est conservée. 

### <a name="fixes-1905"></a>Correctifs

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Correction d’un problème qui provoquait l’affichage d’un avertissement d’**agent hôte de calcul** après le redémarrage d’un nœud dans l’unité d’échelle.

- Correction des problèmes dans la gestion de la Place de marché dans le portail administrateur qui provoquaient l’affichage de résultats incorrects après l’application de filtres et l’affichage de noms d’éditeurs en double dans la liste filtrée des éditeurs. Les performances ont également été améliorées pour afficher les résultats plus rapidement.

- Correction d’un problème dans le panneau des sauvegardes disponibles qui montrait une nouvelle sauvegarde comme disponible avant qu’elle ait été chargée à l’emplacement de stockage externe. La sauvegarde disponible est désormais listée seulement après son chargement à l’emplacement de stockage. 

<!-- ICM: 114819337; Task: 4408136 -->
- Correction d’un problème avec l’obtention des clés de récupération pendant une opération de sauvegarde. 

<!-- Bug: 4525587 -->
- Correction d’un problème avec la mise à jour OEM qui affichait la version comme étant « non définie » dans le portail de l’opérateur.

### <a name="security-updates-1905"></a>Mises à jour de sécurité

Pour plus d’informations sur les mises à jour de sécurité dans cette mise à jour d’Azure Stack, consultez [Mises à jour de sécurité Azure Stack](release-notes-security-updates.md).

## <a name="update-planning-1905"></a>Planification des mises à jour

Avant d’appliquer la mise à jour, veillez à consulter les informations suivantes :

- [Problèmes connus](known-issues.md)
- [Mises à jour de sécurité](release-notes-security-updates.md)
- [Liste de vérification des activités avant et après l’application de la mise à jour](release-notes-checklist.md)

## <a name="download-the-update-1905"></a>Télécharger la mise à jour

Vous pouvez télécharger la mise à jour 1905 d’Azure Stack à partir de la [page de téléchargement d’Azure Stack](https://aka.ms/azurestackupdatedownload). Lorsque vous utilisez l’outil de téléchargement, veillez à utiliser la version la plus récente et pas une copie mise en cache à partir de votre répertoire de téléchargements.

## <a name="hotfixes-1905"></a>Correctifs logiciels

Azure Stack publie des correctifs logiciels à intervalles réguliers. Avant d’installer la mise à jour 1905 d’Azure Stack, veillez à installer le dernier correctif logiciel d’Azure Stack pour la build 1904.

Les correctifs logiciels Azure Stack sont uniquement applicables aux systèmes intégrés Azure Stack. N’essayez pas d’installer des correctifs logiciels sur l’ASDK.

### <a name="before-applying-the-1905-update"></a>Avant d’appliquer la mise à jour 1905

La version 1905 d’Azure Stack doit être appliquée à la version 1904 avec les correctifs suivants :

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Correctif Azure Stack 1.1904.4.45](https://support.microsoft.com/help/4505688)

### <a name="after-successfully-applying-the-1905-update"></a>Une fois la mise à jour 1905 correctement appliquée

Après l’installation de cette mise à jour, installez les correctifs logiciels applicables. Pour plus d’informations, consultez notre [stratégie de maintenance](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Correctif Azure Stack 1.1905.3.48](https://support.microsoft.com/help/4510078)
::: moniker-end

::: moniker range=">=azs-1905"
## <a name="automatic-update-notifications"></a>Notifications de mise à jour automatique

Les systèmes qui peuvent accéder à Internet à partir du réseau d’infrastructure voient le message **Mise à jour disponible** dans le portail opérateur. Les systèmes sans accès à Internet peuvent télécharger et importer le fichier .zip avec le fichier .xml correspondant.

> [!TIP]  
> Pour rester informé des publications des correctifs logiciels d’Azure Stack, abonnez-vous aux flux *RSS* ou *Atom* suivants :
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="archive"></a>Archivage

Pour accéder aux notes de publication archivées d’une version antérieure, utilisez le sélecteur de version en haut à gauche de la page, puis sélectionnez la version qui vous intéresse. 

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble de la gestion des mises à jour dans Azure Stack, consultez [Gérer les mises à jour dans Azure Stack - Vue d’ensemble](azure-stack-updates.md).  
- Pour plus d’informations sur la façon d’appliquer des mises à jour avec Azure Stack, consultez [Effectuer des mises à jour dans Azure Stack](azure-stack-apply-updates.md).
- Pour passer en revue la stratégie de maintenance pour les systèmes intégrés Azure Stack et pour connaître la marche à suivre afin de conserver votre système dans un état de prise en charge, consultez [Stratégie de maintenance Azure Stack](azure-stack-servicing-policy.md).  
- Pour utiliser le Point de terminaison privilégié (PEP) afin de surveiller et de reprendre les mises à jour, consultez [Surveiller les mises à jour dans Azure Stack à l’aide du point de terminaison privilégié](azure-stack-monitor-update.md).
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
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

::: moniker range="<azs-1905"
Vous pouvez accéder aux [versions antérieures des notes de publication d’Azure Stack dans la galerie TechNet](https://aka.ms/azsarchivedrelnotes). Ces documents archivés sont fournis uniquement pour référence et n’impliquent aucune prise en charge de ces versions. Pour plus d’informations sur le support d’Azure Stack, consultez [Stratégie de maintenance Azure Stack](azure-stack-servicing-policy.md). Pour obtenir de l’aide, contactez les services de support technique Microsoft.
::: moniker-end


