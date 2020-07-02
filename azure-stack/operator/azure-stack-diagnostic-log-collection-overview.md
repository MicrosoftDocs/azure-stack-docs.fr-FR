---
title: Collecte des journaux de diagnostic dans Azure Stack Hub
description: Découvrez-en plus sur la collecte des journaux de diagnostic dans Azure Stack Hub Aide et support.
author: justinha
ms.topic: article
ms.date: 02/26/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 02/26/2020
ms.openlocfilehash: d3c6ecaa062f97aef76835d3c291b4ecaf405b11
ms.sourcegitcommit: b2b0fe629d840ca8d5b6353a90f1fcb392a73bd5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/25/2020
ms.locfileid: "85377158"
---
# <a name="diagnostic-log-collection-in-azure-stack-hub"></a>Collecte des journaux de diagnostic dans Azure Stack Hub

::: moniker range=">= azs-2002"

Azure Stack Hub est une grande collection de composants Windows et de services Azure locaux qui interagissent les uns avec les autres. Tous ces composants et services génèrent leur propre ensemble de journaux. Afin de permettre au support Microsoft de diagnostiquer efficacement les problèmes, nous avons fourni une expérience fluide pour la collecte des journaux de diagnostic.

L’approche de la collecte des journaux de diagnostic dans **Aide et support**  permet aux opérateurs de collecter et de partager rapidement les journaux de diagnostic avec le support Microsoft dans une interface utilisateur simple, qui ne nécessite pas PowerShell. Les journaux sont collectés, même si d’autres services d’infrastructure sont défaillants.  

Nous vous recommandons d'utiliser cette approche de la collecte des journaux et de ne recourir à l'[utilisation du point de terminaison privilégié (PEP)](azure-stack-get-azurestacklog.md) que si le portail administrateur ou le panneau **Aide et support** est indisponible.

>[!NOTE]
>Azure Stack Hub doit être inscrit et disposer d’une connexion Internet pour utiliser la collecte des journaux de diagnostic. Si Azure Stack Hub n'est pas inscrit, [utilisez le point de terminaison privilégié (PEP)](azure-stack-get-azurestacklog.md) pour partager les journaux.

![Options de collecte des journaux de diagnostic dans Azure Stack Hub](media/azure-stack-help-and-support/banner-enable-automatic-log-collection.png)

## <a name="collection-options-and-data-handling"></a>Options de collecte et gestion des données

La fonctionnalité de collecte des journaux de diagnostic offre deux options pour envoyer des journaux. Le tableau suivant décrit chaque option et la façon dont vos données sont gérées dans chaque cas.

### <a name="send-logs-proactively"></a>Send logs proactively (Envoyer les journaux de manière proactive)

