---
title: Notes de publication pour Azure Stack 1907 | Microsoft Docs
description: En savoir plus sur les mises à jour 1907 pour les systèmes intégrés Azure Stack
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
ms.date: 10/29/2019
ms.author: sethm
ms.reviewer: prchint
ms.lastreviewed: 10/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 9d5aeb68190f8e2b13ec2a49c3237b59ee33bfff
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248637"
---
# <a name="azure-stack-updates-1907-release-notes"></a>Mises à jour d’Azure Stack: Notes de publication 1907

Cet article décrit le contenu des packages de mise à jour d’Azure Stack. La mise à jour inclut des améliorations, nouveautés et correctifs pour cette version d’Azure Stack.

Pour accéder aux notes de publication d'une autre version, utilisez le menu déroulant de sélection de la version, situé au-dessus de la table des matières à gauche.

> [!IMPORTANT]  
> Cette mise à jour est destinée uniquement aux systèmes intégrés d’Azure Stack. N’appliquez pas cette mise à jour au Kit de développement Azure Stack.

> [!IMPORTANT]  
> Si votre instance Azure Stack est en retard de plus de deux mises à jour, elle est considérée comme non conforme. Pour bénéficier de la prise en charge, vous devez [mettre à jour avec au moins la version minimale prise en charge](../azure-stack-servicing-policy.md#keep-your-system-under-support).

## <a name="update-planning"></a>Planification des mises à jour

Avant d’appliquer la mise à jour, veillez à consulter les informations suivantes :

- [Problèmes connus](known-issues-1907.md)
- [Mises à jour de sécurité](../release-notes-security-updates.md)
- [Liste de vérification des activités avant et après l’application de la mise à jour](../release-notes-checklist.md)

Pour obtenir de l'aide sur le dépannage des mises à jour et le processus de mise à jour, voir [Résolution des problèmes liés aux correctifs et aux mises à jour pour Azure Stack](../azure-stack-updates-troubleshoot.md).

## <a name="1907-build-reference"></a>Référence de build 1907

Le numéro de build de la mise à jour 1907 d’Azure Stack est **1.1907.0.20**.

### <a name="update-type"></a>Type de mise à jour

Le type de build de la mise à jour 1907 d’Azure Stack est **Express**. Pour plus d’informations sur les types de build de mise à jour, consultez l’article [Gérer les mises à jour dans Azure Stack](../azure-stack-updates.md). D'après des tests internes, la mise à jour 1907 prend environ 13 heures.

- La durée d’exécution exacte des mises à jour dépend généralement de la capacité utilisée sur votre système par les charges de travail client, de la connectivité réseau de votre système (s’il est connecté à Internet) et de la configuration de votre matériel système.
- Les durées d’exécution plus longues que prévu ne sont pas rares et ne nécessitent aucune action de la part des opérateurs Azure Stack, sauf si la mise à jour échoue.
- Cette approximation d’exécution est propre à la mise à jour 1907 et n’est pas comparable aux autres mises à jour d’Azure Stack.

## <a name="whats-in-this-update"></a>Éléments de cette mise à jour

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Nouveautés

<!-- What's new, also net new experiences and features. -->

- Mise à la disposition générale du service de collecte des journaux de diagnostic Azure Stack pour faciliter et améliorer la collecte des journaux de diagnostic. Le service de collecte des journaux de diagnostic Azure Stack propose un moyen simplifié de collecter et de partager des journaux de diagnostic avec les services de support technique Microsoft. Ce service de collecte des journaux de diagnostic offre une nouvelle expérience utilisateur dans le portail d’administration Azure Stack, qui permet aux opérateurs de configurer le chargement automatique des journaux de diagnostic sur un objet blob de stockage lorsque certaines alertes critiques sont générées, ou d’effectuer la même opération à la demande. Pour plus d’informations, consultez l'article [Collecte des journaux de diagnostic](../azure-stack-diagnostic-log-collection-overview.md).

- Mise à la disposition générale de la validation de l’infrastructure réseau Azure Stack dans le cadre de l'outil de validation Azure Stack **Test-AzureStack**. L'infrastructure réseau Azure Stack fera partie de **Test-AzureStack** pour identifier toute défaillance sur l'infrastructure. Le test vérifie la connectivité de l’infrastructure réseau en contournant le réseau à définition logicielle Azure Stack. Il démontre la connectivité d’une adresse IP virtuelle publique aux redirecteurs DNS, serveurs NTP et points de terminaison d'identité configurés. En outre, il vérifie la connectivité à Azure quand Azure AD est utilisé en tant que fournisseur d’identité ou au serveur fédéré quand ADFS est utilisé. Pour plus d’informations, consultez l'article [Outil de validation Azure Stack](../azure-stack-diagnostic-test.md).

- Ajout d’une procédure de rotation des secrets internes pour faire tourner les certificats TLS SQL internes en fonction des besoins pendant une mise à jour du système.

### <a name="improvements"></a>Améliorations

<!-- Changes and product improvements with tangible customer-facing value. -->

- Le panneau de mise à jour Azure Stack affiche désormais une heure **Dernière étape terminée** pour les mises à jour actives. Pour la consulter, accédez au panneau de mise à jour et cliquez sur une mise à jour en cours d’exécution. **Dernière étape terminée** est ensuite disponible dans la section **Détails de l'exécution de la mise à jour**.

- Améliorations apportées aux actions de l’opérateur **Start-AzureStack** et **Stop-AzureStack**. Le temps de démarrage d'Azure Stack a été réduit d'environ 50 %. Le temps d’arrêt d'Azure Stack a été réduit d'environ 30 %. Les temps de démarrage et d’arrêt moyens sont les mêmes lorsque le nombre de nœuds augmente dans une unité d’échelle.

- Amélioration de la gestion des erreurs pour l’outil Place de marché déconnecté. Si un téléchargement échoue ou réussit partiellement lors de l'utilisation de **Export-AzSOfflineMarketplaceItem**, un message s'affiche avec plus de détails sur l'erreur et la procédure d’atténuation, le cas échéant.

- Amélioration des performances de création de disques managés à partir d’un objet blob de pages/instantané de grande taille. Auparavant, cela déclenchait un délai d’expiration lors de la création d’un disque de grande taille.  

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/127669774/home -->
- Amélioration de la vérification d'intégrité du disque virtuel avant l’arrêt d’un nœud pour éviter le détachement inattendu du disque.

- Amélioration du stockage des journaux internes pour les opérations de l’administrateur. Cela permet d’améliorer les performances et la fiabilité lors des opérations de l’administrateur en limitant la consommation de mémoire et de stockage des processus de journalisation interne. Vous remarquerez peut-être une amélioration des délais de chargement des pages du panneau de mise à jour dans le portail administrateur. Dans le cadre de cette amélioration, les journaux des mises à jour datant de plus de 6 mois ne seront plus disponibles dans le système. Si vous avez besoin de ces journaux, veillez à [Télécharger le résumé](../azure-stack-apply-updates.md) de toutes les exécutions de mises à jour datant de plus de 6 mois avant d’effectuer la mise à jour 1907.

### <a name="changes"></a>Modifications

- Azure Stack version 1907 contient une alerte d’avertissement qui indique aux opérateurs de bien mettre à jour le package OEM de leur système vers la version 2.1 ou ultérieure avant d’effectuer la mise à jour vers la version 1908. Pour plus d’informations sur l’application de mises à jour OEM dans Azure Stack, consultez [Appliquer des mises à jour de fabricants d’ordinateurs à Azure Stack](../azure-stack-update-oem.md).

- Ajout d’une nouvelle règle de trafic sortant (HTTPS) afin d'activer la communication pour le service de collecte des journaux de diagnostic Azure Stack. Pour plus d’informations, consultez [Intégration au centre de données Azure Stack - Publier des points de terminaison](../azure-stack-integrate-endpoints.md#ports-and-urls-outbound).

- Désormais, le service de sauvegarde d’infrastructure supprime partiellement les sauvegardes téléchargées si la capacité de l’emplacement de stockage externe est insuffisante.

- Les sauvegardes d’infrastructure n’incluent plus la sauvegarde des données des services de domaine. Cela s'applique uniquement aux systèmes utilisant Azure Active Directory en tant que fournisseur d'identité.

- Nous vérifions désormais que l'ingestion d'une image dans le panneau **Calcul -> Images de machine virtuelle** est de type objet blob de pages.

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

Pour plus d’informations sur les mises à jour de sécurité dans cette mise à jour d’Azure Stack, consultez [Mises à jour de sécurité Azure Stack](../release-notes-security-updates.md).

## <a name="download-the-update"></a>Télécharger la mise à jour

Vous pouvez télécharger la mise à jour 1907 d’Azure Stack sur la [page de téléchargement d’Azure Stack](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes"></a>Correctifs

Azure Stack publie des correctifs logiciels à intervalles réguliers. Avant d’installer la mise à jour 1907 d’Azure Stack, veillez à installer le dernier correctif logiciel d’Azure Stack pour la build 1906.

Les correctifs logiciels Azure Stack sont uniquement applicables aux systèmes intégrés Azure Stack. N’essayez pas d’installer des correctifs logiciels sur l’ASDK.

### <a name="before-applying-the-1907-update"></a>Avant d’appliquer la mise à jour 1907

La version 1907 d’Azure Stack doit être appliquée à la version 1906 avec les correctifs logiciels suivants :

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Correctif logiciel Azure Stack 1.1906.15.60](https://support.microsoft.com/help/4524559)

### <a name="after-successfully-applying-the-1907-update"></a>Une fois la mise à jour 1907 correctement appliquée

Après l’installation de cette mise à jour, installez les correctifs logiciels applicables. Pour plus d’informations, consultez notre [stratégie de maintenance](../azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Correctif logiciel Azure Stack 1.1907.17.54](https://support.microsoft.com/help/4523826)