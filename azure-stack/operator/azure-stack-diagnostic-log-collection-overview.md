---
title: Vue d’ensemble de la collecte automatique des journaux de diagnostic Azure Stack | Microsoft Docs
description: Explique la collecte des journaux de diagnostic dans Azure Stack Aide et support, y compris la collecte automatique et à la demande des journaux.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 09/04/2019
ms.openlocfilehash: 338df8d335376ebbaf14230e62d8dee9f813994f
ms.sourcegitcommit: e2f6205e6469b39c2395ee09424bb7632cb94c40
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70271740"
---
# <a name="overview-of-azure-stack-diagnostic-log-collection"></a>Vue d’ensemble de la collecte automatique des journaux de diagnostic Azure Stack 

*S’applique à : systèmes intégrés Azure Stack*

Azure Stack est une grande collection de composants qui fonctionnent ensemble et interagissent. Tous ces composants génèrent leurs propres journaux d’activité. Cela peut compliquer le diagnostic des problèmes, notamment quand les erreurs proviennent de plusieurs composants Azure Stack en interaction. Pour résoudre ce problème, nous avons conçu une expérience de collecte des journaux de diagnostic. 

Avant la version 1907, l’expérience de diagnostic comprenait l’utilisation de [Test-AzureStack](azure-stack-diagnostic-test.md) pour valider l’intégrité du système et l’utilisation du [point de terminaison privilégié (PEP)](azure-stack-configure-on-demand-diagnostic-log-collection.md#using-pep) pour collecter les journaux pour la résolution des problèmes. 

Depuis la version 1907, la page **Aide et support** simplifie l’utilisation de la **collecte des journaux de diagnostic**. 
**La collecte des journaux de diagnostic** fait partie d’un investissement continu pour améliorer l’expérience de l’opérateur Azure Stack dans le processus de résolution des problèmes. Grâce à ces améliorations, les opérateurs peuvent rapidement collecter et partager des journaux de diagnostic avec les services de support technique Microsoft (CSS). Les journaux peuvent être stockés dans un conteneur d’objets blob dans Azure, où l’accès peut être personnalisé en fonction des besoins.    
   
**La collecte des journaux de diagnostic** peut collecter les journaux de diagnostic de deux façons différentes :

- **Collecte automatique** : si elle est activée (recommandé), la collecte des journaux est déclenchée automatiquement par certaines alertes d’intégrité et stockée dans votre compte de stockage Azure.
- **Collecter les journaux maintenant** : il s’agit d’une option à la demande dans laquelle vous pouvez choisir de collecter les journaux dans une fenêtre glissante de 1 à 4 heures au cours des sept derniers jours.

![Capture d’écran des options de collecte des journaux de diagnostic](media/azure-stack-automatic-log-collection/azure-stack-log-collection-overview.png)

**La collecte des journaux de diagnostic** a une interface utilisateur simple et ne nécessite pas PowerShell. Les journaux sont collectés de façon fiable, même si les services d’infrastructure sont défaillants.
Si votre stratégie autorise le partage des journaux de diagnostic avec CSS, la **collecte des journaux de diagnostic** est la méthode de collecte recommandée depuis la version 1907. Vous ne devez utiliser [le PEP](azure-stack-configure-on-demand-diagnostic-log-collection.md#using-pep) pour collecter les journaux que si la **collecte des journaux de diagnostic** dans Aide et support n’est pas disponible.

## <a name="automatic-diagnostic-log-collection"></a>Collecte automatique des journaux de diagnostic 

La collecte de journaux de diagnostic automatique télécharge de manière proactive les journaux de diagnostic d’Azure Stack vers un objet blob de stockage dans Azure lorsque certaines alertes critiques sont générées, ce qui réduit considérablement le temps nécessaire au partage des journaux de diagnostic avec CSS.

Pour plus d’informations sur la collecte automatique des journaux, consultez [Configurer la collecte automatique des journaux de diagnostic Azure Stack](azure-stack-configure-automatic-diagnostic-log-collection.md).

## <a name="on-demand-diagnostic-log-collection"></a>Collecte des journaux de diagnostic à la demande

Avec la collecte à la demande, les journaux de diagnostic sont chargés d’Azure Stack vers un objet blob de stockage dans Azure lorsqu’un opérateur Azure Stack déclenche manuellement la collecte.
CSS fournit une URL de signature d’accès partagé (SAS) à l’objet blob de stockage de CSS. Un opérateur Azure Stack peut cliquer sur **Collecter les journaux maintenant** et entrer l’URL SAS. Les journaux de diagnostic sont ensuite chargés directement vers l’objet blob CSS sans partage intermédiaire. 

Pour plus d’informations sur la collecte des journaux à la demande, consultez [Collecter les journaux de diagnostic Azure Stack maintenant](azure-stack-configure-on-demand-diagnostic-log-collection.md).

## <a name="bandwidth-considerations"></a>Remarques relatives à la bande passante

La taille moyenne de la collecte des journaux de diagnostic varie selon que la collecte des journaux est à la demande ou automatique. La taille moyenne de la collecte de journaux automatique est d’environ 2 Go, tandis que la taille de la collection de journaux à la demande dépend du nombre d’heures collectées. 

Le tableau suivant répertorie les éléments à prendre en compte pour les environnements avec des connexions limitées à Azure.

| Connexion réseau | Impact |
|--------------------|--------|
| Connexion à faible bande passante/latence élevée | Le chargement du journal va prendre un certain temps. | 
| Connexion partagée | Le chargement peut également avoir un impact sur d’autres applications/utilisateurs partageant la connexion réseau. |
| Connexion limitée | Des frais supplémentaires peuvent être facturés par votre fournisseur de services Internet pour l’utilisation supplémentaire du réseau. |

Pour plus d’informations, consultez [Meilleures pratiques pour la collecte automatique des journaux Azure Stack](azure-stack-best-practices-automatic-diagnostic-log-collection.md).

## <a name="see-also"></a>Voir aussi

[Gestion des données client et des journaux Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-data-collection)

[Utilisation des signatures d’accès partagé (SAP)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)

[Meilleures pratiques pour la collecte automatique des journaux Azure Stack](azure-stack-best-practices-automatic-diagnostic-log-collection.md)
