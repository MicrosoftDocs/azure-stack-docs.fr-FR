---
title: Vue d’ensemble de l’aide et du support pour Microsoft Azure Stack Hub
description: Bénéficiez d’un support pour Microsoft Azure Stack Hub.
author: justinha
ms.topic: article
ms.date: 07/24/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 07/24/2019
ms.openlocfilehash: 621c0abb4f2e50c72e5a024cc3c1e634e73fe0c2
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76882233"
---
# <a name="microsoft-azure-stack-hub-help-and-support"></a>Aide et support de Microsoft Azure Stack Hub

Dans le portail Azure Stack Hub, **Aide et support** propose aux opérateurs des ressources pour en savoir plus sur Azure Stack Hub, vérifier leurs options de support et bénéficier d’une aide spécialisée. À partir de la version 1907, les opérateurs peuvent aussi utiliser Aide et support pour collecter des journaux de diagnostic à des fins de résolution des problèmes.  

## <a name="help-resources"></a>Ressources d’aide 

Les opérateurs peuvent également utiliser **Aide et support** pour en savoir plus sur Azure Stack Hub, vérifier leurs options de support et bénéficier d’une aide personnalisée. 

### <a name="things-to-try-first"></a>Premières actions à essayer

En haut de **Aide + support** se trouvent des liens vers des actions que vous êtes susceptible d’essayer en premier, comme découvrir un nouveau concept, comprendre le fonctionnement de la facturation ou consulter les options de support disponibles. 

![Support en libre-service](media/azure-stack-help-and-support/get-support-tiles.png)

- **Documentation**. La [documentation pour les opérateurs Azure Stack Hub](index.yml) inclut des concepts, des rubriques pratiques et des tutoriels qui montrent comment proposer des services Azure Stack Hub comme des machines virtuelles, des bases de données SQL, des applications web et plus encore. 

- **En savoir plus sur la facturation**. Lisez des conseils sur [l’utilisation et la facturation](azure-stack-billing-and-chargeback.md).

