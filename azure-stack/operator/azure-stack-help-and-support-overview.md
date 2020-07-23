---
title: Aide et support d’Azure Stack Hub
description: Bénéficiez d’un support pour Microsoft Azure Stack Hub.
author: justinha
ms.topic: article
ms.date: 02/26/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 02/26/2020
ms.openlocfilehash: f08f2af4cf85d1e4346b6fc378135d218216c9d0
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86488839"
---
# <a name="azure-stack-hub-help-and-support"></a>Aide et support d’Azure Stack Hub

::: moniker range=">= azs-2002"

Les opérateurs Azure Stack Hub peuvent utiliser **Aide + support** pour collecter les journaux de diagnostic et les envoyer à Microsoft à des fins de résolution des problèmes. **Aide + support** dans le portail Azure Stack Hub est accessible à partir du portail administrateur. Il propose aux opérateurs des ressources pour en savoir plus sur Azure Stack, vérifier leurs options de support et bénéficier d’une aide spécialisée.  

![Comment accéder à l’aide et au support dans le portail administrateur Azure Stack Hub](media/azure-stack-help-and-support/help-and-support.png)

## <a name="help-resources"></a>Ressources d’aide

Les opérateurs peuvent utiliser **Aide et support** pour en savoir plus sur Azure Stack Hub, vérifier leurs options de support et bénéficier d’une aide personnalisée.

### <a name="things-to-try-first"></a>Premières actions à essayer

En haut de **Aide + support** se trouvent des actions que vous devez essayer en premier, comme découvrir de nouveaux concepts, apprendre le fonctionnement de la facturation ou consulter les options de support disponibles.

![Support en libre-service dans Azure Stack Hub](media/azure-stack-help-and-support/get-support-tiles.png)

- **Documentation**. La [documentation pour les opérateurs Azure Stack Hub](index.yml) inclut des concepts, des rubriques pratiques et des tutoriels qui montrent comment proposer des services Azure Stack Hub. Parmi ces services figurent les machines virtuelles, les bases de données SQL et les applications web.

- **En savoir plus sur la facturation**. Lisez des conseils sur [l’utilisation et la facturation](azure-stack-billing-and-chargeback.md).

