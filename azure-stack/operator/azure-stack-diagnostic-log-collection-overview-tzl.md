---
title: Vue d’ensemble de la collecte des journaux de diagnostic dans Azure Stack Hub
description: Explique la collecte des journaux de diagnostic dans Azure Stack Hub Aide et support, y compris la collecte proactive et à la demande des journaux.
author: justinha
ms.topic: article
ms.date: 02/26/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 02/26/2020
ms.openlocfilehash: 8f97ecd20e7ef8db69033268baf96060e1315751
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2020
ms.locfileid: "79520632"
---
# <a name="overview-of-azure-stack-hub-diagnostic-log-collection"></a>Vue d’ensemble de la collecte automatique des journaux de diagnostic Azure Stack Hub 

Azure Stack Hub est une grande collection de composants Windows et de services Azure locaux qui interagissent les uns avec les autres. Tous ces composants et services génèrent leur propre ensemble de journaux. Pour permettre aux services de support technique Microsoft de diagnostiquer efficacement les problèmes, nous avons fourni une expérience fluide pour la collecte des journaux de diagnostic. 

La collecte des journaux de diagnostic dans Aide et support permet aux opérateurs de collecter et de partager rapidement les journaux de diagnostic avec les services de support technique Microsoft, interface utilisateur simple, qui ne nécessite pas PowerShell. Les journaux sont collectés, même si d’autres services d’infrastructure sont défaillants.  
 
Nous vous recommandons d’utiliser cette approche de la collecte des journaux et de recourir à l’[utilisation du point de terminaison privilégié (PEP)](azure-stack-get-azurestacklog.md) seulement si le portail administrateur ou le panneau Aide et support n’est pas disponible. 

>[!NOTE]
>Azure Stack Hub doit être inscrit et disposer d’une connexion Internet pour utiliser la collecte des journaux de diagnostic. Si Azure Stack Hub n’est pas inscrit, [utilisez le point de terminaison privilégié (PEP)](azure-stack-get-azurestacklog.md) pour partager les journaux. 

![Capture d’écran des options de collecte des journaux de diagnostic](media/azure-stack-help-and-support/banner-enable-automatic-log-collection.png)

## <a name="collection-options-and-data-handling"></a>Options de collecte et gestion des données

La fonctionnalité de collecte des journaux de diagnostic offre deux options pour envoyer des journaux. Le tableau suivant décrit chaque option et la façon dont vos données sont gérées dans chaque cas. 

### <a name="send-logs-proactively"></a>Send logs proactively (Envoyer les journaux de manière proactive)