La [collecte proactive des journaux](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md) rationalise et simplifie la collecte des journaux de diagnostic afin que les clients puissent envoyer des journaux à Microsoft avant d’ouvrir un cas de support. Les journaux de diagnostic sont chargés de manière proactive à partir d’Azure Stack Hub à des fins d’analyse. Ces journaux sont collectés uniquement quand une [alerte d’intégrité du système](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md#proactive-diagnostic-log-collection-alerts) est déclenchée et ne sont accessibles au support Microsoft que dans le contexte d’un cas de support.

#### <a name="how-the-data-is-handled"></a>Gestion des données

Vous acceptez que Microsoft procède régulièrement et automatiquement à la collecte des journaux sur la seule base des alertes d’intégrité du système Azure Stack Hub. Vous reconnaissez et acceptez également le chargement et la conservation de ces journaux dans un compte de stockage Azure managé et contrôlé par Microsoft.

Les données sont exclusivement utilisées à des fins de dépannage des alertes d'intégrité du système, et non à des fins de marketing, de publicité ou à d'autres fins commerciales si vous n'y avez pas consenti. Les données peuvent être conservées pendant une période maximale de 90 jours, et toutes les données collectées par Microsoft sont traitées conformément à nos [pratiques de confidentialité standard](https://privacy.microsoft.com/).

Les données déjà collectées avec votre consentement ne sont pas affectées par la révocation de votre autorisation.

Les journaux collectés à l’aide de la **collecte proactive des journaux** sont chargés sur un compte de stockage Azure managé et contrôlé par Microsoft. Microsoft peut accéder à ces journaux dans le contexte d'un cas de support et pour améliorer l'intégrité d'Azure Stack Hub.

### <a name="send-logs-now"></a>Envoyer des journaux maintenant

[Send logs now](azure-stack-configure-on-demand-diagnostic-log-collection-portal-tzl.md) (Envoyer les journaux maintenant) est une option manuelle qui permet de charger les journaux de diagnostic à partir d’Azure Stack Hub uniquement quand vous (en tant que client) lancez la collecte, généralement avant d’ouvrir un cas de support.

Les opérateurs Azure Stack peuvent envoyer des journaux de diagnostic à la demande au support Microsoft en utilisant le portail administrateur ou PowerShell. Si Azure Stack Hub est connecté à Azure, l'utilisation de la méthode [Envoyer les journaux maintenant sur le portail administrateur](azure-stack-configure-on-demand-diagnostic-log-collection-portal-tzl.md) est recommandée, car il s'agit de la plus simple pour envoyer les journaux directement à Microsoft. Si le portail n’est pas disponible, les opérateurs doivent alors [envoyer les journaux maintenant avec PowerShell](azure-stack-configure-on-demand-diagnostic-log-collection-powershell-tzl.md).

Si vous êtes déconnecté d'Internet ou souhaitez uniquement enregistrer les journaux localement, utilisez la méthode [Get-AzureStackLog](azure-stack-get-azurestacklog.md) pour envoyer les journaux. L’organigramme suivant montre l’option à utiliser pour l’envoi des journaux de diagnostic dans chaque cas.

![Organigramme montrant comment envoyer des journaux à Microsoft](media/azure-stack-help-and-support/send-logs-now-flowchart.png)

#### <a name="how-the-data-is-handled"></a>Gestion des données

En lançant la collecte des journaux de diagnostic à partir d’Azure Stack Hub, vous reconnaissez et acceptez de charger ces journaux et de les conserver dans un compte de stockage Azure managé et contrôlé par Microsoft. Le support Microsoft peut accéder à ces journaux immédiatement avec le cas de support sans avoir à contacter le client pour la collecte des journaux.

Les données sont exclusivement utilisées à des fins de dépannage des alertes d'intégrité du système, et non à des fins de marketing, de publicité ou à d'autres fins commerciales si vous n'y avez pas consenti. Les données peuvent être conservées pendant une période maximale de 90 jours, et toutes les données collectées par Microsoft sont traitées conformément à nos [pratiques de confidentialité standard](https://privacy.microsoft.com/).

Les journaux collectés via la méthode **Envoyer les journaux maintenant** sont chargés sur un stockage managé et contrôlé par Microsoft. Microsoft accède à ces journaux dans le contexte d’un cas de support et pour améliorer l’intégrité d’Azure Stack Hub.

## <a name="bandwidth-considerations"></a>Remarques relatives à la bande passante

La taille moyenne de la collecte des journaux de diagnostic varie selon que la collecte est proactive ou manuelle. La taille moyenne de la **collecte proactive des journaux** est d’environ 2 Go. La taille de la collecte pour l’option **Send logs now** (Envoyer les journaux maintenant) dépend du nombre d’heures collectées.

Le tableau suivant répertorie les éléments à prendre en compte pour les environnements avec des connexions limitées à Azure.

| Connexion réseau | Impact |
|----|---|
| Connexion à faible bande passante/latence élevée | Le chargement des journaux prendra beaucoup de temps. |
| Connexion partagée | Le chargement peut également avoir un impact sur d'autres applications/utilisateurs partageant la connexion réseau. |
| Connexion limitée | Des frais supplémentaires peuvent être facturés par votre fournisseur de services Internet pour l'utilisation supplémentaire du réseau. |

::: moniker-end
::: moniker range="<= azs-1910"

## <a name="collecting-logs-from-multiple-azure-stack-hub-systems"></a>Collecte des journaux de plusieurs systèmes Azure Stack Hub

Configurez un conteneur d’objets blob pour chaque unité d’échelle Azure Stack Hub dont vous souhaitez collecter les journaux. Pour plus d’informations sur la configuration du conteneur d’objets blob, consultez [Configurer la collecte automatique des journaux de diagnostic Azure Stack Hub](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md). Il est recommandé d’enregistrer uniquement les journaux de diagnostic de la même unité d’échelle Azure Stack Hub dans un seul conteneur d’objets blob.

## <a name="retention-policy"></a>Stratégie de rétention

Créez une [règle de gestion du cycle de vie](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts) du stockage d’objets blob Azure pour gérer la stratégie de rétention des journaux. Nous vous suggérons de conserver les journaux de diagnostic pendant 30 jours. Pour créer une règle de gestion du cycle de vie dans Stockage Azure, connectez-vous au portail Azure, sélectionnez **Comptes de stockage**, sélectionnez le conteneur d'objets blob, puis, sous **Service BLOB**, sélectionnez **Gestion du cycle de vie**.

![Gestion du cycle de vie sur le portail Azure](media/azure-stack-automatic-log-collection/blob-storage-lifecycle-management.png)

## <a name="sas-token-expiration"></a>Expiration du jeton SAS

Configurez l’expiration de l’URL SAS sur deux ans. Si vous renouvelez vos clés de compte de stockage, veillez à régénérer l’URL SAS. Vous devez gérer le jeton SAS conformément aux meilleures pratiques. Pour plus d’informations, consultez [Meilleures pratiques SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#best-practices-when-using-sas).

## <a name="bandwidth-consumption"></a>Consommation de bande passante

La taille moyenne de la collecte des journaux de diagnostic varie selon que la collecte des journaux est à la demande ou automatique.

Pour la collecte de journaux à la demande, la taille de la collecte des journaux dépend du nombre d’heures collectées. Vous pouvez choisir une fenêtre de 1à 4 heures sur les sept derniers jours.

Lorsque la collecte automatique des journaux de diagnostic est activée, le service surveille les alertes critiques. Dès qu’une alerte critique est déclenchée et persiste pendant environ 30 minutes, le service collecte et charge les journaux correspondants. La taille de la collecte de journaux est d’environ 2 Go en moyenne. En cas d'échec d'un correctif et d'une mise à jour, la collecte automatique des journaux ne démarre que si une alerte critique est générée et persiste pendant environ 30 minutes. Nous vous recommandons de suivre les [instructions relatives à la surveillance des correctifs et des mises à jour](azure-stack-updates.md). L’analyse des alertes, la collecte des journaux et le téléchargement sont transparents pour l’utilisateur.

Sur un système sain, les journaux ne seront pas collectés du tout. Dans un système défectueux, la collecte des journaux peut s’exécuter deux ou trois fois par jour, mais en général une seule fois. Dans le pire des cas, il peut s'exécuter 10 fois par jour.  

Le tableau suivant peut aider les environnements avec des connexions limitées à Azure à tenir compte de l’impact de l’activation de la collecte automatique des journaux.

| Connexion réseau | Impact |
|---|---|
| Connexion à faible bande passante/latence élevée | Le chargement des journaux prendra beaucoup de temps. | 
| Connexion partagée | Le chargement peut également avoir un impact sur d'autres applications/utilisateurs partageant la connexion réseau. |
| Connexion limitée | Des frais supplémentaires peuvent être facturés par votre fournisseur de services Internet pour l'utilisation supplémentaire du réseau. |

## <a name="managing-costs"></a>Gestion des coûts

Les [frais liés au stockage d'objets blob](https://azure.microsoft.com/pricing/details/storage/blobs/) Azure dépendent de la quantité de données enregistrées tous les mois et d'autres facteurs tels que la redondance des données. Si vous n'avez pas de compte de stockage, vous pouvez vous connecter au portail Azure, sélectionner **Comptes de stockage** et suivre les étapes pour [créer une URL SAS de conteneur d'objets blob Azure](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md).

Il est recommandé de créer une [stratégie de gestion du cycle de vie](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts) du stockage d’objets blob Azure pour réduire les coûts de stockage. Pour plus d’informations sur la configuration du compte de stockage, consultez [Configurer la collecte automatique des journaux de diagnostic Azure Stack Hub](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md).

::: moniker-end

## <a name="see-also"></a>Voir aussi

[Gestion des journaux et des données client Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-data-collection)