- **Options de support**. Les opérateurs Azure Stack Hub ont le choix parmi une gamme d’[options de support Azure](https://aka.ms/azstacksupport) qui peuvent répondre aux besoins de toute entreprise.

### <a name="get-expert-help"></a>Bénéficier d’une aide spécialisée

Pour un système intégré, il existe un processus d’escalade et de résolution coordonné entre Microsoft et nos partenaires fabricants d’ordinateurs OEM.

En cas de problème avec des services cloud, le support est assuré par le support Microsoft. Vous pouvez sélectionner **Aide** (point d’interrogation) dans le coin supérieur droit du portail administrateur et sélectionner ensuite sur **Aide + support** pour ouvrir **Aide + support - Vue d’ensemble** et envoyer une nouvelle demande de support. La création d’une demande de support présélectionne le service Azure Stack Hub. Nous recommandons fortement aux clients d’utiliser ce moyen pour envoyer des tickets de support, plutôt que d’utiliser le portail Azure global.

En cas de problème de déploiement, de correctif et de mise à jour, de matériel (y compris les unités remplaçables sur site) et de logiciel d’une marque de matériel (notamment les logiciels exécutés sur l’hôte du cycle de vie du matériel), contactez d’abord votre fournisseur de matériel OEM. Pour tout autre problème, contactez le support Microsoft.

![Bénéficier d’une aide spécialisée pour les systèmes intégrés](media/azure-stack-help-and-support/get-support-integrated.png)

Pour le Kit de développement Azure Stack (ASDK), vous pouvez poser des questions de support technique sur le [Forum MSDN Azure Stack Hub](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack).

Sélectionnez **Aide** (point d’interrogation) dans le coin supérieur droit du portail d’administration et sélectionnez ensuite **Aide + support** pour ouvrir **Aide + support - Vue d’ensemble**, où vous trouverez un lien vers le forum. Les forums MSDN sont consultés régulièrement. ASDK étant un environnement d’évaluation, le support Microsoft n’offre aucun support officiel.

Vous pouvez aussi contacter les forums MSDN pour trouver de l’aide concernant un problème, ou suivre une formation en ligne pour améliorer vos propres compétences.

![Bénéficier d’une aide spécialisée pour Azure Stack Hub](media/azure-stack-help-and-support/get-support-cards.png)

### <a name="get-up-to-speed-with-azure-stack-hub"></a>Apprendre tout ce qu’il faut savoir sur Azure Stack Hub

Cette série de tutoriels est personnalisée selon que vous exécutez le Kit ASDK ou des systèmes intégrés afin de vous permettre d’être rapidement opérationnel dans votre environnement.

![Obtenir des tutoriels de support pour Azure Stack Hub](media/azure-stack-help-and-support/get-support-tutorials.png)

## <a name="diagnostic-log-collection"></a>Collecte des journaux de diagnostic

Vous pouvez envoyer des journaux de diagnostic à Microsoft de deux manières :

- [Send logs proactively](./azure-stack-configure-automatic-diagnostic-log-collection.md?view=azs-2002) (Envoyer les journaux de manière proactive) : si cette option est activée, la collecte des journaux est déclenchée par des alertes d’intégrité spécifiques.
- [Send logs now](./azure-stack-configure-on-demand-diagnostic-log-collection-portal.md?view=azs-2002) (Envoyer les journaux maintenant) : vous pouvez choisir manuellement une fenêtre glissante spécifique comme délai d’exécution pour la collecte des journaux.

![Options de collecte des journaux de diagnostic](media/azure-stack-help-and-support/banner-enable-automatic-log-collection.png)

::: moniker-end
::: moniker range="<= azs-1910"

## <a name="diagnostic-log-collection"></a>Collecte des journaux de diagnostic

À partir de la version 1907, il existe deux nouvelles façons de collecter les journaux dans **Aide et support** :

- **Collecte automatique** : si cette option est activée, la collecte des journaux est déclenchée par des alertes d’intégrité spécifiques.
- **Collecter les journaux maintenant** : vous pouvez choisir une fenêtre de 1 à 4 heures sur les sept derniers jours.

![Options de collecte des journaux de diagnostic](media/azure-stack-automatic-log-collection/azure-stack-log-collection-overview.png)

Les systèmes intégrés peuvent partager les journaux de diagnostic avec le support Microsoft. Le kit de développement Azure Stack (ASDK) étant un environnement d’évaluation, il n’est pas pris en charge par le support Microsoft. Pour plus d’informations, consultez [Vue d’ensemble de la collecte automatique des journaux de diagnostic Azure Stack Hub](azure-stack-diagnostic-log-collection-overview.md).

## <a name="help-and-support-for-earlier-releases-azure-stack-hub-pre-1905"></a>Aide et support pour les versions d’Azure Stack Hub antérieures à la version 1905

Dans les versions antérieures d’Azure Stack Hub, vous avez également un lien **Aide + support** qui vous redirige vers la [Documentation pour les opérateurs Azure Stack Hub](https://aka.ms/adminportaldocs).

![Obtenir des tutoriels de support](media/azure-stack-help-and-support/get-support-previous.png)

En cas de problème avec des services cloud, le support est assuré par le support Microsoft. Vous pouvez sélectionner **Aide** (point d’interrogation) dans le coin supérieur droit du portail administrateur, **Aide et support**, puis **Nouvelle demande de support** pour envoyer une nouvelle demande de support directement au support Microsoft.

Pour un système intégré, il existe un processus d’escalade et de résolution coordonné entre Microsoft et nos partenaires OEM. En cas de problème avec des services cloud, le support est assuré par le support Microsoft.

En cas de problème de déploiement, de correctif et de mise à jour, de matériel (y compris les unités remplaçables sur site) et de logiciel d’une marque de matériel, notamment les logiciels exécutés sur l’hôte du cycle de vie du matériel, contactez d’abord votre fournisseur de matériel OEM. Pour tout autre problème, contactez le support Microsoft.

Pour l’ADSK, vous pouvez poser des questions de support sur le [Forum MSDN Azure Stack Hub](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack).

Sélectionnez **Aide** (point d’interrogation) dans le coin supérieur droit du portail administrateur et sélectionnez ensuite **Nouvelle demande de support** pour obtenir de l’aide de la part de spécialistes parmi la communauté Azure Stack Hub. ASDK étant un environnement d’évaluation, le support Microsoft n’offre aucun support officiel.

::: moniker-end

## <a name="next-steps"></a>Étapes suivantes

- Découvrez la [collecte des journaux de diagnostic](./azure-stack-diagnostic-log-collection-overview.md?view=azs-2002).
- Découvrez comment [trouver votre ID cloud](azure-stack-find-cloud-id.md).
- Découvrez la [résolution des problèmes Azure Stack Hub](azure-stack-troubleshooting.md).