La [collecte proactive des journaux](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md) rationalise et simplifie la collecte des journaux de diagnostic afin que les clients puissent envoyer des journaux à Microsoft avant d’ouvrir un cas de support. Les journaux de diagnostic sont chargés de manière proactive à partir d’Azure Stack Hub à des fins d’analyse. Ces journaux sont collectés uniquement quand une [alerte d’intégrité du système](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md#proactive-diagnostic-log-collection-alerts) est déclenchée et ne sont accessibles qu’aux Services de support technique dans le contexte d’un cas de support.


#### <a name="how-the-data-is-handled"></a>Gestion des données

Vous acceptez que Microsoft procède régulièrement et automatiquement à la collecte des journaux sur la seule base des alertes d’intégrité du système Azure Stack Hub. Vous reconnaissez et acceptez également le chargement et la conservation de ces journaux dans un compte de stockage Azure managé et contrôlé par Microsoft. 

Les données sont utilisées uniquement à des fins de dépannage des alertes d’intégrité du système et ne sont pas utilisées à des fins marketing, de publicité ou à d’autres fins commerciales sans votre consentement. Les données peuvent être conservées pendant une période maximale de 90 jours, et toutes les données collectées par Microsoft sont traitées conformément à nos [pratiques de confidentialité standard](https://privacy.microsoft.com/).

Toutes les données déjà collectées avec votre consentement ne sont pas affectées par la révocation de votre autorisation.

Les journaux collectés à l’aide de la **collecte proactive des journaux** sont chargés sur un compte de stockage Azure managé et contrôlé par Microsoft. Microsoft peut accéder à ces journaux dans le contexte d’un cas de support et pour améliorer l’intégrité d’Azure Stack Hub.

### <a name="send-logs-now"></a>Envoyer des journaux maintenant

[Send logs now](azure-stack-configure-on-demand-diagnostic-log-collection-portal-tzl.md) (Envoyer les journaux maintenant) est une option manuelle qui permet de charger les journaux de diagnostic à partir d’Azure Stack Hub uniquement quand vous (en tant que client) lancez la collecte, généralement avant d’ouvrir un cas de support. 

Les opérateurs Azure Stack peuvent envoyer des journaux de diagnostic à la demande aux services de support technique Microsoft (CSS) à l’aide du portail administrateur ou de PowerShell. Si Azure Stack Hub est connecté à Azure, l’utilisation de la méthode permettant d’[envoyer les journaux maintenant dans le portail administrateur](azure-stack-configure-on-demand-diagnostic-log-collection-portal-tzl.md) est recommandée parce que c’est la plus simple pour envoyer les journaux directement à Microsoft. Si le portail n’est pas disponible, les opérateurs doivent alors [envoyer les journaux maintenant avec PowerShell](azure-stack-configure-on-demand-diagnostic-log-collection-powershell-tzl.md). 

Si vous êtes déconnecté d’Internet ou souhaitez uniquement enregistrer les journaux localement, utilisez la méthode [Get-AzureStackLog](azure-stack-get-azurestacklog.md) pour envoyer les journaux. L’organigramme suivant montre l’option à utiliser pour l’envoi des journaux de diagnostic dans chaque cas. 

![Organigramme montrant comment envoyer des journaux à Microsoft](media/azure-stack-help-and-support/send-logs-now-flowchart.png)

#### <a name="how-the-data-is-handled"></a>Gestion des données

En lançant la collecte des journaux de diagnostic à partir d’Azure Stack Hub, vous reconnaissez et acceptez de charger ces journaux et de les conserver dans un compte de stockage Azure managé et contrôlé par Microsoft. Les services de support technique Microsoft peuvent accéder à ces journaux immédiatement avec le cas de support sans avoir à contacter le client pour la collecte des journaux. 

Les données sont utilisées uniquement à des fins de dépannage des alertes d’intégrité du système et ne sont pas utilisées à des fins marketing, de publicité ou à d’autres fins commerciales sans votre consentement. Les données peuvent être conservées pendant une période maximale de 90 jours, et toutes les données collectées par Microsoft sont traitées conformément à nos [pratiques de confidentialité standard](https://privacy.microsoft.com/). 

Les journaux collectés à l’aide de l’option Send logs now (Envoyer les journaux maintenant) sont chargés sur un stockage managé et contrôlé par Microsoft. Microsoft accède à ces journaux dans le contexte d’un cas de support et pour améliorer l’intégrité d’Azure Stack Hub. 

## <a name="bandwidth-considerations"></a>Remarques relatives à la bande passante

La taille moyenne de la collecte des journaux de diagnostic varie selon que la collecte est proactive ou manuelle. La taille moyenne de la **collecte proactive des journaux** est d’environ 2 Go. La taille de la collecte pour l’option **Send logs now** (Envoyer les journaux maintenant) dépend du nombre d’heures collectées.

Le tableau suivant répertorie les éléments à prendre en compte pour les environnements avec des connexions limitées à Azure.


| Connexion réseau | Impact |
|--------------------|--------|
| Connexion à faible bande passante/latence élevée | Le chargement du journal va prendre un certain temps. | 
| Connexion partagée | Le chargement peut également avoir un impact sur d’autres applications/utilisateurs partageant la connexion réseau. |
| Connexion limitée | Des frais supplémentaires peuvent être facturés par votre fournisseur de services Internet pour l’utilisation supplémentaire du réseau. | 

## <a name="see-also"></a>Voir aussi

[Gestion des journaux et des données client Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-data-collection)

