---
title: Vue d’ensemble de l’aide et du support pour Microsoft Azure Stack Hub
description: Bénéficiez d’un support pour Microsoft Azure Stack Hub.
author: justinha
ms.topic: article
ms.date: 02/26/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 02/26/2020
ms.openlocfilehash: d4d5fbd3979d7f30354cb3716e3277b895ddac78
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "79520614"
---
# <a name="microsoft-azure-stack-hub-help-and-support"></a>Aide et support de Microsoft Azure Stack Hub

Les opérateurs Azure Stack Hub peuvent utiliser **Aide + support** pour collecter les journaux de diagnostic et les envoyer à Microsoft à des fins de résolution des problèmes. **Aide + support** dans le portail Azure Stack Hub est accessible à partir du portail administrateur. Il propose aux opérateurs des ressources pour en savoir plus sur Azure Stack, vérifier leurs options de support et bénéficier d’une aide spécialisée.  

![Capture d’écran montrant comment accéder à Aide et support dans le portail administrateur](media/azure-stack-help-and-support/help-and-support.png)

## <a name="help-resources"></a>Ressources d’aide 

Les opérateurs peuvent utiliser **Aide et support** pour en savoir plus sur Azure Stack Hub, vérifier leurs options de support et bénéficier d’une aide personnalisée. 

### <a name="things-to-try-first"></a>Premières actions à essayer

En haut de **Aide + support** se trouvent des liens vers des actions que vous êtes susceptible d’essayer en premier, comme découvrir un nouveau concept, comprendre le fonctionnement de la facturation ou consulter les options de support disponibles. 

![Support en libre-service](media/azure-stack-help-and-support/get-support-tiles.png)

- **Documentation**. La [documentation pour les opérateurs Azure Stack Hub](index.yml) inclut des concepts, des rubriques pratiques et des tutoriels qui montrent comment proposer des services Azure Stack Hub comme des machines virtuelles, des bases de données SQL, des applications web et plus encore. 

- **En savoir plus sur la facturation**. Lisez des conseils sur [l’utilisation et la facturation](azure-stack-billing-and-chargeback.md).

- **Options de support**. Les opérateurs Azure Stack Hub ont le choix parmi une gamme d’[options de support Azure](https://aka.ms/azstacksupport) qui peuvent répondre aux besoins de toute entreprise. 


### <a name="get-expert-help"></a>Bénéficier d’une aide spécialisée 

Pour un système intégré, il existe un processus d’escalade et de résolution coordonné entre Microsoft et nos partenaires fabricants de matériel OEM.

En cas de problème au niveau des services cloud, la prise en charge s’effectue via les services de support technique Microsoft (CSS). Vous pouvez cliquer sur **Aide** (point d’interrogation) dans le coin supérieur droit du portail administrateur et cliquer ensuite sur **Aide + support** pour ouvrir **Aide + support - Vue d’ensemble** et envoyer une nouvelle demande de support. La création d’une demande de support présélectionne le service Azure Stack Hub. Nous recommandons fortement aux clients d’utiliser ce moyen pour envoyer des tickets de support, plutôt que d’utiliser le portail Azure global. 

En cas de problème de déploiement, de correctif et de mise à jour, de matériel (y compris les unités remplaçables sur site) et de logiciel d’une marque de matériel, notamment les logiciels exécutés sur l’hôte du cycle de vie du matériel, contactez d’abord votre fournisseur de matériel OEM. Pour tout autre problème, contactez Microsoft CSS.

![Bénéficier d’une aide spécialisée pour les systèmes intégrés](media/azure-stack-help-and-support/get-support-integrated.png)

Pour l’ADSK, vous pouvez poser des questions de support sur le [Forum MSDN Azure Stack Hub](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 

Vous pouvez cliquer sur **Aide** (point d’interrogation) dans le coin supérieur droit du portail d’administration et cliquer ensuite sur **Aide + support** pour ouvrir **Aide + support - Vue d’ensemble**, où vous trouverez un lien vers le forum. Les forums MSDN sont consultés régulièrement.  
Le Kit de développement étant un environnement d’évaluation, il n’y a aucune prise en charge officielle de la part des services de support technique Microsoft.

Vous pouvez aussi contacter les forums MSDN pour trouver de l’aide concernant un problème, ou suivre une formation en ligne pour améliorer vos propres compétences. 

![Bénéficier d’une aide spécialisée](media/azure-stack-help-and-support/get-support-cards.png)

### <a name="get-up-to-speed-with-azure-stack-hub"></a>Apprendre tout ce qu’il faut savoir sur Azure Stack Hub

Cette série de tutoriels est personnalisée selon que vous exécutez le Kit ASDK ou des systèmes intégrés afin de vous permettre d’être rapidement opérationnel dans votre environnement. 

![Obtenir des tutoriels de support](media/azure-stack-help-and-support/get-support-tutorials.png)

## <a name="diagnostic-log-collection"></a>Collecte des journaux de diagnostic

Il existe deux façons d’envoyer des journaux de diagnostic à Microsoft : 

- [Send logs proactively](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md) (Envoyer les journaux de manière proactive) : Si cette option est activée, la collecte des journaux est déclenchée par des alertes d’intégrité spécifiques 
- [Send logs now](azure-stack-configure-on-demand-diagnostic-log-collection-portal-tzl.md) (Envoyer les journaux maintenant) : vous pouvez choisir manuellement une fenêtre glissante spécifique comme délai d’exécution pour la collecte des journaux

![Capture d’écran des options de collecte des journaux de diagnostic](media/azure-stack-help-and-support/banner-enable-automatic-log-collection.png)


## <a name="next-steps"></a>Étapes suivantes

- Découvrez la [collecte des journaux de diagnostic](azure-stack-diagnostic-log-collection-overview-tzl.md).
- Découvrez comment [trouver votre ID cloud](azure-stack-find-cloud-id.md).
- Apprenez-en davantage sur la [résolution des problèmes Azure Stack Hub](azure-stack-troubleshooting.md).