- **Options de support**. Les opérateurs Azure Stack Hub ont le choix parmi une gamme d’[options de support Azure](https://aka.ms/azstacksupport) qui peuvent répondre aux besoins de toute entreprise. 

### <a name="get-expert-help"></a>Bénéficier d’une aide spécialisée 

Pour un système intégré, il existe un processus d’escalade et de résolution coordonné entre Microsoft et nos partenaires fabricants de matériel OEM.

En cas de problème au niveau des services cloud, la prise en charge s’effectue via les services de support technique Microsoft (CSS). Vous pouvez cliquer sur **Aide** (point d’interrogation) dans le coin supérieur droit du portail administrateur et cliquer ensuite sur **Aide + support** pour ouvrir **Aide + support - Vue d’ensemble** et envoyer une nouvelle demande de support. La création d’une demande de support présélectionne le service Azure Stack Hub. Nous recommandons fortement aux clients d’utiliser ce moyen pour envoyer des tickets de support, plutôt que d’utiliser le portail Azure public. 

En cas de problème de déploiement, de correctif et de mise à jour, de matériel (y compris les unités remplaçables sur site) et de logiciel d’une marque de matériel, notamment les logiciels exécutés sur l’hôte du cycle de vie du matériel, contactez d’abord votre fournisseur de matériel OEM. Pour tout autre problème, contactez Microsoft CSS.

![Bénéficier d’une aide spécialisée pour les systèmes intégrés](media/azure-stack-help-and-support/get-support-integrated.png)

Pour l’ADSK, vous pouvez poser des questions de support sur le [Forum MSDN Azure Stack Hub](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 

Vous pouvez cliquer sur **Aide** (point d’interrogation) dans le coin supérieur droit du portail d’administration et cliquer ensuite sur **Aide + support** pour ouvrir **Aide + support - Vue d’ensemble**, où vous trouverez un lien vers le forum. Les forums MSDN sont consultés régulièrement.  
Le Kit de développement étant un environnement d’évaluation, il n’y a aucune prise en charge officielle de la part des services de support technique Microsoft.

![Bénéficier d’une aide spécialisée pour le Kit ASDK](media/azure-stack-help-and-support/get-support-asdk.png)

Vous pouvez aussi contacter les forums MSDN pour trouver de l’aide concernant un problème, ou suivre une formation en ligne pour améliorer vos propres compétences. 

![Bénéficier d’une aide spécialisée](media/azure-stack-help-and-support/get-support-cards.png)

### <a name="get-up-to-speed-with-azure-stack-hub"></a>Apprendre tout ce qu’il faut savoir sur Azure Stack Hub

Cette série de tutoriels est personnalisée selon que vous exécutez le Kit ASDK ou des systèmes intégrés afin de vous permettre d’être rapidement opérationnel dans votre environnement. 

![Obtenir des tutoriels de support](media/azure-stack-help-and-support/get-support-tutorials.png)

## <a name="diagnostic-log-collection"></a>Collecte des journaux de diagnostic

À partir de la version 1907, il existe deux nouvelles façons de collecter les journaux dans **Aide et support** :

- **Collecte automatique** : Si cette option est activée, la collecte des journaux est déclenchée par des alertes d’intégrité spécifiques 
- **Collecter les journaux maintenant** : Vous pouvez choisir une fenêtre de 1 à 4 heures sur les sept derniers jours.

![Capture d’écran des options de collecte des journaux de diagnostic](media/azure-stack-automatic-log-collection/azure-stack-log-collection-overview.png)

Les systèmes intégrés peuvent partager les journaux de diagnostic avec les services de support technique Microsoft (CSS). Le kit de développement Azure Stack (ASDK) étant un environnement d'évaluation, il n'est pas pris en charge par CSS. Pour plus d’informations, consultez [Vue d’ensemble de la collecte automatique des journaux de diagnostic Azure Stack Hub](azure-stack-diagnostic-log-collection-overview.md).



## <a name="help-and-support-for-earlier-releases-azure-stack-hub-pre-1905"></a>Aide et support pour les versions d’Azure Stack Hub antérieures à la version 1905

Dans les versions antérieures d’Azure Stack Hub, vous avez également un lien **Aide + support** qui vous redirige vers la [Documentation pour les opérateurs Azure Stack Hub](https://aka.ms/adminportaldocs).

![Obtenir des tutoriels de support](media/azure-stack-help-and-support/get-support-previous.png)

En cas de problème au niveau des services cloud, la prise en charge s’effectue via les services de support technique Microsoft (CSS). Vous pouvez cliquer sur **Aide** (point d’interrogation) dans le coin supérieur droit du portail administrateur, cliquer sur **Aide + support** et cliquer ensuite sur **Nouvelle demande de support** pour envoyer une nouvelle demande de support directement à Microsoft CSS.

Pour un système intégré, il existe un processus d’escalade et de résolution coordonné entre Microsoft et nos partenaires OEM. En cas de problème avec des services cloud, le support est assuré par Microsoft CSS. 

En cas de problème de déploiement, de correctif et de mise à jour, de matériel (y compris les unités remplaçables sur site) et de logiciel d’une marque de matériel, notamment les logiciels exécutés sur l’hôte du cycle de vie du matériel, contactez d’abord votre fournisseur de matériel OEM. Pour tout autre problème, contactez Microsoft CSS.

Pour le Kit de développement, vous pouvez poser des questions de support sur le [Forum MSDN Azure Stack Hub](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). Vous pouvez cliquer sur **Aide** (point d’interrogation) dans le coin supérieur droit du portail administrateur et cliquer ensuite sur **Nouvelle demande de support** pour obtenir de l’aide de la part de spécialistes parmi la communauté Azure Stack Hub.
Le Kit de développement étant un environnement d’évaluation, il n’y a aucune prise en charge officielle de la part des services de support technique Microsoft.

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur la [résolution des problèmes Azure Stack Hub](azure-stack-troubleshooting.md)