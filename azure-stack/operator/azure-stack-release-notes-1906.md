---
title: Notes de publication pour Azure Stack 1906 | Microsoft Docs
description: 'Découvrez la mise à jour 1906 des systèmes intégrés Azure Stack : nouveautés, problèmes connus et emplacement de téléchargement de la mise à jour.'
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
ms.date: 07/01/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 07/01/2019
ms.openlocfilehash: cc348bba064ddb003bbe6a2a06d2694714ad7894
ms.sourcegitcommit: 068350a79805366e7e6536fb7df85a412bd0be99
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67511269"
---
# <a name="azure-stack-1906-update"></a>Mise à jour 1906 d’Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Cet article décrit le contenu du package de mise à jour 1906. La mise à jour inclut des améliorations, nouveautés et correctifs pour cette version d’Azure Stack.

> [!IMPORTANT]  
> Cette mise à jour est destinée uniquement aux systèmes intégrés d’Azure Stack. N’appliquez pas cette mise à jour au Kit de développement Azure Stack.

## <a name="build-reference"></a>Référence de build

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

## <a name="security-updates"></a>Mises à jour de sécurité

Pour plus d’informations sur les mises à jour de sécurité dans cette mise à jour d’Azure Stack, consultez [Mises à jour de sécurité Azure Stack](azure-stack-release-notes-security-updates-1906.md).

## <a name="update-planning"></a>Planification des mises à jour

Avant d’appliquer la mise à jour, veillez à consulter les informations suivantes :

- [Problèmes connus](azure-stack-release-notes-known-issues-1906.md)
- [Mises à jour de sécurité](azure-stack-release-notes-security-updates-1906.md)
- [Liste de vérification des activités avant et après l’application de la mise à jour](azure-stack-release-notes-checklist.md)

## <a name="download-the-update"></a>Télécharger la mise à jour

Vous pouvez télécharger la mise à jour 1906 d’Azure Stack sur la [page de téléchargement d’Azure Stack](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes"></a>Correctifs logiciels

Azure Stack publie des correctifs logiciels à intervalles réguliers. Avant d’installer la mise à jour 1906 d’Azure Stack, veillez à installer le dernier correctif logiciel d’Azure Stack pour la build 1905.

Les correctifs logiciels Azure Stack sont uniquement applicables aux systèmes intégrés Azure Stack. N’essayez pas d’installer des correctifs logiciels sur l’ASDK.

### <a name="before-applying-the-1906-update"></a>Avant d’appliquer la mise à jour 1906

La version 1906 d’Azure Stack doit être appliquée à la version 1905 avec les correctifs logiciels suivants :

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack hotfix 1.1905.3.48](https://support.microsoft.com/help/4510078)

### <a name="after-successfully-applying-the-1906-update"></a>Une fois la mise à jour 1906 correctement appliquée

Après l’installation de cette mise à jour, installez les correctifs logiciels applicables. Pour plus d’informations, consultez notre [stratégie de maintenance](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Correctif Azure Stack 1.1906.2.37](https://support.microsoft.com/help/4511282)

## <a name="automatic-update-notifications"></a>Notifications de mise à jour automatique

Les clients utilisant des systèmes qui peuvent accéder à Internet à partir du réseau d’infrastructure voient le message **Mise à jour disponible** dans le portail opérateur. Les systèmes sans accès à Internet peuvent télécharger et importer le fichier .zip avec le fichier .xml correspondant.

> [!TIP]  
> Pour rester informé des publications des correctifs logiciels d’Azure Stack, abonnez-vous aux flux *RSS* ou *Atom* suivants :
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="archived-release-notes"></a>Notes de publication archivées

Vous pouvez voir les [versions antérieures des notes de publication d’Azure Stack dans la galerie TechNet](https://aka.ms/azsarchivedrelnotes). Ces notes de publication archivées sont fournies uniquement à des fins de référence et n’impliquent aucune prise en charge de ces versions. Pour plus d’informations sur le support d’Azure Stack, consultez [Stratégie de maintenance Azure Stack](azure-stack-servicing-policy.md). Pour obtenir de l’aide, contactez les services de support technique Microsoft.

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble de la gestion des mises à jour dans Azure Stack, consultez [Gérer les mises à jour dans Azure Stack - Vue d’ensemble](azure-stack-updates.md).  
- Pour plus d’informations sur la façon d’appliquer des mises à jour avec Azure Stack, consultez [Effectuer des mises à jour dans Azure Stack](azure-stack-apply-updates.md).
- Pour passer en revue la stratégie de maintenance pour les systèmes intégrés Azure Stack et pour connaître la marche à suivre afin de conserver votre système dans un état de prise en charge, consultez [Stratégie de maintenance Azure Stack](azure-stack-servicing-policy.md).  
- Pour utiliser le Point de terminaison privilégié (PEP) afin de surveiller et de reprendre les mises à jour, consultez [Surveiller les mises à jour dans Azure Stack à l’aide du point de terminaison privilégié](azure-stack-monitor-update.md).  
